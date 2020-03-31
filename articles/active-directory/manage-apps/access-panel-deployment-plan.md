---
title: Planowanie wdrożenia panelu dostępu usługi Azure Active Directory
description: Wskazówki dotyczące wdrażania panelu dostępu usługi Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d8b6c6d40aa81bf56baed59f90417f2147fa56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897080"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Planowanie wdrożenia panelu dostępu usługi Azure Active Directory

Panel dostępu usługi Azure Active Directory (Azure AD) to portal internetowy, który pomaga obniżyć koszty pomocy technicznej, zwiększyć produktywność i bezpieczeństwo oraz zmniejszyć frustrację użytkowników. System zawiera szczegółowe raporty, które śledzi, gdy dostęp do systemu i powiadamia administratorów o niewłaściwym użyciu lub nadużyciu.

Korzystając z panelu dostępu usługi Azure AD, można:

* Odnajduj i uzyskaj dostęp do wszystkich zasobów połączonych z usługą Azure AD, takich jak aplikacje
* Żądanie dostępu do nowych aplikacji i grup
* Zarządzanie dostępem do tych zasobów dla innych
* Zarządzanie samoobsługowym resetowaniem haseł i ustawieniami uwierzytelniania wieloskładnikowego platformy Azure
* Zarządzanie urządzeniami

Umożliwia również administratorom zarządzanie:

* Warunki korzystania z usługi
* Organizacje
* Przeglądy dostępu


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Zalety integracji z panelem dostępu usługi Azure AD

Panel dostępu usługi Azure AD przynosi korzyści firmom w następujący sposób:

**Zapewnia intuicyjne środowisko użytkownika:** Panel dostępu zapewnia jedną platformę dla wszystkich aplikacji połączonych z logiem jednokrotnym platformy Azure. Masz ujednolicony portal, aby znaleźć istniejące ustawienia i nowe możliwości, takie jak zarządzanie grupami i samoobsługowe resetowanie haseł, gdy są dodawane. Intuicyjna obsługa pozwala użytkownikom szybciej wracać do pracy i pracować wydajniej, jednocześnie zmniejszając ich frustrację.

**Zwiększa produktywność:** wszystkie aplikacje użytkowników w panelu dostępu mają włączoną funkcję SSO. Włączenie logowania jednokrotnego w aplikacjach korporacyjnych i usłudze Office 365 tworzy doskonałe środowisko logowania, zmniejszając lub eliminując dodatkowe monity logowania. Panel dostępu korzysta z samoobsługowego i dynamicznego członkostwa oraz zwiększa ogólne bezpieczeństwo systemu tożsamości. Czyni to poprzez zapewnienie, że odpowiednie osoby zarządzają dostępem do aplikacji. Panel dostępu służy jako spójna strona docelowa, aby szybko znaleźć zasoby i kontynuować zadania robocze.

**Zarządzanie kosztami:** Włączenie panelu dostępu za pomocą usługi Azure AD może pomóc w zbyciu infrastruktury lokalnej. Zmniejsza koszty pomocy technicznej, zapewniając spójny portal, aby znaleźć wszystkie aplikacje, zażądać dostępu do zasobów i zarządzać kontami.

**Zwiększa elastyczność i bezpieczeństwo:** Panel dostępu zapewnia dostęp do zabezpieczeń i elastyczności zapewniane przez platformę chmurową. Administratorzy mogą łatwo zmieniać ustawienia na aplikacje i zasoby oraz spełniać nowe wymagania dotyczące zabezpieczeń bez wpływu na użytkowników.

**Umożliwia niezawodne śledzenie inspekcji i użycia:** inspekcja i śledzenie użycia dla wszystkich funkcji użytkownika pozwala wiedzieć, gdy użytkownicy korzystają z ich zasobów i zapewnia, że można ocenić bezpieczeństwo.

### <a name="licensing-considerations"></a>Zagadnienia dotyczące licencjonowania

