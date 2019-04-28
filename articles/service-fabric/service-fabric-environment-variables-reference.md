---
title: Zmienne środowiskowe w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dokumentacja dotycząca usługi Service Fabric zmiennych środowiskowych
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: 1c8400898dba59f312ba9d994ee711a5e241973a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946726"
---
# <a name="service-fabric-environment-variables"></a>Zmienne środowiskowe usługi Service Fabric

Usługa Service Fabric zawiera zmienne środowiskowe wbudowanych ustawione dla każdego wystąpienia usługi. Pełną listę zmiennych środowiskowych znajduje się poniżej:

| Zmienna środowiskowa                         | Opis                                                            | Przykład                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Nazwa identyfikatora uri sieci szkieletowej aplikacji                                 | Service Fabric: / MyApplication                                                |
| Fabric_CodePackageName                       | Nazwa pakietu kodu, do której należy ten proces              | Kod                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | Adres ip lub nazwa FQDN punktu końcowego                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | Numer portu dla punktu końcowego                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Folder dziennika                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | Folder tymczasowy                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Folder roboczy                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | Folder główny aplikacji                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Bool, określając, czy proces jest kontenerem                   | false                                                                |
| Fabric_NodeId                                | Identyfikator węzła węzła, w którym jest uruchomiony proces                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | Plik manifestu adresu IP lub nazwa FQDN węzła, jak określono w klastrze. | nazwy LocalHost ani 10.0.0.1                                                |
| Fabric_NodeName                              | Nazwa węzła węzła, w którym jest uruchomiony proces                          | _Node_0                                                              |
| Fabric_ServiceName                           | Nazwa identyfikatora uri sieci szkieletowej usługi, jeśli usługa jest hostowana w trybie ExclusiveProcess. Ta wartość zmiennej jest dostępna tylko w przypadku utworzenia usługi ServicePackageActivationMode ExclusiveProcess.  | Service Fabric: / MyApplication/Moja_usługa                                               |
| Fabric_ServicePackageActivationId            | The ServicePackageActivationId                                         | IDENTYFIKATOR GUID                                                               |
| Fabric_ServicePackageName                    | Nazwa pakietu z procesu jest częścią                     | Web1Pkg                                                              |

Zmienne środowiskowe wewnętrznego, używane przez środowisko uruchomieniowe usługi Service Fabric:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName
