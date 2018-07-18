---
title: Korzystanie z szyfrowania dynamicznego AES usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Dostarczanie zawartości szyfrowane przy użyciu kluczy szyfrowania AES 128-bitowy przy użyciu usługi Microsoft Azure Media Services. Media Services udostępnia również usługa dostarczania kluczy, która dostarcza kluczy szyfrowania do autoryzowanych użytkowników. W tym temacie przedstawiono sposób dynamicznego szyfrowania przy użyciu algorytmu AES-128 i korzystania z usługi dostarczania kluczy.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: juliako
ms.openlocfilehash: 3e5de521570a587b049702dabd3e3692c4227796
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114797"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Dynamiczne szyfrowanie AES-128 i usługę dostarczania kluczy

Usługa Media Services umożliwia dostarczanie HTTP Live Streaming (HLS), MPEG-DASH i Smooth Streaming, zaszyfrowany przy użyciu AES przy użyciu kluczy szyfrowania 128-bitowego. Media Services udostępnia również usługa dostarczania kluczy, która dostarcza kluczy szyfrowania do autoryzowanych użytkowników. Chcąc usługi Media Services zaszyfrować element zawartości, należy skojarzyć klucz szyfrowania z StreamingLocator, a także skonfigurować zasad klucza zawartości. Zleconą strumienia za pomocą odtwarzacza Media Services używa określonego klucza dynamiczne szyfrowanie zawartości przy użyciu szyfrowania AES. Aby odszyfrować strumienia, gracz żądań klucz usługi dostarczania kluczy. Aby ustalić, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zawartości zasad kluczy, które podane dla klucza.

Artykuł jest oparty na [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) próbki. Przykład przedstawia sposób tworzenia kodowania transformacji, który używa wbudowanego ustawienie wstępne kodowania z adaptacyjną szybkością transmisji bitów i pozyskuje pliku bezpośrednio z [HTTPs źródłowy adres URL](job-input-from-http-how-to.md). Następnie elementu zawartości wyjściowej została opublikowana, za pomocą szyfrowania AES (ClearKey). Dane wyjściowe z przykładu jest adres URL usługi Azure Media Player, w tym zarówno DASH manifest, jak i token AES, potrzebne do odtwarzania zawartości. Przykład termin wygaśnięcia tokenu JWT do 1 godziny. Możesz otworzyć przeglądarkę i wkleić wynikowy adres URL, aby uruchomić stronę pokaz usługi Azure Media Player przy użyciu adresu URL i tokenu wypełniona już w następującym formacie: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

