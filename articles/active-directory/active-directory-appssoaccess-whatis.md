---
title: Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory? | Microsoft Docs
description: Użyj usługi Azure Active Directory, aby włączyć logowanie jednokrotne na wszystkie aplikacje sieci web i SaaS, które są potrzebne dla firm.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/11/2017
ms.author: curtand
ms.reviewer: asmalser
ms.custom: it-pro
ms.openlocfilehash: 29aaf28e1565a5972671df4fc48ca5618e43ea83
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-application-access-and-single-sign-on-with-azure-active-directory"></a>Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?
Logowanie jednokrotne oznacza dostępowi do wszystkich aplikacji i zasobów potrzebnych do prowadzenia działalności, logując się tylko raz przy użyciu jednego konta użytkownika. Po zalogowaniu możesz uzyskać dostęp do wszystkich aplikacji bez konieczności uwierzytelnienia (na przykład wpisz hasło) po raz drugi.

W wielu organizacjach opierają się na oprogramowania jako aplikacje usługi (SaaS), takich jak Office 365, pole i Salesforce na produktywność użytkownika końcowego. W przeszłości personel działu informatycznego musi być indywidualnie tworzenie i aktualizowanie kont użytkowników w każdej aplikacji SaaS i użytkownicy mają do zapamiętania hasła dla każdej aplikacji SaaS.

Azure Active Directory rozszerza lokalnej usługi Active Directory do chmury, umożliwieniem użytkownikom korzystania swoje konta organizacyjne podstawowego nie tylko zalogować się na urządzeniach przyłączonych do domeny i zasobów firmy, ale również wszystkie sieci web i aplikacji SaaS wymagany dla ich zadania.

Więc nie tylko użytkowników nie trzeba zarządzać wiele zestawów nazw użytkowników i hasła, aplikacjach dostępu mogą być automatycznie udostępniane lub usuwane oparte na ich elementy członkowskie grupy organizacji i ich stan jako pracownika. Usługa Azure Active Directory wprowadza formantów Zarządzanie zabezpieczeniami i dostępem, umożliwiające centralne zarządzanie dostępem użytkowników do aplikacji SaaS.

Usługi Azure AD umożliwia łatwą integrację do wielu popularnych aplikacji SaaS współczesnych; Umożliwia zarządzanie tożsamościami i dostępem i umożliwia użytkownikom logowanie jednokrotne do aplikacji bezpośrednio, lub odnajdywania i uruchamiania ich z portalu, takich jak Office 365 lub panel dostępu usługi Azure AD.

Architektura integracji składa się z następujących czterech głównych bloków konstrukcyjnych:

* Logowanie jednokrotne umożliwia użytkownikom dostęp do aplikacji SaaS, ich oparte na swoje konta organizacyjne w usłudze Azure AD. Logowanie jednokrotne jest, co umożliwia użytkownikom uwierzytelnianie do aplikacji przy użyciu pojedynczego konta organizacyjnego.
* Inicjowanie obsługi użytkowników umożliwia użytkownika aprowizację i anulowanie obsługi do obiektu docelowego, który SaaS na podstawie zmian wprowadzonych w systemie Windows Server Active Directory i/lub Azure AD. Konto elastycznie to, co umożliwia użytkownikowi mieć autoryzację do korzystania z aplikacji, po uwierzytelnienia za pośrednictwem rejestracji jednokrotnej.
* Zarządzania dostępem do aplikacji scentralizowany dostęp do aplikacji pojedynczy punkt SaaS umożliwia portalu Azure i zarządzania, z możliwością delegować podejmowania decyzji na dostęp do aplikacji i zatwierdzeń innym osobom w organizacji
* Jednolite raportowanie i monitorowania aktywności użytkowników w usłudze Azure AD

## <a name="how-does-single-sign-on-with-azure-active-directory-work"></a>Jak działa logowanie jednokrotne z usługą Azure Active Directory?
Gdy "zalogowaniu użytkownika" do aplikacji, komputery przechodzą przez proces uwierzytelniania gdzie są one wymagane w celu potwierdzenia, że są one kto mówią, że są one. Bez rejestracji jednokrotnej jest to zazwyczaj wykonywane przez wprowadzenie hasła, które są przechowywane w aplikacji, a użytkownik musi znać hasło.

Usługi Azure AD obsługuje trzy różne sposoby, aby zalogować się do aplikacji:

