---
title: Konfigurowanie uaktualnienia aplikacji usługi Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia dotyczące uaktualniania aplikacji sieci szkieletowej usług za pomocą programu Microsoft Visual Studio.
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
ms.openlocfilehash: faf7fd137d5c1efcd425cf28fd4860c62a719a67
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211654"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Konfigurowanie uaktualnienia aplikacji usługi Service Fabric w programie Visual Studio
Visual Studio tools dla sieci szkieletowej usług Azure zapewniają obsługę uaktualnienia publikowanie do klastrów lokalnym lub zdalnym. Istnieją trzy scenariusze, w których ma zostać uaktualniony do nowszej wersji zamiast zastępowania aplikacji podczas testowania i debugowania aplikacji:

* Dane aplikacji nie będzie utracone podczas uaktualniania.
* Dostępności pozostaje wysoka, nie będzie przerw w działaniu usługi podczas uaktualniania, jeśli brak wystarczającej liczby wystąpień usługi rozmieszczenie domen uaktualnienia.
* Testy mogą być uruchamiane na aplikacji, podczas gdy jest uaktualniany.

## <a name="parameters-needed-to-upgrade"></a>Parametry niezbędne do uaktualnienia
Są dostępne dwa typy wdrażania: regularne lub uaktualniania. Regularne wdrożenia usuwa wszelkie poprzednie informacje na temat wdrażania i dane w klastrze, podczas wdrażania uaktualnienia zachowuje on. Podczas uaktualniania aplikacji usługi Service Fabric w programie Visual Studio, musisz podać parametry uaktualniania aplikacji i kondycji Sprawdź zasady. Parametry uaktualniania aplikacji ograniczyć uaktualnienia, podczas gdy zasady dotyczące kondycji wyboru ustalić, czy uaktualnienie zakończyło się pomyślnie. Zobacz [uaktualniania aplikacji usługi Service Fabric: parametry uaktualnienia](service-fabric-application-upgrade-parameters.md) więcej szczegółów.

Istnieją trzy tryby uaktualnienia: *monitorowanej*, *UnmonitoredAuto*, i *UnmonitoredManual*.

* Uaktualnienie monitorowanej automatyzuje uaktualnienia i sprawdzenie kondycji aplikacji.
* Uaktualnienie UnmonitoredAuto automatyzuje uaktualnienia, ale pomija sprawdzanie kondycji aplikacji.
* Po wykonaniu uaktualnienia UnmonitoredManual, należy ręcznie uaktualnić każdej z domen.

Każdy tryb uaktualniania wymaga różnych zestawów parametrów. Zobacz [parametry uaktualniania aplikacji](service-fabric-application-upgrade-parameters.md) Aby dowiedzieć się więcej o dostępnych opcjach uaktualniania.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Uaktualnianie aplikacji usługi Service Fabric w programie Visual Studio
Jeśli używasz narzędzia Visual Studio sieci szkieletowej usług do uaktualniania aplikacji usługi Service Fabric, można określić proces publikowania, do uaktualnienia, a nie regularnego wdrażania sprawdzając **uaktualnienie aplikacji** pole wyboru.

### <a name="to-configure-the-upgrade-parameters"></a>Aby skonfigurować parametry uaktualnienia
1. Kliknij przycisk **ustawienia** przycisk obok pola wyboru. **Edytuj parametry uaktualnienia** zostanie wyświetlone okno dialogowe. **Edytuj parametry uaktualnienia** okno dialogowe obsługuje monitorowana, UnmonitoredAuto i UnmonitoredManual tryby uaktualnienia.
2. Wybierz tryb uaktualniania, który chcesz wykorzystać, a następnie wypełnij siatce parametrów.

    Każdy parametr ma wartości domyślnej. Opcjonalny parametr *DefaultServiceTypeHealthPolicy* przyjmuje wprowadzania tabeli skrótów. Poniżej przedstawiono przykładowy format wejściowy tabeli skrótów dla *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* jest inny opcjonalnym parametrem, który przyjmuje wprowadzania tabeli skrótów w następującym formacie:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Oto przykład rzeczywistych:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. W przypadku wybrania UnmonitoredManual tryb uaktualniania, należy ręcznie uruchomić konsolę programu PowerShell, aby kontynuować, a następnie Zakończ proces uaktualniania. Zapoznaj się [uaktualniania aplikacji usługi Service Fabric: Tematy zaawansowane](service-fabric-application-upgrade-advanced.md) Aby dowiedzieć się, jak Ręczne uaktualnianie działa.

## <a name="upgrade-an-application-by-using-powershell"></a>Uaktualnianie aplikacji przy użyciu programu PowerShell
Polecenia cmdlet programu PowerShell służą do uaktualnienia aplikacji sieci szkieletowej usług. Zobacz [samouczek uaktualniania aplikacji sieci szkieletowej usług](service-fabric-application-upgrade-tutorial.md) i [Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx) Aby uzyskać szczegółowe informacje.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Określ zasady sprawdzania kondycji w pliku manifestu aplikacji
Każda usługa w aplikacji platformy Service Fabric może mieć własną parametry zasad kondycji, które zastępują ustawienia domyślne. Możesz podać te wartości parametrów w pliku manifestu aplikacji.

Poniższy przykład pokazuje, jak zastosować zasady sprawdzania kondycji unikatowy dla poszczególnych usług w manifeście aplikacji.

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
Aby uzyskać więcej informacji na temat uaktualniania aplikacji, zobacz [uaktualniania aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md).
