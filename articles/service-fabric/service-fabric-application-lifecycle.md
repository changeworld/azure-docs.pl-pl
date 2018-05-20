---
title: Cykl życia aplikacji w sieci szkieletowej usług | Dokumentacja firmy Microsoft
description: W tym artykule opisano tworzenie, wdrażanie, testowania, uaktualniania, utrzymywaniem i usunięcie aplikacji sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 08837cca-5aa7-40da-b087-2b657224a097
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/19/2018
ms.author: ryanwi
ms.openlocfilehash: e7dddfca3640615cb851fb6dce9eaa80260ccbf6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-application-lifecycle"></a>Cykl życia aplikacji w sieci szkieletowej usług
Ponieważ z innych platform, aplikację na sieć szkieletowa usług Azure zazwyczaj przechodzi przez następujących faz: projekt, programowanie testowania, wdrożenia, uaktualnianie, obsługi i usuwania. Sieć szkieletowa usług zawiera najwyższej jakości pomoc techniczną dla pełnej aplikacji cyklem życia aplikacji w chmurze, od projektowania do wdrożenia, codziennego zarządzania i konserwacji do likwidacji ostatecznego. Model usługi umożliwia kilku różnych ról niezależnie uczestniczyć w cyklu życia aplikacji. Ten artykuł zawiera omówienie interfejsów API i jak są używane przez różne role w etapy cyklu życia aplikacji sieci szkieletowej usług.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

W poniższym filmie Microsoft Virtual Academy opisano sposób zarządzania cyklu użytkowania Twojej aplikacji: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-application-lifecycle/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="service-model-roles"></a>Usługi ról modelu
Dostępne są następujące usługi ról modelu:

* **Usługa developer**: rozwija moduły i ogólne usług, które może być konieczności zmiany i używany w wielu aplikacjach tego samego typu lub różnych typów. Na przykład usługi kolejki może służyć do tworzenia aplikacji obsługi biletów (Pomoc techniczna) lub aplikacji handlu elektronicznego (koszyk).
* **Deweloper aplikacji**: tworzy aplikacje integrując kolekcja usług do spełnienia określonych wymagań lub scenariuszy. Na przykład witryną internetową handlu elektronicznego może zintegrować "JSON frontonu usługi bezstanowej," "Aukcji Stateful Usługa" oraz "kolejki Stateful" auctioning rozwiązania.
* **Administrator aplikacji**: sprawia, że decyzje dotyczące konfiguracji aplikacji (wypełnianie konfiguracji parametrów szablonu), wdrożenie (mapowanie do dostępnych zasobów) i jakości usług. Na przykład administrator aplikacji decyduje język ustawienia regionalne (angielski dla Stanów Zjednoczonych) lub języka japońskiego dla Japonii, na przykład aplikacji. Różne wdrożonej aplikacji może mieć różne ustawienia.
* **Operator**: wdraża aplikacje na podstawie konfiguracji aplikacji i wymaganiami określonymi przez administratora aplikacji. Na przykład operator przepisy i wdraża aplikację i zapewnia, że jest uruchomiona na platformie Azure. Operatory monitorowanie informacji o kondycji i wydajności aplikacji i konserwacja infrastruktury fizycznej, zgodnie z potrzebami.

## <a name="develop"></a>Programowanie
1. A *usługi developer* osiąga różnych typów usług przy użyciu [Reliable Actors](service-fabric-reliable-actors-introduction.md) lub [niezawodne usługi](service-fabric-reliable-services-introduction.md) model programowania.
2. A *usługi developer* deklaratywnie opisano typy rozwinięte usługi w pliku manifestu usługi składające się z co najmniej jednego pakietu kodu, konfiguracji i danych.
3. *Deweloper aplikacji* utworzy aplikację przy użyciu usługi różnych typów.
4. *Deweloper aplikacji* deklaratywnie opisuje typ aplikacji w manifeście aplikacji odwołuje się do usługi manifesty składników usług i odpowiednio zastępowanie i ustawianie różnych ustawienia konfiguracji i wdrażania usług składowych.

Zobacz [wprowadzenie Reliable Actors](service-fabric-reliable-actors-get-started.md) i [Rozpoczynanie pracy z usługami Reliable Services](service-fabric-reliable-services-quick-start.md) przykłady.

