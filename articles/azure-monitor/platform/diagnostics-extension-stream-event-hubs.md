---
title: Wysyłanie danych z rozszerzenia diagnostyki systemu Windows Azure do usługi Azure Event Hubs
description: Skonfiguruj rozszerzenie diagnostyki w usłudze Azure Monitor, aby wysyłać dane do usługi Azure Event Hub, aby można było przesyłać je dalej do lokalizacji poza platformą Azure.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 5e5034e99d37d3681192c2ad066f28acd1c4aeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672535"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Wysyłanie danych z rozszerzenia diagnostyki systemu Windows Azure do usługi Azure Event Hubs
Rozszerzenie diagnostyki platformy Azure jest agentem w usłudze Azure Monitor, który zbiera dane monitorowania z systemu operacyjnego gościa i obciążeń maszyn wirtualnych platformy Azure i innych zasobów obliczeniowych. W tym artykule opisano sposób wysyłania danych z rozszerzenia diagnostyki systemu Windows Azure (WAD) do [usługi Azure Event Hubs,](https://azure.microsoft.com/services/event-hubs/) dzięki czemu można przesyłać dalej do lokalizacji poza platformą Azure.

## <a name="supported-data"></a>Obsługiwane dane

Dane zebrane z systemu operacyjnego gościa, które mogą być wysyłane do centrum zdarzeń, obejmują następujące czynności. Inne źródła danych zebrane przez WAD, w tym dzienniki i zrzuty awaryjne i zrzuty awaryjne, nie mogą być wysyłane do centrów zdarzeń.

* Zdarzenia funkcji Śledzenie zdarzeń systemu Windows (ETW)
* Liczniki wydajności
* Dzienniki zdarzeń systemu Windows, w tym dzienniki aplikacji w dzienniku zdarzeń systemu Windows
* Dzienniki infrastruktury diagnostyki Azure

## <a name="prerequisites"></a>Wymagania wstępne

* Rozszerzenie diagnostyki systemu Windows 1.6 lub nowsze. Zobacz [wersje schematu konfiguracji rozszerzenia usługi Azure Diagnostics, aby](diagnostics-extension-versions.md) uzyskać historię wersji i [omówienie rozszerzenia diagnostyki platformy Azure](diagnostics-extension-overview.md) dla obsługiwanych zasobów.
* Obszar nazw Centrum zdarzeń musi być zawsze aprowizowana. Zobacz [Wprowadzenie do centrów zdarzeń, aby](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) uzyskać szczegółowe informacje.


## <a name="configuration-schema"></a>Schemat konfiguracji
Zobacz [Instalowanie i konfigurowanie rozszerzenia diagnostyki systemu Windows Azure (WAD)](diagnostics-extension-windows-install.md) dla różnych opcji włączania i konfigurowania rozszerzenia diagnostyki i [schematu konfiguracji diagnostyki platformy Azure](diagnostics-extension-schema-windows.md) w celu uzyskania odniesienia do schematu konfiguracji. W dalszej części tego artykułu opisano, jak używać tej konfiguracji do wysyłania danych do centrum zdarzeń. 

Diagnostyka platformy Azure zawsze wysyła dzienniki i metryki do konta usługi Azure Storage. Można skonfigurować co najmniej jeden *pochłaniacz danych,* które wysyłają dane do dodatkowych lokalizacji. Każdy obiekt sink jest zdefiniowany w [SinksConfig element](diagnostics-extension-schema-windows.md#sinksconfig-element) konfiguracji publicznej z poufnych informacji w konfiguracji prywatnej. Ta konfiguracja dla centrów zdarzeń używa wartości w poniższej tabeli.

| Właściwość | Opis |
|:---|:---|
| Nazwa | Opisowa nazwa ujścia. Używane w konfiguracji, aby określić, które źródła danych do wysłania do ujścia. |
| Url  | Adres URL centrum zdarzeń \<w obszarze\>nazw event-hubs-servicebus.windows.net/\<event-hub-name\>.          |
| SharedAccessKeyName | Nazwa zasady dostępu współdzielonego dla centrum zdarzeń, które ma co najmniej **uprawnienia wysyłania.** |
| SharedAccessKey     | Klucz podstawowy lub pomocniczy z zasad dostępu współużytkowego dla Centrum zdarzeń. |

Poniżej przedstawiono przykładowe konfiguracje publiczne i prywatne. Jest to minimalna konfiguracja z jednego licznika wydajności i dziennika zdarzeń, aby zilustrować, jak skonfigurować i używać ujścia danych centrum zdarzeń. Zobacz [schemat konfiguracji diagnostyki platformy Azure](diagnostics-extension-schema-windows.md) dla bardziej złożonego przykładu.

### <a name="public-configuration"></a>Konfiguracja publiczna

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120
        },
        "PerformanceCounters": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
                "DataSource": [
                {
                    "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                }
            ]
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Konfiguracja prywatna

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Opcje konfiguracji
Aby wysłać dane do ujścia danych, należy określić atrybut **pochłaniacze** w węźle źródła danych. Miejsce, w którym atrybut **sinks** określa zakres przypisania. W poniższym przykładzie **sinks** atrybut jest zdefiniowany do **węźle PerformanceCounters,** co spowoduje, że wszystkie liczniki wydajności podrzędnych mają być wysyłane do centrum zdarzeń.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


W poniższym przykładzie **atrybucje** atrybut jest stosowany bezpośrednio do trzech liczników, które spowoduje, że tylko te liczniki wydajności mają być wysyłane do centrum zdarzeń. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>Sprawdzanie poprawności konfiguracji
Można użyć różnych metod, aby sprawdzić, czy dane są wysyłane do centrum zdarzeń. ne prosta metoda polega na użyciu przechwytywania centrum zdarzeń zgodnie z opisem w [usłudze Capture events za pośrednictwem usługi Azure Event Hubs w usłudze Azure Blob Storage lub usłudze Azure Data Lake Storage.](../../event-hubs/event-hubs-capture-overview.md) 


## <a name="troubleshoot-event-hubs-sinks"></a>Rozwiązywanie problemów z zajmami z centrami zdarzeń

- Spójrz na tabeli usługi Azure Storage **WADDiagnosticInfrastructureLogsTable,** która zawiera dzienniki i błędy dla samej diagnostyki platformy Azure. Jedną z opcji jest użycie narzędzia, takiego jak [Eksplorator usługi Azure Storage,](https://www.storageexplorer.com) aby połączyć się z tym kontem magazynu, wyświetlić tę tabelę i dodać kwerendę dla sygnatury czasowej w ciągu ostatnich 24 godzin. Za pomocą tego narzędzia można wyeksportować plik csv i otworzyć go w aplikacji, takiej jak Microsoft Excel. Program Excel ułatwia wyszukiwanie ciągów kart telefonicznych, takich jak **EventHubs,** aby zobaczyć, jaki błąd jest zgłaszany.  

- Sprawdź, czy centrum zdarzeń zostało pomyślnie zainicjowane. Wszystkie informacje o połączeniu w **sekcji PrivateConfig** konfiguracji muszą być zgodne z wartościami zasobu, jak w portalu. Upewnij się, że masz zdefiniowane zasady sygnatury dostępu Współdzielonego *(SendRule* w przykładzie) w portalu i że uprawnienie *Wyślij* jest przyznawane.  

## <a name="next-steps"></a>Następne kroki

* [Przegląd usługi Event Hubs](../../event-hubs/event-hubs-about.md)
* [Tworzenie centrum zdarzeń](../../event-hubs/event-hubs-create.md)
* [Event Hubs — często zadawane pytania](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



