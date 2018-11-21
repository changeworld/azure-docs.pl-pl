---
title: Rozwiązywanie problemów z widokami kosztów platformy Azure w przedsiębiorstwach | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać wszelkie problemy, które może być za pomocą widoków koszt organizacji w witrynie Azure portal.
author: rthorn17
manager: adpick
editor: ''
ms.assetid: ''
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2017
ms.author: cwatson
ms.openlocfilehash: be74714667c2fed57a797a972ddcd7949cae5933
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52274674"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Rozwiązywanie problemów z widoki kosztów przedsiębiorstwa 

Rejestracje enterprise są wiele ustawień, które może spowodować, że użytkownicy nie będą mogli wyświetlić koszty w ramach rejestracji.  Te ustawienia są zarządzane przez administratora rejestracji lub przez partnera, jeśli rejestracja nie została zakupiona bezpośrednio od firmy Microsoft.  Ten artykuł pomaga zrozumieć, co to są ustawienia oraz ich wpływ na rejestracji. Te ustawienia są niezależne od [ról RBAC platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). 


## <a name="enabling-access-to-costs"></a>Umożliwianie dostępu do kosztów

Widzisz komunikat bez autoryzacji, lub *"widoki kosztów są wyłączone w Twojej rejestracji".* Jeśli szukasz informacji o koszcie? ![nieautoryzowanych](media/billing-enterprise-mgmt-groups/unauthorized.png)

Może to być spowodowane jedną z następujących powodów:

1. Azure zakupione za pośrednictwem partnera przedsiębiorstwa i partnera nie zostało wydane jeszcze cen. Aby zwolnić, cennik, skontaktuj się z partnerem, aby zaktualizować ustawienia w ramach [witryny Enterprise portal](https://ea.azure.com).
2. Alternatywnie Jeśli jesteś klientem z bezpośrednimi umowami EA, istnieje kilka możliwości:
    * Jesteś właścicielem konta i Administrator rejestracji została wyłączona "opłaty widoku AO" ustawienie.  
    * Jesteś administratorem działu i administratorem rejestracji została wyłączona "opłaty widoku DA" ustawienie.
    * Skontaktuj się z administratorem rejestracji, aby uzyskać dostęp. Administrator rejestracji może odwiedzić [witryny Enterprise portal](https://ea.azure.com/manage/enrollment) i zaktualizować ustawienia, jak pokazano tutaj:

![Ustawienia portalu Enterprise](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)


## <a name="asset-is-unavailable"></a>Zasób jest niedostępny? 
Jeśli wyświetlany jest komunikat o błędzie "ten zasób jest niedostępny" podczas próby uzyskania dostępu do subskrypcji lub grupę zarządzania, a następnie możesz ma odpowiednią rolę, aby wyświetlić ten element.  

![zasób nie można odnaleźć](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Skontaktuj się z administrowanie grup subskrypcji i zarządzania dostępem.  
* W przypadku subskrypcji, należy odwołać [based kontroli dostępu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) dokumentu, aby uzyskać pomoc, na którym rola jest wymagana.
