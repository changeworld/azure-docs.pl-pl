---
title: Przewodnik z operacjami nadzoru usługi Azure Active Directory
description: W tym przewodniku dotyczącym operacji opisano kontrole i działania, które należy podjąć w celu zabezpieczenia zarządzania
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 4826bcdc85e0c6189c51aa262014fe154bb479b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535459"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Przewodnik z operacjami nadzoru usługi Azure Active Directory

W tej sekcji [przewodnika odnośnych operacji usługi Azure AD](active-directory-ops-guide-intro.md) opisano kontrole i akcje, które należy podjąć w celu oceny i testowania dostępu przyznanego tożsamości nieuprzywilejowanych i uprzywilejowanych, inspekcji i kontroli zmian w środowisku.

> [!NOTE]
> Zalecenia te są aktualne na dzień publikacji, ale mogą się zmieniać w czasie. Organizacje powinny stale oceniać swoje praktyki w zakresie zarządzania w miarę rozwoju produktów i usług firmy Microsoft w miarę rozwoju w czasie.

## <a name="key-operational-processes"></a>Kluczowe procesy operacyjne

### <a name="assign-owners-to-key-tasks"></a>Przypisywanie właścicieli do kluczowych zadań

Zarządzanie usługą Azure Active Directory wymaga ciągłego wykonywania kluczowych zadań operacyjnych i procesów, które mogą nie być częścią projektu wdrożenia. Nadal ważne jest, aby skonfigurować te zadania w celu optymalizacji środowiska. Do kluczowych zadań i ich zalecanych właścicieli należą:

| Zadanie | Właściciel |
| :- | :- |
| Archiwizuj dzienniki inspekcji usługi Azure AD w systemie SIEM | Zespół operacyjny InfoSec |
| Odnajdowanie aplikacji zarządzanych niezgodnie z przepisami | Zespół operacyjny IAM |
| Regularne przeglądy dostępu do aplikacji | Zespół architektury InfoSec |
| Regularne przeglądy dostępu do tożsamości zewnętrznych | Zespół architektury InfoSec |
| Regularne przeglądanie, kto ma uprzywilejowane role | Zespół architektury InfoSec |
| Definiowanie bram zabezpieczeń w celu aktywowania ról uprzywilejowanych | Zespół architektury InfoSec |
| Regularne przeglądy dotacji na zgodę | Zespół architektury InfoSec |
| Katalogi projektów i pakiety dostępu dla aplikacji i zasobów dla pracowników w organizacji | Właściciele aplikacji |
| Definiowanie zasad zabezpieczeń w celu przypisywania użytkowników do pakietów dostępu | Zespół InfoSec + Właściciele aplikacji |
| Jeśli zasady obejmują przepływy pracy zatwierdzania, regularnie sprawdzaj zatwierdzenia przepływu pracy | Właściciele aplikacji |
| Przeglądanie wyjątków w zasadach zabezpieczeń, takich jak zasady dostępu warunkowego, przy użyciu przeglądów dostępu | Zespół operacyjny InfoSec |

Podczas przeglądania listy może okazać się konieczne przypisanie właściciela do zadań, w których brakuje właściciela, lub dostosowanie własności do zadań z właścicielami, które nie są zgodne z powyższymi zaleceniami.

#### <a name="owner-recommended-reading"></a>Właściciel zaleca czytanie

