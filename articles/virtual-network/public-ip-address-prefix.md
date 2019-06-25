---
title: Prefiks adresu w usłudze Azure publiczny adres IP | Dokumentacja firmy Microsoft
description: Dowiedz się o jakie Azure publiczny prefiks adresu IP i jak może ono pomóc przypisać przewidywalne publicznych adresów IP dla zasobów.
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
ms.openlocfilehash: fe163fc2370e884bed58bfffe5baffa5642b6d90
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147996"
---
# <a name="public-ip-address-prefix"></a>Publiczny prefiks adresu IP

Publiczny prefiks adresu IP jest zastrzeżony zakres adresów IP dla sieci publicznych punktów końcowych na platformie Azure. Platforma Azure przydziela ciągły zakres adresów ze swoją subskrypcją, w oparciu o ile określisz. Jeśli nie jesteś zaznajomiony z publicznymi adresami, zobacz [publiczne adresy IP.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Publiczne adresy IP są przypisywane z puli adresów w każdym regionie platformy Azure. Możesz [Pobierz](https://www.microsoft.com/download/details.aspx?id=56519) listę zakresów adresów, platforma Azure używa dla każdego regionu. Na przykład 40.121.0.0/16 jest jedną z ponad 100 zakresów, używaną przez platformę Azure w regionie wschodnie stany USA. Ten zakres obejmuje można używać adresów 40.121.0.1 - 40.121.255.254.

Utwórz publiczny prefiks adresu IP w regionie platformy Azure i subskrypcję, określając nazwę, a ile adresów mają prefiks do uwzględnienia. Na przykład, jeśli utworzysz publiczny prefiks adresu IP/28, platforma Azure przydziela 16 adresów z jednego z jego zakresów dla Ciebie. Nie wiesz, którym z zakresu platformy Azure zostanie przypisana do czasu utworzenia zakresu, ale adresy są ciągłe. Prefiksy publicznych adresów IP mają opłaty. Aby uzyskać więcej informacji, zobacz [cennik publicznych adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Dlaczego warto utworzyć publiczny prefiks adresu IP?

Podczas tworzenia zasobów publicznych adresów IP, platforma Azure przypisuje dostępne publiczny adres IP z żadnym z zakresów, do użytku w tym regionie. Gdy platforma Azure przypisuje adres, wiesz, co to jest adres, ale dopóki platforma Azure przypisuje adres, nie wiadomo, jakiego adresu może zostać przypisana. Może to być problematyczne, na przykład możesz lub partnerów biznesowych Konfigurowanie reguł zapory zezwalających na określone adresy IP. Każdorazowo, gdy nowy publiczny adres IP można przypisać do tego zasobu, adres musi być dodany do reguły zapory. Podczas przypisywania adresów do zasobów z publicznych prefiksu adresu IP, reguły zapory nie trzeba można zaktualizować każdorazowo, gdy jeden z adresów, przypisać, ponieważ całego zakresu, może zostać dodany do reguły.

## <a name="benefits"></a>Korzyści

- Można utworzyć zasobów publicznych adresów IP z zakresu znane.
- Użytkownik lub partnerów biznesowych można utworzyć reguły zapory z zakresami, zawierające publiczne adresy IP, aktualnie przypisanej, a także adresy, które nie zostały jeszcze przypisane. Eliminuje to potrzebę zmienić reguły zapory, ponieważ przypisywania adresów IP do nowych zasobów.
- Domyślny rozmiar zakresu, które możesz utworzyć to/28 lub 16 adresów IP.
- Nie ma ograniczeń co ile zakresów można tworzyć, jednak istnieją limity maksymalną liczbę statycznych publicznych adresów IP, które masz w subskrypcji platformy Azure. W rezultacie liczba zakresów, które możesz utworzyć nie może obejmować więcej statyczne publiczne adresy IP nie może mieć w ramach subskrypcji. Aby uzyskać więcej informacji, zobacz [limity platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Do dowolnych zasobów platformy Azure, którą można przypisać publiczny adres IP można przypisać adresy, które możesz utworzyć przy użyciu adresów z prefiksu.
- Można łatwo zobaczyć, które adresy IP, które są przydzielane i jeszcze nie jest przydzielany z zakresu.

## <a name="scenarios"></a>Scenariusze
Następujące zasoby do statyczny publiczny adres IP można skojarzyć z prefiksu:

|Resource|Scenariusz|Kroki|
|---|---|---|
|Maszyny wirtualne| Kojarzenie publiczne adresy IP z prefiksem z maszynami wirtualnymi na platformie Azure zmniejsza nakład, jeśli chodzi o dodanie do listy dozwolonych adresów IP w zaporze. Możesz po prostu dozwolonych całego prefiks z pojedynczą regułę zapory. Podczas skalowania maszyn wirtualnych na platformie Azure, adresy IP można skojarzyć z tym samym prefiksem, oszczędność kosztów i czasu oraz narzutu związanego z zarządzaniem.| Aby skojarzyć adresy IP z prefiksem do maszyny wirtualnej: 1. [Utwórz prefiksu.](manage-public-ip-address-prefix.md) 2. [Utwórz adres IP z prefiksu.](manage-public-ip-address-prefix.md) 3. [Skojarzenie adresu IP do interfejsu sieciowego maszyny wirtualnej.](virtual-network-network-interface-addresses.md#add-ip-addresses)
| Moduły równoważenia obciążenia | Kojarzenie publiczne adresy IP z prefiksem na adres IP frontonu konfiguracji lub wychodzącą regułą równoważenia obciążenia zapewnia uproszczenia usługi platformy Azure przestrzeń publicznych adresów IP. Pielęgnacja połączenia wychodzące do być pochodzenia zakres sąsiadujących adresów IP określone przez publiczny prefiks IP można uprościć danego scenariusza. | Aby skojarzyć adresy IP z prefiksem do modułu równoważenia obciążenia: 1. [Utwórz prefiksu.](manage-public-ip-address-prefix.md) 2. [Utwórz adres IP z prefiksu.](manage-public-ip-address-prefix.md) 3. Podczas tworzenia modułu równoważenia obciążenia, wybierz lub zaktualizować adres IP utworzony w kroku 2 powyżej jako adresu IP frontonu modułu równoważenia obciążenia. |
| Azure Firewall | Za pomocą publicznego adresu IP z prefiksem dla SNAT wychodzących. Oznacza to, że cały wychodzący ruch sieciowy wirtualnego jest tłumaczona na [zapory usługi Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) publicznego adresu IP. Ponieważ ten adres IP pochodzą ze wstępnie zdefiniowanych prefiksu, jest bardzo proste wiedzieć wcześniejsze, jak będzie wyglądać Twojej publicznych obecności adresów IP na platformie Azure. | 1. [Utwórz prefiksu.](manage-public-ip-address-prefix.md) 2. [Utwórz adres IP z prefiksu.](manage-public-ip-address-prefix.md) 3. Gdy użytkownik [wdrażania zapory platformy Azure](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), pamiętaj wybrać poprzednio przydzielony adres IP z prefiksem.|

## <a name="constraints"></a>Ograniczenia

- Nie można określić adresy IP dla prefiksu. Platforma Azure przydziela adresów IP dla prefiksu, w zależności od rozmiaru, który określisz.
- Można utworzyć prefiks maksymalnie 16 adresów IP lub / 28. Aby uzyskać więcej informacji, zobacz [limity platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Zakres, nie można zmienić po utworzeniu prefiks.
- Zakres jest tylko adresy IPv4. Zakres nie zawiera adresów IPv6.
- Można przypisać tylko statyczne publiczne adresy IP utworzone za pomocą standardowej jednostki SKU z zakresu prefiksu. Aby dowiedzieć się więcej na temat publiczny adres IP adres jednostki SKU, zobacz [publiczny adres IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Adres z zakresu można przypisać tylko do zasobów usługi Azure Resource Manager. Nie można przypisać adresy z zasobami w klasycznym modelu wdrażania.
- Wszystkie publiczne adresy IP utworzone na podstawie prefiksu muszą znajdować się w tym samym regionie platformy Azure i subskrypcji, ponieważ prefiks i muszą być przypisane do zasobów, w tym samym regionie i subskrypcji.
- Nie można usunąć prefiksu, jeśli wszystkie adresy w niej są przypisane do zasobami publicznego adresu IP adres skojarzony z zasobem. Usuń skojarzenie wszystkie zasoby publicznych adresów IP adresów, które są najpierw przypisane adresy IP z prefiksu.


## <a name="next-steps"></a>Kolejne kroki

- [Utwórz](manage-public-ip-address-prefix.md) publiczny prefiks adresu IP
