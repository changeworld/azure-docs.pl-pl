---
title: Powierzchni interfejsu API języka C# samouczek | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Tworzenie prostej aplikacji systemu Windows, którym kognitywnych API rozpoznawania emocji — warstwa usług do wykrywania kroje obrazu przy określaniu powierzchni.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ac4f247eb38cafc5582c1ffbb3752e049f0305ba
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347621"
---
# <a name="getting-started-with-face-api-in-c35-tutorial"></a>Wprowadzenie do powierzchni interfejsu API w języku C&#35; samouczka

W tym samouczku utworzysz aplikacji WPF systemu Windows, która używa interfejsu API twarzy na obrazie. Aplikacja wykrywa kroje obrazu, rysuje ramki wokół każdego krój i wyświetla opis kroju na pasku stanu.

![GettingStartCSharpScreenshot](../Images/getting-started-cs-detected.png)

## <a name="Preparation"></a>Przygotowanie

Aby korzystać z tego samouczka, potrzebne są następujące wymagania wstępne:

- Upewnij się, że jest zainstalowany program Visual Studio 2015 lub nowszego.

## <a name="step1"></a>Krok 1: Subskrybować powierzchni interfejsu API i Pobierz klucz subskrypcji

Przed rozpoczęciem korzystania z interfejsu API krój, musisz zarejestrować się, do subskrybowania powierzchni interfejsu API w portalu usługi kognitywnych firmy Microsoft. Zobacz [subskrypcje](https://azure.microsoft.com/try/cognitive-services/). Klucz podstawowy lub pomocniczy subskrypcji można użyć w tym samouczku.

## <a name="step2"></a>Krok 2: Tworzenie rozwiązań programu Visual Studio

W tym kroku utworzysz projekt aplikacji Windows WPF do tworzenia podstawowej aplikacji, aby wybrać i wyświetlić obraz. Wykonaj te instrukcje:

1. Otwórz program Visual Studio.
1. Z **pliku** menu, kliknij przycisk **nowy**, następnie **projektu**.
1. Wybierz WPF dla aplikacji w **nowy projekt** okno dialogowe.

   W programie Visual Studio 2015, rozwiń węzeł **zainstalowana** &gt; **szablony** &gt; **Visual C#** &gt; **Windows** &gt; **Klasyczny pulpit** &gt; i wybierz **aplikacji WPF**.

   W programie Visual Studio 2017 r, rozwiń węzeł **zainstalowana** &gt; **szablony** &gt; **Visual C#** &gt; **Windows Classic Pulpitu** &gt; i wybierz **WPF aplikacji (.NET Framework)**.
1. Nazwa aplikacji **FaceTutorial**, następnie kliknij przycisk **OK**.

   ![Okno dialogowe Nowy projekt z wybrane aplikacji WPF](../Images/vs2017-new-project.png)

1. Zlokalizuj **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt (**FaceTutorial** w tym przypadku), a następnie kliknij przycisk **Zarządzaj pakietami NuGet**.
1. W **Menedżera pakietów NuGet** wybierz **nuget.org** jako źródła pakietu.
1. Wyszukaj **Newtonsoft.Json**, następnie **zainstalować**. (W programie Visual Studio 2017 r, najpierw kliknij **Przeglądaj** karcie następnie **wyszukiwania**).

   ![GettingStartCSharpPackageManager](../Images/install-nsoft-json.png)

## <a name="step3"></a>Krok 3: Konfigurowanie biblioteki klienckiej powierzchni interfejsu API

Powierzchnia interfejsu API jest chmury interfejsu API, które można wywołać za pomocą żądania HTTPS REST. Dla łatwość użycia w aplikacjach .NET biblioteki klienta .NET hermetyzuje żądań powierzchni interfejsu API REST. W tym przykładzie używamy biblioteki klienckiej uprościć pracę.

Wykonaj te instrukcje, aby skonfigurować biblioteki klienta:

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt (**FaceTutorial** w tym przypadku), a następnie kliknij przycisk **Zarządzaj pakietami NuGet**.
1. W **Menedżera pakietów NuGet** wybierz **nuget.org** jako źródła pakietu.
1. Wyszukaj **Microsoft.ProjectOxford.Face**, następnie **zainstalować**. (W programie Visual Studio 2017 r, najpierw kliknij **Przeglądaj** karcie następnie **wyszukiwania**).

   ![GettingStartCSharpPackageManagerSDK](../Images/install-project-oxford-face.png)

1. W **Eksploratora rozwiązań**, Sprawdź odwołania projektu. Odwołania **Microsoft.ProjectOxford.Common**, **Microsoft.ProjectOxford.Face**, i **Newtonsoft.Json** są dodawane automatycznie podczas instalacji zakończy się pomyślnie.

   ![GetStartedCSharp CheckInstallation.png](../Images/GetStartedCSharp-CheckInstallation.png)

## <a name="step3"></a>Krok 4: Skopiuj i Wklej kod początkowej

1. Otwórz MainWindow.xaml i Zamień istniejący kod następujący kod, aby utworzyć okno interfejsu użytkownika:

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

1. Otwórz MainWindow.xaml.cs i zastąpić istniejący kod następującym kodem:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading.Tasks;
    using System.Windows;
    using System.Windows.Input;
    using System.Windows.Media;
    using System.Windows.Media.Imaging;
    using Microsoft.ProjectOxford.Common.Contract;
    using Microsoft.ProjectOxford.Face;
    using Microsoft.ProjectOxford.Face.Contract;

    namespace FaceTutorial
    {
        public partial class MainWindow : Window
        {
            // Replace the first parameter with your valid subscription key.
            //
            // Replace or verify the region in the second parameter.
            //
            // You must use the same region in your REST API call as you used to obtain your subscription keys.
            // For example, if you obtained your subscription keys from the westus region, replace
            // "westcentralus" in the URI below with "westus".
            //
            // NOTE: Free trial subscription keys are generated in the westcentralus region, so if you are using
            // a free trial subscription key, you should not need to change this region.
            private readonly IFaceServiceClient faceServiceClient =
                new FaceServiceClient("<Subscription Key>", "https://westcentralus.api.cognitive.microsoft.com/face/v1.0");

            Face[] faces;                   // The list of detected faces.
            String[] faceDescriptions;      // The list of descriptions for the detected faces.
            double resizeFactor;            // The resize factor for the displayed image.
            
            public MainWindow()
            {
                InitializeComponent();
            }
            
            // Displays the image and calls Detect Faces.

            private void BrowseButton_Click(object sender, RoutedEventArgs e)
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

1. Wstaw klucz subskrypcji i sprawdź regionu.

    Znajdź ten wiersz w pliku MainWindow.xaml.cs (linie 28 i 29):

    ```csharp
    private readonly IFaceServiceClient faceServiceClient =
            new FaceServiceClient("<Subscription Key>", "https://westcentralus.api.cognitive.microsoft.com/face/v1.0");
    ```

    Zastąp `<Subscription Key>` w pierwszym parametrze kluczem subskrypcji powierzchni interfejsu API z kroku 1.

    Sprawdź również, drugi parametr upewnij się, że używasz lokalizacji, w którym uzyskać klucze subskrypcji. Jeśli Twoje klucze subskrypcji został uzyskany z regionu westus, na przykład, zastąp "**westcentralus**"w identyfikatorze URI z"**westus**".

    Odebranie klucze subskrypcji przy użyciu bezpłatnej wersji próbnej, region dla swoich kluczy jest **westcentralus**, więc zmiana nie jest wymagane.

Teraz aplikacji można wyszukać zdjęcie i wyświetl ją w oknie.

![GettingStartCSharpUI](../Images/getting-started-cs-ui.png)

## <a name="step4"></a>Krok 5: Przekaż obrazy do wykrywania powierzchni

Najbardziej oczywistym sposobem wykryć kroje jest przez wywołanie metody [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) interfejsu API, przekazując bezpośrednio do pliku obrazu.
Podczas korzystania z biblioteki klienta, można to zrobić za pomocą metody asynchronicznej DetectAsync FaceServiceClient.
Każdego zwróconego krój zawiera prostokąt wskazująca lokalizacji, w połączeniu z serii krój opcjonalne atrybuty.

Wstaw następujący kod w **właściwości MainWindow** klasy:

```csharp
// Uploads the image file and calls Detect Faces.

private async Task<Face[]> UploadAndDetectFaces(string imageFilePath)
{
    // The list of Face attributes to return.
    IEnumerable<FaceAttributeType> faceAttributes =
        new FaceAttributeType[] { FaceAttributeType.Gender, FaceAttributeType.Age, FaceAttributeType.Smile, FaceAttributeType.Emotion, FaceAttributeType.Glasses, FaceAttributeType.Hair };

    // Call the Face API.
    try
    {
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            Face[] faces = await faceServiceClient.DetectAsync(imageFileStream, returnFaceId: true, returnFaceLandmarks:false, returnFaceAttributes: faceAttributes);
            return faces;
        }
    }
    // Catch and display Face API errors.
    catch (FaceAPIException f)
    {
        MessageBox.Show(f.ErrorMessage, f.ErrorCode);
        return new Face[0];
    }
    // Catch and display all other errors.
    catch (Exception e)
    {
        MessageBox.Show(e.Message, "Error");
        return new Face[0];
    }
}
```

## <a name="step5"></a>Krok 6: Oznacz skierowany w obrazie

W tym kroku będziemy połączyć wszystkie poprzednie kroki i oznacz wykryto kroje w obrazie.

W **MainWindow.xaml.cs**, modyfikatora "async" można dodać **BrowseButton_Click** metody:

```csharp
private async void BrowseButton_Click(object sender, RoutedEventArgs e)
```

Wstaw następujący kod na końcu **BrowseButton_Click** obsługi zdarzeń:

```csharp
// Detect any faces in the image.
Title = "Detecting...";
faces = await UploadAndDetectFaces(filePath);
Title = String.Format("Detection Finished. {0} face(s) detected", faces.Length);

if (faces.Length > 0)
{
    // Prepare to draw rectangles around the faces.
    DrawingVisual visual = new DrawingVisual();
    DrawingContext drawingContext = visual.RenderOpen();
    drawingContext.DrawImage(bitmapSource,
        new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
    double dpi = bitmapSource.DpiX;
    resizeFactor = 96 / dpi;
    faceDescriptions = new String[faces.Length];

    for (int i = 0; i < faces.Length; ++i)
    {
        Face face = faces[i];

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
    faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="step6"></a>Krok 7: Opisano kroje w obrazie

W tym kroku firma Microsoft bada właściwości krój i wygeneruj ciąg opisujący kroju. Ten ciąg wyświetlany, gdy wskaźnik myszy jest przesuwany nad prostokąt twarzy na obrazie.

I Dodaj tę metodę, aby **właściwości MainWindow** klasy w celu przekonwertowania na ciąg krój szczegóły:

```csharp
// Returns a string that describes the given face.

private string FaceDescription(Face face)
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
    EmotionScores emotionScores = face.FaceAttributes.Emotion;
    if (emotionScores.Anger     >= 0.1f) sb.Append(String.Format("anger {0:F1}%, ",     emotionScores.Anger * 100));
    if (emotionScores.Contempt  >= 0.1f) sb.Append(String.Format("contempt {0:F1}%, ",  emotionScores.Contempt * 100));
    if (emotionScores.Disgust   >= 0.1f) sb.Append(String.Format("disgust {0:F1}%, ",   emotionScores.Disgust * 100));
    if (emotionScores.Fear      >= 0.1f) sb.Append(String.Format("fear {0:F1}%, ",      emotionScores.Fear * 100));
    if (emotionScores.Happiness >= 0.1f) sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
    if (emotionScores.Neutral   >= 0.1f) sb.Append(String.Format("neutral {0:F1}%, ",   emotionScores.Neutral * 100));
    if (emotionScores.Sadness   >= 0.1f) sb.Append(String.Format("sadness {0:F1}%, ",   emotionScores.Sadness * 100));
    if (emotionScores.Surprise  >= 0.1f) sb.Append(String.Format("surprise {0:F1}%, ",  emotionScores.Surprise * 100));

    // Add glasses.
    sb.Append(face.FaceAttributes.Glasses);
    sb.Append(", ");

    // Add hair.
    sb.Append("Hair: ");

    // Display baldness confidence if over 1%.
    if (face.FaceAttributes.Hair.Bald >= 0.01f)
        sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

    // Display all hair color attributes over 10%.
    HairColor[] hairColors = face.FaceAttributes.Hair.HairColor;
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

## <a name="step6"></a>Krok 8: Wyświetl opis krój

Zastąp **FacePhoto_MouseMove** metodę z następującym kodem:

```csharp
private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
{
    // If the REST call has not completed, return from this method.
    if (faces == null)
        return;

    // Find the mouse position relative to the image.
    Point mouseXY = e.GetPosition(FacePhoto);

    ImageSource imageSource = FacePhoto.Source;
    BitmapSource bitmapSource = (BitmapSource)imageSource;

    // Scale adjustment between the actual size and displayed size.
    var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

    // Check if this mouse position is over a face rectangle.
    bool mouseOverFace = false;

    for (int i = 0; i < faces.Length; ++i)
    {
        FaceRectangle fr = faces[i].FaceRectangle;
        double left = fr.Left * scale;
        double top = fr.Top * scale;
        double width = fr.Width * scale;
        double height = fr.Height * scale;

        // Display the face description for this face if the mouse is over this face rectangle.
        if (mouseXY.X >= left && mouseXY.X <= left + width && mouseXY.Y >= top && mouseXY.Y <= top + height)
        {
            faceDescriptionStatusBar.Text = faceDescriptions[i];
            mouseOverFace = true;
            break;
        }
    }

    // If the mouse is not over a face rectangle.
    if (!mouseOverFace)
        faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
}
```

Uruchomienie tej aplikacji i wyszukaj obraz zawierającego powierzchnię. Poczekaj kilka sekund, chmury interfejsu API na potrzeby odpowiedzi. Po wykonaniu tej zobaczysz czerwony prostokąt na powierzchni w obrazie. Opis kroju przez przesuwanie wskaźnika myszy nad prostokąt krój, pojawi się na pasku stanu:

![GettingStartCSharpScreenshot](../Images/getting-started-cs-detected.png)

## <a name="summary"></a> Podsumowanie

W tym samouczku przedstawiono przebieg przy użyciu interfejsu API krój i utworzyć aplikację w celu wyświetlania twarzy obrazy. Aby uzyskać więcej informacji o szczegóły powierzchni interfejsu API, zobacz instrukcje i [dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="fullsource"></a> Pełne źródło

Pełna źródła dla aplikacji systemu Windows WPF jest przedstawionych w tym miejscu.

MainWindow.xaml:

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

MainWindow.xaml.cs:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using Microsoft.ProjectOxford.Common.Contract;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;

namespace FaceTutorial
{
    public partial class MainWindow : Window
    {
        // Replace the first parameter with your valid subscription key.
        //
        // Replace or verify the region in the second parameter.
        //
        // You must use the same region in your REST API call as you used to obtain your subscription keys.
        // For example, if you obtained your subscription keys from the westus region, replace
        // "westcentralus" in the URI below with "westus".
        //
        // NOTE: Free trial subscription keys are generated in the westcentralus region, so if you are using
        // a free trial subscription key, you should not need to change this region.
        private readonly IFaceServiceClient faceServiceClient =
            new FaceServiceClient("<Subscription Key>", "https://westcentralus.api.cognitive.microsoft.com/face/v1.0");

        Face[] faces;                   // The list of detected faces.
        String[] faceDescriptions;      // The list of descriptions for the detected faces.
        double resizeFactor;            // The resize factor for the displayed image.

        public MainWindow()
        {
            InitializeComponent();
        }

        // Displays the image and calls Detect Faces.

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

            // Detect any faces in the image.
            Title = "Detecting...";
            faces = await UploadAndDetectFaces(filePath);
            Title = String.Format("Detection Finished. {0} face(s) detected", faces.Length);

            if (faces.Length > 0)
            {
                // Prepare to draw rectangles around the faces.
                DrawingVisual visual = new DrawingVisual();
                DrawingContext drawingContext = visual.RenderOpen();
                drawingContext.DrawImage(bitmapSource,
                    new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
                double dpi = bitmapSource.DpiX;
                resizeFactor = 96 / dpi;
                faceDescriptions = new String[faces.Length];

                for (int i = 0; i < faces.Length; ++i)
                {
                    Face face = faces[i];

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
                faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
            }
        }

        // Displays the face description when the mouse is over a face rectangle.

        private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
        {
            // If the REST call has not completed, return from this method.
            if (faces == null)
                return;

            // Find the mouse position relative to the image.
            Point mouseXY = e.GetPosition(FacePhoto);

            ImageSource imageSource = FacePhoto.Source;
            BitmapSource bitmapSource = (BitmapSource)imageSource;

            // Scale adjustment between the actual size and displayed size.
            var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

            // Check if this mouse position is over a face rectangle.
            bool mouseOverFace = false;

            for (int i = 0; i < faces.Length; ++i)
            {
                FaceRectangle fr = faces[i].FaceRectangle;
                double left = fr.Left * scale;
                double top = fr.Top * scale;
                double width = fr.Width * scale;
                double height = fr.Height * scale;

                // Display the face description for this face if the mouse is over this face rectangle.
                if (mouseXY.X >= left && mouseXY.X <= left + width && mouseXY.Y >= top && mouseXY.Y <= top + height)
                {
                    faceDescriptionStatusBar.Text = faceDescriptions[i];
                    mouseOverFace = true;
                    break;
                }
            }

            // If the mouse is not over a face rectangle.
            if (!mouseOverFace)
                faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
        }

        // Uploads the image file and calls Detect Faces.

        private async Task<Face[]> UploadAndDetectFaces(string imageFilePath)
        {
            // The list of Face attributes to return.
            IEnumerable<FaceAttributeType> faceAttributes =
                new FaceAttributeType[] { FaceAttributeType.Gender, FaceAttributeType.Age, FaceAttributeType.Smile, FaceAttributeType.Emotion, FaceAttributeType.Glasses, FaceAttributeType.Hair };

            // Call the Face API.
            try
            {
                using (Stream imageFileStream = File.OpenRead(imageFilePath))
                {
                    Face[] faces = await faceServiceClient.DetectAsync(imageFileStream, returnFaceId: true, returnFaceLandmarks: false, returnFaceAttributes: faceAttributes);
                    return faces;
                }
            }
            // Catch and display Face API errors.
            catch (FaceAPIException f)
            {
                MessageBox.Show(f.ErrorMessage, f.ErrorCode);
                return new Face[0];
            }
            // Catch and display all other errors.
            catch (Exception e)
            {
                MessageBox.Show(e.Message, "Error");
                return new Face[0];
            }
        }

        // Returns a string that describes the given face.

        private string FaceDescription(Face face)
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
            EmotionScores emotionScores = face.FaceAttributes.Emotion;
            if (emotionScores.Anger >= 0.1f) sb.Append(String.Format("anger {0:F1}%, ", emotionScores.Anger * 100));
            if (emotionScores.Contempt >= 0.1f) sb.Append(String.Format("contempt {0:F1}%, ", emotionScores.Contempt * 100));
            if (emotionScores.Disgust >= 0.1f) sb.Append(String.Format("disgust {0:F1}%, ", emotionScores.Disgust * 100));
            if (emotionScores.Fear >= 0.1f) sb.Append(String.Format("fear {0:F1}%, ", emotionScores.Fear * 100));
            if (emotionScores.Happiness >= 0.1f) sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
            if (emotionScores.Neutral >= 0.1f) sb.Append(String.Format("neutral {0:F1}%, ", emotionScores.Neutral * 100));
            if (emotionScores.Sadness >= 0.1f) sb.Append(String.Format("sadness {0:F1}%, ", emotionScores.Sadness * 100));
            if (emotionScores.Surprise >= 0.1f) sb.Append(String.Format("surprise {0:F1}%, ", emotionScores.Surprise * 100));

            // Add glasses.
            sb.Append(face.FaceAttributes.Glasses);
            sb.Append(", ");

            // Add hair.
            sb.Append("Hair: ");

            // Display baldness confidence if over 1%.
            if (face.FaceAttributes.Hair.Bald >= 0.01f)
                sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

            // Display all hair color attributes over 10%.
            HairColor[] hairColors = face.FaceAttributes.Hair.HairColor;
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
    }
}
```

## <a name="related"></a> Następne kroki

- [Wprowadzenie do korzystania z powierzchni interfejsu API w języku Java dla systemu Android](FaceAPIinJavaForAndroidTutorial.md)
- [Wprowadzenie do powierzchni interfejsu API w języku Python](FaceAPIinPythonTutorial.md)
