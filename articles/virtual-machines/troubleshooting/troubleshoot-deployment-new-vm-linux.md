---
title: Rozwiązywanie problemów z wdrożenia maszyny Wirtualnej systemu Linux | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów dotyczących wdrożenia usługi Resource Manager, podczas tworzenia nowej maszyny wirtualnej systemu Linux na platformie Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: dd6241f75311ea956b204b5181ae17db0ef3faa1
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710488"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Rozwiązywanie problemów dotyczących wdrożenia usługi Resource Manager za pomocą tworzenia nowej maszyny wirtualnej systemu Linux na platformie Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Najczęstsze problemy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Inne problemy i pytania dotyczące wdrażania maszyn wirtualnych można znaleźć w artykule [Rozwiązywanie problemów z wdrażaniem maszyny wirtualnej z systemem Linux na platformie Azure](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Dzienniki aktywności zbieranie
Aby rozpocząć rozwiązywanie problemów, zbieranie dzienników aktywności, aby zidentyfikować ten błąd, skojarzone z emisją. Poniższe linki zawierają szczegółowe informacje na temat procesu do wykonania.

[Wyświetlanie operacji wdrażania](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Wyświetlanie dzienników aktywności do zarządzania zasobami platformy Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Jeśli systemem operacyjnym jest Linux uogólniony, a przekazane lub przechwycone z ustawieniem uogólniony, nie będzie żadnych błędów. Podobnie jeśli systemem operacyjnym jest Linux wyspecjalizowany, przekazany lub przechwycone z ustawieniem wyspecjalizowane, a następnie nie będzie żadnych błędów.

**Błędy przekazywania:**

**N<sup>1</sup>:** Jeśli system operacyjny Linux uogólniony, a jest przekazywany jako wyspecjalizowany, otrzymasz błąd limitu czasu inicjowania obsługi, ponieważ maszyna wirtualna jest zablokowany na etapie inicjowania obsługi administracyjnej.

**N<sup>2</sup>:** Jeśli systemem operacyjnym jest Linux wyspecjalizowane i przekazaniu jako uogólniony, zostanie wyświetlony błąd inicjowania obsługi administracyjnej, ponieważ nowa maszyna wirtualna jest uruchomiona przy użyciu oryginalnej nazwy komputera, nazwę użytkownika i hasło.

**Rozwiązanie:**

Aby rozwiązać obu tych błędów, należy przekazać oryginalny dysk VHD, dostępne w środowisku lokalnym za pomocą tego samego ustawienia, jak dla systemu operacyjnego (uogólniony/wyspecjalizowane). Można przekazać jako uogólniony, pamiętaj, aby uruchomić — najpierw anulować aprowizację.

**Przechwytywania błędów:**

**N<sup>3</sup>:** Jeśli system operacyjny Linux uogólniony, a jest przechwytywany jako wyspecjalizowany, otrzymasz błąd limitu czasu inicjowania obsługi administracyjnej ponieważ oryginalna maszyna wirtualna nie jest używany, ponieważ jest ona oznaczona jako uogólniona.

**N<sup>4</sup>:** Jeśli systemem operacyjnym jest Linux wyspecjalizowane i są przechwytywane, jako uogólniony, zostanie wyświetlony błąd inicjowania obsługi, ponieważ nowa maszyna wirtualna jest uruchomiona przy użyciu oryginalnej nazwy komputera, nazwę użytkownika i hasło. Ponadto oryginalnego maszyny Wirtualnej nie jest używany, ponieważ jest ona oznaczona jako specjalne.

**Rozwiązanie:**

Usuń oba te błędy, Usuń bieżący obraz z portalu i [ponownie wykonaj operację przechwytywania przy bieżącym wirtualnych dysków twardych](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) przy użyciu tego samego ustawienia, jak dla systemu operacyjnego (uogólniony/wyspecjalizowane).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problem: Niestandardowe / Galeria / obrazu z witryny marketplace; Niepowodzenie alokacji
Ten błąd pojawia się w sytuacjach, gdy nowe żądanie maszyna wirtualna jest przypięta do klastra, który nie obsługuje żądanego rozmiaru maszyny Wirtualnej lub nie ma dostępnego wolnego miejsca, aby obsłużyć żądanie.

**Przyczyny 1:** Klaster nie obsługuje żądanego rozmiaru maszyny Wirtualnej.

**Rozwiązanie nr 1.**

* Ponów żądanie, używając mniejszego rozmiaru maszyny Wirtualnej.
* Jeśli nie można zmienić rozmiar żądanej maszyny Wirtualnej:
  * Zatrzymaj wszystkie maszyny wirtualne w zestawie dostępności.
    Kliknij przycisk **grup zasobów** > *grupy zasobów* > **zasobów** > *poziomu dostępności zestawu*  >  **Maszyn wirtualnych** > *maszyny wirtualnej* > **zatrzymać**.
  * Po zatrzymania wszystkich maszyn wirtualnych, należy utworzyć nową maszynę Wirtualną w żądany rozmiar.
  * Najpierw należy uruchomić nową maszynę Wirtualną, a następnie wybierz poszczególne zatrzymanych maszyn wirtualnych i kliknij **Start**.

**Przyczyny 2:** Klaster nie ma bezpłatnymi zasobami.

**Rozwiązanie nr 2.**

* Ponów żądanie w późniejszym czasie.
* Jeśli nowa maszyna wirtualna może być częścią zestawu dostępności różnych
  * Utwórz nową maszynę Wirtualną w innej zestaw dostępności (w tym samym regionie).
  * Dodaj nową maszynę Wirtualną do tej samej sieci wirtualnej.

## <a name="next-steps"></a>Kolejne kroki
Jeśli wystąpią problemy podczas uruchamiania zatrzymanej maszyny Wirtualnej systemu Linux lub zmienić rozmiar istniejącej maszyny Wirtualnej systemu Linux na platformie Azure, zobacz [problemów dotyczących wdrożenia usługi Resource Manager Rozwiązywanie problemów przy ponownym uruchamianiu lub zmiany rozmiaru istniejącej maszyny wirtualnej systemu Linux na platformie Azure](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

