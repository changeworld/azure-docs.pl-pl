---
title: Koryguj niezgodne serwery konfiguracji Azure Automation stanu
description: Jak ponownie zastosować konfiguracje na żądanie do serwerów, na których nastąpiło przetworzenie stanu konfiguracji
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: a6c7639cb4988eb13dfaa1c151085cda6e53b5d3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68614500"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Korygowanie niezgodnych serwerów DSC

Po zarejestrowaniu serwerów z Azure Automation konfiguracja stanu, tryb konfiguracji ' ma wartość ApplyOnly, ApplyandMonitor lub ApplyAndAutoCorrect.
Jeśli tryb nie jest ustawiony na Autokorekta, serwery, które przestają ze stanu zgodnego z dowolnego powodu, pozostaną niezgodne do momentu ich ręcznego skorygowania.

Usługa Azure COMPUTE oferuje funkcję o nazwie Run, która umożliwia klientom uruchamianie skryptów wewnątrz maszyn wirtualnych.
Ten dokument zawiera przykładowe skrypty dla tej funkcji podczas ręcznego korygowania dryfu konfiguracji.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Poprawianie oddryfu maszyn wirtualnych z systemem Windows przy użyciu programu PowerShell

Instrukcje krok po kroku dotyczące korzystania z funkcji Uruchom polecenie na maszynach wirtualnych z systemem Windows można znaleźć na stronie dokumentacji [Uruchamianie skryptów programu PowerShell na maszynie wirtualnej z systemem Windows za pomocą polecenia Uruchom](/azure/virtual-machines/windows/run-command).

Aby wymusić pobranie najnowszej konfiguracji przez węzeł Konfiguracja stanu Azure Automation i zastosować go, użyj `Update-DscConfiguration` polecenia cmdlet.
Aby uzyskać szczegółowe informacje, zobacz dokumentację poleceń cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Poprawianie dryfu maszyn wirtualnych z systemem Linux

Podobne funkcje nie są obecnie dostępne dla serwerów z systemem Linux.
Jedyną opcją jest powtórzenie procesu rejestracji.
W przypadku węzłów platformy Azure korekta dryfu może odbywać się w portalu lub za pomocą poleceń cmdlet AZ Automation.
Szczegółowe informacje o tym procesie zostały udokumentowane na maszynach dołączanych do stron w [celu zarządzania przez Azure Automation konfigurację stanu](/azure/automation/automation-dsc-onboarding#azure-portal).
W przypadku węzłów hybrydowych korekta dryfu można wykonać przy użyciu dołączonych skryptów języka Python.
Zapoznaj się z dokumentacją w obszarze [REPOZYTORIUM DSC dla programu PowerShell dla systemu Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz temat [polecenia cmdlet konfiguracji stanu Azure Automation](/powershell/module/azurerm.automation/#automation)
- Aby zapoznać się z przykładem użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania, zobacz [wdrażanie ciągłe przy użyciu konfiguracji stanu Azure Automation i czekolady](automation-dsc-cd-chocolatey.md)
