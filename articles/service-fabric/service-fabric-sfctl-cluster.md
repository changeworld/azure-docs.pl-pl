---
title: Interfejsu wiersza polecenia usługi Service Fabric - sfctl klaster usługi Azure | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi Service Fabric sfctl klastra.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 7bb399472d7e0ab14e6399fc8652d2eb132a866a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837332"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Wybierz, zarządzanie i działają z klastrami usługi Service Fabric.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| wersje kodu | Pobiera listę sieci szkieletowej wersje kodu, które są udostępniane w klastrze usługi Service Fabric. |
| config-versions | Pobiera listę sieci szkieletowej wersji konfiguracji, które są udostępniane w klastrze usługi Service Fabric. |
| zdrowie | Pobiera kondycji klastra usługi Service Fabric. |
| Manifest | Pobierz manifest klastra usługi Service Fabric. |
| Anuluj operację | Anuluje operację błędów wywołane przez użytkownika. |
| Lista operacji | Pobiera listę błędów wywołane przez użytkownika działań filtrowane według podane dane wejściowe. |
| aprowizacja | Aprowizuj pakiety kodu lub konfiguracji klastra usługi Service Fabric. |
| recover-system | Klaster usługi Service Fabric wskazywać, że powinien podejmować próbę odzyskiwania usług systemowych, które są aktualnie zablokowane utraciła kworum. |
| report-health | Wysyła raport o kondycji w klastrze usługi Service Fabric. |
| zaznacz | Nawiązanie połączenia z punktem końcowym klastra usługi Service Fabric. |
| show-connection | Pokaż klaster usługi Service Fabric, w którym to wystąpienie interfejsu sfctl jest połączony z. |
| Wstrzymanie obsługi administracyjnej | Wstrzymaj obsługę administracyjną pakiety kodu lub konfiguracji klastra usługi Service Fabric. |
| uaktualnij | Uruchom uaktualnianie wersji kodu lub konfiguracji klastra usługi Service Fabric. |
| Wznów uaktualnienia | Należy przejść do następnej domeny uaktualnienia uaktualniania klastra. |
| upgrade-rollback | Wycofywanie uaktualnienia klastra usługi Service Fabric. |
| upgrade-status | Pobiera postęp bieżącego uaktualniania klastra. |
| Aktualizacja dla uaktualnienie | Zaktualizuj parametry uaktualniania uaktualniania klastra usługi Service Fabric. |

## <a name="sfctl-cluster-code-versions"></a>wersje kodu sfctl klastra
Pobiera listę sieci szkieletowej wersje kodu, które są udostępniane w klastrze usługi Service Fabric.

Pobiera listę informacji o sieci szkieletowej wersje kodu, które są udostępniane w klastrze. Parametr CodeVersion można również filtrować dane wyjściowe do tej konkretnej wersji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --code-version | Wersja produktu usługi Service Fabric. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-config-versions"></a>wersje konfiguracji interfejsu sfctl klastra
Pobiera listę sieci szkieletowej wersji konfiguracji, które są udostępniane w klastrze usługi Service Fabric.

Pobiera listę informacji o sieci szkieletowej wersji konfiguracji, które są udostępniane w klastrze. Parametr ConfigVersion można również filtrować dane wyjściowe do tej konkretnej wersji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --config-version | Wersja konfiguracji usługi Service Fabric. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-health"></a>Interfejs sfctl kondycji klastra
Pobiera kondycji klastra usługi Service Fabric.

