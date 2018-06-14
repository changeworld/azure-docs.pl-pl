---
title: Omówienie agenta maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie agenta maszyny wirtualnej platformy Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: danielsollondon
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: danis
ms.openlocfilehash: a5da5cfb6cda940f68171e42d4512b304fc5f114
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915677"
---
# <a name="azure-virtual-machine-agent-overview"></a>Omówienie usługi Azure agenta maszyny wirtualnej

Agent maszyny wirtualnej programu Microsoft Azure (Agent maszyny Wirtualnej) jest procesem zabezpieczonych, lekkie, który zarządza wirtualna interakcji z kontrolerem sieci szkieletowej Azure. Agent maszyny Wirtualnej ma podstawową rolą włączenie i wykonywania rozszerzenia maszyny wirtualnej platformy Azure. Włączanie rozszerzenia maszyny Wirtualnej po konfiguracji wdrożenia maszyn wirtualnych, takie jak instalowanie i konfigurowanie oprogramowania. Rozszerzenia maszyn wirtualnych również włączyć funkcje odzyskiwania, np. zresetowania hasła administracyjnego maszyny wirtualnej. Bez agenta maszyny Wirtualnej Azure nie można uruchomić rozszerzenia maszyny wirtualnej.

Ten dokument zawiera szczegóły dotyczące instalacji, wykrywania i usuwania agenta maszyny wirtualnej Azure.

## <a name="install-the-vm-agent"></a>Zainstaluj agenta maszyny Wirtualnej

### <a name="azure-gallery-image"></a>Obraz w galerii Azure

Agent maszyny Wirtualnej Azure jest instalowany domyślnie wdrażana z obrazu galerii Azure maszyny wirtualnej systemu Windows. Podczas wdrażania obrazu galerii Azure z portalu, programu PowerShell, interfejsu wiersza polecenia lub szablonu usługi Azure Resource Manager, jest również instalowany Agent maszyny Wirtualnej Azure. 

### <a name="manual-installation"></a>Instalacja ręczna

Agent maszyny Wirtualnej systemu Windows można ręcznie zainstalować za pomocą pakietu Instalatora Windows. Instalacja ręczna może być konieczne, podczas tworzenia obrazu niestandardowego maszyny wirtualnej, który zostanie wdrożony na platformie Azure. Aby ręcznie zainstalować agenta maszyny Wirtualnej systemu Windows, Pobierz Instalator agenta maszyny Wirtualnej z tej lokalizacji [Pobierz agenta maszyny Wirtualnej systemu Windows Azure](http://go.microsoft.com/fwlink/?LinkID=394789). 

Agent maszyny Wirtualnej mogą być instalowane przez dwukrotne kliknięcie pliku Instalatora windows. Automatyczna lub z instalacji nienadzorowanej instalacji agenta maszyny Wirtualnej uruchom następujące polecenie.

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Wykryj agenta maszyny Wirtualnej

### <a name="powershell"></a>PowerShell

Moduł programu PowerShell usługi Azure Resource Manager można pobrać informacji o maszynach wirtualnych platformy Azure. Uruchomiona `Get-AzureRmVM` zwraca dość nieco informacji w tym stan inicjowania obsługi administracyjnej dla agenta maszyny Wirtualnej Azure.

```PowerShell
Get-AzureRmVM
```

Poniżej znajduje się tylko ich podzbiór `Get-AzureRmVM` danych wyjściowych. Powiadomienie `ProvisionVMAgent` zagnieżdżona właściwość `OSProfile`, ta właściwość służy do określenia, czy agent maszyny Wirtualnej został wdrożony do maszyny wirtualnej.

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : muUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Poniższy skrypt może służyć do zwrócenia listę krótkie nazwy maszyn wirtualnych i stan agenta maszyny Wirtualnej.

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Ręczne wykrywania

Po zalogowaniu się do maszyny Wirtualnej systemu Windows Azure, Menedżer zadań może służyć do sprawdzenia uruchomionych procesów. Aby sprawdzić, czy Agent maszyny Wirtualnej Azure, otwórz Menedżera zadań > kliknij kartę szczegółów, a następnie wyszukaj nazwę procesu `WindowsAzureGuestAgent.exe`. Obecność tego procesu wskazuje, czy agent maszyny Wirtualnej jest zainstalowany.

## <a name="upgrade-the-vm-agent"></a>Uaktualnienie agenta maszyny Wirtualnej

Azure VM Agent dla systemu Windows zostanie automatycznie uaktualniony. Podczas wdrażania nowych maszyn wirtualnych na platformie Azure, otrzymają najnowsza wersja agenta maszyny Wirtualnej. Niestandardowe obrazy maszyny Wirtualnej należy ręcznie zaktualizować Aby dołączyć nowy agent maszyny Wirtualnej.
