---
title: Opisujący usług i aplikacji usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak manifestów są używane do opisywania, usług i aplikacji usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/19/2018
ms.author: atsenthi
ms.openlocfilehash: 5e93bb3b206fbef6beb09b7aca6df0742a80ccf1
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662146"
---
# <a name="service-fabric-application-and-service-manifests"></a>Manifesty aplikacji usługi Service Fabric i usługi
W tym artykule opisano, jak aplikacje usługi Service Fabric i usługi są zdefiniowane i kontrolą wersji, przy użyciu plików ApplicationManifest.xml i ServiceManifest.xml.  Aby uzyskać bardziej szczegółowe przykłady, zobacz [aplikacji i usługi manifestu przykłady](service-fabric-manifest-examples.md).  Schemat XML dla tych plików manifestu jest udokumentowany w [dokumentacja schematu ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

> [!WARNING]
> Schemat do pliku manifestu XML wymusza poprawne kolejność elementów podrzędnych.  Jako obejście częściowe należy otworzyć "C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd" w programie Visual Studio podczas tworzenia lub modyfikowania dowolnego manifestów usługi Service Fabric. To umożliwi Sprawdź uporządkowanie elementów podrzędnych i zapewnia sprzężeniami.

## <a name="describe-a-service-in-servicemanifestxml"></a>Opisz usługę w ServiceManifest.xml
Manifest usługi definiuje sposób deklaratywny usługi typu i wersji. Określa metadane usługi, takie jak typ usługi, właściwości kondycji, równoważenie obciążenia metryki, pliki binarne usługi i plików konfiguracji.  Innymi słowy, opisano w nim pakiety kodu, konfiguracji i danych, wchodzących w skład pakietu usług do obsługi co najmniej jeden typ usługi. Manifest usługi może zawierać wiele kodu, konfiguracji i pakiety danych, które mogą być poddany kontroli wersji niezależnie. Oto manifest usługi dla usługi frontonu sieci web platformy ASP.NET Core z [przykładową aplikację głosowania](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (a Oto niektóre [bardziej szczegółowymi przykładami](service-fabric-manifest-examples.md)):

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
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

**Wersja** atrybuty są ciągami bez określonej struktury i nie są analizowane przez system. Wersja atrybuty są używane do wersji każdy składnik do uaktualnienia.

**ServiceTypes** oświadcza, jakie usługi są obsługiwane przez **CodePackages** w tym manifeście. Gdy usługa zostanie uruchomiony na jednym z tych typów usług, wszystkie pakiety kodu zadeklarowany w tym manifeście zostaną aktywowane przez uruchomienie ich punkty wejścia. Wynikowy procesy powinny zarejestrować obsługiwanych typów usług w czasie wykonywania. Typy usług są zadeklarowane na poziomie manifestu, a nie na poziomie pakietu kodu. Dlatego w przypadku wielu pakietów kodu ich wszystkich aktywacji zawsze wtedy, gdy system wyszukuje jeden z typów zadeklarowane usług.

Plik wykonywalny określony przez **punktu wejścia** jest zazwyczaj długotrwałych hosta usługi. **SetupEntryPoint** jest punktem wejścia uprzywilejowanych, który jest uruchamiany z tymi samymi poświadczeniami co Usługa Service Fabric (zazwyczaj *LocalSystem* konta) przed innymi punktu wejścia.  Obecność punktu wejścia Instalatora oddzielne pozwala na uniknięcie konieczności uruchamiania hosta usługi z wysokim poziomem uprawnień na dłuższy czas. Plik wykonywalny określony przez **punktu wejścia** po zakończeniu **SetupEntryPoint** kończy się pomyślnie. Jeśli ten proces nigdy nie kończy się lub ulega awarii, proces wynikowy jest monitorowana i ponownego uruchomienia (począwszy od ponownie **SetupEntryPoint**).  

Typowe scenariusze użycia **SetupEntryPoint** są podczas uruchomienia pliku wykonywalnego przed uruchomieniem usługi lub w trakcie operacji z podwyższonym poziomem uprawnień. Na przykład:

* Konfigurowanie i inicjując zmienne środowiskowe, które wymaga pliku wykonywalnego usługi. To nie jest ograniczona do tylko pliki wykonywalne napisane przy użyciu modeli programowania usługi Service Fabric. Na przykład npm.exe musi mieć pewne zmienne środowiskowe skonfigurowane do wdrażania aplikacji node.js.
* Konfigurowanie kontroli dostępu, instalując certyfikatów zabezpieczeń.

Aby uzyskać więcej informacji na temat konfigurowania SetupEntryPoint, zobacz [Skonfiguruj zasady dla punktu wejścia usługi Instalatora](service-fabric-application-runas-security.md)

**EnvironmentVariables** (innej wartości w powyższym przykładzie) zawiera listę zmiennych środowiskowych, które są ustawione dla tego pakietu kodu. Zmienne środowiskowe można przesłonić w `ApplicationManifest.xml` zapewniają różne wartości dla wystąpień innej usługi. 

**DataPackage** (innej wartości w powyższym przykładzie) oświadcza, folderem o nazwie określonej przez **nazwa** atrybut, który zawiera dowolne dane statyczne do użycia przez proces w czasie wykonywania.

**ConfigPackage** deklaruje folderem o nazwie określonej przez **nazwa** atrybut, który zawiera *Settings.xml* pliku. Plik ustawień zawiera sekcje ustawień zdefiniowanych przez użytkownika, pary klucz wartość pary, które proces odczytuje Wstecz w czasie wykonywania. Podczas uaktualniania, jeśli jest to jedyna **ConfigPackage** **wersji** uległ zmianie, a następnie uruchomiony proces nie zostanie uruchomiona ponownie. Zamiast tego wywołania zwrotnego powiadamia procesu, które uległy zmianie ustawień konfiguracji, aby dynamicznie załadowania. Oto przykład *Settings.xml* pliku:

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

Usługa Service Fabric **punktu końcowego** znajduje się przykład w przypadku zasobów sieci szkieletowej usług; Zasób usługi Service Fabric może być zadeklarowana/zmienić bez konieczności zmieniania kodu skompilowanego. Dostęp do zasobów usługi Service Fabric, które są określone w manifeście usługi mogą być kontrolowane za pośrednictwem **SecurityGroup** w manifeście aplikacji. Gdy zasób punktu końcowego jest zdefiniowany w manifeście usługi, usługi Service Fabric przypisuje porty z zakresu portów zarezerwowanych aplikacji, jeśli port nie jest jawnie określona. Przeczytaj więcej na temat [określenie lub punkt końcowy zasoby przesłaniające](service-fabric-service-manifest-resources.md).


<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Opis aplikacji w ApplicationManifest.xml
Manifest aplikacji zawiera opis deklaratywne, typ i wersja aplikacji. Określa usługę kompozycji metadane, takie jak nazwy stabilny, partycjonowanie schemat, współczynnik liczby/replikacji wystąpienia, zasady zabezpieczeń i izolacji, ograniczeniami dotyczącymi umieszczania, konfiguracji zastąpień i typów usług składowych. Opisano również domen równoważenia obciążenia, w którym znajduje się aplikacja.

W związku z tym manifest aplikacji zawiera opis elementów na poziomie aplikacji i odwołuje się do co najmniej jeden manifestów usługi do redagowania typu aplikacji. Oto manifest aplikacji dla [przykładową aplikację głosowania](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (a Oto niektóre [bardziej szczegółowymi przykładami](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
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
</ApplicationManifest>
```

Manifestów usługi, takie jak **wersji** atrybuty są ciągami bez określonej struktury i nie są analizowane przez system. Wersja atrybuty są również używane do wersji każdy składnik do uaktualnienia.

**Parametry** definiuje parametry używane w manifeście aplikacji. Wartości tych parametrów można podać, gdy aplikacja zostanie uruchomiony i można zastąpić aplikacji lub ustawienia konfiguracji usługi.  Domyślna wartość parametru jest używana, jeśli wartość nie ulega zmianie podczas tworzenia wystąpienia aplikacji. Aby dowiedzieć się, jak obsługa różnych aplikacji i parametrów usługi dla poszczególnych środowisk, zobacz [Zarządzanie parametry aplikacji dla wielu środowisk](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** zawiera odwołania do manifestów usługi, wchodzących w skład tego typu aplikacji. Manifest aplikacji może zawierać wiele importów manifestu usługi, każdy z nich mogą być poddany kontroli wersji niezależnie. Manifestów importowanych usługi określają, jakie usługi są prawidłowe w przypadku tego typu aplikacji. W ramach ServiceManifestImport możesz zastąpić wartości konfiguracji w Settings.xml i zmiennymi środowiskowymi w plikach ServiceManifest.xml. **Zasady** (nie ustawiono w powyższym przykładzie) dla powiązania punktu końcowego, zabezpieczeń i dostępu oraz pakiet udostępnianie można ustawić na manifestów importowanych usługi.  Aby uzyskać więcej informacji, zobacz [podczas konfigurowania zasad zabezpieczeń dla aplikacji](service-fabric-application-runas-security.md).

**DefaultServices** deklaruje wystąpień usług, które są tworzone automatycznie, gdy aplikacja zostanie uruchomiony dla tego typu aplikacji. Domyślne usługi są po prostu jako udogodnienie i zachowują się jak normalne usług pod każdym względem, po ich utworzeniu. One są uaktualniane razem z innymi usługami, w przypadku aplikacji i może zostać także usunięty. Manifest aplikacji może zawierać wiele usług domyślnych.

**Certyfikaty** (innej wartości w powyższym przykładzie) oświadcza, certyfikaty używane do [skonfigurować punkty końcowe HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) lub [szyfrować klucze tajne w manifeście aplikacji](service-fabric-application-secret-management.md).

**Zasady** (innej wartości w powyższym przykładzie) w tym artykule opisano zbieranie dzienników [domyślnej Uruchom jako](service-fabric-application-runas-security.md), [kondycji](service-fabric-health-introduction.md#health-policies), i [dostępu zabezpieczeń](service-fabric-application-runas-security.md) zasady można ustawić w Poziom aplikacji.

**Jednostki** (nie ustawiono w powyższym przykładzie) opisano podmiotów zabezpieczeń (użytkowników lub grupy) wymagane do [uruchamiania usług i zasobów Usługa bezpiecznego](service-fabric-application-runas-security.md).  Jednostki są określone w **zasady** sekcje.





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie pakietu aplikacji](service-fabric-package-apps.md) i przypisz ją jako gotowe do wdrożenia.
- [Wdrażanie i usuwać aplikacje](service-fabric-deploy-remove-applications.md).
- [Skonfiguruj parametry i zmienne środowiskowe dla optymalizacji różne wystąpienia aplikacji](service-fabric-manage-multiple-environment-app-configuration.md).
- [Podczas konfigurowania zasad zabezpieczeń dla aplikacji](service-fabric-application-runas-security.md).
- [Konfigurowanie punktów końcowych HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Szyfrowanie kluczy tajnych w manifeście aplikacji](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



