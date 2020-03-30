---
title: Konfigurowanie autonomicznego klastra usługi Azure Service Fabric
description: Dowiedz się, jak skonfigurować autonomiczny lub lokalny klaster sieci szkieletowej usługi Azure.
author: dkkapur
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: dekapur
ms.openlocfilehash: 0f9b625dfbe9c39bea7771dcc5fd58805ce19811
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458375"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Ustawienia konfiguracji autonomicznego klastra systemu Windows
W tym artykule opisano ustawienia konfiguracji autonomicznego klastra sieci szkieletowej usług Azure, który można ustawić w pliku *ClusterConfig.json.* Ten plik służy do określania informacji o węzłach klastra, konfiguracjach zabezpieczeń, a także topologii sieci pod względem domen błędów i uaktualnień.  Po zmianie lub dodaniu ustawień konfiguracji można [utworzyć samodzielny klaster](service-fabric-cluster-creation-for-windows-server.md) lub [uaktualnić konfigurację autonomicznego klastra](service-fabric-cluster-config-upgrade-windows-server.md).

Po [pobraniu autonomicznego pakietu sieci szkieletowej usług,](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)ClusterConfig.json przykłady są również uwzględnione. Przykłady, które mają "DevCluster" w ich nazwach utworzyć klaster ze wszystkimi trzema węzłami na tym samym komputerze, przy użyciu węzłów logicznych. Z tych węzłów co najmniej jeden musi być oznaczony jako węzeł podstawowy. Ten typ klastra jest przydatne w środowiskach deweloperskich lub testowych. Nie jest obsługiwany jako klaster produkcyjny. Przykłady, które mają "MultiMachine" w ich nazwach pomóc utworzyć klastrów klasy produkcyjnej, z każdego węzła na osobnym komputerze. Liczba węzłów podstawowych dla tych klastrów jest oparta na [poziomie niezawodności](#reliability)klastra. W wersji 5.7, WERSJA INTERFEJSU API 05-2017, usunęliśmy właściwości na poziomie niezawodności. Zamiast tego nasz kod oblicza najbardziej zoptymalizowany poziom niezawodności dla klastra. Nie próbuj ustawiać wartości dla tej właściwości w wersjach 5.7.

* ClusterConfig.Unsecure.DevCluster.json i ClusterConfig.Unsecure.MultiMachine.json pokazują, jak utworzyć niezabezpieczony test lub klaster produkcyjny.

* ClusterConfig.Windows.DevCluster.json i ClusterConfig.Windows.MultiMachine.json pokazują, jak utworzyć klastry testowe lub produkcyjne, które są zabezpieczone przy użyciu [zabezpieczeń systemu Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.json i ClusterConfig.X509.MultiMachine.json pokazują, jak utworzyć klastry testowe lub produkcyjne, które są zabezpieczone przy użyciu [zabezpieczeń opartych na certyfikatach X509](service-fabric-windows-cluster-x509-security.md).

Teraz przeanalizujmy różne sekcje pliku ClusterConfig.json.

## <a name="general-cluster-configurations"></a>Ogólne konfiguracje klastra
Ogólne konfiguracje klastra obejmują szerokie konfiguracje specyficzne dla klastra, jak pokazano w następującym urywek JSON:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Możesz nadać dowolną przyjazną nazwę klastrowi sieci szkieletowej usług, przypisując ją do zmiennej name. ClusterConfigurationVersion jest numerem wersji klastra. Zwiększ go przy każdym uaktualnieniu klastra sieci szkieletowej usług. Pozostaw apiVersion ustawioną na wartość domyślną.

## <a name="nodes-on-the-cluster"></a>Węzły w klastrze
Węzły klastra sieci szkieletowej usług można skonfigurować przy użyciu sekcji węzłów, zgodnie z następującym fragmentem kodu:
```json
"nodes": [{
    "nodeName": "vm0",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD0"
}, {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType1",
    "faultDomain": "fd:/dc1/r1",
    "upgradeDomain": "UD1"
}, {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType2",
    "faultDomain": "fd:/dc1/r2",
    "upgradeDomain": "UD2"
}],
```

Klaster sieci szkieletowej usług musi zawierać co najmniej trzy węzły. Można dodać więcej węzłów do tej sekcji zgodnie z konfiguracją. W poniższej tabeli opisano ustawienia konfiguracji dla każdego węzła:

| **Konfiguracja węzła** | **Opis** |
| --- | --- |
| nazwa węzła |Możesz nadać dowolnej przyjaznej nazwie węzłowi. |
| Ipaddress |Znajdź adres IP węzła, otwierając okno polecenia `ipconfig`i wpisując polecenie . Zanotuj adres IPV4 i przypisz go do zmiennej iPAddress. |
| węzełTypeRef |Każdemu węzłowi można przypisać inny typ węzła. [Typy węzłów](#node-types) są zdefiniowane w poniższej sekcji. |
| usterkaDomena |Domeny błędów umożliwiają administratorom klastra definiowanie węzłów fizycznych, które mogą zakończyć się niepowodzeniem w tym samym czasie z powodu współużytkowania fizycznego. |
| uaktualnienieDojada |Domeny uaktualniania opisują zestawy węzłów, które są zamykane dla uaktualnień sieci szkieletowej usług w tym samym czasie. Można wybrać węzły, które mają zostać przypisane do domen uaktualnienia, ponieważ nie są one ograniczone żadnymi wymaganiami fizycznymi. |

## <a name="cluster-properties"></a>Właściwości klastra
Sekcja właściwości w clusterconfig.json służy do konfigurowania klastra w sposób pokazany:

### <a name="reliability"></a>Niezawodność
Koncepcja reliabilLevel definiuje liczbę replik lub wystąpień usług systemu sieci szkieletowej usług, które można uruchomić w węzłach podstawowych klastra. Określa niezawodność tych usług, a tym samym klastra. Wartość jest obliczana przez system w czasie tworzenia i uaktualniania klastra.

### <a name="diagnostics"></a>Diagnostyka
W sekcji diagnosticsStore można skonfigurować parametry, aby włączyć diagnostykę i rozwiązywanie problemów z błędami węzłów lub klastra, jak pokazano w poniższym urywek: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

Metadane są opisem diagnostyki klastra i można je ustawić zgodnie z konfiguracją. Te zmienne pomagają w zbieraniu dzienników śledzenia ETW i zrzutów awaryjnych, a także liczników wydajności. Aby uzyskać więcej informacji na temat dzienników śledzenia ETW, zobacz [Śledzenie tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) i [ETW](https://msdn.microsoft.com/library/ms751538.aspx). Wszystkie dzienniki, w tym [zrzuty awaryjne](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) i [liczniki wydajności,](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)można przekierować do folderu connectionString na komputerze. Można również użyć usługi AzureStorage do przechowywania diagnostyki. Zobacz następujący fragment przykładu:

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Zabezpieczenia
Sekcja zabezpieczeń jest niezbędna dla bezpiecznego autonomicznego klastra sieci szkieletowej usług. Następujący fragment kodu przedstawia część tej sekcji:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

Metadane są opisem bezpiecznego klastra i można je ustawić zgodnie z konfiguracją. ClusterCredentialType i ServerCredentialType określają typ zabezpieczeń, które implementuje klaster i węzły. Można je ustawić na *X509* dla zabezpieczeń opartych na certyfikatach lub *windows* dla zabezpieczeń opartych na usłudze Active Directory. Pozostała część sekcji zabezpieczeń jest oparta na typie zabezpieczeń. Aby uzyskać informacje dotyczące wypełniania pozostałej części sekcji zabezpieczeń, zobacz [Zabezpieczenia oparte na certyfikatach w klastrze autonomicznym](service-fabric-windows-cluster-x509-security.md) lub [zabezpieczenia systemu Windows w klastrze autonomicznym](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Typy węzłów
Sekcja nodeTypes opisuje typ węzłów, które ma klaster. Dla klastra należy określić co najmniej jeden typ węzła, jak pokazano w poniższym urywek: 

```json
"nodeTypes": [{
    "name": "NodeType0",
    "clientConnectionEndpointPort": "19000",
    "clusterConnectionEndpointPort": "19001",
    "leaseDriverEndpointPort": "19002"
    "serviceConnectionEndpointPort": "19003",
    "httpGatewayEndpointPort": "19080",
    "reverseProxyEndpointPort": "19081",
    "applicationPorts": {
        "startPort": "20575",
        "endPort": "20605"
    },
    "ephemeralPorts": {
        "startPort": "20606",
        "endPort": "20861"
    },
    "isPrimary": true
}]
```

Nazwa jest przyjazną nazwą dla tego typu węzła. Aby utworzyć węzeł tego typu węzła, przypisz jego przyjazną nazwę do zmiennej nodeTypeRef dla tego [węzła,](#nodes-on-the-cluster)jak wcześniej wspomniano . Dla każdego typu węzła należy zdefiniować punkty końcowe połączenia, które są używane. Można wybrać dowolny numer portu dla tych punktów końcowych połączenia, o ile nie są one w konflikcie z innymi punktami końcowymi w tym klastrze. W klastrze wielozędowym istnieje jeden lub więcej węzłów podstawowych (czyli isPrimary jest ustawiona na *true),* w zależności od [niezawodnościPoziom.](#reliability) Aby dowiedzieć się więcej o typach węzłów podstawowych i nieprimary, zobacz [Zagadnienia planowania pojemności klastra sieci szkieletowej usług, aby uzyskać](service-fabric-cluster-capacity.md) informacje na temat typów węzłów i niezawodnościPoziom. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Punkty końcowe używane do konfigurowania typów węzłów
* clientConnectionEndpointPort jest portem używanym przez klienta do łączenia się z klastrem, gdy używane są interfejsy API klienta. 
* clusterConnectionEndpointPort jest portem, w którym węzły komunikują się ze sobą.
* leaseDriverEndpointPort to port używany przez sterownik dzierżawy klastra, aby dowiedzieć się, czy węzły są nadal aktywne. 
* serviceConnectionEndpointPort to port używany przez aplikacje i usługi wdrożone w węźle do komunikowania się z klientem sieci szkieletowej usług w tym określonym węźle.
* httpGatewayEndpointPort to port używany przez Eksploratora sieci szkieletowej usług do łączenia się z klastrem.
* ulotnePorty zastępują [porty dynamiczne używane przez system operacyjny](https://support.microsoft.com/kb/929851). Usługa Service Fabric używa części tych portów jako portów aplikacji, a pozostałe są dostępne dla systemu operacyjnego. Mapuje również ten zakres do istniejącego zakresu obecnego w os, więc dla wszystkich celów, można użyć zakresów podanych w przykładowych plikach JSON. Upewnij się, że różnica między portami początkowymi i końcowymi wynosi co najmniej 255. Możesz napotkać konflikty, jeśli ta różnica jest zbyt niska, ponieważ ten zakres jest współużytkowane z systemu operacyjnego. Aby wyświetlić skonfigurowany zakres `netsh int ipv4 show dynamicport tcp`portów dynamicznych, uruchom program .
* applicationPorts to porty, które są używane przez aplikacje sieci szkieletowej usług. Zakres portów aplikacji powinien być wystarczająco duży, aby pokryć wymagania punktu końcowego aplikacji. Zakres ten powinien być wyłączny z zakresu portów dynamicznych na komputerze, czyli zakresu portów tymczasowych, zgodnie z konfiguracją. Usługa Service Fabric używa tych portów, gdy wymagane są nowe porty i zajmuje się otwieraniem zapory dla tych portów. 
* reverseProxyEndpointPort jest opcjonalnym punktem końcowym odwrotnego serwera proxy. Aby uzyskać więcej informacji, zobacz [Serwer proxy odwrotnej sieci szkieletowej usług](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Ustawienia dziennika
W sekcji fabricSettings można ustawić katalogi główne dla danych sieci szkieletowej usług i dzienników. Katalogi te można dostosować tylko podczas początkowego tworzenia klastra. Zobacz następujący fragment przykładu tej sekcji:

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

Zaleca się użycie dysku bez systemu operacyjnego jako FabricDataRoot i FabricLogRoot. Zapewnia większą niezawodność w unikaniu sytuacji, gdy system operacyjny przestaje odpowiadać. Jeśli dostosujesz tylko katalog główny danych, katalog główny dziennika zostanie umieszczony o jeden poziom poniżej katalogu głównego danych.

### <a name="stateful-reliable-services-settings"></a>Ustawienia niezawodnych usług stanowych
W sekcji KtlLogger można ustawić globalne ustawienia konfiguracji dla niezawodnych usług. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [Konfigurowanie stanowych niezawodnych usług](service-fabric-reliable-services-configuration.md). W poniższym przykładzie pokazano, jak zmienić dziennik transakcji udostępnionych, który zostanie utworzony w celu utworzenia kopii zapasowej wszelkich niezawodnych kolekcji dla usług stanowych:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Funkcje dodatkowe
Aby skonfigurować funkcje dodatkowe, skonfiguruj apiVersion jako 04-2017 lub nowszą i skonfiguruj funkcje addonFeatures, jak pokazano poniżej:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Wszystkie dostępne funkcje dodatków można zobaczyć w [odwołaniu do interfejsu API REST sieci szkieletowej usług.](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-addonfeatures)

### <a name="container-support"></a>Obsługa kontenerów
Aby włączyć obsługę kontenerów zarówno dla kontenerów systemu Windows Server, jak i kontenerów funkcji Hyper-V dla klastrów autonomicznych, należy włączyć funkcję dodatku DnsService.

## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu kompletnego pliku *ClusterConfig.json* zgodnie z konfiguracją autonomicznego klastra można wdrożyć klaster. Wykonaj kroki opisane w [1.](service-fabric-cluster-creation-for-windows-server.md) 

Jeśli masz wdrożony samodzielny klaster, możesz również [uaktualnić konfigurację autonomicznego klastra](service-fabric-cluster-config-upgrade-windows-server.md). 

Dowiedz się, jak [wizualizować klaster za pomocą Eksploratora sieci szkieletowej usług.](service-fabric-visualizing-your-cluster.md)

