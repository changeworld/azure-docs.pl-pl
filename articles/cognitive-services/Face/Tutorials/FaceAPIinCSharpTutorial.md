---
title: 'Samouczek: wykrywanie i oznaczanie ramką twarzy na obrazie — interfejs API rozpoznawania twarzy, C#'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz aplikację dla systemu Windows, która korzysta z interfejsu API rozpoznawania twarzy do wykrywania i oznaczania ramką twarzy na obrazie.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: nolachar
ms.openlocfilehash: 657c471761c36de5095763623210909308f55c2a
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162615"
---
# <a name="tutorial-create-a-wpf-app-to-detect-and-frame-faces-in-an-image"></a>Samouczek: tworzenie aplikacji WPF wykrywającej i oznaczającej ramką twarze na obrazie

W tym samouczku utworzysz aplikację Windows Presentation Framework (WPF), która korzysta z usługi rozpoznawania twarzy za pośrednictwem jej biblioteki klienta platformy .NET. Aplikacja wykrywa twarze na obrazie, rysuje ramkę wokół każdej twarzy i wyświetla jej opis na pasku stanu. Kompletny przykładowy kod jest dostępny w witrynie GitHub na stronie z opisem [wykrywania i oznaczania ramką twarzy na obrazie w systemie Windows](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample).

![Zrzut ekranu przedstawiający wykryte twarze otoczone prostokątami](../Images/getting-started-cs-detected.png)

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> - Tworzenie aplikacji WPF
> - Instalowanie biblioteki klienta usługi rozpoznawania twarzy
> - Korzystanie z biblioteki klienta do wykrywania twarzy na obrazie
> - Rysowanie ramki wokół każdej wykrytej twarzy
> - Wyświetlanie opisu twarzy na pasku stanu

## <a name="prerequisites"></a>Wymagania wstępne

- Do uruchomienia przykładu potrzebny jest klucz subskrypcji. Klucze subskrypcji bezpłatnej wersji próbnej możesz uzyskać na stronie [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/). Dla programu Visual Studio 2017 wymagany jest pakiet roboczy Programowanie aplikacji klasycznych. W tym samouczku używany jest program Visual Studio 2017 Community Edition.
- Pakiet NuGet biblioteki klienta [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview). Pobieranie pakietu nie jest konieczne. Instrukcje instalacji znajdują się poniżej.

## <a name="create-the-visual-studio-solution"></a>Tworzenie rozwiązania programu Visual Studio

Wykonaj następujące kroki, aby utworzyć projekt aplikacji WPF dla systemu Windows.

1. Otwórz program Visual Studio i w menu **Plik** kliknij pozycję **Nowy**, a następnie pozycję **Projekt**.
   - W programie Visual Studio 2017 rozwiń węzeł **Zainstalowane**, a następnie pozycję **Inne języki**. Wybierz pozycję **Visual C#**, a następnie pozycję **Aplikacja WPF (.NET Framework)**.
   - W programie Visual Studio 2015 rozwiń węzeł **Zainstalowane**, a następnie pozycję **Szablony**. Wybierz pozycję **Visual C#**, a następnie pozycję **Aplikacja WPF**.
1. Nazwij aplikację **FaceTutorial**, a następnie kliknij przycisk **OK**.

## <a name="install-the-face-service-client-library"></a>Instalowanie biblioteki klienta usługi rozpoznawania twarzy

Wykonaj te instrukcje, aby zainstalować bibliotekę klienta.

1. Z menu **Narzędzia** wybierz pozycję **Menedżer pakietów NuGet**, a następnie pozycję **Konsola menedżera pakietów**.
1. W **Konsoli menedżera pakietów** wklej następujące polecenie, a następnie naciśnij klawisz **Enter**.

    `Install-Package Microsoft.Azure.CognitiveServices.Vision.Face -Version 2.2.0-preview`

## <a name="add-the-initial-code"></a>Dodawanie początkowego kodu

### <a name="mainwindowxaml"></a>MainWindow.xaml

