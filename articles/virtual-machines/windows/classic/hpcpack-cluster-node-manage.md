---
title: Zarządzanie węzły obliczeniowe klastra HPC Pack | Dokumentacja firmy Microsoft
description: Więcej informacji na temat narzędzia skryptu programu PowerShell do dodawania, usuwania, uruchomić i zatrzymać węzły obliczeniowe klastra HPC Pack 2012 R2 na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 4193f03b-94e9-4704-a7ad-379abde063a9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 453f53be15b24b96f183b4935cc45fc97ad058bd
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Zarządzanie liczbą i dostępnością węzłów obliczeniowych w klastrze pakietu HPC Pack na platformie Azure
Jeśli utworzony klaster HPC Pack 2012 R2 w maszynach wirtualnych platformy Azure, możesz sposobów, aby łatwo dodać, usunąć, start (zainicjować obsługi administracyjnej) lub zatrzymać (deprovision) obliczeniowe niektórych maszyn wirtualnych węzła w klastrze. Aby wykonać te zadania, uruchomić skrypty programu Azure PowerShell, które są zainstalowane w węźle głównym maszyny Wirtualnej. Skrypty te można kontrolować liczbę i dostępności zasobów klastra HPC Pack, więc można kontrolować koszty.

> [!IMPORTANT] 
> Ten artykuł dotyczy tylko w klastrach HPC Pack 2012 R2 na platformie Azure utworzonych przy użyciu klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> Ponadto skrypty programu PowerShell opisanych w tym artykule nie są dostępne w HPC Pack 2016.

## <a name="prerequisites"></a>Wymagania wstępne
* **Klaster HPC Pack 2012 R2 w maszynach wirtualnych platformy Azure**: Tworzenie klastra HPC Pack 2012 R2 w klasycznym modelu wdrażania. Na przykład można zautomatyzować wdrożenie przy użyciu obrazu HPC Pack 2012 R2 z maszyny Wirtualnej w portalu Azure Marketplace i skrypt programu PowerShell systemu Azure. Aby uzyskać informacje i wymagania wstępne, zobacz [utworzyć klaster HPC z skrypt wdrożenia HPC Pack IaaS](hpcpack-cluster-powershell-script.md).
  
    Po wdrożeniu odnaleźć węzła zarządzania skryptów w folderze % CCP\_folder bin % głównej w węźle głównym. Uruchamianie wszystkich skryptów jako administrator.
* **Certyfikat zarządzania lub pliku ustawień publikowania na platformie Azure**: należy wykonać jedną z następujących czynności w węźle głównym:
  
  * **Plik ustawień publikowania importu platformy Azure**. Aby to zrobić, uruchom następujące polecenia cmdlet programu Azure PowerShell w węźle głównym:
    
    ```PowerShell
    Get-AzurePublishSettingsFile
    
    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```
  * **Konfigurowanie certyfikatu zarządzania platformy Azure w węźle głównym**. Jeśli plik cer, zaimportuj go w magazynie certyfikatów CurrentUser\My, a następnie uruchom następujące polecenie cmdlet programu Azure PowerShell do środowiska Azure (AzureCloud lub AzureChinaCloud):
    
    ```PowerShell
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Dodaj węzeł obliczeniowy maszyny wirtualne
Dodaj węzły obliczeniowe z **HpcIaaSNode.ps1 Dodaj** skryptu.

### <a name="syntax"></a>Składnia
```PowerShell
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Parametry
* **ServiceName**: Nazwa usługi w chmurze, która obliczeniowe nowego węzła maszyny wirtualne są dodawane do.
* **Nazwa_obrazu**: Nazwa obrazu maszyny Wirtualnej platformy Azure, który można uzyskać za pośrednictwem portalu Azure lub polecenia cmdlet programu Azure PowerShell **Get-AzureVMImage**. Obraz musi spełniać następujące wymagania:
  
  1. Musi być zainstalowany w systemie operacyjnym Windows.
  2. HPC Pack musi być zainstalowany w roli węzła obliczeń.
  3. Obraz musi być prywatny obrazu w kategorii użytkownika nie publicznego obrazu maszyny Wirtualnej platformy Azure.
* **Ilość**: numer węzła obliczeniowego maszyn wirtualnych ma zostać dodana.
* **InstanceSize**: rozmiar węzła obliczeniowego maszyn wirtualnych.
* **DomainUserName**: nazwa użytkownika domeny, który jest używany do dołączania nowych maszyn wirtualnych do domeny.
* **DomainUserPassword**: hasło użytkownika domeny.
* **NodeNameSeries** (opcjonalnie): nazewnictwa wzorca dla węzłów obliczeniowych. Musi mieć format &lt; *głównego\_nazwa*&gt;&lt;*Start\_numer*&gt;%. Na przykład MyCN % 10% oznacza serii od MyCN11 nazwy węzłów obliczeniowych. Jeśli nie zostanie określony, skrypt używa skonfigurowanego węzeł nazwy serii w klastrze HPC.

