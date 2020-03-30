---
title: Migracja bramy sieci VPN classic do menedżera zasobów | Dokumenty firmy Microsoft
description: Ta strona zawiera omówienie migracji klasycznej bramy sieci VPN do Menedżera zasobów.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2020
ms.author: amsriva
ms.openlocfilehash: c1a75630c6419816b048495ee87d24c81979af16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064771"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Migracja klasycznej bramy sieci VPN do Menedżera zasobów
Bramy sieci VPN można teraz migrować z klasycznego modelu wdrażania menedżera zasobów. Więcej informacji o [funkcjach i korzyściach](../azure-resource-manager/management/overview.md)usługi Azure Resource Manager . W tym artykule szczegółowo opisano sposób migracji z klasycznych wdrożeń do nowszego modelu opartego na Menedżerze zasobów. 

Bramy sieci VPN są migrowane w ramach migracji sieci wirtualnej z klasycznego do Menedżera zasobów. Ta migracja jest wykonywana jedna sieć wirtualna naraz. Nie ma żadnych dodatkowych wymagań w zakresie narzędzi lub wymagań wstępnych do migracji. Kroki migracji są identyczne z istniejącą migracją sieci wirtualnej i są udokumentowane na [stronie migracji zasobów IaaS](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Nie ma przestojów ścieżki danych podczas migracji, a zatem istniejące obciążenia będą nadal działać bez utraty łączności lokalnej podczas migracji. Publiczny adres IP skojarzony z bramą sieci VPN nie zmienia się podczas procesu migracji. Oznacza to, że po zakończeniu migracji nie trzeba będzie ponownie konfigurować routera lokalnego.  

Model w Menedżerze zasobów różni się od modelu klasycznego i składa się z bram sieci wirtualnej, bram sieci lokalnej i zasobów połączeń. Reprezentują one samą bramę sieci VPN, lokację lokalną reprezentującą lokalną przestrzeń adresową i łączność między nimi odpowiednio. Po zakończeniu migracji bramy nie będą dostępne w modelu klasycznym, a wszystkie operacje zarządzania na bramy sieci wirtualnej, bramy sieci lokalnej i obiekty połączeń muszą być wykonywane przy użyciu modelu Menedżera zasobów.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Większość typowych scenariuszy łączności sieci VPN są objęte migracją klasyczną do Menedżera zasobów. Obsługiwane scenariusze obejmują :

* Łączność punktowa z lokacją
* Łączność lokacja-lokacja z bramą sieci VPN połączoną z lokalizacją lokalną
* Łączność sieci wirtualnej z siecią wirtualną między dwiema sieciami wirtualnymi przy użyciu bram sieci VPN
* Wiele sieci wirtualnych połączonych z tymi samymi w lokalizacji lokalnej
* Łączność wielomiejscowa
* Wymuszone tunelowanie włączone sieci wirtualne

Scenariusze, które nie są obsługiwane, obejmują  

* Sieć wirtualna z bramą usługi ExpressRoute i bramą sieci VPN nie jest obecnie obsługiwana.
* Scenariusze przesyłania tranzytowego, w których rozszerzenia maszyn wirtualnych są połączone z serwerami lokalnymi. Ograniczenia łączności sieci VPN w tranzycie są opisane poniżej.

> [!NOTE]
> Sprawdzanie poprawności CIDR w modelu Menedżera zasobów jest bardziej rygorystyczne niż w modelu klasycznym. Przed migracją upewnij się, że klasyczne zakresy adresów podane są zgodne z prawidłowym formatem CIDR przed rozpoczęciem migracji. Cidr można sprawdzić za pomocą dowolnych wspólnych modułów sprawdzania poprawności CIDR. Sieć wirtualna lub lokacje lokalne z nieprawidłowymi zakresami CIDR podczas migracji spowodowałoby niepowodzenie stanu.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Migracja łączności sieci wirtualnej do sieci wirtualnej
Łączność sieci wirtualnej do sieci wirtualnej w wersji klasycznej została osiągnięta przez utworzenie lokalnej reprezentacji lokacji połączonej sieci wirtualnej. Klienci byli zobowiązani do utworzenia dwóch lokalnych lokacji, które reprezentowały dwie sieci wirtualne, które musiały być połączone ze sobą. Następnie zostały one połączone z odpowiednimi sieciami wirtualnymi przy użyciu tunelu IPsec w celu ustanowienia łączności między dwiema sieciami wirtualnymi. Ten model ma wyzwania związane z zarządzaniem, ponieważ wszelkie zmiany zakresu adresów w jednej sieci wirtualnej muszą być również obsługiwane w odpowiedniej reprezentacji lokacji lokalnej. W modelu Menedżera zasobów to obejście nie jest już potrzebne. Połączenie między dwiema sieciami wirtualnymi można osiągnąć bezpośrednio przy użyciu typu połączenia "Vnet2Vnet" w zasobie połączenia. 

![Zrzut ekranu przedstawiający migrację sieci wirtualnej do sieci wirtualnej.](./media/vpn-gateway-migration/migration1.png)

Podczas migracji sieci wirtualnej wykrywamy, że połączona jednostka z bramą sieci VPN bieżącej sieci wirtualnej jest inną siecią wirtualną i zapewniamy, że po zakończeniu migracji obu sieci wirtualnych nie będą już widoczne dwie lokacje lokalne reprezentujące drugą sieć wirtualną. Klasyczny model dwóch bram sieci VPN, dwóch lokacji lokalnych i dwóch połączeń między nimi jest przekształcany w model Menedżera zasobów z dwiema bramami sieci VPN i dwoma połączeniami typu Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Łączność tranzytowa VPN
Bramy sieci VPN można skonfigurować w topologii, tak aby łączność lokalna dla sieci wirtualnej została osiągnięta przez połączenie z inną siecią wirtualną, która jest bezpośrednio połączona z siecią lokalną. Jest to tranzytowa łączność sieci VPN, w której wystąpienia w pierwszej sieci wirtualnej są połączone z zasobami lokalnymi za pośrednictwem przesyłania do bramy sieci VPN w połączonej sieci wirtualnej, która jest bezpośrednio połączona z lokalnymi. Aby osiągnąć tę konfigurację w klasycznym modelu wdrażania, należy utworzyć lokację lokalną, która ma zagregowane prefiksy reprezentujące zarówno podłączoną sieć wirtualną, jak i lokalną przestrzeń adresową. Ta reprezentacyjna lokacja lokalna jest następnie podłączona do sieci wirtualnej w celu uzyskania łączności tranzytowej. Ten klasyczny model ma również podobne wyzwania związane z zarządzaniem, ponieważ wszelkie zmiany w zakresie adresów lokalnych muszą być również obsługiwane w lokacji lokalnej reprezentującej agregację sieci wirtualnej i lokalną. Wprowadzenie obsługi protokołu BGP w obsługiwanych przez Menedżera zasobów bram upraszcza zarządzanie, ponieważ połączone bramy mogą uczyć się tras z lokalnego bez ręcznej modyfikacji prefiksów.

![Zrzut ekranu przedstawiający scenariusz routingu tranzytowego.](./media/vpn-gateway-migration/migration2.png)

Ponieważ przekształcamy łączność sieci wirtualnej na sieć wirtualną bez konieczności tworzenia lokacji lokalnych, scenariusz przesyłania traci łączność lokalną dla sieci wirtualnej, która jest pośrednio połączona z siecią lokalną. Utratę łączności można złagodzić na dwa sposoby, po zakończeniu migracji - 

* Włącz usługę BGP w bramkach sieci VPN połączonych ze sobą oraz w środowisku lokalnym. Włączenie protokołu BGP przywraca łączność bez żadnych innych zmian konfiguracji, ponieważ trasy są poznawanie i anonsowane między bramami sieci wirtualnej. Należy zauważyć, że opcja BGP jest dostępna tylko w standardowych i wyższych jednostkach SKU.
* Ustanawianie jawnego połączenia z sieci wirtualnej, którego dotyczy problem, z bramą sieci lokalnej reprezentującą lokalizację lokalną. Wymagałoby to również zmiany konfiguracji na routerze lokalnym w celu utworzenia i skonfigurowania tunelu IPsec.

## <a name="next-steps"></a>Następne kroki
Po zapoznaniu się z pomocą techniczną dotyczącą migracji bramy sieci VPN przejdź do [obsługiwanej przez platformę migracji zasobów IaaS z klasycznego do Menedżera zasobów,](../virtual-machines/windows/migration-classic-resource-manager-ps.md) aby rozpocząć.

