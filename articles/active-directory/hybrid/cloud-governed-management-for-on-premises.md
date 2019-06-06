---
title: Chmury usługi AD systemu Azure podlegają zarządzania dla obciążeń w środowisku lokalnym — Azure
description: W tym temacie opisano zarządzanie w chmurze, które podlegają dla obciążeń lokalnych.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b7e54f6acfe1cbbe6e46fe92d132ebdaa91ff33
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742340"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>W jaki sposób usługa Azure AD zapewnia chmura podlegają zarządzania dla obciążeń w środowisku lokalnym

Azure Active Directory (Azure AD) to kompleksowe tożsamości jako rozwiązanie usługą (IDaaS) używane przez miliony organizacji, które rozciągają się wszystkie aspekty tożsamości, zarządzania dostępem i bezpieczeństwa. Usługa Azure AD zawiera ponad miliard tożsamości użytkowników i pomaga użytkownikom, zaloguj się i bezpiecznie uzyskiwać dostęp do obu:

* Zasoby zewnętrzne, takie jak Microsoft Office 365, witryny Azure portal i tysiącach innych aplikacji Software-as-a-Service (SaaS).
* Zasobów wewnętrznych, takich jak aplikacje w sieci firmowej organizacji i intranet, wraz z dowolnej aplikacji w chmurze opracowanych przez tę organizację.

Organizacje mogą używać usługi Azure AD, jeśli są one "czystego chmura" lub jako "hybrydowe" wdrożenie niepowodzeniem, jeśli mają one obciążeń lokalnych. Wdrożenie hybrydowe usługi Azure AD może być częścią strategii dla organizacji migracji swoich zasobów IT do chmury lub zintegrować z istniejącą infrastrukturą lokalną wraz z nowych usług w chmurze w dalszym ciągu.

W przeszłości organizacji "hybrydowe" Zauważyliśmy już Azure AD zgodnie z rozszerzeniem istniejącej infrastruktury lokalnej. W przypadku tych wdrożeń administracji nadzoru tożsamości w środowisku lokalnym, Windows Server Active Directory lub innych systemów wewnętrznych directory są punkty kontrolne, a użytkownicy i grupy są synchronizowane z tych systemów do katalogu w chmurze takich jak usługa Azure AD. Po tych tożsamości w chmurze, ich można udostępnione do usługi Office 365, Azure i innych aplikacji.

