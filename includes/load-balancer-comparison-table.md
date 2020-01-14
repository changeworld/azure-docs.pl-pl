---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 4b419bd8c19cffaf84582cb4fa9e46592746da3f
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752109"
---
| | Standardowy SKU | Podstawowy SKU |
| --- | --- | --- |
| Rozmiar puli zaplecza | Obsługuje do 1000 wystąpień. | Obsługuje do 100 wystąpień. |
| Punkty końcowe puli zaplecza | Dowolna maszyna wirtualna w jednej sieci wirtualnej, w tym mieszanka maszyn wirtualnych, zestawów dostępności i zestawów skalowania maszyn wirtualnych. | Maszyny wirtualne w jednym zestawie dostępności lub zestaw skalowania maszyn wirtualnych. |
| [Sondy kondycji](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Zachowanie sondy kondycji podczas niepowodzenia](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | Połączenia TCP pozostają aktywne na sondie wystąpienia __, a__ wszystkie sondy w dół. | Połączenia TCP pozostają aktywne na sondie wystąpienia. Wszystkie połączenia TCP kończą się, gdy wszystkie sondy są wyłączone. |
| Strefy dostępności | Nadmiarowe strefy i frontony dla ruchu przychodzącego i wychodzącego. Niepowodzenie podczas mapowania przepływów wychodzących. Równoważenie obciążenia między strefami. | Niedostępne |
| Diagnostyka | Azure Monitor. Metryki wielowymiarowe, w tym liczniki bajtów i pakietów. Stan sondy kondycji. Próby połączenia (TCP SYN). Kondycja połączenia wychodzącego (przepływy (pomyślne i zakończone niepowodzeniem). Pomiary aktywnej płaszczyzny danych. | Log Analytics platformy Azure tylko dla Load Balancer publicznych. Alert wyczerpania. Liczba kondycji puli zaplecza. |
| Porty wysokiej dostępności | Wewnętrzny moduł równoważenia obciążenia | Niedostępne |
| Zabezpieczenie domyślne | Publiczny adres IP, publiczne punkty końcowe Load Balancer i wewnętrzne punkty końcowe Load Balancer są zamykane do przepływów przychodzących, chyba że jest to dozwolone przez grupę zabezpieczeń sieci. Należy pamiętać, że ruch wewnętrzny z sieci wirtualnej do wewnętrznego modułu równoważenia obciążenia nadal jest dozwolony. | Otwórz domyślnie. Opcjonalna Grupa zabezpieczeń sieci. |
| [Połączenia wychodzące](../articles/load-balancer/load-balancer-outbound-connections.md) | Można jawnie zdefiniować oparty na puli translator adresów sieciowych ruchu wychodzącego za pomocą [reguł ruchu wychodzącego](../articles/load-balancer/load-balancer-outbound-rules-overview.md). Można użyć wielu frontonów z każdą regułą równoważenia obciążenia. Scenariusz wychodzący _należy_ jawnie utworzyć dla maszyny wirtualnej, zestawu dostępności lub zestawu skalowania maszyn wirtualnych, aby można było używać łączności wychodzącej. Punkty końcowe usługi sieci wirtualnej można uzyskać bez definiowania łączności wychodzącej i nie są one wliczane do przetworzonych danych. Wszystkie publiczne adresy IP, w tym usługi Azure PaaS Services niedostępne jako punkty końcowe usługi sieci wirtualnej, muszą zostać osiągnięte przy użyciu łączności wychodzącej i liczby do przetworzonych danych. Gdy tylko wewnętrzna Load Balancer obsługuje maszynę wirtualną, zestaw dostępności lub zestaw skalowania maszyn wirtualnych, połączenia wychodzące za pośrednictwem domyślnego protokołu nie są dostępne. Zamiast tego użyj [reguł ruchu wychodzącego](../articles/load-balancer/load-balancer-outbound-rules-overview.md) . Programowanie wychodzącego elementu reportowego jest zależne od protokołu transportu w przychodzącej regule równoważenia obciążenia. | Pojedynczy fronton, wybierany losowo, gdy występuje wiele frontonów. Gdy tylko wewnętrzna Load Balancer obsługuje maszynę wirtualną, zestaw dostępności lub zestaw skalowania maszyn wirtualnych, używany jest domyślny przydział adresów sieciowych. |
| [Reguły ruchu wychodzącego](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Deklaratywna konfiguracja wychodzącego NAT, przy użyciu publicznych adresów IP lub prefiksów publicznych adresów IP. Konfigurowalny limit czasu bezczynności (4-120 minut). Alokacja portu niestandardowego obiektu podrzędnego | Niedostępne |
| [Resetowanie protokołu TCP w wyniku bezczynności](../articles/load-balancer/load-balancer-tcp-reset.md) | Włączanie resetowania protokołu TCP (TCP RST) w wyniku upływu limitu czasu bezczynności dla dowolnej reguły | Niedostępne |
| [Wiele frontonów](../articles/load-balancer/load-balancer-multivip-overview.md) | Ruch przychodzący i [wychodzący](../articles/load-balancer/load-balancer-outbound-connections.md) | Tylko transfer przychodzący |
| Operacje zarządzania | Większość operacji < 30 sekund | typowe 60 – 90 sekund |
| Umowa SLA | 99,99% dla ścieżki danych z dwoma maszynami wirtualnymi w dobrej kondycji. | Nie dotyczy | 
| Cennik | Rozliczane na podstawie liczby reguł, przetworzonych dane przychodzących i wychodzących skojarzonych z zasobem. | Brak opłaty |
|  |  |  |
