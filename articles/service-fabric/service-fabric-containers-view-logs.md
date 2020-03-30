---
title: Wyświetlanie dzienników kontenerów w sieci szkieletowej usług Azure
description: W tym artykule opisano sposób wyświetlania dzienników kontenerów dla uruchomionych usług kontenera sieci szkieletowej usług przy użyciu Eksploratora sieci szkieletowej usług.
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: c47a408b272f95dbfcf3d791c644bfeb52254a72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458185"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Wyświetlanie dzienników usługi kontenera sieci szkieletowej usług
Usługa Azure Service Fabric jest koordynatorem kontenerów i obsługuje [kontenery systemu Linux i Windows.](service-fabric-containers-overview.md)  W tym artykule opisano sposób wyświetlania dzienników kontenerów uruchomionej usługi kontenera lub martwego kontenera, dzięki czemu można zdiagnozować i rozwiązać problemy.

## <a name="access-the-logs-of-a-running-container"></a>Dostęp do dzienników uruchomionego kontenera
Dzienniki kontenerów są dostępne za pomocą [Eksploratora sieci szkieletowej usług](service-fabric-visualizing-your-cluster.md).  W przeglądarce sieci Web otwórz Eksploratora sieci szkieletowej `http://mycluster.region.cloudapp.azure.com:19080/Explorer`usług z punktu końcowego zarządzania klastra, przechodząc do .  

Dzienniki kontenerów znajdują się w węźle klastra, na który jest uruchomione wystąpienie usługi kontenera. Na przykład pobierz dzienniki kontenera front-endu sieci Web [aplikacji przykładowej głosowania Linuksa](service-fabric-quickstart-containers-linux.md). W widoku drzewa rozwiń węzeł>**szkieletowy aplikacji**>>klastra:/Głosowanie/azurevotefront . **Cluster****VotingType****fabric:/Voting/azurevotefront**  Następnie rozwiń partycję (d1aa737e-f22a-e347-be16-eec90be24bc1, w tym przykładzie) i zobacz, że kontener jest uruchomiony w węźle klastra *_lnxvm_0*.

W widoku drzewa znajdź pakiet kodu w węźle *_lnxvm_0,* rozwijając **węzły**>**_lnxvm_0**>**sieci szkieletowej:/Kod**>**pakietów**>kodu**AzurevotfrontPkg**>**.**  Następnie wybierz opcję **Dzienniki kontenerów,** aby wyświetlić dzienniki kontenerów.

![Platforma Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Dostęp do dzienników martwego lub rozbitego kontenera
Począwszy od wersji 6.2, można również pobrać dzienniki dla martwego lub rozbitego kontenera przy użyciu [interfejsów API REST](/rest/api/servicefabric/sfclient-index) lub [interfejsu wiersza polecenia sieci szkieletowej usług (SFCTL).](service-fabric-cli.md)

### <a name="set-container-retention-policy"></a>Ustawianie zasad przechowywania kontenerów
Aby ułatwić diagnozowanie błędów uruchamiania kontenerów, usługa Service Fabric (w wersji 6.1 lub nowszej) obsługuje przechowywanie kontenerów, w przypadku których działanie zostało przerwane lub uruchamianie nie powiodło się. Te zasady można ustawić w pliku **ApplicationManifest.xml**, jak pokazano w poniższym fragmencie kodu:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

Ustawienie **ContainersRetentionCount** określa liczbę kontenerów do przechowywania w przypadku wystąpienia w nich błędu. Jeśli zostanie określona wartość ujemna, będą przechowywane wszystkie kontenery z błędami. Gdy **ContainersRetentionCount** atrybut nie jest określony, żadne kontenery zostaną zachowane. Atrybut **ContainersRetentionCount** obsługuje też parametry aplikacji, dzięki czemu użytkownicy mogą określać różne wartości dla klastrów testowych i produkcyjnych. W przypadku używania tej funkcji można zastosować ograniczenia rozmieszczania, tak aby obiektem docelowym usługi kontenera był określony węzeł, co zapobiega przenoszeniu usługi kontenera do innych węzłów. Wszelkie kontenery przechowywane przy użyciu tej funkcji należy usunąć ręcznie.

Ustawienie **RunInteractive** odpowiada docker `--interactive` i `tty` [flagi](https://docs.docker.com/engine/reference/commandline/run/#options). Gdy to ustawienie jest ustawione na true w pliku manifestu, flagi te są używane do uruchamiania kontenera.  

### <a name="rest"></a>REST
Użyj [Get Container Logs wdrożone w węźle](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) operacji, aby uzyskać dzienniki dla kontenera awarii. Określ nazwę węzła, na który był uruchomiony kontener, nazwę aplikacji, nazwę manifestu usługi i nazwę pakietu kodu.  Podaj wartość `&Previous=true`. Odpowiedź będzie zawierać dzienniki kontenera dla martwego kontenera wystąpienia pakietu kodu.

Identyfikator URI żądania ma następujący formularz:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Przykładowe żądanie:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 Organ reagowania:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Sieci szkieletowej usług (SFCTL)
Użyj polecenia [get-container-logs usługi sfctl,](service-fabric-sfctl-service.md) aby pobrać dzienniki dla rozbitego kontenera.  Określ nazwę węzła, na który był uruchomiony kontener, nazwę aplikacji, nazwę manifestu usługi i nazwę pakietu kodu. Określ `--previous` flagę.  Odpowiedź będzie zawierać dzienniki kontenera dla martwego kontenera wystąpienia pakietu kodu.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Odpowiedź:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Następne kroki
- Praca za pomocą [samouczka Tworzenie aplikacji kontenera systemu Linux](service-fabric-tutorial-create-container-images.md).
- Dowiedz się więcej o [sieci szkieletowej usług i kontenerach](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
