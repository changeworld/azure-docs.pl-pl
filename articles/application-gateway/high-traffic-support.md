---
title: Obsługa dużych przewozów bramy aplikacji
description: Ten artykuł zawiera wskazówki dotyczące konfigurowania usługi Azure Application Gateway w celu obsługi scenariuszy woluminu o dużym natężeniu ruchu sieciowego.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 65f404b52a5fc06d8fa5bb5aad291e57fde8caba
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617201"
---
# <a name="application-gateway-high-traffic-support"></a>Obsługa dużego natężenia ruchu usługi Application Gateway

>[!NOTE]
> W tym artykule opisano kilka sugerowanych wskazówek, które pomogą Ci skonfigurować bramę aplikacji do obsługi dodatkowego ruchu z powodu dużej ilości ruchu, który może wystąpić z powodu kryzysu COVID-19.

Bramy aplikacji z zaporą aplikacji sieci Web (WAF) można użyć do skalowalnego i bezpiecznego zarządzania ruchem do aplikacji sieci web.

Poniższe sugestie ułatwiają skonfigurowanie bramy aplikacji z waf do obsługi dodatkowego ruchu.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Użyj jednostki SKU w wersji 2 w wersji 1, aby uzyskać możliwości skalowania automatycznego i korzyści z wydajności
Jednostka SKU w wersji 2 oferuje skalowanie automatyczne, aby upewnić się, że brama aplikacji może być skalowana w górę wraz ze wzrostem ruchu. Oferuje również inne znaczące korzyści z wydajności, takie jak 5-krotnie lepsza wydajność odciążania TLS, szybsze wdrażanie i aktualizowanie, nadmiarowość strefy i inne w porównaniu z wersjami 1. Aby uzyskać więcej informacji, zapoznaj się z naszą [dokumentacją v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Ustawianie maksymalnej liczby wystąpień na maksymalną możliwą (125)
 
Zakładając, że masz jednostkę SKU bramy aplikacji w wersji 2, ustawienie maksymalnej liczby wystąpień na maksymalną możliwą wartość 125 umożliwia bramie aplikacji skalowanie w poziomie w razie potrzeby. Dzięki temu do obsługi możliwe zwiększenie ruchu do aplikacji. Opłata zostanie naliczona tylko za jednostki pojemnościowe, których używasz.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Ustaw minimalną liczbę wystąpień na podstawie średniego użycia CU

Przy założeniu, że masz SKU bramy aplikacji w wersji 2, skalowanie automatyczne trwa od sześciu do siedmiu minut, aby skalować w poziomie. Przy wyższej minimalnej liczbie wystąpień brama aplikacji może lepiej obsługiwać ruch, gdy obciążenie jest zwiększone, ponieważ skok ruchu nie wymaga operacji skalowania automatycznego.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Alert, jeśli pewna metryka przekracza 75% średniego wykorzystania CU 
Zobacz [dokumentację metryk bramy aplikacji,](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) aby uzyskać szczegółowe wyjaśnienie naszych metryk i innych instruktajów. 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Przykład: Konfigurowanie alertu o 75% średniego użycia cu

W tym przykładzie pokazano, jak za pomocą witryny Azure portal, aby skonfigurować alert po osiągnięciu 75% średniego użycia cu. 
1. Przejdź do **bramy aplikacji**.
2. Na lewym panelu wybierz **pozycję Metryki** na karcie **Monitorowanie.** 
3. Dodaj metrykę dla **średnich bieżących jednostek obliczeniowych**. 
![Konfigurowanie danych WAF](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Jeśli ustawisz minimalną liczbę wystąpień jako średnie użycie cu, śmiało i ustawić alert, gdy 75% minimalnych wystąpień są w użyciu. Na przykład jeśli średnie użycie wynosi 10 cUs, ustaw alert na 7,5 CU. To ostrzega, jeśli użycie wzrasta i daje czas na odpowiedź. Możesz podnieść minimum, jeśli uważasz, że ten ruch zostanie utrzymany, aby ostrzec Cię, że ruch może być coraz większy. 
![Konfigurowanie alertu WAF](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> Można ustawić alert występuje na niższy lub wyższy procent wykorzystania CU w zależności od tego, jak wrażliwe mają być do potencjalnych skoków ruchu.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Konfigurowanie waf z geofiltringiem i ochroną botów w celu powstrzymania ataków
Jeśli potrzebujesz dodatkowej warstwy zabezpieczeń przed aplikacją, użyj usługi SKU bramy aplikacji WAF_v2 dla możliwości usługi WAF. Zestaw SKU w wersji 2 można skonfigurować tak, aby zezwalała na dostęp tylko do aplikacji z danego kraju lub krajów. Reguła niestandardowa WAF jest konfigurowana w celu jawnego zezwalania lub blokowania ruchu na podstawie geolokalizacji. Aby uzyskać więcej informacji, zobacz [geofiltrowanie reguł niestandardowych](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) i [konfigurowanie reguł niestandardowych w bramie aplikacji WAF_v2 jednostce SKU za pomocą programu PowerShell](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

Włącz ochronę botów, aby zablokować znane złe boty. Powinno to zmniejszyć ilość ruchu dotarcie do aplikacji. Aby uzyskać więcej informacji, zobacz [ochrona botów z instrukcjami konfiguracji](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Włączanie diagnostyki w bramie aplikacji i w ustawie WAF

Dzienniki diagnostyczne umożliwiają wyświetlanie dzienników zapory, dzienników wydajności i dzienników dostępu. Za pomocą tych dzienników na platformie Azure można zarządzać bramami aplikacji i rozwiązywać problemy. Aby uzyskać więcej informacji, zapoznaj się z naszą [dokumentacją diagnostyczną](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Konfigurowanie zasad TLS dla dodatkowych zabezpieczeń
Upewnij się, że używasz najnowszej wersji zasad TLS ([AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)). Wymusza to TLS 1.2 i silniejsze szyfry. Aby uzyskać więcej informacji, zobacz [konfigurowanie wersji zasad TLS i zestawów szyfrowania za pośrednictwem programu PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell).
