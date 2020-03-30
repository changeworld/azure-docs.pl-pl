---
title: Typy elementów runbook automatyzacji platformy Azure
description: 'W tym artykule opisano różne typy elementów runbook, których można użyć w usłudze Azure Automation i zagadnienia, które należy wziąć pod uwagę przy określaniu typu, którego chcesz użyć. '
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 6346c29210b6390f11c884ff51e0b60af89bbbb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278612"
---
# <a name="azure-automation-runbook-types"></a>Typy elementów runbook usługi Azure Automation

Usługa Azure Automation obsługuje kilka typów elementów runbook, które są krótko opisane w poniższej tabeli.  Poniższe sekcje zawierają dalsze informacje na temat każdego typu, w tym kwestie dotyczące tego, kiedy należy z nich korzystać.

| Typ | Opis |
|:--- |:--- |
| [Element graficzny](#graphical-runbooks)|Na podstawie programu Windows PowerShell i utworzone i edytowane całkowicie w edytorze graficznym w witrynie Azure portal. |
| [Graficzny przepływ pracy programu PowerShell](#graphical-runbooks)|Na podstawie przepływu pracy programu Windows PowerShell i utworzone i edytowane całkowicie w edytorze graficznym w witrynie Azure portal. |
| [Powershell](#powershell-runbooks) |Podręcznik na podstawie skryptu programu Windows PowerShell. |
| [Przepływ pracy programu PowerShell](#powershell-workflow-runbooks)|Podręcznikowy program runbook oparty na przepływie pracy programu Windows PowerShell. |
| [Python](#python-runbooks) |Podręcznik podstawowy oparty na Pythonie. |

## <a name="graphical-runbooks"></a>Graficzne programy runbook

[Graficzne](automation-runbook-types.md#graphical-runbooks) i graficzne programy PowerShell Workflow runbooks są tworzone i edytowane za pomocą edytora graficznego w witrynie Azure portal.  Można wyeksportować je do pliku, a następnie zaimportować do innego konta automatyzacji. Nie można ich jednak tworzyć ani edytować za pomocą innego narzędzia. Graficzne programy runbook generują kod programu PowerShell, ale nie można bezpośrednio wyświetlić ani zmodyfikować kodu. Eksmisków graficznych nie można przekonwertować na jeden z [formatów tekstowych](automation-runbook-types.md)ani nie można przekonwertować podręcznika na format graficzny. Graficzne programy runbook można konwertować na graficzne programy runbook przepływu pracy programu PowerShell podczas importowania i odwrotnie.

### <a name="advantages"></a>Zalety

* Wizualny model tworzenia konfiguracji wstawiania łączy
* Skoncentruj się na przepływie danych przez proces
* Wizualne reprezentowanie procesów zarządzania
* Uwzględnianie innych śmięty jako umniech ydów podrzędnych w celu tworzenia przepływów pracy wysokiego poziomu
* Zachęca do programowania modułowego

### <a name="limitations"></a>Ograniczenia

* Nie można edytować uruchomieniu poza witryną Azure portal.
* Może wymagać działania Code zawierającego kod programu PowerShell do wykonywania złożonej logiki.
* Nie można wyświetlić ani bezpośrednio edytować kodu programu PowerShell utworzonego przez graficzny przepływ pracy. Można wyświetlić kod utworzony w dowolnej działaniach Code.
* Nie można uruchomić w hybrydowym uziemiacza systemu Linux

## <a name="powershell-runbooks"></a>Runbooki programu PowerShell

Podręczniki programu PowerShell są oparte na programie Windows PowerShell.  Bezpośrednio edytować kod życiówki za pomocą edytora tekstu w witrynie Azure portal.  Można również użyć dowolnego edytora tekstu w trybie offline i [zaimportować program runbook](manage-runbooks.md) do usługi Azure Automation.

### <a name="advantages"></a>Zalety

* Zaimplementuj całą złożoną logikę za pomocą kodu programu PowerShell bez dodatkowych złożoności przepływu pracy programu PowerShell.
* Runbook uruchamia się szybciej niż runbook przepływu pracy programu PowerShell, ponieważ nie trzeba kompilować przed uruchomieniem.
* Można uruchomić na platformie Azure lub na obu hybrydowych robotach runbook systemu Linux i Windows

### <a name="limitations"></a>Ograniczenia

* Musi być zaznajomiony ze skryptami programu PowerShell.
* Nie można użyć [przetwarzania równoległego](automation-powershell-workflow.md#parallel-processing) do wykonywania wielu akcji równolegle.
* Nie można użyć [punktów kontrolnych,](automation-powershell-workflow.md#checkpoints) aby wznowić element runbook, jeśli występuje błąd.
* Runbooki przepływu pracy programu PowerShell i graficzne życiorysy można uwzględnić tylko jako podręczniki podrzędne przy użyciu polecenia cmdlet Start-AzureAutomationRunbook, które tworzy nowe zadanie.

### <a name="known-issues"></a>Znane problemy

Poniżej przedstawiono bieżące znane problemy z podręcznikami runbook programu PowerShell.

* Nie można pobrać niezaszyfrowanego [zasobu zmiennej](automation-variables.md) o wartości null.
* Nie można pobrać [składnika aktywów zmiennych](automation-variables.md) w *~* nazwie.
* Get-Process w pętli w elemicie runbook programu PowerShell może ulec awarii po około 80 iteracji.
* Runbook programu PowerShell może zakończyć się niepowodzeniem, jeśli próbuje zapisać dużą ilość danych do strumienia wyjściowego na raz.   Zazwyczaj można obejść ten problem, wyprowadzając tylko informacje potrzebne podczas pracy z dużymi obiektami.  Na przykład zamiast wyprowadzać coś takiego jak *Get-Process,* można wyprowadzić tylko wymagane pola za pomocą *get-process | Wybierz ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>Runy przepływu pracy programu PowerShell

Runy przepływu pracy programu PowerShell są podręcznikami runbook opartymi na przepływie pracy programu [Windows PowerShell](automation-powershell-workflow.md).  Bezpośrednio edytować kod życiówki za pomocą edytora tekstu w witrynie Azure portal.  Można również użyć dowolnego edytora tekstu w trybie offline i [zaimportować program runbook](manage-runbooks.md) do usługi Azure Automation.

### <a name="advantages"></a>Zalety

* Zaimplementuj całą złożoną logikę za pomocą kodu przepływu pracy programu PowerShell.
* Użyj [punktów kontrolnych,](automation-powershell-workflow.md#checkpoints) aby wznowić element runbook, jeśli występuje błąd.
* Użyj [przetwarzania równoległego](automation-powershell-workflow.md#parallel-processing) do wykonywania wielu akcji równolegle.
* Może zawierać inne programy runbook graficzne i runbook przepływu pracy programu PowerShell jako umownicza podrzędne w celu tworzenia przepływów pracy wysokiego poziomu.

### <a name="limitations"></a>Ograniczenia

* Autor musi być zaznajomiony z przepływem pracy programu PowerShell.
* Runbook musi radzić sobie z dodatkową złożonością przepływu pracy programu PowerShell, takich jak [obiekty deserializowane.](automation-powershell-workflow.md#code-changes)
* Uruchomienie jest trwa dłużej niż runbook programu PowerShell, ponieważ musi zostać skompilowane przed uruchomieniem.
* Podręczniki programu PowerShell można uwzględnić tylko jako podrzędne podręczniki, korzystając z polecenia cmdlet Start-AzureAutomationRunbook, które tworzy nowe zadanie.
* Nie można uruchomić w hybrydowym uziemiacza systemu Linux

## <a name="python-runbooks"></a>Runbooki pythona

Python runbooks kompilować w Pythonie 2.  Można bezpośrednio edytować kod życiornika za pomocą edytora tekstu w witrynie Azure portal lub za pomocą edytora tekstu w trybie offline i [zaimportować element runbook](manage-runbooks.md) do usługi Azure Automation.

### <a name="advantages"></a>Zalety

* Korzystaj z niezawodnych bibliotek Pythona.
* Można uruchomić na platformie Azure lub na obu hybrydowych workers y uruchomieniu systemu Linux. Hybrydowe środowiska owe pracowników systemu Windows są obsługiwane z [zainstalowanym systemem Python2.7.](https://www.python.org/downloads/release/latest/python2)

### <a name="limitations"></a>Ograniczenia

* Musi być zaznajomiony z skryptów Pythona.
* Tylko Python 2 jest obecnie obsługiwany, co oznacza, że określone funkcje Pythona 3 nie powiodą się.
* Aby korzystać z bibliotek innych firm, należy [zaimportować pakiet](python-packages.md) do konta automatyzacji, aby był używany.

## <a name="considerations"></a>Zagadnienia do rozważenia

Należy wziąć pod uwagę następujące dodatkowe uwagi przy określaniu, który typ ma być używany dla określonego elementów runbook.

* Nie można konwertować tekstów z graficznego na tekstowy ani na odwrót.
* Istnieją ograniczenia dotyczące używania elementów runbook różnych typów jako elementów elementów śmiętnych podrzędnych. Aby uzyskać więcej informacji, zobacz [Podrzędne elementy runbook w usłudze Azure Automation](automation-child-runbooks.md).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o tworzeniem uruchomieniu grafików, zobacz [Tworzenie graficzne w usłudze Azure Automation](automation-graphical-authoring-intro.md)
* Aby zrozumieć różnice między przepływami pracy programu PowerShell i PowerShell dla uruchomieniu, zobacz [Uczenie się przepływu pracy programu Windows PowerShell](automation-powershell-workflow.md)
* Aby uzyskać więcej informacji na temat tworzenia lub importowania ekwiwania ekwiwania, zobacz [Tworzenie lub importowanie ekwiwania ekwiwania](manage-runbooks.md)
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym odwoływania się do języka i modułów szkoleniowych, zobacz [Dokumenty programu PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
