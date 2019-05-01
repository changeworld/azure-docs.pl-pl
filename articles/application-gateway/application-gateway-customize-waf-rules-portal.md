---
title: Dostosowywanie reguł zapory aplikacji sieci web w usłudze Azure Application Gateway — witryna Azure portal
description: Ten artykuł zawiera informacje na temat sposobu dostosowywania reguł zapory aplikacji internetowych w usłudze Application Gateway przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: f7ffb8d6adfd4afc75618834a3fe82cf9a3d0c9f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720386"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Dostosowywanie reguł zapory aplikacji sieci web za pośrednictwem witryny Azure portal

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

**Ustawienia zapory aplikacji internetowych** strona zapewnia możliwość filtrowania reguły za pomocą funkcji wyszukiwania tekstu. Wynik przedstawia tylko grup reguł i reguł, które zawierają tekst, który wyszukiwany.

![Wyszukaj reguły][2]

## <a name="disable-rule-groups-and-rules"></a>Wyłącz grup reguł i reguł

> [!IMPORTANT]
> Wyłączanie wszystkich innych grup reguł i reguł, należy zachować ostrożność. Może to wystawić na zagrożenia bezpieczeństwa.

Masz wyłączając reguły można wyłączyć grupy całej reguły lub określone zasady w co najmniej jedną grupę reguł. 

**Aby wyłączyć grup reguł lub określone zasady**

   1. Wyszukaj reguły lub grupy reguł, które chcesz wyłączyć.
   2. Usuń zaznaczenie pola wyboru dla reguł, które chcesz wyłączyć. 
   2. Wybierz pozycję **Zapisz**. 

![Zapisz zmiany][3]

## <a name="mandatory-rules"></a>Obowiązujące reguły

Poniższa lista zawiera warunki powodujące zapory aplikacji sieci Web zablokować żądania w trybie zapobiegania. W trybie wykrywania są rejestrowane jako wyjątki.

Nie są skonfigurowane lub wyłączone:

* Nie można przeanalizować treść żądania wyników w żądaniu blokowane, chyba że jednostka inspekcji jest wyłączona (XML, JSON, dane formularza)
* Długość danych treści (nie plikami) żądania jest większy niż skonfigurowany limit
* Żądanie jest większy niż limit treści (w tym pliki)
* Wystąpił błąd wewnętrzny związany w aparacie zapory aplikacji sieci Web

CRS 3.x określonych:

* Liczba przychodzących anomalii wynik przekroczył próg

## <a name="next-steps"></a>Kolejne kroki

Po skonfigurowaniu reguł wyłączonych, możesz dowiedzieć się, jak wyświetlić dzienniki zapory aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [diagnostyki usługi Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
