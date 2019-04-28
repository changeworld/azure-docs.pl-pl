---
title: Jak używać magazynu obiektów (Blob) ze środowiska Xamarin | Dokumentacja firmy Microsoft
description: Biblioteka klienta usługi Azure Storage dla platformy Xamarin umożliwia deweloperom tworzenie systemów iOS, Android i Windows Store apps za pomocą ich natywne interfejsy użytkownika. W tym samouczku pokazano, jak utworzyć aplikację, która korzysta z usługi Azure Blob storage za pomocą platformy Xamarin.
services: storage
documentationcenter: xamarin
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.openlocfilehash: b9c707dcc1628f685661f88aaed29612465a5469
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098086"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Jak używać magazynu obiektów Blob z platformy Xamarin

Xamarin umożliwia deweloperom korzystanie z udostępnionych C# bazy kodu w celu tworzenia systemów iOS, Android i Windows Store apps za pomocą ich natywne interfejsy użytkownika. W tym samouczku dowiesz się, jak używać usługi Azure Blob storage przy użyciu aplikacji Xamarin. Jeśli chcesz dowiedzieć się więcej o usłudze Azure Storage przed rozpoczęciem pracy z kodem, zobacz [wprowadzenie do usługi Microsoft Azure Storage](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Utwórz nową aplikację platformy Xamarin
W tym samouczku będziemy utworzona aplikacja, która dotyczy systemów Android, iOS i Windows. Ta aplikacja będzie po prostu utworzyć kontener i przekażesz obiekt blob do kontenera, w tym. Będziemy używać programu Visual Studio na Windows, ale sama nauka można stosować podczas tworzenia aplikacji za pomocą programu Xamarin Studio dla systemu macOS.

Wykonaj następujące kroki, aby utworzyć aplikację:

1. Jeśli jeszcze nie, Pobierz i zainstaluj [Xamarin dla programu Visual Studio](https://www.xamarin.com/download).
2. Otwórz program Visual Studio, a utworzona pusta aplikacja (natywny przenośny): **Plik > Nowy > Projekt > wiele Platform > Pusta aplikacja (natywna przenośna)**.
3. Kliknij prawym przyciskiem myszy rozwiązania w okienku Eksploratora rozwiązań, a następnie wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**. Wyszukaj **WindowsAzure.Storage** i zainstaluj najnowszą wersję stabilną do wszystkich projektów w rozwiązaniu.
4. Skompiluj i uruchom projekt.

Teraz masz aplikację, która pozwala na kliknij przycisk, który zwiększa licznik.

## <a name="create-container-and-upload-blob"></a>Tworzenie kontenera i przekazywanie obiektu blob
Następnie w obszarze usługi `(Portable)` projektu, należy dodać działał kod służący do `MyClass.cs`. Ten kod tworzy kontener i przekazywanie obiektu blob do tego kontenera. `MyClass.cs` powinien wyglądać następująco:

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

Upewnij się zastąpić "your_account_name_here" i "your_account_key_here" z rzeczywista nazwa konta i klucz konta. 

Usługi dla systemu iOS, Android i Windows Phone wszystkich projektów odwołujących się do projektu przenośnych - co oznacza, że piszesz udostępnionego kodu w jednym miejscu i go używać we wszystkich projektach. Można teraz dodać następujący wiersz kodu do każdego projektu, aby rozpocząć, wykorzystując: `MyClass.performBlobOperation()`

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
                // Release any cached data, images, etc. that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=391641

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
Teraz możesz uruchomić tę aplikację w emulatorze systemu Android lub Windows Phone. Można również uruchomić tę aplikację w emulatorze z systemem iOS, ale wymaga to Mac. Aby uzyskać szczegółowe instrukcje w tym celu, przeczytaj dokumentację [łączenie programu Visual Studio do komputera Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Po uruchomieniu aplikacji, zostanie utworzony kontener `mycontainer` na koncie magazynu. Powinien on zawierać obiekt blob, `myblob`, która zawiera tekst, `Hello, world!`. Można to sprawdzić za pomocą [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób tworzenia aplikacji dla wielu platform w środowisku Xamarin, która używa usługi Azure Storage, ze specjalnym uwzględnieniem jeden scenariusz w magazynie obiektów Blob. Jednak można zrobić o wiele bardziej nie tylko magazynu obiektów Blob, lecz również przy użyciu tabel, plików i Queue Storage. Sprawdź, czy się z następującymi artykułami, aby dowiedzieć się więcej:

* [Rozpoczynanie pracy z usługą Azure Blob Storage przy użyciu platformy .NET](storage-dotnet-how-to-use-blobs.md)
* [Wprowadzenie do usługi Azure Files](../files/storage-files-introduction.md)
* [Tworzenie oprogramowania dla usługi Azure Files przy użyciu platformy .NET](../files/storage-dotnet-how-to-use-files.md)
* [Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu platformy .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Rozpoczynanie pracy z usługą Azure Queue Storage przy użyciu platformy .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]