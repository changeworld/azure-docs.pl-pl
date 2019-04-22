---
title: Co to jest usługa w chmurze model i pakiet | Dokumentacja firmy Microsoft
description: W tym artykule opisano modelem usługi chmury (.csdef, cscfg) i pakiet (cspkg) na platformie Azure
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 4ce2feb5-0437-496c-98da-1fb6dcb7f59e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 9c9f7dfd9ecbf085da19fc010e497caef8c18629
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58917315"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Co to jest model usługi w chmurze i jak jest pakiet?
Usługa w chmurze jest tworzona z trzech składników i definicji usługi *(csdef)*, konfiguracji usługi *(cscfg)*, a pakiet usługi *(cspkg)*. Zarówno **ServiceDefinition.csdef** i **ServiceConfig.cscfg** pliki są oparte na języku XML i opisz strukturę usługę w chmurze i sposobu jego konfiguracji; pod wspólną nazwą modelu. **ServicePackage.cspkg** plik zip, który jest generowany na podstawie **ServiceDefinition.csdef** i między innymi, zawiera wszystkie zależności wymagane na podstawie pliku binarnego. Platforma Azure tworzy usługi w chmurze z obu **ServicePackage.cspkg** i **ServiceConfig.cscfg**.

Gdy usługa w chmurze zostanie uruchomiona na platformie Azure, można ponownie skonfigurować ją przy użyciu **ServiceConfig.cscfg** plik, ale nie można zmienić definicji.

