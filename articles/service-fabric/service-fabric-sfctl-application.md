---
title: Aplikacja interfejsu WIERSZA POLECENIA usługi Azure Service
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń do zarządzania aplikacjami.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: b4e1066bba1db387c9dc0600bc55522f0b5fe897
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906197"
---
# <a name="sfctl-application"></a>sfctl application
Tworzenie, usuwanie i zarządzanie aplikacjami i typami aplikacji.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| create | Tworzy aplikację sieci szkieletowej usług przy użyciu określonego opisu. |
| delete | Usuwa istniejącą aplikację sieci szkieletowej usług. |
| Wdrożony | Pobiera informacje o aplikacji wdrożonej w węźle sieci szkieletowej usług. |
| wdrożony-zdrowie | Pobiera informacje o kondycji aplikacji wdrożonej w węźle sieci szkieletowej usług. |
| wdrożona lista | Pobiera listę aplikacji wdrożonych w węźle sieci szkieletowej usług. |
| Zdrowia | Pobiera kondycję aplikacji sieci szkieletowej usług. |
| informacje o | Pobiera informacje o aplikacji sieci szkieletowej usług. |
| list | Pobiera listę aplikacji utworzonych w klastrze sieci szkieletowej usług, które pasują do określonych filtrów. |
| ładowanie | Pobiera informacje o załadowaniu aplikacji sieci szkieletowej usług. |
| manifest | Pobiera manifest opisujący typ aplikacji. |
| Przepis | Przepisy lub rejestruje typ aplikacji sieci szkieletowej usług z klastra przy użyciu pakietu ".sfpkg" w magazynie zewnętrznym lub przy użyciu pakietu aplikacji w magazynie obrazów. |
| raport-zdrowie | Wysyła raport kondycji w aplikacji sieci szkieletowej usług. |
| type | Pobiera listę typów aplikacji w klastrze sieci szkieletowej usług pasujące dokładnie określoną nazwę. |
| lista typów | Pobiera listę typów aplikacji w klastrze sieci szkieletowej usług. |
| unprovision | Usuwa lub wyrejestrowyje typ aplikacji sieci szkieletowej usług z klastra. |
| uaktualnienie | Rozpoczyna uaktualnianie aplikacji w klastrze sieci szkieletowej usług. |
| uaktualnienie-życiorys | Wznawia uaktualnianie aplikacji w klastrze sieci szkieletowej usług. |
| upgrade-rollback | Rozpoczyna wycofywanie aktualnie trwa uaktualnianie aplikacji w klastrze sieci szkieletowej usług. |
| stan uaktualnienia | Pobiera szczegółowe informacje na temat najnowszego uaktualnienia wykonywanego w tej aplikacji. |
| przekazywanie | Skopiuj pakiet aplikacji sieci szkieletowej usług do magazynu obrazów. |

## <a name="sfctl-application-create"></a>tworzenie aplikacji sfctl
Tworzy aplikację sieci szkieletowej usług przy użyciu określonego opisu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --nazwa aplikacji [Wymagane] | Nazwa aplikacji, w tym schemat\:URI "fabric". |
| --app-type [Wymagane] | Nazwa typu aplikacji znaleziona w manifeście aplikacji. |
| --app-version [Wymagane] | Wersja typu aplikacji zgodnie z definicją w manifeście aplikacji. |
| --max-node-count | Maksymalna liczba węzłów, w których sieci szkieletowej usług będzie rezerwować zdolności produkcyjne dla tej aplikacji. Należy zauważyć, że nie oznacza to, że usługi tej aplikacji zostaną umieszczone na wszystkich tych węzłach. |
| --metryki | A JSON zakodowana lista opisów metryk pojemności aplikacji. Metryka jest zdefiniowana jako nazwa skojarzona z zestawem pojemności dla każdego węzła, na których istnieje aplikacja. |
| --min-node-count | Minimalna liczba węzłów, w których sieci szkieletowej usług będzie rezerwować zdolności produkcyjne dla tej aplikacji. Należy zauważyć, że nie oznacza to, że usługi tej aplikacji zostaną umieszczone na wszystkich tych węzłach. |
| --parametry | A JSON zakodowana lista zastępów parametru aplikacji, które mają być stosowane podczas tworzenia aplikacji. |
| --timeout -t | Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-delete"></a>usuwanie aplikacji sfctl
Usuwa istniejącą aplikację sieci szkieletowej usług.

Aplikacja musi zostać utworzona, zanim będzie można ją usunąć. Usunięcie aplikacji spowoduje usunięcie wszystkich usług, które są częścią tej aplikacji. Domyślnie sieci szkieletowej usług spróbuje zamknąć repliki usługi w sposób pełen wdzięku, a następnie usunąć usługę. Jednak jeśli usługa ma problemy z zamknięciem repliki bezpiecznie, operacja usuwania może zająć dużo czasu lub utknąć. Użyj opcjonalnej ForceRemove flagi pominąć sekwencji zamknięcia w sposób wdzięku i zdecydowanie usunąć aplikację i wszystkie jej usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
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

