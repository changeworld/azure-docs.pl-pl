---
title: Przekazywanie, kodowanie i przesyłanie strumieniowe przy użyciu usługi Azure Media Services | Microsoft Docs
description: Wykonaj kroki opisane w tym samouczku, aby przekazać plik, zakodować wideo oraz przesłać strumieniowo zawartość za pomocą usługi REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 0faed5d72002f24d7be7602c5f16c18e66a0089e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38308617"
---
# <a name="tutorial-upload-encode-and-stream-videos-with-rest"></a>Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe wideo za pomocą usługi REST

Ten samouczek przedstawia sposób przekazywania, kodowania i przesyłania strumieniowego plików wideo w usłudze Azure Media Services.

Usługa Media Services umożliwia kodowanie plików multimedialnych do formatów, które można odtworzyć w różnych przeglądarkach i na różnych urządzeniach. Na przykład może zaistnieć potrzeba strumieniowego odtwarzania treści w formatach HLS lub MPEG DASH firmy Apple. Przed odtwarzaniem strumieniowym należy zakodować wysokiej jakości plik multimediów cyfrowych. Aby uzyskać wskazówki dotyczące kodowania, zobacz temat [Encoding concept](encoding-concept.md) (Koncepcja kodowania).

![Odtwarzanie wideo](./media/stream-files-tutorial-with-api/final-video.png)

Ten samouczek przedstawia sposób wykonania następujących czynności:    

