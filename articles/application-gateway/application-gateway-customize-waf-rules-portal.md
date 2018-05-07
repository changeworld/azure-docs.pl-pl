---
title: Dostosowywanie reguły zapory aplikacji sieci web w brama usługi aplikacji Azure — portalu Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje na temat sposobu dostosowywania sieci web reguły zapory aplikacji w polu Brama aplikacji w portalu Azure.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: victorh
ms.openlocfilehash: ae61e3a8308e95c16ccde71de37fb10666ef0df9
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Dostosowywanie reguły zapory aplikacji sieci web za pośrednictwem portalu Azure

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [Program PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](application-gateway-customize-waf-rules-cli.md)

Brama aplikacji w usłudze Azure zapory aplikacji sieci web (WAF) zapewnia ochronę dla aplikacji sieci web. Te zabezpieczenia są dostarczane przez Otwórz sieci Web aplikacji zabezpieczeń projektu (OWASP) podstawowe reguły Ustaw (CRS). Niektóre zasady mogą spowodować fałszywych alarmów i zablokowanie ruchu prawdziwe. Z tego powodu bramy aplikacji oferuje możliwość dostosowywania grup reguł i zasad. Aby uzyskać więcej informacji na określonej reguły grup i reguł, zobacz [listy grup reguł CRS zapory aplikacji sieci web i reguł](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Jeśli bramy aplikacji nie używa warstwy zapory aplikacji sieci Web, opcja uaktualnienia brama aplikacji w warstwie zapory aplikacji sieci Web zostanie wyświetlony w okienku po prawej stronie. 

![Włączanie zapory aplikacji sieci Web][fig1]

## <a name="view-rule-groups-and-rules"></a>Widok grup reguł i zasad

**Aby wyświetlić grupy reguł i zasad**
   1. Przejdź na bramie aplikacji, a następnie wybierz **zapory aplikacji sieci Web**.  
   2. Wybierz **reguły zaawansowanej konfiguracji**.  
   Ten widok przedstawia tabeli na stronie grupy reguł podaną w zestawie reguł wybrany. Zaznaczono wszystkie pola wyboru reguły.

![Konfigurowanie reguł wyłączone][1]

## <a name="search-for-rules-to-disable"></a>Wyszukaj regułę do wyłączenia

**Aplikacja, ustawienia zapory w sieci Web** bloku umożliwia filtrowanie reguł za pomocą funkcji wyszukiwania tekstu. Wynik wyświetla tylko grupy reguł i reguł, które zawierają tekst, który wyszukiwany.

![Wyszukaj zasady][2]

## <a name="disable-rule-groups-and-rules"></a>Wyłącz zasady grupy i zasady

Gdy sieci są wyłączenie reguł, można wyłączyć reguły całej grupy lub określone zasady w co najmniej jedną grupę reguł. 

**Aby wyłączyć grupy reguł lub określone zasady**

   1. Wyszukiwanie reguły lub grupy reguł, które mają zostać wyłączone.
   2. Usuń zaznaczenie pola wyboru dla reguł, które mają zostać wyłączone. 
   2. Wybierz pozycję **Zapisz**. 

![Zapisz zmiany][3]

## <a name="next-steps"></a>Kolejne kroki

Po skonfigurowaniu reguł wyłączonych można poznać sposoby wyświetlania dzienników zapory aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [diagnostyki bramy aplikacji](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
