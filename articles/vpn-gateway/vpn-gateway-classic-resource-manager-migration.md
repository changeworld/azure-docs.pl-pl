---
title: Klasycznego bramy sieci VPN do migracji Menedżera zasobów | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie sieci VPN bramy klasycznego do migracji Menedżera zasobów.
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
ms.date: 05/02/2017
ms.author: amsriva
ms.openlocfilehash: 1164fc24355657af22b6befaad74685ebbc2b5cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23885217"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Klasycznego bramy sieci VPN do migracji Menedżera zasobów
Teraz można migrować bramy sieci VPN z klasycznego modelu wdrażania Menedżera zasobów. Możesz przeczytać więcej na temat usługi Azure Resource Manager [funkcje i korzyści](../azure-resource-manager/resource-group-overview.md). W tym artykule firma Microsoft szczegółowo opisano sposób migracji w przypadku wdrożeń klasycznych do nowszej Menedżera zasobów na podstawie modelu. 

Bramy sieci VPN są migrowane w ramach sieci wirtualnej migracji ze środowiska klasycznego do Menedżera zasobów. Ta migracja zostanie zakończona jednej sieci wirtualnej w czasie. Nie jest wymagane dodatkowe narzędzia lub wymagania wstępne dotyczące migracji. Kroki migracji są identyczne z istniejących migracji sieci wirtualnej i są udokumentowane w [strona migracji zasobów IaaS](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Nie bez przestojów ścieżki danych podczas migracji a w związku z tym istniejących obciążeń będzie nadal działać bez utraty połączenia lokalnego podczas migracji. Publiczny adres IP skojarzone z bramą sieci VPN nie ulega zmianie podczas procesu migracji. Oznacza to, że nie należy ponownie skonfigurować router lokalny, po zakończeniu migracji.  

Model w programie Menedżer zasobów różni się od klasycznego modelu i składa się z bramy sieci wirtualnej, bramy sieci lokalnej i połączenia zasobów. Reprezentuje on bramy sieci VPN, reprezentujący przestrzeń adresów lokalnych i łączność między nimi odpowiednio lokacji lokalnej. Po ukończeniu migracji nie jest dostępny w modelu klasycznego bramy sieci, a wszystkie operacje zarządzania dla bramy sieci wirtualnej, bramy sieci lokalnej i połączenia obiektów odbywa się przy użyciu modelu Resource Manager.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Najbardziej typowych scenariuszy połączenia sieci VPN są objęte klasycznego do migracji Menedżera zasobów. Obsługiwane scenariusze-

* Polecenie łączność między lokacjami
* Łączność między lokacjami z bramą sieci VPN jest podłączony do lokalizacji lokalnego
* Sieć wirtualną do sieci wirtualnej łączności między dwiema sieciami wirtualnymi przy użyciu bramy sieci VPN
* Wiele sieci wirtualnych połączone takie same w lokalizacji lokalnej
* Połączenie z wieloma lokacjami
* Wymuszanie tunelowania włączone sieci wirtualnych

Dostępne są następujące scenariusze, które nie są obsługiwane —  

* Sieć wirtualna zarówno bramę usługi ExpressRoute, jak i bramy sieci VPN nie jest obecnie obsługiwane.
* Przesyłania scenariuszy, w której rozszerzeń maszyny Wirtualnej są podłączone do serwerów lokalnych. Ograniczenia połączenia sieci VPN przesyłania są szczegółowo opisane poniżej.

> [!NOTE]
> Sprawdzanie poprawności CIDR w modelu usługi Resource Manager jest ściślejsze niż w klasycznym modelu. Przed migracją zapewnienia zgodności zakresów adresów klasycznego podane z nieprawidłowym formacie CIDR przed rozpoczęciem migracji. CIDR mogą być sprawdzone za pomocą wszystkie typowe moduły CIDR. Sieć wirtualna lub lokalnych witryn na nieprawidłowe zakresy CIDR podczas migracji spowoduje awarię.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Sieci wirtualnej do migracji łączność sieci wirtualnej
Sieć wirtualną z łącznością sieć wirtualną w klasycznym został osiągane przez utworzenie reprezentację połączonych sieci wirtualnej w lokacji lokalnej. Klienci są wymagane do utworzenia dwie lokacje lokalne, które reprezentowane dwie sieci wirtualne, które potrzebne być połączone ze sobą. Zostały one dołączenie do odpowiedniej sieci wirtualnych przy użyciu tunelu IPsec do ustanowienia połączenia między dwiema sieciami wirtualnymi. Ten model ma wyzwania możliwości zarządzania, ponieważ zmiany zakresu adresów w jednej sieci wirtualnej również muszą zostać zachowane w odpowiedniej reprezentacji lokacji lokalnej. W modelu usługi Resource Manager to rozwiązanie nie jest już potrzebne. Połączenie między dwiema sieciami wirtualnymi można bezpośrednio uzyskać przy użyciu typu połączenia 'Vnet2Vnet' w zasobie połączenia. 

![Zrzut ekranu z sieci wirtualnej do migracji sieci wirtualnej.](./media/vpn-gateway-migration/migration1.png)

Podczas migracji sieci wirtualnej zostanie wykryte, że jednostki podłączonej do bramy sieci VPN bieżącej sieci wirtualnej jest innej sieci wirtualnej i upewnij się, że po zakończeniu migracji sieci zarówno wirtualnych, nie zobaczysz dwie lokacje lokalne reprezentujący innych sieci wirtualnej. Modelu klasycznego bramy sieci VPN, dwie lokacje lokalne i dwa połączenia między nimi jest przekształcana na modelu Resource Manager za pomocą dwóch bram sieci VPN i dwa połączenia typu Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Połączenie z siecią VPN przesyłania
Bramy sieci VPN w topologii można skonfigurować tak, aby łączność z lokalnymi dla sieci wirtualnej jest to osiągane przez łączenie z innej sieci wirtualnej podłączonej bezpośrednio do środowiska lokalnego. Jest przesyłania połączenie z siecią VPN, gdzie wystąpień w pierwszej sieci wirtualnej są połączone z lokalnymi zasobami za pośrednictwem przesyłania do bramy sieci VPN w połączonych sieci wirtualnej podłączonej bezpośrednio do środowiska lokalnego. Aby uzyskać tę konfigurację w klasycznym modelu wdrożenia, należy utworzyć lokalnej lokacji, który ma zagregowane prefiksy reprezentujący zarówno połączenia sieci wirtualnej i przestrzeń adresową lokalnymi. Ta representational lokacji lokalnej następnie jest podłączony do sieci wirtualnej do osiągnięcia przesyłania łączności. Klasycznego modelu ma również podobne wyzwania możliwości zarządzania, ponieważ każda zmiana zakresu adresów lokalnych również muszą zostać zachowane w lokalnej lokacji reprezentujący agregacji sieci wirtualnej i lokalnej. Wprowadzenie obsługi protokołu BGP w bram Resource Manager obsługiwane upraszcza możliwości zarządzania, ponieważ połączenia bramy można znaleźć trasy z lokalnie bez ręcznej modyfikacji prefiksy.

![Zrzut ekranu przedstawiający Scenariusz routingu tranzytowego.](./media/vpn-gateway-migration/migration2.png)

Ponieważ firma Microsoft przekształcić sieci wirtualnej z łącznością w sieci wirtualnej, bez konieczności lokalnych witryn, scenariusz przesyłania utraci łączność lokalnej sieci wirtualnej podłączonej pośrednio do lokalnego. Utrata połączenia można zminimalizować dwa sposoby, po zakończeniu migracji- 

* Należy włączyć protokół BGP dla bramy sieci VPN, które są połączone ze sobą i z lokalnym. Włączanie protokołu BGP przywraca łączności bez zmiany konfiguracji, ponieważ trasy są rozpoznawane i anonsowane między bramy sieci wirtualnej. Należy pamiętać, że BGP opcja jest dostępna tylko na standardowych i nowsze wersje produktu.
* Ustanowić połączenie jawne dotyczy sieci wirtualnej bramy sieci lokalnej, reprezentujący lokalizacji lokalnej. To również wymagają zmiany konfiguracji na routerze lokalnego, aby utworzyć i skonfigurować tunelu IPsec.

## <a name="next-steps"></a>Następne kroki
Po szkoleniowe dotyczące obsługi migracji bramy sieci VPN, przejdź do [obsługiwane platformy migracji zasobów IaaS ze środowiska klasycznego do Menedżera zasobów](../virtual-machines/windows/migration-classic-resource-manager-ps.md) rozpocząć pracę.

