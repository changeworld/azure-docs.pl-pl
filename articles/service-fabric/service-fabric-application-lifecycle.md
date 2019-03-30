---
title: Cykl życia aplikacji w usłudze Service Fabric | Dokumentacja firmy Microsoft
description: Zawiera opis tworzenia, wdrażania, testowania, uaktualnianie, obsługi i usuwania aplikacji usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 08837cca-5aa7-40da-b087-2b657224a097
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/19/2018
ms.author: atsenthi
ms.openlocfilehash: 53cab3591ea11721e36b48438f35df016e2a9f3a
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664985"
---
# <a name="service-fabric-application-lifecycle"></a>Cykl życia aplikacji usługi Service Fabric
Zgodnie z innymi platformami aplikacji w usłudze Azure Service Fabric zwykle odbywa się przez następujące fazy: projektowania, rozwoju, testowania, wdrażanie, uaktualnianie, obsługi i usuwania. Usługa Service Fabric zapewnia najwyższej jakości pomoc techniczna dla całego cyklu życia aplikacji z aplikacji w chmurze, od projektowania do wdrażania, zarządzania infrastrukturą i konserwacji do ostatecznego wycofania. Model usługi umożliwia kilku różnych ról niezależnie uczestniczyć w cyklu życia aplikacji. Ten artykuł zawiera omówienie interfejsów API i jak są one używane przez różne role, w fazach cyklu życia aplikacji usługi Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Usługi modelu ról
Role usługi w modelu są:

* **Usługi dla deweloperów**: Rozwija modułowego i ogólny usług, które może być przeznaczenie i używany w wielu aplikacjach typu tej samej lub różnych typów. Na przykład usługa kolejki może służyć do tworzenia biletów aplikacji (Pomoc techniczna) lub aplikacji handlu elektronicznego (koszyka).
* **Deweloper aplikacji**: Tworzy aplikacje dzięki integracji kolekcję usług, które spełniają pewne określone wymagania lub scenariuszy. Na przykład w witrynie sieci Web handlu elektronicznego może zintegrować "JSON bezstanowej frontonu usługi", "Usługa stanowa aukcji" i "Usługa stanowa kolejki" Tworzenie auctioning rozwiązania.
* **Administrator aplikacji**: Podejmuje decyzje dotyczące konfiguracji aplikacji (wypełnianie za pomocą parametrów szablonu konfiguracji), wdrażania (mapowanie dostępnych zasobów i) i jakości usługi. Na przykład administrator aplikacji decyduje o języka ustawień regionalnych (w języku angielskim dla Stanów Zjednoczonych) lub w języku japońskim dla Japonii, na przykład aplikacji. Różne wdrożonej aplikacji może mieć różne ustawienia.
* **Operator**: Wdraża aplikacje na podstawie konfiguracji aplikacji i wymaganiami określonymi przez administratora aplikacji. Na przykład operator aprowizuje i wdraża aplikację i gwarantuje, że jest on uruchomiony na platformie Azure. Operatory monitorować informacje o kondycji i wydajności aplikacji i obsługa infrastruktury fizycznej, zgodnie z potrzebami.

## <a name="develop"></a>Programowanie
1. A *usługi dla deweloperów* opracowuje różnego rodzaju usług przy użyciu [elementów Reliable Actors](service-fabric-reliable-actors-introduction.md) lub [usług Reliable Services](service-fabric-reliable-services-introduction.md) model programowania.
2. A *usługi dla deweloperów* deklaratywnie w tym artykule opisano typy usług opracowanych w pliku manifestu usługi składający się z co najmniej jednego pakietu kodu, konfiguracji i danych.
3. *Deweloper aplikacji* następnie kompiluje aplikację przy użyciu różne typy usług.
4. *Deweloper aplikacji* deklaratywne opisuje typ aplikacji w manifeście aplikacji przez odwołanie do manifestów usługi składników usług i odpowiednio zastępowanie i parametryzacja różnych ustawienia konfiguracji i wdrażania usług składowych.

Zobacz [rozpocząć pracę z elementami Reliable Actors](service-fabric-reliable-actors-get-started.md) i [wprowadzenie do usług Reliable Services](service-fabric-reliable-services-quick-start.md) przykłady.

