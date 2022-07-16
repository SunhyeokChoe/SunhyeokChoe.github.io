---
title: "[WPF] 팝업 알림창 MVVM 패턴으로 구현하기"
author:
  name: SunhyeokChoe
  link: https://github.com/SunhyeokChoe
date: 2022-02-18 21:51:18 +0900
categories: [C#, WPF]
tags: [WPF, MVVM, Popup Notification]
math: true
mermaid: true
pin: false
---

WPF 앱에서 특정 이벤트 발생시 화면 구석에 Popup Notification이 띄워지도록 해봅시다. 소스는 **[Growl Alike WPF Notifications](https://www.codeproject.com/Articles/499241/Growl-Alike-WPF-Notifications)** 글을 참고해 만들었습니다. MVVM 패턴에 맞도록 변환했고, 앱 전역에서 호출할 수 있도록 정적 Singleton Wrapper 클래스를 정의하였으며, 외부에서 메시지 창 출력 요청시 메시지 타입, 이미지 타입, 커스텀 메시지 등 손쉽게 호출할 수 있도록 타입 기반으로 분리해 놓았습니다. 그리고 데스크탑 우상단이 아닌 앱 기준 우상단에 팝업창이 나타나도록 했습니다(다중 모니터를 사용하는 환경에서 앱이 최초 실행된 모니터에서만 팝업창이 나타나므로).

![팝업 알림창 최종 결과물 예시](/assets/img/2022-02-18/C%23/Growl Notification/팝업 알림창 최종 결과물 예시.png)
_팝업 알림창 최종 결과물 예시_

해당 소스에서는 default로 알림창 발생시 fade-in 효과로 2초 동안 서서히 나타나도록 되어있고, 6초동안 머물러 있다가 2초 동안 fade-out 되도록 되어 있습니다. 또한, 사용자가 fade-in 혹은 fade-out 중인 상태의 메시지 창 위에 마우스를 올리면 fade-in/out 효과가 사라지고 완전히 나타나게 됩니다. 이러한 애니메이션 효과(또는 디자인)는 입맛에 맞게 직접 수정해 사용하시면 됩니다.

## 클래스 구조

```
|-- Views
     |-- GrowlNotificationsView.xaml
          |-- GrowlNotificationsView.xaml.cs
|-- ViewModels
     |-- GrowlNotificationsViewModel.cs
|-- Models
     |-- Notificates
          |-- Notification.cs
          |-- Notificator.cs
          |-- NotificatorWrapper.cs
     |-- Structures
          |-- NotificationType.cs
|-- Interfaces
     |-- IWindowFunc.cs
```

※ GrowlNotificationsViewModel, Notificator, Notification 클래스에서 상속받는 ViewModelBase 클래스는 INotifyPropertyChanged 인터페이스를 구현하는 클래스입니다. RaisePropertyChanged 메서드는 따로 구현해 주시기 바랍니다.

### Views: **GrowlNotificationsView.xaml & GrowlNotificationsView.xaml.cs**

- UserControl이 아닌 Window 형태
- DataContext는 GrowlNotificationsViewModel.cs
- ItemsControl에 DataContext의 Notifications가 바인딩됨

```xml
<!--  **GrowlNotificationsView.xaml**  -->

<Window x:Class="__PROJECT_NAMESPACE__"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:model="clr-namespace:__PROJECT_NAMESPACE__.Models.Notificates"
        mc:Ignorable="d"
        Title="GrowlNotifications" Height="630" Width="330"
        ShowActivated="False" AllowsTransparency="True" WindowStyle="None" ShowInTaskbar="False" Background="Transparent" UseLayoutRounding="True">
    <Window.Resources>

        <SolidColorBrush x:Key="NormalBorderBrush" Color="Black" />
        <SolidColorBrush x:Key="DefaultedBorderBrush" Color="Black" />
        <SolidColorBrush x:Key="DisabledForegroundBrush" Color="#888" />
        <SolidColorBrush x:Key="DisabledBackgroundBrush" Color="#EEE" />
        <SolidColorBrush x:Key="DisabledBorderBrush" Color="#AAA" />
        <SolidColorBrush x:Key="WindowBackgroundBrush" Color="#FFF" />
        <SolidColorBrush x:Key="SelectedBackgroundBrush" Color="#DDD" />

        <Style x:Key="ButtonFocusVisual">
            <Setter Property="Control.Template">
                <Setter.Value>
                    <ControlTemplate>
                        <Border>
                            <Rectangle
                                Margin="2"
                                StrokeThickness="1"
                                Stroke="#60000000"
                                StrokeDashArray="1 2"/>
                        </Border>
                    </ControlTemplate>
                </Setter.Value>
            </Setter>
        </Style>

        <LinearGradientBrush x:Key="CloseNormal" StartPoint="0.5,0" EndPoint="0.5,1">
            <GradientStop Color="#2b2c2e" Offset="0.0"/>
            <GradientStop Color="#28292b" Offset="1.0"/>
        </LinearGradientBrush>
        <LinearGradientBrush x:Key="CloseOver" StartPoint="0.5,0" EndPoint="0.5,1">
            <GradientStop Color="#68717d" Offset="0.0"/>
            <GradientStop Color="#565d66" Offset="1.0"/>
        </LinearGradientBrush>
        <SolidColorBrush x:Key="ClosePressed" Color="#1f1f1f" />

        <!--  닫기 버튼 스타일 -->
        <Style x:Key="CloseButton" TargetType="{x:Type Button}">
            <Setter Property="SnapsToDevicePixels" Value="true"/>
            <Setter Property="OverridesDefaultStyle" Value="true"/>
            <Setter Property="FocusVisualStyle" Value="{StaticResource ButtonFocusVisual}"/>
            <Setter Property="MinHeight" Value="16"/>
            <Setter Property="MinWidth" Value="16"/>
            <Setter Property="Cursor" Value="Hand"/>
            <Setter Property="Template">
                <Setter.Value>

                    <ControlTemplate TargetType="{x:Type Button}">

                        <Grid>
                            <Border x:Name="Border" CornerRadius="3" BorderThickness="0" ClipToBounds="False" Background="{StaticResource CloseNormal}" BorderBrush="{StaticResource NormalBorderBrush}">
                                <Border.Effect>
                                    <DropShadowEffect ShadowDepth="0" Opacity=".4" BlurRadius="5" Color="Black"/>
                                </Border.Effect>
                                <Grid>
                                    <Image Source="pack://application:,,,/Resources/close.png" IsHitTestVisible="False" Margin="2">
                                        <Image.Effect>
                                            <DropShadowEffect Direction="90" ShadowDepth="1" BlurRadius="1"/>
                                        </Image.Effect>
                                    </Image>
                                    <ContentPresenter HorizontalAlignment="Center" VerticalAlignment="Center" RecognizesAccessKey="True"/>
                                </Grid>
                            </Border>
                        </Grid>

                        <ControlTemplate.Triggers>
                            <Trigger Property="IsMouseOver" Value="True">
                                <Setter TargetName="Border" Property="Background" Value="{StaticResource CloseOver}" />
                            </Trigger>
                            <Trigger Property="IsPressed" Value="True">
                                <Setter TargetName="Border" Property="Background" Value="{StaticResource ClosePressed}" />
                            </Trigger>
                            <Trigger Property="IsKeyboardFocused" Value="true">
                                <Setter TargetName="Border" Property="BorderBrush" Value="{StaticResource DefaultedBorderBrush}" />
                            </Trigger>
                            <Trigger Property="IsDefaulted" Value="true">
                                <Setter TargetName="Border" Property="BorderBrush" Value="{StaticResource DefaultedBorderBrush}" />
                            </Trigger>
                            <Trigger Property="IsEnabled" Value="false">
                                <Setter TargetName="Border" Property="Background" Value="{StaticResource DisabledBackgroundBrush}" />
                                <Setter TargetName="Border" Property="BorderBrush" Value="{StaticResource DisabledBorderBrush}" />
                                <Setter Property="Foreground" Value="{StaticResource DisabledForegroundBrush}"/>
                            </Trigger>
                        </ControlTemplate.Triggers>

                    </ControlTemplate>

                </Setter.Value>
            </Setter>
        </Style>

        <Storyboard x:Key="CollapseStoryboard">
            <DoubleAnimation From="100" To="0" Storyboard.TargetProperty="Height" Duration="0:0:1"/>
        </Storyboard>

        <!--  DataType을 Notification.cs로 지정  -->
        <DataTemplate x:Key="MessageTemplate" DataType="model:Notification">

            <Grid x:Name="NotificationWindow" Tag="{Binding Path=Id}" Background="Transparent" SizeChanged="NotificationWindowSizeChanged">
                <Border Name="border" Background="#252525" BorderThickness="0" CornerRadius="1" Margin="1, 5">
                    <Border.Effect>
                        <DropShadowEffect ShadowDepth="0" Opacity="0.8" BlurRadius="10"/>
                    </Border.Effect>
                    <Grid Height="70" Width="280" Margin="6">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto"></RowDefinition>
                            <RowDefinition Height="*"></RowDefinition>
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto"></ColumnDefinition>
                            <ColumnDefinition Width="*"></ColumnDefinition>
                        </Grid.ColumnDefinitions>

                        <!--  좌측 메인 이미지  -->
                        <Image Grid.RowSpan="2" Source="{Binding Path=ImageUrl}" Margin="0, 4, 12, 4" Width="60"></Image>

                        <!--  제목  -->
                        <TextBlock Grid.Column="1" Text="{Binding Path=Title}"  TextOptions.TextRenderingMode="ClearType" TextOptions.TextFormattingMode="Display" Foreground="White"
                                   FontFamily="Arial" FontSize="14" FontWeight="Bold" VerticalAlignment="Center"  Margin="2,4,4,2" TextWrapping="Wrap" TextTrimming="CharacterEllipsis" />

                        <!--  닫기 버튼  -->
                        <Button x:Name="CloseButton" Grid.Column="1" Width="16" Height="16" HorizontalAlignment="Right" Margin="0" Style="{StaticResource CloseButton}" />

                        <!--  본문  -->
                        <TextBlock Grid.Row="1" Grid.Column="1" Text="{Binding Path=Message}"  TextOptions.TextRenderingMode="ClearType" TextOptions.TextFormattingMode="Display" Foreground="White"
                                   FontFamily="Arial" VerticalAlignment="Center"  Margin="2,2,8,6" TextWrapping="Wrap" TextTrimming="CharacterEllipsis" />
                    </Grid>
                </Border>
            </Grid>

            <DataTemplate.Triggers>
                <EventTrigger RoutedEvent="Window.Loaded" SourceName="NotificationWindow">
                    <BeginStoryboard x:Name="FadeInStoryBoard">
                        <Storyboard>
                            <DoubleAnimation Storyboard.TargetName="NotificationWindow" From="0.01" To="1" Storyboard.TargetProperty="Opacity" Duration="0:0:1"/>
                            <DoubleAnimation Storyboard.TargetName="NotificationWindow" From="1" To="0" Storyboard.TargetProperty="Opacity" Duration="0:0:2" BeginTime="0:0:6"/>
                        </Storyboard>
                    </BeginStoryboard>
                </EventTrigger>

                <Trigger Property="IsMouseOver" Value="True">
                    <Trigger.EnterActions>
                        <SeekStoryboard Offset="0:0:3" BeginStoryboardName="FadeInStoryBoard" />
                        <PauseStoryboard BeginStoryboardName="FadeInStoryBoard" />
                    </Trigger.EnterActions>
                    <Trigger.ExitActions>
                        <SeekStoryboard Offset="0:0:3" BeginStoryboardName="FadeInStoryBoard" />
                        <ResumeStoryboard BeginStoryboardName="FadeInStoryBoard"></ResumeStoryboard>
                    </Trigger.ExitActions>
                </Trigger>

                <EventTrigger RoutedEvent="Button.Click" SourceName="CloseButton">
                    <BeginStoryboard>
                        <Storyboard>
                            <DoubleAnimation Storyboard.TargetName="NotificationWindow" From="1" To="0" Storyboard.TargetProperty="(Grid.Opacity)" Duration="0:0:0"/>
                        </Storyboard>
                    </BeginStoryboard>
                </EventTrigger>

                <Trigger SourceName="NotificationWindow" Property="Opacity" Value="0">
                    <Setter TargetName="NotificationWindow" Property="Visibility" Value="Hidden"></Setter>
                    <Trigger.EnterActions>
                        <BeginStoryboard Storyboard="{StaticResource CollapseStoryboard}"/>
                    </Trigger.EnterActions>
                </Trigger>
            </DataTemplate.Triggers>
        </DataTemplate>

    </Window.Resources>

    <!--
        DataContext는 Code-Behind에서 정의됐음
        DataContext: GrowlNotificationsViewModel은 ItemsControl의 DataContext
        ItmesSource: GrowlNotificationsViewModel에서 Notification 소스에 바인딩
        ItemTemplate: 각 메시지 아이템의 속성 (각각 다른 값을 지님)
    -->
    <ItemsControl
        x:Name="NotificationsControl"
        FocusVisualStyle="{x:Null}"
        ItemsSource="{Binding Notifications}"
        ItemTemplate="{StaticResource MessageTemplate}"
    />
</Window>
```

```csharp
// **GrowlNotificationsView.xaml.cs**

namespace __PROJECT_NAMESPACE__
{
    using System;
    using System.Windows;
    using System.Windows.Controls;
    using __PROJECT_NAME__.Models.Notificates;
    using __PROJECT_NAME__.ViewModels.Interfaces;
    using __PROJECT_NAME__.ViewModels.Windows;
    using __PROJECT_NAME__.Models.Structures;

    public partial class GrowlNotificationsView : Window
    {
        #region fields
        /// <summary> Top margin. </summary>
        int _topOffset = 20;

        /// <summary> Left margin. </summary>
        int _leftOffset = 335;
        #endregion fields

        #region ctors
        public GrowlNotificationsView()
        {
            DataContext = new GrowlNotificationsViewModel();

            Initialized += GrowlNotificationsView_Loaded;

            InitializeComponent();

            this.Top = App.Current.MainWindow.Top + _topOffset;
            this.Left = App.Current.MainWindow.Left + App.Current.MainWindow.Width - _leftOffset;
        }
        #endregion ctors

        #region methods
        /// <summary>
        /// MainWIndow의 Location 값이 변동됐을 때 MainWindow의 Top, Left 값과 offset 값을 활용해 우상단에 고정되도록 값 조정.
        /// </summary>
        public void SetLocation()
        {
            this.Top = App.Current.MainWindow.Top + _topOffset;
            this.Left = App.Current.MainWindow.Left + App.Current.MainWindow.Width - _leftOffset;
        }

        /// <summary>
        /// MainWindow의 Width, Height 값이 변동됐을 때 MainWindow의 Top, Left 값과 offset 값을 활용해 우상단에 고정되도록 값 조정.
        /// </summary>
        public void SetActualLocation()
        {
            if (App.Current.MainWindow.WindowState.Equals(WindowState.Maximized))
            {
                this.Top = _topOffset;

                // 두 개 이상의 모니터를 쓰는 환경에서 메인 모니터의 좌측 서브 모니터로 앱을 옮긴 경우 위치 조정
                this.Left = App.Current.MainWindow.Left < 0 ?
                    -_leftOffset :
                    App.Current.MainWindow.ActualWidth - _leftOffset;

                return;
            }

            this.Top = App.Current.MainWindow.Top + _topOffset;
            this.Left = App.Current.MainWindow.Left + App.Current.MainWindow.ActualWidth - _leftOffset;
        }

        /// <summary>
        /// GrowlNotificationsView가 렌더된 후 DataContext의 Show, Hide에
        /// 창 닫기, 열기 메서드를 익명함수로 넘겨 DataContext가 Show, Hide를 호출할 수 있도록 합니다.
        /// </summary>
        private void GrowlNotificationsView_Loaded(object sender, EventArgs e)
        {
            if (DataContext is IWindowFunc vm)
            {
                try
                {
                    vm.Show = () =>
                    {
                        this.Show();
                    };

                    vm.Hide = () =>
                    {
                        this.Hide();
                    };
                }
                catch (Exception)
                {
                    NotificatorWrapper.AddNotification(new Notification(MessageType.custom, "[GrowlNotificationsView.xaml.cs] Show() & Hide() 호출 에러 발생.", ImageType.error));
                }
            }
        }

        /// <summary> Grid의 SizeChanged 이벤트 </summary>
        private void NotificationWindowSizeChanged(object sender, SizeChangedEventArgs e)
        {
            if (e.NewSize.Height != 0.0)
                return;

            int gridTagId = int.Parse((sender as Grid).Tag.ToString());

            NotificatorWrapper.RemoveNotification(gridTagId);
        }
        #endregion methods
    }
}
```

Notification이 새로 추가되면 고유 ID를 할당받습니다. 이 고유한 ID는 Notifications 컬렉션에서 Notification 요소를 제거할 때 필요합니다. View에서 팝업창이 하나 사라질 때, 즉 Collapsed 상태가 되면 Height가 0이 됩니다. View의 Code-Behind 영역에서 NotificationWindowSizeChanged 메서드를 통해 Height가 0인 것을 알아차렸을 때(팝업창 하나가 사라짐을 알았을 때) 요소 하나를 제거하도록 합니다.

### ViewModels: **GrowlNotificationsViewModel.cs**

- 뷰의 뷰모델로서, Notification들을 담는 Notifications 컬렉션을 프로퍼티로 참조해 뷰에 바인딩
- Notificator 객체가 뷰 Window를 Show 혹은 Hide 할 수 있도록 Action 델리게이트를 인터페이스로 전달받음

```csharp
namespace __PROJECT_NAMESPACE__
{
    using System;
    using __PROJECT_NAME__.Models.Notificates;
    using __PROJECT_NAME__.ViewModels.Interfaces;

    /// <summary>
    /// Notificator.cs의 Notifications & buffer 컬랙션에 담길 Model 데이터 클래스
    /// </summary>
    internal class GrowlNotificationsViewModel : ViewModelBase, IWindowFunc
    {
        #region fields
        /// <summary>
        /// NotificationWrapper->Notification.Notifications ObservableCollection를 참조하는 프로퍼티.
        /// View와 바인딩되어 있는 Notifications 프로퍼티는 Notificator내의 notifications 객체를 참조하는 형식이다.
        /// </summary>
        private Notifications _Notifications = null;
        #endregion fields

        #region ctors
        /// <summary>
        /// MainWindow가 렌더링된 시점에 _growlNotificationsView(GrowlNotificationsView type) 객체 생성. 생성시 코드 플로우는 다음과 같다.
        /// 1. _growlNotificationsView의 기본생성자 호출
        /// 2. DataContext에 뷰모델 객체 GrowlNotificationsViewModel() 바인딩
        /// 3. InitializeComponent() 호출
        /// 4. GrowlNotificationsView.xaml 코드가 실행되어 각 Element에 대응되는 클래스 생성된 후 렌더링
        /// 5. GrowlNotificationsView.xaml의 최하단에 ItemsControl의 속성 ItemsSource에 팝업 메시지가 담긴 ObservableCollection 객체 Notifications 바인딩
        ///    ItemsControl의 속성 ItemTemplate에 key 이름이 "MessageTemplate"인 DataTemplate로 지정해 해당 템플릿에서 정의한 스타일 및 애니메이션 기반으로 메시지가 출력되도록 함
        /// </summary>
        public GrowlNotificationsViewModel()
        {
            _Notifications ??= NotificatorWrapper.GetNotifications();

            // this에 대한 참조를 Notificator에 주입 (Show(), Hide() 호출 위함)
            NotificatorWrapper.InjectDependency(this);
        }
        #endregion ctors

        #region properties
        /// <summary>
        /// Singleton Notificator.Notifications 객체 주소를 참조하는 프로퍼티.
        /// </summary>
        public Notifications Notifications
        {
            get => _Notifications;

            set
            {
                if (_Notifications != value)
                {
                    _Notifications = value;

                    RaisePropertyChanged(nameof(Notifications));
                }
            }
        }

        #region GrowlNotificationsView Window Show() & Hide()
        // GrowlNotificationsView의 Window가 Loaded 상태에 놓여 GrowlNotificationsView_Loaded가 실행됐을 때,
        // 이 Action 델리게이트에 this.Show()를 포함하는 람다 코드 전달
        public Action Show { get; set; }

        public Action Hide { get; set; }
        #endregion GrowlNotificationsView Window Show() & Hide()

        #endregion properties
    }
}
```

### Models: **Notification.cs**

- GrowlNotificationsView에 컬렉션 형태로 바인딩 될 Model 클래스
- 팝업창의 기본적인 메타데이터 포함  
  ex) Title, MsgType, Message, ImageUrl, Id
- 외부에서 NotificatorWrapper.AddNotification 및 NotificatorWrapper.RemoveNotification 을 호출할 때 파라미터로 활용됨

```csharp
namespace __PROJECT_NAMESPACE__
{
    using System.Linq;
    using System.Collections.ObjectModel;
    using __PROJECT_NAME__.Models.Structures;

    /// <summary> Notification을 담을 컬렉션 </summary>
    internal class Notifications : ObservableCollection<Notification> { }

    /// <summary>
    /// Notificator.cs의 Notifications & buffer 컬랙션에 담길 Model 데이터 클래스
    /// </summary>
    internal class Notification : ViewModelBase
    {
        #region fields
        private string _Message;

        /// <summary>
        /// 외부에서 AddNotification 호출시 MessageType을 정의하지 않을 경우
        /// Custom 메시지로 간주하므로 반드시 Message를 입력해야 함
        /// </summary>
        private MessageType _MsgType = MessageType.custom;

        private int _Id;
        private string _ImageUrl;
        private string _Title;
        #endregion fields

        #region ctors
        /// <summary>
        /// 메시지 생성자.
        /// default of imgType = ImageType.info
        /// </summary>
        /// <param name="msgType"> 메시지 타입 </param>
        /// <param name="msg"> 메시지 </param>
        /// <param name="imgType"> 이미지 타입 </param>
        public Notification(MessageType msgType, string msg = null, ImageType imgType = (ImageType)0)
        {
            string mt = msgType.ToString();
            if (msgType == MessageType.custom)
            {
                if (string.IsNullOrEmpty(msg))
                    throw new System.NotImplementedException("[Notification.cs] 메시지타입이 custom이나, message가 비어있습니다.");

                Message = msg;
            }
            else
                Message = EnumHelper.GetDescription(msgType);

            /* 
             * info, warn 그리고 error 이 세 가지의 타입만으론 부족해 더 많은 타입을 추가해야 한다면
             * 타입을 한 단계 더 높게 추상화 하시면 if-else 문을 제거하실 수 있습니다.
             */
            if (NotificationType.infoList.Contains(mt))
            {
                Title = ImageType.info.ToString();
                ImageUrl = EnumHelper.GetDescription(ImageType.info);
            }
            else if (NotificationType.warningList.Contains(mt))
            {
                Title = ImageType.warn.ToString();
                ImageUrl = EnumHelper.GetDescription(ImageType.warn);
            }
            else if (NotificationType.errorList.Contains(mt))
            {
                Title = ImageType.error.ToString();
                ImageUrl = EnumHelper.GetDescription(ImageType.error);
            }
            else // MessageType.custom
            {
                Title = imgType.ToString();
                ImageUrl = EnumHelper.GetDescription(imgType);
            }
        }

        public Notification() { }
        #endregion ctors

        #region properties
        /// <summary> Notification의 본문 메시지 </summary>
        public string Message
        {
            get { return _Message; }

            set
            {
                if (_Message == value) return;
                _Message = value;
                RaisePropertyChanged(nameof(Message));
            }
        }

        /// <summary> Notification의 메시지 타입 </summary>
        public MessageType MsgType
        {
            get { return _MsgType; }

            set
            {
                if (_MsgType == value) return;
                MsgType = value;
                RaisePropertyChanged(nameof(MsgType));
            }
        }

        /// <summary> Grid의 Tag ID </summary>
        public int Id
        {
            get { return _Id; }

            set
            {
                if (_Id == value) return;
                _Id = value;
                RaisePropertyChanged(nameof(Id));
            }
        }

        /// <summary> Notification의 이미지 경로 </summary>
        public string ImageUrl
        {
            get { return _ImageUrl; }

            set
            {
                if (_ImageUrl == value) return;
                _ImageUrl = value;
                RaisePropertyChanged(nameof(ImageUrl));
            }
        }

        /// <summary> Notification의 타이틀 제목 </summary>
        public string Title
        {
            get { return _Title; }

            set
            {
                if (_Title == value) return;
                _Title = value;
                RaisePropertyChanged(nameof(Title));
            }
        }
        #endregion properties
    }
}
```
Notification.cs 클래스에서 48번째 라인부터 사용하는 EnumHelper.GetDescription 메서드는 enum 값에 붙은 Description string 값을 얻게 해주는 역할을 합니다. 코드는 다음과 같습니다.
```csharp
namespace __PROJECT_NAMESPACE__
{
    using System;
    using System.ComponentModel;
    using System.Reflection;

    public static class EnumHelper 
    {
        /// <summary>
        /// Enum의 특정 인덱스의 Description 반환
        /// </summary>
        /// <param name="en"> description을 받아올 enum 인덱스 </param>
        public static string GetDescription(Enum en)
        {
            Type type = en.GetType();
            MemberInfo[] memInfo = type.GetMember(en.ToString());
            if (memInfo != null && memInfo.Length > 0)
            {
                // 해당 인덱스의 Description raw text 추출
                object[] attrs = memInfo[0].GetCustomAttributes(typeof(DescriptionAttribute), false);

                if (attrs != null && attrs.Length > 0)
                {
                    return ((DescriptionAttribute)attrs[0]).Description;
                }
            }
            return en.ToString();
        }
    }
}
```

### Models: **Notificator.cs**

- Notification을 담는 ObservableCollection \_notifications 객체(최대 5개)와 그 5개를 넘는 요소를 임시로 담아놓는 ObservableCollection \_buffer 객체(버퍼 역할) 포함
- Notification을 두 컬렉션에 Boundary check 해가며 Add, Delete 수행하는 로직 포함

```csharp
namespace __PROJECT_NAMESPACE__
{
    using System;
    using System.Linq;
    using __PROJECT_NAME__.Models.Structures;
    using __PROJECT_NAME__.ViewModels.Interfaces;

    internal class Notificator : ViewModelBase, IDisposable
    {
        #region fields
        private static readonly log4net.ILog logger = log4net.LogManager.GetLogger(System.Reflection.MethodBase.GetCurrentMethod().DeclaringType);

        /// <summary> 최대 메시지 수 </summary>
        private const byte MAX_NOTIFICATIONS = 5;

        /// <summary> 최대 버퍼 메시지 수 </summary>
        private const byte MAX_BUFFER_SIZE = 8;

        /// <summary> 메시지 카운트 </summary>
        private int _count;

        /// <summary>
        /// Notification 메시지들이 담긴 ObservableCollection.
        /// GrowlNotificationsView의 ItemsControl에 바인딩됨
        /// </summary>
        public static Notifications _notifications = new Notifications();

        /// <summary>
        /// 팝업창을 닫기 위해 GrowlNotificationsView의
        /// Show, Hide 델리게이트 참조
        /// </summary>
        private static IWindowFunc _growlNotificationsViewModel = null;

        /// <summary>
        /// "if) count > MAX_NOTIFICATIONS == true" 일 경우
        /// 담을 buffer ObservableCollection
        /// </summary>
        private Notifications _buffer = new Notifications();

        /// <summary> 중복 호출 검색 </summary>
        private bool _disposedValue = false;
        #endregion fields

        #region methods
        /// <summary> Notificator의 Notifications를 Get </summary>
        public Notifications GetNotifications()
        {
            return _notifications;
        }

        /// <summary>
        /// GrowlNotificationsViewModel 객체를 Injection 받는 메서드.
        /// 본 클래스에서 팝업창 Show(), Hide() 활용 가능
        /// </summary>
        /// <param name="vm"> Show, Hide 액션 프로퍼티 </param>
        public void InjectDependency(IWindowFunc vm)
        {
            _growlNotificationsViewModel = vm;
        }

        /// <summary>
        /// Notifications ObervableCollection에 Notification 객체 enqueue.
        /// UI Thread와 현재 Thread의 상이함(Cross-Thread issue)을 해결하기 위해
        /// AppDomain의 Dispatcher thread에 delegate 코드 위임
        /// </summary>
        /// <param name="notification"> 팝업창 Model </param>
        private void Add(Notification notification)
        {
            App.Current.Dispatcher.Invoke(new Action(() =>
            {
                _notifications.Add(notification);
            }));
        }

        /// <summary>
        /// Notifications에서 노드 하나 Remove
        /// </summary>
        /// <param name="notification"> 팝업창 Model </param>
        private void Remove(Notification notification)
        {
            App.Current.Dispatcher.Invoke(new Action(() =>
            {
                _notifications.Remove(notification);
            }));
        }

        /// <summary>
        /// GrowlNotificationsView Window를 Show
        /// </summary>
        private void Show()
        {
            App.Current.Dispatcher.Invoke(new Action(() =>
            {
                _growlNotificationsViewModel.Show();
            }));
        }

        /// <summary>
        /// GrowlNotificationsView Window를 Hide
        /// </summary>
        private void Hide()
        {
            App.Current.Dispatcher.Invoke(new Action(() =>
            {
                _growlNotificationsViewModel.Hide();
            }));
        }

        /// <summary>
        /// 메시지를 버퍼에 추가.
        /// </summary>
        /// <param name="notification"> 팝업창 Model </param>
        private void AddToBuffer(Notification notification)
        {
            if (!LimitationChcek())
                return;

            _buffer.Add(notification);
        }

        /// <summary>
        /// Notifications 큐 컬렉션에 Notification 추가.
        /// </summary>
        /// <param name="notification"></param>
        public void AddNotification(Notification notification)
        {
            try
            {
                notification.Id = _count++;
                if (_notifications.Count + 1 > MAX_NOTIFICATIONS)
                    AddToBuffer(notification);
                else
                    Add(notification);

                // notifications가 있을 경우 window를 띄움
                if (_notifications.Count > 0)
                    Show();
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.Print(ex.ToString());
                logger.Error($"Notificator 에러 발생. \n{ex}\n");
            }
        }

        /// <summary>
        /// Notifications 큐 컬렉션에 Notification 제거.
        /// </summary>
        /// <param name="gridTagId"> 제거할 Notification Model ID </param>
        public void RemoveNotification(int gridTagId)
        {
            // 제거해야 하는 Notification 쿼리
            Notification nodeToDelete = _notifications.First(n => n.Id == gridTagId);

            if (nodeToDelete == null)
                throw new NotImplementedException("[Notificator.cs] nodeToDelete == null");

            if (_notifications.Contains(nodeToDelete))
                Remove(nodeToDelete);

            if (_buffer.Count > 0)
            {
                Add(_buffer[0]);

                if (_buffer.Count > 0)
                    _buffer.RemoveAt(0);
            }

            // 더이상 보여줄 notifications가 없을 경우 윈도우 닫음
            if (_notifications.Count < 1)
                Hide();
        }

        private bool LimitationChcek()
        {
            try
            {
                if ((_buffer.Count + 1) > MAX_BUFFER_SIZE)
                {
                    throw new Exception("작업 요청이 너무 많습니다. 잠시 후 다시 시도해 주세요.", new System.InvalidOperationException());
                }

                return true;
            }
            catch (Exception ex)
            {
                // buffer의 맨 앞 요소 제거 후 위 Exception 메시지 추가
                _buffer.Clear();

                NotificatorWrapper.AddNotification(new Notification(MessageType.custom, ex.Message, ImageType.error));

                return false;
            }
        }
        #endregion methods

        #region IDisposable Support
        public void Dispose()
        {
            Dispose(true);
        }

        protected virtual void Dispose(bool disposing)
        {
            if (!_disposedValue)
            {
                if (disposing)
                {
                    _notifications = null;
                    _buffer = null;
                }

                _disposedValue = true;
            }
        }
        #endregion IDisposable Support
    }
}
```

### Models: **NotificatorWrapper.cs**

- Notificator를 캡슐화
- Notificator를 외부에서 호출할 수 있도록 static으로 만들었으며, 유일한 객체를 가지므로 Singleton 역할을 함 (Redundancy DCL)
- MainWindow OnClosing 시점에 Raise 되는 메서드 내 초입에서 Dispose 호출해 메모리 명시적 수집. (프로그램이 닫혔는데도 메모리상 static 객체가 동적 객체를 지속해서 참조하는 행위 방지)

```csharp
namespace __PROJECT_NAMESPACE__
{
    using System;
    using System.Threading.Tasks;
    using __PROJECT_NAME__.ViewModels.Interfaces;

    internal class NotificatorWrapper
    {
        #region fields
        private static NotificatorWrapper _instance;
        private Notificator Origin { get; set; }
        private static readonly object lockObj = new object();
        #endregion fields

        #region ctors
        private NotificatorWrapper()
        {
            Origin = new Notificator();
        }
        #endregion ctors

        #region properties
        private static NotificatorWrapper Instance
        {
            get
            {
                // double-checked locking
                if (_instance == null)
                {
                    lock (lockObj)
                    {
                        if (_instance == null)
                        {
                            _instance = new NotificatorWrapper();
                        }
                    }
                }

                return _instance;
            }
        }
        #endregion properties

        #region methods
        /// <summary>
        /// GrowlNotificationsViewModel 객체를 Injection 받는 메서드.
        /// Notificator 클래스에서 팝업창 Show(), Hide() 할 수 있도록 주입
        /// </summary>
        /// <param name="vm"> Show, Hide 액션 프로퍼티 </param>
        public void InjectDependency(IWindowFunc vm)
        {
            Instance.Origin.InjectDependency(vm);
        }

        /// <summary>
        /// Notificator의 Notifications를 return하여 참조할 수 있도록 함.
        /// </summary>
        public static Notifications GetNotifications()
        {
            return Instance.Origin.GetNotifications();
        }

        /// <summary>
        /// Notifications 큐 컬렉션에 Notification 추가.
        /// </summary>
        /// <param name="notification"></param>
        public static void AddNotification(Notification notification)
        {
            // Invoke to Thread Pool Thread to avoid overhead using fire & forget
            Task.Run(new Action(() =>
                Instance.Origin.AddNotification(notification)
            )).ConfigureAwait(false);
        }

        /// <summary>
        /// Notifications 큐 컬렉션에서 Notification 제거.
        /// </summary>
        /// <param name="gridTagId"></param>
        public static void RemoveNotification(int gridTagId)
        {
            Task.Run(new Action(() =>
                Instance.Origin.RemoveNotification(gridTagId)
            )).ConfigureAwait(false);
        }

        public static void Release()
        {
            Instance.Origin.Dispose();

            _instance = null;
        }
        #endregion methods
    }
}
```

팝업창을 띄우려면 다음과 같이 Wrapper 객체의 AddNotification 메서드를 호출하면 됩니다.

```csharp
// 사용자 정의 메시지 팝업
NotificatorWrapper.AddNotification(new Notification(MessageType.custom, $"[EXE:Communicator] {errMsg}", ImageType.error));

NotificatorWrapper.AddNotification(
    new Notification(MessageType.custom,
    $"An unexpected error occurred. see /logs/global/[{DateTime.Now:yyyy.mm.dd}]global.txt file.",
    ImageType.error));

// 메시지 타입에 따른 메시지 팝업
NotificatorWrapper.AddNotification(new Notification(MessageType.noPortAvailable));

NotificatorWrapper.AddNotification(new Notification(MessageType.noSlaveScanned));
```

### IWindowFunc.cs

- Notificator 객체가 GrowlNotificationsViewModel 객체의 Window 창을 보여주거나 가릴 수 있도록 액션 프로퍼티 Show, Hide를 노출한 인터페이스

```csharp
namespace __PROJECT_NAMESPACE__
{
    using System;

    internal interface IWindowFunc
    {
        /// <summary> Window Show()를 호출하는 메서드. </summary>
        Action Show { get; set; }

        /// <summary> Window Hide()를 호출하는 메서드. </summary>
        Action Hide { get; set; }

        /// <summary> Window OnClosing 시 호출되는 메서드. </summary>
        void OnClosing();
    }
}
```

### **NotificationType.cs**

- 알림에 대한 메시지 타입들을 enum 형태로 저장
- Notificator 클래스와 Add, Remove하는 외부 클래스에서 참조됨

```csharp
namespace __PROJECT_NAMESPACE__
{
    using System.ComponentModel;

    public enum MessageType
    {
        // 아래 enum 타입에 없는 경우 커스텀 메시지로 분기
        custom,

        [Description("연결에 성공했습니다.")]
        connectionSuccess,

        [Description("타임아웃이 발생했습니다.")]
        timeout,

        [Description("포트에 접근할 수 없습니다.")]
        portOccupied,

        // etc...
    }

    public enum ImageType
    {
        [Description("pack://application:,,,/Resources/info.png")]
        info,                    /* 알림 */

        [Description("pack://application:,,,/Resources/warning.png")]
        warn,                    /* 주의 */

        [Description("pack://application:,,,/Resources/error.png")]
        error,                   /* 경고 */

        // etc...
    }

    /// <summary> MessageType에 따른 ImageType 그룹핑 </summary>
    public static class NotificationType
    {
        /// <summary> Info 그룹 </summary>
        public static string[] infoList =
        {
            "portOccupied",
            "noPortAvailable",
            "modbus_aleadyConnected",
            "modbus_connected",
        };

        /// <summary> Warning 그룹 </summary>
        public static string[] warningList =
        {
            "timeout",
            "modbus_slaveError_read",
            "modbus_slaveError_write",
            "rxLimitDetected",
        };

        /// <summary> Error 그룹 </summary>
        public static string[] errorList =
        {
            "modbus_crcError",
            "modbus_connectionFailed",
            "serial_initError",
        };

        // etc...
    }
}
```

### App.xaml.cs

팝업창은 글로벌 영역에서 생성되어야 하므로 App.xaml.cs 에서 생성합니다.

```csharp
// ... App 렌더링 완료 직후 ...

// 팝업 윈도우 생성. _mainWindow의 Left, Top, Width를 참조하므로 메인윈도우 생성 후 생성
_growlNotificationsView = new GrowlNotificationsView();

/*
 * _growlNotificationsView 윈도우의 소유권을 _mainWindow로 넘김
 * (참조: https://docs.microsoft.com/ko-kr/dotnet/api/system.windows.window.owner?view=netcore-3.1)
 */
_growlNotificationsView.Owner = _mainWindow;
```

글 초입에 말씀 드렸듯이 PC 화면 우상단에 anchor되는 것이 아닌 프로그램 우상단에 anchor되도록 구현합니다.

```csharp
/// <summary>
/// 메인윈도우 Top, Left 위치 변동 감지시 팝업창 위치를
/// 메인윈도우 우상단에 고정되도록 조정
/// </summary>
/// <param name="sender"> AppDomain </param>
private void MainWindow_LocationChanged(object sender, EventArgs e)
{
    var window = sender as Window;

    // MainWindow가 Maximized 상태가 아닌 경우에만 변경해야 함
    if (!window.WindowState.Equals(WindowState.Maximized))
        _growlNotificationsView?.SetLocation();
}

/// <summary>
/// 메인윈도우 ActualWidth, ActualHeight 너비 변동 감지시
/// 팝업창 위치를 메인윈도우 우상단에 고정되도록 조정
/// </summary>
/// <param name="sender"> AppDomain </param>
private void MainWindow_SizeChanged(object sender, SizeChangedEventArgs e)
{
    _growlNotificationsView?.SetActualLocation();
}
```

앱 종료 시에도 지속적으로 값을 참조하는 hanging 문제를 피하기 위해 앱 종료(App.OnClosing) 시점에 명시적으로 _growlNotifiactionsView.Close() 를 호출하도록 합니다.

```csharp
_growlNotificationsView.Close();
```

<br/><br/><br/>
**_References_**

---

- [_Growl Alike WPF Notifications_](https://www.codeproject.com/Articles/499241/Growl-Alike-WPF-Notifications)
