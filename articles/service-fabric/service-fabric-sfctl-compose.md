---
title: Azure interfejsu wiersza polecenia usługi Service Fabric - sfctl compose | Dokumentacja firmy Microsoft
description: W tym artykule opisano interfejs wiersza polecenia usługi Service Fabric sfctl compose poleceń.
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
ms.openlocfilehash: 4b5cbb4a24b61de7e64a52ef950deedab3eec263
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837270"
---
# <a name="sfctl-compose"></a>sfctl compose
Tworzenie, usuwanie i zarządzać aplikacjami narzędzia Docker Compose.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| create | Tworzy usługi Service Fabric wdrożenia redagowania. |
| list | Pobiera listę tworzą wdrożeń utworzonych w klastrze usługi Service Fabric. |
| usuń | Usuwa istniejące usługi Service Fabric tworzą wdrożenia z klastra. |
| status | Wdrożenie redagowania pobiera informacje o usłudze Service Fabric. |
| uaktualnij | Rozpoczyna się uaktualnianie wdrożenia compose w klastrze usługi Service Fabric. |
| upgrade-rollback | Wycofywanie wdrożenia compose rozpoczyna się uaktualnienie z klastra usługi Service Fabric. |
| upgrade-status | Wdrożenie redagowania pobiera szczegóły najnowszą aktualizację, które są wykonywane w tej usłudze Service Fabric. |

## <a name="sfctl-compose-create"></a>Interfejs sfctl compose tworzenie
Tworzy usługi Service Fabric wdrożenia redagowania.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa wdrożenia | Nazwa wdrożenia. |
| — Ścieżka pliku [wymagane] | Ścieżka do pliku docelowego narzędzia Docker Compose. |
| --encrypted-pass | Zamiast monitowania o hasło rejestru kontenerów, użyj już zaszyfrowane hasło. |
| --has-pass | Wyświetli monit o podanie hasła do rejestru kontenerów. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |
| --user | Nazwa użytkownika, aby nawiązać połączenie z rejestru kontenerów. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-compose-list"></a>Interfejs sfctl compose list
Pobiera listę tworzą wdrożeń utworzonych w klastrze usługi Service Fabric.

Pobiera stan o wdrożeniach compose, które zostały utworzone, lub trwa proces tworzonych w klastrze usługi Service Fabric. Odpowiedź zawiera nazwę, stan i inne szczegóły dotyczące wdrożenia compose. Jeśli lista wdrożeń nie mieszczą się na stronie, jednej stronie wyników jest zwracany oraz token kontynuacji, którego można uzyskać następnej strony.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --token kontynuacji | Parametr tokenu kontynuacji służy do uzyskania następny zestaw wyników. Token kontynuacji o wartości niepuste znajduje się w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie istnieją żadne dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| --max-results | Maksymalna liczba wyników do zwrócenia w ramach kwerend stronicowanych. Ten parametr określa górną granicę na liczbie zwrócone wyniki. Wyniki zwracane, może być mniejsza niż określona maksymalna liczba wyników, jeśli nie mieszczą się w komunikacie zgodnie z ograniczeniami rozmiar maksymalny komunikatu zdefiniowane w konfiguracji. Jeśli ten parametr ma wartość zero lub nie określono, stronicowane zapytanie zawiera dowolną liczbę wyników jak to możliwe, który mieści się w komunikacie zwrotu. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-compose-remove"></a>Interfejs sfctl compose remove
Usuwa istniejące usługi Service Fabric tworzą wdrożenia z klastra.

Wdrożenie redagowania usuwa istniejące usługi Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa wdrożenia | Tożsamość wdrożenia. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-compose-status"></a>Interfejs sfctl compose status
Wdrożenie redagowania pobiera informacje o usłudze Service Fabric.

Zwraca stan wdrożenia compose, który został utworzony lub w trakcie tworzonych w klaster usługi Service Fabric, którego nazwa odpowiada dokumentem określonym jako parametr. Odpowiedź zawiera nazwę, stan i inne szczegóły dotyczące wdrożenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa wdrożenia | Tożsamość wdrożenia. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-compose-upgrade"></a>Interfejs sfctl compose uaktualnienia
Rozpoczyna się uaktualnianie wdrożenia compose w klastrze usługi Service Fabric.