* **Federacyjne logowanie jednokrotne** umożliwia aplikacjom przekierowanie do usługi Azure AD do uwierzytelniania użytkowników zamiast monitowanie o własnego hasła. Jest to obsługiwane w przypadku aplikacji obsługa protokołów, takich jak SAML 2.0, WS-Federation lub OpenID Connect, i jest najszerszym tryb rejestracji jednokrotnej.
* **Oparte na hasłach rejestracji jednokrotnej** umożliwia bezpiecznego magazynu haseł aplikacji i powtarzania przy użyciu rozszerzenia przeglądarki sieci web lub aplikacji mobilnej. Wykorzystuje istniejący proces logowania udostępniany przez aplikację, ale umożliwia administratorowi zarządzać hasłami i wymaga od użytkownika znać hasło.
* **Istniejące rejestracji jednokrotnej** umożliwia usłudze Azure AD korzystać z żadnych istniejących rejestracji jednokrotnej, który został skonfigurowany do aplikacji, ale umożliwia te aplikacje do odnosić się do portali panel dostępu usługi Office 365 lub Azure AD, a także umożliwia dodatkowe Raportowanie w usłudze Azure AD, gdy istnieje uruchamiania aplikacji.

Gdy użytkownik został uwierzytelniony przy użyciu aplikacji, muszą mieć rekord konta zainicjowane w aplikacji, która określa, że aplikacja gdzie istnieje uprawnienia i poziom dostępu są wewnątrz aplikacji. Inicjowanie obsługi administracyjnej z tego konta można albo automatycznie są wykonywane, lub może być wykonywane ręcznie przez administratora, zanim użytkownik podano dostępu rejestracji jednokrotnej.

 Więcej informacji na temat pojedynczego logowania jednokrotnego trybów i Inicjowanie obsługi poniżej.

### <a name="federated-single-sign-on"></a>Federacyjne logowanie jednokrotne
Federacyjne logowanie jednokrotne umożliwia użytkownikom można zostanie automatycznie zalogowany do aplikacji SaaS innych firm przez usługę Azure AD przy użyciu informacji o koncie użytkownika z usługi Azure AD w organizacji.

W tym scenariuszu gdy możesz już zostały zarejestrowane w usłudze Azure Active Directory i chcesz uzyskiwać dostęp do zasobów, które są kontrolowane przez innych firm aplikacji SaaS, federacyjnego eliminuje potrzebę stosowania można ponownie uwierzytelnić użytkownika.

Usługi Azure AD mogą obsługiwać federacyjne logowanie jednokrotne z aplikacjami, które obsługują SAML 2.0, WS-Federation, lub OpenID connect protokołów.

Zobacz też: [zarządzanie certyfikatami dla federacyjnych rejestracji jednokrotnej](active-directory-sso-certs.md)

### <a name="password-based-single-sign-on"></a>Logowanie jednokrotne oparte na hasłach
Konfigurowanie opartego na hasłach logowania jednokrotnego pozwala użytkownikom w organizacji automatycznie zalogowani do aplikacji SaaS innych firm przez usługę Azure AD przy użyciu informacji o koncie użytkownika z innych aplikacji SaaS. Po włączeniu tej funkcji usługi Azure AD zbiera i bezpiecznie przechowywane są informacje o koncie użytkownika i hasło powiązane.

Usługi Azure AD może obsługiwać opartego na hasłach rejestracji jednokrotnej dla dowolnego chmurowych aplikacji, który ma oparty na języku HTML strony logowania. Za pomocą wtyczki przeglądarki niestandardowych, automatyzuje użytkownika procesu logowania za pośrednictwem bezpiecznego pobierania poświadczeń aplikacji, takie jak nazwa użytkownika i hasło z katalogu usługi AAD i wejścia te poświadczenia aplikacji strony logowania dla użytkownik. Istnieją dwa przypadki użycia:

