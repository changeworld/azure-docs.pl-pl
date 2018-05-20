---
title: Usługa sieci szkieletowej Azure pliki sterowników woluminu (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Sieć szkieletowa usług obsługuje przy użyciu plików Azure do tworzenia kopii zapasowych woluminów z Twojej kontenera. Ta funkcja jest obecnie w wersji zapoznawczej.
services: service-fabric
documentationcenter: other
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/30/2018
ms.author: subramar
ms.openlocfilehash: 2d98cff1a5869091aa81097bbb34da6e525a2ad5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Usługa sieci szkieletowej Azure plików woluminu sterownika (wersja zapoznawcza)
Wtyczka woluminu pliki Azure jest [wtyczki woluminu Docker](https://docs.docker.com/engine/extend/plugins_volume/) zapewnia [plików Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) na podstawie woluminów kontenerów Docker. Ten dodatek plug-in woluminu Docker jest dostarczana jako aplikacji sieci szkieletowej usług, które można wdrożyć do klastrów sieci szkieletowej usług. Jego celem jest zapewnienie plików Azure opartej na woluminach dla innych aplikacji kontenera sieci szkieletowej usług, które są wdrażane w klastrze.

> [!NOTE]
> Wersja 6.255.389.9494 wtyczki woluminu pliki Azure jest wersji zapoznawczej, która jest dostępna z tego dokumentu. Wersja preview, jest **nie** obsługiwane do użytku w środowiskach produkcyjnych.
>

## <a name="prerequisites"></a>Wymagania wstępne
* Wersja wtyczki woluminu plików Azure działa na [systemu Windows Server w wersji 1709](https://docs.microsoft.com/en-us/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 w wersji 1709](https://docs.microsoft.com/en-us/windows/whats-new/whats-new-windows-10-version-1709) lub nowszymi systemami operacyjnymi. Wersji systemu Linux wtyczki woluminu plików Azure działa we wszystkich wersjach systemu operacyjnego, obsługiwany przez sieć szkieletowa usług.

* Postępuj zgodnie z instrukcjami [dokumentacji usługi pliki Azure](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-create-file-share) do utworzenia udziału plików dla aplikacji kontenera sieci szkieletowej usług do użycia jako wolumin.

* Konieczne będzie [programu Powershell z modułem platformy Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started) lub [SFCTL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cli) zainstalowane.

## <a name="deploy-the-service-fabric-azure-files-application"></a>Wdrażanie aplikacji usługi sieci szkieletowej Azure plików

Aplikacja usługi Service Fabric, która zapewnia woluminów dla kontenerów można pobrać z następujących [łącze](https://aka.ms/sfvolume). Aplikację można wdrożyć w klastrze za pomocą [PowerShell](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications), [CLI](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-lifecycle-sfctl) lub [interfejsów API klienta fabricclient z rolą](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. Korzystając z wiersza polecenia, zmień katalog na główny katalog pobrany pakiet aplikacji. 

    ```powershell 
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Skopiuj pakiet aplikacji do magazynu obrazów, uruchom polecenie poniżej z odpowiednią wartość dla [ApplicationPackagePath] i [element ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Rejestracja typu aplikacji

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Tworzenie aplikacji w poleceniu, aby utworzyć aplikację poniżej, należy pamiętać, **ListenPort** parametr aplikacji. Ta wartość określona dla parametru tej aplikacji jest port, na którym wtyczki woluminu plików Azure nasłuchuje żądań z demona Docker. Należy zapewnić portu dostarczony do aplikacji nie powoduje konfliktu z dowolnego portu używanego przez klaster lub aplikacji.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]

> Windows Server 2016 w centrum danych nie obsługuje instalacji SMB mapowania do kontenerów ([która jest tylko obsługiwana w systemie Windows Server w wersji 1709](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Tego ograniczenia zapobiega mapowania woluminu sieci i plików Azure woluminu sterowników w wersjach starszych niż 1709. 
>   

### <a name="deploy-the-application-on-a-local-development-cluster"></a>Wdrażanie aplikacji na lokalny klaster projektowy
Domyślną liczbę wystąpień usługi dla aplikacji wtyczki woluminu pliki Azure jest wartość -1, co oznacza, że jest wystąpieniem usługi wdrożone do każdego węzła w klastrze. Jednak w przypadku wdrażania aplikacji usługi pliki Azure woluminu wtyczki na lokalny klaster projektowy, liczba wystąpień usługi powinny być określone jako 1. Można to zrobić za pomocą **InstanceCount** parametr aplikacji. W związku z tym polecenie wdrożenia aplikacji Azure plików woluminu wtyczki na lokalny klaster projektowy jest:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>Skonfiguruj aplikacje korzystają z woluminu.
Poniższy fragment kodu przedstawia, jak woluminu na podstawie plików Azure można określić w manifeście aplikacji w aplikacji. Element określonych zainteresowań to **woluminu** tagu:

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
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
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

Nazwa sterownika dla wtyczki woluminu pliki Azure jest **sfazurefile**. Ta wartość jest ustawiana dla **sterownika** atrybutu **woluminu** elementu w manifeście aplikacji.

W **woluminu** element we fragmencie powyżej wtyczki woluminu plików Azure wymaga następujących tagów: 
- **Źródło** — odnosi się do folderu źródłowego, który może być folderem w maszynie Wirtualnej, która obsługuje kontenery lub trwałego magazynu zdalnego
- **Docelowy** — ten tag to lokalizacja, w który **źródła** jest mapowany w kontenerze uruchomione. W związku z tym folderu docelowego nie może być lokalizacji, która już istnieje w kontenerze sieci

Jak pokazano w **DriverOption** elementy we fragmencie powyżej wtyczki woluminu plików Azure obsługuje następujące opcje sterownika:

- **Nazwa udziału** — nazwa udziału plików Azure plików, który udostępnia wolumin kontenera
- **storageAccountName** — nazwa konta magazynu Azure, który zawiera plik plików Azure udziału
- **storageAccountKey** -klucza dostępu dla konta magazynu platformy Azure, zawierający pliki Azure udziału plików

Wymagane są wszystkie opcje sterownika powyżej. 

## <a name="using-your-own-volume-or-logging-driver"></a>Za pomocą własnych woluminu lub sterownik rejestrowania
Sieć szkieletowa usług umożliwia także użycie własnego niestandardowego woluminu lub sterowniki rejestrowania. Jeśli sterownik wolumin/rejestrowania Docker nie jest zainstalowany w klastrze, można zainstalować go ręcznie przy użyciu protokołu RDP/SSH. Można wykonać instalację z tych protokołów za pośrednictwem [zestawu skalowania maszyn wirtualnych uruchamiania skryptu](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) lub [skryptu SetupEntryPoint](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

Przykładowy skrypt do zainstalowania [sterownik woluminu Docker na platformie Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) wygląda następująco:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

W aplikacjach, woluminu lub rejestrowania sterownik został zainstalowany, możesz musi podaj odpowiednie wartości w **woluminu** i **LogConfig** elementy w obszarze  **ContainerHostPolicies** w manifeście aplikacji. 

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

## <a name="next-steps"></a>Kolejne kroki
* Aby wyświetlić przykłady kontenera, w tym sterownik woluminu odwiedź [przykłady kontenera sieci szkieletowej usług](https://github.com/Azure-Samples/service-fabric-containers)
* Do wdrażanie kontenerów do klastra usługi sieć szkieletowa, zobacz artykuł [wdrażanie kontenera w sieci szkieletowej usług](service-fabric-deploy-container.md)


