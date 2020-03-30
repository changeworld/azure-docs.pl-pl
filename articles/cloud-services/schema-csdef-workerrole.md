---
title: Schemat obnażania schematu zaroli usług w chmurze platformy Azure | Dokumenty firmy Microsoft
description: Rola procesu roboczego platformy Azure jest używana do rozwoju uogólnionego i może wykonywać przetwarzanie w tle dla roli sieci web. Dowiedz się więcej o schemacie roli procesu roboczego platformy Azure.
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
author: tgore03
ms.author: tagore
ms.openlocfilehash: 26225442c72fb209bb1ac4cd2bf4777fb39542fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534375"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>Schemat definicji definicji usług w chmurze platformy Azure
Rola procesu roboczego platformy Azure jest rolą, która jest przydatna dla rozwoju uogólnionego i może wykonywać przetwarzanie w tle dla roli sieci web.

Domyślnym rozszerzeniem pliku definicji usługi jest .csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Podstawowy schemat definicji usługi dla roli procesu roboczego.
Podstawowy format pliku definicji usługi zawierającego rolę procesu roboczego jest następujący.

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
Plik definicji usługi zawiera te elementy, opisane szczegółowo w kolejnych sekcjach w tym temacie:

[Robotniczarol](#WorkerRole)

[Configurationsettings](#ConfigurationSettings)

[Ustawienie](#Setting)

[Zasoby lokalne](#LocalResources)

[Localstorage](#LocalStorage)

[Punkty końcowe](#Endpoints)

[Punkt wprowadzania](#InputEndpoint)

[Punkt wewnętrzny](#InternalEndpoint)

[Punkt instancja](#InstanceInputEndpoint)

[PrzydzielpublicznieOd](#AllocatePublicPortFrom)

[Port stały](#FixedPort)

[Stała relacja portów](#FixedPortRange)

[Certyfikaty](#Certificates)

[Certyfikat](#Certificate)

[Przywozu](#Imports)

[Import](#Import)

[Środowisko uruchomieniowe](#Runtime)

[Środowisko](#Environment)

[EntryPoint](#EntryPoint)

[NetFxEntryPoint (NetFxEntryPoint)](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Zmienna](#Variable)

[Wartość roli](#RoleInstanceValue)

[Uruchamianie](#Startup)

[Zadanie](#Task)

[Zawartość](#Contents)

[Zawartość](#Content)

[ŹródłoDirectory](#SourceDirectory)

##  <a name="workerrole"></a><a name="WorkerRole"></a>Robotniczarol
Element `WorkerRole` opisuje rolę, która jest przydatna dla rozwoju uogólnionego i może wykonywać przetwarzanie w tle dla roli sieci web. Usługa może zawierać zero lub więcej ról procesu roboczego.

W poniższej tabeli `WorkerRole` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagany. Nazwa roli procesu roboczego. Nazwa roli musi być unikatowa.|
|enableNativeCodeExecution (Umożliwianie ujedn.|wartość logiczna|Element opcjonalny. Wartością domyślną jest `true`; wykonanie kodu macierzystego i pełne zaufanie są domyślnie włączone. Ustaw ten atrybut, aby `false` wyłączyć wykonanie kodu macierzystego dla roli procesu roboczego i zamiast tego użyj częściowego zaufania platformy Azure.|
|rozmiar maszyn wirtualnych|ciąg|Element opcjonalny. Ustaw tę wartość, aby zmienić rozmiar maszyny wirtualnej, która jest przydzielona do tej roli. Wartością domyślną jest `Small`. Aby uzyskać listę możliwych rozmiarów maszyn wirtualnych i ich atrybutów, zobacz [Rozmiary maszyn wirtualnych dla usług w chmurze](cloud-services-sizes-specs.md).|

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a>Configurationsettings
Element `ConfigurationSettings` opisuje kolekcję ustawień konfiguracji dla roli procesu roboczego. Ten element jest elementem nadrzędnym `Setting` elementu.

##  <a name="setting"></a><a name="Setting"></a>Ustawienie
Element `Setting` opisuje parę nazw i wartości, która określa ustawienie konfiguracji dla wystąpienia roli.

W poniższej tabeli `Setting` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagany. Unikatowa nazwa ustawienia konfiguracji.|

Ustawienia konfiguracji roli to pary nazw i wartości, które są zadeklarowane w pliku definicji usługi i ustawione w pliku konfiguracji usługi.

##  <a name="localresources"></a><a name="LocalResources"></a>Zasoby lokalne
Element `LocalResources` opisuje kolekcję zasobów magazynu lokalnego dla roli procesu roboczego. Ten element jest elementem nadrzędnym `LocalStorage` elementu.

##  <a name="localstorage"></a><a name="LocalStorage"></a>Localstorage
Element `LocalStorage` identyfikuje zasób magazynu lokalnego, który zapewnia miejsce w systemie plików dla usługi w czasie wykonywania. Rola może zdefiniować zero lub więcej zasobów magazynu lokalnego.

> [!NOTE]
>  Element `LocalStorage` może pojawić się jako `WorkerRole` element podrzędny elementu do obsługi zgodności z wcześniejszymi wersjami pakietu Azure SDK.

W poniższej tabeli `LocalStorage` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagany. Unikatowa nazwa lokalnego sklepu.|
|cleanOnRoleRecycle|wartość logiczna|Element opcjonalny. Wskazuje, czy magazyn lokalny powinien zostać wyczyszczony po ponownym uruchomieniu roli. Wartością `true`domyślną jest .|
|rozmiarInMb|int|Element opcjonalny. Żądana ilość miejsca do przydzielenia dla magazynu lokalnego w MB. Jeśli nie zostanie określony, domyślne przydzielone miejsce do magazynowania wynosi 100 MB. Minimalna ilość miejsca do magazynowania, które mogą być przydzielone jest 1 MB.<br /><br /> Maksymalny rozmiar zasobów lokalnych zależy od rozmiaru maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Rozmiary maszyn wirtualnych dla usług w chmurze](cloud-services-sizes-specs.md).|

Nazwa katalogu przydzielonego do zasobu magazynu lokalnego odpowiada wartości podanej dla atrybutu nazwa.

##  <a name="endpoints"></a><a name="Endpoints"></a>Punkty końcowe
Element `Endpoints` opisuje zbieranie danych wejściowych (zewnętrznych), wewnętrznych i wystąpień wejściowych punktów końcowych dla roli. Ten element jest elementem nadrzędnym `InputEndpoint`elementu , `InternalEndpoint`i `InstanceInputEndpoint` elementów.

Wejściowe i wewnętrzne punkty końcowe są przydzielane oddzielnie. Usługa może mieć łącznie 25 wejściowych, wewnętrznych i wystąpień wejściowych punktów końcowych, które mogą być przydzielane między 25 ról dozwolonych w usłudze. Na przykład jeśli mają 5 ról można przydzielić 5 wejściowych punktów końcowych na rolę lub można przydzielić 25 wejściowych punktów końcowych do jednej roli lub można przydzielić 1 wejściowego punktu końcowego do 25 ról.

> [!NOTE]
>  Każda wdrożona rola wymaga jednego wystąpienia na rolę. Domyślna inicjowanie obsługi administracyjnej dla subskrypcji jest ograniczona do 20 rdzeni i w związku z tym jest ograniczona do 20 wystąpień roli. Jeśli aplikacja wymaga więcej wystąpień niż jest podana przez domyślne inicjowania obsługi administracyjnej [zobacz rozliczenia, zarządzanie subskrypcjami i obsługi przydziałów](https://azure.microsoft.com/support/options/) więcej informacji na temat zwiększania przydziału.

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a>Punkt wprowadzania
Element `InputEndpoint` opisuje zewnętrzny punkt końcowy roli procesu roboczego.

Można zdefiniować wiele punktów końcowych, które są kombinacją punktów końcowych HTTP, HTTPS, UDP i TCP. Można określić dowolny numer portu, który wybierzesz dla wejściowego punktu końcowego, ale numery portów określone dla każdej roli w usłudze muszą być unikatowe. Jeśli na przykład określisz, że rola używa portu 80 dla protokołu HTTP i portu 443 dla protokołu HTTPS, możesz określić, że druga rola używa portu 8080 dla protokołu HTTP i portu 8043 dla protokołu HTTPS.

W poniższej tabeli `InputEndpoint` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagany. Unikatowa nazwa zewnętrznego punktu końcowego.|
|Protokół|ciąg|Wymagany. Protokół transportu dla zewnętrznego punktu końcowego. Dla roli pracownika możliwe `HTTP`wartości `HTTPS` `UDP`to `TCP`, , , lub .|
|port|int|Wymagany. Port dla zewnętrznego punktu końcowego. Można określić dowolny numer portu, który wybierzesz, ale numery portów określone dla każdej roli w usłudze muszą być unikatowe.<br /><br /> Możliwe wartości wahają się od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1.7 lub nowszej).|
|certyfikat|ciąg|Wymagane dla punktu końcowego HTTPS. Nazwa certyfikatu zdefiniowanego `Certificate` przez element.|
|Port_lokalny|int|Element opcjonalny. Określa port używany dla połączeń wewnętrznych w punkcie końcowym. Atrybut `localPort` mapuje port zewnętrzny w punkcie końcowym do portu wewnętrznego w roli. Jest to przydatne w scenariuszach, w których rola musi komunikować się z wewnętrznym składnikiem na porcie, który różni się od tego, który jest narażony na zewnątrz.<br /><br /> Jeśli nie zostanie określony, wartość `localPort` jest `port` taka sama jak atrybut. Ustaw wartość `localPort` na "*", aby automatycznie przypisać nieprzydzielony port, który jest wykrywalny przy użyciu interfejsu API środowiska wykonawczego.<br /><br /> Możliwe wartości wahają się od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1.7 lub nowszej).<br /><br /> Atrybut `localPort` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.3 lub nowszej.|
|ignoreRoleInstanceStatus ignoruje|wartość logiczna|Element opcjonalny. Gdy wartość tego atrybutu jest `true`ustawiona na , stan usługi jest ignorowany i punkt końcowy nie zostanie usunięty przez moduł równoważenia obciążenia. Ustawienie tej `true` wartości jest przydatne do debugowania zajęty wystąpień usługi. Wartością domyślną jest `false`. **Uwaga:** Punkt końcowy nadal może odbierać ruch, nawet jeśli rola nie jest w stanie Gotowy.|
|loadBalancerProbe|ciąg|Element opcjonalny. Nazwa sondy modułu równoważenia obciążenia skojarzonej z wejściowym punktem końcowym. Aby uzyskać więcej informacji, zobacz [Schemat LoadBalancerProbe](schema-csdef-loadbalancerprobe.md).|

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a>Punkt wewnętrzny
Element `InternalEndpoint` opisuje wewnętrzny punkt końcowy roli procesu roboczego. Wewnętrzny punkt końcowy jest dostępny tylko dla innych wystąpień roli uruchomionych w ramach usługi; nie jest dostępna dla klientów spoza usługi. Rola procesu roboczego może mieć maksymalnie pięć wewnętrznych punktów końcowych HTTP, UDP lub TCP.

W poniższej tabeli `InternalEndpoint` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagany. Unikatowa nazwa wewnętrznego punktu końcowego.|
|Protokół|ciąg|Wymagany. Protokół transportu dla wewnętrznego punktu końcowego. Możliwe wartości `HTTP` `TCP`to `UDP`, `ANY`, , lub .<br /><br /> Wartość `ANY` określa, że każdy protokół, każdy port jest dozwolony.|
|port|int|Element opcjonalny. Port używany dla połączeń z równoważenia obciążenia wewnętrznego w punkcie końcowym. Punkt końcowy z równoważenia obciążenia używa dwóch portów. Port używany dla publicznego adresu IP i portu używanego na prywatnym adresie IP. Zazwyczaj są to takie same, ale można wybrać użycie różnych portów.<br /><br /> Możliwe wartości wahają się od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1.7 lub nowszej).<br /><br /> Atrybut `Port` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.3 lub nowszej.|

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a>Punkt instancja
Element `InstanceInputEndpoint` opisuje punkt końcowy wejściowego wystąpienia do roli procesu roboczego. Wejściowy punkt końcowy wystąpienia jest skojarzony z wystąpieniem określonej roli przy użyciu przekazywania portów w równoważeniu obciążenia. Każdy punkt końcowy wejściowego wystąpienia jest mapowany do określonego portu z zakresu możliwych portów. Ten element jest elementem nadrzędnym `AllocatePublicPortFrom` elementu.

Element `InstanceInputEndpoint` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.7 lub nowszej.

W poniższej tabeli `InstanceInputEndpoint` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagany. Unikatowa nazwa punktu końcowego.|
|Port_lokalny|int|Wymagany. Określa port wewnętrzny, który będą nasłuchiwać wszystkich wystąpień roli w celu odbierania ruchu przychodzącego przesyłany dalej z modułu równoważenia obciążenia. Możliwe wartości wahają się od 1 do 65535 włącznie.|
|Protokół|ciąg|Wymagany. Protokół transportu dla wewnętrznego punktu końcowego. Możliwe wartości to `udp` lub `tcp`. Użyj `tcp` dla ruchu opartego na http/https.|

##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a>PrzydzielpublicznieOd
Element `AllocatePublicPortFrom` opisuje zakres portów publicznych, które mogą być używane przez klientów zewnętrznych, aby uzyskać dostęp do każdego punktu końcowego wejściowego wystąpienia. Numer portu publicznego (VIP) jest przydzielany z tego zakresu i przypisywany do każdego punktu końcowego wystąpienia roli podczas wdrażania i aktualizacji dzierżawy. Ten element jest elementem nadrzędnym `FixedPortRange` elementu.

Element `AllocatePublicPortFrom` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.7 lub nowszej.

##  <a name="fixedport"></a><a name="FixedPort"></a>Port stały
Element `FixedPort` określa port dla wewnętrznego punktu końcowego, który umożliwia połączenia z równoważenia obciążenia w punkcie końcowym.

Element `FixedPort` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli `FixedPort` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|port|int|Wymagany. Port dla wewnętrznego punktu końcowego. Ma to taki sam `FixedPortRange` efekt jak ustawienie min i max na tym samym porcie.<br /><br /> Możliwe wartości wahają się od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1.7 lub nowszej).|

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a>Stała relacja portów
Element `FixedPortRange` określa zakres portów, które są przypisane do wewnętrznego punktu końcowego końcowego lub punktu końcowego wejściowego wystąpienia i ustawia port używany dla połączeń z równoważenia obciążenia w punkcie końcowym.

> [!NOTE]
>  Element `FixedPortRange` działa inaczej w zależności od elementu, w którym się znajduje. Gdy `FixedPortRange` element znajduje `InternalEndpoint` się w elemencie, otwiera wszystkie porty na modułze równoważenia obciążenia w zakresie atrybutów min i max dla wszystkich maszyn wirtualnych, na których działa rola. Gdy `FixedPortRange` element znajduje `InstanceInputEndpoint` się w elemencie, otwiera tylko jeden port w zakresie atrybutów min i max na każdej maszynie wirtualnej z uruchomioną rolą.

Element `FixedPortRange` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli `FixedPortRange` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|min|int|Wymagany. Minimalny port w zakresie. Możliwe wartości wahają się od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1.7 lub nowszej).|
|max|ciąg|Wymagany. Maksymalny port w zakresie. Możliwe wartości wahają się od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1.7 lub nowszej).|

##  <a name="certificates"></a><a name="Certificates"></a>Certyfikaty
Element `Certificates` opisuje kolekcję certyfikatów dla roli procesu roboczego. Ten element jest elementem nadrzędnym `Certificate` elementu. Rola może mieć dowolną liczbę skojarzonych certyfikatów. Aby uzyskać więcej informacji na temat używania elementu certyfikatów, zobacz [Modyfikowanie pliku definicji usługi za pomocą certyfikatu](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="certificate"></a><a name="Certificate"></a>Certyfikat
Element `Certificate` opisuje certyfikat, który jest skojarzony z rolą procesu roboczego.

W poniższej tabeli `Certificate` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagany. Nazwa tego certyfikatu, który jest używany do odwoływania się do `InputEndpoint` niego, gdy jest skojarzony z elementem HTTPS.|
|Storelocation|ciąg|Wymagany. Lokalizacja magazynu certyfikatów, w której ten certyfikat można znaleźć na komputerze lokalnym. Możliwe wartości `CurrentUser` `LocalMachine`są i .|
|Storename|ciąg|Wymagany. Nazwa magazynu certyfikatów, w którym ten certyfikat znajduje się na komputerze lokalnym. Możliwe wartości obejmują wbudowane `My`nazwy `Root` `CA`sklepu `Trust` `Disallowed`, `TrustedPeople` `TrustedPublisher`, `AuthRoot` `AddressBook`, , , , , , , , lub dowolną niestandardową nazwę sklepu. Jeśli określono niestandardową nazwę sklepu, magazyn jest tworzony automatycznie.|
|uprawnieniaPoziom|ciąg|Element opcjonalny. Określa uprawnienia dostępu przyznane procesom roli. Jeśli chcesz, aby tylko procesy z podwyższonym poziomem `elevated` uprawnień mogły uzyskać dostęp do klucza prywatnego, określ uprawnienie. `limitedOrElevated`uprawnienie umożliwia wszystkim procesom roli dostęp do klucza prywatnego. Możliwe wartości to `limitedOrElevated` lub `elevated`. Wartością domyślną jest `limitedOrElevated`.|

##  <a name="imports"></a><a name="Imports"></a>Przywozu
Element `Imports` opisuje kolekcję modułów importu dla roli procesu roboczego, które dodają składniki do systemu operacyjnego gościa. Ten element jest elementem nadrzędnym `Import` elementu. Ten element jest opcjonalny i rola może mieć tylko jeden blok środowiska uruchomieniowego.

Element `Imports` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.3 lub nowszej.

##  <a name="import"></a><a name="Import"></a>Importu
Element `Import` określa moduł, który ma być dodawany do systemu operacyjnego gościa.

Element `Import` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli `Import` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|nazwa modułu|ciąg|Wymagany. Nazwa modułu do zaimportowania. Prawidłowe moduły importu to:<br /><br /> - RemoteAccess<br />- RemoteForwarder<br />- Diagnostyka<br /><br /> Moduły Dostępu zdalnego i programu RemoteForwarder umożliwiają skonfigurowanie wystąpienia roli dla połączeń pulpitu zdalnego. Aby uzyskać więcej informacji, zobacz [Włączanie połączenia pulpitu zdalnego](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Moduł Diagnostyka umożliwia zbieranie danych diagnostycznych dla wystąpienia roli|

##  <a name="runtime"></a><a name="Runtime"></a>Środowiska wykonawczego
Element `Runtime` opisuje kolekcję ustawień zmiennych środowiskowych dla roli procesu roboczego, które kontrolują środowisko wykonawcze procesu hosta platformy Azure. Ten element jest elementem nadrzędnym `Environment` elementu. Ten element jest opcjonalny i rola może mieć tylko jeden blok środowiska uruchomieniowego.

Element `Runtime` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli `Runtime` opisano atrybuty elementu:

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|Executioncontext|ciąg|Element opcjonalny. Określa kontekst, w którym jest uruchamiany proces roli. Domyślnym kontekstem jest `limited`.<br /><br /> -   `limited`– Proces jest uruchamiany bez uprawnień administratora.<br />-   `elevated`– Proces jest uruchamiany z uprawnieniami administratora.|

##  <a name="environment"></a><a name="Environment"></a>Środowiska
Element `Environment` opisuje zbiór ustawień zmiennej środowiskowej dla roli procesu roboczego. Ten element jest elementem nadrzędnym `Variable` elementu. Rola może mieć dowolną liczbę zmiennych środowiskowych ustawionych.

##  <a name="variable"></a><a name="Variable"></a>Zmiennej
Element `Variable` określa zmienną środowiskową ustawioną w trybie obsługi gościa.

Element `Variable` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli `Variable` opisano atrybuty elementu:

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagany. Nazwa zmiennej środowiskowej do ustawienia.|
|value|ciąg|Element opcjonalny. Wartość ustawiona dla zmiennej środowiskowej. Należy dołączyć atrybut wartości lub `RoleInstanceValue` element.|

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a>Wartość roli
Element `RoleInstanceValue` określa xPath, z którego można pobrać wartość zmiennej.

W poniższej tabeli `RoleInstanceValue` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|Xpath|ciąg|Element opcjonalny. Ścieżka lokalizacji ustawień wdrażania dla wystąpienia. Aby uzyskać więcej informacji, zobacz [Zmienne konfiguracyjne za pomocą programu XPath](cloud-services-role-config-xpath.md).<br /><br /> Należy dołączyć atrybut wartości lub `RoleInstanceValue` element.|

##  <a name="entrypoint"></a><a name="EntryPoint"></a>Entrypoint
Element `EntryPoint` określa punkt wejścia dla roli. Ten element jest elementem nadrzędnym `NetFxEntryPoint` elementów. Te elementy umożliwiają określenie aplikacji innej niż domyślna WaWorkerHost.exe do działania jako punkt wejścia roli.

Element `EntryPoint` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.5 lub nowszej.

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a>NetFxEntryPoint (NetFxEntryPoint)
Element `NetFxEntryPoint` określa program do uruchomienia dla roli.

> [!NOTE]
>  Element `NetFxEntryPoint` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.5 lub nowszej.

W poniższej tabeli `NetFxEntryPoint` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|Assemblyname|ciąg|Wymagany. Ścieżka i nazwa pliku zestawu zawierającego punkt wejścia. Ścieżka jest względem folderu `commandLine` ** \\%ROLEROOT%\Approot** (nie należy określać ** \\%ROLEROOT%\Approot** in , zakłada się). **%ROLEROOT%** jest zmienną środowiskową obsługiwaną przez platformę Azure i reprezentuje lokalizację folderu głównego dla Twojej roli. Folder ** \\%ROLEROOT%\Approot** reprezentuje folder aplikacji dla Twojej roli.|
|Targetframeworkversion|ciąg|Wymagany. Wersja platformy .NET, na której został zbudowany zestaw. Na przykład `targetFrameworkVersion="v4.0"`.|

##  <a name="programentrypoint"></a><a name="ProgramEntryPoint"></a>ProgramEntryPoint
Element `ProgramEntryPoint` określa program do uruchomienia dla roli. Element `ProgramEntryPoint` umożliwia określenie punktu wejścia programu, który nie jest oparty na zestawie .NET.

> [!NOTE]
>  Element `ProgramEntryPoint` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.5 lub nowszej.

W poniższej tabeli `ProgramEntryPoint` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|Commandline|ciąg|Wymagany. Ścieżka, nazwa pliku i wszelkie argumenty wiersza polecenia programu do wykonania. Ścieżka jest względem folderu **%ROLEROOT%\Approot** (nie określaj **%ROLEROOT%\Approot** w commandLine, zakłada się). **%ROLEROOT%** jest zmienną środowiskową obsługiwaną przez platformę Azure i reprezentuje lokalizację folderu głównego dla Twojej roli. Folder **%ROLEROOT%\Approot** reprezentuje folder aplikacji dla Twojej roli.<br /><br /> Jeśli program się kończy, rola jest poddawana recyklingowi, więc zazwyczaj ustaw program tak, aby nadal działał, zamiast być programem, który dopiero się uruchamia i uruchamia skończone zadanie.|
|setReadyOnProcessStart|wartość logiczna|Wymagany. Określa, czy wystąpienie roli czeka na sygnał, że program wiersza polecenia zostanie uruchomiony. Wartość ta musi `true` być ustawiona na w tej chwili. Ustawienie wartości `false` jest zarezerwowane do wykorzystania w przyszłości.|

##  <a name="startup"></a><a name="Startup"></a>Uruchamiania
Element `Startup` opisuje zbiór zadań, które są uruchamiane po uruchomieniu roli. Ten element może być `Variable` elementem nadrzędnym elementu. Aby uzyskać więcej informacji na temat korzystania z zadań uruchamiania ról, zobacz [Jak skonfigurować zadania uruchamiania](cloud-services-startup-tasks.md). Ten element jest opcjonalny i rola może mieć tylko jeden blok uruchamiania.

W poniższej tabeli opisano `Startup` atrybut elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|priority|int|Tylko do użytku wewnętrznego.|

##  <a name="task"></a><a name="Task"></a>Zadanie
Element `Task` określa zadanie uruchamiania, które ma miejsce po uruchomieniu roli. Zadania uruchamiania mogą służyć do wykonywania zadań, które przygotowują rolę do uruchamiania takich składników oprogramowania instalowania lub uruchamiania innych aplikacji. Zadania wykonywane w kolejności, w `Startup` jakiej pojawiają się w bloku elementu.

Element `Task` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli `Task` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|Commandline|ciąg|Wymagany. Skrypt, taki jak plik CMD, zawierający polecenia do uruchomienia. Pliki poleceń uruchamiania i wsadowych muszą być zapisane w formacie ANSI. Formaty plików, które ustawiają znacznik kolejności bajtów na początku pliku, nie będą poprawnie przetwarzać.|
|Executioncontext|ciąg|Określa kontekst, w którym skrypt jest uruchamiany.<br /><br /> -   `limited`[Domyślnie] — uruchom z tymi samymi uprawnieniami, co rola hostująca proces.<br />-   `elevated`– Uruchom z uprawnieniami administratora.|
|Tasktype|ciąg|Określa zachowanie wykonywania polecenia.<br /><br /> -   `simple`[Domyślnie] — system czeka na zakończenie zadania przed uruchomieniem innych zadań.<br />-   `background`– System nie czeka na zakończenie zadania.<br />-   `foreground`– Podobnie jak w tle, z wyjątkiem roli nie jest uruchamiany ponownie, dopóki nie zakończy się wszystkie zadania pierwszego planu.|

##  <a name="contents"></a><a name="Contents"></a>Zawartość
Element `Contents` opisuje kolekcję zawartości dla roli procesu roboczego. Ten element jest elementem nadrzędnym `Content` elementu.

Element `Contents` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.5 lub nowszej.

##  <a name="content"></a><a name="Content"></a>Zawartości
Element `Content` definiuje lokalizację źródłową zawartości, która ma zostać skopiowana do maszyny wirtualnej platformy Azure i ścieżkę docelową, do której jest kopiowana.

Element `Content` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.5 lub nowszej.

W poniższej tabeli `Content` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|destination|ciąg|Wymagany. Lokalizacja na maszynie wirtualnej platformy Azure, do której jest umieszczana zawartość. Ta lokalizacja jest względem folderu **%ROLEROOT%\Approot**.|

Ten element jest elementem `SourceDirectory` nadrzędnym elementu.

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a>ŹródłoDirectory
Element `SourceDirectory` definiuje katalog lokalny, z którego kopiowana jest zawartość. Ten element służy do określania zawartości lokalnej do kopiowania na maszynie wirtualnej platformy Azure.

Element `SourceDirectory` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.5 lub nowszej.

W poniższej tabeli `SourceDirectory` opisano atrybuty elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|ścieżka|ciąg|Wymagany. Ścieżka względna lub bezwzględna katalogu lokalnego, którego zawartość zostanie skopiowana na maszynę wirtualną platformy Azure. Obsługiwane jest rozszerzanie zmiennych środowiskowych w ścieżce katalogu.|

## <a name="see-also"></a>Zobacz też
[Schemat definicji usługi w chmurze (klasycznej)](schema-csdef-file.md)



