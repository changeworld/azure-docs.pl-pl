---
title: Planowanie wdrożenia panelu dostępu Azure Active Directory
description: Wskazówki dotyczące wdrażania funkcji panelu dostępu usługi Azure AD
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
ms.date: 08/16/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 448af7075297c2b18df1eceaeaac34d5da762489
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576526"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Planowanie wdrożenia panelu dostępu Azure Active Directory

Panel dostępu Azure Active Directory jest portalem opartym na sieci Web, który umożliwia obniżenie kosztów pomocy technicznej, zwiększenie produktywności i bezpieczeństwa oraz obniżenie frustrację użytkowników. System zawiera szczegółowe raporty, które są śledzone, gdy użytkownicy uzyskują dostęp do systemu, i powiadamiają administratorów o niewłaściwych lub nadużyciach.

Panel dostępu Azure Active Directory umożliwia użytkownikom:

* Odkryj i uzyskaj dostęp do Azure Active Directory połączonych zasobów firmy, takich jak aplikacje.
* Zażądaj dostępu do nowych aplikacji i grup lub Zarządzaj dostępem do tych zasobów innym użytkownikom.
* Zarządzaj ustawieniami resetowania hasła i uwierzytelniania wieloskładnikowego.
* Zarządzanie swoimi urządzeniami.

Umożliwia także administratorom zarządzanie:

* Warunki użytkowania usługi
* Organizacje
* Przeglądy dostępu


## <a name="benefits-of-azure-active-directory-access-panel-integration"></a>Zalety integracji z Azure Active Directory panelu dostępu

Panel dostępu Microsoft Azure Active Directory (Azure AD) korzysta z firm w następujący sposób:

**Zapewnia intuicyjne środowisko użytkownika**: Włączenie panelu dostępu pozwala użytkownikom z możliwością korzystania z jednej konsoli uruchamiania dla wszystkich aplikacji połączonych z systemem Azure. Ponieważ dodawane są funkcje, takie jak zarządzanie grupami i Samoobsługowe resetowanie haseł, użytkownicy nadal mają ujednolicony Portal, aby znaleźć te ustawienia. Intuicyjne środowisko zapewnia użytkownikom szybsze działanie i zwiększa produktywność przy jednoczesnym zmniejszeniu ich frustrację.

**Zwiększa produktywność**: Wszystkie aplikacje użytkownika w panelu dostępu mają włączone Logowanie jednokrotne (SSO). Włączenie logowania jednokrotnego w aplikacjach dla przedsiębiorstw i pakietu Office 365 zapewnia doskonałe środowisko logowania dla istniejących użytkowników, zmniejszając lub eliminując dodatkowe instrukcje logowania. Panel dostępu umożliwia samoobsługowe i dynamiczne członkostwo oraz poprawia ogólne zabezpieczenia systemu tożsamości, zapewniając odpowiednie osoby do zarządzania dostępem do aplikacji. Panel dostępu służy jako spójna Strona docelowa umożliwiająca użytkownikowi szybkie znajdowanie zasobów i kontynuowanie zadań.

**Zarządza kosztami**: Włączenie panelu dostępu za pomocą Azure Active Directory może umożliwić odpłatę infrastruktury lokalnej. Pozwala to zmniejszyć koszty pomocy technicznej, umożliwiając użytkownikom spójny Portal, aby znalazł wszystkie swoje aplikacje, zażądać dostępu do zasobów i zarządzać swoimi kontami.

**Zwiększa elastyczność i bezpieczeństwo**: Panel dostępu pozwala uzyskać dostęp do zabezpieczeń i elastyczności zapewnianej przez platformę chmury. Administratorzy mogą łatwo zmieniać ustawienia dla aplikacji i zasobów oraz uwzględniać nowe wymagania dotyczące zabezpieczeń bez wpływu na użytkowników.

**Włącza niezawodne inspekcje i śledzenie użycia**: Inspekcja i śledzenie użycia dla wszystkich możliwości użytkowników końcowych pozwalają znać, kiedy użytkownicy korzystają z zasobów i gwarantują ocenę zabezpieczeń.

