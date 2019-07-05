---
title: Typy elementów Runbook usługi Azure Automation
description: 'W tym artykule opisano różne typy elementów runbook, które można używać w usłudze Azure Automation i zagadnienia, które należy wziąć pod uwagę podczas określania, którego typu użyć. '
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8d90f1c03a9ed892a9dc8a4fecbeb9815c2dd376
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476898"
---
# <a name="azure-automation-runbook-types"></a>Typy elementów runbook w usłudze Azure Automation

Usługa Azure Automation obsługuje kilka typów elementów runbook, krótko opisanych w poniższej tabeli.  Poniższe sekcje zawierają dodatkowe informacje o każdym typie, łącznie z uwagi na tym, kiedy należy używać każdego.

| Typ | Opis |
|:--- |:--- |
| [Element graficzny](#graphical-runbooks)|Oparte na programie Windows PowerShell i tworzone oraz edytowane całkowicie w edytorze graficznym w witrynie Azure portal. |
| [Graficzny przepływ pracy programu PowerShell](#graphical-runbooks)|Oparte na przepływie pracy programu Windows PowerShell i tworzone oraz edytowane całkowicie za edytor graficzny, w witrynie Azure portal. |
| [Program PowerShell](#powershell-runbooks) |Element runbook tekstu, oparte na skrypt programu Windows PowerShell. |
| [Przepływ pracy programu PowerShell](#powershell-workflow-runbooks)|Element runbook tekstu, oparte na przepływie pracy programu Windows PowerShell. |
| [Python](#python-runbooks) |Element runbook tekst oparty na języku Python. |

## <a name="graphical-runbooks"></a>Graficzne elementy runbook

[Graficzny](automation-runbook-types.md#graphical-runbooks) i graficzny przepływ pracy programu PowerShell, elementów runbook są tworzone i edytować za pomocą graficznego edytora w witrynie Azure portal.  Można wyeksportować do pliku, a następnie zaimportować je do innego konta usługi automation. Ale nie można utworzyć lub edytować je przy użyciu innego narzędzia. Graficzne elementy runbook generowania kodu programu PowerShell, ale nie można bezpośrednio wyświetlić lub zmodyfikować kod. Graficzne elementy runbook nie można przekonwertować do jednego z [formatów tekstu](automation-runbook-types.md), ani tekstowego elementu runbook można przekonwertować w postaci graficznej. Graficzne elementy runbook mogą być konwertowane do elementów runbook graficzny przepływ pracy programu PowerShell podczas importowania i odwrotnie.

### <a name="advantages"></a>Zalety

* Model tworzenia skonfigurować łącze insert Visual  
* Skup się na sposób przepływu danych przez proces  
* Wizualnie przedstawiają procesów zarządzania  
* Uwzględnić inne elementy runbook jako podrzędne elementy runbook można tworzyć przepływy pracy wysokiego poziomu  
* Zachęca programowanie modułowe  

### <a name="limitations"></a>Ograniczenia

* Nie można edytować element runbook poza witryny Azure portal.
* Może wymagać działania kodu zawierające kod programu PowerShell do wykonywania złożonej logiki.
* Nie można wyświetlić ani bezpośrednio edytować kod programu PowerShell, który jest tworzony przez graficzny przepływ pracy. Możesz wyświetlić kod, który tworzysz w żadnych działań kodu.
* Nie uruchomiono na systemie Linux hybrydowego procesu roboczego Runbook

## <a name="powershell-runbooks"></a>Elementy runbook programu PowerShell

Elementy runbook programu PowerShell są oparte na programie Windows PowerShell.  Możesz bezpośrednio edytować kod elementu runbook za pomocą edytora tekstów w witrynie Azure portal.  Można również użyć dowolnego edytora tekstów w trybie offline i [zaimportuj element runbook](manage-runbooks.md) do usługi Azure Automation.

### <a name="advantages"></a>Zalety

* Zaimplementuj wszystkie złożonej logiki przy użyciu programu PowerShell kodu bez przeprowadzania dodatkowej złożoności przepływu pracy programu PowerShell.
* Element Runbook uruchamia się szybciej niż w elementach runbook przepływu pracy programu PowerShell, ponieważ nie musi on być skompilowany przed uruchomieniem.
* Można uruchomić na platformie Azure lub z systemem Linux i Windows hybrydowych procesów roboczych Runbook

### <a name="limitations"></a>Ograniczenia

* Należy zapoznać się z skryptów środowiska PowerShell.
* Nie można użyć [przetwarzania równoległego](automation-powershell-workflow.md#parallel-processing) wykonywać wiele akcji równolegle.
* Nie można użyć [punktów kontrolnych](automation-powershell-workflow.md#checkpoints) wznowienie elementu runbook, jeśli występuje błąd.
* Elementy runbook przepływu pracy programu PowerShell i graficzne elementy runbook można tylko dołączone jako podrzędne elementy runbook za pomocą polecenia cmdlet Start-AzureAutomationRunbook, co powoduje utworzenie nowego zadania.

### <a name="known-issues"></a>Znane problemy

Poniżej przedstawiono obecnie znane problemy z elementami runbook programu PowerShell.

* Elementy runbook programu PowerShell nie można pobrać niezaszyfrowane [zasób zmiennej](automation-variables.md) o wartości null.
* Elementy runbook programu PowerShell nie można pobrać [zasób zmiennej](automation-variables.md) z *~* w nazwie.
* Get-Process w pętli programu PowerShell runbook może ulec awarii po około 80 iteracjach.
* Element runbook programu PowerShell może zakończyć się niepowodzeniem, gdy podejmowana jest próba zapisu dużej ilości danych w strumieniu wyjściowym jednocześnie.   Można zwykle obejść ten problem przez podawania tylko informacje, które będą potrzebne podczas pracy z dużych obiektów.  Na przykład zamiast podawania podobny *Get-Process*, mogą przesyłać dane wyjściowe po prostu wymagane pola *Get-Process | Wybierz Nazwaprocesu, procesora CPU*.

## <a name="powershell-workflow-runbooks"></a>Elementy runbook przepływu pracy programu PowerShell

Elementy runbook przepływu pracy programu PowerShell są tekstowymi elementami runbook opartymi na [przepływu pracy środowiska Windows PowerShell](automation-powershell-workflow.md).  Możesz bezpośrednio edytować kod elementu runbook za pomocą edytora tekstów w witrynie Azure portal.  Można również użyć dowolnego edytora tekstów w trybie offline i [zaimportuj element runbook](manage-runbooks.md) do usługi Azure Automation.

### <a name="advantages"></a>Zalety

* Zaimplementuj wszystkie złożonej logiki przy użyciu kodu przepływu pracy programu PowerShell.
* Użyj [punktów kontrolnych](automation-powershell-workflow.md#checkpoints) wznowienie elementu runbook, jeśli występuje błąd.
* Użyj [przetwarzania równoległego](automation-powershell-workflow.md#parallel-processing) do wykonania wielu akcji równolegle.
* Może zawierać innych graficznych elementów runbook i elementy runbook przepływu pracy programu PowerShell jako podrzędne elementy runbook można tworzyć przepływy pracy wysokiego poziomu.

### <a name="limitations"></a>Ograniczenia

* Autor należy zapoznać się z przepływu pracy programu PowerShell.
* Element Runbook musi obsłużyć dodatkowe złożoność przepływu pracy programu PowerShell takich jak [deserializacji obiektów](automation-powershell-workflow.md#code-changes).
* Element Runbook zajmuje więcej czasu start niż elementy runbook programu PowerShell, który musi być skompilowany przed uruchomieniem.
* Elementy runbook programu PowerShell może być tylko dołączone jako podrzędne elementy runbook za pomocą polecenia cmdlet Start-AzureAutomationRunbook, co powoduje utworzenie nowego zadania.
* Nie uruchomiono na systemie Linux hybrydowego procesu roboczego Runbook

## <a name="python-runbooks"></a>Elementy runbook języka Python

Elementy runbook języka Python kompilacji w ramach języka Python 2.  Można bezpośrednio edytować kod elementu runbook za pomocą edytora tekstu, w witrynie Azure portal lub za pomocą edytora tekstów w trybie offline i [zaimportuj element runbook](manage-runbooks.md) do usługi Azure Automation.

### <a name="advantages"></a>Zalety

* Korzystaj z niezawodnej biblioteki języka Python.
* Może być uruchomione na platformie Azure lub na oba Linux hybrydowych procesów roboczych Runbook. Windows hybrydowych procesów roboczych Runbook są obsługiwane za pomocą [python2.7](https://www.python.org/downloads/release/latest/python2) zainstalowane.

### <a name="limitations"></a>Ograniczenia

* Należy zapoznać się z obsługi skryptów języka Python.
* Tylko języka Python 2 jest obsługiwany w tej chwili, co oznacza, że określone funkcje języka Python 3 zakończy się niepowodzeniem.
* Aby korzystać z bibliotek innych firm, należy najpierw [zaimportować pakiet](python-packages.md) na konto usługi Automation dla niego ma być używany.

## <a name="considerations"></a>Zagadnienia do rozważenia

Weź pod uwagę następujące dodatkowe zagadnienia podczas określania, którego typu użyć określonego elementu runbook.

* Nie można przekonwertować typu tekstową lub odwrotnie elementów runbook z graficznego.
* Istnieją pewne ograniczenia przy użyciu elementów runbook o różnych typach jako podrzędnego elementu runbook. Aby uzyskać więcej informacji, zobacz [podrzędnych elementów runbook w usłudze Azure Automation](automation-child-runbooks.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat tworzenia graficznego elementu runbook, zobacz [tworzenia elementów graficznych w usłudze Azure Automation](automation-graphical-authoring-intro.md)
* Aby zrozumieć różnice między programu PowerShell i programu PowerShell przepływów pracy dla elementów runbook, zobacz [Windows uczenie się przepływu pracy programu PowerShell](automation-powershell-workflow.md)
* Aby uzyskać więcej informacji na temat sposobu Tworzenie lub importowanie elementu Runbook, zobacz [Tworzenie lub importowanie elementu Runbook](manage-runbooks.md)

