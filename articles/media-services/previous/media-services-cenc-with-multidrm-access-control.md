---
title: Projektowanie systemu ochrony zawartości z kontrolą dostępu przy użyciu usługi Azure Media Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak uzyskać licencję na zestaw Microsoft Smooth Streaming Client Porting Kit.
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
ms.openlocfilehash: 68f42aa13288c2416257f3ba6c0b6072c1572977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162994"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Projektowanie systemu ochrony zawartości z kontrolą dostępu przy użyciu usługi Azure Media Services 

## <a name="overview"></a>Omówienie

Projektowanie i tworzenie podsystemu zarządzania prawami cyfrowymi (DRM) dla rozwiązania ott (over-the-top) lub online streaming jest złożonym zadaniem. Operatorzy/dostawcy wideo online zazwyczaj zlecają to zadanie wyspecjalizowanym dostawcom usług DRM. Celem tego dokumentu jest przedstawienie projektu referencyjnego i implementacji kompleksowego podsystemu DRM w rozwiązaniu OTT lub do przesyłania strumieniowego online.

Docelowymi czytnikami tego dokumentu są inżynierowie, którzy pracują w podsystemach DRM OTT lub rozwiązaniach online do przesyłania strumieniowego/wieloekranowych lub czytnikach zainteresowanych podsystemami DRM. Założenie jest takie, że czytelnicy są zaznajomieni z co najmniej jedną z technologii DRM dostępnych na rynku, takich jak PlayReady, Widevine, FairPlay lub Adobe Access.

W tej dyskusji drm, możemy również uwzględnić wspólne szyfrowanie (CENC) z multi-DRM. Głównym trendem w streamingu online i branży OTT jest wykorzystanie CENC z wielorodzimymi DRM na różnych platformach klienckich. Ten trend jest przejściem od poprzedniego, który używał pojedynczego drm i jego sdk klienta dla różnych platform klienckich. W przypadku korzystania z cenc z wielonatywną drm, zarówno PlayReady i Widevine są szyfrowane zgodnie ze [specyfikacją Common Encryption (ISO/IEC 23001-7 CENC).](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/)

Zalety CENC z multi-DRM jest to, że:

* Zmniejsza koszty szyfrowania, ponieważ pojedynczy proces szyfrowania jest używany do kierowania różnych platform z jego natywnych drms.
* Zmniejsza koszty zarządzania zaszyfrowanymi zasobami, ponieważ potrzebna jest tylko jedna kopia zaszyfrowanych zasobów.
* Eliminuje koszty licencjonowania klienta DRM, ponieważ natywny klient DRM jest zwykle wolny na swojej natywnej platformie.

Microsoft jest aktywnym promotorem DASH i CENC wraz z kilkoma głównymi graczami w branży. Usługa Azure Media Services zapewnia obsługę dash i CENC. Aby zapoznać się z najnowszymi ogłoszeniami, zobacz następujące blogi:

