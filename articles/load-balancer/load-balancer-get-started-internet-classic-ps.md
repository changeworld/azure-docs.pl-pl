---
title: Tworzenie modułu równoważenia obciążenia dostępnego z Internetu — Azure PowerShell klasycznego
titlesuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć dostępny z Internetu moduł równoważenia obciążenia w trybie klasycznym przy użyciu programu PowerShell
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
tags: azure-service-management
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 54ef8782620a387d60454023d0e446279e467a99
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "64730333"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Wprowadzenie do tworzenia dostępnego z Internetu modułu równoważenia obciążenia (klasycznego) przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Program PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Przed rozpoczęciem pracy z zasobami platformy Azure, ważne jest zrozumienie, że platforma Azure ma obecnie dwa modele wdrażania: Usługa Azure Resource Manager i model klasyczny. Przed rozpoczęciem pracy z dowolnym zasobem Azure należy zapoznać się z [modelami i narzędziami wdrażania](../azure-classic-rm.md). Dokumentację dotyczącą różnych narzędzi można wyświetlić, klikając karty w górnej części artykułu. W tym artykule opisano klasyczny model wdrażania. Możesz też zapoznać się z artykułem na temat [tworzenia dostępnego z Internetu modułu równoważenia obciążenia za pomocą usługi Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-load-balancer-using-powershell"></a>Konfiguracja modułu równoważenia obciążenia za pomocą programu PowerShell

Aby skonfigurować moduł równoważenia obciążenia za pomocą programu PowerShell, wykonaj następujące czynności:

1. Jeśli nie znasz programu Azure PowerShell, zapoznaj się z artykułem [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) i postępuj zgodnie z instrukcjami aż do momentu logowania się w programie Azure i wyboru subskrypcji.
2. Po utworzeniu maszyny wirtualnej możesz użyć poleceń cmdlet programu PowerShell, aby dodać moduł równoważenia obciążenia do maszyny wirtualnej w ramach tej samej usługi w chmurze.

W poniższym przykładzie opisano sposób dodania zestawu modułu równoważenia obciążenia o nazwie „webfarm” do usługi w chmurze „mytestcloud” (lub myctestcloud.cloudapp.net), przy jednoczesnym dodaniu punktów końcowych modułu równoważenia obciążenia do maszyn wirtualnych o nazwach „web1” i „web2”. Moduł równoważenia obciążenia odbiera ruch sieciowy przez port 80, a równoważenie obciążenia między maszynami wirtualnymi jest definiowane przez lokalny punkt końcowy (w tym przypadku port 80) przy użyciu protokołu TCP.

### <a name="step-1"></a>Krok 1

Tworzenie punktu końcowego o zrównoważonym obciążeniu dla pierwszej maszyny wirtualnej „web1”

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

### <a name="step-2"></a>Krok 2

Tworzenie kolejnego punktu końcowego dla drugiej maszyny wirtualnej „web2” za pomocą zestawu modułu równoważenia obciążenia o tej samej nazwie

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Usuwanie maszyny wirtualnej z modułu równoważenia obciążenia

Możesz wykorzystać polecenie Remove-AzureEndpoint, aby usunąć punkt końcowy maszyny wirtualnej z modułu równoważenia obciążenia

```powershell
Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin | Update-AzureVM
```

## <a name="next-steps"></a>Kolejne kroki

Możesz też zapoznać się z artykułem na temat [wprowadzenia do tworzenia wewnętrznego modułu równoważenia obciążenia](load-balancer-get-started-ilb-classic-ps.md) i określić typ [trybu dystrybucji](load-balancer-distribution-mode.md) dotyczącego danego ruchu sieciowego modułu równoważenia obciążenia.

Jeśli zastosowanie wymaga zachowania działających połączeń z serwerami za modułem równoważenia obciążenia, zapoznaj się z informacjami o [ustawieniach limitu czasu bezczynności protokołu TCP modułu równoważenia obciążenia](load-balancer-tcp-idle-timeout.md). Pozwala to zrozumieć zachowanie bezczynnego połączenia podczas używania usługi Azure Load Balancer.
