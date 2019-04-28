---
title: Wyświetlanie dzienników kontenerów w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Opisuje sposób wyświetlania dzienników kontenera dla uruchomionej usługi kontenerów usługi Service Fabric przy użyciu narzędzia Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: aljo
ms.openlocfilehash: 0408010a49b8ec83aa02c74887139f663788ad80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881488"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Wyświetl dzienniki dla kontenera usługi Service Fabric
Usługa Azure Service Fabric jest koordynatora kontenerów i obsługuje zarówno [kontenerów systemu Linux i Windows](service-fabric-containers-overview.md).  W tym artykule opisano sposób wyświetlania dzienników kontenera uruchomioną usługę kontenera lub martwy kontenera, aby zdiagnozować i rozwiązać problemy.

## <a name="access-the-logs-of-a-running-container"></a>Uzyskiwanie dostępu do dzienników działający kontener
Dzienniki kontenerów można uzyskać dostęp za pomocą [narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  W przeglądarce sieci web, Otwórz narzędzie Service Fabric Explorer z punktu końcowego zarządzania klastrem, przechodząc do [ http://mycluster.region.cloudapp.azure.com:19080/Explorer ](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Dzienniki kontenera znajdują się w węźle klastra, który wystąpienie usługi kontenera. Na przykład pobrać dzienniki sieci web frontonu kontener [Linux głosowanie w przykładowej aplikacji](service-fabric-quickstart-containers-linux.md). W widoku drzewa rozwiń **klastra**>**aplikacje**>**VotingType**>**Service fabric: / Voting / azurevotefront**.  Następnie rozwiń węzeł partycji (d1aa737e-f22a-e347-be16-eec90be24bc1, w tym przykładzie) i zobacz, że kontener działa w węźle klastra *_lnxvm_0*.

W widoku drzewa, Znajdź pakiet kodu na *_lnxvm_0* węzła, rozwijając **węzłów**>**_lnxvm_0**>**Service fabric: / Voting**  > **azurevotfrontPkg**>**pakiety kodu**>**kodu**.  Następnie wybierz pozycję **dzienniki kontenerów** opcję, aby wyświetlić dzienniki kontenerów.

![Platforma Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Uzyskiwanie dostępu do dzienników kontenera nieużywany lub które uległy awarii
Począwszy od v6.2, możesz również pobrać dzienniki dla kontenera nieużywany lub które uległy awarii przy użyciu [interfejsów API REST](/rest/api/servicefabric/sfclient-index) lub [Service interfejs wiersza polecenia platformy Service Fabric (SFCTL)](service-fabric-cli.md) poleceń.

### <a name="set-container-retention-policy"></a>Ustawianie zasad przechowywania kontenerów
Aby ułatwić diagnozowanie błędów uruchamiania kontenerów, usługa Service Fabric (w wersji 6.1 lub nowszej) obsługuje przechowywanie kontenerów, w przypadku których działanie zostało przerwane lub uruchamianie nie powiodło się. Te zasady można ustawić w pliku **ApplicationManifest.xml**, jak pokazano w poniższym fragmencie kodu:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

Ustawienie **ContainersRetentionCount** określa liczbę kontenerów do przechowywania w przypadku wystąpienia w nich błędu. Jeśli zostanie określona wartość ujemna, będą przechowywane wszystkie kontenery z błędami. Gdy **ContainersRetentionCount** atrybut nie zostanie określony, będą przechowywane nie kontenery. Atrybut **ContainersRetentionCount** obsługuje też parametry aplikacji, dzięki czemu użytkownicy mogą określać różne wartości dla klastrów testowych i produkcyjnych. W przypadku używania tej funkcji można zastosować ograniczenia rozmieszczania, tak aby obiektem docelowym usługi kontenera był określony węzeł, co zapobiega przenoszeniu usługi kontenera do innych węzłów. Wszelkie kontenery przechowywane przy użyciu tej funkcji należy usunąć ręcznie.

Ustawienie **RunInteractive** odnosi się do platformy Docker `--interactive` i `tty` [flagi](https://docs.docker.com/engine/reference/commandline/run/#options). Gdy to ustawienie ma wartość true w pliku manifestu, te flagi są używane do uruchamiania kontenera.  

### <a name="rest"></a>REST
Użyj [Pobierz dzienniki wdrożone na węzeł kontenera](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) operację, aby pobrać dzienniki dla kontenera, które uległy awarii. Określ nazwę węzła, że kontener został uruchomiony na, nazwa aplikacji, nazwy manifestu usługi i nazwy pakietu kodu.  Określ `&Previous=true`. Odpowiedź będzie zawierać dzienniki kontenerów martwy kontenera wystąpienie pakietu kodu.

Identyfikator URI żądania ma następującą postać:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Przykładowe żądanie:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

Treść odpowiedzi 200:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Użyj [sfctl get-container dzienniki usługi](service-fabric-sfctl-service.md) polecenie, aby pobrać dzienniki dla kontenera które uległy awarii.  Określ nazwę węzła, że kontener został uruchomiony na, nazwa aplikacji, nazwy manifestu usługi i nazwy pakietu kodu. Określ `--previous` flagi.  Odpowiedź będzie zawierać dzienniki kontenerów martwy kontenera wystąpienie pakietu kodu.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Odpowiedź:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Kolejne kroki
- Trzymać się kolejności [tworzenie samouczek aplikacji kontenera systemu Linux](service-fabric-tutorial-create-container-images.md).
- Dowiedz się więcej o [usługi Service Fabric i kontenery](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
