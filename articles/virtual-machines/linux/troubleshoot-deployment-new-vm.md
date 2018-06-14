---
title: Rozwiązywanie problemów z wdrożenia maszyny Wirtualnej systemu Linux-RM | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów dotyczących wdrożenia usługi Resource Manager podczas tworzenia nowej maszyny wirtualnej systemu Linux na platformie Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: felixwu
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: aea5db05843b0175b8ef8b713944e12262e33010
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2018
ms.locfileid: "27579267"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Rozwiązywanie problemów wdrożenia usługi Resource Manager z Tworzenie nowej maszyny wirtualnej systemu Linux na platformie Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Najważniejsze problemy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Inne problemy z wdrażaniem maszyny Wirtualnej i pytania, zobacz [Rozwiązywanie problemów z wdrażanie problemy dotyczące maszyny wirtualnej systemu Linux na platformie Azure](troubleshoot-deploy-vm.md).
## <a name="collect-activity-logs"></a>Rejestruje działania zbieranie
Zacząć Rozwiązywanie problemów zbierać dzienniki działania, aby zidentyfikować ten błąd skojarzone z problem. Poniższe łącza zawierają szczegółowe informacje dla procesu, które należy wykonać.

[Wyświetlanie operacji wdrażania](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Wyświetl dzienniki aktywności do zarządzania zasobami Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**/ Y:** Jeśli system operacyjny Linux uogólniony i jest przekazany lub przechwycone z ustawieniem uogólniony, wówczas nie będzie błędów. Podobnie w przypadku systemu operacyjnego Linux specjalizowany, jest przekazywane lub przechwycić ustawienia specjalne, a następnie nie będzie błędów.

**Przekazywanie błędów:**

**N<sup>1</sup>:** Jeśli system operacyjny Linux uogólniony, a jest przekazywany jako wyspecjalizowane, otrzymasz inicjowania obsługi administracyjnej błąd upływu limitu czasu, ponieważ maszyna wirtualna jest zablokowana w fazie inicjowania obsługi administracyjnej.

**N<sup>2</sup>:** w przypadku systemu operacyjnego Linux specjalizowany, a przesłaniem jako uogólniony, otrzymasz inicjowania obsługi administracyjnej błąd niepowodzenie, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalnej nazwy komputera, nazwę użytkownika i hasło.

**Rozwiązanie:**

Aby rozwiązać oba te błędy, Przekaż oryginalny dysk VHD, dostępne lokalnych, jak to samo ustawienie dla systemu operacyjnego (uogólniony/specjalizowany). Można przekazać jako uogólniony, pamiętaj, aby uruchomić - anulowanie zastrzeżenia najpierw.

**Zapisz błędy:**

**N<sup>3</sup>:** Jeśli system operacyjny Linux uogólniony, a jest przechwytywany jako wyspecjalizowane, otrzymasz inicjowania obsługi administracyjnej błąd upływu limitu czasu, ponieważ oryginalna maszyna wirtualna nie jest używany, ponieważ jest oznaczony jako uogólniony.

**N<sup>4</sup>:** w przypadku systemu operacyjnego Linux specjalizowany i jest przechwytywany jako uogólniony, otrzymasz inicjowania obsługi administracyjnej błąd niepowodzenie, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalnej nazwy komputera, nazwę użytkownika i hasło. Ponadto oryginalna maszyna wirtualna nie jest używany ponieważ jest oznaczony jako specjalne.

**Rozwiązanie:**

Usuń oba te błędy, Usuń bieżący obraz z portalu, i [je ponownie przechwycić z bieżącym wirtualnych dysków twardych](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) to samo ustawienie, jak te dla systemu operacyjnego (uogólniony/specjalizowany).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problem: Niestandardowy / galerii / obrazu z witryny marketplace; Błąd alokacji
Ten błąd pojawia się w sytuacjach, gdy nowe żądanie maszyna wirtualna jest przypięta do klastra, która nie obsługuje żądanej rozmiaru maszyny Wirtualnej, lub nie ma dostępnego wolnego miejsca, aby zmieścił się w żądaniu.

**Przyczyna 1:** klastra nie może obsługiwać żądany rozmiar maszyny Wirtualnej.

**Rozwiązanie 1:**

* Ponów żądanie przy użyciu mniejszego rozmiaru maszyny Wirtualnej.
* Jeśli nie można zmienić rozmiar żądanej maszyny wirtualnej:
  * Zatrzymanie wszystkich maszyn wirtualnych w zestawie dostępności.
    Kliknij przycisk **grup zasobów** > *grupie zasobów* > **zasobów** > *zestawu dostępności* > **maszyn wirtualnych** > *maszyny wirtualnej* > **zatrzymać**.
  * Po zatrzymanie wszystkich maszyn wirtualnych, należy utworzyć nową maszynę Wirtualną w wymagany rozmiar.
  * Najpierw należy uruchomić nową maszynę Wirtualną, a następnie wybierz poszczególne zatrzymania maszyn wirtualnych i kliknij przycisk **Start**.

**Przyczyny 2:** klastra nie ma wolnego zasobów.

**Rozdzielczość 2:**

* Ponów żądanie w późniejszym czasie.
* Jeśli nowa maszyna wirtualna może być częścią zestawu dostępności różnych
  * Utwórz nową maszynę Wirtualną w różnych dostępności, ustawić (w tym samym regionie).
  * Dodaj nową maszynę Wirtualną do tej samej sieci wirtualnej.

## <a name="next-steps"></a>Kolejne kroki
Jeśli wystąpią problemy podczas uruchamiania zatrzymanej maszyny Wirtualnej systemu Linux lub zmień rozmiar istniejącej maszyny Wirtualnej systemu Linux na platformie Azure, zobacz [problemy z wdrażaniem Rozwiązywanie problemów z Menedżera zasobów z ponownym uruchomieniem lub zmiana rozmiaru istniejącej maszyny wirtualnej systemu Linux na platformie Azure](restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

