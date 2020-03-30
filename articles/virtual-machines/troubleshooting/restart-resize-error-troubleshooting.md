---
title: Problemy z ponownym uruchamianiem lub zmiany rozmiaru maszyny Wirtualnej na platformie Azure | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z wdrażaniem usługi Resource Manager podczas ponownego uruchamiania lub zmiany rozmiaru istniejącej maszyny wirtualnej na platformie Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a6532558107463311c4225b9855bc4cd3f19eed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75965621"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Rozwiązywanie problemów z wdrożeniem przy ponownym uruchamianiu lub zmienianiu rozmiaru istniejącej maszyny wirtualnej z systemem Windows na platformie Azure
Podczas próby uruchomienia zatrzymanej maszyny wirtualnej platformy Azure (VM) lub ponownego rozmiaru istniejącej maszyny wirtualnej platformy Azure typowym błędem, który napotkasz, jest błąd alokacji. Ten błąd powoduje, gdy klaster lub region nie ma dostępnych zasobów lub nie może obsługiwać żądanego rozmiaru maszyny Wirtualnej.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Zbieranie dzienników aktywności
Aby rozpocząć rozwiązywanie problemów, należy zebrać dzienniki aktywności, aby zidentyfikować błąd skojarzony z problemem. Poniższe linki zawierają szczegółowe informacje na temat procesu:

[Wyświetlanie operacji wdrażania](../../azure-resource-manager/templates/deployment-history.md)

[Wyświetlanie dzienników aktywności w celu zarządzania zasobami platformy Azure](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problem: błąd podczas uruchamiania zatrzymanej maszyny wirtualnej
Spróbuj uruchomić zatrzymaną maszynę wirtualną, ale uzyskać błąd alokacji.

### <a name="cause"></a>Przyczyna
Żądanie uruchomienia zatrzymanej maszyny Wirtualnej musi zostać podjęta w oryginalnym klastrze obsługującym usługę w chmurze. Jednak klaster nie ma wolnego miejsca dostępnego do spełnienia żądania.

### <a name="resolution"></a>Rozwiązanie
* Zatrzymaj wszystkie maszyny wirtualne w zestawie dostępności, a następnie uruchom ponownie każdą maszynę wirtualną.
  
  1. Kliknij **pozycję Zasoby grupują** > **zasoby,** > w przypadku*gdy* > *dostępność jest ustawiana przez* > **maszyny** > *wirtualne, zatrzymuje maszynę wirtualną* > **Stop**.
  2. Po zatrzymaniu wszystkich maszyn wirtualnych wybierz każdą zatrzymaną maszynę wirtualną i kliknij przycisk Start.
* Ponów próbę ponownego uruchomienia żądania w późniejszym czasie.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problem: błąd podczas zmiany rozmiaru istniejącej maszyny wirtualnej
Spróbuj zmienić rozmiar istniejącej maszyny Wirtualnej, ale uzyskać błąd alokacji.

### <a name="cause"></a>Przyczyna
Żądanie, aby zmienić rozmiar maszyny Wirtualnej musi być podjęta próba w oryginalnym klastrze, który obsługuje usługę w chmurze. Jednak klaster nie obsługuje żądanego rozmiaru maszyny Wirtualnej.

### <a name="resolution"></a>Rozwiązanie
* Ponów próbę żądania przy użyciu mniejszego rozmiaru maszyny Wirtualnej.
* Jeśli nie można zmienić rozmiaru żądanej maszyny Wirtualnej:
  
  1. Zatrzymaj wszystkie maszyny wirtualne w zestawie dostępności.
     
     * Kliknij **pozycję Zasoby grupują** > **zasoby,** > w przypadku*gdy* > *dostępność jest ustawiana przez* > **maszyny** > *wirtualne, zatrzymuje maszynę wirtualną* > **Stop**.
  2. Po zatrzymaniu wszystkich maszyn wirtualnych, zmienić rozmiar żądanej maszyny Wirtualnej do większego rozmiaru.
  3. Zaznacz maszynę wirtualną o przesłonie i kliknij przycisk **Start**, a następnie uruchom każdą zatrzymaną maszynę wirtualną.

## <a name="next-steps"></a>Następne kroki
Jeśli wystąpią problemy podczas tworzenia nowej maszyny Wirtualnej systemu Windows na platformie Azure, zobacz [Rozwiązywanie problemów z wdrażaniem podczas tworzenia nowej maszyny wirtualnej systemu Windows na platformie Azure](../windows/troubleshoot-deployment-new-vm.md).

