---
title: Azure Cloud Services def. Rola procesu roboczego schemat | Microsoft Docs
services: cloud-services
ms.custom: ''
ms.date: 04/14/2015
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 41cd46bc-c479-43fa-96e5-d6c83e4e6d89
caps.latest.revision: 55
author: georgewallace
ms.author: gwallace
manager: gwallace
ms.openlocfilehash: 4ecad6ffc1b57feb16583fd95525a456f6e315a5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162105"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>Schemat rola procesu roboczego definicji Cloud Services platformy Azure
Rola procesu roboczego platformy Azure to rola, która jest przydatna do uogólnionego programowania i może wykonywać przetwarzanie w tle dla roli sieci Web.

Domyślnym rozszerzeniem dla pliku definicji usługi jest. csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Podstawowy schemat definicji usługi dla roli procesu roboczego.
Poniżej przedstawiono podstawowy format pliku definicji usługi zawierającego rolę procesu roboczego.

```xml
<ServiceDefinition …>
  <WorkerRole name="<worker-role-name>" vmsize="<worker-role-size>" enableNativeCodeExecution="[true|false]">
    <Certificates>
      <Certificate name="<certificate-name>" storeLocation="[CurrentUser|LocalMachine]" storeName="[My|Root|CA|Trust|Disallow|TrustedPeople|TrustedPublisher|AuthRoot|AddressBook|<custom-store>" />
    </Certificates>
    <ConfigurationSettings>
      <Setting name="<setting-name>" />
    </ConfigurationSettings>
    <Endpoints>
      <InputEndpoint name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<local-port-number>" port="<port-number>" certificate="<certificate-name>" loadBalancerProbe="<load-balancer-probe-name>" />
      <InternalEndpoint name="<internal-endpoint-name" protocol="[http|tcp|udp|any]" port="<port-number>">
         <FixedPort port="<port-number>"/>
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
      </InternalEndpoint>
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">
         <AllocatePublicPortFrom>
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
         </AllocatePublicPortFrom>
      </InstanceInputEndpoint>
    </Endpoints>
    <Imports>
      <Import moduleName="[RemoteAccess|RemoteForwarder|Diagnostics]"/>
    </Imports>
    <LocalResources>
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    </LocalResources>
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    <Runtime executionContext="[limited|elevated]">
      <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
      </Environment>
      <EntryPoint>
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]"/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>">
      <Task commandLine="" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">
        <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
        </Environment>
      </Task>
    </Startup>
    <Contents>
      <Content destination="<destination-folder-name>" >
        <SourceDirectory path="<local-source-directory>" />
      </Content>
    </Contents>
  </WorkerRole>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementy schematu
Plik definicji usługi zawiera te elementy, które opisano szczegółowo w kolejnych sekcjach w tym temacie:

[Rola procesu roboczego](#WorkerRole)

[ConfigurationSettings](#ConfigurationSettings)

[Ustawienie](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Punktów końcowych](#Endpoints)

[InputEndpoint](#InputEndpoint)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certyfikaty](#Certificates)

[Certyfikat](#Certificate)

[Importowania](#Imports)

[Importowanie](#Import)

[Środowiska uruchomieniowego](#Runtime)

[Środowisko](#Environment)

[Punkt](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Zmiennej](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Folderze](#Startup)

[Zadanie podrzędne](#Task)

[Contents](#Contents)

[Zawartość](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WorkerRole"></a>Rola procesu roboczego
Element `WorkerRole` opisuje rolę, która jest przydatna w przypadku opracowywania uogólnionego, i może wykonywać przetwarzanie w tle dla roli sieci Web. Usługa może zawierać co najmniej zero ról procesów roboczych.

W poniższej tabeli opisano atrybuty elementu `WorkerRole`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Nazwa roli procesu roboczego. Nazwa roli musi być unikatowa.|
|enableNativeCodeExecution|wartość logiczna|Opcjonalny. Wartość domyślna to `true`; wykonywanie kodu natywnego i pełne zaufanie są domyślnie włączone. Ustaw ten atrybut na `false`, aby wyłączyć wykonywanie kodu natywnego dla roli procesu roboczego, a zamiast tego użyj częściowej relacji zaufania platformy Azure.|
|VmSize|string|Opcjonalny. Ustaw tę wartość, aby zmienić rozmiar maszyny wirtualnej przydzielonej dla tej roli. Wartość domyślna to `Small`. Aby uzyskać listę możliwych rozmiarów maszyn wirtualnych i ich atrybutów, zobacz [rozmiary maszyn wirtualnych dla Cloud Services](cloud-services-sizes-specs.md).|

##  <a name="ConfigurationSettings"></a>ConfigurationSettings
Element `ConfigurationSettings` opisuje zbiór ustawień konfiguracji dla roli proces roboczy. Ten element jest elementem nadrzędnym elementu `Setting`.

##  <a name="Setting"></a>Konfigurowania
Element `Setting` opisuje parę nazw i wartości, która określa ustawienia konfiguracji dla wystąpienia roli.

W poniższej tabeli opisano atrybuty elementu `Setting`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Unikatowa nazwa ustawienia konfiguracji.|

Ustawienia konfiguracji roli to pary nazw i wartości, które są zadeklarowane w pliku definicji usługi i ustawiane w pliku konfiguracji usługi.

##  <a name="LocalResources"></a>LocalResources
Element `LocalResources` opisuje kolekcję zasobów magazynu lokalnego dla roli proces roboczy. Ten element jest elementem nadrzędnym elementu `LocalStorage`.

##  <a name="LocalStorage"></a>LocalStorage
Element `LocalStorage` identyfikuje zasób magazynu lokalnego, który zapewnia przestrzeń systemu plików dla usługi w czasie wykonywania. Rola może definiować zero lub więcej zasobów magazynu lokalnego.

> [!NOTE]
>  Element `LocalStorage` może być wyświetlany jako element podrzędny elementu `WorkerRole` w celu zapewnienia zgodności z wcześniejszymi wersjami zestawu Azure SDK.

W poniższej tabeli opisano atrybuty elementu `LocalStorage`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Unikatowa nazwa magazynu lokalnego.|
|cleanOnRoleRecycle|wartość logiczna|Opcjonalny. Wskazuje, czy Magazyn lokalny ma być czyszczony, gdy rola zostanie ponownie uruchomiona. Wartość domyślna to `true`.|
|Wartość argumentu sizeinmb|int|Opcjonalny. Wymagana ilość miejsca do magazynowania do przydzielenia dla lokalnego magazynu, w MB. Jeśli nie zostanie określony, domyślnym miejscem do magazynowania jest 100 MB. Minimalna ilość dostępnego miejsca do magazynowania to 1 MB.<br /><br /> Maksymalny rozmiar zasobów lokalnych zależy od rozmiaru maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych dla Cloud Services](cloud-services-sizes-specs.md).|

Nazwa katalogu przypisana do zasobu magazynu lokalnego odpowiada wartości podanej dla atrybutu Name.

##  <a name="Endpoints"></a>Punktów końcowych
Element `Endpoints` opisuje kolekcję wejściowych punktów końcowych (zewnętrznych), wewnętrznych i wejściowych wystąpień dla roli. Ten element jest elementem nadrzędnym elementów `InputEndpoint`, `InternalEndpoint`i `InstanceInputEndpoint`.

Wejściowe i wewnętrzne punkty końcowe są przydzielone osobno. Usługa może mieć łączną liczbę punktów końcowych wejściowych, wewnętrznych i wejściowych wystąpień, które można przydzielyć w ramach 25 ról dozwolonych w usłudze. Na przykład jeśli masz 5 ról, możesz przydzielić 5 wejściowych punktów końcowych na rolę lub przydzielić 25 wejściowych punktów końcowych do pojedynczej roli lub można przydzielić 1 wejściowy punkt końcowy każdy do 25 ról.

> [!NOTE]
>  Wszystkie wdrożone role wymagają jednego wystąpienia na rolę. Domyślne Inicjowanie obsługi dla subskrypcji jest ograniczone do 20 rdzeni i w rezultacie jest ograniczone do 20 wystąpień roli. Jeśli aplikacja wymaga więcej wystąpień niż jest to obsługiwane przez domyślne Inicjowanie obsługi [, zobacz Zarządzanie rozliczeniami, subskrypcjami i przydziałami,](https://azure.microsoft.com/support/options/) Aby uzyskać więcej informacji na temat zwiększania limitu przydziału.

##  <a name="InputEndpoint"></a>InputEndpoint
Element `InputEndpoint` opisuje zewnętrzny punkt końcowy do roli procesu roboczego.

Istnieje możliwość zdefiniowania wielu punktów końcowych, które są kombinacją punktów końcowych HTTP, HTTPS, UDP i TCP. Możesz określić dowolny numer portu wybrany dla wejściowego punktu końcowego, ale numery portów określone dla każdej roli w usłudze muszą być unikatowe. Na przykład, jeśli określisz, że w przypadku protokołu HTTP i portu 443 dla roli jest stosowany port 80, możesz określić, że druga rola używa portu 8080 dla protokołu HTTP i portu 8043 dla protokołu HTTPS.

W poniższej tabeli opisano atrybuty elementu `InputEndpoint`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Unikatowa nazwa zewnętrznego punktu końcowego.|
|Protokol|string|Wymagany. Protokół transportu dla zewnętrznego punktu końcowego. Dla roli proces roboczy możliwe wartości to `HTTP`, `HTTPS`, `UDP`lub `TCP`.|
|port|int|Wymagany. Port zewnętrznego punktu końcowego. Możesz określić dowolny wybrany numer portu, ale numery portów określone dla każdej roli w usłudze muszą być unikatowe.<br /><br /> Możliwe wartości mieszczą się w zakresie od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1,7 lub nowszej).|
|certyfikat|string|Wymagane dla punktu końcowego HTTPS. Nazwa certyfikatu zdefiniowana przez element `Certificate`.|
|localPort|int|Opcjonalny. Określa port używany na potrzeby połączeń wewnętrznych w punkcie końcowym. Atrybut `localPort` mapuje port zewnętrzny w punkcie końcowym na port wewnętrzny w roli. Jest to przydatne w scenariuszach, w których rola musi komunikować się z wewnętrznym składnikiem na porcie, który różni się od tego, który jest udostępniany zewnętrznie.<br /><br /> Jeśli nie zostanie określony, wartość `localPort` jest taka sama jak atrybut `port`. Ustaw wartość `localPort` na "*", aby automatycznie przypisywać nieprzydzielony port, który jest wykrywalny przy użyciu interfejsu API środowiska uruchomieniowego.<br /><br /> Możliwe wartości mieszczą się w zakresie od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1,7 lub nowszej).<br /><br /> Atrybut `localPort` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.|
|ignoreRoleInstanceStatus|wartość logiczna|Opcjonalny. Gdy wartość tego atrybutu jest ustawiona na `true`, stan usługi jest ignorowany, a punkt końcowy nie zostanie usunięty przez moduł równoważenia obciążenia. Ustawienie tej wartości `true` przydatne do debugowania zajętych wystąpień usługi. Wartość domyślna to `false`. **Uwaga:** Punkt końcowy może nadal odbierać ruch nawet wtedy, gdy rola nie jest w stanie gotowości.|
|loadBalancerProbe|string|Opcjonalny. Nazwa sondy modułu równoważenia obciążenia skojarzona z wejściowym punktem końcowym. Aby uzyskać więcej informacji, zobacz [schemat LoadBalancerProbe](schema-csdef-loadbalancerprobe.md).|

##  <a name="InternalEndpoint"></a>InternalEndpoint
Element `InternalEndpoint` opisuje wewnętrzny punkt końcowy do roli procesu roboczego. Wewnętrzny punkt końcowy jest dostępny tylko dla innych wystąpień roli uruchomionych w ramach usługi. nie jest on dostępny dla klientów spoza usługi. Rola procesu roboczego może mieć maksymalnie pięć wewnętrznych punktów końcowych HTTP, UDP lub TCP.

W poniższej tabeli opisano atrybuty elementu `InternalEndpoint`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Unikatowa nazwa wewnętrznego punktu końcowego.|
|Protokol|string|Wymagany. Protokół transportu dla wewnętrznego punktu końcowego. Możliwe wartości to `HTTP`, `TCP`, `UDP`lub `ANY`.<br /><br /> Wartość `ANY` określa, że dowolny protokół, dowolny port jest dozwolony.|
|port|int|Opcjonalny. Port używany do wewnętrznych połączeń z równoważeniem obciążenia w punkcie końcowym. Punkt końcowy ze zrównoważonym obciążeniem używa dwóch portów. Port używany dla publicznego adresu IP oraz port używany przez prywatny adres IP. Zazwyczaj są to te same ustawienia, ale można wybrać używanie różnych portów.<br /><br /> Możliwe wartości mieszczą się w zakresie od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1,7 lub nowszej).<br /><br /> Atrybut `Port` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.|

##  <a name="InstanceInputEndpoint"></a>InstanceInputEndpoint
Element `InstanceInputEndpoint` opisuje wejściowy punkt końcowy wystąpienia do roli procesu roboczego. Wejściowy punkt końcowy wystąpienia jest skojarzony z określonym wystąpieniem roli przy użyciu przekazywania portów w module równoważenia obciążenia. Każdy wejściowy punkt końcowy wystąpienia jest mapowany na określony port z zakresu możliwych portów. Ten element jest elementem nadrzędnym elementu `AllocatePublicPortFrom`.

Element `InstanceInputEndpoint` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,7 lub nowszej.

W poniższej tabeli opisano atrybuty elementu `InstanceInputEndpoint`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Unikatowa nazwa punktu końcowego.|
|localPort|int|Wymagany. Określa port wewnętrzny, do którego wszystkie wystąpienia ról będą nasłuchiwać w celu odbierania ruchu przychodzącego przesyłanego z modułu równoważenia obciążenia. Możliwe wartości mieszczą się w zakresie od 1 do 65535 włącznie.|
|Protokol|string|Wymagany. Protokół transportu dla wewnętrznego punktu końcowego. Możliwe wartości to `udp` lub `tcp`. Użyj `tcp` dla ruchu opartego na protokole HTTP/HTTPS.|

##  <a name="AllocatePublicPortFrom"></a>AllocatePublicPortFrom
Element `AllocatePublicPortFrom` opisuje publiczny zakres portów, który może być używany przez klientów zewnętrznych do uzyskiwania dostępu do każdego wejściowego punktu końcowego wystąpienia. Numer portu publicznego (VIP) jest przydzielany z tego zakresu i przypisywany do każdego punktu końcowego poszczególnych wystąpień ról podczas wdrażania i aktualizowania dzierżawy. Ten element jest elementem nadrzędnym elementu `FixedPortRange`.

Element `AllocatePublicPortFrom` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,7 lub nowszej.

##  <a name="FixedPort"></a>FixedPort
`FixedPort` element określa port wewnętrznego punktu końcowego, który umożliwia połączenia z równoważeniem obciążenia w punkcie końcowym.

Element `FixedPort` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

W poniższej tabeli opisano atrybuty elementu `FixedPort`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|port|int|Wymagany. Port wewnętrznego punktu końcowego. Ma to taki sam efekt jak ustawienie `FixedPortRange` minimum i maksimum na ten sam port.<br /><br /> Możliwe wartości mieszczą się w zakresie od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1,7 lub nowszej).|

##  <a name="FixedPortRange"></a>FixedPortRange
`FixedPortRange` element określa zakres portów przypisanych do wewnętrznego punktu końcowego lub wejściowy punkt końcowy wystąpienia oraz ustawia port używany na potrzeby połączeń z równoważeniem obciążenia w punkcie końcowym.

> [!NOTE]
>  `FixedPortRange` element działa inaczej w zależności od elementu, w którym się znajduje. Gdy `FixedPortRange` elementu znajduje się w elemencie `InternalEndpoint`, otwiera wszystkie porty w module równoważenia obciążenia w zakresie atrybutów minimalnych i maksymalnych dla wszystkich maszyn wirtualnych, na których jest uruchomiona rola. Gdy `FixedPortRange` element znajduje się w elemencie `InstanceInputEndpoint`, otwiera tylko jeden port w zakresie atrybutów minimalnych i maksymalnych na każdej maszynie wirtualnej, na której działa rola.

Element `FixedPortRange` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

W poniższej tabeli opisano atrybuty elementu `FixedPortRange`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|min.|int|Wymagany. Minimalny port w zakresie. Możliwe wartości mieszczą się w zakresie od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1,7 lub nowszej).|
|Maksymalny|string|Wymagany. Maksymalny port w zakresie. Możliwe wartości mieszczą się w zakresie od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1,7 lub nowszej).|

##  <a name="Certificates"></a>Przystawki
Element `Certificates` opisuje zbiór certyfikatów dla roli proces roboczy. Ten element jest elementem nadrzędnym elementu `Certificate`. Rola może mieć dowolną liczbę skojarzonych certyfikatów. Aby uzyskać więcej informacji na temat korzystania z elementu Certificates, zobacz [Modyfikowanie pliku definicji usługi za pomocą certyfikatu](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a>Certyfikatu
Element `Certificate` opisuje certyfikat skojarzony z rolą proces roboczy.

W poniższej tabeli opisano atrybuty elementu `Certificate`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Nazwa tego certyfikatu, która jest używana do odwoływania się do niego, gdy jest skojarzona z elementem `InputEndpoint` HTTPS.|
|storeLocation|string|Wymagany. Lokalizacja magazynu certyfikatów, w którym ten certyfikat znajduje się na komputerze lokalnym. Możliwe wartości to `CurrentUser` i `LocalMachine`.|
|storeName|string|Wymagany. Nazwa magazynu certyfikatów, w którym znajduje się ten certyfikat na komputerze lokalnym. Możliwe wartości obejmują nazwy wbudowanych magazynów `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`lub dowolnej nazwy magazynu niestandardowego. Jeśli określono niestandardową nazwę magazynu, magazyn zostanie utworzony automatycznie.|
|permissionLevel|string|Opcjonalny. Określa uprawnienia dostępu nadawane procesom roli. Jeśli chcesz, aby dostęp do klucza prywatnego był możliwy tylko z podniesionymi procesami, określ uprawnienia `elevated`. uprawnienie `limitedOrElevated` umożliwia wszystkim procesom roli dostęp do klucza prywatnego. Możliwe wartości to `limitedOrElevated` lub `elevated`. Wartość domyślna to `limitedOrElevated`.|

##  <a name="Imports"></a>Importowania
Element `Imports` opisuje zbiór modułów importu dla roli proces roboczy, który dodaje składniki do systemu operacyjnego gościa. Ten element jest elementem nadrzędnym elementu `Import`. Ten element jest opcjonalny, a rola może mieć tylko jeden blok czasu wykonywania.

Element `Imports` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

##  <a name="Import"></a>Zaimportować
Element `Import` określa moduł, który ma zostać dodany do systemu operacyjnego gościa.

Element `Import` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

W poniższej tabeli opisano atrybuty elementu `Import`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|moduleName|string|Wymagany. Nazwa modułu do zaimportowania. Prawidłowe moduły importu:<br /><br /> -RemoteAccess<br />- RemoteForwarder<br />-Diagnostyka<br /><br /> Moduły RemoteAccess i RemoteForwarder umożliwiają skonfigurowanie wystąpienia roli dla połączeń pulpitu zdalnego. Aby uzyskać więcej informacji, zobacz [włączanie Podłączanie pulpitu zdalnego](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Moduł diagnostyki umożliwia zbieranie danych diagnostycznych dla wystąpienia roli|

##  <a name="Runtime"></a>Środowiska uruchomieniowego
Element `Runtime` opisuje zbiór ustawień zmiennych środowiskowych dla roli procesu roboczego kontrolujących środowisko uruchomieniowe procesu hosta platformy Azure. Ten element jest elementem nadrzędnym elementu `Environment`. Ten element jest opcjonalny, a rola może mieć tylko jeden blok czasu wykonywania.

Element `Runtime` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

W poniższej tabeli opisano atrybuty elementu `Runtime`:

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|Kontekście wykonywania|string|Opcjonalny. Określa kontekst, w którym uruchamiany jest proces roli. Domyślny kontekst to `limited`.<br /><br /> -   `limited` — proces jest uruchamiany bez uprawnień administratora.<br />-   `elevated` — proces jest uruchamiany z uprawnieniami administratora.|

##  <a name="Environment"></a>Naturalne
Element `Environment` opisuje zbiór ustawień zmiennych środowiskowych dla roli proces roboczy. Ten element jest elementem nadrzędnym elementu `Variable`. Rola może mieć określoną liczbę zmiennych środowiskowych.

##  <a name="Variable"></a>Zmiennej
Element `Variable` określa zmienną środowiskową do ustawienia w systemie operacyjnym gościa.

Element `Variable` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

W poniższej tabeli opisano atrybuty elementu `Variable`:

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Nazwa zmiennej środowiskowej, która ma zostać ustawiona.|
|wartość|string|Opcjonalny. Wartość, która ma zostać ustawiona dla zmiennej środowiskowej. Musisz dołączyć atrybut value lub `RoleInstanceValue` elementu.|

##  <a name="RoleInstanceValue"></a>RoleInstanceValue
Element `RoleInstanceValue` określa wyrażenie xPath, z którego ma zostać pobrana wartość zmiennej.

W poniższej tabeli opisano atrybuty elementu `RoleInstanceValue`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|Lokalizacja|string|Opcjonalny. Ścieżka lokalizacji ustawień wdrożenia dla tego wystąpienia. Aby uzyskać więcej informacji, zobacz [Zmienne konfiguracyjne z wyrażeniem XPath](cloud-services-role-config-xpath.md).<br /><br /> Musisz dołączyć atrybut value lub `RoleInstanceValue` elementu.|

##  <a name="EntryPoint"></a>Punkt
Element `EntryPoint` określa punkt wejścia dla roli. Ten element jest elementem nadrzędnym `NetFxEntryPoint` elementów. Te elementy umożliwiają określenie aplikacji innej niż domyślna WaWorkerHost. exe do działania jako punkt wejścia roli.

Element `EntryPoint` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,5 lub nowszej.

##  <a name="NetFxEntryPoint"></a>NetFxEntryPoint
`NetFxEntryPoint` element określa program do uruchomienia dla roli.

> [!NOTE]
>  Element `NetFxEntryPoint` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,5 lub nowszej.

W poniższej tabeli opisano atrybuty elementu `NetFxEntryPoint`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|AssemblyName|string|Wymagany. Ścieżka i nazwa pliku zestawu zawierającego punkt wejścia. Ścieżka jest względna w stosunku do folderu **\\%ROLEROOT%\Approot** (nie określaj **\\%ROLEROOT%\Approot** w `commandLine`, przyjmuje się, że założono). **% ROLEROOT%** to zmienna środowiskowa obsługiwana przez platformę Azure, która reprezentuje lokalizację folderu głównego dla Twojej roli. Folder **\\%ROLEROOT%\Approot** reprezentuje folder aplikacji dla Twojej roli.|
|TargetFrameworkVersion|string|Wymagany. Wersja programu .NET Framework, na którym został skompilowany zestaw. Na przykład `targetFrameworkVersion="v4.0"`.|

##  <a name="ProgramEntryPoint"></a>ProgramEntryPoint
`ProgramEntryPoint` element określa program do uruchomienia dla roli. Element `ProgramEntryPoint` umożliwia określenie punktu wejścia programu, który nie jest oparty na zestawie .NET.

> [!NOTE]
>  Element `ProgramEntryPoint` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,5 lub nowszej.

W poniższej tabeli opisano atrybuty elementu `ProgramEntryPoint`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|Wiersza polecenia|string|Wymagany. Ścieżka, nazwa pliku i argumenty wiersza polecenia programu do wykonania. Ścieżka jest względna w stosunku do folderu **%ROLEROOT%\Approot** (nie określaj **%ROLEROOT%\Approot** w wierszu polecenia, jest założono). **% ROLEROOT%** to zmienna środowiskowa obsługiwana przez platformę Azure, która reprezentuje lokalizację folderu głównego dla Twojej roli. Folder **%ROLEROOT%\Approot** reprezentuje folder aplikacji dla Twojej roli.<br /><br /> Jeśli program zostanie zakończony, rola jest odtwarzana, dlatego na ogół ustawia program do dalszego uruchomienia, a nie program, który właśnie uruchamia i uruchamia skończone zadanie.|
|setReadyOnProcessStart|wartość logiczna|Wymagany. Określa, czy wystąpienie roli czeka na uruchomienie programu wiersza polecenia. Ta wartość musi być teraz ustawiona na `true`. Ustawienie wartości `false` jest zarezerwowane do użytku w przyszłości.|

##  <a name="Startup"></a>Folderze
Element `Startup` opisuje zbiór zadań, które są uruchamiane, gdy rola zostanie uruchomiona. Ten element może być elementem nadrzędnym elementu `Variable`. Aby uzyskać więcej informacji o korzystaniu z zadań uruchamiania roli, zobacz [jak skonfigurować zadania uruchamiania](cloud-services-startup-tasks.md). Ten element jest opcjonalny i rola może mieć tylko jeden blok startowy.

W poniższej tabeli opisano atrybut elementu `Startup`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|priority|int|Tylko do użytku wewnętrznego.|

##  <a name="Task"></a>Zadaniem
`Task` element Określa zadanie uruchamiania, które odbywa się podczas uruchamiania roli. Zadania uruchamiania mogą służyć do wykonywania zadań przygotowujących rolę do uruchamiania takich składników oprogramowania lub uruchamiania innych aplikacji. Zadania są wykonywane w kolejności, w jakiej występują w bloku elementu `Startup`.

Element `Task` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

W poniższej tabeli opisano atrybuty elementu `Task`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|Wiersza polecenia|string|Wymagany. Skrypt, taki jak plik CMD zawierający polecenia do uruchomienia. Polecenia uruchamiania i pliki wsadowe muszą być zapisane w formacie ANSI. Formaty plików ustawiające znacznik kolejności bajtów na początku pliku nie będą przetwarzać się prawidłowo.|
|Kontekście wykonywania|string|Określa kontekst, w którym skrypt jest uruchamiany.<br /><br /> -   `limited` [domyślnie] — Uruchom z takimi samymi uprawnieniami jak rola hostującym proces.<br />-   `elevated` — Uruchom z uprawnieniami administratora.|
|taskType|string|Określa zachowanie wykonywania polecenia.<br /><br /> -   `simple` [domyślnie] — system czeka na zakończenie zadania przed uruchomieniem innych zadań.<br />-   `background` — system nie czeka na zakończenie zadania.<br />-   `foreground` — podobnie jak w tle, z wyjątkiem tego, że rola nie zostanie uruchomiona ponownie do momentu zakończenia wszystkich zadań pierwszego planu.|

##  <a name="Contents"></a>Contents
Element `Contents` opisuje zbiór zawartości dla roli proces roboczy. Ten element jest elementem nadrzędnym elementu `Content`.

Element `Contents` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,5 lub nowszej.

##  <a name="Content"></a>Treści
Element `Content` definiuje lokalizację źródłową zawartości do skopiowania na maszynę wirtualną platformy Azure i ścieżkę docelową, do której jest kopiowany.

Element `Content` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,5 lub nowszej.

W poniższej tabeli opisano atrybuty elementu `Content`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|punktu|string|Wymagany. Lokalizacja na maszynie wirtualnej platformy Azure, do której zostanie umieszczona zawartość. Ta lokalizacja jest określana względem folderu **%ROLEROOT%\Approot**.|

Ten element jest elementem nadrzędnym elementu `SourceDirectory`.

##  <a name="SourceDirectory"></a>SourceDirectory
Element `SourceDirectory` definiuje katalog lokalny, z którego kopiowana jest zawartość. Użyj tego elementu, aby określić lokalną zawartość do skopiowania na maszynę wirtualną platformy Azure.

Element `SourceDirectory` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,5 lub nowszej.

W poniższej tabeli opisano atrybuty elementu `SourceDirectory`.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|ścieżka|string|Wymagany. Ścieżka względna lub bezwzględna katalogu lokalnego, którego zawartość zostanie skopiowana na maszynę wirtualną platformy Azure. Rozszerzanie zmiennych środowiskowych w ścieżce katalogu jest obsługiwane.|

## <a name="see-also"></a>Zobacz też
[Schemat definicji usługi w chmurze (klasycznej)](schema-csdef-file.md)