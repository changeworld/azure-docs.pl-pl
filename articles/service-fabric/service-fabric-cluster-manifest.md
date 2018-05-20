---
title: Konfigurowanie klastra autonomiczny sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak konfigurować autonomicznych lub lokalnego klastra usługi sieć szkieletowa usług Azure.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: e0fed608ac9dd02a6fe5563eefc30edb63d224b1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Ustawienia konfiguracji dla autonomicznych klastra systemu Windows
W tym artykule opisano sposób konfigurowania klastra usługi sieć szkieletowa usług Azure autonomiczny przy użyciu pliku pliku ClusterConfig.json. Aby podać informacje o węzłach klastra, konfiguracjach zabezpieczeń, a także topologii sieci w domenach awarii i uaktualniania użyjesz tego pliku.

Gdy możesz [Pobierz autonomiczny pakiet sieci szkieletowej usług](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), uwzględniane są również próbek w pliku ClusterConfig.json. Przykłady, które mają w nazwach "DevCluster" Tworzenie klastra z wszystkich trzech węzłów na tym samym komputerze, używając węzłów logiczne. Spoza tych węzłach co najmniej jeden musi być oznaczona jako węzła podstawowego. Ten typ klastra jest przydatne w przypadku środowisk deweloperskich lub testowania. Nie jest obsługiwany jako klastra produkcyjnego. Przykłady, które mają w nazwach "MultiMachine" utworzenia produkcji klastrów klasy z każdego węzła na osobnym komputerze fizycznym. Liczba węzłów głównej dla tych klastrach opiera się na klastra [poziom niezawodności](#reliability). W wersji 5.7, wersja interfejsu API 05 2017, firma Microsoft usunęła właściwości poziomu niezawodności. Zamiast tego naszego kodu oblicza poziomu niezawodności najbardziej zoptymalizowane dla klastra. Nie należy ustawić wartość tej właściwości w wersji 5.7 lub nowszej.


* ClusterConfig.Unsecure.DevCluster.json i ClusterConfig.Unsecure.MultiMachine.json pokazują, jak utworzyć odpowiednio niezabezpieczona testu lub klastra produkcyjnego.

* ClusterConfig.Windows.DevCluster.json i ClusterConfig.Windows.MultiMachine.json pokazują, jak utworzyć klastry testowym lub produkcyjnym, które są zabezpieczone za pomocą [zabezpieczenia systemu Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.json i ClusterConfig.X509.MultiMachine.json pokazują, jak utworzyć klastry testowym lub produkcyjnym, które są zabezpieczone za pomocą [X509 zabezpieczeń oparte na certyfikatach](service-fabric-windows-cluster-x509-security.md).

Teraz Przeanalizujmy różnych sekcji pliku pliku ClusterConfig.json.

## <a name="general-cluster-configurations"></a>Konfiguracje klastrów ogólne
Konfiguracje klastrów ogólne obejmują szeroki konfiguracji specyficznych dla klastra, jak pokazano w poniższy fragment kodu JSON:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Nadaj z żadnych przyjazną nazwę do klastra usługi sieć szkieletowa, przypisując do nazwy zmiennej. ClusterConfigurationVersion jest numer wersji klastra. Powinna ona być za każdym razem, gdy uaktualniania klastra sieci szkieletowej usług. Pozostaw apiVersion zestawu do wartości domyślnej.

## <a name="nodes-on-the-cluster"></a>Węzły w klastrze

    <a id="clusternodes"></a>

Węzły w klastrze usługi sieć szkieletowa można skonfigurować przy użyciu sekcji węzłów poniższy fragment kodu przedstawia:

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

Klaster sieci szkieletowej usług musi zawierać co najmniej trzy węzły. Zgodnie z ustawień, można dodać więcej węzłów do tej sekcji. W poniższej tabeli opisano ustawienia konfiguracji dla każdego węzła:

| **Konfiguracja węzła** | **Opis** |
| --- | --- |
| nodeName |Można nadać dowolną przyjazną nazwę do węzła. |
| Adres IP |Sprawdzić adres IP węzła, otwierając okno polecenia i wpisując `ipconfig`. Zanotuj adres IPV4, a następnie przypisz go do zmiennej adres IP. |
| wartość nodetyperef równą |Każdy węzeł można przypisać typu innego węzła. [Typy węzłów](#node-types) są zdefiniowane w poniższej sekcji. |
| faultDomain |Domen błędów umożliwiają administratorom klastra zdefiniuj węzłów fizycznych, które może zakończyć się niepowodzeniem w tym samym czasie, z powodu zależności fizycznej udostępnionego. |
| upgradeDomain |Domen uaktualnienia opisano zestaw węzłów, które są wyłączone uaktualnienia sieci szkieletowej usług na o tym samym czasie. Węzły, które można przypisać do uaktualnienia domen, można wybrać, ponieważ nie są ograniczeni żadnych fizyczne wymagania. |

## <a name="cluster-properties"></a>Właściwości klastra
Sekcja właściwości w pliku ClusterConfig.json służy do konfigurowania klastra, jak pokazano:

### <a name="reliability"></a>Niezawodność
Pojęcie reliabilityLevel definiuje liczbę replik lub wystąpień usług systemowych sieci szkieletowej usług, które można uruchamiać na głównej węzłów klastra. Określa niezawodności tych usług i tym samym klastrze. Wartość jest obliczana przez system podczas tworzenia i uaktualniania klastra.

    <a id="reliability"></a>

### <a name="diagnostics"></a>Diagnostyka
W sekcji diagnosticsStore można skonfigurować parametry, aby włączyć diagnostykę i rozwiązywanie problemów z awarii węzła lub klastra, jak pokazano w poniższy fragment kodu: 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

Metadane znajduje się opis diagnostycznej klastra i można ustawić zgodnie z konfiguracji. Te zmienne pomóc zbierania dzienników śledzenia zdarzeń systemu Windows i zrzuty awaryjne oraz liczniki wydajności. Aby uzyskać więcej informacji na dzienniki śledzenia zdarzeń systemu Windows, zobacz [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) i [śledzenia zdarzeń systemu Windows](https://msdn.microsoft.com/library/ms751538.aspx). Wszystkie dzienniki, w tym [awarii zrzuty](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) i [liczniki wydajności](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx), może zostać skierowany do folderu connectionString na tym komputerze. Można również użyć AzureStorage do przechowywania diagnostyki. Zobacz poniższy fragment próbki:

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Bezpieczeństwo
Sekcja zabezpieczeń jest niezbędne do bezpiecznego autonomicznej klastra sieci szkieletowej usług. Poniższy fragment kodu przedstawia części tej sekcji:

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

Metadane jest opis bezpiecznego klastra i można ustawić zgodnie z konfiguracji. ClusterCredentialType i ServerCredentialType określają typ zabezpieczeń, który implementuje klastra oraz węzłów. Mogą być ustawione jako *X509* dla zabezpieczenia oparte na certyfikatach lub *Windows* dla zabezpieczeń opartych na usłudze Azure Active Directory. Pozostała część sekcji zabezpieczeń jest oparty na typie zabezpieczeń. Uzyskać informacji na temat sposobu wypełniania pozostałej części sekcji zabezpieczeń, zobacz [zabezpieczenia oparte na certyfikaty w klastrze autonomiczny](service-fabric-windows-cluster-x509-security.md) lub [zabezpieczenia systemu Windows w klastrze autonomiczny](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Typy węzłów

    <a id="nodetypes"></a>

W sekcji elementów NodeType opisuje typ węzłów, które ma w klastrze. Należy określić typ co najmniej jeden węzeł klastra, jak pokazano w poniższy fragment kodu: 

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

Nazwa jest przyjazną nazwę dla tego typu określonego węzła. Aby utworzyć węzeł tego typu węzła, należy przypisać do zmiennej wartość nodetyperef równą jej przyjazną nazwę dla tego węzła, jako [wcześniej wymienionymi](#nodes-on-the-cluster). Dla każdego typu węzła punkty końcowe połączenia, które są używane. Można wybrać dowolny inny numer portu na te punkty końcowe połączenia, pod warunkiem, że nie są one w konflikcie z innych punktów końcowych w tym klastrze. W przypadku klastra wielowęzłowego istnieją co najmniej jeden węzeł podstawowy (oznacza to, że ustawiono isPrimary *true*), w zależności od [reliabilityLevel](#reliability). Aby dowiedzieć się więcej na temat typów węzłów głównych i nonprimary, zobacz [zagadnienia związane z planowaniem pojemności klastra usługi sieć szkieletowa](service-fabric-cluster-capacity.md) informacji na temat elementów NodeType i reliabilityLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Punkty końcowe umożliwiają skonfigurowanie typy węzłów
* clientConnectionEndpointPort jest to port używany przez klienta, aby połączyć się z klastrem w przypadku korzystania z interfejsów API klienta. 
* clusterConnectionEndpointPort jest port, gdzie węzły komunikują się ze sobą.
* leaseDriverEndpointPort jest port używany przez sterownik dzierżawę klastra, aby sprawdzić, czy węzły są nadal aktywne. 
* serviceConnectionEndpointPort jest to port używany przez aplikacje i usługi wdrożone w węźle do komunikacji z klientem usługi sieć szkieletowa na dany węzeł.
* httpGatewayEndpointPort jest port używany przez Service Fabric Explorer, aby połączyć się z klastrem.
* zastąpienie ephemeralPorts [dynamiczne porty używane przez system operacyjny](https://support.microsoft.com/kb/929851). Sieć szkieletowa usług korzysta część tych portów jako porty aplikacji, a pozostałe są dostępne dla systemu operacyjnego. Również mapowania tego zakresu istniejący zakres obecne w systemie operacyjnym, więc dla wszystkich celów, można użyć zakresów podany w przykładowych plików JSON. Upewnij się, że różnica między początek i koniec portów jest co najmniej 255. Jeśli różnica jest zbyt niski, ponieważ ten zakres jest współużytkowany z systemu operacyjnego może wystąpiły konflikty. Aby wyświetlić skonfigurowany dynamicznego zakresu portów, uruchom `netsh int ipv4 show dynamicport tcp`.
* applicationPorts są porty, które są używane przez aplikacje platformy Service Fabric. Zakres portów aplikacji powinien być wystarczająco duży, aby pokryć zapotrzebowanie punkt końcowy aplikacji. Ten zakres powinien być wyłączne z dynamicznego zakresu portów na komputerze, oznacza to, że zakres ephemeralPorts zgodnie z ustawieniami w konfiguracji. Sieć szkieletowa usług te porty są używane w każdym przypadku, gdy nowe porty są wymagane i zajmuje się otwarcie tych portów zapory. 
* reverseProxyEndpointPort jest punktem końcowym opcjonalne zwrotnego serwera proxy. Aby uzyskać więcej informacji, zobacz [sieci szkieletowej usług odwrotny serwer proxy](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Ustawienia dziennika
W sekcji fabricSettings można ustawić katalogów głównych dla dzienników i danych sieci szkieletowej usług. Można dostosować te katalogi tylko podczas tworzenia klastra. Zobacz poniższy fragment próbki w tej sekcji:

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Firma Microsoft zaleca, należy użyć dysku z systemem innym niż systemu operacyjnego jako zmiennej FabricDataRoot i lokalizacji FabricLogRoot. Zapewnia niezawodność więcej w unikaniu sytuacji, gdy system operacyjny przestaje odpowiadać. Jeśli dostosowania pierwiastek danych głównych dziennika znajduje się jeden poziom w dół w katalogu głównym danych.

### <a name="stateful-reliable-services-settings"></a>Stanowe ustawienia niezawodne usługi
W sekcji KtlLogger można zdefiniować ustawienia globalne konfiguracji niezawodne usługi. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [skonfigurować stanowych usług Reliable Services](service-fabric-reliable-services-configuration.md). Poniższy przykład przedstawia sposób zmienić dziennika transakcji udostępnionego, który jest tworzony kopii wszystkie kolekcje niezawodnej dla stanowych usług:

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="add-on-features"></a>Dodatkowe funkcje
Aby skonfigurować dodatkowe funkcje, skonfigurować apiVersion jako 04 2017 lub nowszej i skonfiguruj addonFeatures, jak pokazano poniżej:

    "apiVersion": "04-2017",
    "properties": {
      "addOnFeatures": [
          "DnsService",
          "RepairManager"
      ]
    }

### <a name="container-support"></a>Obsługa kontenerów
Aby włączyć obsługę kontenera kontenery systemu Windows Server i kontenerami autonomicznych klastrów funkcji Hyper-V, funkcja dodatku DnsService musi być włączona.


## <a name="next-steps"></a>Kolejne kroki
Po pełny plik pliku ClusterConfig.json skonfigurowany zgodnie z autonomicznej ustawień klastra, można wdrożyć klaster. Postępuj zgodnie z instrukcjami [tworzenia klastra usługi sieć szkieletowa autonomiczny](service-fabric-cluster-creation-for-windows-server.md). Następnie przejdź do [wizualizowanie klastra za pomocą Eksploratora usługi sieć szkieletowa](service-fabric-visualizing-your-cluster.md) i postępuj zgodnie z instrukcjami.

