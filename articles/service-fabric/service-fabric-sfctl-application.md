---
title: Azure usługi Service Fabric interfejsu wiersza polecenia sfctl aplikacji | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi Service Fabric sfctl aplikacji.
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
ms.openlocfilehash: d4fec5d8131d269d3df229360066452c37a92430
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837491"
---
# <a name="sfctl-application"></a>sfctl application
Tworzenie, usuwanie i zarządzanie aplikacjami i typami aplikacji.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| create | Tworzy aplikację usługi Service Fabric przy użyciu określonego opisu. |
| delete | Usuwa istniejącą aplikację usługi Service Fabric. |
| wdrożony | Pobiera informacje o aplikacji wdrożonej w węźle usługi Service Fabric. |
| wdrożone kondycji | Pobiera informacje o kondycji aplikacji wdrożonej w węźle usługi Service Fabric. |
| deployed-list | Pobiera listę aplikacji wdrożonych w węźle usługi Service Fabric. |
| zdrowie | Pobiera kondycji aplikacji usługi Service fabric. |
| informacje | Pobiera informacje o aplikacji usługi Service Fabric. |
| list | Pobiera listę aplikacji utworzonych w klastrze usługi Service Fabric, spełniające warunki określonych filtrów. |
| ładowanie | Pobiera załadować informacji o aplikacji usługi Service Fabric. |
| Manifest | Pobiera manifest opisujące typu aplikacji. |
| aprowizacja | Przepisy lub rejestrów aplikacji usługi Service Fabric to typ klastra przy użyciu pakietu sfpkg z magazynu zewnętrznego lub przy użyciu pakietu aplikacji w magazynie obrazów. |
| report-health | Wysyła raport o kondycji aplikacji usługi Service Fabric. |
| type | Pobiera listę typów aplikacji w klastrze usługi Service Fabric dopasowania określonej nazwy. |
| Lista typów | Pobiera listę typów aplikacji w klastrze usługi Service Fabric. |
| Wstrzymanie obsługi administracyjnej | Usuwa lub wyrejestrowanie typu aplikacji usługi Service Fabric z klastra. |
| uaktualnij | Rozpoczyna uaktualnianie aplikacji w klastrze usługi Service Fabric. |
| Wznów uaktualnienia | Wznawia uaktualniania aplikacji w klastrze usługi Service Fabric. |
| upgrade-rollback | Rozpoczyna się wycofywanie uaktualnienia obecnie w toku aplikacji w klastrze usługi Service Fabric. |
| upgrade-status | Pobiera szczegóły najnowszą aktualizację, które są wykonywane w tej aplikacji. |
| przekazywanie | Skopiuj pakiet aplikacji usługi Service Fabric do magazynu obrazów. |

## <a name="sfctl-application-create"></a>Tworzenie aplikacji interfejsu sfctl
Tworzy aplikację usługi Service Fabric przy użyciu określonego opisu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| Nazwa aplikacji —, [wymagane] | Nazwa aplikacji, w tym "Service fabric\:" schemat identyfikatora URI. |
| --aplikacji — typ [wymagane] | Nazwa typu aplikacji w manifeście aplikacji. |
| Wersja aplikacji —, [wymagane] | Wersja typu aplikacji, zgodnie z definicją w manifeście aplikacji. |
| --max-node-count | Maksymalna liczba węzłów, w którym usługi Service Fabric będzie wydajność rezerwowa dla tej aplikacji. Należy pamiętać, że nie oznacza to, że usług tej aplikacji zostaną umieszczone na wszystkich tych węzłów. |
| — metryki | JSON zakodowany listę opisów metryki wydajności aplikacji. Metryka jest zdefiniowany jako nazwę skojarzonej z zestawem pojemności dla każdego węzła, który aplikacja istnieje na. |
| --min-node-count | Minimalna liczba węzłów, w którym usługi Service Fabric będzie wydajność rezerwowa dla tej aplikacji. Należy pamiętać, że nie oznacza to, że usług tej aplikacji zostaną umieszczone na wszystkich tych węzłów. |
| --Parametry | Zastępuje kodowany w formacie JSON listę parametrów aplikacji mają być stosowane podczas tworzenia aplikacji. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-application-delete"></a>Usuwanie aplikacji interfejsu sfctl
Usuwa istniejącą aplikację usługi Service Fabric.

