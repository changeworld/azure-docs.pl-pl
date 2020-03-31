---
title: Szyfrowanie wideo za pomocą AES-128
titleSuffix: Azure Media Services
description: Dowiedz się, jak szyfrować wideo za pomocą szyfrowania 128-bitowego AES i jak korzystać z usługi dostarczania kluczy w usłudze Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 126700e6290650221a9cb9711b22472301409fca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974176"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Samouczek: Szyfrowanie wideo za pomocą AES-128 i korzystanie z usługi dostarczania kluczy

> [!NOTE]
> Mimo że samouczek używa przykładów [zestawu SDK .NET,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) ogólne kroki są takie same dla [interfejsu API REST,](https://docs.microsoft.com/rest/api/media/liveevents) [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)lub innych [obsługiwanych zestawu SDK.](media-services-apis-overview.md#sdks)

Za pomocą usługi Media Services można dostarczać transmisje strumieniowe na żywo HTTP (HLS), MPEG-DASH i płynne przesyłanie strumieniowe szyfrowane za pomocą usługi AES przy użyciu 128-bitowych kluczy szyfrowania. Usługa Media Services udostępnia również usługę dostarczania kluczy, która dostarcza klucze szyfrowania autoryzowanym użytkownikom. Jeśli program Media Services ma dynamicznie szyfrować wideo, należy skojarzyć klucz szyfrowania z lokalizatorem przesyłania strumieniowego, a także skonfigurować zasady klucza zawartości. Gdy odtwarzacz zażąda strumienia, usługa Media Services używa określonego klucza do dynamicznego szyfrowania zawartości za pomocą systemu AES-128. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby określić, czy użytkownik został autoryzowany do otrzymywania klucza, usługa ocenia zasady kluczy zawartości, które wybrano dla klucza.

Każdy element zawartości można szyfrować przy użyciu wielu typów szyfrowania (AES-128, PlayReady, Widevine, FairPlay). Zobacz [Streaming protocols and encryption types (Protokoły i typy szyfrowania przesyłania strumieniowego)](content-protection-overview.md#streaming-protocols-and-encryption-types), aby sprawdzić, które rozwiązania warto łączyć. Zobacz też [Jak chronić za pomocą drm](protect-with-drm.md).

Dane wyjściowe z przykładu ten artykuł zawiera adres URL do programu Azure Media Player, adres URL manifestu i token AES potrzebne do odtwarzania zawartości. Przykład ustawia wygaśnięcie tokenu JSON Web Token (JWT) na 1 godzinę. Możesz otworzyć przeglądarkę i wkleić wynikowy adres URL, aby uruchomić stronę demonstracyjną programu ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```Azure Media Player z wypełnionym dla Ciebie adresem URL i tokenem już w następującym formacie: .

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Pobierz [przykład EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) opisane w tym artykule.
> * Zacznij używać interfejsów API usług multimedialnych przy użyciu pliku .NET SDK.
> * Utwórz zasób wyjściowy.
> * Tworzenie przekształcenia kodowania.
> * Prześlij zadanie.
> * Poczekaj na zakończenie zadania.
> * Tworzenie zasad klucza zawartości
> * Skonfiguruj zasady do używania ograniczenia tokenu JWT.
> * Utwórz lokalizator przesyłania strumieniowego.
> * Skonfiguruj lokalizator przesyłania strumieniowego, aby zaszyfrować wideo za pomocą narzędzia AES (ClearKey).
> * Pobierz token testowy.
> * Tworzenie adresu URL przesyłania strumieniowego.
> * Oczyszczenie zasobów.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Następujące elementy są wymagane do wykonania czynności przedstawionych w samouczku.

* Zapoznanie się z artykułem [Content protection overview (Omówienie ochrony zawartości)](content-protection-overview.md).
* Zainstalowanie narzędzia Visual Studio Code lub Visual Studio.
* [Utwórz konto usługi Media Services](create-account-cli-quickstart.md).
* Uzyskaj poświadczenia potrzebne do korzystania z interfejsów API usług Media Services, wykonując następujące [interfejsy API programu Access](access-api-cli-how-to.md).

## <a name="download-code"></a>Pobieranie kodu

Sklonuj repozytorium GitHub zawierające pełny przykład dla platformy .NET opisany w tym artykule na swoją maszynę za pomocą następującego polecenia:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Próbka "Szyfruj za pomocą AES-128" znajduje się w folderze [EncryptWithAES.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES)

> [!NOTE]
> Przykład tworzy unikatowe zasoby przy każdym uruchomieniu aplikacji. Zazwyczaj będzie ponownie używać istniejących zasobów, takich jak przekształcenia i zasady (jeśli istniejące zasoby mają wymagane konfiguracje).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services z .NET, należy utworzyć obiekt **AzureMediaServicesClient.** Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Tworzenie zasobu wyjściowego  

[Zasób](https://docs.microsoft.com/rest/api/media/assets) wyjściowy przechowuje wynik zadania kodowania.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Pobieranie lub tworzenie obiektu Transform kodowania

Podczas tworzenia nowego wystąpienia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy określić, jakie dane wyjściowe ma ono tworzyć. Wymagany parametr to obiekt **TransformOutput**, jak pokazano w poniższym kodzie. Każdy obiekt **TransformOutput** zawiera element **Preset**. Element **Preset** zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania wymaganego obiektu **TransformOutput**. Przykład opisany w tym artykule używa wbudowanego elementu Preset o nazwie **AdaptiveStreaming**. Preset koduje wejściowy obraz wideo do automatycznie generowanej drabiny szybkości transmisji bitów (pary o rozdzielczości bitrate) na podstawie rozdzielczości wejściowej i szybkości transmisji bitów, a następnie tworzy pliki ISO MP4 z wideo H.264 i dźwiękiem AAC odpowiadającym każdej parze o rozdzielczości bitrate.

Przed utworzeniem nowego [przekształcenia,](https://docs.microsoft.com/rest/api/media/transforms)najpierw sprawdź, czy już istnieje przy użyciu **Get** metody, jak pokazano w kodzie, który następuje. W przypadku usługi Media Services 3 metody **Get** wywołane dla obiektów zwracają **wartość null**, jeśli obiekt nie istnieje (sprawdzana jest nazwa bez uwzględniania wielkości liter).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Przesyłanie zadania

Jak wspomniano powyżej, obiekt [Transform](https://docs.microsoft.com/rest/api/media/transforms) jest przepisem, a obiekt [Job](https://docs.microsoft.com/rest/api/media/jobs) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. **Zadanie** określa informacje, takie jak lokalizacja wejściowego wideo i lokalizacja danych wyjściowych.

W tym samouczku tworzymy dane wejściowe zadania na podstawie pliku, który jest pozyskiwany bezpośrednio z [źródłowego adresu URL HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Zadanie zajmuje trochę czasu. Gdy tak się stanie, chcesz otrzymywać powiadomienia. Poniższy przykład kodu pokazuje, jak sondować usługę pod kątem stanu [zadania](https://docs.microsoft.com/rest/api/media/jobs). Sondowanie nie jest zalecane najlepsze rozwiązanie dla aplikacji produkcyjnych ze względu na potencjalne opóźnienie. Jeśli sondowanie będzie nadużywane w ramach konta, może zostać ograniczone. Deweloperzy zamiast niego powinni używać usługi Event Grid. Aby uzyskać więcej informacji, zobacz [Kierowanie zdarzeń do niestandardowego punktu końcowego sieci Web](job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled (Zaplanowane)**, **Queued (W kolejce)**, **Processing (Przetwarzane)**, **Finished (Zakończone)** (stan końcowy). Jeśli zadanie natknie się na błąd, pojawi się **komunikat Błąd.** Jeśli zadanie jest w trakcie anulowania, otrzymasz **Canceling** i **Canceled** po zakończeniu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Tworzenie zasad klucza zawartości

Klucz zawartości zapewnia bezpieczny dostęp do elementów zawartości. Należy utworzyć **zasady klucza zawartości,** który konfiguruje sposób dostarczania klucza zawartości do klientów końcowych. Klucz zawartości jest skojarzony z **lokalizatorem przesyłania strumieniowego**. Usługa Media Services udostępnia również usługę dostarczania kluczy, która dostarcza klucze szyfrowania autoryzowanym użytkownikom.

Gdy odtwarzacz zażąda strumienia, usługa Media Services używa określonego klucza do dynamicznego szyfrowania zawartości (w tym przypadku przy użyciu szyfrowania AES). Aby odszyfrować strumień, gracz żąda klucza z usługi dostarczania kluczy. Aby określić, czy użytkownik został autoryzowany do otrzymywania klucza, usługa ocenia zasady kluczy zawartości, które wybrano dla klucza.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego

Po zakończeniu kodowania i ustawieniu zasad kluczy zawartości następnym krokiem jest udostępnienie klientom w wyjściowym elemencie zawartości pliku wideo, który można odtwarzać. Film udostępnia się w dwóch krokach:

1. Utwórz [lokalizator przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators).
2. Utworzenie adresów URL przesyłania strumieniowego, których klienci mogą używać.

Proces tworzenia **lokalizatora przesyłania strumieniowego** jest nazywany publikowaniem. Domyślnie **lokalizator przesyłania strumieniowego** jest prawidłowy natychmiast po wywołaniu interfejsu API. Trwa do momentu usunięcia, chyba że skonfigurujesz opcjonalne godziny rozpoczęcia i zakończenia.

Podczas tworzenia [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators)należy określić żądaną **nazwę StreamingPolicyName**. W tym samouczku używamy jednego z PredefinedStreamingPolicies, który informuje usługi Azure Media Services, jak opublikować zawartość do przesyłania strumieniowego. W tym przykładzie stosowane jest szyfrowanie koperty AES (szyfrowanie to jest również nazywane szyfrowaniem ClearKey, ponieważ klucz jest dostarczany do klienta odtwarzania za pośrednictwem protokołu HTTPS, a nie licencji DRM).

> [!IMPORTANT]
> Podczas korzystania z niestandardowej [usługi StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies)należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i ponownie używać ich do lokalizatorów przesyłania strumieniowego, gdy są potrzebne te same opcje szyfrowania i protokoły. Konto usługi Media Service jest objęte limitem przydziału dotyczącym liczby pozycji elementu StreamingPolicy. Nie należy tworzyć nowy StreamingPolicy dla każdego lokalizatora przesyłania strumieniowego.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Pobieranie tokenu testowego

W tym samouczku określamy zasady klucza zawartości z ograniczenie dotyczącym tokenu. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługi Media Services obsługuje tokeny w formacie [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) i to, co możemy skonfigurować w przykładzie.

ContentKeyIdentifierClaim jest używany w **zasadach klucza zawartości**, co oznacza, że token przedstawiony do usługi dostarczania kluczy musi mieć identyfikator klucza zawartości w nim. W przykładzie nie określiliśmy klucza zawartości podczas tworzenia lokalizatora przesyłania strumieniowego, system utworzył dla nas losowy klucz. Aby wygenerować token testowy, musimy uzyskać ContentKeyId umieścić w ContentKeyIdentifierClaim oświadczenia.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Kompilowanie adresu URL przesyłania strumieniowego DASH

Teraz, gdy [lokalizator przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators) został utworzony, możesz uzyskać adresy URL przesyłania strumieniowego. Aby utworzyć adres URL, należy połączyć nazwę hosta [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) i ścieżkę **lokalizatora przesyłania strumieniowego.** W tym przykładzie jest używany *domyślny* **punkt końcowy przesyłania strumieniowego**. Po pierwszym utworzeniu konta usługi Media Service ten *domyślny* **punkt końcowy przesyłania strumieniowego** będzie zatrzymany, więc należy wywołać metodę **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Ogólnie rzecz biorąc należy oczyścić wszystko z wyjątkiem obiektów, które planujesz ponownie użyć (zazwyczaj będzie ponownie używać przekształceń, lokalizatorów przesyłania strumieniowego i tak dalej). Jeśli chcesz, aby twoje konto było czyste po eksperymentowaniu, usuń zasoby, których nie zamierzasz ponownie używać. Na przykład poniższy kod usuwa zadania:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ochrona przy użyciu technologii DRM](protect-with-drm.md)
