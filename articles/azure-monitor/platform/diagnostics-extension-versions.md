---
title: Historia wersji schematu konfiguracji rozszerzenia diagnostyki systemu Windows Azure (WAD)
description: Istotne dla zbierania liczników perf w maszynach wirtualnych platformy Azure, zestawów skalowania maszyn wirtualnych, sieci szkieletowej usług i usług w chmurze.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: 4dd91363cdebf18e6303238816e8269065a6a317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672246"
---
# <a name="windows-azure-diagnostics-extension-wad-configuration-schema-versions-and-history"></a>Wersje i historia schematu konfiguracji rozszerzenia diagnostyki systemu Windows Azure (WAD)
Ten artykuł zawiera historię wersji rozszerzenia diagnostyki platformy Azure dla wersji schematu [systemu Windows (WAD)](diagnostics-extension-overview.md) dostarczonych w ramach zestawu SDK platformy Microsoft Azure.  


## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Wykres wysyłkowy zestawów SDK i diagnostyki platformy Azure  

|Wersja zestawu SDK platformy Azure | Wersja rozszerzenia diagnostyki | Model|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |Wtyczki|  
|2.0 - 2.4         |1.0                            |Wtyczki|  
|2.5               |1.2                            |rozszerzenie|  
|2,6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2,8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 Usługa Azure Diagnostics w wersji 1.0 została po raz pierwszy dostarczona w modelu wtyczki — co oznacza, że po zainstalowaniu narzędzia Azure SDK została dostarczona wersja diagnostyki platformy Azure.  

 Począwszy od SDK 2.5 (diagnostyka w wersji 1.2), diagnostyka platformy Azure przeszła do modelu rozszerzenia. Narzędzia do korzystania z nowych funkcji były dostępne tylko w nowszych zestawach SDK platformy Azure, ale każda usługa korzystająca z diagnostyki platformy Azure odbierała najnowszą wersję wysyłkową bezpośrednio z platformy Azure. Na przykład każda osoba, która nadal używa zestawu SDK 2.5, będzie ładować najnowszą wersję pokazaną w poprzedniej tabeli, niezależnie od tego, czy korzysta z nowszych funkcji.  

## <a name="schemas-index"></a>Indeks schematów  
Różne wersje diagnostyki platformy Azure używają różnych schematów konfiguracji. Schematy 1.0 i 1.2 zostały przestarzałe. Aby uzyskać więcej informacji na temat wersji 1.3 i [nowszych, zobacz Diagnostyka 1.3 i nowsza Konfiguracja Schemat](diagnostics-extension-schema-windows.md)  

## <a name="version-history"></a>Historia wersji

### <a name="diagnostics-extension-111"></a>Rozszerzenie diagnostyki 1.11
Dodano obsługę ujścia usługi Azure Monitor. Ten umywalka ma zastosowanie tylko do liczników wydajności. Umożliwia wysyłanie liczników wydajności zebranych na maszynie wirtualnej, maszynie Wirtualnej lub usłudze w chmurze do usługi Azure Monitor jako metryki niestandardowe. Ujście usługi Azure Monitor obsługuje:
* Pobieranie wszystkich liczników wydajności wysyłanych do usługi Azure Monitor za pośrednictwem [interfejsów API metryk usługi Azure Monitor.](https://docs.microsoft.com/rest/api/monitor/metrics/list)
* Alerty dotyczące wszystkich liczników wydajności wysyłanych do usługi Azure Monitor za pośrednictwem nowego [środowiska ujednoliconych alertów](../../azure-monitor/platform/alerts-overview.md) w usłudze Azure Monitor
* Traktowanie operatora symboli wieloznacznych w licznikach wydajności jako wymiaru "Wystąpienie" w metryki. Na przykład, jeśli zebrano\*licznik "LogicalDisk( )/DiskWrites/sec" można filtrować i dzielić wymiar "Wystąpienie", aby wykreślić lub alert na dysku Zapisy/s dla każdego dysku logicznego (C:, D:, itp.)

Definiowanie usługi Azure Monitor jako nowego ujścia w konfiguracji rozszerzenia diagnostyki
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
> Konfigurowanie ujścia usługi Azure Monitor dla klasycznych maszyn wirtualnych i klasycznej usługi CLoud wymaga zdefiniowania większej liczby parametrów w prywatnej konfiguracji rozszerzenia diagnostyki.
>
> Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją szczegółowego schematu rozszerzenia diagnostyki.](diagnostics-extension-schema-windows.md)

