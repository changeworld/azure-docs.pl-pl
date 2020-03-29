---
title: Cykl życia aplikacji w sieci szkieletowej usług
description: W tym artykule opisano tworzenie, wdrażanie, testowanie, uaktualnianie, utrzymywanie i usuwanie aplikacji sieci szkieletowej usług.
ms.topic: conceptual
ms.date: 1/19/2018
ms.openlocfilehash: beeb1f1512cf94582dd561fa768f2e8e6649d686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378008"
---
# <a name="service-fabric-application-lifecycle"></a>Service Fabric application lifecycle (Cykl życia aplikacji usługi Service Fabric)
Podobnie jak w przypadku innych platform, aplikacja w sieci szkieletowej usług Azure zwykle przechodzi przez następujące fazy: projektowanie, tworzenie, testowanie, wdrażanie, uaktualnianie, konserwacja i usuwanie. Usługa Service Fabric zapewnia najwyższej klasy obsługę pełnego cyklu życia aplikacji w chmurze, od tworzenia przez wdrażanie, codzienne zarządzanie i konserwację do ostatecznej likwidacji. Model usługi umożliwia kilka różnych ról do udziału niezależnie w cyklu życia aplikacji. Ten artykuł zawiera omówienie interfejsów API i jak są one używane przez różne role w fazie cyklu życia aplikacji sieci szkieletowej usług.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Role modelu usługi
Role modelu usługi to:

* **Deweloper usługi:** Opracowuje usługi modułowe i ogólne, które mogą być repurposed i używane w wielu aplikacjach tego samego typu lub różnych typów. Na przykład usługa kolejki może służyć do tworzenia aplikacji biletowej (helpdesk) lub aplikacji e-commerce (koszyk).
* **Deweloper aplikacji:** Tworzy aplikacje przez integrację kolekcji usług w celu spełnienia określonych wymagań lub scenariuszy. Na przykład witryny e-commerce może zintegrować "JSON bezstanowe usługi front-end", "Aukcja stateful service" i "Queue Stateful Service" do tworzenia rozwiązania aukcji.
* **Administrator aplikacji**: Podejmuje decyzje dotyczące konfiguracji aplikacji (wypełnianie parametrów szablonu konfiguracji), wdrażania (mapowanie dostępnych zasobów) i jakości usług. Na przykład administrator aplikacji decyduje o ustawieniach regionalnych języka (angielski dla Stanów Zjednoczonych lub japoński dla Japonii, na przykład) aplikacji. Inna wdrożona aplikacja może mieć różne ustawienia.
* **Operator**: Wdraża aplikacje na podstawie konfiguracji aplikacji i wymagań określonych przez administratora aplikacji. Na przykład operator aplikuje i wdraża aplikację i zapewnia, że jest uruchomiona na platformie Azure. Operatorzy monitorują informacje o kondycji i wydajności aplikacji oraz w razie potrzeby utrzymują infrastrukturę fizyczną.

## <a name="develop"></a>Programowanie
1. *Deweloper usługi* rozwija różne typy usług przy użyciu modelu programowania [niezawodne podmioty](service-fabric-reliable-actors-introduction.md) lub [niezawodne usługi.](service-fabric-reliable-services-introduction.md)
2. *Deweloper usługi* deklaratywnie opisuje opracowane typy usług w pliku manifestu usługi składający się z jednego lub więcej kodu, konfiguracji i pakietów danych.
3. Deweloper *aplikacji* następnie tworzy aplikację przy użyciu różnych typów usług.
4. *Deweloper aplikacji* deklaratywnie opisuje typ aplikacji w manifeście aplikacji, odwołując się do manifestów usługi składników usług i odpowiednio zastępowanie i parametryzowanie różnych ustawień konfiguracji i wdrażania usług składowych.

Zobacz [Wprowadzenie do reliable actors](service-fabric-reliable-actors-get-started.md) i wprowadzenie do [niezawodnych usług](service-fabric-reliable-services-quick-start.md) dla przykładów.