1. **Administrator zarządza poświadczeniami** — Administratorzy mogą tworzyć i zarządzać poświadczeń aplikacji i przypisz tych poświadczeń do użytkowników lub grupy, którzy potrzebują dostępu do aplikacji. W takich przypadkach użytkownik końcowy musi znać poświadczeń, ale nadal uzyskuje dostęp pojedynczego logowania do aplikacji po prostu, klikając go w ich panelu dostępu lub przy użyciu podanego łącza. Dzięki temu zarówno zarządzania cyklem życia poświadczeń przez administratora, a także ułatwienia dla użytkowników końcowych, zgodnie z którymi nie muszą pamiętać lub zarządzać hasłami specyficzny dla aplikacji. Poświadczenia są zaciemniona od użytkownika końcowego podczas automatycznego logowania; jednak jest technicznie wykrywalny przez użytkownika za pomocą narzędzia debugowania sieci web, a użytkownicy i Administratorzy należy stosować te same zasady zabezpieczeń tak, jakby poświadczenia były widoczne bezpośrednio przez użytkownika. Poświadczenia administratora są przydatne podczas tworzenia konta dostępu, który jest współużytkowane przez wielu użytkowników, takich jak mediami społecznościowymi lub aplikacji do udostępniania dokumentu.
2. **Użytkownik zarządza poświadczeniami** — Administratorzy mogą przypisywać aplikacje do użytkowników lub grup i umożliwić użytkownikom końcowym o wprowadzenie poświadczeń bezpośrednio na uzyskiwanie dostępu do aplikacji po raz pierwszy w ich panelu dostępu. Spowoduje to utworzenie udogodnienie dla użytkowników końcowych, zgodnie z którymi nie ma potrzeby stale wprowadzać haseł specyficzny dla aplikacji za każdym razem, ich dostęp do aplikacji. Użytkownicy mogą nadal zarządzać hasłami ich aktualizowania lub usuwania ich w razie potrzeby. Ten przypadek użycia mogą służyć jako kamieniem wykonywania krokowego administracyjne zarządzania poświadczeniami, zgodnie z którymi administrator może ustawić nowe poświadczenia dla aplikacji w przyszłości bez zmieniania obsługi dostępu do aplikacji przez użytkownika końcowego.

W obu przypadkach poświadczenia są przechowywane w zaszyfrowanej stanu w katalogu, a tylko są przekazywane za pośrednictwem protokołu HTTPS podczas automatycznego logowania. Za pomocą opartego na hasłach rejestracji jednokrotnej, usługi Azure AD oferuje wygodny dostępu rozwiązania do zarządzania tożsamościami dla aplikacji, które nie są w stanie obsłużyć protokoły federacji.

Na podstawie hasła logowania jednokrotnego polega na rozszerzenia przeglądarki, aby bezpiecznie uzyskać informacje specyficzne dla użytkownika i aplikacji z usługi Azure AD i zastosować je do usługi. Większość aplikacji SaaS innych firm, które są obsługiwane przez usługę Azure AD obsługuje tę funkcję.

Logowanie Jednokrotne opartego na hasłach można przeglądarki przez użytkownika końcowego:
* Internet Explorer 11 — w systemie Windows 7 lub nowszy
* Krawędź w systemie Windows 10 Anniversary Edition lub nowszy 
* Chrome — W systemie Windows 7 lub nowszy oraz System MacOS x lub nowszych
* Firefox 26.0 lub później — w systemie Windows XP z dodatkiem SP2 lub nowszy oraz w systemie Mac OS X 10,6 lub nowszy

### <a name="existing-single-sign-on"></a>Istniejące rejestracji jednokrotnej
Podczas konfigurowania rejestracji jednokrotnej dla aplikacji, Azure portal udostępnia trzecia opcja z "istniejącej rejestracji jednokrotnej". Ta opcja umożliwia po prostu administratorem, aby utworzyć łącze do aplikacji i umieść ją na panel dostępu dla wybranych użytkowników.

Na przykład w przypadku aplikacji, która jest skonfigurowana do uwierzytelniania użytkowników za pomocą Active Directory Federation Services 2.0, administrator może użyć opcji "istniejącej rejestracji jednokrotnej" Aby utworzyć łącze do niego w panelu dostępu. Gdy użytkownicy uzyskują dostęp do łącza, ich uwierzytelniania przy użyciu Active Directory Federation Services 2.0 lub dowolnego pojedynczego logowania jednokrotnego rozwiązania jest zapewniana przez aplikację.

### <a name="user-provisioning"></a>Inicjowanie obsługi użytkowników
Wybierz aplikacje usługi Azure AD umożliwia automatyczne użytkownika aprowizację i anulowanie obsługi kont w innych firm aplikacji SaaS w portalu zarządzania Azure przy użyciu informacji o tożsamości systemu Windows Server Active Directory lub Azure AD. Gdy użytkownik otrzymuje uprawnienia w usłudze Azure AD dla jednej z tych aplikacji, konta mogą być automatycznie tworzone (udostępnione) w celu aplikacji SaaS.

