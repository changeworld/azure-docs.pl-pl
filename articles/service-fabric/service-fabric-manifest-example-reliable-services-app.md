---
title: Przykłady manifestów aplikacji niezawodnych usług
description: Dowiedz się, jak skonfigurować ustawienia manifestu aplikacji i usługi dla niezawodnej aplikacji sieci szkieletowej usług.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: f40e54f5260f827f0b18c833d23d1f57b5ebc3a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282356"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Przykłady manifestu aplikacji i usługi Reliable Services
Poniżej przedstawiono przykłady manifestów aplikacji i usługi dla aplikacji sieci szkieletowej usług z front-endem sieci web ASP.NET Core i stanowym zapleczem. Celem tych przykładów jest pokazanie, jakie ustawienia są dostępne i jak z nich korzystać. Te manifesty aplikacji i usług są oparte na manifestach sieci szkieletowej sieci szkieletowej [.NET.](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/)

Wyświetlane są następujące funkcje:

|Manifest|Funkcje|
|---|---|
|[Manifest aplikacji](#application-manifest)| [zarządzanie zasobami](service-fabric-resource-governance.md), [uruchamianie usługi jako konta administratora lokalnego](service-fabric-application-runas-security.md), [stosowanie zasad domyślnych do wszystkich pakietów kodu usługi,](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages) [tworzenie podmiotów zabezpieczeń użytkowników i grup,](service-fabric-application-runas-security.md)udostępnianie pakietu danych między wystąpieniami usługi, [zastępowanie punktów końcowych usługi](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|Manifest usługi Usługi FrontEndService| [Uruchamianie skryptu podczas uruchamiania usługi](service-fabric-run-script-at-service-startup.md), [definiowanie punktu końcowego HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|Manifest usługi backendservice| [Deklarowanie pakietu konfiguracyjnego](service-fabric-application-and-service-manifests.md), [deklarowanie pakietu danych,](service-fabric-application-and-service-manifests.md) [konfigurowanie punktu końcowego](service-fabric-service-manifest-resources.md)| 

Zobacz [elementy manifestu aplikacji,](#application-manifest-elements) [elementy manifestu usługi VotingWeb](#votingweb-service-manifest-elements)i [elementy manifestu usługi VotingData,](#votingdata-service-manifest-elements) aby uzyskać więcej informacji na temat określonych elementów XML.

## <a name="application-manifest"></a>Manifest aplikacji

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>Manifest usługi VotingWeb

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>Manifest usługi VotingData

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
    </Endpoints>
  </Resources>

</ServiceManifest>

```

## <a name="application-manifest-elements"></a>Elementy manifestu aplikacji
### <a name="applicationmanifest-element"></a>ApplicationManifest Element
Deklaratywnie opisuje typ aplikacji i wersję. Co najmniej jeden manifesty usługi usług składników usług są odwoływane do tworzenia typu aplikacji. Ustawienia konfiguracji usług składowych można zastąpić za pomocą sparametryzowanych ustawień aplikacji. Domyślne usługi, szablony usług, podmioty, zasady, konfiguracja diagnostyki i certyfikaty mogą również być zadeklarowane na poziomie aplikacji. Aby uzyskać więcej informacji, zobacz [ApplicationManifest Element](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Element parametrów
Deklaruje parametry, które są używane w tym manifeście aplikacji. Wartość tych parametrów może być podana, gdy aplikacja jest tworzone i może służyć do zastąpienia ustawień konfiguracji aplikacji lub usługi. Aby uzyskać więcej informacji, zobacz [Element parametrów](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Parameter — Element
Parametr aplikacji, który ma być używany w tym manifeście. Wartość parametru można zmienić podczas tworzenia wystąpienia aplikacji lub, jeśli nie podano żadnej wartości, używana jest wartość domyślna. Aby uzyskać więcej informacji, zobacz [Element parametru](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>ServiceManifestImport Element
Importuje manifest usługi utworzony przez dewelopera usługi. Manifest usługi musi być importowany dla każdej usługi składowej w aplikacji. Zastąpienia konfiguracji i zasady mogą być zadeklarowane dla manifestu usługi. Aby uzyskać więcej informacji, zobacz [ServiceManifestImport Element](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>ServiceManifestRef Element
Importuje manifest usługi przez odwołanie. Obecnie plik manifestu usługi (ServiceManifest.xml) musi znajdować się w pakiecie kompilacji. Aby uzyskać więcej informacji, zobacz [ServiceManifestRef Element](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="resourceoverrides-element"></a>ResourceOverrides Element
Określa zastąpienia zasobów dla punktów końcowych zadeklarowanych w zasobach manifestu usługi. Aby uzyskać więcej informacji, zobacz [ResourceOverrides Element](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Element punktów końcowych
Punkt końcowy(y) do zastąpienia. Aby uzyskać więcej informacji, zobacz [Element punktów końcowych](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Element punktu końcowego
Punkt końcowy, zadeklarowany w manifeście usługi, aby zastąpić. Aby uzyskać więcej informacji, zobacz [Element punktu końcowego](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Element zasad
W tym artykule opisano zasady (powiązanie punktu końcowego, udostępnianie pakietów, uruchamianie i dostęp do zabezpieczeń) do zastosowania w manifeście importowanej usługi. Aby uzyskać więcej informacji, zobacz [Element zasad](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy Element
Definiuje zasady ładu zasobów, które są stosowane na poziomie całego pakietu usług. Aby uzyskać więcej informacji, zobacz [ServicePackageResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Element polityki rządowej zasobów
Określa limity zasobów dla pakietu kodu. Aby uzyskać więcej informacji, zobacz [ResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>Element polityki w pakiecie
Wskazuje, czy kod, config lub pakiet danych powinny być współużytkowane przez wystąpienia usługi tego samego typu usługi. Aby uzyskać więcej informacji, zobacz [PackageSharingPolicy Element](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy Element
Udziela uprawnień dostępu do podmiotu zabezpieczeń w zasobie (takim jak punkt końcowy) zdefiniowany w manifeście usługi. Zazwyczaj jest bardzo przydatne do kontrolowania i ograniczania dostępu usług do różnych zasobów w celu zminimalizowania zagrożeń bezpieczeństwa. Jest to szczególnie ważne, gdy aplikacja jest zbudowana z kolekcji usług z rynku, które są opracowywane przez różnych deweloperów. Aby uzyskać więcej informacji, zobacz [SecurityAccessPolicy Element](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>RunAsPolicy Element
Określa konto użytkownika lokalnego lub lokalnego systemu, które pakiet kodu usługi będzie działać jako. Konta domeny są obsługiwane we wdrożeniach systemu Windows Server, w których dostępna jest usługa Azure Active Directory. Domyślnie aplikacje są uruchamiane na koncie, pod które działa proces Fabric.exe. Aplikacje można również uruchomić jako inne konta, które muszą być zadeklarowane w sekcji Podmioty. W przypadku zastosowania zasad RunAs do usługi, a manifest usługi deklaruje zasoby punktu końcowego za pomocą protokołu HTTP, należy również określić SecurityAccessPolicy, aby upewnić się, że porty przydzielone do tych punktów końcowych są poprawnie kontroli dostępu wymienione dla RunAs konto użytkownika, pod którymi działa usługa. Dla punktu końcowego HTTPS należy również zdefiniować EndpointBindingPolicy, aby wskazać nazwę certyfikatu, aby powrócić do klienta. Aby uzyskać więcej informacji, zobacz [RunAsPolicy Element](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>Element Usługi domyślne
Deklaruje wystąpienia usługi, które są tworzone automatycznie za każdym razem, gdy aplikacja jest tworzona dla tego typu aplikacji. Aby uzyskać więcej informacji, zobacz [DefaultServices Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Element serwisu
Deklaruje usługę, która ma zostać utworzona automatycznie po utworzeniu aplikacji. Aby uzyskać więcej informacji, zobacz [Element usługi](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>StatefulService Element
Definiuje usługę stanową. Aby uzyskać więcej informacji, zobacz [StatefulService Element](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>Element usługi bezstanowej
Definiuje usługę bezstanową. Aby uzyskać więcej informacji, zobacz [StatelessService Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Element Zleceniodawców
W tym artykule opisano podmioty zabezpieczeń (użytkowników, grupy) wymagane dla tej aplikacji do uruchamiania usług i zabezpieczania zasobów. Podmioty są odwoływane w sekcjach zasad. Aby uzyskać więcej informacji, zobacz [Element zleceniodawców](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Groups, element
Deklaruje zestaw grup jako podmioty zabezpieczeń, do których można się odwoływać w zasadach. Grupy są przydatne, jeśli istnieje wielu użytkowników dla różnych punktów wejścia usługi i muszą mieć pewne typowe uprawnienia, które są dostępne na poziomie grupy. Aby uzyskać więcej informacji, zobacz [Element grup](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Group, element
Deklaruje grupę jako podmiot zabezpieczeń, do którego można się odwoływać w zasadach. Aby uzyskać więcej informacji, zobacz [Element grupy](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Element członkostwa
 Aby uzyskać więcej informacji, zobacz [Element członkostwa](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>Element Grupy Systemowej
 Aby uzyskać więcej informacji, zobacz [Element Grupy Systemowej](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Element Użytkownicy
Deklaruje zestaw użytkowników jako podmioty zabezpieczeń, do których można się odwoływać w zasadach. Aby uzyskać więcej informacji, zobacz [Element użytkowników](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Element użytkownika
Deklaruje użytkownika jako podmiot zabezpieczeń, do którego można się odwoływać w zasadach. Aby uzyskać więcej informacji, zobacz [Element użytkownika](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>MemberOf Element
Użytkownicy mogą być dodawane do dowolnej istniejącej grupy członkostwa, dzięki czemu można dziedziczyć wszystkie właściwości i ustawienia zabezpieczeń tej grupy członkostwa. Grupa członkowska może służyć do zabezpieczania zasobów zewnętrznych, które muszą być dostępne dla różnych usług lub tej samej usługi (na innym komputerze). Aby uzyskać więcej informacji, zobacz [MemberOf Element](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>Element Grupy Systemowej
Grupa systemowa, do jak należy dodać użytkownika.  Grupa systemowa musi być zdefiniowana w sekcji Grupy. Aby uzyskać więcej informacji, zobacz [Element Grupy Systemowej](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Group, element
Grupa, do jaka ma być dodana.  Grupa musi być zdefiniowana w sekcji Grupy. Aby uzyskać więcej informacji, zobacz [Element grupy](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Element zasad
W tym artykule opisano zasady (zbieranie dzienników, domyślne uruchamianie, kondycja i dostęp do zabezpieczeń) do zastosowania na poziomie aplikacji. Aby uzyskać więcej informacji, zobacz [Element zasad](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>DefaultRunAsPolicy Element
Określ domyślne konto użytkownika dla wszystkich pakietów kodu usługi, które nie mają określonego RunAsPolicy zdefiniowane w ServiceManifestImport sekcji. Aby uzyskać więcej informacji, zobacz [DefaultRunAsPolicy Element](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>Elementy manifestu usługi VotingWeb
### <a name="servicemanifest-element"></a>ServiceManifest Element
Deklaratywnie opisuje typ usługi i wersję. Wyświetla listę niezależnie uaktualnianych pakietów kodu, konfiguracji i danych, które razem tworzą pakiet usługi do obsługi jednego lub więcej typów usług. Zasoby, ustawienia diagnostyczne i metadane usługi, takie jak typ usługi, właściwości kondycji i metryki równoważenia obciążenia, są również określone. Aby uzyskać więcej informacji, zobacz [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Definiuje, jakie typy usług są obsługiwane przez CodePackage w tym manifeście. Gdy usługa jest tworzone dla jednego z tych typów usług, wszystkie pakiety kodu zadeklarowane w tym manifeście są aktywowane przez uruchomienie ich punktów wejścia. Typy usług są zadeklarowane na poziomie manifestu, a nie na poziomie pakietu kodu. Aby uzyskać więcej informacji, zobacz [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Element typu usługi bezstanowej
Opisuje typ usługi bezstanowej. Aby uzyskać więcej informacji, zobacz [StatelessServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Element pakietu kodu
W tym artykule opisano pakiet kodu, który obsługuje zdefiniowany typ usługi. Gdy usługa jest tworzone dla jednego z tych typów usług, wszystkie pakiety kodu zadeklarowane w tym manifeście są aktywowane przez uruchomienie ich punktów wejścia. Procesy wynikowe oczekuje się zarejestrować obsługiwane typy usług w czasie wykonywania. Gdy istnieje wiele pakietów kodu, wszystkie są aktywowane za każdym razem, gdy system szuka jednego z zadeklarowanych typów usług. Aby uzyskać więcej informacji, zobacz [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>SetupEntryPoint Element
Uprzywilejowany punkt wejścia, który domyślnie jest uruchamiany z tymi samymi poświadczeniami co sieć szkieletowa usług (zazwyczaj konto NETWORKSERVICE) przed jakimkolwiek innym punktem wejścia. Plik wykonywalny określony przez entrypoint jest zazwyczaj długotrwałym hostem usługi. Obecność oddzielnego punktu wejścia konfiguracji pozwala uniknąć konieczności uruchamiania hosta usługi z wysokimi uprawnieniami przez dłuższy czas. Aby uzyskać więcej informacji, zobacz [SetupEntryPoint Element](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost Element
 Aby uzyskać więcej informacji, zobacz [Element ExeHost](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Element programu
Nazwa wykonywalna.  Na przykład "MySetup.bat" lub "MyServiceHost.exe". Aby uzyskać więcej informacji, zobacz [Element programu](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Element argumentów
 Aby uzyskać więcej informacji, zobacz [Argumenty elementu](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Element roboczy
Katalog roboczy dla procesu w pakiecie kodu w węźle klastra, w którym jest wdrażana aplikacja. Można określić trzy wartości: Praca (domyślna), CodePackage lub CodeBase. CodeBase określa, że katalog roboczy jest ustawiony na katalog, w którym EXE jest zdefiniowany w pakiecie kodu. CodePackage ustawia katalog roboczy jako katalog główny pakietu kodu, niezależnie od tego, gdzie EXE jest zdefiniowany w katalogu pakietów kodu. Praca ustawia katalog pracy na unikatowy folder utworzony w węźle.  Ten folder jest taki sam dla całego wystąpienia aplikacji. Domyślnie katalog roboczy wszystkich procesów w aplikacji jest ustawiony na folder roboczy aplikacji. W tym miejscu procesy mogą zapisywać dane. Zapisywanie danych w pakiecie kodu lub bazy kodu nie jest zalecane, ponieważ te foldery mogą być współużytkowane między różnymi wystąpieniami aplikacji i mogą zostać usunięte. Aby uzyskać więcej informacji, zobacz [Element roboczy](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>Element polecenia konsoli

> [!WARNING]
> Nie należy używać przekierowania konsoli w aplikacji produkcyjnej, należy go używać tylko do lokalnego rozwoju i debugowania. Przekierowuje dane wyjściowe konsoli ze skryptu startowego do pliku wyjściowego w folderze aplikacji o nazwie "log" w węźle klastra, w którym aplikacja jest wdrażana i uruchamiana. Aby uzyskać więcej informacji, zobacz [ConsoleRedirection Element](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>EntryPoint Element
Plik wykonywalny określony przez entrypoint jest zazwyczaj długotrwałym hostem usługi. Obecność oddzielnego punktu wejścia konfiguracji pozwala uniknąć konieczności uruchamiania hosta usługi z wysokimi uprawnieniami przez dłuższy czas. Plik wykonywalny określony przez entrypoint jest uruchamiany po pomyślnym zamknięciu programu SetupEntryPoint. Wynikowy proces jest monitorowany i uruchamiany ponownie (zaczynając od nowa za pomocą programu SetupEntryPoint), jeśli kiedykolwiek zostanie zakończony lub ulegnie awarii. Aby uzyskać więcej informacji, zobacz [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost Element
 Aby uzyskać więcej informacji, zobacz [Element ExeHost](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>Element pakietu konfiguracyjnego
Deklaruje folder o nazwie według atrybutu Nazwa w obszarze PackageRoot zawierający plik Settings.xml. Ten plik zawiera sekcje zdefiniowanych przez użytkownika ustawień pary klucz-wartość, które proces może odczytać w czasie wykonywania. Podczas uaktualniania, jeśli tylko configPackage wersja została zmieniona, a następnie uruchomiony proces nie jest ponownie uruchamiany. Zamiast tego wywołanie zwrotne powiadamia proces, że ustawienia konfiguracji zostały zmienione, dzięki czemu można je ponownie załadować dynamicznie. Aby uzyskać więcej informacji, zobacz [Element configPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Element zasobów
W tym artykule opisano zasoby używane przez tę usługę, które można zadeklarować bez modyfikowania skompilowanego kodu i zmieniane podczas wdrażania usługi. Dostęp do tych zasobów jest kontrolowany za pośrednictwem zasad i zasad sekcje manifestu aplikacji. Aby uzyskać więcej informacji, zobacz [Element zasobów](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element punktów końcowych
Definiuje punkty końcowe dla usługi. Aby uzyskać więcej informacji, zobacz [Element punktów końcowych](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element punktu końcowego
Punkt końcowy, zadeklarowany w manifeście usługi, aby zastąpić. Aby uzyskać więcej informacji, zobacz [Element punktu końcowego](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>Elementy manifestu usługi VotingData
### <a name="servicemanifest-element"></a>ServiceManifest Element
Deklaratywnie opisuje typ usługi i wersję. Wyświetla listę niezależnie uaktualnianych pakietów kodu, konfiguracji i danych, które razem tworzą pakiet usługi do obsługi jednego lub więcej typów usług. Zasoby, ustawienia diagnostyczne i metadane usługi, takie jak typ usługi, właściwości kondycji i metryki równoważenia obciążenia, są również określone. Aby uzyskać więcej informacji, zobacz [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Definiuje, jakie typy usług są obsługiwane przez CodePackage w tym manifeście. Gdy usługa jest tworzone dla jednego z tych typów usług, wszystkie pakiety kodu zadeklarowane w tym manifeście są aktywowane przez uruchomienie ich punktów wejścia. Typy usług są zadeklarowane na poziomie manifestu, a nie na poziomie pakietu kodu. Aby uzyskać więcej informacji, zobacz [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>Element Typu usługi stanowej
Opisuje typ usługi stanowej. Aby uzyskać więcej informacji, zobacz [StatefulServiceType Element](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Element pakietu kodu
W tym artykule opisano pakiet kodu, który obsługuje zdefiniowany typ usługi. Gdy usługa jest tworzone dla jednego z tych typów usług, wszystkie pakiety kodu zadeklarowane w tym manifeście są aktywowane przez uruchomienie ich punktów wejścia. Procesy wynikowe oczekuje się zarejestrować obsługiwane typy usług w czasie wykonywania. Gdy istnieje wiele pakietów kodu, wszystkie są aktywowane za każdym razem, gdy system szuka jednego z zadeklarowanych typów usług. Aby uzyskać więcej informacji, zobacz [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint Element
Plik wykonywalny określony przez entrypoint jest zazwyczaj długotrwałym hostem usługi. Obecność oddzielnego punktu wejścia konfiguracji pozwala uniknąć konieczności uruchamiania hosta usługi z wysokimi uprawnieniami przez dłuższy czas. Plik wykonywalny określony przez entrypoint jest uruchamiany po pomyślnym zamknięciu programu SetupEntryPoint. Wynikowy proces jest monitorowany i uruchamiany ponownie (zaczynając od nowa za pomocą programu SetupEntryPoint), jeśli kiedykolwiek zostanie zakończony lub ulegnie awarii. Aby uzyskać więcej informacji, zobacz [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost Element
 Aby uzyskać więcej informacji, zobacz [Element ExeHost](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Element programu
Nazwa wykonywalna.  Na przykład "MySetup.bat" lub "MyServiceHost.exe". Aby uzyskać więcej informacji, zobacz [Element programu](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Element roboczy
Katalog roboczy dla procesu w pakiecie kodu w węźle klastra, w którym jest wdrażana aplikacja. Można określić trzy wartości: Praca (domyślna), CodePackage lub CodeBase. CodeBase określa, że katalog roboczy jest ustawiony na katalog, w którym EXE jest zdefiniowany w pakiecie kodu. CodePackage ustawia katalog roboczy jako katalog główny pakietu kodu, niezależnie od tego, gdzie EXE jest zdefiniowany w katalogu pakietów kodu. Praca ustawia katalog pracy na unikatowy folder utworzony w węźle.  Ten folder jest taki sam dla całego wystąpienia aplikacji. Domyślnie katalog roboczy wszystkich procesów w aplikacji jest ustawiony na folder roboczy aplikacji. W tym miejscu procesy mogą zapisywać dane. Zapisywanie danych w pakiecie kodu lub bazy kodu nie jest zalecane, ponieważ te foldery mogą być współużytkowane między różnymi wystąpieniami aplikacji i mogą zostać usunięte. Aby uzyskać więcej informacji, zobacz [Element roboczy](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>Element pakietu konfiguracyjnego
Deklaruje folder o nazwie według atrybutu Nazwa w obszarze PackageRoot zawierający plik Settings.xml. Ten plik zawiera sekcje zdefiniowanych przez użytkownika ustawień pary klucz-wartość, które proces może odczytać w czasie wykonywania. Podczas uaktualniania, jeśli tylko configPackage wersja została zmieniona, a następnie uruchomiony proces nie jest ponownie uruchamiany. Zamiast tego wywołanie zwrotne powiadamia proces, że ustawienia konfiguracji zostały zmienione, dzięki czemu można je ponownie załadować dynamicznie. Aby uzyskać więcej informacji, zobacz [Element configPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Element pakietu danych
Deklaruje folder o nazwie przez name atrybutu, w obszarze PackageRoot, który zawiera statyczne pliki danych do korzystania przez proces w czasie wykonywania. Sieci szkieletowej usług będzie odtwarzać wszystkie EXEs i DLLHOSTs określone w pakietach hosta i pomocy technicznej, gdy którykolwiek z pakietów danych wymienionych w manifeście usługi są uaktualniane. Aby uzyskać więcej informacji, zobacz [DataPackage Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Element zasobów
W tym artykule opisano zasoby używane przez tę usługę, które można zadeklarować bez modyfikowania skompilowanego kodu i zmieniane podczas wdrażania usługi. Dostęp do tych zasobów jest kontrolowany za pośrednictwem zasad i zasad sekcje manifestu aplikacji. Aby uzyskać więcej informacji, zobacz [Element zasobów](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element punktów końcowych
Definiuje punkty końcowe dla usługi. Aby uzyskać więcej informacji, zobacz [Element punktów końcowych](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element punktu końcowego
Punkt końcowy, zadeklarowany w manifeście usługi, aby zastąpić. Aby uzyskać więcej informacji, zobacz [Element punktu końcowego](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

