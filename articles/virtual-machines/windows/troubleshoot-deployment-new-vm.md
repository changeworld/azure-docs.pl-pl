---
title: Rozwiązywanie problemów z wdrażaniem maszyny Wirtualnej systemu Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Menedżer zasobów Rozwiązywanie problemów dotyczących wdrożenia podczas tworzenia nowej maszyny wirtualnej systemu Windows na platformie Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fff29f6cfed4989386ca5bbd12184dce525add76
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2018
ms.locfileid: "27580375"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Rozwiązywanie problemów dotyczących wdrożenia podczas tworzenia nowej maszyny Wirtualnej systemu Windows na platformie Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Najważniejsze problemy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Inne problemy z wdrażaniem maszyny Wirtualnej i pytania, zobacz [rozwiązać wdrażanie systemu Windows maszyny wirtualnej na platformie Azure](troubleshoot-deploy-vm.md).

## <a name="collect-activity-logs"></a>Rejestruje działania zbieranie
Zacząć Rozwiązywanie problemów zbierać dzienniki działania, aby zidentyfikować ten błąd skojarzone z problem. Poniższe łącza zawierają szczegółowe informacje dla procesu, które należy wykonać.

[Wyświetlanie operacji wdrażania](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Wyświetl dzienniki aktywności do zarządzania zasobami Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**/ Y:** Jeśli systemem operacyjnym jest Windows uogólniony, a jest przekazany lub przechwycone z ustawieniem uogólniony, a następnie nie będzie błędów. Podobnie jeśli system operacyjny jest specjalizowany systemu Windows, jest przekazywane lub przechwycone specjalne ustawienia, a następnie nie będzie żadnych błędów.

**Przekazywanie błędów:**

**N<sup>1</sup>:** Jeśli systemem operacyjnym jest Windows uogólniony, a jest przekazywany jako wyspecjalizowane, otrzymasz inicjowania obsługi administracyjnej błąd upływu limitu czasu z zablokowana na ekranie OOBE maszyny Wirtualnej.

**N<sup>2</sup>:** w przypadku systemu operacyjnego Windows specjalizowany, a przesłaniem jako uogólniony, otrzymasz błędem błąd inicjowania obsługi administracyjnej z maszyną Wirtualną zablokowana na ekranie OOBE, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalnej nazwy komputera, nazwę użytkownika i hasło.

**Rozdzielczość**

Aby rozwiązać obu tych błędów, użyj [AzureRmVhd Dodaj do przekazania oryginalny dysk VHD](https://msdn.microsoft.com/library/mt603554.aspx), dostępne lokalnie, jak to samo ustawienie dla systemu operacyjnego (uogólniony/specjalizowany). Aby przekazać jako uogólniony, pamiętaj, aby najpierw uruchom program sysprep.

**Zapisz błędy:**

**N<sup>3</sup>:** Jeśli systemem operacyjnym jest Windows uogólniony, a jest przechwytywany jako wyspecjalizowane, otrzymasz inicjowania obsługi administracyjnej błąd upływu limitu czasu, ponieważ oryginalna maszyna wirtualna nie jest używany, ponieważ jest oznaczony jako uogólniony.

**N<sup>4</sup>:** w przypadku systemu operacyjnego Windows specjalizowany i jest przechwytywany jako uogólniony, otrzymasz inicjowania obsługi administracyjnej błąd niepowodzenie, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalną nazwę komputera, nazwę użytkownika i hasło. Ponadto oryginalna maszyna wirtualna nie jest używany ponieważ jest oznaczony jako specjalne.

**Rozdzielczość**

Usuń oba te błędy, Usuń bieżący obraz z portalu, i [je ponownie przechwycić z bieżącym wirtualnych dysków twardych](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) to samo ustawienie, jak te dla systemu operacyjnego (uogólniony/specjalizowany).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problem: Niestandardowy/galerii/obrazu witryny marketplace; Błąd alokacji
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
Jeśli wystąpią problemy podczas uruchamiania zatrzymanej maszyny Wirtualnej systemu Windows lub zmień rozmiar istniejącej maszyny Wirtualnej systemu Windows na platformie Azure, zobacz [problemy z wdrażaniem Rozwiązywanie problemów z Menedżera zasobów z ponownym uruchomieniem lub zmiana rozmiaru istniejącej maszyny wirtualnej systemu Windows na platformie Azure](restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

