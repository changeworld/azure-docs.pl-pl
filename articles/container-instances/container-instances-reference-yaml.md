---
title: Azure Container Instances odwołanie YAML
description: Odwołanie do pliku YAML obsługiwanego przez Azure Container Instances w celu skonfigurowania grupy kontenerów
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 08/12/2019
ms.author: danlep
ms.openlocfilehash: 2e6be18371cf3ff96d1ce91d4dde26ff1f14021b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179906"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML: Azure Container Instances

W tym artykule opisano składnię i właściwości pliku YAML obsługiwanego przez Azure Container Instances w celu skonfigurowania [grupy kontenerów](container-instances-container-groups.md). Użyj pliku YAML, aby wprowadzić konfigurację grupy do polecenia [AZ Container Create][az-container-create] w interfejsie CLI platformy Azure. 

Plik YAML jest wygodnym sposobem konfigurowania grupy kontenerów dla powtarzalnych wdrożeń. Jest to zwięzła alternatywa dla używania [szablonu Menedżer zasobów](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) lub zestawów SDK Azure Container Instances do tworzenia lub aktualizowania grupy kontenerów.

> [!NOTE]
> To odwołanie dotyczy plików YAML w wersji `2018-10-01`interfejsu API REST Azure Container Instances.

## <a name="schema"></a>Schemat 

