---
title: Przekazywanie, kodowanie i przesyłanie strumieniowe za pomocą usługi Media Services w wersji 3
titleSuffix: Azure Media Services
description: Samouczek pokazujący sposób przekazywania pliku, kodowania wideo i przesyłania strumieniowego zawartości za pomocą usługi Azure Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/22/2019
ms.author: juliako
ms.openlocfilehash: 4e40d26e392219fb751328bc54855d87e80bae19
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345993"
---
# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>Samouczek: Przesyłanie, kodowanie i przesyłanie strumieniowe filmów za pomocą usługi Media Services w wersji 3

> [!NOTE]
> Mimo że w tym samouczku użyto przykładów [zestawu .NET SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) ogólne kroki są takie same dla [interfejsu API REST,](https://docs.microsoft.com/rest/api/media/liveevents) [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)lub innych [obsługiwanych zestawu SDK.](media-services-apis-overview.md#sdks)

Usługa Azure Media Services umożliwia kodowanie plików multimedialnych do formatów odtwarzanych w wielu różnych przeglądarkach i urządzeniach. Na przykład może zaistnieć potrzeba strumieniowego odtwarzania treści w formatach HLS lub MPEG DASH firmy Apple. Przed odtwarzaniem strumieniowym należy zakodować wysokiej jakości plik multimediów cyfrowych. Aby uzyskać pomoc dotyczącą kodowania, zobacz [Koncepcja kodowania](encoding-concept.md). W tym samouczku opisano przekazanie lokalnego pliku wideo oraz jego kodowanie. Można również zakodować zawartość, która jest dostępna za pośrednictwem adresu URL HTTPS. Aby uzyskać więcej informacji, zobacz temat [Create a job input from an HTTP(s) URL](job-input-from-http-how-to.md) (Tworzenie danych wejściowych zadania przy użyciu adresu URL protokołów HTTP).

![Odtwarzanie klipu wideo za pomocą programu Azure Media Player](./media/stream-files-tutorial-with-api/final-video.png)

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Pobierz przykładową aplikację opisaną w temacie.
> * Sprawdź kod, który przekazuje, koduje i strumieni.
> * Uruchom aplikację.
> * Przetestuj adres URL przesyłania strumieniowego.
> * Oczyszczenie zasobów.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz zainstalowanego programu Visual Studio, możesz uzyskać [program Visual Studio Community 2017.](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).<br/>Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Musisz ich używać, aby uzyskać dostęp do interfejsu API.

## <a name="download-and-set-up-the-sample"></a>Pobierz i skonfiguruj przykład

Sklonuj repozytorium GitHub, które ma próbkę .NET przesyłaną strumieniowo na komputerze, używając następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Przykład znajduje się w folderze [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

Otwórz [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) w pobranym projekcie. Zastąp wartości przy użyciu poświadczeń uzyskanych w sekcji z opisem [uzyskiwania dostępu do interfejsów API](access-api-cli-how-to.md).

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Sprawdzanie kodu operacji przekazywania, kodowania i odtwarzania strumieniowego

W tej sekcji są analizowane funkcje zdefiniowane w pliku [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) projektu *UploadEncodeAndStreamFiles*.

W przykładzie są wykonywane następujące akcje:

1. Tworzy nowe **przekształcenie** (najpierw sprawdza, czy istnieje określony transform).
2. Tworzy **zasób** wyjściowy, który jest używany jako dane wyjściowe **zadania**kodowania.
3. Tworzenie **zasobu** danych wejściowych oraz przekazanie do niego wybranego lokalnego pliku wideo. Zasób jest używany jako dane wejściowe zadania.
4. Przesyłanie zadania kodowania przy użyciu utworzonych danych wejściowych i wyjściowych.
5. Sprawdza stan zadania.
6. Tworzy **lokalizator przesyłania strumieniowego**.
7. Utworzenie adresów URL przesyłania strumieniowego.

### <a name="start-using-media-services-apis-with-net-sdk"></a><a id="start_using_dotnet" />Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia potrzebne do połączenia klienta z platformą Azure przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Tworzenie zasobu wejściowego i przekazywanie do niego pliku lokalnego

Funkcja **CreateInputAsset** tworzy nowy [zasób](https://docs.microsoft.com/rest/api/media/assets) wejściowy i przekazuje do niego określony lokalny plik wideo. Ten **zasób** jest używany jako dane wejściowe do zadania kodowania. W układzie Media Services w wersji 3 dane wejściowe do **zadania** mogą być **zasobem** lub zawartością udostępnianą kontu usługi Media Services za pośrednictwem adresów URL HTTPS. Aby dowiedzieć się, jak kodować z adresu URL HTTPS, zobacz [ten](job-input-from-http-how-to.md) artykuł.

W usłudze Media Services 3 do przekazywania plików służą interfejsy API usługi Azure Storage. Przedstawia to poniższy fragment kodu dla platformy .NET.

Poniższa funkcja wykonuje następujące akcje:

* Tworzy **zasób**.
* Pobiera zapisywalny [adres URL sygnatury dostępu Współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do [kontenera zasobu w magazynie.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)

    Jeśli do uzyskania adresów URL SYGNAtury dostępu Współdzielonego jest używanie funkcji [ListContainerSas](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) zasobu, należy pamiętać, że funkcja zwraca wiele adresów URL Sygnatury dostępu Współdzielonego, ponieważ dla każdego konta magazynu są dwa klucze konta magazynu. Konto magazynu ma dwa klucze, ponieważ umożliwia bezproblemowe obracanie kluczy konta magazynu (na przykład zmień jeden podczas korzystania z drugiego, a następnie zacznij używać nowego klucza i obróć drugi klucz). Adres URL 1 sygnatury dostępu Współdzielonego reprezentuje klucz magazynu1 i drugi klucz magazynu2.
* Przekazuje plik do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu Współdzielonego.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Tworzenie zasobu wyjściowego na potrzeby przechowywania wyników zadania

[Zasób](https://docs.microsoft.com/rest/api/media/assets) wyjściowy przechowuje wynik zadania kodowania. Projekt definiuje funkcję **DownloadResults**, która pobiera wyniki z zasobu wyjściowego do folderu wyjściowego, umożliwiając zapoznanie się z nimi.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Tworzenie przekształcenia i zadania kodującego przekazany plik

Podczas kodowania lub przetwarzania zawartości w programie Media Services jest to typowy wzorzec, aby skonfigurować ustawienia kodowania jako przepis. Następnie przesyła się **zadanie** w celu zastosowania tego przepisu do wideo. Przesyłając nowe zadania dla każdego nowego filmu, stosujesz ten przepis do wszystkich filmów w bibliotece. Przepis w programie Media Services jest nazywany **transformacją**. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transform-concept.md). Przykład opisany w tym samouczku definiuje przepis, który umożliwia kodowanie wideo w celu jego przesyłania strumieniowego do różnych urządzeń z systemami iOS i Android.

#### <a name="transform"></a>Przekształcanie

Podczas tworzenia nowego wystąpienia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy określić, jakie dane wyjściowe ma ono tworzyć. Wymagany parametr to obiekt **TransformOutput**, jak pokazano w poniższym kodzie. Każdy obiekt **TransformOutput** zawiera element **Preset**. Element **Preset** zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania wymaganego obiektu **TransformOutput**. Przykład opisany w tym artykule używa wbudowanego elementu Preset o nazwie **AdaptiveStreaming**. Element Preset umożliwia kodowanie wejściowego wideo do automatycznie generowanej drabiny szybkości transmisji bitów (zestawu par „szybkość transmisji bitów-rozdzielczość”) na podstawie rozdzielczości wejściowej i szybkości transmisji bitów oraz tworzenie plików MP4 zgodnych ze standardem ISO, które zawierają wideo w formacie H.264 i dźwięk w formacie AAC, dla każdej pary „szybkość transmisji bitów-rozdzielczość”. Aby dowiedzieć się więcej na temat elementu Preset, zobacz informacje o [automatycznie generowanej drabinie szybkości transmisji bitów](autogen-bitrate-ladder.md).

Możesz użyć wbudowanych elementów EncoderNamedPreset lub użyć niestandardowych ustawień wstępnych. Aby uzyskać więcej informacji, zobacz temat [How to customize encoder presets](customize-encoder-presets-how-to.md) (Dostosowywanie ustawień wstępnych kodera).

Podczas tworzenia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy najpierw sprawdzić, czy taki obiekt już istnieje, używając metody **Get**, tak jak przedstawiono w poniższym kodzie. W przypadku usługi Media Services 3 metody **Get** wywołane dla obiektów zwracają **wartość null**, jeśli obiekt nie istnieje (sprawdzana jest nazwa bez uwzględniania wielkości liter).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Zadanie

Jak wspomniano powyżej, obiekt [Transform](https://docs.microsoft.com/rest/api/media/transforms) jest przepisem, a obiekt [Job](https://docs.microsoft.com/rest/api/media/jobs) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. Obiekt **Job** określa informacje takie jak lokalizacja wejściowego pliku wideo oraz danych wyjściowych.

W tym przykładzie wejściowy plik wideo został przekazany z maszyny lokalnej. Jeśli chcesz dowiedzieć się, jak kodować z adresu URL HTTPS, zobacz [ten](job-input-from-http-how-to.md) artykuł.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Ukończenie zadania zajmuje trochę czasu, a Ty chcesz otrzymać powiadomienie o tym fakcie. Poniższy przykład kodu pokazuje, jak sondować usługę pod kątem stanu [zadania](https://docs.microsoft.com/rest/api/media/jobs). Sondowanie nie jest zalecane najlepsze rozwiązanie dla aplikacji produkcyjnych ze względu na potencjalne opóźnienie. Jeśli sondowanie będzie nadużywane w ramach konta, może zostać ograniczone. Deweloperzy zamiast niego powinni używać usługi Event Grid.

Usługę Event Grid zaprojektowano pod kątem wysokiej dostępności, stałego poziomu wydajności i dynamicznej skalowalności. Dzięki usłudze Event Grid Twoje aplikacje mogą nasłuchiwać zdarzeń pochodzących z praktycznie wszystkich usług platformy Azure i ze źródeł niestandardowych oraz reagować na nie. Prosta, reaktywna obsługa zdarzeń oparta na protokole HTTP pomaga w tworzeniu wydajnych rozwiązań za pośrednictwem inteligentnego filtrowania i routingu zdarzeń.  Zobacz [Kierowanie zdarzeń do niestandardowego internetowego punktu końcowego](job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled (Zaplanowane)**, **Queued (W kolejce)**, **Processing (Przetwarzane)**, **Finished (Zakończone)** (stan końcowy). Jeśli zadanie napotka błąd, może być w stanie **Error (Błąd)**. Jeśli zadanie jest w trakcie anulowania, otrzymasz **Canceling** i **Canceled** po zakończeniu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="get-a-streaming-locator"></a>Pobieranie lokalizatora przesyłania strumieniowego

Po zakończeniu kodowania następnym krokiem jest udostępnienie klientom w zasobie wyjściowym pliku wideo, który można odtwarzać. Można go udostępnić w dwóch krokach: najpierw utwórz [lokalizator przesyłania strumieniowego,](https://docs.microsoft.com/rest/api/media/streaminglocators)a po drugie, skompiluj adresy URL przesyłania strumieniowego, z których mogą korzystać klienci.

Proces tworzenia **lokalizatora przesyłania strumieniowego** jest nazywany publikowaniem. Domyślnie **lokalizator przesyłania strumieniowego** jest prawidłowy natychmiast po wywołaniu interfejsu API i trwa do momentu jego usunięcia, chyba że skonfigurujesz opcjonalne godziny rozpoczęcia i zakończenia.

Podczas tworzenia [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators)należy określić żądaną **nazwę StreamingPolicyName**. W tym przykładzie będziesz przesyłać strumieniowo zawartość w postaci przezroczystej (lub niezaszyfrowanej), więc używana jest wstępnie zdefiniowana, przejrzysta zasada przesyłania strumieniowego (**PredefinedStreamingPolicy.ClearStreamingOnly).**

> [!IMPORTANT]
> W przypadku korzystania z niestandardowych [zasad przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingpolicies) należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i używać ich ponownie dla obiektów StreamingLocator zawsze, gdy są potrzebne takie same opcje szyfrowania i protokoły. Konto usługi Media Service jest objęte limitem przydziału dotyczącym liczby pozycji zasad przesyłania strumieniowego. Nie należy tworzyć nowe zasady przesyłania strumieniowego dla każdego lokalizatora przesyłania strumieniowego.

Poniższy kod zakłada, że wywołujesz funkcję z unikatowym locatorName.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Przykład przedstawiony w tym temacie dotyczy przesyłania strumieniowego, lecz tego samego wywołania można użyć do utworzenia lokalizatora przesyłania strumieniowego na potrzeby dostarczania wideo przy użyciu pobierania progresywnego.

### <a name="get-streaming-urls"></a>Pobieranie adresów URL przesyłania strumieniowego

Teraz, po utworzeniu [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators), możesz pobrać adresy URL przesyłania strumieniowego, jak pokazano w metodzie **GetStreamingURLs**. Aby utworzyć adres URL, musisz połączyć nazwę hosta [punktu końcowego przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints) i ścieżkę **lokalizatora przesyłania strumieniowego**. W tym przykładzie jest używany *domyślny* **punkt końcowy przesyłania strumieniowego**. Po pierwszym utworzeniu konta usługi Media Service ten *domyślny* **punkt końcowy przesyłania strumieniowego** będzie zatrzymany, więc należy wywołać metodę **Start**.

> [!NOTE]
> W przypadku tej metody jest potrzebny element locatorName, który został użyty podczas tworzenia **lokalizatora przesyłania strumieniowego** dla zasobu wyjściowego.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Ogólnie rzecz biorąc należy oczyścić wszystko z wyjątkiem obiektów, które planujesz ponownie użyć (zazwyczaj będziesz ponownie używać przekształceń i będziesz utrzymywać StreamingLocators, itp.). Jeśli chcesz, aby Twoje konto było czyste po eksperymentowaniu, usuń zasoby, których nie zamierzasz ponownie używać. Na przykład poniższy kod usuwa zadania:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

1. Naciśnij klawisze Ctrl+F5, aby uruchomić aplikację *EncodeAndStreamFiles.*
2. Skopiuj jeden z adresów URL przesyłania strumieniowego z konsoli.

Ten przykład umożliwia wyświetlenie adresów URL, których można użyć do odtworzenia wideo za pomocą różnych protokołów:

![Przykładowe dane wyjściowe przedstawiające adresy URL dla strumieniowego przesyłania wideo przez usługę Media Services](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testowanie adresu URL przesyłania strumieniowego

W tym artykule strumień jest testowany za pomocą odtwarzacza Azure Media Player.

> [!NOTE]
> Jeśli odtwarzacz jest hostowany w witrynie korzystającej z protokołu HTTPS, zmień adres URL tak, aby zawierał ciąg „https”.

1. Otwórz przeglądarkę internetową [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)i przejdź do pliku .
2. W polu **URL:** wklej jedną z wartości adresu URL przesyłania strumieniowego, które zostały wyświetlone po uruchomieniu aplikacji.
3. Wybierz **pozycję Aktualizuj odtwarzacz**.

Program Azure Media Player może służyć do testowania, ale nie powinny być używane w środowisku produkcyjnym.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Wielowątkowość

Zestawy SDK usługi Azure Media Services w wersji 3 nie są bezpieczne dla wątków. Podczas tworzenia aplikacji wielowątkowej, należy wygenerować i używać nowego obiektu AzureMediaServicesClient na wątek.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy już wiesz, jak przekazywać, kodować i przesyłać strumieniowo wideo, zobacz następujący artykuł: 

> [!div class="nextstepaction"]
> [Analizowanie plików wideo](analyze-videos-tutorial-with-api.md)