Po usunięciu użytkownika lub ich informacje zmian w usłudze Azure AD, te zmiany są również uwzględniane w aplikacji SaaS. Oznacza to, że zarządzanie cyklem życia tożsamości automatyczne konfigurowanie umożliwia administratorom kontrolowanie i podaj zautomatyzowaną aprowizację i anulowanie obsługi z poziomu aplikacji SaaS. W usłudze Azure AD ta Automatyzacja zarządzania cyklem życia tożsamości jest włączana przez Inicjowanie obsługi użytkowników.

Aby dowiedzieć się więcej, zobacz [automatyczne Inicjowanie obsługi użytkowników i anulowania zastrzeżenia do aplikacji SaaS](active-directory-saas-app-provisioning.md)

## <a name="get-started-with-the-azure-ad-application-gallery"></a>Rozpoczynanie pracy z galerią aplikacji usługi Azure AD
Chcesz zacząć? Do wdrażania rejestracji jednokrotnej między usługą Azure AD i aplikacji SaaS, które organizacja używa, postępuj zgodnie z poniższymi wskazówkami.

### <a name="using-the-azure-ad-application-gallery"></a>Za pomocą galerii aplikacji Azure AD
[Galerii aplikacji programu Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/) zawiera listę aplikacji, które są znane do obsługi formularza rejestracji jednokrotnej z usługą Azure Active Directory.

![Galerii online aplikacji Azure](media/active-directory-appssoaccess-whatis/onlineappgallery.png)

Poniżej przedstawiono kilka wskazówek do znajdowania aplikacji przez jakie funkcje, które obsługują:

* Usługi Azure AD obsługuje automatyczną aprowizację i anulowanie obsługi dla wszystkich aplikacji "Proponowanym" w [galerii aplikacji programu Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/).
* Wykaz aplikacji federacyjnych w szczególności obsługiwać federacyjnym logowaniem jednokrotnym przy użyciu protokołu, takich jak SAML, WS-Federation, lub OpenID Connect można znaleźć [tutaj](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx).

Po znalezieniu aplikacji, możesz rozpocząć pracę, wykonując instrukcje krok po kroku przedstawiony w galerii aplikacji i w portalu zarządzania Azure, aby włączyć logowanie jednokrotne.

### <a name="application-not-in-the-gallery"></a>Aplikacji nie znajduje się w galerii?
Jeśli aplikacja nie zostanie znaleziony w galerii aplikacji usługi Azure AD, masz następujące opcje:

* **Dodaj nieznajdujące się na liście aplikacji są za pomocą** -Użyj kategorię niestandardową w galerii aplikacji w portalu zarządzania Azure, łączenie nieznajdujące się na liście aplikacji korzystających z Twojej organizacji. Możesz dodać dowolnej aplikacji, który obsługuje SAML 2.0 jako aplikacji federacyjnych lub dowolnej aplikacji, która jest oparty na języku HTML strony logowania jako aplikację hasło logowania jednokrotnego. Aby uzyskać więcej informacji, zobacz ten artykuł w [Dodawanie własnych aplikacji](application-config-sso-how-to-configure-federated-sso-non-gallery.md).
* **Dodaj własną aplikację, tworzysz** — Jeśli aplikacja została zaprojektowana samodzielnie, postępuj zgodnie z wytycznymi w dokumentacji dewelopera usługi Azure AD do wdrożenia federacyjnego logowania jednokrotnego lub interfejsu API programu graph Inicjowanie obsługi przy użyciu usługi Azure AD. Aby uzyskać więcej informacji zobacz następujące zasoby:
  
  * [Scenariusze uwierzytelniania dla usługi Azure AD](active-directory-authentication-scenarios.md)
  * [https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)
* **Żądanie integracji aplikacji** -żądania pomocy technicznej dla aplikacji, należy za pomocą [forum opinii usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory/).

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Rozszerzenie usługi Active Directory w portalu Azure służy do konfigurowania aplikacji rejestracji jednokrotnej. Pierwszym krokiem musisz wybrać katalog z sekcji usługi Active Directory w portalu:

![][2]

Aby zarządzać aplikacji SaaS innych firm, można zmienić na karcie aplikacje w wybranym katalogu. Ten widok umożliwia administratorom:

* Dodaj nowe aplikacje z galerii Azure AD, a także aplikacje, które tworzysz
* Usuń zintegrowanych aplikacji
* Zarządzanie aplikacjami, które już zostały zintegrowana

