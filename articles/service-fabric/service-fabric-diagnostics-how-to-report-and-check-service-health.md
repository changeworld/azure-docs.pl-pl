---
title: Raport i Sprawdź kondycję z sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Więcej informacji, sposobu wysyłania raportów o kondycji z kodu usługi i sprawdzać stan usługi za pomocą narzędzi monitorowania kondycji, które zapewnia sieć szkieletowa usług Azure.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: mfussell
editor: ''
ms.assetid: 7c712c22-d333-44bc-b837-d0b3603d9da8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/2/2017
ms.author: dekapur
ms.openlocfilehash: 82ee3cbca40713d527f64ae4698cb9ce64a10215
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208424"
---
# <a name="report-and-check-service-health"></a>Tworzenie raportów i sprawdzanie kondycji usług
W przypadku wystąpienia problemów z usługami możliwość uwzględniał i naprawić zdarzenia i awarie zależy od możliwości szybko wykrywać problemy. Jeśli zgłaszanie problemów i błędów Menedżera kondycji sieci szkieletowej usług Azure w kodzie usługi, możesz użyć standardowego monitorowania narzędzi dostarczanych do sprawdzania stanu kondycji sieci szkieletowej usług kondycji.

Istnieją trzy sposoby raportowania kondycji usługi:

* Użyj [partycji](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) lub [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext) obiektów.  
  Można użyć `Partition` i `CodePackageActivationContext` obiektów do raportu kondycji elementów, które są częścią bieżącego kontekstu. Na przykład kod, który działa jako część repliki może raportować kondycję tylko w tej repliki, należącego do partycji i aplikacji, która jest częścią.
* Użyj `FabricClient`.   
  Można użyć `FabricClient` do raportu kondycji z kodem usługi, jeśli klaster nie jest [bezpiecznego](service-fabric-cluster-security.md) lub jeśli usługa jest uruchomiona z uprawnieniami administratora. Większość przypadków ze świata rzeczywistego nie za pomocą niezabezpieczonego klastrów, lub Udostępnij uprawnień administratora. Z `FabricClient`, mogą raportować kondycję na każda jednostka, która jest częścią klastra. Najlepiej, jeśli jednak kodu usługi należy wysłać tylko raporty, które są związane z własną kondycji.
* Użyć interfejsów API REST w klastra, aplikacji, wdrożonych aplikacji, usługi, pakiet usługi, partycji, repliki lub poziomy węzła. Może to służyć do raportu kondycji z znajdujące się w kontenerze.

W tym artykule przedstawiono przykład, w którym Raporty kondycji z kodem usługi. W przykładzie przedstawiono również sposób z narzędzi dostarczanych przez sieć szkieletowa usług może służyć do sprawdzania stanu kondycji. Ten artykuł ma być szybkie wprowadzenie do monitorowania możliwości sieci szkieletowej usług kondycji. Aby uzyskać szczegółowe informacje można znaleźć serii szczegółowe artykułów na temat kondycji rozpoczynających się od łącza na końcu tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne
Musi być zainstalowane następujące oprogramowanie:

* Program Visual Studio 2015 lub Visual Studio 2017
* Usługa SDK sieci szkieletowej

## <a name="to-create-a-local-secure-dev-cluster"></a>Aby utworzyć klaster lokalny deweloperów bezpiecznego
* Otwórz program PowerShell z uprawnieniami administratora i uruchom następujące polecenia:

