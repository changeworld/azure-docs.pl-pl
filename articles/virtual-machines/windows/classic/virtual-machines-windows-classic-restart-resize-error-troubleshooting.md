---
title: Maszyna wirtualna, ponownego uruchamiania lub zmieniania jej rozmiaru problemów | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z problemy z klasycznym wdrożeniem przy ponownym uruchamianiu lub zmiany rozmiaru istniejącej maszyny wirtualnej Windows na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: aa854fff-c057-4b8e-ad77-e4dbc39648cc
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: required
ms.date: 06/15/2018
ms.devlang: na
ms.author: delhan
ms.openlocfilehash: 400b20e474257650a22e04c89ddde581bf0552f4
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091734"
---
# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Rozwiązywanie problemów z problemy z klasycznym wdrożeniem przy ponownym uruchamianiu lub zmiany rozmiaru istniejącej maszyny wirtualnej Windows na platformie Azure
> [!div class="op_single_selector"]
> * [Wdrożenie klasyczne](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
> * [Resource Manager](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> 
> 

Podczas próby uruchomienia zatrzymania maszyny wirtualnej platformy Azure (VM) lub zmienić rozmiar istniejącej maszyny Wirtualnej platformy Azure typowym błędem, które wystąpią jest wystąpił błąd alokacji. Ten błąd rezultatem klastra lub regionie nie ma dostępnych zasobów albo nie obsługuje żądanego rozmiaru maszyny Wirtualnej.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md).  Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> 
> 

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Dzienniki inspekcji zbieranie
Aby rozpocząć rozwiązywanie problemów, należy zebrać dzienniki inspekcji, aby zidentyfikować ten błąd, skojarzone z emisją.

W witrynie Azure portal kliknij pozycję **Przeglądaj** > **maszyn wirtualnych** > *maszynę wirtualną Windows*  >   **Ustawienia** > **dzienniki inspekcji**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problem: Błąd podczas uruchamiania zatrzymanej maszyny Wirtualnej
Spróbuj uruchomić zatrzymanej maszyny Wirtualnej, ale wystąpić błąd alokacji.

### <a name="cause"></a>Przyczyna
Żądania uruchomienia zatrzymanej maszyny Wirtualnej musi nastąpić w oryginalnym klastrze, który hostuje usługę w chmurze. Klaster nie ma jednak wolne miejsce dostępne do spełnienia żądania.

### <a name="resolution"></a>Rozwiązanie
* Utwórz nową usługę w chmurze i skojarz go z oboma, region lub oparte na region sieci wirtualnej, ale nie grupy koligacji.
* Usuwanie zatrzymania maszyny Wirtualnej.
* Ponowne utworzenie maszyny Wirtualnej w nowej usługi w chmurze przy użyciu dysków.
* Uruchom ponownie utworzyć maszynę Wirtualną.

Jeśli wystąpi błąd podczas próby utworzenia nowej usługi w chmurze, spróbuj ponownie później albo zmień region usługi w chmurze.

> [!IMPORTANT]
> Nową usługę w chmurze odniesie nowej nazwy i adresu VIP, więc musisz zmienić te informacje dla wszystkich zależności, które używają tych informacji dla istniejącej usługi w chmurze.
> 
> 

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problem: Błąd podczas zmiany rozmiaru istniejącej maszyny Wirtualnej
Spróbuj zmienić rozmiar istniejącej maszyny Wirtualnej, ale wystąpić błąd alokacji.

### <a name="cause"></a>Przyczyna
Żądanie, aby zmienić rozmiar maszyny Wirtualnej musi nastąpić w oryginalnym klastrze, który hostuje usługę w chmurze. Jednak klastra nie obsługuje żądanego rozmiaru maszyny Wirtualnej.

### <a name="resolution"></a>Rozwiązanie
Zmniejsz żądany rozmiar maszyny Wirtualnej, a następnie ponów próbę wykonania żądania zmiany rozmiaru.

* Kliknij przycisk **Przeglądaj wszystko** > **maszyny wirtualne (klasyczne)** > *maszyny wirtualnej* > **ustawienia**  >  **Rozmiar**. Aby uzyskać szczegółowe instrukcje, zobacz [rozmiaru maszyny wirtualnej](https://msdn.microsoft.com/library/dn168976.aspx).

Jeśli nie jest możliwe zmniejszenie rozmiaru maszyny Wirtualnej, wykonaj następujące kroki:

* Utwórz nową usługę w chmurze, zapewniając jest niepołączona do grupy koligacji i nie jest skojarzona z sieci wirtualnej, która jest połączona z grupą koligacji.
* Utwórz nową, o większym rozmiarze maszyny Wirtualnej w nim.

Można skonsolidować wszystkich maszyn wirtualnych w tej samej usłudze w chmurze. Istniejącą usługę w chmurze jest skojarzone z sieci wirtualnej na podstawie regionu, można połączyć z nowej usługi w chmurze do istniejącej sieci wirtualnej.

Jeśli istniejącej usługi w chmurze nie jest skojarzony z sieci wirtualnej na podstawie regionie, musisz usunąć maszyny wirtualne w istniejącej usługi w chmurze i utwórz je ponownie w nowej usługi w chmurze z ich dysków. Jest jednak pamiętać, że nową usługę w chmurze będzie miała nowej nazwy i adresu VIP, dlatego należy zaktualizować je wszystkie zależności, które aktualnie używają tych informacji istniejącej usługi w chmurze.

## <a name="next-steps"></a>Kolejne kroki
Jeśli wystąpią problemy podczas tworzenia maszyny Wirtualnej z systemem Windows na platformie Azure, zobacz [Rozwiązywanie problemów dotyczących wdrażania za pomocą tworzenia maszyny wirtualnej Windows na platformie Azure](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

