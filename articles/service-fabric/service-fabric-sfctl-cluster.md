---
title: Klaster interfejsu wiersza polecenia sieci szkieletowej usługi Azure
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń do zarządzania klastrami.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 007ad6f59f0ce304db579f4faa1bb95611a93a37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906156"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Wybierz klastry sieci szkieletowej usług, zarządzaj nimi i obsługa ich.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| wersje kodu | Pobiera listę wersji kodu sieci szkieletowej, które są aprowizacji w klastrze sieci szkieletowej usług. |
| wersje konfiguracyjne | Pobiera listę wersji konfigurau sieci szkieletowej, które są aprowizacji w klastrze sieci szkieletowej usług. |
| Zdrowia | Pobiera kondycję klastra sieci szkieletowej usług. |
| manifest | Pobierz manifest klastra sieci szkieletowej usług. |
| operacja-anulowanie | Anuluje operację błędu wywołaną przez użytkownika. |
| lista operacji | Pobiera listę operacji błędów wywołanych przez użytkownika filtrowane przez dostarczone dane wejściowe. |
| Przepis | Aprowizuj pakiety kodu lub konfiguracji klastra sieci szkieletowej usług. |
| system odzyskiwania | Wskazuje klastrowi sieci szkieletowej usług, że należy podjąć próbę odzyskania usług systemowych, które obecnie utknęły w utracie kworum. |
| raport-zdrowie | Wysyła raport kondycji w klastrze sieci szkieletowej usług. |
| Wybierz | Łączy się z punktem końcowym klastra sieci szkieletowej usług. |
| show-połączenie | Pokaż, do którego klastra sieci szkieletowej usług jest połączone to wystąpienie sfctl. |
| unprovision | Anulowanie obsługi administracyjnej pakietów kodu lub konfiguracji klastra sieci szkieletowej usług. |
| uaktualnienie | Rozpocznij uaktualnianie kodu lub wersji konfiguracji klastra sieci szkieletowej usług. |
| uaktualnienie-życiorys | Aby uaktualnienie klastra przejść do następnej domeny uaktualnienia. |
| upgrade-rollback | Wycofaj uaktualnienie klastra sieci szkieletowej usług. |
| stan uaktualnienia | Pobiera postęp bieżącego uaktualnienia klastra. |
| aktualizacja uaktualnienia | Aktualizowanie parametrów uaktualniania uaktualnienia klastra sieci szkieletowej usług. |

## <a name="sfctl-cluster-code-versions"></a>wersje kodowe klastra sfctl
Pobiera listę wersji kodu sieci szkieletowej, które są aprowizacji w klastrze sieci szkieletowej usług.

Pobiera listę informacji o wersjach kodu sieci szkieletowej, które są aprowizacji w klastrze. Parametr CodeVersion może służyć do opcjonalnego filtrowania danych wyjściowych tylko do tej konkretnej wersji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --code-version | Wersja produktu sieci szkieletowej usług. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-config-versions"></a>sfctl klastra config-versions
Pobiera listę wersji konfigurau sieci szkieletowej, które są aprowizacji w klastrze sieci szkieletowej usług.

Pobiera listę informacji o wersjach konfigurau sieci szkieletowej, które są aprowizacji w klastrze. Parametr ConfigVersion może służyć do opcjonalnego filtrowania danych wyjściowych tylko do tej konkretnej wersji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --config-wersja | Wersja konfiguracjowa sieci szkieletowej usług. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-health"></a>sfctl kondycja klastra
Pobiera kondycję klastra sieci szkieletowej usług.

