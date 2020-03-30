---
title: Elementy runbook podrzędne podrzędne w usłudze Azure Automation
description: W tym artykule opisano różne metody uruchamiania uruchomieniu w usłudze Azure Automation z innego systemu runbook i udostępniania informacji między nimi.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 42362a170f493afd51a5d4ee139620ad25b54e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367367"
---
# <a name="child-runbooks-in-azure-automation"></a>Elementy runbook podrzędne podrzędne w usłudze Azure Automation

Jest zalecaną praktyką w usłudze Azure Automation do pisania wielokrotnego, modułowych elementów runbook z dyskretną funkcją, która jest wywoływana przez inne elementy runbook. Nadrzędny element runbook często wywołuje jeden lub więcej obrażeń śunkowych podrzędnych w celu wykonania wymaganych funkcji. Istnieją dwa sposoby wywoływania podrzędnego śmigania podrzędnego i istnieją różne różnice, które należy zrozumieć, aby móc określić, który jest najlepszy dla scenariuszy.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Wywoływanie podrzędnego łańca przy użyciu wykonywania wbudowanego

Aby wywołać wbudowany element runbook z innego zestawu runbook, należy użyć nazwy zestawu runbook i podać wartości dla jego parametrów, podobnie jak można użyć działania lub polecenia cmdlet. Wszystkie elementy runbook na tym samym koncie automatyzacji są dostępne dla wszystkich innych, które mają być używane w ten sposób. Nadrzędny element runbook czeka na element runbook podrzędny, aby zakończyć przed przejściem do następnego wiersza, a wszelkie dane wyjściowe zwraca bezpośrednio do nadrzędnego.

Element Runbook wywoływany śródwierszowo jest uruchamiany w tym samym zadaniu co nadrzędny element Runbook. W historii zadań podrzędnego podręcznika nie ma żadnych wskazówek. Wszelkie wyjątki i wszelkie wyjścia strumienia z podrzędnego śmiękania są skojarzone z elementem nadrzędnym. To zachowanie powoduje mniejszą liczbę zadań i ułatwia ich śledzenie i rozwiązywanie problemów.

Po opublikowaniu wiązki ślikdowej wszystkie podrzędne, które wywołuje, muszą być już opublikowane. Powodem jest to, że usługa Azure Automation tworzy skojarzenia z dowolnymi elementami runbook podrzędnymi podczas kompilowania wiązki uruchomieniu. wiązaków. Jeśli podrzędne księgi runbook nie zostały jeszcze opublikowane, nadrzędny element runbook wydaje się opublikować poprawnie, ale generuje wyjątek po jego uruchomieniu. W takim przypadku można ponownie opublikować nadrzędny element runbook, aby poprawnie odwoływać się do ślikmy podrzędnych. Nie trzeba ponownie publikować nadrzędnego ślikdy wiązania, jeśli dowolny element runbook podrzędny jest zmieniany, ponieważ skojarzenie zostało już utworzone.

