---
title: 'Samouczek: wykrywanie i wyświetlanie danych twarzy na obrazie za pomocą zestawu SDK platformy .NET'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz aplikację dla systemu Windows, która będzie używać interfejsu API rozpoznawania twarzy do wykrywania i oznaczania ramką twarzy na obrazie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: pafarley
ms.openlocfilehash: 6a60afc45894518f92115976876ddd50efa1e410
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815350"
---
# <a name="tutorial-create-a-wpf-app-to-display-face-data-in-an-image"></a>Samouczek: tworzenie aplikacji WPF do wyświetlania danych twarzy na obrazie

W tym samouczku nauczysz się, jak używać interfejsu API rozpoznawania twarzy na platformie Azure za pośrednictwem klienckiego zestawu SDK platformy .NET, aby wykrywać twarze na obrazie i prezentować te dane w interfejsie użytkownika. Utworzysz prostą aplikację Windows Presentation Framework (WPF), która będzie wykrywać twarze, rysować ramkę wokół każdej twarzy i wyświetlać opis twarzy na pasku stanu. 

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> - Tworzenie aplikacji WPF
> - Instalowanie biblioteki klienta interfejsu API rozpoznawania twarzy
> - Korzystanie z biblioteki klienta do wykrywania twarzy na obrazie
> - Rysowanie ramki wokół każdej wykrytej twarzy
> - Wyświetlanie opisu wyróżnionej twarzy na pasku stanu

![Zrzut ekranu przedstawiający wykryte twarze otoczone prostokątami](../Images/getting-started-cs-detected.png)

Kompletny przykładowy kod jest dostępny w repozytorium [Cognitive Face CSharp sample](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) (Przykład rozpoznawania twarzy w języku C# za pomocą usług Cognitive) w witrynie GitHub.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). 


## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji interfejsu API rozpoznawania twarzy. Klucz subskrypcji bezpłatnej wersji próbnej możesz uzyskać na stronie [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Możesz też wykonać instrukcje z tematu [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Tworzenie konta usług Cognitive Services), aby subskrybować usługę interfejsu API rozpoznawania twarzy i uzyskać klucz.
- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

Wykonaj następujące kroki, aby utworzyć nowy projekt aplikacji WPF.

1. W programie Visual Studio otwórz okno dialogowe Nowy projekt. Rozwiń węzeł **Zainstalowane** i węzeł **Visual C#**, a następnie wybierz pozycję **Aplikacja WPF (.NET Framework)**.
1. Nazwij aplikację **FaceTutorial**, a następnie kliknij przycisk **OK**.
1. Pobierz wymagane pakiety NuGet. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz pozycję **Zarządzaj pakietami NuGet**, a następnie znajdź i zainstaluj następujący pakiet:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-the-initial-code"></a>Dodawanie początkowego kodu

W tej sekcji dodasz podstawową strukturę aplikacji bez funkcji specyficznych dla rozpoznawania twarzy.

### <a name="create-the-ui"></a>Tworzenie interfejsu użytkownika

Otwórz plik *MainWindow.xaml* i zamień jego zawartość na następujący kod &mdash; umożliwi to utworzenie okna interfejsu użytkownika. Zwróć uwagę, że `FacePhoto_MouseMove` i `BrowseButton_Click` to procedury obsługi zdarzeń, które zdefiniujesz później.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?range=1-18)]

### <a name="create-the-main-class"></a>Tworzenie głównej klasy

Otwórz plik *MainWindow.xaml.cs* i dodaj przestrzenie nazw biblioteki klienta oraz inne wymagane przestrzenie nazw. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=1-12)]

Następnie wstaw poniższy kod w klasie **MainWindow**. Spowoduje to utworzenie wystąpienia elementu **FaceClient** przy użyciu klucza subskrypcji, który musisz wprowadzić samodzielnie. Musisz także ustawić ciąg regionu w elemencie `faceEndpoint` na poprawny region dla Twojej subskrypcji (zobacz [dokumentację interfejsu API rozpoznawania twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), aby zapoznać się z listą wszystkich punktów końcowych regionów).

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=18-46)]

Następnie wklej poniższy kod w metodzie **MainWindow**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=50-61)]

