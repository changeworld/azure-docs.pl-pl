---
title: Azure IoT Hub IP połączenia filtrów | Dokumentacja firmy Microsoft
description: Jak używać adresu IP filtrowania w celu połączenia bloków z określonych adresów IP do usługi Azure IoT hub. Możesz zablokować możliwość połączenia z indywidualnych lub zakresów adresów IP.
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: rezas
ms.openlocfilehash: 864af9cae35912d95f2c0bf0b574a5ca2404a608
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190645"
---
# <a name="use-ip-filters"></a>Użyj filtrów IP

Zabezpieczenia są istotnym elementem każde rozwiązanie IoT, w oparciu o usługi Azure IoT Hub. Czasami Musisz jawnie określić adresy IP, z których można połączyć urządzenia jako część konfiguracji zabezpieczeń. _Filtru IP_ funkcja umożliwia skonfigurowanie reguł dla odrzuca lub akceptowania ruchu z określonych adresów IPv4.

## <a name="when-to-use"></a>Kiedy stosować

Istnieją dwa szczególne przypadki użycia, gdy jest ona przydatne blokowanie punktów końcowych usługi IoT Hub dla określonych adresów IP:

- Centrum IoT hub powinny odbierać ruch tylko z określonego zakresu adresów IP i odrzucić wszystkie inne elementy. Na przykład używasz usługi IoT hub przy użyciu [Usługa Azure Expressroute] na tworzenie prywatnych połączeń między centrum IoT hub i infrastrukturą lokalną.
- Należy odrzucić ruch z adresów IP, które zostały zidentyfikowane jako podejrzane przez administratora usługi IoT hub.

## <a name="how-filter-rules-are-applied"></a>Sposób stosowania reguły filtrowania

Reguły filtrowania adresów IP są stosowane na poziomie usługi IoT Hub. W związku z tym reguł filtrowania adresów IP stosowane do wszystkich połączeń z urządzeniami i aplikacjami zaplecza za pomocą dowolnego obsługiwanego protokołu.

Każda próba połączenia z adresu IP, który jest zgodny z wydzielenia regułą adresów IP w usłudze IoT hub odbiera kod stanu 401 nieautoryzowane i opis. Komunikat odpowiedzi nie mogą zawierać reguły adresów IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie **filtru IP** siatki w portalu dla usługi IoT hub jest pusty. To ustawienie domyślne oznacza, że Centrum akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest odpowiednikiem regułę, która akceptuje zakres adresów IP 0.0.0.0/0.

![Ustawienia filtru IP domyślnej usługi IoT Hub][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>Dodawanie lub edytowanie reguły filtrowania adresów IP

Po dodaniu regułę filtrowania adresów IP, zostanie wyświetlony monit o następujących wartości:

- **Nazwa reguły filtrowania adresów IP** który musi być unikatowy, bez uwzględniania wielkości liter, alfanumerycznego ciągu maksymalnie 128 znaków. Tylko znaki ASCII 7-bitowe znaki alfanumeryczne oraz `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` są akceptowane.
- Wybierz **Odrzuć** lub **zaakceptować** jako **akcji** reguły filtrowania adresów IP.
- Podaj pojedynczy adres IPv4 lub bloku adresów IP w notacji CIDR. Na przykład w CIDR 192.168.100.0/22 notacji reprezentuje 1024 adresów IPv4 od 192.168.100.0 do 192.168.103.255.

![Dodawanie reguły filtrowania adresów IP do usługi IoT hub][img-ip-filter-add-rule]

Po zapisaniu reguły zobaczysz alert informujący, że aktualizacja jest w toku.

![Powiadomienie dotyczące zapisywania regułę filtrowania adresów IP][img-ip-filter-save-new-rule]

**Dodaj** opcja jest wyłączona po przejściu do maksymalnie 10 reguł filtrowania adresów IP.

Możesz edytować istniejącą regułę, klikając dwukrotnie wiersz, który zawiera daną zasadę.

> [!NOTE]
> Odrzuca IP adresów można zapobiec innych usług platformy Azure (np. usługi Azure Stream Analytics, Azure Virtual Machines lub Device Explorer w portalu) interakcji z usługą IoT hub.

> [!WARNING]
> Jeśli używasz usługi Azure Stream Analytics (ASA) odczytywanie komunikatów z usługi IoT hub za pomocą filtrowania adresów IP włączone, użyj nazwy zgodnego z Centrum zdarzeń i punkt końcowy usługi IoT Hub w parametrach połączenia ASA.

## <a name="delete-an-ip-filter-rule"></a>Usuń regułę filtrowania adresów IP

Aby usunąć regułę filtrowania adresów IP, wybierz co najmniej jedną regułę w siatce i kliknij przycisk **Usuń**.

![Usuń regułę filtrowania adresów IP Centrum IoT][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>Ocenę reguł filtrowania adresów IP

Reguły filtrowania adresów IP są stosowane w kolejności i pierwszej reguły, który jest zgodny z adresem IP określa akcję Zaakceptuj lub Odrzuć.

Na przykład jeśli chcesz zaakceptować adresów w 192.168.100.0/22 zakresu i odrzucić wszystkie inne elementy, pierwszą regułę w siatce powinna obsługiwać 192.168.100.0/22 zakresu adresów. Następną regułę należy odrzucić wszystkie adresy, używając 0.0.0.0/0 zakresu.

Można zmienić kolejność reguł filtrowania adresów IP w siatce, klikając Wielokropek pionowy na początku wiersza i za pomocą przeciągania i upuszczania.

Aby zapisać nowy kolejność reguł filtrowania adresów IP, kliknij przycisk **Zapisz**.

![Zmień kolejność reguł filtrowania adresów IP Centrum IoT][img-ip-filter-rule-order]

## <a name="next-steps"></a>Kolejne kroki

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

- [Monitorowanie operacji][lnk-monitor]
- [Metryki usługi IoT Hub][lnk-metrics]

<!-- Images -->
[img-ip-filter-default]: ./media/iot-hub-ip-filtering/ip-filter-default.png
[img-ip-filter-add-rule]: ./media/iot-hub-ip-filtering/ip-filter-add-rule.png
[img-ip-filter-save-new-rule]: ./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png
[img-ip-filter-delete-rule]: ./media/iot-hub-ip-filtering/ip-filter-delete-rule.png
[img-ip-filter-rule-order]: ./media/iot-hub-ip-filtering/ip-filter-rule-order.png


<!-- Links -->

[IoT Hub developer guide]: iot-hub-devguide.md
[Usługa Azure Expressroute]:  https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-metrics]: iot-hub-metrics.md
