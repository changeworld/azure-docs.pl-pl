---
title: Usługa Azure historię wersji schematu konfiguracji diagnostyki rozszerzenia
description: Właściwe dla zbierania liczników wydajności w usłudze Azure Virtual Machines, usługi VM Scale Sets, usługi Service Fabric i usługi w chmurze.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/20/2018
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: 1230a9bcea01ef394a6299c50b8d5537850cfee5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60527309"
---
# <a name="azure-diagnostics-extension-configuration-schema-versions-and-history"></a>Usługa Azure wersji schematu konfiguracji rozszerzenia diagnostyki i Historia
Indeksy tej strony wersje Schemat rozszerzenia diagnostyki Azure dostarczana jako część programu Microsoft Azure SDK.  

> [!NOTE]
> Rozszerzenie diagnostyki platformy Azure to składnik używany do zbierania liczników wydajności i innych danych statystycznych od:
> - Usługa Azure Virtual Machines
> - Zestawy skali maszyn wirtualnych
> - Service Fabric
> - Usługi w chmurze
> - Grupy zabezpieczeń sieci
>
> Ta strona ma zastosowanie tylko jeśli używasz jednej z tych usług.

Rozszerzenie diagnostyki platformy Azure jest używana z innymi produktami firmy Microsoft diagnostyki, takich jak Azure Monitor, który zawiera usługi Application Insights i Log Analytics. Aby uzyskać więcej informacji, zobacz [omówienie narzędzi monitorowania Microsoft](../../azure-monitor/overview.md).

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure wersji zestawu SDK i Diagnostyka wysyłania wykresu  

|Wersja zestawu SDK platformy Azure | Wersja rozszerzenia diagnostyki | Modelowanie|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |plug-in|  
|2.0 - 2.4         |1.0                            |plug-in|  
|2.5               |1.2                            |Rozszerzenie|  
|2.6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 Azure Diagnostics w wersji 1.0 po raz pierwszy wysłane w dodatku model — co oznacza, że podczas instalowania zestawu Azure SDK masz wersję diagnostyki platformy Azure są dostarczane z go.  

 Począwszy od zestawu SDK 2.5 (Diagnostyka w wersji 1.2), usługi Diagnostyka Azure zmieniał się do modelu rozszerzeń. Narzędzia umożliwiające korzystanie z nowych funkcji tylko były dostępne w nowszej zestawy SDK platformy Azure, ale dowolną usługą za pomocą diagnostyki platformy Azure będzie pobranie najnowszej wersji wysyłki bezpośrednio na platformie Azure. Na przykład każda osoba nadal przy użyciu zestawu SDK 2.5 będą ładowane najnowszej wersji, które są wyświetlane w poprzedniej tabeli, niezależnie od tego, jeśli użytkownicy korzystają z nowszych funkcji.  

## <a name="schemas-index"></a>Indeks schematów  
Różne wersje diagnostyki platformy Azure Użyj innej konfiguracji schematów.

[Schemat konfiguracji usługi Diagnostyka 1.0](diagnostics-extension-schema-1dot0.md)  

[Schemat konfiguracji usługi Diagnostyka 1.2](diagnostics-extension-schema-1dot2.md)  

[Diagnostyka 1.3 i nowsze schemat konfiguracji](diagnostics-extension-schema-1dot3.md)  

## <a name="version-history"></a>Historia wersji

### <a name="diagnostics-extension-111"></a>Rozszerzenie diagnostyki 1.11
Dodano obsługę dla ujścia usługi Azure Monitor. Ten obiekt sink ma zastosowanie tylko do liczników wydajności. Umożliwia wysyłanie liczniki wydajności zebrane na maszynie Wirtualnej, zestawu skalowania maszyn wirtualnych lub usługi w chmurze do usługi Azure Monitor jako metryki niestandardowe. Obsługuje ujścia usługi Azure Monitor:
* Pobieranie wszystkich liczników wydajności wysyłane do usługi Azure Monitor za pośrednictwem [metryk usługi Azure Monitor interfejsów API.](https://docs.microsoft.com/rest/api/monitor/metrics/list)
* Alertów dla wszystkich liczników wydajności wysyłane do usługi Azure Monitor w nowej witrynie [ujednolicone środowisko alertów](../../azure-monitor/platform/alerts-overview.md) w usłudze Azure Monitor
* Traktowanie operator symbolu wieloznacznego w liczniki wydajności jako wymiar "Wystąpienie" w swoje metryki. Na przykład, gdy zostały zebrane "dysk logiczny (\*) / DiskWrites na sekundę" licznik będzie mieć możliwość filtrowania i podział wymiaru "Wystąpienie" do kreślenia lub alertu na zapisy dysku/s dla każdego dysku logicznego (C: D:, itp.)

Usługa Azure Monitor jest definiowana jako nowy obiekt sink w konfiguracji rozszerzenia diagnostyki
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```XML
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> Konfigurowanie ujścia usługi Azure Monitor dla klasycznych maszyn wirtualnych i klasycznej usługi w chmurze wymaga więcej parametrów zdefiniowanych w konfiguracji prywatnej rozszerzenia diagnostyki.
>
> Aby uzyskać więcej informacji, zapoznaj się z odwołania [szczegółowa dokumentacja Schemat rozszerzenia diagnostyki.](diagnostics-extension-schema-1dot3.md)

Następnie należy skonfigurować liczniki wydajności do ujścia usługi Azure Monitor.
```json
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "AzureMonitorSink",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT1M",
            "unit": "percent"
        }
    ]
},
```
```XML
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>Rozszerzenie diagnostyki 1.9
Dodano obsługę platformy Docker.