> [!NOTE]
> Umożliwia ona szyfrowanie każdego zasobu ze wszystkimi wiele typów szyfrowania (AES-128, PlayReady, Widevine i FairPlay). Zobacz [protokoły i typy szyfrowania przesyłania strumieniowego](content-protection-overview.md#streaming-protocols-and-encryption-types), aby zobaczyć, co ma sens połączyć.

## <a name="prerequisites"></a>Wymagania wstępne

Następujące elementy są wymagane do wykonania czynności przedstawionych w samouczku.

* Przegląd [zawartości Omówienie ochrony](content-protection-overview.md) artykułu.
* Instalowanie narzędzia Visual Studio Code lub Visual Studio
* Utwórz nowe konto usługi Azure Media Services, zgodnie z opisem w [ten przewodnik Szybki Start](create-account-cli-quickstart.md).
* Uzyskiwanie poświadczeń potrzebnych do korzystania z interfejsów API usług Media Services wykonując [dostęp do interfejsów API](access-api-cli-how-to.md)

## <a name="download-code"></a>Pobierz program code

Klonowanie repozytorium GitHub, który zawiera pełny przykład .NET omówionych w tym artykule do komputera za pomocą następującego polecenia:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Przykład "szyfrowania przy użyciu algorytmu AES-128" znajduje się w [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) folderu.

> [!NOTE]
> Przykładowa aplikacja tworzy unikatowy zasoby za każdym razem, gdy aplikacja zostanie uruchomiona. Zazwyczaj będzie ponowne używanie istniejących zasobów, takich jak przekształceń i zasad (Jeśli istniejący zasób ma wymagany konfiguracji). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowane na początku tego artykułu **GetCredentialsAsync** funkcja tworzy obiekt ServiceClientCredentials oparte na poświadczeniach dostarczona w pliku konfiguracji lokalnej. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Tworzenie zasobu wyjściowego  

[Zasób](https://docs.microsoft.com/rest/api/media/assets) wyjściowy przechowuje wynik zadania kodowania.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Uzyskaj lub Utwórz kodowania przekształcenia

Podczas tworzenia nowego wystąpienia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy określić, jakie dane wyjściowe ma ono tworzyć. Wymagany parametr to obiekt **TransformOutput**, jak pokazano w poniższym kodzie. Każdy obiekt **TransformOutput** zawiera element **Preset**. Element **Preset** zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania wymaganego obiektu **TransformOutput**. Przykład opisany w tym artykule używa wbudowanego elementu Preset o nazwie **AdaptiveStreaming**. Element Preset umożliwia kodowanie wejściowego wideo do automatycznie generowanej drabiny szybkości transmisji bitów (zestawu par „szybkość transmisji bitów-rozdzielczość”) na podstawie rozdzielczości wejściowej i szybkości transmisji bitów oraz tworzenie plików MP4 zgodnych ze standardem ISO, które zawierają wideo w formacie H.264 i dźwięk w formacie AAC, dla każdej pary „szybkość transmisji bitów-rozdzielczość”. 

Przed utworzeniem nowego [Przekształcanie](https://docs.microsoft.com/rest/api/media/transforms), powinny najpierw sprawdzić, jeśli istnieje już przy użyciu **uzyskać** metodzie, jak pokazano w kodzie, który jest zgodna.  W przypadku usługi Media Services 3 metody **Get** wywołane dla obiektów zwracają **wartość null**, jeśli obiekt nie istnieje (sprawdzana jest nazwa bez uwzględniania wielkości liter).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Prześlij zadanie

Jak wspomniano powyżej, obiekt [Transform](https://docs.microsoft.com/rest/api/media/transforms) jest przepisem, a obiekt [Job](https://docs.microsoft.com/rest/api/media/jobs) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. Obiekt **Job** określa informacje takie jak lokalizacja wejściowego pliku wideo oraz danych wyjściowych.

W tym samouczku utworzymy dane wejściowe zadania na podstawie pliku, pobieranym bezpośrednio z [HTTPs źródłowy adres URL](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Ukończenie zadania zajmuje trochę czasu, a Ty chcesz otrzymać powiadomienie o tym fakcie. Poniższy przykładowy kod przedstawia sposób sondowania usługi pod kątem stanu [zadania](https://docs.microsoft.com/rest/api/media/jobs). Sondowanie nie jest najlepszym rozwiązaniem w przypadku zastosowań produkcyjnych ze względu na możliwe opóźnienia. Jeśli sondowanie będzie nadużywane w ramach konta, może zostać ograniczone. Deweloperzy zamiast niego powinni używać usługi Event Grid. Zobacz [Kierowanie zdarzeń do niestandardowego internetowego punktu końcowego](job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled (Zaplanowane)**, **Queued (W kolejce)**, **Processing (Przetwarzane)**, **Finished (Zakończone)** (stan końcowy). Jeśli zadanie napotka błąd, może być w stanie **Error (Błąd)**. Jeśli zadanie jest w trakcie anulowania, może być w stanie **Canceling (Anulowanie)**, a po zakończeniu tej operacji w stanie **Canceled (Anulowane)**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>Utwórz ContentKeyPolicy

Klucz zawartości zapewnia bezpieczny dostęp do zasobów. Należy utworzyć **ContentKeyPolicy** , konfiguruje sposób dostarczania klucza zawartości dla klientów końcowych. Klucz zawartości jest skojarzony z **StreamingLocator**. Media Services udostępnia również usługa dostarczania kluczy, która dostarcza kluczy szyfrowania do autoryzowanych użytkowników. 

Zleconą strumienia za pomocą odtwarzacza Media Services używa określonego klucza dynamiczne szyfrowanie zawartości (w tym przypadku za pomocą szyfrowania AES). Aby odszyfrować strumienia, gracz żądań klucz usługi dostarczania kluczy. Aby ustalić, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zawartości zasad kluczy, które podane dla klucza.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>Tworzenie lokalizatora przesyłania strumieniowego

Po zakończeniu kodowania i zawartości klucza zasady zostały ustawione tak, następnym krokiem jest film wideo w danych wyjściowych dostępne dla klientów do odtwarzania elementu zawartości. Można to zrobić w dwóch etapach: 

1. Tworzenie [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. Twórz adresy URL przesyłania strumieniowego, których klienci mogą używać. 

Proces tworzenia **StreamingLocator** nosi nazwę publikowania. Domyślnie obiekt **StreamingLocator** jest ważny natychmiast po wykonaniu wywołań interfejsu API i aż do jego usunięcia, chyba że skonfigurujesz opcjonalne czasy rozpoczęcia i zakończenia. 

Podczas tworzenia obiektu [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) musisz określić żądany element **StreamingPolicyName**. W tym samouczku używamy jednego PredefinedStreamingPolicies, który informuje usługi Azure Media Services, jak publikować zawartość do przesyłania strumieniowego. W tym przykładzie szyfrowania szyfrowanie AES Envelope zastosowano (określana także jako ClearKey szyfrowania, ponieważ klucz jest dostarczany klientowi odtwarzania za pośrednictwem protokołu HTTPS, a nie licencji DRM).

> [!IMPORTANT]
> W przypadku korzystania z niestandardowego elementu [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i używać ich ponownie dla obiektów StreamingLocator zawsze, gdy są potrzebne takie same opcje szyfrowania i protokoły. Konto usługi Media Service jest objęte limitem przydziału dotyczącym liczby pozycji elementu StreamingPolicy. Nie należy tworzyć nowego elementu StreamingPolicy dla każdego obiektu StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Pobieranie tokenu testowego
        
W tym samouczku określamy zawartości klucza zasad, mają ograniczenia tokenu. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje następujące formaty tokenów [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) formatów (JWT) oraz że jest skonfigurujemy w próbce.

ContentKeyIdentifierClaim jest używany w ContentKeyPolicy, która oznacza, że token przedstawiony usługi dostarczania klucza muszą mieć identyfikator ContentKey w nim. W tym przykładzie klucz zawartości nie jest określona podczas tworzenia StreamingLocator, system tworzy losowe jeden dla nas. W celu generowania testu tokenu, firma Microsoft musi uzyskać ContentKeyId należy umieścić w oświadczenie ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Tworzenie KRESKĄ URL przesyłania strumieniowego

Teraz, gdy [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) został utworzony, można uzyskać adresy URL przesyłania strumieniowego. Aby utworzyć adres URL, musisz połączyć nazwę hosta obiektu [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) i ścieżkę obiektu **StreamingLocator**. W tym przykładzie jest używany *domyślny* obiekt **StreamingEndpoint**. Po pierwszym utworzeniu konta usługi Media Service ten *domyślny* obiekt **StreamingEndpoint** będzie zatrzymany, więc należy wywołać metodę **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Zazwyczaj należy wyczyścić wszystko z wyjątkiem obiektów, których zamierzasz użyć ponownie (zazwyczaj są to obiekty Transform, obiekty StreamingLocator są utrwalane itd.). Jeśli Twoje konto ma być czyste po przeprowadzeniu eksperymentów, należy usunąć zasoby, których ponowne użycie nie jest planowane.  Na przykład następujący kod usuwa zadania.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z jak [chronić za pomocą technologii DRM](protect-with-drm.md)
