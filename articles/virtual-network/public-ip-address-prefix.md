---
title: Prefiks publicznego adresu IP platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, czym jest prefiks publicznego adresu IP platformy Azure i jak może pomóc w przypisywaniu przewidywalnych publicznych adresów IP do zasobów.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: 12fb7e03062600745cd8511d37b439ce44f2ef78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640724"
---
# <a name="public-ip-address-prefix"></a>Prefiks publicznego adresu IP

Prefiks publicznego adresu IP to zarezerwowany zakres adresów IP dla publicznych punktów końcowych na platformie Azure. Platforma Azure przydziela ciągły zakres adresów do subskrypcji na podstawie liczby określonych adresów. Jeśli nie znasz adresów publicznych, zobacz [Publiczne adresy IP.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Publiczne adresy IP są przypisywane z puli adresów w każdym regionie platformy Azure. Możesz [pobrać](https://www.microsoft.com/download/details.aspx?id=56519) listę zakresów używanych przez platformę Azure dla każdego regionu. Na przykład 40.121.0.0/16 jest jednym z ponad 100 zakresów używa platformy Azure w regionie wschodnich stanów USA. Zakres obejmuje adresy użytkowe 40.121.0.1 - 40.121.255.254.

Prefiks publicznego adresu IP można utworzyć w regionie platformy Azure i subskrypcji, określając nazwę i liczbę adresów, które mają zawierać prefiks. Na przykład Jeśli utworzysz prefiks publicznego adresu IP /28, platforma Azure przydziela 16 adresów z jednego z jego zakresów dla Ciebie. Nie wiesz, który zakres platforma Azure zostanie przypisana, dopóki nie utworzysz zakresu, ale adresy są ciągłe. Prefiksy publicznego adresu IP mają opłatę. Aby uzyskać szczegółowe informacje, zobacz [ceny publicznych adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Dlaczego warto utworzyć prefiks publicznego adresu IP?

Podczas tworzenia publicznych zasobów adresów IP platforma Azure przypisuje dostępny publiczny adres IP z dowolnego zakresu używanego w regionie. Gdy platforma Azure przypisuje adres, wiesz, jaki jest adres, ale dopóki platforma Azure nie przypisze adresu, nie wiesz, jaki adres może zostać przypisany. Może to być problematyczne, gdy na przykład ty lub twoi partnerzy biznesowi konfiguruje reguły zapory, które zezwalają na określone adresy IP. Za każdym razem, gdy nowy publiczny adres IP do zasobu zostanie przypisany, adres ten musi zostać dodany do reguły zapory. Podczas przypisywania adresów do zasobów z prefiksu publicznego adresu IP reguły zapory nie muszą być aktualizowane za każdym razem, gdy przypisujesz jeden z adresów, ponieważ cały zakres może zostać dodany do reguły.

## <a name="benefits"></a>Korzyści

- Publiczne zasoby adresów IP można tworzyć z znanego zakresu.
- Ty lub Twoi partnerzy biznesowi możecie tworzyć reguły zapory z zakresami zawierającymi obecnie przypisane publiczne adresy IP, a także adresy, które nie zostały jeszcze przypisane. Eliminuje to konieczność zmiany reguł zapory podczas przypisywania adresów IP do nowych zasobów.
- Domyślny rozmiar zakresu, który można utworzyć, to /28 lub 16 adresów IP.
- Nie ma żadnych ograniczeń co do liczby zakresów, które można utworzyć, jednak istnieją ograniczenia dotyczące maksymalnej liczby statycznych publicznych adresów IP, które można mieć w subskrypcji platformy Azure. W rezultacie liczba utworzonych zakresów nie może obejmować więcej statycznych publicznych adresów IP niż w ramach subskrypcji. Aby uzyskać więcej informacji, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Adresy utworzone przy użyciu adresów z prefiksu można przypisać do dowolnego zasobu platformy Azure, do którego można przypisać publiczny adres IP.
- Można łatwo zobaczyć, które adresy IP, które są przydzielane i nie są jeszcze przydzielone w zakresie.

## <a name="scenarios"></a>Scenariusze
Następujące zasoby można skojarzyć ze statycznym publicznym adresem IP z prefiksu:

|Zasób|Scenariusz|Kroki|
|---|---|---|
|Maszyny wirtualne| Kojarzenie publicznych adresów IP z prefiksu z maszynami wirtualnymi na platformie Azure zmniejsza obciążenie związane z zarządzaniem, jeśli chodzi o umieszczanie adresów IP na białej liście w zaporze. Możesz po prostu umieścić na białej liście cały prefiks z pojedynczą regułą zapory. Podczas skalowania z maszynami wirtualnymi na platformie Azure można skojarzyć adresy IP z tego samego prefiksu, oszczędzając koszty, czas i obciążenie związane z zarządzaniem.| Aby skojarzyć adresy IP z prefiksu do maszyny wirtualnej: 1. [Utwórz prefiks.](manage-public-ip-address-prefix.md) 2. [Utwórz adres IP z prefiksu.](manage-public-ip-address-prefix.md) 3. [Skojarz adres IP z interfejsem sieciowym maszyny wirtualnej.](virtual-network-network-interface-addresses.md#add-ip-addresses) Można również [skojarzyć adresy IP z zestawem skalowania maszyny wirtualnej](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Standardowe moduły równoważenia obciążenia | Kojarzenie publicznych adresów IP z prefiksu do konfiguracji ip frontonu lub reguły wychodzącej modułu równoważenia obciążenia zapewnia uproszczenie przestrzeni adresów IP platformy Azure. Można uprościć scenariusz przez uwodzenie połączeń wychodzących, które mają pochodzić z zakresu ciągłych adresów IP zdefiniowanych przez publiczny prefiks IP. | Aby skojarzyć wiadomości IP z prefiksu z modułem równoważenia obciążenia: 1. [Utwórz prefiks.](manage-public-ip-address-prefix.md) 2. [Utwórz adres IP z prefiksu.](manage-public-ip-address-prefix.md) 3. Podczas tworzenia modułu równoważenia obciążenia wybierz lub zaktualizuj adres IP utworzony w kroku 2 powyżej jako adres IP frontendu modułu równoważenia obciążenia. |
| Azure Firewall | Publiczny adres IP można użyć z prefiksu wychodzącego SNAT. Oznacza to, że cały wychodzący ruch sieci wirtualnej jest tłumaczony na publiczny adres IP [zapory azure.](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Ponieważ ten adres IP pochodzi z prefiksu z góry określony, jest bardzo łatwo wiedzieć z wyprzedzeniem, jak będzie wyglądać publiczny ślad ip na platformie Azure. | 1. [Utwórz prefiks.](manage-public-ip-address-prefix.md) 2. [Utwórz adres IP z prefiksu.](manage-public-ip-address-prefix.md) 3. Podczas [wdrażania zapory platformy Azure](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)należy wybrać adres IP, który wcześniej został przydzielony z prefiksu.|
| Brama aplikacji w wersji 2 | Publiczny adres IP można użyć z prefiksu skalowania automatycznego i bramy aplikacji nadmiarowej strefy w wersji 2. Ponieważ ten adres IP pochodzi z prefiksu z góry określony, jest bardzo łatwo wiedzieć z wyprzedzeniem, jak będzie wyglądać publiczny ślad ip na platformie Azure. | 1. [Utwórz prefiks.](manage-public-ip-address-prefix.md) 2. [Utwórz adres IP z prefiksu.](manage-public-ip-address-prefix.md) 3. Podczas [wdrażania bramy aplikacji](../application-gateway/quick-create-portal.md#create-an-application-gateway)należy wybrać adres IP, który został wcześniej przydzielony z prefiksu.|

## <a name="constraints"></a>Ograniczenia

- Nie można określić adresów IP prefiksu. Platforma Azure przydziela adresy IP dla prefiksu na podstawie określonego rozmiaru.
- Można utworzyć prefiks maksymalnie 16 adresów IP lub /28. Aby uzyskać więcej informacji, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Po utworzeniu prefiksu nie można zmienić zakresu.
- Z zakresu prefiksu można przypisać tylko statyczne publiczne adresy IP utworzone za pomocą standardowej jednostki SKU. Aby dowiedzieć się więcej o publicznych jednostkach SKU dotyczących adresów IP, zobacz [publiczny adres IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Adresy z zakresu można przypisać tylko do zasobów usługi Azure Resource Manager. Adresów nie można przypisać do zasobów w klasycznym modelu wdrażania.
- Wszystkie publiczne adresy IP utworzone na podstawie prefiksu muszą istnieć w tym samym regionie platformy Azure i subskrypcji jako prefiks i muszą być przypisane do zasobów w tym samym regionie i subskrypcji.
- Nie można usunąć prefiksu, jeśli wszystkie adresy w nim są przypisane do publicznych zasobów adresów IP skojarzonych z zasobem. Rozłącz wszystkie zasoby publicznego adresu IP, które są najpierw przypisane adresy IP od prefiksu.


## <a name="next-steps"></a>Następne kroki

- [Tworzenie](manage-public-ip-address-prefix.md) prefiksu publicznego adresu IP
