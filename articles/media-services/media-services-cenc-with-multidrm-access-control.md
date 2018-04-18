---
title: 'CENC z wieloma DRM i kontrola dostępu: odwołanie do projektu i wdrożenia na platformie Azure i usługi Azure Media Services | Dokumentacja firmy Microsoft'
description: Więcej informacji na temat sposobu licencjonowania Microsoft Smooth Streaming klienta Eksportowanie zestawu.
services: media-services
documentationcenter: ''
author: willzhan
manager: cfowler
editor: ''
ms.assetid: 7814739b-cea9-4b9b-8370-538702e5c615
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;kilroyh;yanmf;juliako
ms.openlocfilehash: cac1513d805e01f2c9633b3b318ad6808027904e
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2017
---
# <a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC z wieloma DRM i kontrola dostępu: odwołanie do projektu i wdrożenia na platformie Azure i usługi Azure Media Services
 
## <a name="introduction"></a>Wprowadzenie
Projektowanie i tworzenie podsystemu zarządzania (prawami cyfrowymi DRM) prawami cyfrowymi over-górnym rogu (OTT) lub online rozwiązań do przesyłania strumieniowego, jest złożonym zadaniem. Operatory/online dostawców wideo zwykle zewnętrzny to zadanie specjalne DRM usługodawców. Celem tego dokumentu jest do prezentowania odwołanie do projektowania i implementacji podsystemu DRM end-to-end w OTT lub online rozwiązania do przesyłania strumieniowego.

Docelowe czytników dla tego dokumentu są inżynierów pracujący w podsystemów DRM OTT lub online rozwiązań przesyłania strumieniowego Wieloekranowy lub czytników zainteresowanych podsystemów DRM. Zakłada się, że czytników zapoznali się z co najmniej jednej z technologii DRM na rynku, takich jak PlayReady i Widevine, FairPlay albo Adobe dostępu.

