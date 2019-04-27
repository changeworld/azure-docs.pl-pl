---
title: Uruchom usługę Azure Service Fabric w ramach kont zabezpieczeń lokalnych i systemu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uruchomić aplikację usługi Service Fabric w ramach kont zabezpieczeń lokalnych i systemu.  Utwórz podmiotów zabezpieczeń i Zastosuj zasady Uruchom jako do bezpiecznego uruchamiania usługi.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: aljo
ms.openlocfilehash: 28cd1162d7cae2b3a16062bdf18a2971e1f05aad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621177"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Uruchamianie usługi jako konto użytkownika lokalnego lub konta system lokalny
Za pomocą usługi Azure Service Fabric, można zabezpieczyć aplikacje, które są uruchomione w klastrze, w ramach różnych kont użytkowników. Domyślnie aplikacje usługi Service Fabric uruchamiana na koncie, zgodną z procesu Fabric.exe. Usługa Service Fabric udostępnia także możliwości uruchamiania aplikacji przy użyciu lokalnego konta użytkownika lub systemu. Typy kont obsługiwany system lokalny to **LocalUser**, **NetworkService**, **LocalService**, i **LocalSystem**.  Jeśli korzystasz z usługi Service Fabric w klastrze autonomicznego Windows, można uruchomić usługi w ramach [kont domeny usługi Active Directory](service-fabric-run-service-as-ad-user-or-group.md) lub [konta usług zarządzane przez grupę](service-fabric-run-service-as-gmsa.md).

W manifeście aplikacji należy zdefiniować konta użytkowników wymagane do uruchamiania usług lub bezpiecznych zasobów w **podmiotów** sekcji. Można również zdefiniować i utworzyć grupy użytkowników, tak aby co najmniej jeden użytkownik może zarządzać razem. Jest to przydatne, gdy jest wielu użytkowników do innej usługi, punkty wejścia i potrzebują typowych uprawnień, które są dostępne na poziomie grupy.  Użytkownicy są następnie odwoływać w zasadach RunAs, która jest stosowana do określonej usługi lub wszystkich usług w aplikacji. 

Domyślnie zasady RunAs są stosowane do główny punkt wejścia.  Ją również zastosować zasady RunAs do punktu wejścia instalacji, jeśli musisz [uruchamiania niektórych operacji ustawienia wysokiego poziomu przy użyciu konta system](service-fabric-run-script-at-service-startup.md), lub obu main i skonfigurować punkty wejścia.  

> [!NOTE] 
> Jeśli zastosujesz zasady RunAs do usługi i manifestu usługi deklaruje zasobów punktu końcowego przy użyciu protokołu HTTP, należy określić **SecurityAccessPolicy**.  Aby uzyskać więcej informacji, zobacz [przypisywanie zasad dostępu zabezpieczeń dla punktów końcowych HTTP i HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Uruchamianie usługi za użytkownika lokalnego
Można utworzyć użytkownika lokalnego, który może służyć do zabezpieczania usług w ramach aplikacji. Gdy **LocalUser** typ konta jest określone w sekcji jednostki manifestu aplikacji usługi Service Fabric tworzy konta użytkownika lokalnego na komputerach, na którym aplikacja jest wdrażana. Domyślnie te konta nie mają takie same nazwy, jak te określone w manifeście aplikacji (na przykład *Customer3* w poniższym przykładzie manifest aplikacji). Zamiast tego są generowane dynamicznie i losowych haseł.

W **RunAsPolicy** sekcji **ServiceManifestImport**, określ konto użytkownika z **podmiotów** , aby uruchomić pakietu kodu usługi.  Poniższy przykład pokazuje, jak utworzyć użytkownika lokalnego i stosować zasady funkcji RunAs do główny punkt wejścia:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Można utworzyć grupy użytkowników i dodać co najmniej jednego użytkownika do grupy. Jest to przydatne, jeśli jest wielu użytkowników do innej usługi, punkty wejścia, a także muszą mieć pewne typowe uprawnienia, które są dostępne na poziomie grupy. W poniższym przykładzie manifest aplikacji zawiera grupy lokalnej o nazwie *LocalAdminGroup* , ma uprawnienia administratora. Dwóch użytkowników *serwer Customer1* i *Customer2*, zostają członkami tej grupy lokalnej. W **ServiceManifestImport** sekcji RunAs, zasady są stosowane do uruchomienia *Stateful1Pkg* pakietu kodu jako *Customer2*.  Inne zasady RunAs zastosowano do uruchomienia *Web1Pkg* pakietu kodu jako *serwer Customer1*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Zastosuj zasady domyślne do wszystkie pakiety kodu usługi
Możesz użyć **DefaultRunAsPolicy** sekcji, aby określić domyślne konto użytkownika dla całego kodu pakiety, które nie mają określonego **RunAsPolicy** zdefiniowane. Jeśli większość pakiety kodu, które są określone w manifeście usługi używane przez aplikację muszą działać w ramach tego samego użytkownika, aplikacja właśnie można zdefiniować domyślne zasady Uruchom jako przy użyciu tego konta użytkownika. Poniższy przykład określa, że jeśli nie ma pakietu kodu **RunAsPolicy** określony, pakiet kodu powinna być uruchamiana **MyDefaultAccount** użytkownika określonego w sekcji podmiotów zabezpieczeń.  Typy obsługiwanych kont są LocalUser, Usługa sieciowa, system lokalny i Usługa lokalna.  Jeśli używasz użytkownika lokalnego lub usługi, również określić nazwę konta i hasło.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Debuguj lokalnie przy użyciu konsoli przekierowania pakietu kodu
Od czasu do czasu jest to przydatne do celów debugowania wyświetlić dane wyjściowe konsoli z uruchomionej usługi. Możesz ustawić zasady przekierowywania konsoli w punkcie wejścia w manifeście usługi, która zapisuje dane wyjściowe do pliku. Plik wyjściowy plik zostanie zapisany do folderu aplikacji o nazwie **dziennika** w węźle klastra, w którym aplikacja jest wdrażany i uruchamiany. 

> [!WARNING]
> Nigdy nie używaj zasad przekierowania konsoli w aplikacji, która jest wdrażana w środowisku produkcyjnym, ponieważ może to wpłynąć na pracę awaryjną aplikacji. *Tylko* używane dla rozwoju lokalnych i celów debugowania.  
> 
> 

Następująca usługa manifestu Włączanie przekierowywanie konsoli z wartością FileRetentionCount pokazano w przykładzie:

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
* [Informacje o modelu aplikacji](service-fabric-application-model.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