*  [Powiadomienie o usługach dostarczania licencji Google Widevine w usłudze Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Usługa Azure Media Services dodaje opakowanie Google Widevine do dostarczania strumienia multi-DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>Cele artykułu

Celem tego artykułu są:

* Podaj projekt referencyjny podsystemu DRM, który używa CENC z wieloma DRM.
* Podaj implementację odwołania na platformie Azure/Media Services.
* Omów niektóre tematy związane z projektowaniem i implementacją.

W artykule termin "multi-DRM" obejmuje następujące produkty:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

W poniższej tabeli podsumowano natywną platformę/natywną aplikację i przeglądarki obsługiwane przez każdą drm.

| **Klient platforma** | **Natywna obsługa drm** | **Przeglądarka/aplikacja** | **Formaty przesyłania strumieniowego** |
| --- | --- | --- | --- |
| **Inteligentne telewizory, operatorzy STBs, OTT STBs** |PlayReady przede wszystkim i/lub Widevine i/lub inne |Linux, Opera, WebKit, inne |Różne formaty |
| **Urządzenia z systemem Windows 10 (komputery z systemem Windows, tablety z systemem Windows, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Platforma uniwersalna systemu Windows |DASH (dla HLS, PlayReady nie jest obsługiwany)<br/><br/>DASH, Smooth Streaming (dla HLS, PlayReady nie jest obsługiwany) |
| **Urządzenia z systemem Android (telefon, tablet, telewizor)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), klienci systemu OS X i Apple TV** |FairPlay |Safari 8+/EME |HLS |

Biorąc pod uwagę bieżący stan wdrożenia dla każdego drm, usługa zazwyczaj chce zaimplementować dwa lub trzy drms, aby upewnić się, że adres wszystkich typów punktów końcowych w najlepszy sposób.

Istnieje kompromis między złożonością logiki usługi i złożoności po stronie klienta, aby osiągnąć pewien poziom doświadczenia użytkownika na różnych klientach.

Aby dokonać wyboru, pamiętaj:

* PlayReady jest natywnie realizowane w każdym urządzeniu z systemem Windows, na niektórych urządzeniach z systemem Android i dostępne za pośrednictwem oprogramowania SDK na praktycznie każdej platformie.
* Widevine jest natywnie zaimplementowany na każdym urządzeniu z Androidem, w Chrome i na niektórych innych urządzeniach.
* Program FairPlay jest dostępny tylko na klientach z systemem iOS i Mac OS lub za pośrednictwem programu iTunes.

Istnieją dwie opcje dla typowego multi-DRM:

* PlayReady i Widevine
* PlayReady, Widevine i FairPlay

## <a name="a-reference-design"></a>Projekt referencyjny
W tej sekcji przedstawiono projekt odniesienia, który jest niezależny od technologii używanych do jego zaimplementowania.

Podsystem DRM może zawierać następujące składniki:

* Zarządzanie kluczami
* Opakowania DRM
* Dostarczanie licencji DRM
* Sprawdzanie uprawnień
* Uwierzytelnianie/autoryzacja
* Odtwarzacz
* Sieć pochodzenia/dostarczania zawartości (CDN)

Na poniższym diagramie przedstawiono interakcję wysokiego poziomu między składnikami w podsystemie DRM:

![Podsystem DRM z CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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
| **Usługa tokenu zabezpieczającego (STS)** |Azure AD |
| **Przepływ pracy ochrony DRM** |Dynamiczna ochrona usługi Media Services |
| **Dostarczanie licencji DRM** |* Dostarczanie licencji Usługi Media Services (PlayReady, Widevine, FairPlay) <br/>* Serwer licencji Axinom <br/>* Niestandardowy serwer licencji PlayReady |
| **Origin** |Punkt końcowy przesyłania strumieniowego usług Media Services |
| **Zarządzanie kluczami** |Nie jest potrzebne do implementacji referencyjnej |
| **Zarządzanie zawartością** |Aplikacja konsoli języka C# |

Innymi słowy zarówno IDP, jak i STS są używane z usługą Azure AD. [Interfejs API programu Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) jest używany dla odtwarzacza. Usługi multimedialne i odtwarzacz multimedialny obsługują program DASH i CENC z wieloma drmami.

Na poniższym diagramie przedstawiono ogólną strukturę i przepływ z poprzednim mapowaniem technologii:

![CENC w serwisie medialnym](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Aby skonfigurować dynamiczne szyfrowanie CENC, narzędzie do zarządzania zawartością korzysta z następujących danych wejściowych:

* Otwieranie zawartości
* Klucz zawartości z generowania klucza/zarządzania
* Adresy URL pozyskiwania licencji
* Lista informacji z usługi Azure AD

Oto dane wyjściowe narzędzia do zarządzania treścią:

* ContentKeyAuthorizationPolicy zawiera specyfikację, w jaki sposób dostarczanie licencji weryfikuje specyfikacje licencji JSON Web Token (JWT) i DRM.
* AssetDeliveryPolicy zawiera specyfikacje dotyczące formatu przesyłania strumieniowego, ochrony DRM i adresów URL pozyskiwania licencji.

Oto przepływ podczas wykonywania:

* Po uwierzytelnieniu użytkownika jest generowany JWT.
* Jednym z oświadczeń zawartych w JWT jest oświadczenie grupy, które zawiera identyfikator obiektu grupy EntitledUserGroup. To roszczenie służy do przekazywania kontroli uprawnień.
* Gracz pobiera manifest klienta zawartości chronionej cenc i identyfikuje następujące elementy:
   * Identyfikator klucza.
   * Zawartość jest chroniona przez CENC.
   * Adresy URL pozyskiwania licencji.
* Odtwarzacz składa żądanie nabycia licencji na podstawie obsługiwanej przeglądarki/DRM. W żądaniu nabycia licencji przesyłany jest również identyfikator klucza i JWT. Usługa dostarczania licencji weryfikuje JWT i oświadczenia zawarte przed jej wydawaniem potrzebnej licencji.

## <a name="implementation"></a>Wdrażanie
### <a name="implementation-procedures"></a>Procedury wdrożeniowe
Implementacja obejmuje następujące kroki:

1. Przygotowanie zasobów testowych. Kodowanie/pakiet wideo testowe do wielosadłowej pofragmentowanej mp4 w umiaźnym umio. Ten zasób *nie* jest chroniony przez drm. Ochrona DRM odbywa się później dzięki dynamicznej ochronie.

2. Utwórz identyfikator klucza i klucz zawartości (opcjonalnie z inicjatora klucza). W tym przypadku system zarządzania kluczami nie jest potrzebny, ponieważ tylko jeden identyfikator klucza i klucz zawartości są wymagane dla kilku zasobów testowych.

3. Użyj interfejsu API usługi Media Services, aby skonfigurować usługi dostarczania licencji wielu DRM dla zasobu testowego. Jeśli używasz niestandardowych serwerów licencji przez firmę lub dostawców firmy zamiast usług licencyjnych w umiań Programu Media Services, możesz pominąć ten krok. Adresy URL pozyskiwania licencji można określić w kroku podczas konfigurowania dostarczania licencji. Interfejs API usługi Media Services jest potrzebny do określenia niektórych szczegółowych konfiguracji, takich jak ograniczenia zasad autoryzacji i szablony odpowiedzi licencji dla różnych usług licencji DRM. W tej chwili witryny Azure portal nie zapewnia interfejsu użytkownika dla tej konfiguracji. Aby uzyskać informacje na poziomie interfejsu API i przykładowy kod, zobacz [Używanie funkcji PlayReady i/lub Widevine dynamic common encryption](media-services-protect-with-playready-widevine.md).

4. Użyj interfejsu API usługi Media Services, aby skonfigurować zasady dostarczania zasobów dla zasobu testowego. Aby uzyskać informacje na poziomie interfejsu API i przykładowy kod, zobacz [Używanie funkcji PlayReady i/lub Widevine dynamic common encryption](media-services-protect-with-playready-widevine.md).

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

* Informacje o deweloperach można znaleźć w [przewodniku dla deweloperów usługi Azure Active Directory](../../active-directory/azuread-dev/v1-overview.md).
* Informacje o administratorze można znaleźć w obszarze [Administrowanie katalogiem dzierżawy usługi Azure AD](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Niektóre kwestie związane z wdrażaniem
Skorzystaj z następujących informacji dotyczących rozwiązywania problemów, aby uzyskać pomoc dotyczącą problemów z implementacją.

* Adres URL wystawcy musi kończyć się na "/". Odbiorcy muszą być identyfikatorem klienta aplikacji odtwarzacza. Ponadto dodaj "/" na końcu adresu URL wystawcy.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    W [dekoderze JWT](http://jwt.calebb.net/)widać **aud** i **iss**, jak pokazano w JWT:

    ![Jwt](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Dodaj uprawnienia do aplikacji w usłudze Azure AD na karcie **Konfigurowanie** aplikacji. Uprawnienia są wymagane dla każdej aplikacji, zarówno wersji lokalnych, jak i wdrożonych.

    ![Uprawnienia](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

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

## <a name="additional-topics-for-implementation"></a>Dodatkowe tematy do wdrożenia
W tej sekcji omówiono kilka dodatkowych tematów w projektowaniu i implementacji.

### <a name="http-or-https"></a>HTTP lub HTTPS?
Aplikacja odtwarzacza ASP.NET MVC musi obsługiwać następujące elementy:

* Uwierzytelnianie użytkowników za pośrednictwem usługi Azure AD, która jest w obszarze HTTPS.
* Wymiana JWT między klientem a usługą Azure AD, która jest w obszarze HTTPS.
* Nabycie licencji DRM przez klienta, które musi być w obszarze HTTPS, jeśli dostarczanie licencji jest dostarczane przez program Media Services. Pakiet produktów PlayReady nie upoważnia protokołu HTTPS do dostarczania licencji. Jeśli serwer licencji PlayReady znajduje się poza usługą Media Services, można użyć protokołu HTTP lub HTTPS.

Aplikacja ASP.NET player używa protokołu HTTPS jako najlepszego rozwiązania, więc program Media Player znajduje się na stronie https. Jednak protokół HTTP jest preferowany do przesyłania strumieniowego, więc należy wziąć pod uwagę problem zawartości mieszanej.

* Przeglądarka nie zezwala na zawartość mieszaną. Ale wtyczki, takie jak Silverlight i wtyczka OSMF dla smooth i DASH pozwalają na to. Zawartość mieszana jest problemem bezpieczeństwa ze względu na zagrożenie możliwością wstrzykiwania złośliwego języka JavaScript, co może spowodować ryzyko naruszenia danych klientów. Przeglądarki domyślnie blokują tę funkcję. Jedynym sposobem obejść to jest po stronie serwera (pochodzenia), umożliwiając wszystkie domeny (niezależnie od HTTPS lub HTTP). To chyba też nie jest dobry pomysł.
* Unikaj mieszanej zawartości. Zarówno aplikacja odtwarzacza, jak i odtwarzacz multimedialny powinny używać protokołu HTTP lub HTTPS. Podczas odtwarzania zawartości mieszanej technologia silverlightSS wymaga wyczyszczenie ostrzeżenia o zawartości mieszanej. Technologia flashSS obsługuje zawartość mieszaną bez ostrzeżenia o zawartości mieszanej.
* Jeśli punkt końcowy przesyłania strumieniowego został utworzony przed sierpniem 2014 r., nie będzie obsługiwać protokołu HTTPS. W takim przypadku należy utworzyć i używać nowego punktu końcowego przesyłania strumieniowego dla protokołu HTTPS.

W implementacji odwołania dla zawartości chronionej przez DRM zarówno aplikacji, jak i przesyłania strumieniowego są w obszarze HTTPS. W przypadku otwartej zawartości odtwarzacz nie potrzebuje uwierzytelniania ani licencji, dzięki czemu można użyć protokołu HTTP lub HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Przerzucie klucza podpisywania usługi Azure Active Directory
Podpisywanie klucza rollover jest ważnym punktem, aby wziąć pod uwagę w implementacji. Jeśli go zignorujesz, gotowy system ostatecznie przestanie działać całkowicie, najwyżej w ciągu sześciu tygodni.

Usługa Azure AD używa standardów branżowych do ustanawiania zaufania między sobą a aplikacjami korzystającymi z usługi Azure AD. W szczególności usługa Azure AD używa klucza podpisywania, który składa się z pary kluczy publicznych i prywatnych. Gdy usługa Azure AD tworzy token zabezpieczający, który zawiera informacje o użytkowniku, jest podpisany przez usługę Azure AD za pomocą klucza prywatnego, zanim zostanie odesłany do aplikacji. Aby sprawdzić, czy token jest prawidłowy i pochodzi z usługi Azure AD, aplikacja musi sprawdzić poprawność podpisu tokenu. Aplikacja używa klucza publicznego udostępniane przez usługę Azure AD, który jest zawarty w dokumencie metadanych federacji dzierżawy. Ten klucz publiczny i klucz podpisywania, z którego pochodzi, jest taki sam, który jest używany dla wszystkich dzierżaw w usłudze Azure AD.

Aby uzyskać więcej informacji na temat przerzucenia kluczy usługi Azure AD, zobacz [Ważne informacje dotyczące podpisywania narzutu kluczy w usłudze Azure AD](../../active-directory/active-directory-signing-key-rollover.md).

Między [parą kluczy publiczno-prywatnych:](https://login.microsoftonline.com/common/discovery/keys/)

* Klucz prywatny jest używany przez usługę Azure AD do generowania JWT.
* Klucz publiczny jest używany przez aplikację, taką jak usługi dostarczania licencji DRM w umiaźnej usługi Media Services w celu weryfikacji JWT.

Ze względów bezpieczeństwa usługa Azure AD okresowo obraca certyfikat (co sześć tygodni). W przypadku naruszenia zabezpieczeń przerzucie klucza może nastąpić w dowolnym momencie. W związku z tym usługi dostarczania licencji w usłudze Media Services należy zaktualizować klucz publiczny używany jako usługi Azure AD obraca parę kluczy. W przeciwnym razie uwierzytelnianie tokenów w umiań w umiań i nie jest wystawiana żadna licencja.

Aby skonfigurować tę usługę, należy ustawić TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument podczas konfigurowania usług dostarczania licencji DRM.

Oto przepływ JWT:

* Usługa Azure AD wystawia narzędzie JWT z bieżącym kluczem prywatnym dla uwierzytelnionego użytkownika.
* Gdy gracz widzi CENC z zawartością chroniona przez wiele drm, najpierw lokalizuje JWT wydane przez usługę Azure AD.
* Odtwarzacz wysyła żądanie nabycia licencji z JWT do licencjonowania usług dostawy w programie Media Services.
* Usługi dostarczania licencji w usłudze Media Services używają bieżącego/prawidłowego klucza publicznego z usługi Azure AD do weryfikacji narzędzia JWT przed wydaniem licencji.

Usługi dostarczania licencji DRM zawsze sprawdzają, czy bieżący/prawidłowy klucz publiczny z usługi Azure AD. Klucz publiczny przedstawiony przez usługę Azure AD jest kluczem używanym do weryfikacji JWT wystawionego przez usługę Azure AD.

Co zrobić, jeśli przelanie klucza nastąpi po wygenerowaniu usługi Azure AD JWT, ale przed wysłaniem JWT przez graczy do usług dostarczania licencji DRM w usłudze Media Services w celu weryfikacji?

Ponieważ klucz można przewracać w dowolnym momencie, więcej niż jeden prawidłowy klucz publiczny jest zawsze dostępny w dokumencie metadanych federacji. Dostarczanie licencji usługi Media Services może używać dowolnego klucza określonego w dokumencie. Ponieważ jeden klucz może być przewinięty wkrótce, inny może być jego wymiana, i tak dalej.

### <a name="where-is-the-access-token"></a>Gdzie jest token dostępu?
Jeśli spojrzysz na to, jak aplikacja sieci web wywołuje aplikację interfejsu API w obszarze [Tożsamość aplikacji z poświadczeniem klienta OAuth 2.0,](../../active-directory/azuread-dev/web-api.md)przepływ uwierzytelniania jest następujący:

* Użytkownik loguje się do usługi Azure AD w aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [przeglądarka sieci Web do aplikacji sieci Web](../../active-directory/azuread-dev/web-app.md).
* Punkt końcowy autoryzacji usługi Azure AD przekierowuje agenta użytkownika z powrotem do aplikacji klienckiej z kodem autoryzacji. Agent użytkownika zwraca kod autoryzacji do identyfikatora URI przekierowania aplikacji klienckiej.
* Aplikacja sieci web musi uzyskać token dostępu, dzięki czemu można uwierzytelnić do internetowego interfejsu API i pobrać żądany zasób. Sprawia, że żądanie do punktu końcowego tokenu usługi Azure AD i zapewnia poświadczenia, identyfikator klienta i identyfikator aplikacji interfejsu internetowego identyfikator URI. Przedstawia kod autoryzacji, aby udowodnić, że użytkownik wyraził zgodę.
* Usługa Azure AD uwierzytelnia aplikację i zwraca token dostępu JWT, który jest używany do wywoływania internetowego interfejsu API.
* Za pośrednictwem protokołu HTTPS aplikacja sieci web używa zwróconego tokenu dostępu JWT do dodania ciągu JWT z oznaczeniem "Nośnik" w nagłówku "Autoryzacja" żądania do internetowego interfejsu API. Interfejs API sieci web następnie sprawdza poprawność JWT. Jeśli sprawdzanie poprawności zakończy się pomyślnie, zwraca żądany zasób.

W tym przepływie tożsamości aplikacji internetowy interfejs API ufa, że aplikacja sieci web uwierzytelniła użytkownika. Z tego powodu ten wzorzec jest nazywany zaufanym podsystemem. [Diagram przepływu autoryzacji](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) opisuje, jak działa przepływ autoryzacji-kodu-udzielić.

Nabycie licencji z ograniczeniem tokenu odbywa się z tym samym wzorcem zaufanego podsystemu. Usługa dostarczania licencji w programie Media Services jest zasobem interfejsu API sieci Web lub "zasobem zaplecza", do który aplikacja sieci web musi uzyskać dostęp. Więc gdzie jest token dostępu?

Token dostępu jest uzyskiwany z usługi Azure AD. Po pomyślnym uwierzytelnieniu użytkownika zwracany jest kod autoryzacji. Kod autoryzacji jest następnie używany, wraz z identyfikatorem klienta i kluczem aplikacji, do wymiany tokenu dostępu. Token dostępu jest używany do uzyskiwania dostępu do aplikacji "wskaźnik", która wskazuje lub reprezentuje usługę dostarczania licencji usługi Media Services.

Aby zarejestrować i skonfigurować aplikację wskaźnika w usłudze Azure AD, należy wykonać następujące kroki:

1. W dzierżawie usługi Azure AD:

   * Dodaj aplikację (zasób) z adresem URL logowania https://[resource_name].azurewebsites.net/. 
   * Dodaj identyfikator aplikacji z adresem URL https://[aad_tenant_name].onmicrosoft.com/[resource_name].

2. Dodaj nowy klucz dla aplikacji zasobu.

3. Zaktualizuj plik manifestu aplikacji, tak aby właściwość groupMembershipClaims ma wartość "groupMembershipClaims": "Wszystkie".

4. W aplikacji usługi Azure AD, która wskazuje na aplikację internetową gracza, w sekcji **Uprawnienia do innych aplikacji**, dodaj aplikację zasobów, która została dodana w kroku 1. W obszarze **Uprawnienia delegowane**wybierz pozycję **Dostęp [resource_name]**. Ta opcja daje aplikacji sieci web uprawnienia do tworzenia tokenów dostępu, które uzyskują dostęp do aplikacji zasobu. Zrób to dla lokalnej i wdrożonej wersji aplikacji sieci web, jeśli opracujesz za pomocą programu Visual Studio i aplikacji sieci Web platformy Azure.

JWT wydane przez usługę Azure AD jest token dostępu używany do dostępu do zasobu wskaźnika.

### <a name="what-about-live-streaming"></a>A co z transmisją na żywo?
Poprzednia dyskusja koncentrowała się na aktywach na żądanie. A co z transmisją na żywo?

Można użyć dokładnie tego samego projektu i implementacji do ochrony przesyłania strumieniowego na żywo w umiaście w umiaście, traktując zasób skojarzony z programem jako zasób VOD.

W szczególności, aby zrobić transmisję na żywo w umiaście, należy utworzyć kanał, a następnie utworzyć program pod kanałem. Aby utworzyć program, należy utworzyć zasób zawierający archiwum na żywo dla programu. Aby zapewnić CENC ochronę multi-DRM zawartości na żywo, zastosuj tę samą konfigurację/przetwarzanie do zasobu, tak jakby był to zasób VOD przed uruchomieniem programu.

### <a name="what-about-license-servers-outside-media-services"></a>Co z serwerami licencji poza usługą Media Services?
Często klienci inwestowali w farmę serwerów licencji we własnym centrum danych lub w centrum obsługiwanym przez dostawców usług DRM. Dzięki programowi Media Services Content Protection można pracować w trybie hybrydowym. Zawartość może być hostowana i dynamicznie chroniona w programie Media Services, podczas gdy licencje DRM są dostarczane przez serwery spoza usługi Media Services. W takim przypadku należy wziąć pod uwagę następujące zmiany:

* Sts musi wystawiać tokeny, które są dopuszczalne i mogą być weryfikowane przez farmę serwerów licencji. Na przykład serwery licencji Widevine dostarczone przez firmę Axinom wymagają określonego certyfikatu JWT zawierającego komunikat o uprawnieniach. W związku z tym należy mieć STS do wydania takiego JWT. Aby uzyskać informacje na temat tego typu implementacji, przejdź do [Centrum dokumentacji platformy Azure](https://azure.microsoft.com/documentation/) i zobacz Używanie systemu [Axinom do dostarczania licencji Widevine do usługi Azure Media Services.](media-services-axinom-integration.md)
* Nie trzeba już konfigurować usługi dostarczania licencji (ContentKeyAuthorizationPolicy) w programie Media Services. Podczas konfigurowania assetdeliveryPolicy należy podać adresy URL nabycia licencji (dla PlayReady, Widevine i FairPlay) podczas konfigurowania AssetDeliveryPolicy w celu skonfigurowania cenc z wieloma drm.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Co zrobić, jeśli chcę używać niestandardowego STS?
Klient może użyć niestandardowego sts do dostarczania JWTs. Powody to:

* IDP używane przez klienta nie obsługuje STS. W takim przypadku opcja może być niestandardowa sts.
* Klient może potrzebować bardziej elastycznej lub ściślejszej kontroli, aby zintegrować usługę STS z systemem rozliczeniowym subskrybenta klienta. Na przykład operator MVPD może oferować wiele pakietów subskrybentów OTT, takich jak premium, podstawowe i sportowe. Operator może chcieć dopasować oświadczenia w tokenie z pakietem subskrybenta, dzięki czemu tylko zawartość w określonym pakiecie są udostępniane. W takim przypadku niestandardowy STS zapewnia niezbędną elastyczność i kontrolę.

Podczas korzystania z niestandardowego STS należy wyw.

* Podczas konfigurowania usługi dostarczania licencji dla zasobu należy określić klucz zabezpieczeń używany do weryfikacji przez niestandardowy sts zamiast bieżącego klucza z usługi Azure AD. (Więcej szczegółów po.) 
* Podczas generowania tokenu JTW klucz zabezpieczeń jest określony zamiast klucza prywatnego bieżącego certyfikatu X509 w usłudze Azure AD.

Istnieją dwa typy kluczy zabezpieczeń:

* Klucz symetryczny: Ten sam klucz jest używany do generowania i weryfikacji JWT.
* Klucz asymetryczny: Para kluczy publiczno-prywatnych w certyfikacie X509 jest używana z kluczem prywatnym do szyfrowania/generowania JWT i przy użyciu klucza publicznego w celu zweryfikowania tokenu.

> [!NOTE]
> Jeśli używasz .NET Framework/C# jako platformy programistycznej, certyfikat X509 używany dla asymetrycznego klucza zabezpieczeń musi mieć długość klucza co najmniej 2048. Jest to wymaganie klasy System.IdentityModel.Tokens.X509AsymmetricSecurityKey w .NET Framework. W przeciwnym razie zgłaszany jest następujący wyjątek:
> 
> IDX10630: "System.IdentityModel.Tokens.X509AsymmetricSecurityKey" do podpisywania nie może być mniejszy niż bity "2048".

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

![Niestandardowe konto dzierżawy usługi Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Konto domeny Microsoft z kartą inteligentną**: Strona logowania dostosowana przez firmowy dział IT firmy Microsoft z uwierzytelnianiem dwuskładnikowym.

![Niestandardowe konto dzierżawy usługi Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Konto Microsoft**: Strona logowania konta Microsoft dla konsumentów.

![Niestandardowe konto dzierżawy usługi Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Używanie zaszyfrowanych rozszerzeń multimediów dla playready
W nowoczesnej przeglądarce z rozszerzeń multimediów szyfrowanych (EME) dla obsługi PlayReady, takich jak Internet Explorer 11 w systemie Windows 8.1 lub nowszym i przeglądarka Microsoft Edge w systemie Windows 10, PlayReady jest podstawowym DRM dla EME.

![Korzystanie z EME dla PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Ciemny obszar odtwarzacza jest, ponieważ ochrona PlayReady uniemożliwia przechwytywanie ekranu chronionego wideo.

Na poniższym zrzucie ekranu przedstawiono wtyczki odtwarzacza i obsługę programu Microsoft Security Essentials (MSE)/EME:

![Wtyczki odtwarzacza do PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME w programie Microsoft Edge i Internet Explorer 11 w systemie Windows 10 umożliwia wywoływanie [playready SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) na urządzeniach z systemem Windows 10, które go obsługują. PlayReady SL3000 odblokowuje przepływ ulepszonej zawartości premium (4K, HDR) i nowych modeli dostarczania treści (dla ulepszonych treści).

Aby skupić się na urządzeniach z systemem Windows, PlayReady jest jedynym drm w sprzęcie dostępnym na urządzeniach z systemem Windows (PlayReady SL3000). Usługa przesyłania strumieniowego może korzystać z funkcji PlayReady za pośrednictwem technologii EME lub aplikacji Universal Windows Platform i oferować wyższą jakość wideo przy użyciu funkcji PlayReady SL3000 niż innej pamięci DRM. Zazwyczaj zawartość do 2K przepływa przez Chrome lub Firefox, a zawartość do 4K przepływa przez microsoft edge/Internet Explorer 11 lub aplikację Uniwersalnej platformy Windows na tym samym urządzeniu. Kwota zależy od ustawień usługi i implementacji.

#### <a name="use-eme-for-widevine"></a>Użyj EME dla Widevine
W nowoczesnej przeglądarce z obsługą EME / Widevine, takich jak Chrome 41 + w systemie Windows 10, Windows 8.1, Mac OSX Yosemite i Chrome na Androidzie 4.4.4, Google Widevine jest DRM za EME.

![Użyj EME dla Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine nie uniemożliwia przechwytywania ekranu chronionego wideo.

![Wtyczki odtwarzacza dla Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Użytkownicy bez tytułu
Jeśli użytkownik nie jest członkiem grupy "Uprawnieni użytkownicy", użytkownik nie przejdzie kontroli uprawnień. Usługa licencji multi-DRM następnie odmawia wydania żądanej licencji, jak pokazano. Szczegółowy opis to "Licencja nabycia nie powiodła się", który jest zaprojektowany.

![Użytkownicy bez tytułu](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Uruchamianie niestandardowej usługi tokenu zabezpieczającego
Po uruchomieniu niestandardowego STS, JWT jest wystawiany przez niestandardowe STS przy użyciu klucza symetrycznego lub asymetrycznego.

Poniższy zrzut ekranu przedstawia scenariusz, który używa klucza symetrycznego (za pomocą Chrome):

![Niestandardowy STS z kluczem symetrycznym](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Poniższy zrzut ekranu przedstawia scenariusz, który używa klucza asymetrycznego za pośrednictwem certyfikatu X509 (przy użyciu nowoczesnej przeglądarki firmy Microsoft):

![Niestandardowy STS z kluczem asymetrycznym](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

W obu poprzednich przypadkach uwierzytelnianie użytkownika pozostaje takie samo. Odbywa się za pośrednictwem usługi Azure AD. Jedyną różnicą jest to, że JWTs są wydawane przez niestandardowe STS zamiast usługi Azure AD. Podczas konfigurowania dynamicznej ochrony CENC ograniczenie usługi dostarczania licencji określa typ JWT, klucz symetryczny lub asymetryczny.

## <a name="summary"></a>Podsumowanie

W tym dokumencie omówiono CENC z wielonatywnej drm i kontroli dostępu za pośrednictwem uwierzytelniania tokenu, jego projektu i jego implementacji przy użyciu platformy Azure, media services i media player.

* Zaprezentowano projekt referencyjny, który zawiera wszystkie niezbędne składniki w podsystemie DRM/CENC.
* Implementacja referencyjna została przedstawiona na platformie Azure, usłudze Media Services i programie Media Player.
* Omówiono również niektóre tematy bezpośrednio związane z projektowaniem i wdrażaniem.

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
