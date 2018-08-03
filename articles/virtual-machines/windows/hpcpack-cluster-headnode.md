---
title: Utwórz węzeł główny HPC Pack w Maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć węzłem Microsoft HPC Pack 2012 R2 w Maszynie wirtualnej platformy Azure za pomocą witryny Azure portal i modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,hpc-pack
ms.assetid: e6a13eaf-9124-47b4-8d75-2bc4672b8f21
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 70c1d95f704315ee6a481367188e2bb620916702
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428960"
---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Tworzenie węzła głównego klastra pakietu HPC Pack na maszynie wirtualnej platformy Azure przy użyciu obrazu portalu Marketplace
Użyj [obrazu maszyny wirtualnej Microsoft HPC Pack 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) z portalu Azure Marketplace i witryny Azure portal, aby utworzyć węzłem głównym klastra HPC. Ten obraz maszyny Wirtualnej pakietu HPC Pack jest oparty na Windows Server 2012 R2 Datacenter z preinstalowanym HPC Pack 2012 R2 Update 3. Użyj tego węzła głównego w celu weryfikacji koncepcji wdrożenia pakietu HPC Pack na platformie Azure. Następnie można dodać węzłów obliczeniowych w klastrze do uruchamiania obciążeń HPC.

> [!TIP]
> Do wdrożenia całego klastra pakietu HPC Pack 2012 R2 na platformie Azure, która zawiera węzeł główny i węzły obliczeniowe, zaleca się, że używasz zautomatyzowanej metody. Opcje obejmują [skryptem wdrażania IaaS pakietu HPC Pack](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) i szablonów usługi Resource Manager, takie jak [klastra pakietu HPC Pack na potrzeby obciążeń Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Szablony usługi Resource Manager są również dostępne dla [Microsoft HPC Pack 2016 klastry](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates). 
> 
> 

## <a name="planning-considerations"></a>Zagadnienia dotyczące planowania
Jak pokazano na poniższej ilustracji, możesz wdrożyć węzeł główny HPC Pack w domenie usługi Active Directory w sieci wirtualnej platformy Azure.

![Węzeł główny HPC Pack][headnode]

* **Domena usługi Active Directory**: HPC Pack 2012 R2 węzeł główny musi być przyłączony do domeny usługi Active Directory na platformie Azure, przed rozpoczęciem HPC usług na maszynie Wirtualnej. Jak pokazano w tym artykule w celu weryfikacji koncepcji wdrożenia, możesz podwyższyć poziom maszyny Wirtualnej, można utworzyć węzła głównego jako kontroler domeny przed rozpoczęciem usług HPC. Innym rozwiązaniem jest, aby wdrożyć kontroler oddzielne domeny i lasu w systemie Azure, do której możesz dołączyć do węzła głównego maszyny Wirtualnej.

* **Model wdrażania**: dla większości nowych wdrożeń, firma Microsoft zaleca użycie modelu wdrażania usługi Resource Manager. W tym artykule założono, że używasz tego modelu wdrażania.

* **Usługa Azure virtual network**: modelu wdrażania usługi Resource Manager umożliwia wdrażanie węzła głównego, możesz określić tworzyć lub siecią wirtualną platformy Azure. Możesz użyć sieci wirtualnej, jeśli potrzebujesz do dołączenia do węzła głównego do istniejącej domeny usługi Active Directory. Należy również je później do dodania węzła obliczeniowego maszyn wirtualnych do klastra.

## <a name="steps-to-create-the-head-node"></a>Kroki, aby utworzyć węzeł główny
Poniżej przedstawiono ogólne kroki, aby utworzyć Maszynę wirtualną platformy Azure za węzeł główny HPC Pack za pomocą modelu wdrażania usługi Resource Manager za pomocą witryny Azure portal. 

1. Jeśli chcesz utworzyć nowy las usługi Active Directory na platformie Azure przy użyciu maszyn wirtualnych kontrolerów domeny w oddzielnych, jedną z opcji jest użycie [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc). Proste weryfikacji koncepcji wdrożenia jest dobrym rozwiązaniem pominąć ten krok i skonfigurować węzłem samą maszynę Wirtualną jako kontroler domeny. Ta opcja jest opisane w dalszej części tego artykułu.
1. Na [HPC Pack 2012 R2 w witrynie Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) w portalu Azure Marketplace kliknij **Utwórz maszynę wirtualną**. 
1. W portalu na **HPC Pack 2012 R2 w systemie Windows Server 2012 R2** wybierz opcję **usługi Resource Manager** modelu wdrażania, a następnie kliknij przycisk **Utwórz**.
   
    ![Obraz pakietu HPC Pack][marketplace]
1. Aby skonfigurować ustawienia, a następnie utwórz maszynę Wirtualną za pomocą portalu. Jeśli jesteś nowym użytkownikiem platformy Azure, należy wykonać kroki samouczka [Utwórz maszynę wirtualną Windows w witrynie Azure portal](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). W celu weryfikacji koncepcji wdrożenia zazwyczaj można zaakceptować wartości domyślne lub zalecanych ustawień.
   
   > [!NOTE]
   > Jeśli chcesz dołączyć węzła głównego do istniejącej domeny usługi Active Directory na platformie Azure, upewnij się, że można określić sieci wirtualnej dla tej domeny, podczas tworzenia maszyny Wirtualnej.
   > 
   > 
1. Po utworzenia maszyny Wirtualnej i maszyna wirtualna jest uruchomiona, [nawiązać połączenie z maszyną Wirtualną](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przez pulpit zdalny. 
1. Przyłącz ją do lasu usługi Active Directory w domenie, wybierając jedną z następujących opcji:
   
   * Jeśli utworzono maszynę Wirtualną w sieci wirtualnej platformy Azure z istniejącym lesie domeny, przyłącz ją do lasu przy użyciu standardowych narzędzi Menedżera serwera lub środowiska Windows PowerShell. Następnie uruchom ponownie.
   * W przypadku utworzenia maszyny Wirtualnej w nowej sieci wirtualnej (bez istniejącego lasu domeny), podwyższanie poziomu maszyny Wirtualnej, co kontroler domeny. Wykonaj standardowe kroki, aby zainstalować i skonfigurować rolę Active Directory Domain Services w węźle głównym. Aby uzyskać szczegółowe instrukcje, zobacz [zainstaluj nowy systemu Windows Server 2012 las usługi Active Directory](https://technet.microsoft.com/library/jj574166.aspx).
1. Po maszyny Wirtualnej jest uruchomiony i jest dołączony do lasu usługi Active Directory, uruchomić usług pakietu HPC Pack w następujący sposób:
   
    a. Łączenie z węzłem głównym maszyny Wirtualnej przy użyciu konta domeny, które jest członkiem lokalnej grupy administratorów. Na przykład użyć konta administratora, które można skonfigurować podczas tworzenia maszyny Wirtualnej węzła głównego.
   
    b. Dla domyślnej konfiguracji węzła głównego Uruchom program Windows PowerShell jako administrator i wpisz następujące polecenie:
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    Może potrwać kilka minut, zanim uruchomienie usług pakietu HPC Pack.
   
    Dla opcji konfiguracji dodatkowego węzła głównego, wpisz `get-help HPCHNPrepare.ps1`.

## <a name="next-steps"></a>Kolejne kroki
* Teraz możesz pracować z węzła głównego klastra pakietu HPC Pack. Na przykład uruchomić Menedżer klastrów HPC i ukończyć [listy zadań do wykonania wdrożenia](https://technet.microsoft.com/library/jj884141.aspx).
* Jeśli chcesz zwiększyć klastra obliczeniowego pojemności na żądanie, należy dodać [węzłów poszerzenie Azure](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) w usłudze w chmurze. 
* Spróbuj uruchomić test obciążenia w klastrze. Aby uzyskać przykład, zobacz pakietu HPC Pack [przewodnik wprowadzenie](https://technet.microsoft.com/library/jj884144).

<!--Image references-->
[headnode]: ./media/hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/hpcpack-cluster-headnode/marketplace.png
