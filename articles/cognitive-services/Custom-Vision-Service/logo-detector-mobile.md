---
title: 'Samouczek: Używanie niestandardowego detektora logo do rozpoznawania usług platformy Azure — Custom Vision'
titleSuffix: Azure Cognitive Services
description: W tym samouczku zostaniesz krok po przykładowej aplikacji, która używa niestandardowej wizji jako część scenariusza wykrywania logo. Dowiedz się, jak usługa Custom Vision wraz z innymi składnikami jest używana do dostarczenia całościowej aplikacji.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 51fa6d4859eb4b7f059b499ba73d84d9fc65e6f6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78398977"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Samouczek: Rozpoznawanie logo usługi Platformy Azure na zdjęciach z kamer

W tym samouczku zapoznajesz się z przykładową aplikacją, która używa wizji niestandardowej w ramach większego scenariusza. Aplikacja sztucznej inteligencji aprowizacji wizualnej, aplikacja Xamarin.Forms dla platform mobilnych, analizuje obrazy logo usług platformy Azure z kamery, a następnie wdraża rzeczywiste usługi na koncie użytkownika platformy Azure. Tutaj dowiesz się, jak używa ona usługi Custom Vision we współpracy z innymi składnikami w celu dostarczenia przydatnej całościowej aplikacji. Możesz uruchomić cały scenariusz aplikacji dla siebie lub możesz ukończyć tylko część niestandardowej wizji konfiguracji i zbadać, jak aplikacja z niej korzysta.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> - Tworzenie wykrywacza niestandardowego obiektu w celu rozpoznawania logo usług platformy Azure.
> - Łączenie Twojej aplikacji z usługami Azure Computer Vision i Custom Vision.
> - Tworzenie konta jednostki usługi platformy Azure w celu wdrożenia usług platformy Azure z poziomu aplikacji.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem. 

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio 2017 lub nowsze](https://www.visualstudio.com/downloads/)
- Obciążenia na platformie Xamarin dla programu Visual Studio (zobacz [Instalacja platformy Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- Emulator systemu iOS lub Android dla programu Visual Studio
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (opcjonalnie)

## <a name="get-the-source-code"></a>Uzyskiwanie kodu źródłowego

Jeśli chcesz korzystać z dostarczonej aplikacji internetowej, sklonuj lub pobierz kod źródłowy aplikacji z repozytorium [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) w usłudze GitHub. Otwórz plik *Source/VisualProvision.sln* w programie Visual Studio. Później będziesz edytować niektóre pliki projektu, aby było możliwe uruchomienie aplikacji.

## <a name="create-an-object-detector"></a>Tworzenie detektora obiektu

Zaloguj się do [witryny internetowej usługi Custom Vision](https://customvision.ai/) i utwórz nowy projekt. Określ projekt wykrywania obiektów i użyj domeny Logo. Umożliwi to usłudze użycie algorytmu zoptymalizowanego pod kątem wykrywania logo. 

![Okno nowego projektu w witrynie internetowej usługi Custom Vision w przeglądarce Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Następnie wytrenuj algorytm wykrywania logo, przekazując obrazy logo usług platformy Azure i tagując je ręcznie. Repozytorium AIVisualProvision zawiera zbiór obrazów do szkolenia, którego można użyć. W witrynie sieci Web wybierz przycisk **Dodaj obrazy** na karcie **Obrazy szkoleniowe.** Następnie przejdź do folderu **Dokumenty/Obrazy/Training_DataSet** repozytorium. Konieczne będzie ręczne tagowanie logo na każdym obrazie, jeśli więc tylko testujesz ten projekt, możesz chcieć przekazać tylko podzbiór obrazów. Przekaż co najmniej 15 wystąpień każdego tagu, który ma być używany.

Po przekazaniu obrazów treningowych wybierz pierwszy z nich na ekranie. Pojawi się okno tagowania. Narysuj pola i przypisz tagi do każdego logo na każdym obrazie. 

![Tagowanie logo w witrynie internetowej usługi Custom Vision](media/azure-logo-tutorial/tag-logos.png)

Aplikacja jest skonfigurowana do pracy z określonymi ciągami tagów. Definicje znajdziesz w pliku *Source\VisualProvision\Services\Recognition\RecognitionService.cs*:

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?name=snippet_constants)]

Po otagowaniu obrazu przejdź w prawo, aby otagować następny. Po zakończeniu zamknij okno tagowania.

## <a name="train-the-object-detector"></a>Szkolenie detektora obiektów

W okienku po lewej stronie ustaw przełącznik **Tags (Tagi)** w pozycji **Tagged (Otagowane)**, aby wyświetlić swoje obrazy. Następnie kliknij zielony przycisk w górnej części strony, aby wytrenować model. Algorytm będzie szkolić do rozpoznawania tych samych tagów w nowych obrazów. Przetestuje to również model na niektórych Twoich istniejących obrazach w celu wygenerowania ocen dokładności.

![Witryna internetowa witryny Internetowej Usługi Custom Vision na karcie Obrazy szkoleniowe. Na tym zrzucie ekranu przycisk Pociąg jest opisany](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Pobieranie adresu URL prognozy

Po wytrenowaniu modelu można już zintegrować go z Twoją aplikacją. Musisz uzyskać adres URL punktu końcowego (adres modelu, który aplikacja będzie wysyłać zapytania) i klucz przewidywania (aby udzielić aplikacji dostępu do żądań prognozowania). Na karcie **Performance (Wydajność)** naciśnij przycisk **Prediction URL (Adres URL przewidywania)** u góry strony.

![Witryna internetowa usługi Custom Vision przedstawiająca okno interfejsu API przewidywania, które wyświetla adres URL i klucz interfejsu API](media/azure-logo-tutorial/cusvis-endpoint.png)

Skopiuj adres URL punktu końcowego i wartość **Klucz przewidywania** do odpowiednich pól w pliku *Source\VisualProvision\AppSettings.cs:*

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_cusvis_keys)]

## <a name="examine-custom-vision-usage"></a>Sprawdzenie użycia usługi Custom Vision

Otwórz plik *Source/VisualProvision/Services/Recognition/CustomVisionService.cs*, aby zobaczyć, jak aplikacja używa Twojego klucza usługi Custom Vision i adresu URL punktu końcowego. Metoda **PredictImageContentsAsync** przyjmuje strumień bajtów pliku obrazu wraz z tokenem anulowania (do asynchronicznego zarządzania zadaniami), wywołuje interfejs API prognozowania usługi Custom Vision i zwraca wynik prognozowania. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?name=snippet_prediction)]

