---
title: Odwołanie YAML dla grupy kontenerów
description: Odwołanie do pliku YAML obsługiwanego przez wystąpienia kontenera platformy Azure w celu skonfigurowania grupy kontenerów
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896568"
---
# <a name="yaml-reference-azure-container-instances"></a>Odwołanie DO YAML: Wystąpienia kontenera platformy Azure

Ten artykuł obejmuje składnię i właściwości pliku YAML obsługiwanego przez wystąpienia kontenerów platformy Azure w celu skonfigurowania [grupy kontenerów](container-instances-container-groups.md). Użyj pliku YAML, aby wprowadzić konfigurację grupy do polecenia [tworzenia kontenera az][az-container-create] w wierszu polecenia interfejsu wiersza polecenia platformy Azure. 

Plik YAML to wygodny sposób konfigurowania grupy kontenerów dla powtarzalnych wdrożeń. Jest zwięzłą alternatywą dla tworzenia lub aktualizowania grupy kontenerów przy użyciu [szablonu Usługi Resource Manager](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) lub zestawów SDK wystąpień kontenerów platformy Azure.

> [!NOTE]
> To odwołanie dotyczy plików YAML dla wersji `2018-10-01`interfejsu API REST wystąpień kontenerów platformy Azure .

## <a name="schema"></a>Schemat 

