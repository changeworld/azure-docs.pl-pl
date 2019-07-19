---
title: Azure Resource Manager obsługa Load Balancer | Microsoft Docs
description: Korzystanie z programu PowerShell dla Load Balancer z Azure Resource Manager. Używanie szablonów dla usługi równoważenia obciążenia
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 839b607b7787d51151401737848a46d7b66229dd
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275485"
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Używanie Azure Resource Manager pomocy technicznej z Azure Load Balancer



Azure Resource Manager jest preferowaną strukturą zarządzania dla usług platformy Azure. Azure Load Balancer można zarządzać przy użyciu opartych na Azure Resource Manager interfejsów API i narzędzi.

## <a name="concepts"></a>Pojęcia

W przypadku Menedżer zasobów Azure Load Balancer zawiera następujące zasoby podrzędne:

* Konfiguracja adresu IP frontonu — moduł równoważenia obciążenia może zawierać co najmniej jeden adres IP frontonu, w przeciwnym razie nazywany wirtualnymi adresami VIP. Te adresy IP są używane podczas transferu danych przychodzących.
* Pula adresów zaplecza — są to adresy IP skojarzone z kartą sieciową maszyny wirtualnej, do której jest dystrybuowane obciążenie.
* Reguły równoważenia obciążenia — Właściwość reguły mapuje daną kombinację adresu IP frontonu i portu na zestaw adresów IP zaplecza i kombinacji portów. Moduł równoważenia obciążenia może mieć różne reguły równoważenia obciążenia. Każda reguła jest kombinacją adresu IP frontonu i portu oraz adresu IP zaplecza i portu skojarzonego z maszynami wirtualnymi.
* Sondy — sondy umożliwiają śledzenie kondycji wystąpień maszyn wirtualnych. Jeśli sonda kondycji zakończy się niepowodzeniem, wystąpienie maszyny wirtualnej jest wyłączane automatycznie.
* Reguły NAT dla ruchu przychodzącego — reguły NAT definiujące ruch przychodzący przepływający przez adres IP frontonu i dystrybuowany do adresu IP zaplecza.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Szablony szybkiego startu

Usługa Azure Resource Manager pozwala inicjować obsługę aplikacji za pomocą deklaratywnych szablonów. W pojedynczym szablonie możesz wdrożyć wiele usług wraz z ich zależnościami. Za pomocą tego samego szablonu możesz wdrażać aplikację na każdym etapie jej cyklu życia.

Szablony mogą zawierać definicje Virtual Machines, sieci wirtualnych, zestawów dostępności, interfejsów sieciowych (nic), kont magazynu, modułów równoważenia obciążenia, sieciowych grup zabezpieczeń i publicznych adresów IP. Za pomocą szablonów można utworzyć wszystko, czego potrzebujesz w przypadku złożonej aplikacji. Plik szablonu można sprawdzić w systemie zarządzania zawartością na potrzeby kontroli wersji i współpracy.

[Dowiedz się więcej o szablonach](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Dowiedz się więcej o zasobach sieciowych](../networking/networking-overview.md)

Szablony szybkiego startu używające Azure Load Balancer można znaleźć w [repozytorium GitHub](https://github.com/Azure/azure-quickstart-templates) , które hostuje zestaw szablonów wygenerowanych przez społeczność.

Przykłady szablonów:

* [2 maszyny wirtualne w Load Balancer i reguły równoważenia obciążenia](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 maszyny wirtualne w sieci wirtualnej z wewnętrznymi Load Balancer i regułami Load Balancer](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 maszyny wirtualne w Load Balancer i konfigurowanie reguł translatora adresów sieciowych w LB](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Konfigurowanie Azure Load Balancer przy użyciu programu PowerShell lub interfejsu wiersza polecenia

Wprowadzenie do Azure Resource Manager poleceń cmdlet, narzędzi wiersza polecenia i interfejsów API REST

* [Polecenia cmdlet sieci platformy Azure](https://docs.microsoft.com/powershell/module/az.network#networking) mogą służyć do tworzenia Load Balancer.
* [Jak utworzyć moduł równoważenia obciążenia przy użyciu Azure Resource Manager](load-balancer-get-started-ilb-arm-ps.md)
* [Korzystanie z interfejsu wiersza polecenia platformy Azure z usługą Azure Resource Management](../xplat-cli-azure-resource-manager.md)
* [Interfejsy API REST Load Balancer](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Następne kroki

Możesz również rozpocząć [Tworzenie modułu równoważenia obciążenia połączonego z Internetem](load-balancer-get-started-internet-arm-ps.md) i skonfigurować typ [trybu dystrybucji](load-balancer-distribution-mode.md) dla określonego zachowania ruchu sieciowego usługi równoważenia obciążenia.

Dowiedz się, jak zarządzać [ustawieniami limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia](load-balancer-tcp-idle-timeout.md). Jest to ważne, gdy aplikacja musi zachować aktywność połączeń dla serwerów za modułem równoważenia obciążenia.
