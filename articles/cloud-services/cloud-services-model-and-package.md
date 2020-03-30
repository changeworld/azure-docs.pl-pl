---
title: Co to jest model usługi w chmurze i pakiet | Dokumenty firmy Microsoft
description: Zawiera opis modelu usługi w chmurze (csdef, cscfg) i pakietu (.cspkg) na platformie Azure
services: cloud-services
author: tanmaygore
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 32603f4ab33e020245861e5dc66d2ade545fa627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247490"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Co to jest model usługi w chmurze i jak go spakować?
Usługa w chmurze jest tworzona z trzech składników, definicji usługi *(csdef),* konfiguracji usługi *(cscfg)* i pakietu usług *(.cspkg)*. Pliki **ServiceDefinition.csdef** i **ServiceConfig.cscfg** są oparte na języku XML i opisują strukturę usługi w chmurze oraz sposób jej konfigurowania; model zwany zbiorczo. **ServicePackage.cspkg** to plik zip, który jest generowany z **ServiceDefinition.csdef** i między innymi zawiera wszystkie wymagane zależności oparte na binarnych. Platforma Azure tworzy usługę w chmurze zarówno z **servicepackage.cspkg,** jak i **ServiceConfig.cscfg**.

Gdy usługa w chmurze jest uruchomiona na platformie Azure, można ponownie skonfigurować go za pomocą pliku **ServiceConfig.cscfg,** ale nie można zmienić definicji.

