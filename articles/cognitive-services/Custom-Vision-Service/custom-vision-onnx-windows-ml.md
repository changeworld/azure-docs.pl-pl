---
title: Niestandardowe modelu ONNX wizji ml Windows - kognitywnych usług | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia aplikacji systemu Windows, który używa modelu ONNX wyeksportowany z usługi kognitywnych.
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: larryfr
ms.openlocfilehash: 0b128ba1800e74c20c09a9c5711c8473f1dd00d0
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939472"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Samouczek: Za pomocą modelu ONNX z wizji niestandardowe ML systemu Windows (wersja zapoznawcza)

Dowiedz się, jak używać modelu ONNX wyeksportowany z usługi wizji niestandardowe ml systemu Windows (wersja zapoznawcza).

Informacje przedstawione w tym dokumencie przedstawiono sposób użycia pliku ONNX wyeksportowany z usługi wizji niestandardowe ml systemu Windows. Podano przykład aplikacji systemu Windows. Trenowanego modelu, który może rozpoznawać psów i kotów jest uwzględniona w przykładzie. Kroki znajdują się również na wykorzystania własnych modelu z tym przykładem.

> [!div class="checklist"]
> * O przykładową aplikację
> * Pobierz przykładowy kod
> * Uruchomić przykład
> * Użyć własnego modelu

## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenia z systemem Windows 10 z:

    * Aparatu.

    * Visual Studio 2017 wersji 15.7 lub nowszym z __rozwoju platformy uniwersalnej systemu Windows__ obciążenia włączone.

    * Włączony tryb dewelopera. Aby uzyskać więcej informacji, zobacz [włączyć urządzenia na potrzeby programowania](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) dokumentu.

