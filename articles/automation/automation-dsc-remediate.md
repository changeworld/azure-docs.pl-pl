---
title: Korygowanie niezgodnych serwerów konfiguracji stanu automatyzacji platformy Azure
description: Jak ponownie zastosować konfiguracje na żądanie do serwerów, na których stan konfiguracji się zmienił
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: f4ca76f4be9d00e185f8774fc33296d1af1aeece
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585497"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Korygowanie niezgodnych serwerów DSC

Gdy serwery są zarejestrowane w konfiguracji stanu automatyzacji platformy Azure, "Tryb konfiguracji" jest ustawiony na ApplyOnly, ApplyandMonitor lub ApplyAndAutoCorrect.
Jeśli tryb nie jest ustawiony na Autokorektę, serwery, które z jakiegokolwiek powodu oddalają się od stanu zgodnego, pozostaną niezgodne, dopóki nie zostaną ręcznie poprawione.

Dane obliczeniowe platformy Azure oferuje funkcję o nazwie Uruchom polecenie, która umożliwia klientom uruchamianie skryptów wewnątrz maszyn wirtualnych.
Ten dokument zawiera przykładowe skrypty dla tej funkcji podczas ręcznego korygowania dryfu konfiguracji.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Prawidłowe dryfowanie maszyn wirtualnych systemu Windows przy użyciu programu PowerShell

Aby uzyskać instrukcje krok po kroku przy użyciu funkcji Uruchom polecenie na maszynach wirtualnych systemu Windows, zobacz stronę dokumentacji [Uruchamianie skryptów programu PowerShell w maszynie Wirtualnej systemu Windows za pomocą polecenia Uruchom](/azure/virtual-machines/windows/run-command)polecenie .

Aby wymusić węzeł konfiguracji stanu automatyzacji platformy Azure, `Update-DscConfiguration` aby pobrać najnowszą konfigurację i zastosować ją, użyj polecenia cmdlet.
Aby uzyskać szczegółowe informacje, zobacz dokumentację polecenia cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Prawidłowy dryf maszyn wirtualnych systemu Linux

Podobna funkcjonalność nie jest obecnie dostępna dla serwerów Linuksa.
Jedyną opcją jest powtórzenie procesu rejestracji.
W przypadku węzłów platformy Azure korekcja dryfu można wykonać z portalu lub przy użyciu poleceń cmdlet Az Automation.
Szczegółowe informacje na temat tego procesu są udokumentowane na stronie [Maszyny dołączania do zarządzania przez konfigurację stanu automatyzacji usługi Azure.](/azure/automation/automation-dsc-onboarding#onboard-a-vm-using-azure-portal)
W przypadku węzłów hybrydowych korekcja dryfu może być wykonywana przy użyciu dołączonych skryptów Języka Python.
Zobacz dokumentację w [programie PowerShell DSC for Linux repo](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o poleceniach cmdlet programu PowerShell, zobacz [Polecenia cmdlet konfiguracji stanu automatyzacji platformy Azure](/powershell/module/azurerm.automation/#automation)
- Aby wyświetlić przykład użycia konfiguracji stanu automatyzacji platformy Azure w potoku ciągłego wdrażania, zobacz [Ciągłe wdrażanie przy użyciu konfiguracji stanu automatyzacji platformy Azure i czekoladowe](automation-dsc-cd-chocolatey.md)