## <a name="sfctl-application-deployed"></a>wdrożona aplikacja sfctl
Pobiera informacje o aplikacji wdrożonej w węźle sieci szkieletowej usług.

Ta kwerenda zwraca informacje o aplikacji systemowej, jeśli podany identyfikator aplikacji jest przeznaczony dla aplikacji systemowej. Wyniki obejmują wdrożone aplikacje w stanach aktywnych, aktywujących i pobierających. Ta kwerenda wymaga, aby nazwa węzła odpowiadała węzłowi w klastrze. Kwerenda kończy się niepowodzeniem, jeśli podana nazwa węzła nie wskazuje żadnych aktywnych węzłów sieci szkieletowej usług w klastrze.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --node-name [Wymagane] | Nazwa węzła. |
| --include-health-state | Uwzględnij stan kondycji jednostki. Jeśli ten parametr jest false lub nie określono, a następnie stan kondycji zwrócone jest "Nieznany". Po ustawieniu na wartość true kwerenda przechodzi równolegle do węzła i usługi systemu kondycji przed scaleniem wyników. W rezultacie kwerenda jest droższa i może potrwać dłużej. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-deployed-health"></a>Sfctl aplikacja wdrożona-kondycja
Pobiera informacje o kondycji aplikacji wdrożonej w węźle sieci szkieletowej usług.

Pobiera informacje o kondycji aplikacji wdrożonej w węźle sieci szkieletowej usług. Użyj EventsHealthStateFilter opcjonalnie filtrować dla kolekcji HealthEvent obiektów zgłoszonych w wdrożonej aplikacji na podstawie stanu kondycji. Użyj DeployedServicePackagesHealthStateFilter opcjonalnie filtrować dla dzieci DeployedServicePackageHealth na podstawie stanu kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --node-name [Wymagane] | Nazwa węzła. |
| --deployed-service-packages-health-state-filter | Umożliwia filtrowanie wdrożonych obiektów stanu kondycji pakietu usługi zwracanych w wyniku wdrożonej kwerendy kondycji aplikacji na podstawie ich stanu kondycji. Możliwe wartości dla tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko wdrożone pakiety usług, które pasują do filtru. Wszystkie wdrożone pakiety usług są używane do oceny stanu kondycji zagregowane wdrożonej aplikacji. Jeśli nie zostanie określony, wszystkie wpisy są zwracane. Wartości stanu są wyliczenia oparte na flagi, więc wartość może być kombinacją tych wartości, uzyskane za pomocą bitowego "OR" operatora. Na przykład jeśli podana wartość wynosi 6, zwracany jest stan kondycji pakietów usług o wartości HealthState OK (2) i Warning (4).  <br> - Domyślna — wartość domyślna. Pasuje do dowolnego HealthState. Wartość wynosi zero.  <br> - Brak — filtr, który nie pasuje do żadnej wartości HealthState. Używane w celu zwrócenia żadnych wyników w danej kolekcji stanów. Wartość wynosi 1.  <br> - Ok - Filtr, który pasuje do danych wejściowych z HealthState wartość Ok. Wartość wynosi 2.  <br> - Ostrzeżenie — filtr, który pasuje do danych wejściowych z HealthState wartość Ostrzeżenie. Wartość wynosi 4.  <br> - Błąd — filtr, który pasuje do danych wejściowych z HealthState wartość Error. Wartość wynosi 8.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej wartości HealthState. Wartość to 65535. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji HealthEvent obiektów zwracanych na podstawie stanu kondycji. Możliwe wartości dla tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które pasują do filtru. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, wszystkie wpisy są zwracane. Wartości stanu są wyliczenia oparte na flagi, więc wartość może być kombinacją tych wartości, uzyskane przy użyciu bitowego "OR" operatora. Na przykład Jeśli podana wartość wynosi 6, zwracane są wszystkie zdarzenia o wartości HealthState OK (2) i Warning (4).  <br> - Domyślna — wartość domyślna. Pasuje do dowolnego HealthState. Wartość wynosi zero.  <br> - Brak — filtr, który nie pasuje do żadnej wartości HealthState. Używane w celu zwrócenia żadnych wyników w danej kolekcji stanów. Wartość wynosi 1.  <br> - Ok - Filtr, który pasuje do danych wejściowych z HealthState wartość Ok. Wartość wynosi 2.  <br> - Ostrzeżenie — filtr, który pasuje do danych wejściowych z HealthState wartość Ostrzeżenie. Wartość wynosi 4.  <br> - Błąd — filtr, który pasuje do danych wejściowych z HealthState wartość Error. Wartość wynosi 8.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej wartości HealthState. Wartość to 65535. |
| --exclude-health-statistics | Wskazuje, czy statystyki kondycji powinny być zwracane jako część wyniku kwerendy. False domyślnie. Statystyki pokazują liczbę jednostek podrzędnych w stanie kondycji Ok, Ostrzeżenie i Błąd. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-deployed-list"></a>wdrożona lista aplikacji sfctl
Pobiera listę aplikacji wdrożonych w węźle sieci szkieletowej usług.

