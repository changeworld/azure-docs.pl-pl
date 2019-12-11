---
title: Projekt systemu ochrony zawartości przy użyciu kontroli dostępu przy użyciu usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu licencjonowania, Microsoft Smooth Streaming klienta przenoszenie Kit.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.openlocfilehash: b0fec44a59bd70c6f1d0236861d93e81aaba033c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969449"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Projekt systemu ochrony zawartości przy użyciu kontroli dostępu przy użyciu usługi Azure Media Services 

## <a name="overview"></a>Przegląd

Projektowanie i tworzenie podsystemu zarządzania (prawami cyfrowymi DRM) prawami cyfrowymi Ott (OTT) lub online rozwiązań do przesyłania strumieniowego jest złożonym zadaniem. Operatory/online dostawców wideo zazwyczaj zlecają obsługę tego zadania wyspecjalizowane dostawców usług DRM. Celem tego dokumentu jest istnieje odwołanie do projektowania i implementacji podsystemu DRM end-to-end w OTT lub rozwiązanie do przesyłania strumieniowego online.

Docelowe czytelnicy dla tego dokumentu są inżynierów, którzy pracują w podsystemu DRM OTT lub online rozwiązania przesyłania strumieniowego / (wiele ekranów) lub czytelników, którzy są zainteresowani podsystemu DRM. Zakłada się, czy czytelnicy są zapoznać się z co najmniej jedną z technologii DRM na rynku, takich jak PlayReady, Widevine, FairPlay lub Adobe Access.

