---
title: Listy wykluczeń zapory aplikacji sieci Web w drzwiach frontowych platformy Azure — witryna Azure portal
description: Ten artykuł zawiera informacje na temat konfiguracji list wykluczeń w usłudze Azure Front za pomocą witryny Azure portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925932"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Zapora aplikacji sieci Web (WAF) z listami wykluczeń usługi drzwiami frontowymi 

Czasami Zapora aplikacji sieci Web (WAF) może zablokować żądanie, które chcesz zezwolić na aplikację. Na przykład usługa Active Directory wstawia tokeny, które są używane do uwierzytelniania. Tokeny te mogą zawierać znaki specjalne, które mogą wyzwolić fałszywie dodatnie z reguł WAF. Listy wykluczeń WAF umożliwiają pominięcie niektórych atrybutów żądania z oceny WAF.  Listę wykluczeń można skonfigurować przy użyciu [aplikacji PowserShell](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0), [Azure CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), [Rest API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)lub witryny Azure portal. W poniższym przykładzie przedstawiono konfigurację portalu Azure. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Konfigurowanie list wykluczeń przy użyciu witryny Azure portal
**Zarządzanie wykluczeniami** jest dostępne z portalu WAF w obszarze **Reguły zarządzane**

![Zarządzanie](../media/waf-front-door-exclusion/exclusion1.png)
![wykluczeniem Zarządzaj exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 Przykładowa lista ![wykluczeń: Zarządzanie exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

W tym przykładzie nie obejmuje wartości w polu nagłówka *użytkownika.* Prawidłowe żądanie może zawierać pole *użytkownika,* które zawiera ciąg, który wyzwala regułę iniekcji SQL. W takim przypadku można wykluczyć parametr *użytkownika,* aby reguła WAF nie oceniała niczego w tym polu.

Następujące atrybuty można dodać do list wykluczeń według nazwy. Wartości pól, których używasz nie są oceniane względem reguł WAF, ale ich nazwy są oceniane. Listy wykluczeń usuwają inspekcję wartości pola.

* Nazwa nagłówka żądania
* Nazwa pliku cookie żądania
* Nazwa argumentów ciągów kwerend
* Nazwa argumentów żądania wpisu treści

Można określić dokładne dopasowanie nagłówka żądania, treści, pliku cookie lub ciągu zapytania.  Lub opcjonalnie można określić częściowe dopasowania. Obsługiwane kryteria dopasowania są następujące operatory:

- **Równa się:** Ten operator jest używany do dokładnego dopasowania. Na przykład, aby wybrać nagłówek o nazwie **bearerToken**, użyj operatora równego sobie z selektorem ustawionym jako **bearerToken**.
- **Rozpoczyna się**od: Ten operator dopasowuje wszystkie pola rozpoczynające się od określonej wartości selektora.
- **Kończy się:** Ten operator dopasowuje wszystkie pola żądania, które kończą się określoną wartością selektora.
- **Zawiera**: Ten operator dopasowuje wszystkie pola żądania, które zawierają określoną wartość selektora.
- **Równa się :** Ten operator dopasowuje wszystkie pola żądania. * jest wartością selektora.

Nazwy nagłówków i plików cookie są niewrażliwe na wielkości liter.

Listę wykluczeń można zastosować do wszystkich reguł w ramach zestawu reguł zarządzanych, reguł dla określonej grupy reguł lub do pojedynczej reguły, jak pokazano w poprzednim przykładzie. 

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu ustawień WAF dowiedz się, jak wyświetlić dzienniki WAF. Aby uzyskać więcej informacji, zobacz [Diagnostyka drzwi przednich](../afds/waf-front-door-monitor.md).
