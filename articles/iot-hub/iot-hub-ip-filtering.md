---
title: Filtry połączenia IP Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Jak używać IP filtrowania bloku połączeń z określonych adresów IP do Centrum Azure IoT. Możesz zablokować połączenia z poszczególnych lub zakresów adresów IP.
services: iot-hub
documentationcenter: ''
author: BeatriceOltean
manager: timlt
editor: ''
ms.assetid: f833eac3-5b5f-46a7-a47b-f4f6fc927f3f
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: boltean
ms.openlocfilehash: 0229b8b16de1fa04d953c90895b29e4fbad60ec3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="use-ip-filters"></a>Używanie filtrów IP

Zabezpieczenia są istotnym elementem każde rozwiązanie IoT oparte na usłudze Azure IoT Hub. Czasami trzeba jawnie określić adresy IP, z których mogą łączyć się urządzenia jako część konfiguracji zabezpieczeń. _Filtrów IP_ funkcja umożliwia konfigurowanie reguł odrzucenia lub akceptować ruch z określonych adresów IPv4.

## <a name="when-to-use"></a>Kiedy stosować

Istnieją dwa określonych przypadków użycia gdy będzie blokować punkty końcowe Centrum IoT dla określonych adresów IP:

- Centrum IoT powinny odbierać dane tylko z określonego zakresu adresów IP i odrzucić wszystkie inne elementy. Na przykład w przypadku korzystania z Centrum IoT [Azure Express Route] do utworzenia prywatnej połączeń między centrum IoT i infrastruktury lokalnej.
- Należy odrzucić ruch sieciowy z adresów IP, które zostały zidentyfikowane jako podejrzane przez administratora Centrum IoT.

## <a name="how-filter-rules-are-applied"></a>Sposób stosowania reguły filtrowania

Reguły filtru IP są stosowane na poziomie usługi IoT Hub. W związku z tym reguł filtru IP stosowane do wszystkich połączeń z urządzeniami i aplikacjami zaplecza za pomocą obsługiwanych protokołów.

Każda próba połączenia z adresu IP odpowiadającego wydzielenia reguły IP w Centrum IoT odbiera kod stanu 401 nieautoryzowane i opis. Komunikat odpowiedzi nie mogą zawierać reguły IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie **filtrów IP** siatki w portalu dla Centrum IoT jest pusta. To ustawienie domyślne oznacza, że Centrum akceptuje połączenia dowolnego adresu IP. To domyślne ustawienie jest odpowiednikiem regułę, która akceptuje 0.0.0.0/0 zakres adresów IP.

![Filtr IP domyślnych Centrum IoT][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>Dodawanie lub edytowanie reguły filtru adresu IP

Po dodaniu reguły filtru adresu IP, zostanie wyświetlony monit o następujących wartości:

- **Nazwę reguły filtru IP** który musi być unikatowy, bez uwzględniania wielkości liter, alfanumeryczne ciągu maksymalnie 128 znaków. Tylko ASCII 7-bitowe znaki alfanumeryczne oraz `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` są akceptowane.
- Wybierz **Odrzuć** lub **zaakceptować** jako **akcji** reguły filtru adresu IP.
- Podaj pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w CIDR 192.168.100.0/22 notacji reprezentuje 1024 adresy IPv4 z 192.168.100.0 192.168.103.255.

![Dodaj regułę filtru IP do Centrum IoT][img-ip-filter-add-rule]

Po zapisaniu reguła zostanie wyświetlony alert informujący, że aktualizacja jest w toku.

![Powiadomienie o zapisywaniu regułę filtru IP][img-ip-filter-save-new-rule]

**Dodaj** opcja jest wyłączona po przejściu maksymalnie 10 reguł filtru IP.

Można edytować istniejącą regułę, klikając wiersz, który zawiera reguły.

> [!NOTE]
> Odrzucanie IP adresy można zapobiec innymi usługami Azure (np. Azure Stream Analytics, maszynach wirtualnych platformy Azure lub Explorer urządzenia w portalu) interakcję z Centrum IoT.

> [!WARNING]
> Jeśli używasz usługi Azure Stream Analytics (ASA) odczytać wiadomości z Centrum IoT z filtrowania IP włączone, użyj nazwy zgodnego Centrum zdarzeń i punktu końcowego Centrum IoT w parametrach połączenia ASA.

## <a name="delete-an-ip-filter-rule"></a>Usuń regułę filtru IP

Aby usunąć regułę filtru IP, wybierz co najmniej jedną regułę siatki i kliknij przycisk **usunąć**.

![Usuń regułę filtru IP Centrum IoT][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>Szacowanie reguły filtru adresu IP

Reguły filtru IP są stosowane w kolejności, pierwszej reguły, który odpowiada adresowi IP określa akcji Zaakceptuj lub Odrzuć.

Na przykład jeśli chcesz zaakceptować adresów w zakresie 192.168.100.0/22 i odrzucić wszystkie inne pierwszą regułę w siatce powinna obsługiwać 192.168.100.0/22 zakresu adresów. Reguły następnej Odrzuć wszystkie adresy przy użyciu 0.0.0.0/0 zakresu.

Można zmienić kolejność reguł filtru IP w siatce klikając Wielokropek pionowy na początku wiersza i przy użyciu przeciągania i upuszczania.

Aby zapisać nową kolejność reguły filtru adresu IP, kliknij przycisk **zapisać**.

![Zmień kolejność reguł filtru IP Centrum IoT][img-ip-filter-rule-order]

## <a name="next-steps"></a>Kolejne kroki

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

- [Operacje monitorowania][lnk-monitor]
- [Metryki Centrum IoT][lnk-metrics]

<!-- Images -->
[img-ip-filter-default]: ./media/iot-hub-ip-filtering/ip-filter-default.png
[img-ip-filter-add-rule]: ./media/iot-hub-ip-filtering/ip-filter-add-rule.png
[img-ip-filter-save-new-rule]: ./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png
[img-ip-filter-delete-rule]: ./media/iot-hub-ip-filtering/ip-filter-delete-rule.png
[img-ip-filter-rule-order]: ./media/iot-hub-ip-filtering/ip-filter-rule-order.png


<!-- Links -->

[IoT Hub developer guide]: iot-hub-devguide.md
[Azure Express Route]:  https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-metrics]: iot-hub-metrics.md
