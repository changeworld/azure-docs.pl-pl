---
title: Jak tag zasobu maszyny Wirtualnej systemu Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o znakowanie utworzona na platformie Azure przy użyciu modelu wdrażania Menedżera zasobów systemu Windows maszyny wirtualnej
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 5044bda855b6ac88eb5784f257686bf8a1838222
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30917503"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Jak tagu maszyny wirtualnej systemu Windows na platformie Azure
W tym artykule opisano różne sposoby tagu maszyny wirtualnej systemu Windows na platformie Azure za pośrednictwem modelu wdrażania usługi Resource Manager. Tagi to pary klucz wartość zdefiniowana przez użytkownika, które mogą być umieszczone bezpośrednio na zasób lub grupa zasobów. Azure obecnie obsługuje maksymalnie 15 znaczników dla zasobu i grupy zasobów. Tagi mogą dotyczącymi zasobów w czasie tworzenia lub dodać do istniejącego zasobu. Należy pamiętać, że tagi są obsługiwane dla zasobów utworzone za pośrednictwem tylko modelu wdrażania Menedżera zasobów. Jeśli chcesz oznaczyć maszyny wirtualnej systemu Linux, zobacz [jak tagu maszyny wirtualnej systemu Linux na platformie Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Znakowanie przy użyciu programu PowerShell
Aby utworzyć, dodawanie i usuwanie tagów za pomocą programu PowerShell, należy najpierw skonfigurować Twoje [środowiska PowerShell z usługą Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Po zakończeniu instalacji można umieścić znaczniki zasobów obliczeniowych, sieci i magazynu podczas tworzenia lub zasób jest tworzony za pomocą programu PowerShell. W tym artykule będzie skoncentrować się na wyświetlanie/edytowanie tagów umieszczone na maszynach wirtualnych.

Po pierwsze, przejdź do maszyny wirtualnej za pomocą `Get-AzureRmVM` polecenia cmdlet.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Maszyna wirtualna zawiera już znaczników, zostanie wtedy wyświetlone wszystkie tagi na zasobu:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Jeśli chcesz dodać tagów za pomocą programu PowerShell, możesz użyć `Set-AzureRmResource` polecenia. Należy zwrócić uwagę podczas aktualizowania tagów za pomocą programu PowerShell, tagi są aktualizowane jako całość. Więc jeden tag jest dodawane do zasobu, który ma już tagi, należy uwzględnić wszystkie tagi, które mają zostać umieszczone w zasobie. Poniżej przedstawiono przykładowy sposób dodawania dodatkowych tagów do zasobów za pomocą poleceń cmdlet programu PowerShell.

To pierwsze polecenie cmdlet Ustawia wszystkie tagi dotyczącymi *MyTestVM* do *$tags* przy użyciu zmiennej `Get-AzureRmResource` i `Tags` właściwości.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Drugie polecenie wyświetla tagi dla danego zmiennej.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment

Trzecie polecenie dodaje tag dodatkowe do *$tags* zmiennej. Zwróć uwagę na użycie **+=** do dołączenia nową parę klucz wartość do *$tags* listy.

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

Czwarte polecenie ustawia wszystkie tagi zdefiniowane w *$tags* zmienną do określonych zasobów. W takim przypadku jest MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Polecenie piątego Wyświetla wszystkie tagi zasobu. Jak widać, *lokalizacji* jest teraz zdefiniowany jako tag *MyLocation* jako wartość.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment
        Value        MyLocation
        Name        Location

Aby dowiedzieć się więcej na temat znakowanie za pomocą programu PowerShell, zapoznaj się [polecenia cmdlet usługi Azure Resource][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat znakowanie zasobów platformy Azure, zobacz [Omówienie usługi Azure Resource Manager] [ Azure Resource Manager Overview] i [przy użyciu tagów do organizowania zasobów platformy Azure][Using Tags to organize your Azure Resources].
* Aby dowiedzieć się, jak tagów można zarządzać korzystanie z zasobów platformy Azure, zobacz [Opis rachunku Azure] [ Understanding your Azure Bill] i [uzyskać wgląd w Microsoft Azure użycia zasobów][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/powershell-azure-resource-manager.md
[Azure Resource Cmdlets]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
