---
title: Tworzenie raportów i sprawdzanie kondycji za pomocą usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Informacje o sposobie wysyłania raportów o kondycji z kodu usługi oraz sprawdzić kondycję usługi przy użyciu narzędzi monitorowania kondycji, udostępnianych przez usługi Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: mfussell
editor: ''
ms.assetid: 7c712c22-d333-44bc-b837-d0b3603d9da8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 0db341a9e36d61761321821de5631a564adea050
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66428171"
---
# <a name="report-and-check-service-health"></a>Tworzenie raportów i sprawdzanie kondycji usług
W przypadku wystąpienia problemów z usług możliwość odpowiedzieć i rozwiązać zdarzenia i awarii zależy od możliwości, aby szybko wykrywać problemy. Jeśli raportów problemów i błędów do Menedżera kondycji usługi Azure Service Fabric z poziomu kodu usługi, możesz użyć standardowego kondycji narzędzia, które Usługa Service Fabric udostępnia, aby sprawdzić stan kondycji monitorowania.

Istnieją trzy sposoby raportowania kondycji usługi:

* Użyj [partycji](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) lub [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext) obiektów.  
  Możesz użyć `Partition` i `CodePackageActivationContext` obiektów kondycji elementów, które są częścią bieżącego kontekstu. Na przykład kod, który działa jako część repliki można raportować kondycję tylko dla tej repliki, partycji, który należy do i aplikacji, która jest częścią.
* Użyj `FabricClient`.   
  Możesz użyć `FabricClient` kondycję raportu z poziomu kodu usługi, jeśli klaster nie jest [bezpiecznego](service-fabric-cluster-security.md) lub jeśli usługa jest uruchomiona z uprawnieniami administratora. Większość scenariuszy w rzeczywistych warunkach nie używaj niezabezpieczonych klastrów lub podaj uprawnienia administratora. Za pomocą `FabricClient`, można raportować kondycję na każda jednostka, która jest częścią klastra. Najlepiej, jeśli jednak kod usługi powinien wysyłać tylko raporty, które są powiązane z własną kondycji.
* Użyć interfejsów API REST w klaster, aplikacji, wdrożonej aplikacji, usługi, pakiet usługi, partycji, repliki lub poziomy węzła. To może służyć do raportowania kondycji z znajdujące się w kontenerze.

W tym artykule przedstawiono przykład, w którym Raporty kondycji z kodem usługi. W przykładzie pokazano również, jak używać narzędzi dostarczanych przez usługę Service Fabric można sprawdzić stanu kondycji. Ten artykuł ma być krótkie wprowadzenie do monitorowania możliwości usługi Service Fabric kondycji. Aby uzyskać szczegółowe informacje można znaleźć serii szczegółowe artykuły na temat kondycji rozpoczynających się od łącza na końcu tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne
Musisz mieć zainstalowane następujące oprogramowanie:

* Visual Studio 2015 lub Visual Studio 2019 r.
* Zestaw SDK usługi Service Fabric

## <a name="to-create-a-local-secure-dev-cluster"></a>Aby utworzyć klaster lokalny dev bezpiecznego
* Otwórz program PowerShell z uprawnieniami administratora i uruchom następujące polecenia:

![Polecenia, które pokazują, jak utworzyć klaster bezpiecznego dev](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Aby wdrożyć aplikację i sprawdzić jego kondycję
1. Otwórz program Visual Studio jako administrator.
1. Utwórz projekt za pomocą **usługa stanowa** szablonu.
   
    ![Tworzenie aplikacji usługi Service Fabric przy użyciu usługi stanowe](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Naciśnij klawisz **F5** do uruchamiania aplikacji w trybie debugowania. Aplikacja jest wdrażana w klastrze lokalnym.
1. Po uruchomieniu aplikacji, kliknij prawym przyciskiem myszy ikonę Menedżer klastra lokalnego, w obszarze powiadomień i wybierz **Zarządzaj klastrem lokalnym** z menu skrótów, aby otworzyć narzędzie Service Fabric Explorer.
   
    ![Otwórz narzędzie Service Fabric Explorer z obszaru powiadomień](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. Kondycja aplikacji powinna być wyświetlana tak jak ten obraz. W tej chwili, aplikacja powinna być w dobrej kondycji bez błędów.
   
    ![Dobrej kondycji aplikacji w narzędziu Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Możesz również sprawdzić kondycję przy użyciu programu PowerShell. Możesz użyć ```Get-ServiceFabricApplicationHealth``` można użyć do sprawdzenia kondycji aplikacji, a ```Get-ServiceFabricServiceHealth``` do sprawdzenia kondycji usługi. Raport o kondycji dla tej samej aplikacji w programie PowerShell jest do tego obrazu.
   
    ![Aplikacja działa prawidłowo w programie PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Aby dodać zdarzenia dotyczące kondycji niestandardowych do kodu usługi
Szablony projektu usługi Service Fabric w programie Visual Studio zawiera przykładowy kod. Poniższe kroki pokazują, jak zgłosić zdarzenia dotyczące kondycji niestandardowych w kodzie usługi. Raporty takie będą automatycznie wyświetlane w standardowych narzędzi w celu monitorowania kondycji, że Usługa Service Fabric udostępnia narzędzia Service Fabric Explorer, widok kondycji portalu platformy Azure i programu PowerShell.

1. Otwórz ponownie aplikację, który został wcześniej utworzony w programie Visual Studio lub Utwórz nową aplikację za pomocą **usługa stanowa** szablonu programu Visual Studio.
1. Otwórz plik Stateful1.cs i Znajdź `myDictionary.TryGetValueAsync` wywołania `RunAsync` metody. Widać, że ta metoda zwraca `result` zawierający bieżącą wartość licznika klucza logiki w tej aplikacji jest liczyć uruchomiona. Jeśli ta aplikacja była rzeczywistej aplikacji i braku wyniku reprezentowane awarię, będzie chciała Flaga tego zdarzenia.
1. Aby zgłosić zdarzenie kondycji, jeśli brak wyniku reprezentuje błąd, należy dodać następujące kroki.
   
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
    Możemy raportować kondycję repliki, ponieważ jest zgłaszany z usługi stanowej. `HealthInformation` Parametr zapisuje informacje o problem z kondycją, który jest zgłaszany.
   
    Jeśli usługa bezstanowa została utworzona, użyj poniższego kodu
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Jeśli usługa jest uruchomiona z uprawnieniami administratora, lub jeśli klaster nie jest [bezpiecznego](service-fabric-cluster-security.md), można również użyć `FabricClient` raportów kondycji, jak pokazano w poniższych krokach.  
   
    a. Tworzenie `FabricClient` wystąpienia po `var myDictionary` deklaracji.
   
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
1. Umożliwia symulowanie tego błędu i być widoczna w narzędzia do monitorowania kondycji. Aby symulować błąd, komentarz w pierwszym wierszu kodu, który dodano wcześniej raportowania kondycji. Po ujmiesz w komentarz wiersz pierwszy, kod będzie wyglądać podobnie jak w poniższym przykładzie.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Ten kod jest uruchamiany raport o kondycji każdorazowo `RunAsync` wykonuje. Po wprowadzeniu zmian, naciśnij klawisz **F5** do uruchomienia aplikacji.
1. Po uruchomieniu aplikacji Otwórz narzędzia Service Fabric Explorer, aby sprawdzić kondycję aplikacji. Tym razem narzędzia Service Fabric Explorer pokazuje, że aplikacja jest w złej kondycji. Aplikacja przedstawiono jako w złej kondycji, ponieważ błąd to zgłoszone od kodu, który dodano wcześniej.
   
    ![Aplikacja w złej kondycji w narzędziu Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Jeśli wybierzesz repliki podstawowej w narzędziu Service Fabric Explorer, w widoku drzewa, zostanie wyświetlone **stan kondycji** wskazuje błąd, zbyt. Narzędzie Service Fabric Explorer wyświetla również szczegóły raportu kondycji, które zostały dodane do `HealthInformation` parametru w kodzie. Możesz zobaczyć tych samych raportów kondycji w programie PowerShell i witryny Azure portal.
   
    ![Kondycja repliki w narzędziu Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Ten raport pozostaje w Menedżera kondycji, dopóki nie został zastąpiony przez inny raport, lub do momentu usunięcia tej repliki. Ponieważ firma Microsoft nie ustawiła prawidłowo `TimeToLive` dla tego raportu kondycji w `HealthInformation` obiektu raportu nigdy nie wygasa.

Zaleca się, że kondycja powinny być raportowane na najbardziej szczegółowym poziomie, w tym przypadku jest repliką. Można również zgłosić kondycji `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Raport kondycji na `Application`, `DeployedApplication`, i `DeployedServicePackage`, użyj `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Kolejne kroki
* [Szczegółowe informacje dotyczące kondycji usługi Service Fabric](service-fabric-health-introduction.md)
* [Interfejs API REST na potrzeby raportowania kondycji usługi](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [Interfejs API REST na potrzeby raportowania kondycji aplikacji](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