W tej dyskusji DRM możemy również obejmować szyfrowania common encryption (CENC) przy użyciu technologii multi-DRM. Główne trend w przemyśle OTT i przesyłania strumieniowego online jest CENC za pomocą wielu natywnych DRM na różnych platformach klienckich. Tego trendu jest przesunięcia od poprzedniego używany pojedynczy DRM i jego zestawu SDK klienta dla różnych platform klienta. Gdy używasz CENC przy użyciu wielu natywnych DRM PlayReady i Widevine są szyfrowane na [szyfrowania Common Encryption (CENC 23001-7 ISO/IEC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specyfikacji.

Zalety CENC przy użyciu technologii multi-DRM są jej:

* Zmniejsza koszty szyfrowania, ponieważ proces szyfrowania jednym jest używana do przeznaczonych dla różnych platform przy użyciu jego natywnych protokołów DRM.
* Zmniejsza koszt zarządzanie zasobami zaszyfrowane, ponieważ potrzebna jest tylko jednej kopii zaszyfrowanych zasoby.
* Eliminuje klienta DRM, koszt licencji, ponieważ native DRM client to zwykle wolnego miejsca na jego platformy natywnej.

Firma Microsoft jest aktywne promoter DASH i CENC wraz z niektórych odtwarzaczy głównych branży. Usługa Azure Media Services zapewniają obsługę DASH i CENC. Najnowszych ogłoszeniach znajduje się na następujących blogach:

*  [Powiadomienie o usługach dostarczania licencji Google Widevine w usłudze Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Usługa Azure Media Services dodaje Google Widevine tworzenie pakietów na potrzeby dostarczać strumień multi-DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>Cele tego artykułu

Cele w tym artykule są:

* Należy podać odwołanie do projektu podsystemu DRM, który używa CENC przy użyciu technologii multi-DRM.
* Należy podać implementacja referencyjna na platformie Azure/Media Services.
* Omówiono niektóre tematy projektowania i implementacji.

W artykule termin "multi-DRM" obejmuje następujące produkty:

* PlayReady firmy Microsoft
* Google Widevine
* Technologia FairPlay firmy Apple 

W poniższej tabeli podsumowano aplikacji natywnej platformy/środowiska macierzystego i przeglądarek obsługiwanych przez każdy DRM.

| **Platforma klienta** | **Natywna obsługa technologii DRM** | **Przeglądarka/aplikacji** | **Formatów przesyłania strumieniowego** |
| --- | --- | --- | --- |
| **Telewizorów typu Smart TV, operator odbiornikami, odbiornikami OTT** |PlayReady przede wszystkim i/lub Widevine i/lub inne |Linux, Opera, aparatu WebKit, inne |Różne formaty |
| **Urządzenia z systemem Windows 10 (Windows PC, tablety Windows, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Platforma uniwersalna systemu Windows |KRESKA (dla protokołu HLS, PlayReady nie jest obsługiwane)<br/><br/>DASH, Smooth Streaming (HLS, PlayReady nie jest obsługiwane) |
| **Urządzenia z systemem android (telefon, tablet, takich jak Telewizor)** |Widevine |Dla programu Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), OS X, klientów i Apple TV** |FairPlay |Safari 8 +/ EME |HLS |

Biorąc pod uwagę bieżący stan wdrożenia dla każdego DRM usługi zazwyczaj chce zaimplementować dwóch lub trzech protokołów DRM, aby upewnić się, że rozwiązać wszystkie typy punktów końcowych w najlepszy sposób.

Istnieje zależność między złożoność logiki usług i złożoności po stronie klienta do osiągnięcia poziomu środowisko użytkownika w różnych klientów.

Aby ułatwić wybór, należy pamiętać:

* PlayReady natywnie jest implementowane w każdym urządzeniu Windows na niektórych urządzeniach z systemem Android i dostępne za pośrednictwem zestawów SDK oprogramowania, na praktycznie dowolnej platformie.
* Widevine zaimplementowano natywnie każdego urządzenia z systemem Android, Chrome i inne urządzenia.
* Technologia FairPlay jest dostępny tylko w systemach iOS i Mac OS klientów lub za pomocą programu iTunes.

Dostępne są dwie opcje dla typowych multi-DRM:

* Technologie PlayReady i Widevine
* PlayReady, Widevine i FairPlay

## <a name="a-reference-design"></a>Projekt wzorcowy
W tej sekcji przedstawiono projekt odwołania, który jest niezależny od technologii używanych do jej wdrożenia.

Podsystemu DRM może zawierać następujące składniki:

* Zarządzanie kluczami
* Pakowanie DRM
* Dostarczanie licencji DRM
* Sprawdzanie uprawnień
* Uwierzytelnianie i autoryzacja
* Odtwarzacz
* Źródło/usługa content delivery network (CDN)

Na poniższym diagramie przedstawiono ogólne interakcji między składnikami w podsystemie DRM:

![CENC podsystemu DRM](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Projekt ma trzy warstwy podstawowa:

* Warstwę zaplecza (czarny) nie jest udostępniany zewnętrznie.
* Warstwa sieci obwodowej (ciemny niebieski) zawiera wszystkie punkty końcowe, które stają przed publicznego.
* Warstwę publicznego Internetu (jasnoniebieski) zawiera CDN i graczy z ruchem w publicznej sieci internet.

Również powinien być narzędziem do zarządzania zawartością do kontroli ochronie DRM, niezależnie od tego, czy jest to statyczne lub dynamiczne szyfrowanie. Dane wejściowe do szyfrowania DRM obejmują:

* Zawartość wideo MBR
* Klucz zawartości
* Adresy URL pozyskiwania licencji

Poniżej przedstawiono ogólny przepływ podczas odtwarzania:

* Użytkownik jest uwierzytelniony.
* Token autoryzacji jest tworzony dla użytkownika.
* Zawartość chroniona DRM (manifest) zostanie pobrana do odtwarzacza.
* Odtwarzacz przesyła żądanie nabycie licencji do serwerów licencji wraz z kluczem identyfikator i token autoryzacji.

W poniższej sekcji omówiono projektu zarządzania kluczami.

| **ContentKey do zasobu** | **Scenariusz** |
| --- | --- |
| 1-do-1 |Najprostszym przypadku. Zapewnia wyrafinowane kontroli. Ale to rozmieszczenie powoduje ogólnie najwyższy koszt dostarczania licencji. Co najmniej jedno żądanie licencji jest wymagana dla każdego chronionego zasobu. |
| 1-do wielu |Można użyć tego samego klucza zawartości dla wielu zasobów. Na przykład do wszystkich zasobów w grupę logiczną, takich jak określonego rodzaju lub podzbiór gatunku (lub gene filmu), umożliwia pojedynczego klucza zawartości. |
| Wiele do-1 |Używanie wielu kluczy zawartości są wymagane dla każdego zasobu. <br/><br/>Na przykład jeśli trzeba zastosować dynamicznych ochrony CENC przy użyciu technologii multi-DRM dla standardu MPEG-DASH i dynamiczne szyfrowanie AES-128 dla protokołu HLS, potrzebujesz dwóch oddzielnych kluczy zawartości. Każdy klucz zawartości musi mieć swój własny ContentKeyType. (Dla klucza zawartości, używane do dynamicznego CENC ochrony, użyj ContentKeyType.CommonEncryption. Dla klucza zawartości, używany do dynamicznego szyfrowania AES-128 Użyj ContentKeyType.EnvelopeEncryption).<br/><br/>Inny przykład CENC ochrony zawartości DASH, teoretycznie można użyć jednego klucza zawartości chronić strumienia wideo i innej zawartości klucz do ochrony strumienia audio. |
| Wiele do wielu |Kombinacja dwóch poprzednich scenariuszach. Jeden zestaw kluczy zawartości jest używany dla każdego z wielu zasobów w tej samej grupie zasobów. |

Innym ważnym czynnikiem do przeanalizowania jest użycie licencji na trwałe i nietrwałe.

Te zagadnienia są ważne

Jeśli używasz chmury publicznej do dostarczania licencji, licencje trwałe i nietrwałe mają bezpośredni wpływ na koszt dostarczania licencji. Następujące dwa przypadki różnorodności służą do zilustrowania:

* Subskrypcja miesięczna: trwałego licencji i mapowanie klucz do zasobu z zawartości 1-do wielu. Na przykład dla wszystkich dzieci filmy, używamy jednego klucza zawartości do szyfrowania. W takim przypadku:

    Całkowita liczba licencji wymagane dla wszystkich dzieci filmy/urządzenie = 1

* Subskrypcja miesięczna: nonpersistent licencji i mapowania 1-do-1 między klucz zawartości i zasobów. W takim przypadku:

    Całkowita liczba licencji wymagane dla wszystkich dzieci filmy/urządzenie = [liczba filmów, którzy oglądali transmisje stacji] x [liczba sesji]

Wzorce zbytnio licencji doprowadzić do dwóch różnych projektów. Różnych wzorców doprowadzić do dostarczania innej licencji, w przypadku usługi dostarczania licencji znajduje się w chmurze publicznej, takich jak usługa Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Mapowanie projektu do technologii dla implementacji
Następnie ogólnego projektu jest mapowany do technologii na platformie Azure/Media Services, określając technologii każdy blok konstrukcyjny na użytek.

W poniższej tabeli przedstawiono mapowania.

| **Blok konstrukcyjny** | **Technologia** |
| --- | --- |
| **Player** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Dostawcy tożsamości (IDP)** |Azure Active Directory (Azure AD) |
| **Usługa tokenu zabezpieczającego (STS)** |Azure AD |
| **Przepływ pracy ochrony DRM** |Dynamiczne ochrony usługi Media Services |
| **Dostarczanie licencji DRM** |* Usługa Media Services licencji dostarczania (PlayReady, Widevine i FairPlay) <br/>* Axinom serwera licencji <br/>* Niestandardowego serwera licencji PlayReady |
| **Origin** |Punkt końcowy przesyłania strumieniowego usługi Media Services |
| **Zarządzanie kluczami** |Nie wymagane przez implementację referencyjną |
| **Zarządzanie zawartością** |Aplikacja konsolowa C# |

Innymi słowy tożsamości i usługi STS są używane z usługą Azure AD. [Interfejsu API usługi Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) jest używany dla gracza. Usługa Media Services i Media Player obsługuje DASH i CENC przy użyciu technologii multi-DRM.

Na poniższym diagramie przedstawiono ogólną strukturę i przepływ za pomocą poprzedniej mapowania technologii:

![CENC w usłudze Media Services](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Aby skonfigurować szyfrowanie CENC dynamiczne, narzędzia do zarządzania zawartością używa następujących danych wejściowych:

* Otwórz zawartości
* Klucz zawartości generowania i zarządzania nimi.
* Adresy URL pozyskiwania licencji
* Lista informacji z usługi Azure AD

Oto dane wyjściowe narzędzia do zarządzania zawartością:

* ContentKeyAuthorizationPolicy zawiera specyfikację, w jaki sposób dostarczania licencji sprawdza Web tokenu JSON (JWT) i specyfikacje licencji DRM.
* AssetDeliveryPolicy zawiera specyfikacje dotyczące przesyłania strumieniowego, format, ochronie DRM i adresy URL pozyskiwania licencji.

Poniżej przedstawiono przepływ w czasie wykonywania:

* Po uwierzytelnieniu użytkownika jest generowany, token JWT.
* Jedno z oświadczeń zawartych w tokenu JWT jest oświadczenie grupy, które zawiera EntitledUserGroup identyfikator obiektu grupy. To oświadczenie jest używany do przekazywania sprawdzenie uprawnień.
* Odtwarzacz pliki do pobrania manifestu klienta z zawartości chronionej przez CENC i identyfikuje następujące czynności:
   * Identyfikator klucza.
   * Zawartość jest chroniona CENC.
   * Adresy URL pozyskiwania licencji.
* Odtwarzacz żąda licencji nabycia oparte na przeglądarce DRM obsługiwane. W licencji nabycia żądanie, klucz także przesyłane identyfikator i tokenu JWT. Usługi dostarczania licencji sprawdza tokenu JWT i oświadczeń zawartych przed emituje wymaganych licencji.

## <a name="implementation"></a>Wdrażanie
### <a name="implementation-procedures"></a>Procedury implementacji
Implementacja obejmuje następujące kroki:

1. Przygotuj zasoby testu. Kodowanie/pakietów testów wideo MP4 o różnej szybkości transmisji bitów podzielonej zawartości w usłudze Media Services. Ten zasób jest *nie* DRM chronione. Ochronie DRM odbywa się przez dynamiczne protection później.

2. Utwórz klucz Identyfikatora i klucza zawartości (opcjonalnie z inicjatora klucza). W tym wypadku systemem zarządzania kluczami nie jest potrzebna, ponieważ tylko jeden klucz identyfikator i klucz zawartości są wymagane dla kilku zasobów testowych.

3. Interfejsu API usług Media Services umożliwia skonfigurowanie usług dostarczania licencji technologii multi-DRM dla trwałego testu. Jeśli używasz serwerów licencji niestandardowego przez Twoją firmę lub dostawców firmy zamiast licencji usług w usłudze Media Services, możesz pominąć ten krok. Po skonfigurowaniu dostarczania licencji, możesz określić adresy URL pozyskiwania licencji w kroku. Pozwala określić niektóre szczegółowe konfiguracje, takie jak ograniczenia zasad autoryzacji i szablony odpowiedzi licencji dla różnych usług licencji DRM na potrzeby interfejsu API usługi Media. W tej chwili witryny Azure portal nie zawiera wymaganych interfejsu użytkownika dla tej konfiguracji. Aby uzyskać informacje na poziomie interfejsu API i przykładowy kod, zobacz [Użyj PlayReady i Widevine dynamicznego szyfrowania common encryption](media-services-protect-with-playready-widevine.md).

4. Konfigurowanie zasad dostarczania elementów zawartości dla zasobów testowych za pomocą interfejsu API usługi multimediów. Aby uzyskać informacje na poziomie interfejsu API i przykładowy kod, zobacz [Użyj PlayReady i Widevine dynamicznego szyfrowania common encryption](media-services-protect-with-playready-widevine.md).

5. Tworzyć i konfigurować dzierżawę usługi Azure AD na platformie Azure.

6. Utwórz kilka kont użytkowników i grup w dzierżawie usługi Azure AD. Utwórz co najmniej "Uprawniony użytkownik" grupę, a następnie dodać użytkownika do tej grupy. Sprawdź uprawnienia są przekazywane przez użytkowników w tej grupie w nabycie licencji. Użytkownicy nie w tej grupie nie można przekazać sprawdzenie uwierzytelniania i nie można uzyskać licencję. Członkostwo w tej grupie "Uprawniony użytkownik" jest oświadczenie wymaganych grup w token JWT wystawione przez usługę Azure AD. To wymaganie oświadczeń określa się w kroku podczas konfigurowania usług dostarczania licencji technologii multi-DRM.

7. Tworzenie aplikacji ASP.NET MVC do obsługi odtwarzacza wideo. Ta aplikacja ASP.NET jest chroniony przy użyciu uwierzytelniania użytkownika względem dzierżawy usługi Azure AD. Oświadczenia właściwe znajdują się w tokenach dostępu uzyskany po uwierzytelnieniu użytkownika. Firma Microsoft zaleca OpenID Connect i interfejsu API w tym kroku. Zainstaluj następujące pakiety NuGet:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Tworzenie odtwarzacza przy użyciu [interfejsu API usługi Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Użyj [interfejsu API usługi Azure Media Player ProtectionInfo](https://amp.azure.net/libs/amp/latest/docs/) do określenia technologii DRM na różnych platformach DRM.

9. W poniższej tabeli przedstawiono macierzy testów.

    | **DRM** | **Przeglądarka** | **Wynik uprawniony użytkownik** | **Wynik unentitled użytkownika** |
    | --- | --- | --- | --- |
    | **PlayReady** |Przeglądarka Microsoft Edge lub Internet Explorer 11 w systemie Windows 10 |Zakończ pomyślnie |Niepowodzenie |
    | **Widevine** |Chrome, Firefox, Opera |Zakończ pomyślnie |Niepowodzenie |
    | **FairPlay** |Safari w systemie macOS      |Zakończ pomyślnie |Niepowodzenie |
    | **AES-128** |Większość nowoczesnych przeglądarek  |Zakończ pomyślnie |Niepowodzenie |

Aby uzyskać informacji na temat sposobu konfigurowania usługi Azure AD dla aplikacji odtwarzacza ASP.NET MVC, zobacz [integracji aplikacji opartych na usłudze Azure Media Services OWIN MVC za pomocą usługi Azure Active Directory i ograniczenie klucza dostarczania zawartości na podstawie oświadczeń JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Aby uzyskać więcej informacji, zobacz [uwierzytelniania tokenu JWT w usłudze Azure Media Services oraz szyfrowania dynamicznego](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Aby uzyskać informacje dotyczące usługi Azure AD:

* Można znaleźć informacje dla deweloperów w [przewodnik dewelopera usługi Azure Active Directory](../../active-directory/develop/v1-overview.md).
* Można znaleźć informacje o Administratorze w [administrowanie katalogiem usługi Azure AD dzierżawy](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Niektóre problemy w implementacji
Skorzystaj z poniższych informacji dotyczące rozwiązywania problemów, aby uzyskać pomoc dotyczącą problemów z implementacją.

* Wystawca, którego adres URL musi kończyć się znakiem "/". Odbiorcy musi mieć identyfikator klienta aplikacji odtwarzacza. Ponadto Dodaj "/" na końcu adresu URL wystawcy.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    W [dekodera tokenów JWT](http://jwt.calebb.net/), zostanie wyświetlony **aud** i **iss**, jak pokazano na tokenu JWT:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Dodaj uprawnienia do aplikacji w usłudze Azure AD na **Konfiguruj** kartę w aplikacji. Uprawnienia są wymagane dla każdej aplikacji, zarówno lokalnych, jak i wdrożonych wersji.

    ![Uprawnienia](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* Po skonfigurowaniu dynamicznej CENC ochrony, należy użyć poprawne wystawcy.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Nie działa następujące czynności:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    Identyfikator GUID jest identyfikator dzierżawy usługi Azure AD Identyfikator GUID można znaleźć w **punktów końcowych** menu podręczne w witrynie Azure portal.

* Członkostwo w grupie GRANT oświadczeń uprawnień. Upewnij się, że następujące znajduje się w pliku manifestu aplikacji usługi Azure AD: 

    "groupMembershipClaims": "All" (wartość domyślna to null)

* Podczas tworzenia ograniczenia, wymagania, należy ustawić odpowiednie TokenType.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Możesz dodać obsługę JWT (Azure AD) oprócz SWT (ACS), wartość domyślna TokenType dlatego TokenType.JWT. Jeśli używasz SWT/ACS, należy ustawić token do TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Dodatkowe tematy dotyczące implementacji
W tej sekcji omówiono niektóre dodatkowe tematy w projektowania i implementacji.

### <a name="http-or-https"></a>HTTP lub HTTPS?
Aplikacja odtwarzacza platformy ASP.NET MVC musi obsługiwać następujące czynności:

* Uwierzytelnianie użytkowników za pomocą usługi Azure AD, która jest w trakcie protokołu HTTPS.
* Token JWT wymiany między klientem i usługi Azure AD, która jest w trakcie protokołu HTTPS.
* Nabycie licencji DRM przez klienta, który musi być w ramach protokołu HTTPS, jeśli jest dostępna dostarczania licencji Media Services. Pakiet produktów technologii PlayReady nie zmusza HTTPS dostarczania licencji. Jeśli serwer licencji PlayReady znajduje się poza usługi Media Services, można użyć protokołu HTTP lub HTTPS.

Aplikacja odtwarzacza ASP.NET używa protokołu HTTPS najlepszym rozwiązaniem, więc Media Player znajduje się na stronie w obszarze protokołu HTTPS. Jednak HTTP była preferowana dla przesyłania strumieniowego, więc należy wziąć pod uwagę problem zawartości mieszanej.

* Przeglądarka nie zezwala na zawartość mieszana. A dodatki plug-in jak Silverlight i wtyczki dla OSMF złagodzenia i KRESKI. Zawartość mieszana jest kwestią zabezpieczeń ze względu na zagrożenie możliwość iniekcji złośliwego kodu JavaScript, który może spowodować, że dane klienta być narażone na ryzyko. Przeglądarki domyślnego blokowania z tej możliwości. Jedynym sposobem, aby go obejść, jest po stronie serwera (źródło), umożliwiając wszystkich domen (niezależnie od HTTP lub HTTPS). To prawdopodobnie nie jest dobrym pomysłem albo.
* Należy unikać zawartości mieszanej. Zarówno aplikacja odtwarzacza, jak i Media Player należy używać protokołu HTTP lub HTTPS. Podczas odtwarzania zawartości mieszanej silverlightSS tech wymaga czyszczenia ostrzeżenie mieszane zawartość. FlashSS tech obsługuje zawartość mieszana bez ostrzeżenia mieszane zawartość.
* Jeśli punkt końcowy przesyłania strumieniowego zostało utworzone przed sierpnia 2014, nie obsługuje protokołu HTTPS. W tym wypadku Utwórz i używać nowego punktu końcowego przesyłania strumieniowego do połączeń HTTPS.

W implementacji odwołania dla chronionych DRM zawartości aplikacji i przesyłania strumieniowego są w ramach protokołu HTTPS. Otwórz zawartości odtwarzacz nie wymaga uwierzytelniania lub licencji, aby można było używać protokołu HTTP lub HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Usługa Azure Active Directory Przerzucanie klucza podpisywania
Przerzucanie klucza podpisywania jest należy wziąć pod uwagę w danej implementacji. Jeśli zignorujesz go Zakończono systemu po pewnym czasie całego całkowicie, w ciągu sześciu tygodni najbardziej.

Usługa Azure AD używa standardów branżowych, aby ustanowić zaufanie między sobą i aplikacje, które używają usługi Azure AD. W szczególności usługa Azure AD używa klucza podpisywania, który składa się z pary kluczy publicznych i prywatnych. Gdy usługa Azure AD tworzy token zabezpieczający, który zawiera informacje o użytkowniku, jest podpisany przez usługę Azure AD przy użyciu klucza prywatnego przed wysłaniem go do aplikacji. Aby sprawdzić, czy token jest prawidłowy i pochodzącej z usługi Azure AD, aplikacja musi go zweryfikować podpisu tokenu. Aplikacja korzysta z klucza publicznego, udostępniane przez usługę Azure AD, który jest zawarty w dokument metadanych Federacji dzierżawy. Ten klucz publiczny i klucz podpisywania, z którego pochodzi, jest taka sama, jedną dla wszystkich dzierżawców w usłudze Azure AD.

Aby uzyskać więcej informacji na temat przerzucania klucza usługi Azure AD, zobacz [ważne informacje na temat Przerzucanie klucza podpisywania w usłudze Azure AD](../../active-directory/active-directory-signing-key-rollover.md).

Między [pary kluczy publiczny prywatny](https://login.microsoftonline.com/common/discovery/keys/):

* Klucz prywatny jest używany przez usługę Azure AD można wygenerować token JWT.
* Klucz publiczny jest używany przez aplikację taką jak usług dostarczania licencji DRM w usłudze Media Services można zweryfikować tokenu JWT.

Ze względów bezpieczeństwa usługa Azure AD obraca certyfikatu okresowego (co sześć tygodni). W przypadku naruszenia zabezpieczeń ilekroć może wystąpić, przerzucania klucza. W związku z tym usług dostarczania licencji w usłudze Media Services konieczne zaktualizowanie klucza publicznego używany jako usługi Azure AD obraca się para kluczy. W przeciwnym razie token uwierzytelniania w usłudze Media Services zakończy się niepowodzeniem i wystawiono żadna licencja.

Aby skonfigurować tę usługę, należy ustawić TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument podczas konfigurowania usług dostarczania licencji DRM.

Poniżej przedstawiono przepływ JWT:

* Usługa Azure AD wystawia token JWT przy użyciu bieżącego klucza prywatnego dla uwierzytelnionego użytkownika.
* Gdy gracz widzi CENC przy użyciu technologii multi-DRM, chronione zawartość, najpierw lokalizuje JWT wystawione przez usługę Azure AD.
* Odtwarzacz wysyła żądanie nabycie licencji przy użyciu tokenu JWT do usług dostarczania licencji w usłudze Media Services.
* Usług dostarczania licencji w usłudze Media Services Użyj bieżącego/nieprawidłowy klucz publiczny z usługi Azure AD, aby sprawdzić tokenu JWT przed wystawieniem licencji.

Usług dostarczania licencji DRM zawsze Sprawdź, czy bieżąca/nieprawidłowy klucz publiczny z usługi Azure AD. Klucz publiczny przedstawiony przez usługę Azure AD jest klucz używany do sprawdzenia token JWT wystawione przez usługę Azure AD.

Co zrobić, jeśli przerzucania klucza się dzieje, gdy usługa Azure AD wygeneruje token JWT, ale przed wysłaniem przez graczy tokenu JWT do usług dostarczania licencji DRM w usłudze Media Services w celu weryfikacji?

Ponieważ klucz może przenoszone w dowolnym momencie, więcej niż jeden prawidłowy klucz publiczny jest zawsze dostępna w dokumencie metadanych federacji. Dostarczania licencji Media Services można użyć dowolnego z kluczy określonej w dokumencie. Ponieważ jeden klucz może zostać wkrótce wycofana, inny może być zamiennikach i tak dalej.

### <a name="where-is-the-access-token"></a>Gdzie znajduje się token dostępu?
Jeśli przyjrzymy się jak aplikacja sieci web wywołuje aplikację interfejsu API w ramach [tożsamość aplikacji za pomocą przyznanie poświadczenia klienta OAuth 2.0](../../active-directory/develop/web-api.md), przepływ uwierzytelniania jest następujący:

* Użytkownik loguje się do usługi Azure AD w aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [przeglądarki sieci Web do aplikacji sieci web](../../active-directory/develop/web-app.md).
* Punkt końcowy autoryzacji usługi Azure AD przekierowuje agenta użytkownika do aplikacji klienckiej z kodem autoryzacji. Agent użytkownika zwraca kod autoryzacji do identyfikatora URI przekierowania aplikacji klienta.
* Aplikacja sieci web musi uzyskiwanie tokenu dostępu, dzięki czemu mogą uwierzytelniania interfejsu API sieci web i pobrać żądanego zasobu. On kieruje żądanie do punktu końcowego tokenu usługi Azure AD i zapewnia poświadczeń, Identyfikatora klienta i identyfikator URI aplikacji internetowego interfejsu API. Stanowi kod autoryzacji, aby potwierdzić, że użytkownik wyraził zgodę.
* Usługa Azure AD uwierzytelnia aplikacji i zwraca token JWT token dostępu, które jest używane do wywołania interfejsu API sieci web.
* Przy użyciu protokołu HTTPS aplikacja internetowa używa zwrócony token dostępu JWT do dodawania ciągu JWT z oznaczeniem "Bearer" w nagłówku "Autoryzacja" żądanie do internetowego interfejsu API. Internetowy interfejs API następnie sprawdza poprawność tokenu JWT. Jeśli weryfikacja zakończy się pomyślnie, zwraca żądanego zasobu.

W tym przepływie tożsamości aplikacji interfejsu API sieci web ufa, czy aplikacja sieci web uwierzytelniony użytkownik. Z tego powodu ten wzorzec jest nazywany zaufany podsystem. [Diagram przepływu autoryzacji](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) w tym artykule opisano sposób-przyznawanie kodu autoryzacji — przepływ działa.

Nabycie licencji za pomocą ograniczenia tokenu wybrane w jest zgodna z tym samym wzorcem zaufany podsystem. Usługi dostarczania licencji w usłudze Media Services jest zasobu internetowego interfejsu API lub "zasobu back-end", o które aplikacji sieci web wymaga dostępu do. Gdzie jest to token dostępu?

Token dostępu jest pobierany z usługi Azure AD. Po uwierzytelnieniu użytkownik pomyślnie zwracany jest kod autoryzacji. Kod autoryzacji jest następnie używany, wraz z Identyfikatorem klienta i klucza aplikacji do wymiany dla tokenu dostępu. Token dostępu jest używany do dostępu do aplikacji "wskaźnik", który wskazuje lub reprezentuje usługi dostarczania licencji Media Services.

Aby zarejestrować i skonfigurować aplikację wskaźnika w usłudze Azure AD, wykonaj następujące czynności:

1. W ramach dzierżawy usługi Azure AD:

   * Dodawanie aplikacji (zasobów) za pomocą https://[resource_name].azurewebsites.net/ adres URL logowania jednokrotnego. 
   * Dodaj identyfikator aplikacji o https://[aad_tenant_name].onmicrosoft.com/[resource_name adresu URL].

2. Dodaj nowy klucz aplikacji zasobu.

3. Aktualizowanie pliku manifestu aplikacji, tak aby właściwość groupMembershipClaims ma wartość "groupMembershipClaims": "All".

4. W aplikacji usługi Azure AD, która wskazuje na odtwarzaczu aplikacji sieci web, w sekcji **uprawnień dotyczących innych aplikacji**, Dodaj aplikacji zasobu, który został dodany w kroku 1. W obszarze **delegowane uprawnienia**, wybierz opcję **dostępu [resource_name]** . Ta opcja zapewnia uprawnienia aplikacji sieci web do tworzenia tokenów dostępu, uzyskujących dostęp do aplikacji zasobu. W tym lokalnych i wdrożonych wersji aplikacji sieci web w przypadku tworzenia przy użyciu programu Visual Studio i aplikacji sieci web platformy Azure.

Token JWT wystawione przez usługę Azure AD jest token dostępu, które umożliwiają dostęp do zasobów wskaźnika.

### <a name="what-about-live-streaming"></a>Co transmisja strumieniowa na żywo?
Poprzednie dyskusji koncentruje się na zasoby na żądanie. Co transmisja strumieniowa na żywo?

Do ochrony, przesyłanie strumieniowe na żywo w usłudze Media Services przez traktowanie zasób skojarzony z programem jako zawartości wideo na żądanie, można użyć dokładnie tego samego projektu i implementacji.

W szczególności do transmisja strumieniowa na żywo w usłudze Media Services, należy utworzyć kanał powiadomień, a następnie utwórz program w obszarze kanału. Aby utworzyć program, należy utworzyć element zawartości zawierający dynamiczne archiwum do programu. Aby zapewnić CENC przy użyciu technologii multi-DRM ochrony zawartości na żywo, dotyczą tego samego procesu konfiguracji/przetwarzania zasobu tak, jakby były zawartości wideo na żądanie, przed rozpoczęciem korzystania z programu.

### <a name="what-about-license-servers-outside-media-services"></a>Jak wygląda serwerów licencji spoza usługi Media Services?
Często klienci inwestowanych w farmie serwerów licencji albo we własnych centrum danych lub jednym, będąc hostowaną przez usługodawców DRM. Za pomocą nośnika usługi Content Protection może działać w trybie hybrydowym. Zawartość można hostowanych i dynamicznie chronione w usłudze Media Services, podczas dostarczania licencji DRM przez serwery spoza usługi Media Services. W takim przypadku należy wziąć pod uwagę następujące zmiany:

* Usługa STS musi wystawiać tokeny są akceptowane, które można sprawdzić w farmie serwerów licencji. Na przykład serwerów licencji Widevine, które są dostarczane przez Axinom wymagać określonych token JWT z komunikatem o uprawnienia. W związku z tym musisz mieć usługi STS do wystawiania tokenów JWT. Aby uzyskać informacji na temat tego typu wdrożenia, przejdź do [Centrum dokumentacji platformy Azure](https://azure.microsoft.com/documentation/) zobaczyć [Użyj Axinom w celu dostarczania licencji Widevine do usługi Azure Media Services](media-services-axinom-integration.md).
* Nie potrzebujesz już do konfigurowania usługi dostarczania licencji (ContentKeyAuthorizationPolicy) w usłudze Media Services. Należy podać adresy URL pozyskiwania licencji (w przypadku technologii PlayReady, Widevine i FairPlay) po skonfigurowaniu AssetDeliveryPolicy skonfigurowania CENC przy użyciu technologii multi-DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Co zrobić, jeśli chcę użyć niestandardowej usługi STS?
Klient może wybrać do użycia niestandardowej usługi STS w celu zapewnienia tokenów Jwt. Przyczyny:

* Dostawca tożsamości używany przez klienta nie obsługuje usługi STS. W tym przypadku niestandardowej usługi STS może być opcją.
* Klient może być konieczne bardziej elastyczne większego formantu lub do integracji usługi STS z subskrypcją klienta z systemem rozliczeniowym. Na przykład MVPD operator może być wiele pakietów subskrybenta OTT, takich jak podstawowa, premium i sportu. Operator może być zgodne oświadczenia w tokenie pakietem subskrybenta, aby są udostępniane tylko zawartości określonego pakietu. W tym przypadku niestandardowej usługi STS zapewnia wymagane elastyczność i kontrolę.

Jeśli używasz niestandardowej usługi STS, przeprowadza się dwie zmiany:

* Podczas konfigurowania usługi dostarczania licencji dla zasobu, należy określić klucz zabezpieczeń używane na potrzeby weryfikacji przez niestandardowej usługi STS, zamiast bieżącego klucza z usługi Azure AD. (Więcej szczegółów wykonaj). 
* Podczas generowania tokenu JTW określono klucza zabezpieczeń, zamiast X509 bieżącego klucza prywatnego certyfikatu w usłudze Azure AD.

Istnieją dwa typy kluczy zabezpieczeń:

* Klucz symetryczny: ten sam klucz służy do generowania i sprawdź token JWT.
* Klucz asymetryczny: pary kluczy publiczny prywatny w X509 certyfikat jest używany z kluczem prywatnym, aby zaszyfrować/wygenerować token JWT i przy użyciu klucza publicznego w celu zweryfikowania tokenu.

> [!NOTE]
> Jeśli używasz środowiska .NET Framework / C# jako platformy projektowej, X509 certyfikat używany dla klucza asymetrycznego zabezpieczeń musi mieć klucz o długości co najmniej 2048. Jest to wymagane klasy System.IdentityModel.Tokens.X509AsymmetricSecurityKey w programie .NET Framework. W przeciwnym razie jest zgłaszany następujący wyjątek:
> 
> IDX10630: "System.IdentityModel.Tokens.X509AsymmetricSecurityKey" do podpisywania nie może być mniejszy niż "2048" bitów.

## <a name="the-completed-system-and-test"></a>Ukończone system i testowanie
Ta sekcja przeprowadzi Cię przez następujące scenariusze ukończone systemu end-to-end tak, aby przed uzyskaniem konto logowania, może mieć podstawowego obrazu zachowania:

* Jeśli potrzebujesz niezintegrowany scenariusza:

    * Dla zasobów wideo hostowany w usłudze Media Services, które są albo bez ochrony lub DRM chroniona, ale bez tokenu uwierzytelniania (wystawianie licencji do spojrzenia do żądanego), można ją przetestować bez rejestrowania się w. Przełącz się do protokołu HTTP, jeśli Twoja strumieniowe przesyłanie wideo za pośrednictwem protokołu HTTP.

* Jeśli potrzebujesz to zintegrowane scenariusz end-to-end:

    * Wideo zasobów w ramach dynamicznej ochronie DRM w usłudze Media Services za pomocą uwierzytelnianie przy użyciu tokenów i tokenów JWT, generowane przez usługę Azure AD musisz zarejestrować się w.

Odtwarzacz aplikacji sieci web i jej logowania, zobacz [tej witryny sieci Web](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Logowanie użytkowników
Aby przetestować zintegrowanego systemu DRM end-to-end, musisz mieć konto utworzone lub dodane.

Jakie konta?

Mimo że Azure pierwotnie zezwalała na dostęp tylko przez użytkowników kont Microsoft, dostęp jest teraz dozwolony przez użytkowników z tymi dwoma systemami. Wszystkie właściwości platformy Azure teraz ufała usłudze Azure AD do uwierzytelniania, a usługa Azure AD uwierzytelnia użytkowników w organizacji. Relacja Federacji został utworzony, w którym usługi Azure AD ufa system kont Microsoft konsumenta tożsamości na potrzeby uwierzytelniania konsumentów. W rezultacie usługi Azure AD można uwierzytelnić gościa Microsoft kont również jako natywny usługi Azure AD.

Ponieważ usługa Azure AD ufa domena konta Microsoft, można dodać żadnych kont z dowolnego z następujących domen do niestandardowych usługi Azure AD dzierżawy i korzystać z konta do logowania:

| **Nazwa domeny** | **Domeny** |
| --- | --- |
| **Domena dzierżawy niestandardowy usługi Azure AD** |somename.onmicrosoft.com |
| **Domeny firmowej** |microsoft.com |
| **Domena konta Microsoft** |Outlook.com, live.com, hotmail.com |

Możesz skontaktować się ze wszystkich autorów konta utworzone lub dodawane.

Poniższych zrzutach ekranu przedstawiono różne stron logowania w usługach używany przez inną domenę konta:

**Niestandardowy usługi Azure AD dzierżawy konta domeny**: dostosowanego strony logowania w niestandardowych usługi Azure AD dzierżawy domeny.

![Konto domeny dzierżawy niestandardowy usługi Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Konto domeny firmy Microsoft przy użyciu karty inteligentnej**: strona logowania dostosowywane przez firmy Microsoft IT za pomocą uwierzytelniania dwuskładnikowego.

![Konto domeny dzierżawy niestandardowy usługi Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Konto Microsoft**: strony logowania konta Microsoft dla konsumentów.

![Konto domeny dzierżawy niestandardowy usługi Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Używanie rozszerzeń zaszyfrowanych multimediów dla usług PlayReady
Na nowoczesnej przeglądarce z Encrypted Media Extensions (EME) do obsługi technologii PlayReady, takich jak program Internet Explorer 11 na Windows 8.1 lub nowszym i przeglądarki Microsoft Edge w systemie Windows 10 Usługa Playroute jest podstawowym DRM dla EME.

![Na użytek EME PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Obszar ciemny player jest, ponieważ objętych ochroną PlayReady i uniemożliwia wprowadzanie zrzut ekranu wideo chronionych.

Poniższy zrzut ekranu przedstawia dodatków plug-in player i Microsoft Security Essentials (MSE) / EME obsługuje:

![Dodatki plug-in Player PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME Microsoft Edge i Internet Explorer 11 w systemie Windows 10 umożliwia [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) do wywołania na urządzeniach Windows 10, które go obsługują. PlayReady SL3000 odblokowuje przepływ zawartości premium rozszerzone (4K, HDR) i nowe zawartości modelu dostarczania (na dodatkową zawartość).

Aby skoncentrować się na urządzeniach Windows, usługa Playroute jest tylko DRM sprzętu dostępna na urządzeniach Windows (PlayReady SL3000). Usługa przesyłania strumieniowego za pomocą technologii PlayReady za pomocą rozszerzeń EME lub za pomocą aplikacji uniwersalnych platformy Windows i oferują wyższej jakości wideo za pomocą PlayReady SL3000 niż innym DRM. Zwykle zawartość w górę do 2K odbywa się za pośrednictwem przeglądarki Chrome lub Firefox i zawartości maksymalnie 4K odbywa się za pośrednictwem programu Microsoft Edge/Internet Explorer 11 lub aplikacji Universal Windows Platform, w tym samym urządzeniu. Wielkość zależy od ustawienia usługi i implementację.

#### <a name="use-eme-for-widevine"></a>Na użytek EME Widevine
Na nowoczesnej przeglądarce z obsługą EME/Widevine, takiego jak Chrome 41 + w systemie Windows 10, Windows 8.1, systemu Mac OS x Yosemite i Chrome w systemie Android 4.4.4 Google Widevine jest DRM za EME.

![Na użytek EME Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine nie uniemożliwić wprowadzanie zrzut ekranu wideo chronionych.

![Dodatki plug-in Player Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Unentitled użytkowników
Jeśli użytkownik nie jest członkiem grupy "Użytkownicy pt.", użytkownik nie zakończy się pomyślnie sprawdzanie uprawnień. Następnie usługa licencji technologii multi-DRM odmawia wystawiać żądanej licencji, jak pokazano. Szczegółowy opis jest "uzyskania licencji nie powiodło się," który jest zgodnie z założeniami.

![Unentitled użytkowników](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Uruchom usługę tokenu zabezpieczającego niestandardowe
Jeśli uruchamiasz niestandardowej usługi STS, tokenu JWT wystawiony przez niestandardowej usługi STS przy użyciu symetryczne lub klucza asymetrycznego.

Poniższy zrzut ekranu przedstawia scenariusz, który używa klucza symetrycznego (używanym w przeglądarce Chrome):

![Niestandardowej usługi STS przy użyciu klucza symetrycznego](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Poniższy zrzut ekranu przedstawia scenariusz, który używa klucza asymetrycznego, za pośrednictwem X509 certyfikatu (przy użyciu nowoczesnej przeglądarce firmy Microsoft):

![Niestandardowej usługi STS przy użyciu klucza asymetrycznego](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Uwierzytelnianie użytkownika w obu przypadkach poprzedniego pozostaje taka sama. Jego odbywa się za pośrednictwem usługi Azure AD. Jedyna różnica polega na tym, że tokenów Jwt wystawione przez niestandardowej usługi STS, a nie usługi Azure AD. Po skonfigurowaniu dynamicznej ochrony CENC, ograniczenie usługi dostarczania licencji określa typ token JWT symetryczne bądź klucza asymetrycznego.

## <a name="summary"></a>Podsumowanie

W tym dokumencie omówiono CENC przy użyciu wielu natywnych DRM i kontroli dostępu za pośrednictwem uwierzytelniania tokenu, jego projektowania i implementacji przy użyciu usługi Azure Media Services i Media Player.

* Projekt odwołania został przedstawiony zawierający wszystkie niezbędne składniki podsystemu DRM/CENC.
* Zawiera ona implementację referencyjną został przedstawiony na platformie Azure, usługa Media Services i Media Player.
* Niektóre tematy, które są bezpośrednio związane z projektowanie i implementacja zostały również omówione.

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Prześlij opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
