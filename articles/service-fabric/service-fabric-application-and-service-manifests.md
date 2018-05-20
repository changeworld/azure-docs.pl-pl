---
title: Opisujące aplikacji usługi Azure Service Fabric i usług | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak manifestów służą do opisywania sieci szkieletowej usług aplikacji i usług.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: ryanwi
ms.openlocfilehash: b79206b9d456226d14984e8a1c1002c07c4f626a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-application-and-service-manifests"></a>Sieć szkieletowa usług aplikacji i manifestów usługi
W tym artykule opisano, jak sieć szkieletowa usług aplikacji i usług są zdefiniowane i wersji przy użyciu plików ApplicationManifest.xml i ServiceManifest.xml.  Schemat XML te pliki manifestu jest udokumentowany w [ServiceFabricServiceModel.xsd schematu dokumentacji](service-fabric-service-model-schema.md).

## <a name="describe-a-service-in-servicemanifestxml"></a>Opis usługi w pliku ServiceManifest.xml
Manifest usługi deklaratywnie definiuje typ usługi i wersji. Określa usługę metadane, takie jak typ usługi, właściwości kondycji metryki równoważenia obciążenia, pliki binarne usługi i pliki konfiguracyjne.  Innymi słowy, opisuje pakiety kodu, konfiguracji i danych, które tworzą pakietu usług do obsługi co najmniej jeden typ usługi. Manifest usługi może zawierać wiele kodu, konfiguracji i pakietów danych, co może mieć określonej wersji niezależnie. Oto usługi frontonu sieci web platformy ASP.NET Core dla manifest usługi [głosowanie w przykładowej aplikacji](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
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

**Wersja** atrybuty są ciągami bez struktury i nie zostały przeanalizowane przez system. Wersja atrybuty są używane do wersji każdego składnika dla uaktualnienia.

**Serivcetype** deklaruje, jakie typy usługi są obsługiwane przez **CodePackages** w tym manifeście. Usługa jest uruchomione na jednym z następujących typów usługi, wszystkie pakiety kodu zadeklarowany w tym manifeście zostaną aktywowane przez uruchomienie ich punktów wejścia. Wynikowa procesów oczekuje się zarejestrować obsługiwanych typów usług w czasie wykonywania. Typ usługi został zadeklarowany na poziomie manifestu i nie poziomu pakiet kodu. Dlatego w przypadku wielu pakietów kodu ich wszystkich aktywacji zawsze, gdy system wyszukuje jeden z typów usług zadeklarowane.

Plik wykonywalny określony przez **punktu wejścia** jest zazwyczaj długotrwałe hosta usługi. **SetupEntryPoint** punkt wejścia uprzywilejowanych uruchamiane przy użyciu poświadczeń usługi sieć szkieletowa (zazwyczaj *LocalSystem* konta) przed innymi punktu wejścia.  Obecność punktu wejścia instalacji oddzielnych pozwala uniknąć konieczności uruchamiania usługi hosta z wysokiego poziomu uprawnień przez dłuższy czas. Plik wykonywalny określony przez **punktu wejścia** po zakończeniu **SetupEntryPoint** kończy się pomyślnie. Jeśli kiedykolwiek proces kończy lub ulegnie awarii, proces wynikowy jest monitorowane i ponownego uruchomienia (począwszy od ponownie **SetupEntryPoint**).  

Typowe scenariusze korzystania **SetupEntryPoint** są podczas uruchomienia pliku wykonywalnego przed uruchomieniem usługi lub wykonać operację z podwyższonym poziomem uprawnień. Na przykład:

* Konfigurowanie i Inicjowanie zmiennych środowiskowych, które wymaga pliku wykonywalnego usługi. To nie jest ograniczona do tylko pliki wykonywalne napisane przez modele programowania sieci szkieletowej usług. Na przykład npm.exe musi niektóre zmienne środowiskowe skonfigurowane do wdrażania aplikacji node.js.
* Konfigurowanie kontroli dostępu, instalując certyfikaty zabezpieczeń.

Aby uzyskać więcej informacji na temat konfigurowania **SetupEntryPoint**, zobacz [skonfigurować zasady dla punktu wejścia instalacji usługi](service-fabric-application-runas-security.md)

**EnvironmentVariables** (nie ustawione w poprzednim przykładzie) zawiera listę zmiennych środowiskowych, które są ustawione dla tego pakietu kodu. Zmienne środowiskowe może zostać zastąpiona w `ApplicationManifest.xml` o podanie wartości różnych wystąpień innej usługi. 

**DataPackage** (nie ustawione w poprzednim przykładzie) deklaruje folder o nazwie **nazwa** atrybutu, zawierającego dowolne dane statyczne zużywanych przez proces w czasie wykonywania.

**Lokalizacji ConfigPackage** deklaruje folder o nazwie **nazwa** atrybut, który zawiera *Settings.xml* pliku. Plik ustawień zawiera sekcje ustawień pary klucz wartość, zdefiniowanych przez użytkownika, które proces odczytuje Wstecz w czasie wykonywania. Podczas uaktualniania, jeśli tylko **lokalizacji ConfigPackage** **wersji** została zmieniona, a następnie uruchomiony proces nie zostanie ponownie uruchomiony. Zamiast tego wywołania zwrotnego powiadamia proces, który zmieniono ustawienia konfiguracji, mogą być ładowane dynamicznie. Oto przykład *Settings.xml* pliku:

```xml
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

**Zasoby**, takich jak punkty końcowe, używanych przez usługę być zadeklarowany jako zmienione bez zmiany kodu skompilowanego.  Dostęp do zasobów, które są określone w manifeście usługi mogą być kontrolowane za pośrednictwem **SecurityGroup** w manifeście aplikacji.  Gdy **punktu końcowego** zasobu jest zdefiniowany w manifeście usługi, sieci szkieletowej usług przypisuje porty z zakresu portów zastrzeżone aplikacji, jeśli port nie jest jawnie określony.  Przeczytaj więcej na temat [Określanie lub przesłonięcie zasobów punktu końcowego](service-fabric-service-manifest-resources.md).


<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Opis aplikacji w pliku ApplicationManifest.xml
Manifest aplikacji zawiera opis deklaratywnie, typ i wersja aplikacji. Określa usługę kompozycji metadane, takie jak nazwy stabilny oraz schemat partycji, współczynnik liczby/replikacji wystąpienia, zasady zabezpieczeń i izolacji, ograniczenia umieszczania, operacje zastępowania konfiguracji, typów usług składowych. Opisano również domen równoważenia obciążenia, w których znajduje się aplikacja.

W związku z tym manifest aplikacji zawiera opis elementów na poziomie aplikacji i odwołuje się do co najmniej jeden manifestów usługi utworzenie typu aplikacji. Oto dla manifest aplikacji [głosowanie w przykładowej aplikacji](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

Manifesty usługi, takich jak **wersji** atrybuty są ciągami bez struktury i nie są analizowane przez system. Atrybuty wersji są również używane do wersji każdego składnika dla uaktualnienia.

**Parametry** definiuje parametry używane w manifeście aplikacji. Wartości tych parametrów można podać, gdy aplikacja jest instatiated, można zmienić ustawienia konfiguracji usługi lub aplikacji.  Zostanie użyta domyślna wartość parametru, jeśli wartość nie ulega zmianie podczas tworzenia wystąpienia aplikacji. Aby dowiedzieć się, jak należy dbać o różnych aplikacji i parametrów usługi dla poszczególnych środowisk, zobacz [Zarządzanie parametry aplikacji dla wielu środowiskach](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** zawiera odwołania do usługi manifestów, które tworzą ten typ aplikacji. Manifest aplikacji może zawierać wiele importów manifestu usługi, każdy z nich może być wersjonowany niezależnie. Manifesty importowanych usługi określają, jakie typy usługi są prawidłowe w ramach tego typu aplikacji. W ramach ServiceManifestImport można zastąpić wartości konfiguracji w pliku Settings.xml i środowiska zmiennych w plikach pliku ServiceManifest.xml. **Zasady** (nie ustawiono w poprzednim przykładzie) dla powiązania punktu końcowego, zabezpieczeń i dostępu i pakietu udostępnianie można ustawić na manifesty importowanych usługi.  Aby uzyskać więcej informacji, zobacz [konfigurowania zasad zabezpieczeń dla aplikacji](service-fabric-application-runas-security.md).

**DefaultServices** deklaruje wystąpień usług, które są tworzone automatycznie, gdy aplikacja zostanie uruchomiony przed tego typu aplikacji. Domyślne usługi są po prostu udogodnienie i zachowywać się jak normalne usług w każdym względem po ich utworzeniu. Uaktualnienia wraz z innych usług, w przypadku aplikacji, a można również usunąć. Manifest aplikacji może zawierać wiele usług domyślnych.

**Certyfikaty** (nie ustawione w poprzednim przykładzie) deklaruje certyfikaty używane do [ustawienia punktów końcowych HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) lub [zaszyfrowania kluczy tajnych w manifeście aplikacji](service-fabric-application-secret-management.md).

**Zasady** (nie ustawione w poprzednim przykładzie) opisano zbierania dzienników [domyślne Uruchom jako](service-fabric-application-runas-security.md), [kondycji](service-fabric-health-introduction.md#health-policies), i [dostęp zabezpieczeń](service-fabric-application-runas-security.md) zasady można ustawić na Poziom aplikacji.

**Podmioty zabezpieczeń** (nie ustawiono w poprzednim przykładzie) opisano podmiotów zabezpieczeń (użytkownicy lub grupy) wymagane do [uruchamiania usługi i zasoby Usługa bezpiecznego](service-fabric-application-runas-security.md).  Podmioty zabezpieczeń są wymienione w **zasady** sekcje.





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Kolejne kroki
- [Pakiet aplikacji](service-fabric-package-apps.md) i przygotowania do wdrożenia.
- [Wdrażanie i usunąć aplikacje](service-fabric-deploy-remove-applications.md).
- [Konfigurowanie parametrów i zmiennych środowiskowych dla różnych aplikacji wystąpień](service-fabric-manage-multiple-environment-app-configuration.md).
- [Konfigurowania zasad zabezpieczeń dla aplikacji](service-fabric-application-runas-security.md).
- [Ustawienia punktów końcowych HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Szyfrowanie kluczy tajnych w manifeście aplikacji](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



