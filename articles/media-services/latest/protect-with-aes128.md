---
title: Korzystać z dynamicznego szyfrowania AES usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Dostarczanie zawartości zaszyfrowanych za pomocą kluczy szyfrowania AES 128-bitowego za pomocą usługi Microsoft Azure Media Services. Usługi Media Services udostępnia usługę klucza dostawy, która dostarcza klucze szyfrowania do autoryzowanych użytkowników. W tym temacie przedstawiono sposób dynamicznego szyfrowania z AES-128 i korzystania z usługi dostarczania klucza.
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
ms.date: 06/26/2018
ms.author: juliako
ms.openlocfilehash: 0da5bbee6d0d6401a35c301a8b35dc0efa77da7d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133034"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Użyj dynamicznego szyfrowania AES-128 i usługi dostarczania klucza

Usługi Media Services umożliwia dostarczanie HTTP Live Streaming (HLS), MPEG DASH i Smooth Streaming zaszyfrowanych za pomocą AES za pomocą 128-bitowe klucze szyfrowania. Usługi Media Services udostępnia usługę klucza dostawy, która dostarcza klucze szyfrowania do autoryzowanych użytkowników. Dla usługi Media Services zaszyfrować element zawartości należy skojarzyć klucz szyfrowania z StreamingLocator i również skonfigurować zasady klucza zawartości. Strumień zleconą przez odtwarzacz usługi Media Services używa określonego klucza do dynamicznego szyfrowania zawartości przy użyciu szyfrowania AES. Aby odszyfrować strumienia, odtwarzacza żądań klucz z usługi dostarczania klucza. Aby ustalić, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zasad autoryzacji, które podane dla klucza.

