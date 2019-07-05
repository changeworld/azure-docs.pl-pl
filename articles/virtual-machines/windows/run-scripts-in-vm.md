---
title: Uruchamianie skryptów w maszynie Wirtualnej Windows Azure
description: W tym temacie opisano sposób uruchamiania skryptów na maszynie wirtualnej Windows
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e10cab5261d6bd970135273bd4632b7c916641de
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476756"
---
# <a name="run-scripts-in-your-windows-vm"></a>Uruchamianie skryptów w maszynie Wirtualnej Windows

Aby zautomatyzować zadania lub rozwiązywania problemów, może być konieczne do uruchamiania poleceń na maszynie wirtualnej. Następujący artykuł zawiera krótkie omówienie funkcji, które są dostępne na uruchamianie skryptów i poleceń w maszynach wirtualnych.

## <a name="custom-script-extension"></a>Rozszerzenie niestandardowego skryptu

[Rozszerzenia niestandardowego skryptu](../extensions/custom-script-windows.md) jest używany głównie dla wpisu wdrożenia Konfiguracja i Instalacja oprogramowania.

* Pobierz i uruchom skrypty w usłudze Azure virtual machines.
* Można uruchomić przy użyciu szablonów usługi Azure Resource Manager, wiersza polecenia platformy Azure, interfejsu API REST, programu PowerShell lub witryny Azure portal.
* Pliki skryptów można pobrać z usługi Azure storage lub GitHub lub z komputera, gdy są uruchomione w witrynie Azure portal.
* Skrypt programu PowerShell są uruchamiane na maszynach Windows albo funkcji Bash skryptu na maszynach z systemem Linux.
* Przydatne w przypadku konfiguracji po wdrożeniu, instalacja oprogramowania i innych konfiguracji lub zadania związane z zarządzaniem.

## <a name="run-command"></a>Uruchom polecenie

[Uruchom polecenie](run-command.md) funkcja umożliwia maszyn wirtualnych i zarządzanie aplikacjami oraz rozwiązywanie problemów przy użyciu skryptów i jest dostępny nawet wtedy, gdy komputer nie jest dostępny, na przykład jeśli Zapora gościa nie ma portu RDP lub SSH Otwórz.

* Uruchom skrypty w usłudze Azure virtual machines.
* Mogą być uruchamiane za pomocą [witryny Azure portal](run-command.md), [interfejsu API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [wiersza polecenia platformy Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), lub [programu PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand)
* Szybkie uruchamianie skryptów i wyświetlanie danych wyjściowych i powtórz zgodnie z potrzebami w witrynie Azure portal.
* Skrypt można wpisać bezpośrednio lub uruchomienie jednego z wbudowanych skryptów.
* Skrypt programu PowerShell są uruchamiane na maszynach Windows albo funkcji Bash skryptu na maszynach z systemem Linux.
* Przydatne dla maszyn wirtualnych i zarządzanie aplikacjami i uruchamiania skryptów w przypadku maszyn wirtualnych, które są nieosiągalne.

## <a name="hybrid-runbook-worker"></a>Hybrydowy proces roboczy elementu Runbook

[Hybrydowego procesu roboczego Runbook](../../automation/automation-hybrid-runbook-worker.md) umożliwia ogólne zarządzanie komputera, aplikacji i środowiska za pomocą skryptów niestandardowych użytkownika przechowywane na koncie usługi Automation.

* Uruchamianie skryptów w systemie Azure i maszyn nienależących do platformy Azure.
* Można uruchomić przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure, interfejsu API REST, programu PowerShell, elementów webhook.
* Skrypty przechowywane i zarządzane w ramach konta usługi Automation.
* Uruchamianie elementów runbook programu PowerShell, przepływ pracy programu PowerShell, Python lub graficzny
* Brak limitu czasu skryptu w czasie wykonywania.
* Wiele skryptów można uruchamiać jednocześnie.
* Dane wyjściowe skryptu pełną został zwrócony, a następnie przechowywane.
* Historia zadania są dostępne przez 90 dni.
* Umożliwia uruchamianie skryptów, jako System lokalny lub przy użyciu poświadczeń dostarczonych przez użytkownika.
* Wymaga [Instalacja ręczna](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Konsola szeregowa

[Konsoli szeregowej](serial-console.md) zapewnia bezpośredni dostęp do maszyny Wirtualnej, podobnie jak o klawiatury podłączonej do maszyny Wirtualnej.

* Uruchamianie poleceń na maszynach wirtualnych platformy Azure.
* Można uruchomić przy użyciu konsoli oparte na tekście do maszyny w witrynie Azure portal.
* Zaloguj się do komputera przy użyciu konta użytkownika lokalnego.
* Parametr jest przydatne, gdy wymagany jest dostęp do maszyny wirtualnej w niezależnie od tego stanu sieci lub systemu operacyjnego na komputerze.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat różnych funkcji, które są dostępne na uruchamianie skryptów i poleceń w maszynach wirtualnych.

* [Rozszerzenie niestandardowego skryptu](../extensions/custom-script-windows.md)
* [Uruchom polecenie](run-command.md)
* [Hybrydowego procesu roboczego Runbook](../../automation/automation-hybrid-runbook-worker.md)
* [Konsola szeregowa](serial-console.md)
