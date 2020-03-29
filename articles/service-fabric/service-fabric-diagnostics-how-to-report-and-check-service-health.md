---
title: Raportowanie i sprawdzanie kondycji za pomocą sieci szkieletowej usług Azure
description: Dowiedz się, jak wysyłać raporty kondycji z kodu usługi i jak sprawdzić kondycję usługi przy użyciu narzędzi monitorowania kondycji, które zapewnia usługa Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 2b7a9c44a84e3ce15eaec22c8f57bb48f79dae05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464645"
---
# <a name="report-and-check-service-health"></a>Tworzenie raportów i sprawdzanie kondycji usług
Gdy twoje usługi napotkają problemy, zdolność do reagowania na zdarzenia i awarie oraz naprawianie zależy od możliwości szybkiego wykrycia problemów. Jeśli zgłaszasz problemy i błędy do menedżera kondycji usługi Azure Service Fabric z kodu usługi, można użyć standardowych narzędzi monitorowania kondycji, które zapewnia sieci szkieletowej usług, aby sprawdzić stan kondycji.

Istnieją trzy sposoby raportowania kondycji z usługi:

* Użyj [partycji](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) lub [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext) obiektów.  
  Można użyć `Partition` i `CodePackageActivationContext` obiektów do raportowania kondycji elementów, które są częścią bieżącego kontekstu. Na przykład kod, który działa jako część repliki można zgłosić kondycję tylko na tej replice, partycji, do której należy i aplikacji, która jest częścią.
* Użyj witryny `FabricClient`.   
  Można użyć `FabricClient` do raportowania kondycji z kodu usługi, jeśli klaster nie jest [bezpieczny](service-fabric-cluster-security.md) lub jeśli usługa jest uruchomiona z uprawnieniami administratora. Większość rzeczywistych scenariuszy nie używa niezabezpieczonych klastrów ani nie udostępnia uprawnień administratora. Za `FabricClient`pomocą programowi można raportować kondycję dla dowolnej encji, która jest częścią klastra. Najlepiej jednak kod usługi należy wysyłać tylko raporty, które są związane z jego kondycji.
* Użyj interfejsów API REST na poziomie klastra, aplikacji, wdrożonej aplikacji, usługi, pakietu usług, partycji, repliki lub węzła. To może służyć do raportowania kondycji z poziomu kontenera.

W tym artykule przedstawia się przykład, który raportuje kondycję z kodu usługi. W przykładzie pokazano również, jak narzędzia dostarczone przez sieci szkieletowej usług może służyć do sprawdzania stanu kondycji. Ten artykuł ma być szybkie wprowadzenie do możliwości monitorowania kondycji sieci szkieletowej usług. Aby uzyskać bardziej szczegółowe informacje, można przeczytać serię szczegółowych artykułów na temat kondycji, które zaczynają się od łącza na końcu tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne
Musisz mieć zainstalowany następujący problem:

* Visual Studio 2015 lub Visual Studio 2019
* SDK sieci szkieletowej usług

## <a name="to-create-a-local-secure-dev-cluster"></a>Aby utworzyć lokalny klaster bezpiecznego dewelopera
* Otwórz program PowerShell z uprawnieniami administratora i uruchom następujące polecenia:

![Polecenia, które pokazują, jak utworzyć bezpieczny klaster deweloperów](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Aby wdrożyć aplikację i sprawdzić jej kondycję
1. Otwórz program Visual Studio jako administrator.
1. Utwórz projekt przy użyciu szablonu **Usługi stanowej.**
   
    ![Tworzenie aplikacji sieci szkieletowej usług za pomocą usługi stanowej](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Naciśnij **klawisz F5,** aby uruchomić aplikację w trybie debugowania. Aplikacja jest wdrażana w klastrze lokalnym.
1. Po uruchomieniu aplikacji kliknij prawym przyciskiem myszy ikonę Lokalnego Menedżera klastrów w obszarze powiadomień i wybierz polecenie **Zarządzaj klastrem lokalnym** z menu skrótów, aby otworzyć Eksploratora sieci szkieletowej usług.
   
    ![Otwórz Eksploratora sieci szkieletowej usług z obszaru powiadomień](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. Kondycja aplikacji powinna być wyświetlana tak, jak na tym obrazie. W tej chwili aplikacja powinna być w dobrej kondycji bez błędów.
   
    ![Zdrowa aplikacja w Eksploratorze sieci szkieletowej usług](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Kondycję można również sprawdzić za pomocą programu PowerShell. Można użyć, ```Get-ServiceFabricApplicationHealth``` aby sprawdzić kondycję aplikacji i ```Get-ServiceFabricServiceHealth``` można użyć do sprawdzenia kondycji usługi. Raport kondycji dla tej samej aplikacji w programie PowerShell znajduje się na tym obrazie.
   
    ![Zdrowa aplikacja w programie PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Aby dodać niestandardowe zdarzenia kondycji do kodu usługi
Szablony projektów sieci szkieletowej usług w programie Visual Studio zawierają przykładowy kod. Poniższe kroki pokazują, jak można zgłaszać niestandardowe zdarzenia kondycji z kodu usługi. Takie raporty są wyświetlane automatycznie w standardowych narzędziach do monitorowania kondycji, które zapewnia usługa Service Fabric, takich jak Eksplorator sieci szkieletowej usług, widok kondycji portalu Azure i program PowerShell.

1. Otwórz ponownie aplikację utworzoną wcześniej w programie Visual Studio lub utwórz nową aplikację przy użyciu szablonu **usługi stanowej** programu Visual Studio.
1. Otwórz plik Stateful1.cs i znajdź wywołanie `myDictionary.TryGetValueAsync` w `RunAsync` metodzie. Widać, że ta metoda `result` zwraca bieżącą wartość licznika, ponieważ logika klucza w tej aplikacji jest utrzymanie licznika uruchomiony. Jeśli ta aplikacja była rzeczywista aplikacja i jeśli brak wyniku reprezentowane błąd, należy oflagować to zdarzenie.
1. Aby zgłosić zdarzenie kondycji, gdy brak wyniku reprezentuje błąd, dodaj następujące kroki.
   
    a. Dodaj `System.Fabric.Health` obszar nazw do pliku Stateful1.cs.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Dodaj następujący kod `myDictionary.TryGetValueAsync` po wywołaniu
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Raportujemy kondycję repliki, ponieważ jest on zgłaszany z usługi stanowej. Parametr `HealthInformation` przechowuje informacje o zgłaszanym problemie ze zdrowiem.
   
    Jeśli utworzono usługę bezstanową, użyj następującego kodu
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Jeśli usługa jest uruchomiona z uprawnieniami administratora lub jeśli klaster nie jest [bezpieczny,](service-fabric-cluster-security.md)można również użyć `FabricClient` do raportowania kondycji, jak pokazano w poniższych krokach.  
   
    a. Utwórz `FabricClient` wystąpienie `var myDictionary` po deklaracji.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Dodaj następujący kod `myDictionary.TryGetValueAsync` po wywołaniu.
   
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
1. Symulujmy ten błąd i zobaczmy, że jest on widoczny w narzędziach monitorowania kondycji. Aby symulować błąd, skomentuj pierwszy wiersz w kodzie raportowania kondycji, który został dodany wcześniej. Po skomentowaniu pierwszego wiersza kod będzie wyglądał jak w poniższym przykładzie.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Ten kod uruchamia raport `RunAsync` kondycji za każdym razem wykonuje. Po dokonaniu zmiany naciśnij **klawisz F5,** aby uruchomić aplikację.
1. Po uruchomieniu aplikacji otwórz Eksploratora sieci szkieletowej usług, aby sprawdzić kondycję aplikacji. Tym razem Eksplorator sieci szkieletowej usług pokazuje, że aplikacja jest w złej kondycji. Aplikacja jest wyświetlana w złej kondycji, ponieważ błąd, który został zgłoszony z kodu, który wcześniej dodaliśmy.
   
    ![Nieprawidłowa aplikacja w Eksploratorze sieci szkieletowej usług](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Jeśli wybierzesz replikę podstawową w widoku drzewa Eksploratora sieci szkieletowej usług, zobaczysz, że **stan kondycji** wskazuje również błąd. Eksplorator sieci szkieletowej usług wyświetla również `HealthInformation` szczegóły raportu kondycji, które zostały dodane do parametru w kodzie. Możesz zobaczyć te same raporty dotyczące kondycji w programie PowerShell i witrynie Azure portal.
   
    ![Kondycja repliki w Eksploratorze sieci szkieletowej usług](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Ten raport pozostaje w menedżerze kondycji, dopóki nie zostanie zastąpiony innym raportem lub dopóki ta replika nie zostanie usunięta. Ponieważ nie ustawiliśmy `TimeToLive` dla tego `HealthInformation` raportu kondycji w obiekcie, raport nigdy nie wygasa.

Zaleca się, że kondycja powinny być zgłaszane na najbardziej szczegółowym poziomie, który w tym przypadku jest repliką. Można również raportować `Partition`kondycję na .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Aby zgłosić `Application` `DeployedApplication`kondycję `DeployedServicePackage`na `CodePackageActivationContext`, i , użyj .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Następne kroki
* [Głębokie nurkowanie na temat kondycji sieci usług](service-fabric-health-introduction.md)
* [INTERFEJS API REST do raportowania kondycji usługi](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [INTERFEJS API REST do raportowania kondycji aplikacji](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