Artykuł jest oparta na [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) próbki. Przykład przedstawia sposób tworzenia transformacji kodowania, używającego wbudowaną ustawienie wstępne kodowania adaptacyjną szybkością transmisji bitów i wysyła strumień pliku bezpośrednio z [HTTPs źródłowy adres URL](job-input-from-http-how-to.md). Elementu zawartości wyjściowej jest następnie opublikowane przy użyciu szyfrowania AES (ClearKey). Dane wyjściowe z próbki jest adres URL usługi Azure Media Player, w tym zarówno manifest kreska i token AES potrzebne do odtwarzania plików. Próbki Ustawia godzinę wygaśnięcia tokenu JWT. Można Otwórz przeglądarkę i wklej adres URL wynikowej można uruchomić strony pokaz usługi Azure Media Player przy użyciu adresu URL i wypełnione już token (w następującym formacie: ``` https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.)

> [!NOTE]
> Można zaszyfrować poszczególnych zasobów z wieloma typami szyfrowania (AES-128, PlayReady, Widevine, FairPlay). Zobacz [przesyłania strumieniowego protokoły i typy szyfrowania](content-protection-overview.md#streaming-protocols-and-encryption-types), aby zobaczyć, co ma sens połączyć.

## <a name="prerequisites"></a>Wymagania wstępne

Następujące elementy są wymagane do wykonania czynności przedstawionych w samouczku.

* Przegląd [zawartości Omówienie ochrony](content-protection-overview.md) artykułu.
* Instalowanie narzędzia Visual Studio Code lub Visual Studio
* Utwórz nowe konto usługi Azure Media Services, zgodnie z opisem w [tego przewodnika Szybki Start](create-account-cli-quickstart.md).
* Pobierz poświadczenia niezbędne do używania interfejsów API usługi multimediów, postępując [dostępu do interfejsów API](access-api-cli-how-to.md)

## <a name="download-code"></a>Pobierz kod

Klonowanie repozytorium GitHub, który zawiera pełny przykład .NET omówione w tym temacie do komputera za pomocą następującego polecenia:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Przykładowe "szyfrowania z AES-128" znajduje się w [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) folderu.

> [!NOTE]
> Przykład tworzy unikatowy zasobów każdym uruchomieniu aplikacji. Zazwyczaj będzie używał istniejących zasobów, takich jak zasady i transformacje (Jeśli istniejących zasobów wymaganych konfiguracji). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Tworzenie zasobu wyjściowego  

[Zasób](https://docs.microsoft.com/rest/api/media/assets) wyjściowy przechowuje wynik zadania kodowania. Po ukończeniu kodowania zawartości wyjściowej została opublikowana przy użyciu szyfrowania AES (ClearKey).  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Uzyskaj lub Utwórz kodowania przekształcenia

Podczas tworzenia nowego wystąpienia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy określić, jakie dane wyjściowe ma ono tworzyć. Wymagany parametr to obiekt **TransformOutput**, jak pokazano w poniższym kodzie. Każdy obiekt **TransformOutput** zawiera element **Preset**. Element **Preset** zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania wymaganego obiektu **TransformOutput**. Przykład opisany w tym artykule używa wbudowanego elementu Preset o nazwie **AdaptiveStreaming**. Element Preset umożliwia kodowanie wejściowego wideo do automatycznie generowanej drabiny szybkości transmisji bitów (zestawu par „szybkość transmisji bitów-rozdzielczość”) na podstawie rozdzielczości wejściowej i szybkości transmisji bitów oraz tworzenie plików MP4 zgodnych ze standardem ISO, które zawierają wideo w formacie H.264 i dźwięk w formacie AAC, dla każdej pary „szybkość transmisji bitów-rozdzielczość”. 

Przed utworzeniem nowego [przekształcenie](https://docs.microsoft.com/rest/api/media/transforms), należy najpierw sprawdzić, czy istnieje już przy użyciu **uzyskać** metody, jak pokazano w następującym kodzie.  W przypadku usługi Media Services 3 metody **Get** wywołane dla obiektów zwracają **wartość null**, jeśli obiekt nie istnieje (sprawdzana jest nazwa bez uwzględniania wielkości liter).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Prześlij zadanie

Jak wspomniano powyżej, obiekt [Transform](https://docs.microsoft.com/rest/api/media/transforms) jest przepisem, a obiekt [Job](https://docs.microsoft.com/rest/api/media/jobs) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. Obiekt **Job** określa informacje takie jak lokalizacja wejściowego pliku wideo oraz danych wyjściowych.

W tym samouczku utworzymy dane wejściowe zadania na podstawie pliku, który jest pozyskanych bezpośrednio z [HTTPs źródłowy adres URL](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Ukończenie zadania zajmuje trochę czasu, a Ty chcesz otrzymać powiadomienie o tym fakcie. Poniższy przykładowy kod przedstawia sposób sondowania usługi pod kątem stanu [zadania](https://docs.microsoft.com/rest/api/media/jobs). Sondowanie nie jest najlepszym rozwiązaniem w przypadku zastosowań produkcyjnych ze względu na możliwe opóźnienia. Jeśli sondowanie będzie nadużywane w ramach konta, może zostać ograniczone. Deweloperzy zamiast niego powinni używać usługi Event Grid. Zobacz [Kierowanie zdarzeń do niestandardowego internetowego punktu końcowego](job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled (Zaplanowane)**, **Queued (W kolejce)**, **Processing (Przetwarzane)**, **Finished (Zakończone)** (stan końcowy). Jeśli zadanie napotka błąd, może być w stanie **Error (Błąd)**. Jeśli zadanie jest w trakcie anulowania, może być w stanie **Canceling (Anulowanie)**, a po zakończeniu tej operacji w stanie **Canceled (Anulowane)**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkey-policy"></a>Tworzenie zasad ContentKey

Klucz zawartości zapewnia bezpieczny dostęp do zasobów. Należy utworzyć zasadę klucza zawartości, która określa, jak klucz zawartości jest dostarczany do końca klientów. Klucz zawartości jest skojarzony z StreamingLocator. Usługi Media Services udostępnia usługę klucza dostawy, która dostarcza klucze szyfrowania do autoryzowanych użytkowników. 

Gdy odtwarzacza zażąda strumienia, usługa Media Services używa określonego klucza do dynamicznego szyfrowania zawartości (w tym przypadku za pomocą szyfrowania AES). Aby odszyfrować strumienia, odtwarzacza żądań klucz z usługi dostarczania klucza. Aby ustalić, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zasad autoryzacji, które podane dla klucza.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="get-a-token"></a>Uzyskaj token
        
W tym samouczku określono zawartości zasad klucza, która ma ograniczenie tokenu. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje tokenów w [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) formatów (JWT) oraz że jest możemy skonfigurować w próbce.

ContentKeyIdentifierClaim jest używany w ContentKeyPolicy, co oznacza token przedstawione usługi dostarczania klucz musi mieć identyfikator ContentKey w nim. W przykładzie podczas tworzenia StreamingLocator klucz zawartości nie jest określona, system tworzy losowe jeden firmie Microsoft. Aby wygenerować test tokenu, musi uzyskujemy ContentKeyId mają zostać umieszczone w ContentKeyIdentifierClaim oświadczeń.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="create-a-streaminglocator"></a>Tworzenie lokalizatora przesyłania strumieniowego

Po zakończeniu kodowania następnym krokiem jest udostępnienie klientom w zasobie wyjściowym pliku wideo, który można odtwarzać. Działanie to można wykonać w dwóch krokach: najpierw należy utworzyć obiekt [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), a następnie utworzyć adresy URL przesyłania strumieniowego, których mogą używać klienci. 

Proces tworzenia obiektu **StreamingLocator** jest nazywany publikowaniem. Domyślnie obiekt **StreamingLocator** jest ważny natychmiast po wykonaniu wywołań interfejsu API i aż do jego usunięcia, chyba że skonfigurujesz opcjonalne czasy rozpoczęcia i zakończenia. 

Podczas tworzenia obiektu [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) musisz określić żądany element **StreamingPolicyName**. W tym samouczku użyto jednego z PredefinedStreamingPolicies, w których usługi Azure Media Services można publikować zawartość do przesyłania strumieniowego. W tym przykładzie szyfrowanie AES Envelope jest stosowany (określane również jako ClearKey szyfrowania, ponieważ klucz jest dostarczany klientowi odtwarzania za pośrednictwem protokołu HTTPS, a nie licencji DRM).

> [!IMPORTANT]
> W przypadku korzystania z niestandardowego elementu [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i używać ich ponownie dla obiektów StreamingLocator zawsze, gdy są potrzebne takie same opcje szyfrowania i protokoły. Konto usługi Media Service jest objęte limitem przydziału dotyczącym liczby pozycji elementu StreamingPolicy. Nie należy tworzyć nowego elementu StreamingPolicy dla każdego obiektu StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="build-a-dash-streaming-url"></a>Tworzenie ŁĄCZNIKIEM URL przesyłania strumieniowego

Teraz, gdy [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) został utworzony, można uzyskać adresy URL przesyłania strumieniowego. Aby utworzyć adres URL, musisz połączyć nazwę hosta obiektu [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) i ścieżkę obiektu **StreamingLocator**. W tym przykładzie jest używany *domyślny* obiekt **StreamingEndpoint**. Po pierwszym utworzeniu konta usługi Media Service ten *domyślny* obiekt **StreamingEndpoint** będzie zatrzymany, więc należy wywołać metodę **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Zazwyczaj należy wyczyścić wszystko z wyjątkiem obiektów, których zamierzasz użyć ponownie (zazwyczaj są to obiekty Transform, obiekty StreamingLocator są utrwalane itd.). Jeśli Twoje konto ma być czyste po przeprowadzeniu eksperymentów, należy usunąć zasoby, których ponowne użycie nie jest planowane.  Na przykład następujący kod usuwa zadania.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="next-steps"></a>Kolejne kroki

[Omówienie](content-protection-overview.md)