Schemat pliku YAML jest następujący, w tym komentarze do wyróżniania właściwości klucza. Aby uzyskać opis właściwości w tym schemacie, zapoznaj się z sekcją [wartości właściwości](#property-values) .

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
      ports: # Exposed ports on the instance
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

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Obiekt Microsoft. ContainerInstance/containerGroups

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa grupy kontenerów. |
|  apiVersion | Wyliczenia | Tak | 2018-10-01 |
|  location | ciąg | Nie | Lokalizacja zasobu. |
|  tags | object | Nie | Tagi zasobów. |
|  identity | object | Nie | Tożsamość grupy kontenerów, jeśli została skonfigurowana. - [Obiekt ContainerGroupIdentity](#ContainerGroupIdentity) |
|  properties | object | Tak | [Obiekt ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Obiekt ContainerGroupIdentity

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  type | Wyliczenia | Nie | Typ tożsamości używany dla grupy kontenerów. Typ "SystemAssigned, UserAssigned" obejmuje zarówno niejawnie utworzoną tożsamość, jak i zestaw tożsamości przypisanych do użytkownika. Typ "none" usunie wszystkie tożsamości z grupy kontenerów. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, brak |
|  Resourceidentity | object | Nie | Lista tożsamości użytkowników skojarzonych z grupą kontenerów. Odwołania do klucza słownika tożsamości użytkownika będą Azure Resource Manager identyfikatorów zasobów w postaci: "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName }'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Obiekt ContainerGroupProperties

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  containers | array | Tak | Kontenery w obrębie grupy kontenerów. - [Obiekt kontenera](#Container) |
|  imageRegistryCredentials | array | Nie | Poświadczenia rejestru obrazu, na podstawie których zostanie utworzona grupa kontenerów. - [Obiekt ImageRegistryCredential](#ImageRegistryCredential) |
|  restartPolicy | Wyliczenia | Nie | Zasady ponownego uruchamiania dla wszystkich kontenerów w grupie kontenerów. - `Always`Zawsze uruchamiaj `OnFailure` ponownie po `Never` niepowodzeniu — nigdy nie uruchamiaj ponownie. -Zawsze, OnFailure, nigdy |
|  Adresu | object | Nie | Typ adresu IP grupy kontenerów. - [IpAddress — obiekt](#IpAddress) |
|  osType | Wyliczenia | Tak | Typ systemu operacyjnego wymagany przez kontenery w grupie kontenerów. — Windows lub Linux |
|  woluminy | array | Nie | Lista woluminów, które mogą być instalowane przez kontenery w tej grupie kontenerów. - [Obiekt woluminu](#Volume) |
|  diagnostyka | object | Nie | Informacje diagnostyczne dla grupy kontenerów. - [Obiekt ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  networkProfile | object | Nie | Informacje o profilu sieciowym dla grupy kontenerów. - [Obiekt ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  Powodzenie | object | Nie | Informacje o konfiguracji DNS dla grupy kontenerów. - [Obiekt DnsConfiguration](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Obiekt kontenera

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa podana przez użytkownika wystąpienia kontenera. |
|  properties | object | Tak | Właściwości wystąpienia kontenera. - [Obiekt ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Obiekt ImageRegistryCredential

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  server | ciąg | Tak | Serwer rejestru platformy Docker bez protokołu, takiego jak "http" i "https". |
|  username | ciąg | Tak | Nazwa użytkownika dla rejestru prywatnego. |
|  password | ciąg | Nie | Hasło do rejestru prywatnego. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress — obiekt

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  ports | array | Tak | Lista portów uwidocznionych w grupie kontenerów. - [Obiekt portu](#Port) |
|  type | Wyliczenia | Tak | Określa, czy adres IP jest narażony na publiczny Internet lub prywatną sieć wirtualną. — Publiczne lub prywatne |
|  adres ip | ciąg | Nie | Adres IP uwidoczniony dla publicznego Internetu. |
|  dnsNameLabel | ciąg | Nie | Etykieta nazwy DNS dla adresu IP. |


<a id="Volume" />

### <a name="volume-object"></a>Obiekt woluminu

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa woluminu. |
|  AzureFile | object | Nie | Wolumin plików platformy Azure. - [Obiekt AzureFileVolume](#AzureFileVolume) |
|  emptyDir | object | Nie | Pusty wolumin katalogu. |
|  secret | object | Nie | Wolumin tajny. |
|  gitRepo | object | Nie | Wolumin repozytorium git. - [Obiekt GitRepoVolume](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Obiekt ContainerGroupDiagnostics

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | Nie | Informacje o usłudze log Analytics grupy kontenerów. - [Obiekt LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Obiekt ContainerGroupNetworkProfile

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  id | ciąg | Tak | Identyfikator profilu sieciowego. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Obiekt DnsConfiguration

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  Serwery nazw | array | Tak | Serwery DNS dla grupy kontenerów. -String |
|  searchDomains | ciąg | Nie | Domeny wyszukiwania DNS dla wyszukiwania nazw hostów w grupie kontenerów. |
|  options | ciąg | Nie | Opcje usługi DNS dla grupy kontenerów. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Obiekt ContainerProperties

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  image | ciąg | Tak | Nazwa obrazu użytego do utworzenia wystąpienia kontenera. |
|  Polecenie | array | Nie | Polecenia do wykonania w ramach wystąpienia kontenera w formularzu exec. -String |
|  ports | array | Nie | Uwidocznione porty w wystąpieniu kontenera. - [Obiekt ContainerPort](#ContainerPort) |
|  environmentVariables | array | Nie | Zmienne środowiskowe do ustawienia w wystąpieniu kontenera. - [Obiekt zmiennych środowiskowych](#EnvironmentVariable) |
|  — zasoby | object | Tak | Wymagania dotyczące zasobów wystąpienia kontenera. - [Obiekt ResourceRequirements](#ResourceRequirements) |
|  volumeMounts | array | Nie | Zainstaluj woluminy dostępne dla wystąpienia kontenera. - [Obiekt VolumeMount](#VolumeMount) |
|  livenessProbe | object | Nie | Sonda na żywo. - [Obiekt ContainerProbe](#ContainerProbe) |
|  readinessProbe | object | Nie | Sonda gotowości. - [Obiekt ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Obiekt portu

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | Wyliczenia | Nie | Protokół skojarzony z portem. -TCP lub UDP |
|  port | integer | Tak | Numer portu. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Obiekt AzureFileVolume

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  shareName | ciąg | Tak | Nazwa udziału plików platformy Azure, który ma zostać zainstalowany jako wolumin. |
|  Trybie | boolean | Nie | Flaga wskazująca, czy plik platformy Azure udostępniony jako wolumin jest tylko do odczytu. |
|  storageAccountName | ciąg | Tak | Nazwa konta magazynu zawierającego udział plików platformy Azure. |
|  storageAccountKey | ciąg | Nie | Klucz dostępu konta magazynu używany do uzyskiwania dostępu do udziału plików platformy Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Obiekt GitRepoVolume

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  katalog | ciąg | Nie | Nazwa katalogu docelowego. Nie może zawierać ani zaczynać się od "..".  Jeśli zostanie podana wartość ".", katalog woluminu będzie repozytorium git.  W przeciwnym razie wolumin będzie zawierać repozytorium Git w podkatalogu o podanej nazwie. |
|  repository | ciąg | Tak | Adres URL repozytorium |
|  skorygowan | ciąg | Nie | Skrót zatwierdzenia dla określonej poprawki. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Obiekt LogAnalytics

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  workspaceId | ciąg | Tak | Identyfikator obszaru roboczego dla usługi log Analytics |
|  workspaceKey | ciąg | Tak | Klucz obszaru roboczego dla usługi log Analytics |
|  logType | Wyliczenia | Nie | Typ dziennika, który ma być używany. -ContainerInsights lub ContainerInstanceLogs |
|  metadane | object | Nie | Metadane usługi log Analytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Obiekt ContainerPort

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | Wyliczenia | Nie | Protokół skojarzony z portem. -TCP lub UDP |
|  port | integer | Tak | Numer portu uwidoczniony w obrębie grupy kontenerów. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Obiekt zmiennych środowiskowych

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa zmiennej środowiskowej. |
|  value | ciąg | Nie | Wartość zmiennej środowiskowej. |
|  secureValue | ciąg | Nie | Wartość zmiennej środowiskowej Secure. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Obiekt ResourceRequirements

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  Żądania | object | Tak | Żądania zasobów tego wystąpienia kontenera. - [Obiekt ResourceRequests](#ResourceRequests) |
|  Ograniczeń | object | Nie | Limity zasobów tego wystąpienia kontenera. - [Obiekt ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Obiekt VolumeMount

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa instalacji woluminu. |
|  mountPath | ciąg | Tak | Ścieżka wewnątrz kontenera, w którym należy zainstalować wolumin. Nie może zawierać dwukropka (:). |
|  Trybie | boolean | Nie | Flaga wskazująca, czy instalacja woluminu jest tylko do odczytu. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Obiekt ContainerProbe

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  szef | object | Nie | Wykonywanie polecenia do sondowania — [obiekt ContainerExec](#ContainerExec) |
|  Narzędzia HttpGet | object | Nie | Ustawienia http get do Probe- [ContainerHttpGet Object](#ContainerHttpGet) |
|  initialDelaySeconds | integer | Nie | Początkowe opóźnienie w sekundach. |
|  periodSeconds | integer | Nie | Okres w sekundach. |
|  failureThreshold | integer | Nie | Próg błędu. |
|  successThreshold | integer | Nie | Próg sukcesu. |
|  timeoutSeconds | integer | Nie | Limit czasu w sekundach. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Obiekt ResourceRequests

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | Tak | Żądanie pamięci w GB tego wystąpienia kontenera. |
|  procesor | number | Tak | Żądanie procesora CPU tego wystąpienia kontenera. |
|  gpu | object | Nie | Żądanie procesora GPU tego wystąpienia kontenera. - [Obiekt GpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Obiekt ResourceLimits

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | Nie | Limit pamięci w GB tego wystąpienia kontenera. |
|  procesor | number | Nie | Limit procesorów dla tego wystąpienia kontenera. |
|  gpu | object | Nie | Limit procesora GPU dla tego wystąpienia kontenera. - [Obiekt GpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Obiekt ContainerExec

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  Polecenie | array | Nie | Polecenia do wykonania w kontenerze. -String |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Obiekt ContainerHttpGet

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  path | ciąg | Nie | Ścieżka do sondy. |
|  port | integer | Tak | Numer portu do sondowania. |
|  schemat | Wyliczenia | Nie | Schemat. -http lub https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Obiekt GpuResource

|  Name | Type | Wymagane | Value |
|  ---- | ---- | ---- | ---- |
|  count | integer | Tak | Liczba zasobów procesora GPU. |
|  sku | Wyliczenia | Tak | Jednostka SKU zasobu GPU. -K80, P100, V100 |


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkiem [wdrażanie grupy wielokontenerowej przy użyciu pliku YAML](container-instances-multi-container-yaml.md).

Zobacz przykłady użycia pliku YAML do wdrażania grup kontenerów w [sieci wirtualnej](container-instances-vnet.md) lub [instalowania woluminu zewnętrznego](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

