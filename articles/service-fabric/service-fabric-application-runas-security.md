---
title: Uruchamianie usługi w systemie i lokalnych kontach zabezpieczeń
description: Dowiedz się, jak uruchomić aplikację sieci szkieletowej usług w systemach i lokalnych kontach zabezpieczeń.  Tworzenie podmiotów zabezpieczeń i zastosować zasady Uruchom jako bezpiecznie uruchomić swoje usługi.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: 53212f8636602705899834b6db1d3f0d80b5fe4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610129"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Uruchamianie usługi jako lokalnego konta użytkownika lub lokalnego konta systemowego
Za pomocą usługi Azure Service Fabric, można zabezpieczyć aplikacje, które są uruchomione w klastrze w ramach różnych kont użytkowników. Domyślnie aplikacje sieci szkieletowej usług są uruchamiane na koncie, pod które działa proces Fabric.exe. Sieć szkieletowa usług zapewnia również możliwość uruchamiania aplikacji w ramach konta użytkownika lokalnego lub systemu. Obsługiwane typy kont systemu lokalnego to **LocalUser**, **NetworkService**, **LocalService**i **LocalSystem**.  Jeśli w samodzielnym klastrze systemu Windows jest uruchomiona usługa w obszarze [Konta domeny usługi Active Directory](service-fabric-run-service-as-ad-user-or-group.md) lub grupy [zarządzanych kont usług](service-fabric-run-service-as-gmsa.md).

W manifeście aplikacji można zdefiniować konta użytkowników wymagane do uruchamiania usług lub bezpiecznych zasobów w sekcji **Podmioty.** Można również zdefiniować i utworzyć grupy użytkowników, tak aby jeden lub więcej użytkowników można było zarządzać razem. Jest to przydatne, gdy istnieje wielu użytkowników dla różnych punktów wejścia usługi i potrzebują wspólnych uprawnień, które są dostępne na poziomie grupy.  Użytkownicy są następnie odwołuje się do zasad RunAs, który jest stosowany do określonej usługi lub wszystkich usług w aplikacji. 

Domyślnie zasady RunAs są stosowane do głównego punktu wejścia.  Zasady RunAs można również zastosować do punktu wejścia instalatora, jeśli konieczne jest [uruchomienie pewnych operacji konfiguracji wysokich uprawnień w ramach konta systemowego](service-fabric-run-script-at-service-startup.md)lub zarówno głównych, jak i instalacyjnych punktów wejścia.  

> [!NOTE] 
> W przypadku zastosowania zasady RunAs do usługi, a manifest usługi deklaruje zasoby punktu końcowego za pomocą protokołu HTTP, należy określić **SecurityAccessPolicy**.  Aby uzyskać więcej informacji, zobacz [Przypisywanie zasad dostępu zabezpieczeń dla punktów końcowych HTTP i HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Uruchamianie usługi jako użytkownik lokalny
Można utworzyć użytkownika lokalnego, który może służyć do zabezpieczania usługi w aplikacji. Gdy typ konta **LocalUser** jest określony w sekcji podmioty manifestu aplikacji, sieć szkieletowa usług tworzy konta użytkowników lokalnych na komputerach, na których jest wdrażana aplikacja. Domyślnie te konta nie mają takich samych nazw jak te określone w manifeście aplikacji (na przykład *Customer3* w poniższym przykładzie manifestu aplikacji). Zamiast tego są generowane dynamicznie i mają losowe hasła.

W **RunAsPolicy** sekcji dla **ServiceManifestImport**, określ konto użytkownika z **principals** sekcji, aby uruchomić pakiet kodu usługi.  W poniższym przykładzie pokazano, jak utworzyć użytkownika lokalnego i zastosować zasady RunAs do głównego punktu wejścia:

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

## <a name="create-a-local-user-group"></a>Tworzenie lokalnej grupy użytkowników
Można utworzyć grupy użytkowników i dodać do grupy jednego lub więcej użytkowników. Jest to przydatne, jeśli istnieje wielu użytkowników dla różnych punktów wejścia usługi i muszą mieć pewne typowe uprawnienia, które są dostępne na poziomie grupy. W poniższym przykładzie manifestu aplikacji pokazano grupę lokalną o nazwie *LocalAdminGroup,* która ma uprawnienia administratora. Dwóch użytkowników, *Customer1* i *Customer2,* są członkami tej lokalnej grupy. W **serviceManifestImport** sekcji RunAs zasady są stosowane do uruchamiania pakietu kodu *Stateful1Pkg* jako *Customer2*.  Inne zasady RunAs są stosowane do uruchamiania pakietu kodu *Web1Pkg* jako *customer1*.

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

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Stosowanie zasad domyślnych do wszystkich pakietów kodów usługi
Użyj **DefaultRunAsPolicy** sekcji, aby określić domyślne konto użytkownika dla wszystkich pakietów kodu, które nie mają określonego **RunAsPolicy** zdefiniowane. Jeśli większość pakietów kodu, które są określone w manifeście usługi używane przez aplikację trzeba uruchomić w ramach tego samego użytkownika, aplikacja może po prostu zdefiniować domyślne zasady RunAs z tego konta użytkownika. Poniższy przykład określa, że jeśli pakiet kodu nie ma **RunAsPolicy** określony, pakiet kodu należy uruchomić w ramach **MyDefaultAccount** użytkownika określonego w sekcji podmiotów.  Obsługiwane typy kont to LocalUser, NetworkService, LocalSystem i LocalService.  Jeśli korzystasz z lokalnego użytkownika lub usługi, należy również określić nazwę konta i hasło.

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

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Debugowanie pakietu kodu lokalnie przy użyciu przekierowania konsoli
Od czasu do czasu jest przydatne do celów debugowania, aby wyświetlić dane wyjściowe konsoli z uruchomionej usługi. Można ustawić zasady przekierowania konsoli w punkcie wejścia w manifeście usługi, który zapisuje dane wyjściowe do pliku. Dane wyjściowe pliku są zapisywane w folderze aplikacji **nazywanym logiem** w węźle klastra, w którym aplikacja jest wdrażana i uruchamiana. 

> [!WARNING]
> Nigdy nie używaj zasad przekierowania konsoli w aplikacji, która jest wdrażana w produkcji, ponieważ może to mieć wpływ na proces pracy awaryjnej aplikacji. *Użyj* tego tylko do lokalnych celów rozwoju i debugowania.  
> 
> 

Poniższy przykład manifestu usługi pokazuje włączanie przekierowania konsoli z wartością FileRetentionCount:

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
## <a name="next-steps"></a>Następne kroki
* [Opis modelu aplikacji](service-fabric-application-model.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
