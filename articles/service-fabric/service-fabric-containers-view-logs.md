---
title: Wyświetl dzienniki kontenery w sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób wyświetlania kontenera dzienniki uruchomione usługi kontenera sieci szkieletowej usług za pomocą Eksploratora usługi sieć szkieletowa.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: ryanwi
ms.openlocfilehash: b2b3562f65e7e861b7e4dff7b7c26d58081ff29e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Wyświetl dzienniki dla usługi kontenera sieci szkieletowej usług
Azure Service Fabric jest orchestrator kontenera i obsługuje zarówno [kontenery systemu Linux i Windows](service-fabric-containers-overview.md).  W tym artykule opisano sposób wyświetlania dzienników kontenera uruchomioną usługę kontenera lub martwy kontenera, aby zdiagnozować i rozwiązać problemy.

## <a name="access-the-logs-of-a-running-container"></a>Uzyskiwanie dostępu do dzienników uruchomionych kontenera
Kontener Dzienniki mogą uzyskać dostęp za pomocą [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  W przeglądarce sieci web Otwórz Eksploratora usługi sieć szkieletowa z klastra zarządzania końcowego przechodząc do [ http://mycluster.region.cloudapp.azure.com:19080/Explorer ](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Kontener Dzienniki znajdują się w węźle klastra, który wystąpienie usługi kontenera. Na przykład Pobierz dzienniki sieci web frontonu kontenera [Linux głosowanie w przykładowej aplikacji](service-fabric-quickstart-containers-linux.md). W widoku drzewa, rozwiń węzeł **klastra**>**aplikacji**>**VotingType**>**fabric: / głosowania / azurevotefront**.  Następnie rozwiń partycji (d1aa737e-f22a-e347-be16-eec90be24bc1, w tym przykładzie) i sprawdzić, czy kontenera jest uruchomiona w węźle klastra *_lnxvm_0*.

W widoku drzewa, Znajdź pakiet kodu na *_lnxvm_0* węzła rozwijając **węzłów**>**_lnxvm_0**>**fabric: / głosu**  > **azurevotfrontPkg**>**pakiety kodu**>**kod**.  Następnie wybierz **dzienniki kontenera** opcję, aby wyświetlić dzienniki kontenera.

![Platforma Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Uzyskiwanie dostępu do dzienników martwy lub awaria kontenera
Począwszy od v6.2 możesz można również pobrać dzienniki dla kontenera martwy lub awaria przy użyciu [interfejsów API REST](/rest/api/servicefabric/sfclient-index) lub [usługi sieci szkieletowej interfejsu wiersza polecenia (SFCTL)](service-fabric-cli.md) poleceń.

### <a name="rest"></a>REST
Użyj [Pobierz dzienniki wdrożone na węzeł kontenera](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) operację, aby pobrać dzienniki dla kontenera awaria. Określ nazwę węzła, który kontenera była uruchomiona na, nazwa aplikacji, nazwa manifestu usługi i nazwy pakietu kodu.  Określ `&Previous=true`. Odpowiedź będzie zawierać dzienniki kontenera martwy kontenera wystąpienia pakiet kodu.

Identyfikator URI żądania ma następujący format:

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

### <a name="service-fabric-sfctl"></a>Usługa sieci szkieletowej (SFCTL)
Użyj [sfctl get kontenera — dzienniki usługi](service-fabric-sfctl-service.md) polecenie, aby pobrać dzienniki dla kontenera awaria.  Określ nazwę węzła, który kontenera była uruchomiona na, nazwa aplikacji, nazwa manifestu usługi i nazwy pakietu kodu. Określ `-previous` flagi.  Odpowiedź będzie zawierać dzienniki kontenera martwy kontenera wystąpienia pakiet kodu.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –previous
```
Odpowiedź:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Kolejne kroki
- Pracy za pośrednictwem [utworzyć samouczek aplikacji kontenera Linux](service-fabric-tutorial-create-container-images.md).
- Dowiedz się więcej o [sieci szkieletowej usług i kontenerów](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