Aplikacja musi zostać utworzona, aby można było usunąć. Usunięcie aplikacji spowoduje usunięcie wszystkich usług, które są częścią tej aplikacji. Domyślnie Usługa Service Fabric podejmie próbę zamknięcie usługi replik w sposób płynnego, a następnie usuń usługę. Jednak jeśli usługa występują problemy, które bez problemu zmieniała zamknięcia repliki, operacja usuwania może zająć dużo czasu lub zatrzymywane. Opcjonalna Flaga ForceRemove umożliwia pominąć łagodne zamknięcia sekwencji i wymuszone usunięcie aplikacji i wszystkich jego usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| --force-remove | Usuwanie aplikacji usługi Service Fabric lub usługa wymuszone bez pośrednictwa sekwencji łagodne zamykanie. Ten parametr może służyć do wymuszone usunięcie aplikacji lub usługi w przypadku usuwania, które jest przekroczeniem limitu czasu z powodu problemów w kodzie usługi, który uniemożliwia łagodne zamknięcia repliki. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-application-deployed"></a>Wdróż aplikację interfejsu sfctl
Pobiera informacje o aplikacji wdrożonej w węźle usługi Service Fabric.

Ta kwerenda zwraca informacje o aplikacji w systemie, jeśli podany identyfikator aplikacji jest przeznaczony dla aplikacji systemu. Wyniki obejmują wdrożone aplikacje w aktywny, aktywacja i pobieranie stanów. To zapytanie wymaga, że nazwa węzła odnosi się do węzła w klastrze. Zapytanie nie powiedzie się, jeśli nazwa podanego węzła nie wskazuje wszystkie węzły sieci szkieletowej usługi active w klastrze.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --include-health-state | Obejmują stan kondycji obiektu. Jeśli ten parametr ma wartość false lub nie został określony, zwracany stan kondycji jest "Nieznane". Gdy wartość true, zapytanie wprowadzona równolegle do węzła i kondycji usługi system przed scaleniem wyniki. W wyniku zapytania jest bardziej kosztowne i może potrwać dłuższy czas. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-application-deployed-health"></a>Interfejs sfctl wdrożone kondycji aplikacji
Pobiera informacje o kondycji aplikacji wdrożonej w węźle usługi Service Fabric.

Pobiera informacje o kondycji aplikacji wdrożonej w węźle usługi Service Fabric. Użyj EventsHealthStateFilter opcjonalnie filtrowania dla kolekcji obiektów HealthEvent zgłoszone wdrożonej aplikacji na podstawie stanu kondycji. Umożliwia DeployedServicePackagesHealthStateFilter opcjonalne filtrowanie dla dzieci DeployedServicePackageHealth oparte na stanie kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --deployed-service-packages-health-state-filter | Umożliwia filtrowanie obiektów stanu kondycji pakietu wdrożonej usługi zwracane w wynikach zapytania kondycji wdrożonej aplikacji oparte na ich kondycji. Możliwe wartości dla tego parametru to wartość całkowitą, jednego z następujących stanów kondycji. Tylko wdrożone usługi, pakiety, które są zgodne z filtrem są zwracane. Wszystkie wdrożone pakiety usługi są używane do oceny stanu kondycji zagregowane wdrożonej aplikacji. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenia, więc wartość może być kombinacją tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie kondycję pakietów usługi z wartością atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślną. Pasuje do dowolnego atrybutu HealthState. Ta wartość wynosi zero.  <br> -Brak — filtr, który nie jest zgodny z dowolną wartością atrybutu HealthState. Używany, aby zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok — filtrowanie, że dopasowania danych wejściowych z wartością atrybutu HealthState Ok. Wartość jest równa 2.  <br> -Warning - filtru, że wprowadzanie dopasowania z atrybutem HealthState wartość ostrzeżenie. Wartość wynosi 4.  <br> -Błąd — filtr, który pasuje do danych wejściowych z wartością atrybutu HealthState błędu. Wartość jest 8.  <br> -Al - filtr, który pasuje do danych wejściowych z dowolną wartością atrybutu HealthState. Wartość jest 65535. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości dla tego parametru to wartość całkowitą, jednego z następujących stanów kondycji. Zwracane są tylko te zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczanie oparte na flagi, dzięki czemu może to być kombinacją tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia z wartością atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślną. Pasuje do dowolnego atrybutu HealthState. Ta wartość wynosi zero.  <br> -Brak — filtr, który nie jest zgodny z dowolną wartością atrybutu HealthState. Używany, aby zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok — filtrowanie, że dopasowania danych wejściowych z wartością atrybutu HealthState Ok. Wartość jest równa 2.  <br> -Warning - filtru, że wprowadzanie dopasowania z atrybutem HealthState wartość ostrzeżenie. Wartość wynosi 4.  <br> -Błąd — filtr, który pasuje do danych wejściowych z wartością atrybutu HealthState błędu. Wartość jest 8.  <br> -Al - filtr, który pasuje do danych wejściowych z dowolną wartością atrybutu HealthState. Wartość jest 65535. |
| --exclude-health-statistics | Wskazuje, czy statystyki kondycji ma zostać zwrócone w wyniku zapytania. Wartość false, domyślnie. Statystyki pokazują liczbę elementów podrzędnych jednostek w kondycja Ok, ostrzeżenia i błędu. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-application-deployed-list"></a>Interfejs sfctl wdrożone — lista aplikacji
Pobiera listę aplikacji wdrożonych w węźle usługi Service Fabric.

