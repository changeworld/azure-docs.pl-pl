---
title: Migrowanie zasad dostępu warunkowego — Azure Active Directory
description: Dowiedz się, co musisz wiedzieć, aby przeprowadzić migrację klasycznych zasad w Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75d664f6e61dbbaaf0b8ab74c392596a206ff644
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380554"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>Co to jest migracja zasad w Azure Active Directory dostęp warunkowy? 

[Dostęp warunkowy](../active-directory-conditional-access-azure-portal.md) jest funkcją usługi Azure Active Directory (Azure AD), która umożliwia kontrolowanie sposobu, w jaki autoryzowani użytkownicy uzyskują dostęp do aplikacji w chmurze. Mimo że przeznaczenie jest takie samo, wydanie nowej Azure Portal wprowadziło znaczące ulepszenia dotyczące sposobu działania dostępu warunkowego.

Rozważ Migrowanie zasad, które nie zostały utworzone w Azure Portal, ponieważ:

- Teraz możesz rozwiązywać scenariusze, których nie można obsłużyć wcześniej.
- Aby zmniejszyć liczbę zasad, którymi trzeba zarządzać, można je skonsolidować.   
- Można zarządzać wszystkimi zasadami dostępu warunkowego w jednej centralnej lokalizacji.
- Klasyczny portal Azure zostanie wycofany.   

W tym artykule wyjaśniono, co należy wiedzieć, aby przeprowadzić migrację istniejących zasad dostępu warunkowego do nowej struktury.
 
## <a name="classic-policies"></a>Zasady klasyczne

