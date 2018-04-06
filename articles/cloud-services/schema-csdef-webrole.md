---
title: Domyślna usługi w chmurze Azure Sieć Web schematu | Dokumentacja firmy Microsoft
ms.custom: ''
ms.date: 04/14/2015
ms.prod: azure
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 85368e4e-a0db-4c02-8dbc-8e2928fa6091
caps.latest.revision: 60
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: b2873f61dcab9a14089949f27f40ca5bedaf14ee
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Azure Cloud Services definicji schematu sieć Web
Rola sieci web platformy Azure jest rolę, która jest dostosowany do programowania aplikacji sieci web, ponieważ obsługiwane przez usługi IIS 7, takich jak ASP.NET, PHP, Windows Communication Foundation i FastCGI.

Domyślne rozszerzenie pliku definicji usługi jest csdef.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Podstawowa usługa definicji schematu dla roli sieci web  
Podstawowy format pliku definicji usługi zawierający rolę sieci web ma następującą składnię.

```xml
<ServiceDefinition …>  
  <WebRole name="<web-role-name>" vmsize="<web-role-size>" enableNativeCodeExecution="[true|false]">  
    <Certificates>  
      <Certificate name="<certificate-name>" storeLocation="<certificate-store>" storeName="<store-name>" />  
    </Certificates>      
    <ConfigurationSettings>  
      <Setting name="<setting-name>" />  
    </ConfigurationSettings>  
    <Imports>  
      <Import moduleName="<import-module>"/>  
    </Imports>  
    <Endpoints>  
      <InputEndpoint certificate="<certificate-name>" ignoreRoleInstanceStatus="[true|false]" name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<port-number>" port="<port-number>" loadBalancerProbe="<load-balancer-probe-name>" />  
      <InternalEndpoint name="<internal-endpoint-name>" protocol="[http|tcp|udp|any]" port="<port-number>">  
         <FixedPort port="<port-number>"/>  
         <FixedPortRange min="<minium-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minium-port-number>" max="<maximum-port-number>"/>  
         </AllocatePublicPortFrom>  
      </InstanceInputEndpoint>  
    </Endpoints>  
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
      </EntryPoint>  
    </Runtime>  
    <Sites>  
      <Site name="<web-site-name>">  
        <VirtualApplication name="<application-name>" physicalDirectory="<directory-path>"/>  
        <VirtualDirectory name="<directory-path>" physicalDirectory="<directory-path>"/>  
        <Bindings>  
          <Binding name="<binding-name>" endpointName="<endpoint-name-bound-to>" hostHeader="<url-of-the-site>"/>  
        </Bindings>  
      </Site>  
    </Sites>  
    <Startup priority="<for-internal-use-only>">  
      <Task commandLine="<command-to=execute>" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">  
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
  </WebRole>  
</ServiceDefinition>  
```  

## <a name="schema-elements"></a>Elementy schematu  
Pliku definicji usługi obejmują następujące elementy, opisano szczegółowo w kolejnych sekcjach tego tematu:  

