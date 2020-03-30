---
title: Lista kontrolna dotycząca wdrażania usługi Azure AD
description: Lista kontrolna wdrażania funkcji usługi Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: ''
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: f84226a631014b51338d47887fe3bafc969dc571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063649"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Przewodnik wdrażania funkcji usługi Azure Active Directory

Wdrożenie usługi Azure Active Directory (Azure AD) w organizacji i zapewnienie jej bezpieczeństwa może wydawać się trudne. W tym artykule identyfikowane są typowe zadania, które klienci uważają za pomocne w fazach, w ciągu 30, 60, 90 dni lub więcej, aby zwiększyć ich postawę zabezpieczeń. Nawet organizacje, które już wdrożyły usługę Azure AD, mogą użyć tego przewodnika, aby upewnić się, że w pełni wykorzystują swoje inwestycje.

Dobrze zaplanowana i wykonana infrastruktura tożsamości toruje drogę do bezpiecznego dostępu do obciążeń i danych zwiększających produktywność tylko przez znanych użytkowników i urządzenia.

Ponadto klienci mogą sprawdzić swój [wynik bezpieczne tożsamości,](identity-secure-score.md) aby zobaczyć, jak są wyrównane są do najlepszych rozwiązań firmy Microsoft. Sprawdź swój bezpieczny wynik przed i po wdrożeniu tych zaleceń, aby zobaczyć, jak dobrze sobie radzisz w porównaniu z innymi osobami w twojej branży i innymi organizacjami o twoim rozmiarze.

## <a name="prerequisites"></a>Wymagania wstępne

Wiele zaleceń zawartych w tym przewodniku można zaimplementować za pomocą bezpłatnej usługi Azure AD lub w ogóle nie ma licencji. W przypadku, gdy wymagane są licencje, określamy, która licencja jest wymagana co najmniej do wykonania zadania.

Dodatkowe informacje na temat licencjonowania można znaleźć na następujących stronach:

