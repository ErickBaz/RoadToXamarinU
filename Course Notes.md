---
tags:
  - training
  - goals
  - university
  - Xamarin
---
# Xamarin University Classes

## AND101 Introduction to Xamarin Android

Xamarin.Android apps -> use utilities from three libraries

- java.* (Xamarin provides C# wrappers for all Android Java libraries)
- android.* (Xamarin provides C# wrappers for all Androind APIs) (When a new version of Androis is released, new wrappers are released within days)
- Includes most .NET types but not the entire Mono library

Use JNI or Bindings Library to incorporate third-party java libraries into you Xamarin.Androind app
Binding library is built on top of JNI, takes some time to setup but is easier to use.

- ArcGIS (Mapping)
- PayPal (Finance)
- Triton player (music)

Create a new android app result:

- control id => android:id="@+id/MyButton"
- primary entry point => MainLauncher = true
- activity icon => Icon = "@drawable/icon"
- Instantiates UI => SetContentView(Resource.Layout.Main);
- Gain acccess to views in the UI => `FindViewById<Button>(Resource.Id.MyButton)`;

Android APP is a collection of Activities (UI + CodeBehind) and extra files (images, data files, etc.)

And Android UI is composed of Views (like button, textbox, etc) and ViewGroups divided in Collection Views(lists, grids, etc) and Layout Panels (elements that size and position their children like LinearLayout, GridLayout, RelativeLayout, AbsoluteLayout)
Views are also called **widgets**

A view is a UI component with on-screen visuals and behaviour such as events

Layout file:

- `*.axml` extension
- is a resource (is located in the resources folder then in layout)
- Most Common layouts: Linear, Grid, Relative, Absolute

Values folder: contains symbolic constants

- View attributes are used to set properties on the underlying objects
- View attributes must be prefixed (by convention) with the **Android** namespace when defined in XML
- View sizing **required**: LinearLayout requires ``layout_width`` and ``layout_height`` on **every** view (options: ``match_parent`` or ``wrap_content``)

Fixed units of measure: px (Screen pixel), pt (1/72''), in (inch) and mm for sizing **NOT RECOMMENDED** because they don't adapt to different screens

px = dp * dpi / 160

Physical pixels = dp size * dpi screen size / 160

A 100dp button on a 480 dpi screen will occupy 300 physical pixels: 100 * 480 / 160 = 300

- Activity = layout file + code behind
- Activity code behind must inherit from Activity base class and have the Activity attribute
- base.OnCreate must be called or an exception is thrown
- build process autogenerates a Resources.Layout class that contains an identifier for all the UI elements (that have an identifier assigned).
- The view class defines an ID INT property
- @+id to create the ID for an UI element
- With FindViewByID you can get an UI element in the code behind (null returned when the id is not found).

Here: 57:51

- Xamarin.Android uses Android native tools and libraries:
  - JDK (Java SDK) : is the collection of libraries and tools needed to build and run java applications (specially Dev tools like compiler, jar signer, etc because they are used in the Android Build Process)
  - Android SDK : APIs and tools needed to create and run a native Android App.
    - Core libraries : java.* and android.*
    - Optional libraries like google apis for calendar or email access.
    - Tools: build tools like bytecode compiler and runtime support like debug tools
  - Android NDK :  collection of code and tools that let you write part of your native android app in a language like C and C++. may not increase performance, useful in games or to reuse existing code.
  - Mono .NET : open source implementation of the .Net framework
    - Base class library : Many of there are available in Xamarin.Android
    - Mono class library : Not used
    - Mono Runtime : Used to execute your IL
    - C# compiler : Used to compiler your C#
- Native compilation: Java -> Java Compiler -> Java bytecodes - > Android DEX compiler - > DEX files (dalvik bytecodes)
- Native packaging: DEX files + Resources (images, etc.) -> Android APK builder -> .apk file (application package). For uploading to Play Store more steps are required: Signing (jarsigner) and optimizing with ZipAlign
- Native Execution: APK files needs to be uploaded into the ART (android runtime), this can be in a physical device or an emulator. Bytecodes are compiled to native code at installation: Ahead of time or AOT.
    Apps run in theri own proces with their own copy of the ART
    Before Android 5.0 is Dalvik VM instead of ART
- Android compilation: C# -> C# compiler -> assembly (IL and metadata)
- Xamarin.Android linking: Xamarin.Android linker removes unused IL to reduce the size of your app for deployment (mono assemblies and your assemblies are filtered using linker)
  - Project settings and code attributes let you control which assemblies are linked. Dynamic code should not use the linker (eg members accessed via reflection)
- Xamarin.Android apps have the Mono Runtime packaged in their APK file because it is needed to execute IL. Mono Runtime -> Android Builder -> APK file
- Mono runtime (your C# code + .net libraries) and Android Runtime (java libraries and android libraries) run side by side to execute a Xamarin.Android App
- Android SDK updates needs to be updated manually in order to have access to the latest versions of Android. (once a month is good).
- Android SDK manager helps you install new and old versions of the Android SDK
  - Android SDK Tools
  - Android SDK Platform-Tools (recomendation: install only platforms that you want to target)
  - Andoird SDK Build-tools

## AND102 Activities and Intents

### Activities

- Context is an access point to the Android environment running your app. Gives you:
  - Application info from APK file
  - Access to resources and assets
  - Access to on device files
- Activity **is a** context

``` csharp
var intent = new Intent(this, typeof(Activity2));
base.StartActivity(intent);
```

### Intents

- Intent is a **Request** you sent android to start a new activity
- Navigation: describes the paths you create in your app to let the user swith between your various activities
- Back button: closes the current activity and returns to the previous one.
- [Navigation patterns: Navigation Course](https://university.xamarin.com/videos/and205-navigation-patterns)
  - Stack :
    - the 'backstack' records the sequence of Activities in a stack to enable the user to return from any activity to the one that started it
    - the activities in the backstack may span multiple apps
  - Tab
  - Drawer
- ``Finish`` method ends current activity
- Passing arguments into an activity (2 ways)
  - Explicit

  ``` csharp
    var bundle =  new Bundle();
    bundle.PutInt("ContactId", 123456789);

    var intent = new Intent();
    intent.PutExtras(bundle);
  ```

  - Convenience

``` csharp
    var intent = new Intent();
    intent.PutExtra("ContactId", 123456789);
```

- Retrieve intent extras (2 ways)

``` csharp
        int id = base.Intent.Extras.GetInt("ContactId", -1);

        int id = base.Intent.GetIntExtra("ContactId", -1);  //-1 is the value to be returned if key not found
```

- Bundle is a collection of key-value
- Bundle supports:
  - Integer
  - Float
  - Boolean
  - String
- Bundle serialization supports: [Samples to implement bundle serialization](https://github.com/xamarin/monodroid-samples/blob/master/ExportAttribute/ExportAttributeTest/MainActivity.cs)
  - `Android.OS.IParcelable`
  - `Java.IO.ISerializable`
- Result code: an Enum that an activity uses to indicate success/failure
  - OK
  - Canceled
  - First user (intended to create user-defined result codes)
- Report result: the target activity calls `SetResult`
- Receive result: source activity handles `OnActivityResult`
- StartActivityForResult: to get a report result from the target activity (identified by a result code)
- **Implicit intent**: describes what you want done without specifying which activity should do it (make a phone call, take a picture, show a something on a map)
  Load several pieces that describe the operation (to better know how this information needs to be loaded, check documentation):
  - Action
  - Data
  - MIME type
  - Categories
  - Extras
- **Intent Action**: specifies the type of work you need done -> "Display some info", "Dial the phone", "Send a message"
- Intent actions:
    `Intent.ActionView`     ->  android.intent.action.VIEW
    `Intent.ActionDial`     ->  android.intent.action.DIAL
    `Intent.ActionEdit`     ->  android.intent.action.EDIT
    `Intent.ActionSendTo`       ->  android.intent.action.SENDTO
- Some actions are packaged with the classes they are associated with (i.e. `MediaStore.ActionImageCapture`)
- The action can be set from the constructor or with the `SetAction` method
- the data can be defined using `SetData`. Single piece of information for use by the target activity (data form map activity, data for phone dialer, data for browser activity)
- MIME type indicates the type of data you want the intento to manipulate
    - insert a new contact: vnd.android.cursor.dir/contact
    - add a calendar event: vnd.android.cursor.dir/event
    - select an image: image/* (image/jpeg)
- Category (optional) restricts the kind of activity you would like to handle your intent
    - Preference (i.e. settings panel)
    - Tab (Intended to live inside a tab)
    - Openable (i.e picker)
- `Intent.AddCategory` to add one or more categories to an intent
- Extras: `Intent.PutExtra` a bundle storing key value pairs. Load information into an intent. Source and target activities agree about the type of extra. Most of the symbolic constans are defined in the class they work.
    Examples:
    - Intent.ExtraEmail -> list of addresses for an email
    - MediaStore.ExtraOutput -> location for camera to save
    - AlarmClock.ExtraRingtone -> tone to play for an alarm
    - EventsColumns.Title -> calendar event title
- Intent example: show a location on a map
    ``` csharp
    var intent = new Intent();
    intent.SetAction(Intent.ActionView);
    intent.SetData(Android.Net.Uri.Parse("geo:37.797776, -122.401881?z=16"));
    ```
- Intent example: send an email

``` csharp
    var intent = new Intent();
    intent.SetAction(Intent.ActionSendTo);
    //tell Android to use only email apps to service this request
    intent.SetData(Android.Net.Uri.Parse("mailto:"));

    intent.PutExtra(Intent.ExtraEmail, new string[] {"hello@xamarin.com"});
    intent.PutExtra(Intent.ExtraSubject, "How are you?");
```

- Error checking: to avoid runtime exception you should verify that your implicit intent is valid using `Intent.ResolveActivity(PackageManager)`

[Implicit Intents](https://developer.android.com/guide/components/intents-common.html)
[Xamarin Evolve 2014 - MVVM Light](https://www.youtube.com/watch?v=FrcVQGTXZKM)
[Service Locator is an Anti-Pattern](http://blog.ploeh.dk/2010/02/03/ServiceLocatorisanAnti-Pattern/)

## AND 180

[Toolbar](https://developer.android.com/reference/android/widget/Toolbar.html)
[Action Bar Icons](https://developer.android.com/guide/practices/ui_guidelines/icon_design_action_bar.html "Action Bar Icons")
[App Bar structure](https://material.io/guidelines/layout/structure.html#structure-app-bar)
[James Montemagno's tips](https://blog.xamarin.com/android-tips-hello-material-design-v7-appcompat/)

## iOS101

OS X Yosemite 10.10+

The starting class must inherit from **UIApplicationDelegate**

- *Entitlements*: what the app can access: apple pay, iCloud, Passbook, in app purchases
- *Info*: describe metadata for the application: bundle identifier, version, build, devices, deployment target, device orientations, launch screens

### iOS Terminology

- MVC: 1970
- Model: data, information and logic, business layer -> functions, processing logic, persistence logic, data entities, validation logic
- View: visual components. The base class is UIView. Can be defined by code, storyboard or XBI
- Controller:  is the moderator. in ios derive from UIViewController
- Delegate: a set of methods and properties (more similar to an interface than a .Net Delegate)
- Protocol: closest analogy -> interface. Protocols can define methods and properties that are optional

> NOTE
> UI, CG is because objective C does not support namespaces!

On finished launching: three statements:

- Create a window
- Define the viewcontroller
- MakeKeyAndVisible

> ATTENTION
> Profiling is only available for business license

Are done in the mac build host

- Cross compiler
- linker
- package

> HINT
> iOS102 and iOS300 covers the iOS designer

## iOS102

Xamarin Designer for iOS

## iOS 205

[Apple developer](https://developer.apple.com/ios/human-interface-guidelines/app-architecture/navigation/)
[ivomynttinen](https://ivomynttinen.com/blog/ios-design-guidelines)
[natashatherobot.com](https://www.natashatherobot.com/navigation-bar-interactions-ios8/)
[Alwyn Lombaard's blog](http://blog.lombaard.co.uk/blog/2015/10/27/xamarin-ios-swipable-multiple-step-process-used-for-onboarding/)

## XAM110 Introduction to cross platform mobile development

- Xamarin applications are native and therefore will *always* include some platform specific code
- Sharable code is split between *reusable components* (40%) and *platform independent code* (60%)
- Xamarin.* Libraries (components) [Open Source Xamarin APIs] (github.com/xamarin)
  - Xamarin.Social (fb & tt support)
  - Xamarin.Auth (oauth support)
  - Xamarin.Mobile (location, camera and photos access)
- Another component source -> [Dot Net Foundation](http://www.dotnetfoundation.org)
  - MailKit
  - Rx
- AWESOME! [James Montemagno's plugin initiative](https://github.com/xamarin/plugins)
  - geofencing!
  - push notifications (android & iOS)
  - Toast notifications
  - device motion (accelerometer, gyroscope, magnetometer, compass)
- Another component: Xamarin.Forms -> shared set of UI controls (render native controls) [Xamarin Forms Home] (xamarin.com/forms)
- Must use PCL for XAML pages; for other shared code can use Shared Projects or PCL
- More shared code: Database access
  - SQLite is default in Android and IOS and can be added to WinPhone [XAM160: Lessons on SQLite and Data in mobile](https://university.xamarin.com/videos/xam160-data-in-mobile)
  - Another options: Azure, Amazon, Dropox, etc.
- Web Services
  - HttpClient supported for **REST**
    - System.XML, System.Json
    - LINQ to XML
    - Newtonsoft JSON.Net component
  - Use WCF or .asmx for **SOAP**
  - [XAM150: Web services](https://university.xamarin.com/videos/xam150-cross-platform-web-services)
  - ServiceStack and RestSharp supported also
- Nuget to find components. [Nuget](www.nuget.org)
- Component store (eww :P )

### Shared Project

- Used anytime the code doesn't depend of platform features (talk to a web service, parses a data format, works with database, performs processing/business logic)
- Two options:
  - **File linking**
    - Renaming could no work properly
  - **Shared Projects** (VS2013 R2)
    - Adding a reference to a shared project adds all the files to the target during the compile process, so each source file is compiled for the target.
    - Start with xamarin blank solution and add the required platforms one by one and finally add a **shared** project from miscellaneous
    - in the shared project only things that are not platform specific
  - Stategies for manage platform specific code in **file linking** or **shared project**
    - Conditional compilation:
          ``` csharpcsharp
              #if __MOBILE__
              #if __ANDROID__
              #if __IOS__
              #if WINDOWS_PHONE
              #if SILVERLIGHT
          ```
    - Class Mirroring: shared project uses a method that was defined independently in all the specific platform projects
    - Partial classes: split classes between shared and platform-specific
    - Optional Partial methods: the method can or cannot be defined in the partial class. If not, the call is ommited by the compiler

PROS:

- All APIS Available
- Platform specific logic can be added directly
- All file types can be Shared
- Anything supported by the target platform can be added to a shared project

CONS:

- Can lead to spaghetti code
- Cannot be unit tested on it's own
- Must be shipped in source form

### PCL Project

- Tied to specific platform + framework
- Some platform combinations are not allowed because there is not profile defined
- IDE will attempt to pick the closest variation (or give an error)
- Only code supported by all platforms. Techniques to reinforce this:
  - Callbacks
  - Factories
  - Dependency Injection
- For complex requirements, use of Interfaces is recommended
- PROS:
  - Enforces architectural design
  - Can be unit tested separately
  - Can be shipped in binary form (nugget)
- CONS:
  - Limited APIs available
  - Difficult to share non code files
  - Requires more work to integrate platform specific code

## XAM120 Introduction to Xamarin.Forms

- Xamarin.Forms allows to describe the UI once using a shared set of controls while still rendering a native UI
- Xamarin.Forms: cross platform UI framework to create mobile apps:
  - Android 4.0 +
  - iOS 6.1 +
  - Windows Phone 8.0 +
- XF offers:
  - Navigation
  - Layouts
  - Mapping support
  - Full XAML support
  - Data binding
  - Styles and triggers
- Not suitable for all types of application:
  - Great for data-driven apps
  - Not ideal if the UI needs to be highly customized
- Can be used for quickly prototyping
- UI is defined in terms of
    - *Pages*: abstract class used to define a single screen (derived types provides specific visualization and behavior)
      - Content: a single piece of content (one button, one label, a list)
      - MasterDetail: two panes of information (i.e. 2 content pages). Renders different depending on platform
        - Master:
        - Detail:
      - Navigation: manages a stack of pages with navigation bar
      - Tabbed: Pages that navigate between children using a tab bar
      - Carousel: pages allowing swipe gestures to switch between children
    - *Views*: is the base class of all visual controls:
      - Label, Image, Searchbar, Entry, Progress Bar, Activity Indicator, Button, Slider, OpenGL view, Editor, Stepper, Web View, DatePicker, Switch, ListView, BoxView, TimePicker, Frame, Picker
- Rendering views: Platform defines a *renderer* for each view that creates a native representation of the UI
    - There is a renderer for each control in each platform.
    - Button equals to:
      - Android.Widget.Button (Android)
      - UIButton (iOS)
      - System.Windows.Button
- **DEFINE UI ONCE** and then share it
- The UI will look slightly different in each platform
- Controls use:
    - *Properties* to define its look
    - *Events* to control its behavior

> TIP
> In the configuration manager, check only the build/deploy required checkboxes to ensure the compilation process runs faster

- a call to **Xamarin.Forms.Forms.Init()** initializes the framework
- a calle to **LoadApplication** method connects the platform to the abstract
- App.cs (VS)
  - InitializeComponents
  - Define MainPage content
- SolutionName.cs (XS)

> ATTENTION
> **ANDROID**: Main activity usually is descendant of Activity, but for Xamarin forms the parent class is ``Xamarin.Forms.Platform.Android.FormsApplicationActivity`` (OnCreate, FormsInit, LoadApplication)
> **iOS**: Parent class is ``Xamarin.Forms.Platform.iOS.FormsApplicationDelegate``(Init, LoadApplication, base.FinishedLaunching)

### Layouts

- All has 'Children' property to add views to the container
- Available layout:
  - Stacklayout
    - Orientation : vertical or horizontal
    - HeightRequest and WidthRequest: can or can not be honored.
    - Width and Height are readonly only to see the final calculated values
    - Padding property: define additional padding around the child
    - Spacing: space in between the children
  - AbsoluteLayout
  - RelativeLayout
  - GridLayout
    - Spacing: ``RowSpacing`` - ``ColumnSpacing``
  - ScrollView

### How to access to platform specific features

! ONLY IN SHARED CODE

- ``Device.OnPlatform``
  - One delegate per platform
- ``Device.OnPlatform<T>``
- ``Device.Idiom``, ``Device.OS``, ``Device.OpenUri``, ``Page.DisplayAlert``, ``Device.StartTimer``, ``Device.BeginInvoke``
- ``Xamarin.Forms.Maps``

> HINT
> Recommended lectures to get more about platform specific features: XAM110 and XAM300

### Creating abstractions

- Call functionality from native projects using interfaces and the dependency service locator integrated in Xamarin Forms
- Service Locator
  - Xamarin.Forms includes a ServiceLocator (DependencyService)
  1 Define an interface or abstract class in the PCL
  2 Provide implementation of the abstraction in each platform-specific project
  3 Expose platform-specific implementation using assembly-level attribute
  4 Retrieve and use the dependency: ``DependencyService.Get<T>`` (shared and platform projects can use this API!!!)
- Dependency Injection

## XAM 130 - XAML in Xamarin Forms

Creating the UI by code drawbacks

- Significant portion of code behind used to create UI
- The code ends with a mix of ui and behavior
- Designers cannot get involved easily

HTML like designers advantages:

- Toolable
- Human readable
- Extensible

XAML benefits

- Potential for division of labor
- Designer friendly
- Separation of UI from behavior

Xamarin forms -> XAML 2009

> ATTENTION
> Two item templates available:
> - Content Page => Entire screen of content
> - Content View => Composite control (smaller than a page)

Xaml pages must be created in cross-platform project

Like other XML: XAML is case sensitive

XAML files are stored as: EmbeddedResource with a special build type: MSBuild:UpdateDesignTimeXAML

- First step in any XAML codebehind: **InitializeComponent**, this will initialize all the fields created in XAML
- XML attributes are always strings: tryparse will be required (bool.tryparse, double.tryparse, enum.tryparse or TypeConverters)
  - Font, ImageSource, Color and Thickness all have built in type converters
- **Attached properties** refer to properties of another element (typically the parent)
- **Default properties**: the property that will be affected when child content is assigned to an element (example: text is default property for Label)

- To be able to access xaml controls they need an ID created with x:Name, this will create a private field in the codebehind
- To publish this field it can be wrapped in a public property (only the getter, a setter should not be provided because change the value won't change the on screen value)
- The events handlers can be defined in the codebehind or in XAML directly
- Scope of event handlers can be anything you want
- OnPlatform can be used in XAML too to provide values for iOS, Android or WindowsPhone
- To set values that can be known only at runtime use: IMarkupExtensions, identified by curly braces
- It's possible too to read values from static classes using: x:Static followed by the name of the class (in curly braces)
- Other markup extensions: {StaticResource}, {DynamicResource}, {x:Null}, {x:Type}, {x:Array} {Binding}
- XAML fragments -> ContentView similar definition as a page, but it won't use the full screen

- Xamarin Forms 2.0 includes XAMLC, a compiler that allows to compile the ui to intermediate language (IL) disabled by default for compatibility reasons.
- Specific classes (pages) can be excluded/included to be compiled

using Xamarin.Forms.Xaml;
[assembly: XamlCompilationAttribute(XamlCompilationOptions.Compile)]

or in a 'page by page' basis:

``` csharp
using Xamarin.Forms.Xaml;

[XamlCompilationAttribute(XamlCompilationOptions.Compile)]
public partial class MainPage : ContentPage {

}
```

## XAM 135

- By default, most views try to size themselves just large enough to hold their content (ej label takes only enough space to present its text)
- Explicit sizes in forms have no intrinsic units, the values are interpreted by each platform according to that platform's rules:
  - Windows: Effective pixels
  - iOS: points
  - Android: density independent pixels
- Views report its location the same way: x, y, width, height
- LayoutOptions: horizontal or Vertical
  - start
  - center
  - end
  - fill (generally overrides size preferences) (default)
  - Expands (only by stacklayout)
- Margin: extra space around. The type is a struct: thickness
- Padding: only applies to layout containers. not individual view. extra space around the inner space
- Stacklayout:
  - vertical or horizontal
  - In vertical the horizontal options are respected.
  - In vertical, the vertical options are NOT respected.
  - In a direction oposite to his orientation, the "and expand" options are ignored
  - expands content only in there direction of its orientation. The available space is distributed evenly between the views that are requesting for an specific space
- Attached properties: special type of properties that are defined in one class but are applied to objects of a different class. ie: class button has attached properties row and button that are handled by the parent grid (if any)
- infraestructure for AttachedProperty:
  - BindableProperty (registration: CreateAttached)
  - BindableObject (usage: get & set value)
- The owner of an attached property defines the property and access methods:
  ``` csharp
      public partial class Grid : Layout<View>
      {

          public static readonly BindableProperty RowProperty = BindableProperty.CreateAttached(...);

          public static int GetRow (BindableObject bindable) { ... }
          public static void SetRow (BindableObject bindable, int value) { ... }

      }
  ```
- In code:
  - Grid.SetRow (myButton, 1);
  - Grid.SetColumn (myButton, 2);
- In XAML -> special syntax:
  ``<Button Grid.Row="1"/>``

### Grid

- Organize its children into cells formed from rows and columns
- To define the size of a row/column
  - Absolute
  - Auto
  - Star
- To define rows/columns:
  - RowDefinition and ColumnDefinition:
    - GridLenght:
      - GridUnitType: absolute (fixed size), auto (adjust to the highest child) , star (proportional)
      - Double
- Span can be defined for rows and columns (default for this property is 1)
- Grid has his own list type for his children with several specialized 'add' methods
    .Add (label, column, row)
    .Add (button, column, yield column+n, row, row+n)

### ScrollView

- Add scrolling to a part of the layout
- Single child
- Orientation : Vertical, Horizontal, Both
- Don't nest scroll views (ie: listview inside scrollview)

**HERE: 33:53**

## XAM 140 Resources and Styles in Xamarin.Forms

Static Resources
Dynamic Resources

Implicit style (without x:Key, applied to all the control in the page of the specified style)

BasedOn to inherit style (only static resource allowed)

## XAM 150 Consuming REST-based Web Services

Connection type (cellular, wifi, ethernet, usb)
Connection Status (disconnected available, connection)
Connection Cost (Roaming, metered connection)

- Android -> ConnectivityManager
- iOS -> NetworkReachability (deverloper.xamarin.com/samples/reachabilitysample)
- DeviceNetworkInformation

Connection status
Android -> BoradcastReceiver (monitor 'ConnectivityChange' events)
iOS -> check out sample
Windows -> hook to two events: one for network and other for TCP address changes

Connection Types

- Cellular (metered)
- Roaming Cellular
- WiFi
- No network available

How to know the network type:

- Android -> NetworkInfo
- iOS -> see reachability sample
- Windows -> NetworkInterfaceList

Android and Windows can detect "high cost" networks

For PCL: Nugget Connectivity Plugin (xam.plugin.connectivity)

For Xamarin Forms: native indicator using IsBusy property of the **Page**

## AND205

[Class Links and Resources](https://gist.github.com/kphillpotts/d3c8f4f58c81bf1a5122)
[Wireframing](https://developer.android.com/training/design-navigation/wireframing.html)
[Wireframe, Mockup, Prototype](http://www.mindinventory.com/blog/the-difference-between-wireframe-mockup-and-prototype/)
[Material io](https://material.io/)
[GenericPagerAdapter](https://gist.github.com/Krumelur/02ee7dc1349ca848d0ad)
[Back Navigation for Fragments](https://developer.android.com/training/implementing-navigation/temporal.html#back-fragments)
