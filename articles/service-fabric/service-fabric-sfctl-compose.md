---
title: Interfejs wiersza polecenia platformy Azure Service Fabric sfctl Microsoft Docs
description: Opisuje polecenie sfctl polecenia tworzenia Service Fabric w programie.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: dab844246d99b0ab80e1e86219c2064c79e74e4f
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035111"
---
# <a name="sfctl-compose"></a>sfctl compose
Twórz i usuwaj Docker Compose aplikacje oraz zarządzaj nimi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| tworzenie | Tworzy wdrożenie Service Fabric tworzenia. |
| list | Pobiera listę wdrożeń tworzenia utworzonych w klastrze Service Fabric. |
| usuń | Usuwa istniejące Service Fabric redagowanie wdrożenia z klastra. |
| status | Pobiera informacje o rozmieszczeniu tworzenia Service Fabric. |
| upgrade | Zaczyna uaktualniać wdrożenie redagowania w klastrze Service Fabric. |
| uaktualnienie — wycofanie | Uruchamia wycofywanie uaktualnienia redagowania wdrożenia w klastrze Service Fabric. |
| uaktualnienie — stan | Pobiera szczegóły dotyczące najnowszego uaktualnienia wykonywanego na tym Service Fabric redagowanie wdrożenia. |

## <a name="sfctl-compose-create"></a>Tworzenie sfctl Utwórz
Tworzy wdrożenie Service Fabric tworzenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Deployment-Name [wymagane] | Nazwa wdrożenia. |
| --ścieżka pliku [wymagana] | Ścieżka do docelowego pliku Docker Compose. |
| --Encrypted-Pass | Zamiast monitowania o hasło rejestru kontenera, należy użyć już zaszyfrowanego frazy. |
| --has-pass | Zostanie wyświetlony monit o podanie hasła do rejestru kontenerów. |
| --timeout-t | Limit czasu serwera (w sekundach).  Wartość\: domyślna 60. |
| --user | Nazwa użytkownika, aby połączyć się z rejestrem kontenerów. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-list"></a>Lista redagowania sfctl
Pobiera listę wdrożeń tworzenia utworzonych w klastrze Service Fabric.

Pobiera stan utworzonych wdrożeń tworzenia lub w procesie tworzonym w klastrze Service Fabric. Odpowiedź zawiera nazwę, stan i inne szczegóły dotyczące wdrożeń redagowania. Jeśli lista wdrożeń nie mieści się na stronie, zwracana jest jedna strona wyników, a także token kontynuacji, który może służyć do uzyskania następnej strony.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Kontynuacja — token | Parametr tokenu kontynuacji służy do uzyskiwania następnego zestawu wyników. Token kontynuacji z niepustą wartością jest dołączany do odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przenoszona do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --Max-Results | Maksymalna liczba wyników do zwrócenia w ramach zapytań stronicowanych. Ten parametr definiuje górną granicę wartości zwracanych wyników. Zwracane wyniki mogą być mniejsze niż określone maksymalne wyniki, jeśli nie mieszczą się w komunikacie zgodnie z maksymalnymi ograniczeniami rozmiaru komunikatów zdefiniowanymi w konfiguracji. Jeśli ten parametr ma wartość zero lub nie zostanie określony, zapytanie stronicowane zawiera tyle wyników, ile to możliwe, które mieszczą się w komunikacie zwrotnym. |
| --timeout-t | Limit czasu serwera (w sekundach).  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-remove"></a>Usuwanie sfctl
Usuwa istniejące Service Fabric redagowanie wdrożenia z klastra.

Usuwa istniejące wdrożenie Service Fabric tworzenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Deployment-Name [wymagane] | Tożsamość wdrożenia. |
| --timeout-t | Limit czasu serwera (w sekundach).  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-status"></a>stan redagowania sfctl
Pobiera informacje o rozmieszczeniu tworzenia Service Fabric.

Zwraca stan utworzonego wdrożenia tworzenia lub w procesie tworzenia w klastrze Service Fabric, którego nazwa jest zgodna z nazwą określoną jako parametr. Odpowiedź zawiera nazwę, stan i inne szczegóły dotyczące wdrożenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Deployment-Name [wymagane] | Tożsamość wdrożenia. |
| --timeout-t | Limit czasu serwera (w sekundach).  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-upgrade"></a>sfctl — uaktualnienie
Zaczyna uaktualniać wdrożenie redagowania w klastrze Service Fabric.

