---
title: Problemy z maszyny Wirtualnej, ponownego uruchamiania lub zmiana rozmiaru na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów wdrożenia usługi Resource Manager z ponownego uruchamiania lub zmiana rozmiaru istniejącej maszyny wirtualnej systemu Windows na platformie Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: Deland-Han
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.workload: required
ms.date: 11/03/2017
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 482a194535cba8715b70ecff4679691515b206a9
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2018
ms.locfileid: "27581400"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Rozwiązywanie problemów dotyczących wdrożenia z ponownym uruchomieniem lub zmiana rozmiaru istniejącej maszyny Wirtualnej systemu Windows na platformie Azure
Podczas uruchamiania zatrzymanej maszyny wirtualnej Azure (VM), lub zmień rozmiar istniejącej maszyny Wirtualnej Azure, wystąpią typowych błędów jest błąd alokacji. Ten błąd powoduje klastra lub regionie nie ma dostępu do zasobów lub nie może obsługiwać żądany rozmiar maszyny Wirtualnej.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Rejestruje działania zbieranie
Zacząć Rozwiązywanie problemów zbierać dzienniki działania, aby zidentyfikować ten błąd skojarzone z problem. Poniższe łącza zawierają szczegółowe informacje na temat procesu:

[Wyświetlanie operacji wdrażania](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Wyświetl dzienniki aktywności do zarządzania zasobami Azure](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problem: Błąd podczas uruchamiania zatrzymanej maszyny Wirtualnej
Próby uruchomienia zatrzymanej maszyny Wirtualnej, ale awaria alokacji.

### <a name="cause"></a>Przyczyna
Żądania uruchomienia zatrzymanej maszyny Wirtualnej musi podjąć w oryginalnego klastra, który jest hostem usługi w chmurze. Jednak klaster nie ma wolnego miejsca do spełnienia żądania.

### <a name="resolution"></a>Rozwiązanie
* Zatrzymanie wszystkich maszyn wirtualnych w zestawie dostępności i uruchom ponownie każdej maszyny Wirtualnej.
  
  1. Kliknij przycisk **grup zasobów** > *grupie zasobów* > **zasobów** > *zestawu dostępności* > **maszyn wirtualnych** > *maszyny wirtualnej* > **zatrzymać**.
  2. Po zatrzymanie wszystkich maszyn wirtualnych, wybierz poszczególne zatrzymania maszyn wirtualnych, a następnie kliknij przycisk Uruchom.
* Ponów żądanie ponownego uruchomienia w późniejszym czasie.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problem: Błąd podczas zmiany rozmiaru istniejącej maszyny Wirtualnej
Spróbuj zmienić rozmiar istniejącej maszyny Wirtualnej, ale jest wyświetlany błąd alokacji.

### <a name="cause"></a>Przyczyna
Żądanie zmiany rozmiaru maszyny Wirtualnej musi podjąć w oryginalnego klastra, który jest hostem usługi w chmurze. Klaster nie obsługuje jednak żądany rozmiar maszyny Wirtualnej.

### <a name="resolution"></a>Rozwiązanie
* Ponów żądanie przy użyciu mniejszego rozmiaru maszyny Wirtualnej.
* Jeśli nie można zmienić rozmiar żądanej maszyny wirtualnej:
  
  1. Zatrzymanie wszystkich maszyn wirtualnych w zestawie dostępności.
     
     * Kliknij przycisk **grup zasobów** > *grupie zasobów* > **zasobów** > *zestawu dostępności* > **maszyn wirtualnych** > *maszyny wirtualnej* > **zatrzymać**.
  2. Po zatrzymanie wszystkich maszyn wirtualnych, Zmień rozmiar odpowiednią maszynę Wirtualną na większy rozmiar.
  3. Wybierz po zmianie rozmiaru maszyny Wirtualnej, a następnie kliknij przycisk **Start**, a następnie ponowne uruchomienie wszystkich zatrzymania maszynach wirtualnych.

## <a name="next-steps"></a>Kolejne kroki
Jeśli wystąpią problemy podczas tworzenia nowej maszyny Wirtualnej systemu Windows na platformie Azure, zobacz [Rozwiązywanie problemów dotyczących wdrożenia z Tworzenie nowej maszyny wirtualnej systemu Windows na platformie Azure](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

