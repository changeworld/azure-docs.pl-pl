---
title: Zdarzenia interfejsu wiersza polecenia sieci szkieletowej usługi Azure
description: Zawiera opis poleceń zdarzeń sfctl sieci szkieletowej usług.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906095"
---
# <a name="sfctl-events"></a>sfctl wydarzenia
Pobieranie zdarzeń z magazynu zdarzeń (jeśli usługa EventStore jest już zainstalowana).

Usługę systemu EventStore można dodać za pomocą uaktualnienia konfiguracji do dowolnego klastra SFRP uruchomionego >=6.4. Sprawdź następujący adres\: URL\:https //docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| lista wszystkich aplikacji | Pobiera wszystkie zdarzenia związane z aplikacjami. |
| lista wszystkich węzłów | Pobiera wszystkie zdarzenia związane z węzłami. |
| lista wszystkich partycji | Pobiera wszystkie zdarzenia związane z partycjami. |
| lista wszystkich usług | Pobiera wszystkie zdarzenia związane z usługami. |
| lista aplikacji | Pobiera zdarzenia związane z aplikacją. |
| lista klastrów | Pobiera wszystkie zdarzenia związane z klastrem. |
| lista węzłów | Pobiera zdarzenia związane z węzłem. |
| lista wszystkich replik partycji | Pobiera wszystkie zdarzenia związane z replikami dla partycji. |
| lista partycji | Pobiera zdarzenia związane z partycją. |
| lista replik partycji | Pobiera zdarzenia związane z repliką partycji. |
| lista usług | Pobiera zdarzenia związane z usługą. |

## <a name="sfctl-events-all-applications-list"></a>sfctl events all-applications-list sfctl events all-applications-list sfctl events all-applications-list sfc
Pobiera wszystkie zdarzenia związane z aplikacjami.

Odpowiedź jest lista ApplicationEvent obiektów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --end-time-utc [Wymagane] | Godzina zakończenia kwerendy odnośnika w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Wymagane] | Godzina rozpoczęcia kwerendy odnośnej w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Jest to ciąg oddzielony przecinkami określający typy FabricEvents, które powinny być uwzględnione tylko w odpowiedzi. |
| --exclude-analysis-events | Ten param wyłącza pobieranie AnalysisEvents jeśli true jest przekazywana. |
| --skip-correlation-lookup | Ten param wyłącza wyszukiwanie informacji CorrelatedEvents, jeśli prawda jest przekazywana. w przeciwnym razie CorrelationEvents uzyskać przetwarzane i HasCorrelatedEvents pola w każdym FabricEvent zostanie wypełniona. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl events all-nodes-list sfctl events all-nodes-list Sfctl events all-nodes-list Sfc
Pobiera wszystkie zdarzenia związane z węzłami.

Odpowiedzią jest lista obiektów NodeEvent.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --end-time-utc [Wymagane] | Godzina zakończenia kwerendy odnośnika w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Wymagane] | Godzina rozpoczęcia kwerendy odnośnej w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Jest to ciąg oddzielony przecinkami określający typy FabricEvents, które powinny być uwzględnione tylko w odpowiedzi. |
| --exclude-analysis-events | Ten param wyłącza pobieranie AnalysisEvents jeśli true jest przekazywana. |
| --skip-correlation-lookup | Ten param wyłącza wyszukiwanie informacji CorrelatedEvents, jeśli prawda jest przekazywana. w przeciwnym razie CorrelationEvents uzyskać przetwarzane i HasCorrelatedEvents pola w każdym FabricEvent zostanie wypełniona. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl zdarzenia all-partitions-list
Pobiera wszystkie zdarzenia związane z partycjami.

