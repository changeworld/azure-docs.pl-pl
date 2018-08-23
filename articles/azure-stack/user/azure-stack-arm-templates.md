---
title: Korzystanie z szablonów usługi Azure Resource Manager w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą szablonów usługi Azure Resource Manager w usłudze Azure Stack w celu inicjowania obsługi administracyjnej zasobów.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: 456f27b97ee644aef34f9bb9e2c0525bd61c1c84
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055221"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Korzystanie z szablonów usługi Azure Resource Manager w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Szablony usługi Azure Resource Manager umożliwia wdrażanie i Inicjowanie obsługi administracyjnej wszystkie zasoby dla aplikacji w jednej, skoordynowanej operacji. Można także wdrożyć ponownie szablonów, aby wprowadzić zmiany do zasobów w grupie zasobów.

Szablony te można wdrożyć z portalu Microsoft Azure Stack, programu PowerShell, wiersz polecenia i programu Visual Studio.

Następujące szablony szybkiego startu są dostępne na [GitHub](http://aka.ms/azurestackgithub).

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Wdrażanie programu SharePoint Server (bez — wysokiej dostępności wdrożenia)

Użyj rozszerzenia DSC programu PowerShell do utworzenia farmy programu SharePoint Server 2013, która zawiera następujące zasoby:

* Sieć wirtualna
* Trzy konta magazynu
* Dwa moduły równoważenia obciążenia zewnętrzne
* Jedną maszynę wirtualną (VM) skonfigurowany jako kontroler domeny dla nowego lasu z jedną domeną
* Jedna maszyna wirtualna skonfigurowana jako autonomiczny serwer programu SQL Server 2014
* Jedna maszyna wirtualna konfigurowane jako farmy programu SharePoint Server 2013 na jednym komputerze

## <a name="deploy-ad-non-high-availability-deployment"></a>Wdrażanie AD (inne niż wysokiej — dostępność — wdrożenia)

Użyj rozszerzenia DSC programu PowerShell, aby utworzyć serwer kontrolera domeny usługi AD, która zawiera następujące zasoby:

* Sieć wirtualna
* Jedno konto magazynu
* Jeden zewnętrzny moduł równoważenia obciążenia
* Jedną maszynę wirtualną (VM) skonfigurowany jako kontroler domeny dla nowego lasu z jedną domeną

## <a name="deploy-adsql-non-high-availability-deployment"></a>Wdrażanie AD/SQL (inne niż wysokiej — dostępność — wdrożenia)

Rozszerzenie DSC programu PowerShell umożliwia utworzenie autonomicznego serwera SQL Server 2014 i zawiera następujące zasoby:

* Sieć wirtualna
* Dwa konta magazynu
* Jeden zewnętrzny moduł równoważenia obciążenia
* Jedną maszynę wirtualną (VM) skonfigurowany jako kontroler domeny dla nowego lasu z jedną domeną
* Jedna maszyna wirtualna skonfigurowana jako autonomiczny serwer programu SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Konfigurowanie istniejącej maszyny wirtualnej lokalnego Configuration Manager (LCM) i zarejestruj go z serwerem ściągania DSC konta automatyzacji platformy Azure za pomocą rozszerzenia DSC programu PowerShell.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Tworzenie maszyny wirtualnej na podstawie obrazu użytkownika

Tworzenie maszyny wirtualnej na podstawie obrazu użytkownika niestandardowego. Ten szablon wdraża również sieć wirtualną (z systemem DNS), publiczny adres IP i interfejsu sieciowego.

## <a name="basic-virtual-machine"></a>Podstawowa maszyna wirtualna

Wdróż maszynę Wirtualną Windows, zawierająca sieć wirtualną (z systemem DNS), publiczny adres IP i interfejsu sieciowego.

## <a name="cancel-a-running-template-deployment"></a>Anuluj uruchomione wdrożenie szablonu

Aby anulować bieżącym wdrożeniu szablonu, należy użyć `Stop-AzureRmResourceGroupDeployment` polecenia cmdlet programu PowerShell.

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie szablonów za pomocą portalu](azure-stack-deploy-template-portal.md)
* [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
