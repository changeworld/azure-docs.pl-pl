---
title: 'Samouczek: używanie modelu ONNX z funkcją Windows ML — Custom Vision Service'
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć aplikację Windows UWP używającą modelu ONNX wyeksportowanego z Azure Cognitive Services.
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 06/19/2018
ms.author: larryfr
ms.openlocfilehash: 3a9e9bc92ce38c4bb8d6d83c8017fa223342e7d2
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365608"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Samouczek: używanie modelu ONNX z usługi Custom Vision z funkcją Windows ML (wersja zapoznawcza)

Dowiedz się, jak używać modelu ONNX wyeksportowanego z usługi Custom Vision z funkcją Windows ML (wersja zapoznawcza).

W tym artykule opisano sposób używania pliku ONNX wyeksportowanego z Custom Vision Service z funkcją Windows ML. Dostępna jest przykładowa aplikacja Windows UWP. Do przykładowej aplikacji dołączony jest wyszkolony model rozpoznający psy i koty. Podano również instrukcję używania własnego modelu z tą przykładową aplikacją.

> [!div class="checklist"]
> * Informacje o przykładowej aplikacji
> * Pobieranie przykładowego kodu
> * Uruchomienie przykładu
> * Używanie własnego modelu

## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie z systemem Windows 10 wyposażone w:

    * aparat.

    * Visual Studio 2017 w wersji 15.7 lub nowszej z włączonym pakietem __Opracowywanie zawartości dla platformy uniwersalnej systemu Windows__.

    * Włączony tryb dewelopera. Więcej informacji można znaleźć w artykule [Enable your device for development](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) (Konfigurowanie urządzenia pod kątem tworzenia aplikacji).