Użyj EventsHealthStateFilter do filtrowania kolekcji zdarzeń dotyczących kondycji zgłoszone w klastrze, na podstawie stanu kondycji. Podobnie, użyj NodesHealthStateFilter i ApplicationsHealthStateFilter do filtrowania kolekcji węzłów i zwrócony aplikacje oparte na ich zagregowanej kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --applications-health-state-filter | Umożliwia filtrowanie zwracane w wynikach zapytania kondycji klastra na podstawie ich stanu kondycji obiektów stan kondycji aplikacji. Możliwe wartości dla tego parametru to uzyskany od członków lub bitowe operacje na elementach członkowskich wyliczenia HealthStateFilter z liczbą całkowitą. Zwracane są tylko te aplikacje, które są zgodne z filtrem. Wszystkie aplikacje są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczanie oparte na flagi, dzięki czemu może to być kombinacją tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie stan kondycji aplikacji przy użyciu wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślną. Pasuje do dowolnego atrybutu HealthState. Ta wartość wynosi zero.  <br> -Brak — filtr, który nie jest zgodny z dowolną wartością atrybutu HealthState. Używany, aby zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok — filtrowanie, że dopasowania danych wejściowych z wartością atrybutu HealthState Ok. Wartość jest równa 2.  <br> -Warning - filtru, że wprowadzanie dopasowania z atrybutem HealthState wartość ostrzeżenie. Wartość wynosi 4.  <br> -Błąd — filtr, który pasuje do danych wejściowych z wartością atrybutu HealthState błędu. Wartość jest 8.  <br> -Al - filtr, który pasuje do danych wejściowych z dowolną wartością atrybutu HealthState. Wartość jest 65535. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości dla tego parametru to wartość całkowitą, jednego z następujących stanów kondycji. Zwracane są tylko te zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczanie oparte na flagi, dzięki czemu może to być kombinacją tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia z wartością atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślną. Pasuje do dowolnego atrybutu HealthState. Ta wartość wynosi zero.  <br> -Brak — filtr, który nie jest zgodny z dowolną wartością atrybutu HealthState. Używany, aby zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok — filtrowanie, że dopasowania danych wejściowych z wartością atrybutu HealthState Ok. Wartość jest równa 2.  <br> -Warning - filtru, że wprowadzanie dopasowania z atrybutem HealthState wartość ostrzeżenie. Wartość wynosi 4.  <br> -Błąd — filtr, który pasuje do danych wejściowych z wartością atrybutu HealthState błędu. Wartość jest 8.  <br> -Al - filtr, który pasuje do danych wejściowych z dowolną wartością atrybutu HealthState. Wartość jest 65535. |
| --exclude-health-statistics | Wskazuje, czy statystyki kondycji ma zostać zwrócone w wyniku zapytania. Wartość false, domyślnie. Statystyki pokazują liczbę elementów podrzędnych jednostek w kondycja Ok, ostrzeżenia i błędu. |
| --include-system-application-health-statistics | Określa, czy statystyki kondycji powinien zawierać sieci szkieletowej\:statystyki kondycji aplikacji/system. Wartość false, domyślnie. Jeśli właściwości IncludeSystemApplicationHealthStatistics jest ustawiona na wartość true, kondycji statystyka zawiera jednostki, które należą do sieci szkieletowej\:aplikacji/system. W przeciwnym razie wynik zapytania zawiera statystyki kondycji tylko dla aplikacji użytkownika. Statystyki kondycji muszą być zawarte w wyniku zapytania dla tego parametru, które mają być stosowane. |
| --nodes-health-state-filter | Umożliwia filtrowanie obiektów stan kondycji węzłów zwracane w wynikach zapytania kondycji klastra na podstawie ich stanu kondycji. Możliwe wartości dla tego parametru to wartość całkowitą, jednego z następujących stanów kondycji. Zwracane są tylko te węzły, które są zgodne z filtrem. Wszystkie węzły są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczanie oparte na flagi, dzięki czemu może to być kombinacją tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie stan kondycji węzłów przy użyciu wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślną. Pasuje do dowolnego atrybutu HealthState. Ta wartość wynosi zero.  <br> -Brak — filtr, który nie jest zgodny z dowolną wartością atrybutu HealthState. Używany, aby zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok — filtrowanie, że dopasowania danych wejściowych z wartością atrybutu HealthState Ok. Wartość jest równa 2.  <br> -Warning - filtru, że wprowadzanie dopasowania z atrybutem HealthState wartość ostrzeżenie. Wartość wynosi 4.  <br> -Błąd — filtr, który pasuje do danych wejściowych z wartością atrybutu HealthState błędu. Wartość jest 8.  <br> -Al - filtr, który pasuje do danych wejściowych z dowolną wartością atrybutu HealthState. Wartość jest 65535. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-manifest"></a>Interfejs sfctl manifestu klastra
Pobierz manifest klastra usługi Service Fabric.

