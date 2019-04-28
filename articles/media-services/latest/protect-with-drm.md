---
title: Używanie usługi dostarczania licencji szyfrowania dynamicznego technologii DRM za pomocą usługi Azure Media Services | Microsoft Docs
description: Usługa Azure Media Services umożliwia dostarczanie strumieni zaszyfrowanych za pomocą licencji Microsoft PlayReady, Google Widevine i Apple FairPlay.
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
ms.date: 02/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f53ae122e9888f3e537a3557b6ac5bd76856c2eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995862"
---
# <a name="use-drm-dynamic-encryption-and-license-delivery-service"></a>Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM

Usługa Azure Media Services umożliwia dostarczanie strumieni MPEG-DASH, Smooth Streaming i HTTP Live Streaming (HLS) chronionych przy użyciu [usługi zarządzania prawami cyfrowymi (DRM) PlayReady](https://www.microsoft.com/playready/overview/). Usługi Media Services można również używać do dostarczania zaszyfrowanych strumieni DASH z licencjami DRM **Google Widevine**. Obie usługi, PlayReady i Widevine, szyfrują dane zgodnie ze specyfikacją Common Encryption (ISO/IEC CENC 23001-7). Usługa Media Services pozwala też na szyfrowanie zawartości HLS przy użyciu **systemu Apple FairPlay** (AES-128 CBC). 

Ponadto usługa Media Services udostępnia usługę dostarczania następujących licencji DRM: PlayReady, Widevine i FairPlay. Gdy użytkownik zażąda zawartości chronionej przy użyciu funkcji DRM, aplikacja odtwarzacza zażąda licencji od usługi licencjonowania Media Services. Jeśli aplikacja odtwarzacza zostanie autoryzowana, usługa licencjonowania Media Services wystawi licencję odtwarzaczowi. Licencja zawiera klucz odszyfrowujący, który może zostać użyty przez odtwarzacz klienta do odszyfrowania i strumieniowego przesyłania zawartości.

Ten artykuł jest oparty na przykładzie [szyfrowania przy użyciu technologii DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM). W tym przykładzie przedstawiono między innymi sposób wykonywania następujących czynności:

* Tworzenie obiektu Transform kodowania, który używa wbudowanego ustawienia wstępnego na potrzeby kodowania z adaptacyjną szybkością transmisji bitów i pozyskuje plik bezpośrednio ze [źródłowego adresu URL protokołu HTTPs](job-input-from-http-how-to.md).
* Ustawianie klucza podpisywania używanego na potrzeby weryfikacji tokenu.
* Ustawianie wymagań (ograniczeń) dotyczących zasad kluczy zawartości, które muszą zostać spełnione, aby można było dostarczać klucze z określoną konfiguracją. 

    * Konfigurowanie 
    
        W tym przykładzie licencje [PlayReady](playready-license-template-overview.md) i [Widevine](widevine-license-template-overview.md) są konfigurowane tak, aby można było dostarczać je przy użyciu usługi dostarczania licencji Media Services. Mimo że ta przykładowa aplikacja nie konfiguruje licencji [FairPlay](fairplay-license-overview.md), zawiera ona metodę, której można użyć do skonfigurowania systemu FairPlay. Jeśli chcesz, możesz dodać konfigurację systemu FairPlay jako inną opcję.

    * Ograniczenie

        Aplikacja ustawia ograniczenie typu tokenu JWT w obrębie zasad.

* Tworzenie obiektu StreamingLocator przeznaczonego dla określonego elementu zawartości i o określonej nazwie zasad przesyłania strumieniowego. W tym przypadku są używane wstępnie zdefiniowane zasady. Ustawiają one dwa klucze zawartości w elemencie StreamingLocator: AES-128 (koperta) i CENC (PlayReady i Widevine).  
    
    Po utworzeniu obiektu StreamingLocator element zawartości danych wyjściowych jest publikowany i udostępniany klientom do odtwarzania.

    > [!NOTE]
    > Upewnij się, że punkt końcowy przesyłania strumieniowego, z którego chcesz przesyłać zawartość strumieniowo, ma stan Uruchomiony.

* Tworzenie adresu URL do usługi Azure Media Player, który obejmuje manifest DASH i token PlayReady potrzebne do odtwarzania szyfrowanej zawartości PlayReady. W przykładzie czas wygaśnięcia tokenu został ustawiony na 1 godzinę. 

    Można otworzyć przeglądarkę i wkleić wynikowy adres URL, aby uruchomić stronę pokazu usługi Azure Media Player przy użyciu już wypełnionego adresu URL i tokenu.  

    ![Ochrona przy użyciu technologii DRM](./media/protect-with-drm/playready_encrypted_url.png)

> [!NOTE]
> Każdy element zawartości można szyfrować przy użyciu wielu typów szyfrowania (AES-128, PlayReady, Widevine, FairPlay). Zobacz [Streaming protocols and encryption types (Protokoły i typy szyfrowania przesyłania strumieniowego)](content-protection-overview.md#streaming-protocols-and-encryption-types), aby sprawdzić, które rozwiązania warto łączyć.

Opisany w tym artykule przykład generuje następujący wynik:

![Usługa AMS z filmem wideo chronionym przez technologię DRM](./media/protect-with-drm/ams_player.png)

## <a name="prerequisites"></a>Wymagania wstępne

Następujące elementy są wymagane do wykonania czynności przedstawionych w samouczku.

* Zapoznanie się z artykułem [Content protection overview (Omówienie ochrony zawartości)](content-protection-overview.md).
* Zapoznanie się z artykułem [Design multi-drm content protection system with access control](design-multi-drm-system-with-access-control.md) (Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu).
* Instalowanie narzędzia Visual Studio Code lub Visual Studio
* Utworzenie nowego konta usługi Azure Media Services zgodnie z opisem w [tym przewodniku Szybki start](create-account-cli-quickstart.md).
* Uzyskanie poświadczeń potrzebnych do korzystania z interfejsów API usługi Media Services przez wykonanie kroków [uzyskiwania dostęp do interfejsów API](access-api-cli-how-to.md).
* Ustawienie odpowiednich wartości w pliku konfiguracji aplikacji (appsettings.json).

## <a name="download-code"></a>Pobieranie kodu

Sklonuj repozytorium GitHub zawierające pełny przykład dla platformy .NET opisany w tym artykule na swoją maszynę za pomocą następującego polecenia:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Przykład szyfrowania za pomocą technologii DRM znajduje się w folderze [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> Przykładowa aplikacja tworzy unikatowe zasoby po każdym uruchomieniu aplikacji. Zazwyczaj istniejące zasoby, takie jak przekształcenia i zasady, będą ponownie używane (jeśli istniejący zasób ma wymagane konfiguracje). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Tworzenie zasobu wyjściowego  

[Zasób](https://docs.microsoft.com/rest/api/media/assets) wyjściowy przechowuje wynik zadania kodowania.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Pobieranie lub tworzenie obiektu Transform kodowania

Podczas tworzenia nowego wystąpienia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy określić, jakie dane wyjściowe ma ono tworzyć. Wymagany parametr to obiekt **TransformOutput**, jak pokazano w poniższym kodzie. Każdy obiekt **TransformOutput** zawiera element **Preset**. Element **Preset** zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania wymaganego obiektu **TransformOutput**. Przykład opisany w tym artykule używa wbudowanego elementu Preset o nazwie **AdaptiveStreaming**. Element Preset umożliwia kodowanie wejściowego wideo do automatycznie generowanej drabiny szybkości transmisji bitów (zestawu par „szybkość transmisji bitów-rozdzielczość”) na podstawie rozdzielczości wejściowej i szybkości transmisji bitów oraz tworzenie plików MP4 zgodnych ze standardem ISO, które zawierają wideo w formacie H.264 i dźwięk w formacie AAC, dla każdej pary „szybkość transmisji bitów-rozdzielczość”. 

Przed utworzeniem nowego obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy najpierw sprawdzić, czy taki obiekt już istnieje, używając metody **Get**, tak jak przedstawiono w poniższym kodzie.  W przypadku usługi Media Services 3 metody **Get** wywołane dla obiektów zwracają **wartość null**, jeśli obiekt nie istnieje (sprawdzana jest nazwa bez uwzględniania wielkości liter).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Przesyłanie zadania

Jak wspomniano powyżej, obiekt [Transform](https://docs.microsoft.com/rest/api/media/transforms) jest przepisem, a obiekt [Job](https://docs.microsoft.com/rest/api/media/jobs) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. Obiekt **Job** określa informacje takie jak lokalizacja wejściowego pliku wideo oraz danych wyjściowych.

W tym samouczku utworzymy dane wejściowe zadania w oparciu o plik pozyskany ze [źródłowego adresu protokołu HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Ukończenie zadania zajmuje trochę czasu, a Ty chcesz otrzymać powiadomienie o tym fakcie. Poniższy przykładowy kod przedstawia sposób sondowania usługi pod kątem stanu [zadania](https://docs.microsoft.com/rest/api/media/jobs). Sondowanie nie jest najlepszym rozwiązaniem w przypadku zastosowań produkcyjnych ze względu na możliwe opóźnienia. Jeśli sondowanie będzie nadużywane w ramach konta, może zostać ograniczone. Deweloperzy zamiast niego powinni używać usługi Event Grid. Zobacz [Kierowanie zdarzeń do niestandardowego internetowego punktu końcowego](job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled** (Zaplanowane), **Queued** (W kolejce), **Processing** (Przetwarzane), **Finished** (Zakończone — jest to stan końcowy). Jeśli zadanie napotka błąd, może być w stanie **Error (Błąd)**. Jeśli zadanie jest w trakcie anulowania, może być w stanie **Canceling (Anulowanie)**, a po zakończeniu tej operacji w stanie **Canceled (Anulowane)**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>Tworzenie zasad ContentKeyPolicy

Klucz zawartości zapewnia bezpieczny dostęp do elementów zawartości. Należy utworzyć zasady klucza zawartości, które konfigurują sposób dostarczania klucza zawartości do klientów końcowych. Klucz zawartości jest skojarzony z lokalizatorem StreamingLocator. Usługa Media Services udostępnia również usługę dostarczania kluczy, która dostarcza klucze szyfrowania i licencje do autoryzowanych użytkowników. 

Należy ustawić wymagania (ograniczenia) dotyczące zasad kluczy zawartości, które muszą zostać spełnione, aby można było dostarczać klucze z określoną konfiguracją. W tym przykładzie ustawimy następujące konfiguracje i wymagania:

* Konfigurowanie 

    Licencje [PlayReady](playready-license-template-overview.md) i [Widevine](widevine-license-template-overview.md) są skonfigurowane tak, aby można było dostarczać je przy użyciu usługi dostarczania licencji w usłudze Media Services. Mimo że ta przykładowa aplikacja nie konfiguruje licencji [FairPlay](fairplay-license-overview.md), zawiera ona metodę, której można użyć do skonfigurowania systemu FairPlay. Możesz dodać konfigurację systemu FairPlay jako inną opcję.

* Ograniczenie

    Aplikacja ustawia ograniczenie typu tokenu JWT w obrębie zasad.

Gdy odtwarzacz żąda strumienia, usługa Media Services używa wybranego klucza do dynamicznego szyfrowania zawartości. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby określić, czy użytkownik został autoryzowany do otrzymywania klucza, usługa ocenia zasady kluczy zawartości, które wybrano dla klucza.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>Tworzenie lokalizatora przesyłania strumieniowego

Po zakończeniu kodowania i ustawieniu zasad kluczy zawartości następnym krokiem jest udostępnienie klientom w wyjściowym elemencie zawartości pliku wideo, który można odtwarzać. Można to zrobić, wykonując dwie czynności: 

1. Utworzenie obiektu [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators).
2. Utworzenie adresów URL przesyłania strumieniowego, których klienci mogą używać. 

Proces tworzenia obiektu **StreamingLocator** jest nazywany publikowaniem. Domyślnie obiekt **StreamingLocator** jest ważny natychmiast po wykonaniu wywołań interfejsu API i aż do jego usunięcia, chyba że skonfigurujesz opcjonalne czasy rozpoczęcia i zakończenia. 

Podczas tworzenia obiektu [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) musisz określić żądany element **StreamingPolicyName**. W tym samouczku używamy jednej ze wstępnie zdefiniowanych zasad StreamingPolicies, która informuje usługę Azure Media Services o wymaganym sposobie publikowania zawartości na potrzeby przesyłania strumieniowego. W tym przykładzie ustawiamy element StreamingLocator.StreamingPolicyName na zasady „Predefined_MultiDrmCencStreaming”. Te zasady wskazują, że chcesz wygenerować dwa klucze zawartości (koperta i CENC) i ustawić je w lokalizatorze. Dlatego stosowane są szyfrowania typu koperta, PlayReady i Widevine (klucz jest dostarczany do klienta odtwarzania w oparciu o skonfigurowane licencje DRM). Jeśli chcesz także zaszyfrować strumień przy użyciu metody CBCS (FairPlay), użyj zasad „Predefined_MultiDrmStreaming”. 

> [!IMPORTANT]
> W przypadku korzystania z niestandardowego elementu [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i używać ich ponownie dla obiektów StreamingLocator zawsze, gdy są potrzebne takie same opcje szyfrowania i protokoły. Konto usługi Media Service jest objęte limitem przydziału dotyczącym liczby pozycji elementu StreamingPolicy. Nie należy tworzyć nowego elementu StreamingPolicy dla każdego obiektu StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Pobieranie tokenu testowego
        
W tym samouczku określamy zasady klucza zawartości z ograniczenie dotyczącym tokenu. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje tokeny w formatach [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) — ten format skonfigurujemy w przykładzie.

Element ContentKeyIdentifierClaim jest używany w zasadach ContentKeyPolicy, co oznacza, że token przedstawiany usłudze dostarczania kluczy musi zawierać identyfikator ContentKey. W tym przykładzie nie określamy klucza zawartości podczas tworzenia obiektu StreamingLocator. System tworzy dla nas losowy klucz. W celu wygenerowania tokenu testowego musimy uzyskać identyfikator ContentKeyId do umieszczenia w oświadczeniu ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Kompilowanie adresu URL przesyłania strumieniowego DASH

Teraz, po utworzeniu obiektu [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), możesz pobrać adresy URL przesyłania strumieniowego. Aby utworzyć adres URL, musisz połączyć nazwę hosta obiektu [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) i ścieżkę obiektu **StreamingLocator**. W tym przykładzie jest używany *domyślny* obiekt **StreamingEndpoint**. Po pierwszym utworzeniu konta usługi Media Service ten *domyślny* obiekt **StreamingEndpoint** będzie zatrzymany, więc należy wywołać metodę **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Zazwyczaj należy wyczyścić wszystko z wyjątkiem obiektów, których zamierzasz użyć ponownie (zazwyczaj są to obiekty Transform, obiekty StreamingLocator są utrwalane itd.). Jeśli Twoje konto ma być czyste po przeprowadzeniu eksperymentów, należy usunąć zasoby, których ponowne użycie nie jest planowane.  Na przykład następujący kod usuwa zadania.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [stosować ochronę przy użyciu algorytmu AES-128](protect-with-aes128.md)
