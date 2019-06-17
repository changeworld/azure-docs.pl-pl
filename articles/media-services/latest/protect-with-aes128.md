---
title: Użyj usługi Azure Media Services można zaszyfrować wideo przy użyciu algorytmu AES-128 | Dokumentacja firmy Microsoft
description: Dostarczanie zawartości szyfrowane przy użyciu kluczy szyfrowania AES 128-bitowy przy użyciu usługi Microsoft Azure Media Services. Media Services udostępnia również usługa dostarczania kluczy, która dostarcza kluczy szyfrowania do autoryzowanych użytkowników. W tym temacie przedstawiono sposób dynamicznego szyfrowania przy użyciu algorytmu AES-128 i korzystania z usługi dostarczania kluczy.
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
ms.openlocfilehash: c957a98cdb6c195f7ed9b41dabc66a32714f57e7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65142528"
---
# <a name="tutorial-use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Samouczek: Dynamiczne szyfrowanie AES-128 i usługę dostarczania kluczy

Usługa Media Services umożliwia dostarczanie HTTP Live Streaming (HLS), MPEG-DASH i Smooth Streaming, zaszyfrowany przy użyciu AES przy użyciu kluczy szyfrowania 128-bitowego. Media Services udostępnia również usługa dostarczania kluczy, która dostarcza kluczy szyfrowania do autoryzowanych użytkowników. Chcąc usługi Media Services dynamicznie szyfrując Twój film wideo, możesz skojarzyć klucz szyfrowania z lokalizatora przesyłania strumieniowego, a także skonfigurować zasad klucza zawartości. Zleconą strumienia za pomocą odtwarzacza Media Services używa określonego klucza dynamiczne szyfrowanie zawartości przy użyciu algorytmu AES-128. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby określić, czy użytkownik został autoryzowany do otrzymywania klucza, usługa ocenia zasady kluczy zawartości, które wybrano dla klucza.

