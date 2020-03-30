---
title: Dostosowywanie reguł przy użyciu portalu — Zapora aplikacji sieci Web platformy Azure
description: Ten artykuł zawiera informacje dotyczące dostosowywania reguł zapory aplikacji sieci Web w bramie aplikacji za pomocą witryny Azure portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048381"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Dostosowywanie reguł zapory aplikacji sieci Web przy użyciu portalu Azure

Zapora aplikacji bramy aplikacji azure (WAF) zapewnia ochronę aplikacji sieci web. Zabezpieczenia te są dostarczane przez podstawowy zestaw reguł projektu OWASP (Open Web Application Security Project). Niektóre reguły mogą powodować fałszywe alarmy i blokować rzeczywisty ruch. Z tego powodu brama aplikacji umożliwia dostosowanie grup reguł i reguł. Aby uzyskać więcej informacji na temat określonych grup i reguł reguł, zobacz [Lista grup reguł i reguł crs zapory aplikacji sieci Web](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Jeśli brama aplikacji nie używa warstwy WAF, opcja uaktualnienia bramy aplikacji do warstwy WAF jest wyświetlana w prawym okienku. 

![Włączanie wafie WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Wyświetlanie grup reguł i reguł

**Aby wyświetlić grupy reguł i reguły**
1. Przejdź do bramy aplikacji, a następnie wybierz **zaporę aplikacji sieci Web**.  
2. Wybierz zasady **WAF**.
2. Wybierz **pozycję Reguły zarządzane**.

   W tym widoku jest wyświetlana tabela na stronie wszystkich grup reguł z wybranym zestawem reguł. Zaznaczone są wszystkie pola wyboru reguły.

## <a name="disable-rule-groups-and-rules"></a>Wyłączanie grup i reguł

> [!IMPORTANT]
> Należy zachować ostrożność podczas wyłączania grup reguł lub reguł. Może to narazić cię na zwiększone zagrożenia bezpieczeństwa.

Po wyłączeniu reguł można wyłączyć całą grupę reguł lub określone reguły w ramach co najmniej jednej grupy reguł. 

**Aby wyłączyć grupy reguł lub określone reguły**

   1. Wyszukaj reguły lub grupy reguł, które chcesz wyłączyć.
   2. Zaznacz pola wyboru reguł, które chcesz wyłączyć. 
   3. Wybierz akcję u góry strony (włącz/wyłącz) dla wybranych reguł.
   2. Wybierz **pozycję Zapisz**. 

![Zapisz zmiany][3]

## <a name="mandatory-rules"></a>Przepisy obowiązkowe

Poniższa lista zawiera warunki, które powodują, że WAF zablokować żądanie w trybie zapobiegania. W trybie wykrywania są one rejestrowane jako wyjątki.

Tych nie można skonfigurować ani wyłączyć:

* Niesprzejęcie analizy treści żądania powoduje zablokowanie żądania, chyba że inspekcja ciała jest wyłączona (XML, JSON, dane formularza)
* Długość danych treści żądania (bez plików) jest większa niż skonfigurowany limit
* Treść żądania (łącznie z plikami) jest większa niż limit
* Wystąpił błąd wewnętrzny w silniku WAF

CRS 3.x specyficzne:

* Wynik anomalii ruchu przychodzącego przekroczył próg

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu wyłączonych reguł możesz dowiedzieć się, jak wyświetlać dzienniki WAF. Aby uzyskać więcej informacji, zobacz [Diagnostyka bramy aplikacji](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
