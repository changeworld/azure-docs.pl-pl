---
title: Dostosowywanie reguł zapory aplikacji sieci web w usłudze Azure Application Gateway — witryna Azure portal | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje na temat sposobu dostosowywania reguł zapory aplikacji internetowych w usłudze Application Gateway przy użyciu witryny Azure portal.
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
ms.openlocfilehash: 30df26dc3a9697d3435779f91c32b2d99a747b88
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990471"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Dostosowywanie reguł zapory aplikacji sieci web za pośrednictwem witryny Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [Program PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](application-gateway-customize-waf-rules-cli.md)

Zapora aplikacji sieci web usługi Azure Application Gateway (WAF) zapewnia ochronę aplikacji sieci web. Te zabezpieczenia stosowane są dostarczane przez Otwórz sieci Web aplikacji Security Project (OWASP) podstawowych reguł Ustaw (CRS). Niektóre reguły może spowodować, że wyniki fałszywie dodatnie i blokować ruch rzeczywistych. Z tego powodu Application Gateway oferuje możliwości dostosowywania grup reguł i reguł. Aby uzyskać więcej informacji na temat grup określonych reguł i reguł, zobacz [listę grup reguł CRS zapory aplikacji sieci web i reguły](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Jeśli Twoja brama application gateway nie korzysta z warstwy zapory aplikacji sieci Web, opcja uaktualnienia bramy aplikacji do warstwy zapory aplikacji sieci Web pojawi się w okienku po prawej stronie. 

![Włączanie zapory aplikacji sieci Web][fig1]

## <a name="view-rule-groups-and-rules"></a>Widok grup reguł i reguł

**Aby wyświetlić grup reguł i reguł**
   1. Przejdź do usługi application gateway, a następnie wybierz pozycję **zapory aplikacji sieci Web**.  
   2. Wybierz **Zaawansowana konfiguracja reguł**.  
   Ten widok przedstawia tabelę na stronie wszystkie grupy reguł, wyposażone w zestawie reguł wybrany. Wszystkie reguły pola wyboru są zaznaczone.

![Konfigurowanie reguł wyłączone][1]

## <a name="search-for-rules-to-disable"></a>Wyszukaj reguły, aby wyłączyć

**Ustawienia zapory aplikacji internetowych** bloku umożliwia filtrowanie reguł za pomocą funkcji wyszukiwania tekstu. Wynik przedstawia tylko grup reguł i reguł, które zawierają tekst, który wyszukiwany.

![Wyszukaj reguły][2]

## <a name="disable-rule-groups-and-rules"></a>Wyłącz grup reguł i reguł

Gdy usługi są wyłączając reguły, możesz wyłączyć grupy całej reguły lub określone zasady w co najmniej jedną grupę reguł. 

**Aby wyłączyć grup reguł lub określone zasady**

   1. Wyszukaj reguły lub grupy reguł, które chcesz wyłączyć.
   2. Usuń zaznaczenie pola wyboru dla reguł, które chcesz wyłączyć. 
   2. Wybierz pozycję **Zapisz**. 

![Zapisz zmiany][3]

## <a name="next-steps"></a>Kolejne kroki

Po skonfigurowaniu reguł wyłączonych, możesz dowiedzieć się, jak wyświetlić dzienniki zapory aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [diagnostyki usługi Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