Sprawdza poprawność podanych parametrów uaktualniania i rozpoczyna uaktualnianie wdrożenia, jeśli parametry są prawidłowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa wdrożenia | Nazwa wdrożenia. |
| — Ścieżka pliku [wymagane] | Ścieżka do obiektu docelowego platformy Docker compose pliku. |
| --default-svc-type-health-map | Słownik, który opisano zasad dotyczących kondycji używane do oceny kondycji usług zakodowane JSON. |
| --encrypted-pass | Zamiast monitowania o hasło rejestru kontenerów, użyj już zaszyfrowane hasło. |
| — Akcja błędu | Możliwe wartości to\: "Nieprawidłowy", "Wycofać", "Manual". |
| --force-restart | Procesy wymuszone zostaną ponownie uruchomione podczas uaktualniania, nawet wtedy, gdy wersja kodu nie została zmieniona. <br><br> Uaktualnianie tylko zmiany konfiguracji lub danych. |
| --has-pass | Wyświetli monit o podanie hasła do rejestru kontenerów. |
| --health-check-retry | Odstęp czasu między próbami sprawdzać kondycję, jeśli aplikacja lub klastra nie jest w dobrej kondycji. |
| --health-check-stable | Ilość czasu, aplikacji lub klastra musi pozostać dobrej kondycji przed uaktualnienia przechodzi do następnej domeny uaktualnienia. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. |
| --health-check-wait | Czas oczekiwania po wykonaniu uaktualnienia domeny przed rozpoczęciem kondycji sprawdza, czy proces. |
| --replica-set-check | Maksymalna ilość czasu blokowania przetwarzania domeny uaktualnienia, a przed utratą dostępności w przypadku nieoczekiwanych problemów. <br><br> Po wygaśnięciu tego limitu czasu przetwarzania przez domenę uaktualnienia będzie kontynuowana bez względu na to, problemy z dostępnością utraty. Limit czasu jest resetowany na początku każdej z domen. Prawidłowe wartości należą do zakresu od 0 i 42949672925 (włącznie). |
| --svc-type-health-map | Listę obiektów, które opisują zasady dotyczące kondycji używane do oceny kondycji różne typy usług zakodowane JSON. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |
| --unhealthy-app | Maksymalna dozwolona wartość procentowa aplikacje w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład aby zezwolić na 10% aplikacje będącą w złej kondycji, wartość ta wynosi 10. Wartość procentowa reprezentuje maksymalny procent tolerowana aplikacje, które mogą być złej kondycji, zanim klaster zostanie uznane za błąd. Jeśli wartość procentowa jest zachowana, ale istnieje co najmniej jednej aplikacji w złej kondycji, kondycji jest oceniane jako ostrzeżenie. To jest obliczana przez podzielenie liczby aplikacji w złej kondycji przez całkowitą liczbę wystąpień aplikacji w klastrze. |
| --upgrade-domain-timeout | Czas każdej z domen musi wykonać, zanim zostanie wykonany FailureAction. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. |
| --upgrade-kind | Domyślne\: stopniowe. |
| --Tryb uaktualniania | Możliwe wartości to\: "Nieprawidłowy", "UnmonitoredAuto", "UnmonitoredManual", "Monitorowania".  Domyślne\: UnmonitoredAuto. |
| — limit czasu uaktualniania | Czas ogólną uaktualnienia musi wykonać, zanim zostanie wykonany FailureAction. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. |
| --user | Nazwa użytkownika, aby nawiązać połączenie z rejestru kontenerów. |
| --warning-as-error | Wskazuje, czy ostrzeżenia są traktowane z tego samego ważność jako błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-compose-upgrade-rollback"></a>Interfejs sfctl compose wycofywanie uaktualnienia
Wycofywanie wdrożenia compose rozpoczyna się uaktualnienie z klastra usługi Service Fabric.

Wycofanie usługi Service fabric tworzą uaktualnianie wdrożenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa wdrożenia | Tożsamość wdrożenia. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-compose-upgrade-status"></a>Interfejs sfctl compose stan uaktualnienia
Wdrożenie redagowania pobiera szczegóły najnowszą aktualizację, które są wykonywane w tej usłudze Service Fabric.

Zwraca informacje o stanie uaktualnianie wdrożenia compose wraz ze szczegółami ułatwiające debugowanie problemów z kondycją aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa wdrożenia | Tożsamość wdrożenia. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |


## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi Service Fabric.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi Service Fabric [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).