Pobierz manifest klastra usługi Service Fabric. Manifest klastra zawiera właściwości klastra, które obejmują różnych typów węzłów klastra, konfiguracji zabezpieczeń, błędów i topologii domeny uaktualnień, itp. Te właściwości są określone jako część pliku ClusterConfig.JSON podczas wdrażania autonomicznego klastra. Jednak większość informacji w manifeście klastra jest generowany wewnętrznie przez usługę service fabric podczas wdrażania klastra w innych scenariuszach wdrożenia (np. przy użyciu witryny Azure portal). Zawartość manifestu klastra, jest tylko do celów informacyjnych i użytkownicy nie powinny się skorzystać z zależności od formatu zawartość pliku lub jego interpretacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-operation-cancel"></a>Interfejs sfctl klastra operacji anulowania
Anuluje operację błędów wywołane przez użytkownika.

Następujące interfejsy API uruchomić operacji błędów, które mogą zostać anulowane, za pomocą CancelOperation\: StartDataLoss StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Jeśli wymuszone ma wartość false, następnie określonej operacji usługi wywołane przez użytkownika zostanie bez problemu zmieniała zatrzymany i wyczyszczone.  Jeśli życie ma wartość true, polecenia zostaną przerwane i mogą pozostać pewne stanu wewnętrznego.  Określanie życie jako wartość true, należy używać ostrożnie. Wywołanie tego interfejsu API przy wymuszonego ma wartość true nie jest dozwolone, dopóki tego interfejsu API została już wywołana na tym samym poleceniu życie równa false pierwszego testu lub jeśli tego polecenia nie ma jeszcze OperationState OperationState.RollingBack. 

Wyjaśnienie\: OperationState.RollingBack oznacza, że system będzie/czyści systemu wewnętrznego stanu spowodowanych wykonaniem polecenia.  Go nie spowoduje przywrócenia danych Jeśli polecenie testu zakończyło się do utraty danych.  Na przykład jeśli wywołanie StartDataLoss wywołać ten interfejs API, system będzie wyczyścić tylko stan wewnętrzny z uruchomieniem polecenia. Go nie spowoduje przywrócenia danych na partycji docelowej, jeśli polecenie wykazała postępów w stopniu do utraty danych. 

