---
title: Omówienie zestawu SDK interfejsu api dla systemu Azure Automation Grpahical
description: W tym artykule opisano sposób korzystania z zestawu SDK graficznego podręcznika azure automation
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: d4dcf6681ade977847c204dd1237f7cd7a67775e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75418260"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Korzystanie z zestawu SDK graficznego systemu Azure Automation

[Graficzne programy runbook](automation-graphical-authoring-intro.md) są elementami runbook, które pomagają zarządzać złożonością podstawowego kodu przepływu pracy programu Windows PowerShell lub programu PowerShell. Zestaw SDK tworzenia grafiki automatyzacji usługi Microsoft Azure Automation umożliwia deweloperom tworzenie i edytowanie wiązek autorskich do deweloperów do użytku z usługą Azure Automation. Poniższe fragmenty kodu pokazują podstawowy przepływ tworzenia graficznego elementów runbook z kodu.

## <a name="pre-requisites"></a>Wymagania wstępne

Aby rozpocząć, `Microsoft.Azure.Automation.GraphicalRunbook.Model` zaimportuj pakiet do projektu.

## <a name="create-a-runbook-object-instance"></a>Tworzenie wystąpienia obiektu egoisty

Odwołanie `Orchestrator.GraphRunbook.Model` się do zestawu i `Orchestrator.GraphRunbook.Model.GraphRunbook` utworzyć wystąpienie klasy:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Dodawanie parametrów umnienia

Tworzenie wystąpienia `Orchestrator.GraphRunbook.Model.Parameter` obiektów i dodawanie ich do łańczy ekwidu:

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

Tworzenie wystąpienia działań odpowiednich typów i dodawanie ich do elementów runbook:

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

Działania są implementowane przez następujące `Orchestrator.GraphRunbook.Model` klasy w obszarze nazw:

|Klasa  |Działanie  |
|---------|---------|
|Działania poleceń     | Wywołuje polecenie programu PowerShell (polecenie cmdlet, funkcja itp.).        |
|InvokeRunbookAktywność     | Wywołuje inny wbudowany podręcznik.        |
|Działalność skrzyżowań     | Czeka na zakończenie wszystkich przychodzących gałęzi.        |
|Działania w języku roboczym     | Wykonuje blok kodu przepływu pracy programu PowerShell lub programu PowerShell (w zależności od typu elementów runbook) w kontekście elementów runbook. Jest to potężne narzędzie, ale nie należy go nadużywania: interfejs użytkownika pokaże ten blok skryptu jako tekst; aparat wykonywania będzie traktować dostarczony blok jako czarne pole i nie będzie podejmować żadnych prób analizowania jego zawartości, z wyjątkiem podstawowego sprawdzania składni. Jeśli wystarczy wywołać pojedyncze polecenie programu PowerShell, preferuj CommandActivity.        |

> [!NOTE]
> Nie należy wyprowadzać własnych działań z podanych klas: Usługa Azure Automation nie będzie mogła używać elementów runbook z niestandardowymi typami działań.

CommandActivity i InvokeRunbookActivity parametry muszą być podane jako deskryptory wartości, a nie bezpośrednie wartości. Deskryptory wartości określają sposób sporządzania rzeczywistych wartości parametrów. Obecnie dostępne są następujące deskryptory wartości:


|Deskryptora  |Definicja  |
|---------|---------|
|Deskryptor ConstantValue     | Odnosi się do wartości stałej zakodowane.        |
|Deskryptor języka RunbookParameterValue     | Odnosi się do parametru runbook według nazwy.        |
|Deskryptor aplikacji ActivityOutputValueDescriptor     | Odnosi się do danych wyjściowych działania nadrzędnego, umożliwiając jedno działanie do "subskrybowania" danych utworzonych przez inne działanie.        |
|AutomationVariableValueDeptor     | Odnosi się do zasobu zmiennej automatyzacji według nazwy.         |
|AutomationCredentialValueDeptor     | Odnosi się do zasobu certyfikatu automatyzacji według nazwy.        |
|Deskryptor AutomationConnectionValueDescriptor     | Odnosi się do zasobu połączenia automatyzacji według nazwy.        |
|Deskryptor programu PowerShellExpressionValueDescriptor     | Określa wyrażenie programu PowerShell w postaci swobodnej, które zostanie ocenione tuż przed wywołaniem działania.  <br/>Jest to potężne narzędzie, ale nie należy go nadużywania: interfejs użytkownika pokaże to wyrażenie jako tekst; aparat wykonywania będzie traktować dostarczony blok jako czarne pole i nie będzie podejmować żadnych prób analizowania jego zawartości, z wyjątkiem podstawowego sprawdzania składni. Jeśli to możliwe, preferuj bardziej szczegółowe deskryptory wartości.      |

> [!NOTE]
> Nie należy wyprowadzać własnych deskryptorów wartości z podanych klas: usługa Azure Automation nie będzie mogła używać elementów runbook z niestandardowymi typami deskryptora wartości.

Tworzenie wystąpienia łączy łączących działania i dodawanie ich do egonatu:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Zapisywanie emuńnika w pliku

Służy `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` do serializacji śmigania do ciągu:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Ten ciąg można zapisać w pliku z rozszerzeniem **.graphrunbook,** a ten plik można zaimportować do usługi Azure Automation.
Format serializowany może ulec zmianie `Orchestrator.GraphRunbook.Model.dll`w przyszłych wersjach programu . Obiecujemy zgodność wsteczną: każdy element runbook serializowany ze starszą wersją `Orchestrator.GraphRunbook.Model.dll` może być deserializowany przez dowolną nowszą wersję. Zgodność z przekazywaniem dalej nie jest gwarantowana: system runbook serializowany z nowszą wersją może nie być deserializowalny przez starsze wersje.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o elementach ekwolucie graficznym w usłudze Azure Automation, zobacz [Wprowadzenie do tworzenia grafiki](automation-graphical-authoring-intro.md)

