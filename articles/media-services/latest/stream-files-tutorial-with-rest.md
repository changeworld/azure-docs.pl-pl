---
title: Kodowanie pliku zdalnego na podstawie adresu URL i przesyłanie strumieniowe przy użyciu usługi Azure Media Services — usługa REST | Microsoft Docs
description: Wykonaj kroki opisane w tym samouczku, aby zakodować plik na podstawie adresu URL i przesłać strumieniowo zawartość za pośrednictwem usługi Azure Media Services, korzystając z usługi REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: bb62a28798010d3e18c5f19fa0062001a70b9622
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675649"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---rest"></a>Samouczek: kodowanie pliku zdalnego na podstawie adresu URL i strumieniowego wideo — REST

Usługa Azure Media Services umożliwia kodowanie plików multimedialnych do formatów, które można odtworzyć w różnych przeglądarkach i na różnych urządzeniach. Na przykład może zaistnieć potrzeba strumieniowego odtwarzania treści w formatach HLS lub MPEG DASH firmy Apple. Przed odtwarzaniem strumieniowym należy zakodować wysokiej jakości plik multimediów cyfrowych. Aby uzyskać wskazówki dotyczące kodowania, zobacz temat [Encoding concept](encoding-concept.md) (Koncepcja kodowania).

Ten samouczek zawiera instrukcje pozwalające zakodować plik na podstawie adresu URL i przesłać strumieniowo zawartość wideo za pośrednictwem usługi Azure Media Services, korzystając z usługi REST. 

![Odtwórz wideo](./media/stream-files-tutorial-with-api/final-video.png)

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

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).

    Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services