- [Przypisywanie ról administratorów w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Nadzór na platformie Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>Testowanie zmian konfiguracji

Istnieją zmiany, które wymagają specjalnych zagadnień podczas testowania, od prostych technik, takich jak wdrażanie podzbiór docelowy użytkowników do wdrażania zmiany w dzierżawy testu równoległego. Jeśli strategia testowania nie została zaimplementowana, należy zdefiniować podejście testowe na podstawie wytycznych w poniższej tabeli:

| Scenariusz| Zalecenie |
|-|-|
|Zmiana typu uwierzytelniania z federacyjnego na PHS/PTA lub odwrotnie| Użyj [wdrożenia etapowego,](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) aby przetestować wpływ zmiany typu uwierzytelniania.|
|Wdrażanie nowej zasady dostępu warunkowego (CA) lub zasad ochrony tożsamości|Utwórz nową zasadę urzędu certyfikacji i przypisz do użytkowników testowych.|
|Dołączanie środowiska testowego aplikacji|Dodaj aplikację do środowiska produkcyjnego, ukryj ją w panelu MyApps i przypisz ją do użytkowników testowych podczas fazy zapewniania jakości .Add the application to a production environment, hide it from the MyApps panel, and assign it to test users during the quality assurance (QA) phase.|
|Zmiana reguł synchronizacji|Wykonaj zmiany w testie usługi Azure AD Connect z tą samą konfiguracją, która jest obecnie w wersji produkcyjnej, znany również jako tryb przemieszczania i analizuj wyniki CSExport. Jeśli jest to spełnione, zamienić na produkcję, gdy jest gotowy.|
|Zmiana marki|Testuj w oddzielnej dzierżawie testowej.|
|Wdrażanie nowej funkcji|Jeśli funkcja obsługuje wdrożenie do docelowego zestawu użytkowników, zidentyfikuj użytkowników pilotażowych i skompiluj. Na przykład samoobsługowe resetowanie hasła i uwierzytelnianie wieloskładnikowe mogą być kierowane na określonych użytkowników lub grupy.|
|Prześcij aplikację od lokalnego dostawcy tożsamości (IdP), na przykład usługi Active Directory, do usługi Azure AD|Jeśli aplikacja obsługuje wiele konfiguracji IdP, na przykład Salesforce, skonfiguruj zarówno i przetestować usługę Azure AD podczas okna zmiany (w przypadku, gdy aplikacja wprowadza stronę HRD). Jeśli aplikacja nie obsługuje wielu identyfikatorów, należy zaplanować testowanie podczas okna kontroli zmian i przestojów programu.|
|Aktualizowanie reguł grupy dynamicznej|Utwórz równoległą grupę dynamiczną z nową regułą. Porównaj z obliczonym wynikiem, na przykład uruchom program PowerShell z tym samym warunkiem.<br>Jeśli test zda egzamin, zamienić miejsca, w których użyto starej grupy (jeśli jest to możliwe).|
|Migracja licencji produktów|Zobacz [zmienianie licencji dla pojedynczego użytkownika w licencjonowanej grupie w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses).|
|Zmienianie reguł usług AD FS, takich jak Autoryzacja, Wydawanie, UWIERZYTELNIANIE WIELOSKŁADNIKIE|Użyj oświadczenia grupy, aby kierować reklamy na podzbiór użytkowników.|
|Zmienianie środowiska uwierzytelniania usług AD FS lub podobnych zmian w całej farmie|Utwórz farmę równoległą o tej samej nazwie hosta, zaimplementuj zmiany konfiguracji, przetestuj z klientów przy użyciu pliku HOSTS, reguł routingu równoważenia obciążenia sieciowego lub podobnego routingu.<br>Jeśli platforma docelowa nie obsługuje plików HOSTS (na przykład urządzeń przenośnych), należy sterować zmianą.|

## <a name="access-reviews"></a>Przeglądy dostępu

### <a name="access-reviews-to-applications"></a>Dostęp do recenzji aplikacji

Z biegiem czasu użytkownicy mogą gromadzić dostęp do zasobów, gdy przemieszczają się po różnych zespołach i pozycjach. Ważne jest, aby właściciele zasobów regularnie przeglądali dostęp do aplikacji i usuwali uprawnienia, które nie są już potrzebne w całym cyklu życia użytkowników. [Przeglądy dostępu usługi](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) Azure AD umożliwiają organizacjom efektywne zarządzanie członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Właściciele zasobów powinni regularnie sprawdzać dostęp użytkowników, aby upewnić się, że tylko właściwi użytkownicy mają stały dostęp. W idealnym przypadku należy rozważyć użycie przeglądów dostępu usługi Azure AD dla tego zadania.

![Strona początkowa przeglądów programu Access](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Każdy użytkownik, który wchodzi w interakcję z przeglądami dostępu, musi mieć płatną licencję usługi Azure AD Premium P2.

### <a name="access-reviews-to-external-identities"></a>Dostęp do przeglądów tożsamości zewnętrznych

Ważne jest, aby zachować dostęp do tożsamości zewnętrznych ograniczone tylko do zasobów, które są potrzebne, w czasie, który jest potrzebny. Ustanowienie regularnego procesu automatycznego przeglądu dostępu dla wszystkich tożsamości zewnętrznych i dostępu do aplikacji przy użyciu [przeglądów dostępu usługi](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)Azure AD . Jeśli proces już istnieje lokalnie, należy rozważyć użycie przeglądów dostępu usługi Azure AD. Gdy aplikacja jest wycofana lub nie jest już używana, usuń wszystkie tożsamości zewnętrzne, które miały dostęp do aplikacji.

> [!NOTE]
> Każdy użytkownik, który wchodzi w interakcję z przeglądami dostępu, musi mieć płatną licencję usługi Azure AD Premium P2.

## <a name="privileged-account-management"></a>Zarządzanie kontami uprzywilejowanymi

### <a name="privileged-account-usage"></a>Użycie konta uprzywilejowanego

Hakerzy często atakują konta administratorów i inne elementy uprzywilejowanego dostępu, aby szybko uzyskać dostęp do poufnych danych i systemów.Ponieważ użytkownicy z uprzywilejowanymi rolami mają tendencję do akumulacji w czasie, ważne jest, aby regularnie przeglądać i zarządzać dostępem administratora i zapewnić uprzywilejowany dostęp do zasobów usługi Azure AD i platformy Azure.

Jeśli w organizacji nie istnieje żaden proces zarządzania kontami uprzywilejowanymi lub obecnie administratorzy, którzy używają swoich zwykłych kont użytkowników do zarządzania usługami i zasobami, należy natychmiast rozpocząć korzystanie z oddzielnych kont, na przykład jednego dla zwykłych codziennych kont. działalności; drugi dla uprzywilejowanego dostępu i skonfigurowany z uwierzytelnianiem wieloskładnikowego. Jeszcze lepiej, jeśli twoja organizacja ma subskrypcję usługi Azure AD Premium P2, należy natychmiast wdrożyć [usługę Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements) (PIM). W tym samym tokenie należy również przejrzeć te konta uprzywilejowane i [przypisać mniej uprzywilejowane role,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) jeśli ma to zastosowanie.

Innym aspektem uprzywilejowanego zarządzania kontem, który powinien zostać wdrożony, jest definiowanie [przeglądów dostępu](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) dla tych kont, ręcznie lub [zautomatyzowanych za pośrednictwem usługi PIM.](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review)

#### <a name="privileged-account-management-recommended-reading"></a>Zaleca się czytanie uprzywilejowanego zarządzania kontem

- [Role w zarządzania tożsamościami uprzywilejowanymi usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>Konta dostępu awaryjnego

Organizacje muszą utworzyć [konta awaryjne,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) aby przygotować się do zarządzania usługą Azure AD w przypadkach, takich jak awarie uwierzytelniania, takie jak:

- Składniki awarii infrastruktury uwierzytelniania (AD FS, lokalna usługa AD, usługa MFA)
- Rotacja personelu administracyjnego

Aby zapobiec przypadkowemu zablokowaniu dzierżawy, ponieważ nie można zalogować się ani aktywować istniejącego konta indywidualnego użytkownika jako administratora, należy utworzyć dwa lub więcej kont awaryjnych i upewnić się, że są one implementowane i zgodne z [najlepszymi praktykami firmy Microsoft](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) i [procedurami łamania szkła](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency).

### <a name="privileged-access-to-azure-ea-portal"></a>Uprzywilejowany dostęp do portalu EA platformy Azure

Portal [Azure Enterprise Agreement (Azure EA)](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) umożliwia tworzenie subskrypcji platformy Azure na przykładu umowy Enterprise Agreement, która jest potężną rolą w przedsiębiorstwie. Często uruchamia się ten portal, zanim jeszcze zostanie wprowadzony usługa Azure AD, konieczne jest użycie tożsamości usługi Azure AD do zablokowania jej, usunięcia kont osobistych z portalu, upewnienia się, że odpowiednie delegowanie jest na miejscu i zmniejszenia ryzyka blokady .

Aby było jasne, jeśli poziom autoryzacji portalu EA jest obecnie ustawiony na "tryb mieszany", należy usunąć wszystkie konta Microsoft ze wszystkich [uprzywilejowanych dostępów](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) w portalu EA i skonfigurować portal EA do używania tylko kont usługi Azure AD. Jeśli delegowane role portalu EA nie są skonfigurowane, należy również znaleźć i zaimplementować delegowane role dla działów i kont.

#### <a name="privileged-access-recommended-reading"></a>Zalecany odczyt dostępu uprzywilejowanego

- [Uprawnienia ról administratorów w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>Zarządzanie upoważnieniami

[Zarządzanie uprawnieniami (EM)](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) umożliwia właścicielom aplikacji łączenie zasobów i przypisywanie ich do określonych person w organizacji (zarówno wewnętrznych, jak i zewnętrznych). Em umożliwia samoobsługowe rejestracje i delegowanie do właścicieli firm przy zachowaniu zasad zarządzania w celu udzielenia dostępu, ustawiania czasu trwania dostępu i zezwalania na zatwierdzanie przepływów pracy. 

> [!NOTE]
> Usługa Azure AD Entitlement Management wymaga licencji usługi Azure AD Premium P2.

## <a name="summary"></a>Podsumowanie

Istnieje osiem aspektów bezpiecznego zarządzania tożsamością. Ta lista pomoże Ci zidentyfikować akcje, które należy podjąć w celu oceny i testowania dostępu przyznanego tożsamościom nieuprzywilejowanym i uprzywilejowanym, inspekcji i kontroli zmian w środowisku.

- Przypisz właścicieli do kluczowych zadań.
- Wdrożenie strategii testowania.
- Korzystaj z przeglądów usługi Azure AD Access, aby skutecznie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról.
- Ustanowienie regularnego, zautomatyzowanego procesu przeglądu dostępu dla wszystkich typów tożsamości zewnętrznych i dostępu do aplikacji.
- Ustanowienie procesu przeglądu dostępu do regularnego przeglądania i zarządzania dostępem administratora i zapewnienia uprzywilejowanego dostępu do zasobów usługi Azure AD i platformy Azure tylko w czasie.
- Aprowizuj konta awaryjne, które mają być przygotowane do zarządzania usługą Azure AD w przypadku nieoczekiwanych awarii.
- Zablokuj dostęp do portalu Usługi Azure EA.
- Zaimplementuj zarządzanie uprawnieniami, aby zapewnić zarządzany dostęp do kolekcji zasobów.

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do [kontroli i akcji operacyjnych usługi Azure AD](active-directory-ops-guide-ops.md).