Otwórz plik *MainWindow.xaml* (porada: zamień okienka przy użyciu **ikony strzałki w górę/w dół**) i zastąp jego zawartość następującym kodem. Ten kod xaml służy do tworzenia okna interfejsu użytkownika. Zwróć uwagę na procedury obsługi zdarzeń, `FacePhoto_MouseMove` i `BrowseButton_Click`.

```xml
<Window x:Class="FaceTutorial.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="700" Width="960">
    <Grid x:Name="BackPanel">
        <Image x:Name="FacePhoto" Stretch="Uniform" Margin="0,0,0,50" MouseMove="FacePhoto_MouseMove" />
        <DockPanel DockPanel.Dock="Bottom">
            <Button x:Name="BrowseButton" Width="72" Height="20" VerticalAlignment="Bottom" HorizontalAlignment="Left"
                    Content="Browse..."
                    Click="BrowseButton_Click" />
            <StatusBar VerticalAlignment="Bottom">
                <StatusBarItem>
                    <TextBlock Name="faceDescriptionStatusBar" />
                </StatusBarItem>
            </StatusBar>
        </DockPanel>
    </Grid>
</Window>
```

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Rozwiń pozycję *MainWindow.xaml*, a następnie otwórz plik *MainWindow.xaml.cs* i zastąp jego zawartość następującym kodem. Zignoruj czerwone faliste podkreślenia; znikną one po pierwszej kompilacji.

Pierwsze dwa wiersze importują przestrzenie nazw biblioteki klienta. Następnie tworzony jest element `FaceClient`, do którego przekazywany jest klucz subskrypcji. Region platformy Azure jest ustawiany w konstruktorze `MainWindow`. Dwie metody, `BrowseButton_Click` i `FacePhoto_MouseMove`, odpowiadają procedurom obsługi zdarzeń zadeklarowanym w pliku *MainWindow.xaml*.

Metoda `BrowseButton_Click` tworzy element `OpenFileDialog`, który pozwala użytkownikowi wybrać obraz w formacie jpg. Obraz jest odczytywany i wyświetlany w oknie głównym. Pozostały kod metody `BrowseButton_Click` i kod metody `FacePhoto_MouseMove` jest wstawiany w kolejnych krokach.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;