- Zainstaluj klienta REST programu [Postman](https://www.getpostman.com/), aby uruchomić interfejsy API REST przedstawione w niektórych samouczkach dotyczących AMS REST. 

    W przykładzie użyto programu **Postman**, ale odpowiednie będzie każde narzędzie REST. Można również użyć: programu **Visual Studio Code** z wtyczką REST lub programu **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Pobieranie plików Postman

Sklonuj repozytorium GitHub zawierające kolekcję programu Postman oraz pliki środowiska.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="configure-postman"></a>Konfigurowanie programu Postman

### <a name="configure-the-environment"></a>Konfigurowanie środowiska 

1. Otwórz aplikację **Poster** .
2. Po prawej stronie ekranu wybierz opcję **Zarządzaj środowiskiem**.

    ![Zarządzanie środowiskiem](./media/develop-with-postman/postman-import-env.png)
4. W oknie dialogowym **Zarządzaj środowiskiem** kliknij opcję **Importuj**.
2. Przejdź do pliku `Azure Media Service v3 Environment.postman_environment.json`, który został pobrany podczas klonowania `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Zostanie dodane środowisko **Azure Media Service v3 Environment**.

    > [!Note]
    > Zaktualizuj zmienne dostępu przy użyciu wartości z sekcji **Uzyskiwanie dostępu do interfejsu API usługi Media Services** powyżej.

7. Kliknij dwukrotnie wybrany plik, a następnie wprowadź wartości uzyskane podczas wykonywania kroków [uzyskiwania dostępu do interfejsu API](#access-the-media-services-api).
8. Zamknij okno dialogowe.
9. Z listy rozwijanej wybierz środowisko **Azure Media Service v3 Environment**.

    ![Wybór środowiska](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Konfigurowanie kolekcji

1. Kliknij przycisk **Importuj**, aby zaimportować plik kolekcji.
1. Przejdź do pliku `Media Services v3.postman_collection.json`, który został pobrany podczas klonowania `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Wybierz plik **Media Services v3.postman_collection.json**.

    ![Importowanie pliku](./media/develop-with-postman/postman-import-collection.png)

## <a name="send-requests-using-postman"></a>Wysyłanie żądań przy użyciu programu Postman

W tej sekcji opisano wysyłanie żądań istotnych dla kodowania i tworzenia adresów URL, aby można było przesyłać strumieniowo plik. W szczególności wysyłane są następujące żądania:

1. Pobieranie tokenu usługi Azure AD do uwierzytelniania jednostki usługi
2. Tworzenie zasobu wyjściowego
3. Tworzenie **przekształcenia**
4. Tworzenie **zadania**
5. Tworzenie **lokalizatora przesyłania strumieniowego**
6. Podanie listy ścieżek **lokalizatora przesyłania strumieniowego**

> [!Note]
>  W tym samouczku założono, że tworzysz wszystkie zasoby o unikatowych nazwach.  

### <a name="get-azure-ad-token"></a>Pobieranie tokenu usługi Azure AD 

1. W lewym oknie aplikacji Poster wybierz pozycję "krok 1: pobieranie tokenu uwierzytelniania usługi AAD".
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

1. W lewym oknie aplikacji Poster wybierz pozycję "zasoby".
2. Następnie wybierz opcję „Utwórz lub aktualizuj zasób”.
3. Kliknij pozycję **Wyślij**.

    * Zostanie wysłana następująca operacja **PUT**:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
        ```
    * Operacja ma następującą treść:

        ```json
        {
        "properties": {
            "description": "My Asset",
            "alternateId" : "some GUID"
         }
        }
        ```

### <a name="create-a-transform"></a>Tworzenie przekształcenia

W przypadku kodowania lub przetwarzania zawartości w usłudze Media Services typowym wzorcem postępowania jest skonfigurowanie ustawień kodowania jako przepisu. Następnie przesyła się **zadanie** w celu zastosowania tego przepisu do wideo. Przesłanie nowego zadania dla każdego nowego wideo powoduje zastosowanie tego przepisu do wszystkich wideo w bibliotece. Przepis w usłudze Media Services nazywa się **przekształceniem**. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transform-concept.md). Przykład opisany w tym samouczku definiuje przepis, który umożliwia kodowanie wideo w celu jego przesyłania strumieniowego do różnych urządzeń z systemami iOS i Android. 

Podczas tworzenia nowego wystąpienia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy określić, jakie dane wyjściowe ma ono tworzyć. Wymagany parametr to obiekt **TransformOutput**. Każdy obiekt **TransformOutput** zawiera element **Preset**. Element **Preset** zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania wymaganego obiektu **TransformOutput**. Przykład opisany w tym artykule używa wbudowanego elementu Preset o nazwie **AdaptiveStreaming**. Element Preset umożliwia kodowanie wejściowego wideo do automatycznie generowanej drabiny szybkości transmisji bitów (zestawu par „szybkość transmisji bitów-rozdzielczość”) na podstawie rozdzielczości wejściowej i szybkości transmisji bitów oraz tworzenie plików MP4 zgodnych ze standardem ISO, które zawierają wideo w formacie H.264 i dźwięk w formacie AAC, dla każdej pary „szybkość transmisji bitów-rozdzielczość”. Aby dowiedzieć się więcej na temat elementu Preset, zobacz informacje o [automatycznie generowanej drabinie szybkości transmisji bitów](autogen-bitrate-ladder.md).

Możesz użyć wbudowanych elementów EncoderNamedPreset lub użyć niestandardowych ustawień wstępnych. 

> [!Note]
> Podczas tworzenia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy najpierw sprawdzić, czy taki obiekt już istnieje, używając metody **Get**. W tym samouczku założono, że tworzysz przekształcenie o unikatowej nazwie.

1. W lewym oknie aplikacji Poster wybierz pozycję "kodowanie i analiza".
2. Następnie wybierz pozycję „Utwórz przekształcenie”.
3. Kliknij pozycję **Wyślij**.

    * Zostanie wysłana następująca operacja **PUT**.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
        ```
    * Operacja ma następującą treść:

        ```json
        {
            "properties": {
                "description": "Standard Transform using an Adaptive Streaming encoding preset from the library of built-in Standard Encoder presets",
                "outputs": [
                    {
                    "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                    "preset": {
                        "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                        "presetName": "AdaptiveStreaming"
                    }
                    }
                ]
            }
        }
        ```

### <a name="create-a-job"></a>Tworzenie zadania

Obiekt [Job](https://docs.microsoft.com/rest/api/media/jobs) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie utworzonego obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. Obiekt **Job** określa informacje takie jak lokalizacja wejściowego pliku wideo oraz danych wyjściowych.

W tym przykładzie dane wejściowe zadania są oparte na adresie URL HTTPS ("https: \//nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/").

1. W lewym oknie aplikacji Poster wybierz pozycję "kodowanie i analiza".
2. Następnie wybierz opcję „Utwórz lub aktualizuj zadanie”.
3. Kliknij pozycję **Wyślij**.

    * Zostanie wysłana następująca operacja **PUT**.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
        ```
    * Operacja ma następującą treść:

        ```json
        {
        "properties": {
            "input": {
            "@odata.type": "#Microsoft.Media.JobInputHttp",
            "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
            "files": [
                    "Ignite-short.mp4"
                ]
            },
            "outputs": [
            {
                "@odata.type": "#Microsoft.Media.JobOutputAsset",
                "assetName": "testAsset1"
            }
            ]
        }
        }
        ```

Ukończenie zadania zajmuje trochę czasu, a Ty chcesz otrzymać powiadomienie o tym fakcie. Aby wyświetlić postęp zadania, zaleca się użycie usługi Event Grid. Zaprojektowano ją pod kątem wysokiej dostępności, stałego poziomu wydajności i dynamicznej skalowalności. Dzięki usłudze Event Grid Twoje aplikacje mogą nasłuchiwać zdarzeń i reagować na zdarzenia w praktycznie wszystkich usługach platformy Azure, a także źródłach niestandardowych. Prosta, reaktywna obsługa zdarzeń oparta na protokole HTTP pomaga w tworzeniu wydajnych rozwiązań za pośrednictwem inteligentnego filtrowania i routingu zdarzeń.  Zobacz [Kierowanie zdarzeń do niestandardowego internetowego punktu końcowego](job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled (Zaplanowane)** , **Queued (W kolejce)** , **Processing (Przetwarzane)** , **Finished (Zakończone)** (stan końcowy). Jeśli zadanie napotka błąd, może być w stanie **Error (Błąd)** . Jeśli zadanie jest w trakcie anulowania, może być w stanie **Canceling (Anulowanie)** , a po zakończeniu tej operacji w stanie **Canceled (Anulowane)** .

#### <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="create-a-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego

Po zakończeniu kodowania następnym krokiem jest udostępnienie wideo w **zasobie** wyjściowym klientom na potrzeby odtwarzania. Działanie to można wykonać w dwóch krokach: najpierw należy utworzyć [lokalizator przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators), a następnie utworzyć adresy URL przesyłania strumieniowego, których mogą używać klienci. 

Proces tworzenia **lokalizatora przesyłania strumieniowego** jest nazywany publikowaniem. Domyślnie **lokalizator przesyłania strumieniowego** jest ważny natychmiast po wykonaniu wywołań interfejsu API i aż do jego usunięcia, chyba że skonfigurujesz opcjonalne czasy rozpoczęcia i zakończenia. 

Podczas tworzenia [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators)należy określić żądany **StreamingPolicyName**. W tym przykładzie będziesz przesyłać strumieniowo zawartość w postaci nieoczyszczonej (lub nieszyfrowanej), więc zostanie użyta wstępnie zdefiniowana zasada "Predefined_ClearStreamingOnly".

> [!IMPORTANT]
> W przypadku korzystania z niestandardowego elementu [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i używać ich ponownie dla obiektów StreamingLocator zawsze, gdy są potrzebne takie same opcje szyfrowania i protokoły. 

Konto usługi Media Service jest objęte limitem przydziału dotyczącym liczby pozycji **zasad przesyłania strumieniowego**. Nie należy tworzyć nowych **zasad przesyłania strumieniowego** dla każdego **lokalizatora przesyłania strumieniowego**.

1. W lewym oknie aplikacji Poster wybierz pozycję "zasady przesyłania strumieniowego".
2. Następnie wybierz pozycję „Utwórz lokalizatora przesyłania strumieniowego”.
3. Kliknij pozycję **Wyślij**.

    * Zostanie wysłana następująca operacja **PUT**.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingPolicies/:streamingPolicyName?api-version={{api-version}}
        ```
    * Operacja ma następującą treść:

        ```json
        {
            "properties":{
            "assetName": "{{assetName}}",
            "streamingPolicyName": "{{streamingPolicyName}}"
            }
        }
        ```

### <a name="list-paths-and-build-streaming-urls"></a>Ścieżki listy i adresy URL przesyłania strumieniowego kompilacji

#### <a name="list-paths"></a>Ścieżki listy

Teraz, po utworzeniu obiektu [Lokalizator przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators), możesz pobrać adresy URL przesyłania strumieniowego.

1. W lewym oknie aplikacji Poster wybierz pozycję "zasady przesyłania strumieniowego".
2. Następnie wybierz opcję „Ścieżki listy”.
3. Kliknij pozycję **Wyślij**.

    * Zostanie wysłana następująca operacja **POST**.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName/listPaths?api-version={{api-version}}
        ```
        
    * Operacja nie ma treści:
        
4. Zanotuj jedną ze ścieżek, której chcesz użyć do przesyłania strumieniowego. Użyjesz jej w kolejnej sekcji. W tym przypadku zostały zwrócone następujące ścieżki:
    
    ```
    "streamingPaths": [
        {
            "streamingProtocol": "Hls",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)"
            ]
        },
        {
            "streamingProtocol": "Dash",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=mpd-time-csf)"
            ]
        },
        {
            "streamingProtocol": "SmoothStreaming",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest"
            ]
        }
    ]
    ```

#### <a name="build-the-streaming-urls"></a>Tworzenie adresów URL przesyłania strumieniowego

W tej sekcji utworzymy adres URL przesyłania strumieniowego HLS. Adresy URL składają się z następujących wartości:

1. Protokół, przy użyciu którego są wysyłane dane. W tym przypadku „https”.

    > [!NOTE]
    > Jeśli odtwarzacz jest hostowany w witrynie korzystającej z protokołu HTTPS, zmień adres URL tak, aby zawierał ciąg „https”.

2. Nazwa hosta StreamingEndpoint. W tym przypadku nazwą jest „amsaccount-usw22.streaming.media.azure.net”.

    W celu pobrania nazwy hosta można użyć następującej operacji GET:
    
    ```
    https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingEndpoints/default?api-version={{api-version}}
    ```
    
3. Ścieżka uzyskana w poprzedniej sekcji (tworzenie listy ścieżek).  

W efekcie został utworzony następujący adres URL HLS

```
https://amsaccount-usw22.streaming.media.azure.net/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>Testowanie adresu URL przesyłania strumieniowego