Następnie można skonfigurować liczniki wydajności, które mają być kierowane do ujścia usługi Azure Monitor.
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
Można określić token sygnatury dostępu Współdzielonego zamiast klucza konta magazynu w konfiguracji prywatnej. Jeśli zostanie podany token sygnatury dostępu Współdzielonego, klucz konta magazynu jest ignorowany.


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
Dodano typ magazynu do PublicConfig. StorageType może być *Tabela*, *Blob*, *TableAndBlob*. *Tabela* jest wartością domyślną.


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
Dodano możliwość trasy do EventHub.

### <a name="diagnostics-extension-15"></a>Rozszerzenie diagnostyki 1.5
Dodano element pochłaniacze i możliwość wysyłania danych diagnostycznych do [usługi Application Insights,](../../azure-monitor/app/cloudservices.md) co ułatwia diagnozowanie problemów w aplikacji, a także na poziomie systemu i infrastruktury.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Zestaw SDK 2.6 platformy Azure i rozszerzenie diagnostyki 1.3
W przypadku projektów usługi w chmurze w programie Visual Studio wprowadzono następujące zmiany. (Te zmiany dotyczą również nowszych wersji narzędzia Azure SDK).

* Lokalny emulator obsługuje teraz diagnostykę. Ta zmiana oznacza, że można zbierać dane diagnostyczne i upewnij się, że aplikacja tworzy odpowiednie ślady podczas tworzenia i testowania w programie Visual Studio. Parametry `UseDevelopmentStorage=true` połączenia umożliwia zbieranie danych diagnostycznych podczas uruchamiania projektu usługi w chmurze w programie Visual Studio przy użyciu emulatora magazynu platformy Azure. Wszystkie dane diagnostyczne są zbierane na koncie magazynu (Magazyn dewelopera).
* Parametry połączenia konta magazynu diagnostyki (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) są ponownie przechowywane w pliku konfiguracji usługi (cscfg). W usłudze Azure SDK 2.5 konto magazynu diagnostyki zostało określone w pliku diagnostics.wadcfgx.

Istnieją pewne istotne różnice między jak parametry połączenia działał w usłudze Azure SDK 2.4 i wcześniejszych i jak to działa w usłudze Azure SDK 2.6 i nowsze.

* W usłudze Azure SDK 2.4 i wcześniejszych ciąg połączenia był używany w czasie wykonywania przez wtyczkę diagnostyki, aby uzyskać informacje o koncie magazynu do przesyłania dzienników diagnostycznych.
* W usłudze Azure SDK 2.6 lub nowszych visual studio używa ciągu połączenia diagnostyki, aby skonfigurować rozszerzenie diagnostyki z odpowiednimi informacjami o koncie magazynu podczas publikowania. Parametry połączenia umożliwia definiowanie różnych kont magazynu dla różnych konfiguracji usługi, które będą używane przez program Visual Studio podczas publikowania. Jednak ponieważ wtyczka diagnostyki nie jest już dostępna (po platformie Azure SDK 2.5), plik .cscfg sam nie może włączyć rozszerzenia diagnostyki. Należy włączyć rozszerzenie oddzielnie za pomocą narzędzi, takich jak Visual Studio lub PowerShell.
* Aby uprościć proces konfigurowania rozszerzenia diagnostyki za pomocą programu PowerShell, dane wyjściowe pakietu z programu Visual Studio zawierają również publicznego konfiguracji XML dla rozszerzenia diagnostyki dla każdej roli. Visual Studio używa ciągu połączenia diagnostyki do wypełniania informacji o koncie magazynu znajdujących się w konfiguracji publicznej. Publiczne pliki konfiguracyjne są tworzone w folderze `PaaSDiagnostics.<RoleName>.PubConfig.xml`Rozszerzenia i są zgodne ze wzorcem . Wszystkie wdrożenia oparte na programie PowerShell można użyć tego wzorca do mapowania każdej konfiguracji do roli.
* Parametry połączenia w pliku cscfg są również używane przez witrynę Azure portal w celu uzyskania dostępu do danych diagnostycznych, dzięki czemu mogą być wyświetlane na karcie **Monitorowanie.** Parametry połączenia jest potrzebne do skonfigurowania usługi, aby wyświetlić pełne dane monitorowania w portalu.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrowanie projektów do usługi Azure SDK 2.6 lub nowszych
Podczas migracji z narzędzia Azure SDK 2.5 do usługi Azure SDK 2.6 lub nowszej, jeśli masz konto magazynu diagnostyki określone w pliku .wadcfgx, a następnie pozostanie tam. Aby skorzystać z elastyczności przy użyciu różnych kont magazynu dla różnych konfiguracji magazynu, należy ręcznie dodać parametry połączenia do projektu. Jeśli przeprowadzasz migrację projektu z narzędzia Azure SDK 2.4 lub starszego do narzędzia Azure SDK 2.6, parametry połączenia diagnostyki zostaną zachowane. Należy jednak zwrócić uwagę na zmiany w sposobie traktowania ciągów połączeń w usłudze Azure SDK 2.6, jak określono w poprzedniej sekcji.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Jak program Visual Studio określa konto magazynu diagnostyki
* Jeśli ciąg połączenia diagnostyki jest określony w pliku cscfg, program Visual Studio używa go do skonfigurowania rozszerzenia diagnostyki podczas publikowania i podczas generowania publicznych plików xml konfiguracji podczas pakowania.
* Jeśli w pliku cscfg nie określono żadnego ciągu połączenia diagnostycznego, program Visual Studio powróci do korzystania z konta magazynu określonego w pliku wadcfgx w celu skonfigurowania rozszerzenia diagnostyki podczas publikowania i generowania publicznych plików xml konfiguracji podczas pakowania.
* Parametry połączenia diagnostyki w pliku cscfg mają pierwszeństwo przed kontem magazynu w pliku wadcfgx. Jeśli ciąg połączenia diagnostyki jest określony w pliku cscfg, program Visual Studio używa tego i ignoruje konto magazynu w programie .wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Co zawiera ciągi połączeń "Aktualizuj parametry połączeń magazynu deweloperów..." pole wyboru zrobić?
Pole wyboru **Dla aktualizowania parametry połączenia magazynu deweloperów dla diagnostyki i buforowania z poświadczeniami konta magazynu platformy Microsoft Azure podczas publikowania na platformie Microsoft Azure** zapewnia wygodny sposób aktualizowania wszystkich ciągów połączeń magazynu deweloperów za pomocą konta magazynu platformy Azure określonego podczas publikowania.

