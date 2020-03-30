---
title: System ochrony zawartości wielu drm - Azure Media Services w wersji 3
description: W tym artykuły przedstawiono szczegółowy opis sposobu projektowania systemu ochrony zawartości wielu drm za pomocą usługi Azure Media Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161787"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu 

Projektowanie i tworzenie podsystemu DRM (Digital Rights Management) dla rozwiązania ott (over-the-top) lub online streaming jest złożonym zadaniem. Operatorzy/dostawcy wideo online zazwyczaj zlecają to zadanie wyspecjalizowanym dostawcom usług DRM. Celem tego dokumentu jest przedstawienie projektu referencyjnego i implementacji referencyjnej kompleksowego podsystemu DRM w rozwiązaniu OTT lub do przesyłania strumieniowego online.

Docelowymi czytnikami tego dokumentu są inżynierowie, którzy pracują w podsystemach DRM OTT lub rozwiązaniach online do przesyłania strumieniowego/wieloekranowych lub czytnikach zainteresowanych podsystemami DRM. Założenie jest takie, że czytelnicy są zaznajomieni z co najmniej jedną z technologii DRM dostępnych na rynku, takich jak PlayReady, Widevine, FairPlay lub Adobe Access.

W tej dyskusji przez multi-DRM uwzględniamy 3 DRMs obsługiwane przez usługi Azure Media Services: Common Encryption (CENC) dla PlayReady i Widevine, FairPlay, a także AES-128 szyfrowania klucza wyczyść. Głównym trendem w streamingu online i branży OTT jest używanie natywnych drmów na różnych platformach klienckich. Ten trend jest przejściem od poprzedniego, który używał pojedynczego drm i jego sdk klienta dla różnych platform klienckich. W przypadku korzystania z cenc z wielonatywną drm, zarówno PlayReady i Widevine są szyfrowane zgodnie ze [specyfikacją Common Encryption (ISO/IEC 23001-7 CENC).](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/)

Zalety korzystania z natywnego multi-DRM do ochrony zawartości jest to, że:

* Zmniejsza koszty szyfrowania, ponieważ pojedynczy proces szyfrowania jest używany do kierowania różnych platform z jego natywnych drms.
* Zmniejsza koszty zarządzania zasobami, ponieważ w magazynie potrzebna jest tylko jedna kopia zasobu.
* Eliminuje koszty licencjonowania klienta DRM, ponieważ natywny klient DRM jest zwykle wolny na swojej natywnej platformie.

### <a name="goals-of-the-article"></a>Cele artykułu

Celem tego artykułu są:

* Podaj projekt referencyjny podsystemu DRM, który używa wszystkich 3 drmów (CENC dla DASH, FairPlay dla HLS i PlayReady dla płynnego przesyłania strumieniowego).
* Podaj implementację referencyjną na platformie Azure i platformie Azure Media Services.
* Omów niektóre tematy związane z projektowaniem i implementacją.

W poniższej tabeli podsumowano natywną obsługę drm na różnych platformach i obsługę eme w różnych przeglądarkach.

| **Klient platforma** | **Natywna drm** | **Eme** |
| --- | --- | --- |
| **Inteligentne telewizory, stbs** | PlayReady, Widevine i/lub inne | Wbudowana przeglądarka/EME dla PlayReady i/lub Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 dla PlayReady|
| **Urządzenia z systemem Android (telefon, tablet, telewizor)** |Widevine |Chrome dla Widevine |
| **iOS** | FairPlay | Safari dla FairPlay (od iOS 11.2) |
| **Macos** | FairPlay | Safari dla FairPlay (od Safari 9+ na Mac OS X 10.11+ El Capitan)|
| **tvOS** | FairPlay | |

Biorąc pod uwagę bieżący stan wdrożenia dla każdego drm, usługa zazwyczaj chce zaimplementować dwa lub trzy drms, aby upewnić się, że adres wszystkich typów punktów końcowych w najlepszy sposób.

Istnieje kompromis między złożonością logiki usługi i złożoności po stronie klienta, aby osiągnąć pewien poziom doświadczenia użytkownika na różnych klientach.

