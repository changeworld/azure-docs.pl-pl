---
title: Analizowanie klipów wideo za pomocą usługi Media Services w wersji 3
titleSuffix: Azure Media Services
description: Dowiedz się, jak analizować klipy wideo przy użyciu usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: b7864d89cc14a1473fd43e94bfe74c368bcb391d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349480"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Samouczek: Analizowanie klipów wideo za pomocą usługi Media Services w wersji 3

> [!NOTE]
> Mimo że w tym samouczku użyto przykładów [zestawu .NET SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) ogólne kroki są takie same dla [interfejsu API REST,](https://docs.microsoft.com/rest/api/media/liveevents) [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)lub innych [obsługiwanych zestawu SDK.](media-services-apis-overview.md#sdks)

W tym samouczku przedstawiono sposób analizowania wideo za pomocą usługi Azure Media Services. Istnieje wiele scenariuszy, w przypadku których głęboka analiza zarejestrowanego wideo lub dźwięku może być przydatna. Na przykład organizacje mogą zamieniać mowę na tekst w celu przekształcenia nagrań rozmów działu obsługi klienta w katalog z możliwością wyszukiwania, indeksami i pulpitami nawigacyjnymi — aby dzięki jego wykorzystaniu zwiększyć zadowolenie klientów. Następnie mogą uzyskać wgląd w swoją działalność. Te spostrzeżenia obejmują listę typowych skarg, źródła takich skarg i inne przydatne informacje.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Pobierz przykładową aplikację opisaną w temacie.
> * Sprawdź kod, który analizuje określony film wideo.
> * Uruchom aplikację.
> * Zbadaj dane wyjściowe.
> * Oczyszczenie zasobów.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Zgodność, ochrona prywatności i zabezpieczenia
 
Jako ważne przypomnienie musisz przestrzegać wszystkich obowiązujących przepisów podczas korzystania z indeksatora wideo i nie możesz używać indeksatora wideo ani żadnej innej usługi platformy Azure w sposób, który narusza prawa innych osób lub może być szkodliwy dla innych osób. Przed przesłaniem jakichkolwiek filmów, w tym jakichkolwiek danych biometrycznych, do usługi Video Indexer w celu przetwarzania i przechowywania, Użytkownik musi mieć wszystkie odpowiednie prawa, w tym wszystkie odpowiednie zgody, od osób w filmie. Aby dowiedzieć się więcej o zgodności, prywatności i bezpieczeństwie w programie Video Indexer, [postanowienia dotyczące usług Microsoft Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/). W przypadku zobowiązań firmy Microsoft w zakresie ochrony prywatności i postępowania z danymi użytkownika należy zapoznać się z [Zasadami zachowania poufności informacji](https://privacy.microsoft.com/PrivacyStatement)firmy Microsoft, [Postanowieniami dotyczącymi usług online](https://www.microsoft.com/licensing/product-licensing/products) ("OST") i [uzupełnieniem dotyczącym przetwarzania danych](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Dodatkowe informacje o prywatności, w tym dotyczące przechowywania, usuwania/niszczenia danych, są dostępne w OST i [tutaj](../video-indexer/faq.md). Korzystając z Video Indexer, użytkownik wyraża zgodę na przestrzeganie Warunków świadczenia usług Cognitive Services, OST, DPA i Polityki prywatności.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz zainstalowanego programu Visual Studio, pobierz [program Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).<br/>Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Musisz ich używać, aby uzyskać dostęp do interfejsu API.

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładu

Sklonuj repozytorium GitHub zawierające przykład dla platformy .NET na swoją maszynę za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Przykład znajduje się w folderze [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

Otwórz [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) w pobranym projekcie. Zastąp wartości poświadczeniami, które otrzymałeś [w celu uzyskania dostępu do interfejsów API](access-api-cli-how-to.md).

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Badanie kodu do analizy wybranego wideo

W tej sekcji są analizowane funkcje zdefiniowane w pliku [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) projektu *AnalyzeVideos*.

Próbka kończy następujące czynności:

1. Tworzy **przekształcenie** i **zadanie** analizujące wideo.
2. Tworzy **zasób** wejściowy i przekazuje do niego wideo. Zasób jest używany jako dane wejściowe zadania.
3. Tworzy zasób wyjściowy, w którym są przechowywane dane wyjściowe zadania.
4. Przesyła zadanie.
5. Sprawdza stan zadania.
6. Pobiera pliki, które powstały w wyniku uruchomienia zadania.

> [!NOTE]
> Jeśli używasz wstępnych ustawień analizatora wideo lub dźwięku, skorzystaj z witryny Azure Portal i ustaw na koncie 10 jednostek zarezerwowanych multimediów S3. Aby uzyskać więcej informacji, zobacz temat [Scale media processing (Skalowanie przetwarzania multimediów)](media-reserved-units-cli-how-to.md).

### <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Tworzenie zasobu wejściowego i przekazywanie do niego pliku lokalnego 

Funkcja **CreateInputAsset** tworzy nowy [zasób](https://docs.microsoft.com/rest/api/media/assets) wejściowy i przekazuje do niego określony lokalny plik wideo. Ten zasób służy jako dane wejściowe zadania kodowania. W usłudze Media Services 3 danymi wejściowymi zadania może być zasób lub zawartość udostępniona dla konta usługi Media Services za pośrednictwem adresów URL protokołu HTTPS. Aby dowiedzieć się, jak kodować z adresu URL HTTPS, zobacz [ten](job-input-from-http-how-to.md) artykuł.  

W usłudze Media Services 3 do przekazywania plików służą interfejsy API usługi Azure Storage. Przedstawia to poniższy fragment kodu dla platformy .NET.

Następujące funkcje kończy następujące akcje:

* Tworzy zasób.
* Pobiera zapisywalny [adres URL sygnatury dostępu Współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do [kontenera zasobu w magazynie.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)

    Jeśli do uzyskania adresów URL SYGNAtury dostępu Współdzielonego jest używanie funkcji [ListContainerSas](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) zasobu, należy pamiętać, że funkcja zwraca wiele adresów URL Sygnatury dostępu Współdzielonego, ponieważ dla każdego konta magazynu są dwa klucze konta magazynu. Konto magazynu ma dwa klucze, ponieważ umożliwia bezproblemowe obracanie kluczy konta magazynu (na przykład zmień jeden podczas korzystania z drugiego, a następnie zacznij używać nowego klucza i obróć drugi klucz). Adres URL 1 sygnatury dostępu Współdzielonego reprezentuje klucz magazynu1 i drugi klucz magazynu2.
* Przekazuje plik do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu Współdzielonego.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Tworzenie zasobu wyjściowego do przechowywania wyników zadania

[Zasób](https://docs.microsoft.com/rest/api/media/assets) wyjściowy przechowuje wynik zadania. Projekt definiuje funkcję **DownloadResults**, która pobiera wyniki z zasobu wyjściowego do folderu wyjściowego, umożliwiając zapoznanie się z nimi.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Tworzenie przekształcenia i zadania analizującego wideo

Podczas kodowania lub przetwarzania zawartości w programie Media Services jest to typowy wzorzec, aby skonfigurować ustawienia kodowania jako przepis. Następnie przesyła się **zadanie** w celu zastosowania tego przepisu do wideo. Przesyłając nowe oferty pracy dla każdego nowego filmu, stosujesz ten przepis do wszystkich filmów w bibliotece. Przepis w programie Media Services jest nazywany **transformacją**. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transform-concept.md). Przykład opisany w tym samouczku umożliwia zdefiniowanie przepisu, który analizuje określone wideo.

#### <a name="transform"></a>Przekształcanie

Podczas tworzenia nowego wystąpienia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy określić, jakie dane wyjściowe ma ono tworzyć. **TransformOutput** jest wymaganym parametrem. Każdy obiekt **TransformOutput** zawiera element **Preset**. Element **Preset** zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania docelowego obiektu **TransformOutput**. W tym przykładzie używane jest predefiniowane ustawienie **VideoAnalyzerPreset,** a język ("en-US") jest przekazywany do konstruktora (`new VideoAnalyzerPreset("en-US")`). To ustawienie wstępne umożliwia przeprowadzenie wielu analiz dźwięku i wideo tworzących plik wideo. Istnieje możliwość użycia ustawienia wstępnego **AudioAnalyzerPreset**, jeśli jest potrzebne przeprowadzenie wielu analiz dźwięku w pliku wideo.

Podczas tworzenia **Transform**, najpierw sprawdź, czy już istnieje przy użyciu **Get** metody, jak pokazano w kodzie, który następuje. W przypadku usługi Media Services 3 metody **Get** wywołane dla obiektów zwracają **wartość null**, jeśli obiekt nie istnieje (sprawdzana jest nazwa bez uwzględniania wielkości liter).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Zadanie

Jak wspomniano powyżej, obiekt [Transform](https://docs.microsoft.com/rest/api/media/transforms) jest przepisem, a obiekt [Job](https://docs.microsoft.com/rest/api/media/jobs) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. **Zadanie** określa informacje, takie jak lokalizacja wejściowego wideo i lokalizacja danych wyjściowych. Istnieje możliwość określenia lokalizacji pliku wideo za pomocą adresów URL protokołu HTTPS, adresów URL sygnatury dostępu współdzielonego lub zasobów znajdujących się na koncie usługi Media Service.

W tym przykładzie dane wejściowe zadania to lokalny film wideo.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Zadanie zajmuje trochę czasu. Gdy tak się stanie, chcesz otrzymywać powiadomienia. Są dostępne różne opcje powiadamiania o ukończeniu [zadania](https://docs.microsoft.com/rest/api/media/jobs). Najprostszą opcją (która jest wyświetlana w tym miejscu) jest użycie sondowania.

Sondowanie nie jest zalecane najlepsze rozwiązanie dla aplikacji produkcyjnych ze względu na potencjalne opóźnienie. Jeśli sondowanie będzie nadużywane w ramach konta, może zostać ograniczone. Deweloperzy zamiast niego powinni używać usługi Event Grid.

Usługę Event Grid zaprojektowano pod kątem wysokiej dostępności, stałego poziomu wydajności i dynamicznej skalowalności. Dzięki usłudze Event Grid Twoje aplikacje mogą nasłuchiwać zdarzeń pochodzących z praktycznie wszystkich usług platformy Azure i ze źródeł niestandardowych oraz reagować na nie. Prosta, reaktywna obsługa zdarzeń oparta na protokole HTTP pomaga w tworzeniu wydajnych rozwiązań za pośrednictwem inteligentnego filtrowania i routingu zdarzeń. Aby uzyskać więcej informacji, zobacz [Kierowanie zdarzeń do niestandardowego punktu końcowego sieci Web](job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled (Zaplanowane)**, **Queued (W kolejce)**, **Processing (Przetwarzane)**, **Finished (Zakończone)** (stan końcowy). Jeśli zadanie natknie się na błąd, pojawi się **komunikat Błąd.** Jeśli zadanie jest w trakcie anulowania, otrzymasz **Canceling,** a następnie **anulowane,** gdy jest gotowe.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>Pobieranie wyniku zadania

Następująca funkcja pobiera wyniki z [zasobu](https://docs.microsoft.com/rest/api/media/assets) wyjściowego do folderu "output", dzięki czemu można sprawdzić wyniki zadania.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Czyszczenie zasobów na koncie usługi Media Services

Ogólnie rzecz biorąc należy oczyścić wszystko z wyjątkiem obiektów, które planujesz ponownie użyć (zazwyczaj będziesz ponownie używać przekształceń i utrwalać StreamingLocators). Jeśli chcesz, aby twoje konto było czyste po eksperymentowaniu, usuń zasoby, których nie zamierzasz ponownie używać. Na przykład poniższy kod usuwa zadania:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

Naciśnij klawisze Ctrl+F5, aby uruchomić aplikację *AnalyzeVideos.*

Po uruchomieniu programu zadanie tworzy miniatury dla każdej twarzy wykrytej w wideo. Tworzy także plik insights.json.

## <a name="examine-the-output"></a>Sprawdzanie danych wyjściowych

Plik wyjściowy analizy wideo ma nazwę insights.json. Ten plik zawiera wyniki analiz wideo. Opis elementów znajdujących się w pliku json zawiera artykuł [Inteligentna analiza multimediów](intelligence-concept.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Wielowątkowość

Zestawy SDK usługi Azure Media Services w wersji 3 nie są bezpieczne dla wątków. Podczas pracy z aplikacją wielowątkową należy wygenerować nowy obiekt AzureMediaServicesClient na wątek.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md)
