---
title: Przegląd zestawu SDK Azure Automation Grpahical Runbook
description: W tym artykule opisano sposób korzystania z Azure Automation graficznego zestawu Runbook SDK
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a06c190931fdd0f49132f815b153c08ece68c9f3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849551"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Korzystanie z Azure Automation graficznego zestawu Runbook SDK

[Graficzne elementy Runbook](automation-graphical-authoring-intro.md) są elementami Runbook, które ułatwiają zarządzanie złożonością podstawowego kodu przepływu pracy programu Windows PowerShell lub programu PowerShell. Zestaw SDK tworzenia graficznego usługi Microsoft Azure Automation umożliwia deweloperom tworzenie i edytowanie graficznych elementów Runbook do użycia z usługą Azure Automation. Poniższe fragmenty kodu pokazują podstawowy przepływ tworzenia graficznego elementu Runbook z kodu.

## <a name="pre-requisites"></a>Wymagania wstępne

Aby rozpocząć, zaimportuj pakiet `Microsoft.Azure.Automation.GraphicalRunbook.Model` do projektu.

## <a name="create-a-runbook-object-instance"></a>Utwórz wystąpienie obiektu elementu Runbook

Odwołuje się do zestawu `Orchestrator.GraphRunbook.Model` i Utwórz wystąpienie klasy `Orchestrator.GraphRunbook.Model.GraphRunbook`:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Dodaj parametry elementu Runbook

Tworzenie wystąpienia `Orchestrator.GraphRunbook.Model.Parameter` obiektów i dodawanie ich do elementu Runbook:

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

## <a name="add-activities-and-links"></a>Dodawanie działań i linków

Utwórz wystąpienia działań odpowiednich typów i Dodaj je do elementu Runbook:

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

Działania są implementowane przez następujące klasy w przestrzeni nazw `Orchestrator.GraphRunbook.Model`:

|Klasa  |Działanie  |
|---------|---------|
|Polecenie polecenia     | Wywołuje polecenie programu PowerShell (polecenie cmdlet, funkcję itp.).        |
|InvokeRunbookActivity     | Wywołuje inny element Runbook w tekście.        |
|Połączenie     | Czeka na zakończenie wszystkich rozgałęzień przychodzących.        |
|WorkflowScriptActivity     | Wykonuje blok kodu przepływu pracy programu PowerShell lub programu PowerShell (w zależności od typu elementu Runbook) w kontekście elementu Runbook. Jest to zaawansowane narzędzie, ale nie powoduje ich nadmiernego użycia: interfejs użytkownika będzie wyświetlał ten blok skryptu jako tekst; aparat wykonywania będzie traktować podany blok jako czarny Box i nie będzie podejmować prób analizowania jego zawartości, z wyjątkiem podstawowej kontroli składni. Jeśli musisz tylko wywołać pojedyncze polecenie programu PowerShell, Preferuj polecenie.        |

> [!NOTE]
> Nie należy wyprowadzać własnych działań z dostarczonych klas: Azure Automation nie będzie można używać elementów Runbook z typami działań niestandardowych.

Parametry poleceń i InvokeRunbookActivity muszą być podane jako deskryptory wartości, a nie wartości bezpośrednie. Deskryptory wartości określają sposób tworzenia rzeczywistych wartości parametrów. Obecnie są dostępne następujące deskryptory wartości:


|Opis  |Definicja  |
|---------|---------|
|ConstantValueDescriptor     | Odwołuje się do zakodowanej wartości stałej.        |
|RunbookParameterValueDescriptor     | Odwołuje się do parametru elementu Runbook według nazwy.        |
|ActivityOutputValueDescriptor     | Odwołuje się do danych wyjściowych działań nadrzędnych, umożliwiając jedno działanie "Subskrybuj" dane generowane przez inne działanie.        |
|AutomationVariableValueDescriptor     | Odwołuje się do zasobu zmiennej usługi Automation według nazwy.         |
|AutomationCredentialValueDescriptor     | Odwołuje się do zasobu certyfikatu usługi Automation według nazwy.        |
|AutomationConnectionValueDescriptor     | Odwołuje się do zasobu połączenia usługi Automation według nazwy.        |
|PowerShellExpressionValueDescriptor     | Określa bezpłatne wyrażenie programu PowerShell, które zostanie ocenione tuż przed wywołaniem działania.  <br/>Jest to zaawansowane narzędzie, ale nie powoduje ich nadmiernego użycia: interfejs użytkownika wyświetli to wyrażenie jako tekst; aparat wykonywania będzie traktować podany blok jako czarny Box i nie będzie podejmować prób analizowania jego zawartości, z wyjątkiem podstawowej kontroli składni. Jeśli to możliwe, Preferuj bardziej szczegółowe deskryptory wartości.      |

> [!NOTE]
> Nie należy wyprowadzać własnych deskryptorów wartości z dostarczonych klas: Azure Automation nie będzie można używać elementów Runbook z niestandardowymi typami deskryptorów wartości.

Tworzenie wystąpienia linków łączących działania i dodawanie ich do elementu Runbook:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Zapisz element Runbook w pliku

Użyj `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` do serializacji elementu Runbook do ciągu:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Ten ciąg może być zapisany w pliku z rozszerzeniem **. graphrunbook** , a ten plik może zostać zaimportowany do Azure Automation.
Format serializowany może ulec zmianie w przyszłych wersjach `Orchestrator.GraphRunbook.Model.dll`. Firma Microsoft gwarantuje zgodność z poprzednimi wersjami: wszystkie elementy Runbook serializowane ze starszą wersją `Orchestrator.GraphRunbook.Model.dll` mogą zostać rozszeregowane przez dowolną nowszą wersję. Zgodność nie jest gwarantowana: element Runbook serializowany z nowszą wersją może nie być deserializowany przez starsze wersje.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat graficznych elementów Runbook w Azure Automation, zobacz [wprowadzenie do tworzenia graficznego](automation-graphical-authoring-intro.md)

