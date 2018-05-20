---
title: Określanie punktów końcowych usługi sieć szkieletowa usług | Dokumentacja firmy Microsoft
description: Jak opisano zasobów punktu końcowego w manifestu usługi, oraz o sposobie konfigurowania punktów końcowych HTTPS
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: ce2bc8cc8d9b149b16aee9c5e601d9872621e277
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="specify-resources-in-a-service-manifest"></a>Określanie zasobów w manifeście usługi
## <a name="overview"></a>Przegląd
Manifest usługi umożliwia zasobów, które są używane przez usługę być zadeklarowany jako zmienione bez zmiany kodu skompilowanego. Sieć szkieletowa usług Azure obsługuje konfigurację punktu końcowego zasobów dla usługi. Dostęp do zasobów, które są określone w manifeście usługi można sterować za pośrednictwem SecurityGroup w manifeście aplikacji. Deklaracja zasobów umożliwia można zmienić w czasie wdrażania, co oznacza, że usługa, nie trzeba wprowadzić nowy mechanizm konfiguracji tych zasobów. Definicja schematu dla pliku ServiceManifest.xml jest instalowany z zestawu SDK sieci szkieletowej usług i narzędzi do *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Punkty końcowe
Zdefiniowane w manifeście usługi zasobu punktu końcowego sieci szkieletowej usług przypisuje porty z zakresu portów zastrzeżone aplikacji Jeśli port nie jest jawnie określony. Na przykład wyglądać w punkcie końcowym *ServiceEndpoint1* określony we fragmencie manifestu po tym akapitu. Ponadto usługi także mogą żądać w zasobie określonego portu. Repliki usługi uruchomione w węzłach klastra różnych można przypisać różne numery portów, gdy repliki usługi uruchomionej na tym samym węźle Udostępnianie portów. Repliki usługi można następnie używać tych portów zgodnie z potrzebami replikacji i nasłuchuje żądań klientów.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Jeśli istnieje wiele pakietów kodu w pakiecie pojedynczą usługę, a następnie pakietu kodu również musi odwoływać się do **punkty końcowe** sekcji.  Na przykład jeśli **ServiceEndpoint2a** i **ServiceEndpoint2b** jest punktów końcowych z tego samego pakietu usługi odwołuje się do pakietów inny kod pakietu kodu odpowiadający każdego punktu końcowego wyjaśniono, w następujący sposób:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Zapoznaj się [Konfigurowanie stanowych usług niezawodnej](service-fabric-reliable-services-configuration.md) aby przeczytać więcej informacji na temat odwołujące się do punktów końcowych z ustawień pakietu konfiguracji pliku (pliku settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Przykład: określenie punktu końcowego HTTP dla usługi
Definiuje następujące manifestu usługi, jeden zasób punktu końcowego TCP i dwa zasoby punkt końcowy HTTP w &lt;zasobów&gt; elementu.

Punkty końcowe HTTP są automatycznie ACL czy przez sieć szkieletowa usług.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Przykład: Określanie punkt końcowy HTTPS dla usługi
Protokół HTTPS zapewnia uwierzytelnianie na serwerze i służy także do szyfrowania komunikacji klient serwer. Aby włączyć protokół HTTPS w usłudze Service Fabric, należy określić protokół w *zasoby -> punkty końcowe -> punktu końcowego* sekcji manifestu usługi, jak pokazano wcześniej dla punktu końcowego *ServiceEndpoint3*.

> [!NOTE]
> Nie można zmienić protokołu usługi podczas uaktualniania aplikacji. Jeśli zostanie on zmieniony podczas uaktualniania, jest istotne zmiany.
> 
> 

Oto przykład ApplicationManifest które należy ustawić dla protokołu HTTPS. Należy podać odcisk palca dla certyfikatu. EndpointRef jest odwołaniem do EndpointResource w ServiceManifest, dla którego ustawieniu protokołu HTTPS. Można dodać więcej niż jeden EndpointCertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

W przypadku klastrów systemu Linux **MY** wartości domyślne w folderze przechowywania **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Zastępowanie punktów końcowych w pliku ServiceManifest.xml

W ApplicationManifest Dodaj sekcję ResourceOverrides, co będzie równorzędny ConfigOverrides sekcji. W tej sekcji można określić zastąpienia dla sekcji punktów końcowych w sekcji zasobów określona w manifeście usługi. Przesłanianie punktów końcowych jest obsługiwane w środowisku uruchomieniowym 5.7.217/SDK 2.7.217 i wyższych.

Aby zastąpić punktu końcowego w ServiceManifest przy użyciu ApplicationParameters Zmień ApplicationManifest następujący:

W sekcji ServiceManifestImport dodania nowej sekcji "ResourceOverrides"

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

W parametrach dodać poniżej:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Podczas wdrażania aplikacji teraz można przekazać te wartości jako ApplicationParameters na przykład:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Uwaga: Jeśli wartości umożliwiają ApplicationParameters jest pusty możemy wróć do określonych w ServiceManifest odpowiedniego EndPointName wartość domyślna.

Na przykład:

Jeśli w ServiceManifest określona

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

I Port1 i Protocol1 wartości dla parametrów aplikacji ma wartość null lub pusty. Port jest nadal decyzji ServiceFabric. I będzie protokołu tcp.

Załóżmy, że określasz niepoprawną wartość. Podobnie jak Port podano wartość ciągu "Foo" zamiast int.  Nowe ServiceFabricApplication polecenia zakończy się niepowodzeniem z powodu błędu: parametr zastąpienie z atrybutem "ServiceEndpoint1" name "Port1" w sekcji "ResourceOverrides" jest nieprawidłowy. Określona wartość jest "Foo" i wymagane jest "int".