Typowe zadania administracyjne dla aplikacji SaaS innych firm są:

* Włączanie rejestracji jednokrotnej z usługą Azure AD przy użyciu hasła logowania jednokrotnego lub, jeśli jest dostępna dla docelowej SaaS, federacyjnego logowania jednokrotnego
* Opcjonalnie włączenie użytkownika inicjowania obsługi administracyjnej dla użytkownika aprowizację i anulowanie obsługi (Zarządzanie cyklem życia tożsamości)
* Dla aplikacji, których inicjowanie obsługi użytkowników jest włączone Wybieranie użytkowników, którzy mają dostęp do tej aplikacji

Do galerii aplikacji, które obsługują federacyjnego logowania jednokrotnego Konfiguracja zwykle wymaga podania dodatkowe ustawienia konfiguracji, takich jak certyfikaty i metadanych utworzyć relację zaufania federacji między aplikacji innych firm i Azure AD. Kreator konfiguracji przeprowadzi Cię przez kolejne szczegóły i zapewnia łatwy dostęp do danych specyficznych dla aplikacji SaaS i instrukcje.

Do galerii aplikacji, które obsługują automatycznej aprowizacji użytkowników należy nadać kontu uprawnienia do zarządzania kont użytkownika w aplikacji SaaS usługi Azure AD. Co najmniej musisz podać poświadczenia usługi Azure AD musi być używane podczas uwierzytelniania w aplikacji docelowej. Określa, czy ustawienia konfiguracji dodatkowe muszą być podawane zależy od wymagań aplikacji.

## <a name="deploying-azure-ad-integrated-applications-to-users"></a>Wdrażanie usługi Azure AD zintegrowanych aplikacji dla użytkowników
Usługa Azure AD zapewnia kilka metod można dostosować do wdrożenia aplikacji dla użytkowników końcowych w organizacji:

* Panel dostępu usługi Azure AD
* Uruchamianie aplikacji usługi Office 365
* Bezpośrednie logowanie do aplikacji federacyjnych
* Linki bezpośrednie do federacyjnych, opartych na hasłach lub istniejących aplikacjach

Metody, które chcesz wdrożyć w organizacji jest uznania.

### <a name="azure-ad-access-panel"></a>Panel dostępu usługi Azure AD
Panelu dostępu pod adresem https://myapps.microsoft.com jest oparte na sieci web portalu, który umożliwia użytkownikowi końcowemu za pomocą konta organizacyjnego w usłudze Azure Active Directory, aby wyświetlić i uruchom aplikacje oparte na chmurze do których ma zostać udzielony dostęp przez administratora usługi Azure AD. Jeśli użytkownik końcowy z [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), można również korzystać z możliwości zarządzania grupami samoobsługi za pomocą panelu dostępu.

![Panel dostępu usługi Azure AD](media/active-directory-appssoaccess-whatis/azure-ad-access-panel.png)

Panel dostępu jest oddzielony od portalu Azure i nie wymaga użytkowników do subskrypcji platformy Azure lub subskrypcji usługi Office 365.

Aby uzyskać więcej informacji na panelu dostępu do usługi Azure AD, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

### <a name="office-365-application-launcher"></a>Uruchamianie aplikacji usługi Office 365
W przypadku organizacji, które zostały wdrożone usługi Office 365, aplikacje przypisane do użytkowników za pomocą usługi Azure AD są również wyświetlane w portalu usługi Office 365 w https://portal.office.com/myapps. To ułatwia wygodne dla użytkowników w organizacji, aby uruchomić swoje aplikacje bez konieczności użycia portalu drugi i jest zalecaną aplikację uruchamiania rozwiązania dla organizacji przy użyciu usługi Office 365.

![][4]