Pobiera listę aplikacji wdrożonych w węźle sieci szkieletowej usług. Wyniki nie zawierają informacji o wdrożonych aplikacjach systemowych, chyba że jawnie wyszukiwane przez identyfikator. Wyniki obejmują wdrożone aplikacje w stanach aktywnych, aktywujących i pobierających. Ta kwerenda wymaga, aby nazwa węzła odpowiadała węzłowi w klastrze. Kwerenda kończy się niepowodzeniem, jeśli podana nazwa węzła nie wskazuje żadnych aktywnych węzłów sieci szkieletowej usług w klastrze.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --node-name [Wymagane] | Nazwa węzła. |
| --continuation-token | Parametr tokenu kontynuacji jest używany do uzyskania następnego zestawu wyników. Token kontynuacji z wartością niepustą jest uwzględniony w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --include-health-state | Uwzględnij stan kondycji jednostki. Jeśli ten parametr jest false lub nie określono, a następnie stan kondycji zwrócone jest "Nieznany". Po ustawieniu na wartość true kwerenda przechodzi równolegle do węzła i usługi systemu kondycji przed scaleniem wyników. W rezultacie kwerenda jest droższa i może potrwać dłużej. |
| --max-wyniki | Maksymalna liczba wyników do zwrotu w ramach zapytań stronicowanych. Ten parametr definiuje górną granicę na liczbę zwracanych wyników. Zwracane wyniki mogą być mniejsze niż określone maksymalne wyniki, jeśli nie mieszczą się w wiadomości zgodnie z ograniczeniami maksymalnego rozmiaru wiadomości zdefiniowanymi w konfiguracji. Jeśli ten parametr jest zerowy lub nie określony, kwerenda stronicowana zawiera jak najwięcej wyników, które pasują do wiadomości zwrotnej. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-health"></a>sfctl kondycji aplikacji
Pobiera kondycję aplikacji sieci szkieletowej usług.

Zwraca stan wrzosu aplikacji sieci szkieletowej usług. Odpowiedź zgłasza stan kondycji Ok, Error lub Warning. Jeśli jednostka nie zostanie znaleziona w magazynie kondycji, zwróci błąd.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --deployed-applications-health-state-filter | Umożliwia filtrowanie wdrożonych obiektów stanu kondycji wdrożonych aplikacji zwracanych w wyniku kwerendy kondycji aplikacji na podstawie ich stanu kondycji. Możliwe wartości dla tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zostaną zwrócone tylko wdrożone aplikacje, które pasują do filtru. Wszystkie wdrożone aplikacje są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, wszystkie wpisy są zwracane. Wartości stanu są wyliczenia oparte na flagi, więc wartość może być kombinacją tych wartości, uzyskane za pomocą bitowego "OR" operatora. Na przykład jeśli podana wartość wynosi 6, zwracany jest stan kondycji wdrożonych aplikacji o wartości HealthState OK (2) i Warning (4).  <br> - Domyślna — wartość domyślna. Pasuje do dowolnego HealthState. Wartość wynosi zero.  <br> - Brak — filtr, który nie pasuje do żadnej wartości HealthState. Używane w celu zwrócenia żadnych wyników w danej kolekcji stanów. Wartość wynosi 1.  <br> - Ok - Filtr, który pasuje do danych wejściowych z HealthState wartość Ok. Wartość wynosi 2.  <br> - Ostrzeżenie — filtr, który pasuje do danych wejściowych z HealthState wartość Ostrzeżenie. Wartość wynosi 4.  <br> - Błąd — filtr, który pasuje do danych wejściowych z HealthState wartość Error. Wartość wynosi 8.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej wartości HealthState. Wartość to 65535. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji HealthEvent obiektów zwracanych na podstawie stanu kondycji. Możliwe wartości dla tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które pasują do filtru. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, wszystkie wpisy są zwracane. Wartości stanu są wyliczenia oparte na flagi, więc wartość może być kombinacją tych wartości, uzyskane przy użyciu bitowego "OR" operatora. Na przykład Jeśli podana wartość wynosi 6, zwracane są wszystkie zdarzenia o wartości HealthState OK (2) i Warning (4).  <br> - Domyślna — wartość domyślna. Pasuje do dowolnego HealthState. Wartość wynosi zero.  <br> - Brak — filtr, który nie pasuje do żadnej wartości HealthState. Używane w celu zwrócenia żadnych wyników w danej kolekcji stanów. Wartość wynosi 1.  <br> - Ok - Filtr, który pasuje do danych wejściowych z HealthState wartość Ok. Wartość wynosi 2.  <br> - Ostrzeżenie — filtr, który pasuje do danych wejściowych z HealthState wartość Ostrzeżenie. Wartość wynosi 4.  <br> - Błąd — filtr, który pasuje do danych wejściowych z HealthState wartość Error. Wartość wynosi 8.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej wartości HealthState. Wartość to 65535. |
| --exclude-health-statistics | Wskazuje, czy statystyki kondycji powinny być zwracane jako część wyniku kwerendy. False domyślnie. Statystyki pokazują liczbę jednostek podrzędnych w stanie kondycji Ok, Ostrzeżenie i Błąd. |
| --services-health-state-filter | Umożliwia filtrowanie obiektów stanu kondycji usług zwracanych w wyniku kwerendy kondycji usług na podstawie ich stanu kondycji. Możliwe wartości dla tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko usługi, które pasują do filtru. Wszystkie usługi są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, wszystkie wpisy są zwracane. Wartości stanu są wyliczenia oparte na flagi, więc wartość może być kombinacją tych wartości, uzyskane za pomocą bitowego "OR" operatora. Na przykład jeśli podana wartość jest 6 następnie stan kondycji usług z HealthState wartość OK (2) i ostrzeżenie (4) zostaną zwrócone.  <br> - Domyślna — wartość domyślna. Pasuje do dowolnego HealthState. Wartość wynosi zero.  <br> - Brak — filtr, który nie pasuje do żadnej wartości HealthState. Używane w celu zwrócenia żadnych wyników w danej kolekcji stanów. Wartość wynosi 1.  <br> - Ok - Filtr, który pasuje do danych wejściowych z HealthState wartość Ok. Wartość wynosi 2.  <br> - Ostrzeżenie — filtr, który pasuje do danych wejściowych z HealthState wartość Ostrzeżenie. Wartość wynosi 4.  <br> - Błąd — filtr, który pasuje do danych wejściowych z HealthState wartość Error. Wartość wynosi 8.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej wartości HealthState. Wartość to 65535. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-info"></a>informacje o aplikacji sfctl
Pobiera informacje o aplikacji sieci szkieletowej usług.