[WebRole](#WebRole)

[AppSettings](#ConfigurationSettings)

[Ustawienie](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Punkty końcowe](#Endpoints)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certyfikaty](#Certificates)

[certyfikat](#Certificate)

[Importy](#Imports)

[Importowanie](#Import)

[Środowisko uruchomieniowe](#Runtime)

[Środowisko](#Environment)

[Zmienna](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Lokacje](#Sites)

[Lokacji](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Powiązania](#Bindings)

[Powiązanie](#Binding)

[Uruchamianie](#Startup)

[Zadanie podrzędne](#Task)

[Zawartość](#Contents)

[Zawartość](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WebRole"></a> Sieć Web  
`WebRole` Element opisano rolę, która jest dostosowany do programowania aplikacji sieci web, obsługiwane przez usługi IIS 7 i platformy ASP.NET. Usługa może zawierać zero lub więcej ról sieci web.

W poniższej tabeli opisano atrybuty `WebRole` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|name|ciąg|Wymagany. Nazwa roli sieci web. Nazwa roli musi być unikatowa.|  
|enableNativeCodeExecution|wartość logiczna|Opcjonalny. Wartość domyślna to `true`; macierzysty wykonanie kodu i pełnego zaufania są domyślnie włączone. Ten atrybut na `false` wyłączyć wykonanie kodu natywnego dla roli sieci web, a zamiast tego użyj Azure częściowej relacji zaufania.|  
|vmsize|ciąg|Opcjonalny. Ustaw tę wartość, aby zmienić rozmiar maszyny wirtualnej, który jest przydzielony do roli. Wartość domyślna to `Small`. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych dla usług w chmurze](cloud-services-sizes-specs.md).|  

##  <a name="ConfigurationSettings"></a> AppSettings  
`ConfigurationSettings` Element opisuje kolekcję ustawień konfiguracji dla roli sieci web. Ten element jest elementem nadrzędnym `Setting` elementu.

##  <a name="Setting"></a> Ustawienie  
`Setting` Element opisuje parę nazwy i wartości, która określa ustawienia konfiguracji dla wystąpienia roli.

W poniższej tabeli opisano atrybuty `Setting` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|name|ciąg|Wymagany. Unikatową nazwę ustawienia konfiguracji.|  

Ustawienia konfiguracji dla roli są pary nazw i wartości, które są zadeklarowane w pliku definicji usługi i w pliku konfiguracji usługi.

##  <a name="LocalResources"></a> LocalResources  
`LocalResources` Element opisuje kolekcję zasoby magazynu lokalnego dla roli sieci web. Ten element jest elementem nadrzędnym `LocalStorage` elementu.

##  <a name="LocalStorage"></a> LocalStorage  
`LocalStorage` Element identyfikuje zasobu magazynu lokalnego, który udostępnia miejsce w systemie plików usługi w czasie wykonywania. Rola może zdefiniować zero lub więcej zasobów magazynu lokalnego.

> [!NOTE]
>  `LocalStorage` Element może pojawić się jako element podrzędny `WebRole` element do obsługi zgodność ze starszymi wersjami zestawu SDK platformy Azure.

W poniższej tabeli opisano atrybuty `LocalStorage` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|name|ciąg|Wymagany. Unikatowa nazwa magazynu lokalnego.|  
|cleanOnRoleRecycle|wartość logiczna|Opcjonalny. Wskazuje, czy lokalnego magazynu powinny być czyszczone po ponownym uruchomieniu roli. Wartość domyślna to `true`.|  
|sizeInMb|int|Opcjonalny. Wymaganej ilości miejsca do magazynowania można przydzielić do lokalnego magazynu w Megabajtach. Jeśli nie określono domyślnego miejsca do magazynowania przydzielone wynosi 100 MB. Minimalna ilość miejsca do magazynowania, która może zostać przydzielona wynosi 1 MB.<br /><br /> Maksymalny rozmiar zasobów lokalnych jest zależna od rozmiaru maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych dla usług w chmurze](cloud-services-sizes-specs.md).|  
  
Nazwa katalogu przydzielony do zasobu lokalnego magazynu odpowiada wartość podana dla atrybutu name.

##  <a name="Endpoints"></a> Punkty końcowe  
`Endpoints` Element opisuje kolekcję danych wejściowych (zewnętrzne), wewnętrzny i wystąpienia wejściowych punktów końcowych dla roli. Ten element jest elementem nadrzędnym `InputEndpoint`, `InternalEndpoint`, i `InstanceInputEndpoint` elementy.

Dane wejściowe i wewnętrznych punktów końcowych są przydzielane oddzielnie. Usługa może mieć łącznie 25 danych wejściowych, wewnętrzne, i wystąpienia wejściowych punktów końcowych, które mogą być przydzielone 25 ról, dozwolone w usłudze. Na przykład jeśli mają 5 role można przydzielić 5 wejściowych punktów końcowych dla każdej roli, możesz przydzielić 25 wejściowych punktów końcowych dla jednej roli lub można przydzielić 1 wejściowy punkt końcowy każdego do 25 ról.

> [!NOTE]
>  Każda rola wdrożone wymaga jednego wystąpienia dla każdej roli. Domyślne Inicjowanie obsługi administracyjnej dla subskrypcji jest ograniczona do 20 rdzeni i w związku z tym jest ograniczona do 20 wystąpień roli. Jeśli aplikacja wymaga więcej wystąpień, niż jest dostępny domyślnie Obsługa administracyjna zobacz [, Zarządzanie subskrypcją przydziału Pomoc dotycząca rozliczeń i](https://azure.microsoft.com/support/options/) uzyskać więcej informacji o zwiększenie limitu przydziału.

##  <a name="InputEndpoint"></a> InputEndpoint  
`InputEndpoint` Element opisuje zewnętrznego punktu końcowego roli sieci web.

Można zdefiniować wiele punktów końcowych, które są kombinacją protokołu HTTP, HTTPS, UDP i TCP punktów końcowych. Można określić dowolny inny numer portu wybranego wejściowego punktu końcowego, ale numery portów określonej dla każdej roli w ramach usługi musi być unikatowa. Na przykład jeśli określono, że rola sieci web korzysta z portu 80 dla protokołu HTTP i portu 443 dla protokołu HTTPS, może następnie możesz określić drugą rolę sieci web używa portu 8080 dla protokołu HTTP i portu 8043 dla protokołu HTTPS.

W poniższej tabeli opisano atrybuty `InputEndpoint` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|name|ciąg|Wymagany. Unikatowa nazwa zewnętrznego punktu końcowego.|  
|protokół|ciąg|Wymagany. Protokół transportu dla zewnętrznego punktu końcowego. Dla roli sieci web, możliwe wartości to `HTTP`, `HTTPS`, `UDP`, lub `TCP`.|  
|port|int|Wymagany. Port zewnętrznego punktu końcowego. Można określić dowolny inny numer portu wybranego, ale numery portów określonej dla każdej roli w ramach usługi musi być unikatowa.<br /><br /> Możliwe wartości należą do zakresu od 1 do 65535 włącznie (zestaw Azure SDK w wersji 1,7 lub nowszej).|  
|certyfikat|ciąg|Wymagany dla punktu końcowego protokołu HTTPS. Nazwa certyfikatu, zdefiniowane przez `Certificate` elementu.|  
|localPort|int|Opcjonalny. Określa port używany dla wewnętrznej połączeń w punkcie końcowym. `localPort` Porcie zewnętrznym punkcie końcowym mapowań atrybutów do wewnętrznych portów w roli. Jest to przydatne w scenariuszach, w której roli musi komunikować się do wewnętrznej składnika z portem czy innego niż ten, który jest uwidaczniany zewnętrznie.<br /><br /> Jeśli nie zostanie określony, wartość `localPort` jest taka sama jak `port` atrybutu. Ustaw wartość `localPort` na "*" do automatycznego przypisywania nieprzydzielonego portu wykrywalny, przy użyciu interfejsu API środowiska uruchomieniowego.<br /><br /> Możliwe wartości należą do zakresu od 1 do 65535 włącznie (zestaw Azure SDK w wersji 1,7 lub nowszej).<br /><br /> `localPort` Atrybut jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.|  
|ignoreRoleInstanceStatus|wartość logiczna|Opcjonalny. Jeśli wartość tego atrybutu jest równa `true`, stan usługi zostanie zignorowana i punkt końcowy nie zostaną usunięte przez moduł równoważenia obciążenia. Ustawienie tej wartości na `true` przydatne w przypadku debugowania zajęty wystąpień usługi. Wartość domyślna to `false`. **Uwaga:** punkt końcowy nadal mogą odbierać ruch nawet wtedy, gdy rola nie jest w stanie gotowe.|  
|loadBalancerProbe|ciąg|Opcjonalny. Nazwa sondę modułu równoważenia obciążenia, które są skojarzone z wejściowym punkcie końcowym. Aby uzyskać więcej informacji, zobacz [schematu LoadBalancerProbe](schema-csdef-loadbalancerprobe.md).|  

##  <a name="InternalEndpoint"></a> InternalEndpoint  
`InternalEndpoint` Element opisuje wewnętrzny punkt końcowy do roli sieci web. Wewnętrzny punkt końcowy jest dostępny tylko dla innych wystąpień roli uruchomiony w ramach usługi; nie jest dostępna dla klientów poza usługi. Sieci Web ról, które nie zawierają `Sites` element może mieć tylko jeden HTTP, UDP lub TCP wewnętrzny punkt końcowy.

W poniższej tabeli opisano atrybuty `InternalEndpoint` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|name|ciąg|Wymagany. Unikatowa nazwa wewnętrzny punkt końcowy.|  
|protokół|ciąg|Wymagany. Protokół transport wewnętrzny punkt końcowy. Możliwe wartości to `HTTP`, `TCP`, `UDP`, lub `ANY`.<br /><br /> Wartość `ANY` Określa, że wszystkie protokołu dowolnego portu.|  
|port|int|Opcjonalny. Port używany dla połączenia wewnętrznego ze zrównoważonym obciążeniem w punkcie końcowym. Dwa porty używa punktu końcowego o zrównoważonym obciążeniu. Port używany do publicznego adresu IP i port używany na prywatny adres IP. Zazwyczaj są to te są ustawione takie same, ale mogą być używane inne porty.<br /><br /> Możliwe wartości należą do zakresu od 1 do 65535 włącznie (zestaw Azure SDK w wersji 1,7 lub nowszej).<br /><br /> `Port` Atrybut jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.|  

##  <a name="InstanceInputEndpoint"></a> InstanceInputEndpoint  
`InstanceInputEndpoint` Element opisuje wystąpienie wejściowy punkt końcowy do roli sieci web. Wejściowy punkt końcowy wystąpienia jest skojarzone z wystąpieniem konkretnej roli za pomocą przekierowania portów w usłudze równoważenia obciążenia. Wejściowy punkt końcowy każdego wystąpienia jest zamapowany na określonym porcie z zakres portów to możliwe. Ten element jest elementem nadrzędnym `AllocatePublicPortFrom` elementu.

`InstanceInputEndpoint` Element jest dostępne wyłącznie przy użyciu wersji zestawu Azure SDK 1.7 lub nowszej.

W poniższej tabeli opisano atrybuty `InstanceInputEndpoint` elementu.
  
| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|name|ciąg|Wymagany. Unikatowa nazwa punktu końcowego.|  
|localPort|int|Wymagany. Określa port wewnętrzny, który będzie nasłuchiwać wszystkich wystąpień roli w celu odbierania ruchu przychodzącego przekazywane z modułu równoważenia obciążenia. Możliwe wartości należą do zakresu od 1 do 65535 włącznie.|  
|protokół|ciąg|Wymagany. Protokół transport wewnętrzny punkt końcowy. Możliwe wartości to `udp` lub `tcp`. Użyj `tcp` do ruchu http/https.|  
  
##  <a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom  
`AllocatePublicPortFrom` Element opisuje zakres portów: publicznego używanego przez klientów zewnętrznych w wejściowym punkcie końcowym każdego wystąpienia dostępu do. Numer portu publicznego (VIP) jest przydzielony z tego zakresu i przypisane do każdego punktu końcowego wystąpienia roli poszczególnych podczas wdrażania dzierżawcy i aktualizacji. Ten element jest elementem nadrzędnym `FixedPortRange` elementu.

`AllocatePublicPortFrom` Element jest dostępne wyłącznie przy użyciu wersji zestawu Azure SDK 1.7 lub nowszej.

##  <a name="FixedPort"></a> FixedPort  
`FixedPort` Element Określa port wewnętrzny punkt końcowy, które umożliwia ładowanie zrównoważonym połączeń w punkcie końcowym.

`FixedPort` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli opisano atrybuty `FixedPort` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|port|int|Wymagany. Numer portu wewnętrzny punkt końcowy. Jest to ten sam efekt co ustawienie `FixedPortRange` min i max z tym samym portem.<br /><br /> Możliwe wartości należą do zakresu od 1 do 65535 włącznie (zestaw Azure SDK w wersji 1,7 lub nowszej).|  

##  <a name="FixedPortRange"></a> FixedPortRange  
`FixedPortRange` Element określa zakres portów, które są przypisane do wewnętrzny punkt końcowy lub wejściowy punkt końcowy wystąpienia i port używany dla obciążenia zestawów o zrównoważonym połączeń w punkcie końcowym.

> [!NOTE]
>  `FixedPortRange` Element zmieniło się w zależności od tego elementu, w której znajduje się. Gdy `FixedPortRange` element `InternalEndpoint` elementu otwiera wszystkie porty w module równoważenia obciążenia w zakresie min i max atrybuty dla wszystkich maszyn wirtualnych, na których działa rola. Gdy `FixedPortRange` element `InstanceInputEndpoint` elementu, zostanie otwarty tylko jeden port w zakresie min i max atrybutów na każdej maszynie wirtualnej, na którym jest uruchomiona rola.

`FixedPortRange` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli opisano atrybuty `FixedPortRange` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|min.|int|Wymagany. Minimalna port w zakresie. Możliwe wartości należą do zakresu od 1 do 65535 włącznie (zestaw Azure SDK w wersji 1,7 lub nowszej).|  
|maks.|ciąg|Wymagany. Maksymalna port w zakresie. Możliwe wartości należą do zakresu od 1 do 65535 włącznie (zestaw Azure SDK w wersji 1,7 lub nowszej).|  

##  <a name="Certificates"></a> Certyfikaty  
`Certificates` Element opisuje kolekcję certyfikatów dla roli sieci web. Ten element jest elementem nadrzędnym `Certificate` elementu. Rola może mieć dowolną liczbę certyfikatów skojarzone. Aby uzyskać więcej informacji na temat używania certyfikatów elementu, zobacz [zmodyfikować plik definicji usługi za pomocą certyfikatu](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a> certyfikat  
`Certificate` Element opisano certyfikaty, które są skojarzone z rolą sieci web.

W poniższej tabeli opisano atrybuty `Certificate` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|name|ciąg|Wymagany. Nazwa tego certyfikatu, który służy do odwoływania się do niego, gdy jest on skojarzony z protokołu HTTPS `InputEndpoint` elementu.|  
|storeLocation|ciąg|Wymagany. Lokalizacja magazynu certyfikatów, gdzie można znaleźć tego certyfikatu na komputerze lokalnym. Możliwe wartości to `CurrentUser` i `LocalMachine`.|  
|storeName|ciąg|Wymagany. Nazwa magazynu certyfikatów, w którym ten certyfikat znajduje się na komputerze lokalnym. Możliwe wartości zawierają nazwy magazynu wbudowanych `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`, lub dowolną nazwę magazynu niestandardowego. Jeśli zostanie określona nazwa magazynu niestandardowego, jest tworzony automatycznie magazynu.|  
|permissionLevel|ciąg|Opcjonalny. Określa uprawnienia dostępu do procesów roli. Jeśli chcesz tylko z podwyższonym poziomem uprawnień procesy, aby można było uzyskać dostępu do klucza prywatnego, a następnie określ `elevated` uprawnienia. `limitedOrElevated` uprawnienie umożliwia wszystkie procesy roli do dostępu do klucza prywatnego. Możliwe wartości to `limitedOrElevated` lub `elevated`. Wartość domyślna to `limitedOrElevated`.|  

##  <a name="Imports"></a> Importy  
`Imports` Element opisuje kolekcję modułów importu dla roli sieci web, które dodać składniki do systemu operacyjnego gościa. Ten element jest elementem nadrzędnym `Import` elementu. Ten element jest opcjonalny i rola może mieć tylko jeden blok importowania. 

`Imports` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

##  <a name="Import"></a> Import  
`Import` Element Określa moduł, który chcesz dodać do systemu operacyjnego gościa.

`Import` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli opisano atrybuty `Import` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|moduleName|ciąg|Wymagany. Nazwa modułu do zaimportowania. Nieprawidłowy import moduły są:<br /><br /> -RemoteAccess<br />-RemoteForwarder<br />-Diagnostyki<br /><br /> Moduły RemoteAccess i RemoteForwarder pozwalają skonfigurować wystąpienie roli dla połączeń pulpitu zdalnego. Aby uzyskać więcej informacji, zobacz [Podłączanie pulpitu zdalnego włączyć](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Moduł Diagnostyka służy do zbierania danych diagnostycznych dla wystąpienia roli.|  

##  <a name="Runtime"></a> Środowisko uruchomieniowe  
`Runtime` Element opisuje kolekcję ustawień zmiennych środowiskowych dla roli sieci web kontrolujących środowiska wykonawczego procesu hosta platformy Azure. Ten element jest elementem nadrzędnym `Environment` elementu. Ten element jest opcjonalny i rola może mieć tylko jeden blok środowiska wykonawczego.

`Runtime` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli opisano atrybuty `Runtime` elementu:  

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|executionContext|ciąg|Opcjonalny. Określa kontekst, w którym jest uruchomiony proces roli. Domyślny kontekst jest `limited`.<br /><br /> -   `limited` — Proces jest uruchomiony bez uprawnień administratora.<br />-   `elevated` — Proces jest uruchamiana z uprawnieniami administratora.|  

##  <a name="Environment"></a> Środowisko  
`Environment` Element opisuje kolekcję ustawień zmiennych środowiskowych dla roli sieci web. Ten element jest elementem nadrzędnym `Variable` elementu. Rola może mieć dowolną liczbę zestaw zmiennych środowiska.

##  <a name="Variable"></a> Zmienna  
`Variable` Element Określa zmienną środowiskową można ustawić w operacyjnego gościa.

`Variable` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli opisano atrybuty `Variable` elementu:  

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|name|ciąg|Wymagany. Nazwa zmiennej środowiskowej, aby ustawić.|  
|wartość|ciąg|Opcjonalny. Wartość do ustawienia zmiennej środowiskowej. Musi zawierać atrybut value lub `RoleInstanceValue` elementu.|  

##  <a name="RoleInstanceValue"></a> RoleInstanceValue  
`RoleInstanceValue` Element określa wyrażenie xPath, z którego można pobrać wartości zmiennej.

W poniższej tabeli opisano atrybuty `RoleInstanceValue` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|wyrażenie XPath|ciąg|Opcjonalny. Ścieżka do lokalizacji ustawień wdrożenia dla wystąpienia. Aby uzyskać więcej informacji, zobacz [zmienne konfiguracji z XPath](cloud-services-role-config-xpath.md).<br /><br /> Musi zawierać atrybut value lub `RoleInstanceValue` elementu.|  

##  <a name="EntryPoint"></a> EntryPoint  
`EntryPoint` Element określa punkt wejścia dla roli. Ten element jest elementem nadrzędnym `NetFxEntryPoint` elementów. Elementy te umożliwiają określenie aplikacji innej niż default WaWorkerHost.exe pełnienie roli punktu wejścia.

`EntryPoint` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.5 lub nowszej.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint  
`NetFxEntryPoint` Element Określa program do uruchomienia dla roli.

> [!NOTE]
>  `NetFxEntryPoint` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.5 lub nowszej.

W poniższej tabeli opisano atrybuty `NetFxEntryPoint` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|AssemblyName|ciąg|Wymagany. Nazwa i ścieżka pliku zestawu zawierającego punkt wejścia. Ścieżka jest względne wobec folderu  **\\%ROLEROOT%\Approot** (nie należy określać  **\\%ROLEROOT%\Approot** w `commandLine`, zakłada się). **% ROLEROOT %** zmienną środowiskową obsługiwany przez platformę Azure, reprezentuje lokalizację folderu głównego dla roli użytkownika.  **\\%ROLEROOT%\Approot** folder reprezentuje folder aplikacji dla roli użytkownika.<br /><br /> Dla ról użytkownika ścieżka jest zawsze względem  **\\%ROLEROOT%\Approot\bin** folderu.<br /><br /> Pełna usług IIS i usługi IIS Express web ról, jeśli nie można odnaleźć zestawu względem  **\\%ROLEROOT%\Approot** folderu,  **\\%ROLEROOT%\Approot\bin** jest przeszukiwany.<br /><br /> Ta bazowy zachowanie dla usługi IIS nie jest to najlepsze rozwiązanie zaleca się i może być usunięte w przyszłych wersjach.|  
|targetFrameworkVersion|ciąg|Wymagany. Wersja programu .NET framework, na którym został utworzony zestaw. Na przykład `targetFrameworkVersion="v4.0"`.|  

##  <a name="Sites"></a> Lokacje  
`Sites` Element opisuje kolekcję witryn sieci Web i aplikacji sieci web hostowanych w roli sieci web. Ten element jest elementem nadrzędnym `Site` elementu. Jeśli nie określisz `Sites` elementu roli sieci web jest obsługiwana jako rola starszych sieci web i może mieć tylko jedną witrynę sieci Web hostowanych w roli sieci web. Ten element jest opcjonalny i rola może mieć tylko jeden blok witryny.

`Sites` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

##  <a name="Site"></a> Lokacji  
`Site` Element określa witryny sieci Web lub aplikacji sieci web, która jest częścią roli sieci web.

`Site` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli opisano atrybuty `Site` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|name|ciąg|Wymagany. Nazwa witryny sieci Web lub aplikacji.|  
|physicalDirectory|ciąg|Lokalizacja zawartości katalogu dla katalogu głównego witryny. Można określić lokalizację jako ścieżkę bezwzględną ani względną wobec lokalizacji csdef.|  

##  <a name="VirtualApplication"></a> VirtualApplication  
`VirtualApplication` Element definiuje aplikacji w Internet Information Services (IIS) 7 jest to grupa plików, która dostarcza zawartość lub udostępnia usługi za pośrednictwem protokołów, takich jak HTTP. Po utworzeniu aplikacji w usługach IIS 7 ścieżka aplikacji staje się częścią adresu URL witryny.

`VirtualApplication` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli opisano atrybuty `VirtualApplication` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|name|ciąg|Wymagany. Określa nazwę identyfikującą aplikacji wirtualnej.|  
|physicalDirectory|ciąg|Wymagany. Określa ścieżkę na komputerze deweloperskim, który zawiera aplikację wirtualną. W emulatorze obliczeń usług IIS jest skonfigurowany do pobierania zawartości z tej lokalizacji. Podczas wdrażania platformy Azure, zawartość katalogu fizycznego pakiecie oraz pozostała część usługi. Po wdrożeniu pakietu usługi Azure usług IIS jest skonfigurowany z lokalizacji rozpakowanej zawartości.|  

##  <a name="VirtualDirectory"></a> VirtualDirectory  
`VirtualDirectory` Element Określa nazwę katalogu (zwaną także ścieżki) określ, w usługach IIS, a następnie mapować katalog fizyczny na serwerze lokalnym lub zdalnym.

`VirtualDirectory` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli opisano atrybuty `VirtualDirectory` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|name|ciąg|Wymagany. Określa nazwę katalogu wirtualnego.|  
|wartość|physicalDirectory|Wymagany. Określa ścieżkę na komputerze deweloperskim, zawierający witryny sieci Web lub wirtualne zawartości katalogu. W emulatorze obliczeń usług IIS jest skonfigurowany do pobierania zawartości z tej lokalizacji. Podczas wdrażania platformy Azure, zawartość katalogu fizycznego pakiecie oraz pozostała część usługi. Po wdrożeniu pakietu usługi Azure usług IIS jest skonfigurowany z lokalizacji rozpakowanej zawartości.|  

##  <a name="Bindings"></a> Powiązania  
`Bindings` Element opisuje kolekcję powiązań dla witryny sieci Web. Jest elementem nadrzędnym `Binding` elementu. Element jest wymagany dla każdego `Site` elementu. Aby uzyskać więcej informacji na temat konfigurowania punktów końcowych, zobacz [włączyć komunikacji dla wystąpień roli](cloud-services-enable-communication-role-instances.md).

`Bindings` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

##  <a name="Binding"></a> Powiązanie  
`Binding` Element określa informacje konfiguracyjne wymagane dla żądań kierowanych do komunikowania się z witryny sieci Web lub aplikacji sieci web.

`Binding` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|name|ciąg|Wymagany. Określa nazwę identyfikującą powiązania.|  
|endpointName|ciąg|Wymagany. Określa nazwę punktu końcowego do powiązania.|  
|nagłówka hosta|ciąg|Opcjonalny. Określa nazwę hosta, który umożliwia hostowanie wielu witryn, z nazwami inny host, na jednym kombinacji numer portu adresu IP.|  

##  <a name="Startup"></a> Uruchamianie  
`Startup` Element opisuje kolekcję zadań, które są uruchamiane podczas uruchamiania roli. Ten element może być elementem nadrzędnym `Variable` elementu. Aby uzyskać więcej informacji na temat za pomocą zadania uruchamiania roli, zobacz [jak skonfigurować uruchamianie zadania](cloud-services-startup-tasks.md). Ten element jest opcjonalny i rola może mieć tylko jeden blok uruchamiania.

W poniższej tabeli opisano atrybut `Startup` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|priorytet|int|Tylko do użytku wewnętrznego.|  

##  <a name="Task"></a> Zadanie  
`Task` Element Określa zadanie uruchamiania, która ma miejsce podczas uruchamiania roli. Uruchamianie zadania może służyć do wykonywania zadań, które przygotowanie roli, aby uruchomić instalację tych składników oprogramowania lub uruchamiania innych aplikacji. Zadania wykonywane w kolejności, w jakiej występują w ramach `Startup` bloku elementu.

`Task` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

W poniższej tabeli opisano atrybuty `Task` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|Wiersz polecenia|ciąg|Wymagany. Skrypt, takich jak plik CMD, zawierający polecenie do uruchomienia. Pliki uruchomienia polecenia i partii musi zostać zapisana w formacie ANSI. Formaty plików, które ustawić znacznik kolejności bajtów na początku pliku nie przetworzy prawidłowo.|  
|executionContext|ciąg|Określa kontekst, w którym skrypt jest uruchamiany.<br /><br /> -   `limited` [Domyślne] — Uruchom takie same uprawnienia w roli procesu hostingu.<br />-   `elevated` — Uruchomione z uprawnieniami administratora.|  
|taskType|ciąg|Określa sposób wykonywania polecenia.<br /><br /> -   `simple` [Domyślne] — System oczekuje na zadanie zakończyć działanie przed inne zadania będą uruchamiane.<br />-   `background` — System czeka zadań zakończyć.<br />-   `foreground` — Podobnie jak w tle, z wyjątkiem roli nie zostanie ponownie uruchomiony do momentu zamknąć wszystkie zadania pierwszego planu.|  

##  <a name="Contents"></a> Zawartość  
`Contents` Element opisuje zbiór zawartości dla roli sieci web. Ten element jest elementem nadrzędnym `Content` elementu.

`Contents` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.5 lub nowszej.

##  <a name="Content"></a> Zawartość  
`Content` Element definiuje lokalizacji źródła zawartości, które mają być kopiowane do maszyny wirtualnej platformy Azure i ścieżkę docelową, do której są kopiowane.

`Content` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.5 lub nowszej.

W poniższej tabeli opisano atrybuty `Content` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|Miejsce docelowe|ciąg|Wymagany. Lokalizacja na maszynie wirtualnej Azure, do której jest umieszczona zawartość. Ta lokalizacja jest względne wobec folderu **%ROLEROOT%\Approot**.|  

Ten element jest elementem nadrzędnym `SourceDirectory` elementu.

##  <a name="SourceDirectory"></a> SourceDirectory  
`SourceDirectory` Element definiuje katalogu lokalnego, z którego zawartość zostanie skopiowana. Ten element umożliwia określenie zawartości lokalnej do skopiowania do maszyny wirtualnej platformy Azure.

`SourceDirectory` Element jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.5 lub nowszej.

W poniższej tabeli opisano atrybuty `SourceDirectory` elementu.

| Atrybut | Typ | Opis |  
| --------- | ---- | ----------- |  
|ścieżka|ciąg|Wymagany. Ścieżka względna lub bezwzględna lokalnego katalogu, którego zawartość zostanie skopiowana do maszyny wirtualnej platformy Azure. Rozszerzanie zmiennych środowiskowych w ścieżce katalogu jest obsługiwana.|  
  
## <a name="see-also"></a>Zobacz też
[Schematu definicji (klasyczny) usługi w chmurze](schema-csdef-file.md)
