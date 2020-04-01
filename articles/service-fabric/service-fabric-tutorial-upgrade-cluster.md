---
title: Uaktualnianie środowiska wykonawczego sieci szkieletowej usług na platformie Azure
description: W ramach tego samouczka dowiesz się, jak przy użyciu programu PowerShell uaktualnić środowisko uruchomieniowe klastra usługi Service Fabric hostowanego na platformie Azure.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 2fb08d7aba3e35fb6147b75bbcee35b46873b5f6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78252730"
---
# <a name="tutorial-upgrade-the-runtime-of-a-service-fabric-cluster-in-azure"></a>Samouczek: uaktualnianie środowiska uruchomieniowego klastra usługi Service Fabric na platformie Azure

Ten samouczek jest częścią czwartą serii i pokazuje, jak uaktualnić środowisko uruchomieniowe sieci szkieletowej usług w klastrze sieci szkieletowej usług Azure. Ta część samouczka jest napisana dla klastrów sieci szkieletowej usług uruchomionych na platformie Azure i nie ma zastosowania do autonomicznych klastrów sieci szkieletowej usług.

> [!WARNING]
> Na potrzeby tej części samouczka wymagany jest program PowerShell. Uaktualnianie środowiska uruchomieniowego klastra nie jest jeszcze obsługiwane przez narzędzia interfejsu wiersza polecenia platformy Azure. Alternatywnie klaster możesz uaktualnić w portalu. Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra usługi Azure Service Fabric](service-fabric-cluster-upgrade.md).

Jeśli w klastrze jest już uruchomiony najnowszy środowiska uruchomieniowego sieci szkieletowej usług, nie trzeba wykonać tego kroku. Jednak korzystając z tego artykułu, możesz zainstalować dowolne obsługiwane środowisko uruchomieniowe w klastrze usługi Azure Service Fabric.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Odczytywanie wersji klastra
> * Ustawianie wersji klastra

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) na platformie Azure przy użyciu szablonu
> * [Monitorowanie klastra](service-fabric-tutorial-monitor-cluster.md)
> * [Skalowanie klastra na zewnątrz lub do wewnątrz](service-fabric-tutorial-scale-cluster.md)
> * Uaktualnianie środowiska uruchomieniowego klastra
> * [Usuwanie klastra](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Zainstaluj [platformę Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) lub [platformę Azure CLI](/cli/azure/install-azure-cli).
* Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) na platformie Azure
* Skonfiguruj środowisko deweloperskie w systemie Windows. Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com) i tworzenie platformy **Azure,** **ASP.NET i tworzenie stron internetowych**oraz obciążenia **programistyczne .NET Core na wielu platformach.**  Następnie skonfiguruj [środowisko deweloperskie platformy .NET](service-fabric-get-started.md).

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Przed wykonaniem poleceń platformy Azure zaloguj się na konto platformy Azure i wybierz subskrypcję.

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>Uzyskiwanie wersji środowiska uruchomieniowego

Po nawiązaniu połączenia z platformą Azure, wybranej subskrypcji zawierającej klaster sieci szkieletowej usług, można uzyskać wersję środowiska wykonawczego klastra.

```powershell
Get-AzServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

Możesz też uzyskać listę wszystkich klastrów w ramach subskrypcji w następującym przykładzie:

```powershell
Get-AzServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

Zanotuj wartość **ClusterCodeVersion**. Będzie ona używana w następnej sekcji.

## <a name="upgrade-the-runtime"></a>Uaktualnianie środowiska uruchomieniowego

Podaj wartość **ClusterCodeVersion** z poprzedniej sekcji w poleceniu cmdlet `Get-ServiceFabricRuntimeUpgradeVersion`, aby dowiedzieć się, do jakich wersji możesz uaktualnić. To polecenie cmdlet możesz uruchomić tylko na komputerze, który został połączony z Internetem. Jeśli na przykład chcesz zobaczyć wersje środowiska uruchomieniowego, do których można uaktualnić wersję `5.7.198.9494`, użyj następującego polecenia:

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

Dysponując listą wersji, możesz wydać klastrowi usługi Azure Service Fabric polecenie uaktualnienia do nowszej wersji środowiska uruchomieniowego. Jeśli na przykład na potrzeby uaktualnienia jest dostępna wersja `6.0.219.9494`, użyj następującego polecenia w celu uaktualnienia klastra.

```powershell
Set-AzServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> Uaktualnienie środowiska uruchomieniowego klastra może zająć dużo czasu. Program PowerShell jest zablokowany podczas uaktualniania. Stan uaktualnienia możesz sprawdzić za pomocą innej sesji programu PowerShell.

Stan uaktualnienia możesz monitorować przy użyciu programu PowerShell lub interfejsu wiersza polecenia usługi Azure Service Fabric (sfctl).

Najpierw połącz się z klastrem przy użyciu certyfikatu SSL utworzonego w pierwszej części samouczka. Użyj polecenia cmdlet `Connect-ServiceFabricCluster` lub polecenia `sfctl cluster upgrade-status`.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```console
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Następnie użyj polecenia `Get-ServiceFabricClusterUpgrade` lub `sfctl cluster upgrade-status`, aby wyświetlić stan. Zostanie wyświetlony wynik podobny do następującego.

```powershell
Get-ServiceFabricClusterUpgrade

TargetCodeVersion                          : 6.0.219.9494
TargetConfigVersion                        : 3
StartTimestampUtc                          : 11/28/2017 3:09:48 AM
UpgradeState                               : RollingForwardPending
UpgradeDuration                            : 00:09:00
CurrentUpgradeDomainDuration               : 00:09:00
NextUpgradeDomain                          : 1
UpgradeDomainsStatus                       : { "0" = "Completed";
                                             "1" = "Pending";
                                             "2" = "Pending";
                                             "3" = "Pending";
                                             "4" = "Pending" }
UpgradeKind                                : Rolling
RollingUpgradeMode                         : Monitored
FailureAction                              : Rollback
ForceRestart                               : False
UpgradeReplicaSetCheckTimeout              : 37201.09:59:01
HealthCheckWaitDuration                    : 00:05:00
HealthCheckStableDuration                  : 00:05:00
HealthCheckRetryTimeout                    : 00:45:00
UpgradeDomainTimeout                       : 02:00:00
UpgradeTimeout                             : 12:00:00
ConsiderWarningAsError                     : False
MaxPercentUnhealthyApplications            : 0
MaxPercentUnhealthyNodes                   : 100
ApplicationTypeHealthPolicyMap             : {}
EnableDeltaHealthEvaluation                : True
MaxPercentDeltaUnhealthyNodes              : 0
MaxPercentUpgradeDomainDeltaUnhealthyNodes : 0
ApplicationHealthPolicyMap                 : {}
```

```console
sfctl cluster upgrade-status

{
  "codeVersion": "6.0.219.9494",
  "configVersion": "3",

... item cut to save space ...

  },
  "upgradeDomains": [
    {
      "name": "0",
      "state": "Completed"
    },
    {
      "name": "1",
      "state": "Pending"
    },
    {
      "name": "2",
      "state": "Pending"
    },
    {
      "name": "3",
      "state": "Pending"
    },
    {
      "name": "4",
      "state": "Pending"
    }
  ],
  "upgradeDurationInMilliseconds": "PT1H2M4.63889S",
  "upgradeState": "RollingForwardPending"
}
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uzyskiwanie wersji środowiska uruchomieniowego klastra
> * Uaktualnianie środowiska uruchomieniowego klastra
> * Monitorowanie uaktualnienia

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Usuwanie klastra](service-fabric-tutorial-delete-cluster.md)