Zwraca informacje o aplikacji, która została utworzona lub w procesie tworzenia w klastrze sieci szkieletowej usług i którego nazwa odpowiada nazwie określonej jako parametr. Odpowiedź zawiera nazwę, typ, stan, parametry i inne szczegóły dotyczące aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --exclude-application-parameters | Flaga określająca, czy parametry aplikacji zostaną wykluczone z wyniku. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-list"></a>lista aplikacji sfctl
Pobiera listę aplikacji utworzonych w klastrze sieci szkieletowej usług, które pasują do określonych filtrów.

Pobiera informacje o aplikacjach, które zostały utworzone lub w procesie tworzenia w klastrze sieci szkieletowej usług i dopasować określone filtry. Odpowiedź zawiera nazwę, typ, stan, parametry i inne szczegóły dotyczące aplikacji. Jeśli aplikacje nie mieszczą się w stronie, zwracana jest jedna strona wyników, a także token kontynuacji, który może służyć do uzyskania następnej strony. Nie można określić filtrów ApplicationTypeName i ApplicationDefinitionKindFilter w tym samym czasie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-definition-kind-filter | Służy do filtrowania na ApplicationDefinitionKind, który jest mechanizm używany do definiowania aplikacji sieci szkieletowej usług.  <br> - Domyślna - Wartość domyślna, która pełni tę samą funkcję co wybranie "Wszystkie". Wartość wynosi 0.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej wartości ApplicationDefinitionKind. Wartość to 65535.  <br> - ServiceFabricApplicationDescription — filtr, który pasuje do danych wejściowych z ApplicationDefinitionKind wartość ServiceFabricApplicationDescription. Wartość wynosi 1.  <br> - Compose - Filtr, który pasuje do danych wejściowych z ApplicationDefinitionKind wartość Compose. Wartość wynosi 2. |
| --nazwa typu aplikacji | Nazwa typu aplikacji używana do filtrowania aplikacji do kwerendy. Ta wartość nie powinna zawierać wersji typu aplikacji. |
| --continuation-token | Parametr tokenu kontynuacji jest używany do uzyskania następnego zestawu wyników. Token kontynuacji z wartością niepustą jest uwzględniony w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --exclude-application-parameters | Flaga określająca, czy parametry aplikacji zostaną wykluczone z wyniku. |
| --max-wyniki | Maksymalna liczba wyników do zwrotu w ramach zapytań stronicowanych. Ten parametr definiuje górną granicę na liczbę zwracanych wyników. Zwracane wyniki mogą być mniejsze niż określone maksymalne wyniki, jeśli nie mieszczą się w wiadomości zgodnie z ograniczeniami maksymalnego rozmiaru wiadomości zdefiniowanymi w konfiguracji. Jeśli ten parametr jest zerowy lub nie określony, kwerenda stronicowana zawiera jak najwięcej wyników, które pasują do wiadomości zwrotnej. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-load"></a>obciążenie aplikacji sfctl
Pobiera informacje o załadowaniu aplikacji sieci szkieletowej usług.

