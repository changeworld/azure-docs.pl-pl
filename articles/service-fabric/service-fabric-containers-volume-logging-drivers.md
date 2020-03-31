---
title: Sterownik woluminu usługi Azure Files dla sieci szkieletowej usług
description: Usługa Service Fabric obsługuje przy użyciu plików azure do tworzenia kopii zapasowych woluminów z kontenera.
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: 514a0cb12359d58e38ebc30ae12cdb277757f2b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750042"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Sterownik woluminu usługi Azure Files dla sieci szkieletowej usług

Sterownik woluminu usługi Azure Files to [wtyczka woluminu platformy Docker,](https://docs.docker.com/engine/extend/plugins_volume/) która udostępnia woluminy oparte na [plikach Azure](/azure/storage/files/storage-files-introduction) dla kontenerów platformy Docker. Jest on dostarczany jako aplikacja sieci szkieletowej usług, które można wdrożyć w klastrze sieci szkieletowej usług w celu zapewnienia woluminów dla innych aplikacji kontenerów sieci szkieletowej usług w klastrze.

> [!NOTE]
> Wersja 6.5.661.9590 wtyczki woluminu usługi Azure Files została wydana w celu zapewnienia ogólnej dostępności.
>

## <a name="prerequisites"></a>Wymagania wstępne
* Wtyczka woluminu usługi Azure Files w systemie Windows działa tylko w [wersji 1709](/windows-server/get-started/whats-new-in-windows-server-1709)systemu [Windows 10, Windows 10 w wersji 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) lub nowszej.

* Werekcie woluminu usługi Azure Files dla systemu Windows działa we wszystkich wersjach systemu operacyjnego obsługiwanych przez usługę Service Fabric.

* Wtyczka woluminu usługi Azure Files działa tylko w sieci szkieletowej usług w wersji 6.2 i nowszej.

* Postępuj zgodnie z instrukcjami w [dokumentacji usługi Azure Files,](/azure/storage/files/storage-how-to-create-file-share) aby utworzyć udział plików dla aplikacji kontenera sieci szkieletowej usług do użycia jako wolumin.

* Program Powershell będzie potrzebny [z zainstalowanym modułem sieci szkieletowej usług](/azure/service-fabric/service-fabric-get-started) lub modułem [SFCTL.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)

* Jeśli używasz kontenerów funkcji Hyper-V, należy dodać następujące fragmenty kodu w sekcji ClusterManifest (klaster lokalny) lub fabricSettings w szablonie usługi Azure Resource Manager (klaster azure) lub ClusterConfig.json (klaster autonomiczny).

W ClusterManifest należy dodać następujące elementy w sekcji Hosting. W tym przykładzie nazwa woluminu to **sfazurefile,** a port, który nasłuchuje w klastrze, to **19100**. Zastąp je odpowiednimi wartościami dla klastra.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

W sekcji fabricSettings w szablonie usługi Azure Resource Manager (dla wdrożeń platformy Azure) lub ClusterConfig.json (dla wdrożeń autonomicznych) należy dodać następujący fragment kodu. Ponownie zastąp nazwę woluminu i wartości portów własnymi.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Wdrażanie przykładowej aplikacji przy użyciu sterownika woluminu usługi Azure Files

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Korzystanie z usługi Azure Resource Manager za pośrednictwem dostarczonego skryptu programu Powershell (zalecane)

Jeśli klaster ma swoją siedzibę na platformie Azure, zaleca się wdrażanie aplikacji przy użyciu modelu zasobów aplikacji usługi Azure Resource Manager w celu ułatwienia użycia i aby ułatwić przejście do modelu obsługi infrastruktury jako kodu. Takie podejście eliminuje konieczność śledzenia wersji aplikacji dla sterownika woluminu usługi Azure Files. Umożliwia również obsługę oddzielnych szablonów usługi Azure Resource Manager dla każdego obsługiwanego systemu operacyjnego. Skrypt zakłada, że wdrażasz najnowszą wersję aplikacji Usługi Azure Files i przyjmuje parametry dla typu systemu operacyjnego, identyfikatora subskrypcji klastra i grupy zasobów. Skrypt można pobrać z [witryny pobierania sieci szkieletowej usług](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip). Należy zauważyć, że to automatycznie ustawia ListenPort, który jest portem, na którym wtyczka woluminu usługi Azure Files nasłuchuje żądań z demona platformy Docker, do 19100. Można go zmienić, dodając parametr o nazwie "listenPort". Upewnij się, że port nie jest w konflikcie z żadnym innym portem używanym przez klaster lub aplikacje.
 

Polecenie wdrożenia usługi Azure Resource Manager dla systemu Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Polecenie wdrożenia usługi Azure Resource Manager dla systemu Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Po pomyślnym uruchomieniu skryptu można przejść do [sekcji konfigurowania aplikacji.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Ręczne wdrażanie klastrów autonomicznych

Aplikacja sieci szkieletowej usług, która udostępnia woluminy dla kontenerów, można pobrać z [witryny pobierania sieci szkieletowej usług.](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip) Aplikację można wdrożyć w klastrze za pośrednictwem interfejsów API [programu PowerShell,](./service-fabric-deploy-remove-applications.md) [interfejsu wiersza polecenia](./service-fabric-application-lifecycle-sfctl.md) lub sieci [Szkieletowej.](./service-fabric-deploy-remove-applications-fabricclient.md)

1. Korzystając z wiersza polecenia, zmień katalog na katalog główny pobranego pakietu aplikacji.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Następnie skopiuj pakiet aplikacji do magazynu obrazów z odpowiednimi wartościami dla [ApplicationPackagePath] i [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Zarejestruj typ aplikacji

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Utwórz aplikację, zwracając szczególną uwagę na wartość parametru aplikacji **ListenPort.** Ta wartość jest portem, na którym wtyczka woluminu usługi Azure Files nasłuchuje żądań z demona platformy Docker. Upewnij się, że port dostarczony do aplikacji jest zgodny z portalami VolumePluginPorts w klastrzeManifest i nie powoduje konfliktu z żadnym innym portem używanym przez klaster lub aplikacje.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Centrum danych systemu Windows Server 2016 nie obsługuje mapowania instalacji SMB do kontenerów[(jest to obsługiwane tylko w systemie Windows Server w wersji 1709](/virtualization/windowscontainers/manage-containers/container-storage)). To ograniczenie zapobiega mapowaniu woluminów sieciowych i sterowników woluminów usługi Azure Files w wersjach starszych niż 1709.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>Wdrażanie aplikacji w lokalnym klastrze programistycznym
Wykonaj kroki 1-3 z [powyższego.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#manual-deployment-for-standalone-clusters)

 Domyślna liczba wystąpień usługi dla aplikacji wtyczki woluminu usługi Azure Files wynosi -1, co oznacza, że istnieje wystąpienie usługi wdrożonej w każdym węźle w klastrze. Jednak podczas wdrażania aplikacji wtyczki woluminu usługi Azure Files w lokalnym klastrze deweloperów liczba wystąpień usługi powinna być określona jako 1. Można to zrobić za pomocą parametru application **instancecount.** W związku z tym polecenie do tworzenia aplikacji wtyczki woluminu usługi Azure Files w lokalnym klastrze rozwoju jest:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Konfigurowanie aplikacji do używania woluminu
Poniższy fragment kodu pokazuje, jak wolumin oparty na plikach azure można określić w pliku manifestu aplikacji aplikacji. Szczególnym elementem zainteresowania jest tag **woluminu:**

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
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

Nazwa sterownika wtyczki woluminu usługi Azure Files **to sfazurefile**. Ta wartość jest ustawiona dla **driver** **atrybutu woluminu** tagu w manifeście aplikacji.

W tagu **woluminu** we wemie powyżej wtyczka woluminu usługi Azure Files wymaga następujących atrybutów:
- **Źródło** — jest to nazwa woluminu. Użytkownik może wybrać dowolną nazwę dla swojego woluminu.
- **Miejsce docelowe** — ten atrybut jest lokalizacją, na którą wolumin jest mapowany w uruchomionym kontenerze. W związku z tym miejsce docelowe nie może być lokalizacją, która już istnieje w kontenerze

Jak pokazano w **DriverOption** elementów we urywce powyżej, wtyczka woluminu usługi Azure Files obsługuje następujące opcje sterownika:
- **shareName** — nazwa udziału plików usługi Azure Files, który zapewnia wolumin dla kontenera.
- **storageAccountName** — nazwa konta magazynu platformy Azure, które zawiera udział plików usługi Azure Files.
- **storageAccountKey** — klucz dostępu dla konta magazynu platformy Azure, który zawiera udział plików usługi Azure Files.
- **storageAccountFQDN** — nazwa domeny skojarzona z kontem magazynu. Jeśli nazwa storageAccountFQDN nie jest określona, nazwa domeny zostanie utworzona przy użyciu domyślnego sufiksu(.file.core.windows.net) z nazwą magazynuAccountName.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>Korzystanie z własnego woluminu lub sterownika rejestrowania
Usługa Sieci szkieletowej umożliwia również użycie własnego [woluminu](https://docs.docker.com/engine/extend/plugins_volume/) niestandardowego lub [rejestrowania](https://docs.docker.com/engine/admin/logging/overview/) sterowników. Jeśli sterownik woluminu/rejestrowania platformy Docker nie jest zainstalowany w klastrze, można go zainstalować ręcznie przy użyciu protokołów RDP/SSH. Instalację z tymi protokołami można wykonać za pomocą [skryptu rozruchowego zestawu zestawów skalowania maszyny wirtualnej](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) lub [skryptu SetupEntryPoint](/azure/service-fabric/service-fabric-application-model).

Przykład skryptu do zainstalowania [sterownika woluminu platformy Docker dla platformy Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) jest następujący:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

W aplikacjach, aby użyć zainstalowanego sterownika woluminu lub rejestrowania, należy określić odpowiednie wartości w **volume** i **LogConfig** elementów w obszarze **ContainerHostPolicies** w manifeście aplikacji.

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

Podczas określania wtyczki woluminu sieć szkieletowa usług automatycznie tworzy wolumin przy użyciu określonych parametrów. **Tag Źródło** elementu **Wolumin** jest nazwą woluminu, a tag **Sterownik** określa wtyczkę sterownika woluminu. Tag **miejsce docelowe** jest lokalizacją, na którą **źródło** jest mapowane w uruchomionym kontenerze. W związku z tym miejsce docelowe nie może być lokalizacją, która już istnieje w kontenerze. Opcje można określić za pomocą tagu **DriverOption** w następujący sposób:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Parametry aplikacji są obsługiwane dla woluminów, jak pokazano w poprzednim `MyStorageVar` fragmentie manifestu (poszukaj przykładowego użycia).

Jeśli sterownik dziennika platformy Docker jest określony, należy wdrożyć agentów (lub kontenerów) do obsługi dzienników w klastrze. **Tag DriverOption** może służyć do określania opcji dla sterownika dziennika.

## <a name="next-steps"></a>Następne kroki
* Aby wyświetlić przykłady kontenerów, w tym sterownik woluminu, odwiedź [przykłady kontenerów sieci szkieletowej usług](https://github.com/Azure-Samples/service-fabric-containers)
* Aby wdrożyć kontenery w klastrze sieci szkieletowej usług, zapoznaj się z [artykułem Wdrażanie kontenera w sieci szkieletowej usług](service-fabric-deploy-container.md)