namespace FaceTutorial
{
    public partial class MainWindow : Window
    {
        // Replace <SubscriptionKey> with your valid subscription key.
        // For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // Replace or verify the region.
        //
        // You must use the same region as you used to obtain your subscription
        // keys. For example, if you obtained your subscription keys from the
        // westus region, replace "westcentralus" with "westus".
        //
        // NOTE: Free trial subscription keys are generated in the westcentralus
        // region, so if you are using a free trial subscription key, you should
        // not need to change this region.
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });

        IList<DetectedFace> faceList;   // The list of detected faces.
        String[] faceDescriptions;      // The list of descriptions for the detected faces.
        double resizeFactor;            // The resize factor for the displayed image.

        public MainWindow()
        {
            InitializeComponent();

            if (Uri.IsWellFormedUriString(faceEndpoint, UriKind.Absolute))
            {
                faceClient.Endpoint = faceEndpoint;
            }
            else
            {
                MessageBox.Show(faceEndpoint,
                    "Invalid URI", MessageBoxButton.OK, MessageBoxImage.Error);
                Environment.Exit(0);
            }
        }

        // Displays the image and calls UploadAndDetectFaces.
        private async void BrowseButton_Click(object sender, RoutedEventArgs e)
        {
            // Get the image file to scan from the user.
            var openDlg = new Microsoft.Win32.OpenFileDialog();

            openDlg.Filter = "JPEG Image(*.jpg)|*.jpg";
            bool? result = openDlg.ShowDialog(this);

            // Return if canceled.
            if (!(bool)result)
            {
                return;
            }

            // Display the image file.
            string filePath = openDlg.FileName;

            Uri fileUri = new Uri(filePath);
            BitmapImage bitmapSource = new BitmapImage();

            bitmapSource.BeginInit();
            bitmapSource.CacheOption = BitmapCacheOption.None;
            bitmapSource.UriSource = fileUri;
            bitmapSource.EndInit();

            FacePhoto.Source = bitmapSource;
        }

        // Displays the face description when the mouse is over a face rectangle.
        private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
        {
        }
    }
}
```

### <a name="insert-your-subscription-key-and-verify-or-change-the-region"></a>Wstawianie własnego klucza subskrypcji i weryfikowanie lub zmienianie regionu

- Znajdź następujący wiersz w pliku *MainWindow.xaml.cs* i zastąp element `<Subscription Key>` kluczem subskrypcji interfejsu API rozpoznawania twarzy:

    ```csharp
    private const string subscriptionKey = "<SubscriptionKey>";
    ```

- Znajdź następujący wiersz w pliku *MainWindow.xaml.cs* i zastąp lub sprawdź region platformy Azure skojarzony z Twoim kluczem subskrypcji:

    ```csharp
    private const string Endpoint =
        "https://westcentralus.api.cognitive.microsoft.com";
    ```

    Upewnij się, że lokalizacja jest taka sama jak miejsce uzyskania kluczy subskrypcji. Jeśli na przykład masz klucze subskrypcji uzyskane w regionie **westus**, zastąp ciąg `Westcentralus` ciągiem `Westus`.

    Jeśli Twoje klucze subskrypcji pochodzą z bezpłatnej wersji próbnej, regionem dla kluczy jest **westcentralus**, więc żadna zmiana nie jest potrzebna.

### <a name="test-the-app"></a>Testowanie aplikacji

Naciśnij pozycję **Uruchom** w menu, aby przetestować aplikację. Po otwarciu okna kliknij przycisk **Browse** (Przeglądaj) w lewym dolnym rogu. Zostanie otwarte okno dialogowe **File Open** (Otwieranie pliku), gdzie można przeglądać pliki i wybrać zdjęcie, które zostanie następnie wyświetlone w oknie.

![Zrzut ekranu przedstawiający niezmodyfikowany obraz z twarzami](../Images/getting-started-cs-ui.png)

## <a name="upload-an-image-to-detect-faces"></a>Przekazywanie obrazu w celu wykrywania twarzy

Najprostszym sposobem wykrycia twarzy jest wywołanie metody `FaceClient.Face.DetectWithStreamAsync`, która opakowuje metodę [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) interfejsu API na potrzeby przekazania obrazu lokalnego.

Wstaw następującą metodę w klasie `MainWindow`, poniżej metody `FacePhoto_MouseMove`.

Zostanie utworzona lista atrybutów twarzy do analizy, a przesłany plik obrazu zostanie wczytany do elementu `Stream`. Jedno i drugie jest przekazywane do wywołania `DetectWithStreamAsync`.

```csharp
// Uploads the image file and calls DetectWithStreamAsync.
private async Task<IList<DetectedFace>> UploadAndDetectFaces(string imageFilePath)
{
    // The list of Face attributes to return.
    IList<FaceAttributeType> faceAttributes =
        new FaceAttributeType[]
        {
            FaceAttributeType.Gender, FaceAttributeType.Age,
            FaceAttributeType.Smile, FaceAttributeType.Emotion,
            FaceAttributeType.Glasses, FaceAttributeType.Hair
        };

    // Call the Face API.
    try
    {
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            // The second argument specifies to return the faceId, while
            // the third argument specifies not to return face landmarks.
            IList<DetectedFace> faceList =
                await faceClient.Face.DetectWithStreamAsync(
                    imageFileStream, true, false, faceAttributes);
            return faceList;
        }
    }
    // Catch and display Face API errors.
    catch (APIErrorException f)
    {
        MessageBox.Show(f.Message);
        return new List<DetectedFace>();
    }
    // Catch and display all other errors.
    catch (Exception e)
    {
        MessageBox.Show(e.Message, "Error");
        return new List<DetectedFace>();
    }
}
```

## <a name="draw-rectangles-around-each-face"></a>Rysowanie prostokątów wokół każdej twarzy

Dodaj kod, który będzie rysował prostokąt wokół każdej wykrytej twarzy na obrazie.

W pliku *MainWindow.xaml.cs* dodaj modyfikator `async` do metody `BrowseButton_Click`.

```csharp
private async void BrowseButton_Click(object sender, RoutedEventArgs e)
```

Wstaw następujący kod na końcu metody `BrowseButton_Click`, po wierszu `FacePhoto.Source = bitmapSource`.

Lista wykrytych twarzy jest wypełniana przez wywołanie metody `UploadAndDetectFaces`. Następnie wokół każdej twarzy rysowany jest prostokąt, a zmodyfikowany obraz jest wyświetlany w oknie głównym.

```csharp
// Detect any faces in the image.
Title = "Detecting...";
faceList = await UploadAndDetectFaces(filePath);
Title = String.Format(
    "Detection Finished. {0} face(s) detected", faceList.Count);