* (Opcjonalnie) Plik ONNX wyeksportowany z usługi wizji niestandardowe. Aby uzyskać więcej informacji, zobacz [wyeksportować modelu do użytku z urządzeniami przenośnymi](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dokumentu.

    > [!NOTE]
    > Aby korzystać z własnych modelu, postępuj zgodnie z instrukcjami [użyć własnych modelu](#use-your-own-model) sekcji.

## <a name="about-the-example-app"></a>O przykładową aplikację

Aplikacja jest ogólny aplikacji systemu Windows. Używa aparatu na urządzeniu z systemem Windows 10 do dostarczania obrazów do modelu. Znaczniki i wyniki zwrócone przez model są wyświetlane poniżej podglądu wideo.

* Jako dane pochodzą jednak aparatu, [MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader) jest używany do wyodrębniania poszczególnych klatek. Ramki są wysyłane do modelu wyników.

* Model zwraca tagi, które zostało ćwiczenie i wartość typu float, która wskazuje, jak pewność jest czy obraz zawiera tego elementu.

### <a name="the-ui"></a>Interfejs użytkownika

Interfejs użytkownika dla przykładowej aplikacji jest tworzony przy użyciu __CaptureElement__ i __blok tekstu__ kontrolki. CaptureElement Wyświetla podgląd wideo z aparatu fotograficznego, oraz kontrolka TextBlock wyników zwróconych z modelu. 

### <a name="the-model"></a>Model

Model (`cat-or-dog.onnx`) dostarczonego z programem przykładzie został utworzony i uczony przy użyciu usługi kognitywnych wizji niestandardowe usługi. Trenowanego modelu następnie został wyeksportowany jako model ONNX. Aby uzyskać więcej informacji dotyczących korzystania z tej usługi, zobacz [sposób tworzenia klasyfikatora](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) i [wyeksportować modelu do użytku z urządzeniami przenośnymi](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dokumentów.

> [!IMPORTANT]
> Model wyposażone w przykładzie został uczone z niewielki zestaw dog i cat obrazów. Dlatego nie może być najbardziej na świecie na rozpoznawanie psów i kotów.

### <a name="the-model-class-file"></a>Plik klasy modelu

Po dodaniu pliku ONNX do aplikacji systemu Windows, tworzy plik CS. Ten plik ma taką samą nazwę jak `.onnx` pliku (`cat-or-dog` w tym przykładzie) i zawiera klasy używane do pracy z modelem w języku C#. Jednak jednostek w wygenerowana klasa może mieć nazwy, jak `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Można bezpiecznie zmienić te wpisy (kliknij prawym przyciskiem myszy, Zmień nazwę) do przyjazną nazwę.

> [!NOTE]
> Przykładowy kod ma refaktoryzowane wygenerowane klasy i metody nazwy do następującego:
>
> * `ModelInput`
> * `ModelOutput`
> * `Model`
> * `CreateModel`

### <a name="camera-access"></a>Dostęp do aparatu

__Możliwości__ karcie `Package.appxmanifest` plik jest skonfigurowany w celu umożliwienia dostępu do kamery internetowej i mikrofonu.

> [!NOTE]
> Mimo że w tym przykładzie nie używa audio, I ma włączyć mikrofon, aby była dostępna na dostęp do aparatu w urządzeniu.

Aplikacja próbuje pobrać kamerę z tyłu urządzenia, jeśli jest dostępny. Używa [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) klasę, aby uruchomić przechwytywanie obrazu wideo z aparatu. [MediaFrameReader](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) służy do przechwytywania ramek wideo i wysyłać je do modelu.

## <a name="get-the-example-code"></a>Pobierz przykładowy kod

Przykładowa aplikacja jest dostępna na [ https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP ](https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP).

## <a name="run-the-example"></a>Uruchomić przykład

1. Użyj `F5` klawisz, aby uruchomić aplikację w programie Visual Studio. Może pojawić się prośba Aby włączyć tryb dewelopera. Aby uzyskać więcej informacji, zobacz [włączyć urządzenia na potrzeby programowania](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) dokumentu.

2. Po wyświetleniu monitu umożliwić aplikacji dostęp do aparatu i mikrofon na urządzeniu.

3. Punkt na dog lub cat aparatu. Wynik dla tego, czy obraz zawiera dog lub cat jest wyświetlana poniżej Podgląd w aplikacji.

    > [!TIP]
    > Jeśli nie masz dog lub cat skorzystać, można użyć zdjęcie dog lub cat.

## <a name="use-your-own-model"></a>Użyć własnego modelu

Aby korzystać z własnych modelu, wykonaj następujące kroki:

> [!IMPORTANT]
> Kroki opisane w tej sekcji, Zmień nazwę bieżącego modelu (cat lub dog.cs) i Refaktoryzuj nazwy klasy i metody nowego modelu. Pozwoli to uniknąć kolizji z modelem przykład nazw.

1. Uczenie modelu przy użyciu przy użyciu usługi wizji niestandardowe. Aby uzyskać informacje na temat do uczenia modelu, zobacz [sposób tworzenia klasyfikatora](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier).

2. Wyeksportuj trenowanego modelu jako model ONNX. Aby uzyskać informacje na temat sposobu eksportowania modelu, zobacz [wyeksportować modelu do użytku z urządzeniami przenośnymi](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dokumentu.

3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy __cat lub dog.cs__ i zmienić jego nazwę na __cat lub dog.txt__. Zmiana jego nazwy zapobiega konflikty nazw w nowym modelu.

    > [!TIP]
    > Można także użyć innej nazwy dla nazwy klas w nowy model, ale ponowne wykorzystywanie istniejących nazw jest prostsze.

4. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy __VisionApp__ wpis, a następnie wybierz __Dodaj__ > __istniejący element...__ .

5. Aby wygenerować klasy dla modelu, wybierz plik ONNX do zaimportowania, a następnie wybierz __Dodaj__ przycisku. Nową klasę o takiej samej nazwie jak plik ONNX (ale `.cs` rozszerzenia) jest dodawany w Eksploratorze rozwiązań.

6. Otwórz plik .cs wygenerowane i znajdowanie nazw następujące elementy:

    > [!IMPORTANT]
    > Skorzystaj z przykładu `cat-or-dog.txt` pliku jako przewodnik dotyczący rozpoznaje klasy i funkcje.

    * Klasa, która definiuje model danych wejściowych. Wygenerowana nazwa może być podobna do `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Zmień nazwę tej klasy do __ModelInput__.
    * Klasa, która definiuje model danych wyjściowych. Wygenerowana nazwa może być podobna do `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelOutput`. Zmień nazwę tej klasy do __ModelOutput__.
    * Klasa, która definiuje modelu. Wygenerowana nazwa może być podobna do `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Zmień nazwę tej klasy do __modelu__.
    * Metoda, która tworzy modelu. Wygenerowana nazwa może być podobna do `Create_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Zmień nazwę tej metody do __CreateModel__.

7. W Eksploratorze rozwiązań, Przenieś `.onnx` pliku do __zasoby__ folderu. 

8. Aby dołączyć plik ONNX pakiet aplikacji, wybierz `.onnx` pliku i ustaw __Akcja kompilacji__ do __zawartości__ we właściwościach.

9. Otwórz __MainPage.xaml.cs__ pliku. Znajdź następujący wiersz i Zmień nazwę pliku do nowego `.onnx` pliku:

    ```csharp
    var file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/cat-or-dog.onnx"));
    ```

    Ta zmiana ładuje nowego modelu w czasie wykonywania.

10. Skompiluj i uruchom aplikację. Używa teraz nowy model do wyniku obrazów.

## <a name="next-steps"></a>Kolejne kroki

Aby odnaleźć inne sposoby eksportowania i korzystają z modelu wizji niestandardowych, można znaleźć w następujących dokumentach:

* [Eksportowanie modelu](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Użyj modelu Tensorflow wyeksportowanego w aplikacji systemu Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Użyj wyeksportowane CoreML modelu w aplikacji systemu iOS Swift](https://go.microsoft.com/fwlink/?linkid=857726)
* [Użyj wyeksportowane CoreML modelu w aplikacji systemu iOS za pomocą platformy Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Aby uzyskać więcej informacji na temat używania modeli ONNX ml systemu Windows, temacie [integracji modelu w aplikacji Windows ml](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model) dokumentu.