Pobiera listę aplikacji wdrożonych w węźle usługi Service Fabric. Wyniki nie zawierają informacji o aplikacjach wdrożony system, chyba że jawnie kwerenda według identyfikatora. Wyniki obejmują wdrożone aplikacje w aktywny, aktywacja i pobieranie stanów. To zapytanie wymaga, że nazwa węzła odnosi się do węzła w klastrze. Zapytanie nie powiedzie się, jeśli nazwa podanego węzła nie wskazuje wszystkie węzły sieci szkieletowej usługi active w klastrze.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --token kontynuacji | Parametr tokenu kontynuacji służy do uzyskania następny zestaw wyników. Token kontynuacji o wartości niepuste znajduje się w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie istnieją żadne dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| --include-health-state | Obejmują stan kondycji obiektu. Jeśli ten parametr ma wartość false lub nie został określony, zwracany stan kondycji jest "Nieznane". Gdy wartość true, zapytanie wprowadzona równolegle do węzła i kondycji usługi system przed scaleniem wyniki. W wyniku zapytania jest bardziej kosztowne i może potrwać dłuższy czas. |
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

## <a name="sfctl-application-health"></a>Interfejs sfctl kondycji aplikacji
Pobiera kondycji aplikacji usługi Service fabric.

Zwraca stan kondycji aplikacji usługi Service fabric. Odpowiedź raportów, kondycja Ok, błąd lub ostrzeżenie. Nie znaleziono jednostki w magazynie kondycji, zwróci błąd.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| --deployed-applications-health-state-filter | Umożliwia filtrowanie obiektów stanu kondycji wdrożone aplikacje zwrócone w wyniku zapytania kondycji aplikacji na podstawie ich stanu kondycji. Możliwe wartości dla tego parametru to wartość całkowitą, jednego z następujących stanów kondycji. Zostaną zwrócone tylko wdrożone aplikacje, które są zgodne z filtrem. Wszystkie wdrożone aplikacje są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczanie oparte na flagi, dzięki czemu może to być kombinacją tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie kondycję wdrożonych aplikacji przy użyciu wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślną. Pasuje do dowolnego atrybutu HealthState. Ta wartość wynosi zero.  <br> -Brak — filtr, który nie jest zgodny z dowolną wartością atrybutu HealthState. Używany, aby zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok — filtrowanie, że dopasowania danych wejściowych z wartością atrybutu HealthState Ok. Wartość jest równa 2.  <br> -Warning - filtru, że wprowadzanie dopasowania z atrybutem HealthState wartość ostrzeżenie. Wartość wynosi 4.  <br> -Błąd — filtr, który pasuje do danych wejściowych z wartością atrybutu HealthState błędu. Wartość jest 8.  <br> -Al - filtr, który pasuje do danych wejściowych z dowolną wartością atrybutu HealthState. Wartość jest 65535. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości dla tego parametru to wartość całkowitą, jednego z następujących stanów kondycji. Zwracane są tylko te zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczanie oparte na flagi, dzięki czemu może to być kombinacją tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia z wartością atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślną. Pasuje do dowolnego atrybutu HealthState. Ta wartość wynosi zero.  <br> -Brak — filtr, który nie jest zgodny z dowolną wartością atrybutu HealthState. Używany, aby zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok — filtrowanie, że dopasowania danych wejściowych z wartością atrybutu HealthState Ok. Wartość jest równa 2.  <br> -Warning - filtru, że wprowadzanie dopasowania z atrybutem HealthState wartość ostrzeżenie. Wartość wynosi 4.  <br> -Błąd — filtr, który pasuje do danych wejściowych z wartością atrybutu HealthState błędu. Wartość jest 8.  <br> -Al - filtr, który pasuje do danych wejściowych z dowolną wartością atrybutu HealthState. Wartość jest 65535. |
| --exclude-health-statistics | Wskazuje, czy statystyki kondycji ma zostać zwrócone w wyniku zapytania. Wartość false, domyślnie. Statystyki pokazują liczbę elementów podrzędnych jednostek w kondycja Ok, ostrzeżenia i błędu. |
| --services-health-state-filter | Umożliwia filtrowanie obiektów stan kondycji usług zwrócone w wyniku zapytania o kondycję usług na podstawie ich stanu kondycji. Możliwe wartości dla tego parametru to wartość całkowitą, jednego z następujących stanów kondycji. Zwracane są tylko te usługi, które są zgodne z filtrem. Wszystkie usługi są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczanie oparte na flagi, dzięki czemu może to być kombinacją tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 wtedy kondycję usługi przy użyciu wartości atrybutu HealthState OK (2) i ostrzeżenia (4) zostanie zwrócony.  <br> -Domyślnie — wartość domyślną. Pasuje do dowolnego atrybutu HealthState. Ta wartość wynosi zero.  <br> -Brak — filtr, który nie jest zgodny z dowolną wartością atrybutu HealthState. Używany, aby zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok — filtrowanie, że dopasowania danych wejściowych z wartością atrybutu HealthState Ok. Wartość jest równa 2.  <br> -Warning - filtru, że wprowadzanie dopasowania z atrybutem HealthState wartość ostrzeżenie. Wartość wynosi 4.  <br> -Błąd — filtr, który pasuje do danych wejściowych z wartością atrybutu HealthState błędu. Wartość jest 8.  <br> -Al - filtr, który pasuje do danych wejściowych z dowolną wartością atrybutu HealthState. Wartość jest 65535. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-application-info"></a>informacje o aplikacji interfejsu sfctl
Pobiera informacje o aplikacji usługi Service Fabric.

