---
title: Opisywanie aplikacji i usług sieci szkieletowej usługi Azure
description: W tym artykule opisano, jak manifesty są używane do opisywania aplikacji i usług sieci szkieletowej usług.
ms.topic: conceptual
ms.date: 8/12/2019
ms.openlocfilehash: 6014ef6a9b6ec810aafd5e5be96223b8ed92d576
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349963"
---
# <a name="service-fabric-application-and-service-manifests"></a>Manifesty aplikacji i usług sieci szkieletowej usług
W tym artykule opisano, jak aplikacje i usługi sieci szkieletowej usług są definiowane i wersjonowane przy użyciu plików ApplicationManifest.xml i ServiceManifest.xml.  Aby uzyskać bardziej szczegółowe przykłady, zobacz [przykłady manifestów aplikacji i usług](service-fabric-manifest-examples.md).  Schemat XML dla tych plików manifestu jest udokumentowany w [dokumentacji schematu ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

> [!WARNING]
> Schemat pliku XML manifestu wymusza prawidłową kolejność elementów podrzędnych.  Jako częściowe obejście otwórz "C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd" w programie Visual Studio podczas tworzenia lub modyfikowania dowolnego manifestu sieci szkieletowej usług. Pozwoli to sprawdzić kolejność elementów podrzędnych i zapewnia intelli-sense.

## <a name="describe-a-service-in-servicemanifestxml"></a>Opis usługi w pliku ServiceManifest.xml
Manifest usługi deklaratywnie definiuje typ usługi i wersję. Określa metadane usługi, takie jak typ usługi, właściwości kondycji, metryki równoważenia obciążenia, pliki binarne usługi i pliki konfiguracyjne.  Innymi słowy, opisano kod, konfigurację i pakiety danych, które tworzą pakiet usługi do obsługi jednego lub więcej typów usług. Manifest usługi może zawierać wiele pakietów kodu, konfiguracji i danych, które mogą być wersjona niezależnie. Oto manifest usługi dla ASP.NET Core usługi front-end sieci [web aplikacji przykładowej głosowania](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (i oto kilka bardziej szczegółowych [przykładów):](service-fabric-manifest-examples.md)

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

**Atrybuty wersji** są ciągami bez struktury i nie są analizowane przez system. Atrybuty wersji są używane do wersji każdego składnika dla uaktualnień.

**ServiceTypes** deklaruje, jakie typy usług są obsługiwane przez **Pakiety Kodu w** tym manifeście. Gdy usługa jest tworzone dla jednego z tych typów usług, wszystkie pakiety kodu zadeklarowane w tym manifeście są aktywowane przez uruchomienie ich punktów wejścia. Procesy wynikowe oczekuje się zarejestrować obsługiwane typy usług w czasie wykonywania. Typy usług są zadeklarowane na poziomie manifestu, a nie na poziomie pakietu kodu. Tak więc, gdy istnieje wiele pakietów kodu, wszystkie są aktywowane za każdym razem, gdy system szuka jednego z zadeklarowanych typów usług.

Plik wykonywalny określony przez **entrypoint** jest zazwyczaj długotrwałym hostem usługi. **SetupEntryPoint** to uprzywilejowany punkt wejścia, który działa z tymi samymi poświadczeniami co sieć szkieletowa usług (zazwyczaj konto *LocalSystem)* przed jakimkolwiek innym punktem wejścia.  Obecność oddzielnego punktu wejścia konfiguracji pozwala uniknąć konieczności uruchamiania hosta usługi z wysokimi uprawnieniami przez dłuższy czas. Plik wykonywalny określony przez **entrypoint** jest uruchamiany po pomyślnym zamknięciu **programu SetupEntryPoint.** Jeśli proces zostanie zakończony lub ulegnie awarii, wynikowy proces zostanie monitorowany i ponownie uruchomiony (zaczynając od nowa za pomocą **programu SetupEntryPoint).**  

Typowe scenariusze korzystania z **Programu SetupEntryPoint** są po uruchomieniu pliku wykonywalnego przed uruchomieniem usługi lub wykonaniu operacji z podwyższonym poziomem uprawnień. Przykład:

* Konfigurowanie i inicjowanie zmiennych środowiskowych, których potrzebuje plik wykonywalny usługi. Nie jest to ograniczone tylko do plików wykonywalnych napisanych za pośrednictwem modeli programowania sieci szkieletowej usług. Na przykład npm.exe potrzebuje niektórych zmiennych środowiskowych skonfigurowanych do wdrażania aplikacji node.js.
* Konfigurowanie kontroli dostępu przez instalowanie certyfikatów zabezpieczeń.

Aby uzyskać więcej informacji na temat konfigurowania programu SetupEntryPoint, zobacz [Konfigurowanie zasad dla punktu wejścia konfiguracji usługi](service-fabric-application-runas-security.md)

**EnvironmentVariables** (nie ustawiona w poprzednim przykładzie) zawiera listę zmiennych środowiskowych, które są ustawione dla tego pakietu kodu. Zmienne środowiskowe mogą być `ApplicationManifest.xml` zastąpione w celu zapewnienia różnych wartości dla różnych wystąpień usługi. 

**DataPackage** (nie ustawiona w poprzednim przykładzie) deklaruje folder o nazwie przez **Name** atrybut, który zawiera dowolne dane statyczne do korzystania przez proces w czasie wykonywania.

**ConfigPackage** deklaruje folder o nazwie według atrybutu **Nazwa,** który zawiera plik *Settings.xml.* Plik ustawień zawiera sekcje zdefiniowanych przez użytkownika ustawień pary klucz-wartość, które proces odczytuje w czasie wykonywania. Podczas uaktualniania, jeśli tylko **configPackage** **wersja** została zmieniona, a następnie uruchomiony proces nie jest ponownie uruchamiany. Zamiast tego wywołanie zwrotne powiadamia proces, że ustawienia konfiguracji zostały zmienione, dzięki czemu można je ponownie załadować dynamicznie. Oto przykładowy plik *Settings.xml:*

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

Punkt końcowy usługi sieci **szkieletowej** usług jest przykładem zasobu sieci szkieletowej usług. Zasób sieci szkieletowej usług można zadeklarować/zmienić bez zmiany skompilowanego kodu. Dostęp do zasobów sieci szkieletowej usług, które są określone w manifeście usługi mogą być kontrolowane za pośrednictwem **SecurityGroup** w manifeście aplikacji. Gdy zasób punktu końcowego jest zdefiniowany w manifeście usługi, usługa Sieci szkieletowej przypisuje porty z zakresu portów aplikacji zastrzeżonych, gdy port nie jest jawnie określony. Dowiedz się więcej o [określaniu lub zastępowaniu zasobów punktu końcowego](service-fabric-service-manifest-resources.md).

 
> [!WARNING]
> Zgodnie z projektem porty statyczne nie powinny pokrywać się z zakresem portów aplikacji określonym w ClusterManifest. Jeśli określisz port statyczny, przypisz go poza zakresem portów aplikacji, w przeciwnym razie spowoduje to konflikty portów. Wraz z wersją 6.5CU2 wydamy **ostrzeżenie zdrowotne,** gdy wykryjemy taki konflikt, ale pozwolimy, aby wdrożenie było kontynuowane z zachowaniem wysłanego 6.5. Możemy jednak uniemożliwić wdrożenie aplikacji z następnych głównych wersji.
>

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Opis aplikacji w pliku ApplicationManifest.xml
Manifest aplikacji deklaratywnie opisuje typ aplikacji i wersję. Określa metadane składu usługi, takie jak stabilne nazwy, schemat partycjonowania, współczynnik liczby/replikacji wystąpień, zasady zabezpieczeń/izolacji, ograniczenia umieszczania, zastąpienia konfiguracji i typy usług składowych. Opisano również domeny równoważenia obciążenia, w których aplikacja jest umieszczana.

W związku z tym manifest aplikacji opisuje elementy na poziomie aplikacji i odwołuje się do jednego lub więcej manifestów usługi do tworzenia typu aplikacji. Oto manifest wniosku dla [wniosku o głosowanie próbki](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (i oto kilka bardziej szczegółowych [przykładów):](service-fabric-manifest-examples.md)

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
         <PlacementConstraints>(NodeType==NodeType0)</PlacementConstraints
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

Podobnie jak manifesty **usługi, Atrybuty Version** są ciągami niestrukturalnymi i nie są analizowane przez system. Atrybuty wersji są również używane do wersji każdego składnika dla uaktualnień.

**Parametry** definiuje parametry używane w całym manifeście aplikacji. Wartości tych parametrów mogą być dostarczane, gdy aplikacja jest tworzone i można zastąpić ustawienia konfiguracji aplikacji lub usługi.  Domyślna wartość parametru jest używana, jeśli wartość nie zostanie zmieniona podczas tworzenia wystąpienia aplikacji. Aby dowiedzieć się, jak zachować różne parametry aplikacji i usługi dla poszczególnych środowisk, zobacz [Zarządzanie parametrami aplikacji dla wielu środowisk](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** zawiera odwołania do manifestów usługi, które tworzą ten typ aplikacji. Manifest aplikacji może zawierać wiele importu manifestu usługi, każdy z nich może być wersjona niezależnie. Importowane manifesty usług określają, jakie typy usług są prawidłowe w ramach tego typu aplikacji. W ramach servicemanifestImport należy zastąpić wartości konfiguracji w pliku Settings.xml i zmiennych środowiskowych w plikach ServiceManifest.xml. **Zasady** (nie ustawione w poprzednim przykładzie) dla powiązania punktu końcowego, zabezpieczeń i dostępu oraz udostępniania pakietów można ustawić w manifestach importowanej usługi.  Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad zabezpieczeń dla aplikacji](service-fabric-application-runas-security.md).

**DefaultServices** deklaruje wystąpienia usługi, które są tworzone automatycznie za każdym razem, gdy aplikacja jest tworzona dla tego typu aplikacji. Usługi domyślne są tylko wygodą i zachowują się jak normalne usługi pod każdym względem po ich utworzeniu. Są one uaktualniane wraz z innymi usługami w wystąpieniu aplikacji i mogą być również usuwane. Manifest aplikacji może zawierać wiele usług domyślnych.

**Certyfikaty** (niestawione w poprzednim przykładzie) deklarują certyfikaty używane do [konfigurowania punktów końcowych HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) lub [szyfrowania wpisów tajnych w manifeście aplikacji](service-fabric-application-secret-management.md).

**Ograniczenia umieszczania** to instrukcje definiujące, gdzie usługi powinny być uruchamiane. Te instrukcje są dołączone do poszczególnych usług, które można wybrać dla jednej lub więcej właściwości węzła. Aby uzyskać więcej informacji, zobacz [Ograniczenia umieszczania i składnia właściwości węzła](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#placement-constraints-and-node-property-syntax)

**Zasady** (nie ustawione w poprzednim przykładzie) opisuje kolekcję dziennika, [domyślne zasady uruchamiania](service-fabric-application-runas-security.md)jako , [kondycji](service-fabric-health-introduction.md#health-policies)i [zasad dostępu do zabezpieczeń,](service-fabric-application-runas-security.md) które mają być ustawione na poziomie aplikacji, w tym, czy usługi mają dostęp do środowiska uruchomieniowego sieci szkieletowej usług.

> [!NOTE] 
> Domyślnie aplikacje sieci szkieletowej usług mają dostęp do środowiska uruchomieniowego sieci szkieletowej usług w postaci punktu końcowego akceptującego żądania specyficzne dla aplikacji i zmiennych środowiskowych wskazujących ścieżki plików na hoście zawierającym pliki sieci szkieletowej i pliki specyficzne dla aplikacji . Należy rozważyć wyłączenie tego dostępu, gdy aplikacja obsługuje niezaufany kod (tj. kod, którego pochodzenie jest nieznane lub którego właściciel aplikacji wie, że nie jest bezpieczny do wykonania). Aby uzyskać więcej informacji, zobacz [najważniejsze wskazówki dotyczące zabezpieczeń w sieci szkieletowej usług](service-fabric-best-practices-security.md#platform-isolation). 
>

**Podmioty** (niestawiewające w poprzednim przykładzie) opisują podmioty zabezpieczeń (użytkowników lub grupy) wymagane do [uruchamiania usług i bezpiecznych zasobów usługi.](service-fabric-application-runas-security.md)  Podmioty są odwoływane w **sekcjach Zasady.**





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Następne kroki
- [Zapakuj aplikację](service-fabric-package-apps.md) i przygotuj ją do wdrożenia.
- [Wdrażanie i usuwanie aplikacji](service-fabric-deploy-remove-applications.md).
- [Skonfiguruj parametry i zmienne środowiskowe dla różnych wystąpień aplikacji](service-fabric-manage-multiple-environment-app-configuration.md).
- [Skonfiguruj zasady zabezpieczeń dla aplikacji](service-fabric-application-runas-security.md).
- [Konfigurowanie punktów końcowych HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Szyfrowanie wpisów tajnych w manifeście aplikacji](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