### <a name="diagnostics-extension-181"></a>Rozszerzenie diagnostyki 1.8.1
Można określić token sygnatury dostępu Współdzielonego zamiast klucza konta magazynu w konfiguracji prywatnej. Jeśli zostanie podany SAS token, klucz konta magazynu jest ignorowany.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Rozszerzenie diagnostyki 1.8
Typ magazynu dodanych do PublicConfig. Może być StorageType *tabeli*, *Blob*, *TableAndBlob*. *Tabela* jest ustawieniem domyślnym.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Rozszerzenie diagnostyki 1.7
Dodano możliwość kierować do Centrum zdarzeń.

### <a name="diagnostics-extension-15"></a>Rozszerzenie diagnostyki w wersji 1.5
Dodano element ujścia i możliwość wysyłania danych diagnostycznych [usługi Application Insights](../../azure-monitor/app/cloudservices.md) ułatwia diagnozowanie problemów z aplikacji, jak i w poziomie systemu i infrastruktury.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Rozszerzenie platformy Azure SDK 2.6 i Diagnostyka 1.3
Dla projektów usług w chmurze w programie Visual Studio wprowadzono następujące zmiany. (Te zmiany dotyczą również nowsze wersje zestawu Azure SDK.)

* Lokalne emulator obsługuje teraz diagnostyki. Ta zmiana oznacza, że można zbierać dane diagnostyczne i upewnij się, że aplikacja tworzy odpowiednie dane śledzenia, podczas tworzenia i testowania w programie Visual Studio. Parametry połączenia `UseDevelopmentStorage=true` powoduje włączenie zbierania danych diagnostycznych, gdy korzystasz z projektu usługi w chmurze w programie Visual Studio przy użyciu emulatora usługi Azure storage. Wszystkie dane diagnostyczne są zbierane w ramach konta magazynu (Tworzenie magazynu).
* Parametry połączenia konta magazynu diagnostyki (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) są przechowywane w jeszcze raz w pliku konfiguracji (cscfg) usługi. W systemie Azure SDK 2.5 w pliku diagnostics.wadcfgx określono konto magazynu diagnostyki.

Istnieją pewne istotne różnice między jak parametry połączenia pracy w usłudze Azure SDK 2.4 i starszych i jak działa w wersji 2.6 zestawu SDK platformy Azure i później.

