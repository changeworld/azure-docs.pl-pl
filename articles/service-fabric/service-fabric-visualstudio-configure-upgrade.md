---
title: Konfigurowanie uaktualnienia aplikacji sieci szkieletowej usług
description: Dowiedz się, jak skonfigurować ustawienia uaktualniania aplikacji sieci szkieletowej usług przy użyciu programu Microsoft Visual Studio.
author: mikkelhegn
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: mikhegn
ms.openlocfilehash: ef88a528dbb6d326e5cc742d14c27218eb7502f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464078"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Konfigurowanie uaktualnienia aplikacji sieci szkieletowej usług w programie Visual Studio
Narzędzia programu Visual Studio dla usługi Azure Service Fabric zapewniają obsługę uaktualniania publikowania w klastrach lokalnych lub zdalnych. Istnieją trzy scenariusze, w których chcesz uaktualnić aplikację do nowszej wersji zamiast zastępowania aplikacji podczas testowania i debugowania:

* Dane aplikacji nie zostaną utracone podczas uaktualniania.
* Dostępność pozostaje wysoka, więc nie będzie żadnych przerw w usłudze podczas uaktualniania, jeśli istnieje wystarczająca liczba wystąpień usługi rozłożone w domenach uaktualnienia.
* Testy można uruchomić przeciwko aplikacji podczas uaktualniania.

## <a name="parameters-needed-to-upgrade"></a>Parametry potrzebne do uaktualnienia
Można wybrać jeden z dwóch typów wdrożenia: regularne lub uaktualnienia. Regularne wdrożenie usuwa wszelkie poprzednie informacje o wdrożeniu i dane w klastrze, podczas gdy wdrożenie uaktualnienia zachowuje go. Podczas uaktualniania aplikacji sieci szkieletowej usług w programie Visual Studio, należy podać parametry uaktualniania aplikacji i zasady sprawdzania kondycji. Parametry uaktualniania aplikacji pomagają kontrolować uaktualnienie, podczas gdy zasady sprawdzania kondycji określają, czy uaktualnienie zakończyło się pomyślnie. Zobacz [uaktualnienie aplikacji sieci szkieletowej usług: parametry uaktualnienia,](service-fabric-application-upgrade-parameters.md) aby uzyskać więcej informacji.

Istnieją trzy tryby aktualizacji: *Monitorowane,* *NiemonitorowaneAuto*i *NiemonitorowaneManualne.*

* Monitorowane uaktualnienie automatyzuje sprawdzanie kondycji uaktualnienia i aplikacji.
* Uaktualnienie UnmonitoredAuto automatyzuje uaktualnienie, ale pomija sprawdzanie kondycji aplikacji.
* Podczas wykonywania unmonitoredManual uaktualnienia, należy ręcznie uaktualnić każdej domeny uaktualnienia.

Każdy tryb uaktualnienia wymaga różnych zestawów parametrów. Zobacz [Parametry uaktualniania aplikacji,](service-fabric-application-upgrade-parameters.md) aby dowiedzieć się więcej o dostępnych opcjach uaktualniania.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Uaktualnianie aplikacji sieci szkieletowej usług w programie Visual Studio
Jeśli używasz narzędzi sieci szkieletowej usługi Visual Studio do uaktualniania aplikacji sieci szkieletowej usług, można określić proces publikowania do uaktualnienia, a nie regularne wdrożenie, zaznaczając pole wyboru **Uaktualnij aplikację.**

### <a name="to-configure-the-upgrade-parameters"></a>Aby skonfigurować parametry uaktualnienia
1. Kliknij przycisk **Ustawienia** obok pola wyboru. Zostanie wyświetlone okno dialogowe **Edytowanie parametrów uaktualnienia.** Okno dialogowe **Edytowanie parametrów uaktualnienia** obsługuje tryby uaktualniania Monitorowane, NiemonitorowaneAuto i Niemonitorowane Wiele.
2. Wybierz tryb uaktualnienia, którego chcesz użyć, a następnie wypełnij siatkę parametrów.

    Każdy parametr ma wartości domyślne. Opcjonalny parametr *DefaultServiceTypeHealthPolicy* przyjmuje dane wejściowe tabeli mieszania. Oto przykład formatu wprowadzania tabeli mieszania *defaultservicetypehealthpolicy:*

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* to kolejny opcjonalny parametr, który przyjmuje dane wejściowe tabeli mieszania w następującym formacie:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Oto prawdziwy przykład:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Jeśli wybierzesz UnmonitoredManual upgrade mode, należy ręcznie uruchomić konsolę programu PowerShell, aby kontynuować i zakończyć proces uaktualniania. Zapoznaj się [z uaktualnieniem aplikacji sieci szkieletowej usług: zaawansowane tematy,](service-fabric-application-upgrade-advanced.md) aby dowiedzieć się, jak działa ręczne uaktualnianie.

## <a name="upgrade-an-application-by-using-powershell"></a>Uaktualnianie aplikacji przy użyciu programu PowerShell
Polecenia cmdlet programu PowerShell umożliwiają uaktualnienie aplikacji sieci szkieletowej usług. Zobacz [samouczek uaktualniania aplikacji sieci szkieletowej usług](service-fabric-application-upgrade-tutorial.md) i [Uruchamianie UsługiFabricApplicationUpligrade, aby](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) uzyskać szczegółowe informacje.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Określanie zasad sprawdzania kondycji w pliku manifestu aplikacji
Każda usługa w aplikacji sieci szkieletowej usług może mieć własne parametry zasad kondycji, które zastępują wartości domyślne. Te wartości parametrów można podać w pliku manifestu aplikacji.

W poniższym przykładzie pokazano, jak zastosować unikatowe zasady sprawdzania kondycji dla każdej usługi w manifeście aplikacji.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat uaktualniania aplikacji, zobacz [Uaktualnianie aplikacji za pomocą programu Visual Studio](service-fabric-application-upgrade-tutorial.md).
