---
title: Konfigurowanie autonomicznego klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować autonomiczny lub lokalnego klastra usługi Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: dekapur
ms.openlocfilehash: f94a65e469fdb3cee4f02bc5a8f6f5a4a1ea5a16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386724"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Ustawienia konfiguracji dla autonomicznego klastra Windows
W tym artykule opisano ustawienia konfiguracji klastra usługi Azure Service Fabric autonomicznego, który można ustawić w *ClusterConfig.json* pliku. Aby podać informacje o węzłach klastra, konfiguracji zabezpieczeń, a także topologii sieci pod kątem błędów i uaktualnień będzie używać tego pliku.  Po zmianę lub dodanie ustawienia konfiguracji, można kliknąć przycisk [Tworzenie klastra autonomicznego](service-fabric-cluster-creation-for-windows-server.md) lub [uaktualnić konfiguracji klastra autonomicznego](service-fabric-cluster-config-upgrade-windows-server.md).

Po użytkownik [pobierania pakietu autonomicznego usługi Service Fabric](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), przykłady ClusterConfig.json dostępne są również. Przykłady, których nazwy zawierają "DevCluster" Tworzenie klastra z wszystkie trzy węzły na tym samym komputerze, przy użyciu logicznych węzłów. Z tych węzłów co najmniej jedna musi być oznaczona jako węzła podstawowego. Ten typ klastra jest przydatne w przypadku środowiska deweloperskie lub testowe. Nie jest obsługiwany jako w warunkach produkcyjnych klastra. Przykłady, które mają "MultiMachine" w nazwach pomoc, tworzenie klastrów klasy produkcyjnej z każdego węzła na osobnym komputerze. Liczba węzłów podstawowy dla tych klastrów zależy od klastra [poziom niezawodności](#reliability). W wersji 5.7, interfejsu API w wersji 05-2017, firma Microsoft usunęła właściwości poziomu niezawodności. Zamiast tego nasz kod oblicza najbardziej zoptymalizowane poziom niezawodności klastra. Nie należy ustawić wartość tej właściwości w wersji 5.7 lub nowszy.

* ClusterConfig.Unsecure.DevCluster.json i ClusterConfig.Unsecure.MultiMachine.json pokazują, jak utworzyć niezabezpieczony testu lub klastra produkcyjnego, odpowiednio.

* ClusterConfig.Windows.DevCluster.json i ClusterConfig.Windows.MultiMachine.json przedstawiające sposób tworzenia klastrów testowym lub produkcyjnym, które są zabezpieczone przy użyciu [zabezpieczeń Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.json i ClusterConfig.X509.MultiMachine.json przedstawiające sposób tworzenia klastrów testowym lub produkcyjnym, które są zabezpieczone przy użyciu [X509 zabezpieczenia oparte na certyfikatach](service-fabric-windows-cluster-x509-security.md).

Teraz Przeanalizujmy różnych sekcji pliku ClusterConfig.json.

## <a name="general-cluster-configurations"></a>Konfiguracje klastra ogólne
Konfiguracje klastra ogólne obejmują szeroki konfiguracje specyficzne dla klastra, jak pokazano w poniższym fragmencie kodu JSON:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Można nadać dowolnej przyjaznej nazwy do klastra usługi Service Fabric, przypisując go do nazwy zmiennej. ClusterConfigurationVersion jest numerem wersji klastra. Powinna ona być za każdym razem, gdy Uaktualnianie klastra usługi Service Fabric. Pozostaw wartość domyślną ustawiony wersja interfejsu API.

## <a name="nodes-on-the-cluster"></a>Węzły w klastrze
Węzły w klastrze usługi Service Fabric można skonfigurować za pomocą sekcji węzłów, co ilustruje poniższy fragment kodu:
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

Klaster usługi Service Fabric musi zawierać co najmniej trzy węzły. Możesz dodać więcej węzłów do tej sekcji, zgodnie z konfiguracją. W poniższej tabeli opisano ustawienia konfiguracji dla każdego węzła:

| **Konfiguracja węzła** | **Opis** |
| --- | --- |
| nodeName |W węźle można nadać dowolnej przyjaznej nazwy. |
| iPAddress |Dowiedz się, adres IP węzła, otwierając okno polecenia i wpisując `ipconfig`. Zanotuj adres IPV4, a następnie przypisać ją do zmiennej adres IP. |
| elementu nodeTypeRef |Każdy węzeł może być przypisany typ innego węzła. [Typy węzłów](#node-types) są zdefiniowane w poniższej sekcji. |
| faultDomain |Domeny błędów umożliwiają administratorom klastra do definiowania węzłów fizycznych, które może zakończyć się niepowodzeniem w tym samym czasie udostępnionego fizycznego zależności. |
| upgradeDomain |Domen uaktualnienia opisują zestaw węzłów, które są zamykane uaktualnień usługi Service Fabric w tym samym czasie. Możesz wybrać węzły, które można przypisać do domen uaktualnienia, ponieważ nie są ograniczeni wszelkie fizyczne wymagania. |

## <a name="cluster-properties"></a>Właściwości klastra
Sekcja właściwości w ClusterConfig.json jest używany do konfigurowania klastra, jak pokazano:

### <a name="reliability"></a>Niezawodność
Pojęcie poziomu reliabilityLevel definiuje liczbę replik lub wystąpień usługi systemowe Service Fabric, które można uruchamiać na podstawowe węzły klastra. Określa niezawodności tych usług i tym samym klastrze. Wartość jest obliczana przez system w momencie tworzenia i uaktualniania klastra.

### <a name="diagnostics"></a>Diagnostyka
W sekcji diagnosticsStore można skonfigurować parametrów, aby włączyć diagnostyki i rozwiązywania problemów w przypadku awarii węzła lub klastra, jak pokazano w poniższym fragmencie kodu: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

Metadane znajduje się opis diagnostycznej klastra i można ustawić odpowiednio z konfiguracją. Te zmienne pomoc do zbierania dzienników śledzenia zdarzeń systemu Windows i zrzuty awaryjne, a także liczniki wydajności. Aby uzyskać więcej informacji o dziennikach śledzenia funkcji ETW, zobacz [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) i [śledzenie](https://msdn.microsoft.com/library/ms751538.aspx). Wszystkie dzienniki, w tym [zrzuty awaryjne](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) i [liczniki wydajności](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx), może zostać skierowany do folderu connectionString na swojej maszynie. Możesz również użyć AzureStorage do przechowywania diagnostyki. Zobacz poniższy fragment kodu z próbki:

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Bezpieczeństwo
Sekcja zabezpieczeń jest niezbędne do klastra usługi Service Fabric bezpiecznego autonomicznego. Poniższy fragment kodu pokazuje części tej sekcji:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

Metadane znajduje się opis bezpiecznego klastra i można ustawić odpowiednio z konfiguracją. ClusterCredentialType i ServerCredentialType Określ typ zabezpieczeń, który implementuje klaster i węzłów. Mogą być ustawione na *X509* dla zabezpieczeń oparte na certyfikatach lub *Windows* dla zabezpieczeń opartych na usłudze Azure Active Directory. Pozostała część sekcji Zabezpieczenia opiera się na typ zabezpieczeń. Aby uzyskać informacji na temat sposobu wypełniania pozostała część sekcji Zabezpieczenia, zobacz [zabezpieczenia oparte na certyfikatach klastra autonomicznego](service-fabric-windows-cluster-x509-security.md) lub [zabezpieczeń Windows klastra autonomicznego](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Typy węzłów
Elementy NodeType opisuje typ węzły, które ma klastra. Należy określić typ co najmniej jeden węzeł klastra, jak pokazano w poniższym fragmencie kodu: 

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

Nazwa jest przyjazną nazwę dla tego typu określonego węzła. Aby utworzyć węzeł tego typu węzła, należy przypisać do zmiennej elementu nodeTypeRef jego przyjaznej nazwy dla tego węzła jako [wcześniej wspomniano](#nodes-on-the-cluster). Zdefiniuj punkty końcowe połączenia, które są używane dla każdego typu węzła. Możesz wybrać dowolny inny numer portu dla tych punktów końcowych połączenia, tak długo, jak nie powodują konfliktów z innych punktów końcowych w tym klastrze. W przypadku klastra wielowęzłowego są co najmniej jeden węzeł podstawowy (czyli isPrimary jest ustawiona na *true*), w zależności od [poziomu reliabilityLevel](#reliability). Aby dowiedzieć się więcej na temat typów węzłów głównych i niepodstawowych, zobacz [zagadnienia dotyczące planowania pojemności klastra usługi Service Fabric](service-fabric-cluster-capacity.md) informacji na temat elementy NodeType i poziomu reliabilityLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Punkty końcowe umożliwiają skonfigurowanie typy węzłów
* clientConnectionEndpointPort port jest używany przez klienta do łączenia z klastrem podczas korzystania z interfejsów API klienta. 
* clusterConnectionEndpointPort jest to port, gdzie węzły komunikują się ze sobą.
* leaseDriverEndpointPort to port używany przez sterownik dzierżawy klastra, aby dowiedzieć się, jeśli węzły są nadal aktywne. 
* serviceConnectionEndpointPort port jest używany przez aplikacje i usługach wdrożonych w węźle do komunikacji z klientem usługi Service Fabric, w tym określonym węźle.
* httpGatewayEndpointPort port jest używany przez narzędzie Service Fabric Explorer do łączenia z klastrem.
* Zastąp wartości ephemeralPorts [dynamiczne porty używane przez system operacyjny](https://support.microsoft.com/kb/929851). Usługa Service Fabric używa w ramach tych portów jako porty aplikacji, a pozostałe są dostępne dla systemu operacyjnego. Jest on również mapowany ten zakres istniejący zakres obecne w systemie operacyjnym, więc do wszystkich celów, można użyć zakresów w przykładowe pliki JSON. Upewnij się, że różnica między początkowego i porty zakończenia to co najmniej 255. Jeśli różnica ta jest zbyt niska, ponieważ ten zakres jest udostępniony w systemie operacyjnym, może wystąpić konflikt. Aby wyświetlić skonfigurowany dynamicznego zakresu portów, uruchom `netsh int ipv4 show dynamicport tcp`.
* Właściwość applicationPorts są porty, które są używane przez aplikacje usługi Service Fabric. Zakres portów aplikacji powinien być wystarczająco duży, aby uwzględnić wymagania punktu końcowego aplikacji. Ten zakres ma być wyłączny z dynamicznego zakresu portów na komputerze, oznacza to, że zakres wartości ephemeralPorts według stawki ustalonej w konfiguracji. Usługa Service Fabric używa tych portów w każdym przypadku, gdy nowe porty są wymagane i dba o otwarcie zapory dla tych portów. 
* reverseProxyEndpointPort jest punktem końcowym opcjonalne zwrotnego serwera proxy. Aby uzyskać więcej informacji, zobacz [usługi Service Fabric zwrotny serwer proxy](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Ustawienia dziennika
W sekcji element fabricSettings można ustawić katalogi główne dla usługi Service Fabric danych i dzienników. Można dostosować te katalogi tylko podczas tworzenia klastra. Zobacz poniższy fragment kodu przykładowej przedstawione w tej sekcji:

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

Zalecamy użycie dysku bez systemu operacyjnego jako FabricDataRoot i FabricLogRoot. Zapewnia ona większa niezawodność w unikaniu sytuacje, gdy system operacyjny przestanie odpowiadać. W przypadku dostosowania katalogu głównego danych dziennika głównego jest umieszczany jeden poziom poniżej katalogu głównego danych.

### <a name="stateful-reliable-services-settings"></a>Ustawienia usługi stanowych usług Reliable Services
W sekcji KtlLogger można ustawić globalne ustawienia konfiguracji dla usług Reliable Services. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [skonfigurować stanowych usług Reliable Services](service-fabric-reliable-services-configuration.md). Poniższy przykład pokazuje, jak zmienić dziennik transakcji udostępnione, utworzonej w taki sposób, aby utworzyć kopię żadnych elementów reliable collections usług stanowych:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Funkcje dodatku
Aby skonfigurować funkcje dodatku, skonfiguruj apiVersion jako 04-2017 lub nowszego i skonfiguruj addonFeatures, jak pokazano poniżej:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```

### <a name="container-support"></a>Obsługa kontenerów
Aby włączyć obsługę kontenerów dla kontenerów systemu Windows Server i kontenery funkcji Hyper-V dla autonomicznych klastrów, funkcja dodatku usługa DNS musi być włączona.

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu kompletna *ClusterConfig.json* plik skonfigurowany zgodnie z ustawień klastra autonomicznego, można wdrożyć klaster. Postępuj zgodnie z instrukcjami w [Tworzenie autonomicznego klastra usługi Service Fabric](service-fabric-cluster-creation-for-windows-server.md). 

Jeśli masz klaster autonomiczny wdrożone, można również [uaktualnić konfiguracji klastra autonomicznego](service-fabric-cluster-config-upgrade-windows-server.md). 

Dowiedz się, jak [wizualizowanie klastra przy użyciu narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

