---
title: Azure interfejsu wiersza polecenia usługi Service Fabric - sfctl compose | Dokumentacja firmy Microsoft
description: W tym artykule opisano interfejs wiersza polecenia usługi Service Fabric sfctl compose poleceń.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 3ce0b63c579412d9d8d35b835803becab09f7ef4
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494156"
---
# <a name="sfctl-compose"></a>sfctl compose
Tworzenie, usuwanie i zarządzać aplikacjami narzędzia Docker Compose.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| create | Tworzy usługi Service Fabric wdrożenia redagowania. |
| lista | Pobiera listę tworzą wdrożeń utworzonych w klastrze usługi Service Fabric. |
| usuń | Usuwa istniejące usługi Service Fabric tworzą wdrożenia z klastra. |
| status | Wdrożenie redagowania pobiera informacje o usłudze Service Fabric. |
| uaktualnij | Rozpoczyna się uaktualnianie wdrożenia compose w klastrze usługi Service Fabric. |
| Stan uaktualnienia | Wdrożenie redagowania pobiera szczegóły najnowszą aktualizację, które są wykonywane w tej usłudze Service Fabric. |

## <a name="sfctl-compose-create"></a>Interfejs sfctl compose tworzenie
Tworzy usługi Service Fabric wdrożenia redagowania.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa wdrożenia | Nazwa wdrożenia. |
| — Ścieżka pliku [wymagane] | Ścieżka do pliku docelowego narzędzia Docker Compose. |
| --szyfrowane — dostęp próbny | Zamiast monitowania o hasło rejestru kontenerów, użyj już zaszyfrowane hasło. |
| — zawiera — dostęp próbny | Wyświetli monit o podanie hasła do rejestru kontenerów. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |
| --user | Nazwa użytkownika, aby nawiązać połączenie z rejestru kontenerów. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
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
| — Maksymalna liczba wyników | Maksymalna liczba wyników do zwrócenia w ramach kwerend stronicowanych. Ten parametr określa górną granicę na liczbie zwrócone wyniki. Wyniki zwracane, może być mniejsza niż określona maksymalna liczba wyników, jeśli nie mieszczą się w komunikacie zgodnie z ograniczeniami rozmiar maksymalny komunikatu zdefiniowane w konfiguracji. Jeśli ten parametr ma wartość zero lub nie określono, stronicowane zapytanie zawiera dowolną liczbę wyników jak to możliwe, który mieści się w komunikacie zwrotu. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
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
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
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
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
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
| — Domyślna svc typ kondycji map | Słownik, który opisuje zasad dotyczących kondycji używane do oceny kondycji usług zakodowane JSON. |
| --szyfrowane — dostęp próbny | Zamiast monitowania o hasło rejestru kontenerów, użyj już zaszyfrowane hasło. |
| — Akcja błędu | Możliwe wartości to\: "Nieprawidłowy", "Wycofać", "Manual". |
| --force-restart | Wymuś ponowne uruchomienie. |
| — zawiera — dostęp próbny | Wyświetli monit o podanie hasła do rejestru kontenerów. |
| --ponownej próby sprawdzenia kondycji | Limit czasu ponawiania sprawdzania kondycji jest mierzony w milisekundach. |
| --health-check-stable | Sprawdzanie kondycji stabilne czasu trwania w milisekundach. |
| --health-check-wait | Czas oczekiwania sprawdzania kondycji jest mierzony w milisekundach. |
| — Sprawdzanie w przypadku zestawu replik | Repliki uaktualniania ustawić wyboru limitu czasu mierzony w sekundach. |
| -svc-typu kondycji map | Listę obiektów, które opisują zasady dotyczące kondycji używane do oceny kondycji różne typy usług zakodowane JSON. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |
| --unhealthy-app | Maksymalna dozwolona wartość procentowa aplikacje w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład aby zezwolić na 10% aplikacje będącą w złej kondycji, wartość ta wynosi 10. Wartość procentowa reprezentuje maksymalny procent tolerowana aplikacje, które mogą być złej kondycji, zanim klaster zostanie uznane za błąd. Jeśli wartość procentowa jest zachowana, ale istnieje co najmniej jednej aplikacji w złej kondycji, kondycji jest oceniane jako ostrzeżenie. To jest obliczana przez podzielenie liczby aplikacji w złej kondycji przez całkowitą liczbę wystąpień aplikacji w klastrze. |
| — limit czasu domeny uaktualnienia | Limit czasu domeny uaktualnień mierzony w milisekundach. |
| — typ uaktualnienia | Domyślne\: stopniowe. |
| --Tryb uaktualniania | Możliwe wartości to\: "Nieprawidłowy", "UnmonitoredAuto", "UnmonitoredManual", "Monitorowania".  Domyślne\: UnmonitoredAuto. |
| — limit czasu uaktualniania | Limit czasu uaktualniania mierzony w milisekundach. |
| --user | Nazwa użytkownika, aby nawiązać połączenie z rejestru kontenerów. |
| --warning-as-error | Ostrzeżenia są traktowane z tego samego ważność jako błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
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
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |


## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi Service Fabric.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi Service Fabric [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).