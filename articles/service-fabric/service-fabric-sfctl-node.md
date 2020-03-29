---
title: Węzeł interfejsu wiersza polecenia sieci szkieletowej usługi Azure
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń do zarządzania węzłami klastra.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 5881e6485003abd4fd23a7f6d06a428e768c00fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905883"
---
# <a name="sfctl-node"></a>sfctl node
Zarządzanie węzłami tworzącymi klaster.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| add-configuration-parametr-overrides add-configuration-parameter-overrides add-configuration-parameter-overrides add- | Dodaje listę nadpisań konfiguracji w określonym węźle. |
| Wyłącz | Dezaktywuj węzeł klastra sieci szkieletowej usług o określonej intencji dezaktywacji. |
| Włącz | Uaktywnianie węzła klastra sieci szkieletowej usług, który jest aktualnie dezaktywowany. |
| zastępowania konfiguracji | Pobiera listę zastąpienia konfiguracji w określonym węźle. |
| Zdrowia | Pobiera kondycję węzła sieci szkieletowej usług. |
| informacje o | Pobiera informacje o określonym węźle w klastrze sieci szkieletowej usług. |
| list | Pobiera listę węzłów w klastrze sieci szkieletowej usług. |
| ładowanie | Pobiera informacje o obciążeniu węzła sieci szkieletowej usług. |
| usuwanie-zastępowanie konfiguracji | Usuwa zastąpienia konfiguracji w określonym węźle. |
| stan usunięcia | Powiadamia sieci szkieletowej usług, że utrwalony stan w węźle został trwale usunięty lub utracony. |
| raport-zdrowie | Wysyła raport kondycji w węźle sieci szkieletowej usług. |
| restart | Uruchamia ponownie węzeł klastra sieci szkieletowej usług. |
| Przejścia | Uruchamia lub zatrzymuje węzeł klastra. |
| stan przejścia | Pobiera postęp operacji rozpoczętej przy użyciu StartNodeTransition. |

## <a name="sfctl-node-add-configuration-parameter-overrides"></a>sfctl node add-configuration-parameter-overrides sfctl node add-configuration-parameter
Dodaje listę nadpisań konfiguracji w określonym węźle.

Ten interfejs API umożliwia dodawanie wszystkich istniejących zastąpienia konfiguracji w określonym węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --config-parameter-override-list [Wymagane] | Opis dodawania listy nadpisań konfiguracji. |
| --node-name [Wymagane] | Nazwa węzła. |
| --siła | Wymuszanie zastępowania konfiguracji w określonych węzłach. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-disable"></a>wyłączanie węzła sfctl
Dezaktywuj węzeł klastra sieci szkieletowej usług o określonej intencji dezaktywacji.

