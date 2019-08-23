---
title: Plany wdrażania — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: End-to-end wskazówki dotyczące wdrażania wiele funkcji usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a335bcbb45b1aac48a3aaea282ffeffe1696f4c
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900079"
---
# <a name="azure-active-directory-deployment-plans"></a>Plany wdrażania usługi Azure Active Directory
Szukasz kompleksowej wskazówki dotyczącej wdrażania możliwości usług Azure Active Directory (Azure AD)? Plany wdrażania usługi Azure AD przeprowadzą Cię przez wartość biznesową, zagadnienia dotyczące planowania i procedury operacyjne, które są potrzebne do pomyślnego wdrożenia wspólnych możliwości usługi Azure AD.

Z poziomu dowolnej strony planu Użyj funkcji drukowania do pliku PDF w przeglądarce, aby utworzyć aktualną wersję offline dokumentacji.
## <a name="include-the-right-stakeholders"></a>Uwzględnij odpowiednie strony zainteresowane

Po rozpoczęciu planowania wdrożenia nowej możliwości należy uwzględnić najważniejszych uczestników w organizacji. Zalecamy określenie i udokumentowanie osoby lub osób, które spełniają każdą z następujących ról, i współpracują z nimi w celu określenia zaangażowania w projekt.  

Role mogą zawierać następujące elementy: 

|Role |Opis |
|-|-|
|Użytkownik końcowy|Reprezentatywna Grupa użytkowników, dla których zostanie zaimplementowana funkcja. Często przegląda zmiany w programie pilotażowym.
|Menedżer pomocy technicznej IT|Przedstawiciel działu pomocy technicznej IT, który może wprowadzić dane wejściowe dotyczące tej zmiany w perspektywie pomocy technicznej.  
|Architekt tożsamości lub Administrator globalny platformy Azure|Przedstawiciel zespołu zarządzania tożsamościami odpowiedzialny za definiowanie sposobu wyrównywania tej zmiany z podstawową infrastrukturą zarządzania tożsamościami w organizacji.|
|Właściciel firmy aplikacji |Ogólny właściciel firmy, których dotyczy ta aplikacja, która może obejmować zarządzanie dostępem.  Może również dostarczyć dane wejściowe dotyczące środowiska użytkownika i użyteczności tej zmiany z perspektywy użytkownika końcowego.
|Właściciel zabezpieczeń|Przedstawiciel zespołu ds. zabezpieczeń, który może się wylogować, aby plan spełniał wymagania dotyczące bezpieczeństwa organizacji.|
|Menedżer zgodności|Osoba w organizacji odpowiedzialna za zapewnienie zgodności z wymogami firmowymi, branżowymi lub rządowymi.|

**Poziom zaangażowania może obejmować:**

- **R**esponsible do implementacji planu i wyniku projektu 

- Pproval planu i wyniku projektu 

- **C**ondystrybucyjny do planu i wyniku projektu 

- Nformedm planu i wyniku projektu


## <a name="deploy-authentication"></a>Wdróż uwierzytelnianie