![Cykl życia tożsamości](media/cloud-governed-management-for-on-premises//image1.png)

Gdy organizacje więcej swoją infrastrukturę IT oraz ich aplikacji w chmurze, wiele szukają ulepszone zabezpieczenia i możliwości zarządzania uproszczone zarządzanie tożsamością jako usługa. Funkcje IDaaS świadczona w chmurze w usłudze Azure AD Przyspiesz przejście do zarządzanych zarządzania w chmurze, zapewniając możliwości, które umożliwiają organizacjom szybko przyjmuje i przenieść jeden z jego Zarządzanie tożsamościami z tradycyjnych lokalnych i rozwiązania Systemy z usługą Azure AD, pozostawiając do obsługi istniejących, jak również nowych aplikacji.

W tym dokumencie opisano strategię firmy Microsoft dla hybrydowych IDaaS i w tym artykule opisano, jak organizacje mogą używać usługi Azure AD dla swoich istniejących aplikacji.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Podejście usługi Azure AD do zarządzania tożsamościami zarządzanych w chmurze

Jak organizacje przejście do chmury muszą one gwarancji, że procedury nadzoru nad ich pełne środowisko — większe bezpieczeństwo i więcej widoczność działań, obsługiwane przez usługi automation i proaktywne szczegółowych informacji. "**Chmury podlegają zarządzania**" w tym artykule opisano sposób zarządzania i zarządzać użytkownicy, aplikacje, grup i urządzeń w chmurze w organizacji.

W tym współczesnym świecie organizacje muszą mieć możliwość efektywne zarządzanie na dużą skalę, ze względu na rozprzestrzenianie aplikacje SaaS i zwiększenie roli współpracy i tożsamości zewnętrznych. Nowe pozioma ryzyka chmury oznacza organizacji musi być krótszy — złośliwego aktora, który obniża użytkownika chmury mogą mieć wpływ na aplikacje w chmurze i lokalnych.

W szczególności hybrydowe, organizacje muszą mieć możliwość delegowania i zautomatyzować zadania, które w przeszłości IT została ręcznie. Aby zautomatyzować zadania, muszą one interfejsów API i procesy, które możesz odpowiednio organizować w cyklu życia różnych zasobów związanych z tożsamościami (użytkowników, grup, aplikacji, urządzeń), dzięki czemu można delegować bieżące zarządzanie tymi zasobami więcej osób spoza podstawowe personel IT. Usługa Azure AD adresów tych wymagań, za pośrednictwem zarządzania kontami użytkowników i natywnego uwierzytelniania użytkowników bez konieczności infrastruktury do obsługi tożsamości lokalnych. Nie kompilowania na infrastrukturę lokalną, mogą korzystać organizacje, które mają nowych społeczności użytkowników, takie jak partnerów biznesowych, które nie pochodzą swojego katalogu lokalnego, ale których zarządzania dostępem jest krytyczna do osiągania wyników biznesowych.

Ponadto zarządzania nie zostało zakończone bez nadzoru---i nadzoru w całkiem nowym świecie jest zintegrowaną częścią systemu tożsamości, a nie w dodatku. Zarządzanie tożsamościami umożliwia organizacjom zarządzanie tożsamością i dostępem cyklu życia różnych pracowników, partnerów biznesowych oraz dostawców i usługach i aplikacjach.

Dołączanie zarządzania tożsamościami oraz ułatwia włączanie organizacji, do którego nastąpi przejście do chmury zarządzanych zarządzania oraz umożliwia IT, aby skalować, rozwiązuje wyzwania związane z gości i zapewnia bardziej szczegółowe informacje i automatyzacji niż klienci miał z na infrastrukturę lokalną. Zarządzanie w całkiem nowym świecie oznacza, że możliwości dla organizacji, aby mieć przejrzystości, wglądu i odpowiednie środki kontroli dostępu do zasobów w organizacji. Za pomocą usługi Azure AD operacje zabezpieczeń i inspekcji zespoły mają wgląd w użytkowników mających---i kto powinien mieć — dostęp do zasobów w organizacji (na jakich urządzeniach), użytkownicy ci korzystają z tego dostępu i tego, czy organizacja ma i używa odpowiednich Określa, aby usunąć lub ograniczanie dostępu zgodnie z firmy lub przepisami zasad.

Nowy model zarządzania korzyści organizacji mających zarówno aplikacji SaaS i line-of-business (LOB), ponieważ są w stanie łatwiejsze zarządzanie i zabezpieczanie dostępu do tych aplikacji. Integrowanie aplikacji z usługą Azure AD, organizacji będą mogli używać oraz zarządzanie dostępem w chmurze zarówno i środowiska lokalnego spójne pochodzi tożsamości. Zarządzanie cyklem życia aplikacji staje się bardziej zautomatyzowanej, a usługa Azure AD zapewnia rozbudowane wgląd w użycie aplikacji, której nie niezwykle łatwe zarządzanie tożsamościami w środowisku lokalnym. Za pomocą usługi Azure AD, grupy usługi Office 365 i funkcji samoobsługi zespoły organizacji można łatwo tworzyć grupy na potrzeby zarządzania dostępem i współpracy i dodawać i usuwać użytkowników w chmurze, aby włączyć współpracy i zarządzania wymaganiami dotyczącymi dostępu.

Wybieranie funkcji po prawej stronie w usłudze Azure AD dla chmury, zarządzanie zarządzanych zależy od aplikacji, które ma być używany i integracji tych aplikacji z usługą Azure AD. W poniższych sekcjach opisano metody do wykonania dla aplikacji zintegrowanych z usługą AD i aplikacje, które używają protokołów federacyjnych (na przykład protokołu SAML, OAuth lub OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Zarządzanie w chmurze, które podlegają aplikacji zintegrowanych z usługą AD

Usługa Azure AD zwiększa zarządzania w organizacji i lokalnych aplikacji zintegrowanych z usługą Active Directory za pośrednictwem bezpiecznego dostępu zdalnego i dostępu warunkowego do tych aplikacji. Ponadto usługi Azure AD zapewnia również, Zarządzanie cyklem życia konta i Zarządzanie poświadczeniami dla użytkownika istniejących kont usługi AD, w tym:

* **Bezpiecznego dostępu zdalnego i dostępu warunkowego dla aplikacji lokalnych**

Pierwszym krokiem zarządzania dostępem w chmurze zintegrowanych z usługą AD w środowisku lokalnym w sieci web i aplikacji opartych na pulpitu zdalnego w wielu organizacjach jest wdrożenie [serwera proxy aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) przed tych aplikacji w celu zapewnienia bezpiecznego dostęp zdalny.

Po logowanie jednokrotne do usługi Azure AD użytkownicy mogą korzystać zarówno z chmury i lokalnych aplikacji za pomocą zewnętrznego adresu URL lub portalu wewnętrznych aplikacji. Na przykład serwer Proxy aplikacji udostępnia dostępu zdalnego i logowania jednokrotnego do usług pulpitu zdalnego, SharePoint, a także aplikacji, takich jak Tableau, Qlik i aplikacje biznesowe (LOB). Ponadto, zasady dostępu warunkowego obejmują wyświetlanie [warunki użytkowania](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) i [zapewnienie użytkownik zgodził się do nich](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) przed uzyskaniem dostępu do aplikacji.

![Architektura serwera Proxy aplikacji](media/cloud-governed-management-for-on-premises/image2.png)

* **Automatyczne cyklem życiowym dla kont usługi Active Directory**

Zarządzanie tożsamościami oraz pomaga organizacjom osiągać równowagę między *produktywność* ---jak szybko można osoby mają dostęp do zasobów potrzebują, np. gdy zostają dołączone organizacji? ---i *zabezpieczeń* ---jak należy ich dostęp zmieniać wraz z upływem czasu, takie jak zmiany stanu zatrudnienia osoby? Zarządzanie cyklem życia tożsamości jest podstawą dla zarządzania tożsamościami i skuteczne nadzór w odpowiedniej skali wymaga modernizacji infrastruktury zarządzania cyklem życia tożsamości dla aplikacji.

W przypadku wielu organizacji cyklem życia tożsamości dla pracowników jest powiązany do reprezentacji tego użytkownika w systemie zarządzania kapitałem ludzkim (HCM). W przypadku organizacji korzystanie z produktu Workday jako ich systemu HCM, usługi Azure AD można rolą jest pilnowanie kont użytkowników w AD [automatycznie aprowizowane i anulowanie aprowizacji dla pracowników w produkcie Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial). Dlatego prowadzi do ulepszone produktywność dzięki automatyzacji birthright kont i zarządza ryzykiem poprzez zapewnienie aplikacji dostępu jest aktualizowane automatycznie po użytkownik zmienia role lub opuścił organizację. Aprowizacja użytkowników oparte na dzień roboczy [planu wdrożenia](https://aka.ms/WorkdayDeploymentPlan) jest przewodnik krok po kroku, który organizacji przez implementację najlepsze rozwiązania w zakresie produktu Workday do użytkownika i inicjowania obsługi usługi Active Directory rozwiązania w ramach kroku 5 procesu.

Usługa Azure AD Premium obejmuje również programu Microsoft Identity Manager, który można zaimportować rekordy z innych systemów HCM w środowisku lokalnym, w tym SAP, Oracle eBusiness i Oracle PeopleSoft.

Współpracy między firmami wymaga coraz częściej, udzielanie dostępu do osób spoza organizacji. [Usługa Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) współpracy umożliwia organizacjom bezpieczne udostępnianie swoich aplikacji i usług użytkownicy-goście i partnerom zewnętrznym przy jednoczesnym zachowaniu kontroli nad ich danymi firmowymi.

Usługa Azure AD można [automatycznie tworzyć konta w AD dla użytkowników-gości](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) zgodnie z potrzebami, goście biznesowi, aby uzyskać dostęp do lokalnych aplikacji zintegrowanych z usługą AD bez konieczności używania innego hasła. Organizacje mogą skonfigurować [zasady uwierzytelniania wieloskładnikowego (MFA) dla użytkownika-gościa](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)s, dzięki czemu MFA sprawdza, czy są wykonywane podczas uwierzytelniania serwera proxy aplikacji. Ponadto wszystkie [przeglądów dostępu](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) , są wykonywane w chmurze B2B, użytkownicy mają zastosowanie do użytkowników w środowisku lokalnym. Na przykład usunięcie użytkownika chmury przy użyciu zasad zarządzania cyklem życia użytkowników w środowisku lokalnym jest również usunięte.

**Poświadczenia zarządzania dla kont usługi Active Directory** usługi Azure AD użytkownika samoobsługowego resetowania haseł pozwala użytkownikom, który zapomniał hasła, aby być ponownie uwierzytelniany i zresetowania hasła za pomocą zmiany haseł [zapisywane do lokalnej usługi Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback). Proces resetowania hasła można również użyć zasad haseł usługi Active Directory w środowisku lokalnym: Jeśli użytkownik resetuje hasła, jest sprawdzany w celu upewnij się, że spełnia on w lokalnych zasadach usługi Active Directory przed zatwierdzeniem go do tego katalogu. Samoobsługowe resetowanie haseł [planu wdrożenia](https://aka.ms/deploymentplans/sspr) najlepsze rozwiązania przeprowadzić Samoobsługowe resetowanie haseł w użytkowników za pośrednictwem sieci web i Windows zintegrowanych środowisk.

![Architektura usługi Azure AD SSPR](media/cloud-governed-management-for-on-premises/image3.png)

Na koniec dla organizacji, które pozwalają użytkownikom na zmianę ich haseł w AD, można skonfigurować usługi AD do użycia tego samego hasła jak organizacji w usłudze Azure AD za pomocą [funkcji ochrony hasła usługi Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), obecnie w publiczna wersja zapoznawcza.

Gdy organizacja jest gotowa do przejścia aplikacji w chmurze zintegrowanych z usługą AD, przenosząc hostowanie aplikacji na platformie Azure, system operacyjny [usług domenowych Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) to usługi domenowe AD zgodne (takich jak przyłączanie do domeny, grupy zasad, LDAP i Kerberos/NTLM uwierzytelniania). Azure AD Domain Services integruje się z organizacji istniejącej dzierżawy usługi Azure AD, co umożliwia użytkownikom zalogowanie się przy użyciu swoich poświadczeń firmowych. Ponadto istniejących grup i kont użytkowników może służyć do zabezpieczania dostępu do zasobów, zapewniając gładsze "lift-and-shift" zasobów lokalnych do usług infrastruktury platformy Azure.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Zarządzanie w chmurze, które podlegają dla lokalnych aplikacji federacyjnych

Dla organizacji, która już korzysta z dostawcy tożsamości w środowisku lokalnym przenoszenie aplikacji do usługi Azure AD umożliwia bardziej bezpieczny dostęp i łatwiejsze w obsłudze środowisko administracyjne do federacyjnego zarządzania. Usługa Azure AD umożliwia konfigurowanie formantów szczegółową dostęp do poszczególnych aplikacji, w tym usługi Azure Multi-Factor Authentication, przy użyciu dostępu warunkowego usługi Azure AD. Usługa Azure AD obsługuje więcej możliwości, takie jak certyfikaty podpisywania tokenu specyficzne dla aplikacji i konfigurowalne daty wygaśnięcia certyfikatów. Te funkcje, narzędzia i wskazówki pozwalają organizacjom wycofania ich dostawców tożsamości w środowisku lokalnym. Firmy Microsoft IT, na przykład jeden przeniósł 17,987 aplikacji od firmy Microsoft wewnętrznego Active Directory Federation Services (AD FS) do usługi Azure AD.

![Ewolucja usługi Azure AD](media/cloud-governed-management-for-on-premises/image5.png)

Aby rozpocząć migrację aplikacji federacyjnych do usługi Azure AD jako dostawcy tożsamości, zobacz https://aka.ms/migrateapps zawierającą linki do:

* Oficjalny dokument [migracji Twojej aplikacji do usługi Azure Active Directory](https://aka.ms/migrateapps/whitepaper), które prezentuje zalety migracji i opisuje sposób planowania migracji w czterech fazach jasno opisane: Odnajdywanie, klasyfikacji, migracji i bieżące zarządzanie. Poprowadzą Cię kolejne kroki sposobu myślenia o procesie i podzielić projekt na łatwe gotowego do konsumpcji części. W całym dokumencie podano linki do ważnych zasobów, które pomogą Ci po drodze.

* Przewodnik po rozwiązaniu [migrowanie aplikacji uwierzytelnianie usług federacyjnych Active Directory do usługi Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide) Eksploruje bardziej szczegółowo takie same cztery fazy planowania i wykonywania projektu migracji aplikacji . W tym przewodniku dowiesz się, jak zastosować te etapy do określonego celu, przenoszenie aplikacji z usługi Active Directory Federation Services (AD FS) do usługi Azure AD.

* [Aktywnego katalogu Federacji usługi migracji gotowości skryptu](https://aka.ms/migrateapps/adfstools) mogą być uruchamiane na istniejące serwery usługi Active Directory Federation Services (AD FS) w środowisku lokalnym w celu określenia gotowości aplikacji pod kątem migracji do usługi Azure AD.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Zarządzanie dostępem ciągły w aplikacji w chmurze i lokalnych

Organizacje wymagają procesu do zarządzania dostępem, która jest skalowalna. Użytkownicy nadal są gromadzone prawa dostępu i na końcu poza co początkowo została aprowizowana dla nich. Ponadto przedsiębiorstwa to organizacje muszą być możliwe wydajne skalowanie do tworzenia i wymuszania zasad dostępu i kontroli w sposób ciągły.

Zazwyczaj IT delegatów dostępu zatwierdzeniu podjęte decyzje w kwestii osobom podejmującym decyzje biznesowe. Ponadto IT może obejmować samych użytkowników. Na przykład trzeba znać zasady firmy użytkowników uzyskujących dostęp do danych poufnych klientów w aplikacji marketingowych firmy w Europie. Użytkowników-gości może być również niebranie pod uwagę wymagania dotyczące przetwarzania danych w organizacji, do których one zaproszono.

Organizacje takie jak zautomatyzować proces cyklu życia dostępu za pomocą technologii [grup dynamicznych](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership), powiązanych z aprowizacji użytkowników do [aplikacji SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list), lub [aplikacji zintegrowane, przy użyciu systemu do zarządzania tożsamościami między domenami (Standard SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)) standardowa. Organizacje również można kontrolować, które [użytkownicy-goście mają dostęp do aplikacji lokalnych](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises). Te mogą praw dostępu, a następnie być regularnie analizowane za pomocą cykliczne [przeglądy dostępu w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

## <a name="future-directions"></a>Przyszłe kierunki

W środowiskach hybrydowych strategii firmy Microsoft jest umożliwienie wdrożeń gdzie **chmura znajduje się na płaszczyźnie kontroli dla tożsamości**i w katalogach lokalnych, jak i innych systemów tożsamości, takich jak usługi Active Directory i innych rozwiązań lokalnych aplikacje, czy element docelowy akcji aprowizacji użytkowników z dostępem. Ta strategia, będą w dalszym ciągu upewnij się, prawa, tożsamości i dostępu w tych aplikacji i obciążeń, które korzystają z nich. W tym stanie zakończenia organizacji będzie można zwiększyć wydajność użytkowników końcowych całkowicie w chmurze.

![Architektura usługi Azure AD](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o tym, jak rozpocząć pracę postępowania, zobacz planów wdrożenia usługi Azure AD, znajdujący się w <https://aka.ms/deploymentplans> . Zapewniają one end-to-end wskazówki dotyczące wdrażania funkcji usługi Azure Active Directory (Azure AD). Każdy plan wyjaśnia wartości biznesowej planowania zagadnienia, projektowania i potrzebny, pomyślnie wdrożyć funkcję typowe funkcje związane z usługi Azure AD procedur operacyjnych. Firma Microsoft stale aktualizuje planów wdrożenia z najlepszymi rozwiązaniami wynikającymi z wdrożeń klienta i inne opinie, po dodaniu nowych możliwości zarządzania w chmurze z usługą Azure AD.
