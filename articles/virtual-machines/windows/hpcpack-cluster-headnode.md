---
title: Utwórz węzłem głównym HPC Pack w maszynie Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać portalu Azure i modelu wdrażania usługi Resource Manager można utworzyć węzła głównego Microsoft HPC Pack 2012 R2 w maszynie Wirtualnej platformy Azure.
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
ms.openlocfilehash: acd4cd44dd35a5b1755d9456f683076567d62165
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915263"
---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Tworzenie węzła głównego klastra pakietu HPC Pack na maszynie wirtualnej platformy Azure przy użyciu obrazu portalu Marketplace
Użyj [obraz maszyny wirtualnej Microsoft HPC Pack 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) z portalu Azure Marketplace i portalu Azure, można utworzyć węzła głównego klastra HPC. Tego obrazu maszyny Wirtualnej programu HPC Pack jest oparta na systemie Windows Server 2012 R2 Datacenter z preinstalowanym HPC Pack 2012 R2 Update 3. Użyj tego węzła głównego do weryfikacji koncepcji wdrożenia pakietu HPC na platformie Azure. Następnie można dodać węzłów obliczeniowych do klastra do uruchamiania obciążeń HPC.

> [!TIP]
> Do wdrożenia całego klastra HPC Pack 2012 R2 na platformie Azure, zawierającą węzła głównego i węzły obliczeniowe, zaleca się, że używasz zautomatyzowaną metodę. Dostępne są następujące opcje [skrypt wdrożenia HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) i szablony Menedżera zasobów, takich jak [klastra HPC Pack dla systemu Windows obciążeń](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Dostępne są również szablony Menedżera zasobów [klastrów Microsoft HPC Pack 2016](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates). 
> 
> 

## <a name="planning-considerations"></a>Zagadnienia dotyczące planowania
Jak pokazano na poniższej ilustracji, możesz wdrożyć węzłem głównym HPC Pack w domenie usługi Active Directory w sieci wirtualnej platformy Azure.

![HPC Pack węzła głównego][headnode]

* **Domena usługi Active Directory**: HPC Pack 2012 R2 węzła głównego musi być przyłączony do domeny usługi Active Directory na platformie Azure, przed rozpoczęciem HPC usług na maszynie Wirtualnej. Jak pokazano w tym artykule, weryfikacja koncepcji wdrożenia, możesz podwyższyć poziom maszyny Wirtualnej można utworzyć węzła głównego jako kontroler domeny przed rozpoczęciem usług HPC. Innym rozwiązaniem jest wdrażanie kontrolera osobnej domenie i lesie na platformie Azure, do którego dołączyć węzła głównego maszyny Wirtualnej.

* **Model wdrażania**: dla większości nowych wdrożeń, firma Microsoft zaleca użycie modelu wdrażania usługi Resource Manager. W tym artykule przyjęto założenie, że używasz tego modelu wdrażania.

* **Sieć wirtualna platformy Azure**: gdy modelu wdrażania usługi Resource Manager umożliwia wdrażanie węzła głównego, określ, lub Utwórz sieć wirtualną platformy Azure. Sieć wirtualna jest używane, jeśli należy przyłączyć do istniejącej domeny usługi Active Directory węzła głównego. Należy również go później, aby dodać węzeł obliczeniowy maszyn wirtualnych do klastra.

## <a name="steps-to-create-the-head-node"></a>Kroki umożliwiające utworzenie węzła głównego
Poniżej przedstawiono ogólne kroki, aby utworzyć maszyny Wirtualnej platformy Azure dla węzła głównego HPC Pack przy użyciu modelu wdrażania usługi Resource Manager za pomocą portalu Azure. 

1. Jeśli chcesz utworzyć nowy las usługi Active Directory na platformie Azure z kontrolerem domeny w oddzielnych maszyn wirtualnych, co opcją jest użycie [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc). Proste weryfikacji koncepcji wdrożenia jest poprawnie pominąć ten krok i skonfigurować węzła głównego samej maszyny Wirtualnej jako kontroler domeny. Ta opcja jest opisane w dalszej części tego artykułu.
2. Na [HPC Pack 2012 R2 w witrynie Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) w portalu Azure Marketplace kliknij **Utwórz maszynę wirtualną**. 
3. W portalu na **HPC Pack 2012 R2 w systemie Windows Server 2012 R2** wybierz pozycję **Resource Manager** model wdrażania, a następnie kliknij przycisk **Utwórz**.
   
    ![HPC Pack obrazu][marketplace]
4. Skonfiguruj ustawienia, a następnie utworzyć maszynę Wirtualną za pomocą portalu. Jeśli jesteś nowym użytkownikiem usługi Azure, wykonaj samouczka [Utwórz maszynę wirtualną systemu Windows w portalu Azure](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Do weryfikacji koncepcji wdrożenia zazwyczaj można zaakceptować ustawienie domyślne lub zalecanych ustawień.
   
   > [!NOTE]
   > Jeśli chcesz przyłączyć węzła głównego do istniejącej domeny usługi Active Directory na platformie Azure, upewnij się, że zostanie sieci wirtualnej dla tej domeny podczas tworzenia maszyny Wirtualnej.
   > 
   > 
5. Po utworzenia maszyny Wirtualnej i maszyna wirtualna jest uruchomiona, [połączenie z maszyną Wirtualną](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przez pulpit zdalny. 
6. Dołącz maszynę Wirtualną do lasu usługi Active Directory w domenie, wybierając jedną z następujących opcji:
   
   * Jeśli maszyna wirtualna została utworzona w sieci wirtualnej platformy Azure z istniejącym lesie domeny, Dołącz maszynę Wirtualną do lasu przy użyciu standardowych narzędzi Menedżera serwera lub środowiska Windows PowerShell. Następnie uruchom ponownie.
   * Jeśli maszyna wirtualna została utworzona w nowej sieci wirtualnej (bez istniejącego lasu domeny), podwyższyć poziom maszyny Wirtualnej, jako kontroler domeny. Wykonaj kroki standard, aby zainstalować i skonfigurować rolę usług domenowych w usłudze Active Directory w węźle głównym. Aby uzyskać szczegółowe instrukcje, zobacz [instalowania nowego lasu systemu Windows Server 2012 Active Directory](https://technet.microsoft.com/library/jj574166.aspx).
7. Maszyna wirtualna działa i jest dołączony do lasu usługi Active Directory, uruchamiania usług HPC Pack w następujący sposób:
   
    a. Połączenie z węzłem głównym maszyny Wirtualnej przy użyciu konta domeny, które jest członkiem lokalnej grupy administratorów. Na przykład użyć konta administratora, które można skonfigurować podczas tworzenia węzła głównego maszyny Wirtualnej.
   
    b. Dla domyślnej konfiguracji węzła głównego Uruchom program Windows PowerShell jako administrator i wpisz następujące polecenie:
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    Może upłynąć kilka minut na uruchomienie usług HPC Pack.
   
    Dla węzła głównego dodatkowe opcje konfiguracji, wpisz `get-help HPCHNPrepare.ps1`.

## <a name="next-steps"></a>Kolejne kroki
* Teraz możesz pracować z węzła głównego klastra HPC Pack. Na przykład, uruchom Menedżera klastra HPC i ukończyć [listy zadań do wykonania wdrożenia](https://technet.microsoft.com/library/jj884141.aspx).
* Jeśli chcesz zwiększyć klastra obliczeniowego pojemności na żądanie, Dodaj [Azure serii węzłów](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) w usłudze w chmurze. 
* Spróbuj uruchomić test obciążenia w klastrze. Na przykład zobacz HPC Pack [Wprowadzenie — przewodnik](https://technet.microsoft.com/library/jj884144).

<!--Image references-->
[headnode]: ./media/hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/hpcpack-cluster-headnode/marketplace.png
