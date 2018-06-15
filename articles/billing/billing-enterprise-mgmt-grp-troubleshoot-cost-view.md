---
title: Rozwiązywanie problemów z enterprise koszt widoków - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać problemy mogą się z widokami koszt organizacji w portalu Azure.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2017
ms.author: rithorn
ms.openlocfilehash: 193e4691d2e10dd1c5ad16c26ad25534e3156745
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32770359"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Rozwiązywanie problemów z enterprise koszt widoków 

W ramach rejestracji enterprise istnieje wiele ustawień, które może spowodować, że użytkownicy nie będą mogli wyświetlić kosztów w ramach rejestracji.  Jeśli rejestracja nie została zakupiona bezpośrednio z firmą Microsoft, te ustawienia są zarządzane przez administratora rejestracji lub przez partnera.  Ten artykuł pomaga w zrozumieniu ustawienia są oraz ich wpływ na rejestracji. Te ustawienia są niezależne od [role RBAC Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). 


## <a name="enabling-access-to-costs"></a>Włączanie dostępu do kosztów

Czy występuje komunikat bez autoryzacji, lub *"Koszt widoki są wyłączone w Twojej rejestracji."* Jeśli szukasz informacji o kosztach? ![nieautoryzowanego](media/billing-enterprise-mgmt-groups/unauthorized.png)

Może to być spowodowane jedną z następujących powodów:

1. Azure zostały zakupione w ramach organizacji partnera, a partner nie wydane jeszcze cennik. Aby zwolnić cennika, skontaktuj się z partnerem, aby zaktualizować ustawienia w [Enterprise portal](https://ea.azure.com).
2. Alternatywnie Jeśli odbiorcy EA bezpośredniego, istnieje kilka możliwości:
    * Jesteś właścicielem konta i Administrator rejestracji wyłączył "opłaty widoku AO" ustawienie.  
    * Jesteś administratorem działu, a Administrator rejestracji wyłączył "opłaty widoku DA" ustawienie.
    * Skontaktuj się z administratorem rejestracji, aby uzyskać dostęp. Administrator rejestracji mogą odwiedzać [Enterprise portal](https://ea.azure.com/manage/enrollment) i zaktualizuj ustawienia, jak pokazano poniżej:

![Ustawienia portalu Enterprise](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)


## <a name="asset-is-unavailable"></a>Zasobów jest niedostępny? 
Jeśli wyświetlany jest komunikat o błędzie "ten zasób jest niedostępny" podczas próby uzyskania dostępu do grupy zarządzania lub subskrypcji, możesz nie ma odpowiednią rolę, aby wyświetlić ten element.  

![zasobów nie można odnaleźć](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Skontaktuj się z Administruj grup subskrypcji lub zarządzania, aby uzyskać dostęp.  
* Subskrypcji, odwołanie [based kontroli dostępu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) dokumentu, aby uzyskać pomoc, na którym rola jest wymagana.