W tej dyskusji DRM będziemy również obejmować szyfrowania common encryption (CENC) z wieloma DRM. Główne trendu w online przesyłania strumieniowego oraz z branży OTT polega na użyciu CENC z wielu natywnego DRM na różnych platformach klienckich. Tego trendu jest przesunięcia od poprzedniego używany pojedynczy DRM i zestawu SDK klienta dla różnych platform klienta. Gdy używasz CENC z wielu natywnego DRM, zarówno PlayReady i Widevine są szyfrowane na [Common Encryption (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specyfikacji.

Zalety CENC z wieloma DRM to it:

* Ogranicza koszty szyfrowania, ponieważ proces szyfrowania pojedynczego służy do różnych platform z jego natywnego DRMs docelowych.
* Zmniejsza koszt zarządzanie zasobami zaszyfrowane, ponieważ potrzebna jest tylko jednej kopii zaszyfrowanych zasobów.
* Eliminuje licencjonowania koszt, ponieważ native DRM client to zwykle wolnego miejsca na jego natywnego platforma klientów DRM.

Firma Microsoft dokłada active promotor kreska i CENC wraz z niektórych odtwarzaczy branży głównych. Usługa Azure Media Services obsługuje kreska i CENC. Ostatnie anonsów znajduje się na następujących blogach:

*  [Powiadomienie o usługach dostarczania licencji Google Widevine w usłudze Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Usługa Azure Media Services dodaje tworzenia pakietów Google Widevine dostarczania strumienia wieloma DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="overview-of-this-article"></a>Informacje w tym artykule
Cele w tym artykule są:

* Podaj odwołanie do projektu podsystemu DRM, która używa CENC z wieloma DRM.
* Udostępnić implementację odwołania na platformie Azure/Media Services.
* Omówiono niektóre tematy projekt i implementację.

W artykule określenie "wieloma DRM" obejmuje następujące produkty:

* Microsoft PlayReady
* Google Widevine
* FairPlay firmy Apple 

W poniższej tabeli przedstawiono aplikacji natywnych platformy lub natywne i przeglądarek obsługiwanych przez każdy DRM.

| **Platforma klienta** | **Macierzysty mechanizm obsługi DRM** | **Przeglądarka/aplikacji** | **Formatów przesyłania strumieniowego** |
| --- | --- | --- | --- |
| **Inteligentne telewizorach, operator odbiornikami, odbiornikami OTT** |PlayReady przede wszystkim i/lub Widevine i/lub innych |Linux, Opera, WebKit, innych |Różne formaty. |
| **Urządzeń z systemem Windows 10 (komputera z systemem Windows, tablety z systemem Windows, Windows Phone, Xbox)** |PlayReady |MS krawędzi/IE11/EME<br/><br/><br/>Platforma uniwersalna systemu Windows |DASH (HLS, PlayReady nie jest obsługiwane)<br/><br/>DASH, Smooth Streaming (HLS, PlayReady nie jest obsługiwane) |
| **Urządzenia z systemem android (telefonu, tablecie, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), OS X, klientów i Apple TV** |FairPlay |Safari 8 +/ EME |HLS |


Biorąc pod uwagę bieżący stan wdrożenia dla każdego DRM usługa zwykle chce zaimplementować dwóch lub trzech DRMs, aby upewnić się, że Rozwiąż wszystkie typy punktów końcowych w najlepszy sposób.

Brak zależności od złożoności logiki usługi i po stronie klienta poziom środowisko użytkownika w różnych klientów.

Aby dokonać wyboru, należy pamiętać:

* PlayReady natywnie jest zaimplementowana w każde urządzenie z systemem Windows, na niektórych urządzeniach z systemem Android i dostępne za pośrednictwem zestawów SDK oprogramowania w praktycznie dowolnej platformie.
* Widevine zaimplementowano natywnie każde urządzenie z systemem Android, Chrome i niektórych innych urządzeń.
* FairPlay jest dostępna tylko w systemach iOS i Mac OS klientów lub za pomocą programu iTunes.

Dostępne są dwie opcje dla typowych wieloma DRM:

* PlayReady i Widevine
* PlayReady i Widevine, FairPlay

## <a name="a-reference-design"></a>Odwołanie do projektu
W tej sekcji przedstawiono odwołanie do projektu, który jest niezależny od technologie używane do implementowania go.

Podsystem DRM może zawierać następujące składniki:

* Zarządzanie kluczami
* OPAKOWYWANIE DRM
* Dostarczanie licencji DRM
* Sprawdź uprawnienia
* Uwierzytelniania/autoryzacji
* Odtwarzacz
* Sieci dostarczania pochodzenia/zawartości (CDN)

Na poniższym diagramie przedstawiono ogólny interakcji między składnikami w podsystemie DRM:

![Podsystem DRM z CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Projekt zawiera trzy warstwy podstawowej:

* Warstwa zaplecza (czarny) nie jest uwidaczniana zewnętrznie.
* Warstwa sieci obwodowej (ciemny niebieski) zawiera wszystkie punkty końcowe, które stają przed publicznego.
* Warstwę publiczny internet (niebieski światła) zawiera CDN i odtwarzacze z ruchem w publicznej sieci internet.

Również powinien być narzędziem do zarządzania zawartością do kontroli ochrony DRM, niezależnie od tego, czy jest statyczny lub dynamiczny szyfrowania. Dane wejściowe do szyfrowania DRM obejmują:

* Zawartość wideo MBR
* Klucz zawartości
* Adresy URL pozyskiwania licencji

Poniżej przedstawiono ogólny przepływ podczas odtwarzania:

* Użytkownik jest uwierzytelniony.
* Token autoryzacji jest tworzony dla użytkownika.
* Zawartość chroniona DRM (manifest) zostanie pobrana do odtwarzacza.
* Odtwarzacz przesyła żądanie nabycie licencji serwerów licencji wraz z kluczem identyfikator i token autoryzacji.

W poniższej sekcji omówiono projektu zarządzania kluczami.

| **ContentKey do zasobów** | **Scenariusz** |
| --- | --- |
| 1-do-1 |Najprostszym przypadku. Zapewnia on Najdrobniejsze formantu. Jednak to rozmieszczenie powoduje ogólnie najwyższy koszt dostarczania licencji. Co najmniej jedno żądanie licencji jest wymagana dla każdego chronionego zasobu. |
| 1-do wielu |Wiele zasobów, można użyć tego samego klucza zawartości. Na przykład dla wszystkich zasobów w grupę logiczną, takie jak genre lub podzbiór genre (lub gen film), używając jednego klucza zawartości. |
| Wiele do-1 |Wiele kluczy zawartości są wymagane dla każdego zasobu. <br/><br/>Na przykład jeśli chcesz zastosować dynamicznej ochrony CENC z wieloma DRM dla protokołu MPEG DASH i dynamicznego szyfrowania AES-128 dla protokołu HLS, należy dwa osobne klucze zawartości. Każdy klucz zawartości musi mieć własny ContentKeyType. (Dla klucza zawartości używana na potrzeby dynamicznej CENC ochrony, użyj ContentKeyType.CommonEncryption. Dla zawartości klucz służący do dynamicznego szyfrowania AES-128 użyć ContentKeyType.EnvelopeEncryption).<br/><br/>Inny przykład CENC ochrony zawartości kreska, teoretycznie służy jeden klucz zawartości do ochrony strumienia wideo i innej zawartości klawisz, aby chronić strumieniem audio. |
| Wiele do wielu |Kombinacja poprzednich dwóch scenariuszy. Jeden zestaw kluczy zawartości jest używane dla każdego z wielu zasobów w tej samej grupie zasobów. |

Innym ważnym czynnikiem do przeanalizowania jest użycie stałe i nietrwałe licencji.

Te zagadnienia są ważne

Użycie chmury publicznej w celu dostarczania licencji, licencje stałe i nietrwałe ma bezpośredni wpływ na koszty dostarczania licencji. Służą następujących przypadków projektowania w celu zilustrowania:

* Miesięczna subskrypcja: Użyj trwałego licencji i 1-do wielu zawartości mapowanie klucz do zasobów. Na przykład dla wszystkich dzieci filmów używamy jednego klucza zawartości do szyfrowania. W takim przypadku:

    Całkowita liczba licencji żądany dla dzieci wszystkie filmy lub urządzeniu = 1

* Miesięczna subskrypcja: Użyj nietrwałych licencji i 1-do-1 mapowanie między klucz zawartości i zasobów. W takim przypadku:

    Całkowita liczba licencji żądany dla dzieci wszystkie filmy lub urządzeniu = [liczba filmy obserwowane] x [liczba sesji]

Dwóch różnych projektów spowodować licencji bardzo różnych wzorców żądania. Powoduje różnych wzorców dostarczania licencji różnych kosztów w przypadku usługi dostarczania licencji przez chmury publicznej, takich jak usługi Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Mapowanie projektu do technologii dla implementacji
Następnie ogólnego projektu jest zamapowany na technologii na platformie Azure/Media Services, określając technologii dla każdego bloku konstrukcyjnego.

W poniższej tabeli przedstawiono mapowania.

| **Bloków konstrukcyjnych** | **Technologia** |
| --- | --- |
| **Player** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Dostawcy tożsamości (IDP)** |Azure Active Directory (Azure AD) |
| **Usługa tokenu zabezpieczającego (STS)** |Azure AD |
| **Przepływ pracy ochrony DRM** |Dynamiczne ochrony usługi Media Services |
| **Dostarczanie licencji DRM** |* Media Services licencji dostarczania (PlayReady i Widevine, FairPlay) <br/>* Axinom serwera licencji <br/>* Niestandardowego serwera licencji PlayReady |
| **Punkt początkowy** |Punkt końcowy przesyłania strumieniowego usługi Media Services |
| **Zarządzanie kluczami** |Nie jest wymagane dla implementacji |
| **Zarządzanie zawartością** |Aplikacji konsolowej C# |

Innymi słowy zarówno dostawców tożsamości i usługi STS są używane z usługą Azure AD. [Interfejsu API usługi Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/) służy do odtwarzacza. Zarówno usługi Media Services, jak i Media Player obsługuje kreska i CENC z wieloma DRM.

Poniższy diagram przedstawia ogólną strukturę i przepływ z poprzednich mapowania technologii:

![CENC na usługi Media Services](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Aby skonfigurować szyfrowania dynamicznego CENC, narzędzia do zarządzania zawartością używa następujące dane wejściowe:

* Otwórz zawartość
* Klucz zawartości z klucza generowania i zarządzania nimi.
* Adresy URL pozyskiwania licencji
* Lista informacji z usługi Azure AD

Poniżej przedstawiono dane wyjściowe narzędzia zarządzania zawartością:

* ContentKeyAuthorizationPolicy zawiera specyfikację, w jaki sposób dostarczania licencji sprawdza JSON tokenów sieci Web (JWT) i specyfikacje licencji DRM.
* AssetDeliveryPolicy zawiera specyfikacje dotyczące przesyłania strumieniowego format, ochrony DRM i adresy URL pozyskiwania licencji.

Poniżej przedstawiono przepływ w czasie wykonywania:

* Podczas uwierzytelniania użytkownika zostanie wygenerowany token JWT.
* Jednym z oświadczeń zawartych w tokenu JWT jest oświadczenia grupy zawierającej obiekt EntitledUserGroup Identyfikatora grupy. To oświadczenie używany do przekazywania sprawdzenia uprawnień.
* Odtwarzacz pobiera manifest klienta z zawartości chronionej CENC i identyfikuje następujące czynności:
   * Identyfikator klucza.
   * Zawartość jest chroniona CENC.
   * Adresy URL pozyskiwania licencji.
* Odtwarzacz wysyła żądanie nabycie licencji oparte na przeglądarce DRM obsługiwane. W licencji nabycia żądanie, klucz również przesłane identyfikator i tokenu JWT. Usługi dostarczania licencji sprawdza tokenu JWT i oświadczeń zawartych przed wystawia potrzebnych licencji.

## <a name="implementation"></a>Wdrażanie
### <a name="implementation-procedures"></a>Procedury implementacji
Implementacja obejmuje następujące kroki:

1. Przygotowanie zasobów testu. Kodowanie/package testu wideo do wielokrotnej szybkości transmisji bitów pofragmentowany plik MP4 w usłudze Media Services. Ten zasób jest *nie* DRM chronione. Ochronę DRM odbywa się przez dynamiczne ochrony później.

2. Utwórz klucz identyfikator i klucz zawartości (opcjonalnie z inicjatora klucza). W tym wystąpieniu systemem zarządzania kluczami nie jest potrzebna, ponieważ tylko jeden klucz wymagany z kilku zasobów testu są identyfikator i klucz zawartości.

3. Umożliwia skonfigurowanie usług dostarczania licencji multi DRM trwałego testu Media Services API. Jeśli serwery niestandardowych licencji przez firmę lub dostawców firmy zamiast licencji usługi Media Services, możesz pominąć ten krok. Można określić adresy URL pozyskiwania licencji w kroku podczas konfigurowania dostarczania licencji. Interfejs API usług Media jest potrzebne do określania niektóre szczegółowe konfiguracje, takie jak szablony odpowiedzi licencji dla różnych usług licencji DRM i ograniczenia zasad autoryzacji. W tej chwili portalu Azure nie udostępnia wymaganych interfejsu użytkownika dla tej konfiguracji. Aby uzyskać poziom interfejsu API i przykładowy kod, zobacz [Użyj PlayReady i Widevine dynamicznego szyfrowania common encryption](media-services-protect-with-playready-widevine.md).

4. Skonfiguruj zasady dostarczania zasobu dla zasobu testu za pomocą interfejsu API usługi multimediów. Aby uzyskać poziom interfejsu API i przykładowy kod, zobacz [Użyj PlayReady i Widevine dynamicznego szyfrowania common encryption](media-services-protect-with-playready-widevine.md).

5. Tworzenie i konfigurowanie dzierżawa usługi Azure AD na platformie Azure.

6. Należy utworzyć kilka grup i kont użytkowników w dzierżawie usługi Azure AD. Utwórz co najmniej grupę "Prawo użytkownika" i dodać użytkownika do tej grupy. Użytkownicy w tej grupie przekazać wyboru uprawnień nabycie licencji. Użytkownicy nie znajduje się w tej grupie nie można przekazać sprawdzenie uwierzytelniania i nie można uzyskać licencji. Członkostwo w tej grupie "Prawo użytkownika" to oświadczenie wymaganych grup w JWT wystawione przez usługę Azure AD. To wymaganie oświadczeń określa się w kroku podczas konfigurowania usług dostarczania licencji DRM wielu.

7. Tworzenie aplikacji platformy ASP.NET MVC do hostowania odtwarzacza wideo. Ta aplikacja ASP.NET jest chroniony za pomocą uwierzytelniania użytkownika względem dzierżawy usługi Azure AD. Odpowiednie oświadczenia są uwzględniane w tokenów dostępu uzyskane po uwierzytelnieniu użytkownika. Firma Microsoft zaleca interfejsu API OpenID Connect w tym kroku. Zainstaluj następujące pakiety NuGet:

   * Pakiet instalacyjny Microsoft.Azure.ActiveDirectory.GraphClient
   * Pakiet instalacyjny Microsoft.Owin.Security.OpenIdConnect
   * Pakiet instalacyjny Microsoft.Owin.Security.Cookies
   * Instalacja pakietu Microsoft.Owin.Host.SystemWeb
   * Pakiet instalacyjny Microsoft.IdentityModel.Clients.ActiveDirectory

8. Tworzenie przy użyciu odtwarzacza [interfejsu API usługi Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Użyj [interfejsu API usługi Azure Media Player ProtectionInfo](http://amp.azure.net/libs/amp/latest/docs/) do określenia technologii DRM na różnych platformach DRM.

9. W poniższej tabeli przedstawiono macierzy testów.

    | **DRM** | **Przeglądarka** | **Wynik dla prawo użytkownika** | **Wynik dla unentitled użytkownika** |
    | --- | --- | --- | --- |
    | **PlayReady** |Przeglądarka Microsoft Edge lub program Internet Explorer 11 w systemie Windows 10 |Zakończ pomyślnie |Niepowodzenie |
    | **Widevine** |Chrome w systemie Windows 10 |Zakończ pomyślnie |Niepowodzenie |
    | **FairPlay** |TBD | | |

Aby uzyskać informacje na temat sposobu konfigurowania usługi Azure AD dla aplikacji ASP.NET MVC player, zobacz [integracji aplikacji usługi Azure Media Services OWIN MVC z usługą Azure Active Directory i ograniczenie klucza dostarczania zawartości na podstawie oświadczeń JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Aby uzyskać więcej informacji, zobacz [JWT token uwierzytelniania w usłudze Azure Media Services i szyfrowania dynamicznego](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Aby uzyskać informacje dotyczące usługi Azure AD:

* Możesz znaleźć informacje dla deweloperów w [przewodnik dewelopera usługi Azure Active Directory](../active-directory/active-directory-developers-guide.md).
* Można znaleźć informacje o Administratorze w [administrowanie katalogiem dzierżawy usługi Azure AD](../active-directory/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Niektóre problemy w implementacji
Skorzystaj z poniższych informacji dotyczące rozwiązywania problemów, aby uzyskać pomoc dotyczącą problemów z implementacją.

* Wystawca adres URL musi kończyć się "/". Odbiorców musi być identyfikatorem player aplikacji klienta. Ponadto Dodaj "/" na końcu adresu URL wystawcy.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    W [dekodera tokenów JWT](http://jwt.calebb.net/), zostanie wyświetlony **lub** i **iss**, jak pokazano w tokenu JWT:

    ![TOKEN JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Dodaj uprawnienia do aplikacji w usłudze Azure AD na **Konfiguruj** aplikacji. Uprawnienia są wymagane dla każdej aplikacji lokalnych i wdrożonej wersji.

    ![Uprawnienia](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* Użyj poprawne wystawcy, podczas konfigurowania ochrony CENC dynamicznych.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Nie działa następujące czynności:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    Identyfikator GUID jest identyfikatorem dzierżawy usługi Azure AD Identyfikator GUID można znaleźć w **punkty końcowe** menu podręcznego w portalu Azure.

* Członkostwo w grupie GRANT oświadczeń uprawnień. Upewnij się, że poniżej znajduje się w pliku manifestu aplikacji usługi Azure AD: 

    "groupMembershipClaims": "All" (wartość domyślna to null)

* Prawidłowego obiektu TokenType należy ustawić podczas tworzenia wymagania ograniczeń.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Ponieważ można dodać obsługę JWT (Azure AD) oprócz SWT (ACS), domyślnie TokenType jest TokenType.JWT. Jeśli używasz SWT/ACS, należy ustawić token do TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Dodatkowe tematy dotyczące implementacji
W tej sekcji omówiono niektóre dodatkowe tematy w projektowania i implementacji.

### <a name="http-or-https"></a>HTTP lub HTTPS?
Aplikacja odtwarzacza ASP.NET MVC musi spełniać następujące wymagania:

* Uwierzytelnianie użytkowników za pomocą usługi Azure AD, która znajduje się w HTTPS.
* Token JWT wymiany między klientem a usługą Azure AD, która znajduje się w HTTPS.
* Nabycie licencji DRM przez klienta, który musi być w obszarze HTTPS, jeśli jest dostępna dostarczania licencji Media Services. Pakiet produktów PlayReady nie wprowadzić HTTPS w celu dostarczania licencji. Jeśli serwer licencji PlayReady znajduje się poza Media Services, można użyć protokołu HTTP lub HTTPS.

Aplikacja odtwarzacza ASP.NET używa protokołu HTTPS najlepszym rozwiązaniem, Media Player znajduje się na stronie w obszarze HTTPS. Jednak HTTP jest preferowana do przesyłania strumieniowego, więc należy wziąć pod uwagę problem zawartości mieszanej.

* Przeglądarka nie zezwala na zawartość mieszana. A wtyczek takich jak Silverlight i wtyczki dla OSMF smooth kreska. Zawartość mieszana jest problem dotyczący zabezpieczeń ze względu na zagrożenie możliwość wprowadzenia złośliwego JavaScript, co może powodować danych klienta zagrożenie. Przeglądarki blokować ta funkcja domyślnie. Jedynym sposobem na jego obejścia jest po stronie serwera (źródło), zezwalając wszystkich domenach (niezależnie od HTTP lub HTTPS). To prawdopodobnie nie jest dobrze albo.
* Unikaj zawartości mieszanej. Zarówno aplikacja odtwarzacza, jak i Media Player należy korzystać z protokołu HTTP lub HTTPS. Podczas odtwarzania zawartości mieszanej w nim silverlightSS wymaga wyczyszczenia ostrzeżenie zawartością mieszaną. Techniczna flashSS obsługuje zawartość mieszaną bez ostrzeżenia zawartością mieszaną.
* Jeśli punkt końcowy przesyłania strumieniowego został utworzony przed 2014 sierpnia, nie obsługuje protokołu HTTPS. W takim przypadku należy utworzyć i używać nowego punktu końcowego przesyłania strumieniowego dla protokołu HTTPS.

W implementacji odwołania dla zawartości chronionej przez DRM, aplikacji i przesyłania strumieniowego są w obszarze HTTPS. Dla zawartości Otwórz odtwarzacz nie wymaga uwierzytelniania lub licencji, aby można było używać protokołu HTTP lub HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Usługa Azure Active Directory Przerzucanie klucza podpisywania
Przerzucanie klucza podpisywania jest należy wziąć pod uwagę w implementacji. Jeśli zostanie zignorowany, Zakończono systemu ostatecznie całego całkowicie, w ciągu sześciu tygodni najbardziej.

Usługi Azure AD używa standardy branżowe, aby ustanowić zaufanie między sobą i aplikacji, które używają usługi Azure AD. W szczególności usługi Azure AD używa klucza podpisywania, który składa się z pary kluczy publicznych i prywatnych. Usługi Azure AD tworzy token zabezpieczający, który zawiera informacje o użytkowniku, były podpisane przez usługę Azure AD z kluczem prywatnym przed wysłaniem do aplikacji. Aby sprawdzić, czy token jest prawidłowy i zdalnych z usługi Azure AD, aplikacja musi zweryfikować podpisu tokenu. Aplikacja używa klucza publicznego udostępnianych przez usługi Azure AD, zawarte w dokument metadanych usług federacyjnych dzierżawcy. Ten klucz publiczny i klucz podpisujący, z którego pochodzi, jest taka sama jak używane dla wszystkich dzierżawców w usłudze Azure AD.

Aby uzyskać więcej informacji na Przerzucanie klucza usługi Azure AD, zobacz [ważnych informacji na temat podpisywania przerzucania kluczy w usłudze Azure AD](../active-directory/active-directory-signing-key-rollover.md).

Między [pary kluczy publiczno prywatnych](https://login.microsoftonline.com/common/discovery/keys/):

* Klucz prywatny jest używany przez usługę Azure AD można wygenerować token JWT.
* Klucz publiczny jest używany przez aplikację, takie jak usługi dostarczania licencji DRM w usłudze Media Services można zweryfikować tokenu JWT.

Ze względów bezpieczeństwa usługi Azure AD obraca okresowo certyfikatu (co sześć tygodni). W przypadku naruszenia zabezpieczeń Przerzucanie klucza może wystąpić dowolnej chwili. W związku z tym usług dostarczania licencji Media Services wymaga zaktualizowania klucza publicznego używany jako pary kluczy obraca usługi Azure AD. W przeciwnym razie token uwierzytelniania w usłudze Media Services nie powiedzie się i wystawieniu żadna licencja.

Aby skonfigurować tę usługę, należy ustawić TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument podczas konfigurowania usług dostarczania licencji DRM.

Poniżej przedstawiono przepływ JWT:

* Usługa Azure AD wystawia tokenu JWT z bieżącego klucza prywatnego dla użytkowników uwierzytelnionych.
* Gdy odtwarzacza widzi CENC z wieloma DRM chroniona zawartość, najpierw lokalizuje JWT wystawione przez usługę Azure AD.
* Odtwarzacz wysyła żądanie nabycie licencji z tokenu JWT do usług dostarczania licencji Media Services.
* Do usług dostarczania licencji Media Services Użyj bieżącego/prawidłowy klucz publiczny z usługi Azure AD, aby sprawdzić tokenu JWT przed wystawieniem licencji.

Usług dostarczania licencji DRM zawsze sprawdzić bieżące/prawidłowy klucz publiczny z usługi Azure AD. Klucz publiczny przedstawiony przez usługę Azure AD jest klucz używany do weryfikowania token JWT wystawione przez usługę Azure AD.

Co zrobić, jeśli Przerzucanie klucza się stanie po usługi Azure AD generuje token JWT, ale przed wysłaniem przez graczy tokenu JWT do usług dostarczania licencji DRM w usłudze Media Services na potrzeby weryfikacji?

Ponieważ klucz może przerzuceniem w dowolnym momencie, więcej niż jeden prawidłowy klucz publiczny jest zawsze dostępna w dokumencie metadanych federacji. Dostarczania licencji Media Services można użyć dowolnego z określonych w dokumencie kluczy. Ponieważ jeden klucz może być wkrótce wycofana, inny może być zastąpienia i tak dalej.

### <a name="where-is-the-access-token"></a>Gdzie znajduje się token dostępu?
Jeśli przyjrzymy się jak aplikacji sieci web wywołuje aplikację interfejsu API w obszarze [tożsamość aplikacji z przyznania poświadczeń klienta OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md#web-application-to-web-api), przepływ uwierzytelniania wygląda następująco:

* Gdy użytkownik loguje się do usługi Azure AD w aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [przeglądarki sieci Web do aplikacji sieci web](../active-directory/develop/active-directory-authentication-scenarios.md#web-browser-to-web-application).
* Punkt końcowy autoryzacji usługi Azure AD przekierowuje agenta użytkownika do aplikacji klienckiej z kodem autoryzacji. Agent użytkownika zwraca kod autoryzacji identyfikator URI przekierowania aplikacji klienckiej.
* Aplikacja sieci web musi uzyskać token dostępu, który można uwierzytelniać się na interfejsie API sieci web i pobrać żądanego zasobu. Wysyła żądanie do punktu końcowego tokenu usługi Azure AD, a zawiera poświadczenia, identyfikator klienta i aplikacji sieci web interfejsu API identyfikator URI. Stanowi kod autoryzacji, aby udowodnić, że użytkownik zgodę.
* Usługi Azure AD uwierzytelnia aplikacji i zwraca token dostępu JWT, który służy do wywołania interfejsu API sieci web.
* Przy użyciu protokołu HTTPS aplikacji sieci web używa zwróconych tokenu dostępu JWT Aby dodać ciąg tokenu JWT z oznaczeniem "Bearer" w nagłówku żądania "Autoryzacji" do interfejsu API sieci web. Interfejs API sieci web następnie sprawdza poprawność tokenu JWT. Jeśli weryfikacja zakończy się pomyślnie, zwraca żądanego zasobu.

W tym przepływie tożsamości aplikacji interfejsu API sieci web ufa, czy aplikacja sieci web uwierzytelniony użytkownik. Z tego powodu ten wzorzec jest wywoływana zaufany podsystem. [Diagram przepływu autoryzacji](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) w tym artykule opisano sposób-kod udzielania autoryzacji przepływ działania.

Nabycie licencji z ograniczenia tokenu zgodny ze wzorcem zaufany podsystem tego samego. Usługi dostarczania licencji w usłudze Media Services jest zasobu interfejsu API sieci web lub "zaplecza zasobu" wymagająca dostępu do aplikacji sieci web. Gdzie jest to token dostępu?

Token dostępu są uzyskiwane z usługi Azure AD. Po uwierzytelnieniu użytkownik pomyślnie zostanie zwrócony kod autoryzacji. Kod autoryzacji jest następnie używany, wraz z Identyfikatorem klienta i klucz aplikacji do programu exchange dla tokenu dostępu. Token dostępu umożliwia dostęp do aplikacji "wskaźnik", który wskazuje lub reprezentuje usługi dostarczania licencji Media Services.

Aby zarejestrować i skonfigurować aplikację wskaźnika w usłudze Azure AD, należy wykonać następujące czynności:

1. W dzierżawie usługi Azure AD:

   * Dodawanie aplikacji (zasobów) z https://[resource_name].azurewebsites.net/ adres URL logowania jednokrotnego. 
   * Dodaj identyfikator aplikacji z https://[aad_tenant_name].onmicrosoft.com/[resource_name adresu URL].

2. Dodaj nowy klucz dla zasobów aplikacji.

3. Aktualizacja pliku manifestu aplikacji tak, aby właściwość groupMembershipClaims ma wartość "groupMembershipClaims": "All".

4. W aplikacji usługi Azure AD, która wskazuje player aplikacji sieci web, w sekcji **uprawnień dotyczących innych aplikacji**, dodaj aplikację zasobu, który został dodany w kroku 1. W obszarze **delegowane uprawnienia**, wybierz pozycję **dostępu [resource_name]**. Ta opcja zapewnia uprawnienia aplikacji sieci web do utworzenia tokenów dostępu, które uzyskują dostęp do zasobów aplikacji. W tym lokalnych i wdrożonej wersji aplikacji sieci web tworzonych przy użyciu programu Visual Studio i aplikacji sieci web platformy Azure.

JWT wystawione przez usługę Azure AD jest tokenu dostępu, umożliwiają dostęp do zasobów wskaźnika.

### <a name="what-about-live-streaming"></a>Jak transmisja strumieniowa na żywo?
Poprzedniej dyskusji koncentruje się na zasoby na żądanie. Jak transmisja strumieniowa na żywo?

Dokładnie tego samego projekt i implementację służy do ochrony transmisji strumieniowych na żywo w usłudze Media Services traktując zasób skojarzony z programem jako zasób VOD.

W szczególności na żywo przesyłania strumieniowego w usłudze Media Services, należy utworzyć kanał, a następnie utwórz program w kanale. Aby utworzyć program, należy utworzyć element zawartości zawierający na żywo archiwum dla programu. Aby zapewnić CENC z wieloma DRM ochrony zawartości na żywo, dotyczą tego samego ustawienia/przetwarzania zasobu tak, jakby była zasobów VOD przed uruchomieniem programu.

### <a name="what-about-license-servers-outside-media-services"></a>Co serwerów licencji poza Media Services?
Często klienci zainwestowały w farmie serwerów licencji jest w centrum danych lub jeden z obsługiwanych przez dostawców usług DRM. Z nośnika usługi ochrony zawartości może działać w trybie hybrydowego. Zawartość można obsługiwana i dynamicznie chroniony w usłudze Media Services podczas licencji DRM są dostarczane przez serwery poza Media Services. W takim przypadku należy wziąć pod uwagę następujące zmiany:

* Usługi STS musi wystawiać tokeny są dopuszczalne, które można sprawdzić w farmie serwerów licencji. Na przykład serwerów licencji Widevine, które są udostępniane przez Axinom wymagają określonej tokenu JWT z komunikatem uprawnienia. W związku z tym musisz mieć tokenu Zabezpieczającego wystawianie tokenów JWT. Aby uzyskać informacje na ten typ wdrożenia, przejdź do [Centrum dokumentacji platformy Azure](https://azure.microsoft.com/documentation/) i zobacz [Axinom używany na potrzeby dostarczania licencji Widevine do usługi Azure Media Services](media-services-axinom-integration.md).
* Nie trzeba skonfigurować usługi dostarczania licencji (ContentKeyAuthorizationPolicy) w usłudze Media Services. Należy podać adresy URL pozyskiwania licencji (w przypadku PlayReady i Widevine, FairPlay) podczas konfigurowania AssetDeliveryPolicy, aby skonfigurować CENC z wieloma DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Co zrobić, jeśli ma być użyty niestandardowy STS?
Klienta można użyć niestandardowej STS zapewnienie tokenów Jwt. Przyczyny:

* IDP używanych przez klienta nie obsługuje usługi STS. W takim przypadku niestandardowych STS może być opcję.
* Klient może być konieczne bardziej elastyczne lub zwiększenie poziomu kontroli usługi STS jest integrowana z systemem rozliczeniowym subskrybenta klienta. Na przykład MVPD operator może oferują wiele pakietów subskrybenta OTT, takich jak premium i podstawowa i sportowych. Operator może być odpowiadają oświadczenia do tokenu z pakietem subskrybenta, dzięki czemu są udostępniane tylko zawartości określonego pakietu. W takim przypadku niestandardowych STS zawiera wymagane elastyczność i kontrolę.

Korzystając z STS niestandardowe, należy dwie zmiany:

* Podczas konfigurowania usługi dostarczania licencji dla zasobu, należy określić klucz zabezpieczeń używany podczas weryfikacji przez usługę STS niestandardowych zamiast bieżącego klucza z usługi Azure AD. (Więcej szczegółów wykonaj). 
* Po wygenerowaniu tokenu JTW klucza zabezpieczeń określono zamiast X509 bieżący klucz prywatny certyfikatu w usłudze Azure AD.

Istnieją dwa typy kluczy zabezpieczeń:

* Klucz symetryczny: ten sam klucz służy do generowania i weryfikować token JWT.
* Klucza asymetrycznego: pary kluczy publiczno prywatnych X509 jest używany certyfikat z kluczem prywatnym do szyfrowania/wygenerować token JWT i przy użyciu klucza publicznego do zweryfikowania tokenu.

> [!NOTE]
> Jeśli używasz .NET Framework / C# jako platformy programowanie X509 certyfikat używany dla klucza asymetrycznego zabezpieczeń musi mieć klucz o długości co najmniej 2048. Jest to wymagane klasy System.IdentityModel.Tokens.X509AsymmetricSecurityKey w programie .NET Framework. W przeciwnym razie jest zwracany następujący wyjątek:

> IDX10630: "System.IdentityModel.Tokens.X509AsymmetricSecurityKey" do podpisywania nie może być mniejsza niż "2048" bitów.

## <a name="the-completed-system-and-test"></a>Ukończono systemu i testu
Ta sekcja przeprowadzi Cię w następujących scenariuszach w systemie end-to-end ukończone, dzięki czemu może mieć obrazu podstawowego zachowania, zanim będzie można pobrać konto logowania:

* Jeśli potrzebujesz — zintegrowane scenariusza:

    * Zasobów wideo hostowanych w usłudze Media Services, które są albo niechronionej lub chronione DRM, ale bez tokenu uwierzytelniania (wydawania licencji kto go żądane), można przetestować go bez rejestrowania się w. Przełącz się do protokołu HTTP, jeśli Twoje strumieniowe przesyłanie wideo za pośrednictwem protokołu HTTP.

* Jeśli potrzebujesz scenariusz zintegrowane end-to-end:

    * Zasobów wideo w dynamicznej ochrony DRM w usłudze Media Services z tokenu uwierzytelniania i JWT generowane przez usługę Azure AD należy zalogować.

Dla aplikacji sieci web player i jego logowania, zobacz [tej witryny sieci Web](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Logowanie użytkowników
Aby przetestować end-to-end zintegrowanego systemu DRM, należy mieć konto utworzone lub dodane.

Jakiego konta?

Mimo że Azure pierwotnie zezwalała na dostęp tylko przez użytkowników kont Microsoft, jest teraz dostęp przez użytkowników z tymi dwoma systemami. Wszystkie właściwości Azure teraz zaufania usługi Azure AD na potrzeby uwierzytelniania, a usługa Azure AD uwierzytelnia użytkowników w organizacji. Relacja Federacji został utworzony, gdy usługa Azure AD ufa konsumenta tożsamości system obsługi kont Microsoft na potrzeby uwierzytelniania konsumentów. W związku z tym usługi Azure AD może uwierzytelniać gościa Microsoft kont również jako natywne usługi Azure AD.

Ponieważ usługa Azure AD ufa domeny konta Microsoft, można dodać wszystkich kont z dowolnego z następujących domen do niestandardowych usługi Azure AD dzierżawy i używane do logowania konta:

| **Nazwa domeny** | **Domeny** |
| --- | --- |
| **Domena dzierżawy niestandardowych usługi Azure AD** |somename.onmicrosoft.com |
| **Domeny firmowej** |Microsoft.com |
| **Domena konta Microsoft** |Outlook.com, live.com, hotmail.com |

Możesz skontaktować się żadnego autorów konto utworzone lub dodane.

Poniższe zrzuty ekranu pokazują innej strony logowania, używany przez inną domenę konta:

**Konto domeny dzierżawy niestandardowych usługi Azure AD**: niestandardowe strony logowania niestandardowych usługi Azure AD dzierżawy domeny.

![Konto domeny dzierżawcy niestandardowe usługi Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Konto domeny firmy Microsoft z kartami inteligentnymi**: strona logowania dostosowane przez firmę Microsoft firmowej IT z uwierzytelniania dwuskładnikowego.

![Konto domeny dzierżawcy niestandardowe usługi Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Konto Microsoft**: strony logowania konta Microsoft konsumenta.

![Konto domeny dzierżawcy niestandardowe usługi Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Za pomocą rozszerzeń zaszyfrowanych nośnika dla PlayReady
PlayReady jest podstawowym DRM dla EME na nowoczesne przeglądarki z rozszerzeń szyfrowane nośnika (EME) PlayReady pomocy technicznej, takich jak program Internet Explorer 11 w systemie Windows 8.1 lub nowszym i przeglądarki Microsoft Edge w systemie Windows 10.

![Na użytek EME PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Obszar ciemny player jest ponieważ PlayReady ochrony uniemożliwia wprowadzanie przechwytywania ekranu chronionego wideo.

Poniższy zrzut ekranu przedstawia player dodatków plug-in i Microsoft Security Essentials (MSE) / EME obsługuje:

![Wtyczki Player PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

Umożliwia EME Microsoft Edge i programu Internet Explorer 11 w systemie Windows 10 [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) wywoływanej na obsługujących je urządzeniach z systemem Windows 10. Przepływ zawartości rozszerzone premium (4K, HDR) i nowe odblokowuje PlayReady SL3000 zawartości modelu dostarczania (na potrzeby rozszerzonego zawartości).

Aby skoncentrować się na urządzeniach z systemem Windows, PlayReady jest tylko DRM sprzętu dostępnej na urządzeniach z systemem Windows (PlayReady SL3000). Usługi przesyłania strumieniowego za pomocą PlayReady za pośrednictwem EME lub aplikacji platformy uniwersalnej systemu Windows i oferują wyższej jakości wideo za pomocą PlayReady SL3000 niż innym DRM. Zwykle maksymalnie 2 K przepływ zawartości za pośrednictwem przeglądarki Chrome lub Firefox i zawartości się z przepływem 4 K za pomocą programu Microsoft Edge/Internet Explorer 11 lub aplikacji platformy uniwersalnej systemu Windows, w tym samym urządzeniu. Ilość zależy od implementacji i ustawień usługi.

#### <a name="use-eme-for-widevine"></a>Na użytek EME Widevine
W przypadku nowoczesne przeglądarki z obsługą EME/Widevine, takie jak Chrome 41 + w systemie Windows 10, Windows 8.1, Mac OS x Yosemite i Chrome na Android 4.4.4 Google Widevine jest DRM za EME.

![Na użytek EME Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine nie uniemożliwić wprowadzanie przechwytywania ekranu chronionego wideo.

![Wtyczki Player Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Unentitled użytkowników
Jeśli użytkownik nie jest członkiem grupy "Użytkownicy pod tytułem", użytkownik nie przeszło sprawdzania uprawnień. Następnie usługa licencji DRM wielu odmawia wystawić żądanego licencji, jak pokazano. Szczegółowy opis jest "uzyskania licencji nie powiodło się," który jest zgodnie z założeniami.

![Unentitled użytkowników](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Uruchom usługę tokenu zabezpieczającego niestandardowych
Po uruchomieniu niestandardowych STS tokenu JWT jest wystawiany przez STS niestandardowych za pomocą symetrycznego lub klucza asymetrycznego.

Poniższy zrzut ekranu przedstawia scenariusz, który używa klucza symetrycznego (za pomocą przeglądarki Chrome):

![Niestandardowe STS za pomocą klucza symetrycznego](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Poniższy zrzut ekranu przedstawia scenariusz, który używa klucza asymetrycznego za pośrednictwem X509 certyfikatu (przy użyciu przeglądarki nowoczesnych firmy Microsoft):

![Niestandardowe STS przy użyciu klucza asymetrycznego](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

W obu przypadkach poprzedniej uwierzytelnianie użytkownika pozostaje taki sam. Go odbywa się za pośrednictwem usługi Azure AD. Jedyna różnica polega na tym, że Jwt są wydawane przez usługę STS niestandardowych zamiast usługi Azure AD. Podczas konfigurowania ochrony CENC dynamiczne ograniczenia usługi dostarczania licencji określa typ tokenu JWT symetrycznego lub klucza asymetrycznego.

## <a name="summary"></a>Podsumowanie
Ten dokument omówione CENC z wielu macierzystego formantu DRM i dostęp za pośrednictwem tokenu uwierzytelniania, jego projekt i jego wdrożenie za pomocą usługi Azure Media Services i Media Player.

* Odwołanie do projektu został przedstawiony zawierający wszystkie niezbędne składniki w podsystemie DRM/CENC.
* Implementacja odwołania został przedstawiony na Azure Media Services i Media Player.
* Niektóre tematy, które są bezpośrednio związane z projekt i implementację również zostały omówione.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