Parametry podrzędnego śmigięcie o nazwie inline mogą być dowolnego typu danych, w tym złożonych obiektów. Nie ma [serializacji JSON](start-runbooks.md#runbook-parameters), tak jak podczas uruchamiania systemu runbook przy użyciu portalu Azure lub polecenia cmdlet [Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook)

### <a name="runbook-types"></a>Typy elementów Runbook

Które typy elementów runbook można wywołać ze sobą?

* Element runbook programu [PowerShell](automation-runbook-types.md#powershell-runbooks) i [graficzny element runbook](automation-runbook-types.md#graphical-runbooks) można wywołać siebie w linii, ponieważ oba są oparte na programie PowerShell.
* Element runbook przepływu pracy programu [PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) i graficzny element runbook przepływu pracy programu PowerShell mogą wywoływać siebie w linii, ponieważ oba są oparte na przepływie pracy programu PowerShell.
* Typy programu PowerShell i typy przepływu pracy programu PowerShell nie `Start-AzAutomationRunbook`mogą wywoływać siebie nawzajem w linii wbudowanej i muszą używać .

Kiedy kolejność publikacji ma znaczenie?

Kolejność publikowania liksów likrządowych ma znaczenie tylko dla przepływu pracy programu PowerShell i graficznych likemi przepływu pracy programu PowerShell.

Gdy element runbook wywołuje element runbooka podrzędnego przepływu pracy w programie PowerShell przy użyciu wykonywania wbudowanego, używa nazwy systemu runbook. Nazwa musi zaczynać `.\\` się od tego, aby określić, że skrypt znajduje się w katalogu lokalnym.

### <a name="example"></a>Przykład

W poniższym przykładzie rozpoczyna się testowy element runbook podrzędny, który akceptuje złożony obiekt, wartość całkowitą i wartość logiczną. Dane wyjściowe podrzędnego elementu Runbook są przypisywane do zmiennej. W takim przypadku podrzędny system runbook jest uruchomieniu przepływa pracy programu PowerShell.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Oto ten sam przykład przy użyciu przewodniczącego programu PowerShell jako podrzędnego.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Uruchamianie podrzędnego zestawu runbook przy użyciu polecenia cmdlet

> [!IMPORTANT]
> Jeśli element runbook wywołuje element runbook podrzędny z `Start-AzAutomationRunbook` poleceniem cmdlet z `Wait` parametrem i element runbook podrzędny tworzy wynik obiektu, operacja może wystąpić błąd. Aby obejść ten błąd, zobacz [podrzędne elementy runbook z wyjściem obiektu,](troubleshoot/runbooks.md#child-runbook-object) aby dowiedzieć się, jak zaimplementować logikę do sondowania wyników przy użyciu polecenia cmdlet [Get-AzAutomationJobOutputRecord.](/powershell/module/az.automation/get-azautomationjoboutputrecord)

Można użyć `Start-AzAutomationRunbook` do uruchomienia systemu runbook, jak opisano w [Aby uruchomić program runbook z programem Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Istnieją dwa tryby użycia dla tego polecenia cmdlet. W jednym trybie polecenie cmdlet zwraca identyfikator zadania podczas tworzenia zadania dla śmięty podrzędnego. W innym trybie, który skrypt włącza, określając *Wait* parametru, polecenie cmdlet czeka, aż zadanie podrzędne zakończy się i zwraca dane wyjściowe z śmięty podrzędnego.

Zadanie z podrzędnego zestawu runbook rozpoczętego od polecenia cmdlet jest uruchamiane oddzielnie od zadania nadrzędnego zestawu runbook. To zachowanie powoduje więcej zadań niż uruchamianie elementu runbook wbudowanego i sprawia, że zadania trudniejsze do śledzenia. Element nadrzędny można uruchomić więcej niż jeden podrzędny element runbook asynchronicznie bez oczekiwania na każdy do wykonania. Dla tego równoległego wykonania wywołującego element runbook podrzędnych w linii, nadrzędny element runbook musi używać [równoległego słowa kluczowego](automation-powershell-workflow.md#parallel-processing).

Dane wyjściowe podrzędnego śmięka nie zwraca do nadrzędnego umrodu niezawodnie ze względu na chronometraż. Ponadto zmienne, takie `$VerbosePreference` `$WarningPreference`jak , i inne mogą nie być propagowane do śmigiełków podrzędnych. Aby uniknąć tych problemów, można uruchomić elementy runbook `Start-AzAutomationRunbook` podrzędnych `Wait` jako oddzielne zadania automatyzacji przy użyciu parametru. Ta technika blokuje element runbook nadrzędny do czasu ukończenia podrzędnego elementów runbook.

Jeśli nie chcesz, aby nadrzędny element runbook był blokowany podczas oczekiwania, `Start-AzAutomationRunbook` możesz `Wait` uruchomić element runbook podrzędny przy użyciu bez parametru. W takim przypadku system runbook musi używać [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) czekać na zakończenie zadania. Należy również użyć [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) i [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) do pobierania wyników.

Parametry podrzędnego śmiwalnika rozpoczętego od polecenia cmdlet są dostarczane jako tabela mieszania, zgodnie z opisem w [parametrach uruchomieniu](start-runbooks.md#runbook-parameters). . Można używać tylko prostych typów danych. Jeśli element Runbook ma parametr o złożonym typie danych, musi być wywoływany śródwierszowo.

Kontekst subskrypcji może zostać utracony podczas uruchamiania elementów runbook podrzędnych jako oddzielnych zadań. Dla podrzędnego zestawu runbook do wykonywania poleceń cmdlet modułu Az względem określonej subskrypcji platformy Azure, element podrzędny musi uwierzytelnić się w tej subskrypcji niezależnie od nadrzędnego zestawu runbook.

Jeśli zadania w ramach tego samego konta automatyzacji działają z więcej niż jedną subskrypcją, wybranie subskrypcji w jednym zadaniu może zmienić aktualnie wybrany kontekst subskrypcji dla innych zadań. Aby uniknąć tej `Disable-AzContextAutosave –Scope Process` sytuacji, należy użyć na początku każdego śmiętu. Ta akcja zapisuje tylko kontekst do tego wykonania elementów runbook.

### <a name="example"></a>Przykład

Poniższy przykład rozpoczyna podrzędny projekt runbook z parametrami, `Start-AzAutomationRunbook` a następnie czeka `Wait` na jego zakończenie przy użyciu polecenia cmdlet z parametrem. Po zakończeniu przykład zbiera dane wyjściowe polecenia cmdlet z podrzędnego śmięka. Aby `Start-AzAutomationRunbook`użyć , skrypt musi uwierzytelnić się w ramach subskrypcji platformy Azure.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Porównanie metod wywoływania podrzędnego podręcznika

W poniższej tabeli podsumowano różnice między dwoma sposobami wywoływania elementów runbook z innego elementów runbook.

|  | Śródwierszowo | Polecenie cmdlet |
|:--- |:--- |:--- |
| Zadanie |Podrzędne elementy Runbook są uruchamiane w tym samym zadaniu co element nadrzędny. |Tworzone jest osobne zadanie dla podrzędnego elementu Runbook. |
| Wykonanie |Przed kontynuowaniem nadrzędny element Runbook czeka na ukończenie działania podrzędnego elementu Runbook. |Nadrzędny element runbook jest kontynuowany natychmiast po uruchomieniu podrzędnego śmiękacza *lub* nadrzędny element runbook czeka na zakończenie zadania podrzędnego. |
| Dane wyjściowe |Nadrzędny element Runbook może bezpośrednio pobierać dane wyjściowe z podrzędnego elementu Runbook. |Nadrzędny element runbook musi pobrać dane wyjściowe z zadania bień podrzędny *lub* nadrzędny element runbook można bezpośrednio uzyskać dane wyjściowe z ego księgi chybienia podrzędnego. |
| Parametry |Wartości parametrów podrzędnego elementu Runbook są określane oddzielnie i mogą mieć dowolny typ danych. |Wartości dla podrzędnych parametrów podstawowych muszą być połączone w jeden hashtable. Ten skrót może zawierać tylko proste, tablicy i typów danych obiektów, które używają serializacji JSON. |
| Konto usługi Automation |Nadrzędny element runbook może używać tylko podrzędnego uruchomieniu na tym samym koncie automatyzacji. |Nadrzędne elementy runbook można użyć podrzędnego systemu runbook z dowolnego konta automatyzacji, z tej samej subskrypcji platformy Azure, a nawet z innej subskrypcji, do której masz połączenie. |
| Publikowanie |Podrzędny element Runbook należy opublikować przed opublikowaniem nadrzędnego elementu Runbook. |Podrzędny element runbook jest publikowany w dowolnym momencie przed uruchomieniem nadrzędnego ślikdu. |

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie uruchomieniu w usłudze Azure Automation](start-runbooks.md)
* [Dane wyjściowe i komunikaty w usłudze Azure Automation](automation-runbook-output-and-messages.md)