Za pomocą EventsHealthStateFilter do filtrowania kolekcji zdarzeń kondycji zgłoszonych w klastrze na podstawie stanu kondycji. Podobnie użyj NodesHealthStateFilter i ApplicationsHealthStateFilter do filtrowania kolekcji węzłów i aplikacji zwróconych na podstawie ich zagregowanego stanu kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --applications-health-state-filter | Umożliwia filtrowanie obiektów stanu kondycji aplikacji zwracanych w wyniku kwerendy kondycji klastra na podstawie ich stanu kondycji. Możliwe wartości dla tego parametru obejmują wartość całkowitą uzyskaną z elementów członkowskich lub operacje bitowe na członkach wyliczenia HealthStateFilter. Zwracane są tylko aplikacje, które pasują do filtru. Wszystkie aplikacje są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, wszystkie wpisy są zwracane. Wartości stanu są wyliczenia oparte na flagi, więc wartość może być kombinacją tych wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład jeśli podana wartość wynosi 6, zwracany jest stan kondycji aplikacji o wartości HealthState OK (2) i Warning (4).  <br> - Domyślna — wartość domyślna. Pasuje do dowolnego HealthState. Wartość wynosi zero.  <br> - Brak — filtr, który nie pasuje do żadnej wartości HealthState. Używane w celu zwrócenia żadnych wyników w danej kolekcji stanów. Wartość wynosi 1.  <br> - Ok - Filtr, który pasuje do danych wejściowych z HealthState wartość Ok. Wartość wynosi 2.  <br> - Ostrzeżenie — filtr, który pasuje do danych wejściowych z HealthState wartość Ostrzeżenie. Wartość wynosi 4.  <br> - Błąd — filtr, który pasuje do danych wejściowych z HealthState wartość Error. Wartość wynosi 8.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej wartości HealthState. Wartość to 65535. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji HealthEvent obiektów zwracanych na podstawie stanu kondycji. Możliwe wartości dla tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które pasują do filtru. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, wszystkie wpisy są zwracane. Wartości stanu są wyliczenia oparte na flagi, więc wartość może być kombinacją tych wartości, uzyskane przy użyciu bitowego "OR" operatora. Na przykład Jeśli podana wartość wynosi 6, zwracane są wszystkie zdarzenia o wartości HealthState OK (2) i Warning (4).  <br> - Domyślna — wartość domyślna. Pasuje do dowolnego HealthState. Wartość wynosi zero.  <br> - Brak — filtr, który nie pasuje do żadnej wartości HealthState. Używane w celu zwrócenia żadnych wyników w danej kolekcji stanów. Wartość wynosi 1.  <br> - Ok - Filtr, który pasuje do danych wejściowych z HealthState wartość Ok. Wartość wynosi 2.  <br> - Ostrzeżenie — filtr, który pasuje do danych wejściowych z HealthState wartość Ostrzeżenie. Wartość wynosi 4.  <br> - Błąd — filtr, który pasuje do danych wejściowych z HealthState wartość Error. Wartość wynosi 8.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej wartości HealthState. Wartość to 65535. |
| --exclude-health-statistics | Wskazuje, czy statystyki kondycji powinny być zwracane jako część wyniku kwerendy. False domyślnie. Statystyki pokazują liczbę jednostek podrzędnych w stanie kondycji Ok, Ostrzeżenie i Błąd. |
| --include-system-application-health-statistics | Wskazuje, czy statystyki kondycji powinny\:zawierać statystyki kondycji /System aplikacji. False domyślnie. Jeśli IncludeSystemApplicationHealthStatistics jest ustawiona na true, statystyki kondycji obejmują\:jednostki, które należą do sieci szkieletowej /System aplikacji. W przeciwnym razie wynik kwerendy zawiera statystyki kondycji tylko dla aplikacji użytkownika. Statystyki kondycji muszą być uwzględnione w wyniku kwerendy dla tego parametru, który ma zostać zastosowany. |
| --nodes-health-state-filter | Umożliwia filtrowanie obiektów stanu kondycji węzła zwracanych w wyniku kwerendy kondycji klastra na podstawie ich stanu kondycji. Możliwe wartości dla tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko węzły, które pasują do filtru. Wszystkie węzły są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, wszystkie wpisy są zwracane. Wartości stanu są wyliczenia oparte na flagi, więc wartość może być kombinacją tych wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład jeśli podana wartość wynosi 6, zwracany jest stan kondycji węzłów o wartości HealthState OK (2) i Warning (4).  <br> - Domyślna — wartość domyślna. Pasuje do dowolnego HealthState. Wartość wynosi zero.  <br> - Brak — filtr, który nie pasuje do żadnej wartości HealthState. Używane w celu zwrócenia żadnych wyników w danej kolekcji stanów. Wartość wynosi 1.  <br> - Ok - Filtr, który pasuje do danych wejściowych z HealthState wartość Ok. Wartość wynosi 2.  <br> - Ostrzeżenie — filtr, który pasuje do danych wejściowych z HealthState wartość Ostrzeżenie. Wartość wynosi 4.  <br> - Błąd — filtr, który pasuje do danych wejściowych z HealthState wartość Error. Wartość wynosi 8.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej wartości HealthState. Wartość to 65535. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-manifest"></a>manifest klastra sfctl
Pobierz manifest klastra sieci szkieletowej usług.

