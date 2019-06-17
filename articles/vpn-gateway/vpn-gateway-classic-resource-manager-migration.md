---
title: Klasyczne bramy sieci VPN do migracji usługi Resource Manager | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie sieci VPN bramy klasycznego do migracji usługi Resource Manager.
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
ms.openlocfilehash: b65b47389611bcc0e5acb3c7ebff672f72a87581
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60761588"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Brama sieci VPN migracja z klasycznego do usługi Resource Manager
Teraz można migrować bram sieci VPN z klasycznego modelu wdrażania usługi Resource Manager. Możesz dowiedzieć się więcej o usłudze Azure Resource Manager [funkcjach i korzyściach](../azure-resource-manager/resource-group-overview.md). W tym artykule będziemy szczegółowo opisują jak przeprowadzić migrację z wdrożeń klasycznych do nowszych modelu usługi Resource Manager na podstawie. 

Bramy sieci VPN są migrowane w ramach migracji sieci wirtualnej z wersji klasycznej do usługi Resource Manager. Ta migracja odbywa się w czasie jednej sieci wirtualnej. Nie jest wymagane dodatkowe narzędzia lub wymagania wstępne dotyczące migracji. Kroki migracji są takie same, jak istniejące migracji sieci wirtualnej i są udokumentowane na [stronie migracji zasobów IaaS](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Brak przestojów ścieżki danych podczas migracji, a zatem istniejące obciążenia będą nadal działać bez utraty łączności lokalnej podczas migracji. Publiczny adres IP skojarzony z bramą sieci VPN nie zmienia się podczas procesu migracji. Oznacza to, że nie musisz ponownie skonfigurować router w środowisku lokalnym, po zakończeniu migracji.  

Modelu w usłudze Resource Manager różni się od modelu klasycznego i składa się z bramy sieci wirtualnej i bramy sieci lokalnej i zasoby połączenia. Reprezentują one, bramy sieci VPN witryny lokalnej reprezentujących na przestrzeń adresów lokalnych i łączność między tymi dwoma odpowiednio. Po ukończeniu migracji bram nie będą dostępne w modelu klasycznym, a wszystkie operacje zarządzania na obiekty połączenia, bramy sieci lokalnej i bramy sieci wirtualnej muszą być wykonywane przy użyciu modelu usługi Resource Manager.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Najbardziej typowe scenariusze łączności sieci VPN są objęte z modelu klasycznego do migracji usługi Resource Manager. Obsługiwane scenariusze obejmują-

* Polecenie łączność między lokacjami
* Łączność między lokacjami z bramą sieci VPN jest podłączony do lokalizacji lokalnej
* Łączność sieci wirtualnej między dwiema sieciami wirtualnymi przy użyciu bram sieci VPN typu sieć
* Wiele sieci wirtualnych podłączone do tej samej lokalizacji lokalnej
* Połączenia obejmujące wiele lokacji
* Sieci wirtualne włączyć tunelowania wymuszonego

Dostępne są następujące scenariusze, które nie są obsługiwane-  

* Siecią wirtualną przy użyciu zarówno bramę usługi ExpressRoute, jak i bramy sieci VPN nie jest obecnie obsługiwane.
* Scenariusze przesyłania, gdzie rozszerzenia maszyny Wirtualnej są podłączone do serwerów lokalnych. Ograniczeń połączeń sieci VPN przesyłane są szczegółowo opisane poniżej.

> [!NOTE]
> Weryfikacja CIDR w modelu usługi Resource Manager jest bardziej rygorystyczne niż w modelu klasycznym. Przed migracją zapewniać zakresów adresów klasyczne, biorąc pod uwagę zgodność z prawidłowy format CIDR przed rozpoczęciem migracji. CIDR mogą być sprawdzone za pomocą dowolnego wspólnego moduły weryfikacji CIDR. Sieć wirtualna lub lokacji lokalnych z nieprawidłową zakresy CIDR, podczas migracji spowoduje w stanie niepowodzenia.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Sieć wirtualna migracji łączność w sieci wirtualnej
Połączenie między sieciami w modelu klasycznym wirtualnymi osiągnięto, tworząc reprezentację połączonych sieci wirtualnej w lokacji lokalnej. Klienci musieli utworzyć dwie lokacje lokalne, które są reprezentowane z dwiema sieciami wirtualnymi, które są potrzebne do ze sobą połączone. Następnie zostały one połączone do odpowiednich sieci wirtualnych przy użyciu tunelu IPsec do ustanowienia połączenia między dwiema sieciami wirtualnymi. Ten model ma trudności możliwości zarządzania, ponieważ zmiany zakresu adresów w jednej sieci wirtualnej, również muszą być utrzymywane w reprezentacji odpowiednich lokacji lokalnej. W modelu usługi Resource Manager to rozwiązanie nie jest już potrzebny. Połączenie między dwiema sieciami wirtualnymi można bezpośrednio uzyskać przy użyciu typu połączenia "Vnet2Vnet" w zasobach połączenia. 

![Zrzut ekranu z sieci wirtualnej do migracji sieci wirtualnej.](./media/vpn-gateway-migration/migration1.png)

Podczas migracji sieci wirtualnej zostanie wykryte, że jednostki podłączone do bramy sieci VPN bieżącej sieci wirtualnej jest innej sieci wirtualnej i upewnij się, że po zakończeniu migracji w obu sieciach wirtualnych, nie będzie widział dwie lokacje lokalne reprezentujący do innej sieci wirtualnej. Opartych na modelu klasycznym dwóch bram sieci VPN, dwie lokacje lokalne i dwa połączenia między nimi jest przekształcana w modelu usługi Resource Manager przy użyciu dwóch bram sieci VPN i dwóch połączeń typu Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Połączenie z siecią VPN przesyłania
Taki sposób, że łączność lokalna dla sieci wirtualnej odbywa się przez połączenie z inną siecią wirtualną, podłączona bezpośrednio do serwera lokalnego, można skonfigurować bramy sieci VPN w topologii. Jest przesyłane połączenia sieci VPN, gdzie wystąpień w pierwszej sieci wirtualnej są podłączone do zasobów lokalnych za pośrednictwem przesyłania do bramy sieci VPN w sieci wirtualnej połączonej, podłączona bezpośrednio do serwera lokalnego. Aby uzyskać tę konfigurację w klasycznym modelu wdrażania, będziesz potrzebować do utworzenia lokacji lokalnej, która zawiera zagregowane prefiksy reprezentujących sieci połączonych i przestrzeń adresowa w środowisku lokalnym. Ta lokacja lokalna representational następnie jest podłączony do sieci wirtualnej w celu osiągnięcia przesyłania łączności. Ten model klasyczny również ma podobne trudności możliwości zarządzania, ponieważ każda zmiana w zakresie adresów lokalnych, również muszą być utrzymywane w lokacji lokalnej reprezentujących agregacji w sieci wirtualnej i lokalnej. Wprowadzenie obsługi protokołu BGP w bramach usługi Resource Manager obsługiwane upraszcza możliwości zarządzania, ponieważ połączone bramy można znaleźć tras ze ścieżki lokalnie bez ręcznej modyfikacji prefiksy.

![Zrzut ekranu przedstawiający Scenariusz routingu tranzytowego.](./media/vpn-gateway-migration/migration2.png)

Ponieważ firma Microsoft przekształcić sieci wirtualnej połączenie między sieciami wirtualnymi bez konieczności lokalnych witryn scenariusz przesyłania utraci łączność lokalnej sieci wirtualnej, która pośrednio jest podłączony do sieci lokalnej. Utrata łączności można zminimalizować w dwóch poniższych sposobów po zakończeniu migracji- 

* Należy włączyć protokół BGP dla bram sieci VPN, które są połączone ze sobą oraz do serwera lokalnego. Włączanie protokołu BGP spowoduje przywrócenie łączności bez zmiany konfiguracji, ponieważ trasy są rozpoznane i anonsowane między bramami sieci wirtualnej. Należy pamiętać, że protokołu BGP opcja jest dostępna tylko w przypadku wersji SKU Standard i wyższych.
* Ustanowić jawne połączenie z sieci wirtualnej objęte bramy sieci lokalnej reprezentujących lokalizacji lokalnej. To także wymaga zmiany konfiguracji na router lokalny, aby utworzyć i skonfigurować tunel protokołu IPsec.

## <a name="next-steps"></a>Kolejne kroki
Po zapoznaniu się Obsługa migracji bramy sieci VPN, przejdź do [obsługiwanej przez platformę migracji zasobów rozwiązania IaaS z wersji klasycznej do usługi Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-ps.md) na rozpoczęcie pracy.