* [Zarządzanie licencjonowaniem w usłudze Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Wskazówki dotyczące licencjonowania usługi Azure AD B2B](../b2b/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Faza 1: Zbuduj fundament bezpieczeństwa

W tej fazie administratorzy umożliwiają funkcjom zabezpieczeń linii bazowej tworzenie bezpieczniejszego i łatwiejszego w użyciu fundamentu w usłudze Azure AD przed zaimportowaniem lub utworzeniem zwykłych kont użytkowników. Ta faza fundamentalna zapewnia, że jesteś w bardziej bezpiecznym stanie od samego początku i że użytkownicy końcowi muszą być wprowadzane do nowych koncepcji tylko raz.

| Zadanie | Szczegół | Wymagana licencja |
| ---- | ------ | ---------------- |
| [Wyznaczanie więcej niż jednego administratora globalnego](../users-groups-roles/directory-emergency-access.md) | Przypisz co najmniej dwa konta stałego administratora globalnego tylko w chmurze do użycia w przypadku sytuacji awaryjnej. Konta te nie są używane codziennie i powinny mieć długie i złożone hasła. | Usługa Azure AD — warstwa Bezpłatna |
| [W miarę możliwości korzystaj z nieglobalnych ról administracyjnych](../users-groups-roles/directory-assign-admin-roles.md) | Zapewnij administratorom dostęp tylko do obszarów, do których potrzebują dostępu. Nie wszyscy administratorzy muszą być administratorami globalnymi. | Usługa Azure AD — warstwa Bezpłatna |
| [Włącz zarządzanie tożsamościami uprzywilejowanymi do śledzenia użycia roli administratora](../privileged-identity-management/pim-getting-started.md) | Włącz zarządzanie tożsamościami uprzywilejowanymi, aby rozpocząć śledzenie użycia roli administracyjnej. | Usługa Azure AD — warstwa Premium P2 |
| [Wdrażanie funkcji samoobsługowego resetowania haseł](../authentication/howto-sspr-deployment.md) | Zmniejsz liczbę wywołań pomocy technicznej w zakresie resetowania haseł, umożliwiając pracownikom resetowanie własnych haseł przy użyciu zasad, które są formantem administratora. | |
| [Tworzenie niestandardowej listy haseł zbanowanych w organizacji](../authentication/howto-password-ban-bad-configure.md) | Uniemożliwić użytkownikom tworzenie haseł, które zawierają typowe słowa lub wyrażenia z organizacji lub obszaru. | |
| [Włączanie integracji lokalnej z ochroną hasłem usługi Azure AD](../authentication/concept-password-ban-bad-on-premises.md) | Rozszerz listę zablokowanych haseł do katalogu lokalnego, aby upewnić się, że hasła ustawione lokalnie są również zgodne z listami haseł zablokowanych haseł specyficznych dla globalnego i dzierżawy. | Usługa Azure AD — warstwa Premium P1 |
| [Włączanie wskazówek dotyczących haseł firmy Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Przestań wymagać od użytkowników zmiany hasła zgodnie z ustalonym harmonogramem, wyłącz wymagania dotyczące złożoności, a użytkownicy są bardziej ptliwi, aby zapamiętać swoje hasła i zachować im coś, co jest bezpieczne. | Usługa Azure AD — warstwa Bezpłatna |
| [Wyłączanie okresowego resetowania haseł dla kont użytkowników w chmurze](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Okresowe resetowanie hasła zachęca użytkowników do zwiększania istniejących haseł. Skorzystaj z wytycznych zawartych w doc wskazówki dotyczące haseł firmy Microsoft i dublowanie zasad lokalnych dla użytkowników tylko w chmurze. | Usługa Azure AD — warstwa Bezpłatna |
| [Dostosowywanie inteligentnej blokady usługi Azure Active Directory](../authentication/howto-password-smart-lockout.md) | Zatrzymywać blokowanie użytkowników w chmurze przed repliką do lokalnych użytkowników usługi Active Directory | |
| [Włącz inteligentną blokadę extranet dla usług AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | Blokada ekstranetu usług AD FS chroni przed atakami zgadywania haseł, pozwalając jednocześnie prawidłowym użytkownikom usług AD FS nadal korzystać ze swoich kont. | |
| [Wdrażanie uwierzytelniania wieloskładnikowego usługi Azure AD przy użyciu zasad dostępu warunkowego](../authentication/howto-mfa-getstarted.md) | Wymagaj od użytkowników przeprowadzania weryfikacji dwuetapowej podczas uzyskiwania dostępu do poufnych aplikacji przy użyciu zasad dostępu warunkowego. | Usługa Azure AD — warstwa Premium P1 |
| [Włączanie ochrony tożsamości usługi Azure Active Directory](../identity-protection/overview-identity-protection.md) | Włącz śledzenie ryzykownych logów i poświadczeń, których bezpieczeństwo zostało naruszone dla użytkowników w organizacji. | Usługa Azure AD — warstwa Premium P2 |
| [Wykrywanie ryzyka umożliwia wyzwalanie uwierzytelniania wieloskładnikowego i zmiany hasła](../authentication/tutorial-risk-based-sspr-mfa.md) | Włącz automatyzację, która może wyzwalać zdarzenia, takie jak uwierzytelnianie wieloskładnikowe, resetowanie hasła i blokowanie logów na podstawie ryzyka. | Usługa Azure AD — warstwa Premium P2 |
| [Włącz rejestrację konwergentną w celu samodzielnego resetowania hasła i uwierzytelniania wieloskładnikowego usługi Azure AD (wersja zapoznawcza)](../authentication/concept-registration-mfa-sspr-converged.md) | Zezwalaj użytkownikom na rejestrowanie się z jednego środowiska wspólnego zarówno uwierzytelniania wieloskładnikowego platformy Azure, jak i resetowania hasła samoobsługowego. | Usługa Azure AD — warstwa Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Faza 2: Importowanie użytkowników, włączanie synchronizacji i zarządzanie urządzeniami

Następnie dodajemy do fundamentu ustanowionego w fazie 1, importując naszych użytkowników i umożliwiając synchronizację, planowanie dostępu gościa i przygotowanie do obsługi dodatkowych funkcji.

| Zadanie | Szczegół | Wymagana licencja |
| ---- | ------ | ---------------- |
| [Instalowanie programu Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md) | Przygotuj się do synchronizacji użytkowników z istniejącego katalogu lokalnego z chmurą. | Usługa Azure AD — warstwa Bezpłatna |
| [Implementowanie synchronizacji skrótów haseł](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | Synchronizuj skróty haseł, aby umożliwić replikację zmian hasła, wykrywanie i korygowanie haseł oraz raportowanie wycieków poświadczeń. | Usługa Azure AD — warstwa Bezpłatna |
| [Implementowanie stornia hasła](../authentication/howto-sspr-writeback.md) | Zezwalaj na zapisywanie zmian haseł w chmurze w lokalnym środowisku usługi Active Directory systemu Windows Server. | Usługa Azure AD — warstwa Premium P1 |
| [Implementowanie kondycji usługi Azure AD Connect](../connect-health/active-directory-aadconnect-health.md) | Włącz monitorowanie kluczowych statystyk kondycji serwerów usługi Azure AD Connect, serwerów usług AD FS i kontrolerów domeny. | Usługa Azure AD — warstwa Premium P1 |
| [Przypisywanie licencji użytkownikom według członkostwa w grupach w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-assign.md) | Zaoszczędź czas i nakład pracy, tworząc grupy licencjonowania, które włączają lub wyłączają funkcje według grup zamiast ustawiania na użytkownika. | |
| [Tworzenie planu dostępu użytkownika-gościa](../b2b/what-is-b2b.md) | Współpracuj z użytkownikami-gośćmi, umożliwiając im logowanie się do aplikacji i usług przy ich własnej tożsamości służbowych, szkolnych lub społecznościowych. | [Wskazówki dotyczące licencjonowania usługi Azure AD B2B](../b2b/licensing-guidance.md) |
| [Decydowanie o strategii zarządzania urządzeniami](../devices/overview.md) | Zdecyduj, na co pozwala Twoja organizacja w odniesieniu do urządzeń. Rejestracja vs dołączanie, Bring Your Own Device vs firma pod warunkiem. | |
| [Wdrażanie funkcji Windows Hello dla firm w organizacji](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Przygotowanie do uwierzytelniania bez hasła przy użyciu funkcji Windows Hello | |
| [Wdrażanie metod uwierzytelniania bez hasła dla użytkowników](../authentication/concept-authentication-passwordless.md) | Zapewnij użytkownikom wygodne metody uwierzytelniania bez hasła | Usługa Azure AD — warstwa Premium P1 |

## <a name="phase-3-manage-applications"></a>Faza 3: Zarządzanie aplikacjami

W miarę jak będziemy kontynuować tworzenie na poprzednich fazach, identyfikujemy aplikacje kandydujące do migracji i integracji z usługą Azure AD i konfigurujemy te aplikacje.

| Zadanie | Szczegół | Wymagana licencja |
| ---- | ------ | ---------------- |
| Identyfikowanie aplikacji | Identyfikowanie aplikacji używanych w organizacji: lokalnych, aplikacji SaaS w chmurze i innych aplikacji biznesowych. Określ, czy te aplikacje mogą i powinny być zarządzane za pomocą usługi Azure AD. | Nie jest wymagana licencja |
| [Integracja obsługiwanych aplikacji SaaS w galerii](../manage-apps/add-application-portal.md) | Usługa Azure AD ma galerię zawierającą tysiące wstępnie zintegrowanych aplikacji. Niektóre aplikacje używane przez organizację są prawdopodobnie w galerii dostępne bezpośrednio z witryny Azure portal. | Usługa Azure AD — warstwa Bezpłatna |
| [Używanie serwera proxy aplikacji do integrowania aplikacji lokalnych](../manage-apps/application-proxy-add-on-premises-application.md) | Serwer proxy aplikacji umożliwia użytkownikom dostęp do aplikacji lokalnych, logując się za pomocą konta usługi Azure AD. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Faza 4: Inspekcja tożsamości uprzywilejowanych, ukończenie przeglądu dostępu i zarządzanie cyklem życia użytkownika

Faza 4 widzi administratorów wymuszanie zasad najmniejszych uprawnień dla administracji, ukończenie ich pierwsze przeglądy dostępu i włączenie automatyzacji typowych zadań cyklu życia użytkownika.

| Zadanie | Szczegół | Wymagana licencja |
| ---- | ------ | ---------------- |
| [Wymuszanie korzystania z uprzywilejowanego zarządzania tożsamościami](../privileged-identity-management/pim-security-wizard.md) | Usuwanie ról administracyjnych z zwykłych kont użytkowników z dnia na dzień. Upewnij się, że użytkownicy administracyjni będą mogli korzystać z ich roli po pomyślnym sprawdzeniu uwierzytelniania wieloskładnikowego, podaniu uzasadnienia biznesowego lub zażądaniu zatwierdzenia od wyznaczonych osób zatwierdzających. | Usługa Azure AD — warstwa Premium P2 |
| [Ukończ przegląd dostępu dla ról katalogu usługi Azure AD w usłudze PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Współpracuj z zespołami zabezpieczeń i kierownictwem, aby utworzyć zasady przeglądu dostępu w celu przeglądania dostępu administracyjnego na podstawie zasad organizacji. | Usługa Azure AD — warstwa Premium P2 |
| [Wdrażanie dynamicznych zasad członkostwa w grupach](../users-groups-roles/groups-dynamic-membership.md) | Grupy dynamiczne umożliwia automatyczne przypisywanie użytkowników do grup na podstawie ich atrybutów z działu kadr (lub źródła prawdy), takich jak dział, tytuł, region i inne atrybuty. |  |
| [Implementowanie inicjowania obsługi administracyjnej aplikacji opartej na grupach](../manage-apps/what-is-access-management.md) | Użyj aprowizacji zarządzania dostępem opartym na grupach, aby automatycznie aprowizować użytkowników dla aplikacji SaaS. |  |
| [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej](../app-provisioning/user-provisioning.md) | Usuń ręczne kroki z cyklu życia konta pracownika, aby zapobiec nieautoryzowanemu dostępowi. Synchronizuj tożsamości ze źródła prawdy (HR System) do usługi Azure AD. |  |

## <a name="next-steps"></a>Następne kroki

[Szczegóły licencjonowania i cen usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)

[Konfiguracje obsługi tożsamości i uzyskiwania dostępu do urządzeń](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Typowe zalecane zasady dotyczące tożsamości i dostępu do urządzeń](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