if (faceList.Count > 0)
{
    // Prepare to draw rectangles around the faces.
    DrawingVisual visual = new DrawingVisual();
    DrawingContext drawingContext = visual.RenderOpen();
    drawingContext.DrawImage(bitmapSource,
        new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
    double dpi = bitmapSource.DpiX;
    resizeFactor = (dpi > 0) ? 96 / dpi : 1;
    faceDescriptions = new String[faceList.Count];

    for (int i = 0; i < faceList.Count; ++i)
    {
        DetectedFace face = faceList[i];

        // Draw a rectangle on the face.
        drawingContext.DrawRectangle(
            Brushes.Transparent,
            new Pen(Brushes.Red, 2),
            new Rect(
                face.FaceRectangle.Left * resizeFactor,
                face.FaceRectangle.Top * resizeFactor,
                face.FaceRectangle.Width * resizeFactor,
                face.FaceRectangle.Height * resizeFactor
                )
        );

        // Store the face description.
        faceDescriptions[i] = FaceDescription(face);
    }

    drawingContext.Close();

    // Display the image with the rectangle around the face.
    RenderTargetBitmap faceWithRectBitmap = new RenderTargetBitmap(
        (int)(bitmapSource.PixelWidth * resizeFactor),
        (int)(bitmapSource.PixelHeight * resizeFactor),
        96,
        96,
        PixelFormats.Pbgra32);

    faceWithRectBitmap.Render(visual);
    FacePhoto.Source = faceWithRectBitmap;

    // Set the status bar text.
    faceDescriptionStatusBar.Text =
        "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="describe-the-faces-in-the-image"></a>Opisywanie twarzy na obrazie

Dołącz następującą metodę do klasy `MainWindow`, poniżej metody `UploadAndDetectFaces`.

Metoda ta konwertuje atrybuty twarzy na ciąg opisujący twarz. Ten ciąg jest wyświetlany po zatrzymaniu wskaźnika myszy nad prostokątem otaczającym twarz.

```csharp
// Creates a string out of the attributes describing the face.
private string FaceDescription(DetectedFace face)
{
    StringBuilder sb = new StringBuilder();

    sb.Append("Face: ");

    // Add the gender, age, and smile.
    sb.Append(face.FaceAttributes.Gender);
    sb.Append(", ");
    sb.Append(face.FaceAttributes.Age);
    sb.Append(", ");
    sb.Append(String.Format("smile {0:F1}%, ", face.FaceAttributes.Smile * 100));

    // Add the emotions. Display all emotions over 10%.
    sb.Append("Emotion: ");
    Emotion emotionScores = face.FaceAttributes.Emotion;
    if (emotionScores.Anger >= 0.1f)
        sb.Append(String.Format("anger {0:F1}%, ", emotionScores.Anger * 100));
    if (emotionScores.Contempt >= 0.1f)
        sb.Append(String.Format("contempt {0:F1}%, ", emotionScores.Contempt * 100));
    if (emotionScores.Disgust >= 0.1f)
        sb.Append(String.Format("disgust {0:F1}%, ", emotionScores.Disgust * 100));
    if (emotionScores.Fear >= 0.1f)
        sb.Append(String.Format("fear {0:F1}%, ", emotionScores.Fear * 100));
    if (emotionScores.Happiness >= 0.1f)
        sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
    if (emotionScores.Neutral >= 0.1f)
        sb.Append(String.Format("neutral {0:F1}%, ", emotionScores.Neutral * 100));
    if (emotionScores.Sadness >= 0.1f)
        sb.Append(String.Format("sadness {0:F1}%, ", emotionScores.Sadness * 100));
    if (emotionScores.Surprise >= 0.1f)
        sb.Append(String.Format("surprise {0:F1}%, ", emotionScores.Surprise * 100));

    // Add glasses.
    sb.Append(face.FaceAttributes.Glasses);
    sb.Append(", ");

    // Add hair.
    sb.Append("Hair: ");

    // Display baldness confidence if over 1%.
    if (face.FaceAttributes.Hair.Bald >= 0.01f)
        sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

    // Display all hair color attributes over 10%.
    IList<HairColor> hairColors = face.FaceAttributes.Hair.HairColor;
    foreach (HairColor hairColor in hairColors)
    {
        if (hairColor.Confidence >= 0.1f)
        {
            sb.Append(hairColor.Color.ToString());
            sb.Append(String.Format(" {0:F1}% ", hairColor.Confidence * 100));
        }
    }

    // Return the built string.
    return sb.ToString();
}
```

## <a name="display-the-face-description"></a>Wyświetlanie opisu twarzy

Zastąp metodę `FacePhoto_MouseMove` następującym kodem.

Ta procedura obsługi zdarzeń wyświetla ciąg opisu twarzy po zatrzymaniu wskaźnika myszy nad prostokątem otaczającym twarz.

```csharp
private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
{
    // If the REST call has not completed, return.
    if (faceList == null)
        return;

    // Find the mouse position relative to the image.
    Point mouseXY = e.GetPosition(FacePhoto);

    ImageSource imageSource = FacePhoto.Source;
    BitmapSource bitmapSource = (BitmapSource)imageSource;

    // Scale adjustment between the actual size and displayed size.
    var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

    // Check if this mouse position is over a face rectangle.
    bool mouseOverFace = false;

    for (int i = 0; i < faceList.Count; ++i)
    {
        FaceRectangle fr = faceList[i].FaceRectangle;
        double left = fr.Left * scale;
        double top = fr.Top * scale;
        double width = fr.Width * scale;
        double height = fr.Height * scale;

        // Display the face description if the mouse is over this face rectangle.
        if (mouseXY.X >= left && mouseXY.X <= left + width &&
            mouseXY.Y >= top  && mouseXY.Y <= top + height)
        {
            faceDescriptionStatusBar.Text = faceDescriptions[i];
            mouseOverFace = true;
            break;
        }
    }

    // String to display when the mouse is not over a face rectangle.
    if (!mouseOverFace)
        faceDescriptionStatusBar.Text =
            "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="run-the-app"></a>Uruchamianie aplikacji

Uruchom aplikację i przejdź do obrazu zawierającego twarz. Zaczekaj kilka sekund, aby umożliwić usłudze rozpoznawania twarzy udzielenie odpowiedzi. Po tym zobaczysz czerwony prostokąt na twarzach na obrazie. Podczas przesuwania wskaźnika myszy nad prostokątem otaczającym twarz opis tej twarzy jest wyświetlany na pasku stanu.

![Zrzut ekranu przedstawiający wykryte twarze otoczone prostokątami](../Images/getting-started-cs-detected.png)

## <a name="summary"></a>Podsumowanie

W tym samouczku przedstawiono podstawowy proces korzystania z biblioteki klienta usługi rozpoznawania twarzy i utworzono aplikację do wyświetlania i oznaczania ramkami twarzy na obrazie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wykrywania i używania charakterystycznych elementów twarzy.

> [!div class="nextstepaction"]
> [Jak wykrywać twarze na obrazie](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