* (Opcjonalnie) Plik ONNX wyeksportowany z usługi Custom Vision. Więcej informacji znajdziesz w artykule [Export your model for use with mobile devices](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) (Eksportowanie modelu w celu używania go na urządzeniach mobilnych).

    > [!NOTE]
    > Aby używać własnego modelu, wykonaj czynności opisane w sekcji [Używanie własnego modelu](#use-your-own-model).

## <a name="about-the-example-app"></a>Informacje o przykładowej aplikacji

Aplikacja jest aplikacją ogólną Windows UWP. Używa aparatu urządzenia z Windows 10, aby dostarczać zdjęcia do modelu. Tagi i wyniki zwrócone przez model są wyświetlane pod podglądem wideo.

* Obiekt [MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader) wydobywa pojedyncze klatki z danych otrzymywanych z aparatu. Klatki są wysyłane do modelu w celu uzyskania oceny.

* Model zwraca tagi, przy użyciu których został wyszkolony, oraz liczbę zmiennoprzecinkową oznaczającą stopień pewności, że obraz zawiera dany element.

### <a name="the-ui"></a>Interfejs użytkownika

Interfejs użytkownika przykładowej aplikacji jest tworzony przy użyciu kontrolek __CaptureElement__ i __TextBlock__. Kontrolka CaptureElement wyświetla podgląd wideo z kamery, a TextBlock wyświetla wyniki zwrócone przez model. 

### <a name="the-model"></a>Model

Model (`cat-or-dog.onnx`) udostępniony z przykładową aplikacją został utworzony i wyszkolony za pomocą usług Cognitive Services Custom Vision. Wyszkolony model został wyeksportowany jako model ONNX. Więcej informacji o używaniu tej usługi można znaleźć w artykułach [How to build a classifier](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) (Jak zbudować klasyfikator) i [Export your model for use with mobile devices](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) (Eksportowanie modelu w celu używania go na urządzeniach mobilnych).

> [!IMPORTANT]
> Model udostępniony z przykładową aplikacją został wyszkolony na małym zbiorze zdjęć psów i kotów. Dlatego pewnie nie jest najlepszy na świecie w rozpoznawaniu tych zwierząt.

### <a name="the-model-class-file"></a>Plik klas modelu

Po dodaniu pliku ONNX do aplikacji Windows UWP zostanie utworzony plik cs. Plik ma taką samą nazwę, jak plik `.onnx` (w tym przykładzie `cat-or-dog`) i zawiera klasy umożliwiające używanie modelu z poziomu języka C#. Jednak elementy wygenerowanej klasy mogą mieć nazwy takie, jak `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Te nazwy można zmienić (kliknij prawym przyciskiem myszy i zmień nazwę) na bardziej przyjazne.

> [!NOTE]
> W przykładowym kodzie zmieniliśmy wygenerowane nazwy klas i metod na następujące:
>
> * `ModelInput`
> * `ModelOutput`
> * `Model`
> * `CreateModel`

### <a name="camera-access"></a>Dostęp do aparatu

Na karcie __Możliwości__ w pliku `Package.appxmanifest` jest włączony dostęp do aparatu i mikrofonu.

> [!NOTE]
> Choć w tym przykładzie nie używamy dźwięku, uzyskanie dostępu do aparatu na moim urządzeniu wymagało włączenia mikrofonu.

Aplikacja próbuje używać aparatu z tyłu urządzenia, jeśli jest on dostępny. Używając klasy [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) rozpoczyna przechwytywanie wideo z aparatu. Obiekt [MediaFrameReader](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) przechwytuje klatki wideo i wysyła je do modelu.

## <a name="get-the-example-code"></a>Pobieranie przykładowego kodu

Przykładowa aplikacja jest dostępna na stronie [https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP](https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP).

## <a name="run-the-example"></a>Uruchomienie przykładu

1. Naciśnij klawisz `F5`, aby uruchomić aplikację z poziomu programu Visual Studio. Może zostać wyświetlony monit o włączenie trybu dewelopera. Więcej informacji można znaleźć w artykule [Enable your device for development](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) (Konfigurowanie urządzenia pod kątem tworzenia aplikacji).

2. Zezwól aplikacji na dostęp do aparatu i mikrofonu urządzenia, gdy pojawi się taki monit.

3. Skieruj aparat na psa lub kota. Pod podglądem obrazu zostanie wyświetlony wynik wskazujący,. czy na obrazie znajduje się pies czy kot.

    > [!TIP]
    > Jeśli nie masz koło siebie psa ani kota, możesz użyć zdjęcia jednego z tych zwierząt.

## <a name="use-your-own-model"></a>Używanie własnego modelu

Aby używać własnego modelu, wykonaj następujące czynności:

> [!IMPORTANT]
> Czynności opisane w tej sekcji powodują zmianę nazwy bieżącego modelu (cat-or-dog.cs) oraz zmianę nazw klas i metod nowego modelu. Pozwoli to uniknąć konfliktu z nazwami z przykładowego modelu.

1. Wyszkol model za pomocą usługi Custom Vision. Informacje o sposobie szkolenia modelu znajdziesz w artykule [How to build a classifier](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) (Jak zbudować klasyfikator).

2. Wyeksportuj wyszkolony model jako model ONNX. Informacje o sposobie eksportowania modelu znajdziesz w artykule [Export your model for use with mobile devices](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) (Eksportowanie modelu w celu jego używania na urządzeniach mobilnych).

3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy plik __cat-or-dog.cs__ i zmień jego nazwę na __cat-or-dog.txt__. Pozwoli to uniknąć konfliktu nazw.

    > [!TIP]
    > Możesz też użyć nowych nazw klas w nowym modelu, ale ponowne użycie wcześniejszych nazw jest łatwiejsze.

4. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję __VisionApp__ i wybierz polecenie __Dodaj__ > __Istniejący element...__.

5. Aby wygenerować klasę dla modelu, wybierz plik ONNX do zaimportowania, a następnie kliknij przycisk __Dodaj__. Do Eksploratora rozwiązań zostanie dodana nowa klasa o tej samej nazwie, co plik ONNX (ale z rozszerzeniem `.cs`).

6. Otwórz wygenerowany plik cs i znajdź nazwy następujących elementów:

    > [!IMPORTANT]
    > Użyj pliku `cat-or-dog.txt` przykładowej aplikacji jako pomocy w identyfikacji klas i funkcji.

    * Klasa definiująca wejście modelu. Wygenerowana nazwa może być podobna do `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Zmień nazwę tej klasy na __ModelInput__.
    * Klasa definiująca wyjście modelu. Wygenerowana nazwa może być podobna do `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelOutput`. Zmień nazwę tej klasy na __ModelOutput__.
    * Klasa definiująca model. Wygenerowana nazwa może być podobna do `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Zmień nazwę tej klasy na __Model__.
    * Metoda tworząca model. Wygenerowana nazwa może być podobna do `Create_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Zmień nazwę tej metody na __CreateModel__.

7. W Eksploratorze rozwiązań przenieś plik `.onnx` do folderu __Zasoby__. 

8. Aby umieścić plik ONNX w pakiecie aplikacji, wybierz plik `.onnx` i we właściwościach w polu __Akcja kompilacji__ wybierz opcję __Zawartość__.

9. Otwórz plik __MainPage.xaml.cs__. Znajdź poniższy wiersz i zmień nazwę pliku na nazwę nowego pliku `.onnx`:

    ```csharp
    var file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/cat-or-dog.onnx"));
    ```

    Ta zmiana spowoduje załadowanie nowego modelu w czasie wykonywania.

10. Skompiluj i uruchom aplikację. Będzie ona teraz używać nowego modelu do oceniania obrazów.

## <a name="next-steps"></a>Następne kroki

Aby poznać więcej sposobów eksportowania i używania modeli Custom Vision, przeczytaj następujące artykuły:

* [Eksportowanie modelu](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Używanie wyeksportowanego modelu TensorFlow w aplikacji systemu Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Używanie wyeksportowanego modelu CoreML w aplikacji w języku Swift dla systemu iOS](https://go.microsoft.com/fwlink/?linkid=857726)
* [Używanie wyeksportowanego modelu CoreML w aplikacji dla systemu iOS na platformie Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Więcej informacji o używaniu modeli ONNX z funkcją Windows ML znajdziesz w artykule [Integrate a model into your app with Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model) (Włączanie modelu do aplikacji przy użyciu funkcji Windows ML).
