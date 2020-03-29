---
title: Replika interfejsu wiersza polecenia sieci szkieletowej usługi Azure
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń do zarządzania replikami.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f6ad0b4c08ac8d710340fe654a068d0a3804e58f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905812"
---
# <a name="sfctl-replica"></a>sfctl replica
Zarządzanie replikami należącymi do partycji usługi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Wdrożony | Pobiera szczegóły repliki wdrożone w węźle sieci szkieletowej usług. |
| wdrożona lista | Pobiera listę replik wdrożonych w węźle sieci szkieletowej usług. |
| Zdrowia | Pobiera kondycję repliki usługi usługi sieci szkieletowej lub wystąpienia usługi bezstanowej. |
| informacje o | Pobiera informacje o replice partycji sieci szkieletowej usług. |
| list | Pobiera informacje o replikach partycji usługi sieci szkieletowej. |
| remove | Usuwa replikę usługi działającą w węźle. |
| raport-zdrowie | Wysyła raport kondycji repliki sieci szkieletowej usług. |
| restart | Uruchamia ponownie replikę usługi utrwalone usługi uruchomionej w węźle. |

## <a name="sfctl-replica-deployed"></a>wdrożona replika sfctl
Pobiera szczegóły repliki wdrożone w węźle sieci szkieletowej usług.

Pobiera szczegóły repliki wdrożone w węźle sieci szkieletowej usług. Informacje obejmują rodzaj usługi, nazwę usługi, bieżącą operację usługi, bieżącą godzinę rozpoczęcia operacji usługi, identyfikator partycji, identyfikator repliki/wystąpienia, zgłoszone obciążenie i inne informacje.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --node-name [Wymagane] | Nazwa węzła. |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --replica-id [Wymagane] | Identyfikator repliki. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-replica-deployed-list"></a>wdrożona lista replik sfctl
Pobiera listę replik wdrożonych w węźle sieci szkieletowej usług.

Pobiera listę zawierającą informacje o replikach wdrożonych w węźle sieci szkieletowej usług. Informacje obejmują identyfikator partycji, identyfikator repliki, stan repliki, nazwę usługi, nazwę typu usługi i inne informacje. Użyj parametrów kwerendy PartitionId lub ServiceManifestName, aby zwrócić informacje o wdrożonych replikach pasujących do określonych wartości dla tych parametrów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --node-name [Wymagane] | Nazwa węzła. |
| --partition-id | Tożsamość partycji. |
| --service-manifest-name | Nazwa manifestu usługi zarejestrowanego jako część typu aplikacji w klastrze sieci szkieletowej usług. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-replica-health"></a>sfctl kondycja repliki
Pobiera kondycję repliki usługi usługi sieci szkieletowej lub wystąpienia usługi bezstanowej.

Pobiera kondycję repliki sieci szkieletowej usług. Za pomocą EventsHealthStateFilter do filtrowania kolekcji zdarzeń kondycji zgłoszonych w replice na podstawie stanu kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --replica-id [Wymagane] | Identyfikator repliki. |
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

## <a name="sfctl-replica-info"></a>informacje o repliki sfctl
Pobiera informacje o replice partycji sieci szkieletowej usług.

Odpowiedź zawiera identyfikator, rolę, stan, kondycję, nazwę węzła, czas pracy bez przestojów i inne szczegóły dotyczące repliki.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --replica-id [Wymagane] | Identyfikator repliki. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-replica-list"></a>lista replik sfctl
Pobiera informacje o replikach partycji usługi sieci szkieletowej.

Punkt końcowy GetReplicas zwraca informacje o replikach określonej partycji. Odpowiedź zawiera identyfikator, rolę, stan, kondycję, nazwę węzła, czas pracy bez przestojów i inne szczegóły dotyczące repliki.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --continuation-token | Parametr tokenu kontynuacji jest używany do uzyskania następnego zestawu wyników. Token kontynuacji z wartością niepustą jest uwzględniony w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-replica-remove"></a>usuwanie repliki sfctl
Usuwa replikę usługi działającą w węźle.

Ten interfejs API symuluje błąd repliki sieci szkieletowej usług, usuwając replikę z klastra sieci szkieletowej usług. Usunięcie zamyka replikę, przenosi replikę do roli Brak, a następnie usuwa wszystkie informacje o stanie repliki z klastra. Ten interfejs API testuje ścieżkę usuwania stanu repliki i symuluje trwałą ścieżkę błędu raportu za pośrednictwem interfejsów API klienta. Ostrzeżenie — nie są wykonywane żadne kontrole bezpieczeństwa, gdy używany jest ten interfejs API. Niepoprawne użycie tego interfejsu API może prowadzić do utraty danych dla usług stanowych. Ponadto forceRemove flaga wpływa na wszystkie inne repliki hostowane w tym samym procesie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --node-name [Wymagane] | Nazwa węzła. |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --replica-id [Wymagane] | Identyfikator repliki. |
| --force-remove | Usuń aplikację sieci szkieletowej usług lub usługi na siłę bez przechodzenia przez sekwencję bezpiecznego zamykania. Ten parametr może służyć do zdecydowanego usunięcia aplikacji lub usługi, dla których delete jest limit czasu z powodu problemów w kodzie usługi, który uniemożliwia wdzięczne zamknięcie replik. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-replica-report-health"></a>sfctl repliki raportu-kondycji
Wysyła raport kondycji repliki sieci szkieletowej usług.