Odpowiedź jest lista PartitionEvent obiektów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --end-time-utc [Wymagane] | Godzina zakończenia kwerendy odnośnika w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Wymagane] | Godzina rozpoczęcia kwerendy odnośnej w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Jest to ciąg oddzielony przecinkami określający typy FabricEvents, które powinny być uwzględnione tylko w odpowiedzi. |
| --exclude-analysis-events | Ten param wyłącza pobieranie AnalysisEvents jeśli true jest przekazywana. |
| --skip-correlation-lookup | Ten param wyłącza wyszukiwanie informacji CorrelatedEvents, jeśli prawda jest przekazywana. w przeciwnym razie CorrelationEvents uzyskać przetwarzane i HasCorrelatedEvents pola w każdym FabricEvent zostanie wypełniona. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-all-services-list"></a>sfctl wydarzenia all-services-list
Pobiera wszystkie zdarzenia związane z usługami.

Odpowiedź jest lista ServiceEvent obiektów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --end-time-utc [Wymagane] | Godzina zakończenia kwerendy odnośnika w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Wymagane] | Godzina rozpoczęcia kwerendy odnośnej w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Jest to ciąg oddzielony przecinkami określający typy FabricEvents, które powinny być uwzględnione tylko w odpowiedzi. |
| --exclude-analysis-events | Ten param wyłącza pobieranie AnalysisEvents jeśli true jest przekazywana. |
| --skip-correlation-lookup | Ten param wyłącza wyszukiwanie informacji CorrelatedEvents, jeśli prawda jest przekazywana. w przeciwnym razie CorrelationEvents uzyskać przetwarzane i HasCorrelatedEvents pola w każdym FabricEvent zostanie wypełniona. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-application-list"></a>lista aplikacji zdarzeń sfctl
Pobiera zdarzenia związane z aplikacją.

Odpowiedź jest lista ApplicationEvent obiektów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --end-time-utc [Wymagane] | Godzina zakończenia kwerendy odnośnika w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Wymagane] | Godzina rozpoczęcia kwerendy odnośnej w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Jest to ciąg oddzielony przecinkami określający typy FabricEvents, które powinny być uwzględnione tylko w odpowiedzi. |
| --exclude-analysis-events | Ten param wyłącza pobieranie AnalysisEvents jeśli true jest przekazywana. |
| --skip-correlation-lookup | Ten param wyłącza wyszukiwanie informacji CorrelatedEvents, jeśli prawda jest przekazywana. w przeciwnym razie CorrelationEvents uzyskać przetwarzane i HasCorrelatedEvents pola w każdym FabricEvent zostanie wypełniona. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-cluster-list"></a>lista klastrów zdarzeń sfctl
Pobiera wszystkie zdarzenia związane z klastrem.

Odpowiedzią jest lista obiektów ClusterEvent.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --end-time-utc [Wymagane] | Godzina zakończenia kwerendy odnośnika w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Wymagane] | Godzina rozpoczęcia kwerendy odnośnej w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Jest to ciąg oddzielony przecinkami określający typy FabricEvents, które powinny być uwzględnione tylko w odpowiedzi. |
| --exclude-analysis-events | Ten param wyłącza pobieranie AnalysisEvents jeśli true jest przekazywana. |
| --skip-correlation-lookup | Ten param wyłącza wyszukiwanie informacji CorrelatedEvents, jeśli prawda jest przekazywana. w przeciwnym razie CorrelationEvents uzyskać przetwarzane i HasCorrelatedEvents pola w każdym FabricEvent zostanie wypełniona. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-node-list"></a>lista węzłów zdarzeń sfctl
Pobiera zdarzenia związane z węzłem.

Odpowiedzią jest lista obiektów NodeEvent.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --end-time-utc [Wymagane] | Godzina zakończenia kwerendy odnośnika w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --node-name [Wymagane] | Nazwa węzła. |
| --start-time-utc [Wymagane] | Godzina rozpoczęcia kwerendy odnośnej w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Jest to ciąg oddzielony przecinkami określający typy FabricEvents, które powinny być uwzględnione tylko w odpowiedzi. |
| --exclude-analysis-events | Ten param wyłącza pobieranie AnalysisEvents jeśli true jest przekazywana. |
| --skip-correlation-lookup | Ten param wyłącza wyszukiwanie informacji CorrelatedEvents, jeśli prawda jest przekazywana. w przeciwnym razie CorrelationEvents uzyskać przetwarzane i HasCorrelatedEvents pola w każdym FabricEvent zostanie wypełniona. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl events partition-all-replicas-list Sfctl events partition-all-replicas-list Sfctl events partition-all-replicas-list Sfc
Pobiera wszystkie zdarzenia związane z replikami dla partycji.