## <a name="deploy"></a>Wdrażanie
1. *Administrator aplikacji* dostosowanie typu aplikacji do konkretnej aplikacji do wdrożenia klastra usługi Service Fabric, określając odpowiednie parametry **ApplicationType** elementu w manifeście aplikacji.
2. *Operator* przekazuje pakiet aplikacji do magazynu obrazów klastra przy użyciu [ **CopyApplicationPackage** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) lub [  **Kopiuj ServiceFabricApplicationPackage** polecenia cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Pakiet aplikacji zawiera manifest aplikacji i kolekcji pakietów usługi. Usługa Service Fabric wdroży aplikacji za pomocą pakietu aplikacji, przechowywane w magazynie obrazu, który może być z magazynu obiektów blob platformy Azure lub usługi systemowe Service Fabric.
3. *Operator* następnie Inicjuje obsługę typu aplikacji w klastrze docelowym z przy użyciu pakietu aplikacji przekazanych [ **ProvisionApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient),  [ **ServiceFabricApplicationType rejestru** polecenia cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype), lub [ **Aprowizowanie aplikacji** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. Po zainicjowaniu obsługi administracyjnej aplikacji *operator* uruchomi aplikację za pomocą parametrów dostarczonych przez *administrator aplikacji* przy użyciu [  **CreateApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **New ServiceFabricApplication** polecenia cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication), lub [ **tworzenia aplikacji**  Operacji REST](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. Po wdrożeniu aplikacji *operator* używa [ **CreateServiceAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [ **New ServiceFabricService**  polecenia cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice), lub [ **żądania utworzenia usługi** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) do tworzenia nowych wystąpień usługi dla aplikacji na podstawie typów dostępne usługi.
6. Aplikacja jest teraz uruchomiona w klastrze usługi Service Fabric.

Zobacz [wdrażania aplikacji](service-fabric-deploy-remove-applications.md) przykłady.

## <a name="test"></a>Testowanie
1. Po wdrożeniu na lokalny klaster projektowy lub klastra testowego, *usługi dla deweloperów* uruchamia scenariusza testu trybu failover wbudowanej za pomocą [ **FailoverTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) i [ **FailoverTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) klas, lub [ **Invoke ServiceFabricFailoverTestScenario** polecenia cmdlet](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). Scenariusz testu w trybie failover uruchamia określonej usługi za pośrednictwem ważne przejścia i trybu failover, aby upewnić się, że jest nadal dostępne i działają.
2. *Usługi dla deweloperów* następnie uruchamia chaos wbudowany test scenariusza za pomocą [ **ChaosTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) i [  **ChaosTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario) klas, lub [ **Invoke ServiceFabricChaosTestScenario** polecenia cmdlet](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). Scenariusz testów chaos losowo wywołuje wiele węzłów, pakiet kodu i usterek repliki do klastra.
3. *Usługi dla deweloperów* [testy komunikacji między usługami](service-fabric-testability-scenarios-service-communication.md) tworzenie scenariuszy testowych, łączące replikami podstawowymi w całym klastrze.

Zobacz [Introduction to usługa analizy błędów](service-fabric-testability-overview.md) Aby uzyskać więcej informacji.

## <a name="upgrade"></a>Uaktualnienie
1. A *usługi dla deweloperów* aktualizacje składników usług wystąpień aplikacji i/lub poprawki usterek i zapewnia nowa wersja manifestu usługi.
2. *Deweloper aplikacji* zastępuje parametryzuje dane ustawień konfiguracji i wdrażania usług spójne i zawiera nową wersję w manifeście aplikacji. Deweloper aplikacji następnie dołącza nowe wersje zestawu manifestów usługi do aplikacji i zawiera nową wersję typu aplikacji w pakiecie zaktualizowaną aplikację.
3. *Administrator aplikacji* zawiera nową wersję typu aplikacji do aplikacji docelowej, aktualizując odpowiednie parametry.
4. *Operator* przekazuje pakiet aplikacji do klastra obrazu magazynu przy użyciu [ **CopyApplicationPackage** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) lub [ **Kopiowania ServiceFabricApplicationPackage** polecenia cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Pakiet aplikacji zawiera manifest aplikacji i kolekcji pakietów usługi.
5. *Operator* aprowizuje nową wersję aplikacji w klastrze docelowym przy użyciu [ **ProvisionApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [  **Zarejestruj ServiceFabricApplicationType** polecenia cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype), lub [ **Aprowizowanie aplikacji** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. *Operator* uaktualnień aplikacji docelowej, do nowego przy użyciu wersji [ **UpgradeApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [  **Start-ServiceFabricApplicationUpgrade** polecenia cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade), lub [ **uaktualniania aplikacji** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. *Operator* sprawdza, czy postęp uaktualnienia przy użyciu [ **GetApplicationUpgradeProgressAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [  **Get-ServiceFabricApplicationUpgrade** polecenia cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade), lub [ **uzyskać postęp uaktualniania aplikacji** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Jeśli to konieczne, *operator* modyfikuje i ponowne zastosowanie parametrów bieżącej aplikacji uaktualnianie za pomocą [ **UpdateApplicationUpgradeAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **ServiceFabricApplicationUpgrade aktualizacji** polecenia cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade), lub [ **uaktualnienia aplikacji aktualizacji** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. Jeśli to konieczne, *operator* wycofanie bieżącej aplikacji uaktualnianie za pomocą [ **RollbackApplicationUpgradeAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Start ServiceFabricApplicationRollback** polecenia cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback), lub [ **uaktualnienia aplikacji wycofywania** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Usługa Service Fabric uaktualnia aplikacji docelowej, uruchomione w klastrze bez utraty dostępność dowolną z jego usług składowych.

Zobacz [samouczek dotyczący uaktualniania aplikacji](service-fabric-application-upgrade-tutorial.md) przykłady.

## <a name="maintain"></a>Obsługa
1. Do uaktualnienia systemu operacyjnego i poprawki usługi Service Fabric z interfejsem do infrastruktury platformy Azure, aby zapewnić dostępność wszystkich aplikacji uruchomionych w klastrze.
2. Do uaktualnienia i poprawki dla platformy Service Fabric usługi Service Fabric uaktualnia się bez utraty dostępność aplikacji działających w klastrze.
3. *Administrator aplikacji* zatwierdza dodawania lub usuwania węzłów z klastra po przeanalizowaniu pojemności historyczne dane dotyczące użycia i przewidywaną przyszły popyt.
4. *Operator* dodaje i usuwa węzły określonej przez *administrator aplikacji*.
5. Po dodaniu nowych węzłów do lub istniejące węzły są usuwane z klastra, Usługa Service Fabric automatycznie równoważy obciążenia uruchomionych aplikacji we wszystkich węzłach w klastrze, aby osiągnąć optymalną wydajność.

## <a name="remove"></a>Remove
1. *Operator* można usunąć określonego wystąpienia usługi uruchomione w klastrze bez usuwania całej aplikacji przy użyciu [ **DeleteServiceAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [ **ServiceFabricService Usuń** polecenia cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice), lub [ **żądania usunięcia usługi** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. *Operator* można również usunąć wystąpienie aplikacji i wszystkich swoich usług przy użyciu [ **DeleteApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [  **Usuń ServiceFabricApplication** polecenia cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication), lub [ **Usuń aplikację** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. Po zatrzymaniu aplikacji i usług, *operator* można wstrzymał obsługi administracyjnej typu aplikacji przy użyciu [ **UnprovisionApplicationAsync** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **ServiceFabricApplicationType Wyrejestruj** polecenia cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype), lub [ **wstrzymał obsługi administracyjnej aplikacji** operacji REST](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). Cofnięcie aprowizacji typu aplikacji nie powoduje usunięcia pakietu aplikacji z ImageStore. Należy ręcznie usunąć pakiet aplikacji.
4. *Operator* usuwa pakiet aplikacji z przy użyciu ImageStore [ **RemoveApplicationPackage** metoda](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) lub [  **Usuń ServiceFabricApplicationPackage** polecenia cmdlet](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Zobacz [wdrażania aplikacji](service-fabric-deploy-remove-applications.md) przykłady.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat tworzenia testowania i zarządzania aplikacjami usługi Service Fabric i usługi, zobacz:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Usługi Reliable Services](service-fabric-reliable-services-introduction.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)
* [Uaktualnienie aplikacji](service-fabric-application-upgrade.md)
* [Omówienie testowalności](service-fabric-testability-overview.md)