Zwraca informacje o obciążeniu aplikacji, która została utworzona lub w procesie tworzenia w klastrze sieci szkieletowej usług i którego nazwa jest zgodna z nazwą określoną jako parametr. Odpowiedź zawiera nazwę, minimalne węzły, maksymalne węzły, liczbę węzłów, które aplikacja zajmuje obecnie i informacje metryki obciążenia aplikacji o aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-manifest"></a>manifest aplikacji sfctl
Pobiera manifest opisujący typ aplikacji.

Odpowiedź zawiera xml manifestu aplikacji jako ciąg.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-type-name [Wymagane] | Nazwa typu aplikacji. |
| --application-type-version [Wymagane] | Wersja typu aplikacji. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-provision"></a>udostępnianie aplikacji sfctl
Przepisy lub rejestruje typ aplikacji sieci szkieletowej usług z klastra przy użyciu pakietu ".sfpkg" w magazynie zewnętrznym lub przy użyciu pakietu aplikacji w magazynie obrazów.

Aprobuje typ aplikacji sieci szkieletowej usług z klastrem. Przepis jest wymagany przed wystąpieniem nowych aplikacji. Operację inicjowania obsługi administracyjnej można wykonać na pakiecie aplikacji określonym przez relativePathInImageStore lub przy użyciu identyfikatora URI zewnętrznego ".sfpkg". Jeśli nie jest ustawiona --external-provision, to polecenie będzie oczekiwać aprowizowania magazynu obrazów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-package-download-uri | Ścieżka do pakietu aplikacji ".sfpkg", z którego można pobrać pakiet aplikacji za pomocą protokołów HTTP lub HTTPS. <br><br> Tylko dla zapasu zewnętrznego. Pakiet aplikacji może być przechowywany w magazynie zewnętrznym, który udostępnia operację GET, aby pobrać plik. Obsługiwane protokoły to HTTP i HTTPS, a ścieżka musi zezwalać na dostęp do odczytu. |
| --application-type-build-path | Tylko dla sklepu z obrazami typu provision. Ścieżka względna dla pakietu aplikacji w magazynie obrazów określona podczas poprzedniej operacji przekazywania. |
| --nazwa typu aplikacji | Tylko dla zapasu zewnętrznego. Nazwa typu aplikacji reprezentuje nazwę typu aplikacji znajdującego się w manifeście aplikacji. |
| --application-type-version | Tylko dla zapasu zewnętrznego. Wersja typu aplikacji reprezentuje wersję typu aplikacji znajdującą się w manifeście aplikacji. |
| --zewnętrzna-zapewnienie | Lokalizacja, z której można zarejestrować lub aprowizować pakiet aplikacji. Wskazuje, że przepis dotyczy pakietu aplikacji, który został wcześniej przekazany do magazynu zewnętrznego. Pakiet aplikacji kończy się rozszerzeniem *.sfpkg. |
| --nie czekaj | Wskazuje, czy inicjowanie obsługi administracyjnej powinno odbywać się asynchronicznie. <br><br> Po ustawieniu na true, operacja inicjowania zwraca, gdy żądanie jest akceptowane przez system, a operacja inicjowania obsługi administracyjnej jest kontynuowana bez żadnych ograniczeń czasowych. Wartość domyślna to false. W przypadku dużych pakietów aplikacji zaleca się ustawienie wartości true. |
| --timeout -t | Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-report-health"></a>sfctl raport aplikacji-zdrowie
Wysyła raport kondycji w aplikacji sieci szkieletowej usług.

Raportuje stan kondycji określonej aplikacji sieci szkieletowej usług. Raport musi zawierać informacje o źródle raportu kondycji i właściwości, na których jest raport. Raport jest wysyłany do aplikacji bramy sieci szkieletowej usług, która przekazuje do magazynu kondycji. Raport może zostać zaakceptowany przez bramę, ale odrzucony przez magazyn kondycji po dodatkowej weryfikacji. Na przykład magazyn kondycji może odrzucić raport z powodu nieprawidłowego parametru, takiego jak nieświeży numer sekwencyjny. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, pobierz kondycję aplikacji i sprawdź, czy raport jest wyświetlany.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. <br><br> Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem ' . Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
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

## <a name="sfctl-application-type"></a>typ aplikacji sfctl
Pobiera listę typów aplikacji w klastrze sieci szkieletowej usług pasujące dokładnie określoną nazwę.

