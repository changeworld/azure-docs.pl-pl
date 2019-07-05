---
title: Przegląd elementu runbook usługi Azure Automation Grpahical zestawu SDK
description: W tym artykule opisano sposób korzystania z usługi Azure Automation graficznego elementu Runbook zestawu SDK
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e4229079a1fa50295eef85b42f91bbc1b4a21fc3
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478588"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Użyj usługi Azure Automation graficznego elementu runbook zestawu SDK

[Graficzne elementy runbook](automation-graphical-authoring-intro.md) to elementy runbook, który ułatwia zarządzanie złożonością odpowiedni kod programu Windows PowerShell lub przepływie pracy programu PowerShell. SDK platformy Microsoft Azure Automation graficzny tworzenia umożliwia deweloperom tworzenie i edytowanie graficznych elementów Runbook do użycia z usługą Azure Automation. Poniższe fragmenty kodu przedstawiają podstawowy proces tworzenia graficznego elementu runbook z poziomu kodu.

## <a name="pre-requisites"></a>Wymagania wstępne

Aby rozpocząć, należy zaimportować `Microsoft.Azure.Automation.GraphicalRunbook.Model` pakietu do projektu.

## <a name="create-a-runbook-object-instance"></a>Utwórz wystąpienie obiektu elementów runbook

Odwołanie `Orchestrator.GraphRunbook.Model` zestawu i Utwórz wystąpienie obiektu `Orchestrator.GraphRunbook.Model.GraphRunbook` klasy:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Dodaj parametry elementu runbook

Utwórz wystąpienie `Orchestrator.GraphRunbook.Model.Parameter` obiektów i dodać je do elementu runbook:

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Dodawanie działań i łączy

Utwórz wystąpienie działania odpowiednie typy i dodać je do elementu runbook:

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

Działania są implementowane przez następujące klasy w `Orchestrator.GraphRunbook.Model` przestrzeni nazw:

|Klasa  |Działanie  |
|---------|---------|
|CommandActivity     | Wywołuje polecenie programu PowerShell (polecenia cmdlet, funkcji itp.).        |
|InvokeRunbookActivity     | Wywołuje innego wbudowanego elementu runbook.        |
|JunctionActivity     | Czeka, aż wszystkie przychodzące gałęzie zakończyć.        |
|WorkflowScriptActivity     | Wykonuje blok kodu programu PowerShell lub przepływie pracy programu PowerShell (w zależności od typu elementu runbook) w kontekście elementu runbook. Jest to zaawansowane narzędzie, ale nie nadużywać: interfejs użytkownika wyświetli ten blok skryptu jako tekst. Aparat wykonywania traktują bloku podana jako czarne pole i spowoduje, że próby analizowania jego zawartości, z wyjątkiem sprawdzanie podstawowej składni. Jeśli potrzebujesz wywołać jednego polecenia programu PowerShell, należy najpierw CommandActivity.        |

> [!NOTE]
> Pochodzi od klasy podana własnych działań: Usługa Azure Automation nie będzie elementów runbook za pomocą typów działań niestandardowych.

Parametry CommandActivity i InvokeRunbookActivity musi zostać podana jako wartość deskryptorów wartości nie bezpośrednio. Wartość deskryptorów Określ, jak powinny być tworzone wartości rzeczywistych parametrów. Obecnie dostępne są następujące deskryptory wartość:


|Deskryptor  |Definicja  |
|---------|---------|
|ConstantValueDescriptor     | Odnosi się do zakodowanych wartości stałej.        |
|RunbookParameterValueDescriptor     | Odnosi się do parametru elementu runbook według nazwy.        |
|ActivityOutputValueDescriptor     | Odnosi się do działanie w strumieniu przychodzącym dane wyjściowe, umożliwiając jedno działanie subskrybować "" danych wytworzonych przez innego działania.        |
|AutomationVariableValueDescriptor     | Odnosi się do zasób zmiennej usługi Automation o nazwie.         |
|AutomationCredentialValueDescriptor     | Odnosi się do zasób certyfikatu usługi Automation o nazwie.        |
|AutomationConnectionValueDescriptor     | Odnosi się do elementu zawartości połączenia usługi Automation o nazwie.        |
|PowerShellExpressionValueDescriptor     | Określa dowolnych wyrażenie programu PowerShell, które zostanie obliczone tuż przed wywołaniem działania.  <br/>Jest to zaawansowane narzędzie, ale nie nadużywać: interfejs użytkownika wyświetli tego wyrażenia jako tekst. Aparat wykonywania traktują bloku podana jako czarne pole i spowoduje, że próby analizowania jego zawartości, z wyjątkiem sprawdzanie podstawowej składni. Jeśli to możliwe, należy najpierw dokładniejszą wartość deskryptorów.      |

> [!NOTE]
> Pochodzi od klasy podana deskryptory własne wartości: Usługa Azure Automation nie będzie elementów runbook za pomocą niestandardowej wartości deskryptora typu.

Utwórz wystąpienie działania łącza i dodać je do elementu runbook:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Zapisz element runbook do pliku

Użyj `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` można serializować elementu runbook na ciąg:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Te parametry można zapisywać do pliku za pomocą **graphrunbook** rozszerzenie i ten plik można zaimportować do usługi Azure Automation.
Format serializacji mogą ulec zmianie w przyszłych wersjach `Orchestrator.GraphRunbook.Model.dll`. Gwarantujemy, zgodności z poprzednimi wersjami: każdego elementu runbook, które było serializować ją przy użyciu starszej wersji programu `Orchestrator.GraphRunbook.Model.dll` może być zdeserializowany przy dowolnym nowszej wersji. Nie ma żadnej gwarancji zgodność z nowszymi wersjami: serializacji przy użyciu nowszej wersji elementu runbook może nie być można rozszeregować przez starsze wersje.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat graficznych elementów Runbook w usłudze Azure Automation, zobacz [wprowadzenie do tworzenia elementów graficznych](automation-graphical-authoring-intro.md)

