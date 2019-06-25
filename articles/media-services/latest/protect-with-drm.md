---
title: DRM dynamiczne szyfrowanie i licencji usługi dostarczania za pomocą usługi Azure Media Services | Dokumentacja firmy Microsoft
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
ms.date: 05/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 4f1618260f6bfa0491e919e8aab1841e61603e5b
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273254"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Samouczek: Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM

> [!NOTE]
> Mimo że w tym samouczku użyto [zestawu .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) przykłady ogólne kroki są takie same dla [interfejsu API REST](https://docs.microsoft.com/rest/api/media/liveevents), [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest), lub inne obsługiwane [zestawów SDK](media-services-apis-overview.md#sdks) .

Usługa Azure Media Services umożliwia dostarczanie strumieni zaszyfrowanych za pomocą licencji Microsoft PlayReady, Google Widevine i Apple FairPlay. Aby uzyskać szczegółowe informacje, zobacz [Content protection dzięki dynamicznemu szyfrowaniu](content-protection-overview.md).

Ponadto usługa Media Services udostępnia usługę dostarczania następujących licencji DRM: PlayReady, Widevine i FairPlay. Gdy użytkownik zażąda zawartości chronionej przy użyciu funkcji DRM, aplikacja odtwarzacza zażąda licencji od usługi licencjonowania Media Services. Jeśli aplikacja odtwarzacza zostanie autoryzowana, usługa licencjonowania Media Services wystawi licencję odtwarzaczowi. Licencja zawiera klucz odszyfrowujący, który może zostać użyty przez odtwarzacz klienta do odszyfrowania i strumieniowego przesyłania zawartości.

Ten artykuł jest oparty na przykładzie [szyfrowania przy użyciu technologii DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM). 

Opisany w tym artykule przykład generuje następujący wynik:

![Usługa AMS z filmem wideo chronionym przez technologię DRM](./media/protect-with-drm/ams_player.png)

Ten samouczek przedstawia sposób wykonania następujących czynności:    

> [!div class="checklist"]
> * Utwórz kodowania przekształcenia
> * Ustaw klucz podpisywania używany podczas weryfikacji tokenu
> * Ustaw wymagania dotyczące zasad kluczy zawartości
> * Utwórz StreamingLocator z określonymi zasadami przesyłania strumieniowego
> * Utwórz adres URL używany do odtwarzania pliku

## <a name="prerequisites"></a>Wymagania wstępne

Następujące elementy są wymagane do wykonania czynności przedstawionych w samouczku.

* Zapoznanie się z artykułem [Content protection overview (Omówienie ochrony zawartości)](content-protection-overview.md).
* Przegląd [projektowania technologii multi-DRM systemu ochrony zawartości przy użyciu kontroli dostępu](design-multi-drm-system-with-access-control.md)
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

[Zasób](assets-concept.md) wyjściowy przechowuje wynik zadania kodowania.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Pobieranie lub tworzenie obiektu Transform kodowania

Podczas tworzenia nowego wystąpienia obiektu [Transform](transforms-jobs-concept.md) należy określić, jakie dane wyjściowe ma ono tworzyć. Wymagany jest parametr `transformOutput` obiektu, jak pokazano w poniższym kodzie. Zawiera każdy TransformOutput **ustawienie wstępne**. Ustawienie wstępne w tym artykule opisano krok po kroku instrukcje operacji przetwarzania wideo lub audio, które mają być używane do generowania żądaną TransformOutput. Przykład opisany w tym artykule używa wbudowanego elementu Preset o nazwie **AdaptiveStreaming**. Element Preset umożliwia kodowanie wejściowego wideo do automatycznie generowanej drabiny szybkości transmisji bitów (zestawu par „szybkość transmisji bitów-rozdzielczość”) na podstawie rozdzielczości wejściowej i szybkości transmisji bitów oraz tworzenie plików MP4 zgodnych ze standardem ISO, które zawierają wideo w formacie H.264 i dźwięk w formacie AAC, dla każdej pary „szybkość transmisji bitów-rozdzielczość”. 

Przed utworzeniem nowego obiektu **Transform** należy najpierw sprawdzić, czy taki obiekt już istnieje, używając metody **Get**, tak jak przedstawiono w poniższym kodzie.  W przypadku usługi Media Services 3 metody **Get** wywołane dla obiektów zwracają **wartość null**, jeśli obiekt nie istnieje (sprawdzana jest nazwa bez uwzględniania wielkości liter).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Przesyłanie zadania

Jak wspomniano powyżej, obiekt **Transform** jest przepisem, a obiekt [Job](transforms-jobs-concept.md) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. Obiekt **Job** określa informacje takie jak lokalizacja wejściowego pliku wideo oraz danych wyjściowych.

W tym samouczku utworzymy dane wejściowe zadania w oparciu o plik pozyskany ze [źródłowego adresu protokołu HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Ukończenie zadania zajmuje trochę czasu, a Ty chcesz otrzymać powiadomienie o tym fakcie. Poniższy przykładowy kod przedstawia sposób sondowania usługi pod kątem stanu **zadania**. Sondowanie nie jest najlepszym rozwiązaniem w przypadku zastosowań produkcyjnych ze względu na możliwe opóźnienia. Jeśli sondowanie będzie nadużywane w ramach konta, może zostać ograniczone. Deweloperzy zamiast niego powinni używać usługi Event Grid. Zobacz [Kierowanie zdarzeń do niestandardowego internetowego punktu końcowego](job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled** (Zaplanowane), **Queued** (W kolejce), **Processing** (Przetwarzane), **Finished** (Zakończone — jest to stan końcowy). Jeśli zadanie napotka błąd, może być w stanie **Error (Błąd)** . Jeśli zadanie jest w trakcie anulowania, może być w stanie **Canceling (Anulowanie)** , a po zakończeniu tej operacji w stanie **Canceled (Anulowane)** .

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Utwórz zasadę klucza zawartości

Klucz zawartości zapewnia bezpieczny dostęp do elementów zawartości. Należy utworzyć [zasad klucza zawartości](content-key-policy-concept.md) podczas szyfrowania zawartości przy użyciu DRM. Zasady umożliwiają skonfigurowanie, sposób dostarczania klucza zawartości dla klientów końcowych. Klucz zawartości jest skojarzony z lokalizatorem przesyłania strumieniowego. Usługa Media Services udostępnia również usługę dostarczania kluczy, która dostarcza klucze szyfrowania i licencje do autoryzowanych użytkowników. 

Należy ustawić wymagania (ograniczenia) na **zasad klucza zawartości** , muszą zostać spełnione w celu dostarczania kluczy z określoną konfiguracją. W tym przykładzie ustawimy następujące konfiguracje i wymagania:

* Konfigurowanie 

    Licencje [PlayReady](playready-license-template-overview.md) i [Widevine](widevine-license-template-overview.md) są skonfigurowane tak, aby można było dostarczać je przy użyciu usługi dostarczania licencji w usłudze Media Services. Mimo że ta przykładowa aplikacja nie konfiguruje licencji [FairPlay](fairplay-license-overview.md), zawiera ona metodę, której można użyć do skonfigurowania systemu FairPlay. Możesz dodać konfigurację systemu FairPlay jako inną opcję.

* Ograniczenie

    Aplikacja ustawia ograniczenie typu tokenu JWT w obrębie zasad.

Gdy odtwarzacz żąda strumienia, usługa Media Services używa wybranego klucza do dynamicznego szyfrowania zawartości. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby określić, czy użytkownik został autoryzowany do otrzymywania klucza, usługa ocenia zasady kluczy zawartości, które wybrano dla klucza.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego

Po zakończeniu kodowania i ustawieniu zasad kluczy zawartości następnym krokiem jest udostępnienie klientom w wyjściowym elemencie zawartości pliku wideo, który można odtwarzać. Można to zrobić, wykonując dwie czynności: 

1. Tworzenie [lokalizatora przesyłania strumieniowego](streaming-locators-concept.md)
2. Utworzenie adresów URL przesyłania strumieniowego, których klienci mogą używać. 

Proces tworzenia **lokalizatora przesyłania strumieniowego** nosi nazwę publikowania. Domyślnie **lokalizator przesyłania strumieniowego** jest ważny natychmiast po wykonaniu wywołań interfejsu API i aż do jego usunięcia, chyba że skonfigurujesz opcjonalne czasy rozpoczęcia i zakończenia. 

Podczas tworzenia **lokalizatora przesyłania strumieniowego**, musisz określić żądaną `StreamingPolicyName`. W tym samouczku używamy jednego z wstępnie zdefiniowanych przesyłania strumieniowego zasad, które informuje, jak publikować zawartość dla przesyłania strumieniowego usługi Azure Media Services. W tym przykładzie ustawiamy element StreamingLocator.StreamingPolicyName na zasady „Predefined_MultiDrmCencStreaming”. Szyfrowanie PlayReady i Widevine są stosowane, klucz jest dostarczany klientowi odtwarzania oparte na skonfigurowanym licencji DRM. Jeśli chcesz także zaszyfrować strumień przy użyciu metody CBCS (FairPlay), użyj zasad „Predefined_MultiDrmStreaming”. 

> [!IMPORTANT]
> W przypadku korzystania z niestandardowych [zasad przesyłania strumieniowego](streaming-policy-concept.md) należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i używać ich ponownie dla obiektów StreamingLocator zawsze, gdy są potrzebne takie same opcje szyfrowania i protokoły. Konto usługi Media Service jest objęte limitem przydziału dotyczącym liczby pozycji elementu StreamingPolicy. Nie należy tworzyć nowego elementu StreamingPolicy dla każdego obiektu StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Pobieranie tokenu testowego
        
W tym samouczku określamy zasady klucza zawartości z ograniczenie dotyczącym tokenu. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje tokeny w formatach [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) — ten format skonfigurujemy w przykładzie.

Element ContentKeyIdentifierClaim jest używany w zasadach ContentKeyPolicy, co oznacza, że token przedstawiany usłudze dostarczania kluczy musi zawierać identyfikator ContentKey. W tym przykładzie firma Microsoft określać klucz zawartości, tworząc Lokalizator przesyłania strumieniowego, system tworzy losowe jeden dla nas. W celu wygenerowania tokenu testowego musimy uzyskać identyfikator ContentKeyId do umieszczenia w oświadczeniu ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Utworzenie adresu URL przesyłania strumieniowego

Teraz, po utworzeniu obiektu [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), możesz pobrać adresy URL przesyłania strumieniowego. Aby utworzyć adres URL, musisz złączyć [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) nazwy hosta i **lokalizatora przesyłania strumieniowego** ścieżki. W tym przykładzie jest używany *domyślny* **punkt końcowy przesyłania strumieniowego**. Po pierwszym utworzeniu konta usługi Media Service ten *domyślny* **punkt końcowy przesyłania strumieniowego** będzie zatrzymany, więc należy wywołać metodę **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Po uruchomieniu aplikacji, zobaczysz następujące czynności:

![Ochrona przy użyciu technologii DRM](./media/protect-with-drm/playready_encrypted_url.png)

Można otworzyć przeglądarkę i wkleić wynikowy adres URL, aby uruchomić stronę pokazu usługi Azure Media Player przy użyciu już wypełnionego adresu URL i tokenu. 
 
## <a name="clean-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Zazwyczaj należy wyczyścić wszystko z wyjątkiem obiektów, których zamierzasz użyć ponownie (zazwyczaj są to obiekty Transform, obiekty StreamingLocator są utrwalane itd.). Jeśli Twoje konto ma być czyste po przeprowadzeniu eksperymentów, należy usunąć zasoby, których ponowne użycie nie jest planowane.  Na przykład następujący kod usuwa zadania.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej. 

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Prześlij opinię i pobieranie aktualizacji

Zapoznaj się z [społeczności usługi Azure Media Services](media-services-community.md) artykuł, aby wyświetlić różne sposoby zadawaj pytania, Prześlij opinię i pobrać aktualizacje o usłudze Media Services.

## <a name="next-steps"></a>Kolejne kroki

Sprawdź

> [!div class="nextstepaction"]
> [Ochrona przy użyciu algorytmu AES-128](protect-with-aes128.md)

