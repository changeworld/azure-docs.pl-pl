---
title: Co to jest migracja zasad dostępu warunkowego usługi Azure Active Directory? | Microsoft Docs
description: Dowiedz się, co musisz wiedzieć, by Migrowanie zasad klasycznych w witrynie Azure portal.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy w usłudze Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/24/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 6123e05d6e20d213a859836ddf55a3ca6f7255a5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224572"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>Co to jest migracja zasad dostępu warunkowego usługi Azure Active Directory? 


[Dostęp warunkowy](active-directory-conditional-access-azure-portal.md) jest to funkcja usługi Azure Active directory (Azure AD), która umożliwia kontrolowanie sposobu autoryzowany dostęp użytkownikom aplikacje w chmurze. Gdy celem jest nadal takie same, wydanie nowej witryny Azure portal wprowadził znaczne ulepszenia w sposobie działania dostępu warunkowego.

Należy rozważyć Migrowanie zasad, które nie zostały utworzone w witrynie Azure portal ponieważ:

- Teraz można rozwiązać scenariusze, które nie może obsłużyć przed.

- Można zmniejszyć liczbę zasad, którą trzeba zarządzać konsolidując je.   

- Możesz zarządzać wszystkie zasady dostępu warunkowego w jednej centralnej lokalizacji.

- Klasycznym portalu Azure zostaną wycofane.   

W tym artykule opisano, co musisz wiedzieć, aby przeprowadzić migrację istniejących zasad dostępu warunkowego do nowej struktury.
 
## <a name="classic-policies"></a>Zasady klasyczne

W [witryny Azure portal](https://portal.azure.com), [dostępu warunkowego — zasady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) strona jest punktem wejścia do dostępu warunkowego zasady. Jednak w danym środowisku, może również mieć zasady dostępu warunkowego, które nie zostały utworzone przy użyciu tej strony. Zasady te są znane jako *zasady klasyczne*. Zasady klasyczne zasady dostępu warunkowego, utworzonych przez siebie w:

- Klasyczny portal Azure
- Portalu klasycznego usługi Intune
- Portal usługi Intune App Protection


Na **dostępu warunkowego** stronie klasycznego zasad można skorzystać, klikając [ **zasady klasyczne (wersja zapoznawcza)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) w **Zarządzaj** sekcja. 


![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/71.png)


**Zasady klasyczne** widok zawiera opcję, aby:

- Filtrowanie klasycznych zasad.
 
    ![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/72.png)

- Wyłącz zasady klasyczne.

    ![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/73.png)
   
- Przejrzyj ustawienia zasad klasycznych (i wyłączyć).

    ![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/74.png)


Wyłączenie zasad klasycznych już nie można przywrócić tego kroku. Dlatego można modyfikować członkostwa w grupie przy użyciu zasad klasycznych **szczegóły** widoku. 

![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/75.png)

Zmieniając wybranych grup lub wykluczanie określonych grup można sprawdzić wpływ wyłączenia zasad klasycznych dla kilku użytkowników testowych przed wyłączeniem zasad dla wszystkich jego użytkowników i grup. 



## <a name="azure-ad-conditional-access-policies"></a>Zasady dostępu warunkowego w usłudze Azure AD

Przy użyciu dostępu warunkowego w witrynie Azure portal można zarządzać w jednej centralnej lokalizacji wszystkich zasad. Ponieważ implementacja jak dostęp warunkowy znacznie zmieniła się, należy zapoznać się z podstawowe pojęcia, przed przeprowadzeniem migracji zasad klasycznych.

Zobacz:

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal.md) informacje na temat podstawowych pojęć i terminologii.

- [Najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md) Aby uzyskać wskazówki dotyczące wdrażania dostępu warunkowego w Twojej organizacji.

- [Wymagać uwierzytelniania Wieloskładnikowego dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](active-directory-conditional-access-app-based-mfa.md) możliwość zapoznania się z interfejsem użytkownika w witrynie Azure portal.


 
## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

