---
title: Azure Media Services v3 — często zadawane pytania | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na Azure Media Services v3 często zadawanych pytań.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/21/2019
ms.author: juliako
ms.openlocfilehash: 6e52a51c82529a98ef679dd747b9c8b7d177b660
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338829"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 — często zadawane pytania

W tym artykule przedstawiono odpowiedzi na często zadawane pytania dotyczące Azure Media Services (AMS) v3.

## <a name="general"></a>Ogólne

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Jakie role platformy Azure mogą wykonywać akcje na Azure Media Services zasobach? 

Zobacz [kontroli dostępu opartej na rolach (RBAC) dla kont Media Services](rbac-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Jak mogę skonfigurować jednostki zarezerwowane multimediów?

Analiza Audio i wideo zadania analizy, które są wyzwalane przez usługi Media Services v3 lub Video Indexer, aby uzyskać zaleca aprowizację swojego konta za pomocą 10 S3 lokalizacje MRU. Jeśli potrzebujesz więcej niż 10 S3 lokalizacje MRU, otwórz bilet pomocy technicznej przy użyciu [witryny Azure portal](https://portal.azure.com/).

Aby uzyskać szczegółowe informacje, zobacz [Skalowanie przetwarzania multimediów przy użyciu interfejsu wiersza polecenia](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Jaka jest zalecana metoda przetwarzania wideo?

Za pomocą [transformacji](https://docs.microsoft.com/rest/api/media/transforms) można skonfigurować typowe zadania związane z kodowaniem lub analizowaniem filmów wideo. Każde **przekształcenie** opisuje przepis lub przepływ pracy zadań do przetwarzania plików wideo lub audio. [Zadanie](https://docs.microsoft.com/rest/api/media/jobs) to rzeczywiste żądanie Media Services, aby zastosować transformację do danych wejściowych wideo lub audio. Po utworzeniu przekształcenia można przesłać zadania przy użyciu interfejsów API Media Services lub dowolnego z opublikowanych zestawów SDK. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transforms-jobs-concept.md).

### <a name="how-does-pagination-work"></a>Jak działa stronicowanie?

W przypadku korzystania z stronicowania należy zawsze używać następnego linku do wyliczania kolekcji i nie zależy od określonego rozmiaru strony. Aby uzyskać szczegółowe informacje i przykłady, zobacz [filtrowanie, porządkowanie, stronicowanie](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Jakie funkcje nie są jeszcze dostępne w wersji Azure Media Services v3?

Aby uzyskać szczegółowe informacje, zobacz [luki w funkcji w odniesieniu do interfejsów API w wersji 2](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Jaki jest proces przechodzenia konta Media Services między subskrypcjami?  

Aby uzyskać szczegółowe informacje, zobacz [Przechodzenie do konta Media Services między subskrypcjami](media-services-account-concept.md).

## <a name="live-streaming"></a>Transmisja strumieniowa na żywo 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Jak wstawiać końce/wideo i obrazy obrazów podczas przesyłania strumieniowego na żywo?

Kodowanie na żywo w programie Media Services V3 nie obsługuje jeszcze wstawiania obrazów wideo lub obrazu podczas przesyłania strumieniowego na żywo. 

Możesz użyć [lokalnego kodera](recommended-on-premises-live-encoders.md) do przełączania źródłowego wideo. Wiele aplikacji zapewnia możliwość przełączania źródeł, w tym Wirecast Telestream, przełączników Studio (w systemie iOS), OBS Studio (bezpłatna aplikacja) i wiele innych.

## <a name="content-protection"></a>Ochrona zawartości

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Czy należy używać szyfrowania kluczowego AES-128 lub systemu DRM?

Klienci często zastanawiasz się, czy należy używać szyfrowania AES lub systemu DRM. Podstawowa różnica między tymi dwoma systemami polega na tym, że szyfrowanie AES klucz zawartości jest przesyłany do klienta za pośrednictwem protokołu TLS, dzięki czemu klucz jest szyfrowany podczas przesyłania, ale bez dodatkowego szyfrowania ("w czyszczeniu"). W związku z tym klucz używany do odszyfrowywania zawartości jest dostępny dla odtwarzacza klienta i może być wyświetlany w wyniku śledzenia sieci na kliencie w postaci zwykłego tekstu. Szyfrowanie za pomocą klucza AES-128 jest odpowiednie do przypadków użycia, w których przeglądarka jest zaufaną stroną (na przykład w przypadku szyfrowania wideo firmowych dystrybuowanych przez pracowników).

Systemy DRM, takie jak PlayReady, Widevine i FairPlay, zapewniają dodatkowy poziom szyfrowania na klucz używany do odszyfrowywania zawartości w porównaniu z kluczem czystym AES-128. Klucz zawartości jest szyfrowany do klucza chronionego przez środowisko uruchomieniowe DRM w dodatkowy sposób do dowolnego szyfrowania na poziomie transportu zapewnianego przez protokół TLS. Ponadto odszyfrowywanie jest obsługiwane w bezpiecznym środowisku na poziomie systemu operacyjnego, gdzie jest trudniejsze, złośliwy użytkownik na ataki. DRM jest zalecana dla przypadków użycia, w której przeglądarka może nie być zaufaną stronę i wymagają najwyższy poziom zabezpieczeń.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Jak wyświetlić wideo tylko dla użytkowników, którzy mają określone uprawnienia, bez korzystania z usługi Azure AD?

Nie musisz używać żadnego określonego dostawcy tokenów (takiego jak Azure AD). Za pomocą szyfrowania klucza asymetrycznego można utworzyć własnego dostawcę [JWT](https://jwt.io/) (tzw. usługi STS, Secure Token Service). W niestandardowej usłudze STS można dodawać oświadczenia na podstawie logiki biznesowej.

Upewnij się, że wystawcy, odbiorcy i oświadczenia są dokładnie zgodne z informacjami o tym, co jest w tokenie JWT, i ContentKeyPolicyRestriction używanym w ContentKeyPolicy.

Aby uzyskać więcej informacji, zobacz [Ochrona zawartości przy użyciu Media Services szyfrowania dynamicznego](content-protection-overview.md).

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Jak i gdzie można uzyskać tokenu JWT token przed użyciem w celu żądania licencji lub klucza?

1. W przypadku produkcji należy mieć usługi bezpiecznego tokenu (STS) (usługa sieci Web), która wystawia token JWT przy żądaniu HTTPS. Dla testu, można użyć kod przedstawiony w **GetTokenAsync** metody zdefiniowanej w [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Odtwarzacz należy wysłać żądanie, po uwierzytelnieniu użytkownika do usługi STS do takich token i przypisz ją jako wartość tokenu. Możesz użyć [interfejsu API usługi Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/).

* Na przykład uruchomienia usługi STS, przy użyciu klucza symetrycznego i asymetrycznym można znaleźć [ https://aka.ms/jwt ](https://aka.ms/jwt). 
* Na przykład odtwarzacza, w oparciu o usługi Azure Media Player przy użyciu takich tokenu JWT zobacz [ https://aka.ms/amtest ](https://aka.ms/amtest) (expand link "player_settings", aby wyświetlić dane wejściowe tokenu).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Jak autoryzować żądania strumieniowo pliki wideo przy użyciu szyfrowania AES?

Właściwe podejście jest korzystanie z usługi STS (Secure Token Service):

W programie STS, w zależności od profilu użytkownika, Dodaj różne oświadczenia (na przykład "użytkownik Premium", "użytkownik podstawowy", "bezpłatny użytkownik wersji próbnej"). Za pomocą różnych oświadczeń w token JWT użytkownik może wyświetlić różną zawartość. Oczywiście dla innej zawartości/zasobu ContentKeyPolicyRestriction mają odpowiednie RequiredClaims.

Użyj Azure Media Services interfejsów API do konfigurowania dostarczania licencji/kluczy i szyfrowania zasobów (jak pokazano w [tym przykładzie](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Aby uzyskać więcej informacji, zobacz:

- [Omówienie ochrony zawartości](content-protection-overview.md)
- [Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP lub HTTPS?
Aplikacja odtwarzacza platformy ASP.NET MVC musi obsługiwać następujące czynności:

* Uwierzytelnianie użytkowników za pomocą usługi Azure AD, która jest w trakcie protokołu HTTPS.
* Token JWT wymiany między klientem i usługi Azure AD, która jest w trakcie protokołu HTTPS.
* Nabycie licencji DRM przez klienta, który musi być w ramach protokołu HTTPS, jeśli jest dostępna dostarczania licencji Media Services. Pakiet produktów technologii PlayReady nie zmusza HTTPS dostarczania licencji. Jeśli serwer licencji PlayReady znajduje się poza usługi Media Services, można użyć protokołu HTTP lub HTTPS.

Aplikacja odtwarzacza ASP.NET używa protokołu HTTPS najlepszym rozwiązaniem, więc Media Player znajduje się na stronie w obszarze protokołu HTTPS. Jednak HTTP była preferowana dla przesyłania strumieniowego, więc należy wziąć pod uwagę problem zawartości mieszanej.

* Przeglądarka nie zezwala na zawartość mieszana. A dodatki plug-in jak Silverlight i wtyczki dla OSMF złagodzenia i KRESKI. Zawartość mieszana jest kwestią zabezpieczeń ze względu na zagrożenie możliwość iniekcji złośliwego kodu JavaScript, który może spowodować, że dane klienta być narażone na ryzyko. Przeglądarki domyślnego blokowania z tej możliwości. Jedynym sposobem, aby go obejść, jest po stronie serwera (źródło), umożliwiając wszystkich domen (niezależnie od HTTP lub HTTPS). To prawdopodobnie nie jest dobrym pomysłem albo.
* Należy unikać zawartości mieszanej. Zarówno aplikacja odtwarzacza, jak i Media Player należy używać protokołu HTTP lub HTTPS. Podczas odtwarzania zawartości mieszanej silverlightSS tech wymaga czyszczenia ostrzeżenie mieszane zawartość. FlashSS tech obsługuje zawartość mieszana bez ostrzeżenia mieszane zawartość.
* Jeśli punkt końcowy przesyłania strumieniowego zostało utworzone przed sierpnia 2014, nie obsługuje protokołu HTTPS. W tym wypadku Utwórz i używać nowego punktu końcowego przesyłania strumieniowego do połączeń HTTPS.

### <a name="what-about-live-streaming"></a>Co transmisja strumieniowa na żywo?

Do ochrony, przesyłanie strumieniowe na żywo w usłudze Media Services przez traktowanie zasób skojarzony z programem jako zawartości wideo na żądanie, można użyć dokładnie tego samego projektu i implementacji. Aby zapewnić ochronę przy użyciu technologii wielowątkowości na żywo, Zastosuj tę samą konfigurację/przetwarzanie do zasobu, tak jakby był to zasób VOD przed skojarzeniem elementu zawartości z danymi wyjściowymi na żywo.

### <a name="what-about-license-servers-outside-media-services"></a>Jak wygląda serwerów licencji spoza usługi Media Services?

Często klienci inwestowanych w farmie serwerów licencji albo we własnych centrum danych lub jednym, będąc hostowaną przez usługodawców DRM. Za pomocą usługi Media Services content protection może działać w trybie hybrydowym. Zawartość można hostowanych i dynamicznie chronione w usłudze Media Services, podczas dostarczania licencji DRM przez serwery spoza usługi Media Services. W takim przypadku należy wziąć pod uwagę następujące zmiany:

* Usługa STS musi wystawiać tokeny są akceptowane, które można sprawdzić w farmie serwerów licencji. Na przykład serwerów licencji Widevine, które są dostarczane przez Axinom wymagać określonych token JWT z komunikatem o uprawnienia. W związku z tym musisz mieć usługi STS do wystawiania tokenów JWT. 
* Nie potrzebujesz już do konfigurowania usługi dostarczania licencji w usłudze Media Services. Należy podać adresy URL pozyskiwania licencji (w przypadku technologii PlayReady, Widevine i FairPlay) po skonfigurowaniu ContentKeyPolicies.

## <a name="media-services-v2-vs-v3"></a>Media Services V2 a v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Czy mogę używać Azure Portal do zarządzania zasobami v3?

Obecnie nie można zarządzać zasobami w wersji 3 z witryny Azure Portal. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref) lub jednego z obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

### <a name="is-there-an-assetfile-concept-in-v3"></a>Czy istnieje koncepcja AssetFile w wersji 3?

AssetFiles zostały usunięte z interfejsu API usługi AMS w celu oddzielenia Media Services od zależności zestawu SDK magazynu. Teraz magazyn, nie Media Services, utrzymuje informacje, które należą do magazynu. 

Aby uzyskać więcej informacji, zobacz [Migrowanie do Media Services v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Gdzie działa szyfrowanie magazynu po stronie klienta?

Teraz zaleca się użycie szyfrowania magazynu po stronie serwera (domyślnie włączone). Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Storage platformy Azure dla danych przechowywanych w spoczynku](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Następne kroki

[Media Services wersja 3 — Omówienie](media-services-overview.md)
