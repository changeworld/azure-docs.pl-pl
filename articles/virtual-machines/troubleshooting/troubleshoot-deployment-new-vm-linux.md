---
title: Rozwiązywanie problemów z wdrażaniem maszyny Wirtualnej systemu Linux| Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z wdrażaniem usługi Resource Manager podczas tworzenia nowej maszyny wirtualnej systemu Linux na platformie Azure
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
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: 98c3a6b14230e30ccbb103be741595696a20c236
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981421"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Rozwiązywanie problemów z wdrażaniem usługi Resource Manager podczas tworzenia nowej maszyny wirtualnej systemu Linux na platformie Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Najważniejsze problemy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Inne problemy i pytania dotyczące wdrażania maszyn wirtualnych można znaleźć w artykule [Rozwiązywanie problemów z wdrażaniem maszyny wirtualnej z systemem Linux na platformie Azure](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Zbieranie dzienników aktywności
Aby rozpocząć rozwiązywanie problemów, należy zebrać dzienniki aktywności, aby zidentyfikować błąd skojarzony z problemem. Poniższe łącza zawierają szczegółowe informacje na temat procesu do naśladowania.

[Wyświetlanie operacji wdrażania](../../azure-resource-manager/templates/deployment-history.md)

[Wyświetlanie dzienników aktywności w celu zarządzania zasobami platformy Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Jeśli system operacyjny jest uogólniony w systemie Linux i jest przesyłany i/lub przechwytywany z ustawieniem uogólnionym, nie będzie żadnych błędów. Podobnie, jeśli system operacyjny jest linux wyspecjalizowane, i jest przesyłany i / lub przechwycone z ustawieniem specjalistyczne, a następnie nie będzie żadnych błędów.

**Błędy przekazywania:**

**N<sup>1</sup>:** Jeśli system operacyjny jest uogólniony w systemie Linux i jest przekazytywał jako wyspecjalizowany, otrzymasz błąd limitu czasu inicjowania obsługi administracyjnej, ponieważ maszyna wirtualna jest zablokowana na etapie inicjowania obsługi administracyjnej.

**N<sup>2</sup>:** Jeśli system operacyjny jest linux wyspecjalizowane i jest przekazywane jako uogólnione, otrzymasz błąd inicjowania obsługi administracyjnej, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalną nazwą komputera, nazwę użytkownika i hasło.

**Rozdzielczość:**

Aby rozwiązać oba te błędy, przekaż oryginalny dysk VHD, dostępny lokalnie, z tym samym ustawieniem co dla systemu operacyjnego (uogólniony/wyspecjalizowany). Aby przesłać jako uogólnione, należy pamiętać, aby najpierw uruchomić -deprovision.

**Przechwytywanie błędów:**

**N<sup>3</sup>:** Jeśli system operacyjny jest linux uogólniony i jest przechwytywany jako wyspecjalizowane, otrzymasz błąd limitu czasu inicjowania obsługi administracyjnej, ponieważ oryginalna maszyna wirtualna nie jest użyteczna, ponieważ jest oznaczona jako uogólniona.

**N<sup>4</sup>:** Jeśli system operacyjny jest linux wyspecjalizowane i jest przechwytywany jako uogólnione, otrzymasz błąd inicjowania obsługi administracyjnej, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalną nazwą komputera, nazwę użytkownika i hasło. Ponadto oryginalna maszyna wirtualna nie jest użyteczna, ponieważ jest oznaczona jako wyspecjalizowana.

**Rozdzielczość:**

Aby rozwiązać oba te błędy, usuń bieżący obraz z portalu i [przechwyć go z bieżących vhdów](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) z tym samym ustawieniem, co dla systemu operacyjnego (uogólnione/wyspecjalizowane).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problem: Niestandardowe / galeria / obraz rynku; niepowodzenie alokacji
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
Jeśli wystąpią problemy podczas uruchamiania zatrzymanej maszyny Wirtualnej systemu Linux lub zmiany rozmiaru istniejącej maszyny wirtualnej z systemem Linux na platformie Azure, zobacz [Rozwiązywanie problemów z wdrażaniem usługi Resource Manager podczas ponownego uruchamiania lub zmiany rozmiaru istniejącej maszyny wirtualnej systemu Linux na platformie Azure](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