Aby dokonać wyboru, pamiętaj:

* PlayReady jest natywnie realizowane w każdym urządzeniu z systemem Windows, na niektórych urządzeniach z systemem Android i dostępne za pośrednictwem oprogramowania SDK na praktycznie każdej platformie.
* Widevine jest natywnie zaimplementowany na każdym urządzeniu z Androidem, w Chrome i na niektórych innych urządzeniach. Widevine jest również obsługiwany w przeglądarkach Firefox i Opera przez DASH.
* FairPlay jest dostępny w systemach iOS, macOS i tvOS.


## <a name="a-reference-design"></a>Projekt referencyjny
W tej sekcji przedstawiono projekt odniesienia, który jest niezależny od technologii używanych do jego zaimplementowania.

Podsystem DRM może zawierać następujące składniki:

* Zarządzanie kluczami
* Opakowanie szyfrujące DRM
* Dostarczanie licencji DRM
* Kontrola uprawnień/dostępu
* Uwierzytelnianie/autoryzacja użytkownika
* Aplikacja Player
* Sieć pochodzenia/dostarczania zawartości (CDN)

Na poniższym diagramie przedstawiono interakcję wysokiego poziomu między składnikami w podsystemie DRM:

![Podsystem DRM z CENC](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Projekt ma trzy podstawowe warstwy:

* Warstwa zaplecza (czarna) nie jest narażona zewnętrznie.
* Warstwa DMZ (ciemnoniebieski) zawiera wszystkie punkty końcowe, które stają przed publicznością.
* Publiczna warstwa internetowa (jasnoniebieska) zawiera sieć CDN i odtwarzacze z ruchem w internecie publicznym.

Powinno również istnieć narzędzie do zarządzania zawartością do kontrolowania ochrony DRM, niezależnie od tego, czy jest to szyfrowanie statyczne, czy dynamiczne. Dane wejściowe dla szyfrowania DRM obejmują:

* Zawartość wideo MBR
* Klucz zawartości
* Adresy URL pozyskiwania licencji

Oto przepływ wysokiego poziomu podczas odtwarzania:

* Użytkownik jest uwierzytelniony.
* Token autoryzacji jest tworzony dla użytkownika.
* Zawartość chroniona przez DRM (manifest) jest pobierana do odtwarzacza.
* Gracz przesyła żądanie nabycia licencji do serwerów licencji wraz z identyfikatorem klucza i tokenem autoryzacji.

W poniższej sekcji omówiono projekt zarządzania kluczami.

| **ContentKey do zasobu** | **Scenariusz** |
| --- | --- |
| 1 do 1 |Najprostszy przypadek. Zapewnia najlepszą kontrolę. Ale to rozwiązanie zazwyczaj powoduje najwyższy koszt dostawy licencji. Dla każdego chronionego zasobu wymagane jest co najmniej jedno żądanie licencji. |
| 1 do wielu |Można użyć tego samego klucza zawartości dla wielu zasobów. Na przykład dla wszystkich zasobów w grupie logicznej, takich jak gatunek lub podzbiór gatunku (lub genu filmu), można użyć pojedynczego klucza zawartości. |
| Wiele do 1 |Dla każdego zasobu jest potrzebnych wiele kluczy zawartości. <br/><br/>Na przykład, jeśli chcesz zastosować dynamiczną ochronę CENC z multi-DRM dla MPEG-DASH i dynamiczne szyfrowanie AES-128 dla HLS, potrzebujesz dwóch oddzielnych kluczy zawartości. Każdy klucz zawartości potrzebuje własnego ContentKeyType. (W przypadku klucza zawartości używanego do dynamicznej ochrony CENC należy użyć contentkeytype.commonencryption. W przypadku klucza zawartości używanego do dynamicznego szyfrowania AES-128 należy użyć funkcji ContentKeyType.EnvelopeEncryption).<br/><br/>Innym przykładem jest ochrona zawartości DASH w cenc, teoretycznie można użyć jednego klucza zawartości do ochrony strumienia wideo i innego klucza zawartości w celu ochrony strumienia audio. |
| Wiele do wielu |Kombinacja poprzednich dwóch scenariuszy. Jeden zestaw kluczy zawartości jest używany dla każdego z wielu zasobów w tej samej grupie zasobów. |

Innym ważnym czynnikiem, który należy wziąć pod uwagę, jest użycie licencji trwałych i nietrwałych.

Dlaczego te rozważania są ważne?

Jeśli używasz chmury publicznej do dostarczania licencji, licencje trwałe i nietrwałe mają bezpośredni wpływ na koszt dostarczenia licencji. Do zilustrowania służą następujące dwa różne przypadki projektowe:

* Subskrypcja miesięczna: użyj licencji trwałej i mapowania klucza do zasobów zawartości od 1 do wielu. Na przykład w przypadku wszystkich filmów dla dzieci używamy jednego klucza zawartości do szyfrowania. W takim przypadku:

    Całkowita liczba licencji wymaganych dla wszystkich filmów/urządzeń dla dzieci = 1

* Subskrypcja miesięczna: użyj licencji nietrwać i mapowania 1 do 1 między kluczem zawartości a zasobem. W takim przypadku:

    Łączna liczba wymaganych licencji na wszystkie filmy/urządzenie dla dzieci = [liczba obejrzanych filmów] x [liczba sesji]

Dwa różne projekty powodują bardzo różne wzorce żądań licencji. Różne wzorce powodują różne koszty dostarczania licencji, jeśli usługa dostarczania licencji jest świadczona przez chmurę publiczną, taką jak Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Projektowanie map do technologii do wdrożenia
Następnie projekt ogólny jest mapowany na technologie na platformie Azure/Media Services, określając, która technologia ma być używana dla każdego bloku konstrukcyjnego.

W poniższej tabeli przedstawiono mapowanie.

| **Blok konstrukcyjny** | **Technologia** |
| --- | --- |
| **Odtwarzacz** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Dostawca tożsamości (IDP)** |Azure Active Directory (Azure AD) |
| **Usługa bezpiecznego tokenu (STS)** |Azure AD |
| **Przepływ pracy ochrony DRM** |Ochrona dynamiczna usługi Azure Media Services |
| **Dostarczanie licencji DRM** |* Dostarczanie licencji Usługi Media Services (PlayReady, Widevine, FairPlay) <br/>* Serwer licencji Axinom <br/>* Niestandardowy serwer licencji PlayReady |
| **Origin** |Punkt końcowy przesyłania strumieniowego usługi Azure Media Services |
| **Zarządzanie kluczami** |Nie jest potrzebne do implementacji referencyjnej |
| **Zarządzanie zawartością** |Aplikacja konsoli języka C# |

Innymi słowy zarówno IDP, jak i STS są dostarczane przez usługę Azure AD. [Interfejs API programu Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) jest używany dla odtwarzacza. Usługi Azure Media Services i Azure Media Player obsługują cenc przez DASH, FairPlay przez HLS, PlayReady na płynne przesyłanie strumieniowe i szyfrowanie AES-128 dla DASH, HLS i płynne.

Na poniższym diagramie przedstawiono ogólną strukturę i przepływ z poprzednim mapowaniem technologii:

![CENC w serwisie medialnym](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Aby skonfigurować ochronę zawartości DRM, narzędzie do zarządzania zawartością korzysta z następujących danych wejściowych:

* Otwieranie zawartości
* Klucz zawartości z zarządzania kluczami
* Adresy URL pozyskiwania licencji
* Lista informacji z usługi Azure AD, takich jak odbiorcy, wystawca i oświadczenia tokenu

Oto dane wyjściowe narzędzia do zarządzania treścią:

* ContentKeyPolicy opisuje szablon licencji DRM dla każdego rodzaju drm używane;
* ContentKeyPolicyRestriction opisuje kontrolę dostępu przed wydaniem licencji DRM
* Streamingpolicy opisuje różne kombinacje DRM - tryb szyfrowania - protokół przesyłania strumieniowego - format kontenera, do przesyłania strumieniowego
* StreamingLocator opisuje klucz zawartości/IV używany do szyfrowania i przesyłania strumieniowego adresów URL 

Oto przepływ podczas wykonywania:

* Po uwierzytelnieniu użytkownika jest generowany JWT.
* Jednym z oświadczeń zawartych w JWT jest oświadczenie grupy, które zawiera identyfikator obiektu grupy EntitledUserGroup. To roszczenie służy do przekazywania kontroli uprawnień.
* Gracz pobiera manifest klienta zawartości chronionej cenc i identyfikuje następujące elementy:
   * Identyfikator klucza.
   * Zawartość jest chroniona przez drm.
   * Adresy URL pozyskiwania licencji.
* Odtwarzacz składa żądanie nabycia licencji na podstawie obsługiwanej przeglądarki/DRM. W żądaniu nabycia licencji przesyłany jest również identyfikator klucza i JWT. Usługa dostarczania licencji weryfikuje JWT i oświadczenia zawarte przed jej wydawaniem potrzebnej licencji.

## <a name="implementation"></a>Wdrażanie
### <a name="implementation-procedures"></a>Procedury wdrożeniowe
Implementacja obejmuje następujące kroki:

1. Przygotowanie zasobów testowych. Kodowanie/pakiet wideo testowe do wielosadłowej pofragmentowanej mp4 w umiaźnym umio. Ten zasób *nie* jest chroniony przez drm. Ochrona DRM odbywa się później dzięki dynamicznej ochronie.

2. Utwórz identyfikator klucza i klucz zawartości (opcjonalnie z inicjatora klucza). W tym przypadku system zarządzania kluczami nie jest potrzebny, ponieważ tylko jeden identyfikator klucza i klucz zawartości są wymagane dla kilku zasobów testowych.

3. Użyj interfejsu API usługi Media Services, aby skonfigurować usługi dostarczania licencji wielu DRM dla zasobu testowego. Jeśli używasz niestandardowych serwerów licencji przez firmę lub dostawców firmy zamiast usług licencyjnych w umiań Programu Media Services, możesz pominąć ten krok. Adresy URL pozyskiwania licencji można określić w kroku podczas konfigurowania dostarczania licencji. Interfejs API usługi Media Services jest potrzebny do określenia niektórych szczegółowych konfiguracji, takich jak ograniczenia zasad autoryzacji i szablony odpowiedzi licencji dla różnych usług licencji DRM. W tej chwili witryny Azure portal nie zapewnia interfejsu użytkownika dla tej konfiguracji. Aby uzyskać informacje na poziomie interfejsu API i przykładowy kod, zobacz [Używanie funkcji PlayReady i/lub Widevine dynamic common encryption](protect-with-drm.md).

4. Użyj interfejsu API usługi Media Services, aby skonfigurować zasady dostarczania zasobów dla zasobu testowego. Aby uzyskać informacje na poziomie interfejsu API i przykładowy kod, zobacz [Używanie funkcji PlayReady i/lub Widevine dynamic common encryption](protect-with-drm.md).

5. Tworzenie i konfigurowanie dzierżawy usługi Azure AD na platformie Azure.

6. Utwórz kilka kont użytkowników i grup w dzierżawie usługi Azure AD. Utwórz co najmniej grupę "Uprawniony użytkownik" i dodaj użytkownika do tej grupy. Użytkownicy w tej grupie przechodzą sprawdzanie uprawnień w pozyskiwaniu licencji. Użytkownicy, którzy nie mają w tej grupie, nie przejdą sprawdzania uwierzytelniania i nie mogą uzyskać licencji. Członkostwo w tej grupie "Uprawniony użytkownik" jest wymaganym oświadczeniem grup w JWT wystawionym przez usługę Azure AD. To wymaganie odzewu można określić w kroku podczas konfigurowania usług dostarczania licencji wielu DRM.

7. Utwórz ASP.NET aplikację MVC, aby hostować odtwarzacz wideo. Ta ASP.NET aplikacja jest chroniona za pomocą uwierzytelniania użytkowników w dzierżawie usługi Azure AD. Prawidłowe oświadczenia są uwzględniane w tokenach dostępu uzyskanych po uwierzytelnieniu użytkownika. Dla tego kroku zalecamy interfejs API OpenID Connect. Zainstaluj następujące pakiety NuGet:

   * Pakiet instalacji Microsoft.Azure.ActiveDirectory.GraphClient
   * Pakiet instalacji Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Instalacja-Pakiet Microsoft.Owin.Host.SystemWeb
   * Pakiet instalacji Microsoft.IdentityModel.Clients.ActiveDirectory

8. Utwórz odtwarzacz przy użyciu [interfejsu API programu Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Użyj [interfejsu API Azure Media Player ProtectionInfo,](https://amp.azure.net/libs/amp/latest/docs/) aby określić, która technologia DRM ma być używana na różnych platformach DRM.

9. W poniższej tabeli przedstawiono macierz testową.

    | **Drm** | **Przeglądarka** | **Wynik dla uprawnionego użytkownika** | **Wynik dla użytkownika bez tytułu** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge lub Internet Explorer 11 w systemie Windows 10 |Odnieść sukces |Niepowodzenie |
    | **Widevine** |Chrome, Firefox, Opera |Odnieść sukces |Niepowodzenie |
    | **FairPlay** |Safari w systemie macOS      |Odnieść sukces |Niepowodzenie |
    | **AES-128** |Większość nowoczesnych przeglądarek  |Odnieść sukces |Niepowodzenie |

Aby uzyskać informacje na temat konfigurowania usługi Azure AD dla ASP.NET aplikacji odtwarzacza MVC, zobacz [Integrowanie aplikacji opartej na platformie Azure Media Services OWIN MVC z usługą Azure Active Directory i ograniczanie dostarczania kluczy zawartości na podstawie oświadczeń JWT.](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie tokenów JWT w usłudze Azure Media Services i szyfrowanie dynamiczne](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Aby uzyskać informacje na temat usługi Azure AD:

* Informacje o deweloperach można znaleźć w [przewodniku dla deweloperów usługi Azure Active Directory](../../active-directory/develop/v2-overview.md).
* Informacje o administratorze można znaleźć w obszarze [Administrowanie katalogiem dzierżawy usługi Azure AD](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Niektóre kwestie związane z wdrażaniem

Skorzystaj z następujących informacji dotyczących rozwiązywania problemów, aby uzyskać pomoc dotyczącą problemów z implementacją.

* Adres URL wystawcy musi kończyć się na "/". Odbiorcy muszą być identyfikatorem klienta aplikacji odtwarzacza. Ponadto dodaj "/" na końcu adresu URL wystawcy.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    W [dekoderze JWT](http://jwt.calebb.net/)widać **aud** i **iss**, jak pokazano w JWT:

    ![Jwt](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Dodaj uprawnienia do aplikacji w usłudze Azure AD na karcie **Konfigurowanie** aplikacji. Uprawnienia są wymagane dla każdej aplikacji, zarówno wersji lokalnych, jak i wdrożonych.

    ![Uprawnienia](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* Podczas konfigurowania dynamicznej ochrony CENC należy użyć odpowiedniego wystawcy.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Poniżej nie działa:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    Identyfikator GUID to identyfikator dzierżawy usługi Azure AD. Identyfikator GUID można znaleźć w menu podręcznym **Punkty końcowe** w witrynie Azure portal.

* Udzielanie uprawnień do roszczeń z tytułu członkostwa w grupie. Upewnij się, że w pliku manifestu aplikacji usługi Azure AD są następujące: 

    "groupMembershipClaims": "Wszystkie" (wartość domyślna to null)

* Ustaw odpowiedni TokenType podczas tworzenia wymagań ograniczeń.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Ponieważ oprócz usługi SWT (ACS) dodano obsługę JWT (Azure AD), domyślnym elementem tokentypem jest TokenType.JWT. Jeśli używasz SWT/ACS, należy ustawić token na TokenType.SWT.

## <a name="the-completed-system-and-test"></a>Ukończony system i test

W tej sekcji przedstawiono następujące scenariusze w ukończonym systemie end-to-end, dzięki czemu można mieć podstawowy obraz zachowania przed uzyskaniem konta logowania:

* Jeśli potrzebujesz scenariusza niezintegranego:

    * W przypadku zasobów wideo hostowanych w umiaźnych usługach Media Services, które są niechronione lub chronione przez drm, ale bez uwierzytelniania tokenu (wystawianie licencji każdemu, kto tego zażądał), możesz przetestować ją bez logowania. Przełącz się na http, jeśli przesyłanie strumieniowe wideo odbywa się za pośrednictwem protokołu HTTP.

* Jeśli potrzebujesz zintegrowanego scenariusza end-to-end:

    * W przypadku zasobów wideo w ramach dynamicznej ochrony drm w usłudze Media Services, z uwierzytelnianiem tokenów i JWT generowanym przez usługę Azure AD, musisz się zalogować.

Informacje na temat aplikacji internetowej odtwarzacza i jej logowania można znaleźć na [tej stronie internetowej](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Logowanie użytkowników
Aby przetestować zintegrowany system DRM typu end-to-end, musisz utworzyć lub dodać konto.

Jakie konto?

Mimo że platforma Azure pierwotnie zezwalała na dostęp tylko przez użytkowników kont Microsoft, dostęp jest teraz dozwolony przez użytkowników z obu systemów. Wszystkie właściwości platformy Azure ufają teraz usłudze Azure AD w celu uwierzytelniania, a usługa Azure AD uwierzytelnia użytkowników organizacji. Utworzono relację federacyjny, w której usługa Azure AD ufa systemowi tożsamości konsumenta konta Microsoft w celu uwierzytelnienia użytkowników konsumenckich. W rezultacie usługa Azure AD może uwierzytelniać konta Microsoft gościa, a także natywne konta usługi Azure AD.

Ponieważ usługa Azure AD ufa domenie konta Microsoft, można dodać dowolne konta z dowolnej z następujących domen do niestandardowej dzierżawy usługi Azure AD i używać konta do logowania się:

| **Nazwa domeny** | **Domain** |
| --- | --- |
| **Niestandardowa domena dzierżawy usługi Azure AD** |somename.onmicrosoft.com |
| **Domena korporacyjna** |microsoft.com |
| **Domena konta Microsoft** |outlook.com, live.com, hotmail.com |

Możesz skontaktować się z dowolnym z autorów, aby mieć konto utworzone lub dodane dla Ciebie.

Poniższe zrzuty ekranu pokazują różne strony logowania używane przez różne konta domeny:

**Niestandardowe konto domeny dzierżawy usługi Azure AD:** dostosowana strona logowania niestandardowej domeny dzierżawy usługi Azure AD.

![Niestandardowe konto dzierżawy usługi Azure AD — jedno](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Konto domeny Microsoft z kartą inteligentną**: Strona logowania dostosowana przez firmowy dział IT firmy Microsoft z uwierzytelnianiem dwuskładnikowym.

![Niestandardowe konto dzierżawy usługi Azure AD — dwa](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Konto Microsoft**: Strona logowania konta Microsoft dla konsumentów.

![Niestandardowe konto dzierżawy usługi Azure AD trzy](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Używanie zaszyfrowanych rozszerzeń multimediów dla playready

W nowoczesnej przeglądarce z rozszerzeń multimediów szyfrowanych (EME) dla obsługi PlayReady, takich jak Internet Explorer 11 w systemie Windows 8.1 lub nowszym i przeglądarka Microsoft Edge w systemie Windows 10, PlayReady jest podstawowym DRM dla EME.

![Korzystanie z EME dla PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

Ciemny obszar odtwarzacza jest, ponieważ ochrona PlayReady uniemożliwia przechwytywanie ekranu chronionego wideo.

Na poniższym zrzucie ekranu przedstawiono wtyczki odtwarzacza i obsługę programu Microsoft Security Essentials (MSE)/EME:

![Wtyczki odtwarzacza do PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

EME w programie Microsoft Edge i Internet Explorer 11 w systemie Windows 10 umożliwia wywoływanie [playready SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) na urządzeniach z systemem Windows 10, które go obsługują. PlayReady SL3000 odblokowuje przepływ ulepszonej zawartości premium (4K, HDR) i nowych modeli dostarczania treści (dla ulepszonych treści).

Aby skupić się na urządzeniach z systemem Windows, PlayReady jest jedynym drm w sprzęcie dostępnym na urządzeniach z systemem Windows (PlayReady SL3000). Usługa przesyłania strumieniowego może korzystać z funkcji PlayReady za pośrednictwem technologii EME lub aplikacji Universal Windows Platform i oferować wyższą jakość wideo przy użyciu funkcji PlayReady SL3000 niż innej pamięci DRM. Zazwyczaj zawartość do 2K przepływa przez Chrome lub Firefox, a zawartość do 4K przepływa przez microsoft edge/Internet Explorer 11 lub aplikację Uniwersalnej platformy Windows na tym samym urządzeniu. Kwota zależy od ustawień usługi i implementacji.

#### <a name="use-eme-for-widevine"></a>Użyj EME dla Widevine

W nowoczesnej przeglądarce z obsługą EME / Widevine, takich jak Chrome 41 + w systemie Windows 10, Windows 8.1, Mac OSX Yosemite i Chrome na Androidzie 4.4.4, Google Widevine jest DRM za EME.

![Użyj EME dla Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine nie uniemożliwia przechwytywania ekranu chronionego wideo.

![Wtyczki odtwarzacza dla Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Korzystanie z eme dla FairPlay

Podobnie możesz przetestować zawartość chroniona fairplay w tym odtwarzaczu testowym w przeglądarce Safari w systemie macOS lub iOS 11.2 lub nowszym.

Upewnij się, że umieścić "FairPlay" jako protectionInfo.type i umieścić w odpowiednim adresie URL certyfikatu aplikacji w FPS AC Path (FairPlay Streaming Application Certificate Path).

### <a name="unentitled-users"></a>Użytkownicy bez tytułu

Jeśli użytkownik nie jest członkiem grupy "Uprawnieni użytkownicy", użytkownik nie przejdzie kontroli uprawnień. Usługa licencji multi-DRM następnie odmawia wydania żądanej licencji, jak pokazano. Szczegółowy opis to "Licencja nabycia nie powiodła się", który jest zaprojektowany.

![Użytkownicy bez tytułu](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Uruchamianie niestandardowej usługi tokenu zabezpieczającego

Po uruchomieniu niestandardowego STS, JWT jest wystawiany przez niestandardowe STS przy użyciu klucza symetrycznego lub asymetrycznego.

Poniższy zrzut ekranu przedstawia scenariusz, który używa klucza symetrycznego (za pomocą Chrome):

![Niestandardowy STS z kluczem symetrycznym](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

Poniższy zrzut ekranu przedstawia scenariusz, który używa klucza asymetrycznego za pośrednictwem certyfikatu X509 (przy użyciu nowoczesnej przeglądarki firmy Microsoft):

![Niestandardowy STS z kluczem asymetrycznym](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

W obu poprzednich przypadkach uwierzytelnianie użytkownika pozostaje takie samo. Odbywa się za pośrednictwem usługi Azure AD. Jedyną różnicą jest to, że JWTs są wydawane przez niestandardowe STS zamiast usługi Azure AD. Podczas konfigurowania dynamicznej ochrony CENC ograniczenie usługi dostarczania licencji określa typ JWT, klucz symetryczny lub asymetryczny.

## <a name="next-steps"></a>Następne kroki

* [Często zadawane pytania](frequently-asked-questions.md)
* [Omówienie ochrony zawartości](content-protection-overview.md)
* [Chroń swoje treści za pomocą drm](protect-with-drm.md)