![Polecenia, które pokazują, jak utworzyć klaster bezpiecznego deweloperów](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Aby wdrożyć aplikację i sprawdzić jego kondycji
1. Otwórz program Visual Studio jako administrator.
2. Tworzenie projektu za pomocą **usługi Stateful** szablonu.
   
    ![Tworzenie aplikacji platformy Service Fabric przy Stateful usługi](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
3. Naciśnij klawisz **F5** do uruchomienia aplikacji w trybie debugowania. Aplikacja jest wdrażana na lokalny klaster.
4. Po uruchomieniu aplikacji, kliknij prawym przyciskiem myszy ikonę Local Cluster Manager w obszarze powiadomień i wybierz **Zarządzaj klastrem lokalnym** z menu skrótów, aby otworzyć Eksploratora usługi sieć szkieletowa.
   
    ![Otwórz Eksploratora usługi sieć szkieletowa z obszaru powiadomień](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
5. Kondycja aplikacji powinna być wyświetlana tak jak ten obraz. W tej chwili aplikacja powinna być w dobrej kondycji bez błędów.
   
    ![Aplikacja działa prawidłowo w narzędziu Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
6. Możesz również sprawdzić kondycję przy użyciu programu PowerShell. Można użyć ```Get-ServiceFabricApplicationHealth``` można użyć do sprawdzenia kondycji aplikacji, a ```Get-ServiceFabricServiceHealth``` do sprawdzenia kondycji usługi. Raport o kondycji dla tej samej aplikacji w programie PowerShell jest do tego obrazu.
   
    ![Aplikacja działa prawidłowo w programie PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Aby dodać niestandardowe kondycji zdarzenia do kodu usługi
Szablony projektu sieci szkieletowej usług w programie Visual Studio zawiera przykładowy kod. W poniższej procedurze pokazano, jak mogą raportować kondycję niestandardowych zdarzeń w kodzie usługi. Raporty takie wyświetlane automatycznie w standardowych narzędzi do monitorowania kondycji, czy Usługa Service Fabric realizuje, takich jak Service Fabric Explorer, widok kondycji portalu Azure i programu PowerShell.

1. Otwórz ponownie aplikację utworzonego wcześniej w programie Visual Studio lub Utwórz nową aplikację przy użyciu **usługi Stateful** szablonu Visual Studio.
2. Otwórz plik Stateful1.cs i Znajdź `myDictionary.TryGetValueAsync` wywołanie w `RunAsync` metody. Widać, że ta metoda zwraca `result` zawierający bieżącą wartość licznika, ponieważ klucza logikę w tej aplikacji jest zachowanie wynik jest uruchomiona. Jeśli była to rzeczywista aplikacji i brak wyniku reprezentowane awarii, czy chcesz Flaga tego zdarzenia.
3. Aby zgłosić zdarzenie kondycji w przypadku braku wyników reprezentuje awarii, należy dodać następujące kroki.
   
    a. Dodaj `System.Fabric.Health` przestrzeni nazw do pliku Stateful1.cs.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Dodaj następujący kod po `myDictionary.TryGetValueAsync` wywołania
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Firma Microsoft raport kondycji repliki, ponieważ jest raportowany przez usługi stanowej. `HealthInformation` Parametr zapisuje informacje o problemie kondycji, który jest raportowany.
   
    Jeśli chcesz utworzyć usługi bezstanowej, należy użyć poniższego kodu
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
4. Jeśli usługa jest uruchomiona z uprawnieniami administratora lub klastra nie jest [bezpiecznego](service-fabric-cluster-security.md), można również użyć `FabricClient` do raportu kondycji, jak pokazano w poniższych krokach.  
   
    a. Utwórz `FabricClient` wystąpienie po `var myDictionary` deklaracji.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Dodaj następujący kod po `myDictionary.TryGetValueAsync` wywołania.
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
5. Załóżmy symulować tego błędu i wyświetlić je w narzędziach do monitorowania kondycji. Aby symulować niepowodzenia komentarz w pierwszym wierszu kodu raportowania kondycji dodanego wcześniej. Po komentarz pierwszy wiersz, kod będzie wyglądać jak w następującym przykładzie.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Ten kod generowane raport o kondycji, w każdym `RunAsync` wykonuje. Po wprowadzeniu zmian, naciśnij klawisz **F5** do uruchomienia aplikacji.
6. Po uruchomieniu aplikacji, otwórz Eksploratora usługi sieć szkieletowa, aby sprawdzić kondycji tej aplikacji. Teraz, Service Fabric Explorer pokazuje, że aplikacja jest zła. Wynika to z błędu, który został zgłoszony z kodu, że dodane wcześniej.
   
    ![Zła aplikacji w narzędziu Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
7. W przypadku wybrania repliki podstawowej w widoku drzewa Eksploratora usługi sieć szkieletowa będzie informacja, że **kondycja** zbyt wskazuje błąd. Service Fabric Explorer wyświetla również szczegóły raportu kondycji, które zostały dodane do `HealthInformation` parametru w kodzie. Widać tego samego raportów kondycji w programie PowerShell i portalu Azure.
   
    ![Kondycja repliki w narzędziu Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Ten raport pozostaje w Menedżera kondycji, dopóki nie został on zastąpiony przez inny raport lub do momentu usunięcia tej repliki. Ponieważ firma Microsoft nie określono `TimeToLive` dla tego raportu kondycji w `HealthInformation` obiektu raportu nigdy nie wygasa.

Zaleca się, że kondycji należy podać na poziomie będącego w tym przypadku jest repliką. Można również raporty kondycji dotyczące `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Do raportu kondycji na `Application`, `DeployedApplication`, i `DeployedServicePackage`, użyj `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Kolejne kroki
* [Nowości dotyczące kondycji sieci szkieletowej usług](service-fabric-health-introduction.md)
* [Interfejs API REST dla raportowania kondycji usługi](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [Interfejs API REST dla raportowania kondycji aplikacji](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