> [!NOTE]
> Jeśli ten interfejs API jest wywoływany przy wymuszonego == true, wewnętrzny stan mogą pozostać.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator operacji [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Te informacje są przekazywane do odpowiedniego interfejsu API GetProgress. |
| --force | Wskazuje, czy można bezpiecznie wycofać i wyczyścić stanu systemu wewnętrznego zmodyfikowane przez wykonywanie operacji wywołane przez użytkownika. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-operation-list"></a>Lista operacji interfejsu sfctl klastra
Pobiera listę błędów wywołane przez użytkownika działań filtrowane według podane dane wejściowe.

Pobiera listę operacji wywołane przez użytkownika błędów filtrowane według podane dane wejściowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --state-filter | Używane do filtrowania firmy OperationState operacji wywołane przez użytkownika. <br> 65535 — Zaznacz wszystko <br> 1 — Wybieranie uruchomione <br> 2 — Wybierz wycofywanie <br>8 - wybierz ukończone <br>16 - wybierz Faulted <br>32 - wybierz odwołania <br>64 - wybierz ForceCancelled.  <br>Domyślne\: 65535. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |
| --type-filter | Używane do filtrowania na typ operacji dla operacji wywołane przez użytkownika. <br> 65535 — Zaznacz wszystko <br> 1 — Wybierz PartitionDataLoss. <br> 2 — Wybierz PartitionQuorumLoss. <br> 4 — Wybierz PartitionRestart. <br> 8 - wybierz NodeTransition.  <br> Domyślne\: 65535. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-provision"></a>aprowizowanie klastra sfctl
Aprowizuj pakiety kodu lub konfiguracji klastra usługi Service Fabric.

Sprawdzanie poprawności i aprowizuj pakiety kodu lub konfiguracji klastra usługi Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --cluster-manifest-file-path | Ścieżka do pliku manifestu klastra. |
| --code-file-path | Ścieżka pliku pakietu kodu klastra. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-recover-system"></a>Interfejs sfctl klastra odzyskiwanie systemu
Klaster usługi Service Fabric wskazywać, że powinien podejmować próbę odzyskiwania usług systemowych, które są aktualnie zablokowane utraciła kworum.

Klaster usługi Service Fabric wskazywać, że powinien podejmować próbę odzyskiwania usług systemowych, które są aktualnie zablokowane utraciła kworum. Ta operacja powinna można wykonać tylko, jeśli jest znany, nie będzie można odzyskać replik, które nie działają. Niepoprawne użycie tego interfejsu API może spowodować ryzyko utraty danych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-report-health"></a>Interfejs sfctl klastra raportów kondycji
Wysyła raport o kondycji w klastrze usługi Service Fabric.

Raport musi zawierać informacje o źródle raport o kondycji i właściwości, na którym jest zgłaszany. Raport jest wysyłany do węzła bramy usługi Service Fabric, która przekazuje w magazynie kondycji. Raport może być akceptowane przez bramę, ale odrzucony przez magazynu kondycji po dodatkową walidację. Na przykład magazynu kondycji może odrzucić raport ze względu na nieprawidłowy parametr, takich jak numer sekwencji starych. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, sprawdź, czy raport jest wyświetlany w HealthEvents klastra.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --kondycji — właściwość [wymagane] | Właściwość o kondycji. <br><br> Jednostka może mieć raportów o kondycji dla różnych właściwości. Właściwość jest ciągu i stałych wyliczenia aby zezwalał na elastyczność reportera do kategoryzowania warunek stanu, która powoduje uruchomienie raportu. Na przykład reportera o ID "LocalWatchdog" można monitorować stan wolnego w węźle, aby go zgłosić właściwości "AvailableDisk" w tym węźle. Ten sam reportera monitorować łączność węzeł tak go zgłosić właściwości "Łączność" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako zdarzenia dotyczące kondycji oddzielnych dla określonego węzła. Wraz z SourceId właściwość jednoznacznie identyfikuje informacje o kondycji. |
| --health-state    [Required] | Możliwe wartości to\: "Nieprawidłowy", "Ok", "Ostrzeżenie", "Error", "Nieznany". |
| --source-id       [Required] | Nazwa źródła, która identyfikuje składnik klienta/strażnika/systemu, który wygenerował informacji o kondycji. |
| --description | Opis informacji o kondycji. <br><br> Reprezentuje dowolny tekst, które umożliwiają dodawanie ludzi do odczytu informacji na temat raportu. Maksymalna długość ciągu opisu wynosi 4096 znaków. Jeśli podany ciąg jest dłuższy, zostaną automatycznie obcięte. W przypadku obcięty, ostatnie znaki opis zawiera znacznik "[obcięte]", a ciąg łączny rozmiar wynosi 4096 znaków. Obecność znacznika wskazuje, aby użytkownicy tej obcięcie wystąpił. Należy pamiętać, że gdy obcięty, opis ma mniej niż 4096 znaków z oryginalnego ciągu. |
| --immediate | Flaga, która wskazuje, czy raport powinna zostać wysłana natychmiast. <br><br> Raport o kondycji są wysyłane do aplikacji, która przekazuje w magazynie kondycji bramy usługi Service Fabric. Jeśli bezpośrednie jest ustawiona na wartość true, raport jest wysyłany bezpośrednio z bramy protokołu HTTP w magazynie kondycji niezależnie od ustawień klienta sieci szkieletowej, które używa aplikacji bramy protokołu HTTP. Jest to przydatne dla krytycznych raportów, które mają być wysyłane tak szybko, jak to możliwe. W zależności od czasu i innych warunków wysłaniem raportu może nadal się nie powieść, na przykład jeśli bramy HTTP został zamknięty lub komunikat nie dociera do bramy. Jeśli bezpośrednie jest ustawiona na wartość false, raport jest wysyłana na podstawie ustawień klienta kondycji z bramy protokołu HTTP. W związku z tym będzie partii zgodnie z konfiguracją HealthReportSendInterval. Jest to zalecane ustawienie ponieważ zezwala ona na kondycji klienta do optymalizacji raportowania komunikatów w magazynie danych kondycji, a także przetwarzania raportu kondycji kondycji. Domyślnie raporty nie są wysyłane bezpośrednio. |
| --remove-when-expired | Wartość wskazująca, czy raport jest usuwany z magazynu kondycji po jego wygaśnięciu. <br><br> Jeśli ustawiono wartość true, raport zostanie usunięty z magazynu kondycji po jego wygaśnięciu. Jeśli ma wartość false, raport jest traktowana jako błąd po upływie. Wartość tej właściwości to false domyślnie. Gdy klienci okresowo raportu ustala RemoveWhenExpired false (domyślnie). W ten sposób jest zgłaszającą ma problemy (np. zakleszczenia) i nie można zgłosić jednostki jest oceniany na błąd, po wygaśnięciu raport o kondycji. Oznacza flagą jednostki jako błąd stanu kondycji. |
| --sequence-number | Numer sekwencji dla tego raportu o kondycji jako ciągu numerycznego. <br><br> Numer sekwencyjny raportu służy magazynu kondycji do wykrywania stare raportów. Jeśli nie zostanie określony, numer sekwencyjny został wygenerowany automatycznie przez klienta usługi kondycji po dodaniu raportu. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |
| --ttl | Czas trwania, dla których raport o kondycji jest nieprawidłowy. To pole używa formatu ISO8601 do określania czasu trwania. <br><br> Gdy klienci okresowo raport powinien wysyłać raporty, z częstotliwością wyższe niż czas wygaśnięcia. Jeśli klienci raportować przejścia, ich ustawić czas wygaśnięcia na nieograniczoną. Po wygaśnięciu czasu wygaśnięcia zdarzenie kondycji, który zawiera informacje o kondycji jest usunięte z magazynu kondycji, jeśli jest RemoveWhenExpired wartość true, lub oceniona błąd, jeśli RemoveWhenExpired wartość false. Jeśli nie zostanie określony, czas wygaśnięcia wartością domyślną jest wartość nieskończona. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-select"></a>Wybierz klaster sfctl
Nawiązanie połączenia z punktem końcowym klastra usługi Service Fabric.

Jeśli połączenie z zabezpieczonym klastrem, należy określić ścieżkę bezwzględną do certyfikatu (CRT) i plik klucza (Key) lub pojedynczy plik z obu (PEM). Nie określaj obu. Opcjonalnie w przypadku nawiązywania połączenia z zabezpieczonym klastrem, także określić ścieżkę bezwzględną do pliku pakietu urzędu certyfikacji lub katalogu zaufane certyfikaty urzędu certyfikacji. Jeśli przy użyciu katalogu certyfikaty urzędu certyfikacji `c_rehash <directory>` dostarczone przez OpenSSL muszą być uruchamiane najpierw do obliczenia wartości skrótu certyfikatu i Utwórz odpowiednie linki symboliczne.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — punkt końcowy [wymagane] | Klastra adresu URL punktu końcowego, łącznie z portu i prefiksu protokołu HTTP lub HTTPS. |
| --aad | Usługa Azure Active Directory do uwierzytelniania. |
| --ca | Ścieżka bezwzględna do katalogu certyfikatów urzędu certyfikacji można traktować jako prawidłowa lub pliku pakietu urzędu certyfikacji. |
| --cert | Ścieżka bezwzględna do pliku certyfikatu klienta. |
| --key | Ścieżka bezwzględna do pliku klucza certyfikatu klienta. |
| --nie Sprawdź | Wyłączyć weryfikację certyfikatów przy użyciu protokołu HTTPS, należy pamiętać,\: jest niebezpieczne opcję i nie powinny być używane w środowiskach produkcyjnych. |
| --pem | Ścieżka bezwzględna do certyfikatu klienta w formacie PEM. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-show-connection"></a>Interfejs sfctl klastra show-connection
Pokaż klaster usługi Service Fabric, w którym to wystąpienie interfejsu sfctl jest połączony z.

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-unprovision"></a>Wstrzymanie obsługi administracyjnej sfctl klastra
Wstrzymaj obsługę administracyjną pakiety kodu lub konfiguracji klastra usługi Service Fabric.

Jest ona obsługiwana wstrzymał obsługi administracyjnej kodem i konfiguracją oddzielnie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --code-version | Wersja pakietu kodu klastra. |
| --config-version | Wersja manifestu klastra. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-upgrade"></a>Interfejs sfctl uaktualniania klastra
Uruchom uaktualnianie wersji kodu lub konfiguracji klastra usługi Service Fabric.

Sprawdź podane parametry uaktualniania i rozpocząć uaktualnianie klastra usługi Service Fabric w wersji kodu lub konfiguracji, jeśli parametry są prawidłowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --app-health-map | Zakodowane w formacie JSON. Słownik par nazwa aplikacji i maksymalnej wartości procentowej złej kondycji przed zgłoszeniem błędu. |
| — — Typ — kondycji — Mapa aplikacji | Zakodowane w formacie JSON. Słownik par nazwa typu aplikacji i maksymalnej wartości procentowej złej kondycji przed zgłoszeniem błędu. |
| --code-version | Wersja kodu klastra. |
| --config-version | Wersja konfiguracji klastra. |
| --delta-health-evaluation | Umożliwia ocenę kondycji delta, a nie oceny kondycji bezwzględne po zakończeniu każdej z domen. |
| --delta-unhealthy-nodes | Maksymalne dozwolone procent węzłów degradacji kondycji dozwolone podczas uaktualniania klastra.  Domyślne\: 10. <br><br> Delta jest mierzony między stan węzłów na początku uaktualnienia, a stan węzłów w czasie oceny kondycji. Sprawdzanie jest wykonywane po co domena uaktualnienia zakończenia uaktualnienia, aby upewnić się, że globalnego stanu klastra znajduje się w granicach tolerowana. |
| — Akcja błędu | Możliwe wartości to\: "Nieprawidłowy", "Wycofać", "Manual". |
| --force-restart | Procesy wymuszone zostaną ponownie uruchomione podczas uaktualniania, nawet wtedy, gdy wersja kodu nie została zmieniona. <br><br> Uaktualnianie tylko zmiany konfiguracji lub danych. |
| --health-check-retry | Odstęp czasu między próbami sprawdzać kondycję, jeśli aplikacja lub klastra nie jest w dobrej kondycji. |
| --health-check-stable | Ilość czasu, aplikacji lub klastra musi pozostać dobrej kondycji przed uaktualnienia przechodzi do następnej domeny uaktualnienia. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. |
| --health-check-wait | Czas oczekiwania po wykonaniu uaktualnienia domeny przed rozpoczęciem kondycji sprawdza, czy proces. |
| --replica-set-check-timeout | Maksymalna ilość czasu blokowania przetwarzania domeny uaktualnienia, a przed utratą dostępności w przypadku nieoczekiwanych problemów. <br><br> Po wygaśnięciu tego limitu czasu przetwarzania przez domenę uaktualnienia będzie kontynuowana bez względu na to, problemy z dostępnością utraty. Limit czasu jest resetowany na początku każdej z domen. Prawidłowe wartości należą do zakresu od 0 i 42949672925 (włącznie). |
| --rolling-upgrade-mode | Możliwe wartości to\: "Nieprawidłowy", "UnmonitoredAuto", "UnmonitoredManual", "Monitorowania".  Domyślne\: UnmonitoredAuto. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |
| --unhealthy-applications | Maksymalna dozwolona wartość procentowa aplikacje w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład aby zezwolić na 10% aplikacje będącą w złej kondycji, wartość ta wynosi 10. Wartość procentowa reprezentuje maksymalny procent tolerowana aplikacje, które mogą być złej kondycji, zanim klaster zostanie uznane za błąd. Jeśli wartość procentowa jest zachowana, ale istnieje co najmniej jednej aplikacji w złej kondycji, kondycji jest oceniane jako ostrzeżenie. To jest obliczana na podstawie dzielona całkowitej liczby wystąpień aplikacji w klastrze, z wyjątkiem aplikacji typów aplikacji, które są objęte ApplicationTypeHealthPolicyMap aplikacje w złej kondycji. Obliczenia zaokrągla w górę do tolerowanie awarii jednego w małej liczby aplikacji. |
| --węzłów w złej kondycji — | Maksymalna dozwolona wartość procentowa węzłów w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład aby zezwolić na 10% węzłów będącą w złej kondycji, wartość ta wynosi 10. Wartość procentowa reprezentuje tolerowana maksymalny procent węzłów, które mogą być złej kondycji, zanim klaster zostanie uznane za błąd. Jeśli wartość procentowa jest zachowana, ale istnieje co najmniej jeden węzeł w złej kondycji, kondycji jest oceniane jako ostrzeżenie. Wartość procentowa jest obliczana przez podzielenie liczby węzłów w złej kondycji przez całkowitą liczbę węzłów w klastrze. Obliczenia zaokrągla w górę do tolerowanie awarii jednego w małej liczby węzłów. W dużych klastrach niektóre węzły zawsze będzie wciśnięty lub na zewnątrz do naprawy, dlatego ta wartość procentowa, należy skonfigurować tolerowana liczba, która. |
| --upgrade-domain-delta-unhealthy-nodes | Maksymalna dozwolona wartość procentowa węzły domeny uaktualnień degradacji kondycji dozwolone podczas uaktualniania klastra.  Domyślne\: 15. <br><br> Delta jest mierzony od stanu węzły domeny uaktualnień na początku uaktualnienia i stan węzły domeny uaktualnień w czasie oceny kondycji. Sprawdzanie jest wykonywane po zakończeniu każdej domeny uaktualnień ukończenie uaktualniania dla wszystkich domen uaktualnienia, aby upewnić się, że stan domen uaktualnienia jest wyświetlany w granicach tolerowana. |
| --upgrade-domain-timeout | Czas każdej z domen musi wykonać, zanim zostanie wykonany FailureAction. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. |
| — limit czasu uaktualniania | Czas ogólną uaktualnienia musi wykonać, zanim zostanie wykonany FailureAction. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. |
| --warning-as-error | Wskazuje, czy ostrzeżenia są traktowane z tego samego ważność jako błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-upgrade-resume"></a>Interfejs sfctl klastra uaktualnienie resume
Należy przejść do następnej domeny uaktualnienia uaktualniania klastra.

Podejmij działanie klastra kodu lub konfiguracji uaktualnień na do następnej domeny uaktualnienia Jeśli to stosowne.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — domeny uaktualnienia [wymagane] | Następnej domeny uaktualnienia dla uaktualnienie klastra. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-upgrade-rollback"></a>Interfejs sfctl klastra uaktualnienie — wycofywanie
Wycofywanie uaktualnienia klastra usługi Service Fabric.

Wycofać uaktualnienie kodu lub konfiguracji klastra usługi Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-upgrade-status"></a>Stan uaktualnienia sfctl klastra
Pobiera postęp bieżącego uaktualniania klastra.

Pobiera bieżący postęp uaktualniania bieżące klastra. Jeśli uaktualnienie nie jest obecnie w toku, skorzystaj z ostatniego stanu poprzedniego uaktualnienia klastra.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-cluster-upgrade-update"></a>Uaktualnianie klastra sfctl aktualizacji
Zaktualizuj parametry uaktualniania uaktualniania klastra usługi Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --app-health-map | Zakodowane w formacie JSON. Słownik par nazwa aplikacji i maksymalnej wartości procentowej złej kondycji przed zgłoszeniem błędu. |
| — — Typ — kondycji — Mapa aplikacji | Zakodowane w formacie JSON. Słownik par nazwa typu aplikacji i maksymalnej wartości procentowej złej kondycji przed zgłoszeniem błędu. |
| --delta-health-evaluation | Umożliwia ocenę kondycji delta, a nie oceny kondycji bezwzględne po zakończeniu każdej z domen. |
| --delta-unhealthy-nodes | Maksymalne dozwolone procent węzłów degradacji kondycji dozwolone podczas uaktualniania klastra.  Domyślne\: 10. <br><br> Delta jest mierzony między stan węzłów na początku uaktualnienia, a stan węzłów w czasie oceny kondycji. Sprawdzanie jest wykonywane po co domena uaktualnienia zakończenia uaktualnienia, aby upewnić się, że globalnego stanu klastra znajduje się w granicach tolerowana. |
| — Akcja błędu | Możliwe wartości to\: "Nieprawidłowy", "Wycofać", "Manual". |
| --force-restart | Procesy wymuszone zostaną ponownie uruchomione podczas uaktualniania, nawet wtedy, gdy wersja kodu nie została zmieniona. <br><br> Uaktualnianie tylko zmiany konfiguracji lub danych. |
| --health-check-retry | Odstęp czasu między próbami sprawdzać kondycję, jeśli aplikacja lub klastra nie jest w dobrej kondycji. |
| --health-check-stable | Ilość czasu, aplikacji lub klastra musi pozostać dobrej kondycji przed uaktualnienia przechodzi do następnej domeny uaktualnienia. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. |
| --health-check-wait | Czas oczekiwania po wykonaniu uaktualnienia domeny przed rozpoczęciem kondycji sprawdza, czy proces. |
| --replica-set-check-timeout | Maksymalna ilość czasu blokowania przetwarzania domeny uaktualnienia, a przed utratą dostępności w przypadku nieoczekiwanych problemów. <br><br> Po wygaśnięciu tego limitu czasu przetwarzania przez domenę uaktualnienia będzie kontynuowana bez względu na to, problemy z dostępnością utraty. Limit czasu jest resetowany na początku każdej z domen. Prawidłowe wartości należą do zakresu od 0 i 42949672925 (włącznie). |
| --rolling-upgrade-mode | Możliwe wartości to\: "Nieprawidłowy", "UnmonitoredAuto", "UnmonitoredManual", "Monitorowania".  Domyślne\: UnmonitoredAuto. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |
| --unhealthy-applications | Maksymalna dozwolona wartość procentowa aplikacje w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład aby zezwolić na 10% aplikacje będącą w złej kondycji, wartość ta wynosi 10. Wartość procentowa reprezentuje maksymalny procent tolerowana aplikacje, które mogą być złej kondycji, zanim klaster zostanie uznane za błąd. Jeśli wartość procentowa jest zachowana, ale istnieje co najmniej jednej aplikacji w złej kondycji, kondycji jest oceniane jako ostrzeżenie. To jest obliczana na podstawie dzielona całkowitej liczby wystąpień aplikacji w klastrze, z wyjątkiem aplikacji typów aplikacji, które są objęte ApplicationTypeHealthPolicyMap aplikacje w złej kondycji. Obliczenia zaokrągla w górę do tolerowanie awarii jednego w małej liczby aplikacji. |
| --węzłów w złej kondycji — | Maksymalna dozwolona wartość procentowa węzłów w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład aby zezwolić na 10% węzłów będącą w złej kondycji, wartość ta wynosi 10. Wartość procentowa reprezentuje tolerowana maksymalny procent węzłów, które mogą być złej kondycji, zanim klaster zostanie uznane za błąd. Jeśli wartość procentowa jest zachowana, ale istnieje co najmniej jeden węzeł w złej kondycji, kondycji jest oceniane jako ostrzeżenie. Wartość procentowa jest obliczana przez podzielenie liczby węzłów w złej kondycji przez całkowitą liczbę węzłów w klastrze. Obliczenia zaokrągla w górę do tolerowanie awarii jednego w małej liczby węzłów. W dużych klastrach niektóre węzły zawsze będzie wciśnięty lub na zewnątrz do naprawy, dlatego ta wartość procentowa, należy skonfigurować tolerowana liczba, która. |
| --upgrade-domain-delta-unhealthy-nodes | Maksymalna dozwolona wartość procentowa węzły domeny uaktualnień degradacji kondycji dozwolone podczas uaktualniania klastra.  Domyślne\: 15. <br><br> Delta jest mierzony od stanu węzły domeny uaktualnień na początku uaktualnienia i stan węzły domeny uaktualnień w czasie oceny kondycji. Sprawdzanie jest wykonywane po zakończeniu każdej domeny uaktualnień ukończenie uaktualniania dla wszystkich domen uaktualnienia, aby upewnić się, że stan domen uaktualnienia jest wyświetlany w granicach tolerowana. |
| --upgrade-domain-timeout | Czas każdej z domen musi wykonać, zanim zostanie wykonany FailureAction. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. |
| --upgrade-kind | Możliwe wartości to\: "Nieprawidłowy", stopniowe, "Rolling_ForceRestart".  Domyślne\: stopniowe. |
| — limit czasu uaktualniania | Czas ogólną uaktualnienia musi wykonać, zanim zostanie wykonany FailureAction. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. |
| --warning-as-error | Wskazuje, czy ostrzeżenia są traktowane z tego samego ważność jako błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |


## <a name="next-steps"></a>Kolejne kroki
- [Instalator](service-fabric-cli.md) usługi Service Fabric CLI.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi Service Fabric [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).