> [!div class="checklist"]
> * Tworzenie konta usługi Media Services
> * Uzyskiwanie dostępu do interfejsu API usługi Media Services
> * Pobieranie plików Postman
> * Konfigurowanie programu Postman
> * Wysyłanie żądań przy użyciu programu Postman
> * Testowanie adresu URL przesyłania strumieniowego
> * Oczyszczanie zasobów

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj klienta REST programu [Postman](https://www.getpostman.com/), aby uruchomić interfejsy API REST przedstawione w niektórych samouczkach dotyczących AMS REST. 

    W przykładzie użyto programu **Postman**, ale odpowiednie będzie każde narzędzie REST. Można również użyć: programu **Visual Studio Code** z wtyczką REST lub programu **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Pobieranie plików Postman

Sklonuj repozytorium GitHub zawierające kolekcję programu Postman oraz pliki środowiska.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="configure-postman"></a>Konfigurowanie programu Postman

W tej sekcji skonfigurowano program Postman.

### <a name="configure-the-environment"></a>Konfigurowanie środowiska 

1. Otwórz program **Postman**.
2. Po prawej stronie ekranu wybierz opcję **Zarządzaj środowiskiem**.

    ![Zarządzanie środowiskiem](./media/develop-with-postman/postman-import-env.png)
4. W oknie dialogowym **Zarządzaj środowiskiem** kliknij opcję **Importuj**.
2. Przejdź do pliku `Azure Media Service v3 Environment.postman_environment.json`, który został pobrany podczas klonowania `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Zostanie dodane środowisko **Azure Media Service v3 Environment**.

    > [!Note]
    > Zaktualizuj zmienne dostępu przy użyciu wartości z sekcji **Uzyskiwanie dostępu do interfejsu API usługi Media Services** powyżej.

7. Zamknij okno dialogowe.
8. Z listy rozwijanej wybierz środowisko **Azure Media Service v3 Environment**.

    ![Wybór środowiska](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Konfigurowanie kolekcji

1. Kliknij przycisk **Importuj**, aby zaimportować plik kolekcji.
1. Przejdź do pliku `Media Services v3 (2018-03-30-preview).postman_collection.json`, który został pobrany podczas klonowania `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Wybierz plik **Media Services v3 (2018-03-30-preview).postman_collection.json**.

    ![Importowanie pliku](./media/develop-with-postman/postman-import-collection.png)

## <a name="send-requests-using-postman"></a>Wysyłanie żądań przy użyciu programu Postman

W tej sekcji opisano wysyłanie żądań istotnych dla kodowania i tworzenia adresów URL, aby można było przesyłać strumieniowo plik. W szczególności wysyłane są następujące żądania:

1. Pobieranie tokenu usługi Azure AD do uwierzytelniania jednostki usługi
2. Tworzenie zasobu wyjściowego
3. Tworzenie przekształcenia
4. Tworzenie zadania 
5. Tworzenie lokalizatora przesyłania strumieniowego
6. Podanie listy ścieżek lokalizatora przesyłania strumieniowego

> [!Note]
>  W tym samouczku założono, że tworzysz wszystkie zasoby o unikatowych nazwach.  

### <a name="get-azure-ad-token"></a>Pobieranie tokenu usługi Azure AD 

1. W lewym oknie programu Postman wybierz opcję „Krok 1: Pobierz token uwierzytelniania usługi AAD”.
2. Następnie wybierz pozycję „Pobieranie tokenu usługi Azure AD do uwierzytelniania jednostki usługi”.
3. Kliknij pozycję **Wyślij**.

    Zostanie wysłana następująca operacja **POST**.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Odpowiedź wróci z tokenem i ustawi zmienną środowiskową „AccessToken” na wartość tokenu. Aby wyświetlić kod, który ustawia zmienną „AccessToken”, kliknij kartę **Testy**. 

    ![Pobieranie tokenu usługi AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

### <a name="create-an-output-asset"></a>Tworzenie zasobu wyjściowego

[Zasób](https://docs.microsoft.com/rest/api/media/assets) wyjściowy przechowuje wynik zadania kodowania. 

1. W lewym oknie programu Postman wybierz opcję „Zasoby”.
2. Następnie wybierz opcję „Utwórz lub aktualizuj zasób”.
3. Kliknij pozycję **Wyślij**.

    Zostanie wysłana następująca operacja **PUT**.

    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
    ```

### <a name="create-a-transform"></a>Tworzenie przekształcenia

W przypadku kodowania lub przetwarzania zawartości w usłudze Media Services typowym wzorcem postępowania jest skonfigurowanie ustawień kodowania jako przepisu. Następnie przesyła się **zadanie** w celu zastosowania tego przepisu do wideo. Przesłanie nowego zadania dla każdego nowego wideo powoduje zastosowanie przepisu do wszystkich wideo w bibliotece. Przepis w usłudze Media Services nazywa się **przekształceniem**. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transform-concept.md). Przykład opisany w tym samouczku definiuje przepis, który umożliwia kodowanie wideo w celu jego przesyłania strumieniowego do różnych urządzeń z systemami iOS i Android. 

Podczas tworzenia nowego wystąpienia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy określić, jakie dane wyjściowe ma ono tworzyć. Wymagany parametr to obiekt **TransformOutput**. Każdy obiekt **TransformOutput** zawiera element **Preset**. Element **Preset** zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania wymaganego obiektu **TransformOutput**. Przykład opisany w tym artykule używa wbudowanego elementu Preset o nazwie **AdaptiveStreaming**. Element Preset umożliwia kodowanie wejściowego wideo do automatycznie generowanej drabiny szybkości transmisji bitów (zestawu par „szybkość transmisji bitów-rozdzielczość”) na podstawie rozdzielczości wejściowej i szybkości transmisji bitów oraz tworzenie plików MP4 zgodnych ze standardem ISO, które zawierają wideo w formacie H.264 i dźwięk w formacie AAC, dla każdej pary „szybkość transmisji bitów-rozdzielczość”. Aby dowiedzieć się więcej na temat elementu Preset, zobacz informacje o [automatycznie generowanej drabinie szybkości transmisji bitów](autogen-bitrate-ladder.md).

Możesz użyć wbudowanych elementów EncoderNamedPreset lub użyć niestandardowych ustawień wstępnych. 

> [!Note]
> Podczas tworzenia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy najpierw sprawdzić, czy taki obiekt już istnieje, używając metody **Get**. W tym samouczku założono, że tworzysz przekształcenie o unikatowej nazwie.

1. W lewym oknie programu Postman wybierz opcję „Kodowania i analiza”.
2. Następnie wybierz pozycję „Utwórz przekształcenie”.
3. Kliknij pozycję **Wyślij**.

    Zostanie wysłana następująca operacja **PUT**.

    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
    ```

### <a name="create-a-job"></a>Tworzenie zadania

Obiekt [Job](https://docs.microsoft.com/rest/api/media/jobs) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie utworzonego obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. Obiekt **Job** określa informacje takie jak lokalizacja wejściowego pliku wideo oraz danych wyjściowych.

W tym przykładzie dane wejściowe zadania są oparte na adresie URL protokołu HTTPS („https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/”).

1. W lewym oknie programu Postman wybierz opcję „Kodowania i analiza”.
2. Następnie wybierz opcję „Utwórz lub aktualizuj zadanie”.
3. Kliknij pozycję **Wyślij**.

    Zostanie wysłana następująca operacja **PUT**.

    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```

Ukończenie zadania zajmuje trochę czasu, a Ty chcesz otrzymać powiadomienie o tym fakcie. Aby wyświetlić postęp zadania, zaleca się użycie usługi Event Grid. Zaprojektowano ją pod kątem wysokiej dostępności, stałego poziomu wydajności i dynamicznej skalowalności. Dzięki usłudze Event Grid Twoje aplikacje mogą nasłuchiwać zdarzeń pochodzących z praktycznie wszystkich usług platformy Azure i ze źródeł niestandardowych oraz reagować na nie. Prosta, reaktywna obsługa zdarzeń oparta na protokole HTTP pomaga w tworzeniu wydajnych rozwiązań za pośrednictwem inteligentnego filtrowania i routingu zdarzeń.  Zobacz [Kierowanie zdarzeń do niestandardowego internetowego punktu końcowego](job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled (Zaplanowane)**, **Queued (W kolejce)**, **Processing (Przetwarzane)**, **Finished (Zakończone)** (stan końcowy). Jeśli zadanie napotka błąd, może być w stanie **Error (Błąd)**. Jeśli zadanie jest w trakcie anulowania, może być w stanie **Canceling (Anulowanie)**, a po zakończeniu tej operacji w stanie **Canceled (Anulowane)**.

### <a name="create-a-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego

Po zakończeniu kodowania następnym krokiem jest udostępnienie wideo w zasobie wyjściowym klientom na potrzeby odtwarzania. Działanie to można wykonać w dwóch krokach: najpierw należy utworzyć obiekt [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), a następnie utworzyć adresy URL przesyłania strumieniowego, których mogą używać klienci. 

Proces tworzenia obiektu **StreamingLocator** jest nazywany publikowaniem. Domyślnie obiekt **StreamingLocator** jest ważny natychmiast po wykonaniu wywołań interfejsu API i aż do jego usunięcia, chyba że skonfigurujesz opcjonalne czasy rozpoczęcia i zakończenia. 

Podczas tworzenia obiektu [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) musisz określić żądany element **StreamingPolicyName**. W tym przykładzie zawartość będzie przesyłana strumieniowo w postaci jawnej (bez szyfrowania), dzięki czemu można użyć wstępnie zdefiniowanych zasad przesyłania strumieniowego bez szyfrowania (**PredefinedStreamingPolicy.ClearStreamingOnly**).

> [!IMPORTANT]
> W przypadku korzystania z niestandardowego elementu [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i używać ich ponownie dla obiektów StreamingLocator zawsze, gdy są potrzebne takie same opcje szyfrowania i protokoły. 

Konto usługi Media Service jest objęte limitem przydziału dotyczącym liczby pozycji elementu StreamingPolicy. Nie należy tworzyć nowego elementu StreamingPolicy dla każdego obiektu StreamingLocator.

1. W lewym oknie programu Postman wybierz opcję „Zasady przesyłania strumieniowego”.
2. Następnie wybierz opcję „Utwórz zasady przesyłania strumieniowego”.
3. Kliknij pozycję **Wyślij**.

    Zostanie wysłana następująca operacja **PUT**.

    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingPolicies/:streamingPolicyName?api-version={{api-version}}
    ```

### <a name="list-paths-and-build-streaming-urls"></a>Ścieżki listy i adresy URL przesyłania strumieniowego kompilacji

#### <a name="list-paths"></a>Ścieżki listy

Teraz, po utworzeniu obiektu [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), możesz pobrać adresy URL przesyłania strumieniowego.

1. W lewym oknie programu Postman wybierz opcję „Zasady przesyłania strumieniowego”.
2. Następnie wybierz opcję „Ścieżki listy”.
3. Kliknij pozycję **Wyślij**.

    Zostanie wysłana następująca operacja **POST**.

    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName/listPaths?api-version={{api-version}}
    ```
4. Zanotuj jedną ze ścieżek, której chcesz użyć do przesyłania strumieniowego. Użyjesz jej w kolejnej sekcji. W tym przypadku zostały zwrócone następujące ścieżki:
    
    ```
    {
        "streamingPaths": [
            {
                "streamingProtocol": "Hls",
                "encryptionScheme": "NoEncryption",
                "paths": [
                    "/fd384f76-2d23-4e50-8fad-f9b3ebcd675b/Ignite-short.ism/manifest(format=m3u8-aapl)"
                ]
            },
            {
                "streamingProtocol": "Dash",
                "encryptionScheme": "NoEncryption",
                "paths": [
                    "/fd384f76-2d23-4e50-8fad-f9b3ebcd675b/Ignite-short.ism/manifest(format=mpd-time-csf)"
                ]
            },
            {
                "streamingProtocol": "SmoothStreaming",
                "encryptionScheme": "NoEncryption",
                "paths": [
                    "/fd384f76-2d23-4e50-8fad-f9b3ebcd675b/Ignite-short.ism/manifest"
                ]
            }
        ],
        "downloadPaths": []
    }
    ```

#### <a name="build-the-streaming-urls"></a>Tworzenie adresów URL przesyłania strumieniowego

W tej sekcji utworzymy adres URL przesyłania strumieniowego HLS. Adresy URL składają się z następujących wartości:

1. Protokół, przy użyciu którego są wysyłane dane. W tym przypadku „https”.

    > [!NOTE]
    > Jeśli odtwarzacz jest hostowany w witrynie korzystającej z protokołu HTTPS, zmień adres URL tak, aby zawierał ciąg „https”.

2. Nazwa hosta StreamingEndpoint. W tym przypadku nazwą jest „amsaccount-usw22.streaming.media.azure.net”
3. Ścieżka uzyskana w poprzedniej sekcji.  

W efekcie został utworzony następujący adres URL HLS

```
https://amsaccount-usw22.streaming.media.azure.net/fd384f76-2d23-4e50-8fad-f9b3ebcd675b/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>Testowanie adresu URL przesyłania strumieniowego

W tym artykule strumień jest testowany za pomocą odtwarzacza Azure Media Player. 

1. Otwórz przeglądarkę i przejdź pod adres [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. W polu **URL:** wklej utworzony adres URL. 
3. Naciśnij pozycję **Aktualizuj odtwarzacz**.

Usługi Azure Media Player można użyć do testowania, ale nie należy jej używać w środowisku produkcyjnym. 

## <a name="clean-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Zazwyczaj należy wyczyścić wszystko z wyjątkiem obiektów, których zamierzasz użyć ponownie (zazwyczaj są to obiekty Transform, obiekty StreamingLocator są utrwalane itd.). Jeśli Twoje konto ma być czyste po przeprowadzeniu eksperymentów, należy usunąć zasoby, których ponowne użycie nie jest planowane.  

Aby usunąć zasób, wybierz operację „Usuń...” niezależnie od tego, który zasób chcesz usunąć.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej. Do tego celu możesz użyć narzędzia **CloudShell**.

W usłudze **CloudShell** uruchom następujące polecenie:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy już wiesz, jak przekazywać, kodować i przesyłać strumieniowo wideo, zobacz następujący artykuł: 

> [!div class="nextstepaction"]
> [Analizowanie plików wideo](analyze-videos-tutorial-with-api.md)
