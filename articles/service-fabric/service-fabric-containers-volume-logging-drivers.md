---
title: Usługa Azure Service Fabric Docker Compose (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Sieć szkieletowa usług Azure akceptuje format rozwiązania Docker Compose ułatwiające organizowania istniejących kontenerów przy użyciu sieci szkieletowej usług. Obsługa rozwiązania Docker Compose jest obecnie w wersji zapoznawczej.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 72ff8e99453d3067492d50c3e35396dba9f23c12
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="use-docker-volume-plug-ins-and-logging-drivers-in-your-container"></a>Użyj Docker woluminu dodatków plug-in i sterowniki rejestrowania w sieci kontenera
Sieć szkieletowa usług Azure obsługuje określanie [Docker woluminu dodatków plug-in](https://docs.docker.com/engine/extend/plugins_volume/) i [sterowniki rejestrowania Docker](https://docs.docker.com/engine/admin/logging/overview/) dla usługi kontenera. Można ją utrwalić danych w [plików Azure](https://azure.microsoft.com/services/storage/files/) podczas przeniesienia z kontenera lub uruchomiona ponownie na innym hoście.

Tylko sterowniki wolumin kontenerów Linux są obecnie obsługiwane. Jeśli używasz systemu Windows kontenery woluminu można mapować do usługi pliki Azure [udziału protokołu SMB3](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/) bez sterownika woluminu. Aby to mapowanie, należy zaktualizować maszyn wirtualnych (VM) w klastrze do najnowszej wersji systemu Windows Server 1709.


## <a name="install-the-docker-volumelogging-driver"></a>Instalacja sterownika wolumin/rejestrowania Docker

Jeśli sterownik wolumin/rejestrowania Docker nie jest zainstalowany na komputerze, można zainstalować go ręcznie przy użyciu protokołu RDP/SSH. Można wykonać instalację z tych protokołów za pośrednictwem [zestawu skalowania maszyn wirtualnych uruchamiania skryptu](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) lub [skryptu SetupEntryPoint](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

Przykładowy skrypt do zainstalowania [sterownik woluminu Docker na platformie Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) wygląda następująco:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

> [!NOTE]
> Windows Server 2016 w centrum danych nie obsługuje instalacji SMB mapowania do kontenerów ([która jest tylko obsługiwana w systemie Windows Server w wersji 1709](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Tego ograniczenia zapobiega mapowania woluminu sieci i plików Azure woluminu sterowników w wersjach starszych niż 1709. 
>   


## <a name="specify-the-plug-in-or-driver-in-the-manifest"></a>Określ wtyczka lub sterownik w manifeście
Dodatki plug-in są określone w manifeście aplikacji:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
        </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

**Źródła** znacznika **woluminu** element odwołuje się do folderu źródłowego. Folder źródłowy może być folderem w maszynie Wirtualnej, która obsługuje kontenery lub trwałego magazynu zdalnego. **Docelowego** tag to lokalizacja, w który **źródła** jest mapowany w kontenerze uruchomione. W związku z tym folderu docelowego nie może być już istnieje w kontenerze Twojej lokalizacji.

Parametry aplikacji są obsługiwane dla woluminów, jak pokazano w poprzednim fragment manifestu (Wyszukaj `MyStoreVar` na przykład użyć).

Podczas określania woluminu wtyczki, usługi Service Fabric automatycznie tworzy woluminu przy użyciu określonych parametrów. **Źródła** tag jest nazwa woluminu i **sterownika** tag określa dodatek plug-in sterownika woluminu. Opcje można określić za pomocą **DriverOption** tagu w następujący sposób:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
Sterownik dziennika Docker jest określony, masz wdrażania agentów (lub kontenery) do obsługi dzienników w klastrze. **DriverOption** tag może służyć do określ opcje dla sterownika dziennika.

## <a name="next-steps"></a>Kolejne kroki
Do wdrażanie kontenerów do klastra usługi sieć szkieletowa, zobacz artykuł [wdrażanie kontenera w sieci szkieletowej usług](service-fabric-deploy-container.md).
