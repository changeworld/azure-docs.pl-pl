---
title: Rozwiązywanie problemów z maszyny Wirtualnej systemu Linux wdrożenia — Classic | Dokumentacja firmy Microsoft
description: Klasycznym Rozwiązywanie problemów dotyczących wdrożenia podczas tworzenia nowej maszyny wirtualnej systemu Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: JiangChen79
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: c8a963fa-6b2a-4c7a-a1f4-7793adb02b19
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/06/2016
ms.author: cjiang
ms.openlocfilehash: 581fbaa477bd603fea5fdc0ef77c6ef7498b7897
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27965972"
---
# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Rozwiązywanie problemów wdrożenie klasyczne z Tworzenie nowej maszyny wirtualnej systemu Linux na platformie Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Wersja usługi Resource Manager w tym artykule, dla [tutaj](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Dzienniki inspekcji zbieranie
Zacząć Rozwiązywanie problemów, zbierz dzienniki inspekcji, aby określić błąd skojarzone z problem.

W portalu Azure kliknij **Przeglądaj** > **maszyn wirtualnych** > *maszyny wirtualnej systemu Windows* > **ustawienia** > **dzienniki inspekcji**.

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**/ Y:** Jeśli system operacyjny Linux uogólniony i jest przekazany lub przechwycone z ustawieniem uogólniony, wówczas nie będzie błędów. Podobnie w przypadku systemu operacyjnego Linux specjalizowany, jest przekazywane lub przechwycić ustawienia specjalne, a następnie nie będzie błędów.

**Przekazywanie błędów:**

**N<sup>1</sup>:** Jeśli system operacyjny Linux uogólniony, a jest przekazywany jako wyspecjalizowane, otrzymasz inicjowania obsługi administracyjnej błąd upływu limitu czasu, ponieważ maszyna wirtualna jest zablokowana w fazie inicjowania obsługi administracyjnej.

**N<sup>2</sup>:** w przypadku systemu operacyjnego Linux specjalizowany, a przesłaniem jako uogólniony, otrzymasz inicjowania obsługi administracyjnej błąd niepowodzenie, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalnej nazwy komputera, nazwę użytkownika i hasło.

**Rozwiązanie:**

Aby rozwiązać oba te błędy, Przekaż oryginalny dysk VHD, dostępne lokalnych, jak to samo ustawienie dla systemu operacyjnego (uogólniony/specjalizowany). Można przekazać jako uogólniony, pamiętaj, aby uruchomić - anulowanie zastrzeżenia najpierw. Zobacz [tworzenie i przekazywanie wirtualnego dysku twardego, który zawiera System operacyjny Linux](create-upload-vhd-classic.md) Aby uzyskać więcej informacji.

**Zapisz błędy:**

**N<sup>3</sup>:** Jeśli system operacyjny Linux uogólniony, a jest przechwytywany jako wyspecjalizowane, otrzymasz inicjowania obsługi administracyjnej błąd upływu limitu czasu, ponieważ oryginalna maszyna wirtualna nie jest używany, ponieważ jest oznaczony jako uogólniony.

**N<sup>4</sup>:** w przypadku systemu operacyjnego Linux specjalizowany i jest przechwytywany jako uogólniony, otrzymasz inicjowania obsługi administracyjnej błąd niepowodzenie, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalnej nazwy komputera, nazwę użytkownika i hasło. Ponadto oryginalna maszyna wirtualna nie jest używany ponieważ jest oznaczony jako specjalne.

**Rozwiązanie:**

Usuń oba te błędy, Usuń bieżący obraz z portalu, i [je ponownie przechwycić z bieżącym wirtualnych dysków twardych](capture-image-classic.md) to samo ustawienie, jak te dla systemu operacyjnego (uogólniony/specjalizowany).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problem: Niestandardowy / galerii / obrazu z witryny marketplace; Błąd alokacji
Ten błąd pojawia się w sytuacjach, gdy nowe żądanie maszyny Wirtualnej są wysyłane do klastra, który nie ma dostępnego wolnego miejsca, aby pomieścić żądania albo nie obsługuje żądanej rozmiaru maszyny Wirtualnej. Nie jest możliwe mieszać innej serii maszyn wirtualnych w tej samej usłudze w chmurze. Więc jeśli chcesz tworzyć nowej maszyny Wirtualnej o innym rozmiarze niż co usługi w chmurze może obsługiwać żądania obliczeń zakończy się niepowodzeniem.

