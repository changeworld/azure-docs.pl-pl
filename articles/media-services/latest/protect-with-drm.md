---
title: Usługa szyfrowania drm usługi Azure Media Services i dostarczania licencji
titleSuffix: Azure Media Services
description: Dowiedz się, jak używać dynamicznego szyfrowania DRM i usługi dostarczania licencji do dostarczania strumieni zaszyfrowanych za pomocą licencji Microsoft PlayReady, Google Widevine lub Apple FairPlay.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 14ba5f270138db22a76fd697b264046e22577427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086731"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Samouczek: Korzystanie z usługi szyfrowania dynamicznego i dostarczania licencji DRM

> [!NOTE]
> Mimo że w tym samouczku użyto przykładów [zestawu .NET SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) ogólne kroki są takie same dla [interfejsu API REST,](https://docs.microsoft.com/rest/api/media/liveevents) [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)lub innych [obsługiwanych zestawu SDK.](media-services-apis-overview.md#sdks)

Usługa Azure Media Services umożliwia dostarczanie strumieni zaszyfrowanych za pomocą licencji Microsoft PlayReady, Google Widevine i Apple FairPlay. Aby uzyskać szczegółowe wyjaśnienie, zobacz [Ochrona zawartości za pomocą szyfrowania dynamicznego](content-protection-overview.md).

Usługa Media Services zapewnia również usługę dostarczania licencji PlayReady, Widevine i FairPlay DRM. Gdy użytkownik zażąda zawartości chronionej przez drm, aplikacja odtwarzacza żąda licencji z usługi licencji Media Services. Jeśli aplikacja odtwarzacza jest autoryzowana, usługa licencji usługi Media Services wystawia graczowi licencję. Licencja zawiera klucz odszyfrowujący, który może zostać użyty przez odtwarzacz klienta do odszyfrowania i strumieniowego przesyłania zawartości.

Ten artykuł jest oparty na przykładzie [szyfrowania przy użyciu technologii DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

Opisany w tym artykule przykład generuje następujący wynik:

![Usługa AMS z wideo chronionym przez drm w usłudze Azure Media Player](./media/protect-with-drm/ams_player.png)

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Tworzenie przekształcenia kodowania.
> * Ustawianie klucza podpisywania używanego na potrzeby weryfikacji tokenu.
> * Ustaw wymagania dotyczące zasad klucza zawartości.
> * Utwórz lokalizator przesyłania strumieniowego z określonymi zasadami przesyłania strumieniowego.
> * Utwórz adres URL używany do odtwarzania pliku.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są niezbędne następujące elementy:

* Zapoznanie się z artykułem [Content protection overview (Omówienie ochrony zawartości)](content-protection-overview.md).
* Zapoznaj się z [systemem ochrony zawartości multi-DRM projektu z kontrolą dostępu](design-multi-drm-system-with-access-control.md).
* Zainstalowanie narzędzia Visual Studio Code lub Visual Studio.
* Utworzenie nowego konta usługi Azure Media Services zgodnie z opisem w [tym przewodniku Szybki start](create-account-cli-quickstart.md).
* Uzyskanie poświadczeń potrzebnych do korzystania z interfejsów API usługi Media Services przez wykonanie kroków [uzyskiwania dostęp do interfejsów API](access-api-cli-how-to.md).
* Ustaw odpowiednie wartości w pliku konfiguracji aplikacji (appsettings.json).

## <a name="download-code"></a>Pobieranie kodu

Sklonuj repozytorium GitHub zawierające pełny przykład dla platformy .NET opisany w tym artykule na swoją maszynę za pomocą następującego polecenia:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Przykład szyfrowania za pomocą technologii DRM znajduje się w folderze [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> Przykład tworzy unikatowe zasoby przy każdym uruchomieniu aplikacji. Zazwyczaj będzie ponownie używać istniejących zasobów, takich jak przekształcenia i zasady (jeśli istniejące zasoby mają wymagane konfiguracje).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services z .NET, należy utworzyć obiekt **AzureMediaServicesClient.** Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Tworzenie zasobu wyjściowego  

[Zasób](assets-concept.md) wyjściowy przechowuje wynik zadania kodowania.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Pobieranie lub tworzenie obiektu Transform kodowania

Podczas tworzenia nowego wystąpienia obiektu [Transform](transforms-jobs-concept.md) należy określić, jakie dane wyjściowe ma ono tworzyć. Wymagany parametr jest `transformOutput` obiektem, jak pokazano w poniższym kodzie. Każdy obiekt TransformOutput zawiera element **Preset**. Element Preset zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania wymaganego obiektu TransformOutput. Przykład opisany w tym artykule używa wbudowanego elementu Preset o nazwie **AdaptiveStreaming**. Preset koduje wejściowy obraz wideo do automatycznie generowanej drabiny szybkości transmisji bitów (pary o rozdzielczości bitrate) na podstawie rozdzielczości wejściowej i szybkości transmisji bitów, a także tworzy pliki ISO MP4 z wideo H.264 i dźwiękiem AAC odpowiadającym każdej parze o rozdzielczości bitrate. 

Przed utworzeniem nowego obiektu **Transform** należy najpierw sprawdzić, czy taki obiekt już istnieje, używając metody **Get**, tak jak przedstawiono w poniższym kodzie.  W przypadku usługi Media Services 3 metody **Get** wywołane dla obiektów zwracają **wartość null**, jeśli obiekt nie istnieje (sprawdzana jest nazwa bez uwzględniania wielkości liter).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Przesyłanie zadania

Jak wspomniano powyżej, obiekt **Transform** jest przepisem, a obiekt [Job](transforms-jobs-concept.md) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. **Zadanie** określa informacje, takie jak lokalizacja wejściowego wideo i lokalizacja danych wyjściowych.

W tym samouczku tworzymy dane wejściowe zadania na podstawie pliku, który jest pozyskiwany bezpośrednio z [źródłowego adresu URL HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Zadanie zajmuje trochę czasu. Gdy tak się stanie, chcesz otrzymywać powiadomienia. Poniższy przykład kodu pokazuje, jak sondować usługę pod kątem stanu **zadania**. Sondowanie nie jest zalecane najlepsze rozwiązanie dla aplikacji produkcyjnych ze względu na potencjalne opóźnienie. Jeśli sondowanie będzie nadużywane w ramach konta, może zostać ograniczone. Deweloperzy zamiast niego powinni używać usługi Event Grid. Zobacz [Kierowanie zdarzeń do niestandardowego internetowego punktu końcowego](job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled (Zaplanowane)**, **Queued (W kolejce)**, **Processing (Przetwarzane)**, **Finished (Zakończone)** (stan końcowy). Jeśli zadanie natknie się na błąd, pojawi się **komunikat Błąd.** Jeśli zadanie jest w trakcie anulowania, otrzymasz **Canceling** i **Canceled** po zakończeniu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Tworzenie zasad klucza zawartości

Klucz zawartości zapewnia bezpieczny dostęp do elementów zawartości. Podczas szyfrowania zawartości za pomocą drm należy utworzyć [zasady klucza zawartości.](content-key-policy-concept.md) Zasady konfiguruje sposób dostarczania klucza zawartości do klientów końcowych. Klucz zawartości jest skojarzony z lokalizatorem przesyłania strumieniowego. Usługa Media Services udostępnia również usługę dostarczania kluczy, która dostarcza klucze szyfrowania i licencje do autoryzowanych użytkowników.

Należy ustawić wymagania (ograniczenia) zasad **klucza zawartości,** które muszą być spełnione, aby dostarczyć klucze z określoną konfiguracją. W tym przykładzie ustawimy następujące konfiguracje i wymagania:

* Konfigurowanie

    Licencje [PlayReady](playready-license-template-overview.md) i [Widevine](widevine-license-template-overview.md) są skonfigurowane tak, aby można było dostarczać je przy użyciu usługi dostarczania licencji w usłudze Media Services. Mimo że ta przykładowa aplikacja nie konfiguruje licencji [FairPlay,](fairplay-license-overview.md) zawiera metodę, której można użyć do skonfigurowania fairplay. Konfigurację FairPlay można dodać jako inną opcję.

* Ograniczenie

    Aplikacja ustawia ograniczenie typu tokenu JSON Web Token (JWT) dla zasad.

Gdy odtwarzacz żąda strumienia, usługa Media Services używa wybranego klucza do dynamicznego szyfrowania zawartości. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby określić, czy użytkownik został autoryzowany do otrzymywania klucza, usługa ocenia zasady kluczy zawartości, które wybrano dla klucza.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego

Po zakończeniu kodowania i ustawieniu zasad kluczy zawartości następnym krokiem jest udostępnienie klientom w wyjściowym elemencie zawartości pliku wideo, który można odtwarzać. Film udostępnia się w dwóch krokach:

1. Utwórz [lokalizator przesyłania strumieniowego](streaming-locators-concept.md).
2. Utworzenie adresów URL przesyłania strumieniowego, których klienci mogą używać.

Proces tworzenia **lokalizatora przesyłania strumieniowego** jest nazywany publikowaniem. Domyślnie **lokalizator przesyłania strumieniowego** jest prawidłowy natychmiast po wywołaniu interfejsu API. Trwa do momentu usunięcia, chyba że skonfigurujesz opcjonalne godziny rozpoczęcia i zakończenia.

Podczas tworzenia **lokalizatora przesyłania strumieniowego**należy `StreamingPolicyName`określić żądany plik . W tym samouczku używamy jednej ze wstępnie zdefiniowanych zasad przesyłania strumieniowego, która informuje usługę Azure Media Services, jak opublikować zawartość do przesyłania strumieniowego. W tym przykładzie ustawiamy element StreamingLocator.StreamingPolicyName na zasady „Predefined_MultiDrmCencStreaming”. Stosowane są szyfrowanie PlayReady i Widevine, a klucz jest dostarczany do klienta odtwarzania na podstawie skonfigurowanych licencji DRM. Jeśli chcesz także zaszyfrować strumień przy użyciu metody CBCS (FairPlay), użyj zasad „Predefined_MultiDrmStreaming”.

> [!IMPORTANT]
> W przypadku korzystania z niestandardowych [zasad przesyłania strumieniowego](streaming-policy-concept.md) należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i używać ich ponownie dla obiektów StreamingLocator zawsze, gdy są potrzebne takie same opcje szyfrowania i protokoły. Konto usługi Media Service jest objęte limitem przydziału dotyczącym liczby pozycji elementu StreamingPolicy. Nie należy tworzyć nowy StreamingPolicy dla każdego StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Pobieranie tokenu testowego

W tym samouczku określamy zasady klucza zawartości z ograniczenie dotyczącym tokenu. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługi Media Services obsługuje tokeny w formatach [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) i to, co konfigurujemy w przykładzie.

Element ContentKeyIdentifierClaim jest używany w zasadach ContentKeyPolicy, co oznacza, że token przedstawiany usłudze dostarczania kluczy musi zawierać identyfikator ContentKey. W przykładzie nie określamy klucza zawartości podczas tworzenia lokalizatora przesyłania strumieniowego, system tworzy dla nas losowy klucz. Aby wygenerować token testowy, musimy uzyskać ContentKeyId umieścić w ContentKeyIdentifierClaim oświadczenia.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Tworzenie adresu URL przesyłania strumieniowego

Teraz, po utworzeniu obiektu [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), możesz pobrać adresy URL przesyłania strumieniowego. Aby utworzyć adres URL, należy połączyć nazwę hosta [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) i ścieżkę **lokalizatora przesyłania strumieniowego.** W tym przykładzie jest używany *domyślny* **punkt końcowy przesyłania strumieniowego**. Po pierwszym utworzeniu konta usługi Media Service ten *domyślny* **punkt końcowy przesyłania strumieniowego** będzie zatrzymany, więc należy wywołać metodę **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Po uruchomieniu aplikacji zostanie wyświetlony następujący ekran:

![Ochrona przy użyciu technologii DRM](./media/protect-with-drm/playready_encrypted_url.png)

Można otworzyć przeglądarkę i wkleić wynikowy adres URL, aby uruchomić stronę pokazu usługi Azure Media Player przy użyciu już wypełnionego adresu URL i tokenu.

## <a name="clean-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Ogólnie rzecz biorąc należy oczyścić wszystko z wyjątkiem obiektów, które planujesz ponownie użyć (zazwyczaj będziesz ponownie używać przekształceń, streamingLocators i tak dalej). Jeśli chcesz, aby twoje konto było czyste po eksperymentowaniu, usuń zasoby, których nie zamierzasz ponownie używać. Na przykład poniższy kod usuwa zadania:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="next-steps"></a>Następne kroki

Wyewidencjonuj

> [!div class="nextstepaction"]
> [Ochrona za pomocą AES-128](protect-with-aes128.md)
