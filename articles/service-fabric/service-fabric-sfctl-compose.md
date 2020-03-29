---
title: Sieci szkieletowej usługi Azure — sfctl — komponowanie
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń dla aplikacji dokowych redagowania.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1e40ca4e3c5ec8b7566646aa7ef723bd4c9e45a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906130"
---
# <a name="sfctl-compose"></a>sfctl compose
Tworzenie, usuwanie i zarządzanie aplikacjami docker compose.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| create | Tworzy wdrożenie redagowania sieci szkieletowej usług. |
| list | Pobiera listę wdrożeń redagowania utworzonych w klastrze sieci szkieletowej usług. |
| remove | Usuwa istniejące wdrożenie sieci szkieletowej usług z klastra. |
| status | Pobiera informacje o wdrożeniu redagowania sieci szkieletowej usług. |
| uaktualnienie | Rozpoczyna uaktualnianie wdrożenia redagowania w klastrze sieci szkieletowej usług. |
| upgrade-rollback | Rozpoczyna wycofywanie uaktualnienia wdrażania kompozycji w klastrze sieci szkieletowej usług. |
| stan uaktualnienia | Pobiera szczegółowe informacje dotyczące najnowszego uaktualnienia przeprowadzonego w tym wdrożeniu redagowania sieci szkieletowej usług. |

## <a name="sfctl-compose-create"></a>sfctl skomponuj tworzenie
Tworzy wdrożenie redagowania sieci szkieletowej usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --deployment-name [Wymagane] | Nazwa wdrożenia. |
| --file-path [Wymagane] | Ścieżka do docelowego pliku docker compose. |
| --encrypted-pass | Zamiast monitować o hasło rejestru kontenera, użyj już zaszyfrowanej frazy pass. |
| --has-pass | Wyświetli monit o podanie hasła do rejestru kontenerów. |
| --timeout -t | Wartość\: domyślna 60. |
| --user | Nazwa użytkownika do łączenia się z rejestrem kontenerów. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-list"></a>lista sfctl compose
Pobiera listę wdrożeń redagowania utworzonych w klastrze sieci szkieletowej usług.

Pobiera stan o wdrożeń redagowania, które zostały utworzone lub w trakcie tworzenia w klastrze sieci szkieletowej usług. Odpowiedź zawiera nazwę, stan i inne szczegóły dotyczące wdrożeń redagowania. Jeśli lista wdrożeń nie mieści się na stronie, zwracana jest jedna strona wyników, a także token kontynuacji, który może służyć do uzyskania następnej strony.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --continuation-token | Parametr tokenu kontynuacji jest używany do uzyskania następnego zestawu wyników. Token kontynuacji z wartością niepustą jest uwzględniony w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
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

## <a name="sfctl-compose-remove"></a>sfctl skomponuj usunąć
Usuwa istniejące wdrożenie sieci szkieletowej usług z klastra.

Usuwa istniejące wdrożenie redagowania sieci szkieletowej usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --deployment-name [Wymagane] | Tożsamość wdrożenia. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-status"></a>sfctl stan kompozycji
Pobiera informacje o wdrożeniu redagowania sieci szkieletowej usług.

Zwraca stan wdrożenia redagowania, które zostało utworzone lub w procesie tworzenia w klastrze sieci szkieletowej usług i którego nazwa odpowiada nazwie określonej jako parametr. Odpowiedź zawiera nazwę, stan i inne szczegóły dotyczące wdrożenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --deployment-name [Wymagane] | Tożsamość wdrożenia. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-upgrade"></a>uaktualnienie sfctl compose
Rozpoczyna uaktualnianie wdrożenia redagowania w klastrze sieci szkieletowej usług.

