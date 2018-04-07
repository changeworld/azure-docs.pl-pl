---
title: Zarządzanie maszyn wirtualnych przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się więcej poleceń, które służą do automatyzacji zadań zarządzania maszyn wirtualnych.
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
ms.openlocfilehash: 942141fad09e6233efc7f850212a73f8a39c163c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Zarządzanie maszynami wirtualnymi przy użyciu programu Azure PowerShell
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Typowe poleceń programu PowerShell przy użyciu modelu Resource Manager, zobacz [tutaj](../../virtual-machines-windows-ps-common-ref.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Wiele zadań, czy każdego dnia, aby zarządzać maszyn wirtualnych można zautomatyzować za pomocą poleceń cmdlet programu Azure PowerShell. Ten artykuł zawiera przykładowe polecenia zadania prostszy i łącza do artykułów, które Pokaż polecenia dla bardziej złożonych zadań.

> [!NOTE]
> Jeśli nie zostało to jeszcze zainstalowaniu i skonfigurowaniu programu Azure PowerShell, ale można wyświetlić instrukcje w artykule [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
> 
> 

## <a name="how-to-use-the-example-commands"></a>Jak używać przykładowe polecenia
Należy zastąpić fragment tekstu w poleceniach tekst, który jest odpowiedni dla danego środowiska. < a > symbole wskazują należy zastąpić tekst. Zamień tekst, Usuń symbole, ale pozostawić znaki cudzysłowu w miejscu.

## <a name="get-a-vm"></a>Uzyskiwanie maszyny Wirtualnej
Jest to podstawowe zadania, które będzie używane. Należy uzyskać informacje na temat maszyny Wirtualnej, wykonywania zadań na maszynie Wirtualnej lub pobrać dane wyjściowe mają być przechowywane w zmiennej.

Aby uzyskać informacje o maszynie Wirtualnej, uruchom to polecenie, zastępując wszystko w cudzysłowie, łącznie z < a > znaków:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Aby przechowywać dane wyjściowe w zmiennej $vm, uruchom polecenie:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Zaloguj się do maszyny Wirtualnej z systemem Windows
Uruchom następujące polecenia:

> [!NOTE]
> Nazwa maszyny wirtualnej i w chmurze usługi można uzyskać z widoku **Get-AzureVM** polecenia.
> 
> $svcName = "<cloud service name>" $vmName = "<virtual machine name>" $localPath = "< dysk i lokalizację folderu do przechowywania pobranych plików RDP, przykład: c:\temp >" $localFile = $localPath + "\" $vmname +"RDP"Get-AzureRemoteDesktopFile - ServiceName $svcName-name $vmName - LocalPath $localFile — uruchamianie
> 
> 

## <a name="stop-a-vm"></a>Zatrzymywanie maszyny wirtualnej
Uruchom następujące polecenie:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> Użyj tego parametru, aby zachować wirtualnego adresu IP (VIP) z usługi w chmurze, w przypadku, gdy jest ostatnia maszyna wirtualna w tej usłudze w chmurze. <br><br> Jeśli parametr StayProvisioned nadal zostaną naliczone opłaty dla maszyny Wirtualnej.
> 
> 

## <a name="start-a-vm"></a>Uruchamianie maszyny wirtualnej
Uruchom następujące polecenie:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Dołączanie dysku danych
To zadanie wymaga kilku kroków. Najpierw użyj *** Dodaj — polecenie cmdlet AzureDataDisk ***, aby dodać dysk do obiektu $vm. Następnie należy użyć **AzureVM aktualizacji** polecenia cmdlet można zaktualizować konfiguracji maszyny Wirtualnej.

Musisz także zdecydować, czy dołączyć nowy dysk, czy taki, który zawiera dane. Dla nowego dysku polecenie tworzy plik VHD i dołącza go.

Aby dołączyć nowy dysk, uruchom następujące polecenie:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Aby dołączyć istniejący dysk danych, uruchom następujące polecenie:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Aby dołączyć dyski danych z istniejącego pliku VHD w magazynie obiektów blob, uruchom następujące polecenie:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Tworzenie maszyny Wirtualnej z systemem Windows
Aby utworzyć nową maszynę wirtualną z systemem Windows na platformie Azure, skorzystaj z instrukcji w [użycia programu Azure PowerShell do tworzenia i wstępnie skonfigurować maszyn wirtualnych z systemem Windows](create-powershell.md). Kroki tego tematu, podczas tworzenia zestawu poleceń programu PowerShell systemu Azure tworzy Maszynę wirtualną z systemem Windows można wstępnie:

* Z członkostwa w domenie usługi Active Directory.
* W przypadku dodatkowych dysków.
* Jako element członkowski istniejącej równoważeniem obciążenia należy ustawić.
* Za pomocą statycznego adresu IP.