### <a name="example"></a>Przykład
W poniższym przykładzie dodano 20 węzła obliczeń duży rozmiar maszyn wirtualnych w usłudze w chmurze *hpcservice1*, oparte na obrazie maszyny Wirtualnej *hpccnimage1*.

```PowerShell
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Usuń węzeł obliczeniowy maszyny wirtualne
Usuń węzły obliczeniowe z **HpcIaaSNode.ps1 Usuń** skryptu.

### <a name="syntax"></a>Składnia
```PowerShell
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Parametry
* **Nazwa**: nazwy węzłów klastra ma zostać usunięty. Symbole wieloznaczne są obsługiwane. Nazwa zestawu parametru jest nazwa. Nie można określić zarówno **nazwa** i **węzła** parametrów.
* **Węzeł**: obiekt HpcNode dla węzłów do usunięcia, które można uzyskać za pomocą polecenia cmdlet środowiska PowerShell klastra HPC [błędzie Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Nazwa zestawu parametrów jest węzeł. Nie można określić zarówno **nazwa** i **węzła** parametrów.
* **DeleteVHD** (opcjonalnie): ustawienie, aby usunąć skojarzone dyski dla maszyn wirtualnych, które zostały usunięte.
* **Wymuś** (opcjonalnie): ustawienie, aby wymusić węzłów HPC w trybie offline przed ich usunięciem.
* **Potwierdź** (opcjonalnie): monitu o potwierdzenie przed wykonaniem polecenia.
* **WhatIf**: ustawienie, aby opisano, co się stanie, jeśli polecenie zostanie wykonane bez rzeczywistego wykonania polecenia.

### <a name="example"></a>Przykład
Poniższy przykład wymusza w trybie offline węzłach o nazwach od *HPCNode-CN -* i usuwa je węzły i ich skojarzone dyski.

```PowerShell
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Węzeł obliczeń początkowy maszyny wirtualne
Start obliczeniowe węzłów o **Start HpcIaaSNode.ps1** skryptu.

### <a name="syntax"></a>Składnia
```PowerShell
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Parametry
* **Nazwa**: nazwy węzłów klastra ma zostać uruchomiony. Symbole wieloznaczne są obsługiwane. Nazwa zestawu parametru jest nazwa. Nie można określić zarówno **nazwa** i **węzła** parametrów.
* **Węzeł**-HpcNode obiektu dla węzłów, które ma zostać uruchomiony, które można uzyskać za pomocą polecenia cmdlet środowiska PowerShell klastra HPC [błędzie Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Nazwa zestawu parametrów jest węzeł. Nie można określić zarówno **nazwa** i **węzła** parametrów.

### <a name="example"></a>Przykład
Węzły w poniższym przykładzie rozpoczyna się od nazwy rozpoczynające się *HPCNode-CN -*.

```PowerShell
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Zatrzymaj węźle obliczeń maszyny wirtualne
Zatrzymaj węzły obliczeniowe z **Stop HpcIaaSNode.ps1** skryptu.

### <a name="syntax"></a>Składnia
```PowerShell
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Parametry
* **Nazwa**-nazwy węzłów klastra, aby zostać zatrzymane. Symbole wieloznaczne są obsługiwane. Nazwa zestawu parametru jest nazwa. Nie można określić zarówno **nazwa** i **węzła** parametrów.
* **Węzeł**: obiekt HpcNode dla węzłów, które ma zostać zatrzymany, których można uzyskać za pomocą polecenia cmdlet środowiska PowerShell klastra HPC [błędzie Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Nazwa zestawu parametrów jest węzeł. Nie można określić zarówno **nazwa** i **węzła** parametrów.
* **Wymuś** (opcjonalnie): ustawienie, aby wymusić węzłów HPC w trybie offline przed zatrzymaniem je.

### <a name="example"></a>Przykład
Poniższy przykład wymusza węzłów w trybie offline z nazwami od *HPCNode-CN -* , a następnie zatrzymuje węzłów.

```PowerShell
Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force
```

## <a name="next-steps"></a>Kolejne kroki
* Aby automatycznie zwiększać i zmniejszać węzłów klastra, zgodnie z bieżące obciążenie zadań i zadań w klastrze, zobacz [automatycznie zwiększyć lub zmniejszyć zasobów klastra HPC Pack na platformie Azure zgodnie z obciążenie klastra](hpcpack-cluster-node-autogrowshrink.md).

