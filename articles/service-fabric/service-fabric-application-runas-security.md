---
title: Uruchamianie usługi sieć szkieletowa usług Azure w obszarze kont zabezpieczeń lokalnych i system | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do uruchamiania aplikacji usługi Service Fabric w systemie i kont zabezpieczeń lokalnych.  Tworzenie podmiotów zabezpieczeń i stosowanie zasad Uruchom jako, aby bezpiecznie uruchomić usługi.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: 33ca23834f35e631c6943ec22a88f4fe3dc853e1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212402"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Uruchamianie usługi jako konto użytkownika lokalnego lub lokalnego konta systemowego
Za pomocą usługi Azure Service Fabric, można zabezpieczyć aplikacji uruchomionych w klastrze, w obszarze konta innego użytkownika. Domyślnie aplikacje sieci szkieletowej usług są uruchamiane na koncie, działającą proces Fabric.exe. Usługi sieć szkieletowa dostępne są także możliwość uruchamiania aplikacji przy użyciu lokalnego konta użytkownika lub systemu. Obsługiwany system lokalny typy kont to **LocalUser**, **NetworkService**, **Usługa lokalna**, i **LocalSystem**.  Jeśli korzystasz z usługi Service Fabric w klastrze autonomicznego Windows, można uruchomić usługi w ramach [kont domen usługi Active Directory](service-fabric-run-service-as-ad-user-or-group.md) lub [konta usług zarządzane przez grupę](service-fabric-run-service-as-gmsa.md).

W manifeście aplikacji, należy zdefiniować konta użytkowników wymagane do uruchamiania usług lub bezpiecznych zasobów w **podmiotów** sekcji. Można również zdefiniować i Utwórz grupy użytkowników, aby co najmniej jednego użytkownika, które mogą być zarządzane ze sobą. Jest to przydatne, gdy jest wielu użytkowników do innej usługi, punkty wejścia i muszą typowych uprawnień, które są dostępne na poziomie grupy.  Następnie odwołuje się użytkowników w zasadach RunAs, która jest stosowana do określonej usługi lub wszystkich usług w aplikacji. 

Domyślnie zasady RunAs są stosowane do główny punkt wejścia.  Może także zastosować zasady RunAs do punktu wejścia instalacji, jeśli zachodzi konieczność [uruchamianie niektórych operacji ustawienia wysokiego poziomu przy użyciu konta system](service-fabric-run-script-at-service-startup.md), lub obie main i skonfigurować punkty wejścia.  

> [!NOTE] 
> Jeśli stosowanie zasad RunAs do usługi i manifestu usługi deklaruje punktu końcowego zasobów przy użyciu protokołu HTTP, należy określić **SecurityAccessPolicy**.  Aby uzyskać więcej informacji, zobacz [przypisać zasady zabezpieczeń dostępu dla punktów końcowych HTTP i HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Uruchamianie usługi jako użytkownik lokalny
Można utworzyć użytkownika lokalnego, który może służyć do zabezpieczania usług w aplikacji. Gdy **LocalUser** typ konta jest określone w sekcji podmiotów manifestu aplikacji usługi sieć szkieletowa usług tworzy lokalne konta użytkowników na komputerach, na którym aplikacja jest wdrażana. Domyślnie te konta nie mają tej samej nazwy jak określone w manifeście aplikacji (na przykład *Customer3* w poniższym przykładzie manifestu aplikacji). Zamiast tego są generowane dynamicznie i losowego hasła.

W **RunAsPolicy** sekcji **ServiceManifestImport**, określ konto użytkownika z **podmiotów** , aby uruchomić pakiet kodu usługi.  Poniższy przykład przedstawia tworzenie użytkowników lokalnych i stosowanie zasad RunAs do główny punkt wejścia:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>Utwórz lokalną grupę użytkowników
Można utworzyć grupy użytkowników i dodać co najmniej jednego użytkownika do grupy. Jest to przydatne, jeśli jest wielu użytkowników do innej usługi, punkty wejścia i muszą mieć niektórych typowych uprawnień, które są dostępne na poziomie grupy. W poniższym przykładzie manifest aplikacji zawiera grupy lokalnej o nazwie *LocalAdminGroup* z uprawnieniami administratora. Dwóch użytkowników *Customer1* i *Customer2*, zostają członkami tej grupy lokalnej. W **ServiceManifestImport** sekcji RunAs, zasady są stosowane do uruchomienia *Stateful1Pkg* pakietu kodu jako *Customer2*.  Inny RunAs zasady są stosowane do uruchomienia *Web1Pkg* pakietu kodu jako *Customer1*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Zastosuj zasady domyślne na wszystkie pakiety kodu usługi
Możesz użyć **DefaultRunAsPolicy** sekcji, aby określić konto użytkownika domyślna dla całego kodu pakietami, które nie mają określonego **RunAsPolicy** zdefiniowane. Jeśli większość pakietów kodu, które są określone w manifeście usługi używany przez aplikację muszą działać w ramach tego samego użytkownika, aplikacja właśnie można zdefiniować zasady RunAs domyślne przy użyciu tego konta użytkownika. Poniższy przykład określa, że jeśli pakiet kodu nie ma **RunAsPolicy** określony pakiet kodu należy uruchamiać w ramach **MyDefaultAccount** użytkownika określonego w sekcji podmiotów zabezpieczeń.  Typy kont obsługiwane są LocalUser, Usługa sieciowa, system lokalny i Usługa lokalna.  Jeśli używany jest użytkownika lokalnego lub usługi, również określić nazwę konta i hasło.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Pakiet kodu lokalnie za pomocą konsoli debugowania
Czasami jest przydatna do celów debugowania wyświetlić dane wyjściowe konsoli z uruchomioną usługę. Można ustawić zasad przekierowania konsoli dla punktu wejścia w manifeście usługi, który zapisuje dane wyjściowe do pliku. Plik wyjściowy jest zapisywany w folderze aplikacji o nazwie **dziennika** w węźle klastra, w którym aplikacja jest wdrożona i uruchom. 

> [!WARNING]
> Nigdy nie używaj zasad przekierowania konsoli w aplikacji, które zostało wdrożone w środowisku produkcyjnym, ponieważ może to wpłynąć na pracę awaryjną aplikacji. *Tylko* użyć tej funkcji dla rozwoju lokalnych i debugowania.  
> 
> 

Następująca usługa manifestu przykładzie Włączanie przekierowywania konsoli z wartością FileRetentionCount:

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
* [Zrozumienie model aplikacji](service-fabric-application-model.md)
* [Określ zasoby w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
