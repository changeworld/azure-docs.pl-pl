---
title: Lista kontrolna dotycząca wdrażania usługi Azure AD
description: Usługa Azure Active Directory funkcji listy kontrolnej dotyczącej wdrożenia
services: active-directory
ms.service: active-directory
ms.subservice: ''
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: e668a5238859d8cd8c2a7797200a12197ce72be9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110470"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Przewodnik wdrażania funkcji w usłudze Azure Active Directory

Może wydawać się czasochłonnym do wdrożenia usługi Azure Active Directory (Azure AD) w Twojej organizacji i przechowuj w bezpiecznym miejscu. W tym artykule identyfikuje typowe zadania, czy klienci są przydatne do wykonania w fazach, w ciągu 30, 60, 90 dni lub więcej, aby zwiększyć ich bezpieczeństwo stan. Nawet organizacji, którzy już wdrożyli usługę Azure AD ten przewodnik służy do upewnij się, że pojawiają się one w pełni wykorzystać ich inwestycję.

Infrastruktura tożsamości dobrze zaplanowane i są wykonywane drogę do bezpiecznego dostępu wydajność obciążenia i dane przez wybranych użytkowników i tylko na urządzeniach.

Dodatkowo klienci mogą zapoznać się ich [tożsamości secure wynik](identity-secure-score.md) aby zobaczyć, jak wyrównany mają one najlepszych rozwiązań firmy Microsoft. Sprawdź wynik bezpiecznego przed i po zaimplementowaniu te zalecenia, aby zobaczyć, jak dobrze robią porównaniu do innych osób w Twojej branży i inne organizacje rozmiaru.

## <a name="prerequisites"></a>Wymagania wstępne

Wiele zaleceń w tym przewodniku można zaimplementować przy użyciu usługi Azure AD bezpłatna, podstawowa lub żadna licencja wcale. Gdy licencje nie są wymagane firma Microsoft stanu, które licencja jest wymagana co najmniej do wykonania zadania.

Dodatkowe informacje na temat licencjonowania można znaleźć na następujących stronach:

