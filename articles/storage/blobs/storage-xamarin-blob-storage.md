---
title: Jak korzystać z magazynu obiektów (Blob) z platformy Xamarin | Dokumenty firmy Microsoft
description: Biblioteka klienta usługi Azure Storage dla platformy Xamarin umożliwia deweloperom tworzenie aplikacji dla systemów iOS, Android i Ze Sklepu Windows za pomocą natywnych interfejsów użytkownika. W tym samouczku pokazano, jak używać platformy Xamarin do tworzenia aplikacji korzystającej z magazynu obiektów Blob platformy Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 8a1c91c8a8a59af26386e70e68e7c4fd93f5eaa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726336"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Jak korzystać z magazynu obiektów Blob z platformy Xamarin

Xamarin umożliwia deweloperom używać udostępnionej bazy kodu Języka C# do tworzenia aplikacji dla systemów iOS, Android i Ze Sklepu Windows za pomocą natywnych interfejsów użytkownika. W tym samouczku pokazano, jak używać magazynu obiektów Blob platformy Azure za pomocą aplikacji platformy Xamarin. Jeśli chcesz dowiedzieć się więcej o usłudze Azure Storage, przed zagłębieniem się w kod, zobacz [Wprowadzenie do usługi Microsoft Azure Storage](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Tworzenie nowej aplikacji platformy Xamarin

W tym samouczku będziemy tworzyć aplikację, która jest przeznaczona dla systemów Android, iOS i Windows. Ta aplikacja po prostu utworzyć kontener i przekazać obiekt blob do tego kontenera. Będziemy używać programu Visual Studio w systemie Windows, ale te same nauki mogą być stosowane podczas tworzenia aplikacji przy użyciu programu Xamarin Studio w systemie macOS.

Aby utworzyć aplikację, wykonaj następujące czynności:

1. Jeśli jeszcze tego nie zrobiłeś, pobierz i zainstaluj [xamarin dla programu Visual Studio](https://www.xamarin.com/download).
2. Otwórz program Visual Studio i utwórz pustą aplikację (przenośna natywna): **plik > nowy > project > cross-platform > pusta aplikacja (natywna przenośna).**
3. Kliknij prawym przyciskiem myszy rozwiązanie w okienku Eksplorator rozwiązań i wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**. Wyszukaj **system WindowsAzure.Storage** i zainstaluj najnowszą stabilną wersję dla wszystkich projektów w rozwiązaniu.
4. Skompiluj i uruchom swój projekt.

Teraz powinieneś mieć aplikację, która pozwala na kliknięcie przycisku, który zwiększa licznik.

## <a name="create-container-and-upload-blob"></a>Tworzenie kontenera i przekazywanie obiektów blob

Następnie w `(Portable)` ramach projektu dodasz kod `MyClass.cs`do pliku . Ten kod tworzy kontener i przekazuje obiekt blob do tego kontenera. `MyClass.cs`powinny wyglądać następująco:

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

Pamiętaj, aby zastąpić "your_account_name_here" i "your_account_key_here" rzeczywistą nazwą konta i kluczem konta.

Wszystkie projekty dotyczące systemów iOS, Android i Windows Phone mają odwołania do projektu przenośnego , co oznacza, że możesz napisać cały udostępniony kod w jednym miejscu i używać go we wszystkich projektach. Teraz można dodać następujący wiersz kodu do każdego projektu, aby rozpocząć korzystanie z:`MyClass.performBlobOperation()`

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

Teraz można uruchomić tę aplikację w emulatorze systemu Android lub Windows Phone. Można również uruchomić tę aplikację w emulatorze systemu iOS, ale będzie to wymagało komputera Mac. Szczegółowe instrukcje dotyczące tego, jak to zrobić, zapoznaj się z dokumentacją [dotyczącą łączenia programu Visual Studio z komputerem Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Po uruchomieniu aplikacji utworzy kontener `mycontainer` na koncie magazynu. Powinien zawierać obiekt blob, `myblob`który ma `Hello, world!`tekst, . Można to sprawdzić za pomocą [Eksploratora magazynu platformy Microsoft Azure](https://storageexplorer.com/).

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak utworzyć aplikację między platformami w platformie Xamarin, która używa usługi Azure Storage, w szczególności koncentrując się na jednym scenariuszu w magazynie obiektów blob. Jednak można zrobić o wiele więcej nie tylko magazyn obiektów Blob, ale także z tabeli, plików i magazynu kolejek. Aby dowiedzieć się więcej, zapoznaj się z poniższym artykułem:

* [Rozpoczynanie pracy z usługą Azure Blob Storage przy użyciu platformy .NET](storage-dotnet-how-to-use-blobs.md)
* [Wprowadzenie do usługi Azure Files](../files/storage-files-introduction.md)
* [Tworzenie oprogramowania dla usługi Azure Files przy użyciu platformy .NET](../files/storage-dotnet-how-to-use-files.md)
* [Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu platformy .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Wprowadzenie do usługi Azure Queue storage przy użyciu platformy .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
