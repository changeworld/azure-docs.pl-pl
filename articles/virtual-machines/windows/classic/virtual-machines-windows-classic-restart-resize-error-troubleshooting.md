---
title: Maszyny Wirtualnej, ponownego uruchamiania lub zmiana rozmiaru problemów | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów wdrożenie klasyczne z ponownego uruchamiania lub zmiana rozmiaru istniejącej maszyny wirtualnej systemu Windows na platformie Azure
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
ms.date: 11/03/2017
ms.devlang: na
ms.author: delhan
ms.openlocfilehash: bed5da25042d29983bad9a80cd44bdd7df261c2e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23989298"
---
# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Rozwiązywanie problemów wdrożenie klasyczne z ponownego uruchamiania lub zmiana rozmiaru istniejącej maszyny wirtualnej systemu Windows na platformie Azure
> [!div class="op_single_selector"]
> * [Wdrożenie klasyczne](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
> * [Resource Manager](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> 
> 

Podczas uruchamiania zatrzymanej maszyny wirtualnej Azure (VM), lub zmień rozmiar istniejącej maszyny Wirtualnej Azure, wystąpią typowych błędów jest błąd alokacji. Ten błąd powoduje klastra lub regionie nie ma dostępu do zasobów lub nie może obsługiwać żądany rozmiar maszyny Wirtualnej.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md).  Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> 
> 

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Dzienniki inspekcji zbieranie
Zacząć Rozwiązywanie problemów, zbierz dzienniki inspekcji, aby określić błąd skojarzone z problem.

W portalu Azure kliknij **Przeglądaj** > **maszyn wirtualnych** > *maszyny wirtualnej systemu Windows* > **ustawienia** > **dzienniki inspekcji**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problem: Błąd podczas uruchamiania zatrzymanej maszyny Wirtualnej
Próby uruchomienia zatrzymanej maszyny Wirtualnej, ale awaria alokacji.

### <a name="cause"></a>Przyczyna
Żądania uruchomienia zatrzymanej maszyny Wirtualnej musi podjąć w oryginalnego klastra, który jest hostem usługi w chmurze. Jednak klaster nie ma wolnego miejsca do spełnienia żądania.

### <a name="resolution"></a>Rozwiązanie
* Utwórz nową usługę w chmurze i skojarzyć go z jednego regionu lub sieci wirtualnej region, ale nie grupy koligacji.
* Usuń zatrzymanej maszyny Wirtualnej.
* Utwórz ponownie maszynę Wirtualną w nowej usługi w chmurze przy użyciu dysków.
* Uruchom ponownie utworzyć maszynę Wirtualną.

Jeśli wystąpi błąd podczas próby utworzenia nowej usługi w chmurze, spróbuj ponownie później lub zmienić regionu dla usługi w chmurze.

> [!IMPORTANT]
> Nowa usługa w chmurze będzie miała nowej nazwy i adresu VIP, dlatego należy zmieniać te informacje dla wszystkich zależności, korzystających z tych informacji do istniejącej usługi w chmurze.
> 
> 

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problem: Błąd podczas zmiany rozmiaru istniejącej maszyny Wirtualnej
Spróbuj zmienić rozmiar istniejącej maszyny Wirtualnej, ale jest wyświetlany błąd alokacji.

### <a name="cause"></a>Przyczyna
Żądanie zmiany rozmiaru maszyny Wirtualnej musi podjąć w oryginalnego klastra, który jest hostem usługi w chmurze. Klaster nie obsługuje jednak żądany rozmiar maszyny Wirtualnej.

### <a name="resolution"></a>Rozwiązanie
Zmniejsz żądany rozmiar maszyny Wirtualnej i ponów żądanie zmiany rozmiaru.

* Kliknij przycisk **Przeglądaj wszystkie** > **maszyn wirtualnych (klasyczne)** > *maszyny wirtualnej* > **ustawienia**  >  **Rozmiar**. Aby uzyskać szczegółowe instrukcje, zobacz [zmienić rozmiaru maszyny wirtualnej](https://msdn.microsoft.com/library/dn168976.aspx).

Jeśli nie jest możliwe zmniejszenie rozmiaru maszyny Wirtualnej, wykonaj następujące kroki:

* Utwórz nową usługę w chmurze, zapewniając jest nie jest połączony z grupą koligacji i nie jest skojarzona z siecią wirtualną, która jest połączona z grupą koligacji.
* Utwórz nowy, o większym rozmiarze maszyny Wirtualnej w nim.

Umożliwiającej obsługę wszystkich maszyn wirtualnych w tej samej usłudze w chmurze. Jeśli istniejącą usługę w chmurze jest skojarzone z sieci wirtualnej na podstawie regionu, możesz połączyć nową usługę w chmurze do istniejącej sieci wirtualnej.

Jeśli istniejącą usługę w chmurze nie jest powiązana z siecią wirtualną regionu, następnie należy usunąć maszyn wirtualnych w istniejącej usługi w chmurze i utwórz je ponownie w nowej usługi w chmurze z ich dysków. Jest jednak należy pamiętać, że nowa usługa w chmurze będzie są nowej nazwy i adresu VIP, więc należy zaktualizować te zależności, których te informacje jest obecnie używana dla istniejącej usługi w chmurze.

## <a name="next-steps"></a>Kolejne kroki
Jeśli wystąpią problemy podczas tworzenia maszyny Wirtualnej systemu Windows na platformie Azure, zobacz [Rozwiązywanie problemów dotyczących wdrożenia z tworzenia maszyny wirtualnej systemu Windows na platformie Azure](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

