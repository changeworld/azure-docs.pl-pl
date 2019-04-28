---
title: Azure Cloud Services Def. WorkerRole Schema | Microsoft Docs
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
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 90a11c5bb81a0d29f5f8a1c1696732453aa4b1ab
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095408"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>Azure Cloud Services, definicja workerrole — schemat
Rola procesu roboczego platformy Azure jest rolą, która jest przydatne w przypadku ogólnych rozwoju i może wykonywać przetwarzania w tle dla roli sieci web.

Domyślnym rozszerzeniem dla pliku definicji usługi jest csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Schemat definicji usług w warstwie podstawowa dla roli procesu roboczego.
Podstawowy format pliku definicji usługi, zawierający roli proces roboczy jest w następujący sposób.

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
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]" "/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>”>
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
Plik definicji usługi obejmuje te elementy, opisano szczegółowo w kolejnych sekcjach tego tematu:

[WorkerRole](#WorkerRole)

[AppSettings](#ConfigurationSettings)

[Ustawienie](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Punkty końcowe](#Endpoints)

[InputEndpoint](#InputEndpoint)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certyfikaty](#Certificates)

[Certyfikat](#Certificate)

[Importy](#Imports)

[Importowanie](#Import)

[Środowisko uruchomieniowe](#Runtime)

[Środowisko](#Environment)

[EntryPoint](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Zmienna](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Uruchamianie](#Startup)

[Zadanie podrzędne](#Task)

[Zawartość](#Contents)

[Zawartość](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WorkerRole"></a> Workerrole —
`WorkerRole` Element w tym artykule opisano rolę, która jest przydatne w przypadku ogólnych rozwoju i może wykonywać przetwarzania w tle dla roli sieci web. Usługa może zawierać zero lub więcej ról procesów roboczych.

W poniższej tabeli opisano atrybuty `WorkerRole` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Nazwa roli procesu roboczego. Nazwa roli musi być unikatowa.|
|enableNativeCodeExecution|wartość logiczna|Opcjonalny. Wartość domyślna to `true`; natywne wykonywanie kodu i pełną zaufania są domyślnie włączone. Ustaw ten atrybut na `false` wyłączyć wykonywanie kodu natywnego dla roli procesu roboczego, a zamiast tego użyj Azure częściowej relacji zaufania.|
|vmsize|string|Opcjonalny. Ustaw tę wartość, aby zmienić rozmiar maszyny wirtualnej, który jest przydzielony do tej roli. Wartość domyślna to `Small`. Aby uzyskać listę rozmiarów możliwości maszyn wirtualnych i ich atrybuty, zobacz [rozmiarów maszyn wirtualnych dla usług w chmurze](cloud-services-sizes-specs.md).|

##  <a name="ConfigurationSettings"></a> AppSettings
`ConfigurationSettings` Element opisuje kolekcję ustawień konfiguracji dla roli procesu roboczego. Ten element jest elementem nadrzędnym `Setting` elementu.

##  <a name="Setting"></a> Ustawienie
`Setting` Element opisuje nazwę i wartość pary, który określa ustawienia konfiguracji dla wystąpienia roli.

W poniższej tabeli opisano atrybuty `Setting` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Unikatową nazwę dla ustawienia konfiguracji.|

Ustawienia konfiguracji dla roli są pary nazw i wartości, które są zadeklarowane w pliku definicji usługi i ustawione w pliku konfiguracji usługi.

##  <a name="LocalResources"></a> LocalResources
`LocalResources` Element opisuje kolekcję zasobów magazynu lokalnego dla roli procesu roboczego. Ten element jest elementem nadrzędnym `LocalStorage` elementu.

##  <a name="LocalStorage"></a> LocalStorage
`LocalStorage` Element identyfikuje zasób magazynu lokalnego, który udostępnia miejsce w systemie plików usługi w czasie wykonywania. Rola może zdefiniować zero lub więcej, zasoby magazynu lokalnego.

> [!NOTE]
>  `LocalStorage` Element może znajdować się jako element podrzędny elementu `WorkerRole` element do obsługi zgodność z wcześniejszymi wersjami zestawu Azure SDK.

W poniższej tabeli opisano atrybuty `LocalStorage` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Unikatowa nazwa magazynu lokalnego.|
|cleanOnRoleRecycle|wartość logiczna|Opcjonalny. Wskazuje, czy magazynu lokalnego powinny być czyszczone po ponownym uruchomieniu roli. Wartość domyślna to `true`.|
|sizeInMb|int|Opcjonalny. Wymaganej ilości miejsca do magazynowania można przydzielić dla magazynu lokalnego, w Megabajtach. Jeśli nie zostanie określony, domyślna miejsca do magazynowania przydzielone wynosi 100 MB. Minimalna ilość miejsca do magazynowania, która może być przydzielona to 1 MB.<br /><br /> Maksymalny rozmiar zasobów lokalnych jest zależna od rozmiaru maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych dla usług w chmurze](cloud-services-sizes-specs.md).|

Nazwa katalogu przydzielony do zasobu lokalnego magazynu odpowiada wartość podana dla atrybutu name.

##  <a name="Endpoints"></a> Punkty końcowe
`Endpoints` Element opisuje kolekcję danych wejściowych (zewnętrzny), wewnętrzne i wystąpienie danych wejściowych punktów końcowych dla roli. Ten element jest elementem nadrzędnym `InputEndpoint`, `InternalEndpoint`, i `InstanceInputEndpoint` elementów.

Dane wejściowe i wewnętrznych punktów końcowych są przydzielane oddzielnie. Usługa może mieć łącznie 25 danych wejściowych, wewnętrznych i wystąpienie danych wejściowych punktów końcowych, które mogą być przydzielone 25 ról, które mogą w ramach usługi. Na przykład jeśli masz 5 ról można przydzielić 5 wejściowych punktów końcowych w każdej roli, można przydzielić 25 wejściowych punktów końcowych dla jednej roli lub możesz przydzielić 1 wejściowy punkt końcowy każdego do 25 ról.

> [!NOTE]
>  Każda rola wdrożone wymaga jednego wystąpienia każdej roli. Domyślne aprowizacji dla subskrypcji jest ograniczona do 20 rdzeni i dlatego jest ograniczona do 20 wystąpień roli. Jeśli aplikacja wymaga większej liczby wystąpień niż jest dostępny domyślnie aprowizacji zobacz [rozliczeń, subskrypcji, zarządzanie i obsługa limitu przydziału](https://azure.microsoft.com/support/options/) więcej informacji na temat zwiększenia limitu przydziału.

##  <a name="InputEndpoint"></a> InputEndpoint
`InputEndpoint` Element w tym artykule opisano zewnętrznego punktu końcowego roli procesu roboczego.

Można zdefiniować wiele punktów końcowych, które są kombinacją HTTP, HTTPS, UDP i punktów końcowych TCP. Można określić dowolny inny numer portu wybranego wejściowego punktu końcowego, ale numery portów, określony dla każdej roli w ramach usługi musi być unikatowa. Na przykład jeśli określisz, że rola korzysta z portu 80 dla protokołu HTTP i portu 443 dla protokołu HTTPS, może być następnie możesz określić druga rola używa portu 8080 dla protokołu HTTP i portu 8043 dla protokołu HTTPS.

W poniższej tabeli opisano atrybuty `InputEndpoint` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Unikatowa nazwa jest zewnętrzny punkt końcowy.|
|protokół|string|Wymagany. Protokół transportu jest zewnętrzny punkt końcowy. Rola procesu roboczego, możliwe wartości to `HTTP`, `HTTPS`, `UDP`, lub `TCP`.|
|port|int|Wymagany. Port zewnętrznego punktu końcowego. Można określić dowolny numer portu, które wybierzesz, ale numery portów, określony dla każdej roli w ramach usługi musi być unikatowa.<br /><br /> Możliwe wartości z zakresu od 1 do 65535 (włącznie). (zestaw Azure SDK w wersji 1.7 lub nowszej).|
|certyfikat|string|Wymagane dla punktu końcowego HTTPS. Nazwa certyfikatu z definicją `Certificate` elementu.|
|localPort|int|Opcjonalny. Określa port używany dla wewnętrznych połączeń w punkcie końcowym. `localPort` Porcie zewnętrznym w punkcie końcowym mapowań atrybutów do portu wewnętrznej w roli. Jest to przydatne w scenariuszach, w której roli musi komunikować się do wewnętrznego składnika na porcie, inny niż ten, który jest widoczna zewnętrznie.<br /><br /> Jeśli nie zostanie określony, wartość `localPort` jest taka sama jak `port` atrybutu. Ustaw wartość `localPort` na "*" Aby automatycznie przypisać nieprzydzielone port wykrywalny, przy użyciu interfejsu API środowiska wykonawczego.<br /><br /> Możliwe wartości z zakresu od 1 do 65535 (włącznie). (zestaw Azure SDK w wersji 1.7 lub nowszej).<br /><br /> `localPort` Atrybut jest dostępne tylko przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.|
|ignoreRoleInstanceStatus|wartość logiczna|Opcjonalny. Jeśli wartość tego atrybutu jest równa `true`, stan usługi jest ignorowany i punkt końcowy nie zostaną usunięte przez moduł równoważenia obciążenia. Ustawienie tej wartości na `true` przydatne podczas debugowania zajęty wystąpień usługi. Wartość domyślna to `false`. **Uwaga:** Punkt końcowy nadal mogą odbierać ruch nawet wtedy, gdy rola nie jest w stanie gotowe.|
|loadBalancerProbe|string|Opcjonalny. Nazwa sondy modułu równoważenia obciążenia, które są skojarzone z wejściowym punktem końcowym. Aby uzyskać więcej informacji, zobacz [loadbalancerprobe — schemat](schema-csdef-loadbalancerprobe.md).|

##  <a name="InternalEndpoint"></a> InternalEndpoint
`InternalEndpoint` Element w tym artykule opisano wewnętrzny punkt końcowy do roli procesu roboczego. Wewnętrzny punkt końcowy jest dostępny tylko dla innych wystąpień roli w ramach usługi; nie jest dostępna dla klientów poza usługi. Rola procesu roboczego może mieć maksymalnie pięć HTTP, UDP lub TCP wewnętrznych punktów końcowych.

W poniższej tabeli opisano atrybuty `InternalEndpoint` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Unikatowa nazwa wewnętrznego punktu końcowego.|
|protokół|string|Wymagany. Protokół transportowy wewnętrznego punktu końcowego. Możliwe wartości to `HTTP`, `TCP`, `UDP`, lub `ANY`.<br /><br /> Wartość `ANY` Określa, że każdego protokołu, każdy port jest dozwolone.|
|port|int|Opcjonalny. Port używany dla połączeń punkt końcowy wewnętrznego równoważenia obciążenia. Dwa porty używa punktu końcowego ze zrównoważonym obciążeniem. Port używany do publicznego adresu IP i port używany na prywatny adres IP. Zazwyczaj są to te właściwości są ustawiane na takie same, ale można wybrać używało innych portów.<br /><br /> Możliwe wartości z zakresu od 1 do 65535 (włącznie). (zestaw Azure SDK w wersji 1.7 lub nowszej).<br /><br /> `Port` Atrybut jest dostępne tylko przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.|

##  <a name="InstanceInputEndpoint"></a> InstanceInputEndpoint
`InstanceInputEndpoint` Element opisuje wystąpienie wejściowy punkt końcowy do roli procesu roboczego. Wejściowy punkt końcowy wystąpienia jest skojarzony z instancją konkretnej roli za pomocą przekierowania portów w module równoważenia obciążenia. Wejściowy punkt końcowy każdego wystąpienia jest mapowany do określonego portu z zakresu portów to możliwe. Ten element jest elementem nadrzędnym `AllocatePublicPortFrom` elementu.

`InstanceInputEndpoint` Elementu jest dostępne tylko za pomocą wersji zestawu Azure SDK 1.7 lub nowszej.

W poniższej tabeli opisano atrybuty `InstanceInputEndpoint` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Unikatowa nazwa punktu końcowego.|
|localPort|int|Wymagany. Określa port wewnętrzny, który będzie nasłuchiwać wszystkich wystąpień roli w celu odbierania ruchu przychodzącego przekazywane z modułu równoważenia obciążenia. Możliwe wartości z zakresu od 1 do 65535 (włącznie).|
|protokół|string|Wymagany. Protokół transportowy wewnętrznego punktu końcowego. Możliwe wartości to `udp` lub `tcp`. Użyj `tcp` dla protokołu http/https na podstawie ruchu.|

##  <a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom
`AllocatePublicPortFrom` Element opisuje zakres portu publicznego, który może być używane przez klientów zewnętrznych do dostępu wejściowy punkt końcowy każdego wystąpienia. Publiczny numer portu (VIP) jest przydzielany z tego zakresu i przypisane do każdego punktu końcowego wystąpienia poszczególnych ról podczas wdrażania dzierżawy i aktualizacji. Ten element jest elementem nadrzędnym `FixedPortRange` elementu.

`AllocatePublicPortFrom` Elementu jest dostępne tylko za pomocą wersji zestawu Azure SDK 1.7 lub nowszej.

##  <a name="FixedPort"></a> FixedPort
`FixedPort` Element Określa port wewnętrzny punkt końcowy umożliwia ładowanie połączeń o zrównoważonym obciążeniu w punkcie końcowym.

`FixedPort` Elementu jest dostępne tylko przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli opisano atrybuty `FixedPort` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|port|int|Wymagany. Port wewnętrzny punkt końcowy. Ma to taki sam efekt jak ustawienie `FixedPortRange` min i max do tego samego portu.<br /><br /> Możliwe wartości z zakresu od 1 do 65535 (włącznie). (zestaw Azure SDK w wersji 1.7 lub nowszej).|

##  <a name="FixedPortRange"></a> FixedPortRange
`FixedPortRange` Element określa zakres portów, które są przypisane do wewnętrznego punktu końcowego lub wystąpienia wejściowy punkt końcowy, a następnie ustawia port używany dla obciążenia równoważenia połączenia w punkcie końcowym.

> [!NOTE]
>  `FixedPortRange` Element działa inaczej w zależności od tego elementu, w którym się znajduje. Gdy `FixedPortRange` element znajduje się w `InternalEndpoint` elementu, spowoduje to otwarcie wszystkie porty w module równoważenia obciążenia w zakresie min i max atrybuty dla wszystkich maszyn wirtualnych, na których działa rola. Gdy `FixedPortRange` element znajduje się w `InstanceInputEndpoint` elementu, zostanie otwarty tylko jeden port w zakresie minimalna i maksymalna liczba atrybutów na każdej maszynie wirtualnej, na którym działa rola.

`FixedPortRange` Elementu jest dostępne tylko przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli opisano atrybuty `FixedPortRange` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|min.|int|Wymagany. Minimalna port z zakresu. Możliwe wartości z zakresu od 1 do 65535 (włącznie). (zestaw Azure SDK w wersji 1.7 lub nowszej).|
|maks.|string|Wymagany. Maksymalna port z zakresu. Możliwe wartości z zakresu od 1 do 65535 (włącznie). (zestaw Azure SDK w wersji 1.7 lub nowszej).|

##  <a name="Certificates"></a> Certyfikaty
`Certificates` Element w tym artykule opisano kolekcji certyfikatów dla roli procesu roboczego. Ten element jest elementem nadrzędnym `Certificate` elementu. Rola może mieć dowolną liczbę certyfikatów skojarzonych. Aby uzyskać więcej informacji na temat korzystania z elementu certyfikatów, zobacz [modyfikowania pliku definicji usługi z certyfikatem](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a> Certyfikat
`Certificate` Element w tym artykule opisano certyfikat, który jest skojarzony z rolą proces roboczy.

W poniższej tabeli opisano atrybuty `Certificate` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Nazwa tego certyfikatu, który służy do odwoływania się do niego, gdy jest on skojarzony z przy użyciu protokołu HTTPS `InputEndpoint` elementu.|
|storeLocation|string|Wymagany. Lokalizacja magazynu certyfikatów, gdzie można znaleźć tego certyfikatu na komputerze lokalnym. Możliwe wartości to `CurrentUser` i `LocalMachine`.|
|storeName|string|Wymagany. Nazwa magazynu certyfikatów, gdy ten certyfikat znajduje się na komputerze lokalnym. Możliwe wartości obejmują nazwy magazynu wbudowanych `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`, lub dowolną nazwę magazynu niestandardowego. Jeśli określono nazwy magazyn niestandardowy, magazynu jest tworzone automatycznie.|
|permissionLevel|string|Opcjonalny. Określa uprawnienia dostępu do procesów roli. Jeśli chcesz, aby mieć możliwość dostępu do klucza prywatnego, a następnie określ tylko z podwyższonym poziomem uprawnień procesów `elevated` uprawnień. `limitedOrElevated` uprawnienie umożliwia wszystkie procesy roli do dostępu do klucza prywatnego. Możliwe wartości to `limitedOrElevated` lub `elevated`. Wartość domyślna to `limitedOrElevated`.|

##  <a name="Imports"></a> Importy
`Imports` Element opisuje kolekcję modułów importu dla roli procesu roboczego, które Dodawanie składników do systemu operacyjnego gościa. Ten element jest elementem nadrzędnym `Import` elementu. Ten element jest opcjonalny, a rola może mieć tylko jeden blok środowiska uruchomieniowego.

`Imports` Elementu jest dostępne tylko przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

##  <a name="Import"></a> Import
`Import` Element Określa moduł, który chcesz dodać do systemu operacyjnego gościa.

`Import` Elementu jest dostępne tylko przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli opisano atrybuty `Import` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|moduleName|string|Wymagany. Nazwa modułu do zaimportowania. Nieprawidłowy import moduły są:<br /><br /> -RemoteAccess<br />-RemoteForwarder<br />-Diagnostics<br /><br /> Moduły RemoteAccess i RemoteForwarder umożliwiają skonfigurowanie wystąpienia roli dla połączeń pulpitu zdalnego. Aby uzyskać więcej informacji, zobacz [Podłączanie pulpitu zdalnego włączyć](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Moduł diagnostyki umożliwia zbieranie danych diagnostycznych dla wystąpienia roli|

##  <a name="Runtime"></a> Środowisko uruchomieniowe
`Runtime` Element opisuje kolekcję ustawień zmiennych środowiskowych dla roli procesu roboczego, które kontrolują środowiska wykonawczego procesu hosta platformy Azure. Ten element jest elementem nadrzędnym `Environment` elementu. Ten element jest opcjonalny, a rola może mieć tylko jeden blok środowiska uruchomieniowego.

`Runtime` Elementu jest dostępne tylko przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli opisano atrybuty `Runtime` elementu:

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|executionContext|string|Opcjonalny. Określa kontekst, w której uruchomiono proces roli. Domyślny kontekst jest `limited`.<br /><br /> -   `limited` — Ten proces zostanie uruchomiony bez uprawnień administratora.<br />-   `elevated` — Ten proces jest uruchamiany z uprawnieniami administratora.|

##  <a name="Environment"></a> środowisko
`Environment` Element opisuje kolekcję ustawień zmiennych środowiskowych dla roli proces roboczy. Ten element jest elementem nadrzędnym `Variable` elementu. Rola może mieć dowolną liczbę zmiennych środowiskowych ustawionych.

##  <a name="Variable"></a> Zmienna
`Variable` Element Określa zmienną środowiskową, aby ustawić operacyjnego gościa.

`Variable` Elementu jest dostępne tylko przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli opisano atrybuty `Variable` elementu:

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|name|string|Wymagany. Nazwa zmiennej środowiskowej, aby ustawić.|
|value|string|Opcjonalny. Wartość można ustawić dla zmiennej środowiskowej. Musi zawierać atrybut value lub `RoleInstanceValue` elementu.|

##  <a name="RoleInstanceValue"></a> RoleInstanceValue
`RoleInstanceValue` Element określa wyrażenie xPath, z którego można pobrać wartości zmiennej.

W poniższej tabeli opisano atrybuty `RoleInstanceValue` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|wyrażenie XPath|string|Opcjonalny. Ścieżka do lokalizacji Ustawienia wdrożenia dla tego wystąpienia. Aby uzyskać więcej informacji, zobacz [zmienne konfiguracji za pomocą wyrażenia XPath](cloud-services-role-config-xpath.md).<br /><br /> Musi zawierać atrybut value lub `RoleInstanceValue` elementu.|

##  <a name="EntryPoint"></a> EntryPoint
`EntryPoint` Element określa punkt wejścia dla roli. Ten element jest elementem nadrzędnym `NetFxEntryPoint` elementów. Elementy te umożliwiają określenie aplikacji innej niż domyślna WaWorkerHost.exe do pełnienia roli punktu wejścia.

`EntryPoint` Element jest tylko dostępne przy użyciu zestawu Azure SDK w wersji 1.5 lub nowszej.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint
`NetFxEntryPoint` Element Określa program do uruchomienia dla roli.

> [!NOTE]
>  `NetFxEntryPoint` Element jest tylko dostępne przy użyciu zestawu Azure SDK w wersji 1.5 lub nowszej.

W poniższej tabeli opisano atrybuty `NetFxEntryPoint` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|AssemblyName|string|Wymagany. Ścieżka i nazwa pliku zestawu zawierającego punkt wejścia. Ścieżka jest określana względem folderu  **\\%ROLEROOT%\Approot** (nie należy określać  **\\%ROLEROOT%\Approot** w `commandLine`, zakłada się). **ROLEROOT %** zmienną środowiskową obsługiwany przez platformę Azure, reprezentuje lokalizację folderu głównego dla roli użytkownika.  **\\%ROLEROOT%\Approot** folder reprezentuje folder aplikacji dla roli użytkownika.|
|targetFrameworkVersion|string|Wymagany. Wersja programu .NET framework, na którym został skompilowany zestaw. Na przykład `targetFrameworkVersion="v4.0"`.|

##  <a name="ProgramEntryPoint"></a> ProgramEntryPoint
`ProgramEntryPoint` Element Określa program do uruchomienia dla roli. `ProgramEntryPoint` Element umożliwia określenie punktu wejścia programu, który nie jest oparty na zestaw .NET.

> [!NOTE]
>  `ProgramEntryPoint` Element jest tylko dostępne przy użyciu zestawu Azure SDK w wersji 1.5 lub nowszej.

W poniższej tabeli opisano atrybuty `ProgramEntryPoint` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|Wiersz polecenia|string|Wymagany. Ścieżka, nazwa pliku i argumenty wiersza polecenia programu do wykonania. Ścieżka jest określana względem folderu **%ROLEROOT%\Approot** (nie należy określać **%ROLEROOT%\Approot** w wierszu polecenia, zakłada się). **ROLEROOT %** zmienną środowiskową obsługiwany przez platformę Azure, reprezentuje lokalizację folderu głównego dla roli użytkownika. **%ROLEROOT%\Approot** folder reprezentuje folder aplikacji dla roli użytkownika.<br /><br /> Jeśli program się kończy, rolę recyklingowi, więc ogólnie jest Ustaw, aby kontynuować działanie, zamiast programu, który po prostu uruchamia się i uruchamia zadanie skończoną program.|
|setReadyOnProcessStart|wartość logiczna|Wymagany. Określa, czy wystąpienie roli czeka na sygnał, że jej ponownym uruchomieniu programu wiersza polecenia. Ta wartość musi być równa `true` w tej chwili. Ustawienie wartości `false` jest zarezerwowany do użytku w przyszłości.|

##  <a name="Startup"></a> Uruchamianie
`Startup` Element w tym artykule opisano kolekcji zadań, które są uruchamiane podczas uruchamiania roli. Ten element może być elementem nadrzędnym `Variable` elementu. Aby uzyskać więcej informacji o korzystaniu z zadania uruchamiania roli, zobacz [Konfigurowanie zadań uruchamiania](cloud-services-startup-tasks.md). Ten element jest opcjonalny, a rola może mieć tylko jeden blok uruchamiania.

W poniższej tabeli opisano atrybut `Startup` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|priority|int|Tylko do użytku wewnętrznego.|

##  <a name="Task"></a> Zadanie
`Task` Element określa zadania uruchamiania, który ma miejsce podczas uruchamiania roli. Zadania uruchamiania może służyć do wykonywania zadań, które przygotowują rolę do uruchamiania takich instalacji składników oprogramowania lub uruchamiania innych aplikacji. Zadania wykonywane w kolejności, w jakiej są wyświetlane w ramach `Startup` bloku elementu.

`Task` Elementu jest dostępne tylko przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli opisano atrybuty `Task` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|Wiersz polecenia|string|Wymagany. Skryptu, na przykład plik CMD, zawierający polecenia do uruchomienia. Pliki rozruchowe polecenia i usługi batch musi zostać zapisany w formacie ANSI. Formaty plików, które ustawić znacznik kolejności bajtów na początku tego pliku nie będzie przetwarzać poprawnie.|
|executionContext|string|Określa kontekst, w którym skrypt jest uruchamiany.<br /><br /> -   `limited` [Domyślne] — z tego samego uprawnieniami jako rola procesu hostingu.<br />-   `elevated` — Uruchomione z uprawnieniami administratora.|
|taskType|string|Określa zachowanie wykonywania polecenia.<br /><br /> -   `simple` [Domyślnie] — System oczekuje na zadanie zakończyć działanie przed wszystkie inne zadania będą uruchamiane.<br />-   `background` — System czeka zadania zakończyć pracę.<br />-   `foreground` — Podobnie jak w tle, ale rola nie zostanie ponownie uruchomiona do momentu zamknąć wszystkich zadań pierwszego planu.|

##  <a name="Contents"></a> Zawartość
`Contents` Element w tym artykule opisano zbiór zawartości dla roli procesu roboczego. Ten element jest elementem nadrzędnym `Content` elementu.

`Contents` Element jest tylko dostępne przy użyciu zestawu Azure SDK w wersji 1.5 lub nowszej.

##  <a name="Content"></a> Zawartość
`Content` Element definiuje lokalizacji źródłowej zawartości, które mają być kopiowane do maszyny wirtualnej platformy Azure i ścieżkę docelową, do której są kopiowane.

`Content` Element jest tylko dostępne przy użyciu zestawu Azure SDK w wersji 1.5 lub nowszej.

W poniższej tabeli opisano atrybuty `Content` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|miejsce docelowe|string|Wymagany. Lokalizacja na maszynie wirtualnej platformy Azure, do którego jest umieszczana zawartość. Ta lokalizacja jest względną wobec folderu **%ROLEROOT%\Approot**.|

Ten element jest elementem nadrzędnym `SourceDirectory` elementu.

##  <a name="SourceDirectory"></a> SourceDirectory
`SourceDirectory` Element definiuje katalogu lokalnego, z którego zawartość zostanie skopiowana. Ten element służy do określania lokalnych zawartość można skopiować do maszyny wirtualnej platformy Azure.

`SourceDirectory` Element jest tylko dostępne przy użyciu zestawu Azure SDK w wersji 1.5 lub nowszej.

W poniższej tabeli opisano atrybuty `SourceDirectory` elementu.

| Atrybut | Type | Opis |
| --------- | ---- | ----------- |
|ścieżka|string|Wymagany. Względna lub bezwzględna ścieżka katalogu lokalnego, którego zawartość zostanie skopiowana do maszyny wirtualnej platformy Azure. Rozszerzanie zmiennych środowiskowych w ścieżce katalogu jest obsługiwane.|

## <a name="see-also"></a>Zobacz też
[Chmury usługi (model klasyczny) definicji schematu](schema-csdef-file.md)