## <a name="what-would-you-like-to-know-more-about"></a>Co chcesz się dowiedzieć więcej?
* Chcę, aby dowiedzieć się więcej na temat [ServiceDefinition.csdef](#csdef) i [ServiceConfig.cscfg](#cscfg) plików.
* Wiem już o tym, można uzyskać [przykłady](#next-steps) na co można skonfigurować.
* Chcę utworzyć [ServicePackage.cspkg](#cspkg).
* Używam programu Visual Studio i chcę...
  * [Utwórz usługę w chmurze][vs_create]
  * [Ponownie skonfiguruj istniejącą usługę w chmurze][vs_reconfigure]
  * [Wdróż projekt usługi w chmurze][vs_deploy]
  * [Pulpit zdalny do wystąpienia usługi w chmurze][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
**ServiceDefinition.csdef** pliku określa ustawienia, które są używane przez platformę Azure do skonfigurowania usługi w chmurze. [Schematem definicji usługi platformy Azure (csdef pliku)](/previous-versions/azure/reference/ee758711(v=azure.100)) zapewnia dozwolony format pliku definicji usługi. Poniższy przykład przedstawia ustawienia, które mogą być definiowane dla ról sieć Web i proces roboczy:

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

Możesz zapoznać się z [schematem definicji usługi](/previous-versions/azure/reference/ee758711(v=azure.100)) w celu lepszego zrozumienia schematu XML, używany w tym miejscu, w tym miejscu jest jednak szybkie omówienie niektóre elementy:

**Lokacje**  
Zawiera definicje dla witryn sieci Web lub aplikacji sieci web, które są hostowane w usługach IIS7.

**InputEndpoints**  
Zawiera definicje dla punktów końcowych, które są używane do kontaktowania się z usługą w chmurze.

**InternalEndpoints**  
Zawiera definicje dla punktów końcowych, które są używane przez wystąpień roli do komunikowania się ze sobą.

**AppSettings**  
Zawiera definicje ustawienie funkcji określonej roli.

**Certyfikaty**  
Zawiera definicje dla certyfikatów, które są wymagane przez rolę. W poprzednim przykładzie kodu przedstawiono certyfikatu, który służy do konfiguracji połączenia platformy Azure.

**LocalResources**  
Zawiera definicje dla zasobów magazynu lokalnego. Zasób magazynu lokalnego jest zarezerwowane katalogu w systemie plików maszyny wirtualnej, w którym jest uruchomione wystąpienie roli.

**Importy**  
Zawiera definicje dla zaimportowanych modułów. W poprzednim przykładzie kodu pokazano moduły Podłączanie pulpitu zdalnego i Azure Connect.

**Uruchamianie**  
Zawiera zadania, które są uruchamiane podczas uruchamiania roli. Zadania są zdefiniowane w .cmd lub pliku wykonywalnego.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
Konfigurację ustawień dla usługi w chmurze jest określana przez wartości **ServiceConfiguration.cscfg** pliku. Określasz liczbę wystąpień, które mają zostać wdrożone dla każdej roli, w tym pliku. Wartości ustawienia konfiguracji, które zostały zdefiniowane w pliku definicji usługi są dodawane do pliku konfiguracji usługi. Odciski palców wszelkich certyfikaty zarządzania, które są skojarzone z usługą w chmurze są również dodawane do pliku. [Schematu konfiguracji usługi platformy Azure (cscfg pliku)](/previous-versions/azure/reference/ee758710(v=azure.100)) zapewnia dozwolony format pliku konfiguracji usługi.

Pliku konfiguracji usługi nie jest dostarczana razem z aplikacją, ale zostanie przekazany na platformę Azure w postaci oddzielnych plików i służy do konfigurowania usługi w chmurze. Możesz przekazać plik konfiguracji usługi bez konieczności ponownego wdrażania usługi w chmurze. Po uruchomieniu usługi w chmurze, można zmienić wartości konfiguracji dla usługi w chmurze. Poniższy przykład przedstawia ustawienia konfiguracji, które mogą być definiowane dla ról sieć Web i proces roboczy:

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

Możesz zapoznać się z [schematu konfiguracji usługi](/previous-versions/azure/reference/ee758710(v=azure.100)) dla lepiej poznać schematu XML, używany w tym miejscu, w tym miejscu jest jednak szybkie Omówienie elementów:

**Wystąpienia**  
Umożliwia skonfigurowanie liczby uruchomionych wystąpień roli. Aby uniemożliwić potencjalnie stają się niedostępne podczas uaktualniania usługi w chmurze, zaleca się wdrożenie więcej niż jedno wystąpienie roli przeznaczonych dla sieci web. Przez wdrożenie więcej niż jedno wystąpienie, przestrzega wytycznych w [obliczeń usługa umowy dotyczącej poziomu (SLA)](https://azure.microsoft.com/support/legal/sla/), która gwarantuje łączność zewnętrzną przez 99,95% czasu dla ról dostępnego z Internetu, gdy dwie lub więcej wystąpień roli wdrożona usługa.

**AppSettings**  
Konfiguruje ustawienia dla uruchomionych wystąpień roli. Nazwa `<Setting>` elementów musi odpowiadać definicji ustawień w pliku definicji usługi.

**Certyfikaty**  
Służy do konfigurowania certyfikatów, które są używane przez usługę. W poprzednim przykładzie kodu pokazano, jak zdefiniować certyfikat dla modułu RemoteAccess. Wartość *odcisk palca* atrybutu musi być równa odcisk palca certyfikatu do użycia.

<p/>

> [!NOTE]
> Odcisk palca certyfikatu można dodać do pliku konfiguracji za pomocą edytora tekstu. Lub można dodać wartość **certyfikaty** karcie **właściwości** strony roli w programie Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definiowanie porty dla wystąpień roli
Platforma Azure umożliwia tylko jeden wpis punktu do roli sieci web. Co oznacza, że cały ruch odbywa się za pośrednictwem jednego adresu IP. Można skonfigurować witryny sieci Web, aby udostępnić portu, konfigurując nagłówek hosta, aby kierować żądania do poprawnej lokalizacji. Można również skonfigurować aplikacje, aby nasłuchiwać dobrze znanych portów pod adresem IP.

Poniższy przykład pokazuje konfiguracji dla roli sieci web, aplikacji sieci web i witryny sieci Web. Witryna sieci Web jest skonfigurowany jako domyślna lokalizacja zapisu na porcie 80, a aplikacje sieci web są skonfigurowane do odbierania żądań z nagłówka alternatywnego hosta, który jest nazywany "mail.mysite.cloudapp.net".

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
      <Binding name="mail" endpointName="HttpIn" hostheader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Zmiana konfiguracji roli
Można zaktualizować konfiguracji usługi w chmurze, jest uruchomiona na platformie Azure, bez przełączania w tryb offline usługa. Aby zmienić informacje o konfiguracji, możesz można przekazać nowy plik konfiguracji lub edytować plik konfiguracji w miejscu i zastosować je do uruchomionej usługi. Mogą być wprowadzone następujące zmiany w konfiguracji usługi:

* **Zmiana wartości ustawienia konfiguracji**  
  Podczas konfiguracji, zmiany ustawień, wystąpienie roli można zastosować zmiany, gdy wystąpienie jest w trybie online lub bez problemu zmieniała odtwarzanie wystąpienia, a następnie zastosować zmiany podczas wystąpienie jest w trybie offline.
* **Zmienianie topologii usługi wystąpień roli**  
  Topologia zmiany nie wpływają na uruchomione wystąpienia, z wyjątkiem sytuacji, gdy wystąpienie jest usuwana. Wszystkie pozostałe wystąpienia ogólnie nie muszą zostać odtworzone; można jednak odzyskać wystąpień roli w odpowiedzi na zmiany topologii.
* **Zmiana odcisk palca certyfikatu**  
  Certyfikat można aktualizować tylko wtedy, gdy wystąpienie roli jest w trybie offline. Jeśli certyfikat jest dodane, usunięte lub zmodyfikowane, podczas gdy wystąpienie roli jest w trybie online, Azure bez problemu zmieniała trwa wystąpienia w tryb offline zaktualizuj certyfikat i przełączyć do trybu online po zakończeniu zmiany.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Obsługa zmian w konfiguracji o zdarzeniach środowiska uruchomieniowego usługi
[Biblioteki środowiska uruchomieniowego usługi Azure](/previous-versions/azure/reference/mt419365(v=azure.100)) obejmuje [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee741722(v=azure.100)) przestrzeń nazw, która udostępnia klasy do interakcji ze środowiskiem platformy Azure z roli. [RoleEnvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) klasa definiuje następujące zdarzenia, które są wywoływane przed i po zmianie konfiguracji:

* **[Zmiana](/previous-versions/azure/reference/ee758134(v=azure.100)) zdarzeń**  
  Dzieje się tak, zanim zmiana konfiguracji została zastosowana do określonego wystąpienia roli, dając Ci w walce z wystąpień roli, jeśli to konieczne.
* **[Zmienione](/previous-versions/azure/reference/ee758129(v=azure.100)) zdarzeń**  
  Występuje po zmianie konfiguracji do określonego wystąpienia roli.

> [!NOTE]
> Ponieważ zmiany certyfikatu zawsze pobierają wystąpień roli w trybie offline, nie podnieść RoleEnvironment.Changing lub RoleEnvironment.Changed zdarzeń.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Aby wdrożyć aplikację jako usługa w chmurze na platformie Azure, najpierw należy utworzyć pakiet aplikacji w odpowiednim formacie. Możesz użyć **CSPack** narzędzie wiersza polecenia (zainstalowany za pomocą [zestawu Azure SDK](https://azure.microsoft.com/downloads/)) można utworzyć pliku pakietu jako alternatywa dla programu Visual Studio.

**CSPack** zawartość pliku definicji usługi i pliku konfiguracji usługi jest używana do definiowania zawartości pakietu. **CSPack** generuje plik pakietu aplikacji (cspkg), możesz przekazać na platformę Azure za pomocą [witryny Azure portal](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Domyślnie pakiet o nazwie `[ServiceDefinitionFileName].cspkg`, ale można określić inną nazwę, za pomocą `/out` opcji **CSPack**.

**CSPack** znajduje się w folderze  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (w systemie windows) jest dostępna, uruchamiając **wiersza polecenia platformy Azure Microsoft** skrót, który został zainstalowany przy użyciu zestawu SDK.  
> 
> Uruchom CSPack.exe program samodzielnie zobacz dokumentację dotyczącą wszystkich możliwych przełączników i poleceń.
> 
> 

<p />

> [!TIP]
> Uruchamianie usługi w chmurze lokalnie w **emulatora obliczeń Azure Microsoft**, użyj **/copyonly** opcji. Ta opcja powoduje skopiowanie plików binarnych aplikacji do układu katalogów, w którym może działać w emulatorze obliczeń.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Przykładowe polecenie, aby utworzyć pakiet usługi w chmurze
Poniższy przykład tworzy pakiet aplikacji, który zawiera informacje dla roli sieci web. Polecenie Określa plik definicji usługi, aby używać katalogu, gdzie można znaleźć plików binarnych, a nazwa pliku pakietu.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Jeśli aplikacja zawiera rolę sieci web i roli procesu roboczego, służy następujące polecenie:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Gdy zmienne są zdefiniowane w następujący sposób:

| Zmienna | Wartość |
| --- | --- |
| \[DirectoryName\] |Podkatalog używany w katalogu głównym projektu, który zawiera plik csdef projektu platformy Azure. |
| \[ServiceDefinition\] |Nazwa pliku definicji usługi. Domyślnie ten plik ma nazwę ServiceDefinition.csdef. |
| \[OutputFileName\] |Nazwa pliku wygenerowanego pakietu. Zazwyczaj ustawiono na nazwę aplikacji. Jeśli nazwa pliku nie jest określony, pakiet aplikacji jest tworzona jako \[ApplicationName\]cspkg. |
| \[RoleName\] |Nazwa roli, zgodnie z definicją w pliku definicji usługi. |
| \[RoleBinariesDirectory] |Lokalizacja plików binarnych roli. |
| \[VirtualPath\] |Katalogi fizyczne dla każdej ścieżki wirtualnej zdefiniowane w sekcji witryn w definicji usługi. |
| \[PhysicalPath\] |Katalogi fizyczne zawartości dla każdej ścieżki wirtualnej zdefiniowany w węźle lokacji definicji usługi. |
| \[RoleAssemblyName\] |Nazwa pliku binarnego dla roli. |

## <a name="next-steps"></a>Kolejne kroki
Będę tworzyć pakiet usługi w chmurze i chcę...

* [Konfigurowanie pulpitu zdalnego dla wystąpienia usługi w chmurze][remotedesktop]
* [Wdróż projekt usługi w chmurze][deploy]

Używam programu Visual Studio i chcę...

* [Utwórz nową usługę w chmurze][vs_create]
* [Ponownie skonfiguruj istniejącą usługę w chmurze][vs_reconfigure]
* [Wdróż projekt usługi w chmurze][vs_deploy]
* [Konfigurowanie pulpitu zdalnego dla wystąpienia usługi w chmurze][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