Ten wynik przybiera postać wystąpienia **PredictionResult**, które samo zawiera listę wystąpień **prognozy**. **Prognoza** zawiera wykryty tag i lokalizację otaczającego go pola na obrazie.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?name=snippet_prediction_class)]

Aby dowiedzieć się więcej na temat sposobu obsługi tych danych w aplikacji, zacznij od metody **GetResourcesAsync**. Ta metoda jest zdefiniowana w pliku *Source/VisualProvision/Services/Recognition/RecognitionService.cs*.  

## <a name="add-computer-vision"></a>Dodawanie usługi Computer Vision

Część samouczka dotycząca usługi Custom Vision jest zakończona. Jeśli chcesz uruchomić aplikację, musisz zintegrować również usługę Computer Vision. Aplikacja używa funkcji rozpoznawania tekstu usługi Computer Vision, aby uzupełnić proces wykrywania logo. Logo platformy Azure może być rozpoznawane na podstawie jego wyglądu *lub* tekstu wydrukowanego w jego pobliżu. W przeciwieństwie do modeli usługi Custom Vision, usługa Computer Vision jest wstępnie wytrenowana pod kątem wykonywania pewnych operacji na obrazach lub wideo.

Zasubskrybuj usługę Computer Vision, aby uzyskać klucz i adres URL punktu końcowego. Aby uzyskać pomoc dotyczącą tego kroku, zobacz [Jak uzyskać klucze subskrypcji](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe).

![Usługa Przetwarzania obrazów w witrynie Azure portal z wybranym menu Szybki start. Link do kluczy i adres URL punktu końcowego interfejsu API są zaznaczone](media/azure-logo-tutorial/comvis-keys.png)

Następnie otwórz plik *Source\VisualProvision\AppSettings.cs* i wpisz poprawne wartości do zmiennych `ComputerVisionEndpoint` i `ComputerVisionKey`.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_comvis_keys)]

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Aplikacja wymaga konta nazwy głównej usługi platformy Azure do wdrażania usług w Twojej subskrypcji platformy Azure. Nazwa główna usługi służy do delegowania określonych uprawnień do aplikacji za pomocą kontroli dostępu opartej na rolach. Aby dowiedzieć się więcej, zobacz [przewodnik po jednostkach usług](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals).

Jednostkę usługi możesz utworzyć za pomocą usługi Azure Cloud Shell albo interfejsu wiersza polecenia platformy Azure (jak pokazano tutaj). Aby rozpocząć, zaloguj się i wybierz subskrypcję, której chcesz użyć.

