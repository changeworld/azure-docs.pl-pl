---
title: Migrowanie zasad dostępu warunkowego — usługa Azure Active Directory
description: Dowiedz się, co musisz wiedzieć, aby przeprowadzić migrację klasycznych zasad w witrynie Azure portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33b1f76dd1489e00115d0f805add8d754038df84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185907"
---
# <a name="conditional-access-classic-policy-migration"></a>Migracja zasad klasycznego dostępu warunkowego

Dostęp warunkowy to narzędzie używane przez usługę Azure Active Directory do łączenia sygnałów, podejmowania decyzji i wymuszania zasad organizacyjnych. Dostęp warunkowy znajduje się w centrum nowej płaszczyzny sterowania sterowanej tożsamością. Chociaż cel jest nadal taki sam, wydanie nowego portalu Azure wprowadziła znaczące ulepszenia sposobu działania dostępu warunkowego.

Rozważ migrację zasad, które nie zostały utworzone w witrynie Azure portal, ponieważ:

- Teraz można rozwiązać scenariusze, których wcześniej nie można było obsłużyć.
- Można zmniejszyć liczbę zasad, którymi należy zarządzać, konsolidując je.
- Wszystkie zasady dostępu warunkowego można zarządzać w jednej centralnej lokalizacji.
- Klasyczny portal platformy Azure zostanie wycofany.

W tym artykule wyjaśniono, co należy wiedzieć, aby przeprowadzić migrację istniejących zasad dostępu warunkowego do nowej struktury.

## <a name="classic-policies"></a>Zasady klasyczne