Sprawdza poprawność podanych parametrów uaktualnienia i rozpoczyna uaktualnianie wdrożenia, jeśli parametry są prawidłowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --deployment-name [Wymagane] | Nazwa wdrożenia. |
| --file-path [Wymagane] | Ścieżka do docelowego pliku komponowania platformy Docker. |
| --default-svc-type-health-map | Słownik zakodowany w u usług JSON, który opisuje zasady kondycji używane do oceny kondycji usług. |
| --encrypted-pass | Zamiast monitować o hasło rejestru kontenera, użyj już zaszyfrowanej frazy pass. |
| --failure-action | Możliwe wartości\: to "Nieprawidłowe", "Wycofywanie", "Podręcznik". |
| --force-restart | Procesy są na siłę uruchamiane podczas uaktualniania, nawet jeśli wersja kodu nie uległa zmianie. <br><br> Uaktualnienie zmienia tylko konfigurację lub dane. |
| --has-pass | Wyświetli monit o podanie hasła do rejestru kontenerów. |
| --health-check-retry | Czas między próbami wykonywania kontroli kondycji, jeśli aplikacja lub klaster nie jest w dobrej kondycji. |
| --health-check-stable | Czas, przez który aplikacja lub klaster musi pozostać w dobrej kondycji, zanim uaktualnienie przejdzie do następnej domeny uaktualnienia. <br><br> Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. |
| --health-check-wait | Czas oczekiwania po ukończeniu domeny uaktualnienia przed rozpoczęciem procesu sprawdzania kondycji. |
| --replica-set-check | Maksymalny czas blokowania przetwarzania domeny uaktualnienia i zapobiegania utracie dostępności w przypadku wystąpienia nieoczekiwanych problemów. <br><br> Po upływie tego limitu czasu przetwarzanie domeny uaktualnienia będzie kontynuowane niezależnie od problemów z utratą dostępności. Limit czasu jest resetowany na początku każdej domeny uaktualnienia. Prawidłowe wartości są między 0 i 42949672925 włącznie. |
| --svc-type-health-map | Zakodowana w Usłudze JSON lista obiektów, które opisują zasady kondycji używane do oceny kondycji różnych typów usług. |
| --timeout -t | Wartość\: domyślna 60. |
| --unhealthy-app | Maksymalny dozwolony procent aplikacji w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład, aby umożliwić 10% aplikacji w złej kondycji, ta wartość będzie 10. Wartość procentowa reprezentuje maksymalny tolerowany procent aplikacji, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jedna aplikacja w złej kondycji, kondycja jest oceniana jako Ostrzeżenie. Jest to obliczane przez podzielenie liczby aplikacji w złej kondycji na całkowitą liczbę wystąpień aplikacji w klastrze. |
| --upgrade-domain-timeout | Czas, przez który każda domena uaktualnienia musi zostać ukończona przed wykonaniem FailureAction. <br><br> Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. |
| --upgrade-rodzaju | Domyślne\: walcowanie. |
| --upgrade-mode | Możliwe wartości\: to "Nieprawidłowe", "NiemonitorowaneAuto", "NiemonitorowaneManualne", "Monitorowane".  Domyślnie\: Niemonitorowaneaauto. |
| --limit czasu uaktualnienia | Czas ukończenia całego uaktualnienia przed wykonaniem FailureAction. <br><br> Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. |
| --user | Nazwa użytkownika do łączenia się z rejestrem kontenerów. |
| --warning-as-error | Wskazuje, czy ostrzeżenia są traktowane z taką samą ważnością jak błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl komponować upgrade-rollback
Rozpoczyna wycofywanie uaktualnienia wdrażania kompozycji w klastrze sieci szkieletowej usług.

Wycofywanie uaktualnienia wdrażania sieci szkieletowej usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --deployment-name [Wymagane] | Tożsamość wdrożenia. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl skomponować stan uaktualnienia
Pobiera szczegółowe informacje dotyczące najnowszego uaktualnienia przeprowadzonego w tym wdrożeniu redagowania sieci szkieletowej usług.

Zwraca informacje o stanie uaktualnienia wdrażania komponowania wraz ze szczegółami, aby pomóc debugowania problemów ze zdrowiem aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --deployment-name [Wymagane] | Tożsamość wdrożenia. |
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