Zwraca informacje o aplikacji, który został utworzony, lub trwa proces tworzonych w klaster usługi Service Fabric, którego nazwa odpowiada dokumentem określonym jako parametr. Odpowiedź zawiera nazwę, typ, stan, parametry i inne szczegóły dotyczące aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| --exclude-application-parameters | Flaga określająca, czy parametry aplikacji będą wykluczane z wyników. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-application-list"></a>Lista aplikacji interfejsu sfctl
Pobiera listę aplikacji utworzonych w klastrze usługi Service Fabric, spełniające warunki określonych filtrów.

Pobiera informacje o aplikacji, które zostały utworzone lub właśnie tworzona w usłudze Service Fabric klastra i dopasować określonych filtrów. Odpowiedź zawiera nazwę, typ, stan, parametry i inne szczegóły dotyczące aplikacji. Jeśli aplikacje nie mieszczą się na stronie, jednej stronie wyników jest zwracany oraz token kontynuacji, którego można uzyskać następnej strony. W tym samym czasie, nie można określić ApplicationTypeName filtry i ApplicationDefinitionKindFilter.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-definition-kind-filter | Używane do filtrowania ApplicationDefinitionKind, który jest mechanizm służący do definiowania aplikacji usługi Service Fabric.  <br> -Domyślnie — wartość domyślna, która działa tak samo, jak wybór "All". Wartość wynosi 0.  <br> -Al - filtr, który pasuje do danych wejściowych z dowolną wartością ApplicationDefinitionKind. Wartość jest 65535.  <br> -ServiceFabricApplicationDescription — filtr, który pasuje do danych wejściowych z wartością ApplicationDefinitionKind ServiceFabricApplicationDescription. Wartość to 1.  <br> Filtr — Redaguj -, który pasuje do danych wejściowych z wartością ApplicationDefinitionKind Compose. Wartość jest równa 2. |
| --application-type-name | Nazwa typu aplikacji używane do filtrowania aplikacji dla kwerendy. Ta wartość nie powinna zawierać wersję typu aplikacji. |
| --token kontynuacji | Parametr tokenu kontynuacji służy do uzyskania następny zestaw wyników. Token kontynuacji o wartości niepuste znajduje się w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie istnieją żadne dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| --exclude-application-parameters | Flaga określająca, czy parametry aplikacji będą wykluczane z wyników. |
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

