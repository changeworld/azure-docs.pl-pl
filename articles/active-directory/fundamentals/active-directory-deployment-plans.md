---
title: Plany wdrażania — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: End-to-end wskazówki dotyczące wdrażania wiele funkcji usługi Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f695d9f0240f8c27ea0bedba7e532d37a177752
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304742"
---
# <a name="azure-active-directory-deployment-plans"></a>Plany wdrażania usługi Azure Active Directory
Szukasz kompleksowych wskazówek dotyczących wdrażania niektórych funkcji usługi Azure Active Directory (Azure AD)? W następujących planach wdrażania przedstawiono wartość biznesową, zagadnienia dotyczące planowania, projektowanie i procedury operacyjne potrzebne do pomyślnego wdrożenia niektórych z najpopularniejszych funkcji usługi Azure AD. 

W tych dokumentach znajdziesz szablony wiadomości e-mail, diagramy architektury systemu, typowe przypadki testowe i nie tylko. 

Chętnie poznamy Twoją opinię o tych dokumentach. Weź udział w tej krótkiej [ankiecie](https://aka.ms/deploymentplanfeedback) dotyczącej przydatności tych dokumentów. 

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

- **Pproval planu**i wyniku projektu 

- **C**ondystrybucyjny do planu i wyniku projektu 

- **Nformedm**planu i wyniku projektu
 
## <a name="deployment-plans"></a>Plany wdrożenia



|Scenariusz |Opis |
|-|-|
|[Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)|Azure Multi-Factor Authentication (MFA) to rozwiązanie firmy Microsoft służące do przeprowadzania weryfikacji dwuetapowej. Przy użyciu zatwierdzonych przez administratora metod uwierzytelniania usługa Azure MFA pomaga w zabezpieczaniu dostępu do danych i aplikacji, jednocześnie spełniając wymagania dotyczące prostoty procesu logowania.|
|[Dostęp warunkowy](https://aka.ms/deploymentplans/ca)|Za pomocą dostępu warunkowego można zaimplementować zautomatyzowane decyzje dotyczące kontroli dostępu, które mogą uzyskiwać dostęp do aplikacji w chmurze na podstawie warunków.|
|[Samoobsługowe resetowanie haseł](https://aka.ms/deploymentplans/sspr)|Samoobsługowe resetowanie haseł ułatwia użytkownikom resetowanie ich haseł bez udziału administratora w dowolnym miejscu i czasie.|
|[Privileged Identity Management](https://aka.ms/deploymentplans/pim)|Azure AD Privileged Identity Management (PIM) ułatwia zarządzanie uprzywilejowanymi rolami administracyjnymi w usłudze Azure AD, zasobach platformy Azure i innych usługach online firmy Microsoft. PIM oferuje rozwiązania, takie jak dostęp just in Time, żądania przepływów pracy zatwierdzania i w pełni zintegrowane przeglądy dostępu, dzięki czemu można identyfikować, odkrywać i zapobiegać złośliwym działaniom uprzywilejowanych ról w czasie rzeczywistym.|
|[Logowanie jednokrotne](https://aka.ms/deploymentplans/sso)|Logowanie jednokrotne ułatwia dostęp do wszystkich aplikacji i zasobów potrzebnych do prowadzenia działalności, przy czym można logować się tylko raz, za pomocą jednego konta użytkownika. Po zalogowaniu się można przechodzić z pakietu Microsoft Office do usługi SalesForce lub Box bez konieczności ponownego uwierzytelniania się (na przykład przez wpisanie hasła).|
|[Bezproblemowe logowanie jednokrotne](https://aka.ms/SeamlessSSODPDownload)|Bezproblemowe logowanie jednokrotne w usłudze Azure Active Directory zapewnia automatyczne logowanie użytkowników, gdy ich urządzenia są połączone z siecią firmową. Po włączeniu tej funkcji użytkownicy nie muszą wpisywać haseł, aby logować się do usługi Azure AD, i zazwyczaj nie muszą nawet wpisywać swoich nazw użytkownika. Ta funkcja zapewnia użytkownikom łatwy dostęp do aplikacji w chmurze bez konieczności używania dodatkowych składników lokalnych.|
|[Panel dostępu](https://aka.ms/AccessPanelDPDownload)|Oferuje użytkownikom proste koncentratora do odnajdywania i uzyskują dostęp do wszystkich aplikacji. Umożliwia im mu bardziej wydajnej pracy przy użyciu usługi możliwości samoobsługi, takie jak możliwość żądania dostępu do nowych aplikacji i grup, lub zarządzanie dostępem do tych zasobów w imieniu innych użytkowników.|
|[Synchronizowanie skrótów haseł za pomocą usługi ADFS](https://aka.ms/deploymentplans/adfs2phs)|Funkcja synchronizacji skrótów zapewnia, że skróty haseł użytkowników są synchronizowane z lokalnej usługi Active Directory do usługi Azure AD, dzięki czemu usługa Azure AD może uwierzytelniać użytkowników bez interakcji z lokalną usługą Active Directory.|
|[Uwierzytelnianie przekazywane za pomocą usługi ADFS](https://aka.ms/deploymentplans/adfs2pta)|Dzięki uwierzytelnianiu przekazywanemu w usłudze Azure AD użytkownicy mogą logować się zarówno do aplikacji lokalnych, jak i w chmurze, używając tego samego hasła. Ta funkcja zapewnia lepszą obsługę użytkowników (jedno hasło mniej do zapamiętania) i obniża koszty pomocy technicznej IT, zmniejszając prawdopodobieństwo tego, że użytkownik zapomni sposób logowania się. Gdy użytkownicy logują się za pomocą usługi Azure AD, ta funkcja weryfikuje ich hasła bezpośrednio w lokalnej usłudze Active Directory.|
|[Serwer proxy aplikacji usługi Azure AD](https://aka.ms/deploymentplans/appproxy)|Obecnie pracownicy chcą pracować wydajnie w dowolnym miejscu i czasie, na dowolnym urządzeniu. Chcą pracować na własnych urządzeniach — tabletach, telefonach i laptopach. Pracownicy chcą też mieć dostęp do wszystkich swoich aplikacji — zarówno aplikacji SaaS w chmurze, jak i lokalnych aplikacji firmowych. Zapewnienie dostępu do aplikacji lokalnych wymagało dotychczas wirtualnych sieci prywatnych (VPN) lub stref zdemilitaryzowanych (DMZ). Te rozwiązania nie tylko są skomplikowane i trudne do zabezpieczenia, ale też mają wysokie koszty konfigurowania i zarządzania. Jest lepszy sposób — serwer proxy aplikacji usługi Azure AD.|
|[Aprowizowanie użytkowników](https://aka.ms/UserProvisioningDPDownload)|Usługa Azure AD ułatwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach w chmurze (SaaS), takich jak Dropbox, Salesforce, ServiceNow i nie tylko.|
|[Aprowizacja użytkowników dla ruchu przychodzącego oparte na dzień roboczy](https://aka.ms/WorkdayDeploymentPlan)|Oparte na dzień roboczy dla ruchu przychodzącego aprowizacji użytkowników do usługi Active Directory tworzy ona podstawę do zarządzania tożsamościami bieżące i poprawia jakość procesów biznesowych, które zależą od danych autorytatywne tożsamości. Przy użyciu tej funkcji, można bezproblemowo zarządzać cyklem życia tożsamości pracowników i pracowników warunkowych, konfigurując reguły mapowania akcji aprowizacji IT (np. Utwórz i Włącz, łącznik — modułu przenoszącego spójne kolekcje — wprowadzane procesów (np. Transfer nowego zatrudnienia, zakończenia) Wyłącz, usuwanie kont).|