> [!NOTE]
> Upewnij się, że **punkt końcowy przesyłania strumieniowego**, z którego chcesz strumieniowo przesyłać zawartość, działa.

W tym artykule strumień jest testowany za pomocą odtwarzacza Azure Media Player. 

1. Otwórz przeglądarkę i przejdź pod adres [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. W polu **URL:** wklej utworzony adres URL. 
3. Naciśnij pozycję **Aktualizuj odtwarzacz**.

Usługi Azure Media Player można użyć do testowania, ale nie należy jej używać w środowisku produkcyjnym. 

## <a name="clean-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Zazwyczaj należy wyczyścić wszystko z wyjątkiem obiektów, których zamierzasz użyć ponownie (zazwyczaj są to obiekty **Transform**, obiekty **Lokalizator przesyłania strumieniowego** są utrwalane itd.). Jeśli Twoje konto ma być czyste po przeprowadzeniu eksperymentów, należy usunąć zasoby, których ponowne użycie nie jest planowane.  

Aby usunąć zasób, wybierz operację „Usuń...” niezależnie od tego, który zasób chcesz usunąć.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej.  

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy już wiesz, jak przekazywać, kodować i przesyłać strumieniowo wideo, zobacz następujący artykuł: 

> [!div class="nextstepaction"]
> [Analizowanie plików wideo](analyze-videos-tutorial-with-api.md)
