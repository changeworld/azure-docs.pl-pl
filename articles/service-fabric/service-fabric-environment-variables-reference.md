---
title: Zmienne środowiskowe sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Dokumentacja referencyjna dla zmiennych środowiskowych sieci szkieletowej usług
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
ms.openlocfilehash: f7c36fec7ff58c225e41899e8264ca1dde95ce7c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-environment-variables"></a>Zmienne środowiskowe sieci szkieletowej usług

Sieć szkieletowa usług ma zmienne środowiskowe wbudowanych ustawić dla każdego wystąpienia usługi. Pełną listę zmiennych środowiskowych znajduje się poniżej:

| Zmienna środowiskowa                         | Opis                                                            | Przykład                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Nazwa identyfikatora uri sieci szkieletowej aplikacji                                 | Fabric: / moja_aplikacja                                                |
| Fabric_CodePackageName                       | Nazwa pakietu kodu, do której należy proces              | Kod                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | Adres ip lub nazwa FQDN punktu końcowego                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | Numer portu dla punktu końcowego                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Folder dziennika                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | Folder tymczasowy                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Folderów roboczych                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | Folder macierzysty aplikacji                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Bool, określając, czy proces jest kontenerem                   | false                                                                |
| Fabric_NodeId                                | Identyfikator węzła węzła uruchomionego procesu                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | Adres IP lub nazwę FQDN węzła, jak określono w klastrze pliku manifestu. | nazwy LocalHost ani 10.0.0.1                                                |
| Fabric_NodeName                              | Nazwa węzła węzła uruchomionego procesu                          | _Node_0                                                              |
| Fabric_ServiceName                           | Nazwa usługi, jeśli usługa jest obsługiwana w trybie ExclusiveProcess. Wartość tej zmiennej jest dostępna tylko po utworzeniu usługi z ServicePackageActivationMode ExclusiveProcess.  | MyService                                               |
| Fabric_ServicePackageActivationId            | The ServicePackageActivationId                                         | IDENTYFIKATOR GUID                                                               |
| Fabric_ServicePackageName                    | Nazwa pakietu usługi proces jest częścią                     | Web1Pkg                                                              |

Zmienne środowiskowe wewnętrzny używany przez środowisko uruchomieniowe usługi sieć szkieletowa:

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