## <a name="what-would-you-like-to-know-more-about"></a>O czym chciałbyś wiedzieć więcej?
* Chcę dowiedzieć się więcej o plikach [ServiceDefinition.csdef](#csdef) i [ServiceConfig.cscfg.](#cscfg)
* Już o tym wiem, podam [kilka przykładów](#next-steps) na temat tego, co mogę skonfigurować.
* Chcę utworzyć [ServicePackage.cspkg](#cspkg).
* Używam programu Visual Studio i chcę...
  * [Tworzenie usługi w chmurze][vs_create]
  * [Ponowne konfigurowanie istniejącej usługi w chmurze][vs_reconfigure]
  * [Wdrażanie projektu usługi w chmurze][vs_deploy]
  * [Pulpit zdalny w wystąpieniu usługi w chmurze][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>UsługaDefinicja.csdef
**Plik ServiceDefinition.csdef** określa ustawienia używane przez platformę Azure do konfigurowania usługi w chmurze. [Schemat definicji usługi Azure (plik csdef)](/previous-versions/azure/reference/ee758711(v=azure.100)) zapewnia dozwolony format pliku definicji usługi. W poniższym przykładzie przedstawiono ustawienia, które można zdefiniować dla ról sieci Web i pracownika:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Można odwołać się do [schematu definicji usługi](/previous-versions/azure/reference/ee758711(v=azure.100)) w celu lepszego zrozumienia schematu XML używanego w tym miejscu, jednak tutaj znajduje się szybkie wyjaśnienie niektórych elementów:

**Witryny**  
Zawiera definicje witryn sieci Web lub aplikacji sieci web, które są hostowane w usługach IIS7.

**Punkty wprowadzania**  
Zawiera definicje dla punktów końcowych, które są używane do kontaktu z usługą w chmurze.

**Punkty internalend**  
Zawiera definicje dla punktów końcowych, które są używane przez wystąpienia roli do komunikowania się ze sobą.

**Configurationsettings**  
Zawiera definicje ustawień dla funkcji określonej roli.

**Certyfikaty**  
Zawiera definicje certyfikatów, które są potrzebne dla roli. W przykładzie poprzedniego kodu pokazano certyfikat, który jest używany do konfiguracji usługi Azure Connect.

**Zasoby lokalne**  
Zawiera definicje zasobów magazynu lokalnego. Zasób magazynu lokalnego jest zastrzeżonym katalogiem w systemie plików maszyny wirtualnej, w którym jest uruchomione wystąpienie roli.

**Przywozu**  
Zawiera definicje importowanych modułów. W przykładzie poprzedniego kodu przedstawiono moduły dla usługi Podłączanie pulpitu zdalnego i usługi Azure Connect.

**Uruchamianie**  
Zawiera zadania, które są uruchamiane po uruchomieniu roli. Zadania są definiowane w pliku cmd lub pliku wykonywalnym.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>UsługaConfiguration.cscfg
Konfiguracja ustawień usługi w chmurze zależy od wartości w pliku **ServiceConfiguration.cscfg.** Należy określić liczbę wystąpień, które chcesz wdrożyć dla każdej roli w tym pliku. Wartości ustawień konfiguracji zdefiniowanych w pliku definicji usługi są dodawane do pliku konfiguracji usługi. Odciski palców dla wszystkich certyfikatów zarządzania, które są skojarzone z usługą w chmurze są również dodawane do pliku. [Schemat konfiguracji usługi Azure (plik cscfg)](/previous-versions/azure/reference/ee758710(v=azure.100)) zapewnia dozwolony format pliku konfiguracji usługi.

Plik konfiguracji usługi nie jest spakowany z aplikacją, ale jest przekazyany na platformę Azure jako oddzielny plik i jest używany do konfigurowania usługi w chmurze. Możesz przekazać nowy plik konfiguracji usługi bez ponownego rozmieszczania usługi w chmurze. Wartości konfiguracji usługi w chmurze można zmienić, gdy usługa w chmurze jest uruchomiona. W poniższym przykładzie przedstawiono ustawienia konfiguracji, które można zdefiniować dla ról Sieci Web i Pracownika:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Można odwołać się do [schematu konfiguracji usługi](/previous-versions/azure/reference/ee758710(v=azure.100)) w celu lepszego zrozumienia schematu XML używanego w tym miejscu, jednak tutaj znajduje się szybkie wyjaśnienie elementów:

**Wystąpienia**  
Konfiguruje liczbę uruchomionych wystąpień dla roli. Aby zapobiec potencjalnie niedostępności usługi w chmurze podczas uaktualniania, zaleca się wdrożenie więcej niż jednego wystąpienia ról związanych z sieci web. Wdrażając więcej niż jedno wystąpienie, przestrzegasz wytycznych zawartych w [umowie dotyczącej poziomu usług obliczeniowych platformy Azure (SLA),](https://azure.microsoft.com/support/legal/sla/)która gwarantuje 99,95% łączności zewnętrznej dla ról internetowych, gdy dwa lub więcej wystąpień roli jest wdrażanych dla usługi.

**Configurationsettings**  
Konfiguruje ustawienia dla uruchomionych wystąpień dla roli. Nazwa `<Setting>` elementów musi być zgodna z definicjami ustawień w pliku definicji usługi.

**Certyfikaty**  
Konfiguruje certyfikaty, które są używane przez usługę. W przykładzie poprzedniego kodu pokazano, jak zdefiniować certyfikat dla modułu RemoteAccess. Wartość atrybutu *odcisk palca* musi być ustawiona na odcisk palca certyfikatu do użycia.

<p/>

> [!NOTE]
> Odcisk palca certyfikatu można dodać do pliku konfiguracji za pomocą edytora tekstu. Lub wartość można dodać na karcie **Certyfikaty** na stronie **Właściwości** roli w programie Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definiowanie portów dla wystąpień roli
Platforma Azure zezwala tylko jeden punkt wejścia do roli sieci web. Oznacza to, że cały ruch odbywa się za pośrednictwem jednego adresu IP. Witryny sieci Web można skonfigurować do udostępniania portu, konfigurując nagłówek hosta, aby przekierować żądanie do właściwej lokalizacji. Można również skonfigurować aplikacje do nasłuchiwać dobrze znanych portów na adres IP.

W poniższym przykładzie przedstawiono konfigurację roli sieci web z witryną sieci Web i aplikacją sieci Web. Witryna sieci Web jest skonfigurowana jako domyślna lokalizacja wejścia na porcie 80, a aplikacje sieci web są skonfigurowane do odbierania żądań z alternatywnego nagłówka hosta o nazwie "mail.mysite.cloudapp.net".

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostHeader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Zmienianie konfiguracji roli
Można zaktualizować konfigurację usługi w chmurze, gdy jest uruchomiona na platformie Azure, bez przekładzania usługi w tryb offline. Aby zmienić informacje o konfiguracji, można przekazać nowy plik konfiguracyjny lub edytować plik konfiguracyjny w miejscu i zastosować go do uruchomionej usługi. W konfiguracji usługi można ww ciągu następujących zmian:

* **Zmiana wartości ustawień konfiguracji**  
  Gdy zmienia się ustawienie konfiguracji, wystąpienie roli można zastosować zmiany, gdy wystąpienie jest w trybie online lub odtworzyć wystąpienie bezpiecznie i zastosować zmianę, gdy wystąpienie jest w trybie offline.
* **Zmiana topologii usług wystąpień ról**  
  Zmiany topologii nie mają wpływu na uruchomione wystąpienia, z wyjątkiem sytuacji, gdy wystąpienie jest usuwane. Wszystkie pozostałe wystąpienia zazwyczaj nie muszą być poddane recyklingowi; można jednak wybrać do odtwo oddać do recyklingu wystąpień roli w odpowiedzi na zmianę topologii.
* **Zmiana odcisku palca certyfikatu**  
  Certyfikat można zaktualizować tylko wtedy, gdy wystąpienie roli jest w trybie offline. Jeśli certyfikat zostanie dodany, usunięty lub zmieniony, gdy wystąpienie roli jest w trybie online, platforma Azure bezpiecznie przejmuje wystąpienie do trybu offline, aby zaktualizować certyfikat i przywrócić go do trybu online po zakończeniu zmiany.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Obsługa zmian konfiguracji za pomocą zdarzeń środowiska wykonawczego usługi
[Biblioteka środowiska wykonawczego platformy Azure](/previous-versions/azure/reference/mt419365(v=azure.100)) zawiera obszar nazw [Microsoft.WindowsAzure.ServiceRuntime,](/previous-versions/azure/reference/ee741722(v=azure.100)) który udostępnia klasy do interakcji ze środowiskiem platformy Azure z roli. Klasa [RoleEnvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) definiuje następujące zdarzenia, które są wywoływane przed i po zmianie konfiguracji:

* **[Zmiana](/previous-versions/azure/reference/ee758134(v=azure.100)) zdarzenia**  
  Dzieje się tak, zanim zmiana konfiguracji zostanie zastosowana do określonego wystąpienia roli, co daje szansę na zdejmanie wystąpień roli, jeśli to konieczne.
* **[Zmieniono](/previous-versions/azure/reference/ee758129(v=azure.100)) zdarzenie**  
  Występuje po zastosowaniu zmiany konfiguracji do określonego wystąpienia roli.

> [!NOTE]
> Ponieważ zmiany certyfikatu zawsze biorą wystąpienia roli w tryb offline, nie podnoszą zdarzeń RoleEnvironment.Changing lub RoleEnvironment.Changed.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>Pakiet usług.cspkg
> [!NOTE]
> Maksymalny rozmiar pakietu, który można wdrożyć, to 600 MB

Aby wdrożyć aplikację jako usługę w chmurze na platformie Azure, należy najpierw spakować aplikację w odpowiednim formacie. Za pomocą narzędzia wiersza polecenia **CSPack** (zainstalowanego przy użyciu [zestawu Azure SDK)](https://azure.microsoft.com/downloads/)można utworzyć plik pakietu jako alternatywę dla programu Visual Studio.

**CSPack** używa zawartości pliku definicji usługi i pliku konfiguracji usługi do definiowania zawartości pakietu. **CsPack** generuje plik pakietu aplikacji (.cspkg), który można przekazać na platformę Azure przy użyciu [portalu Azure](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Domyślnie pakiet ma `[ServiceDefinitionFileName].cspkg`nazwę , ale można określić `/out` inną nazwę, korzystając z opcji **CSPack**.

**CSPack** znajduje się pod adresem  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> Program CSPack.exe (w systemie Windows) jest dostępny po uruchomieniu skrótu **wiersza polecenia platformy Microsoft Azure** zainstalowanego przy zestaw SDK.  
> 
> Uruchom program CSPack.exe samodzielnie, aby zobaczyć dokumentację dotyczącą wszystkich możliwych przełączników i poleceń.
> 
> 

<p />

> [!TIP]
> Uruchom usługę w chmurze lokalnie w **emulatorze obliczeń platformy Microsoft Azure**, użyj **/copyonly** opcji. Ta opcja kopiuje pliki binarne aplikacji do układu katalogu, z którego można je uruchomić w emulatorze obliczeniowym.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Przykładowe polecenie do pakowania usługi w chmurze
Poniższy przykład tworzy pakiet aplikacji, który zawiera informacje dla roli sieci web. Polecenie określa plik definicji usługi do użycia, katalog, w którym można znaleźć pliki binarne, oraz nazwę pliku pakietu.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Jeśli aplikacja zawiera zarówno rolę sieci web, jak i rolę procesu roboczego, używane jest następujące polecenie:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

W przypadku gdy zmienne są zdefiniowane w następujący sposób:

| Zmienna | Wartość |
| --- | --- |
| \[Directoryname\] |Podkatalog w katalogu projektu głównego, który zawiera plik csdef projektu platformy Azure. |
| \[Definicja usługi\] |Nazwa pliku definicji usługi. Domyślnie ten plik nosi nazwę ServiceDefinition.csdef. |
| \[Outputfilename\] |Nazwa wygenerowanego pliku pakietu. Zazwyczaj jest to ustawione na nazwę aplikacji. Jeśli nazwa pliku nie jest określona, \[pakiet\]aplikacji jest tworzony jako ApplicationName .cspkg. |
| \[RoleName\] |Nazwa roli zdefiniowanej w pliku definicji usługi. |
| \[RoleBinariesDirectory] |Lokalizacja plików binarnych dla roli. |
| \[Virtualpath\] |Katalogi fizyczne dla każdej ścieżki wirtualnej zdefiniowane w sekcji Lokacje definicji usługi. |
| \[PhysicalPath\] |Katalogi fizyczne zawartości dla każdej ścieżki wirtualnej zdefiniowanej w węźle lokacji definicji usługi. |
| \[Nazwa roli\] |Nazwa pliku binarnego dla roli. |

## <a name="next-steps"></a>Następne kroki
Tworzym pakiet usług w chmurze i chcę...

* [Konfigurowanie pulpitu zdalnego dla wystąpienia usługi w chmurze][remotedesktop]
* [Wdrażanie projektu usługi w chmurze][deploy]

Używam programu Visual Studio i chcę...

* [Tworzenie nowej usługi w chmurze][vs_create]
* [Ponowne konfigurowanie istniejącej usługi w chmurze][vs_reconfigure]
* [Wdrażanie projektu usługi w chmurze][vs_deploy]
* [Konfigurowanie pulpitu zdalnego dla wystąpienia usługi w chmurze][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md