Załóżmy na przykład, że to pole wyboru `UseDevelopmentStorage=true`zostanie zaznaczone, a parametry połączenia diagnostycznego określają . Po opublikowaniu projektu na platformie Azure program Visual Studio automatycznie zaktualizuje parametry połączenia diagnostyki z kontem magazynu określonym w Kreatorze publikowania. Jeśli jednak konto rzeczywistego magazynu zostało określone jako parametry połączenia diagnostyki, zamiast tego używane jest to konto.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Różnice w funkcjonalności diagnostyki między zestawem SDK 2.4 platformy Azure i starszym a zestawem SDK 2.5 platformy Azure lub nowszym
Jeśli uaktualniasz projekt z narzędzia Azure SDK 2.4 do usługi Azure SDK 2.5 lub nowszej, należy pamiętać o następujących różnicach funkcji diagnostyki.

* **Interfejsy API konfiguracji są przestarzałe** — programowa konfiguracja diagnostyki jest dostępna w usłudze Azure SDK 2.4 lub wcześniejszych wersjach, ale jest przestarzała w usłudze Azure SDK 2.5 i nowszych wersjach. Jeśli konfiguracja diagnostyki jest obecnie zdefiniowana w kodzie, należy ponownie skonfigurować te ustawienia od podstaw w zmigrowanym projekcie, aby diagnostyka działała dalej. Plik konfiguracji diagnostyki dla usługi Azure SDK 2.4 to diagnostics.wadcfg i diagnostics.wadcfgx dla usługi Azure SDK 2.5 i nowsze.
* **Diagnostyka dla aplikacji usługi w chmurze można skonfigurować tylko na poziomie roli, a nie na poziomie wystąpienia.**
* **Za każdym razem, gdy wdrażasz aplikację, konfiguracja diagnostyki jest aktualizowana** — może to spowodować problemy z parzystością, jeśli zmienisz konfigurację diagnostyki z Eksploratora serwera, a następnie ponownie wdrożysz aplikację.
* **W usłudze Azure SDK 2.5 i nowszych zrzuty awaryjne są konfigurowane w pliku konfiguracji diagnostyki, a nie w kodzie** — jeśli masz zrzuty awaryjne skonfigurowane w kodzie, musisz ręcznie przenieść konfigurację z kodu do pliku konfiguracji, ponieważ zrzuty awaryjne nie są przesyłane podczas migracji do usługi Azure SDK 2.6.