Raportuje stan kondycji określonej repliki sieci szkieletowej usług. Raport musi zawierać informacje o źródle raportu kondycji i właściwości, na których jest raport. Raport jest wysyłany do bramy sieci szkieletowej usług Replika, która przekazuje do magazynu kondycji. Raport może zostać zaakceptowany przez bramę, ale odrzucony przez magazyn kondycji po dodatkowej weryfikacji. Na przykład magazyn kondycji może odrzucić raport z powodu nieprawidłowego parametru, takiego jak nieświeży numer sekwencyjny. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, uruchom get health repliki i sprawdź, czy raport pojawia się w healthEvents sekcji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --health-property [Wymagane] | Właściwość informacji o kondycji. <br><br> Jednostka może mieć raporty kondycji dla różnych właściwości. Właściwość jest ciągiem, a nie stałe wyliczenie, aby umożliwić elastyczność reporter kategoryzować warunek stanu, który wyzwala raport. Na przykład reporter z SourceId "LocalWatchdog" może monitorować stan dostępnego dysku w węźle, dzięki czemu może zgłosić właściwość "AvailableDisk" w tym węźle. Ten sam reporter może monitorować łączność węzła, dzięki czemu może zgłaszać właściwość "Łączność" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako oddzielne zdarzenia kondycji dla określonego węzła. Wraz z SourceId właściwość jednoznacznie identyfikuje informacje o kondycji. |
| --health-state [Wymagane] | Możliwe wartości\: to "Invalid", "Ok", "Warning", "Error", "Unknown". |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --replica-id [Wymagane] | Tożsamość partycji. |
| --source-id [Wymagane] | Nazwa źródła identyfikująca składnik klienta/watchdog/system, który wygenerował informacje o kondycji. |
| --opis | Opis informacji o kondycji. <br><br> Reprezentuje wolny tekst używany do dodawania informacji o raporcie czytelnych dla ludzi. Maksymalna długość ciągu opisu wynosi 4096 znaków. Jeśli podany ciąg jest dłuższy, zostanie automatycznie obcięty. Po obcięciu ostatnie znaki opisu zawierają znacznik "[Obcięty]", a całkowity rozmiar ciągu to 4096 znaków. Obecność znacznika wskazuje użytkownikom, że doszło do obcinania. Należy zauważyć, że po obcięciu opis ma mniej niż 4096 znaków z oryginalnego ciągu. |
| --natychmiastowe | Flaga wskazująca, czy raport powinien zostać wysłany natychmiast. <br><br> Raport kondycji jest wysyłany do aplikacji bramy sieci szkieletowej usług, która przekazuje do magazynu kondycji. Jeśli immediate jest ustawiona na true, raport jest wysyłany natychmiast z bramy HTTP do magazynu kondycji, niezależnie od ustawień klienta sieci szkieletowej, które używa aplikacji bramy HTTP. Jest to przydatne w przypadku raportów krytycznych, które powinny być wysyłane tak szybko, jak to możliwe. W zależności od czasu i innych warunków wysłanie raportu może zakończyć się niepowodzeniem, na przykład jeśli brama HTTP jest zamknięta lub wiadomość nie dociera do bramy. Jeśli immediate jest ustawiona na false, raport jest wysyłany na podstawie ustawień klienta kondycji z bramy HTTP. W związku z tym będzie partią zgodnie z HealthReportSendInterval konfiguracji. Jest to zalecane ustawienie, ponieważ umożliwia klientowi kondycji optymalizację komunikatów raportowania kondycji do magazynu kondycji, a także przetwarzania raportu kondycji. Domyślnie raporty nie są wysyłane natychmiast. |
| --remove-when-expired | Wartość wskazująca, czy raport jest usuwany z magazynu kondycji po jego wygaśnięciu. <br><br> Jeśli ustawiona wartość true, raport zostanie usunięty z magazynu kondycji po jego wygaśnięciu. Jeśli ustawiona na false, raport jest traktowany jako błąd po wygaśnięciu. Wartość tej właściwości jest domyślnie false. Gdy klienci raportują okresowo, należy ustawić RemoveWhenExpired false (domyślnie). W ten sposób jest reporter ma problemy (np. zakleszczenie) i nie może zgłosić, jednostka jest oceniana w przypadku błędu po wygaśnięciu raportu kondycji. Oznacza to jednostkę jako w stanie kondycji błąd. |
| --sekwencja-numer | Numer sekwencyjny dla tego raportu kondycji jako ciąg numeryczny. <br><br> Numer sekwencyjny raportu jest używany przez magazyn kondycji do wykrywania starych raportów. Jeśli nie zostanie określony, numer sekwencyjny jest generowany automatycznie przez klienta kondycji po dodaniu raportu. |
| --service-kind | Rodzaj repliki usługi (bezstanowe lub stanowe), dla których kondycja jest zgłaszane. Poniżej znajdują\: się możliwe wartości "Bezstanowy", "Stateful".  Domyślny\: stanowy. |
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

## <a name="sfctl-replica-restart"></a>Ponowne uruchomienie repliki sfctl
Uruchamia ponownie replikę usługi utrwalone usługi uruchomionej w węźle.

Uruchamia ponownie replikę usługi utrwalone usługi uruchomionej w węźle. Ostrzeżenie — nie są wykonywane żadne kontrole bezpieczeństwa, gdy używany jest ten interfejs API. Niepoprawne użycie tego interfejsu API może prowadzić do utraty dostępności dla usług stanowych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --node-name [Wymagane] | Nazwa węzła. |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --replica-id [Wymagane] | Identyfikator repliki. |
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
