---
title: Konfigurowanie uaktualniania aplikacji usługi Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia dla uaktualnienie aplikacji usługi Service Fabric za pomocą programu Microsoft Visual Studio.
services: service-fabric
documentationcenter: na
author: mikkelhegn
manager: mfussell
editor: tglee
ms.assetid: 1757ba85-0b7b-4f16-8a23-2ddaa61c86c6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/29/2017
ms.author: mikkelhegn
ms.openlocfilehash: 79120371ca2a62e5ef9f2bf38476635db12e9fcc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082847"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Konfigurowanie uaktualniania aplikacji usługi Service Fabric w programie Visual Studio
Visual Studio tools dla usługi Azure Service Fabric zapewnia obsługę uaktualniania publikowania do klastrów lokalnym lub zdalnym. Istnieją trzy scenariusze, w których ma zostać uaktualniony do nowszej wersji, zamiast zastępowania aplikacji podczas testowania i debugowania aplikacji:

* Dane aplikacji nie będzie utracone podczas uaktualniania.
* Dostępność pozostaje wysokiej, dzięki czemu nie będzie żadnych zakłóceń, podczas uaktualniania, jeżeli ma wystarczającej liczby wystąpień usługi rozkłada domen uaktualnienia.
* Testy mogą być uruchamiane w odniesieniu do aplikacji, gdy jest uaktualniana.

## <a name="parameters-needed-to-upgrade"></a>Parametry wymagane do uaktualnienia
Można wybrać spośród dwóch rodzajów wdrożenia: regularnie lub uaktualnienia. Regularne wdrożenia usuwa wszelkie poprzednie informacje na temat wdrażania i dane w klastrze podczas wdrażania uaktualnienia zachowuje on. Podczas uaktualniania aplikacji usługi Service Fabric, w programie Visual Studio, musisz podać parametry uaktualniania aplikacji i kondycji Sprawdź zasady. Parametry uaktualniania aplikacji pomóc w kontrolowaniu uaktualniania, podczas gdy zasady dotyczące kondycji wyboru ustalić, czy uaktualnienie zakończyło się pomyślnie. Zobacz [uaktualnianie aplikacji usługi Service Fabric: parametry uaktualniania](service-fabric-application-upgrade-parameters.md) Aby uzyskać więcej informacji.

Istnieją trzy tryby uaktualnienia: *Monitorowane*, *UnmonitoredAuto*, i *UnmonitoredManual*.

* Uaktualnienie monitorowanej automatyzuje uaktualnienia i sprawdzanie kondycji aplikacji.
* Uaktualnienie UnmonitoredAuto automatyzuje uaktualnienia, ale pomija sprawdzanie kondycji aplikacji.
* Po wykonaniu uaktualnienia UnmonitoredManual, musisz ręcznie uaktualnić każdej z domen.

Każdy tryb uaktualniania wymaga różnych zestawów parametrów. Zobacz [parametry uaktualniania aplikacji](service-fabric-application-upgrade-parameters.md) Aby dowiedzieć się więcej o dostępnych opcjach uaktualniania.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Uaktualnianie aplikacji usługi Service Fabric, w programie Visual Studio
Uaktualnianie aplikacji usługi Service Fabric za pomocą narzędzi Visual Studio Service Fabric, możesz określić proces publikowania, do uaktualnienia, a nie regularnego wdrażania, sprawdzając **Uaktualnij aplikację** pole wyboru.

### <a name="to-configure-the-upgrade-parameters"></a>Aby skonfigurować parametry uaktualniania
1. Kliknij przycisk **ustawienia** przycisk znajdujący się obok pola wyboru. **Edytuj parametry uaktualniania** pojawi się okno dialogowe. **Edytuj parametry uaktualniania** okno dialogowe obsługuje tryb uaktualniania monitorowane, UnmonitoredAuto i UnmonitoredManual.
2. Wybierz tryb uaktualniania, którego chcesz użyć, a następnie wypełnij siatki parametru.

    Każdy parametr ma wartości domyślnej. Opcjonalny parametr *DefaultServiceTypeHealthPolicy* przyjmuje dane wejściowe z tabeli wyznaczania wartości skrótu. Poniżej przedstawiono przykładowy format wejściowy tabeli wyznaczania wartości skrótu dla *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* jest inny opcjonalnym parametrem, który przyjmuje dane wejściowe z tabeli skrótu w następującym formacie:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Oto przykład rzeczywistych:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Jeśli wybierzesz UnmonitoredManual tryb uaktualniania, należy ręcznie uruchomić konsolę programu PowerShell, aby kontynuować, a następnie zakończyć proces uaktualniania. Zapoznaj się [uaktualnianie aplikacji usługi Service Fabric: Tematy zaawansowane](service-fabric-application-upgrade-advanced.md) Aby dowiedzieć się, jak Ręczne uaktualnianie działa.

## <a name="upgrade-an-application-by-using-powershell"></a>Uaktualnianie aplikacji przy użyciu programu PowerShell
Aby uaktualnić aplikację usługi Service Fabric, można użyć poleceń cmdlet programu PowerShell. Zobacz [samouczek dotyczący uaktualniania aplikacji usługi Service Fabric](service-fabric-application-upgrade-tutorial.md) i [Start ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) Aby uzyskać szczegółowe informacje.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Określ zasady sprawdzania kondycji w pliku manifestu aplikacji
Do każdej usługi w aplikacji usługi Service Fabric może mieć własne parametry zasad kondycji, które zastępują wartości domyślne. Możesz podać te wartości parametrów w pliku manifestu aplikacji.

Poniższy przykład pokazuje, jak zastosować zasady sprawdzania kondycji unikatowy dla każdej usługi w manifeście aplikacji.

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
## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat uaktualniania aplikacji, zobacz [uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md).