## <a name="sfctl-application-load"></a>Interfejs sfctl obciążenia aplikacji
Pobiera załadować informacji o aplikacji usługi Service Fabric.

Zwraca informacje o ładowaniu dotyczące aplikacji, który został utworzony, lub trwa proces tworzonych w klaster usługi Service Fabric, którego nazwa odpowiada dokumentem określonym jako parametr. Odpowiedź zawiera nazwę, minimalna liczba węzłów, maksymalna liczba węzłów, liczbę węzłów, które obecnie zajmuje aplikacji i aplikacji obciążenia metryki informacje o aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-application-manifest"></a>Interfejs sfctl manifest aplikacji
Pobiera manifest opisujące typu aplikacji.

Odpowiedź zawiera kod XML manifestu aplikacji jako ciąg.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| -aplikacji type-name [wymagane] | Nazwa typu aplikacji. |
| --— wersję typu aplikacji — [wymagane] | Wersja typu aplikacji. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-application-provision"></a>aprowizowanie aplikacji interfejsu sfctl
Przepisy lub rejestrów aplikacji usługi Service Fabric to typ klastra przy użyciu pakietu sfpkg z magazynu zewnętrznego lub przy użyciu pakietu aplikacji w magazynie obrazów.

Aprowizuje typem aplikacji usługi Service Fabric z klastrem. Jest to wymagane, zanim jakieś nowe aplikacje, które mogą być utworzone. Operacja aprowizacji można wykonać na pakiet aplikacji, określona przez relativePathInImageStore lub za pomocą identyfikatora URI sfpkg zewnętrznych. Chyba że — aprowizowanie zewnętrznych jest ustawiona, to polecenie będzie oczekiwać, że aprowizacja magazynu obrazów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-package-download-uri | Ścieżka do pakietu aplikacji "sfpkg", skąd można pobrać pakietu aplikacji przy użyciu protokołów HTTP lub HTTPS. <br><br> Aprowizacja rodzaj zewnętrznych przechowywać tylko. Pakiet aplikacji mogą być przechowywane w magazynu zewnętrznego, który zawiera operację pobierania, aby pobrać plik. Obsługiwane protokoły to HTTP i HTTPS, a ścieżka musi zezwalać na dostęp do odczytu. |
| --application-type-build-path | Tylko aprowizacja obrazu rodzaj magazynu. Ścieżka względna dla pakietu aplikacji w magazynie obrazów określone podczas operacji przekazywania poprzedniego. |
| --application-type-name | Aprowizacja rodzaj zewnętrznych przechowywać tylko. Nazwa typu aplikacji reprezentuje nazwę typu aplikacji w manifeście aplikacji. |
| --application-type-version | Aprowizacja rodzaj zewnętrznych przechowywać tylko. Wersja typu aplikacji oznacza wersję typu aplikacji w manifeście aplikacji. |
| --external-provision | Lokalizacja, z której pakiet aplikacji mogą być zarejestrowane lub aprowizowane. Wskazuje, czy dostarczanie jest dla pakietu aplikacji, który został wcześniej przekazany do magazynu zewnętrznego. Pakiet aplikacji, kończy się *.sfpkg rozszerzenia. |
| --nie-wait | Wskazuje, czy Inicjowanie obsługi administracyjnej powinna występować asynchronicznie. <br><br> Po ustawieniu na wartość true, aprowizacja operacji zwraca żądanie zostało zaakceptowane przez system, gdy operacja aprowizacji będzie kontynuowane bez ograniczenia limitu czasu. Wartość domyślna to false. Dla dużych pakietów aplikacji zaleca się ustawienie wartości true. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-application-report-health"></a>Interfejs sfctl aplikacji raportów kondycji
Wysyła raport o kondycji aplikacji usługi Service Fabric.

