---
title: Co to jest migracja zasad dostępu warunkowego w usłudze Azure Active Directory? | Microsoft Docs
description: Dowiedz się, co należy wiedzieć, aby migrować klasyczny zasad w portalu Azure.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy przy użyciu usługi Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: femila
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 4a9b3df66567c4170ba861d3e597261e37271bf1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232428"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>Co to jest migracja zasad dostępu warunkowego w usłudze Azure Active Directory? 


[Dostęp warunkowy](active-directory-conditional-access-azure-portal.md) jest możliwość Azure Active Directory (Azure AD), który umożliwia kontrolowanie sposobu autoryzacji dostępu użytkowników aplikacji w chmurze. Gdy celem jest taka sama, wersji portalu Azure ma wprowadzono znaczne ulepszenia w sposobie działania dostępu warunkowego.

Należy rozważyć Migrowanie zasad, które nie zostały utworzone w portalu Azure ponieważ:

- Można teraz adresów scenariusze, które nie może obsłużyć przed.

- Pozwala zmniejszyć liczbę zasad, które trzeba zarządzać konsolidując je.   

- Można zarządzać z zasad dostępu warunkowego w jednej centralnej lokalizacji.

- Klasycznym portalu Azure zostaną wycofane.   

W tym artykule opisano, co należy wiedzieć, aby przeprowadzić migrację istniejących zasad dostępu warunkowego do nowej struktury.
 
## <a name="classic-policies"></a>Zasady klasyczne

