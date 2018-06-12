---
title: Uruchamianie skryptów w maszynie Wirtualnej platformy Azure w systemie Linux
description: W tym temacie opisano sposób uruchamiania skryptów w ramach maszyny wirtualnej
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: cb811e3dba7be87c83b9893db682475351ada1c1
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267003"
---
# <a name="run-scripts-in-your-linux-vm"></a>Uruchamianie skryptów w sieci maszyny Wirtualnej systemu Linux

Do automatyzacji zadań lub rozwiązywania problemów, może być konieczne uruchamiania poleceń na maszynie wirtualnej. Następujący artykuł zawiera krótki przegląd funkcji, które są dostępne na uruchamianie skryptów i polecenia w obrębie maszyn wirtualnych.

## <a name="custom-script-extension"></a>Rozszerzenie niestandardowego skryptu

[Niestandardowe rozszerzenie skryptu](../extensions/custom-script-linux.md) jest używany głównie do wdrożenia po instalacji konfigurację i oprogramowanie.

* Pobieranie i uruchamianie skryptów w maszynach wirtualnych platformy Azure.
* Można uruchamiać przy użyciu szablonów usługi Azure Resource Manager, interfejsu wiersza polecenia Azure, interfejsu API REST, programu PowerShell lub portalu Azure.
* Pliki skryptów można pobrać z magazynu Azure lub usługi GitHub lub z komputera, gdy zostaje uruchomiony z portalu Azure.
* Uruchom skrypt programu PowerShell w komputerach z systemem Windows i Bash skryptu w maszyny z systemem Linux.
* Przydatne w przypadku konfiguracji wdrożenia post, instalacja oprogramowania i innych konfiguracji lub zadań zarządzania.

## <a name="run-command"></a>Uruchom polecenie

[Uruchom polecenie](run-command.md) funkcja umożliwia maszyny wirtualnej i zarządzania aplikacjami oraz rozwiązywanie problemów przy użyciu skryptów i jest dostępny nawet wtedy, gdy komputer nie jest dostępny, na przykład jeśli zapory gościa nie ma portu protokołu RDP lub SSH Otwórz.

* Uruchom skrypty w maszynach wirtualnych platformy Azure.
* Można uruchomić przy użyciu [portalu Azure](run-command.md), [interfejsu API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [interfejsu wiersza polecenia Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), lub [programu PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand)
* Szybkie uruchamianie skryptu i widok danych wyjściowych i powtórz w razie potrzeby w portalu Azure.
* Skryptu można wpisać bezpośrednio lub uruchomienie jednego z wbudowanych skryptów.
* Uruchom skrypt programu PowerShell w komputerach z systemem Windows i Bash skryptu w maszyny z systemem Linux.
* Przydatne dla maszyn wirtualnych i zarządzania aplikacjami i uruchamianie skryptów w przypadku maszyn wirtualnych, które są niedostępne.

## <a name="hybrid-runbook-worker"></a>Hybrydowy proces roboczy elementu Runbook

[Hybrydowy proces roboczy elementu Runbook](../../automation/automation-hybrid-runbook-worker.md) zapewnia zarządzanie komputera, aplikacji i środowiska użytkownika skrypty niestandardowe przechowywane na koncie automatyzacji.

* Uruchom skrypty w Azure i maszyny z systemem innym niż Azure.
* Można uruchamiać przy użyciu portalu Azure, interfejsu wiersza polecenia Azure, interfejsu API REST, programu PowerShell, elementu webhook.
* Skrypty przechowywanych i zarządzanych w konto automatyzacji.
* Uruchom elementy runbook programu PowerShell, przepływ pracy programu PowerShell, Python lub graficzne
* Brak limitu czasu na czas wykonywania skryptu.
* Wiele skryptów można uruchamiać jednocześnie.
* Dane wyjściowe skryptu pełne został zwrócony, a następnie przechowywane.
* Dostępne przez 90 dni historii zadań.
* Skrypty można uruchomić jako System lokalny lub poświadczenia dostarczone przez użytkownika.
* Wymaga [Instalacja ręczna](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Konsola szeregowa

[Konsoli szeregowej](serial-console.md) zapewnia bezpośredni dostęp do maszyny Wirtualnej, podobnie jak o klawiatury podłączonej do maszyny Wirtualnej.

* Uruchom polecenia w maszynach wirtualnych platformy Azure.
* Można uruchamiać przy użyciu konsoli tekstowy do komputera w portalu Azure.
* Zaloguj się do komputera przy użyciu konta użytkownika lokalnego.
* Przydatne, gdy wymagany jest dostęp do maszyny wirtualnej, niezależnie od tego, stan sieci lub systemu operacyjnego na komputerze.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat różnych funkcji, które są dostępne na uruchamianie skryptów i polecenia w obrębie maszyn wirtualnych.

* [Rozszerzenie niestandardowego skryptu](../extensions/custom-script-linux.md)
* [Uruchom polecenie](run-command.md)
* [Hybrydowy proces roboczy elementu Runbook](../../automation/automation-hybrid-runbook-worker.md)
* [Konsoli szeregowej](serial-console.md)