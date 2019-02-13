---
title: 'Samouczek: używanie detektora niestandardowego logo do rozpoznawania usług platformy Azure — Custom Vision'
titlesuffix: Azure Cognitive Services
description: W tym samouczku zostanie przedstawiona przykładowa aplikacja, która używa usługi Azure Custom Vision jako części scenariusza wykrywania logo. Dowiedz się, jak usługa Custom Vision wraz z innymi składnikami jest używana do dostarczenia całościowej aplikacji.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pafarley
ms.openlocfilehash: 520e1981544f6ad0a6f1412a47dc8ea50fdd53a5
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55771466"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Samouczek: rozpoznawanie logo usług platformy Azure na obrazach z kamery

W tym samouczku zostanie przedstawiona przykładowa aplikacja, która używa usługi Azure Custom Vision jako części większego scenariusza. Aplikacja sztucznej inteligencji aprowizacji wizualnej, aplikacja Xamarin.Forms dla platform mobilnych, analizuje obrazy logo usług platformy Azure z kamery, a następnie wdraża rzeczywiste usługi na koncie użytkownika platformy Azure. Tutaj dowiesz się, jak używa ona usługi Custom Vision we współpracy z innymi składnikami w celu dostarczenia przydatnej całościowej aplikacji. Możesz też chcieć uruchomić całą aplikację dla siebie lub po prostu wykonać część instalacji usługi Custom Vision i zbadać, jak aplikacja z niej korzysta.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> - Tworzenie wykrywacza niestandardowego obiektu w celu rozpoznawania logo usług platformy Azure
> - Łączenie Twojej aplikacji z usługami Azure Computer Vision i Custom Vision
> - Tworzenie konta jednostki usługi platformy Azure w celu wdrożenia usług platformy Azure z poziomu aplikacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). 

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- Obciążenia na platformie Xamarin dla programu Visual Studio (zobacz [Instalacja platformy Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- Emulator systemu iOS lub Android dla programu Visual Studio
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (opcjonalny)

## <a name="get-the-source-code"></a>Uzyskiwanie kodu źródłowego

Jeśli chcesz korzystać z dostarczonej aplikacji internetowej, sklonuj lub pobierz kod źródłowy aplikacji z repozytorium [Aprowizacja wizualna sztucznej Inteligencji](https://github.com/Microsoft/AIVisualProvision) w witrynie GitHub. Otwórz plik *Source/VisualProvision.sln* w programie Visual Studio. Następnie wprowadzisz niezbędne zmiany do niektórych plików projektu, aby można było uruchomić aplikację.

## <a name="create-an-object-detector"></a>Tworzenie detektora obiektu

Zaloguj się do [witryny internetowej usługi Custom Vision](https://customvision.ai/) i utwórz nowy projekt. Określ projekt wykrywania obiektów i użyj domeny Logo. Umożliwi to usłudze użycie algorytmu zoptymalizowanego pod kątem wykrywania logo. 

![nowe okno dialogowe projektu w witrynie internetowej usługi Custom Vision w przeglądarce Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Przekazywanie i tagowanie obrazów

Następnie należy przeszkolić algorytm wykrywania logo, przesyłając obrazy logo usług platformy Azure i tagując je ręcznie. Repozytorium AIVisualProvision zawiera zbiór obrazów do szkolenia, którego można użyć. W witrynie internetowej naciśnij przycisk **Dodaj obrazy** znajdujący się na karcie **Obrazy do szkolenia**, a następnie przejdź do folderu **Documents/Images/Training_DataSet** repozytorium. Konieczne będzie ręczne tagowanie logo na każdym obrazie, jeśli więc tylko testujesz ten projekt, możesz przekazać tylko podzbiór obrazów. Należy przekazać co najmniej 15 wystąpień każdego tagu, którego planujesz używać.

Po przekazaniu obrazów szkoleniowych wybierz pierwszy z nich na ekranie. Spowoduje to wyświetlenie okna tagowania. Narysuj pola i przypisz tagi do każdego logo na każdym obrazie. 

![obraz logo z tagami zastosowanymi w witrynie internetowej usługi Custom Vision](media/azure-logo-tutorial/tag-logos.png)

Aplikacja jest skonfigurowana do pracy z ciągami konkretnych tagów. Zobacz definicje w pliku *Source\VisualProvision\Services\Recognition\RecognitionService.cs*:

[!code-csharp[tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

Po otagowaniu obrazu przejdź w prawo, aby otagować następny. Po zakończeniu opuść okno tagowania.

## <a name="train-the-object-detector"></a>Szkolenie detektora obiektów

W okienku po lewej stronie ustaw przełącznik **Tagi** w położenie **Otagowane**. Powinny zostać wyświetlone wszystkie Twoje obrazy. Następnie kliknij zielony przycisk w górnej części strony, aby przeszkolić model. Spowoduje to nauczenie algorytmu rozpoznawania tych samych tagów na nowych obrazach. Przetestuje to również model na niektórych Twoich istniejących obrazach w celu wygenerowania ocen dokładności.

![witryna internetowa usługi Custom Vision, na karcie Obrazy do szkolenia; zaznaczony przycisk szkolenia](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Pobieranie adresu URL prognozy

Po przeszkoleniu modelu można już zintegrować go z Twoją aplikacją. Aby to zrobić, musisz pobrać adres URL punktu końcowego (adres modelu, do którego aplikacja będzie wysyłać zapytania) i klucz prognozowania (aby udzielić aplikacji dostępu do żądań prognozowania). Na karcie **Wydajność** naciśnij przycisk **Adres URL prognozowania** znajdujący się u góry strony.

![witryna internetowa usługi Custom Vision z ekranem interfejsu API prognozowania przedstawiającym adres URL i klucz interfejsu API](media/azure-logo-tutorial/cusvis-endpoint.png)

Skopiuj adres URL pliku obrazu i wartość `Prediction-key` do odpowiednich pól w pliku *Source\VisualProvision\AppSettings.cs*:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Sprawdzenie użycia usługi Custom Vision

Otwórz plik *Source/VisualProvision/Services/Recognition/CustomVisionService.cs*, aby zobaczyć, jak Twój klucz i adres URL punktu końcowego usługi Custom Vision są używane w aplikacji. Metoda **PredictImageContentsAsync** przyjmuje strumień bajtów pliku obrazu wraz z tokenem anulowania (do asynchronicznego zarządzania zadaniami), wywołuje interfejs API prognozowania usługi Custom Vision i zwraca wynik prognozowania. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

Ten wynik przybiera postać wystąpienia **PredictionResult**, które samo zawiera listę wystąpień **prognozy**. **Prognoza** zawiera wykryty tag i lokalizację otaczającego go pola na obrazie.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

Jeśli chcesz dowiedzieć się więcej na temat sposobu obsługi tych danych w aplikacji, zacznij od metody **GetResourcesAsync** zdefiniowanej w pliku *Source/VisualProvision/Services/Recognition/RecognitionService.cs*. 

## <a name="add-computer-vision"></a>Dodawanie usługi Computer Vision

Część samouczka dotycząca usługi Custom Vision została ukończona, ale jeśli chcesz uruchomić aplikację, konieczne będzie zintegrowanie również usługi Computer Vision. Aplikacja używa funkcji rozpoznawania tekstu usługi Computer Vision do uzupełnienia procesu wykrywania logo. Logo Azure może zostać rozpoznane za pomocą jego wyglądu _lub_ za pomocą tekstu wydrukowanego w pobliżu. W przeciwieństwie do modeli usługi Custom Vision usługa Computer Vision jest wstępnie przeszkolona pod kątem wykonywania pewnych operacji na obrazach lub filmach wideo.

Po prostu zasubskrybuj usługę Computer Vision, aby pobrać klucz i adres URL punktu końcowego. Zobacz temat [Jak uzyskać klucze subskrypcji](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe), jeśli potrzebujesz pomocy dotyczącej tego kroku.

![usługa Computer Vision w witrynie Azure Portal z wybranym menu Szybki start; link do kluczy jest wyróżniony, tak jak i adres URL punktu końcowego interfejsu API](media/azure-logo-tutorial/comvis-keys.png)

Następnie otwórz plik *Source\VisualProvision\AppSettings.cs* i wpisz poprawne wartości do zmiennych `ComputerVisionEndpoint` i `ComputerVisionKey`.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]


## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Aplikacja wymaga konta nazwy głównej usługi platformy Azure do wdrażania usług w Twojej subskrypcji platformy Azure. Nazwa główna usługi służy do delegowania określonych uprawnień do aplikacji za pomocą kontroli dostępu opartej na rolach. Zobacz [przewodnik dotyczący nazwy głównej usługi](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals), jeśli chcesz dowiedzieć się więcej.

Nazwę główną usługi możesz utworzyć za pomocą usługi Azure Cloud Shell albo interfejsu wiersza polecenia platformy Azure (jak pokazano poniżej). Najpierw zaloguj się i wybierz subskrypcję, której chcesz użyć.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Następnie utwórz nazwę główną usługi (pamiętaj, że ta czynność może potrwać pewien czas).

```console
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

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>Uruchamianie aplikacji

W tym momencie został przez Ciebie udzielony aplikacji dostęp do:
* przeszkolonego modelu usługi Custom Vision
* usługi Computer Vision
* konta nazwy głównej usługi 

Wykonaj następujące kroki, aby uruchomić aplikację:

1. W Eksploratorze rozwiązań programu Visual Studio wybierz projekt VisualProvision.Android albo VisualProvision.iOS, a następnie wybierz odpowiedni emulator lub podłączone urządzenie przenośne z menu rozwijanego na głównym pasku narzędzi (pamiętaj, że do uruchomienia emulatora systemu iOS będzie konieczne urządzenie z systemem MacOS). Następnie uruchom aplikację.

1. Na pierwszym załadowanym ekranie wprowadź identyfikator klienta nazwy głównej usługi, identyfikator dzierżawy i hasło. Kliknij przycisk **Zaloguj**.

    > [!NOTE]
    > W niektórych emulatorach przycisk **Zaloguj** może nie być aktywny na tym etapie. W takim przypadku zatrzymaj aplikację, otwórz plik _Source/VisualProvision/Pages/LoginPage.xaml_, znajdź element `Button` oznaczony jako PRZYCISK LOGOWANIA i usuń wiersz:
      ```xaml
      IsEnabled="{Binding IsValid}"
      ```
    
    > Następnie ponownie uruchom aplikację.

    ![ekran aplikacji z polami na poświadczenia nazwy głównej usługi](media/azure-logo-tutorial/app-credentials.png)

1. Na następnym ekranie wybierz swoją subskrypcję platformy Azure z menu rozwijanego (powinno ono zawierać wszystkie subskrypcje, do których nazwa główna usługi ma dostęp). Kliknij przycisk **Kontynuuj**.

    ![ekran aplikacji z polem listy rozwijanej z docelową subskrypcją platformy Azure](media/azure-logo-tutorial/app-az-subscription.png)

    W tym momencie aplikacja może wyświetlić monit o udzielenie dostępu dla tych urządzeń do kamery i magazynu zdjęć. Zaakceptuj te uprawnienia.

1. Następnie kamera w Twoim urządzeniu zostanie uaktywniona. Zrób zdjęcie jednego z logo usługi platformy Azure, które przeszło szkolenie. Okno dialogowe wdrożenia powinno wyświetlić monit o wybór regionu i grupy zasobów dla nowych usług (tak jak w przypadku ich wdrożenia w witrynie Azure Portal). 

    ![ekran aparatu smartfonu z dwoma wycinkami papieru z logo platformy Azure w widoku](media/azure-logo-tutorial/app-camera-capture.png)

    ![ekran aplikacji z polami dla regionu wdrożenia i wpisu grupy zasobów](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli wykonasz już wszystkie kroki w tym scenariuszu i użyjesz aplikacji do wdrażania usług platformy Azure na swoim koncie, pamiętaj, aby po zakończeniu przejść do witryny [Azure Portal](https://ms.portal.azure.com/) i anulować usługi, których nie chcesz używać.

Ponadto, jeśli planujesz w przyszłości utworzenie własnego projektu wykrywania obiektu za pomocą usługi Custom Vision, możesz usunąć projekt wykrywania logo utworzony w ramach tego samouczka. Bezpłatna wersja próbna usługi Custom Vision dopuszcza dwa projekty. W [witrynie internetowej usługi Custom Vision](https://customvision.ai) przejdź do obszaru **Projekty** i wybierz ikonę kosza w obszarze **Mój nowy projekt**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku została skonfigurowana i przebadana w pełni funkcjonalna aplikacji platformy Xamarin.Forms, która korzysta z usługi Custom Vision do wykrywania logo na obrazach kamery mobilnej. Następnie poznaj najlepsze rozwiązania dotyczące budowania modelu usługi Custom Vision, dzięki czemu podczas tworzenia własnych aplikacji możesz zapewnić ich wydajność i dokładność.

> [!div class="nextstepaction"]
> [Jak poprawić swój klasyfikator](getting-started-improving-your-classifier.md)