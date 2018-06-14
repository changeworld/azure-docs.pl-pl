---
title: Co to jest usługa w chmurze modelu i pakietu | Dokumentacja firmy Microsoft
description: Opisuje modelem usługi chmury (pliki csdef, .cscfg) i pakietu (cspkg) na platformie Azure
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 4ce2feb5-0437-496c-98da-1fb6dcb7f59e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 0589f2efeaaafc35bcb9d869c391a0533fe6e502
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
ms.locfileid: "29876563"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Co to jest model usługi w chmurze i jak jest pakiet?
Usługi w chmurze jest tworzona na podstawie trzech składników, definicji usługi *(csdef)*, konfiguracji usługi *(cscfg)*, a pakiet usługi *(cspkg)*. Zarówno **ServiceDefinition.csdef** i **ServiceConfig.cscfg** plików są oparte na języku XML i opisano strukturę usługi w chmurze i sposobu jego konfiguracji; nazywane modelu. **ServicePackage.cspkg** plik zip, który jest generowany na podstawie **ServiceDefinition.csdef** i między innymi zawiera wszystkie zależności wymagane formacie binarnym. Platforma Azure tworzy usługi w chmurze z obu **ServicePackage.cspkg** i **ServiceConfig.cscfg**.

Gdy usługa w chmurze jest uruchomiona na platformie Azure, można ponownie skonfigurować go za pośrednictwem **ServiceConfig.cscfg** pliku, ale nie można zmienić definicji.

