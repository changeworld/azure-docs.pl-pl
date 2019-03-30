---
title: Określanie punktów końcowych usługi Service Fabric | Dokumentacja firmy Microsoft
description: Jak do opisania punktu końcowego zasobów w manifeście usługi, w tym sposób konfigurowania punktów końcowych HTTPS
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 8707a9cb90afe1bf72f3aef6377f8ada409a1c64
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667127"
---
# <a name="specify-resources-in-a-service-manifest"></a>Określanie zasobów w manifeście usługi
## <a name="overview"></a>Przegląd
Manifest usługi umożliwia zasoby, które są używane przez usługę do można zadeklarować/zmienić bez konieczności zmieniania kodu skompilowanego. Usługa Azure Service Fabric obsługuje konfigurację zasobów punktu końcowego usługi. Dostęp do zasobów, które są określone w manifeście usługi mogą być kontrolowane za pośrednictwem grupy SecurityGroup w manifeście aplikacji. Deklaracja zasobów umożliwia następujące zasoby, aby zmienić w czasie wdrażania, co oznacza, że nie trzeba wprowadzić nowy mechanizm konfiguracji usługi. Definicja schematu dla pliku ServiceManifest.xml jest instalowany z programem Service Fabric SDK i narzędzi do *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Punkty końcowe
Gdy zasób punktu końcowego jest zdefiniowany w manifeście usługi, usługi Service Fabric przypisuje porty z zakresu portów zarezerwowanych aplikacji, jeśli port nie jest jawnie określona. Na przykład wyglądać w punkcie końcowym *ServiceEndpoint1* określony we fragmencie manifestu po tego akapitu. Ponadto usługi również poprosić o określonym porcie w zasobie. Repliki usługi uruchomione w węzłach innego klastra można przypisać różne numery portów, natomiast repliki usługi uruchomionej na tym samym węźle Udostępnianie portów. Repliki usługi można następnie użyć tych portów zgodnie z potrzebami, podczas replikacji i nasłuchuje żądań klienta.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Jeśli istnieje wiele pakietów kodu w pakiecie pojedynczą usługę, a następnie pakietu kodu również musi odwoływać się do **punktów końcowych** sekcji.  Na przykład jeśli **ServiceEndpoint2a** i **ServiceEndpoint2b** to punkty końcowe z tego samego pakietu usługi odwołuje się do pakietów inny kod pakietu kodu odpowiadający każdego punktu końcowego wyjaśniono, w następujący sposób:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Zapoznaj się [Konfigurowanie stanowych usług Reliable Services](service-fabric-reliable-services-configuration.md) można odczytać pliku więcej informacji na temat odwołujące się do punktów końcowych przy użyciu ustawień konfiguracji pakietu (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Przykład: określenie punktu końcowego HTTP usługi
Następujące manifest usługi definiuje jeden zasób punktu końcowego TCP i dwa zasoby punkt końcowy HTTP w &lt;zasobów&gt; elementu.

Punktów końcowych HTTP są wykonywane automatycznie listy ACL, czy przez usługę Service Fabric.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
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

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Przykład: określenie punktu końcowego HTTPS dla usługi
Protokół HTTPS zapewnia uwierzytelnianie serwera i służy także do szyfrowania komunikacji klient serwer. Aby włączyć protokół HTTPS w usłudze Service Fabric, należy określić protokół w *zasobów -> punkty końcowe -> punktu końcowego* części manifestu usługi, jak pokazano wcześniej dla punktu końcowego *ServiceEndpoint3*.

> [!NOTE]
> Nie można zmienić Protokół usługi podczas uaktualniania aplikacji. Jeśli zostanie on zmieniony podczas uaktualniania, jest istotną zmianę.
> 

> [!WARNING] 
> Przy użyciu protokołu HTTPS, nie należy używać tych samych portów i certyfikatu dla wystąpień różne usługi (niezależnie od aplikacji), które zostały wdrożone na tym samym węźle. Uaktualnianie dwóch różnych usług za pomocą tego samego portu w różne wystąpienia aplikacji spowoduje niepowodzenia uaktualnienia. Aby uzyskać więcej informacji, zobacz [uaktualniania wielu aplikacji za pomocą punktów końcowych HTTPS ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Oto przykład ApplicationManifest, które należy ustawić dla protokołu HTTPS. Musi być podany odcisk palca certyfikatu. EndpointRef jest odwołaniem do EndpointResource w pliku ServiceManifest, dla której jest ustawiony protokołu HTTPS. Możesz dodać więcej niż jeden EndpointCertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

W przypadku klastrów systemu Linux **MY** przechowywanie wartości domyślne w folderze **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Zastępowanie punktów końcowych w ServiceManifest.xml

W pliku ApplicationManifest Dodaj sekcji ResourceOverrides będzie równorzędny ConfigOverrides sekcji. W tej sekcji można określić zastąpienie sekcji punktów końcowych w sekcji zasobów określone w manifeście usługi. Przesłanianie punktów końcowych jest obsługiwane w środowisku uruchomieniowym 5.7.217/SDK 2.7.217 lub nowszy.

Aby zastąpić punktu końcowego w pliku ServiceManifest przy użyciu zmian ApplicationParameters ApplicationManifest następujący:

W sekcji ServiceManifestImport dodania nowej sekcji "ResourceOverrides".

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

Podczas wdrażania aplikacji można przekazać te wartości jako ApplicationParameters.  Na przykład:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Uwaga: Jeśli wartości są podane ApplicationParameters jest pusta, wrócimy do wartości domyślnej, przewidzianych w pliku ServiceManifest Nazwapunktukoncowego odpowiednie.

Na przykład:

Jeśli w pliku ServiceManifest wskazana

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

I Port1 i Protocol1 wartości parametrów aplikacji o wartości null ani być pusta. Numer portu jest nadal decyzji ServiceFabric. I protokołu tcp będzie.

Załóżmy, że określono nieprawidłową wartość. Podobnie jak dla portu określona wartość ciągu "Foo" zamiast int.  Nowe ServiceFabricApplication polecenie zakończy się niepowodzeniem z powodu błędu: Parametr przesłonięcie z atrybutem "ServiceEndpoint1" name "Port1" w sekcji "ResourceOverrides" jest nieprawidłowy. Określona wartość jest "Foo" i wymagane jest "int".