* W usłudze Azure SDK 2.4 lub starszej parametry połączenia był używany w czasie wykonywania przez wtyczki diagnostyki Aby uzyskać informacje o koncie magazynu, przesyłania dzienników diagnostycznych.
* W wersji 2.6 zestawu SDK platformy Azure i później programu Visual Studio używa parametrów połączenia diagnostyki, aby skonfigurować rozszerzenie diagnostyki z informacjami o koncie magazynu odpowiednie podczas publikowania. Parametry połączenia umożliwiają definiowanie różnych kont magazynu dla konfiguracji innej usługi, używających programu Visual Studio podczas publikowania. Jednak ponieważ wtyczki Diagnostyka nie jest już dostępne (po Azure SDK 2.5), w pliku .cscfg przez sam nie można włączyć rozszerzenie diagnostyki. Należy włączyć rozszerzenie oddzielnie za pomocą narzędzi, takich jak Visual Studio lub programu PowerShell.
* Aby uprościć proces konfigurowania rozszerzenia diagnostyki za pomocą programu PowerShell, dane wyjściowe pakietu Visual Studio zawiera również publiczna Konfiguracja XML dla rozszerzenia diagnostyki dla każdej roli. Visual Studio używa parametrów połączenia diagnostyki do wypełniania informacji o koncie magazynu, które są obecne w konfiguracji publicznego. Pliki konfiguracji publicznego są tworzone w folderze rozszerzeń i postępuj zgodnie ze wzorcem `PaaSDiagnostics.<RoleName>.PubConfig.xml`. Wszystkie wdrożenia programu PowerShell, na podstawie może używać tego wzorca, aby zamapować każdej konfiguracji do roli.
* Parametry połączenia w pliku .cscfg jest również używane przez witryny Azure portal dostępu do danych diagnostycznych, dzięki czemu może występować w **monitorowanie** kartę. Ciąg połączenia jest wymagane do skonfigurowania usługi, aby wyświetlić pełne dane monitorowania w portalu.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrowanie projektów 2.6 zestawu SDK platformy Azure i nowszych
Podczas migracji z zestawu SDK Azure 2.5 do zestawu SDK platformy Azure w wersji 2.6 lub nowszej, jeśli masz konto magazynu diagnostyki, określone w pliku .wadcfgx, następnie pozostanie tam. Aby skorzystać z elastyczności przy użyciu różnych kont magazynu dla konfiguracji innego magazynu, musisz ręcznie dodać parametry połączenia do swojego projektu. W przypadku migrowania projektów z systemu Azure SDK 2.4 lub jego starszej wersji 2.6 zestawu SDK platformy Azure, Diagnostyka parametry połączenia zostaną zachowane. Należy jednak zwrócić uwagę na zmiany w jak parametry połączenia są traktowane w wersji 2.6 zestawu SDK platformy Azure jak określono w poprzedniej sekcji.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Jak program Visual Studio Określa konto magazynu diagnostyki
* Jeśli ciąg połączenia diagnostyki jest określona w pliku .cscfg, Visual Studio używa go skonfigurować rozszerzenie diagnostyki podczas publikowania i podczas generowania pliki xml konfiguracji publicznego podczas tworzenia pakietów.
* Jeśli nie diagnostyki parametrów połączenia jest określona w pliku .cscfg, następnie programu Visual Studio przełączy się konto magazynu określone w pliku .wadcfgx skonfigurować rozszerzenie diagnostyki podczas publikowania i generuje pliki xml konfiguracji publicznej Podczas pakowania.
* Diagnostyka parametry połączenia w pliku .cscfg mają pierwszeństwo przed konta magazynu w pliku .wadcfgx. Jeśli ciąg połączenia diagnostyki jest określona w pliku .cscfg, Visual Studio używa i ignoruje konta magazynu w .wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Do czego służy pole wyboru "Aktualizuj parametry połączenia magazynu rozwoju..."?
Pole wyboru **zaktualizować parametry połączenia magazynu rozwoju dotyczące danych diagnostycznych i pamięci podręcznej przy użyciu poświadczeń konta magazynu Microsoft Azure podczas publikowania w systemie Microsoft Azure** zapewnia wygodny sposób zaktualizować wszelkie rozwoju Parametry połączenia konta magazynu przy użyciu konta usługi Azure storage określone podczas publikowania.

Załóżmy na przykład, zaznacz to pole wyboru i określa parametry połączenia diagnostyki `UseDevelopmentStorage=true`. Podczas publikowania projektu na platformie Azure, programu Visual Studio automatycznie aktualizuje diagnostyki parametry połączenia z kontem magazynu, które określiłeś w Kreatorze publikacji. Jednak jeśli konto magazynu w rzeczywistych został określony jako parametry połączenia, Diagnostyka, następnie to konto jest używana zamiast tego.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diagnostyka różnice w funkcjonalności między Azure SDK 2.4 i starszych i Azure SDK 2.5 lub nowszej
Jeśli aktualizujesz projekt z Azure SDK 2.4 do zestawu SDK Azure 2.5 lub nowszej, możesz powinny mieć na uwadze następujące różnice funkcji diagnostyki.

* **Interfejsy API konfiguracji są przestarzałe** — konfigurację programistyczną diagnostyki jest dostępne w Azure SDK 2.4 i jego wcześniejsze wersje, ale jest przestarzała w programie Azure SDK 2.5 i nowszych. Konfigurację diagnostyki jest obecnie zdefiniowany w kodzie, musisz ponownie skonfigurować te ustawienia od podstaw w projekcie migrowane w kolejności do diagnostyki, aby kontynuować pracę. Plik konfiguracji diagnostyki Azure SDK 2.4 jest diagnostics.wadcfg i diagnostics.wadcfgx dla zestawu SDK Azure 2.5 i nowszych.
* **Diagnostyka dla aplikacji usługi w chmurze można skonfigurować tylko na poziomie roli, nie na poziomie wystąpienia.**
* **Za każdym razem, gdy aplikacja jest wdrażana, zostaje aktualizowana Konfiguracja diagnostyki** — może to spowodować problemy z parzystością, jeśli zmiana konfiguracji diagnostyki za pomocą Eksploratora serwera, a następnie ponownie wdrożyć aplikację.
* **W Azure SDK 2.5 lub nowszej, zrzuty awaryjne są konfigurowane w pliku konfiguracji diagnostyki, nie w kodzie** — Jeśli masz zrzuty awaryjne skonfigurowane w kodzie, musisz ręcznie przenieść konfigurację z kodu do pliku konfiguracji, ponieważ zrzuty awaryjne nie są przekazywane podczas migracji 2.6 zestawu SDK platformy Azure.