W zależności od ograniczeń usługi w chmurze, który jest używany do utworzenia nowej maszyny Wirtualnej może wystąpić błąd spowodowane jedną z dwóch sytuacji.

**Przyczyna 1:** usługi w chmurze jest przypięta do określonego klastra lub jest połączony z grupą koligacji i dlatego przypięty do określonego klastra zgodnie z projektem. Dlatego nowy zasób obliczeniowy żądania w tej grupie koligacji są sprawdzane w tym samym klastrze, gdzie hostowane są istniejących zasobów. Jednak tego samego klastra mogą nie obsługiwać żądany rozmiar maszyny Wirtualnej lub ma za mało dostępnego miejsca, powodując błąd alokacji. Jest to możliwe, czy nowe zasoby są tworzone przez nową usługę w chmurze albo przez istniejącą usługę w chmurze.

**Rozwiązanie 1:**

* Utwórz nową usługę w chmurze i powiązać ją z obszarem lub sieci wirtualnej na podstawie regionu.
* Utwórz nową maszynę Wirtualną w nowej usługi w chmurze.
  Jeśli wystąpi błąd podczas próby utworzenia nowej usługi w chmurze, spróbuj ponownie później lub zmienić regionu dla usługi w chmurze.

> [!IMPORTANT]
> Jeśli próbujesz utworzyć nową maszynę Wirtualną w istniejącej usługi w chmurze, ale nie i utworzyć nową usługę chmury dla nowej maszyny Wirtualnej, można skonsolidować wszystkich maszyn wirtualnych w tej samej usłudze w chmurze. Aby to zrobić, Usuń maszyn wirtualnych w istniejącej usługi w chmurze i ponownie je przechwycić z ich dysków w nowej usługi w chmurze. Jest jednak należy pamiętać, że nowa usługa w chmurze będzie są nowej nazwy i adresu VIP, więc należy zaktualizować te zależności, których te informacje jest obecnie używana dla istniejącej usługi w chmurze.
> 
> 

**Przyczyny 2:** usługi w chmurze jest skojarzona z siecią wirtualną, która jest połączony do grupy koligacji, więc jest przypięta do określonego klastra zgodnie z projektem. Wszystkie nowe zasobów obliczeniowych żądania w tej grupie koligacji w związku z tym są sprawdzane w tym samym klastrze, gdzie hostowane są istniejących zasobów. Jednak tego samego klastra mogą nie obsługiwać żądany rozmiar maszyny Wirtualnej lub ma za mało dostępnego miejsca, powodując błąd alokacji. Jest to możliwe, czy nowe zasoby są tworzone przez nową usługę w chmurze albo przez istniejącą usługę w chmurze.

**Rozdzielczość 2:**

* Tworzenie nowego regionalnej sieci wirtualnej.
* Utwórz nową maszynę Wirtualną w nowej sieci wirtualnej.
* [Połączenie z istniejącą siecią wirtualną](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) do nowej sieci wirtualnej. Zobacz więcej informacji [regionalnych sieci wirtualnych](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). Można też [migrację do regionalnej sieci wirtualnej sieci wirtualnej na podstawie grupy koligacji](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), a następnie utwórz nową maszynę Wirtualną.

## <a name="next-steps"></a>Kolejne kroki
Jeśli wystąpią problemy podczas uruchamiania zatrzymanej maszyny Wirtualnej systemu Linux lub zmień rozmiar istniejącej maszyny Wirtualnej systemu Linux na platformie Azure, zobacz [klasycznego Rozwiązywanie problemów dotyczących wdrożenia z ponownym uruchomieniem lub zmiana rozmiaru istniejącej maszyny wirtualnej systemu Linux na platformie Azure](restart-resize-error-troubleshooting.md).