## <a name="what-would-you-like-to-know-more-about"></a>Co chcesz dowiedzieć się więcej o?
* Chcę, aby dowiedzieć się więcej o [ServiceDefinition.csdef](#csdef) i [ServiceConfig.cscfg](#cscfg) plików.
* Już wiem, temat, który można uzyskać [przykłady](#next-steps) na to, co można skonfigurować.
* Utwórz [ServicePackage.cspkg](#cspkg).
* Używam programu Visual Studio i chcę...
  * [Tworzenie usługi w chmurze][vs_create]
  * [Skonfiguruj ponownie istniejącej usługi w chmurze][vs_reconfigure]
  * [Wdrażanie projektu usługi w chmurze][vs_deploy]
  * [Pulpit zdalny w wystąpieniu usługi chmury][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
**ServiceDefinition.csdef** pliku określa ustawienia, które są używane przez usługi Azure, aby skonfigurować usługi w chmurze. [Schematu definicji usługi Azure (pliki csdef pliku)](https://msdn.microsoft.com/library/azure/ee758711.aspx) zapewnia dopuszczalny formatu pliku definicji usługi. W poniższym przykładzie przedstawiono ustawienia, które mogą być definiowane dla ról sieć Web i procesu roboczego:

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

Można to sprawdzić [schematu definicji usługi](https://msdn.microsoft.com/library/azure/ee758711.aspx) w celu lepszego zrozumienia schematu XML używane w tym miejscu, jednak w tym miejscu jest szybkie wyjaśnienie niektórych elementów:

**Lokacje**  
Zawiera definicje dla witryn sieci Web lub sieci web aplikacji, które są obsługiwane w programie IIS7.

**InputEndpoints**  
Zawiera definicje dla punktów końcowych, które są używane do kontaktowania się z usługą w chmurze.

**InternalEndpoints**  
Zawiera definicje dla punktów końcowych, które są używane przez wystąpień roli do komunikowania się ze sobą.

**AppSettings**  
Zawiera definicje ustawienie funkcji określonej roli.

**Certyfikaty**  
Zawiera definicje dla certyfikatów, które są wymagane przez rolę. W poprzednim przykładzie kodu przedstawiono certyfikatu, który służy do konfiguracji połączenia platformy Azure.

**LocalResources**  
Zawiera definicje dla zasobów magazynu lokalnego. Zasób Magazyn lokalny jest zastrzeżony katalogu w systemie plików, w którym jest uruchomione wystąpienie roli maszyny wirtualnej.

**Importy**  
Zawiera definicje dla zaimportowanych modułów. W poprzednim przykładzie kodu pokazuje modułów dla usługi Podłączanie pulpitu zdalnego i połącz Azure.

**Uruchamianie**  
Zawiera zadania, które są uruchamiane podczas uruchamiania roli. Zadania są definiowane w .cmd lub pliku wykonywalnego.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
Konfiguracja ustawień usługi w chmurze jest określany przez wartości **pliku ServiceConfiguration.cscfg** pliku. Możesz określić liczbę wystąpień, które mają zostać wdrożone dla każdej roli, w tym pliku. Wartości ustawienia konfiguracji, które zostały zdefiniowane w pliku definicji usługi są dodawane do pliku konfiguracji usługi. Odciski palców wszelkich certyfikatów zarządzania, które są skojarzone z usługą w chmurze, również są dodawane do pliku. [Schemat konfiguracji usługi Azure (cscfg pliku)](https://msdn.microsoft.com/library/azure/ee758710.aspx) zapewnia dozwolony format pliku konfiguracji usługi.

Pliku konfiguracji usługi nie jest dostarczana z aplikacją, ale zostało załadowane na platformie Azure jako osobny plik i służy do konfigurowania usługi w chmurze. Możesz przekazać plik konfiguracji usługi bez ponownego wdrożenia usługi w chmurze. Można zmienić wartości konfiguracji dla usługi w chmurze jest uruchomiona usługa w chmurze. W poniższym przykładzie przedstawiono ustawienia konfiguracji, które mogą być definiowane dla ról sieć Web i procesu roboczego:

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

Można to sprawdzić [schemat konfiguracji usługi](https://msdn.microsoft.com/library/azure/ee758710.aspx) dla lepiej zrozumieć schematu XML używane w tym miejscu, jednak w tym miejscu jest szybkie wyjaśnienie elementów:

**Wystąpienia**  
Umożliwia skonfigurowanie liczby uruchomionych wystąpień roli. Aby zapobiec potencjalnie stać się niedostępne podczas uaktualniania usługi w chmurze, zaleca się wdrożenie więcej niż jednego wystąpienia ról udostępnianych w sieci web. Przez wdrożenie więcej niż jedno wystąpienie, są spełnione wskazówki zawarte w [Azure obliczeniowe poziom Umowa dotycząca usług (SLA)](http://azure.microsoft.com/support/legal/sla/), który zapewnia łączność zewnętrzną 99,95% dla ról połączonych z Internetem, gdy dwie lub więcej wystąpień roli są wdrażane dla usługi.

**AppSettings**  
Konfiguruje ustawienia dla uruchomionych wystąpień roli. Nazwa `<Setting>` elementy muszą być zgodne definicji ustawień w pliku definicji usługi.

**Certyfikaty**  
Konfiguruje certyfikaty, które są używane przez usługę. W poprzednim przykładzie kodu pokazano, jak można zdefiniować certyfikat dla modułu dostęp zdalny. Wartość *odcisk palca* atrybut musi mieć ustawioną odcisk palca certyfikatu do użycia.

<p/>

> [!NOTE]
> Odcisk palca certyfikatu można dodać do pliku konfiguracji za pomocą edytora tekstu. Lub może zostać dodana wartość na **certyfikaty** karcie **właściwości** strony roli w programie Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definiowanie portów dla wystąpień roli
Azure umożliwia tylko jeden wpis wskaż roli sieci web. Co oznacza, że cały ruch odbywa się przez jeden adres IP. Można skonfigurować witryny sieci Web, aby udostępnić portu przez skonfigurowanie nagłówek hosta, należy przekierować żądania do poprawnej lokalizacji. Można również skonfigurować aplikacje słuchać dobrze znanych portów na adres IP.

Poniższy przykład przedstawia konfigurację dla roli sieci web z aplikacji sieci web i witryny sieci Web. Witryna sieci Web jest skonfigurowana jako domyślną lokalizację zapisu na porcie 80, a aplikacje sieci web są skonfigurowane do odbierania żądań z nagłówka alternatywnego hosta, który jest nazywany "mail.mysite.cloudapp.net".

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


## <a name="changing-the-configuration-of-a-role"></a>Zmienianie konfiguracji roli
Należy zaktualizować konfiguracji usługi w chmurze, jest uruchomiona na platformie Azure, bez konieczności przełączania w tryb offline usługa. Aby zmienić informacje o konfiguracji, użytkownik może albo Przekaż nowy plik konfiguracji, lub edytować plik konfiguracji w miejscu i zastosować je do uruchomionej usługi. W konfiguracji usługi mogą być wprowadzone następujące zmiany:

* **Zmiana wartości ustawienia konfiguracji**  
  Gdy ustawienie zmian konfiguracji, wystąpienia roli można zastosować zmiany, gdy wystąpienie jest w trybie online lub bezpiecznie Odtwórz wystąpienie do zastosowania zmiany podczas wystąpienie jest w trybie offline.
* **Zmiany topologii usługa wystąpień roli**  
  Topologia zmiany nie wpływają na uruchomione wystąpienia, z wyjątkiem przypadków, gdy wystąpienie jest usuwana. Wszystkie pozostałe wystąpienia zwykle nie trzeba będzie wykonywane; można jednak odzyskać wystąpień roli w odpowiedzi na zmianę topologii.
* **Zmiana odcisk palca certyfikatu**  
  Certyfikat można aktualizować tylko wtedy, gdy wystąpienie roli jest w trybie offline. Jeśli certyfikat jest dodany, usunięty lub zmieniony, gdy wystąpienie roli jest w trybie online, Azure bezpiecznie powoduje wystąpienie w trybie offline do Aktualizuj certyfikat i przełączyć do trybu online po zakończeniu zmiany.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Obsługa zmiany w konfiguracji o zdarzeniach środowiska uruchomieniowego usługi
[Biblioteki wykonawczej Azure](https://msdn.microsoft.com/library/azure/mt419365.aspx) obejmuje [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx) przestrzeni nazw, która udostępnia klasy do interakcji z środowiska platformy Azure z roli. [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) klasa definiuje następujące zdarzenia, które są wywoływane przed i po zmianie konfiguracji:

* **[Zmiana](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx) zdarzeń**  
  Dzieje się tak, przed zastosowaniem zmian w konfiguracji do określonego wystąpienia roli, co daje możliwość wyłączyć wystąpienia roli, jeśli to konieczne.
* **[Zmienione](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx) zdarzeń**  
  Występuje po zmianie konfiguracji jest stosowane do określonego wystąpienia roli.

> [!NOTE]
> Ponieważ zmiany certyfikatu zawsze pobierają wystąpień roli w tryb offline, nie wygenerował RoleEnvironment.Changing lub RoleEnvironment.Changed zdarzeń.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Aby wdrożyć aplikację jako usługa w chmurze na platformie Azure, najpierw należy utworzyć pakiet aplikacji w odpowiednim formacie. Można użyć **CSPack** narzędzia wiersza polecenia (zainstalowaną z [zestawu Azure SDK](https://azure.microsoft.com/downloads/)) można utworzyć pliku pakietu jako alternatywę dla programu Visual Studio.

**CSPack** zawartość pliku definicji usługi i pliku konfiguracji usługi jest używana do definiowania zawartości pakietu. **CSPack** generuje plik pakietu aplikacji (cspkg) można przekazać go do platformy Azure przy użyciu [portalu Azure](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Domyślnie pakiet o nazwie `[ServiceDefinitionFileName].cspkg`, ale możesz określić inną nazwę przy użyciu `/out` opcji **CSPack**.

**CSPack** znajduje się pod adresem  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (w systemie windows) jest dostępny, uruchamiając **wiersza polecenia usługi Microsoft Azure** skrót, który został zainstalowany przy użyciu zestawu SDK.  
> 
> Uruchom CSPack.exe program samodzielnie dokumentacji dotyczące wszystkich możliwych przełączników i poleceń.
> 
> 

<p />

> [!TIP]
> Uruchamianie usługi w chmurze lokalnie w **Microsoft Azure obliczeniowe emulatora**, użyj **/copyonly** opcji. Ta opcja powoduje skopiowanie plików binarnych dla aplikacji do układu katalogu, z którego może działać w emulatorze obliczeń.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Przykładowe polecenie, aby pakiet usługi w chmurze
W poniższym przykładzie jest tworzony pakiet aplikacji zawierający informacje dla roli sieci web. Polecenie określa pliku definicji usługi do użycia, katalog, w którym można znaleźć plików binarnych, a nazwa pliku pakietu.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Jeśli aplikacja zawiera rolę sieci web i roli proces roboczy, służy następujące polecenie:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Gdzie zmienne są zdefiniowane w następujący sposób:

| Zmienna | Wartość |
| --- | --- |
| \[DirectoryName\] |Podkatalogu w katalogu głównym projektu zawierającego plik csdef projektu platformy Azure. |
| \[ServiceDefinition\] |Nazwa pliku definicji usługi. Domyślnie ten plik ma nazwę ServiceDefinition.csdef. |
| \[OutputFileName\] |Nazwa pliku wygenerowany pakiet. Zwykle ta jest ustawiona na nazwę aplikacji. Jeśli nazwa pliku nie jest określona, pakiet aplikacji jest tworzony jako \[ApplicationName\]cspkg. |
| \[RoleName\] |Nazwa roli, zgodnie z definicją w pliku definicji usługi. |
| \[RoleBinariesDirectory] |Lokalizacja plików binarnych roli. |
| \[VirtualPath\] |Katalogi fizyczne dla każdej ścieżki wirtualnej określona w sekcji witryn definicji usługi. |
| \[PhysicalPath\] |Katalogi fizyczne zawartości dla każdej ścieżki wirtualnej zdefiniowany w węźle lokacji definicji usługi. |
| \[RoleAssemblyName\] |Nazwa pliku binarnego dla roli. |

## <a name="next-steps"></a>Kolejne kroki
Tworzenia pakietu usług chmury i chcę...

* [Ustawienia pulpitu zdalnego dla wystąpienia usługi chmury][remotedesktop]
* [Wdrażanie projektu usługi w chmurze][deploy]

Używam programu Visual Studio i chcę...

* [Utwórz nową usługę w chmurze][vs_create]
* [Skonfiguruj ponownie istniejącej usługi w chmurze][vs_reconfigure]
* [Wdrażanie projektu usługi w chmurze][vs_deploy]
* [Ustawienia pulpitu zdalnego dla wystąpienia usługi chmury][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
