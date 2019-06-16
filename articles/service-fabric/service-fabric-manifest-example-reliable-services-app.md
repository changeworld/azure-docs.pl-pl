---
title: Usługa Azure Service Fabric reliable services — przykłady manifestu aplikacji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować aplikację i ustawienia manifestu dla aplikacji usługi Service Fabric reliable services usługi.
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6c4c8f0ee6aa12c58e02f71b42312cd6872076aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60719168"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Przykłady manifestu aplikacji i usługi Reliable Services
Poniżej przedstawiono przykłady manifestów aplikacji i usługi dla aplikacji usługi Service Fabric za pomocą frontonu sieci web platformy ASP.NET Core i stanowej back-end. Te przykłady ma na celu Pokaż jakie ustawienia są dostępne i jak z nich korzystać. Te manifesty aplikacji i usługi są oparte na [usługi Service Fabric platformy .NET Przewodnik Szybki Start](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) manifestów.

Wyświetlane są następujące funkcje:

|Manifest|Funkcje|
|---|---|
|[Manifest aplikacji](#application-manifest)| [nadzór nad zasobami](service-fabric-resource-governance.md), [uruchamianie usługi jako konto administratora lokalnego](service-fabric-application-runas-security.md), [dotyczą zasady domyślne wszystkie pakiety kodu usługi](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [tworzenie użytkowników i grup jednostek](service-fabric-application-runas-security.md), Udostępnij pakiet danych między wystąpieniami usług [zastąpienia punkty końcowe usługi](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|FrontEndService manifestu usługi| [Za pomocą skryptu podczas uruchamiania usługi](service-fabric-run-script-at-service-startup.md), [Definiowanie punktu końcowego HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|BackEndService manifestu usługi| [Deklaracja pakietu konfiguracji](service-fabric-application-and-service-manifests.md), [zadeklarować pakiet danych](service-fabric-application-and-service-manifests.md), [konfigurowania punktu końcowego](service-fabric-service-manifest-resources.md)| 

Zobacz [elementy manifestu aplikacji](#application-manifest-elements), [elementy manifestu usługi VotingWeb](#votingweb-service-manifest-elements), i [elementy manifestu usługi VotingData](#votingdata-service-manifest-elements) więcej informacji na temat określonych XML elementy.

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
      <PackageSharingPolicy PackageRef="VotingDataPkg.Data"/>

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

## <a name="votingdata-service-manifest"></a>VotingData manifestu usługi

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
Opisuje sposób deklaratywny typ i wersja aplikacji. Co najmniej jeden manifestów usługi składowe usług odwołują się do redagowania typu aplikacji. Ustawienia konfiguracji usługi składowe można zastąpić przy użyciu ustawień aplikacji sparametryzowanych. Domyślnie usługi, szablony usług, podmiotów zabezpieczeń, zasady, konfiguracji diagnostyki i certyfikatów mogą również zadeklarowane na poziomie aplikacji. Aby uzyskać więcej informacji, zobacz [ApplicationManifest — Element](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Parametry elementu
Deklaruje parametry, które są używane w tym manifeście aplikacji. Wartości tych parametrów można podać, gdy aplikacja zostanie uruchomiony i może służyć do zastępowania ustawień konfiguracji usługi lub aplikacji. Aby uzyskać więcej informacji, zobacz [parametrów elementu](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Parameter — Element
Parametr aplikacji ma być używany w tym manifeście. Podczas tworzenia wystąpienia aplikacji, można zmienić wartość tego parametru lub, jeśli nie dostarczono żadnej wartości zostanie użyta domyślna wartość. Aby uzyskać więcej informacji, zobacz [Parameter — Element](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>ServiceManifestImport Element
Importuje manifestu usługi utworzone przez dewelopera usługi. Manifest usługi muszą zostać zaimportowane dla poszczególnych składników usługi w aplikacji. Zastępuje element konfiguracji, a zasady mogą być deklarowane manifestu usługi. Aby uzyskać więcej informacji, zobacz [ServiceManifestImport — Element](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>ServiceManifestRef Element
Importuje manifestu usługi przez odwołanie. Obecnie plik manifestu usługi (ServiceManifest.xml) musi być obecne w pakiecie kompilacji. Aby uzyskać więcej informacji, zobacz [ServiceManifestRef — Element](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="resourceoverrides-element"></a>ResourceOverrides Element
Określa przesłonięcia zasobów dla punktów końcowych zadeklarowanych w zasoby manifestu usługi. Aby uzyskać więcej informacji, zobacz [ResourceOverrides — Element](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Element punktów końcowych
Punkty końcowe: do zastąpienia. Aby uzyskać więcej informacji, zobacz [Element punktów końcowych](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Punkt końcowy elementu
Punkt końcowy, zadeklarowane w manifeście usługi, aby zastąpić. Aby uzyskać więcej informacji, zobacz [elementu punktu końcowego](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Element zasad
W tym artykule opisano zasady (punkt końcowy powiązania, pakiet do udostępniania, Uruchom jako i zabezpieczenia dostępu) mają być stosowane w manifeście importowanych usługi. Aby uzyskać więcej informacji, zobacz [Element zasad](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy Element
Definiuje zasady ładu zasobów, która jest stosowana na poziomie pakietu całą usługę. Aby uzyskać więcej informacji, zobacz [ServicePackageResourceGovernancePolicy — Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy Element
Określa limity zasobów dla codepackage. Aby uzyskać więcej informacji, zobacz [ResourceGovernancePolicy — Element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>Element PackageSharingPolicy — Element
Wskazuje, jeśli pakiet kodu, konfiguracji lub danych powinno być współużytkowane przez wystąpień usługi typu usługi. Aby uzyskać więcej informacji, zobacz [zasady PackageSharingPolicy — Element](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy Element
Przyznaje uprawnienia dostępu do jednostki dla zasobu (np. punkt końcowy) zdefiniowany w manifeście usługi. Zazwyczaj jest bardzo przydatny kontrolować i ograniczać dostęp do usług do różnych zasobów, aby zminimalizować zagrożenia bezpieczeństwa. Jest to szczególnie ważne w przypadku, gdy aplikacja jest skompilowana kolekcję usług z witryny marketplace, które są opracowywane przez różnych deweloperów. Aby uzyskać więcej informacji, zobacz [SecurityAccessPolicy — Element](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>RunAsPolicy Element
Określa użytkownika lokalnego lub konta system lokalny, w których będzie używać pakietu kodu usługi. Konta domeny są obsługiwane we wdrożeniach systemu Windows Server, w którym usługi Azure Active Directory jest dostępna. Domyślnie aplikacje są uruchamiane na koncie procesu Fabric.exe jest uruchamiana. Aplikacje można również uruchomić jako innych kont, które muszą być zadeklarowane w sekcji podmiotów zabezpieczeń. Jeśli zastosujesz zasady RunAs do usługi i manifestu usługi deklaruje zasobów punktu końcowego przy użyciu protokołu HTTP, należy także określić SecurityAccessPolicy, aby upewnić się, że porty są przydzielane do tych punktów końcowych są poprawnie kontroli dostępu dla RunAs konto użytkownika, którego usługa będzie uruchamiana. Dla punktu końcowego protokołu HTTPS również należy zdefiniować EndpointBindingPolicy, aby wskazać nazwę certyfikatu, aby powrócić do klienta. Aby uzyskać więcej informacji, zobacz [RunAsPolicy — Element](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>DefaultServices Element
Deklaruje wystąpień usług, które są tworzone automatycznie, gdy aplikacja zostanie uruchomiony dla tego typu aplikacji. Aby uzyskać więcej informacji, zobacz [DefaultServices — Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Element usługi
Deklaruje usługi, które zostaną utworzone automatycznie podczas tworzenia wystąpienia aplikacji. Aby uzyskać więcej informacji, zobacz [Element usługi](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>Element klasy StatefulService
Definiuje usługi stanowej. Aby uzyskać więcej informacji, zobacz [klasy StatefulService — Element](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>StatelessService Element
Definiuje usługę bezstanową. Aby uzyskać więcej informacji, zobacz [StatelessService — Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Element podmiotów zabezpieczeń
W tym artykule opisano podmiotów zabezpieczeń (użytkowników, grup) wymagany dla tej aplikacji do uruchamiania usług i bezpiecznych zasobów. Jednostki są wymienione w sekcji zasady. Aby uzyskać więcej informacji, zobacz [Element podmiotów zabezpieczeń](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Element grupy
Deklaruje zestaw grup jako zabezpieczenie podmiotów zabezpieczeń, które mogą być przywoływane w zasadach. Grupy są przydatne, jeśli jest wielu użytkowników do innej usługi, punkty wejścia, a także muszą mieć pewne typowe uprawnienia, które są dostępne na poziomie grupy. Aby uzyskać więcej informacji, zobacz [elementu grupy](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Element grupy
Deklaruje grupy jako podmiot zabezpieczeń, które mogą być przywoływane w zasadach. Aby uzyskać więcej informacji, zobacz [elementu grupy](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Element członkostwa
 Aby uzyskać więcej informacji, zobacz [elementu Membership](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>SystemGroup Element
 Aby uzyskać więcej informacji, zobacz [SystemGroup — Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Użytkownicy — Element
Deklaruje zestaw użytkowników, jako zabezpieczenie podmiotów zabezpieczeń, które mogą być przywoływane w zasadach. Aby uzyskać więcej informacji, zobacz [Element użytkowników](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Element użytkownika
Deklaruje użytkownika jako podmiot zabezpieczeń, które mogą być przywoływane w zasadach. Aby uzyskać więcej informacji, zobacz [elementu użytkownika](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>MemberOf Element
Użytkownicy mogą zostać dodani do żadnej istniejącej grupy członkostwa, dzięki czemu może dziedziczyć wszystkie właściwości i ustawienia zabezpieczeń tej grupy członkostwa. Członkostwo w grupie może służyć do zabezpieczenia zasobów zewnętrznych, które muszą być dostępne dla różnych usług lub tej samej usługi (na innym komputerze). Aby uzyskać więcej informacji, zobacz [MemberOf — Element](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>SystemGroup Element
Grupa systemu można dodać użytkownika.  Grupa systemu muszą być zdefiniowane w sekcji grupy. Aby uzyskać więcej informacji, zobacz [SystemGroup — Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Element grupy
Można dodać użytkownika do grupy.  Grupy muszą być zdefiniowane w sekcji grupy. Aby uzyskać więcej informacji, zobacz [elementu grupy](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Element zasad
W tym artykule opisano zasady (zbieranie danych dziennika, domyślna, Uruchom jako, kondycji i zabezpieczeń dostępu) mają być stosowane na poziomie aplikacji. Aby uzyskać więcej informacji, zobacz [Element zasad](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>DefaultRunAsPolicy Element
Określ domyślne konto użytkownika dla wszystkich pakietów kod usługi, które nie mają określonych RunAsPolicy, zdefiniowane w sekcji ServiceManifestImport. Aby uzyskać więcej informacji, zobacz [DefaultRunAsPolicy — Element](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>Elementy manifestu usługi VotingWeb
### <a name="servicemanifest-element"></a>ServiceManifest Element
Deklaratywnie w tym artykule opisano usługę typu i wersji. Wyświetla listę niezależnie możliwość uaktualnienia pakiety kodu, konfiguracji i danych, które razem tworzą pakietu usług do obsługi co najmniej jeden typ usługi. Zasoby, ustawienia diagnostyczne i metadanych usługi, takie jak typ usługi, właściwości kondycji i równoważenie obciążenia metryki, również są określone. Aby uzyskać więcej informacji, zobacz [ServiceManifest — Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Definiuje, jakie usługi są obsługiwane przez CodePackage w tym manifeście. Gdy usługa zostanie uruchomiony na jednym z tych typów usług, wszystkie pakiety kodu zadeklarowany w tym manifeście zostaną aktywowane przez uruchomienie ich punkty wejścia. Typy usług są zadeklarowane na poziomie manifestu, a nie na poziomie pakietu kodu. Aby uzyskać więcej informacji, zobacz [ServiceTypes — Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType Element
Opisuje typ usługi bezstanowej. Aby uzyskać więcej informacji, zobacz [StatelessServiceType — Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage Element
W tym artykule opisano pakiet kodu, który obsługuje typ zdefiniowany usługi. Gdy usługa zostanie uruchomiony na jednym z tych typów usług, wszystkie pakiety kodu zadeklarowany w tym manifeście zostaną aktywowane przez uruchomienie ich punkty wejścia. Wynikowy procesy powinny zarejestrować obsługiwanych typów usług w czasie wykonywania. W przypadku wielu pakietów kodu ich wszystkich aktywacji zawsze wtedy, gdy system wyszukuje jeden z typów zadeklarowane usług. Aby uzyskać więcej informacji, zobacz [CodePackage — Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>SetupEntryPoint Element
Punkt wejścia uprzywilejowane, która domyślnie jest uruchamiany z tymi samymi poświadczeniami co Usługa Service Fabric (zazwyczaj konto NETWORKSERVICE) przed innymi punktu wejścia. Plik wykonywalny określony przez punkt wejścia jest zazwyczaj długotrwałych hosta usługi. Obecność punktu wejścia Instalatora oddzielne pozwala na uniknięcie konieczności uruchamiania hosta usługi z wysokim poziomem uprawnień na dłuższy czas. Aby uzyskać więcej informacji, zobacz [SetupEntryPoint — Element](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost Element
 Aby uzyskać więcej informacji, zobacz [ExeHost — Element](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Element programu
Nazwa pliku wykonywalnego.  Na przykład "MySetup.bat" lub "MyServiceHost.exe". Aby uzyskać więcej informacji, zobacz [elementu programu](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Element argumentów
 Aby uzyskać więcej informacji, zobacz [Element argumentów](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder Element
Katalog roboczy dla procesu w pakiecie kod w węźle klastra, w którym aplikacja jest wdrażana. Można określić trzy wartości: Praca (ustawienie domyślne), CodePackage lub kodu. CodeBase Określa, że katalog roboczy jest ustawiony do katalogu, w którym plik EXE jest zdefiniowany w pakiecie kodu. CodePackage Ustawia katalog roboczy być katalogiem głównym pakietu kodu, niezależnie od tego, gdzie plik EXE jest zdefiniowana w katalogu pakietu kodu. Praca Ustawia katalog roboczy unikatowy folder utworzony w węźle.  Ten folder jest taka sama dla wystąpienia całej aplikacji. Domyślnie katalog roboczy przez wszystkie procesy w aplikacji jest równa folderu roboczego aplikacji. Jest to, gdzie procesów można zapisywać dane. Zapisywanie danych w pakiecie kod lub kod podstawowy nie jest zalecane, ponieważ te foldery mogą być współużytkowane przez różne wystąpienia aplikacji i może być usunięty. Aby uzyskać więcej informacji, zobacz [WorkingFolder — Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>ConsoleRedirection Element

> [!WARNING]
> Przekierowywanie konsoli jest używany w aplikacji produkcyjnych, nie tylko używać lokalne programowanie i debugowanie. Przekierowuje dane wyjściowe konsoli skryptu uruchamiania do pliku wyjściowego w folderze aplikacji o nazwie "Zaloguj" w węźle klastra, w którym aplikacja jest wdrażany i uruchamiany. Aby uzyskać więcej informacji, zobacz [ConsoleRedirection — Element](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>EntryPoint — Element
Plik wykonywalny określony przez punkt wejścia jest zazwyczaj długotrwałych hosta usługi. Obecność punktu wejścia Instalatora oddzielne pozwala na uniknięcie konieczności uruchamiania hosta usługi z wysokim poziomem uprawnień na dłuższy czas. Plik wykonywalny określony przez punkt wejścia jest uruchamiany po SetupEntryPoint kończy się pomyślnie. Proces wynikowy jest monitorowana i uruchomione ponownie (od początku ponownie, używając SetupEntryPoint), jeśli kiedykolwiek kończy się lub ulega awarii. Aby uzyskać więcej informacji, zobacz [EntryPoint — Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost Element
 Aby uzyskać więcej informacji, zobacz [ExeHost — Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage Element
Deklaruje folderem o nazwie określonej przez atrybut Name w obszarze PackageRoot, który zawiera plik Settings.xml. Ten plik zawiera sekcje ustawień zdefiniowanych przez użytkownika, pary klucz wartość pary, które ten proces może odczytywać Wstecz w czasie wykonywania. Podczas uaktualniania Jeśli istnieją tylko w wersji ConfigPackage został zmieniony, następnie uruchomiony proces nie zostanie ponownie uruchomiony. Zamiast tego wywołania zwrotnego powiadamia procesu, które uległy zmianie ustawień konfiguracji, aby dynamicznie załadowania. Aby uzyskać więcej informacji, zobacz [ConfigPackage — Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Element zasobów
W tym artykule opisano zasoby używane przez tę usługę, która może być zadeklarowana bez konieczności modyfikowania kodu skompilowanego i zmieniać w przypadku wdrażania usługi. Dostęp do tych zasobów jest kontrolowany za pośrednictwem podmiotów i zasad części manifestu aplikacji. Aby uzyskać więcej informacji, zobacz [elementu zasobów](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element punktów końcowych
Definiuje punkty końcowe usługi. Aby uzyskać więcej informacji, zobacz [Element punktów końcowych](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Punkt końcowy elementu
Punkt końcowy, zadeklarowane w manifeście usługi, aby zastąpić. Aby uzyskać więcej informacji, zobacz [elementu punktu końcowego](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>Elementy manifestu usługi VotingData
### <a name="servicemanifest-element"></a>ServiceManifest Element
Deklaratywnie w tym artykule opisano usługę typu i wersji. Wyświetla listę niezależnie możliwość uaktualnienia pakiety kodu, konfiguracji i danych, które razem tworzą pakietu usług do obsługi co najmniej jeden typ usługi. Zasoby, ustawienia diagnostyczne i metadanych usługi, takie jak typ usługi, właściwości kondycji i równoważenie obciążenia metryki, również są określone. Aby uzyskać więcej informacji, zobacz [ServiceManifest — Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Definiuje, jakie usługi są obsługiwane przez CodePackage w tym manifeście. Gdy usługa zostanie uruchomiony na jednym z tych typów usług, wszystkie pakiety kodu zadeklarowany w tym manifeście zostaną aktywowane przez uruchomienie ich punkty wejścia. Typy usług są zadeklarowane na poziomie manifestu, a nie na poziomie pakietu kodu. Aby uzyskać więcej informacji, zobacz [ServiceTypes — Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>StatefulServiceType Element
W tym artykule opisano typu usługi stanowej. Aby uzyskać więcej informacji, zobacz [StatefulServiceType — Element](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage Element
W tym artykule opisano pakiet kodu, który obsługuje typ zdefiniowany usługi. Gdy usługa zostanie uruchomiony na jednym z tych typów usług, wszystkie pakiety kodu zadeklarowany w tym manifeście zostaną aktywowane przez uruchomienie ich punkty wejścia. Wynikowy procesy powinny zarejestrować obsługiwanych typów usług w czasie wykonywania. W przypadku wielu pakietów kodu ich wszystkich aktywacji zawsze wtedy, gdy system wyszukuje jeden z typów zadeklarowane usług. Aby uzyskać więcej informacji, zobacz [CodePackage — Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint — Element
Plik wykonywalny określony przez punkt wejścia jest zazwyczaj długotrwałych hosta usługi. Obecność punktu wejścia Instalatora oddzielne pozwala na uniknięcie konieczności uruchamiania hosta usługi z wysokim poziomem uprawnień na dłuższy czas. Plik wykonywalny określony przez punkt wejścia jest uruchamiany po SetupEntryPoint kończy się pomyślnie. Proces wynikowy jest monitorowana i uruchomione ponownie (od początku ponownie, używając SetupEntryPoint), jeśli kiedykolwiek kończy się lub ulega awarii. Aby uzyskać więcej informacji, zobacz [EntryPoint — Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost Element
 Aby uzyskać więcej informacji, zobacz [ExeHost — Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Element programu
Nazwa pliku wykonywalnego.  Na przykład "MySetup.bat" lub "MyServiceHost.exe". Aby uzyskać więcej informacji, zobacz [elementu programu](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder Element
Katalog roboczy dla procesu w pakiecie kod w węźle klastra, w którym aplikacja jest wdrażana. Można określić trzy wartości: Praca (ustawienie domyślne), CodePackage lub kodu. CodeBase Określa, że katalog roboczy jest ustawiony do katalogu, w którym plik EXE jest zdefiniowany w pakiecie kodu. CodePackage Ustawia katalog roboczy być katalogiem głównym pakietu kodu, niezależnie od tego, gdzie plik EXE jest zdefiniowana w katalogu pakietu kodu. Praca Ustawia katalog roboczy unikatowy folder utworzony w węźle.  Ten folder jest taka sama dla wystąpienia całej aplikacji. Domyślnie katalog roboczy przez wszystkie procesy w aplikacji jest równa folderu roboczego aplikacji. Jest to, gdzie procesów można zapisywać dane. Zapisywanie danych w pakiecie kod lub kod podstawowy nie jest zalecane, ponieważ te foldery mogą być współużytkowane przez różne wystąpienia aplikacji i może być usunięty. Aby uzyskać więcej informacji, zobacz [WorkingFolder — Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage Element
Deklaruje folderem o nazwie określonej przez atrybut Name w obszarze PackageRoot, który zawiera plik Settings.xml. Ten plik zawiera sekcje ustawień zdefiniowanych przez użytkownika, pary klucz wartość pary, które ten proces może odczytywać Wstecz w czasie wykonywania. Podczas uaktualniania Jeśli istnieją tylko w wersji ConfigPackage został zmieniony, następnie uruchomiony proces nie zostanie ponownie uruchomiony. Zamiast tego wywołania zwrotnego powiadamia procesu, które uległy zmianie ustawień konfiguracji, aby dynamicznie załadowania. Aby uzyskać więcej informacji, zobacz [ConfigPackage — Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage Element
Deklaruje folderem o nazwie określonej przez atrybut Name w obszarze PackageRoot, który zawiera pliki danych statycznych do użycia przez proces w czasie wykonywania. Usługa Service Fabric będzie Odtwórz wszystkich plików exe i DLLHOSTs określone w pakietach hosta i pomocy technicznej, po uaktualnieniu żadnych pakietów danych, wymienione w manifeście usługi. Aby uzyskać więcej informacji, zobacz [DataPackage — Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Element zasobów
W tym artykule opisano zasoby używane przez tę usługę, która może być zadeklarowana bez konieczności modyfikowania kodu skompilowanego i zmieniać w przypadku wdrażania usługi. Dostęp do tych zasobów jest kontrolowany za pośrednictwem podmiotów i zasad części manifestu aplikacji. Aby uzyskać więcej informacji, zobacz [elementu zasobów](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element punktów końcowych
Definiuje punkty końcowe usługi. Aby uzyskać więcej informacji, zobacz [Element punktów końcowych](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Punkt końcowy elementu
Punkt końcowy, zadeklarowane w manifeście usługi, aby zastąpić. Aby uzyskać więcej informacji, zobacz [elementu punktu końcowego](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

