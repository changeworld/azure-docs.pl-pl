---
title: Zmienne środowiskowe Service Fabric platformy Azure
description: Dowiedz się więcej na temat zmiennych środowiskowych w usłudze Azure Service Fabric. Zawiera odwołanie do pełnej listy zmiennych i ich użycia.
author: mikkelhegn
ms.topic: reference
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: b13522b1d9f2acd2aa3f7923c1b623fab696056d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645688"
---
# <a name="service-fabric-environment-variables"></a>Zmienne środowiskowe Service Fabric

Service Fabric ma wbudowane zmienne środowiskowe ustawione dla każdego wystąpienia usługi. Pełna lista zmiennych środowiskowych jest poniżej:

| Zmienna środowiskowa                         | Opis                                                            | Przykład                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Nazwa identyfikatora URI sieci szkieletowej aplikacji                                 | Sieć szkieletowa:/moja aplikacja                                                |
| Fabric_CodePackageName                       | Nazwa pakietu kodu, do którego należy proces              | Code                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | Adres IP lub nazwa FQDN punktu końcowego                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | Numer portu punktu końcowego                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Folder dziennika                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | Folder tymczasowy                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Folder roboczy                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | Folder macierzysty aplikacji                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Wartość logiczna określająca, czy proces jest kontenerem                   | false                                                                |
| Fabric_NodeId                                | IDENTYFIKATOR węzła, w którym jest uruchomiony proces                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | Adres IP lub nazwa FQDN węzła, jak określono w pliku manifestu klastra. | localhost lub 10.0.0.1                                                |
| Fabric_NodeName                              | Nazwa węzła węzła, na którym jest uruchomiony proces.                          | _Node_0                                                              |
| Fabric_ServiceName                           | Nazwa identyfikatora URI sieci szkieletowej usługi, jeśli usługa jest hostowana w trybie ExclusiveProcess. Ta wartość zmiennej jest dostępna tylko w przypadku tworzenia usługi za pomocą korzystanie ExclusiveProcess.  | Sieć szkieletowa:/aplikacje/moje usługi                                               |
| Fabric_ServicePackageActivationId            | The ServicePackageActivationId                                         | IDENTYFIKATOR GUID                                                               |
| Fabric_ServicePackageName                    | Nazwa pakietu usługi, którego częścią jest proces                     | Web1Pkg                                                              |

Wewnętrzne zmienne środowiskowe używane przez Service Fabric środowiska uruchomieniowego:

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