Zwraca informacje o typach aplikacji, które są aprowidywane lub w trakcie inicjowania obsługi administracyjnej w klastrze sieci szkieletowej usług. Wyniki te są typu aplikacji, których nazwa jest zgodna dokładnie z nazwą określoną jako parametr i które są zgodne z podanymi parametrami kwerendy. Zwracane są wszystkie wersje typu aplikacji pasujące do nazwy typu aplikacji, przy czym każda wersja zwracana jest jako jeden typ aplikacji. Odpowiedź zawiera nazwę, wersję, stan i inne szczegóły dotyczące typu aplikacji. Jest to kwerenda stronicowa, co oznacza, że jeśli nie wszystkie typy aplikacji mieszczą się na stronie, zwracana jest jedna strona wyników, a także token kontynuacji, który może służyć do uzyskania następnej strony. Na przykład jeśli istnieje 10 typów aplikacji, ale strona pasuje tylko do pierwszych trzech typów aplikacji lub jeśli max wyniki jest ustawiona na 3, a następnie trzy są zwracane. Aby uzyskać dostęp do pozostałych wyników, pobierz kolejne strony przy użyciu zwróconego tokenu kontynuacji w następnej kwerendzie. Pusty token kontynuacji jest zwracany, jeśli nie ma żadnych kolejnych stron.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-type-name [Wymagane] | Nazwa typu aplikacji. |
| --application-type-version | Wersja typu aplikacji. |
| --continuation-token | Parametr tokenu kontynuacji jest używany do uzyskania następnego zestawu wyników. Token kontynuacji z wartością niepustą jest uwzględniony w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --exclude-application-parameters | Flaga określająca, czy parametry aplikacji zostaną wykluczone z wyniku. |
| --max-wyniki | Maksymalna liczba wyników do zwrotu w ramach zapytań stronicowanych. Ten parametr definiuje górną granicę na liczbę zwracanych wyników. Zwracane wyniki mogą być mniejsze niż określone maksymalne wyniki, jeśli nie mieszczą się w wiadomości zgodnie z ograniczeniami maksymalnego rozmiaru wiadomości zdefiniowanymi w konfiguracji. Jeśli ten parametr jest zerowy lub nie określony, kwerenda stronicowana zawiera jak najwięcej wyników, które pasują do wiadomości zwrotnej. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-type-list"></a>lista typów aplikacji sfctl
Pobiera listę typów aplikacji w klastrze sieci szkieletowej usług.

Zwraca informacje o typach aplikacji, które są aprowidywane lub w trakcie inicjowania obsługi administracyjnej w klastrze sieci szkieletowej usług. Każda wersja typu aplikacji jest zwracana jako jeden typ aplikacji. Odpowiedź zawiera nazwę, wersję, stan i inne szczegóły dotyczące typu aplikacji. Jest to kwerenda stronicowa, co oznacza, że jeśli nie wszystkie typy aplikacji mieszczą się na stronie, zwracana jest jedna strona wyników, a także token kontynuacji, który może służyć do uzyskania następnej strony. Na przykład jeśli istnieje 10 typów aplikacji, ale strona pasuje tylko do pierwszych trzech typów aplikacji lub jeśli max wyniki jest ustawiona na 3, a następnie trzy są zwracane. Aby uzyskać dostęp do pozostałych wyników, pobierz kolejne strony przy użyciu zwróconego tokenu kontynuacji w następnej kwerendzie. Pusty token kontynuacji jest zwracany, jeśli nie ma żadnych kolejnych stron.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-type-definition-kind-filter | Służy do filtrowania na ApplicationTypeDefinitionKind, który jest mechanizm używany do definiowania typu aplikacji sieci szkieletowej usług.  <br> - Domyślna - Wartość domyślna, która pełni tę samą funkcję co wybranie "Wszystkie". Wartość wynosi 0.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej applicationtypedefinitionkind wartość. Wartość to 65535.  <br> - ServiceFabricApplicationPackage — filtr, który dopasowuje dane wejściowe z ApplicationTypeDefinitionKind wartość ServiceFabricApplicationPackage. Wartość wynosi 1.  <br> - Compose - Filtr, który pasuje do danych wejściowych z ApplicationTypeDefinitionKind wartość Compose. Wartość wynosi 2. |
| --continuation-token | Parametr tokenu kontynuacji jest używany do uzyskania następnego zestawu wyników. Token kontynuacji z wartością niepustą jest uwzględniony w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --exclude-application-parameters | Flaga określająca, czy parametry aplikacji zostaną wykluczone z wyniku. |
| --max-wyniki | Maksymalna liczba wyników do zwrotu w ramach zapytań stronicowanych. Ten parametr definiuje górną granicę na liczbę zwracanych wyników. Zwracane wyniki mogą być mniejsze niż określone maksymalne wyniki, jeśli nie mieszczą się w wiadomości zgodnie z ograniczeniami maksymalnego rozmiaru wiadomości zdefiniowanymi w konfiguracji. Jeśli ten parametr jest zerowy lub nie określony, kwerenda stronicowana zawiera jak najwięcej wyników, które pasują do wiadomości zwrotnej. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-unprovision"></a>nierevision aplikacji sfctl
Usuwa lub wyrejestrowyje typ aplikacji sieci szkieletowej usług z klastra.