Raporty stan kondycji określonej aplikacji usługi Service Fabric. Raport musi zawierać informacje o źródle raport o kondycji i właściwości, na którym jest zgłaszany. Raport jest wysyłany do bramy aplikacji, która przekazuje w magazynie kondycji usługi Service Fabric. Raport może być akceptowane przez bramę, ale odrzucony przez magazynu kondycji po dodatkową walidację. Na przykład magazynu kondycji może odrzucić raport ze względu na nieprawidłowy parametr, takich jak numer sekwencji starych. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, Pobierz kondycji aplikacji i sprawdź, czy raport jest wyświetlany.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. <br><br> Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" 6.0 + i "myapp app1" w poprzednich wersjach. |
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

## <a name="sfctl-application-type"></a>Typ aplikacji interfejsu sfctl
Pobiera listę typów aplikacji w klastrze usługi Service Fabric dopasowania określonej nazwy.

Zwraca informacje o typach aplikacji, które są udostępniane, lub trwa proces aprowizowane w klastrze usługi Service Fabric. Te wyniki są typami aplikacji, którego nazwa odpowiada dokładnie dokumentem określonym jako parametr i które są zgodne z parametrami danego zapytania. Zwracane są wszystkie wersje typu aplikacji zgodnych z nazwą typu aplikacji z każdą wersją zwracane jako jeden typ aplikacji. Odpowiedź zawiera nazwę, wersję, stan i inne szczegóły dotyczące typu aplikacji. Jest to stronicowane zapytanie, co oznacza, jeśli nie wszystkie typy aplikacji mieści się na stronie jednej stronie wyników jest zwracany oraz token kontynuacji, którego można uzyskać następnej strony. Na przykład jeśli istnieją 10 typy aplikacji, ale strony tylko pasuje pierwszym typy trzech aplikacji lub maksymalną liczbę wyników jest ustawiona na 3, trzy jest zwracany. Aby uzyskać dostęp do pozostałej części wyników, należy pobrać kolejnych stronach, token kontynuacji zwrócony w kolejnym zapytaniu. Token kontynuacji puste jest zwracany, jeśli brak kolejnych stron.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| -aplikacji type-name [wymagane] | Nazwa typu aplikacji. |
| --application-type-version | Wersja typu aplikacji. |
| --token kontynuacji | Parametr tokenu kontynuacji służy do uzyskania następny zestaw wyników. Token kontynuacji o wartości niepuste znajduje się w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie istnieją żadne dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| --exclude-application-parameters | Flaga określająca, czy parametry aplikacji będą wykluczane z wyników. |
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

## <a name="sfctl-application-type-list"></a>Lista typów interfejsu sfctl aplikacji
Pobiera listę typów aplikacji w klastrze usługi Service Fabric.

