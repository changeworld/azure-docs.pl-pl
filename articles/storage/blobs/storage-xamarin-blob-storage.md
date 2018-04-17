---
title: Jak używać magazynu obiektów (Blob) z Xamarin | Dokumentacja firmy Microsoft
description: Biblioteka klienta usługi Azure Storage dla platformy Xamarin umożliwia deweloperom tworzenie systemu iOS, Android i aplikacji ze Sklepu Windows z ich interfejsy macierzystego użytkownika. W tym samouczku pokazano, jak program Xamarin, aby utworzyć aplikację, która korzysta z magazynu obiektów Blob platformy Azure.
services: storage
documentationcenter: xamarin
author: michaelhauss
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.openlocfilehash: 31dbaeb1dd998d8d27af5eff0fa293117ef7f471
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Jak używać magazynu obiektów Blob z Xamarin

Aby utworzyć systemu iOS, Android i aplikacji ze Sklepu Windows za pomocą ich interfejsów użytkownika natywnego bazy kodu Xamarin umożliwiają deweloperom Użyj udostępnionych C#. Ten samouczek pokazuje, jak używać magazynu obiektów Blob platformy Azure przy użyciu aplikacji Xamarin. Jeśli chcesz dowiedzieć się więcej na temat usługi Azure Storage, przed rozpoczęciem pracy z kodem, zobacz [wprowadzenie do usługi Microsoft Azure Storage](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Tworzenie nowej aplikacji platformy Xamarin
W tym samouczku możemy utworzyć aplikację, która jest przeznaczony dla systemu Android, iOS i Windows. Ta aplikacja będzie po prostu utworzyć kontener i przekazać obiekt blob do tego kontenera. Będziemy używać programu Visual Studio w systemie Windows, ale sam learnings można zastosować podczas tworzenia aplikacji za pomocą platformy Xamarin Studio na macOS.

Wykonaj następujące kroki, aby utworzyć aplikację:

1. Jeśli nie jest jeszcze, Pobierz i zainstaluj [Xamarin dla Visual Studio](https://www.xamarin.com/download).
2. Otwórz program Visual Studio, a utworzona pusta aplikacja (Native przenośne): **Plik > Nowy > Projekt > i Platform > App(Native Portable) puste**.
3. Kliknij prawym przyciskiem myszy rozwiązanie, w okienku Eksplorator rozwiązań i wybierz **Zarządzaj pakietami NuGet dla rozwiązania**. Wyszukaj **WindowsAzure.Storage** i zainstaluj najnowszą wersję stabilna do wszystkich projektów w rozwiązaniu.
4. Tworzenie i uruchamianie projektu.

Teraz powinien mieć aplikację, która umożliwia kliknięcie przycisku, który zwiększa licznik.

## <a name="create-container-and-upload-blob"></a>Tworzenie kontenera i przekazania obiektu blob
Następnie w obszarze Twojej `(Portable)` projektu, dodasz kod służący do `MyClass.cs`. Ten kod tworzy kontener i wysyła do tego kontenera obiektu blob. `MyClass.cs` powinna wyglądać następująco:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

Upewnij się zastąpić "your_account_name_here" i "your_account_key_here" Nazwa rzeczywista konta i klucz konta. 

Z systemu iOS, Android i Windows Phone, wszystkie projekty odwołują się do projektu przenośnych — co oznacza, że pisania kodu udostępnionych w jednej Umieść i używać go we wszystkich projektach. Następujący wiersz kodu można teraz dodać do każdego projektu, aby uruchomić wykorzystaniu: `MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>Uruchamianie aplikacji
Można teraz uruchomić tę aplikację w emulatorze systemu Android i Windows Phone. Można również uruchomić tę aplikację w emulatorze systemu iOS, ale wymaga to Mac. Aby uzyskać szczegółowe instrukcje na temat to zrobić, przeczytaj dokumentację [łączenie programu Visual Studio na komputerze Mac.](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Po uruchomieniu aplikacji będzie utworzyć kontener `mycontainer` na koncie magazynu. Powinien on zawierać obiektów blob, `myblob`, który ma tekstu, `Hello, world!`. Można to sprawdzić za pomocą [Eksploratora usługi Microsoft Azure Storage](http://storageexplorer.com/).

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób tworzenia aplikacji i platform w Xamarin, która używa usługi Azure Storage, w szczególności koncentrujących się na jednym ze scenariuszy w magazynie obiektów Blob. Jednak możesz to zrobić dużo więcej nie tylko magazynu obiektów Blob, lecz również z tabeli, plików i magazynowania kolejki. Sprawdź następujące artykuły, aby dowiedzieć się więcej:

* [Rozpoczynanie pracy z usługą Azure Blob Storage przy użyciu platformy .NET](storage-dotnet-how-to-use-blobs.md)
* [Wprowadzenie do platformy Azure pliki](../files/storage-files-introduction.md)
* [Tworzenie oprogramowania dla usługi Azure Files przy użyciu platformy .NET](../files/storage-dotnet-how-to-use-files.md)
* [Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu platformy .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Rozpoczynanie pracy z usługą Azure Queue Storage przy użyciu platformy .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]