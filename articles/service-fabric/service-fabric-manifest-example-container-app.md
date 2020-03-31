---
title: Przykłady manifestów aplikacji kontenera usługi Azure Service Fabric
description: Dowiedz się, jak skonfigurować ustawienia manifestu aplikacji i usługi dla aplikacji sieci szkieletowej wielu kontenerów.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 5efdbd17db20e69ace33b379ddbb99b2c4a20e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258397"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Przykłady manifestu wielokontenerowej aplikacji i usługi
Poniżej przedstawiono przykłady manifestów aplikacji i usługi dla aplikacji sieci szkieletowej wielu kontenerów. Celem tych przykładów jest pokazanie, jakie ustawienia są dostępne i jak z nich korzystać. Te manifesty aplikacji i usługi są oparte na przykładowych manifestach [kontenera systemu Windows Server 2016.](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows)

Wyświetlane są następujące funkcje:

|Manifest|Funkcje|
|---|---|
|[Manifest aplikacji](#application-manifest)| [zastępowanie zmiennych środowiskowych,](service-fabric-get-started-containers.md#configure-and-set-environment-variables) [konfigurowanie mapowania port-host kontenera,](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery) [konfigurowanie uwierzytelniania rejestru kontenerów,](service-fabric-get-started-containers.md#configure-container-repository-authentication) [zarządzania zasobami,](service-fabric-resource-governance.md) [ustawianie trybu izolacji,](service-fabric-get-started-containers.md#configure-isolation-mode) [określanie obrazów kontenerów specyficznych dla kompilacji systemu operacyjnego](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[Manifest usługi Usługi FrontEndService](#frontendservice-service-manifest)| [ustawianie zmiennych środowiskowych,](service-fabric-get-started-containers.md#configure-and-set-environment-variables) [konfigurowanie punktu końcowego,](service-fabric-get-started-containers.md#configure-communication)przekazywanie poleceń do kontenera, [importowanie certyfikatu do kontenera](service-fabric-securing-containers.md)| 
|[Manifest usługi backendservice](#backendservice-service-manifest)|[ustawianie zmiennych środowiskowych](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [konfigurowanie punktu końcowego,](service-fabric-get-started-containers.md#configure-communication) [konfigurowanie sterownika woluminu](service-fabric-containers-volume-logging-drivers.md)| 

Zobacz [Elementy manifestu aplikacji,](#application-manifest-elements) [elementy manifestu usługi FrontEndService](#frontendservice-service-manifest-elements)i [elementy manifestu usługi BackEndService, aby](#backendservice-service-manifest-elements) uzyskać więcej informacji na temat określonych elementów XML.

## <a name="application-manifest"></a>Manifest aplikacji

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>Manifest usługi Usługi FrontEndService

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>Manifest usługi backendservice

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
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

### <a name="policies-element"></a>Element zasad
W tym artykule opisano zasady (powiązanie punktu końcowego, udostępnianie pakietów, uruchamianie i dostęp do zabezpieczeń) do zastosowania w manifeście importowanej usługi. Aby uzyskać więcej informacji, zobacz [Element zasad](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy Element
Definiuje zasady ładu zasobów, które są stosowane na poziomie całego pakietu usług. Aby uzyskać więcej informacji, zobacz [ServicePackageResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Element polityki rządowej zasobów
Określa limity zasobów dla pakietu kodu. Aby uzyskać więcej informacji, zobacz [ResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies Element
Określa zasady aktywacji hostów kontenerów. Aby uzyskać więcej informacji, zobacz [ContainerHostPolicies Element](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>Element RepozytoriumCredentials
Poświadczenia dla repozytorium obrazów kontenera do ściągania obrazów z. Aby uzyskać więcej informacji, zobacz [Element RepozytoriumCredentials](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>Element wiązania portów
Określa, który zasób punktu końcowego ma być powiązany z portem kontenera narażony. Aby uzyskać więcej informacji, zobacz [PortBinding Element](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Element woluminu
Określa wolumin, który ma być powiązany z kontenerem. Aby uzyskać więcej informacji, zobacz [Element woluminu](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>Element opozycji sterownika
Opcje sterownika, które mają zostać przekazane kierowcy. Aby uzyskać więcej informacji, zobacz [DriverOption Element](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>ImageOverrides Element
Kontenery systemu Windows Server mogą nie być zgodne z różnymi wersjami systemu operacyjnego.  Można określić wiele obrazów systemu operacyjnego na kontener i oznaczyć je wersjami kompilacji systemu operacyjnego. Pobierz wersję kompilacji systemu operacyjnego, uruchamiając "winver" w wierszu polecenia systemu Windows. Jeśli bazowym systemem operacyjnym jest wersja kompilacji 16299 (windows server w wersji 1709), usługa sieci szkieletowej wybiera obraz kontenera z tagiem Os="16299". Zakłada się, że obraz kontenera bez znaczników działa we wszystkich wersjach systemu operacyjnego i zastępuje obraz określony w manifeście usługi. Aby uzyskać więcej informacji, zobacz [ImageOverrides Element](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Element obrazu
Obraz kontenera odpowiadający numerowi wersji kompilacji systemu operacyjnego, który ma zostać uruchomiony. Jeśli atrybut systemu operacyjnego nie jest określony, zakłada się, że obraz kontenera działa we wszystkich wersjach systemu operacyjnego i zastępuje obraz określony w manifeście usługi. Aby uzyskać więcej informacji, zobacz [Image Element](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>EnvironmentOverrides Element
 Aby uzyskać więcej informacji, zobacz [EnvironmentOverrides Element](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>Element zmienny środowiska
Zmienna środowiskowa. Aby uzyskać więcej informacji, zobacz [ŚrodowiskoRóżny Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>Element Odnośnika certyfikatu
Określa informacje o certyfikacie X509, który ma być narażony na działanie środowiska kontenera. Certyfikat musi być zainstalowany w magazynie LocalMachine wszystkich węzłów klastra.
Po uruchomieniu aplikacji środowisko wykonawcze odczytuje certyfikat i generuje plik PFX i hasło (w systemie Windows) lub plik PEM (w systemie Linux).
Plik PFX i hasło są dostępne w kontenerze przy użyciu zmiennych Certificates_ServicePackageName_CodePackageName_CertName_PFX i Certificates_ServicePackageName_CodePackageName_CertName_Password środowiska. Plik PEM jest dostępny w kontenerze przy użyciu zmiennych Certificates_ServicePackageName_CodePackageName_CertName_PEM i Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey środowiska. Aby uzyskać więcej informacji, zobacz [CertificateRef Element](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>Element Usługi domyślne
Deklaruje wystąpienia usługi, które są tworzone automatycznie za każdym razem, gdy aplikacja jest tworzona dla tego typu aplikacji. Aby uzyskać więcej informacji, zobacz [DefaultServices Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Element serwisu
Deklaruje usługę, która ma zostać utworzona automatycznie po utworzeniu aplikacji. Aby uzyskać więcej informacji, zobacz [Element usługi](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>Element usługi bezstanowej
Definiuje usługę bezstanową. Aby uzyskać więcej informacji, zobacz [StatelessService Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>Elementy manifestu usługi Usługi FrontEndService
### <a name="servicemanifest-element"></a>ServiceManifest Element
Deklaratywnie opisuje typ usługi i wersję. Wyświetla listę niezależnie uaktualnianych pakietów kodu, konfiguracji i danych, które razem tworzą pakiet usługi do obsługi jednego lub więcej typów usług. Zasoby, ustawienia diagnostyczne i metadane usługi, takie jak typ usługi, właściwości kondycji i metryki równoważenia obciążenia, są również określone. Aby uzyskać więcej informacji, zobacz [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Definiuje, jakie typy usług są obsługiwane przez CodePackage w tym manifeście. Gdy usługa jest tworzone dla jednego z tych typów usług, wszystkie pakiety kodu zadeklarowane w tym manifeście są aktywowane przez uruchomienie ich punktów wejścia. Typy usług są zadeklarowane na poziomie manifestu, a nie na poziomie pakietu kodu. Aby uzyskać więcej informacji, zobacz [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Element typu usługi bezstanowej
Opisuje typ usługi bezstanowej. Aby uzyskać więcej informacji, zobacz [StatelessServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Element pakietu kodu
W tym artykule opisano pakiet kodu, który obsługuje zdefiniowany typ usługi. Gdy usługa jest tworzone dla jednego z tych typów usług, wszystkie pakiety kodu zadeklarowane w tym manifeście są aktywowane przez uruchomienie ich punktów wejścia. Procesy wynikowe oczekuje się zarejestrować obsługiwane typy usług w czasie wykonywania. Gdy istnieje wiele pakietów kodu, wszystkie są aktywowane za każdym razem, gdy system szuka jednego z zadeklarowanych typów usług. Aby uzyskać więcej informacji, zobacz [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint Element
Plik wykonywalny określony przez entrypoint jest zazwyczaj długotrwałym hostem usługi. Obecność oddzielnego punktu wejścia konfiguracji pozwala uniknąć konieczności uruchamiania hosta usługi z wysokimi uprawnieniami przez dłuższy czas. Plik wykonywalny określony przez entrypoint jest uruchamiany po pomyślnym zamknięciu programu SetupEntryPoint. Wynikowy proces jest monitorowany i uruchamiany ponownie (zaczynając od nowa za pomocą programu SetupEntryPoint), jeśli kiedykolwiek zostanie zakończony lub ulegnie awarii. Aby uzyskać więcej informacji, zobacz [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost Element
 Aby uzyskać więcej informacji, zobacz [ContainerHost Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>Element nazwy obrazu
Repozytorium i [https://hub.docker.com](https://hub.docker.com) obraz na lub Azure Container Registry. Aby uzyskać więcej informacji, zobacz [ImageName Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables Element
Przekazywanie zmiennych środowiskowych do kontenera lub exe.  Aby uzyskać więcej informacji, zobacz [EnvironmentVariables Element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Element zmienny środowiska
Zmienna środowiskowa. Aby uzyskać więcej informacji, zobacz [ŚrodowiskoRóżny Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Element pakietu konfiguracyjnego
Deklaruje folder o nazwie według atrybutu Nazwa, który zawiera plik Settings.xml. Ten plik zawiera sekcje zdefiniowanych przez użytkownika ustawień pary klucz-wartość, które proces może odczytać w czasie wykonywania. Podczas uaktualniania, jeśli tylko configPackage wersja została zmieniona, a następnie uruchomiony proces nie jest ponownie uruchamiany. Zamiast tego wywołanie zwrotne powiadamia proces, że ustawienia konfiguracji zostały zmienione, dzięki czemu można je ponownie załadować dynamicznie. Aby uzyskać więcej informacji, zobacz [Element configPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Element pakietu danych
Deklaruje folder o nazwie według atrybutu Nazwa, który zawiera statyczne pliki danych. Sieci szkieletowej usług będzie odtwarzać wszystkie EXEs i DLLHOSTs określone w pakietach hosta i pomocy technicznej, gdy którykolwiek z pakietów danych wymienionych w manifeście usługi są uaktualniane. Aby uzyskać więcej informacji, zobacz [DataPackage Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Element zasobów
W tym artykule opisano zasoby używane przez tę usługę, które można zadeklarować bez modyfikowania skompilowanego kodu i zmieniane podczas wdrażania usługi. Dostęp do tych zasobów jest kontrolowany za pośrednictwem zasad i zasad sekcje manifestu aplikacji. Aby uzyskać więcej informacji, zobacz [Element zasobów](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element punktów końcowych
Definiuje punkty końcowe dla usługi. Aby uzyskać więcej informacji, zobacz [Element punktów końcowych](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element punktu końcowego
Aby uzyskać więcej informacji, zobacz [Element punktu końcowego](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>Elementy manifestu usługi BackEndService
### <a name="servicemanifest-element"></a>ServiceManifest Element
Deklaratywnie opisuje typ usługi i wersję. Wyświetla listę niezależnie uaktualnianych pakietów kodu, konfiguracji i danych, które razem tworzą pakiet usługi do obsługi jednego lub więcej typów usług. Zasoby, ustawienia diagnostyczne i metadane usługi, takie jak typ usługi, właściwości kondycji i metryki równoważenia obciążenia, są również określone. Aby uzyskać więcej informacji, zobacz [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Definiuje, jakie typy usług są obsługiwane przez CodePackage w tym manifeście. Gdy usługa jest tworzone dla jednego z tych typów usług, wszystkie pakiety kodu zadeklarowane w tym manifeście są aktywowane przez uruchomienie ich punktów wejścia. Typy usług są zadeklarowane na poziomie manifestu, a nie na poziomie pakietu kodu. Aby uzyskać więcej informacji, zobacz [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Element typu usługi bezstanowej
Opisuje typ usługi bezstanowej. Aby uzyskać więcej informacji, zobacz [StatelessServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Element pakietu kodu
W tym artykule opisano pakiet kodu, który obsługuje zdefiniowany typ usługi. Gdy usługa jest tworzone dla jednego z tych typów usług, wszystkie pakiety kodu zadeklarowane w tym manifeście są aktywowane przez uruchomienie ich punktów wejścia. Procesy wynikowe oczekuje się zarejestrować obsługiwane typy usług w czasie wykonywania. Gdy istnieje wiele pakietów kodu, wszystkie są aktywowane za każdym razem, gdy system szuka jednego z zadeklarowanych typów usług. Aby uzyskać więcej informacji, zobacz [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint Element
Plik wykonywalny określony przez entrypoint jest zazwyczaj długotrwałym hostem usługi. Obecność oddzielnego punktu wejścia konfiguracji pozwala uniknąć konieczności uruchamiania hosta usługi z wysokimi uprawnieniami przez dłuższy czas. Plik wykonywalny określony przez entrypoint jest uruchamiany po pomyślnym zamknięciu programu SetupEntryPoint. Wynikowy proces jest monitorowany i uruchamiany ponownie (zaczynając od nowa za pomocą programu SetupEntryPoint), jeśli kiedykolwiek zostanie zakończony lub ulegnie awarii. Aby uzyskać więcej informacji, zobacz [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost Element
Aby uzyskać więcej informacji, zobacz [ContainerHost Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>Element nazwy obrazu
Repozytorium i [https://hub.docker.com](https://hub.docker.com) obraz na lub Azure Container Registry. Aby uzyskać więcej informacji, zobacz [ImageName Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Commands, element
Przekaż rozdzielanych przecinków listę poleceń do kontenera. Aby uzyskać więcej informacji, zobacz [Element poleceń](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables Element
Przekazywanie zmiennych środowiskowych do kontenera lub exe.  Aby uzyskać więcej informacji, zobacz [EnvironmentVariables Element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Element zmienny środowiska
Zmienna środowiskowa. Aby uzyskać więcej informacji, zobacz [ŚrodowiskoRóżny Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Element pakietu konfiguracyjnego
Deklaruje folder o nazwie według atrybutu Nazwa, który zawiera plik Settings.xml. Ten plik zawiera sekcje zdefiniowanych przez użytkownika ustawień pary klucz-wartość, które proces może odczytać w czasie wykonywania. Podczas uaktualniania, jeśli tylko configPackage wersja została zmieniona, a następnie uruchomiony proces nie jest ponownie uruchamiany. Zamiast tego wywołanie zwrotne powiadamia proces, że ustawienia konfiguracji zostały zmienione, dzięki czemu można je ponownie załadować dynamicznie. Aby uzyskać więcej informacji, zobacz [Element configPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Element zasobów
W tym artykule opisano zasoby używane przez tę usługę, które można zadeklarować bez modyfikowania skompilowanego kodu i zmieniane podczas wdrażania usługi. Dostęp do tych zasobów jest kontrolowany za pośrednictwem zasad i zasad sekcje manifestu aplikacji. Aby uzyskać więcej informacji, zobacz [Element zasobów](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element punktów końcowych
Definiuje punkty końcowe dla usługi. Aby uzyskać więcej informacji, zobacz [Element punktów końcowych](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element punktu końcowego
 Aby uzyskać więcej informacji, zobacz [Element punktu końcowego](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

