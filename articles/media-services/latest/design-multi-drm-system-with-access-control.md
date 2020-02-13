---
title: System ochrony zawartości z obsługą technologii DRM — Azure Media Services v3
description: W tym artykule przedstawiono szczegółowy opis sposobu projektowania systemu ochrony zawartości z obsługą technologii DRM przy użyciu Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: fbc6d6fa8f9a3b424eaec1f04a61b5ca24fe14fc
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161787"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Projekt systemu multi-DRM ochrony zawartości przy użyciu kontroli dostępu 

Projektowanie i tworzenie podsystem Digital Rights Management (DRM), dla Ott (OTT) lub online rozwiązań do przesyłania strumieniowego jest złożonym zadaniem. Operatory/online dostawców wideo zazwyczaj zlecają obsługę tego zadania wyspecjalizowane dostawców usług DRM. Celem tego dokumentu jest przedstawia projekt wzorcowy i implementacja referencyjna podsystemu DRM end-to-end w OTT lub rozwiązanie do przesyłania strumieniowego online.

Docelowe czytelnicy dla tego dokumentu są inżynierów, którzy pracują w podsystemu DRM OTT lub online rozwiązania przesyłania strumieniowego / (wiele ekranów) lub czytelników, którzy są zainteresowani podsystemu DRM. Zakłada się, czy czytelnicy są zapoznać się z co najmniej jedną z technologii DRM na rynku, takich jak PlayReady, Widevine, FairPlay lub Adobe Access.