Na [Azure Portal](https://portal.azure.com)strona [zasady dostępu warunkowego](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) jest punktem wejścia do zasad dostępu warunkowego. Jednak w danym środowisku można również mieć zasady dostępu warunkowego, które nie zostały utworzone przy użyciu tej strony. Te zasady są znane jako *zasady klasyczne*. Klasyczne zasady są zasadami dostępu warunkowego, które zostały utworzone w programie:

- Klasyczny portal Azure
- Klasyczny portal usługi Intune
- Portal Intune App Protection

Na stronie **dostęp warunkowy** możesz uzyskać dostęp do klasycznych zasad, klikając pozycję [**zasady klasyczne (wersja zapoznawcza)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) w sekcji **Zarządzanie** . 

![Usługa Azure Active Directory](./media/policy-migration/71.png)

Widok **zasady klasyczne** zapewnia następujące możliwości:

- Filtrowanie klasycznych zasad.
 
   ![Usługa Azure Active Directory](./media/policy-migration/72.png)

- Wyłącz zasady klasyczne.

   ![Usługa Azure Active Directory](./media/policy-migration/73.png)
   
- Przejrzyj ustawienia klasycznych zasad (i wyłącz je).

   ![Usługa Azure Active Directory](./media/policy-migration/74.png)

Jeśli zasady klasyczne zostały wyłączone, nie można już cofnąć tego kroku. Z tego względu można zmodyfikować członkostwo w grupach w zasadach klasycznych, korzystając z widoku **szczegółów** . 

![Usługa Azure Active Directory](./media/policy-migration/75.png)

Zmieniając wybrane grupy lub wyłączając określone grupy, można testować efekt wyłączonych zasad klasycznych dla kilku użytkowników testowych przed wyłączeniem zasad dla wszystkich uwzględnionych użytkowników i grup. 

## <a name="azure-ad-conditional-access-policies"></a>Zasady dostępu warunkowego usługi Azure AD

Za pomocą dostępu warunkowego w Azure Portal można zarządzać wszystkimi zasadami w jednej centralnej lokalizacji. Ponieważ implementacja dostępu warunkowego została zmieniona, należy zapoznać się z podstawowymi pojęciami przed migracją zasad klasycznych.

Zobacz:

- [Co to jest dostęp warunkowy w Azure Active Directory](../active-directory-conditional-access-azure-portal.md) , aby poznać podstawowe koncepcje i terminologię.
- [Najlepsze rozwiązania dotyczące dostępu warunkowego w Azure Active Directory](best-practices.md) , aby uzyskać wskazówki dotyczące wdrażania dostępu warunkowego w organizacji.
- [Wymagaj uwierzytelniania wieloskładnikowego dla konkretnych aplikacji z Azure Active Directory dostęp warunkowy](app-based-mfa.md) , aby zaznajomić się z interfejsem użytkownika w Azure Portal.
 
## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

W tym artykule zasady dostępu warunkowego usługi Azure AD są również określane jako *nowe zasady*.
Twoje klasyczne zasady nadal pracują obok nowych zasad, dopóki nie zostaną wyłączone lub usunięte. 

Następujące aspekty są istotne w kontekście konsolidacji zasad:

- Chociaż zasady klasyczne są powiązane z określoną aplikacją w chmurze, możesz wybrać dowolną liczbę aplikacji w chmurze, które będą potrzebne w ramach nowych zasad.
- Kontrolki klasycznych zasad i nowe zasady dla aplikacji w chmurze wymagają, aby wszystkie kontrolki (*i*) zostały spełnione. 
- W nowych zasadach można:
   - W razie potrzeby należy połączyć wiele warunków w danym scenariuszu. 
   - Wybierz kilka wymagań dotyczących dotacji jako kontroli dostępu i połącz je z logicznym *lub* (wymaga jednego z wybranych kontrolek) lub z logicznym *i* (Wymagaj wszystkich zaznaczonych kontrolek).

   ![Usługa Azure Active Directory](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Pakiet Office 365 Exchange Online

Jeśli chcesz przeprowadzić migrację klasycznych zasad dla **pakietu Office 365 Exchange Online** , który zawiera **Exchange Active Sync** jako warunek aplikacji klienckich, możesz nie być w stanie skonsolidować ich w jednej nowej zasadzie. 

Dotyczy to na przykład sytuacji, w której chcesz obsługiwać wszystkie typy aplikacji klienta. W nowych zasadach, które mają **Exchange Active Sync** jako warunek aplikacji klienckich, nie można wybrać innych aplikacji klienckich.

![Usługa Azure Active Directory](./media/policy-migration/64.png)

Konsolidacja do jednej nowej zasady jest również niemożliwa, jeśli zasady klasyczne zawierają kilka warunków. Nowe zasady, które mają skonfigurowany warunek **Exchange Active Sync** jako aplikacje klienckie, nie obsługują innych warunków:   

![Usługa Azure Active Directory](./media/policy-migration/08.png)

Jeśli masz nowe zasady, dla których skonfigurowano warunek aplikacji klienta **Exchange Active Sync** , musisz upewnić się, że wszystkie inne warunki nie są skonfigurowane. 

![Usługa Azure Active Directory](./media/policy-migration/16.png)
 
Klasyczne zasady [oparte na aplikacji](technical-reference.md#approved-client-app-requirement) dla pakietu Office 365 Exchange Online, które zawierają **Exchange Active Sync** jako warunek aplikacji klienckich Zezwalaj na **obsługiwane** i **nieobsługiwane** [platformy urządzeń](technical-reference.md#device-platform-condition). Chociaż nie można skonfigurować poszczególnych platform urządzeń w odniesieniu do pokrewnych nowych zasad, można ograniczyć obsługę tylko do [obsługiwanych platform urządzeń](technical-reference.md#device-platform-condition) . 

![Usługa Azure Active Directory](./media/policy-migration/65.png)

Można skonsolidować wiele klasycznych zasad, które zawierają **Exchange Active Sync** jako warunek aplikacji klienckich, jeśli mają:

- Tylko **Exchange Active Sync** jako warunek 
- Skonfigurowano kilka wymagań dotyczących przyznawania dostępu

Typowym scenariuszem jest konsolidacja:

- Klasyczne zasady oparte na urządzeniach z klasycznego portalu Azure 
- Klasyczne zasady oparte na aplikacji w portalu ochrony aplikacji usługi Intune 
 
W takim przypadku można skonsolidować zasady klasyczne do jednej nowej zasady, która ma wybrane wymagania.

![Usługa Azure Active Directory](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Platformy urządzeń

Zasady klasyczne z [kontrolkami opartymi na aplikacji](technical-reference.md#approved-client-app-requirement) są wstępnie skonfigurowane z systemami iOS i Android jako [warunek platformy urządzenia](technical-reference.md#device-platform-condition). 

W nowych zasadach należy wybrać [platformy urządzeń](technical-reference.md#device-platform-condition) , które mają być obsługiwane pojedynczo.

![Usługa Azure Active Directory](./media/policy-migration/41.png)

## <a name="next-steps"></a>Następne kroki

- Jeśli chcesz dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zobacz [Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji, Azure Active Directory dostępu warunkowego](app-based-mfa.md).
- Aby skonfigurować zasady dostępu warunkowego dla środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w Azure Active Directory](best-practices.md). 