| Możliwość | Opis|
| -| -|
| [Multi-Factor Authentication](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-getstarted)| Azure Multi-Factor Authentication (MFA) to rozwiązanie firmy Microsoft służące do przeprowadzania weryfikacji dwuetapowej. Korzystając z zaakceptowanych przez administratora metod uwierzytelniania, usługa Azure MFA pomaga chronić dostęp do danych i aplikacji, a jednocześnie spełnia wymagania dotyczące prostego procesu logowania. |
| [Dostęp warunkowy](https://aka.ms/deploymentplans/ca)| Za pomocą dostępu warunkowego można zaimplementować zautomatyzowane decyzje dotyczące kontroli dostępu, które mogą uzyskiwać dostęp do aplikacji w chmurze na podstawie warunków. |
| [Samoobsługowe resetowanie haseł](https://aka.ms/deploymentplans/sspr)| Funkcja samoobsługowego resetowania hasła pomaga użytkownikom resetować swoje hasła bez interwencji administratora, kiedy i gdzie potrzebują. |

## <a name="deploy-application-management"></a>Wdrażanie zarządzania aplikacjami

| Możliwość | Opis|
| -| - |
| [Logowanie jednokrotne](https://aka.ms/deploymentplans/sso)| Logowanie jednokrotne ułatwia użytkownikom dostęp do aplikacji i zasobów, które są potrzebne do prowadzenia działalności, podczas logowania tylko raz. Po zalogowaniu użytkownicy mogą przechodzić z Microsoft Office do usługi SalesForce do wewnętrznych aplikacji bez konieczności wprowadzania poświadczeń po raz drugi. |
| [Panel dostępu](https://aka.ms/deploymentplans/accesspanel)| Oferuje użytkownikom proste koncentratora do odnajdywania i uzyskują dostęp do wszystkich aplikacji. Umożliwiają im wydajniejsze korzystanie z funkcji samoobsługowych, takich jak żądanie dostępu do aplikacji i grup oraz zarządzanie dostępem do zasobów w imieniu innych użytkowników. |


## <a name="deploy-hybrid-scenarios"></a>Wdrażanie scenariuszy hybrydowych

| Możliwość | Opis|
| -| -|
| [Synchronizowanie skrótów haseł za pomocą usługi ADFS](https://aka.ms/deploymentplans/adfs2phs)| W przypadku synchronizacji skrótów haseł skróty użytkowników są synchronizowane z Active Directory lokalnych do usługi Azure AD, dzięki czemu usługa Azure AD uwierzytelnia użytkowników bez interakcji z lokalnym Active Directory |
| [Uwierzytelnianie przekazywane za pomocą usługi ADFS](https://aka.ms/deploymentplans/adfs2pta)| Uwierzytelnianie przekazywane przez usługę Azure AD ułatwia użytkownikom logowanie się do aplikacji lokalnych i opartych na chmurze przy użyciu tych samych haseł. Ta funkcja zapewnia użytkownikom lepszy komfort pracy — mniej hasła do zapamiętania i zmniejsza koszty działu pomocy technicznej, ponieważ użytkownicy nie mogą zapomnieć, jak się zalogować. Gdy użytkownicy logują się za pomocą usługi Azure AD, ta funkcja weryfikuje ich hasła bezpośrednio w lokalnej usłudze Active Directory. |
| [Serwer proxy aplikacji usługi Azure AD](https://aka.ms/deploymentplans/appproxy)| Obecnie pracownicy chcą pracować wydajnie w dowolnym miejscu i czasie, na dowolnym urządzeniu. Muszą oni uzyskiwać dostęp do aplikacji SaaS w chmurze i aplikacji firmowych w środowisku lokalnym. Serwer proxy aplikacji usługi Azure AD umożliwia ten niezawodny dostęp bez kosztownych i złożonych wirtualnych sieci prywatnych (VPN) lub stref zdemilitaryzowana (stref DMZ). |
| [Bezproblemowe logowanie jednokrotne](https://aka.ms/SeamlessSSODPDownload)| Bezproblemowe logowanie jednokrotne w usłudze Azure Active Directory zapewnia automatyczne logowanie użytkowników, gdy ich urządzenia są połączone z siecią firmową. W przypadku tej funkcji użytkownicy nie muszą wpisywać swoich haseł, aby zalogować się do usługi Azure AD i zwykle nie musieli wprowadzać ich nazwy użytkownika. Ta funkcja zapewnia autoryzowanym użytkownikom łatwy dostęp do aplikacji opartych na chmurze bez konieczności używania dodatkowych składników lokalnych. |

## <a name="deploy-user-provisioning"></a>Wdróż Inicjowanie obsługi użytkowników

| Możliwość | Opis|
| -| -|
| [Aprowizowanie użytkowników](https://aka.ms/UserProvisioningDPDownload)| Usługa Azure AD ułatwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach w chmurze (SaaS), takich jak Dropbox, Salesforce, ServiceNow i nie tylko. |
| [Aprowizacja użytkowników dla ruchu przychodzącego oparte na dzień roboczy](https://aka.ms/WorkdayDeploymentPlan)| Oparte na dzień roboczy dla ruchu przychodzącego aprowizacji użytkowników do usługi Active Directory tworzy ona podstawę do zarządzania tożsamościami bieżące i poprawia jakość procesów biznesowych, które zależą od danych autorytatywne tożsamości. Korzystając z tej funkcji, można bezproblemowo zarządzać cyklem życia tożsamości pracowników i pracowników warunkowych przez skonfigurowanie reguł, które mapują procesy w celu aprowizacji łączników (na przykład nowe zatrudnienie, zakończenie i transfer) do akcji inicjowania obsługi IT (np. Tworzenie, Włączanie, Wyłącza |

## <a name="deploy-governance-and-reporting"></a>Wdrażanie ładu i raportowania

| Możliwość | Opis|
| -| -|
| [Privileged Identity Management](https://aka.ms/deploymentplans/pim)| Azure AD Privileged Identity Management (PIM) ułatwia zarządzanie uprzywilejowanymi rolami administracyjnymi w usłudze Azure AD, zasobach platformy Azure i innych usługach online firmy Microsoft. PIM oferuje rozwiązania, takie jak dostęp just in Time, żądania przepływów pracy zatwierdzania i w pełni zintegrowane przeglądy dostępu, dzięki czemu można identyfikować, odkrywać i zapobiegać złośliwym działaniom uprzywilejowanych ról w czasie rzeczywistym. |
| [Raportowanie i monitorowanie](https://aka.ms/deploymentplans/reporting)| Projekt rozwiązania do raportowania i monitorowania usługi Azure AD zależy od wymagań prawnych, bezpieczeństwa i działania, a także istniejącego środowiska i procesów. W tym artykule przedstawiono różne opcje projektowania i przeprowadzimy Cię do odpowiedniej strategii wdrażania. |