W tym artykule, zasady dostępu warunkowego usługi Azure AD są również określane jako *nowe zasady*.
Zasady klasyczne w dalszym ciągu współpracują z nowych zasad, dopóki nie zostanie wyłączone lub usuń je. 

Istotne w kontekście konsolidacji zasad są następujące aspekty:

- Gdy zasady klasyczne są powiązane z aplikacji w określonej chmurze, możesz wybrać dowolną liczbę aplikacji w chmurze na potrzeby nowych zasad.

- Formanty zasad klasycznych i nowych zasad dla aplikacji w chmurze wymagają wszystkie kontrolki (*i*) do spełnienia. 


- W nowych zasad możesz wykonywać następujące czynności:
 
    - Połącz wiele warunków, jeśli jest to wymagane przez danego scenariusza. 

    - Wybierz kilka udzielić wymagania, jak dostęp kontrolować, a także połączyć je z logicznych *lub* (wymaga jednej z wybranych kontrolek) lub wartość logiczna *i* (wymagane są wszystkie z wybranych kontrolek).

        ![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/25.png)




### <a name="office-365-exchange-online"></a>Usługi Office 365 Exchange online

Jeśli chcesz przeprowadzić migrację zasad klasycznych **Office 365 Exchange online** zawierające **programu Exchange Active Sync** jako warunku aplikacji klienta, nie można skonsolidować je do jednego z nowych zasad. 

Dotyczy, na przykład, jeśli chcesz obsługiwać wszystkie typy aplikacji klienckich. W nowych zasad, który ma **programu Exchange Active Sync** jako warunku aplikacji klienta, nie można wybrać inne aplikacje klienckie.

![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/64.png)

Konsolidacji do jednego z nowych zasad jest również możliwe, jeśli zasady klasyczne zawiera kilka warunków. Nowe zasady, które ma **programu Exchange Active Sync** jako aplikacje klienckie skonfigurowane warunku nie obsługuje inne warunki:   

![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/08.png)

Jeśli masz nowe zasady, które ma **programu Exchange Active Sync** jako aplikacje klienckie warunek skonfigurowany, należy się upewnić, że inne warunki nie są skonfigurowane. 

![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/16.png)
 

[Oparty na aplikacji](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) zasady klasyczne dla Office 365 Exchange Online, które obejmują **programu Exchange Active Sync** jako warunku aplikacje klienckie Zezwalaj **obsługiwane** i **nieobsługiwany** [platform urządzeń](active-directory-conditional-access-technical-reference.md#device-platform-condition). Mimo że nie można skonfigurować platform poszczególnych urządzeń w powiązanych nowe zasady, można ograniczyć techniczną, aby [obsługiwane platformy urządzeń](active-directory-conditional-access-technical-reference.md#device-platform-condition) tylko. 

![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/65.png)

Można skonsolidować wiele zasady klasyczne, które obejmują **programu Exchange Active Sync** jako warunku aplikacji klienta, jeśli mają one:

- Tylko **programu Exchange Active Sync** jako warunku 

- Kilka wymagań dotyczących przyznawania dostępu skonfigurowane

Jeden typowy scenariusz polega na konsolidacji:

- Zasady klasyczne opartego na urządzeniach z klasycznego portalu Azure 
- Na podstawie aplikacji zasad klasycznych w portalu usługi Intune app protection 
 
W takim przypadku można skonsolidować zasad klasycznych w jednym nowe zasady, które wiążą się zarówno wymagania wybrane.

![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/62.png)



### <a name="device-platforms"></a>Platformy urządzeń

Zasady klasyczne z [opartego na aplikacji kontrolki](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) mają wstępnie skonfigurowaną dla systemów iOS i Android jako [warunek platformy urządzenia](active-directory-conditional-access-technical-reference.md#device-platform-condition). 

W nowych zasad, musisz wybrać [platform urządzeń](active-directory-conditional-access-technical-reference.md#device-platform-condition) mają być obsługiwane osobno.

![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/41.png)



 
 


## <a name="next-steps"></a>Kolejne kroki

- Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [GRequire MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).

- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md). 