Panel dostępu jest bezpłatny i nie wymaga licencji do użycia na poziomie podstawowym. Jednak liczba obiektów w katalogu i dodatkowe funkcje, które chcesz wdrożyć, mogą wymagać dodatkowych licencji. Niektóre typowe scenariusze usługi Azure AD, które mają wymagania licencyjne, obejmują następujące funkcje zabezpieczeń:

* [Uwierzytelnianie wieloskładnikowe platformy Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Członkostwo w grupach](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Samoobsługowe resetowanie haseł](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Ochrona tożsamości w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Zobacz [pełny przewodnik licencjonowania usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Wymagania wstępne dotyczące wdrażania panelu dostępu usługi Azure AD Access

Przed rozpoczęciem tego projektu należy wykonać następujące wymagania wstępne:

* [Integracja aplikacji SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Zarządzanie infrastrukturą użytkowników i grup usługi Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Planowanie wdrożenia panelu dostępu usługi Azure AD

W poniższej tabeli przedstawiono kluczowe przypadki użycia wdrożenia panelu dostępu:

| Obszar| Opis |
| - | - |
| Dostęp| Portal Panelu dostępu jest dostępny z urządzeń firmowych i osobistych w sieci firmowej. |
|Dostęp | Portal Panelu dostępu jest dostępny z urządzeń firmowych spoza sieci firmowej. |
| Inspekcja| Dane dotyczące użytkowania są pobierane do systemów firmowych co najmniej raz na 29 dni. |
| Nadzór| Cykl życia przypisania użytkowników do aplikacji i grup połączonych z usługą Azure AD jest definiowany i monitorowany. |
| Zabezpieczenia| Dostęp do zasobów jest kontrolowany za pomocą przypisań użytkowników i grup. Tylko autoryzowani użytkownicy mogą zarządzać dostępem do zasobów. |
| Wydajność| Terminy propagacji przydziałów dostępu są dokumentowane i monitorowane. |
| Doświadczenie użytkownika| Użytkownicy są świadomi możliwości Panelu dostępu i sposobu ich używania.|
| Doświadczenie użytkownika| Użytkownicy mogą zarządzać dostępem do aplikacji i grup.|
| Doświadczenie użytkownika| Użytkownicy mogą zarządzać swoimi kontami. |
| Doświadczenie użytkownika| Użytkownicy są świadomi zgodności przeglądarki. |
| Pomoc techniczna| Użytkownicy mogą znaleźć obsługę problemów z Panelem dostępu. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Najważniejsze wskazówki dotyczące wdrażania panelu dostępu usługi Azure AD Access

Funkcjonalność Panelu dostępu można włączać stopniowo. Zaleca się następującą kolejność wdrażania:

1. Moje aplikacje
   * Uruchamianie aplikacji
   * Samoobsługowe zarządzanie aplikacjami
   * Integracja z pakietem Microsoft Office 365

1. Odnajdowanie aplikacji samoobsługowych
   * Samoobsługowe resetowanie haseł
   * Ustawienia uwierzytelniania wieloskładnikowego
   * Zarządzanie urządzeniami
   * Warunki użytkowania
   * Zarządzanie organizacjami

1. Moje grupy
   * Samoobsługowe zarządzanie grupami
1. Przeglądy dostępu
   * Zarządzanie przeglądami dostępu

Począwszy od moich aplikacji wprowadza użytkowników do portalu jako wspólne miejsce dostępu do zasobów. Dodawanie samoobsługowego odnajdowania aplikacji opiera się na środowisko Moje aplikacje. Moje grupy i przeglądy dostępu opierają się na możliwościach samoobsługi.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Planowanie konfiguracji dla panelu dostępu usługi Azure AD Access

W poniższej tabeli wymieniono kilka ważnych konfiguracji Panelu dostępu i typowe wartości, których można użyć:

| Konfigurowanie| Typowe wartości |
| - | - |
| Określanie grup pilotażowych| Zidentyfikuj grupę zabezpieczeń usługi Azure AD, która ma być używana, i upewnij się, że wszyscy członkowie programu pilotażowego są częścią grupy. |
| Określ grupę lub grupy, które mają być włączone dla produkcji.| Zidentyfikuj grupy zabezpieczeń usługi Azure AD lub grupy usługi Active Directory zsynchronizowane z usługą Azure AD, aby były używane. Upewnij się, że wszyscy członkowie programu pilotażowego są częścią grupy. |
| Zezwalaj użytkownikom na używanie funkcji SSO do niektórych typów aplikacji| Sso sfederowane, OAuth, Hasło Logach bez zmiany, Pełnomocnik aplikacji |
| Zezwalaj użytkownikom na samodzielne resetowanie hasła | Tak |
| Zezwalaj użytkownikom na korzystanie z uwierzytelniania wieloskładnikowego| Tak |
| Zezwalaj użytkownikom na korzystanie z zarządzania grupami samoobsługowymi dla niektórych typów grup| Grupy zabezpieczeń, grupy usługi Office 365 |
| Zezwalaj użytkownikom na korzystanie z samoobsługowego zarządzania aplikacjami| Tak |
| Zezwalaj użytkownikom na korzystanie z recenzji dostępu| Tak |

### <a name="plan-consent-strategy"></a>Planowanie strategii zgody

Użytkownicy lub administratorzy muszą wyrazić zgodę na warunki użytkowania aplikacji i politykę prywatności. Jeśli to możliwe, biorąc pod uwagę reguły biznesowe, użyj zgody administratora, co daje użytkownikom lepsze wrażenia.

Aby korzystać z zgody administratora, musisz być administratorem globalnym organizacji, a aplikacje muszą być następujące:

* Zarejestrowana w organizacji

* Zarejestrowana w innej organizacji usługi Azure AD i wcześniej zgód co najmniej jednego użytkownika

Aby uzyskać więcej informacji, zobacz [Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikację w usłudze Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich interesariuszy

Gdy projekty technologiczne nie powiodą się, zazwyczaj robią to z powodu niedopasowanych oczekiwań dotyczących wpływu, wyników i odpowiedzialności. Aby uniknąć tych pułapek, [upewnij się, że angażujesz odpowiednie zainteresowane strony](../fundamentals/active-directory-deployment-plans.md) i że role interesariuszy w projekcie są dobrze zrozumiałe.

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja ma kluczowe znaczenie dla powodzenia każdej nowej usługi. Proaktywnie informuj użytkowników, jak i kiedy zmieni się ich doświadczenie i jak uzyskać wsparcie w razie potrzeby.

Chociaż Panel dostępu zazwyczaj nie tworzy problemów użytkownika, ważne jest, aby się przygotować. Przed uruchomieniem utwórz przewodniki i listę wszystkich zasobów dla personelu pomocy technicznej.

#### <a name="communications-templates"></a>Szablony komunikacji

Firma Microsoft udostępnia [dostosowywalne szablony wiadomości e-mail i innych komunikatów](https://aka.ms/APTemplates) dla Panelu dostępu. Zasoby te można dostosować do wykorzystania w innych kanałach komunikacji, stosownie do kultury korporacyjnej.

## <a name="plan-your-sso-configuration"></a>Planowanie konfiguracji usługi SSO

Gdy użytkownik loguje się do aplikacji, przechodzi przez proces uwierzytelniania i są wymagane, aby udowodnić, kim są. Bez loga logajnego hasło jest przechowywane w aplikacji, a użytkownik musi znać to hasło. Za pomocą logowania sygnacyjnego poświadczenia użytkowników są przekazywane do aplikacji, więc nie trzeba ponownie wdawać haseł dla każdej aplikacji.

Aby uruchomić aplikacje w aplikacji Moje aplikacje, należy włączyć funkcję SSO.

Usługa Azure AD obsługuje trzy różne sposoby włączania [aplikacji logowania jednokrotnego:](what-is-single-sign-on.md)

* **Sfederowane logowanie jednokrotne** 
    * Umożliwia aplikacji przekierowanie do usługi Azure AD w celu uwierzytelnienia użytkownika, zamiast monitowania o hasło. 
    * Jest obsługiwany dla aplikacji korzystających z protokołów, takich jak SAML 2.0, Federacja WS lub OpenID Connect i jest najbogatszym trybem logowania jednokrotnego.

* **Logowanie jednookrotne oparte na hasłach** 
    * Umożliwia bezpieczne przechowywanie haseł aplikacji i odtwarzanie za pomocą rozszerzenia przeglądarki internetowej lub aplikacji mobilnej. 
    * Korzysta z istniejącego procesu logowania dostarczonego przez aplikację, ale umożliwia administratorowi zarządzanie hasłami. Użytkownik nie musi znać hasła.

* **Istniejące logowanie jednokrotne** 
    * Umożliwia usłudze Azure AD korzystanie z dowolnego istniejącego logowania jednokrotnego, który został skonfigurowany dla aplikacji.
    * Umożliwia powiązanie tych aplikacji z portalami panelu dostępu usługi Office 365 lub Azure AD Access. 
    * Umożliwia dodatkowe raportowanie w usłudze Azure AD po uruchomieniu aplikacji. 
    * Obejmuje korzystanie z usługi Azure Application Proxy i połączonego trybu logowania jednokrotnego.

Dowiedz się, jak skonfigurować tryb logowania jednokrotnego aplikacji tutaj: [Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Aby uzyskać najlepsze środowisko ze strony Moje aplikacje, zacznij od integracji aplikacji w chmurze, które są dostępne dla federacyjnego zgłoszeniaszego. Federacyjne samoso umożliwia użytkownikom spójne środowisko jednego kliknięcia na powierzchniach uruchamiania aplikacji i wydaje się być bardziej niezawodne w kontroli konfiguracji.

Użyj federacyjnego loga SSO z usługą Azure AD (OpenID Connect/SAML), gdy aplikacja go obsługuje, zamiast sso oparte na hasłach i ADFS.

Aby uzyskać więcej informacji na temat wdrażania i konfigurowania aplikacji SaaS, zobacz [plan wdrażania logowania sytłego SaaS](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Planowanie wdrożenia rozszerzenia przeglądarki Moje aplikacje

Gdy użytkownicy logują się do aplikacji logowania samosoiowego opartych na hasłach, muszą zainstalować i używać rozszerzenia logowania bezpiecznej my apps. Rozszerzenie wykonuje skrypt, który przesyła hasło do formularza logowania aplikacji. Użytkownicy są monitowani o zainstalowanie rozszerzenia przy pierwszym uruchomieniu aplikacji logajnych opartej na hasłach. Więcej informacji na temat rozszerzenia można znaleźć w tej dokumentacji dotyczącej [instalowania rozszerzenia przeglądarki Panelu programu Access](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav).

Jeśli należy zintegrować aplikacje logowania jednokrotnego oparte na hasłach, należy zdefiniować mechanizm wdrażania rozszerzenia na dużą skalę z [obsługiwanymi przeglądarkami](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Dostępne są następujące opcje:

* [Zasady grupy dla programu Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Menedżer konfiguracji dla programu Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [Pobieranie i konfiguracja oparta na użytkownikach dla Chrome, Firefox, Microsoft Edge lub IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Dowiedz się więcej: [Jak skonfigurować logowanie jednokrotne hasła](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

Użytkownicy, którzy nie korzystają z aplikacji logowania samoso na bazie hasła również korzystać z rozszerzenia. Korzyści te obejmują możliwość uruchamiania dowolnej aplikacji z paska wyszukiwania, znajdowanie dostępu do ostatnio używanych aplikacji i link do strony Moje aplikacje.

Oto, co użytkownik zobaczy podczas uruchamiania aplikacji logującej logowania oparte na hasłach po raz pierwszy:

![Zrzut ekranu przedstawiający ekran instalacji rozszerzenia przeglądarki Moje aplikacje ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Planowanie dostępu mobilnego

Przeglądarka chroniona za pomocą zasad usługi Intune (Microsoft Edge lub Intune Managed Browser) jest niezbędna dla użytkowników mobilnych uruchamianych aplikacje logowania jednokrotne oparte na hasłach. Przeglądarka chroniona zasadami umożliwia przesyłanie hasła zapisanego dla aplikacji. Microsoft Edge lub zarządzana przeglądarka udostępnia zestaw funkcji ochrony danych internetowych. Można również użyć programu Microsoft Edge w scenariuszach korporacyjnych na urządzeniach z systemem iOS i Android. Program Microsoft Edge obsługuje te same scenariusze zarządzania, co zarządzana przeglądarka usługi Intune i poprawia środowisko użytkownika. Dowiedz się więcej: [Zarządzanie dostępem do sieci Web przy użyciu przeglądarki chronionej zasadami usługi Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>Planowanie wdrożenia usługi Moje aplikacje

Podstawą Panelu dostępu jest uruchamianie aplikacji Moje aplikacje, [https://myapps.microsoft.com](https://myapps.microsoft.com/)do którego użytkownicy uzyskują dostęp w programie . Strony Moje aplikacje zapewniają użytkownikom jedno miejsce do rozpoczęcia pracy i uzyskania niezbędnych aplikacji. W tym miejscu użytkownicy znajdują listę wszystkich aplikacji, do których mają dostęp do logowania jednokrotnego. 

![Zrzut ekranu panelu aplikacje](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Te same aplikacje będą wyświetlane w launcherze aplikacji usługi Office 365, gdy użytkownicy korzystają z portalu usługi Office 365.

Zaplanuj kolejność dodawania aplikacji do programu Uruchamiający Moje aplikacje i zdecyduj, czy będziesz je wdrażać stopniowo, czy wszystkie naraz. Aby to zrobić, należy utworzyć spis aplikacji z listą typu uwierzytelniania i wszelkich istniejących integracji samosoków dla każdej aplikacji.

#### <a name="add-applications-to-the-my-apps-panel"></a>Dodawanie aplikacji do panelu Moje aplikacje

Do uruchamiania aplikacji My Apps można dodać dowolną aplikację obsługującą protokół Azure AD Z funkcją SSO. Inne aplikacje są dodawane przy użyciu opcji Połączonego przyłącza SSO. Można skonfigurować kafelek aplikacji, który łączy się z adresem URL istniejącej aplikacji sieci web. Połączone przysiąkaszne umożliwia rozpoczęcie kierowania użytkowników do portalu Moje aplikacje bez migracji wszystkich aplikacji do usługi Azure AD SSO. Można stopniowo przechodzić do aplikacji skonfigurowanych przez usługi Azure AD SSO bez zakłócania środowiska użytkowników.

#### <a name="use-my-apps-collections"></a>Korzystanie z kolekcji Moje aplikacje

Domyślnie wszystkie aplikacje są wyświetlane razem na jednej stronie. Ale można użyć kolekcji do grupowania powiązanych aplikacji i prezentować je na osobnej karcie, dzięki czemu łatwiej je znaleźć. Na przykład można użyć kolekcji do tworzenia logicznych grupowania aplikacji dla określonych ról zadań, zadań, projektów i tak dalej. Aby uzyskać więcej informacji, zobacz [Jak dostosować panele dostępu użytkowników za pomocą kolekcji Moje aplikacje.](access-panel-collections.md) 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Planowanie używania aplikacji Moje aplikacje lub istniejącego portalu

Użytkownicy mogą mieć już aplikację lub portal inny niż Moje aplikacje. Jeśli tak, zdecyduj, czy chcesz obsługiwać oba portale, czy tylko jeden.

Jeśli istniejący portal jest już używany jako punkt wyjścia dla użytkowników, można zintegrować funkcje Moje aplikacje przy użyciu adresów URL dostępu użytkowników. Adresy URL dostępu użytkowników działają jako bezpośrednie łącza do aplikacji dostępnych w portalu Moje aplikacje. Te adresy URL mogą być osadzone w dowolnej istniejącej witrynie. Gdy użytkownik wybierze łącze, otworzy aplikację z portalu Moje aplikacje.

Właściwość adresu URL dostępu użytkownika można znaleźć w obszarze **Właściwości** aplikacji w witrynie Azure portal.

![Zrzut ekranu panelu aplikacje](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Planowanie samoobsługowego wykrywania i uzyskiwania dostępu do aplikacji

Po wdrożeniu podstawowego zestawu aplikacji na stronie Moje aplikacje użytkownika należy włączyć funkcje samoobsługowego zarządzania aplikacjami. Samoobsługowe odnajdowanie aplikacji umożliwia użytkownikom:

* Znajdź nowe aplikacje do dodania do moich aplikacji. 
* Dodaj opcjonalne aplikacje, których mogą nie wiedzieć podczas instalacji.

Przepływy pracy zatwierdzania są dostępne dla jawnego zatwierdzania dostępu do aplikacji. Użytkownicy, którzy są osobami zatwierdzających, będą otrzymywać powiadomienia w portalu Moje aplikacje, gdy istnieje oczekujące żądanie dostępu do aplikacji.

## <a name="plan-self-service-group-membership"></a>Planowanie członkostwa w grupie samoobsługowej 

Można umożliwić użytkownikom tworzenie własnych grup zabezpieczeń lub grup usługi Office 365 w usłudze Azure AD i zarządzanie nimi. Właściciel grupy może zatwierdzać lub odrzucać żądania członkostwa i delegować kontrolę nad członkostwem w grupie. Funkcje zarządzania grupami samoobsługowymi nie są dostępne dla grup zabezpieczeń z obsługą poczty ani list dystrybucyjnych.

Aby zaplanować samoobsługowe członkostwo w grupach, określ, czy zezwolisz wszystkim użytkownikom w organizacji na tworzenie grup i zarządzanie nimi, czy tylko na podzbiór użytkowników. Jeśli zezwalasz na podzbiór użytkowników, musisz skonfigurować grupę, do której są dodawane te osoby. Zobacz [Konfigurowanie zarządzania grupami samoobsługowymi w usłudze Azure Active Directory,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) aby uzyskać szczegółowe informacje na temat włączania tych scenariuszy.

## <a name="plan-reporting-and-auditing"></a>Raportowanie planów i inspekcja

Usługa Azure AD udostępnia [raporty, które oferują szczegółowe informacje techniczne i biznesowe.](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/) Współpracuj z właścicielami aplikacji biznesowych i technicznych, aby przejąć na własność te raporty i regularnie je konsumować. W poniższej tabeli przedstawiono kilka przykładów typowych scenariuszy raportowania.

|   | Zarządzanie ryzykiem| Zwiększ produktywność| Zarządzanie i zgodność |
|  - |- | - | - |
| Typy raportów|  Uprawnienia i użycie aplikacji| Działanie inicjowania obsługi administracyjnej konta| Sprawdź, kto uzyskuje dostęp do aplikacji |
| Potencjalne działania| Inspekcja dostępu; odwoływanie uprawnień| Korygowanie błędów inicjowania obsługi administracyjnej| Cofanie dostępu |

Usługa Azure AD przechowuje większość danych inspekcji przez 30 dni. Dane są dostępne za pośrednictwem portalu administracyjnego platformy Azure lub interfejsu API do pobrania do systemów analizy.

#### <a name="auditing"></a>Inspekcja

Dzienniki inspekcji dostępu do aplikacji są dostępne przez 30 dni. Jeśli inspekcja zabezpieczeń w przedsiębiorstwie wymaga dłuższego przechowywania, dzienniki muszą zostać wyeksportowane do narzędzia SIEM (Security Information Event and Management), takiego jak Splunk lub ArcSight.

W przypadku inspekcji, raportowania i odzyskiwania po awarii należy udokumentować wymaganą częstotliwość pobierania, system docelowy i kto jest odpowiedzialny za zarządzanie każdą kopią zapasową. Może nie być konieczne oddzielne inspekcji i raportowania kopii zapasowych. Kopia zapasowa odzyskiwania po awarii powinna być oddzielną jednostką.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Wdrażanie aplikacji w panelu Moje aplikacje użytkowników

Po skonfigurowaniu aplikacji dla usługi SSO grupom jest przypisywany dostęp. Użytkownicy w przydzielonych grupach będą mieli dostęp i zobaczą aplikację w swoich moich aplikacjach i programie uruchamiania aplikacji usługi Office 365.

Zobacz [Przypisywanie użytkowników i grup do aplikacji w usłudze Active Directory](methods-for-assigning-users-and-groups.md).

Jeśli podczas testowania lub wdrażania chcesz dodać grupy, ale jeszcze nie zezwolić aplikacjom na pokazywanych w obszarze Moje aplikacje, zobacz [Ukrywanie aplikacji przed korzystaniem z usługi Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Wdrażanie aplikacji usługi Microsoft Office 365 w aplikacjach My Apps

W przypadku aplikacji usługi Office 365 użytkownicy otrzymują kopię pakietu Office na podstawie przypisanych im licencji. Warunkiem dostępu do aplikacji pakietu Office jest przypisywanie użytkownikom poprawnych licencji powiązanych z aplikacjami pakietu Office. Po przypisaniu użytkownikowi licencji automatycznie zobaczą aplikacje skojarzone z licencją na stronie Moje aplikacje i w usłudze Office 365 App Launcher.

Jeśli chcesz ukryć zestaw aplikacji pakietu Office przed użytkownikami, istnieje możliwość ukrycia aplikacji z portalu Moje aplikacje, a jednocześnie zezwalania na dostęp z portalu usługi Office 365. Te ustawienia można znaleźć w części Ustawienia użytkownika aplikacji. Dowiedz się więcej: [Ukryj aplikację przed korzystaniem z usługi Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)

![Zrzut ekranu przedstawiający konfigurowanie sposobu ukrywania aplikacji](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Wdrażanie możliwości samoobsługi aplikacji

Dostęp do samoobsługowych aplikacji umożliwia użytkownikom samodzielne wykrywanie i żądanie dostępu do aplikacji. Użytkownicy mają swobodę dostępu do aplikacji, których potrzebują, bez przechodzenia przez grupę IT za każdym razem, aby zażądać dostępu. Gdy użytkownik zażąda dostępu i zostanie zatwierdzony automatycznie lub ręcznie przez właściciela aplikacji, zostanie dodany do grupy na zapleczu. Raportowanie jest włączone, kto zażądał, zatwierdził lub usunął dostęp, i daje kontrolę nad zarządzaniem przypisanymi rolami.

Zatwierdzanie żądań dostępu do aplikacji można delegować do firmowych osób zatwierdzających. Osoba zatwierdzająca firmy może ustawić hasła dostępu do aplikacji na stronie Moje aplikacje osoby zatwierdzającej.

Dowiedz się więcej: [Jak korzystać z dostępu do aplikacji samoobsługowych](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).

![Zrzut ekranu przedstawiający konfigurowanie samoobsługowego zarządzania aplikacjami](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Sprawdzanie poprawności wdrożenia

Upewnij się, że wdrożenie panelu dostępu jest dokładnie przetestowane i wdrożono plan wycofywania.

Następujące testy powinny być przeprowadzane zarówno na urządzeniach należących do firmy, jak i urządzeniach osobistych. Te przypadki testowe powinny również odzwierciedlać przypadki użycia w firmie. Oto kilka przypadków opartych na przykładowych wymaganiach biznesowych w tym dokumencie i typowych scenariuszach technicznych. Dodaj inne specyficzne dla Twoich potrzeb.

#### <a name="application-sso-access-test-case-examples"></a>Przykłady przypadków dostępu do aplikacji SSO:


| Sprawa biznesowa| Oczekiwany wynik |
| - | -|
| Użytkownik loguje się do portalu Moje aplikacje| Użytkownik może się zalogować i zobaczyć swoje aplikacje |
| Użytkownik uruchamia sfederowaną aplikację SSO| Użytkownik jest automatycznie zalogowany do aplikacji |
| Użytkownik uruchamia aplikację logajną pod hasłem po raz pierwszy| Użytkownik musi zainstalować rozszerzenie Moje aplikacje |
| Użytkownik uruchamia aplikację logajną hasła o kolejny czas| Użytkownik jest automatycznie zalogowany do aplikacji |
| Użytkownik uruchamia aplikację z portalu usługi Office 365| Użytkownik jest automatycznie zalogowany do aplikacji |
| Użytkownik uruchamia aplikację z zarządzanej przeglądarki| Użytkownik jest automatycznie zalogowany do aplikacji |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Przykłady przypadków samoobsługowych aplikacji


| Sprawa biznesowa| Oczekiwany wynik |
| - | - |
| Użytkownik może zarządzać członkostwem w aplikacji| Użytkownik może dodawać/usuwać członków, którzy mają dostęp do aplikacji |
| Użytkownik może edytować aplikację| Użytkownik może edytować opis aplikacji i poświadczenia dla aplikacji logowania do logowania bez logowania do haseł |

### <a name="rollback-steps"></a>Kroki wycofywania

Należy zaplanować, co należy zrobić, jeśli wdrożenie nie pójdzie zgodnie z planem. Jeśli konfiguracja sytego systemu sytego nie powiedzie się podczas wdrażania, należy zrozumieć, jak [rozwiązać problemy z przysłanianiem zabezpieczeń](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) i zmniejszyć wpływ na użytkowników. W ekstremalnych okolicznościach może być konieczne [wycofanie SSO](../manage-apps/plan-sso-deployment.md).


## <a name="manage-your-implementation"></a>Zarządzanie implementacją

Aby wykonać wymagane zadanie w usłudze Azure Active Directory, należy użyć roli o najniższych uprawnieniach. [Przejrzyj różne role, które są dostępne](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) i wybierz odpowiedni, aby rozwiązać swoje potrzeby dla każdej persona dla tej aplikacji. Niektóre role mogą wymagać tymczasowego zastosowania i usunięcia po zakończeniu wdrażania.

| Osoby| Role| Rola usługi Azure AD  |
| - | -| -|
| Administrator działu pomocy technicznej| Obsługa poziomu 1| Brak |
| Administrator tożsamości| Konfigurowanie i debugowanie, gdy problemy mają wpływ na usługę Azure AD| Administrator globalny |
| Administrator aplikacji| Zaświadczenie użytkownika w aplikacji, konfiguracja użytkowników z uprawnieniami| Brak |
| Administratorzy infrastruktury| Właściciel najazd cert| Administrator globalny |
| Właściciel firmy/interesariusz| Zaświadczenie użytkownika w aplikacji, konfiguracja użytkowników z uprawnieniami| Brak |

[Zarządzanie tożsamościami uprzywilejowanymi](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) służy do zarządzania rolami w celu zapewnienia dodatkowej inspekcji, kontroli i przeglądu dostępu dla użytkowników z uprawnieniami do katalogów.

### <a name="troubleshoot-access-panel-issues"></a>Rozwiązywanie problemów z panelem dostępu

Tworzenie przewodników rozwiązywania problemów dla organizacji pomocy technicznej z typowych scenariuszy, które wskazują na dokumentację firmy Microsoft w ich rozwiązaniach. Można utworzyć przewodniki, które podzielają obsługę do warstw używanych przez organizację.

Zobacz następujące przewodniki rozwiązywania problemów, aby uzyskać odwołanie:

[Aplikacje nie pojawiają się](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Pojawiające się nieoczekiwane aplikacje](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[Użytkownik nie może zalogować się do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problemy z dostępem do aplikacji samoobsługowych](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problemy z rozszerzeniem przeglądarki](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Następne kroki

[Planowanie wdrożenia uwierzytelniania wieloskładnikowego platformy Azure](https://aka.ms/deploymentplans/mfa)
