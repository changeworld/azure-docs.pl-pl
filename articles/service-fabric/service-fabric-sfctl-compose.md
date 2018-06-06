---
title: Redagowanie Azure Service Fabric interfejsu wiersza polecenia - sfctl | Dokumentacja firmy Microsoft
description: Zawiera opis usługi sieci szkieletowej interfejsu wiersza polecenia sfctl tworzą poleceń.
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
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: cc3d3e35ce3dd457d981dfe9420be765cf9fc45a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763412"
---
# <a name="sfctl-compose"></a>sfctl compose
Tworzenie, usuwanie i zarządzania aplikacjami rozwiązania Docker Compose.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| create | Tworzy usługi sieć szkieletowa tworzą wdrożenia. |
| lista | Pobiera listę tworzą wdrożeń utworzonych w klastrze usługi sieć szkieletowa usług. |
| usuń | Usuwa istniejące sieci szkieletowej usług utworzyć wdrożenia z klastra. |
| status | Pobiera informacje o sieci szkieletowej usług utworzyć wdrożenia. |
| Uaktualnienie | Uruchamia uaktualniania wdrożenia tworzenia klastra usługi sieć szkieletowa usług. |
| Stan uaktualnienia | Pobiera szczegóły dla najnowszą aktualizację w tej sieci szkieletowej usług utworzyć wdrożenia. |

## <a name="sfctl-compose-create"></a>Redagowanie sfctl tworzenie
Tworzy usługi sieć szkieletowa tworzą wdrożenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa wdrożenia [wymagane] | Nazwa wdrożenia. |
| — Ścieżka pliku [wymagane] | Ścieżka do pliku rozwiązania Docker Compose docelowego. |
| --szyfrowane przebiegu | Zamiast monitowania o podanie hasła rejestru kontener, użyj już zaszyfrowane hasło. |
| --ma przebiegu | Wyświetli monit o podanie hasła do rejestru kontenera. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |
| --user | Nazwa użytkownika do połączenia się z rejestrem kontenera. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-compose-list"></a>sfctl utworzenie listy
Pobiera listę tworzą wdrożeń utworzonych w klastrze usługi sieć szkieletowa usług.

Pobiera stan o wdrożeniach Redaguj, które zostały utworzone lub właśnie trwa tworzenie klastra sieci szkieletowej usług. Odpowiedź zawiera nazwy, stanu i inne szczegółowe informacje o wdrożeniach tworzenia. W przypadku listy wdrożeń nie mieści się na stronie, co strony wyników jest zwracana oraz token kontynuacji, który może służyć do pobrania następnej strony.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --token kontynuacji | Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z niepustą wartość jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| — Maksymalna liczba wyników | Maksymalna liczba wyników ma zostać zwrócona w ramach kwerend stronicowanych. Ten parametr określa górną granicę na liczbę wyników zwracanych. Wyniki zwrócone, może być mniejsza niż określona maksymalna liczba wyników, jeśli nie mieszczą się w komunikacie zgodnie z ograniczeniami rozmiar maksymalny komunikatu zdefiniowany w konfiguracji. Jeśli ten parametr jest zerowy lub nie jest określony, stronicowane zapytanie zawiera dowolną liczbę wyników jak to możliwe, który mieści się w komunikacie zwracany. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-compose-remove"></a>Usuń tworzą sfctl
Usuwa istniejące sieci szkieletowej usług utworzyć wdrożenia z klastra.

Usuwa istniejące sieci szkieletowej usług utworzyć wdrożenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa wdrożenia [wymagane] | Tożsamość rozmieszczenia. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-compose-status"></a>Stan tworzenia sfctl
Pobiera informacje o sieci szkieletowej usług utworzyć wdrożenia.

Zwraca informacje o stanie wdrożenia Redaguj, który został utworzony lub właśnie trwa tworzenie klastra sieci szkieletowej usług i której nazwa jest zgodna ze strukturą określony jako parametr. Odpowiedź zawiera nazwy, stanu i inne szczegółowe informacje o wdrożeniu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa wdrożenia [wymagane] | Tożsamość rozmieszczenia. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-compose-upgrade"></a>sfctl tworzą uaktualnienia
Uruchamia uaktualniania wdrożenia tworzenia klastra usługi sieć szkieletowa usług.

Weryfikuje podane parametry uaktualniania i uruchamia uaktualniania wdrożenia, jeśli parametry są prawidłowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa wdrożenia [wymagane] | Nazwa wdrożenia. |
| — Ścieżka pliku [wymagane] | Ścieżka do obiektu docelowego rozwiązania Docker compose pliku. |
| --Domyślna svc--kondycji mapy typu | Słownik, który opisuje zasad dotyczących kondycji używane do oceny kondycji usług zakodowane JSON. |
| --szyfrowane przebiegu | Zamiast monitowania o podanie hasła rejestru kontener, użyj już zaszyfrowane hasło. |
| --Niepowodzenie akcji | Możliwe wartości\: "Nieprawidłowe", "Wycofania", "Ręczny". |
| --force-restart | Wymusić ponowne uruchomienie. |
| --ma przebiegu | Wyświetli monit o podanie hasła do rejestru kontenera. |
| --ponownej próby sprawdzenia kondycji | Limit ponownych prób sprawdzania kondycji mierzony w milisekundach. |
| --health-check-stable | Sprawdzanie kondycji stabilna czas w milisekundach. |
| --health-check-wait | Czas trwania oczekiwania sprawdzania kondycji mierzony w milisekundach. |
| --repliki zestaw sprawdzania | Uaktualnienia replik ustawić wyboru limitu czasu mierzony w sekundach. |
| -svc--kondycji mapy typu | Lista obiektów, które opisano zasady dotyczące kondycji używane do oceny kondycji usługi różnych typów zakodowane JSON. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |
| --unhealthy-app | Maksymalna dozwolona wartość procentowa w złej kondycji aplikacji przed zgłoszeniem błędu. <br><br> Na przykład aby umożliwić 10% aplikacji jest zła, ta wartość wynosi 10. Wartość procentowa reprezentuje maksymalny procent tolerowaną aplikacje, które mogą być zła, zanim klaster zostanie uznane za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jednej aplikacji w złej kondycji, kondycji jest szacowana jako ostrzeżenia. To jest obliczany przez podzielenie liczby złej kondycji aplikacji za pośrednictwem całkowita liczba wystąpień aplikacji w klastrze. |
| — limit czasu domeny uaktualnienia | Limit czasu domeny uaktualnień mierzony w milisekundach. |
| --rodzaj uaktualnienia | Domyślna\: wycofanie. |
| --Tryb uaktualniania | Możliwe wartości\: "Nieprawidłowe", "UnmonitoredAuto", "UnmonitoredManual", "Monitorowanej".  Domyślna\: UnmonitoredAuto. |
| — limit czasu uaktualniania | Limit czasu uaktualniania mierzony w milisekundach. |
| --user | Nazwa użytkownika do połączenia się z rejestrem kontenera. |
| --warning-as-error | Ostrzeżenia są traktowane z tym samym ważność jako błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-compose-upgrade-status"></a>Stan uaktualnienia tworzą sfctl
Pobiera szczegóły dla najnowszą aktualizację w tej sieci szkieletowej usług utworzyć wdrożenia.

Zwraca informacje o stanie uaktualnienia wdrożenia redagowania oraz szczegóły, aby pomóc debugowania problemów kondycji aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa wdrożenia [wymagane] | Tożsamość rozmieszczenia. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |



## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi sieci szkieletowej.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).