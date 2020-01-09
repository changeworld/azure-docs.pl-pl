---
title: Azure Automation typy elementów Runbook
description: 'W tym artykule opisano różne typy elementów Runbook, których można użyć w Azure Automation i zagadnienia, które należy wziąć pod uwagę podczas określania, który typ ma być używany. '
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 6346c29210b6390f11c884ff51e0b60af89bbbb7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420908"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation typy elementów Runbook

Azure Automation obsługuje kilka typów elementów Runbook, które zostały krótko opisane w poniższej tabeli.  W poniższych sekcjach znajdują się dodatkowe informacje o każdym typie, w tym zagadnienia dotyczące sytuacji, w których należy użyć każdego z nich

| Typ | Opis |
|:--- |:--- |
| [Element graficzny](#graphical-runbooks)|W oparciu o program Windows PowerShell i całkowicie utworzony i zmodyfikowany w edytorze graficznym w Azure Portal. |
| [Graficzny przepływ pracy programu PowerShell](#graphical-runbooks)|W oparciu o przepływ pracy programu Windows PowerShell i całkowicie utworzony i zmodyfikowany w edytorze graficznym w Azure Portal. |
| [Program PowerShell](#powershell-runbooks) |Element runbook tekstu, oparte na skrypt programu Windows PowerShell. |
| [Przepływ pracy programu PowerShell](#powershell-workflow-runbooks)|Element runbook tekstu, oparte na przepływie pracy programu Windows PowerShell. |
| [Python](#python-runbooks) |Tekstowy element Runbook oparty na języku Python. |

## <a name="graphical-runbooks"></a>Graficzne elementy Runbook

[Graficzne](automation-runbook-types.md#graphical-runbooks) i graficzne elementy Runbook przepływu pracy programu PowerShell są tworzone i edytowane w edytorze graficznym w Azure Portal.  Możesz wyeksportować je do pliku, a następnie zaimportować je do innego konta usługi Automation. Ale nie można ich tworzyć ani edytować za pomocą innego narzędzia. Graficzne elementy Runbook generują kod programu PowerShell, ale nie można bezpośrednio przeglądać ani modyfikować kodu. Graficzne elementy Runbook nie mogą być konwertowane na jeden z [formatów tekstowych](automation-runbook-types.md)ani nie można przekonwertować tekstu elementu Runbook na format graficzny. Graficzne elementy Runbook można przekonwertować na graficzne elementy Runbook przepływu pracy programu PowerShell podczas importowania i w inny sposób.

### <a name="advantages"></a>Zalety

* Visual INSERT-link-Konfiguruj model tworzenia
* Skup się na przepływie danych przez proces
* Wizualizacja reprezentuje procesy zarządzania
* Dołącz inne elementy Runbook jako podrzędne elementy Runbook, aby utworzyć przepływy pracy wysokiego poziomu
* Zachęca do programowania modularnego

### <a name="limitations"></a>Ograniczenia

* Nie można edytować elementu Runbook poza Azure Portal.
* Może wymagać działania kodu zawierającego kod programu PowerShell do wykonywania złożonej logiki.
* Nie można wyświetlić lub bezpośrednio edytować kodu programu PowerShell tworzonego przez graficzny przepływ pracy. Możesz wyświetlić kod utworzony w ramach jakichkolwiek działań kodu.
* Nie można wykonać w ramach hybrydowego procesu roboczego elementu Runbook systemu Linux

## <a name="powershell-runbooks"></a>Elementy runbook programu PowerShell

Elementy runbook programu PowerShell są oparte na programie Windows PowerShell.  Możesz bezpośrednio edytować kod elementu Runbook przy użyciu edytora tekstu w Azure Portal.  Można również użyć dowolnego edytora tekstu w trybie offline i [zaimportować element Runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Zalety

* Zaimplementuj wszystkie złożonej logiki przy użyciu programu PowerShell kodu bez przeprowadzania dodatkowej złożoności przepływu pracy programu PowerShell.
* Element Runbook uruchamia się szybciej niż w elementach runbook przepływu pracy programu PowerShell, ponieważ nie musi on być skompilowany przed uruchomieniem.
* Mogą być uruchamiane na platformie Azure lub w hybrydowych procesach roboczych elementów Runbook dla systemów Linux i Windows

### <a name="limitations"></a>Ograniczenia

* Musi być zaznajomiony ze skryptami programu PowerShell.
* Nie można użyć [przetwarzania równoległego](automation-powershell-workflow.md#parallel-processing) do wykonywania wielu akcji równolegle.
* Nie można użyć [punktów kontrolnych](automation-powershell-workflow.md#checkpoints) w celu wznowienia elementu Runbook, jeśli wystąpi błąd.
* Elementy Runbook przepływu pracy programu PowerShell i graficzne elementy Runbook można dołączać tylko jako podrzędne elementy Runbook za pomocą polecenia cmdlet Start-AzureAutomationRunbook, które tworzy nowe zadanie.

### <a name="known-issues"></a>Znane problemy

Poniżej przedstawiono bieżące znane problemy dotyczące elementów Runbook programu PowerShell.

* Elementy Runbook programu PowerShell nie mogą pobrać nieszyfrowanego [zasobu zmiennej](automation-variables.md) z wartością null.
* Elementy Runbook programu PowerShell nie mogą pobrać [zasobu zmiennej](automation-variables.md) z *~* w nazwie.
* Proces Get-Process w pętli w elemencie Runbook programu PowerShell może ulec awarii po około 80 iteracjach.
* Element Runbook programu PowerShell może zakończyć się niepowodzeniem, jeśli próbuje napisać dużą ilość danych do strumienia wyjściowego jednocześnie.   Zazwyczaj można obejść ten problem, wprowadzając tylko potrzebne informacje podczas pracy z dużymi obiektami.  Na przykład zamiast umieszczania czegoś takiego jak *Get-Process*, można wyprowadzić tylko wymagane pola za pomocą polecenia *Get-Process | Wybierz pozycję ProcessName, procesor CPU*.

## <a name="powershell-workflow-runbooks"></a>Elementy Runbook przepływu pracy programu PowerShell

Elementy Runbook przepływu pracy programu PowerShell są tekstowymi elementami Runbook opartymi na [przepływie pracy programu Windows PowerShell](automation-powershell-workflow.md).  Możesz bezpośrednio edytować kod elementu Runbook przy użyciu edytora tekstu w Azure Portal.  Można również użyć dowolnego edytora tekstu w trybie offline i [zaimportować element Runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Zalety

* Zaimplementuj wszystkie złożonej logiki przy użyciu kodu przepływu pracy programu PowerShell.
* Jeśli wystąpi błąd, użyj [punktów kontrolnych](automation-powershell-workflow.md#checkpoints) w celu wznowienia działania elementu Runbook.
* Użyj [przetwarzania równoległego](automation-powershell-workflow.md#parallel-processing) do wykonania wielu akcji równolegle.
* Może zawierać inne graficzne elementy Runbook i elementy Runbook przepływu pracy programu PowerShell jako podrzędne elementy Runbook do tworzenia przepływów pracy wysokiego poziomu.

### <a name="limitations"></a>Ograniczenia

* Autor musi znać przepływ pracy programu PowerShell.
* Element Runbook musi zająć się dodatkową złożonością przepływu pracy programu PowerShell, takich jak [obiekty deserializowane](automation-powershell-workflow.md#code-changes).
* Element Runbook zajmuje więcej czasu start niż elementy runbook programu PowerShell, który musi być skompilowany przed uruchomieniem.
* Elementy Runbook programu PowerShell można dołączać tylko jako podrzędne elementy Runbook za pomocą polecenia cmdlet Start-AzureAutomationRunbook, które tworzy nowe zadanie.
* Nie można wykonać w ramach hybrydowego procesu roboczego elementu Runbook systemu Linux

## <a name="python-runbooks"></a>Elementy Runbook języka Python

Kompilacja elementów Runbook języka Python w języku Python 2.  Możesz bezpośrednio edytować kod elementu Runbook przy użyciu edytora tekstu w Azure Portal lub za pomocą edytora tekstu w trybie offline i [zaimportować element Runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Zalety

* Korzystaj z niezawodnych bibliotek języka Python.
* Mogą być uruchamiane na platformie Azure lub w obu hybrydowych procesach roboczych elementów Runbook w systemie Linux. Hybrydowe procesy robocze elementów Runbook systemu Windows są obsługiwane z zainstalowanym środowiskiem [Python 2.7](https://www.python.org/downloads/release/latest/python2) .

### <a name="limitations"></a>Ograniczenia

* Musi być zaznajomiony ze skryptami języka Python.
* Tylko język Python 2 jest obsługiwany, co oznacza, że funkcje języka Python 3 nie powiodą się.
* Aby można było korzystać z bibliotek innych firm, należy [zaimportować pakiet](python-packages.md) do konta usługi Automation, które ma być używane.

## <a name="considerations"></a>Zagadnienia do rozważenia

Należy wziąć pod uwagę następujące dodatkowe zagadnienia podczas określania typu, który ma być używany dla określonego elementu Runbook.

* Nie można konwertować elementów Runbook z graficznej na typ tekstowy ani w inny sposób.
* Istnieją ograniczenia dotyczące używania elementów Runbook różnych typów jako podrzędnego elementu Runbook. Aby uzyskać więcej informacji, zobacz [podrzędne elementy Runbook w Azure Automation](automation-child-runbooks.md).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat graficznego tworzenia elementów Runbook, zobacz [Tworzenie graficzne w Azure Automation](automation-graphical-authoring-intro.md)
* Aby zrozumieć różnice między programem PowerShell i przepływami pracy programu PowerShell dla elementów Runbook, zobacz temat [Omówienie przepływu pracy programu Windows PowerShell](automation-powershell-workflow.md) .
* Aby uzyskać więcej informacji na temat tworzenia lub importowania elementu Runbook, zobacz [Tworzenie lub importowanie elementu Runbook](manage-runbooks.md)
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów dokumentacji i uczenia dotyczącej języka, zapoznaj się z dokumentacją programu [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