## <a name="deploy"></a>Wdrażanie
1. *Administrator aplikacji* dostosowanie typ aplikacji do określonej aplikacji do wdrożenia klastra sieci szkieletowej usług, określając odpowiednie parametry **atrybutów ApplicationType** element w manifeście aplikacji.
2. *Operator* przekazuje pakiet aplikacji do magazynu obrazu klastra przy użyciu [ **CopyApplicationPackage** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) lub [  **Kopiuj ServiceFabricApplicationPackage** polecenia cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Pakiet aplikacji zawiera manifest aplikacji i kolekcji usługi pakietów. Sieć szkieletowa usług wdraża aplikacje z pakietu aplikacji przechowywanych w magazynie obrazu, który może być magazynu obiektów blob platformy Azure lub usługa systemowa sieci szkieletowej usług.
3. *Operator* następnie inicjuje typu aplikacji w klastrze docelowym z pakietów przekazano aplikacji przy użyciu [ **ProvisionApplicationAsync** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_),  [ **ServiceFabricApplicationType rejestru** polecenia cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype), lub [ **udostępnienia aplikacji** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. Po zainicjowaniu obsługi administracyjnej aplikacji, *operator* uruchamia aplikację z parametrami dostarczonych przez *administrator aplikacji* przy użyciu [  **CreateApplicationAsync** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CreateApplicationAsync_System_Fabric_Description_ApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), [ **ServiceFabricApplication nowy** polecenia cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricapplication), lub [ **tworzenie aplikacji**  Operacji REST](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. Po wdrożeniu aplikacji *operator* używa [ **CreateServiceAsync** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_CreateServiceAsync_System_Fabric_Description_ServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), [ **ServiceFabricService nowy**  polecenia cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice), lub [ **Utwórz usługę** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) do tworzenia nowych wystąpień usługi dla aplikacji na podstawie typów dostępność usług.
6. Aplikacja jest teraz uruchomiona w klastrze usługi sieć szkieletowa usług.

Zobacz [wdrażania aplikacji](service-fabric-deploy-remove-applications.md) przykłady.

## <a name="test"></a>Testowanie
1. Po wdrożeniu na lokalny klaster projektowy lub klaster testowy *usługi developer* scenariusza testów wbudowanych pracy awaryjnej jest uruchamiana za pomocą [ **FailoverTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters#System_Fabric_Testability_Scenario_FailoverTestScenarioParameters) i [ **FailoverTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario#System_Fabric_Testability_Scenario_FailoverTestScenario) klas, lub [ **Invoke ServiceFabricFailoverTestScenario** polecenia cmdlet](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). Scenariusz test pracy awaryjnej jest uruchomiona usługa określony za pomocą ważne przejścia i tryb failover, aby upewnić się, że są nadal dostępne i działają.
2. *Usługi developer* następnie uruchamia wbudowanych chaos test scenariusza za pomocą [ **ChaosTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters#System_Fabric_Testability_Scenario_ChaosTestScenarioParameters) i [  **ChaosTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario#System_Fabric_Testability_Scenario_ChaosTestScenario) klas, lub [ **Invoke ServiceFabricChaosTestScenario** polecenia cmdlet](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). Scenariusza testów chaos losowo wywołuje wiele węzłów, pakietu kodu i usterek repliki w klastrze.
3. *Usługi developer* [testów komunikacji usług](service-fabric-testability-scenarios-service-communication.md) przez tworzenie scenariuszy testowania, łączące replik podstawowych wokół klastra.

Zobacz [wprowadzenie do usługi analiza błędów](service-fabric-testability-overview.md) Aby uzyskać więcej informacji.

## <a name="upgrade"></a>Uaktualnienie
1. A *usługi developer* aktualizacje składników usług wystąpień aplikacji i/lub poprawki błędów i zawiera nową wersję manifestu usługi.
2. *Deweloper aplikacji* zastępuje parameterizes ustawienia konfiguracji i wdrażania usług spójne i udostępnia nową wersję manifest aplikacji. Następnie Deweloper aplikacji zawiera nowe wersje manifestów usług do aplikacji i zawiera nową wersję typu aplikacji w pakiecie aplikacji zaktualizowane.
3. *Administrator aplikacji* obejmuje nową wersję tego typu aplikacji w aplikacji docelowej, aktualizując odpowiednie parametry.
4. *Operator* przekazuje pakiet aplikacji zaktualizowane do używania magazynu obrazu klastra [ **CopyApplicationPackage** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) lub [ **Kopiowania ServiceFabricApplicationPackage** polecenia cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Pakiet aplikacji zawiera manifest aplikacji i kolekcji usługi pakietów.
5. *Operator* przepisy przy użyciu nowej wersji aplikacji w klastrze docelowym [ **ProvisionApplicationAsync** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), [  **Rejestr ServiceFabricApplicationType** polecenia cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype), lub [ **udostępnienia aplikacji** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. *Operator* uaktualnia aplikacji docelowej do nowej wersji za pomocą [ **UpgradeApplicationAsync** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpgradeApplicationAsync_System_Fabric_Description_ApplicationUpgradeDescription_System_TimeSpan_System_Threading_CancellationToken_), [  **Start-ServiceFabricApplicationUpgrade** polecenia cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationupgrade), lub [ **uaktualniania aplikacji** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. *Operator* sprawdza postęp uaktualniania przy użyciu [ **GetApplicationUpgradeProgressAsync** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_GetApplicationUpgradeProgressAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), [  **Get-ServiceFabricApplicationUpgrade** polecenia cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade), lub [ **uzyskać postęp uaktualniania aplikacji** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. W razie potrzeby *operator* modyfikuje i przywrócenie parametry bieżącej aplikacji aktualizację, używając [ **UpdateApplicationUpgradeAsync** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpdateApplicationUpgradeAsync_System_Fabric_Description_ApplicationUpgradeUpdateDescription_System_TimeSpan_System_Threading_CancellationToken_), [ **Update-ServiceFabricApplicationUpgrade** polecenia cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/update-servicefabricapplicationupgrade), lub [ **uaktualniania aplikacji aktualizacji** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. W razie potrzeby *operator* wycofuje bieżącej aplikacji aktualizację, używając [ **RollbackApplicationUpgradeAsync** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RollbackApplicationUpgradeAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), [ **Start ServiceFabricApplicationRollback** polecenia cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationrollback), lub [ **uaktualniania aplikacji wycofywania** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Sieć szkieletowa usług uaktualnia aplikacji docelowej uruchamianych w klastrze bez utraty dostępności dowolnego z jego usług składowych.

Zobacz [samouczek uaktualniania aplikacji](service-fabric-application-upgrade-tutorial.md) przykłady.

## <a name="maintain"></a>Obsługa
1. Do uaktualnienia systemu operacyjnego i poprawki usługi sieć szkieletowa interfejsów z infrastruktury platformy Azure, aby zagwarantować dostępność wszystkich aplikacji uruchomionych w klastrze.
2. Aktualizacje i poprawki dla platformy Service Fabric sieci szkieletowej usług uaktualnia się bez utraty dostępność aplikacji uruchomionych w klastrze.
3. *Administrator aplikacji* zatwierdza dodawania lub usuwania węzłów z klastra po przeanalizowaniu pojemności historyczne dane dotyczące użycia i planowanego przyszłego zapotrzebowania.
4. *Operator* dodaje i usuwa określony przez węzły *administrator aplikacji*.
5. Po dodaniu nowych węzłów do lub istniejące węzły są usuwane z klastra, Service Fabric automatycznie równoważy uruchamianie aplikacji we wszystkich węzłach w klastrze, aby osiągnąć optymalną wydajność.

## <a name="remove"></a>Remove
1. *Operator* można usunąć określonego wystąpienia usług uruchomionych w klastrze bez usuwania całej aplikacji przy użyciu [ **DeleteServiceAsync** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_DeleteServiceAsync_System_Fabric_Description_DeleteServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), [ **ServiceFabricService Usuń** polecenia cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricservice), lub [ **usunięcia usługi** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. *Operator* można również usunąć wystąpienia aplikacji i wszystkich jego usług za pomocą [ **DeleteApplicationAsync** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_DeleteApplicationAsync_System_Fabric_Description_DeleteApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), [  **Usuń ServiceFabricApplication** polecenia cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricapplication), lub [ **Usuń aplikację** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. Gdy zatrzymano aplikacji i usług, *operator* można wstrzymał obsługi administracyjnej typu aplikacji przy użyciu [ **UnprovisionApplicationAsync** — metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UnprovisionApplicationAsync_System_String_System_String_System_TimeSpan_System_Threading_CancellationToken_), [ **Unregister-ServiceFabricApplicationType** polecenia cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype), lub [ **wstrzymał obsługi administracyjnej aplikacji** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). W składniku ImageStore wstrzymania obsługi administracyjnej typu aplikacji nie powoduje usunięcia pakietu aplikacji. Należy ręcznie usunąć pakietu aplikacji.
4. *Operator* usuwa pakiet aplikacji za pomocą magazynu ImageStore [ **RemoveApplicationPackage** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RemoveApplicationPackage_System_String_System_String_) lub [  **Usuń ServiceFabricApplicationPackage** polecenia cmdlet](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Zobacz [wdrażania aplikacji](service-fabric-deploy-remove-applications.md) przykłady.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat tworzenia testowania i zarządzanie aplikacjami platformy Service Fabric i usług, zobacz:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Usługi Reliable Services](service-fabric-reliable-services-introduction.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)
* [Uaktualnienie aplikacji](service-fabric-application-upgrade.md)
* [Omówienie testowania](service-fabric-testability-overview.md)