* [Licencjonowanie usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Usługi AD B2B licencjonowania wskazówki dotyczące platformy Azure](../b2b/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Faza 1: Tworzenie fundamentów zabezpieczeń

W tej fazie Administratorzy włączyć funkcje zabezpieczeń linii bazowej, aby utworzyć podstawę bardziej bezpieczny i łatwy w użyciu w usłudze Azure AD, zanim będziemy zaimportuj lub Utwórz zwykłych kont użytkowników. Ta faza podstawowe zapewnia są w stanie bardziej bezpieczne od samego początku i że użytkownicy końcowi mają tylko wprowadzenie do nowych pojęć jeden raz.

| Zadanie | Szczegóły | Wymagana licencja |
| ---- | ------ | ---------------- |
| [Wyznaczenie więcej niż jednego administratora globalnego](../users-groups-roles/directory-emergency-access.md) | W przypadku zagrożenia, należy przypisać co najmniej dwa konta stałe administratora globalnego tylko w chmurze do użycia. Te konta nie są służyć codziennie i powinien mieć długich i złożonych haseł. | Usługa Azure AD — warstwa Bezpłatna |
| [Użyj nieglobalnych ról administracyjnych, jeśli jest to możliwe](../users-groups-roles/directory-assign-admin-roles.md) | Oferowanie administratorów dostępu, które są im potrzebne tylko potrzebne im dostęp do obszarów. Nie wszystkie Administratorzy muszą być administratorów globalnych. | Usługa Azure AD — warstwa Bezpłatna |
| [Włącz Privileged Identity Management dla śledzenia użycia roli administratora](../privileged-identity-management/pim-getting-started.md) | Włącz Privileged Identity Management rozpocząć śledzenie użycia rolę administracyjną. | Usługa Azure AD — warstwa Premium P2 |
| [Wdrażanie funkcji samoobsługowego resetowania haseł](../authentication/howto-sspr-deployment.md) | Zmniejszyć do działu pomocy technicznej związane z resetowaniem haseł, umożliwiając pracownikom resetować swoje hasła przy użyciu zasad jako kontrolkę, która jest administratorem. | Usługa Azure AD — warstwa Podstawowa |
| [Tworzenie listy zakazanych haseł w określonych niestandardowych organizacji](../authentication/howto-password-ban-bad-configure.md) | Uniemożliwić użytkownikom tworzenie haseł, które zawierają powszechnie używanych wyrazów ani fraz z Twojej organizacji lub obszaru. | Usługa Azure AD — warstwa Podstawowa |
| [Włączanie integracji środowiska lokalnego z ochrona za pomocą hasła usługi Azure AD](../authentication/concept-password-ban-bad-on-premises.md) | Rozszerz do katalogu lokalnego, zapewnienie haseł zestawu w środowisku lokalnym są również zgodne z globalnej listy zakazanych haseł i specyficznym dla dzierżawy zakazane listy haseł. | Usługa Azure AD — warstwa Premium P1 |
| [Włączyć wskazówki dotyczące haseł firmy Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Zatrzymaj wymagania użytkowników, aby zmienić swoje hasło, zgodnie z ustalonym harmonogramem, wyłącz wymagania co do złożoności i użytkowników są bardziej apt haseł i zachować coś, co jest bezpieczne. | Usługa Azure AD — warstwa Bezpłatna |
| [Wyłącz resetowania okresowe hasła dla kont użytkowników w chmurze](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Resetowanie hasła okresowe należy zachęcać użytkowników do zwiększyć ich istniejące hasła. Skorzystaj z wskazówek w dokumencie wskazówki dotyczące haseł firmy Microsoft i takie same jak stosowane zasady lokalne do użytkowników tylko w chmurze. | Usługa Azure AD — warstwa Bezpłatna |
| [Dostosowywanie usługi Azure Active Directory inteligentnej blokady](../authentication/howto-password-smart-lockout.md) | Zatrzymaj blokady z użytkowników oparte na chmurze są replikowane do użytkowników usługi Active Directory w środowisku lokalnym | Usługa Azure AD — warstwa Podstawowa |
| [Włącz blokowanie ekstranetu w usługach inteligentnego dla usług AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | Blokowanie ekstranetu w usługach AD FS chroni przed atakami zgadywanie ataki, pozwalając na uprawnionych użytkowników usług AD FS, w dalszym ciągu używać swoich kont. | |
| [Wdrażanie usługi Azure AD Multi-Factor Authentication za pomocą zasad dostępu warunkowego](../authentication/howto-mfa-getstarted.md) | Wymagać od użytkowników do przeprowadzenia weryfikacji dwuetapowej, podczas uzyskiwania dostępu do aplikacji zawierających poufne dane za pomocą zasad dostępu warunkowego. | Usługa Azure AD — warstwa Premium P1 |
| [Włączanie usługi Azure Active Directory Identity Protection](../identity-protection/enable.md) | Włącz śledzenie ryzykownych logowań i naruszonymi poświadczeniami dla użytkowników w Twojej organizacji. | Usługa Azure AD — warstwa Premium P2 |
| [Wyzwalanie uwierzytelnianie wieloskładnikowe i zmiany hasła przy użyciu zdarzeń o podwyższonym ryzyku](../authentication/tutorial-risk-based-sspr-mfa.md) | Włączanie automatyzacji, które mogą wyzwalać zdarzenia, takie jak uwierzytelnianie wieloskładnikowe, resetowania hasła i blokowania logowania na podstawie ryzyka dotyczącego. | Usługa Azure AD — warstwa Premium P2 |
| [Włącz konwergentnej rejestracji samoobsługowego resetowania haseł i uwierzytelniania wieloskładnikowego w usłudze Azure AD (wersja zapoznawcza)](../authentication/concept-registration-mfa-sspr-converged.md) | Użytkownicy będą mogli zarejestrować się na jednym środowisku wspólnego zarówno dla usługi Azure Multi-Factor Authentication i Samoobsługowe resetowanie haseł. | Usługa Azure AD — warstwa Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Faza 2: Importowanie użytkowników, Włącz synchronizację i zarządzania urządzeniami

Następnie dodamy podstawa zawartymi w fazie 1 przez zaimportowanie naszych użytkowników i włączanie synchronizacji, planowanie dostęp gościa i przygotowanie do obsługi dodatkowych funkcji.

| Zadanie | Szczegóły | Wymagana licencja |
| ---- | ------ | ---------------- |
| [Instalowanie programu Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md) | Przygotuj się do synchronizowania użytkowników z istniejącego katalogu lokalnego do chmury. | Usługa Azure AD — warstwa Bezpłatna |
| [Implementowanie synchronizacji skrótów haseł](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | Synchronizacja skrótów haseł w celu umożliwienia zmiany hasła powinny być replikowane, wykrywanie nieprawidłowego hasła oraz korygowania i raportowania ujawnione poświadczenia. | Usługa Azure AD — warstwa Bezpłatna |
| [Implementowanie funkcji zapisywania zwrotnego haseł](../authentication/howto-sspr-writeback.md) | Zezwalaj na zmienianie haseł w chmurze, aby być zapisywane z powrotem do środowiska usługi Active Directory systemu Windows Server w środowisku lokalnym. | Usługa Azure AD — warstwa Premium P1 |
| [Implementowanie usługi Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md) | Aby włączyć monitorowanie kondycji kluczy statystyk dla serwerów usługi Azure AD Connect, serwery usług AD FS i kontrolery domeny. | Usługa Azure AD — warstwa Premium P1 |
| [Przypisywanie licencji do użytkowników, członkostwa w grupach w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-assign.md) | Oszczędzić czas i nakład pracy, tworząc grupy licencji, które włączać lub wyłączać funkcje, według grupy zamiast ustawiać dla poszczególnych użytkowników. | |
| [Utwórz plan dla dostępu użytkowników-gości](../b2b/what-is-b2b.md) | Współpracuj z użytkowników-gości, umożliwiając im logowania do aplikacji i usług za pomocą ich własnych służbowego lub tożsamości w sieciach społecznościowych. | [Usługi AD B2B licencjonowania wskazówki dotyczące platformy Azure](../b2b/licensing-guidance.md) |
| [Podejmij decyzję strategii zarządzania urządzeniami](../devices/overview.md) | Zdecyduj, Twoja organizacja pozwala dotyczące urządzenia. Rejestrowanie programu vs, dołączenie do programu vs Przynieś własne urządzenie firmy pod warunkiem. | |
| [Wdrażanie Windows Hello dla firm w organizacji](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Przygotowanie do uwierzytelniania bez hasła przy użyciu Windows Hello | |

## <a name="phase-3-manage-applications"></a>Faza 3: Zarządzanie aplikacjami

W dalszym ciągu kompilacji na poprzednich faz, możemy zidentyfikować aplikacje Release candidate migracji i integracji z usługą Azure AD i ukończyć instalację tych aplikacji.

| Zadanie | Szczegóły | Wymagana licencja |
| ---- | ------ | ---------------- |
| Identyfikowanie aplikacji | Określenie aplikacji w Twojej organizacji: w środowisku lokalnym, aplikacje SaaS w chmurze i innych aplikacji line-of-business. Określ, jeśli te aplikacje można i powinny być zarządzane za pomocą usługi Azure AD. | Wymagana żadna licencja |
| [Integrowanie obsługiwanych aplikacji SaaS w galerii](../manage-apps/add-application-portal.md) | Usługa Azure AD ma galerii, który zawiera tysiące wstępnie zintegrowanych aplikacji. Niektóre aplikacje, które Twoja organizacja korzysta z prawdopodobnie w galerii jest dostępny bezpośrednio z witryny Azure portal. | Usługa Azure AD — warstwa Bezpłatna |
| [Użyj serwera Proxy aplikacji pozwala zintegrować aplikacje w środowisku lokalnym](../manage-apps/application-proxy-add-on-premises-application.md) | Serwer Proxy aplikacji umożliwia użytkownikom dostęp do aplikacji lokalnych, logując się przy użyciu swojego konta usługi Azure AD. | Usługa Azure AD — warstwa Podstawowa |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Faza 4: Inspekcja uprzywilejowanych tożsamości, kończenie przeglądu dostępu i zarządzanie cyklem życia użytkowników

Faza 4 widzi administratorom wymuszania co najmniej zasad uprawnień dla administracji, wykonanie ich pierwszym przeglądów dostępu i włączanie automatyzacji typowych zadań z cyklem życia użytkowników.

| Zadanie | Szczegóły | Wymagana licencja |
| ---- | ------ | ---------------- |
| [Wymuszanie użycia programu Privileged Identity Management](../privileged-identity-management/pim-security-wizard.md) | Usuń role administracyjne z konta zwykłego użytkownika dnia na dzień. Użytkownicy z uprawnieniami administracyjnymi należy kwalifikujących się do korzystania z ich rolą po sukcesy wyboru usługi Multi-Factor authentication, zapewniając uzasadnienie biznesowe lub żądanie zatwierdzenia od wyznaczone osoby zatwierdzające. | Usługa Azure AD — warstwa Premium P2 |
| [Kończenie przeglądu dostępu dla ról katalogu usługi Azure AD w usłudze PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Praca z zabezpieczeń i kierownictwo zespołom tworzenie zasad przeglądu dostępu do przeglądu dostępu administracyjnego na podstawie zasad w organizacji. | Usługa Azure AD — warstwa Premium P2 |
| [Implementowanie zasad członkostwa grupy dynamicznej](../users-groups-roles/groups-dynamic-membership.md) | Aby automatycznie przypisać użytkowników do grup na podstawie ich atrybutów z działu KADR (lub źródło prawdziwych danych), takich jak dział, tytuł, region i inne atrybuty, należy użyć grup dynamicznych. |  |
| [Implementowanie udostępniania aplikacji na podstawie grupy](../manage-apps/what-is-access-management.md) | Użyj dostępu oparte na grupach zarządzania aprowizacji automatycznie aprowizować użytkowników do aplikacji SaaS. |  |
| [Automatyzowanie i cofania aprowizacji użytkowników](../manage-apps/user-provisioning.md) | Usuń wymagane ręczne wykonanie czynności z cyklu życia konta pracowników w celu uniemożliwienia nieupoważnionego dostępu. Synchronizowanie tożsamości ze źródłem prawdziwych danych (w systemie HR) do usługi Azure AD. |  |

## <a name="next-steps"></a>Kolejne kroki

[Usługa Azure AD licencjonowania i szczegóły cennika](https://azure.microsoft.com/pricing/details/active-directory/)

[Konfiguracje dostępu tożsamości i urządzenia](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Typowe zalecane zasady dostępu do tożsamości i urządzenia](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
