---
title: Maszyna wirtualna, ponownego uruchamiania lub zmieniania jej rozmiaru problemy na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów dotyczących wdrożenia usługi Resource Manager przy ponownym uruchamianiu lub zmiany rozmiaru istniejącej maszyny wirtualnej na platformie Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f510a111a6c8846b300c09f368a3a2a05b2bb7ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306994"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Rozwiązywanie problemów z wdrożeniem przy ponownym uruchamianiu lub zmienianiu rozmiaru istniejącej maszyny Wirtualnej Windows na platformie Azure
Podczas próby uruchomienia zatrzymania maszyny wirtualnej platformy Azure (VM) lub zmienić rozmiar istniejącej maszyny Wirtualnej platformy Azure typowym błędem, które wystąpią jest wystąpił błąd alokacji. Ten błąd rezultatem klastra lub regionie nie ma dostępnych zasobów albo nie obsługuje żądanego rozmiaru maszyny Wirtualnej.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Dzienniki aktywności zbieranie
Aby rozpocząć rozwiązywanie problemów, zbieranie dzienników aktywności, aby zidentyfikować ten błąd, skojarzone z emisją. Poniższe linki zawierają szczegółowe informacje na temat procesu:

[Wyświetlanie operacji wdrażania](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Wyświetlanie dzienników aktywności do zarządzania zasobami platformy Azure](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problem: Błąd podczas uruchamiania zatrzymanej maszyny Wirtualnej
Spróbuj uruchomić zatrzymanej maszyny Wirtualnej, ale wystąpić błąd alokacji.

### <a name="cause"></a>Przyczyna
Żądania uruchomienia zatrzymanej maszyny Wirtualnej musi nastąpić w oryginalnym klastrze, który hostuje usługę w chmurze. Klaster nie ma jednak wolne miejsce dostępne do spełnienia żądania.

### <a name="resolution"></a>Rozwiązanie
* Zatrzymania wszystkich maszyn wirtualnych w zestawie dostępności, a następnie ponownie uruchom każdej maszyny Wirtualnej.
  
  1. Kliknij przycisk **grup zasobów** > *grupy zasobów* > **zasobów** > *poziomu dostępności zestawu*  >  **Maszyn wirtualnych** > *maszyny wirtualnej* > **zatrzymać**.
  2. Po zatrzymać wszystkie maszyny wirtualne, zaznacz wszystkie zatrzymane maszyny wirtualne, a następnie kliknij przycisk Uruchom.
* Ponów żądanie ponownego uruchomienia w późniejszym czasie.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problem: Wystąpił błąd podczas zmiany rozmiaru istniejącej maszyny Wirtualnej
Spróbuj zmienić rozmiar istniejącej maszyny Wirtualnej, ale wystąpić błąd alokacji.

### <a name="cause"></a>Przyczyna
Żądanie, aby zmienić rozmiar maszyny Wirtualnej musi nastąpić w oryginalnym klastrze, który hostuje usługę w chmurze. Jednak klastra nie obsługuje żądanego rozmiaru maszyny Wirtualnej.

### <a name="resolution"></a>Rozwiązanie
* Ponów żądanie, używając mniejszego rozmiaru maszyny Wirtualnej.
* Jeśli nie można zmienić rozmiar żądanej maszyny Wirtualnej:
  
  1. Zatrzymaj wszystkie maszyny wirtualne w zestawie dostępności.
     
     * Kliknij przycisk **grup zasobów** > *grupy zasobów* > **zasobów** > *poziomu dostępności zestawu*  >  **Maszyn wirtualnych** > *maszyny wirtualnej* > **zatrzymać**.
  2. Po zatrzymania wszystkich maszyn wirtualnych, zmienić rozmiar odpowiednią maszynę Wirtualną o większym rozmiarze.
  3. Wybierz maszynę Wirtualną o zmienionym rozmiarze, a następnie kliknij przycisk **Start**, a następnie uruchom wszystkich zatrzymanych maszyn wirtualnych.

## <a name="next-steps"></a>Kolejne kroki
Jeśli wystąpią problemy podczas tworzenia nowej maszyny Wirtualnej Windows na platformie Azure, zobacz [Rozwiązywanie problemów dotyczących wdrażania za pomocą tworzenia nowej maszyny wirtualnej Windows na platformie Azure](../windows/troubleshoot-deployment-new-vm.md).