Sprawdza poprawność podanych parametrów uaktualnienia i zaczyna uaktualniać wdrożenie, jeśli parametry są prawidłowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Deployment-Name [wymagane] | Nazwa wdrożenia. |
| --ścieżka pliku [wymagana] | Ścieżka do docelowego pliku redagowania platformy Docker. |
| --default-svc-type-health-map | Zakodowany słownik JSON, który opisuje zasady dotyczące kondycji używane do oszacowania kondycji usług. |
| --Encrypted-Pass | Zamiast monitowania o hasło rejestru kontenera, należy użyć już zaszyfrowanego frazy. |
| --Niepowodzenie-akcja | Możliwe wartości to\: "nieprawidłowe", "Wycofaj", "ręczne". |
| --force-restart | Procesy są wymuszane ponownie po uaktualnieniu, nawet gdy wersja kodu nie została zmieniona. <br><br> Uaktualnienie zmienia tylko konfigurację lub dane. |
| --has-pass | Zostanie wyświetlony monit o podanie hasła do rejestru kontenerów. |
| --Health-Check-retry | Czas między próbami sprawdzenia kondycji, jeśli aplikacja lub klaster nie są w dobrej kondycji. |
| --health-check-stable | Ilość czasu, przez jaką aplikacja lub klaster muszą pozostawać w dobrej kondycji, zanim uaktualnienie przejdzie do następnej domeny uaktualnienia. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --health-check-wait | Czas oczekiwania po zakończeniu domeny uaktualnienia przed rozpoczęciem procesu kontroli kondycji. |
| --replica-set-check | Maksymalny czas blokowania przetwarzania domeny uaktualnienia i zapobiegania utracie dostępności w przypadku wystąpienia nieoczekiwanych problemów. <br><br> Po upływie tego limitu czasu przetwarzanie domeny uaktualnienia będzie przebiegać bez względu na problemy z utratą dostępności. Limit czasu jest resetowany na początku każdej domeny uaktualnienia. Prawidłowe wartości należą do zakresu od 0 do 42949672925 włącznie. |
| --svc-type-health-map | Zakodowana w formacie JSON lista obiektów, które opisują zasady dotyczące kondycji używane do obliczania kondycji różnych typów usług. |
| --timeout-t | Limit czasu serwera (w sekundach).  Wartość\: domyślna 60. |
| --unhealthy-app | Maksymalny dozwolony procent aplikacji w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład, aby umożliwić złej kondycji 10% aplikacji, ta wartość będzie równa 10. Wartość procentowa reprezentuje maksymalny dopuszczalny procent aplikacji, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jedna aplikacja w złej kondycji, kondycja jest szacowana jako ostrzeżenie. Jest to obliczane przez podzielenie liczby aplikacji w złej kondycji na łączną liczbę wystąpień aplikacji w klastrze. |
| --Upgrade-Domain-timeout | Czas, przez jaki każda domena uaktualnienia musi zakończyć pracę przed wykonaniem FailureAction. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --rodzaj uaktualnienia | Domyślne\: Przetaczanie. |
| --Tryb uaktualnienia | Możliwe wartości to\: "nieprawidłowe", "UnmonitoredAuto", "UnmonitoredManual", "monitorowane".  Domyślny\: UnmonitoredAuto. |
| --Upgrade-timeout | Czas, przez jaki całkowite uaktualnienie musi zakończyć się przed wykonaniem FailureAction. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --user | Nazwa użytkownika, aby połączyć się z rejestrem kontenerów. |
| --warning-as-error | Wskazuje, czy ostrzeżenia są traktowane z taką samą ważnością jak błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl redagowania — wycofywanie
Uruchamia wycofywanie uaktualnienia redagowania wdrożenia w klastrze Service Fabric.

Wycofywanie uaktualnienia tworzenia i wdrażania usługi Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Deployment-Name [wymagane] | Tożsamość wdrożenia. |
| --timeout-t | Limit czasu serwera (w sekundach).  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl redagowania — stan
Pobiera szczegóły dotyczące najnowszego uaktualnienia wykonywanego na tym Service Fabric redagowanie wdrożenia.

Zwraca informacje o stanie uaktualnienia redagowania wdrożenia wraz ze szczegółami dotyczącymi pomocy w debugowaniu problemów z kondycją aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Deployment-Name [wymagane] | Tożsamość wdrożenia. |
| --timeout-t | Limit czasu serwera (w sekundach).  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).