Każdy element zawartości można szyfrować przy użyciu wielu typów szyfrowania (AES-128, PlayReady, Widevine, FairPlay). Zobacz [Streaming protocols and encryption types (Protokoły i typy szyfrowania przesyłania strumieniowego)](content-protection-overview.md#streaming-protocols-and-encryption-types), aby sprawdzić, które rozwiązania warto łączyć. Zobacz też [sposobu ochrony przy użyciu DRM](protect-with-drm.md).

Dane wyjściowe z przykładu w tym artykule zawiera adres URL usługi Azure Media Player, manifestu adresu URL i tokenu AES, potrzebne do odtwarzania zawartości. Przykład termin wygaśnięcia tokenu JWT do 1 godziny. Możesz otworzyć przeglądarkę i wkleić wynikowy adres URL, aby uruchomić stronę pokaz usługi Azure Media Player przy użyciu adresu URL i tokenu wypełniona już w następującym formacie: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

Ten samouczek przedstawia sposób wykonania następujących czynności:    

> [!div class="checklist"]
> * Pobierz [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) przykładowe opisaną w artykule
> * Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK
> * Tworzenie zasobu wyjściowego
> * Utwórz kodowania przekształcenia
> * Przesyłanie zadania
> * Oczekiwanie na zakończenie zadania
> * Utwórz zasadę klucza zawartości
> * Konfigurowanie zasad użytkowania ograniczenia tokenu JWT 
> * Tworzenie lokalizatora przesyłania strumieniowego
> * Konfigurowanie lokalizatora przesyłania strumieniowego w celu zaszyfrowania wideo przy użyciu standardu AES (ClearKey)
> * Pobieranie tokenu testowego
> * Utworzenie adresu URL przesyłania strumieniowego
> * Oczyszczanie zasobów

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Następujące elementy są wymagane do wykonania czynności przedstawionych w samouczku.

* Przegląd [zawartości Omówienie ochrony](content-protection-overview.md) artykułu
* Instalowanie narzędzia Visual Studio Code lub Visual Studio
* [Utwórz konto usługi Media Services](create-account-cli-quickstart.md)
* Uzyskanie poświadczeń potrzebnych do korzystania z interfejsów API usługi Media Services przez wykonanie kroków [uzyskiwania dostęp do interfejsów API](access-api-cli-how-to.md).

## <a name="download-code"></a>Pobieranie kodu

Sklonuj repozytorium GitHub zawierające pełny przykład dla platformy .NET opisany w tym artykule na swoją maszynę za pomocą następującego polecenia:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Przykład "szyfrowania przy użyciu algorytmu AES-128" znajduje się w [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) folderu.

> [!NOTE]
> Przykładowa aplikacja tworzy unikatowe zasoby po każdym uruchomieniu aplikacji. Zazwyczaj istniejące zasoby, takie jak przekształcenia i zasady, będą ponownie używane (jeśli istniejący zasób ma wymagane konfiguracje). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Tworzenie zasobu wyjściowego  

[Zasób](https://docs.microsoft.com/rest/api/media/assets) wyjściowy przechowuje wynik zadania kodowania.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Pobieranie lub tworzenie obiektu Transform kodowania

Podczas tworzenia nowego wystąpienia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy określić, jakie dane wyjściowe ma ono tworzyć. Wymagany parametr to obiekt **TransformOutput**, jak pokazano w poniższym kodzie. Każdy obiekt **TransformOutput** zawiera element **Preset**. Element **Preset** zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania wymaganego obiektu **TransformOutput**. Przykład opisany w tym artykule używa wbudowanego elementu Preset o nazwie **AdaptiveStreaming**. Element Preset umożliwia kodowanie wejściowego wideo do automatycznie generowanej drabiny szybkości transmisji bitów (zestawu par „szybkość transmisji bitów-rozdzielczość”) na podstawie rozdzielczości wejściowej i szybkości transmisji bitów oraz tworzenie plików MP4 zgodnych ze standardem ISO, które zawierają wideo w formacie H.264 i dźwięk w formacie AAC, dla każdej pary „szybkość transmisji bitów-rozdzielczość”. 

Przed utworzeniem nowego obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy najpierw sprawdzić, czy taki obiekt już istnieje, używając metody **Get**, tak jak przedstawiono w poniższym kodzie.  W przypadku usługi Media Services 3 metody **Get** wywołane dla obiektów zwracają **wartość null**, jeśli obiekt nie istnieje (sprawdzana jest nazwa bez uwzględniania wielkości liter).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Przesyłanie zadania

Jak wspomniano powyżej, obiekt [Transform](https://docs.microsoft.com/rest/api/media/transforms) jest przepisem, a obiekt [Job](https://docs.microsoft.com/rest/api/media/jobs) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. Obiekt **Job** określa informacje takie jak lokalizacja wejściowego pliku wideo oraz danych wyjściowych.

W tym samouczku utworzymy dane wejściowe zadania w oparciu o plik pozyskany ze [źródłowego adresu protokołu HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Ukończenie zadania zajmuje trochę czasu, a Ty chcesz otrzymać powiadomienie o tym fakcie. Poniższy przykładowy kod przedstawia sposób sondowania usługi pod kątem stanu [zadania](https://docs.microsoft.com/rest/api/media/jobs). Sondowanie nie jest najlepszym rozwiązaniem w przypadku zastosowań produkcyjnych ze względu na możliwe opóźnienia. Jeśli sondowanie będzie nadużywane w ramach konta, może zostać ograniczone. Deweloperzy zamiast niego powinni używać usługi Event Grid. Zobacz [Kierowanie zdarzeń do niestandardowego internetowego punktu końcowego](job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled** (Zaplanowane), **Queued** (W kolejce), **Processing** (Przetwarzane), **Finished** (Zakończone — jest to stan końcowy). Jeśli zadanie napotka błąd, może być w stanie **Error (Błąd)** . Jeśli zadanie jest w trakcie anulowania, może być w stanie **Canceling (Anulowanie)** , a po zakończeniu tej operacji w stanie **Canceled (Anulowane)** .

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Utwórz zasadę klucza zawartości

Klucz zawartości zapewnia bezpieczny dostęp do elementów zawartości. Należy utworzyć **zasad klucza zawartości** , konfiguruje sposób dostarczania klucza zawartości dla klientów końcowych. Klucz zawartości jest skojarzony z **lokalizatora przesyłania strumieniowego**. Media Services udostępnia również usługa dostarczania kluczy, która dostarcza kluczy szyfrowania do autoryzowanych użytkowników. 

Zleconą strumienia za pomocą odtwarzacza Media Services używa określonego klucza dynamiczne szyfrowanie zawartości (w tym przypadku za pomocą szyfrowania AES). Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby określić, czy użytkownik został autoryzowany do otrzymywania klucza, usługa ocenia zasady kluczy zawartości, które wybrano dla klucza.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego

Po zakończeniu kodowania i ustawieniu zasad kluczy zawartości następnym krokiem jest udostępnienie klientom w wyjściowym elemencie zawartości pliku wideo, który można odtwarzać. Można to zrobić, wykonując dwie czynności: 

1. Tworzenie [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. Utworzenie adresów URL przesyłania strumieniowego, których klienci mogą używać. 

Proces tworzenia **lokalizatora przesyłania strumieniowego** nosi nazwę publikowania. Domyślnie **lokalizator przesyłania strumieniowego** jest ważny natychmiast po wykonaniu wywołań interfejsu API i aż do jego usunięcia, chyba że skonfigurujesz opcjonalne czasy rozpoczęcia i zakończenia. 

Podczas tworzenia obiektu [Lokalizator przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators) musisz określić żądany element **StreamingPolicyName**. W tym samouczku używamy jednej z zasad typu PredefinedStreamingPolicies, która informuje usługę Azure Media Services o wymaganym sposobie publikowania zawartości na potrzeby przesyłania strumieniowego. W tym przykładzie szyfrowania szyfrowanie AES Envelope zastosowano (określana także jako ClearKey szyfrowania, ponieważ klucz jest dostarczany klientowi odtwarzania za pośrednictwem protokołu HTTPS, a nie licencji DRM).

> [!IMPORTANT]
> Korzystając z niestandardowego [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies), należy zaprojektować ograniczony zestaw tych zasad dla swojego konta usługi multimediów i ponownie używać ich na potrzeby Twojego Lokalizatory przesyłania strumieniowego w każdym przypadku, gdy potrzebne są te same opcje szyfrowania i protokołów. Konto usługi Media Service jest objęte limitem przydziału dotyczącym liczby pozycji elementu StreamingPolicy. Możesz powinna nie utworzony nowy StreamingPolicy dla każdego lokalizatora przesyłania strumieniowego.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Pobieranie tokenu testowego
        
W tym samouczku określamy zasady klucza zawartości z ograniczenie dotyczącym tokenu. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje tokeny w formatach [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) — ten format skonfigurujemy w przykładzie.

ContentKeyIdentifierClaim jest używany w **zasad klucza zawartości**, co oznacza, że token przedstawiony usługi dostarczania klucza musi mieć identyfikator klucza zawartości w nim. W tym przykładzie firma Microsoft nie określił zawartości klucza, tworząc Lokalizator przesyłania strumieniowego, system utworzył losowe jeden dla nas. W celu wygenerowania tokenu testowego musimy uzyskać identyfikator ContentKeyId do umieszczenia w oświadczeniu ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Kompilowanie adresu URL przesyłania strumieniowego DASH

Teraz, gdy [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators) został utworzony, można uzyskać adresy URL przesyłania strumieniowego. Aby utworzyć adres URL, musisz złączyć [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) nazwy hosta i **lokalizatora przesyłania strumieniowego** ścieżki. W tym przykładzie jest używany *domyślny* **punkt końcowy przesyłania strumieniowego**. Po pierwszym utworzeniu konta usługi Media Service ten *domyślny* **punkt końcowy przesyłania strumieniowego** będzie zatrzymany, więc należy wywołać metodę **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Ogólnie rzecz biorąc, należy wyczyścić się wszystkim, z wyjątkiem obiektów, które są planowane do ponownego użycia (zwykle będą ponownie używały przekształceń i utrwali Lokalizatory przesyłania strumieniowego, itp.). Jeśli Twoje konto ma być czyste po przeprowadzeniu eksperymentów, należy usunąć zasoby, których ponowne użycie nie jest planowane.  Na przykład następujący kod usuwa zadania.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej. 

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Prześlij opinię i pobieranie aktualizacji

Zapoznaj się z [społeczności usługi Azure Media Services](media-services-community.md) artykuł, aby wyświetlić różne sposoby zadawaj pytania, Prześlij opinię i pobrać aktualizacje o usłudze Media Services.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Ochrona za pomocą technologii DRM](protect-with-drm.md)