Na koniec dodaj do klasy metody **BrowseButton_Click** i **FacePhoto_MouseMove**. Odpowiadają one procedurom obsługi zdarzeń zadeklarowanym w pliku *MainWindow.xaml*. Metoda **BrowseButton_Click** tworzy element **OpenFileDialog**, który pozwala użytkownikowi wybrać obraz jpg. Następnie wyświetla ona obraz w głównym oknie. Wstawisz pozostały kod metod **BrowseButton_Click** i **FacePhoto_MouseMove** w kolejnych krokach. Zwróć też uwagę na odwołanie `faceList` &mdash; listę obiektów **DetectedFace**. W tym miejscu aplikacja będzie przechowywać i wywoływać rzeczywiste dane twarzy.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=64-90,146)]

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=148-150,187)]

### <a name="try-the-app"></a>Testowanie aplikacji

Naciśnij pozycję **Uruchom** w menu, aby przetestować aplikację. Po otwarciu okna aplikacji kliknij pozycję **Browse** (Przeglądaj) w lewym dolnym rogu. Powinno pojawić się okno dialogowe **Otwieranie pliku**. Wybierz obraz z systemu plików i sprawdź, czy został wyświetlony w oknie. Następnie zamknij aplikację i przejdź do kolejnego kroku.

![Zrzut ekranu przedstawiający niezmodyfikowany obraz z twarzami](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Przekazywanie obrazu i wykrywanie twarzy

Aplikacja będzie wykrywać twarze, wywołując metodę **FaceClient.Face.DetectWithStreamAsync**, która opakowuje interfejs API REST [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) na potrzeby przekazywania obrazu lokalnego.

Wstaw następującą metodę w klasie **MainWindow** poniżej metody **FacePhoto_MouseMove**. Spowoduje to zdefiniowanie listy atrybutów twarzy do pobrania oraz odczytanie przesłanego pliku obrazu do elementu **Stream**. Następnie oba te obiekty zostaną przekazane do wywołania metody **DetectWithStreamAsync**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=189-226)]

## <a name="draw-rectangles-around-faces"></a>Rysowanie prostokątów wokół twarzy

Dodasz teraz kod, który będzie rysował prostokąt wokół każdej wykrytej twarzy na obrazie. W klasie **MainWindow** wstaw następujący kod na końcu metody **BrowseButton_Click**, poniżej wiersza `FacePhoto.Source = bitmapSource`. Spowoduje to wypełnienie listy wykrytych twarzy z wywołania metody **UploadAndDetectFaces**. Następnie zostanie narysowany prostokąt wokół każdej twarzy, a zmodyfikowany obraz zostanie wyświetlony w głównym oknie.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=92-145)]

## <a name="describe-the-faces"></a>Opisywanie twarzy

Dodaj następującą metodę do klasy **MainWindow**, poniżej metody **UploadAndDetectFaces**. Spowoduje to przekonwertowanie pobranych atrybutów twarzy na ciąg opisujący twarz.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=228-286)]

## <a name="display-the-face-description"></a>Wyświetlanie opisu twarzy

Dodaj następujący kod do metody **FacePhoto_MouseMove**. Ta procedura obsługi zdarzeń wyświetla ciąg opisu twarzy w elemencie `faceDescriptionStatusBar` po zatrzymaniu kursora na prostokącie wykrytej twarzy.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=151-186)]


## <a name="run-the-app"></a>Uruchamianie aplikacji

Uruchom aplikację i przejdź do obrazu zawierającego twarz. Zaczekaj kilka sekund, aby umożliwić usłudze rozpoznawania twarzy udzielenie odpowiedzi. Wokół każdej twarzy na obrazie powinien pojawić się czerwony prostokąt. Gdy przesuniesz wskaźnik myszy na prostokąt otaczający twarz, powinien pojawić się opis tej twarzy na pasku stanu.

![Zrzut ekranu przedstawiający wykryte twarze otoczone prostokątami](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono podstawowy proces korzystania z zestawu SDK platformy .NET dla usługi rozpoznawania twarzy oraz utworzono aplikację do wykrywania i oznaczania ramkami twarzy na obrazie. Teraz dowiedz się więcej o szczegółach wykrywania twarzy.

> [!div class="nextstepaction"]
> [Jak wykrywać twarze na obrazie](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
