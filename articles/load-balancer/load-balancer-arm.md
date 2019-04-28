---
title: Pomoc techniczna platformy Azure Resource Manager dla modułu równoważenia obciążenia | Dokumentacja firmy Microsoft
description: Przy użyciu programu powershell dla usługi równoważenia obciążenia za pomocą usługi Azure Resource Manager. Za pomocą szablonów dla modułu równoważenia obciążenia
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 596ac871067886ee3124c0f21beb35cb3b8fe1ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888994"
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Przy użyciu pomocy technicznej platformy Azure Resource Manager z usługą Azure Load Balancer



Usługa Azure Resource Manager jest preferowane umożliwiająca usług na platformie Azure. Usługa Azure Load Balancer można zarządzać za pomocą narzędzia i interfejsy API oparte na usłudze Azure Resource Manager.

## <a name="concepts"></a>Pojęcia

Za pomocą Menedżera zasobów usługi Azure Load Balancer zawiera następujące zasoby podrzędne:

* Konfiguracja IP frontonu — moduł równoważenia obciążenia może zawierać jeden lub więcej adresów IP frontonu, znanych także jako wirtualne adresy IP (VIP). Te adresy IP są używane podczas transferu danych przychodzących.
* Pula adresów zaplecza — są to adresy IP skojarzone z maszyny wirtualnej sieci karta sieciowa (NIC) do której jest dystrybuowane obciążenie.
* Reguły równoważenia obciążenia — właściwości reguły mapowania adresu IP podanego frontonu i kombinację portów na zestaw adresów IP zaplecza i kombinację portów. Moduł równoważenia obciążenia może mieć różne reguły równoważenia obciążenia. Każda reguła stanowi kombinację adresu IP frontonu i portu zaplecza adresów IP i port skojarzony z maszynami wirtualnymi.
* Sondy — sondy pozwalają na śledzenie kondycji wystąpień maszyn wirtualnych. Jeśli sonda kondycji nie powiedzie się, wystąpienie maszyny Wirtualnej jest wykluczana z rotacji automatycznie.
* Reguły NAT dla ruchu przychodzącego — translatora adresów Sieciowych reguły określające ruchu przychodzącego przepływu za pośrednictwem adresu IP frontonu i dystrybuowane do adresu IP zaplecza.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Szablony szybkiego startu

Usługa Azure Resource Manager pozwala inicjować obsługę aplikacji za pomocą deklaratywnych szablonów. W pojedynczym szablonie możesz wdrożyć wiele usług wraz z ich zależnościami. Za pomocą tego samego szablonu możesz wdrażać aplikację na każdym etapie jej cyklu życia.

Szablony mogą zawierać definicje dla maszyn wirtualnych, sieci wirtualne, zestawy dostępności, interfejsy sieciowe (NIC), kont magazynu, moduły równoważenia obciążenia, sieciowe grupy zabezpieczeń i publicznych adresów IP. Przy użyciu szablonów można utworzyć wszystko, czego potrzebujesz do złożonych aplikacji. Plik szablonu może zostać sprawdzone w system zarządzania zawartością w kontroli wersji i współpracę.

[Dowiedz się więcej na temat szablonów](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Dowiedz się więcej na temat zasobów sieciowych](../networking/networking-overview.md)

Dla szablonów szybkiego startu przy użyciu usługi Azure Load Balancer, zobacz [repozytorium GitHub](https://github.com/Azure/azure-quickstart-templates) obsługujący zbiór szablony społeczności wygenerowany.

Przykłady szablonów:

* [2 maszyny wirtualne usługi Load Balancer i reguł równoważenia obciążenia](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 maszyny wirtualne w sieci Wirtualnej przy użyciu reguł wewnętrznego modułu równoważenia obciążenia i równoważenia obciążenia](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 maszyny wirtualne w module równoważenia obciążenia i Konfiguruj reguły translatora adresów Sieciowych na równoważenia obciążenia](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Konfigurowanie równoważenia obciążenia platformy Azure za pomocą programu PowerShell lub interfejsu wiersza polecenia

Wprowadzenie do poleceń cmdlet, narzędzi wiersza polecenia i interfejsów API REST usługi Azure Resource Manager

* [Polecenia cmdlet systemu Azure Networking](https://docs.microsoft.com/powershell/module/az.network#networking) może służyć do tworzenia modułu równoważenia obciążenia.
* [Jak utworzyć moduł równoważenia obciążenia przy użyciu usługi Azure Resource Manager](load-balancer-get-started-ilb-arm-ps.md)
* [Przy użyciu wiersza polecenia platformy Azure za pomocą usługi Azure Resource Management](../xplat-cli-azure-resource-manager.md)
* [Interfejsy API REST usługi równoważenia obciążenia](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Kolejne kroki

Możesz również [wprowadzenie do tworzenia modułu równoważenia obciążenia nakierowanego na Internet](load-balancer-get-started-internet-arm-ps.md) i określić typ [trybu dystrybucji](load-balancer-distribution-mode.md) dla zachowania ruchu sieci modułu równoważenia obciążenia.

Dowiedz się, jak zarządzać [bezczynności ustawienia limitu czasu protokołu TCP dla modułu równoważenia obciążenia](load-balancer-tcp-idle-timeout.md). Jest to ważne w przypadku, gdy Twoja aplikacja potrzebuje do podtrzymywania połączenia dla serwerów za modułem równoważenia obciążenia.