Dezaktywuj węzeł klastra sieci szkieletowej usług o określonej intencji dezaktywacji. Po dezaktywacji jest w toku, intencyjny dezaktywacji można zwiększyć, ale nie zmniejszyć (na przykład węzeł, który jest dezaktywowany z zamiarem wstrzymania można dezaktywować dalej z restartu, ale nie na odwrót. Węzły mogą być ponownie aktywowane przy użyciu operacji Aktywuj węzeł w dowolnym momencie po ich dezaktywacji. Jeśli dezaktywacja nie zostanie zakończona, spowoduje to anulowanie dezaktywacji. Węzeł, który przechodzi w dół i wraca z powrotem w górę, gdy dezaktywowany nadal będzie musiał zostać ponownie uaktywniony, zanim usługi zostaną umieszczone w tym węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --node-name [Wymagane] | Nazwa węzła. |
| --dezaktywacja-intencja | Opisuje intencję lub przyczynę dezaktywacji węzła. Możliwe wartości są następujące. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-enable"></a>włącz węzeł sfctl
Uaktywnianie węzła klastra sieci szkieletowej usług, który jest aktualnie dezaktywowany.

Aktywuje węzeł klastra sieci szkieletowej usług, który jest aktualnie dezaktywowany. Po aktywacji węzeł ponownie stanie się realnym celem umieszczania nowych replik, a wszystkie dezaktywowane repliki pozostałe w węźle zostaną ponownie uaktywnione.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --node-name [Wymagane] | Nazwa węzła. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-get-configuration-overrides"></a>zastępowania węzła sfctl
Pobiera listę zastąpienia konfiguracji w określonym węźle.

Ten interfejs API umożliwia uzyskanie wszystkich istniejących zastąpienia konfiguracji w określonym węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --node-name [Wymagane] | Nazwa węzła. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-health"></a>sfctl node health (Kondycja węzła sfctl)
Pobiera kondycję węzła sieci szkieletowej usług.

Pobiera kondycję węzła sieci szkieletowej usług. Użyj EventsHealthStateFilter do filtrowania kolekcji zdarzeń kondycji zgłoszonych w węźle na podstawie stanu kondycji. Jeśli węzeł określony przez nazwę nie istnieje w magazynie kondycji, zwraca błąd.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --node-name [Wymagane] | Nazwa węzła. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji HealthEvent obiektów zwracanych na podstawie stanu kondycji. Możliwe wartości dla tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które pasują do filtru. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, wszystkie wpisy są zwracane. Wartości stanu są wyliczenia oparte na flagi, więc wartość może być kombinacją tych wartości, uzyskane przy użyciu bitowego "OR" operatora. Na przykład Jeśli podana wartość wynosi 6, zwracane są wszystkie zdarzenia o wartości HealthState OK (2) i Warning (4).  <br> - Domyślna — wartość domyślna. Pasuje do dowolnego HealthState. Wartość wynosi zero.  <br> - Brak — filtr, który nie pasuje do żadnej wartości HealthState. Używane w celu zwrócenia żadnych wyników w danej kolekcji stanów. Wartość wynosi 1.  <br> - Ok - Filtr, który pasuje do danych wejściowych z HealthState wartość Ok. Wartość wynosi 2.  <br> - Ostrzeżenie — filtr, który pasuje do danych wejściowych z HealthState wartość Ostrzeżenie. Wartość wynosi 4.  <br> - Błąd — filtr, który pasuje do danych wejściowych z HealthState wartość Error. Wartość wynosi 8.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej wartości HealthState. Wartość to 65535. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-info"></a>informacje o węźle sfctl
Pobiera informacje o określonym węźle w klastrze sieci szkieletowej usług.

Odpowiedź zawiera nazwę, stan, identyfikator, kondycję, czas pracy bez przestojów i inne szczegóły dotyczące węzła.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --node-name [Wymagane] | Nazwa węzła. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-list"></a>lista węzłów sfctl
Pobiera listę węzłów w klastrze sieci szkieletowej usług.

Odpowiedź zawiera nazwę, stan, identyfikator, kondycję, czas pracy bez przestojów i inne szczegóły dotyczące węzłów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --continuation-token | Parametr tokenu kontynuacji jest używany do uzyskania następnego zestawu wyników. Token kontynuacji z wartością niepustą jest uwzględniony w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --max-wyniki | Maksymalna liczba wyników do zwrotu w ramach zapytań stronicowanych. Ten parametr definiuje górną granicę na liczbę zwracanych wyników. Zwracane wyniki mogą być mniejsze niż określone maksymalne wyniki, jeśli nie mieszczą się w wiadomości zgodnie z ograniczeniami maksymalnego rozmiaru wiadomości zdefiniowanymi w konfiguracji. Jeśli ten parametr jest zerowy lub nie określony, kwerenda stronicowana zawiera jak najwięcej wyników, które pasują do wiadomości zwrotnej. |
| --node-status-filter | Umożliwia filtrowanie węzłów na podstawie NodeStatus. Zostaną zwrócone tylko węzły, które pasują do określonej wartości filtru. Wartość filtru może być jedną z następujących czynności.  Domyślna wartość domyślna.\: |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-load"></a>obciążenie węzła sfctl
Pobiera informacje o obciążeniu węzła sieci szkieletowej usług.

Pobiera informacje o obciążeniu węzła sieci szkieletowej usług dla wszystkich metryk, które mają zdefiniowane obciążenie lub pojemność.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --node-name [Wymagane] | Nazwa węzła. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-remove-configuration-overrides"></a>sfctl node remove-configuration-overrides sfctl node remove-configuration-overrides sfctl node remove-configuration-overrides sfc
Usuwa zastąpienia konfiguracji w określonym węźle.

Ten interfejs API umożliwia usunięcie wszystkich istniejących nadpisań konfiguracji w określonym węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --node-name [Wymagane] | Nazwa węzła. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-remove-state"></a>stan usuwania węzła sfctl
Powiadamia sieci szkieletowej usług, że utrwalony stan w węźle został trwale usunięty lub utracony.

Oznacza to, że nie jest możliwe odzyskanie utrwalonych stanu tego węzła. Zazwyczaj dzieje się tak, jeśli dysk twardy został wyczyszczony lub jeśli dysk twardy ulegnie awarii. Węzeł musi być w dół, aby ta operacja zakończyła się pomyślnie. Ta operacja umożliwia sieci szkieletowej usług wiedzieć, że repliki w tym węźle już nie istnieje i że sieci szkieletowej usług należy zatrzymać oczekiwania na te repliki wrócić. Nie należy uruchamiać tego polecenia cmdlet, jeśli stan w węźle nie został usunięty, a węzeł może wrócić z jego stanem nienaruszonym. Począwszy od sieci szkieletowej usług 6.5, aby użyć tego interfejsu API dla węzłów źródłowych, zmień węzły źródłowe na zwykłe (niesiewne) węzły, a następnie wywołaj ten interfejs API, aby usunąć stan węzła. Jeśli klaster jest uruchomiony na platformie Azure, po węźle źródłowym ulegnie upadkowi, usługa Service Fabric spróbuje automatycznie zmienić go na węzeł niebędący źródłem nasion. Aby tak się stało, upewnij się, że liczba węzłów innych niż seed w typie węzła podstawowego jest nie mniejsza niż liczba węzłów źródłowych W dół. W razie potrzeby dodaj więcej węzłów do typu węzła podstawowego, aby to osiągnąć. W przypadku klastra autonomicznego, jeśli węzeł źródłowy W dół nie powinien wrócić z jego stanem\:nienaruszonym, usuń węzeł z klastra, zobacz https //docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --node-name [Wymagane] | Nazwa węzła. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-report-health"></a>sfctl node report-health
Wysyła raport kondycji w węźle sieci szkieletowej usług.

Raportuje stan kondycji określonego węzła sieci szkieletowej usług. Raport musi zawierać informacje o źródle raportu kondycji i właściwości, na których jest raport. Raport jest wysyłany do węzła bramy sieci szkieletowej usług, który przekazuje do magazynu kondycji. Raport może zostać zaakceptowany przez bramę, ale odrzucony przez magazyn kondycji po dodatkowej weryfikacji. Na przykład magazyn kondycji może odrzucić raport z powodu nieprawidłowego parametru, takiego jak nieświeży numer sekwencyjny. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, sprawdź, czy raport jest wyświetlany w sekcji HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --health-property [Wymagane] | Właściwość informacji o kondycji. <br><br> Jednostka może mieć raporty kondycji dla różnych właściwości. Właściwość jest ciągiem, a nie stałe wyliczenie, aby umożliwić elastyczność reporter kategoryzować warunek stanu, który wyzwala raport. Na przykład reporter z SourceId "LocalWatchdog" może monitorować stan dostępnego dysku w węźle, dzięki czemu może zgłosić właściwość "AvailableDisk" w tym węźle. Ten sam reporter może monitorować łączność węzła, dzięki czemu może zgłaszać właściwość "Łączność" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako oddzielne zdarzenia kondycji dla określonego węzła. Wraz z SourceId właściwość jednoznacznie identyfikuje informacje o kondycji. |
| --health-state [Wymagane] | Możliwe wartości\: to "Invalid", "Ok", "Warning", "Error", "Unknown". |
| --node-name [Wymagane] | Nazwa węzła. |
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

## <a name="sfctl-node-restart"></a>Ponowne uruchomienie węzła sfctl
Uruchamia ponownie węzeł klastra sieci szkieletowej usług.

Uruchamia ponownie węzeł klastra sieci szkieletowej usług, który jest już uruchomiony.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --node-name [Wymagane] | Nazwa węzła. |
| --create-fabric-dump | Określ wartość True, aby utworzyć zrzut procesu węzła sieci szkieletowej. Wielkość liter nie jest rozróżniana.  Wartość\: false domyślna. |
| --node-instance-id | Identyfikator wystąpienia węzła docelowego. Jeśli identyfikator wystąpienia jest określony, węzeł jest ponownie uruchamiany tylko wtedy, gdy jest zgodny z bieżącym wystąpieniem węzła. Domyślna wartość "0" będzie zgodna z dowolnym identyfikatorem wystąpienia. Identyfikator wystąpienia można uzyskać za pomocą kwerendy węzła get.  Wartość\: domyślna 0. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-transition"></a>przejście węzła sfctl
Uruchamia lub zatrzymuje węzeł klastra.

Uruchamia lub zatrzymuje węzeł klastra.  Węzeł klastra jest procesem, a nie samym wystąpieniem systemu operacyjnego.  Aby uruchomić węzeł, przekaż w "Start" dla NodeTransitionType parametru. Aby zatrzymać węzeł, przekaż w "Stop" dla NodeTransitionType parametru. Ten interfejs API rozpoczyna operację — gdy interfejs API zwraca węzeł może nie zostały jeszcze zakończone przejście. Wywołanie GetNodeTransitionProgress z tym samym OperationId, aby uzyskać postęp operacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --node-instance-id [Wymagane] | Identyfikator wystąpienia węzła węzła docelowego. Można to ustalić za pomocą interfejsu API GetNodeInfo. |
| --node-name [Wymagane] | Nazwa węzła. |
| --node-transition-type [Wymagane] | Wskazuje typ przejścia do wykonania.  NodeTransitionType.Start uruchomi zatrzymany węzeł. NodeTransitionType.Stop zatrzyma węzeł, który jest w górę. |
| --operation-id [Wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  Jest to przekazywane do odpowiedniego interfejsu API GetProgress. |
| --stop-duration-in-seconds [Wymagane] | Czas trwania w sekundach, aby zatrzymać węzeł.  Minimalna wartość to 600, maksymalna 14400.  Po upływie tego czasu węzeł automatycznie powróci. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-transition-status"></a>Stan przejścia węzła sfctl
Pobiera postęp operacji rozpoczętej przy użyciu StartNodeTransition.

Pobiera postęp operacji rozpoczętej z StartNodeTransition przy użyciu pod warunkiem OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --node-name [Wymagane] | Nazwa węzła. |
| --operation-id [Wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  Jest to przekazywane do odpowiedniego interfejsu API GetProgress. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

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