W tej dyskusji, multi-DRM dołączamy 3 protokołów DRM, obsługiwane przez usługę Azure Media Services: typowe szyfrowanie (CENC) dla technologii PlayReady i Widevine, FairPlay, a także AES-128 szyfrowania otwartym kluczem. Główne trend w przemyśle OTT i przesyłania strumieniowego online jest korzystać z natywnych protokołów DRM na różnych platformach klienckich. Tego trendu jest przesunięcia od poprzedniego używany pojedynczy DRM i jego zestawu SDK klienta dla różnych platform klienta. W przypadku używania CENC z wielonatywną funkcją DRM, zarówno PlayReady, jak i Widevine są szyfrowane zgodnie ze specyfikacją [Common Encryption (ISO/IEC 23001-7 Cenc)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Zalety stosowania macierzystych multi-DRM content Protection to że:

* Zmniejsza koszty szyfrowania, ponieważ proces szyfrowania jednym jest używana do przeznaczonych dla różnych platform przy użyciu jego natywnych protokołów DRM.
* Powodują obniżenie kosztów zarządzania zasobami, ponieważ potrzebna jest tylko jednej kopii zawartości w magazynie.
* Eliminuje klienta DRM, koszt licencji, ponieważ native DRM client to zwykle wolnego miejsca na jego platformy natywnej.

### <a name="goals-of-the-article"></a>Cele tego artykułu

Cele w tym artykule są:

* Należy podać odwołanie do projektu podsystemu DRM, który używa wszystkich protokołów 3 DRM (CENC dla DASH), technologii FairPlay dla HLS i technologii PlayReady funkcji smooth Streaming.
* Należy podać implementacja referencyjna na platformie Azure i usługi Azure Media Services.
* Omówiono niektóre tematy projektowania i implementacji.

W poniższej tabeli podsumowano natywnych obsługę DRM na różnych platformach i EME w różnych przeglądarkach.

| **Platforma klienta** | **Natywna platforma DRM** | **EME** |
| --- | --- | --- |
| **Inteligentne telewizory, STBs** | PlayReady, Widevine i innych | Osadzony przeglądarki/EME dla PlayReady i Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 for PlayReady|
| **Urządzenia z systemem Android (telefon, tablet, telewizja)** |Widevine |Dla programu Chrome dla Widevine |
| **iOS** | FairPlay | Safari dla technologii FairPlay (od 11.2 system iOS) |
| **macOS** | FairPlay | Safari dla technologii FairPlay (od przeglądarki Safari 9 + w systemie Mac OS X 10.11 i El Capitan)|
| **Systemu tvOS** | FairPlay | |

Biorąc pod uwagę bieżący stan wdrożenia dla każdego DRM usługi zazwyczaj chce zaimplementować dwóch lub trzech protokołów DRM, aby upewnić się, że rozwiązać wszystkie typy punktów końcowych w najlepszy sposób.

Istnieje zależność między złożoność logiki usług i złożoności po stronie klienta do osiągnięcia poziomu środowisko użytkownika w różnych klientów.

Aby ułatwić wybór, należy pamiętać:

* PlayReady natywnie jest implementowane w każdym urządzeniu Windows na niektórych urządzeniach z systemem Android i dostępne za pośrednictwem zestawów SDK oprogramowania, na praktycznie dowolnej platformie.
* Widevine zaimplementowano natywnie każdego urządzenia z systemem Android, Chrome i inne urządzenia. Widevine jest też obsługiwana na przeglądarki Firefox i Opera strumienia DASH.
* Technologia FairPlay jest dostępna w systemach iOS, macOS i tvOS.


## <a name="a-reference-design"></a>Projekt wzorcowy
W tej sekcji przedstawiono projekt odwołania, który jest niezależny od technologii używanych do jej wdrożenia.

Podsystemu DRM może zawierać następujące składniki:

* Zarządzanie kluczami
* Tworzenie pakietów szyfrowania DRM
* Dostarczanie licencji DRM
* Uprawnienie wyboru/Kontrola dostępu do
* Autoryzacja/uwierzytelnianie użytkownika
* Aplikacja odtwarzacza
* Źródło/usługa content delivery network (CDN)

Na poniższym diagramie przedstawiono ogólne interakcji między składnikami w podsystemie DRM:

![CENC podsystemu DRM](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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
| **Graczy** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Dostawca tożsamości (dostawcy tożsamości)** |Azure Active Directory (Azure AD) |
| **Usługa Secure Token Service (STS)** |Azure AD |
| **Przepływ pracy ochrony DRM** |Usługa Azure Media Services protection dynamiczne |
| **Dostarczanie licencji DRM** |* Usługa Media Services licencji dostarczania (PlayReady, Widevine i FairPlay) <br/>* Axinom serwera licencji <br/>* Niestandardowego serwera licencji PlayReady |
| **Źródł** |Usługa Azure Media Services, punkt końcowy przesyłania strumieniowego |
| **Zarządzanie kluczami** |Nie wymagane przez implementację referencyjną |
| **Zarządzanie zawartością** |Aplikacja konsolowa C# |

Innymi słowy tożsamości i usługi STS są dostarczane przez usługę Azure AD. [Interfejs API Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) jest używany dla odtwarzacza. Zarówno Azure Media Services, jak i Azure Media Player obsłużyć CENC za pośrednictwem ŁĄCZNIKa, FairPlay ponad HLS, PlayReady przez bezproblemowe przesyłanie strumieniowe i szyfrowanie AES-128 dla ŁĄCZNIKów, HLS i gładkich.

Na poniższym diagramie przedstawiono ogólną strukturę i przepływ za pomocą poprzedniej mapowania technologii:

![CENC w usłudze Media Services](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Aby skonfigurować ochronę zawartości DRM, narzędzia do zarządzania zawartością używa następujących danych wejściowych:

* Otwórz zawartości
* Klucz zawartości z zarządzania kluczami
* Adresy URL pozyskiwania licencji
* Lista informacji z usługi Azure AD, takie jak odbiorców, wystawcy i tokenu oświadczeń

Oto dane wyjściowe narzędzia do zarządzania zawartością:

* ContentKeyPolicy opisuje szablon licencji DRM dla każdego rodzaju DRM używane;
* ContentKeyPolicyRestriction opisuje kontroli dostępu przed wystawieniem licencji DRM
* Streamingpolicy w tym artykule opisano różne kombinacje formatu kontenera — tryb szyfrowania — protokół przesyłania strumieniowego - DRM, przesyłania strumieniowego
* StreamingLocator opisuje zawartości klucza/IV służy do szyfrowania i adresy URL przesyłania strumieniowego 

Poniżej przedstawiono przepływ w czasie wykonywania:

* Po uwierzytelnieniu użytkownika jest generowany, token JWT.
* Jedno z oświadczeń zawartych w tokenu JWT jest oświadczenie grupy, które zawiera EntitledUserGroup identyfikator obiektu grupy. To oświadczenie jest używany do przekazywania sprawdzenie uprawnień.
* Odtwarzacz pliki do pobrania manifestu klienta z zawartości chronionej przez CENC i identyfikuje następujące czynności:
   * Identyfikator klucza.
   * Zawartość jest chroniona DRM.
   * Adresy URL pozyskiwania licencji.
* Odtwarzacz żąda licencji nabycia oparte na przeglądarce DRM obsługiwane. W licencji nabycia żądanie, klucz także przesyłane identyfikator i tokenu JWT. Usługi dostarczania licencji sprawdza tokenu JWT i oświadczeń zawartych przed emituje wymaganych licencji.

## <a name="implementation"></a>Wdrażanie
### <a name="implementation-procedures"></a>Procedury implementacji
Implementacja obejmuje następujące kroki:

1. Przygotuj zasoby testu. Kodowanie/pakietów testów wideo MP4 o różnej szybkości transmisji bitów podzielonej zawartości w usłudze Media Services. Ten zasób *nie* jest chroniony przez DRM. Ochronie DRM odbywa się przez dynamiczne protection później.

2. Utwórz klucz Identyfikatora i klucza zawartości (opcjonalnie z inicjatora klucza). W tym wypadku systemem zarządzania kluczami nie jest potrzebna, ponieważ tylko jeden klucz identyfikator i klucz zawartości są wymagane dla kilku zasobów testowych.

3. Interfejsu API usług Media Services umożliwia skonfigurowanie usług dostarczania licencji technologii multi-DRM dla trwałego testu. Jeśli używasz serwerów licencji niestandardowego przez Twoją firmę lub dostawców firmy zamiast licencji usług w usłudze Media Services, możesz pominąć ten krok. Po skonfigurowaniu dostarczania licencji, możesz określić adresy URL pozyskiwania licencji w kroku. Pozwala określić niektóre szczegółowe konfiguracje, takie jak ograniczenia zasad autoryzacji i szablony odpowiedzi licencji dla różnych usług licencji DRM na potrzeby interfejsu API usługi Media. W tej chwili witryny Azure portal nie zawiera wymaganych interfejsu użytkownika dla tej konfiguracji. Aby uzyskać informacje na temat poziomu interfejsu API i przykładowy kod, zobacz [Korzystanie z dynamicznego szyfrowania Common Encryption (Widevine](protect-with-drm.md)).

4. Konfigurowanie zasad dostarczania elementów zawartości dla zasobów testowych za pomocą interfejsu API usługi multimediów. Aby uzyskać informacje na temat poziomu interfejsu API i przykładowy kod, zobacz [Korzystanie z dynamicznego szyfrowania Common Encryption (Widevine](protect-with-drm.md)).

5. Tworzyć i konfigurować dzierżawę usługi Azure AD na platformie Azure.

6. Utwórz kilka kont użytkowników i grup w dzierżawie usługi Azure AD. Utwórz co najmniej "Uprawniony użytkownik" grupę, a następnie dodać użytkownika do tej grupy. Sprawdź uprawnienia są przekazywane przez użytkowników w tej grupie w nabycie licencji. Użytkownicy nie w tej grupie nie można przekazać sprawdzenie uwierzytelniania i nie można uzyskać licencję. Członkostwo w tej grupie "Uprawniony użytkownik" jest oświadczenie wymaganych grup w token JWT wystawione przez usługę Azure AD. To wymaganie oświadczeń określa się w kroku podczas konfigurowania usług dostarczania licencji technologii multi-DRM.

7. Tworzenie aplikacji ASP.NET MVC do obsługi odtwarzacza wideo. Ta aplikacja ASP.NET jest chroniony przy użyciu uwierzytelniania użytkownika względem dzierżawy usługi Azure AD. Oświadczenia właściwe znajdują się w tokenach dostępu uzyskany po uwierzytelnieniu użytkownika. Firma Microsoft zaleca OpenID Connect i interfejsu API w tym kroku. Zainstaluj następujące pakiety NuGet:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Utwórz odtwarzacz przy użyciu [interfejsu API Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Użyj [interfejsu API Azure Media Player ProtectionInfo](https://amp.azure.net/libs/amp/latest/docs/) , aby określić technologię DRM, która ma być używana na różnych platformach DRM.

9. W poniższej tabeli przedstawiono macierzy testów.

    | **ZASTOSOWANIE** | **Przeglądarka** | **Wynik dla uprawnionego użytkownika** | **Wynik nieuprawnionego użytkownika** |
    | --- | --- | --- | --- |
    | **PlayReady** |Przeglądarka Microsoft Edge lub Internet Explorer 11 w systemie Windows 10 |Zakończ pomyślnie |Niepowodzenie |
    | **Widevine** |Chrome, Firefox, Opera |Zakończ pomyślnie |Niepowodzenie |
    | **FairPlay** |Safari w systemie macOS      |Zakończ pomyślnie |Niepowodzenie |
    | **AES — 128** |Większość nowoczesnych przeglądarek  |Zakończ pomyślnie |Niepowodzenie |

Aby uzyskać informacje na temat sposobu konfigurowania usługi Azure AD dla aplikacji odtwarzacza ASP.NET MVC, zobacz [Integrowanie aplikacji opartej na platformie mvc Azure Media Services Owin z Azure Active Directory i ograniczanie dostarczania kluczy zawartości na podstawie oświadczeń JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Aby uzyskać więcej informacji, zobacz [uwierzytelnianie tokenu JWT w Azure Media Services i szyfrowanie dynamiczne](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Aby uzyskać informacje dotyczące usługi Azure AD:

* Informacje dla deweloperów można znaleźć w [przewodniku dewelopera Azure Active Directory](../../active-directory/develop/v2-overview.md).
* Informacje o administratorze można znaleźć w temacie [administrowanie katalogiem dzierżawy usługi Azure AD](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Niektóre problemy w implementacji

Skorzystaj z poniższych informacji dotyczące rozwiązywania problemów, aby uzyskać pomoc dotyczącą problemów z implementacją.

* Wystawca, którego adres URL musi kończyć się znakiem "/". Odbiorcy musi mieć identyfikator klienta aplikacji odtwarzacza. Ponadto Dodaj "/" na końcu adresu URL wystawcy.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    W [dekoderze JWT](http://jwt.calebb.net/)zobaczysz **AUD** i **ISS**, jak pokazano w tokenie JWT:

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Dodaj uprawnienia do aplikacji w usłudze Azure AD na karcie **Konfiguracja** aplikacji. Uprawnienia są wymagane dla każdej aplikacji, zarówno lokalnych, jak i wdrożonych wersji.

    ![Uprawnienia](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* Po skonfigurowaniu dynamicznej CENC ochrony, należy użyć poprawne wystawcy.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Nie działa następujące czynności:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    Identyfikator GUID jest identyfikator dzierżawy usługi Azure AD Identyfikator GUID można znaleźć w menu podręcznym **punkty końcowe** w Azure Portal.

* Członkostwo w grupie GRANT oświadczeń uprawnień. Upewnij się, że następujące znajduje się w pliku manifestu aplikacji usługi Azure AD: 

    "groupMembershipClaims": "All" (wartość domyślna to null)

* Podczas tworzenia ograniczenia, wymagania, należy ustawić odpowiednie TokenType.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Możesz dodać obsługę JWT (Azure AD) oprócz SWT (ACS), wartość domyślna TokenType dlatego TokenType.JWT. Jeśli używasz SWT/ACS, należy ustawić token do TokenType.SWT.

## <a name="the-completed-system-and-test"></a>Ukończone system i testowanie

Ta sekcja przeprowadzi Cię przez następujące scenariusze ukończone systemu end-to-end tak, aby przed uzyskaniem konto logowania, może mieć podstawowego obrazu zachowania:

* Jeśli potrzebujesz niezintegrowany scenariusza:

    * Dla zasobów wideo hostowany w usłudze Media Services, które są albo bez ochrony lub DRM chroniona, ale bez tokenu uwierzytelniania (wystawianie licencji do spojrzenia do żądanego), można ją przetestować bez rejestrowania się w. Przełącz się do protokołu HTTP, jeśli Twoja strumieniowe przesyłanie wideo za pośrednictwem protokołu HTTP.

* Jeśli potrzebujesz to zintegrowane scenariusz end-to-end:

    * Wideo zasobów w ramach dynamicznej ochronie DRM w usłudze Media Services za pomocą uwierzytelnianie przy użyciu tokenów i tokenów JWT, generowane przez usługę Azure AD musisz zarejestrować się w.

W przypadku aplikacji sieci Web odtwarzacza i jej logowania zobacz [tę witrynę sieci Web](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Logowanie użytkowników
Aby przetestować zintegrowanego systemu DRM end-to-end, musisz mieć konto utworzone lub dodane.

Jakie konta?

Mimo że Azure pierwotnie zezwalała na dostęp tylko przez użytkowników kont Microsoft, dostęp jest teraz dozwolony przez użytkowników z tymi dwoma systemami. Wszystkie właściwości platformy Azure teraz ufała usłudze Azure AD do uwierzytelniania, a usługa Azure AD uwierzytelnia użytkowników w organizacji. Relacja Federacji został utworzony, w którym usługi Azure AD ufa system kont Microsoft konsumenta tożsamości na potrzeby uwierzytelniania konsumentów. W rezultacie usługi Azure AD można uwierzytelnić gościa Microsoft kont również jako natywny usługi Azure AD.

Ponieważ usługa Azure AD ufa domena konta Microsoft, można dodać żadnych kont z dowolnego z następujących domen do niestandardowych usługi Azure AD dzierżawy i korzystać z konta do logowania:

| **Nazwa domeny** | **Domeny** |
| --- | --- |
| **Niestandardowa domena dzierżawy usługi Azure AD** |somename.onmicrosoft.com |
| **Domena firmowa** |microsoft.com |
| **Domena konto Microsoft** |Outlook.com, live.com, hotmail.com |

Możesz skontaktować się ze wszystkich autorów konta utworzone lub dodawane.

Poniższych zrzutach ekranu przedstawiono różne stron logowania w usługach używany przez inną domenę konta:

**Niestandardowe konto domeny dzierżawy usługi Azure AD**: dostosowana Strona logowania niestandardowej domeny dzierżawy usługi Azure AD.

![Konto domeny dzierżawy niestandardowy usługi Azure AD, jeden](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Konto domeny Microsoft z kartą inteligentną**: Strona logowania dostosowana przez firmę Microsoft w firmie przy użyciu uwierzytelniania dwuskładnikowego.

![Konto domeny dzierżawy niestandardowy usługi Azure AD dwóch](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Konto Microsoft**: strona logowania konto Microsoft dla użytkowników.

![Konto domeny dzierżawy niestandardowy usługi Azure AD trzy](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Używanie rozszerzeń zaszyfrowanych multimediów dla usług PlayReady

Na nowoczesnej przeglądarce z Encrypted Media Extensions (EME) do obsługi technologii PlayReady, takich jak program Internet Explorer 11 na Windows 8.1 lub nowszym i przeglądarki Microsoft Edge w systemie Windows 10 Usługa Playroute jest podstawowym DRM dla EME.

![Na użytek EME PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

Obszar ciemny player jest, ponieważ objętych ochroną PlayReady i uniemożliwia wprowadzanie zrzut ekranu wideo chronionych.

Poniższy zrzut ekranu przedstawia dodatków plug-in player i Microsoft Security Essentials (MSE) / EME obsługuje:

![Dodatki plug-in Player PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

EME w przeglądarce Microsoft Edge i Internet Explorer 11 w systemie Windows 10 umożliwia wywoływanie oprogramowania [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) na urządzeniach z systemem Windows 10, które je obsługują. PlayReady SL3000 odblokowuje przepływ zawartości premium rozszerzone (4K, HDR) i nowe zawartości modelu dostarczania (na dodatkową zawartość).

Aby skoncentrować się na urządzeniach Windows, usługa Playroute jest tylko DRM sprzętu dostępna na urządzeniach Windows (PlayReady SL3000). Usługa przesyłania strumieniowego za pomocą technologii PlayReady za pomocą rozszerzeń EME lub za pomocą aplikacji uniwersalnych platformy Windows i oferują wyższej jakości wideo za pomocą PlayReady SL3000 niż innym DRM. Zwykle zawartość w górę do 2K odbywa się za pośrednictwem przeglądarki Chrome lub Firefox i zawartości maksymalnie 4K odbywa się za pośrednictwem programu Microsoft Edge/Internet Explorer 11 lub aplikacji Universal Windows Platform, w tym samym urządzeniu. Wielkość zależy od ustawienia usługi i implementację.

#### <a name="use-eme-for-widevine"></a>Na użytek EME Widevine

Na nowoczesnej przeglądarce z obsługą EME/Widevine, takiego jak Chrome 41 + w systemie Windows 10, Windows 8.1, systemu Mac OS x Yosemite i Chrome w systemie Android 4.4.4 Google Widevine jest DRM za EME.

![Na użytek EME Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine nie uniemożliwić wprowadzanie zrzut ekranu wideo chronionych.

![Dodatki plug-in Player Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Na użytek EME technologii FairPlay

Podobnie można przetestować FairPlay chroniona zawartość w odtwarzaczu tego testu w programie Safari w systemie macOS lub iOS 11.2 i nowsze.

Upewnij się, możesz umieścić "FairPlay" jako protectionInfo.type, a w właściwego adresu URL dla swój certyfikat aplikacji w ścieżce programu AC kl. / s (technologii FairPlay Streaming certyfikatu ścieżka aplikacji).

### <a name="unentitled-users"></a>Unentitled użytkowników

Jeśli użytkownik nie jest członkiem grupy "Użytkownicy pt.", użytkownik nie zakończy się pomyślnie sprawdzanie uprawnień. Następnie usługa licencji technologii multi-DRM odmawia wystawiać żądanej licencji, jak pokazano. Szczegółowy opis jest "uzyskania licencji nie powiodło się," który jest zgodnie z założeniami.

![Unentitled użytkowników](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Uruchom usługę tokenu zabezpieczającego niestandardowe

Jeśli uruchamiasz niestandardowej usługi STS, tokenu JWT wystawiony przez niestandardowej usługi STS przy użyciu symetryczne lub klucza asymetrycznego.

Poniższy zrzut ekranu przedstawia scenariusz, który używa klucza symetrycznego (używanym w przeglądarce Chrome):

![Niestandardowej usługi STS przy użyciu klucza symetrycznego](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

Poniższy zrzut ekranu przedstawia scenariusz, który używa klucza asymetrycznego, za pośrednictwem X509 certyfikatu (przy użyciu nowoczesnej przeglądarce firmy Microsoft):

![Niestandardowej usługi STS przy użyciu klucza asymetrycznego](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

Uwierzytelnianie użytkownika w obu przypadkach poprzedniego pozostaje taka sama. Jego odbywa się za pośrednictwem usługi Azure AD. Jedyna różnica polega na tym, że tokenów Jwt wystawione przez niestandardowej usługi STS, a nie usługi Azure AD. Po skonfigurowaniu dynamicznej ochrony CENC, ograniczenie usługi dostarczania licencji określa typ token JWT symetryczne bądź klucza asymetrycznego.

## <a name="next-steps"></a>Następne kroki

* [Często zadawane pytania](frequently-asked-questions.md)
* [Omówienie ochrony zawartości](content-protection-overview.md)
* [Ochrona zawartości za pomocą technologii DRM](protect-with-drm.md)