Tę operację można wykonać tylko wtedy, gdy wszystkie wystąpienia aplikacji typu aplikacji zostały usunięte. Po wyrejestrowaniu typu aplikacji nie można utworzyć żadnych nowych wystąpień aplikacji dla tego typu aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-type-name [Wymagane] | Nazwa typu aplikacji. |
| --application-type-version [Wymagane] | Wersja typu aplikacji zgodnie z definicją w manifeście aplikacji. |
| --async-parametr | Flaga wskazująca, czy nie może wystąpić nieprzesłania obsługi administracyjnej asynchronicznie. Po ustawieniu na true, operacja unprovision zwraca, gdy żądanie jest akceptowane przez system, a operacja unprovision jest kontynuowana bez żadnych ograniczeń czasowych. Wartość domyślna to false. Jednak zaleca się ustawienie go true dla dużych pakietów aplikacji, które zostały zainicjowane. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-upgrade"></a>uaktualnienie aplikacji sfctl
Rozpoczyna uaktualnianie aplikacji w klastrze sieci szkieletowej usług.

Sprawdza poprawność podanych parametrów uaktualnienia aplikacji i rozpoczyna uaktualnianie aplikacji, jeśli parametry są prawidłowe. Należy zauważyć, że opis uaktualnienia zastępuje istniejący opis aplikacji. Oznacza to, że jeśli parametry nie są określone, istniejące parametry w aplikacjach zostaną zastąpione z pustą listą parametrów. Spowodowałoby to aplikacji przy użyciu wartości domyślnej parametrów z manifestu aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. <br><br> Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --application-version [Wymagane] | Wersja typu aplikacji docelowej (znaleziono w manifeście aplikacji) dla uaktualnienia aplikacji. |
| --parametry [Wymagane] | A JSON zakodowana lista zastępów parametrów aplikacji, które mają być stosowane podczas uaktualniania aplikacji. |
| --default-service-health-policy | Specyfikacja zakodowana w usłudze JSON zasad dotyczących kondycji używana domyślnie do oceny kondycji typu usługi. |
| --failure-action | Akcja do wykonania, gdy monitorowane uaktualnienie napotka zasady monitorowania lub naruszenia zasad kondycji. |
| --force-restart | Na siłę uruchom ponownie procesy podczas uaktualniania, nawet jeśli wersja kodu nie uległa zmianie. |
| --health-check-retry-timeout | Czas między próbami wykonywania kontroli kondycji, jeśli aplikacja lub klaster nie jest w dobrej kondycji.  Domyślna\: wartość PT0H10M0S. |
| --health-check-stable-duration | Czas, przez który aplikacja lub klaster musi pozostać w dobrej kondycji, zanim uaktualnienie przejdzie do następnej domeny uaktualnienia.  Domyślne\: PT0H2M0S. <br><br> Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. |
| --health-check-wait-duration | Czas oczekiwania po ukończeniu domeny uaktualnienia przed rozpoczęciem procesu sprawdzania kondycji.  Wartość\: domyślna 0. |
| --max-unhealthy-apps | Maksymalny dozwolony procent wyszczeconych aplikacji w złej kondycji. Reprezentowana jako liczba od 0 do 100. |
| --tryb | Tryb używany do monitorowania kondycji podczas uaktualniania stopniowego.  Domyślnie\: Niemonitorowaneaauto. |
| --replica-set-check-time-timeout -replica-set-check-timeout --replica-set-check-timeout - | Maksymalny czas blokowania przetwarzania domeny uaktualnienia i zapobiegania utracie dostępności w przypadku wystąpienia nieoczekiwanych problemów. Mierzona w sekundach. |
| --service-health-policy - | Mapa zakodowana w Usłudze JSON z zasadami kondycji typu usługi dla nazwy typu usługi. Mapa jest pusta domyślnie. |
| --timeout -t | Wartość\: domyślna 60. |
| --upgrade-domain-timeout | Czas, przez który każda domena uaktualnienia musi zostać ukończona przed wykonaniem FailureAction.  Domyślnie\: P10675199DT02H48M05.4775807S. <br><br> Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. |
| --limit czasu uaktualnienia | Czas ukończenia całego uaktualnienia przed wykonaniem FailureAction.  Domyślnie\: P10675199DT02H48M05.4775807S. <br><br> Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. |
| --warning-as-error | Wskazuje, czy ostrzeżenia są traktowane z taką samą ważnością jak błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-upgrade-resume"></a>Sfctl aktualizacja-życiorys aplikacji
Wznawia uaktualnianie aplikacji w klastrze sieci szkieletowej usług.