Pobierz manifest klastra sieci szkieletowej usług. Manifest klastra zawiera właściwości klastra, które zawierają różne typy węzłów w klastrze, konfiguracje zabezpieczeń, usterkę i topologie domeny uaktualnienia itp. Właściwości te są określone jako część pliku ClusterConfig.JSON podczas wdrażania autonomicznego klastra. Jednak większość informacji w manifeście klastra jest generowana wewnętrznie przez sieci szkieletowej usług podczas wdrażania klastra w innych scenariuszach wdrażania (np. podczas korzystania z witryny Azure portal). Zawartość manifestu klastra są wyłącznie w celach informacyjnych i użytkownicy nie oczekuje się, aby podjąć zależność od formatu zawartości pliku lub jego interpretacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-operation-cancel"></a>Sfctl cluster operation-cancel sfctl cluster operation-cancel sfctl
Anuluje operację błędu wywołaną przez użytkownika.

Następujące interfejsy API rozpoczynają operacje błędów, które mogą\: zostać anulowane przy użyciu canceloperation StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Jeśli siła jest false, a następnie określonej operacji wywołanej przez użytkownika zostanie bezpiecznie zatrzymany i oczyszczone.  Jeśli siła jest prawdziwa, polecenie zostanie przerwane, a niektóre stan wewnętrzny mogą pozostać w tyle.  Określenie siły jako true należy używać ostrożnie. Wywołanie tego interfejsu API z siłą ustawioną na true nie jest dozwolone, dopóki ten interfejs API nie został już wywołany przez to samo polecenie testowe z siłą ustawioną na false lub jeśli polecenie testowe ma już operationstate operationstate.RollingBack. Wyjaśnienie\: OperationState.RollingBack oznacza, że system będzie/czyści wewnętrzny stan systemu spowodowany wykonaniem polecenia.  Nie przywróci danych, jeśli polecenie testowe miało spowodować utratę danych.  Na przykład jeśli wywołasz StartDataLoss następnie wywołać ten interfejs API, system będzie tylko oczyścić stan wewnętrzny z uruchamiania polecenia. Nie przywróci danych partycji docelowej, jeśli polecenie postępuje wystarczająco daleko, aby spowodować utratę danych. Ważne\: uwaga, jeśli ten interfejs API jest wywoływany z force==true, stan wewnętrzny może pozostać w tyle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --operation-id [Wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  Jest to przekazywane do odpowiedniego interfejsu API GetProgress. |
| --siła | Wskazuje, czy bezpiecznie wycofać i oczyścić wewnętrzny stan systemu zmodyfikowany przez wykonanie operacji wywołanej przez użytkownika. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-operation-list"></a>lista operacji klastra sfctl
Pobiera listę operacji błędów wywołanych przez użytkownika filtrowane przez dostarczone dane wejściowe.

Pobiera listę operacji błędów wywołanych przez użytkownika filtrowane przez dostarczone dane wejściowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --state-filter | Służy do filtrowania operacji OperationState dla operacji wywołanych przez użytkownika. - 65535 - wybierz Wszystkie - 1 - wybierz Running - 2 - wybierz RollingBack - 8 - wybierz Zakończone - 16 - wybierz Faulted - 32 - wybierz Anulowane - 64 - wybierz ForceCancelled.  Wartość\: domyślna 65535. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |
| --type-filter | Służy do filtrowania na OperationType dla operacji wywołanych przez użytkownika. - 65535 - wybierz wszystkie - 1 - wybierz PartitionDataLoss. - 2 - wybierz PartitionQuorumLoss. - 4 - wybierz PartitionRestart. - 8 - wybierz NodeTransition.  Wartość\: domyślna 65535. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-provision"></a>udostępnianie klastra sfctl
Aprowizuj pakiety kodu lub konfiguracji klastra sieci szkieletowej usług.

Sprawdzanie poprawności i aprowizowanie pakietów kodu lub konfiguracji klastra sieci szkieletowej usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --cluster-manifest-file-path | Ścieżka pliku manifestu klastra. |
| --code-file-path | Ścieżka pliku pakietu kodu klastra. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-recover-system"></a>system odzyskiwania klastra sfctl
Wskazuje klastrowi sieci szkieletowej usług, że należy podjąć próbę odzyskania usług systemowych, które obecnie utknęły w utracie kworum.

Wskazuje klastrowi sieci szkieletowej usług, że należy podjąć próbę odzyskania usług systemowych, które obecnie utknęły w utracie kworum. Ta operacja powinna być wykonywana tylko wtedy, gdy wiadomo, że repliki, które są w dół nie można odzyskać. Nieprawidłowe użycie tego interfejsu API może spowodować potencjalną utratę danych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-report-health"></a>sfctl cluster report-health
Wysyła raport kondycji w klastrze sieci szkieletowej usług.

Wysyła raport kondycji w klastrze sieci szkieletowej usług. Raport musi zawierać informacje o źródle raportu kondycji i właściwości, na których jest raport. Raport jest wysyłany do węzła bramy sieci szkieletowej usług, który przekazuje do magazynu kondycji. Raport może zostać zaakceptowany przez bramę, ale odrzucony przez magazyn kondycji po dodatkowej weryfikacji. Na przykład magazyn kondycji może odrzucić raport z powodu nieprawidłowego parametru, takiego jak nieświeży numer sekwencyjny. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, uruchom GetClusterHealth i sprawdź, czy raport pojawia się w healthEvents sekcji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --health-property [Wymagane] | Właściwość informacji o kondycji. <br><br> Jednostka może mieć raporty kondycji dla różnych właściwości. Właściwość jest ciągiem, a nie stałe wyliczenie, aby umożliwić elastyczność reporter kategoryzować warunek stanu, który wyzwala raport. Na przykład reporter z SourceId "LocalWatchdog" może monitorować stan dostępnego dysku w węźle, dzięki czemu może zgłosić właściwość "AvailableDisk" w tym węźle. Ten sam reporter może monitorować łączność węzła, dzięki czemu może zgłaszać właściwość "Łączność" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako oddzielne zdarzenia kondycji dla określonego węzła. Wraz z SourceId właściwość jednoznacznie identyfikuje informacje o kondycji. |
| --health-state [Wymagane] | Możliwe wartości\: to "Invalid", "Ok", "Warning", "Error", "Unknown". |
| --source-id [Wymagane] | Nazwa źródła identyfikująca składnik klienta/watchdog/system, który wygenerował informacje o kondycji. |
| --opis | Opis informacji o kondycji. <br><br> Reprezentuje wolny tekst używany do dodawania informacji o raporcie czytelnych dla ludzi. Maksymalna długość ciągu opisu wynosi 4096 znaków. Jeśli podany ciąg jest dłuższy, zostanie automatycznie obcięty. Po obcięciu ostatnie znaki opisu zawierają znacznik "[Obcięty]", a całkowity rozmiar ciągu to 4096 znaków. Obecność znacznika wskazuje użytkownikom, że doszło do obcinania. Należy zauważyć, że po obcięciu opis ma mniej niż 4096 znaków z oryginalnego ciągu. |
| --natychmiastowe | Flaga wskazująca, czy raport powinien zostać wysłany natychmiast. <br><br> Raport kondycji jest wysyłany do aplikacji bramy sieci szkieletowej usług, która przekazuje do magazynu kondycji. Jeśli immediate jest ustawiona na true, raport jest wysyłany natychmiast z bramy HTTP do magazynu kondycji, niezależnie od ustawień klienta sieci szkieletowej, które używa aplikacji bramy HTTP. Jest to przydatne w przypadku raportów krytycznych, które powinny być wysyłane tak szybko, jak to możliwe. W zależności od czasu i innych warunków wysłanie raportu może zakończyć się niepowodzeniem, na przykład jeśli brama HTTP jest zamknięta lub wiadomość nie dociera do bramy. Jeśli immediate jest ustawiona na false, raport jest wysyłany na podstawie ustawień klienta kondycji z bramy HTTP. W związku z tym będzie partią zgodnie z HealthReportSendInterval konfiguracji. Jest to zalecane ustawienie, ponieważ umożliwia klientowi kondycji optymalizację komunikatów raportowania kondycji do magazynu kondycji, a także przetwarzania raportu kondycji. Domyślnie raporty nie są wysyłane natychmiast. |
| --remove-when-expired | Wartość wskazująca, czy raport jest usuwany z magazynu kondycji po jego wygaśnięciu. <br><br> Jeśli ustawiona wartość true, raport zostanie usunięty z magazynu kondycji po jego wygaśnięciu. Jeśli ustawiona na false, raport jest traktowany jako błąd po wygaśnięciu. Wartość tej właściwości jest domyślnie false. Gdy klienci raportują okresowo, należy ustawić RemoveWhenExpired false (domyślnie). W ten sposób jest reporter ma problemy (np. zakleszczenie) i nie może zgłosić, jednostka jest oceniana w przypadku błędu po wygaśnięciu raportu kondycji. Oznacza to jednostkę jako w stanie kondycji błąd. |
| --sekwencja-numer | Numer sekwencyjny dla tego raportu kondycji jako ciąg numeryczny. <br><br> Numer sekwencyjny raportu jest używany przez magazyn kondycji do wykrywania starych raportów. Jeśli nie zostanie określony, numer sekwencyjny jest generowany automatycznie przez klienta kondycji po dodaniu raportu. |
| --timeout -t | Wartość\: domyślna 60. |
| --ttl | Czas trwania, dla którego ten raport kondycji jest prawidłowy. To pole używa formatu ISO8601 do określania czasu trwania. <br><br> Gdy klienci zgłaszają się okresowo, powinni wysyłać raporty z większą częstotliwością niż czas życia. Jeśli klienci raportują o przejściu, mogą ustawić czas na nieskończoność. Po upływie czasu wygaśnięcia zdarzenia kondycji, który zawiera informacje o kondycji jest usuwany z magazynu kondycji, jeśli RemoveWhenExpired jest true, lub oceniane w przypadku błędu, jeśli RemoveWhenExpired false. Jeśli nie określono, czas na żywo domyślnie nieskończoną wartość. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-select"></a>Wybór klastra sfctl
Łączy się z punktem końcowym klastra sieci szkieletowej usług.

Jeśli łączysz się z bezpiecznym klastrem, określ ścieżkę bezwzględną do pliku certyfikatu (crt) i pliku klucza (.key) lub pojedynczego pliku z obydwoma (pem). Nie należy określać obu tych opcji. Opcjonalnie, jeśli łączysz się z bezpiecznym klastrem, należy również określić ścieżkę bezwzględną do pliku lub katalogu pakietu urzędu certyfikacji zaufanych certyfikatów urzędu certyfikacji.  Nie ma połączenia z klastrem bez wcześniejszego uruchomienia tego polecenia, w tym połączenia z localhost. Jednak nie jawny punkt końcowy jest wymagany do łączenia się z klastrem lokalnym.  Jeśli używasz certyfikatu z podpisem własnym lub innego certyfikatu, który nie jest podpisany przez dobrze znany urząd certyfikacji, przekaż parametr --ca, aby upewnić się, że sprawdzanie poprawności przebiega. Jeśli nie w klastrze produkcyjnym, aby pominąć sprawdzanie poprawności po stronie klienta (przydatne dla podpisu z podpisem samodzielnie lub nie dobrze znanego urzędu certyfikacji podpisanego), należy użyć opcji --no-verify. Chociaż jest to możliwe, nie jest zalecane dla klastrów produkcyjnych. Błąd weryfikacji certyfikatu może spowodować inaczej.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --aad | Użyj usługi Azure Active Directory do uwierzytelniania. |
| --ca | Ścieżka bezwzględna do katalogu certyfikatów urzędu certyfikacji w celu traktowania go jako prawidłowego lub pliku pakietu urzędu certyfikacji. W przypadku korzystania z katalogu `c_rehash <directory>` certyfikatów urzędu certyfikacji, dostarczone przez OpenSSL musi być uruchomiony najpierw do obliczenia skrótów certyfikatu i utworzyć odpowiednie łącza symboliczne. Służy do sprawdzania, czy certyfikat zwrócony przez klaster jest prawidłowy. |
| --cert | Bezwzględna ścieżka do pliku certyfikatu klienta. |
| --punkt końcowy | Adres URL punktu końcowego klastra, w tym port i prefiks HTTP lub HTTPS. Zazwyczaj punkt końcowy będzie wyglądać jak\:https //<adres URL>\:19080. Jeśli nie podano punktu końcowego,\:domyślnie będzie\:http //localhost 19080.  \: Domyślnie\:http //localhost\:19080. |
| --klucz | Bezwzględna ścieżka do pliku klucza certyfikatu klienta. |
| --no-verify | Wyłącz weryfikację certyfikatów podczas\: korzystania z protokołu HTTPS, pamiętaj, że jest to opcja niezabezpieczona i nie powinna być używana w środowiskach produkcyjnych. |
| --pem | Ścieżka bezwzględna do certyfikatu klienta jako plik pem. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-show-connection"></a>Sfctl klastra show-connection
Pokaż, do którego klastra sieci szkieletowej usług jest połączone to wystąpienie sfctl.

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-unprovision"></a>nierevision klastra sfctl
Anulowanie obsługi administracyjnej pakietów kodu lub konfiguracji klastra sieci szkieletowej usług.

Jest obsługiwany do kodu unprovision i konfiguracji oddzielnie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --code-version | Wersja pakietu kodu klastra. |
| --config-wersja | Wersja manifestu klastra. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-upgrade"></a>Uaktualnienie klastra sfctl
Rozpocznij uaktualnianie kodu lub wersji konfiguracji klastra sieci szkieletowej usług.

Sprawdź poprawność podanych parametrów uaktualnienia i rozpocznij uaktualnianie kodu lub wersji konfiguracji klastra sieci szkieletowej usług, jeśli parametry są prawidłowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --app-health-map | JSON zakodowany słownik par nazwy aplikacji i maksymalny procent w złej kondycji przed podniesieniem błędu. |
| --app-type-health-map | JSON zakodowany słownik par nazwy typu aplikacji i maksymalny procent w złej kondycji przed podniesieniem błędu. |
| --code-version | Wersja kodu klastra. |
| --config-wersja | Wersja konfiguracji klastra. |
| --delta-health-evaluation | Umożliwia ocenę kondycji różnicy, a nie bezwzględną ocenę kondycji po zakończeniu każdej domeny uaktualnienia. |
| --delta-unhealthy-nodes | Maksymalny dozwolony procent degradacji kondycji węzłów dozwolony podczas uaktualniania klastra.  Wartość\: domyślna 10. <br><br> Delta jest mierzona między stan węzłów na początku uaktualnienia i stan węzłów w czasie oceny kondycji. Sprawdzanie jest wykonywane po każdym zakończeniu uaktualniania domeny uaktualnienia, aby upewnić się, że globalny stan klastra mieści się w dopuszczalnych granicach. |
| --failure-action | Możliwe wartości\: to "Nieprawidłowe", "Wycofywanie", "Podręcznik". |
| --force-restart | Procesy są na siłę uruchamiane podczas uaktualniania, nawet jeśli wersja kodu nie uległa zmianie. <br><br> Uaktualnienie zmienia tylko konfigurację lub dane. |
| --health-check-retry | Czas między próbami wykonywania kontroli kondycji, jeśli aplikacja lub klaster nie jest w dobrej kondycji. |
| --health-check-stable | Czas, przez który aplikacja lub klaster musi pozostać w dobrej kondycji, zanim uaktualnienie przejdzie do następnej domeny uaktualnienia. <br><br> Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. |
| --health-check-wait | Czas oczekiwania po ukończeniu domeny uaktualnienia przed rozpoczęciem procesu sprawdzania kondycji. |
| --replica-set-check-time-timeout -replica-set-check-timeout --replica-set-check-timeout - | Maksymalny czas blokowania przetwarzania domeny uaktualnienia i zapobiegania utracie dostępności w przypadku wystąpienia nieoczekiwanych problemów. <br><br> Po upływie tego limitu czasu przetwarzanie domeny uaktualnienia będzie kontynuowane niezależnie od problemów z utratą dostępności. Limit czasu jest resetowany na początku każdej domeny uaktualnienia. Prawidłowe wartości są między 0 i 42949672925 włącznie. |
| --rolling-upgrade-mode --rolling-upgrade-mode --rolling-upgrade-mode -- | Możliwe wartości\: to "Nieprawidłowe", "NiemonitorowaneAuto", "NiemonitorowaneManualne", "Monitorowane".  Domyślnie\: Niemonitorowaneaauto. |
| --timeout -t | Wartość\: domyślna 60. |
| --unhealthy-aplikacje | Maksymalny dozwolony procent aplikacji w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład, aby umożliwić 10% aplikacji w złej kondycji, ta wartość będzie 10. Wartość procentowa reprezentuje maksymalny tolerowany procent aplikacji, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jedna aplikacja w złej kondycji, kondycja jest oceniana jako Ostrzeżenie. Jest to obliczane przez podzielenie liczby aplikacji w złej kondycji na całkowitą liczbę wystąpień aplikacji w klastrze, z wyłączeniem aplikacji typów aplikacji, które są zawarte w ApplicationTypeHealthPolicyMap. Obliczenia zaokrągla się tolerować jeden błąd na małej liczbie aplikacji. |
| --niezdrowe węzły | Maksymalny dozwolony procent węzłów w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład, aby umożliwić 10% węzłów w złej kondycji, ta wartość będzie 10. Wartość procentowa reprezentuje maksymalny tolerowany procent węzłów, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jeden węzeł w złej kondycji, kondycja jest oceniana jako Ostrzeżenie. Wartość procentowa jest obliczana przez podzielenie liczby węzłów w złej kondycji na całkowitą liczbę węzłów w klastrze. Obliczenia zaokrągla się tolerować jeden błąd na małej liczbie węzłów. W dużych klastrach niektóre węzły będą zawsze w dół lub na zewnątrz do naprawy, więc ten procent powinien być skonfigurowany do tolerowania tego. |
| --upgrade-domain-delta-unhealthy-nodes --upgrade-domain-delta-unhealthy-nodes --upgrade-domain-delta-unhealthy-nodes -- | Maksymalny dozwolony procent pogorszenia kondycji węzłów domeny uaktualnienia jest dozwolony podczas uaktualniania klastra.  Wartość\: domyślna 15. <br><br> Delta jest mierzona między stanem węzłów domeny uaktualnienia na początku uaktualnienia i stan węzłów domeny uaktualnienia w czasie oceny kondycji. Sprawdzanie jest wykonywane po każdym zakończeniu uaktualniania domeny uaktualnienia dla wszystkich ukończonych domen uaktualnienia, aby upewnić się, że stan domen uaktualnienia mieści się w dopuszczalnych granicach. |
| --upgrade-domain-timeout | Czas, przez który każda domena uaktualnienia musi zostać ukończona przed wykonaniem FailureAction. <br><br> Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. |
| --limit czasu uaktualnienia | Czas ukończenia całego uaktualnienia przed wykonaniem FailureAction. <br><br> Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. |
| --warning-as-error | Wskazuje, czy ostrzeżenia są traktowane z taką samą ważnością jak błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-upgrade-resume"></a>Sfctl klastra upgrade-resume
Aby uaktualnienie klastra przejść do następnej domeny uaktualnienia.

W razie potrzeby przenieś kod klastra lub uaktualnienie konfiguracji do następnej domeny uaktualnienia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --upgrade-domain [Wymagane] | Następna domena uaktualnienia dla tego uaktualnienia klastra. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-upgrade-rollback"></a>uaktualnianie i wycofywanie klastra sfctl
Wycofaj uaktualnienie klastra sieci szkieletowej usług.

Wycofaj uaktualnienie kodu lub konfiguracji klastra sieci szkieletowej usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-upgrade-status"></a>Stan uaktualnienia klastra sfctl
Pobiera postęp bieżącego uaktualnienia klastra.

Pobiera bieżący postęp trwającego uaktualnienia klastra. Jeśli żadne uaktualnienie nie jest obecnie w toku, uzyskać ostatni stan poprzedniego uaktualnienia klastra.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-upgrade-update"></a>aktualizacja uaktualnienia klastra sfctl
Aktualizowanie parametrów uaktualniania uaktualnienia klastra sieci szkieletowej usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --app-health-map | JSON zakodowany słownik par nazwy aplikacji i maksymalny procent w złej kondycji przed podniesieniem błędu. |
| --app-type-health-map | JSON zakodowany słownik par nazwy typu aplikacji i maksymalny procent w złej kondycji przed podniesieniem błędu. |
| --delta-health-evaluation | Umożliwia ocenę kondycji różnicy, a nie bezwzględną ocenę kondycji po zakończeniu każdej domeny uaktualnienia. |
| --delta-unhealthy-nodes | Maksymalny dozwolony procent degradacji kondycji węzłów dozwolony podczas uaktualniania klastra.  Wartość\: domyślna 10. <br><br> Delta jest mierzona między stan węzłów na początku uaktualnienia i stan węzłów w czasie oceny kondycji. Sprawdzanie jest wykonywane po każdym zakończeniu uaktualniania domeny uaktualnienia, aby upewnić się, że globalny stan klastra mieści się w dopuszczalnych granicach. |
| --failure-action | Możliwe wartości\: to "Nieprawidłowe", "Wycofywanie", "Podręcznik". |
| --force-restart | Procesy są na siłę uruchamiane podczas uaktualniania, nawet jeśli wersja kodu nie uległa zmianie. <br><br> Uaktualnienie zmienia tylko konfigurację lub dane. |
| --health-check-retry | Czas między próbami wykonywania kontroli kondycji, jeśli aplikacja lub klaster nie jest w dobrej kondycji. |
| --health-check-stable | Czas, przez który aplikacja lub klaster musi pozostać w dobrej kondycji, zanim uaktualnienie przejdzie do następnej domeny uaktualnienia. <br><br> Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. |
| --health-check-wait | Czas oczekiwania po ukończeniu domeny uaktualnienia przed rozpoczęciem procesu sprawdzania kondycji. |
| --replica-set-check-time-timeout -replica-set-check-timeout --replica-set-check-timeout - | Maksymalny czas blokowania przetwarzania domeny uaktualnienia i zapobiegania utracie dostępności w przypadku wystąpienia nieoczekiwanych problemów. <br><br> Po upływie tego limitu czasu przetwarzanie domeny uaktualnienia będzie kontynuowane niezależnie od problemów z utratą dostępności. Limit czasu jest resetowany na początku każdej domeny uaktualnienia. Prawidłowe wartości są między 0 i 42949672925 włącznie. |
| --rolling-upgrade-mode --rolling-upgrade-mode --rolling-upgrade-mode -- | Możliwe wartości\: to "Nieprawidłowe", "NiemonitorowaneAuto", "NiemonitorowaneManualne", "Monitorowane".  Domyślnie\: Niemonitorowaneaauto. |
| --timeout -t | Wartość\: domyślna 60. |
| --unhealthy-aplikacje | Maksymalny dozwolony procent aplikacji w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład, aby umożliwić 10% aplikacji w złej kondycji, ta wartość będzie 10. Wartość procentowa reprezentuje maksymalny tolerowany procent aplikacji, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jedna aplikacja w złej kondycji, kondycja jest oceniana jako Ostrzeżenie. Jest to obliczane przez podzielenie liczby aplikacji w złej kondycji na całkowitą liczbę wystąpień aplikacji w klastrze, z wyłączeniem aplikacji typów aplikacji, które są zawarte w ApplicationTypeHealthPolicyMap. Obliczenia zaokrągla się tolerować jeden błąd na małej liczbie aplikacji. |
| --niezdrowe węzły | Maksymalny dozwolony procent węzłów w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład, aby umożliwić 10% węzłów w złej kondycji, ta wartość będzie 10. Wartość procentowa reprezentuje maksymalny tolerowany procent węzłów, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jeden węzeł w złej kondycji, kondycja jest oceniana jako Ostrzeżenie. Wartość procentowa jest obliczana przez podzielenie liczby węzłów w złej kondycji na całkowitą liczbę węzłów w klastrze. Obliczenia zaokrągla się tolerować jeden błąd na małej liczbie węzłów. W dużych klastrach niektóre węzły będą zawsze w dół lub na zewnątrz do naprawy, więc ten procent powinien być skonfigurowany do tolerowania tego. |
| --upgrade-domain-delta-unhealthy-nodes --upgrade-domain-delta-unhealthy-nodes --upgrade-domain-delta-unhealthy-nodes -- | Maksymalny dozwolony procent pogorszenia kondycji węzłów domeny uaktualnienia jest dozwolony podczas uaktualniania klastra.  Wartość\: domyślna 15. <br><br> Delta jest mierzona między stanem węzłów domeny uaktualnienia na początku uaktualnienia i stan węzłów domeny uaktualnienia w czasie oceny kondycji. Sprawdzanie jest wykonywane po każdym zakończeniu uaktualniania domeny uaktualnienia dla wszystkich ukończonych domen uaktualnienia, aby upewnić się, że stan domen uaktualnienia mieści się w dopuszczalnych granicach. |
| --upgrade-domain-timeout | Czas, przez który każda domena uaktualnienia musi zostać ukończona przed wykonaniem FailureAction. <br><br> Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. |
| --upgrade-rodzaju | Możliwe wartości\: to "Invalid", "Rolling", "Rolling_ForceRestart".  Domyślne\: walcowanie. |
| --limit czasu uaktualnienia | Czas ukończenia całego uaktualnienia przed wykonaniem FailureAction. <br><br> Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. |
| --warning-as-error | Wskazuje, czy ostrzeżenia są traktowane z taką samą ważnością jak błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia sieci szkieletowej usług.
- Dowiedz się, jak używać interfejsu wiersza polecenia sieci szkieletowej usług przy użyciu [przykładowych skryptów.](/azure/service-fabric/scripts/sfctl-upgrade-application)