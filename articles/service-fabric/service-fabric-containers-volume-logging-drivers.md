---
title: Usługa sieci szkieletowej Azure pliki sterowników woluminów (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Usługa Service Fabric obsługuje przy użyciu usługi Azure Files do tworzenia kopii zapasowych woluminów z kontenera. Ta funkcja jest obecnie dostępna w wersji zapoznawczej.
services: service-fabric
documentationcenter: other
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/10/2018
ms.author: aljo, subramar
ms.openlocfilehash: b8012cbdad02995c3fc98a3ea1fa02a3a08bd2dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870880"
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Sterownik woluminu plików platformy Azure dla usługi sieci szkieletowej (wersja zapoznawcza)
Dodatek woluminu plików platformy Azure jest [wtyczki woluminu Docker](https://docs.docker.com/engine/extend/plugins_volume/) zapewniający [usługi Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) na podstawie woluminów na potrzeby kontenerów platformy Docker. Ta wtyczka woluminu platformy Docker ma postać pakietu aplikacji usługi Service Fabric, który można wdrożyć w klastrach usługi Service Fabric. Ma ona na celu udostępnianie woluminów opartych na usłudze Azure Files w innych aplikacjach kontenerów usługi Service Fabric wdrożonych w klastrze.

> [!NOTE]
> Wersja 6.4.571.9590 wtyczki woluminu plików platformy Azure jest wersja zapoznawcza, która jest dostępna z tego dokumentu. Jako wersji zapoznawczej, jest **nie** przeznaczony do użytku w środowiskach produkcyjnych.
>

## <a name="prerequisites"></a>Wymagania wstępne
* Wtyczka woluminu plików platformy Azure w wersji Windows działa na [systemu Windows Server w wersji 1709](https://docs.microsoft.com/windows-server/get-started/whats-new-in-windows-server-1709), [systemu Windows 10 w wersji 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) lub nowszych systemach operacyjnych. Wersji systemu Linux wtyczki woluminu plików platformy Azure działa z wszystkimi wersjami systemów operacyjnych obsługiwanych przez usługę Service Fabric.

* Wtyczka usługi Azure Files woluminu działa tylko w usłudze Service Fabric w wersji 6.2 i nowszych.

* Postępuj zgodnie z instrukcjami w [dokumentacji usługi Azure Files](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) do utworzenia udziału plików dla aplikacji kontenera usługi Service Fabric do użycia jako wolumin.

* Konieczne będzie [środowiska Powershell z modułu Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) lub [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) zainstalowane.

* Jeśli używasz funkcji Hyper-v kontenery poniższe fragmenty kodu należy dodać w ClusterManifest (klaster lokalny) lub element fabricSettings sekcji szablonu ARM (klastrze platformy Azure) lub ClusterConfig.json (autonomiczny klaster). Konieczne będzie nazwa woluminu i numer portu, którego nasłuchuje woluminu w klastrze. 

W ClusterManifest następujące wymagania do dodania w sekcji hostingu. W tym przykładzie nazwa woluminu jest **sfazurefile** i port nasłuchuje na klastrze jest **19100**.  

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

W sekcji element fabricSettings szablonu ARM (w przypadku wdrożeń na platformie Azure) lub ClusterConfig.json (w przypadku autonomicznych wdrożeniach), należy dodać poniższy fragment kodu. 

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


## <a name="deploy-the-service-fabric-azure-files-application"></a>Wdrażanie aplikacji usługi Service Fabric usługi Azure Files

Aplikacja usługi Service Fabric, która zapewnia woluminów dla swoich kontenerów, można pobrać z następujących [łącze](https://download.microsoft.com/download/C/0/3/C0373AA9-DEFA-48CF-9EBE-994CA2A5FA2F/AzureFilesVolumePlugin.6.4.571.9590.zip). Aplikację można wdrożyć w klastrze za pomocą [PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications), [interfejsu wiersza polecenia](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle-sfctl) lub [interfejsy API FabricClient](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. Przy użyciu wiersza polecenia, zmień katalog na katalog główny pobrany pakiet aplikacji.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Skopiuj pakiet aplikacji do magazynu obrazów, uruchom poniższe polecenie przy użyciu odpowiedniej wartości dla [ApplicationPackagePath] i [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Zarejestrować typ aplikacji

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Tworzenie aplikacji w poleceniu do tworzenia aplikacji poniżej, należy pamiętać, **ListenPort** parametr aplikacji. Ta wartość określona dla parametru tej aplikacji jest port, na którym wtyczki usługi Azure Files woluminu nasłuchuje żądań z demona platformy Docker. Koniecznie upewnij się, że port pod warunkiem zgodności aplikacji VolumePluginPorts w ClusterManifest nie powoduje konfliktu z innego portu używanego przez klaster lub aplikacji.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.4.571.9590 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.4.571.9590 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Windows Server 2016 Datacenter nie obsługuje mapowania instaluje SMB do kontenerów ([, jest tylko obsługiwana w systemie Windows Server w wersji 1709](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). To ograniczenie zapobiega mapowania sieci na woluminie i sterowników woluminów plików platformy Azure w wersji starszej niż 1709.

### <a name="deploy-the-application-on-a-local-development-cluster"></a>Wdrażanie aplikacji na lokalny klaster projektowy
Domyślna liczba wystąpień usługi dla aplikacji usługi Azure Files wtyczki woluminu jest wartość -1, co oznacza, że to wystąpienie usługi, który został wdrożony w każdym węźle w klastrze. Jednak podczas wdrażania aplikacji usługi Azure Files woluminu wtyczki na lokalny klaster projektowy, liczba wystąpień usługi powinien być określony jako 1. Można to zrobić za pomocą **InstanceCount** parametr aplikacji. W związku z tym polecenia do wdrożenia aplikacji usługi Azure Files woluminu wtyczki na lokalny klaster projektowy jest:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.4.571.9590 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.4.571.9590 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>Konfigurowanie aplikacji w taki sposób, aby korzystać z woluminu
Poniższy fragment kodu pokazuje, jak usługi Azure Files na podstawie woluminu można określić w manifeście aplikacji w aplikacji. Element określonych zainteresowań jest **woluminu** tag:

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

Nazwa sterownika dla usługi Azure Files wtyczki woluminu jest **sfazurefile**. Ta wartość jest ustawiana dla **sterownika** atrybutu **woluminu** elementu w manifeście aplikacji.

W **woluminu** element we fragmencie kodu powyżej, wtyczka woluminu plików platformy Azure wymaga następujących znaczników:
- **Źródło** — jest to nazwa woluminu. Użytkownika można wybrać dowolną nazwę ich wielkości.
- **Miejsce docelowe** — ten tag jest to lokalizacja wolumin jest mapowany do uruchomionego kontenera. W efekcie lokalizacji docelowej nie może być lokalizacji, która już istnieje w kontenerze

Jak pokazano na **DriverOption** elementy we fragmencie kodu powyżej, wtyczka woluminu plików platformy Azure obsługuje następujące opcje sterownika:
- **Nazwa udziału** — nazwę udziału plików usługi Azure Files, zapewniająca woluminu dla kontenera.
- **storageAccountName** — nazwa konta magazynu platformy Azure, który zawiera plik usługi Azure Files udostępniania.
- **storageAccountKey** -klucz dostępu dla konta usługi Azure storage, który zawiera udział plików usługi Azure Files.
- **storageAccountFQDN** — nazwę domeny skojarzone z kontem magazynu. Jeśli storageAccountFQDN nie zostanie określony, nazwy domeny będzie sformatowany przy użyciu suffix(.file.core.windows.net) domyślne storageAccountName.  

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

## <a name="using-your-own-volume-or-logging-driver"></a>Korzystając z własnych woluminu lub rejestrowanie sterownika
Usługa Service Fabric umożliwia także użycie własnego niestandardowego [woluminu](https://docs.docker.com/engine/extend/plugins_volume/) lub [rejestrowania](https://docs.docker.com/engine/admin/logging/overview/) sterowników. Jeśli nie zainstalowano sterownika wolumin/rejestrowania platformy Docker w klastrze, można zainstalować go ręcznie przy użyciu protokołów RDP/SSH. Można przeprowadzić instalację za pomocą tych protokołów za pośrednictwem [skrypt uruchamiania zestawu skalowania maszyn wirtualnych](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) lub [skryptu SetupEntryPoint](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model).

Przykładowy skrypt w celu zainstalowania [sterownik woluminu platformy Docker na platformie Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) jest następująca:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

W aplikacjach, woluminu lub rejestrowanie sterownik został zainstalowany, trzeba określić odpowiednie wartości w **woluminu** i **LogConfig** elementy w obszarze  **ContainerHostPolicies** w manifeście aplikacji.

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

Podczas określania wtyczki woluminu, Usługa Service Fabric automatycznie tworzy woluminu przy użyciu określonych parametrów. **Źródła** tagu dla **woluminu** element jest nazwa woluminu i **sterownika** tag określa sterownik woluminu wtyczki. **Docelowego** tagu to lokalizacja, który **źródła** jest mapowany do uruchomionego kontenera. W związku z tym w lokalizacji docelowej nie może być lokalizacji, która już istnieje w kontenerze. Opcje można określić za pomocą **DriverOption** tagu w następujący sposób:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Parametry aplikacji są obsługiwane w przypadku woluminów, jak pokazano w poprzednim fragmencie manifestu (Wyszukaj `MyStorageVar` na przykład użyć).

Sterownik dziennik platformy Docker jest określony, należy wdrożyć agentów (lub kontenery) do obsługi dzienników w klastrze. **DriverOption** tagu może służyć do określania opcji dla sterownika dziennika.

## <a name="next-steps"></a>Kolejne kroki
* Aby wyświetlić przykłady kontenera, w tym sterownik woluminu, odwiedź [przykłady kontenera usługi Service Fabric](https://github.com/Azure-Samples/service-fabric-containers)
* Wdrażanie kontenerów do klastra usługi Service Fabric, zobacz artykuł [wdrażanie kontenera w usłudze Service Fabric](service-fabric-deploy-container.md)
