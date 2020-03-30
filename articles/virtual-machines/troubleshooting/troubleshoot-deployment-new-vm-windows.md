---
title: Rozwiązywanie problemów z wdrażaniem maszyny Wirtualnej systemu Windows na platformie Azure | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z wdrażaniem usługi Resource Manager podczas tworzenia nowej maszyny wirtualnej systemu Windows na platformie Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0bc363b87a9f5b2f013c0bae75a07d79a3a7a830
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981401"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Rozwiązywanie problemów z wdrażaniem podczas tworzenia nowej maszyny Wirtualnej systemu Windows na platformie Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Najważniejsze problemy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Aby zapoznać się z innymi problemami z wdrażaniem maszyn wirtualnych i pytaniami, zobacz [Rozwiązywanie problemów z wdrażaniem komputera wirtualnego systemu Windows na platformie Azure](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Zbieranie dzienników aktywności
Aby rozpocząć rozwiązywanie problemów, należy zebrać dzienniki aktywności, aby zidentyfikować błąd skojarzony z problemem. Poniższe łącza zawierają szczegółowe informacje na temat procesu do naśladowania.

[Wyświetlanie operacji wdrażania](../../azure-resource-manager/templates/deployment-history.md)

[Wyświetlanie dzienników aktywności w celu zarządzania zasobami platformy Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Jeśli system operacyjny jest uogólniony systemem Windows i jest przekazywany i/lub przechwytywany z ustawieniem uogólnionym, nie będzie żadnych błędów. Podobnie, jeśli system operacyjny jest wyspecjalizowany w systemie Windows i jest przekazywany i/lub przechwytywany za pomocą specjalistycznego ustawienia, nie będzie żadnych błędów.

**Błędy przekazywania:**

**N<sup>1</sup>:** Jeśli system operacyjny jest uogólniony w systemie Windows i jest przekazywany jako wyspecjalizowany, otrzymasz błąd limitu czasu inicjowania obsługi administracyjnej z maszyną wirtualną zablokowaną na ekranie OOBE.

**N<sup>2</sup>:** Jeśli system operacyjny jest wyspecjalizowany w systemie Windows i jest przekazywany jako uogólniony, zostanie wyświetlony błąd inicjowania obsługi administracyjnej z maszyną wirtualną zablokowaną na ekranie OOBE, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalną nazwą komputera, nazwą użytkownika i hasłem.

**Rozdzielczość**

Aby rozwiązać oba te błędy, użyj [Add-AzVhd, aby przesłać oryginalny dysk VHD](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd), dostępny lokalnie, z tym samym ustawieniem co dla systemu operacyjnego (uogólniony/wyspecjalizowany). Aby przesłać jako uogólnione, pamiętaj, aby najpierw uruchomić sysprep.

**Przechwytywanie błędów:**

**N<sup>3</sup>:** Jeśli system operacyjny jest uogólniony w systemie Windows i jest przechwytywany jako wyspecjalizowany, zostanie wyświetlony błąd limitu czasu inicjowania obsługi administracyjnej, ponieważ oryginalna maszyna wirtualna nie jest użyteczna, ponieważ jest oznaczona jako uogólniona.

**N<sup>4</sup>:** Jeśli system operacyjny jest wyspecjalizowany w systemie Windows i jest przechwytywany jako uogólniony, zostanie wyświetlony błąd inicjowania obsługi administracyjnej, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalną nazwą komputera, nazwą użytkownika i hasłem. Ponadto oryginalna maszyna wirtualna nie jest użyteczna, ponieważ jest oznaczona jako wyspecjalizowana.

**Rozdzielczość**

Aby rozwiązać oba te błędy, usuń bieżący obraz z portalu i [przechwyć go z bieżących vhdów](../windows/create-vm-specialized.md) z tym samym ustawieniem, co dla systemu operacyjnego (uogólnione/wyspecjalizowane).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problem: obraz niestandardowy/galeria/rynek; niepowodzenie alokacji
Ten błąd występuje w sytuacjach, gdy nowe żądanie maszyny Wirtualnej jest przypięty do klastra, który nie może obsługiwać żądany rozmiar maszyny Wirtualnej lub nie ma wolnego miejsca na obsługę żądania.

**Przyczyna 1:** Klaster nie może obsługiwać żądanego rozmiaru maszyny Wirtualnej.

**Rozwiązanie 1.**

* Ponów próbę żądania przy użyciu mniejszego rozmiaru maszyny Wirtualnej.
* Jeśli nie można zmienić rozmiaru żądanej maszyny Wirtualnej:
  * Zatrzymaj wszystkie maszyny wirtualne w zestawie dostępności.
    Kliknij **pozycję Zasoby grupują** > **zasoby,** > w przypadku*gdy* > *dostępność jest ustawiana przez* > **maszyny** > *wirtualne, zatrzymuje maszynę wirtualną* > **Stop**.
  * Po zatrzymaniu wszystkich maszyn wirtualnych należy utworzyć nową maszynę wirtualną w żądanym rozmiarze.
  * Najpierw uruchom nową maszynę wirtualną, a następnie wybierz każdą z zatrzymanych maszyn wirtualnych i kliknij przycisk **Start**.

**Przyczyna 2:** Klaster nie ma wolnych zasobów.

**Rozwiązanie 2.**

* Ponów próbę żądania w późniejszym czasie.
* Jeśli nowa maszyna wirtualna może być częścią innego zestawu dostępności
  * Utwórz nową maszynę wirtualną w innym zestawie dostępności (w tym samym regionie).
  * Dodaj nową maszynę wirtualną do tej samej sieci wirtualnej.

## <a name="next-steps"></a>Następne kroki
Jeśli wystąpią problemy podczas uruchamiania zatrzymanej maszyny Wirtualnej systemu Windows lub zmiany rozmiaru istniejącej maszyny wirtualnej systemu Windows na platformie Azure, zobacz [Rozwiązywanie problemów z wdrażaniem Menedżera zasobów podczas ponownego uruchamiania lub zmiany rozmiaru istniejącej maszyny wirtualnej systemu Windows na platformie Azure](restart-resize-error-troubleshooting.md).