Schemat pliku YAML następuje, w tym komentarze, aby wyróżnić właściwości klucza. Aby uzyskać opis właściwości w tym schemacie, zobacz [Property values](#property-values) section.

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## <a name="property-values"></a>Wartości właściwości

W poniższych tabelach opisano wartości, które należy ustawić w schemacie.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Obiekt Microsoft.ContainerInstance/containerGroups

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa grupy kontenerów. |
|  apiVersion | enum | Tak | 2018-10-01 |
|  location | ciąg | Nie | Lokalizacja zasobu. |
|  tags | obiekt | Nie | Znaczniki zasobów. |
|  identity | obiekt | Nie | Tożsamość grupy kontenerów, jeśli jest skonfigurowana. - [Obiekt ContainerGroupIdentity](#ContainerGroupIdentity) |
|  properties | obiekt | Tak | [Obiekt ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Obiekt ContainerGroupIdentity

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  type | enum | Nie | Typ tożsamości używanej dla grupy kontenerów. Typ "SystemAssigned, UserAssigned" obejmuje zarówno niejawnie utworzoną tożsamość, jak i zestaw tożsamości przypisanych przez użytkownika. Typ "Brak" usunie wszystkie tożsamości z grupy kontenerów. - Przypisany do systemu, Przypisany przez użytkownika, Przypisany do systemu, Przypisany użytkownik, Brak |
|  użytkownikAssignedIdentities | obiekt | Nie | Lista tożsamości użytkowników skojarzonych z grupą kontenerów. Odwołania do klucza słownika tożsamości użytkownika będą identyfikatorami zasobów usługi Azure Resource Manager w postaci: "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}". |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Obiekt ContainerGroupProperties

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  containers | tablica | Tak | Kontenery w grupie kontenerów. - [Obiekt kontenera](#Container) |
|  imageRegistryCredentials | tablica | Nie | Poświadczenia rejestru obrazów, z których jest tworzona grupa kontenerów. - [ImageRegistryObszerwający obiekt](#ImageRegistryCredential) |
|  restartPolicy | enum | Nie | Zasady ponownego uruchamiania dla wszystkich kontenerów w grupie kontenerów. - `Always`Zawsze restart- `OnFailure` Restart `Never` po awarii- Nigdy nie uruchamiaj ponownie. - Zawsze, OnFailure, Nigdy |
|  Ipaddress | obiekt | Nie | Typ adresu IP grupy kontenerów. - [Obiekt IpAddress](#IpAddress) |
|  osType | enum | Tak | Typ systemu operacyjnego wymagany przez kontenery w grupie kontenerów. - Windows lub Linux |
|  volumes | tablica | Nie | Lista woluminów, które mogą być montowane przez kontenery w tej grupie kontenerów. - [Obiekt woluminu](#Volume) |
|  Diagnostyka | obiekt | Nie | Informacje diagnostyczne dla grupy kontenerów. - [Obiekt ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  siećProfile | obiekt | Nie | Informacje o profilu sieciowym dla grupy kontenerów. - [ContainerGroupNetworkProile obiekt](#ContainerGroupNetworkProfile) |
|  dnsConfig | obiekt | Nie | Informacje o konfiguracji DNS dla grupy kontenerów. - [Obiekt DnsConfiguration](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Obiekt kontenera

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa podane przez użytkownika wystąpienia kontenera. |
|  properties | obiekt | Tak | Właściwości wystąpienia kontenera. - [Obiekt ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>ImageRegistryObszerwający obiekt

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  serwer | ciąg | Tak | Serwer rejestru obrazów platformy Docker bez protokołu, takiego jak "http" i "https". |
|  nazwa użytkownika | ciąg | Tak | Nazwa użytkownika rejestru prywatnego. |
|  hasło | ciąg | Nie | Hasło do rejestru prywatnego. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>Obiekt IpAddress

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  ports | tablica | Tak | Lista portów ujawnionych w grupie kontenerów. - [Obiekt portu](#Port) |
|  type | enum | Tak | Określa, czy adres IP jest narażony na publiczny internet lub prywatną sieć wirtualną. - Publiczne lub prywatne |
|  IP | ciąg | Nie | Ip narażone na publiczny internet. |
|  dnsNameLabel | ciąg | Nie | Etykieta nazwy dns dla adresu IP. |


<a id="Volume" />

### <a name="volume-object"></a>Obiekt woluminu

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa woluminu. |
|  plik azureFile | obiekt | Nie | Wolumin pliku platformy Azure. - [Obiekt AzureFileVolume](#AzureFileVolume) |
|  emptyDir | obiekt | Nie | Pusty wolumin katalogu. |
|  wpis tajny | obiekt | Nie | Tajny tom. |
|  gitRepo | obiekt | Nie | Wolumin repozytorium git. - [GitRepoVolume obiekt](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Obiekt ContainerGroupDiagnostics

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  LogAnalytics | obiekt | Nie | Informacje o analizie dziennika grupy kontenerów. - [Obiekt LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProile obiekt

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  id | ciąg | Tak | Identyfikator profilu sieciowego. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Obiekt DnsConfiguration

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  Nameservers | tablica | Tak | Serwery DNS dla grupy kontenerów. - ciąg |
|  wyszukiwanieDomeny | ciąg | Nie | Domeny wyszukiwania DNS wyszukiwania nazwy hosta w grupie kontenerów. |
|  opcje | ciąg | Nie | Opcje DNS dla grupy kontenerów. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Obiekt ContainerProperties

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  image | ciąg | Tak | Nazwa obrazu użytego do utworzenia wystąpienia kontenera. |
|  command | tablica | Nie | Polecenia do wykonania w wystąpieniu kontenera w formie exec. - ciąg |
|  ports | tablica | Nie | Narażonych portów w wystąpieniu kontenera. - [Obiekt ContainerPort](#ContainerPort) |
|  środowiskoWarzylne | tablica | Nie | Zmienne środowiskowe do ustawienia w wystąpieniu kontenera. - [Obiekt zróżnym środowiskiem](#EnvironmentVariable) |
|  zasoby | obiekt | Tak | Wymagania dotyczące zasobów wystąpienia kontenera. - [Obiekt Wymagania zasobów](#ResourceRequirements) |
|  volumeMounts | tablica | Nie | Wolumin jest instalakowy dostępny dla wystąpienia kontenera. - [Obiekt VolumeMount](#VolumeMount) |
|  żywośćProbe | obiekt | Nie | Sonda żywotności. - [Obiekt ContainerProbe](#ContainerProbe) |
|  gotowośćProbe | obiekt | Nie | Sonda gotowości. - [Obiekt ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Obiekt portu

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  Protokół | enum | Nie | Protokół skojarzony z portem. - TCP lub UDP |
|  port | liczba całkowita | Tak | Numer portu. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Obiekt AzureFileVolume

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  Nazwaudziału | ciąg | Tak | Nazwa udziału pliku platformy Azure do zamontowania jako wolumin. |
|  Readonly | wartość logiczna | Nie | Flaga wskazująca, czy udostępniony plik azure zainstalowany jako wolumin jest tylko do odczytu. |
|  storageAccountName | ciąg | Tak | Nazwa konta magazynu, który zawiera udział pliku azure. |
|  storageAccountKey | ciąg | Nie | Klucz dostępu do konta magazynu używany do uzyskiwania dostępu do udziału pliku usługi Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume obiekt

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  katalog | ciąg | Nie | Nazwa katalogu docelowego. Nie może zawierać ani rozpoczynać się od '..'.  Jeśli podano '.' katalog woluminów będzie repozytorium git.  W przeciwnym razie, jeśli zostanie określony, wolumin będzie zawierał repozytorium git w podkatalogu o podanej nazwie. |
|  repozytorium | ciąg | Tak | Adres URL repozytorium |
|  revision | ciąg | Nie | Przyjmów skrót dla określonej wersji. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Obiekt LogAnalytics

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  workspaceId | ciąg | Tak | Identyfikator obszaru roboczego do analizy dzienników |
|  klawisz workspaceKey | ciąg | Tak | Klucz obszaru roboczego do analizy dzienników |
|  logType | enum | Nie | Typ dziennika, który ma być używany. - ContainerInsights lub ContainerInstanceLogs |
|  metadane | obiekt | Nie | Metadane do analizy dzienników. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Obiekt ContainerPort

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  Protokół | enum | Nie | Protokół skojarzony z portem. - TCP lub UDP |
|  port | liczba całkowita | Tak | Numer portu ujawniony w grupie kontenerów. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Obiekt zróżnym środowiskiem

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa zmiennej środowiskowej. |
|  value | ciąg | Nie | Wartość zmiennej środowiskowej. |
|  secureValue | ciąg | Nie | Wartość zmiennej bezpiecznego środowiska. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Obiekt Wymagania zasobów

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  Żądania | obiekt | Tak | Żądania zasobów tego wystąpienia kontenera. - [Obiekt ResourceRequests](#ResourceRequests) |
|  Limity | obiekt | Nie | Limity zasobów tego wystąpienia kontenera. - [Obiekt ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Obiekt VolumeMount

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa montażu woluminu. |
|  mountPath (MountPath) | ciąg | Tak | Ścieżka w kontenerze, w którym należy zamontować objętość. Nie może zawierać dwukropka (:). |
|  Readonly | wartość logiczna | Nie | Flaga wskazująca, czy mocowanie woluminu jest tylko do odczytu. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Obiekt ContainerProbe

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  Exec | obiekt | Nie | Polecenie wykonywania sondowania - [ContainerExec obiektu](#ContainerExec) |
|  httpGet | obiekt | Nie | Ustawienia http Pobierz do sondowania — [KontenerHttpGet obiektu](#ContainerHttpGet) |
|  initialDelaySekundy | liczba całkowita | Nie | Początkowe sekundy opóźnienia. |
|  okresSekund | liczba całkowita | Nie | Okres sekund. |
|  awariaThreshold | liczba całkowita | Nie | Próg awarii. |
|  sukcesThreshold | liczba całkowita | Nie | Próg sukcesu. |
|  limit czasuSekundy | liczba całkowita | Nie | Sekundy limitu czasu. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Obiekt ResourceRequests

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  pamięćWGB | numer | Tak | Żądanie pamięci w GB tego wystąpienia kontenera. |
|  Procesora | numer | Tak | Żądanie procesora CPU tego wystąpienia kontenera. |
|  Gpu | obiekt | Nie | Żądanie gpu tego wystąpienia kontenera. - [Obiekt GpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Obiekt ResourceLimits

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  pamięćWGB | numer | Nie | Limit pamięci w GB tego wystąpienia kontenera. |
|  Procesora | numer | Nie | Limit procesora CPU tego wystąpienia kontenera. |
|  Gpu | obiekt | Nie | Limit procesora GPU tego wystąpienia kontenera. - [Obiekt GpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Obiekt ContainerExec

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  command | tablica | Nie | Polecenia do wykonania w kontenerze. - ciąg |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>KontenerHttpGet obiektu

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  ścieżka | ciąg | Nie | Ścieżka do sondy. |
|  port | liczba całkowita | Tak | Numer portu do sondowania. |
|  schemat | enum | Nie | Schemat. - http lub https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Obiekt GpuResource

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  count | liczba całkowita | Tak | Liczba zasobów GPU. |
|  sku | enum | Tak | Jednostka SKU zasobu gpu. - K80, P100, V100 |


## <a name="next-steps"></a>Następne kroki

Zobacz samouczek [Wdrażanie grupy wielu kontenerów przy użyciu pliku YAML](container-instances-multi-container-yaml.md).

Zobacz przykłady używania pliku YAML do wdrażania grup kontenerów w [sieci wirtualnej](container-instances-vnet.md) lub [instalowania woluminu zewnętrznego.](container-instances-volume-azure-files.md)

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