Aby uzyskać więcej informacji na temat uruchamiania aplikacji usługi Office 365, zobacz [mają aplikacji są wyświetlane na ekranie uruchamiania aplikacji usługi Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

### <a name="direct-sign-on-to-federated-apps"></a>Bezpośrednie logowanie do aplikacji federacyjnych
Najbardziej aplikacji federacyjnych obsługuje SAML 2.0, WS-Federation lub OpenID connect również pomocy technicznej przez użytkowników w aplikacji, a następnie pobrać zalogowany za pomocą usługi Azure AD przez automatyczne przekierowanie lub klikając łącze do logowania. Jest to nazywane dostawcy usług-inicjowane logowania jednokrotnego i najbardziej aplikacji federacyjnych w galerii aplikacji usługi Azure AD obsługuje tego (dokumentacji połączone z Kreatora konfiguracji rejestracji jednokrotnej aplikacji w portalu zarządzania Azure, aby uzyskać więcej informacji zobacz).

![][5]

### <a name="direct-sign-on-links-for-federated-password-based-or-existing-apps"></a>Linki bezpośrednie logowania jednokrotnego do aplikacji federacyjnych, opartych na hasłach lub istniejących
Usługi Azure AD obsługuje również pojedynczego logowania jednokrotnego linki bezpośrednie do poszczególnych aplikacji, które obsługują opartego na hasłach rejestracji jednokrotnej, istniejące rejestracji jednokrotnej i dowolnej formy federacyjnego logowania jednokrotnego.

Łącza te są specjalnie przygotowany adresów URL, które wysyłania do użytkownika za pomocą konta usługi Azure AD w procesie dla określonej aplikacji bez konieczności użytkownik uruchomić je z panelu dostępu do usługi Azure AD lub usługi Office 365. Te pojedynczy znak na adresy URL można znaleźć na karcie pulpitu nawigacyjnego wszystkie wstępnie zintegrowanych aplikacji w sekcji usługi Active Directory w portalu zarządzania Azure, jak pokazano na poniższym zrzucie ekranu.

![][6]

Te linki mogą można kopiować i wklejać dowolnego miejsca, aby zapewnić logowanie łącza do wybranej aplikacji. Może to być w wiadomości e-mail lub w żadnych niestandardowych portalu sieci web skonfigurowanego użytkownika dostępu do aplikacji. Oto przykład usługi Azure AD bezpośredniego pojedynczy adres URL logowania dla usługi Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Podobnie jak adresy URL w danej organizacji panelu dostępu, można dostosować ten adres URL, dodając jeden aktywny lub zweryfikowanych domen dla katalogu po myapps.microsoft.com domeny. Dzięki temu wszystkie znakowanie organizacji jest ładowany od razu na stronie logowania bez konieczności najpierw wprowadź swój identyfikator użytkownika użytkownik:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Po kliknięciu autoryzowanym użytkownikiem na jednym z poniższych linków, specyficzne dla aplikacji, one najpierw Zobacz ich organizacyjnej strony logowania (przy założeniu, że ich nie jest już zalogowany), a po zalogowaniu są przekierowywane do aplikacji bez uprzedniego zatrzymywania w panelu dostępu. Jeśli użytkownik nie ma dostępu do aplikacji, takich jak rozszerzenie przeglądarki opartego na hasłach funkcji logowania jednokrotnego, wymagania wstępne łącze monitują użytkownika do zainstalowania brakujące rozszerzenie. Adres URL łącza również pozostaje stała, w przypadku zmiany jednego konfiguracji logowania jednokrotnego dla aplikacji.

Te linki używane te same mechanizmy kontroli dostępu do panelu dostępu i usługi Office 365, i tylko ci użytkownicy lub grupy, którzy zostali przypisani do aplikacji w portalu zarządzania platformy Azure będą mógł pomyślnie wykonać uwierzytelnienia. Jednak każdy użytkownik, który nie ma autoryzacji zobaczą komunikat z informacjami o tym, że nie przyznano dostęp i podano łącza do załadowania panel dostępu, aby wyświetlić dostępne aplikacje, dla których mają dostęp.

## <a name="related-articles"></a>Pokrewne artykuły:
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md)
* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Znajdowanie niezatwierdzona aplikacji w chmurze z usługi Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
* [Wprowadzenie do zarządzania dostępem do aplikacji](active-directory-managing-access-to-apps.md)
* [Porównanie funkcji zarządzania w usłudze Azure AD tożsamości zewnętrznych](active-directory-b2b-compare-external-identities.md)

<!--Image references-->
[1]: ./media/active-directory-appssoaccess-whatis/onlineappgallery.png
[2]: ./media/active-directory-appssoaccess-whatis/azuremgmtportal.png
[3]: ./media/active-directory-appssoaccess-whatis/accesspanel.png
[4]: ./media/active-directory-appssoaccess-whatis/officeapphub.png
[5]: ./media/active-directory-appssoaccess-whatis/workdaymobile.png
[6]: ./media/active-directory-appssoaccess-whatis/deeplink.png