```azurecli
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Następnie utwórz swoją jednostkę usługi. (Wykonanie tego procesu może potrwać jakiś czas).

```azurecli
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Po pomyślnym zakończeniu powinny pojawić się następujące dane wyjściowe JSON zawierające niezbędne poświadczenia.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

Zanotuj wartości `clientId` i `tenantId`. Dodaj je do odpowiednich pól w pliku *Source\VisualProvision\AppSettings.cs*.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_serviceprincipal)]

## <a name="run-the-app"></a>Uruchomienie aplikacji

W tym momencie został przez Ciebie udzielony aplikacji dostęp do:

- wytrenowanego modelu usługi Custom Vision
- usługi Computer Vision
- konta jednostki usługi

Wykonaj następujące kroki, aby uruchomić aplikację:

1. W Eksploratorze rozwiązań programu Visual Studio wybierz projekt **VisualProvision.Android** lub projekt **VisualProvision.iOS**. Z menu rozwijanego na głównym pasku narzędzi wybierz odpowiedni emulator lub podłączone urządzenie przenośne. Następnie uruchom aplikację.

    > [!NOTE]
    > Aby uruchomić emulator systemu iOS, musisz mieć urządzenie z systemem MacOS.

1. Na pierwszym ekranie wprowadź identyfikator klienta jednostki usługi, identyfikator dzierżawy i hasło. Naciśnij przycisk **Login (Zaloguj się)**.

    > [!NOTE]
    > W niektórych emulatorach przycisk **Login (Zaloguj się)** może nie być uaktywniony na tym etapie. W takim przypadku zatrzymaj aplikację, otwórz plik *Source/VisualProvision/Pages/LoginPage.xaml*, znajdź element `Button` oznaczony jako **LOGIN BUTTON (PRZYCISK LOGOWANIA)**, usuń następujący wiersz, po czym ponownie uruchom aplikację.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![Ekran aplikacji przedstawiający pola na poświadczenia jednostki usługi](media/azure-logo-tutorial/app-credentials.png)

1. Na następnym ekranie wybierz z listy rozwijanej swoją subskrypcję platformy Azure. (To menu powinno zawierać wszystkie subskrypcje, do których podmiot usługi ma dostęp).) Wybierz przycisk **Kontynuuj.** W tym momencie aplikacja może wyświetlić monit o udzielenie dostępu do aparatu i magazynu zdjęć urządzenia. Udziel uprawnień dostępu.

    ![Ekran aplikacji przedstawiający pole listy rozwijanej dla docelowej subskrypcji platformy Azure](media/azure-logo-tutorial/app-az-subscription.png)


1. Aparat w Twoim urządzeniu zostanie uaktywniony. Zrób zdjęcie jednego z logo usługi platformy Azure, które było uwzględnione podczas trenowania. W oknie wdrożenia powinien pojawić się monit o wybór regionu i grupy zasobów dla nowych usług (tak jak w przypadku ich wdrożenia z witryny Azure Portal). 

    ![Ekran aparatu w smartfonie nakierowany na dwa wycinki papieru z logo platformy Azure](media/azure-logo-tutorial/app-camera-capture.png)

    ![Ekran aplikacji przedstawiający pola dla regionu wdrożenia i grupy zasobów](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli wykonasz już wszystkie kroki w tym scenariuszu i użyjesz aplikacji do wdrażania usług platformy Azure na swoim koncie, przejdź do witryny [Azure Portal](https://ms.portal.azure.com/). Anuluj tam usługi, których nie chcesz używać.

Jeśli planujesz utworzenie własnego projektu wykrywania obiektu za pomocą usługi Custom Vision, możesz chcieć usunąć projekt wykrywania logo utworzony w ramach tego samouczka. Bezpłatna wersja próbna usługi Custom Vision dopuszcza tylko dwa projekty. Aby usunąć projekt wykrywania logo w [witrynie internetowej usługi Custom Vision](https://customvision.ai), otwórz pozycję **Projects (Projekty)**, a następnie wybierz ikonę kosza na śmieci w obszarze **My New Project (Mój nowy projekt)**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku została skonfigurowana i przebadana w pełni funkcjonalna aplikacja platformy Xamarin.Forms, która używa usługi Custom Vision do wykrywania logo na zdjęciach z aparatu urządzenia przenośnego. Następnie poznaj najlepsze rozwiązania dotyczące budowania modelu usługi Custom Vision, dzięki czemu podczas tworzenia własnych aplikacji możesz zapewnić ich wydajność i dokładność.

> [!div class="nextstepaction"]
> [Jak poprawić swój klasyfikator](getting-started-improving-your-classifier.md)