### <a name="licensing-considerations"></a>Zagadnienia dotyczące licencjonowania

Panel dostępu jest bezpłatny i nie wymaga licencji do użycia w przypadku wszystkich użytkowników podstawowych. Jednak liczba obiektów w katalogu i funkcje, które mają zostać wdrożone, mogą wymagać dodatkowych licencji. Typowe scenariusze usługi Azure AD obejmują następujące funkcje zabezpieczeń, które mają wymagania dotyczące licencjonowania.

* [Usługa Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Członkostwo na podstawie grupy](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Wymaganie hasła samoobsługowego](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Ochrona tożsamości](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Zapoznaj się z [pełnym przewodnikiem dotyczącym licencjonowania Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Wymagania wstępne dotyczące wdrażania panelu dostępu usługi Azure AD

Poniższe wymagania wstępne muszą zostać spełnione przed rozpoczęciem tego projektu.

* [Integracja z logowaniem jednokrotnym aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Infrastruktura użytkowników i grup usługi Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Planowanie wdrożenia panelu dostępu usługi Azure AD

W poniższej tabeli przedstawiono najważniejsze przypadki użycia dla wdrożenia panelu dostępu:

| Obszar| Opis |
| - | - |
| Access| Portal panelu dostępu jest dostępny z urządzeń firmowych i osobistych w sieci firmowej. |
|Access | Portal panelu dostępu jest dostępny dla urządzeń firmowych spoza sieci firmowej. |
| Inspekcja| Dane użycia są pobierane do systemów firmowych co najmniej co 29 dni. |
| Ład| Cykl życia przypisań użytkowników do połączonych aplikacji i grup usługi Azure AD jest definiowany i monitorowany. |
| Bezpieczeństwo| Dostęp do zasobów jest kontrolowany za pośrednictwem przypisań użytkowników i grup. Tylko autoryzowani użytkownicy mogą zarządzać dostępem do zasobów. |
| Wydajność| Osie czasu propagacji przydziału dostępu są udokumentowane i monitorowane. |
| Środowisko użytkownika| Użytkownicy są świadomi możliwości panelu dostępu i sposobu ich używania.|
| Środowisko użytkownika| Użytkownicy mogą samoobsługowo zarządzać dostępem do aplikacji i grup.|
| Środowisko użytkownika| Użytkownicy mogą zarządzać swoimi kontami. |
| Środowisko użytkownika| Użytkownicy są świadomi zgodności z przeglądarką. |
| Pomoc techniczna| Użytkownicy mogą znaleźć pomoc techniczną dotyczącą problemów z panelem dostępu. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Najlepsze rozwiązania dotyczące wdrażania panelu dostępu usługi Azure AD

Funkcje panelu dostępu można włączyć stopniowo. Zalecamy wykonanie następującej kolejności wdrożenia:

1. Moje aplikacje
   * Uruchamianie aplikacji
   * Samoobsługowe zarządzanie aplikacjami
   * Integracja z Microsoft Office 365

1. Odnajdywanie aplikacji samoobsługowych
   * Samoobsługowe resetowanie haseł
   * Ustawienia usługi uwierzytelnianie wieloskładnikowe
   * Zarządzanie urządzeniami
   * Warunki użytkowania
   * Zarządzanie organizacjami

1. Moje grupy
   * Samoobsługowe zarządzanie grupami
1. Przeglądy dostępu
   * Zarządzanie przeglądem dostępu

Od mojej aplikacji wprowadzono użytkowników do portalu jako typowe miejsce na dostęp do zasobów. Dodawanie funkcji samoobsługowego odnajdowania aplikacji w środowisku moje aplikacje. Moje grupy i przeglądy dostępu kompilują się na możliwości samoobsługi.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Planowanie konfiguracji w panelu dostępu usługi Azure AD

Poniższa tabela zawiera kilka ważnych konfiguracji panelu dostępu i typowych wartości, których można użyć:

| Konfigurowanie| Typowe wartości |
| - | - |
| Określanie grup pilotażowych| Zidentyfikuj grupę zabezpieczeń usługi Azure AD, która ma zostać użyta, i upewnij się, że wszyscy członkowie zespołu pilotażowego są częścią grupy. |
| Określ grupę lub grupy, które mają być włączone dla środowiska produkcyjnego.| Określ grupy zabezpieczeń usługi Azure AD lub grupy usługi AD zsynchronizowane z usługą Azure AD, które mają być używane. Upewnij się, że wszyscy członkowie zespołu pilotażowego są częścią grupy. |
| Zezwalaj użytkownikom na używanie logowania jednokrotnego do typów aplikacji| Federacyjne Logowanie jednokrotne, uwierzytelnianie OAuth, logowanie jednokrotne, serwer proxy aplikacji |
| Zezwalaj użytkownikom na korzystanie z funkcji samoobsługowego resetowania hasła| Tak |
| Zezwalaj użytkownikom na korzystanie z uwierzytelniania wieloskładnikowego| Tak |
| Zezwalaj użytkownikom na korzystanie z samoobsługowego zarządzania grupami dla typów grup| Grupy zabezpieczeń, grupy usługi O365 |
| Zezwalaj użytkownikom na korzystanie z funkcji samoobsługowego zarządzania aplikacjami| Tak |
| Zezwalaj użytkownikom na korzystanie z przeglądów dostępu| Tak |

### <a name="plan-consent-strategy"></a>Planowanie strategii wyrażania zgody

Użytkownicy lub Administratorzy muszą wyrazić zgodę na warunki użytkowania aplikacji oraz zasady zachowania poufności informacji. Jeśli jest to możliwe, zalecamy korzystanie z zgody administratora, co zapewnia użytkownikom lepszy komfort pracy.

Aby móc korzystać z zgody administratora, musisz być administratorem globalnym dzierżawy, a aplikacje muszą mieć następujące wartości:

* Zarejestrowane w dzierżawie lub

* Zarejestrowane w innej dzierżawie usługi Azure AD i wcześniej wysłane przez co najmniej jednego użytkownika końcowego.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikację w Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich uczestników projektu

Gdy projekty technologii kończą się niepowodzeniem, zazwyczaj są to spowodowane niezgodnymi oczekiwaniami na wpływ, wyniki i obowiązki. Aby uniknąć tych pułapek, [upewnij](../fundamentals/active-directory-deployment-plans.md) się, że interesują Cię odpowiednie osoby zainteresowane i że role udziałowców w projekcie są dobrze zrozumiałe.

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja jest niezwykle ważna dla sukcesu każdej nowej usługi. Proaktywnie Informuj użytkowników, jak i kiedy ich środowisko zostanie zmienione, i w razie potrzeby Uzyskaj pomoc techniczną.

Mimo że panel dostępu nie tworzy zwykle problemów użytkowników, ważne jest przygotowanie. Przed rozpoczęciem Utwórz przewodniki i listę wszystkich zasobów dla personelu pomocy technicznej.

#### <a name="communications-templates"></a>Szablony komunikacji

Firma Microsoft udostępnia [dostosowywalne szablony dla wiadomości e-mail i innych komunikacji](https://aka.ms/APTemplates) z panelem dostępu. Można dostosować te zasoby do użycia w innych kanałach komunikacyjnych, zgodnie z potrzebami kultury firmowej.

## <a name="plan-your-sso-configuration"></a>Planowanie konfiguracji logowania jednokrotnego

Gdy użytkownik loguje się do aplikacji, przechodzi przez proces uwierzytelniania i są wymagane, aby udowodnić, kto je ma. Bez logowania jednokrotnego, hasło przechowywane w aplikacji i użytkownik musi znać to hasło. Poświadczenia użytkowników logowania jednokrotnego (SSO) są przesyłane do aplikacji, dzięki czemu nie muszą ponownie wprowadzać haseł dla każdej aplikacji.

Aby uruchamiać aplikacje w aplikacjach, należy włączyć funkcję logowania jednokrotnego (SSO) dla aplikacji.

Usługa Azure AD obsługuje trzy różne sposoby włączania logowania jednokrotnego [do aplikacji](what-is-single-sign-on.md):

* **Federacyjne Logowanie jednokrotne** 
    * Umożliwia aplikacji przekierowanie do usługi Azure AD w celu uwierzytelnienia użytkownika zamiast monitowania o hasło. 
    * Jest obsługiwana w przypadku aplikacji korzystających z protokołów takich jak SAML 2,0, WS-Federation lub OpenID Connect Connect, a także jest zaawansowanym trybem rejestracji jednokrotnej.

* **Logowanie jednokrotne oparte na hasłach** 
    * Umożliwia bezpieczne przechowywanie i odtwarzanie haseł aplikacji przy użyciu rozszerzenia przeglądarki sieci Web lub aplikacji mobilnej. 
    * Wykorzystuje istniejący proces logowania udostępniany przez aplikację, ale umożliwia administratorowi zarządzanie hasłami. Użytkownik nie musi znać hasła.

* **Istniejące Logowanie jednokrotne** 
    * Umożliwia usłudze Azure AD korzystanie z istniejącego logowania jednokrotnego skonfigurowanego dla aplikacji.
    * Umożliwia połączenie tych aplikacji z portalami pakietu Office 365 lub panel dostępu usługi Azure AD. 
    * Włącza dodatkowe raportowanie w usłudze Azure AD, gdy aplikacje są tam uruchomione. 
    * Obejmuje użycie serwera proxy aplikacji platformy Azure i połączonego trybu logowania jednokrotnego.

Dowiedz się, jak skonfigurować tryb logowania jednokrotnego w aplikacji: Logowanie jednokrotne [do aplikacji w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Aby uzyskać najlepsze środowisko pracy ze stroną moje aplikacje, zalecamy rozpoczęcie integracji aplikacji w chmurze dostępnych dla federacyjnego logowania jednokrotnego. Federacyjne Logowanie jednokrotne umożliwia użytkownikom spójne i jednokrotne korzystanie z nich w swoich aplikacjach, co sprawia, że jest bardziej niezawodna w kontroli konfiguracji.

Użyj federacyjnego logowania jednokrotnego za pomocą usługi Azure AD (OpenID Connect Connect/SAML), gdy aplikacja ją obsługuje, zamiast logowania jednokrotnego i usług ADFS opartych na hasłach.

Aby uzyskać więcej informacji na temat sposobu wdrażania i konfigurowania aplikacji SaaS, zobacz [plan wdrożenia usługi SaaS SSO](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Planowanie wdrożenia rozszerzenia przeglądarki Moje aplikacje

Jeśli aplikacje logowania jednokrotnego oparte na hasłach są udostępniane użytkownikom końcowym, muszą oni zainstalować rozszerzenie moje aplikacje bezpieczne logowanie do logowania. Rozszerzenie wykonuje skrypt, który przesyła hasło do formularza logowania aplikacji. Użytkownicy otrzymają monit o zainstalowanie rozszerzenia podczas pierwszego uruchomienia aplikacji Logowanie jednokrotne oparte na haśle. Więcej informacji o rozszerzeniu można znaleźć w naszej dokumentacji dotyczącej [instalowania rozszerzenia przeglądarki panelu dostępu](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav).

W przypadku konieczności integrowania aplikacji logowania jednokrotnego opartego na hasłach należy zdefiniować mechanizm wdrażania rozszerzenia na dużą skalę [](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)z obsługiwanymi przeglądarkami. Opcje obejmują:

* [zasady grupy programu Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [System Center Configuration Manager (SCCM) dla programu Internet Explorer](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)

* [Pobieranie i Konfigurowanie na podstawie użytkownika dla programu Chrome, Firefox, Microsoft Edge lub IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Więcej informacji: [Jak skonfigurować Logowanie jednokrotne przy użyciu hasła](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

Użytkownicy, którzy nie korzystają z aplikacji logowania jednokrotnego opartego na hasłach, również korzystają z rozszerzenia. Te korzyści obejmują możliwość uruchamiania dowolnej aplikacji z paska wyszukiwania, znajdowania dostępu do ostatnio używanych aplikacji i posiadania linku do strony Moje aplikacje.

Oto, co użytkownik zobaczy podczas pierwszego uruchamiania aplikacji logowania jednokrotnego opartego na haśle:

![Zrzut ekranu przedstawiający ekran instalacji rozszerzenia przeglądarki Moje aplikacje ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Planowanie dostępu mobilnego

Przeglądarka chroniona przy użyciu zasad usługi Intune (Microsoft Edge lub Intune Managed Browser) będzie potrzebna użytkownikom mobilnym, którzy uruchamiają aplikacje logowania jednokrotnego oparte na hasłach. Przeglądarka chroniona zasadami umożliwia transfer hasła zapisanego dla aplikacji. Program Microsoft Edge lub zarządzana przeglądarka udostępnia zestaw funkcji ochrony danych w sieci Web. Na urządzeniach z systemem iOS i Android można również używać programu Microsoft Edge dla przedsiębiorstw. Program Microsoft Edge obsługuje te same scenariusze zarządzania co Intune Managed Browser i usprawnia środowisko użytkownika końcowego. Więcej informacji: [Zarządzanie dostępem do sieci Web za pomocą przeglądarki Microsoft Intune chronionej przez zasady](https://docs.microsoft.com/intune/app-configuration-managed-browser)

## <a name="plan-your-my-apps-deployment"></a>Planowanie wdrożenia aplikacji Moje aplikacje

Podstawa panelu dostępu to aplikacja do uruchamiania aplikacji, do których użytkownicy uzyskują dostęp [https://myapps.microsoft.com](https://myapps.microsoft.com/). Strony Moje aplikacje umożliwiają użytkownikom pojedyncze miejsce na rozpoczęcie pracy i dostęp do niezbędnych aplikacji. W tym miejscu użytkownicy mogą znaleźć listę wszystkich aplikacji, do których mają dostęp z logowaniem jednokrotnym. 

![Zrzut ekranu przedstawiający panel aplikacje](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Te same aplikacje będą wyświetlane w programie uruchamiania aplikacji pakietu Office 365, gdy użytkownicy korzystają z portalu pakietu Office 365.

Zaplanuj kolejność, w jakiej będziesz dodawać aplikacje do programu uruchamiającego moje aplikacje, oraz czy chcesz, aby stopniowo wycofać lub wszystkie jednocześnie. W tym celu Utwórz spis aplikacji zawierający listę typów uwierzytelniania i wszelkich istniejących integracji logowania jednokrotnego (SSO) dla każdej aplikacji.

#### <a name="add-applications-to-the-my-apps-panel"></a>Dodawanie aplikacji do panelu Moje aplikacje

Dowolna aplikacja obsługująca Logowanie jednokrotne w usłudze Azure AD może zostać dodana do programu uruchamiającego moje aplikacje. Inne aplikacje są dodawane przy użyciu opcji połączone Logowanie jednokrotne. Można skonfigurować kafelek aplikacji, który łączy się z adresem URL istniejącej aplikacji sieci Web. Połączone Logowanie jednokrotne pozwala rozpocząć kierowanie użytkowników do portalu My Apps bez konieczności migrowania wszystkich aplikacji do Azure Active Directory rejestracji jednokrotnej. Możesz stopniowo przechodzić do aplikacji skonfigurowanych na potrzeby rejestracji jednokrotnej usługi Azure AD bez zakłócania pracy użytkowników.

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Planowanie korzystania z aplikacji lub istniejącego portalu

Użytkownicy mogą już korzystać z aplikacji lub portalu innych niż moje aplikacje. Jeśli tak, zdecyduj, czy obsługiwać oba portale, czy też użyć tylko jednego z nich.

Jeśli istniejący portal jest już używany jako punkt wyjścia dla użytkowników, można zintegrować funkcje aplikacji przy użyciu "adresów URL dostępu użytkowników". Adresy URL dostępu użytkowników działają jako bezpośrednie linki do aplikacji dostępnych w portalu My Apps. Te adresy URL można osadzić w dowolnej istniejącej witrynie sieci Web. Gdy użytkownik kliknie link, uruchamia aplikację z portalu My Apps.

Właściwość adres URL dostępu użytkownika można znaleźć w obszarze właściwości aplikacji w Azure Portal.

![Zrzut ekranu przedstawiający panel aplikacje](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Planowanie funkcji samoobsługowego odnajdywania aplikacji i uzyskiwania dostępu

Po wdrożeniu podstawowego zestawu aplikacji na stronie Moje aplikacje dla użytkownika zalecamy włączenie funkcji samoobsługowego zarządzania aplikacjami. Funkcja odnajdywania aplikacji samoobsługowych umożliwia:
* Użytkownicy będą mogli znaleźć nowe aplikacje, które mogą dodać do swoich aplikacji. 
* Użytkownicy mogą dodawać opcjonalne aplikacje, których nie trzeba znać podczas instalacji.

Przepływy pracy zatwierdzania są dostępne dla jawnej zgody na dostęp do aplikacji. Użytkownicy, którzy są osobami zatwierdzającymi, otrzymają powiadomienia w portalu Moje aplikacje, gdy istnieją oczekujące żądania dostępu do aplikacji.

## <a name="plan-self-service-group-membership"></a>Planowanie członkostwa w grupie samoobsługowej 

Można umożliwić użytkownikom tworzenie własnych grup zabezpieczeń lub grup usługi Office 365 w usłudze Azure Active Directory (Azure AD) oraz zarządzanie nimi. Właściciel grupy może zatwierdzać lub odrzucać żądania członkostwa oraz delegować kontrolę nad członkostwem w grupie. Funkcje samoobsługowego zarządzania grupami nie są dostępne dla grup zabezpieczeń z włączoną obsługą poczty lub list dystrybucyjnych.

Aby zaplanować członkostwo w grupach samoobsługi, ustal, czy zezwolisz wszystkim użytkownikom w organizacji na tworzenie grup i zarządzanie nimi, czy tylko podzbiór użytkowników. Jeśli podzbiór użytkowników, musisz skonfigurować grupę, do której zostaną dodani te osoby. Zobacz Konfigurowanie samoobsługowego [zarządzania grupami w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) , aby uzyskać szczegółowe informacje na temat włączania tych scenariuszy.

## <a name="plan-reporting-and-auditing"></a>Planowanie raportowania i inspekcji

Usługa Azure AD udostępnia [raporty, które oferują szczegółowe informacje techniczne i biznesowe](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). Współpraca z właścicielami aplikacji firmowych i technicznych w celu założenia na własność i regularnego korzystania z tych raportów. W poniższej tabeli przedstawiono kilka przykładów typowych scenariuszy raportowania.

|   | Zarządzanie ryzykiem| Zwiększ produktywność| Zarządzanie i zgodność |
|  - |- | - | - |
| Typy raportów|  Uprawnienia i użycie aplikacji.| Działanie aprowizacji konta| Sprawdź, kto uzyskuje dostęp do aplikacji |
| Potencjalni akcje| Inspekcja dostępu; Odwołaj uprawnienia| Koryguj wszelkie błędy aprowizacji| Odwołaj dostęp |

Usługa Azure AD przechowuje większość danych inspekcji przez 30 dni. Dane są dostępne za pośrednictwem portalu administracyjnego platformy Azure lub interfejsu API, aby można było pobrać je do systemów analizy.

#### <a name="auditing"></a>Inspekcja

Dzienniki inspekcji dostępu do aplikacji są dostępne przez 30 dni. Jeśli Inspekcja zabezpieczeń w przedsiębiorstwie wymaga dłuższego przechowywania, dzienniki muszą zostać wyeksportowane do narzędzia zdarzenia informacji o zabezpieczeniach i zarządzania (SIEM), takie jak Splunk lub ArcSight.

W przypadku kopii zapasowych inspekcji, raportowania i odzyskiwania po awarii należy udokumentować wymaganą częstotliwość pobierania, system docelowy to i odpowiedzialny za zarządzanie każdą kopią zapasową. Może nie być konieczne oddzielne inspekcje i raportowanie kopii zapasowych. Kopia zapasowa odzyskiwania po awarii powinna być oddzielną jednostką.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Wdrażanie aplikacji w panelu Moje aplikacje dla użytkowników

Po skonfigurowaniu aplikacji do logowania jednokrotnego do grup są przypisywane uprawnienia dostępu. Użytkownicy w przypisanych grupach będą mieli dostęp i będą widzieć aplikację w aplikacjach moje aplikacje i uruchamiania aplikacji pakietu Office 365

Zobacz [przypisywanie użytkowników i grup do aplikacji w Active Directory](methods-for-assigning-users-and-groups.md).

Jeśli podczas testowania lub wdrażania chcesz dodać grupy, ale nie zezwolisz jeszcze na wyświetlanie aplikacji w moich aplikacjach, zobacz [ukrywanie aplikacji ze środowiska użytkownika w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Wdrażanie aplikacji Microsoft Office 365 w aplikacjach

W przypadku aplikacji pakietu Office 365 użytkownicy otrzymują kopię pakietu Office w oparciu o przypisane do nich licencje. Wymagania wstępne dotyczące dostępu do aplikacji pakietu Office przypisują użytkownikom odpowiednie licencje powiązane z aplikacjami pakietu Office. Po przypisaniu użytkownika licencja automatycznie zobaczy aplikacje skojarzone z licencją na stronie Moje aplikacje oraz w obszarze uruchamiania aplikacji usługi O365.

Jeśli chcesz ukryć zestaw aplikacji pakietu Office od użytkowników, istnieje możliwość ukrycia aplikacji z poziomu portalu My Apps, dzięki czemu można uzyskać dostęp z portalu usługi O365. Znajdź te ustawienia w części ustawienia użytkownika aplikacji. Więcej informacji: [Ukryj aplikacje ze środowiska użytkownika w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![Zrzut ekranu przedstawiający Konfigurowanie sposobu ukrywania aplikacji](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Wdrażanie funkcji samoobsługi aplikacji

Samoobsługowy dostęp do aplikacji pozwala użytkownikom na samodzielne odnajdywanie i zażądać dostępu do dowolnych aplikacji. Użytkownicy uzyskują swobodę uzyskiwania dostępu do aplikacji, których potrzebują, bez przechodzenia przez grupę IT przy każdym zażądaniu dostępu. Gdy użytkownik zażąda dostępu i jest zatwierdzany automatycznie lub ręcznie przez właściciela aplikacji, zostanie dodany do grupy na zapleczu. Raportowanie jest włączone, kto zażądał, zatwierdził lub usunął dostęp, i zapewnia kontrolę nad zarządzaniem przypisanymi rolami.

Zatwierdzanie żądań dostępu do aplikacji do osób zatwierdzających firmy można delegować. Osoby zatwierdzające w firmie mogą ustawiać hasła dostępu do aplikacji bezpośrednio na stronie Moje aplikacje osoby zatwierdzającej biznesowe.

Więcej informacji: [Jak korzystać z samoobsługowego dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)

![Zrzut ekranu przedstawiający Konfigurowanie samoobsługowego zarządzania aplikacjami](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Weryfikowanie wdrożenia

Upewnij się, że wdrożenie panelu dostępu zostało dokładnie przetestowane i że plan wycofania jest gotowy.

Poniższe testy powinny być przeprowadzane zarówno w przypadku urządzeń należących do firmy, jak i urządzeń osobistych. Te przypadki testowe powinny również odzwierciedlać Twoje przypadki użycia biznesowego. Poniżej przedstawiono kilka przypadków w oparciu o przykładowe wymagania biznesowe zawarte w tym dokumencie oraz typowe scenariusze techniczne. Dodaj inne osoby specyficzne dla Twoich potrzeb.

#### <a name="application-sso-access-test-case-examples"></a>Przykłady przypadku testowego dostępu SSO aplikacji:


| Analiza biznesowa| Oczekiwany wynik |
| - | -|
| Użytkownik loguje się do portalu My Apps| Użytkownik może zalogować się i zobaczyć swoje aplikacje |
| Użytkownik uruchamia federacyjną aplikację SSO| Użytkownik jest automatycznie zalogowany do aplikacji |
| Użytkownik uruchamia aplikację Logowanie jednokrotne po raz pierwszy| Użytkownik musi zainstalować rozszerzenie moje aplikacje |
| Użytkownik uruchamia aplikację Logowanie jednokrotne przy użyciu hasła| Użytkownik jest automatycznie zalogowany do aplikacji |
| Użytkownik uruchamia aplikację z portalu usługi O365| Użytkownik jest automatycznie zalogowany do aplikacji |
| Użytkownik uruchamia aplikację z Managed Browser| Użytkownik jest automatycznie zalogowany do aplikacji |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Przykłady przypadków testowych funkcji samoobsługi aplikacji


| Analiza biznesowa| Oczekiwany wynik |
| - | - |
| Użytkownik może zarządzać członkostwem w aplikacji| Użytkownik może dodawać/usuwać członków, którzy mają dostęp do aplikacji |
| Użytkownik może edytować aplikację| Użytkownik może edytować opis i poświadczenia aplikacji dla aplikacji SSO logowania jednokrotnego |

### <a name="rollback-steps"></a>Etapy wycofywania

Ważne jest, aby zaplanować czynności wykonywane na wypadek, gdyby wdrożenie nie było planowane. Jeśli konfiguracja logowania jednokrotnego nie powiedzie się podczas wdrażania, należy zrozumieć, jak [rozwiązywać problemy](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) z logowaniem jednokrotnym i ograniczyć wpływ na użytkowników. W skrajnych przypadkach może być konieczne [wycofanie logowania](../manage-apps/plan-sso-deployment.md)jednokrotnego.


## <a name="manage-your-implementation"></a>Zarządzanie implementacją

Firma Microsoft zaleca używanie najmniejszej roli z uprawnieniami do wykonywania wymaganych zadań w ramach Azure Active Directory. Firma Microsoft zaleca [zapoznanie się z różnymi rolami, które są dostępne](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) , a następnie wybranie ich w celu rozwiązania potrzeb każdej osoby dla tej aplikacji. Niektóre role mogą wymagać tymczasowego zastosowania i usunąć po zakończeniu wdrażania.

| Osoby| Role| Rola usługi Azure AD  |
| - | -| -|
| Administrator pomocy technicznej| Obsługa warstwy 1| Brak |
| Administrator tożsamości| Konfigurowanie i debugowanie w przypadku problemów mających wpływ na usługę Azure AD| Administrator globalny |
| Administrator aplikacji| Zaświadczanie użytkownika w aplikacji, konfiguracja dla użytkowników z uprawnieniami| Brak |
| Administratorzy infrastruktury| Właściciel przerzucania certyfikatu| Administrator globalny |
| Właściciel firmy/uczestnik projektu| Zaświadczanie użytkownika w aplikacji, konfiguracja dla użytkowników z uprawnieniami| Brak |

Zalecamy używanie [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) do zarządzania rolami w celu zapewnienia dodatkowej inspekcji, kontroli i przeglądu dostępu użytkownikom z uprawnieniami do katalogów.

### <a name="troubleshoot-access-panel-issues"></a>Rozwiązywanie problemów z panelem dostępu

Utwórz przewodniki dotyczące rozwiązywania problemów dla organizacji pomocy technicznej, korzystając z typowych scenariuszy i wskazując dokumentację firmy Microsoft dotyczącą ich rozwiązań. Możesz chcieć utworzyć prowadnice, które dzielą obsługę na warstwy używane przez organizację.

Zobacz poniższe przewodniki dotyczące rozwiązywania problemów, aby uzyskać informacje na temat:

[Niewyświetlane aplikacje](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Pojawiły się nieoczekiwane aplikacje](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[Użytkownik nie może zalogować się do panelu dostępu](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problemy przy użyciu samoobsługowego dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problemy z rozszerzeniem przeglądarki](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Następne kroki

[Planowanie wdrożenia Azure Active Directory uwierzytelniania wieloskładnikowego](https://aka.ms/deploymentplans/mfa)