W [portalu Azure](https://portal.azure.com), [dostępu warunkowego — zasady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) strona jest punktem wejścia z dostępu warunkowego zasady. Jednak w danym środowisku, może również być zasady dostępu warunkowego, które nie zostały utworzone przy użyciu tej strony. Te zasady są określane jako *klasycznego zasady*. Klasycznym zasady są zasady dostępu warunkowego, zostały utworzone w:

- klasyczny portal Azure
- W klasycznym portalu usługi Intune
- Portal ochrony aplikacji usługi Intune


Na **dostępu warunkowego** strony, klasycznym zasad można uzyskać, klikając [ **zasady klasyczny (wersja zapoznawcza)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) w **Zarządzaj** sekcja. 


![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/71.png)


**Zasady Classic** widok udostępnia opcję, aby:

- Filtr klasycznego zasad.
 
    ![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/72.png)

- Wyłącz zasady klasycznego.

    ![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/73.png)
   
- Przejrzyj ustawienia, klasyczne zasad (i wyłączyć tę funkcję).

    ![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/74.png)


Wyłączenie klasycznego zasad już nie można przywrócić ten krok. Dlatego można zmodyfikować członkostwa grupy w klasycznym zasad przy użyciu opcji **szczegóły** widoku. 

![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/75.png)

Zmieniając wybranych grup lub wyłączając określonych grup można przetestować wpływu wyłączone zasad klasycznego dla kilku użytkowników testowych przed wyłączeniem zasady dla wszystkich jego użytkowników i grup. 



## <a name="azure-ad-conditional-access-policies"></a>Zasady dostępu warunkowego w usłudze Azure AD

Przy użyciu dostępu warunkowego w portalu Azure możesz zarządzać wszystkich zasad w jednej centralnej lokalizacji. Ponieważ zmieniła się znacznie implementacji jak dostęp warunkowy, należy zapoznać się podstawowe pojęcia związane z przed migracją klasycznego zasad.

Zobacz:

- [Co to jest dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal.md) informacje na temat podstawowych pojęć i terminologii.

- [Najlepsze rozwiązania dla dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md) Aby uzyskać instrukcje na temat wdrażania dostępu warunkowego w Twojej organizacji.

- [Uwierzytelniania MFA można wymagać dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](active-directory-conditional-access-app-based-mfa.md) zapoznać się z interfejsu użytkownika w portalu Azure.


 
## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

W tym artykule, zasady dostępu warunkowego usługi Azure AD są również nazywane *nowych zasad*.
Klasycznym zasad w dalszym ciągu działać równolegle z nowych zasad, dopóki Wyłącz lub usuń je. 

Następujące aspekty są ważne w kontekście konsolidacji zasad:

- Podczas aplikacji określonej chmury są również powiązane zasady klasycznego, można wybrać dowolną liczbę aplikacji w chmurze potrzebną do nowych zasad.

- Formanty klasycznego zasad oraz nowe zasady dla aplikacji w chmurze wymagają wszystkich kontrolek (*i*) do spełnienia. 


- W nowych zasad można:
 
    - Jeśli jest to wymagane przez dany scenariusz, należy połączyć wiele warunków. 

    - Wybierz przyznać kilka wymagań dostęp kontroli oraz łączyć je z logiczną *lub* (wymagają wybranych formantów) lub za pomocą operatorów logicznych *i* (wymagają wszystkie formanty).

        ![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/25.png)




### <a name="office-365-exchange-online"></a>Usługi Office 365 Exchange online

Jeśli użytkownik chce migrować klasyczny zasady dla **usługi Office 365 Exchange online** zawierające **programu Exchange Active Sync** warunkiem aplikacji klienta, nie można konsolidować je w jedną nową zasadę. 

Dotyczy, na przykład, jeśli chcesz obsługiwać wszystkie typy aplikacji klienckich. W nowych zasad, które ma **programu Exchange Active Sync** warunkiem aplikacji klienta, nie można wybrać inne aplikacje klienckie.

![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/64.png)

Konsolidacja w jedną nową zasadę również nie jest możliwe, jeśli klasycznego zasad zawiera kilka warunków. Nowe zasady, które ma **programu Exchange Active Sync** jako aplikacje klienckie skonfigurowane warunku nie obsługuje inne warunki:   

![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/08.png)

Jeśli masz nowe zasady, które ma **programu Exchange Active Sync** jako aplikacje klienckie warunku skonfigurowany, należy się upewnić, że wszystkie inne warunki nie są skonfigurowane. 

![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/16.png)
 

[Na podstawie aplikacji](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) klasycznego zasad dla Office 365 usługi Exchange Online, które zawierają **programu Exchange Active Sync** warunkiem aplikacje klienckie Zezwalaj **obsługiwane** i **nieobsługiwany** [platform urządzeń](active-directory-conditional-access-technical-reference.md#device-platform-condition). Mimo że platformy poszczególnych urządzeń nie można skonfigurować w powiązanych nowe zasady, można ograniczyć techniczną w celu [obsługiwanych platform urządzeń](active-directory-conditional-access-technical-reference.md#device-platform-condition) tylko. 

![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/65.png)

Umożliwiającej obsługę wielu zasad klasycznych, które zawierają **programu Exchange Active Sync** warunkiem aplikacji klienckich, gdy mają:

- Tylko **programu Exchange Active Sync** jako warunek 

- Kilka wymagań przyznawania dostępu skonfigurowane

Jeden typowy scenariusz polega na konsolidacji:

- Zasady klasycznego oparta na urządzeniach z klasycznego portalu Azure 
- Na podstawie aplikacji zasad klasycznego w portalu ochrony aplikacji usługi Intune 
 
W takim przypadku można skonsolidować klasycznego zasad w jednym nowe zasady, które ma zarówno wymagania wybrane.

![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/62.png)



### <a name="device-platforms"></a>Platformy urządzeń

Klasycznym zasad z [formantów na podstawie aplikacji](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) są wstępnie skonfigurowany z użyciem systemów iOS i Android jako [warunku platformy urządzenia](active-directory-conditional-access-technical-reference.md#device-platform-condition). 

W nowych zasad, musisz wybrać [platform urządzeń](active-directory-conditional-access-technical-reference.md#device-platform-condition) mają być obsługiwani indywidualnie.

![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/41.png)



 
 


## <a name="next-steps"></a>Kolejne kroki

- Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [GRequire MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).

- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepszych rozwiązań dotyczących dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md). 
