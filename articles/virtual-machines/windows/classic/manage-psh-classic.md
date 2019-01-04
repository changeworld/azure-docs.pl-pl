---
title: Zarządzaj maszynami wirtualnymi przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się więcej poleceń, które służą do automatyzowania zadań związanych z zarządzaniem maszynami wirtualnymi.
services: virtual-machines-windows
documentationcenter: windows
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 7cdf9bd3-6578-4069-8a95-e8585f04a394
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2016
ms.author: kasing
ms.openlocfilehash: b7fafa148417ba1667ec0277b414105f95e428ce
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971788"
---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Zarządzanie maszynami wirtualnymi przy użyciu programu Azure PowerShell
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Usługi Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Typowe poleceń programu PowerShell przy użyciu modelu usługi Resource Manager można znaleźć [tutaj](../../virtual-machines-windows-ps-common-ref.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Za pomocą poleceń cmdlet programu Azure PowerShell można zautomatyzować wiele zadań, możesz zrobić każdego dnia na zarządzanie maszynami wirtualnymi. Ten artykuł zawiera przykładowe polecenia prostsze zadań i linki do artykułów, które pokazanie poleceń dla bardziej złożonych zadań.

> [!NOTE]
> Jeśli nie zostały zainstalowane i skonfigurowaniu programu Azure PowerShell, ale możesz uzyskać instrukcjami w artykule [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).
> 
> 

## <a name="how-to-use-the-example-commands"></a>Jak używać przykładowe polecenia
Należy zastąpić fragment tekstu w poleceniach tekst, który jest odpowiednia dla użytkowanego środowiska. < i > symbole wskazują należy zastąpić tekst. Należy zastąpić tekst, Usuń symbole, ale pozostaw cudzysłów na swoim miejscu.

## <a name="get-a-vm"></a>Uzyskiwanie maszyny Wirtualnej
Jest to podstawowe zadania, które będzie używane. Aby uzyskać informacje na temat maszyny Wirtualnej, wykonywania zadań na maszynie Wirtualnej lub pobrać dane wyjściowe można przechowywać w zmiennej, użyj go.

Aby uzyskać informacji o maszynie Wirtualnej, uruchom następujące polecenie, zastępując wszystkich elementów w cudzysłowie, w tym < a > znaków:

    Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Aby zapisywać dane wyjściowe w zmiennej $vm, uruchom polecenie:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Zaloguj się do maszyny Wirtualnej z systemem Windows
Uruchom następujące polecenia:

> [!NOTE]
> Nazwa usługi maszyny wirtualnej i w chmurze można uzyskać z widoku **Get-AzureVM** polecenia.
> 
> $svcName = `"<cloud service name>"` $vmName = `"<virtual machine name>"` $localPath = `"<drive and folder location to store the downloaded RDP file, example: c:\temp >"` $localFile = $localPath + "\" $vmname +"RDP"get-AzureRemoteDesktopFile - ServiceName $svcName — nazwa $vmName — LocalPath $localFile — uruchamianie
> 
> 

## <a name="stop-a-vm"></a>Zatrzymywanie maszyny wirtualnej
Uruchom następujące polecenie:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> Użyj tego parametru, aby zachować wirtualnego adresu IP (VIP) z usługi w chmurze, w przypadku, gdy jest to ostatnia maszyna wirtualna w tej usłudze w chmurze. <br><br> Jeśli używasz parametru StayProvisioned, możesz nadal będzie naliczana dla maszyny Wirtualnej.
> 
> 

## <a name="start-a-vm"></a>Uruchamianie maszyny wirtualnej
Uruchom następujące polecenie:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Dołączanie dysku danych
To zadanie wymaga kilku krokach. Najpierw należy użyć *** Dodaj — polecenie cmdlet AzureDataDisk ***, aby dodać dysk do obiektu $vm. Następnie należy użyć **Update-AzureVM** polecenia cmdlet, aby zaktualizować konfigurację maszyny Wirtualnej.

Musisz także zdecydować, czy dołączyć nowy dysk, czy taki, który zawiera dane. Dla nowego dysku polecenie tworzy plik VHD i dołącza go.

Aby dołączyć nowy dysk, uruchom następujące polecenie:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Aby dołączyć istniejący dysk danych, uruchom następujące polecenie:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Aby dołączyć dyski z danymi z istniejącego pliku VHD w magazynie obiektów blob, uruchom następujące polecenie:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Tworzenie maszyny Wirtualnej z systemem Windows
Aby utworzyć nową maszynę wirtualną na podstawie Windows na platformie Azure, postępuj zgodnie z instrukcjami w [Użyj programu Azure PowerShell do tworzenia i wstępne konfigurowanie maszyn wirtualnych z systemem Windows](create-powershell.md). Ten temat przeprowadza Cię przez proces tworzenia w programie Azure PowerShell, zestawu poleceń oparte na Windows maszyna wirtualna jest tworzona, można je wstępnie skonfigurować:

* Za pomocą członkostwa w domenie usługi Active Directory.
* Za pomocą dodatkowych dysków.
* Jako członek do istniejącego równoważenia obciążenia należy ustawić.
* Za pomocą statycznego adresu IP.