Wznawia niemonitorowane ręczne uaktualnianie aplikacji sieci szkieletowej usługi. Usługa Sieci szkieletowej uaktualnia jedną domenę uaktualnienia naraz. W przypadku uaktualnień ręcznych niemonitorowanych po zakończeniu domeny uaktualnienia sieci szkieletowej usługi czeka na wywołanie tego interfejsu API przed przejściem do następnej domeny uaktualnienia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --upgrade-domain-name [Wymagane] | Nazwa domeny uaktualnienia, w której ma zostać wznowione uaktualnienie. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl upgrade-rollback aplikacji
Rozpoczyna wycofywanie aktualnie trwa uaktualnianie aplikacji w klastrze sieci szkieletowej usług.

Rozpoczyna wycofywanie bieżącego uaktualnienia aplikacji do poprzedniej wersji. Ten interfejs API może służyć tylko do wycofania bieżącego uaktualnienia w toku, który jest toczenia do przodu do nowej wersji. Jeśli aplikacja nie jest obecnie uaktualniana, użyj interfejsu API StartApplicationUpgrade, aby uaktualnić go do żądanej wersji, w tym wycofywanie do poprzedniej wersji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-upgrade-status"></a>Stan uaktualnienia aplikacji sfctl
Pobiera szczegółowe informacje na temat najnowszego uaktualnienia wykonywanego w tej aplikacji.

Zwraca informacje o stanie najnowszego uaktualnienia aplikacji wraz ze szczegółami, aby pomóc debugowania problemów ze zdrowiem aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-upload"></a>przekazywanie aplikacji sfctl
Skopiuj pakiet aplikacji sieci szkieletowej usług do magazynu obrazów.

Opcjonalnie wyświetl postęp przekazywania dla każdego pliku w pakiecie. Postęp przesyłania jest `stderr`wysyłany do pliku .

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --path [Wymagane] | Ścieżka do lokalnego pakietu aplikacji. |
| --kompres | Dotyczy tylko pakietów aplikacji sieci szkieletowej usług. Utwórz nowy folder zawierający skompresowany pakiet aplikacji do lokalizacji domyślnej lub do lokalizacji określonej przez parametr skompresowanej lokalizacji, a następnie przekaż nowo utworzony folder. <br><br> Jeśli istnieje już skompresowany plik wygenerowany przez sfctl, zostanie zastąpiony, jeśli ta flaga jest ustawiona. Błąd zostanie zwrócony, jeśli katalog nie jest pakietem aplikacji. Jeśli jest to już skompresowany pakiet aplikacji, folder zostanie skopiowany w stanie takim, w jakim jest. Domyślnie nowo utworzony pakiet skompresowanej aplikacji zostanie usunięty po pomyślnym przekazaniu. Jeśli przesyłanie nie powiedzie się, w razie potrzeby ręcznie oczyść skompresowany pakiet. Usunięcie nie usuwa żadnych pustych rejestratorów, które mogły zostać utworzone, jeśli skompresowany parametr lokalizacji odwołuje się do nieistniejących katalogów. |
| --skompresowana lokalizacja | Lokalizacja, aby umieścić pakiet aplikacji skompresowanej. <br><br> Jeśli nie podano lokalizacji, skompresowany pakiet zostanie umieszczony pod nowo utworzonym folderem o nazwie sfctl_compressed_temp w katalogu nadrzędnym określonym w argurze path. Na przykład jeśli argument path\:ma wartość C /FolderA/AppPkg, skompresowany pakiet zostanie dodany do C\:/FolderA/sfctl_compressed_temp/AppPkg. |
| --imagestore-string | Docelowy magazyn obrazów, do aby przekazać pakiet aplikacji.  \: Domyślny\:magazyn obrazów sieci szkieletowej. <br><br> Aby przesłać do lokalizacji pliku, uruchom\:ten parametr za pomocą 'file'. W przeciwnym razie wartość powinna być ciąg połączenia magazynu obrazów, takich jak wartość domyślna. |
| --keep-skompresowany | Określa, czy chcesz zachować wygenerowany skompresowany pakiet po pomyślnym zakończeniu przekazywania. <br><br> Jeśli nie zostanie ustawiona, po pomyślnym zakończeniu skompresowane pakiety aplikacji zostaną usunięte. Jeśli przekazywanie nie powiodło się, pakiet aplikacji będzie zawsze przechowywany w katalogu wyjściowym do ponownego przekazania. |
| --show-postęp | Pokaż postęp przekazywania plików dla dużych pakietów. |
| --timeout -t | Całkowity limit czasu w sekundach. Przekazywanie zakończy się niepowodzeniem i zwróci błąd po upływie limitu czasu przekazywania. Ten limit czasu ma zastosowanie do całego pakietu aplikacji, a poszczególne limity czasu pliku będą równe pozostałemu czasowi limitu czasu. Limit czasu nie obejmuje czasu wymaganego do skompresować pakiet aplikacji.  Wartość\: domyślna 300. |

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
