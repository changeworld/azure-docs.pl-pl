---
title: Przekazywanie, kodowanie i przesyłanie strumieniowe przy użyciu usługi Azure Media Services v3 | Dokumentacja firmy Microsoft
description: Wykonaj kroki tego samouczka, aby przekazać plik i kodowanie wideo i przesyłanie strumieniowe zawartości za pomocą usługi Media Services v3.
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
ms.openlocfilehash: 46a1add812d5721e36d8af030195c42542dc360c
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273244"
---
# <a name="tutorial-upload-encode-and-stream-videos"></a>Samouczek: Przekazywanie, kodowanie i przesyłanie strumieniowe klipów wideo

> [!NOTE]
> Mimo że w tym samouczku użyto [zestawu .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) przykłady ogólne kroki są takie same dla [interfejsu API REST](https://docs.microsoft.com/rest/api/media/liveevents), [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest), lub inne obsługiwane [zestawów SDK](media-services-apis-overview.md#sdks) .

Usługa Azure Media Services umożliwia kodowanie plików multimedialnych do formatów, które można odtworzyć w różnych przeglądarkach i na różnych urządzeniach. Na przykład może zaistnieć potrzeba strumieniowego odtwarzania treści w formatach HLS lub MPEG DASH firmy Apple. Przed odtwarzaniem strumieniowym należy zakodować wysokiej jakości plik multimediów cyfrowych. Aby uzyskać wskazówki dotyczące kodowania, zobacz temat [Encoding concept](encoding-concept.md) (Koncepcja kodowania). W tym samouczku opisano przekazanie lokalnego pliku wideo oraz jego kodowanie. Kodowana jest również zawartość, która jest udostępniana za pośrednictwem adresu URL protokołu HTTPS. Aby uzyskać więcej informacji, zobacz temat [Create a job input from an HTTP(s) URL](job-input-from-http-how-to.md) (Tworzenie danych wejściowych zadania przy użyciu adresu URL protokołów HTTP).

![Odtwarzanie wideo](./media/stream-files-tutorial-with-api/final-video.png)

Ten samouczek przedstawia sposób wykonania następujących czynności:    

> [!div class="checklist"]
> * Pobieranie przykładowej aplikacji opisanej w temacie
> * Sprawdzanie kodu operacji przekazywania, kodowania i odtwarzania strumieniowego
> * Uruchamianie aplikacji
> * Testowanie adresu URL przesyłania strumieniowego
> * Oczyszczanie zasobów

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz zainstalowanego programu Visual Studio, możesz pobrać program [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).<br/>Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Będą one potrzebne w celu uzyskania dostępu do interfejsu API.

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładu

Sklonuj repozytorium GitHub zawierające przykład przesyłania strumieniowego platformy .NET na swoją maszynę za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Przykład znajduje się w folderze [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

Otwórz plik [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) z pobranego projektu. Zastąp wartości przy użyciu poświadczeń uzyskanych w sekcji z opisem [uzyskiwania dostępu do interfejsów API](access-api-cli-how-to.md).

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Sprawdzanie kodu operacji przekazywania, kodowania i odtwarzania strumieniowego

W tej sekcji są analizowane funkcje zdefiniowane w pliku [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) projektu *UploadEncodeAndStreamFiles*.

W przykładzie są wykonywane następujące akcje:

1. Tworzenie nowego **przekształcenia** (najpierw następuje sprawdzenie, czy podane przekształcenie istnieje). 
2. Tworzenie **zasobu** danych wyjściowych, który zostanie użyty jako dane wyjściowe **zadania** kodowania.
3. Tworzenie **zasobu** danych wejściowych oraz przekazanie do niego wybranego lokalnego pliku wideo. Zasób jest używany jako dane wejściowe zadania. 
4. Przesyłanie zadania kodowania przy użyciu utworzonych danych wejściowych i wyjściowych.
5. Sprawdza stan zadania.
6. Tworzy **lokalizator przesyłania strumieniowego**.
7. Utworzenie adresów URL przesyłania strumieniowego.

### <a name="a-idstartusingdotnet-start-using-media-services-apis-with-net-sdk"></a><a id="start_using_dotnet" />Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Tworzenie zasobu wejściowego i przekazywanie do niego pliku lokalnego 

Funkcja **CreateInputAsset** tworzy nowy [zasób](https://docs.microsoft.com/rest/api/media/assets) wejściowy i przekazuje do niego określony lokalny plik wideo. Ten **zasób** służy jako dane wejściowe zadania kodowania. W usłudze Media Services w wersji 3 danymi wejściowymi **zadania** może być **zasób** lub zawartość udostępniona dla konta usługi Media Services za pośrednictwem adresów URL protokołu HTTPS. Jeśli chcesz dowiedzieć się, jak kodować, korzystając z adresu URL protokołu HTTPS jako wejścia, zobacz [ten](job-input-from-http-how-to.md) artykuł.  

W usłudze Media Services 3 do przekazywania plików służą interfejsy API usługi Azure Storage. Przedstawia to poniższy fragment kodu dla platformy .NET.

Poniższa funkcja wykonuje następujące akcje:

* Tworzenie **zasobu** 
* Pobranie zapisywalnego [adresu URL sygnatury dostępu współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do [kontenera zasobu w magazynie](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)
* Przekazanie pliku do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu współdzielonego

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Tworzenie zasobu wyjściowego na potrzeby przechowywania wyników zadania 

[Zasób](https://docs.microsoft.com/rest/api/media/assets) wyjściowy przechowuje wynik zadania kodowania. Projekt definiuje funkcję **DownloadResults**, która pobiera wyniki z zasobu wyjściowego do folderu wyjściowego, umożliwiając zapoznanie się z nimi.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Tworzenie przekształcenia i zadania kodującego przekazany plik

W przypadku kodowania lub przetwarzania zawartości w usłudze Media Services typowym wzorcem postępowania jest skonfigurowanie ustawień kodowania jako przepisu. Następnie przesyła się **zadanie** w celu zastosowania tego przepisu do wideo. Przesłanie nowego zadania dla każdego nowego wideo powoduje zastosowanie tego przepisu do wszystkich wideo w bibliotece. Przepis w usłudze Media Services nazywa się **przekształceniem**. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transform-concept.md). Przykład opisany w tym samouczku definiuje przepis, który umożliwia kodowanie wideo w celu jego przesyłania strumieniowego do różnych urządzeń z systemami iOS i Android. 

#### <a name="transform"></a>Przekształcanie

Podczas tworzenia nowego wystąpienia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy określić, jakie dane wyjściowe ma ono tworzyć. Wymagany parametr to obiekt **TransformOutput**, jak pokazano w poniższym kodzie. Każdy obiekt **TransformOutput** zawiera element **Preset**. Element **Preset** zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania wymaganego obiektu **TransformOutput**. Przykład opisany w tym artykule używa wbudowanego elementu Preset o nazwie **AdaptiveStreaming**. Element Preset umożliwia kodowanie wejściowego wideo do automatycznie generowanej drabiny szybkości transmisji bitów (zestawu par „szybkość transmisji bitów-rozdzielczość”) na podstawie rozdzielczości wejściowej i szybkości transmisji bitów oraz tworzenie plików MP4 zgodnych ze standardem ISO, które zawierają wideo w formacie H.264 i dźwięk w formacie AAC, dla każdej pary „szybkość transmisji bitów-rozdzielczość”. Aby dowiedzieć się więcej na temat elementu Preset, zobacz informacje o [automatycznie generowanej drabinie szybkości transmisji bitów](autogen-bitrate-ladder.md).

Możesz użyć wbudowanych elementów EncoderNamedPreset lub użyć niestandardowych ustawień wstępnych. Aby uzyskać więcej informacji, zobacz temat [How to customize encoder presets](customize-encoder-presets-how-to.md) (Dostosowywanie ustawień wstępnych kodera).

Podczas tworzenia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy najpierw sprawdzić, czy taki obiekt już istnieje, używając metody **Get**, tak jak przedstawiono w poniższym kodzie.  W przypadku usługi Media Services 3 metody **Get** wywołane dla obiektów zwracają **wartość null**, jeśli obiekt nie istnieje (sprawdzana jest nazwa bez uwzględniania wielkości liter).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Zadanie

Jak wspomniano powyżej, obiekt [Transform](https://docs.microsoft.com/rest/api/media/transforms) jest przepisem, a obiekt [Job](https://docs.microsoft.com/rest/api/media/jobs) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. Obiekt **Job** określa informacje takie jak lokalizacja wejściowego pliku wideo oraz danych wyjściowych.

W tym przykładzie wejściowy plik wideo został przekazany z maszyny lokalnej. Jeśli chcesz dowiedzieć się, jak kodować, korzystając z adresu URL protokołu HTTPS jako wejścia, zobacz [ten](job-input-from-http-how-to.md) artykuł.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Ukończenie zadania zajmuje trochę czasu, a Ty chcesz otrzymać powiadomienie o tym fakcie. Poniższy przykładowy kod przedstawia sposób sondowania usługi pod kątem stanu [zadania](https://docs.microsoft.com/rest/api/media/jobs). Sondowanie nie jest najlepszym rozwiązaniem w przypadku zastosowań produkcyjnych ze względu na możliwe opóźnienia. Jeśli sondowanie będzie nadużywane w ramach konta, może zostać ograniczone. Deweloperzy zamiast niego powinni używać usługi Event Grid.

Usługę Event Grid zaprojektowano pod kątem wysokiej dostępności, stałego poziomu wydajności i dynamicznej skalowalności. Dzięki usłudze Event Grid Twoje aplikacje mogą nasłuchiwać zdarzeń pochodzących z praktycznie wszystkich usług platformy Azure i ze źródeł niestandardowych oraz reagować na nie. Prosta, reaktywna obsługa zdarzeń oparta na protokole HTTP pomaga w tworzeniu wydajnych rozwiązań za pośrednictwem inteligentnego filtrowania i routingu zdarzeń.  Zobacz [Kierowanie zdarzeń do niestandardowego internetowego punktu końcowego](job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled** (Zaplanowane), **Queued** (W kolejce), **Processing** (Przetwarzane), **Finished** (Zakończone — jest to stan końcowy). Jeśli zadanie napotka błąd, może być w stanie **Error (Błąd)** . Jeśli zadanie jest w trakcie anulowania, może być w stanie **Canceling (Anulowanie)** , a po zakończeniu tej operacji w stanie **Canceled (Anulowane)** .

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="get-a-streaming-locator"></a>Pobieranie lokalizatora przesyłania strumieniowego

Po zakończeniu kodowania następnym krokiem jest udostępnienie klientom w zasobie wyjściowym pliku wideo, który można odtwarzać. Działanie to można wykonać w dwóch krokach: najpierw należy utworzyć [lokalizator przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators), a następnie utworzyć adresy URL przesyłania strumieniowego, których mogą używać klienci. 

Proces tworzenia **lokalizatora przesyłania strumieniowego** jest nazywany publikowaniem. Domyślnie **lokalizator przesyłania strumieniowego** jest ważny natychmiast po wykonaniu wywołań interfejsu API i aż do jego usunięcia, chyba że skonfigurujesz opcjonalne czasy rozpoczęcia i zakończenia. 

Podczas tworzenia obiektu [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) musisz określić żądany element **StreamingPolicyName**. W tym przykładzie zawartość będzie przesyłana strumieniowo w postaci jawnej (bez szyfrowania), dzięki czemu można użyć wstępnie zdefiniowanych zasad przesyłania strumieniowego bez szyfrowania (**PredefinedStreamingPolicy.ClearStreamingOnly**).

> [!IMPORTANT]
> W przypadku korzystania z niestandardowych [zasad przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingpolicies) należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i używać ich ponownie dla obiektów StreamingLocator zawsze, gdy są potrzebne takie same opcje szyfrowania i protokoły. Konto usługi Media Service jest objęte limitem przydziału dotyczącym liczby pozycji zasad przesyłania strumieniowego. Nie należy tworzyć nowych zasad przesyłania strumieniowego dla każdego lokalizatora przesyłania strumieniowego.

W poniższym kodzie przyjęto, że funkcja jest wywoływana za pomocą unikatowego elementu locatorName.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Przykład przedstawiony w tym temacie dotyczy przesyłania strumieniowego, lecz tego samego wywołania można użyć do utworzenia lokalizatora przesyłania strumieniowego na potrzeby dostarczania wideo przy użyciu pobierania progresywnego.

### <a name="get-streaming-urls"></a>Pobieranie adresów URL przesyłania strumieniowego

Teraz, po utworzeniu [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators), możesz pobrać adresy URL przesyłania strumieniowego, jak pokazano w metodzie **GetStreamingURLs**. Aby utworzyć adres URL, musisz połączyć nazwę hosta [punktu końcowego przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints) i ścieżkę **lokalizatora przesyłania strumieniowego**. W tym przykładzie jest używany *domyślny* **punkt końcowy przesyłania strumieniowego**. Po pierwszym utworzeniu konta usługi Media Service ten *domyślny* **punkt końcowy przesyłania strumieniowego** będzie zatrzymany, więc należy wywołać metodę **Start**.

> [!NOTE]
> W przypadku tej metody jest potrzebny element locatorName, który został użyty podczas tworzenia **lokalizatora przesyłania strumieniowego** dla zasobu wyjściowego.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Zazwyczaj należy wyczyścić wszystko z wyjątkiem obiektów, których zamierzasz użyć ponownie (zazwyczaj są to obiekty Transform, obiekty StreamingLocator są utrwalane itd.). Jeśli Twoje konto ma być czyste po przeprowadzeniu eksperymentów, należy usunąć zasoby, których ponowne użycie nie jest planowane.  Na przykład następujący kod usuwa zadania.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

1. Naciśnij klawisze Ctrl+F5, aby uruchomić aplikację *EncodeAndStreamFiles*.
2. Skopiuj jeden z adresów URL przesyłania strumieniowego z konsoli.

Ten przykład umożliwia wyświetlenie adresów URL, których można użyć do odtworzenia wideo za pomocą różnych protokołów:

![Dane wyjściowe](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testowanie adresu URL przesyłania strumieniowego

W tym artykule strumień jest testowany za pomocą odtwarzacza Azure Media Player. 

> [!NOTE]
> Jeśli odtwarzacz jest hostowany w witrynie korzystającej z protokołu HTTPS, zmień adres URL tak, aby zawierał ciąg „https”.

1. Otwórz przeglądarkę i przejdź pod adres [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. W polu **Adres URL** wklej jedną z wartości adresu URL przesyłania strumieniowego otrzymanych po uruchomieniu aplikacji. 
3. Naciśnij pozycję **Aktualizuj odtwarzacz**.

Usługi Azure Media Player można użyć do testowania, ale nie należy jej używać w środowisku produkcyjnym. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Wielowątkowość

Zestawy SDK usługi Azure Media Services 3 nie są bezpieczne wątkowo. Podczas opracowywania aplikacji wielowątkowej należy wygenerować nowy obiekt AzureMediaServicesClient dla każdego wątku i użyć go.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Prześlij opinię i pobieranie aktualizacji

Zapoznaj się z [społeczności usługi Azure Media Services](media-services-community.md) artykuł, aby wyświetlić różne sposoby zadawaj pytania, Prześlij opinię i pobrać aktualizacje o usłudze Media Services.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy już wiesz, jak przekazywać, kodować i przesyłać strumieniowo wideo, zobacz następujący artykuł: 

> [!div class="nextstepaction"]
> [Analizowanie plików wideo](analyze-videos-tutorial-with-api.md)