Zwraca informacje o typach aplikacji, które są udostępniane, lub trwa proces aprowizowane w klastrze usługi Service Fabric. Każda wersja typu aplikacji jest zwracana jako typ aplikacji. Odpowiedź zawiera nazwę, wersję, stan i inne szczegóły dotyczące typu aplikacji. Jest to stronicowane zapytanie, co oznacza, jeśli nie wszystkie typy aplikacji mieści się na stronie jednej stronie wyników jest zwracany oraz token kontynuacji, którego można uzyskać następnej strony. Na przykład jeśli istnieją 10 typy aplikacji, ale strony tylko pasuje pierwszym typy trzech aplikacji lub maksymalną liczbę wyników jest ustawiona na 3, trzy jest zwracany. Aby uzyskać dostęp do pozostałej części wyników, należy pobrać kolejnych stronach, token kontynuacji zwrócony w kolejnym zapytaniu. Token kontynuacji puste jest zwracany, jeśli brak kolejnych stron.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-type-definition-kind-filter | Używane do filtrowania ApplicationTypeDefinitionKind, który jest mechanizm służący do definiowania typu aplikacji usługi Service Fabric.  <br> -Domyślnie — wartość domyślna, która działa tak samo, jak wybór "All". Wartość wynosi 0.  <br> -Al - filtr, który pasuje do danych wejściowych z dowolną wartością ApplicationTypeDefinitionKind. Wartość jest 65535.  <br> -ServiceFabricApplicationPackage — filtr, który pasuje do danych wejściowych z wartością ApplicationTypeDefinitionKind ServiceFabricApplicationPackage. Wartość to 1.  <br> Filtr — Redaguj -, który pasuje do danych wejściowych z wartością ApplicationTypeDefinitionKind Compose. Wartość jest równa 2. |
| --token kontynuacji | Parametr tokenu kontynuacji służy do uzyskania następny zestaw wyników. Token kontynuacji o wartości niepuste znajduje się w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie istnieją żadne dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| --exclude-application-parameters | Flaga określająca, czy parametry aplikacji będą wykluczane z wyników. |
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

## <a name="sfctl-application-unprovision"></a>Wstrzymanie obsługi administracyjnej sfctl aplikacji
Usuwa lub wyrejestrowanie typu aplikacji usługi Service Fabric z klastra.

Tę operację można wykonać tylko, jeśli zostały usunięte wszystkie wystąpienia aplikacji typu aplikacja. Po wyrejestrować typ aplikacji dla tego typu aplikacji można utworzyć nie nowych wystąpień aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| -aplikacji type-name [wymagane] | Nazwa typu aplikacji. |
| --— wersję typu aplikacji — [wymagane] | Wersja typu aplikacji, zgodnie z definicją w manifeście aplikacji. |
| --async-parameter | Flaga wskazująca, czy wstrzymanie obsługi administracyjnej powinna występować asynchronicznie. Po ustawieniu na wartość true, zwraca operacji Cofnij aprowizację, jeśli żądanie zostało zaakceptowane przez system i operacji Cofnij aprowizację będzie kontynuowane bez ograniczenia limitu czasu. Wartość domyślna to false. Jednak firma Microsoft zaleca ustawienie na wartość true dla dużych pakietów aplikacji, które zostały udostępnione. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-application-upgrade"></a>Interfejs sfctl uaktualnienia aplikacji
Rozpoczyna uaktualnianie aplikacji w klastrze usługi Service Fabric.

