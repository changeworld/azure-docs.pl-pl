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
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 11/14/2018
ms.openlocfilehash: 98bc87dcaf229671647b201fe37eee114abad731
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648816"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Korzystanie z szablonów usługi Azure Resource Manager w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Szablony usługi Azure Resource Manager umożliwia wdrażanie i Inicjowanie obsługi administracyjnej wszystkie zasoby dla aplikacji w jednej, skoordynowanej operacji. Można także wdrożyć ponownie szablonów, aby wprowadzić zmiany do zasobów w grupie zasobów.

Szablony te można wdrożyć z portalu Microsoft Azure Stack, programu PowerShell, wiersz polecenia i programu Visual Studio.

Następujące szablony szybkiego startu są dostępne na [GitHub](https://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Wdrażanie programu SharePoint Server (bez — wysokiej dostępności wdrożenia)

Za pomocą rozszerzenia DSC programu PowerShell do [utworzyć farmę programu SharePoint Server 2013](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha) zawierającą następujące zasoby:

* Sieć wirtualna
* Trzy konta magazynu
* Dwa moduły równoważenia obciążenia zewnętrzne
* Jedną maszynę wirtualną (VM) skonfigurowany jako kontroler domeny dla nowego lasu z jedną domeną
* Jedna maszyna wirtualna skonfigurowana jako autonomiczny serwer programu SQL Server 2014
* Jedna maszyna wirtualna konfigurowane jako farmy programu SharePoint Server 2013 na jednym komputerze

## <a name="deploy-ad-non-high-availability-deployment"></a>Wdrażanie AD (inne niż wysokiej — dostępność — wdrożenia)

Za pomocą rozszerzenia DSC programu PowerShell do [Tworzenie serwera kontrolera domeny usługi AD](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha) zawierającą następujące zasoby:

* Sieć wirtualna
* Jedno konto magazynu
* Jeden zewnętrzny moduł równoważenia obciążenia
* Jedną maszynę wirtualną (VM) skonfigurowany jako kontroler domeny dla nowego lasu z jedną domeną

## <a name="deploy-adsql-non-high-availability-deployment"></a>Wdrażanie AD/SQL (inne niż wysokiej — dostępność — wdrożenia)

Za pomocą rozszerzenia DSC programu PowerShell do [Tworzenie autonomicznego serwera SQL Server 2014](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha) zawierającą następujące zasoby:

* Sieć wirtualna
* Dwa konta magazynu
* Jeden zewnętrzny moduł równoważenia obciążenia
* Jedną maszynę wirtualną (VM) skonfigurowany jako kontroler domeny dla nowego lasu z jedną domeną
* Jedna maszyna wirtualna skonfigurowana jako autonomiczny serwer programu SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Konfigurowanie istniejącej maszyny wirtualnej lokalnego Configuration Manager (LCM) i zarejestruj go z serwerem ściągania DSC konta automatyzacji platformy Azure za pomocą rozszerzenia DSC programu PowerShell.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Tworzenie maszyny wirtualnej na podstawie obrazu użytkownika

[Tworzenie maszyny wirtualnej na podstawie obrazu użytkownika niestandardowego](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-from-user-image). Ten szablon wdraża również sieć wirtualną (z systemem DNS), publiczny adres IP i interfejsu sieciowego.

## <a name="basic-virtual-machine"></a>Podstawowa maszyna wirtualna

[Wdrażanie maszyny Wirtualnej z systemem Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm) zawierającej sieć wirtualną (z systemem DNS), publiczny adres IP i interfejsu sieciowego.

## <a name="cancel-a-running-template-deployment"></a>Anuluj uruchomione wdrożenie szablonu

Aby anulować bieżącym wdrożeniu szablonu, należy użyć [Stop-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment) polecenia cmdlet programu PowerShell.

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie szablonów za pomocą portalu](azure-stack-deploy-template-portal.md)
* [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