Odpowiedzią jest lista replicaevent obiektów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --end-time-utc [Wymagane] | Godzina zakończenia kwerendy odnośnika w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --start-time-utc [Wymagane] | Godzina rozpoczęcia kwerendy odnośnej w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Jest to ciąg oddzielony przecinkami określający typy FabricEvents, które powinny być uwzględnione tylko w odpowiedzi. |
| --exclude-analysis-events | Ten param wyłącza pobieranie AnalysisEvents jeśli true jest przekazywana. |
| --skip-correlation-lookup | Ten param wyłącza wyszukiwanie informacji CorrelatedEvents, jeśli prawda jest przekazywana. w przeciwnym razie CorrelationEvents uzyskać przetwarzane i HasCorrelatedEvents pola w każdym FabricEvent zostanie wypełniona. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-partition-list"></a>lista partycji zdarzeń sfctl
Pobiera zdarzenia związane z partycją.

Odpowiedź jest lista PartitionEvent obiektów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --end-time-utc [Wymagane] | Godzina zakończenia kwerendy odnośnika w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --start-time-utc [Wymagane] | Godzina rozpoczęcia kwerendy odnośnej w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Jest to ciąg oddzielony przecinkami określający typy FabricEvents, które powinny być uwzględnione tylko w odpowiedzi. |
| --exclude-analysis-events | Ten param wyłącza pobieranie AnalysisEvents jeśli true jest przekazywana. |
| --skip-correlation-lookup | Ten param wyłącza wyszukiwanie informacji CorrelatedEvents, jeśli prawda jest przekazywana. w przeciwnym razie CorrelationEvents uzyskać przetwarzane i HasCorrelatedEvents pola w każdym FabricEvent zostanie wypełniona. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl zdarzenia partycji repliki listy
Pobiera zdarzenia związane z repliką partycji.

Odpowiedzią jest lista replicaevent obiektów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --end-time-utc [Wymagane] | Godzina zakończenia kwerendy odnośnika w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --replica-id [Wymagane] | Identyfikator repliki. |
| --start-time-utc [Wymagane] | Godzina rozpoczęcia kwerendy odnośnej w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Jest to ciąg oddzielony przecinkami określający typy FabricEvents, które powinny być uwzględnione tylko w odpowiedzi. |
| --exclude-analysis-events | Ten param wyłącza pobieranie AnalysisEvents jeśli true jest przekazywana. |
| --skip-correlation-lookup | Ten param wyłącza wyszukiwanie informacji CorrelatedEvents, jeśli prawda jest przekazywana. w przeciwnym razie CorrelationEvents uzyskać przetwarzane i HasCorrelatedEvents pola w każdym FabricEvent zostanie wypełniona. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-service-list"></a>lista usług zdarzeń sfctl
Pobiera zdarzenia związane z usługą.

Odpowiedź jest lista ServiceEvent obiektów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --end-time-utc [Wymagane] | Godzina zakończenia kwerendy odnośnika w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --service-id [Wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez\:schematu URI "fabric". Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa usługi\:to "fabric /myapp/app1/svc1", tożsamość\~usługi\~będzie "myapp app1 svc1" w 6.0+ i "myapp/app1/svc1" w poprzednich wersjach. |
| --start-time-utc [Wymagane] | Godzina rozpoczęcia kwerendy odnośnej w iso UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Jest to ciąg oddzielony przecinkami określający typy FabricEvents, które powinny być uwzględnione tylko w odpowiedzi. |
| --exclude-analysis-events | Ten param wyłącza pobieranie AnalysisEvents jeśli true jest przekazywana. |
| --skip-correlation-lookup | Ten param wyłącza wyszukiwanie informacji CorrelatedEvents, jeśli prawda jest przekazywana. w przeciwnym razie CorrelationEvents uzyskać przetwarzane i HasCorrelatedEvents pola w każdym FabricEvent zostanie wypełniona. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

