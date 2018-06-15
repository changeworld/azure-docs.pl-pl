---
title: Typy elementu Runbook usługi Automatyzacja Azure
description: 'Zawiera opis różnych typów elementów runbook, które można używać w automatyzacji Azure i zagadnienia, które należy wziąć pod uwagę podczas określania typu. '
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 94fbc1cf0232eaf48bb0be3c6edc0542142e4681
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196053"
---
# <a name="azure-automation-runbook-types"></a>Typy elementu runbook automatyzacji Azure
Automatyzacja Azure obsługuje kilka typów elementów runbook krótko opisane w poniższej tabeli.  Poniższe sekcje zawierają dodatkowe informacje na temat poszczególnych typów, łącznie z uwagi na kiedy należy używać każdego.

| Typ | Opis |
|:--- |:--- |
| [Element graficzny](#graphical-runbooks) |Na podstawie środowiska Windows PowerShell i utworzony i edytowany całkowicie edytora graficznego usługi w portalu Azure. |
| [Graficzny przepływ pracy programu PowerShell](#graphical-runbooks) |Oparta na przepływ pracy programu Windows PowerShell i utworzyć i edytować całkowicie w edytorze graficzny w portalu Azure. |
| [Program PowerShell](#powershell-runbooks) |Tekst elementu runbook oparte na skrypt programu Windows PowerShell. |
| [Przepływ pracy programu PowerShell](#powershell-workflow-runbooks) |Tekst elementu runbook oparte na przepływ pracy programu Windows PowerShell. |
| [Python](#python-runbooks) |Tekst elementu runbook oparte na języku Python. |

## <a name="graphical-runbooks"></a>Graficznych elementów runbook
[Graficzny](automation-runbook-types.md#graphical-runbooks) i elementów runbook graficzny przepływ pracy programu PowerShell są tworzone i edytować za pomocą edytora graficznego w portalu Azure.  Można wyeksportować je do pliku i zaimportować je na innym koncie automatyzacji, ale nie można utworzyć lub edytować je z innego narzędzia.  Graficznych elementów runbook generowania kodu programu PowerShell, ale nie można bezpośrednio wyświetlić lub zmodyfikować kod. Nie można przekonwertować graficznych elementów runbook do jednego z [formatach tekstowych](automation-runbook-types.md), ani runbook tekst można przekonwertować na format graficznego. Graficznych elementów runbook może zostać przekonwertowany do elementów runbook graficzny przepływ pracy programu PowerShell podczas importowania i na odwrót.

### <a name="advantages"></a>Zalety
* Visual skonfigurować łącze insert tworzenia modelu  
* Skupić się na jak dane przepływają przez proces  
* Wizualnego reprezentowania procesów zarządzania  
* Zawierają inne elementy runbook jako podrzędne elementy runbook do tworzenia przepływów pracy wysokiego poziomu  
* Zachęca moduły programowania  


### <a name="limitations"></a>Ograniczenia
* Nie można edytować elementu runbook poza portalu Azure.
* Może wymagać działania kodu, które zawiera kod programu PowerShell do wykonywania złożonej logiki.
* Nie można wyświetlić lub edytować bezpośrednio kod programu PowerShell, który jest tworzony przez graficzny przepływ pracy. Należy pamiętać, że można wyświetlić kod, który można utworzyć w żadnych działań kodu.

## <a name="powershell-runbooks"></a>Elementy runbook programu PowerShell
Elementy runbook programu PowerShell są oparte na programie Windows PowerShell.  Bezpośredniego edytowania kodu elementu runbook w portalu Azure za pomocą edytora tekstu.  Można również użyć dowolnego edytora tekstu w trybie offline i [zaimportuj element runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) w automatyzacji Azure.

### <a name="advantages"></a>Zalety
* Implementuje całą logikę złożonych z kodem PowerShell bez dodatkowej złożoności przepływu pracy programu PowerShell. 
* Element Runbook uruchamia szybciej niż w elementach runbook przepływu pracy programu PowerShell, ponieważ nie musi być skompilowany przed uruchomieniem.

### <a name="limitations"></a>Ograniczenia
* Należy zapoznać się z skryptów środowiska PowerShell.
* Nie można użyć [przetwarzanie równoległe](automation-powershell-workflow.md#parallel-processing) można wykonywać wiele akcji równolegle.
* Nie można użyć [punktów kontrolnych](automation-powershell-workflow.md#checkpoints) wznowienie elementu runbook w przypadku błędu.
* Elementy runbook przepływu pracy programu PowerShell i graficznych elementów runbook można tylko dołączone jako podrzędne elementy runbook za pomocą polecenia cmdlet Start-AzureAutomationRunbook, który tworzy nowe zadanie.

### <a name="known-issues"></a>Znane problemy
Oto obecnie znane problemy z elementów runbook programu PowerShell.

* Elementy runbook programu PowerShell nie można pobrać niezaszyfrowane [zasób zmiennej](automation-variables.md) o wartości null.
* Nie można pobrać elementów runbook programu PowerShell [zasób zmiennej](automation-variables.md) z *~* w nazwie.
* Get-Process w pętli w programie PowerShell elementu runbook może ulec awarii po około 80 iteracji. 
* Element runbook programu PowerShell może zakończyć się niepowodzeniem, jeśli próbuje zapisać bardzo dużą ilość danych do strumienia wyjściowego na raz.   Ten problem można zwykle obejść przez generowanie właśnie informacji potrzebnych podczas pracy z dużych obiektów.  Na przykład zamiast Generowanie przypominać *Get-Process*, można output tylko wymagane pola z *Get-Process | Wybierz parametr i procesora CPU*.

## <a name="powershell-workflow-runbooks"></a>Elementy runbook przepływu pracy programu PowerShell
Element runbook przepływu pracy programu PowerShell jest tekst elementów runbook na podstawie [przepływu pracy środowiska Windows PowerShell](automation-powershell-workflow.md).  Bezpośredniego edytowania kodu elementu runbook w portalu Azure za pomocą edytora tekstu.  Można również użyć dowolnego edytora tekstu w trybie offline i [zaimportuj element runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) w automatyzacji Azure.

### <a name="advantages"></a>Zalety
* Implementuje całą logikę złożonych z kodem przepływu pracy programu PowerShell.
* Użyj [punktów kontrolnych](automation-powershell-workflow.md#checkpoints) wznowienie elementu runbook w przypadku błędu.
* Użyj [przetwarzanie równoległe](automation-powershell-workflow.md#parallel-processing) można wykonywać wiele akcji równolegle.
* Mogą być inne graficznych elementów runbook i elementy runbook przepływu pracy programu PowerShell jako podrzędne elementy runbook do tworzenia przepływów pracy wysokiego poziomu.

### <a name="limitations"></a>Ograniczenia
* Autor należy zapoznać się z przepływu pracy programu PowerShell.
* Element Runbook musi uwzględniać dodatkowej złożoności przepływu pracy programu PowerShell takich jak [deserializacji obiektów](automation-powershell-workflow.md#code-changes).
* Elementu Runbook dłużej, aby uruchomić niż elementów runbook programu PowerShell, ponieważ musi on skompilowany przed uruchomieniem.
* Elementy runbook programu PowerShell można dołączyć jako podrzędne elementy runbook tylko za pomocą polecenia cmdlet Start-AzureAutomationRunbook, który tworzy nowe zadanie.

## <a name="python-runbooks"></a>Elementy runbook języka Python
Elementy runbook Python kompilacji 2 języka Python.  Kod elementu runbook za pomocą edytora tekstu, w portalu Azure można edytować bezpośrednio lub za pomocą dowolnego edytora tekstu w trybie offline i [zaimportuj element runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) w automatyzacji Azure.

### <a name="advantages"></a>Zalety
* Korzystanie z niezawodne standardowa biblioteka języka Python.

### <a name="limitations"></a>Ograniczenia
* Należy zapoznać się z skryptów języka Python.
* Tylko Python 2 jest obsługiwany w tej chwili, co oznacza, że określone funkcje Python 3 zakończy się niepowodzeniem.

### <a name="known-issues"></a>Znane problemy
Oto obecnie znane problemy z elementami runbook języka Python.

* Aby można było korzystać z innych bibliotek, elementu runbook musi być uruchamiane na [Windows hybrydowego Runbook Worker](https://docs.microsoft.com/azure/automation/automation-windows-hrw-install) lub [procesu roboczego elementu Runbook hybrydowego Linux](https://docs.microsoft.com/azure/automation/automation-linux-hrw-install) z bibliotekami już zainstalowane na komputerze przed Element runbook jest uruchomiony.

## <a name="considerations"></a>Zagadnienia do rozważenia
Należy wziąć pod uwagę następujące uwagi dodatkowe podczas określania typu dla określonego elementu runbook.

* Nie można przekonwertować typu tekstowej lub odwrotnie elementów runbook z graficznego.
* Istnieją ograniczenia dotyczące używania elementów runbook o różnych typach jako podrzędnego elementu runbook.  Zobacz [podrzędnych elementów runbook automatyzacji Azure](automation-child-runbooks.md) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat tworzenia graficznego elementu runbook, zobacz [tworzenia graficznego automatyzacji Azure](automation-graphical-authoring-intro.md)
* Aby poznać różnice między środowiska PowerShell i programu PowerShell przepływy pracy dla elementów runbook, zobacz [Learning Windows PowerShell Workflow](automation-powershell-workflow.md)
* Aby uzyskać więcej informacji na temat Tworzenie lub importowanie elementu Runbook, zobacz [Tworzenie lub importowanie elementu Runbook](automation-creating-importing-runbook.md)