## <a name="deploy"></a>Wdrożenie
1. *Administrator aplikacji* dostosowuje typ aplikacji do określonej aplikacji, która ma zostać wdrożona w klastrze sieci szkieletowej usług, określając odpowiednie parametry elementu **ApplicationType** w manifeście aplikacji.
2. *Operator* przesyła pakiet aplikacji do magazynu obrazów klastra przy użyciu [ **copyapplicationPackage** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) lub [ **Copy-ServiceFabricApplicationPackage** polecenie cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Pakiet aplikacji zawiera manifest aplikacji i kolekcję pakietów usług. Usługa Service Fabric wdraża aplikacje z pakietu aplikacji przechowywane w magazynie obrazów, który może być magazyn obiektu blob platformy Azure lub usługa systemu sieci szkieletowej usług.
3. Operator *operator* następnie przepisuje typ aplikacji w klastrze docelowym z przesłanego pakietu aplikacji przy użyciu [ **metody ProvisionApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **Register-ServiceFabricApplicationType** ](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)lub [ **provision an Application** REST operation](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. Po zainicjowaniu obsługi administracyjnej aplikacji *operator* uruchamia aplikację z parametrami dostarczonymi przez *administratora aplikacji* przy użyciu metody [ **CreateApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), polecenia [cmdlet **New-ServiceFabricApplication** ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication)lub [ **Create Application** REST.](https://docs.microsoft.com/rest/api/servicefabric/create-an-application)
5. Po wdrożeniu aplikacji *operator* używa [ **createServiceAsync** metody,](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) [polecenie cmdlet **New-ServiceFabricService** ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice)lub [ **Create Service** REST operacji](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) do tworzenia nowych wystąpień usługi dla aplikacji na podstawie dostępnych typów usług.
6. Aplikacja jest teraz uruchomiona w klastrze sieci szkieletowej usług.

Zobacz [wdrażanie aplikacji,](service-fabric-deploy-remove-applications.md) aby uzyskać przykłady.

## <a name="test"></a>Testowanie
1. Po wdrożeniu do lokalnego klastra deweloperskiego lub klastra testowego *deweloper usługi* uruchamia wbudowany scenariusz testu trybu failover przy użyciu klasy [**FailoverTestTestarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) i [**FailoverTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) lub [polecenia cmdlet **Invoke-ServiceFabricFailoverTestScenario** ](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). Scenariusz testu trybu failover uruchamia określoną usługę za pośrednictwem ważnych przejść i pracy awaryjnej, aby upewnić się, że jest nadal dostępna i działa.
2. Następnie *deweloper usługi* uruchamia wbudowany scenariusz testu chaosu przy użyciu klas [**ChaosTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) i [**ChaosTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario) lub [polecenia cmdlet **Invoke-ServiceFabricChaosTestScenario** ](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). Scenariusz testu chaosu losowo indukuje wiele węzłów, pakiet kodu i błędy repliki do klastra.
3. *Deweloper usługi* [testuje komunikację między usługami,](service-fabric-testability-scenarios-service-communication.md) tworząc scenariusze testów, które przenoszą repliki podstawowe wokół klastra.

Zobacz [Wprowadzenie do usługi analizy usterek, aby](service-fabric-testability-overview.md) uzyskać więcej informacji.

## <a name="upgrade"></a>Uaktualnienie
1. *Deweloper usługi* aktualizuje usługi składowe aplikacji wystąpienia i/lub naprawia błędy i udostępnia nową wersję manifestu usługi.
2. *Deweloper aplikacji* zastępuje i parametryzuje ustawienia konfiguracji i wdrażania spójnych usług i udostępnia nową wersję manifestu aplikacji. Deweloper aplikacji następnie zawiera nowe wersje manifestów usługi do aplikacji i udostępnia nową wersję typu aplikacji w zaktualizowanym pakiecie aplikacji.
3. *Administrator aplikacji* zawiera nową wersję typu aplikacji do aplikacji docelowej, aktualizując odpowiednie parametry.
4. *Operator* przesyła zaktualizowany pakiet aplikacji do magazynu obrazów klastra przy użyciu metody [ **CopyApplicationPackage** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) lub [polecenia cmdlet **Copy-ServiceFabricApplicationPackage** ](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Pakiet aplikacji zawiera manifest aplikacji i kolekcję pakietów usług.
5. *Operator* udostępnia nową wersję aplikacji w klastrze docelowym przy użyciu [ **provisionApplicationAsync** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **register-ServiceFabricApplicationType** poleceń](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)lub [ **aprowizować aplikację** REST operacji](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. *Operator* uaktualnia aplikację docelową do nowej wersji przy użyciu [ **metody UpgradeApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **Start-ServiceFabricApplicationUplicationUpligrade** ](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)lub [ **Upgrade an Application** REST.](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application)
7. *Operator* sprawdza postęp uaktualniania przy użyciu metody [ **GetApplicationUpgradeProgressAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **Get-ServiceFabricApplicationUpligrade** ](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade)lub [ **Get Application Upgrade Progress** REST](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. W razie potrzeby *operator* modyfikuje i ponownie wprowadza parametry bieżącego uaktualnienia aplikacji przy użyciu metody [ **UpdateApplicationUpgradeAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **Update-ServiceFabricApplicationUplicationUpgrade** ](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade)lub [ **operacji Aktualizuj rest uaktualnienia aplikacji** ](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. W razie potrzeby *operator* wycofuje bieżące uaktualnienie aplikacji przy użyciu metody [ **RollbackApplicationUpgradeAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **Start-ServiceFabricApplicationRollback** ](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback)lub [ **operacji Przywracanie aktualizacji aplikacji** REST](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Sieci szkieletowej usług uaktualnia aplikację docelową działającą w klastrze bez utraty dostępności żadnej z jego usług składowych.

Zobacz [samouczek uaktualnienia aplikacji,](service-fabric-application-upgrade-tutorial.md) aby zapoznać się z przykładami.

## <a name="maintain"></a>Obsługa
1. W przypadku uaktualnień i poprawek systemu operacyjnego sieci szkieletowej usług interfejsy z infrastrukturą platformy Azure, aby zagwarantować dostępność wszystkich aplikacji uruchomionych w klastrze.
2. W przypadku uaktualnień i poprawek do platformy sieci szkieletowej usług, usługa Fabric uaktualnia się bez utraty dostępności żadnej z aplikacji uruchomionych w klastrze.
3. *Administrator aplikacji* zatwierdza dodawanie lub usuwanie węzłów z klastra po przeanalizowaniu historycznych danych wykorzystania zdolności produkcyjnych i przewidywanego przyszłego zapotrzebowania.
4. *Operator* dodaje i usuwa węzły określone przez *administratora aplikacji*.
5. Po dodaniu nowych węzłów lub usunięciu istniejących węzłów z klastra sieci szkieletowej usług automatycznie równoważy uruchomione aplikacje we wszystkich węzłach w klastrze, aby osiągnąć optymalną wydajność.

## <a name="remove"></a>Remove
1. *Operator* może usunąć określone wystąpienie uruchomionej usługi w klastrze bez usuwania całej aplikacji przy użyciu metody [ **DeleteServiceAsync,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) [polecenia cmdlet **Remove-ServiceFabricService** ](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice)lub operacji [ **Delete Service** REST](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. *Operator* może również usunąć wystąpienie aplikacji i wszystkie jego usługi za pomocą metody [ **DeleteApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **Remove-ServiceFabricApplication** ](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication)lub [ **Delete Application** REST.](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application)
3. Po zatrzymaniu aplikacji i usług *operator* może anulować obsługę administracyjną typu aplikacji przy użyciu metody [ **UnprovisionApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **Unregister-ServiceFabricApplicationType** ](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype)lub [ **Unprovision a Application** REST operation](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). Anulowanie obsługi administracyjnej typu aplikacji nie powoduje usunięcia pakietu aplikacji z magazynu ImageStore. Pakiet aplikacji należy usunąć ręcznie.
4. *Operator* usuwa pakiet aplikacji z ImageStore przy użyciu [ **RemoveApplicationPackage** metody](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) lub [ **Remove-ServiceFabricApplicationPackage** polecenie cmdlet](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Zobacz [wdrażanie aplikacji,](service-fabric-deploy-remove-applications.md) aby uzyskać przykłady.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat tworzenia, testowania i zarządzania aplikacjami i usługami sieci szkieletowej usług, zobacz:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)
* [Uaktualnienie aplikacji](service-fabric-application-upgrade.md)
* [Przegląd możliwości testowania](service-fabric-testability-overview.md)