W [witrynie Azure portal](https://portal.azure.com)zasady dostępu warunkowego można znaleźć w obszarze**Dostęp warunkowy****zabezpieczeń** >  **usługi Azure Active Directory** > . Organizacja może mieć również starsze zasady dostępu warunkowego, które nie zostały utworzone przy użyciu tej strony. Zasady te są znane jako *klasyczne zasady*. Zasady klasyczne to zasady dostępu warunkowego utworzone w:

- Klasyczny portal platformy Azure
- Klasyczny portal usługi Intune
- Portal ochrony aplikacji usługi Intune

Na stronie **Dostęp warunkowy** można uzyskać dostęp do klasycznych zasad, klikając [**klasyczne zasady**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) w sekcji **Zarządzanie.** 

![Dostęp warunkowy w usłudze Azure AD z widokiem zasad klasycznych](./media/policy-migration/71.png)

Widok **zasad klasycznych** umożliwia:

- Filtruj klasyczne zasady.
- Wyłącz klasyczne zasady.
- Przejrzyj ustawienia klasycznej zasady i wyłącz ją.

   ![Klasyczne szczegóły zasad, w tym istniejąca konfiguracja zasad](./media/policy-migration/74.png)

> [!WARNING]
> Po wyłączeniu klasycznej zasady nie można ponownie włączyć.

Widok szczegółów zasad klasycznych umożliwia dokumentowanie ustawień, modyfikowanie uwzględnionych lub wykluczonych grup oraz wyłączanie zasad.

![Szczegóły zasad — grupy do uwzględnienia lub wykluczenia](./media/policy-migration/75.png)

Zmieniając wybrane grupy lub wykluczając określone grupy, można przetestować efekt wyłączonej zasady klasycznej dla kilku użytkowników testowych przed wyłączeniem zasad dla wszystkich uwzględnionych użytkowników i grup.
 
## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

W tym artykule zasady dostępu warunkowego usługi Azure AD są również określane jako *nowe zasady*.
Klasyczne zasady będą nadal działać obok nowych zasad, dopóki ich nie wyłączysz lub nie usuniesz. 

Następujące aspekty są ważne w kontekście konsolidacji polityki:

- Podczas gdy klasyczne zasady są powiązane z określoną aplikacją w chmurze, możesz wybrać dowolną liczbę aplikacji w chmurze w nowych zasadach.
- Sterowanie zasadami klasycznymi i nowymi zasadami dla aplikacji w chmurze wymagają spełnienia wszystkich formantów *(AND).* 
- W nowych zasadach można:
   - Połącz wiele warunków, jeśli jest to wymagane przez scenariusz. 
   - Wybierz kilka wymagań dotacji jako kontroli dostępu i połączyć je z logicznym *OR* (wymagają jednego z wybranych formantów) lub z logicznym *I* (wymagają wszystkich wybranych formantów).

### <a name="office-365-exchange-online"></a>Usługa Office 365 Exchange w trybie online

Jeśli chcesz przeprowadzić migrację klasycznych zasad dla **usługi Office 365 Exchange online,** które zawierają **usłudze Exchange Active Sync** jako warunek aplikacji klienckich, konsolidacja ich w jedną nową zasadę może być nied emdysumowana. 

Jest to, na przykład, przypadek, jeśli chcesz obsługiwać wszystkie typy aplikacji klienckich. W nowej zasadie, która ma **warunek Exchange Active Sync** jako aplikacje klienckie, nie można wybrać innych aplikacji klienckich.

![Dostęp warunkowy wybierając aplikacje klienckie](./media/policy-migration/64.png)

Konsolidacja w jedną nową zasadę nie jest również możliwa, jeśli klasyczne zasady zawierają kilka warunków. Nowa zasada, która ma **skonfigurowany** warunek Exchange Active Sync jako aplikacje klienckie, nie obsługuje innych warunków:   

![Program Exchange ActiveSync nie obsługuje wybranych warunków](./media/policy-migration/08.png)

Jeśli masz nową zasadę, która ma **skonfigurowany** warunek Exchange Active Sync jako aplikacje klienckie, musisz się upewnić, że wszystkie inne warunki nie są skonfigurowane. 

![Warunki dostępu warunkowego](./media/policy-migration/16.png)
 
Klasyczne zasady oparte na aplikacjach dla usługi Office 365 Exchange Online, które zawierają **aktywną synchronizację programu Exchange** jako warunek aplikacji klienckich, umożliwiają **obsługiwane** i **nieobsługiwały** platformy urządzeń. Chociaż nie można skonfigurować poszczególnych platform urządzeń w powiązanych nowych zasad, można ograniczyć obsługę tylko do [obsługiwanych platform urządzeń.](concept-conditional-access-conditions.md#device-platforms) 

![Dostęp warunkowy wybierz program Exchange ActiveSync](./media/policy-migration/65.png)

Można skonsolidować wiele klasycznych zasad, które zawierają **Exchange Active Sync** jako warunek aplikacji klienckich, jeśli mają:

- Tylko **usługa Exchange Active Sync** jako warunek 
- Kilka wymagań dotyczących przyznawania dostępu skonfigurowanych

Jednym z typowych scenariuszy jest konsolidacja:

- Klasyczna zasada oparta na urządzeniach z klasycznego portalu platformy Azure 
- Klasyczne zasady oparte na aplikacji w portalu ochrony aplikacji usługi Intune 
 
W takim przypadku można skonsolidować klasyczne zasady w jedną nową zasadę, która ma wybrane oba wymagania.

![Formanty dotacji dostępu warunkowego](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Platformy urządzeń

Klasyczne zasady z formantami opartymi na aplikacjach są wstępnie skonfigurowane w systemach iOS i Android jako warunek platformy urządzenia. 

W nowych zasadach należy wybrać [platformy urządzeń,](concept-conditional-access-conditions.md#device-platforms) które mają być obsługiwane indywidualnie.

![Wybór platform urządzeń dostępu warunkowego](./media/policy-migration/41.png)

## <a name="next-steps"></a>Następne kroki

- [Użyj trybu tylko do raportu dla dostępu warunkowego, aby określić wpływ nowych decyzji dotyczących zasad.](concept-conditional-access-report-only.md)
- Aby dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zobacz [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md).
- Jeśli chcesz skonfigurować zasady dostępu warunkowego dla swojego środowiska, zobacz artykuł [Jak: Planowanie wdrożenia dostępu warunkowego w usłudze Azure Active Directory](plan-conditional-access.md). 
