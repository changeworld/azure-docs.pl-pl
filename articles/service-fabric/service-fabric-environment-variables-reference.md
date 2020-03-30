---
title: Zmienne środowiskowe sieci szkieletowej usługi Azure
description: Dowiedz się więcej o zmiennych środowiskowych w sieci szkieletowej usług Azure. Zawiera odwołanie do pełnej listy zmiennych i ich zastosowań.
author: mikkelhegn
ms.topic: reference
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: b13522b1d9f2acd2aa3f7923c1b623fab696056d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645688"
---
# <a name="service-fabric-environment-variables"></a>Zmienne środowiskowe sieci szkieletowej usług

Usługa Sieci szkieletowej ma wbudowane zmienne środowiskowe ustawione dla każdego wystąpienia usługi. Pełna lista zmiennych środowiskowych znajduje się poniżej:

| Zmienna środowiskowa                         | Opis                                                            | Przykład                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Nazwa uri tkaniny aplikacji                                 | tkanina:/MyApplication                                                |
| Fabric_CodePackageName                       | Nazwa pakietu kodu, do którego należy proces              | Code                                                                 |
| Fabric_Endpoint\_adres\_*serviceendpointname* IPOrFQDN     | Adres IP lub FQDN punktu końcowego                                 | 10.0.0.1                                                     |
| Usługa\_\_*ServiceEndpointName* punktu końcowego sieci szkieletowej              | Numer portu dla punktu końcowego                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Folder dziennika                                                             | C:\\\\\\\\Dziennik MyApplicationType_App12\\\\_Node_0 _App\\\\\\\\danych      |
| Fabric_Folder_App_Temp                       | Folder tymczasowy                                                            | C:\\\\\\\\_App danych\\\\\\\\_Node_0 temp MyApplicationType_App12\\\\     |
| Fabric_Folder_App_Work                       | Folder roboczy                                                            | C:\\\\\\\\MyApplicationType_App12 _App\\\\\\\\\\_Node_0\\danych     |
| Fabric_Folder_Application                    | Folder macierzysty aplikacji                                           | C:\\\\\\\\MyApplicationType_App12\\\\_App _Node_0 MyApplicationType_App12\\\\MyApplicationType_App12 danych             |
| Fabric_IsContainerHost                       | Bool określający, czy proces jest kontenerem                   | false                                                                |
| Fabric_NodeId                                | Identyfikator węzła w węźle uruchamianym procesem                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | Adres IP lub FQDN węzła, jak określono w pliku manifestu klastra. | localhost lub 10.0.0.1                                                |
| Fabric_NodeName                              | Nazwa węzła węzła uruchomionego przez proces                          | _Node_0                                                              |
| Fabric_ServiceName                           | Nazwa usługi uri sieci szkieletowej, jeśli usługa jest hostowana w trybie ExclusiveProcess. Ta wartość zmiennej jest dostępna tylko wtedy, gdy utworzysz usługę za pomocą ServicePackageActivationMode ExclusiveProcess.  | tkanina:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | The ServicePackageActivationId                                         | Identyfikator GUID                                                               |
| Fabric_ServicePackageName                    | Nazwa pakietu usług, do, że proces jest częścią                     | Web1Pkg (własnce do sieci Web)                                                              |

Wewnętrzne zmienne środowiskowe używane przez środowisko uruchomieniowe sieci szkieletowej usług:

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
- TkaninaAktywatorAddress
- FabricPackageFileName
- HostedServiceName