Weryfikuje parametry uaktualniania aplikacji podany i rozpoczyna uaktualnianie aplikacji, jeśli parametry są prawidłowe. Należy pamiętać, że opis uaktualniania zastępuje istniejące opis aplikacji. Oznacza to, że jeśli nie określono parametrów, istniejących parametrów w aplikacji zostaną zastąpione listy puste parametry. Spowoduje to aplikacji przy użyciu wartości domyślne parametrów z manifestu aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. <br><br> Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| — Wersja aplikacji [wymagane] | Docelowy wersję typu aplikacji (znaleziony w manifeście aplikacji) dla uaktualnienie aplikacji. |
| --Parametry [wymagane] | Zastępuje kodowany w formacie JSON listę parametrów aplikacji mają być stosowane podczas uaktualniania aplikacji. |
| ---service kondycji — zasady domyślne | Określanie zasad dotyczących kondycji używany domyślnie do oceny kondycji typ usługi zakodowane JSON. |
| — Akcja błędu | Akcja do wykonania podczas uaktualniania monitorowanej napotka monitorowania zasad lub kondycji naruszenia zasad. |
| --force-restart | Wymuszone ponownie procesów podczas uaktualniania, nawet gdy wersja kodu nie została zmieniona. |
| --health-check-retry-timeout | Odstęp czasu między próbami sprawdzać kondycję, jeśli aplikacja lub klastra nie jest w dobrej kondycji.  Domyślne\: PT0H10M0S. |
| --health-check-stable-duration | Ilość czasu, aplikacji lub klastra musi pozostać dobrej kondycji przed uaktualnienia przechodzi do następnej domeny uaktualnienia.  Domyślne\: PT0H2M0S. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. |
| --health-check-wait-duration | Czas oczekiwania po wykonaniu uaktualnienia domeny przed rozpoczęciem kondycji sprawdza, czy proces.  Domyślne\: 0. |
| --max-unhealthy-apps | Maksymalna dozwolona wartość procentowa wdrożone aplikacje w złej kondycji. Przedstawiona jako liczba od 0 do 100. |
| --mode | Tryb służy do monitorowania kondycji podczas uaktualnień stopniowych.  Domyślne\: UnmonitoredAuto. |
| --replica-set-check-timeout | Maksymalna ilość czasu blokowania przetwarzania domeny uaktualnienia, a przed utratą dostępności w przypadku nieoczekiwanych problemów. Mierzony w sekundach. |
| --service-health-policy | Mapy za pomocą zasad kondycji typ usługi dla usługi, nazwa typu zakodowane JSON. Mapa jest pusta domyślnie. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |
| --upgrade-domain-timeout | Czas każdej z domen musi wykonać, zanim zostanie wykonany FailureAction.  Default\: P10675199DT02H48M05.4775807S. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. |
| — limit czasu uaktualniania | Czas ogólną uaktualnienia musi wykonać, zanim zostanie wykonany FailureAction.  Default\: P10675199DT02H48M05.4775807S. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. |
| --warning-as-error | Wskazuje, czy ostrzeżenia są traktowane z tego samego ważność jako błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-application-upgrade-resume"></a>Interfejs sfctl aplikacji uaktualnienia resume
Wznawia uaktualniania aplikacji w klastrze usługi Service Fabric.

Wznawia uaktualnić niemonitorowane ręczne aplikacji usługi Service Fabric. Usługa Service Fabric uaktualnia jedną domenę uaktualnienia w danym momencie. Niemonitorowane uaktualnień ręcznych po domeny uaktualnienia usługi Service Fabric czeka można wywołać ten interfejs API, przed kontynuowaniem do następnej domeny uaktualnienia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| — Uaktualnianie domain-name [wymagane] | Nazwa przez domenę uaktualnienia, w której chcesz wznowić uaktualnienia. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-application-upgrade-rollback"></a>Interfejs sfctl aplikacji uaktualnienia — wycofywanie
Rozpoczyna się wycofywanie uaktualnienia obecnie w toku aplikacji w klastrze usługi Service Fabric.

Uaktualnij rozpoczyna się wycofywanie bieżącej aplikacji do poprzedniej wersji. Ten interfejs API należy używać tylko można wycofać uaktualnienie w trakcie bieżącego jest stopniowe do przodu do nowej wersji. Jeśli aplikacja nie aktualnie trwa uaktualnianie uaktualnić ją do żądanej wersji, w tym Przywracanie poprzedniej wersji przy użyciu funkcji StartApplicationUpgrade interfejsu API.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-application-upgrade-status"></a>Stan uaktualnienia sfctl aplikacji
Pobiera szczegóły najnowszą aktualizację, które są wykonywane w tej aplikacji.

Zwraca informacje o stanie najnowsze uaktualnienie aplikacji wraz ze szczegółami ułatwiające debugowanie problemów z kondycją aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-application-upload"></a>przekazywanie aplikacji interfejsu sfctl
Skopiuj pakiet aplikacji usługi Service Fabric do magazynu obrazów.

Opcjonalnie można wyświetlić postęp przekazywania dla każdego pliku w pakiecie. Przekaż postępu są wysyłane do `stderr`.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Ścieżka [wymagane] | Ścieżka do pakietu aplikacji lokalnych. |
| --imagestore-string | Przechowuj obrazu docelowego do przekazania do pakietu aplikacji.  Domyślne\: sieci szkieletowej\:ImageStore. |
| --show-progress | Pokaż postęp przekazywania pliku dla dużych pakietów. |

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
