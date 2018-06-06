---
title: Usługa sieci szkieletowej interfejsu wiersza polecenia - sfctl aplikacji Azure | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi sieć szkieletowa sfctl aplikacji.
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
ms.openlocfilehash: 3ecc5a03ff1847dc11c5a5047e35566a4e68fec2
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763701"
---
# <a name="sfctl-application"></a>sfctl application
Tworzenie, usuwanie i zarządzanie aplikacjami i typami aplikacji.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| create | Tworzy aplikację usługi Service Fabric przy użyciu określonego opisu. |
| usuwanie | Usuwa istniejącą aplikację sieci szkieletowej usług. |
| Wdrożony | Pobiera informacje o aplikacji wdrożone w węźle sieci szkieletowej usług. |
| wdrożone kondycji | Pobiera informacje o kondycji aplikacji wdrożonych w węźle sieci szkieletowej usług. |
| wdrożone listy | Pobiera listę aplikacje wdrożone w węźle sieci szkieletowej usług. |
| kondycja | Pobiera kondycji aplikacji sieci szkieletowej usług. |
| informacje | Pobiera informacje o aplikacji sieci szkieletowej usług. |
| lista | Pobiera listę aplikacji utworzony w klastrze usługi sieć szkieletowa spełniające warunki określonych filtrów. |
| ładowanie | Pobiera załadować informacji o aplikacji sieci szkieletowej usług. |
| Manifest | Pobiera manifest opisu typu aplikacji. |
| Zainicjuj obsługę | Przepisy lub rejestrów aplikacji usługi Service Fabric typu z klastrem przy użyciu pakietu .sfpkg w zewnętrznym sklepie lub pakiet aplikacji w magazynie obrazu. |
| report-health | Wysyła raport o kondycji aplikacji sieci szkieletowej usług. |
| type | Pobiera listę typów aplikacji w klastrze usługi sieć szkieletowa dopasowania określonej nazwy. |
| Lista typów | Pobiera listę typów aplikacji w klastrze usługi sieć szkieletowa usług. |
| Wstrzymanie obsługi administracyjnej | Usuwa lub wyrejestrowuje typu sieci szkieletowej usług aplikacji z klastra. |
| Uaktualnienie | Uruchamia uaktualniania aplikacji w klastrze usługi sieć szkieletowa usług. |
| Wznów uaktualnienia | Wznawia uaktualniania aplikacji w klastrze usługi sieć szkieletowa usług. |
| Wycofywanie uaktualnienia | Uruchamia wycofywanie uaktualnienia obecnie w toku aplikacji w klastrze usługi sieć szkieletowa usług. |
| Stan uaktualnienia | Pobiera szczegóły dla najnowszą aktualizację w tej aplikacji. |
| przekazywanie | Skopiuj pakiet sieci szkieletowej usług aplikacji do magazynu obrazów. |

## <a name="sfctl-application-create"></a>Tworzenie aplikacji sfctl
Tworzy aplikację usługi Service Fabric przy użyciu określonego opisu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| [wymagane] - app-name | Nazwa aplikacji, w tym "sieć szkieletowa\:" schemat identyfikatora URI. |
| [wymagane] na typ--aplikacji | Nazwa typu aplikacji w manifeście aplikacji. |
| [wymagane] wersji — aplikacji | Wersja typu aplikacji, zgodnie z definicją w manifeście aplikacji. |
| -max-node-count | Maksymalna liczba węzłów, gdy usługa sieć szkieletowa będzie wydajność rezerwowa dla tej aplikacji. Należy pamiętać, że nie oznacza to, że usługi tej aplikacji zostaną umieszczone na wszystkich tych węzłów. |
| --metryk | Lista opisy metryki pojemności aplikacji zakodowane JSON. Metryka jest zdefiniowany jako nazwę skojarzonej z zestawem pojemności dla każdego węzła, że aplikacja nie istnieje na. |
| min —-node-count | Minimalna liczba węzłów, gdy usługa sieć szkieletowa będzie wydajność rezerwowa dla tej aplikacji. Należy pamiętać, że nie oznacza to, że usługi tej aplikacji zostaną umieszczone na wszystkich tych węzłów. |
| — Parametry | Zastępuje kodowany w formacie JSON lista parametrów aplikacji ma być stosowany podczas tworzenia aplikacji. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-application-delete"></a>Usuwanie aplikacji sfctl
Usuwa istniejącą aplikację sieci szkieletowej usług.

Usuwa istniejącą aplikację sieci szkieletowej usług. Aplikacja musi zostać utworzony, aby można było usunąć. Usunięcie aplikacji spowoduje usunięcie wszystkich usług, które są częścią tej aplikacji. Domyślnie usługi sieć szkieletowa spróbuje zamknięcie repliki usługi w sposób bezpieczne, a następnie usuń usługę. Jednak jeśli usługa ma problemy bezpiecznie zamykania repliki, operacja usuwania może zająć dużo czasu lub zatrzymywane. Opcjonalna Flaga ForceRemove umożliwia Pomiń operację prawidłowego zamknięcia sekwencji i wymuszone usunięcie aplikacji i wszystkich jego usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --force-remove | Usuń sieć szkieletowa usług aplikacji lub usługi wymuszone bez pośrednictwa bezpiecznego zamknięcia sekwencji. Ten parametr może służyć do wymuszone usunięcie aplikacji lub usługi, dla których delete jest przekroczeniem limitu czasu z powodu problemów z kodem usługi, który uniemożliwia łagodne zamykanie replik. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-application-deployed"></a>Aplikacja sfctl wdrożona
Pobiera informacje o aplikacji wdrożone w węźle sieci szkieletowej usług.

Pobiera informacje o aplikacji wdrożone w węźle sieci szkieletowej usług.  To zapytanie zwraca informacje o aplikacji systemu, jeśli podany identyfikator aplikacji dla aplikacji systemu. Wyniki obejmują wdrożonych aplikacji w aktywnych, aktywowanie i pobieranie stanów. To zapytanie wymaga, że nazwa węzła odpowiada węzła w klastrze. Zapytanie nie powiedzie się, jeśli nazwa Podany węzeł nie wskazuje węzły sieci szkieletowej usługi active w klastrze.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| --obejmują Kondycja | Obejmują stanu kondycji jednostki. Jeśli ten parametr ma wartość false lub nie została określona, następnie kondycja zwracane jest "Nieznany". Gdy ma wartość true, zapytanie przechodzi równolegle do węzła i usługę kondycji systemu przed wyniki są łączone. W związku z tym zapytania jest bardziej kosztowne i może potrwać dłuższy czas. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-application-deployed-health"></a>sfctl wdrożone kondycji aplikacji
Pobiera informacje o kondycji aplikacji wdrożonych w węźle sieci szkieletowej usług.

Pobiera informacje o kondycji aplikacji wdrożonych w węźle sieci szkieletowej usług. Filtr EventsHealthStateFilter umożliwia również filtrować dla kolekcji obiektów HealthEvent dotyczył wdrożonej aplikacji na podstawie stanu kondycji. Filtr DeployedServicePackagesHealthStateFilter umożliwia również filtrować dla dzieci DeployedServicePackageHealth oparte na stanie kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| --Deployed-Service-Packages-Health-State-Filter | Umożliwia filtrowanie obiektów stanu kondycji pakietu wdrożonej usługi zwrócone w wyniku zapytania kondycji wdrożonej aplikacji oparte na stanie kondycji. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Tylko wdrożyć usługi, są zwracane pakiety zgodne z filtrem. Wszystkie wdrożone pakiety usługi służą do oceny stanu kondycji zagregowane wdrożonej aplikacji. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są wyliczania opartego na flagi, dlatego wartość może być kombinacją tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie kondycja pakietów usługi z wartością atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero.  <br> -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2.  <br> — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4.  <br> -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8.  <br> -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenie, może to być kombinacją te wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero.  <br> -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2.  <br> — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4.  <br> -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8.  <br> -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535. |
| — Wyklucz kondycji statystyk | Wskazuje, czy statystyki kondycji powinny być zwracane w ramach wyniku zapytania. Wartość false, domyślnie. Statystyki zawierają liczbę elementów podrzędnych obiektów w kondycja Ok, ostrzeżeń i błędów. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-application-deployed-list"></a>wdrożona aplikacja sfctl — listy
Pobiera listę aplikacje wdrożone w węźle sieci szkieletowej usług.

Pobiera listę aplikacje wdrożone w węźle sieci szkieletowej usług. Wyniki nie zawierają informacji o aplikacjach wdrożony system, chyba że jawnie zapytanie dotyczące według identyfikatora. Wyniki obejmują wdrożonych aplikacji w aktywnych, aktywowanie i pobieranie stanów. To zapytanie wymaga, że nazwa węzła odpowiada węzła w klastrze. Zapytanie nie powiedzie się, jeśli nazwa Podany węzeł nie wskazuje węzły sieci szkieletowej usługi active w klastrze.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| --token kontynuacji | Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z niepustą wartość jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| --obejmują Kondycja | Obejmują stanu kondycji jednostki. Jeśli ten parametr ma wartość false lub nie została określona, stan kondycji, zwracana jest "Nieznany". Gdy ma wartość true, zapytanie przechodzi równolegle do węzła i usługę kondycji systemu przed wyniki są łączone. W związku z tym zapytania jest bardziej kosztowne i może potrwać dłuższy czas. |
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

## <a name="sfctl-application-health"></a>Kondycja aplikacji sfctl
Pobiera kondycji aplikacji sieci szkieletowej usług.

Zwraca stan kondycji aplikacji sieci szkieletowej usług. Odpowiedź raporty kondycja Ok, błąd lub ostrzeżenie. Nie znaleziono jednostki w magazynie kondycji, zwróci błąd.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --deployed-applications-health-state-filter | Umożliwia filtrowanie obiekty stanu kondycji wdrożonych aplikacji zwrócone w wyniku zapytania kondycji aplikacji na podstawie ich stanu kondycji. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Tylko wdrożone aplikacje zgodne z filtrem zostaną zwrócone. Wszystkie wdrożone aplikacje są używane do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenie, może to być kombinacją te wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie kondycja wdrożonych aplikacji o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero.  <br> -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2.  <br> — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4.  <br> -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8.  <br> -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenie, może to być kombinacją te wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero.  <br> -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2.  <br> — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4.  <br> -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8.  <br> -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535. |
| — Wyklucz kondycji statystyk | Wskazuje, czy statystyki kondycji powinny być zwracane w ramach wyniku zapytania. Wartość false, domyślnie. Statystyki zawierają liczbę elementów podrzędnych obiektów w kondycja Ok, ostrzeżeń i błędów. |
| --services-health-state-filter | Umożliwia filtrowanie obiektów stanu kondycji usług zwrócone w wyniku zapytania kondycji usług na podstawie ich stanu kondycji. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Zwracane są tylko usługi zgodne z filtrem. Wszystkie usługi są używane do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenie, może to być kombinacją te wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 wtedy kondycja usługi z wartością atrybutu HealthState OK (2) i ostrzeżenia (4) zostanie zwrócony.  <br> -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero.  <br> -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2.  <br> — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4.  <br> -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8.  <br> -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-application-info"></a>informacje o aplikacji sfctl
Pobiera informacje o aplikacji sieci szkieletowej usług.

Zwraca informacje o aplikacji, który został utworzony lub właśnie trwa tworzenie klastra sieci szkieletowej usług i której nazwa jest zgodna ze strukturą określony jako parametr. Odpowiedź zawiera nazwę, typ, stan, parametry i inne szczegółowe informacje o aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| — Wyklucz aplikacji parametrów | Flaga określająca, czy parametry aplikacji zostaną wykluczone z wyników. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-application-list"></a>Lista aplikacji sfctl
Pobiera listę aplikacji utworzony w klastrze usługi sieć szkieletowa spełniające warunki określonych filtrów.

Pobiera informacje o aplikacji, które zostały utworzone lub właśnie tworzona w sieci szkieletowej usług klastra i odpowiada określonych filtrów. Odpowiedź zawiera nazwę, typ, stan, parametry i inne szczegółowe informacje o aplikacji. Jeśli aplikacje nie mieści się na stronie, co strony wyników jest zwracana oraz token kontynuacji, który może służyć do pobrania następnej strony. Nie można określić ApplicationTypeName filtry i ApplicationDefinitionKindFilter w tym samym czasie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-definition-kind-filter | Używane do filtrowania na ApplicationDefinitionKind, które to mechanizm służący do definiowania aplikacji sieci szkieletowej usług.  <br> -Domyślnie — wartość domyślna, która wykonuje tę samą funkcję co zaznaczenie "All". Wartość wynosi 0.  <br> -All - filtru pasującego do danych wejściowych z dowolną wartością ApplicationDefinitionKind. Wartość jest 65535.  <br> -ServiceFabricApplicationDescription - filtru pasującego do danych wejściowych o wartości ApplicationDefinitionKind ServiceFabricApplicationDescription. Wartość to 1.  <br> -Redaguj — filtr, który pasuje do danych wejściowych o wartości ApplicationDefinitionKind tworzenia. Wartość jest równa 2. |
| — Nazwa typu aplikacji | Nazwa typu aplikacji, używane do filtrowania aplikacji dla kwerendy. Ta wartość nie powinna zawierać wersja typu aplikacji. |
| --token kontynuacji | Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z niepustą wartość jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| — Wyklucz aplikacji parametrów | Flaga określająca, czy parametry aplikacji zostaną wykluczone z wyników. |
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

## <a name="sfctl-application-load"></a>sfctl obciążenia aplikacji
Pobiera załadować informacji o aplikacji sieci szkieletowej usług.

Zwraca obciążenia informacje dotyczące aplikacji, która została utworzona lub właśnie trwa tworzenie klastra sieci szkieletowej usług i której nazwa jest zgodna ze strukturą określony jako parametr. Odpowiedź zawiera nazwę, minimalna węzłów, maksymalna liczba węzłów, liczba węzłów, z którymi aplikacja jest obecnie zajęte i aplikacji obciążenia metryki informacji o aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-application-manifest"></a>manifest aplikacji sfctl
Pobiera manifest opisu typu aplikacji.

Pobiera manifest opisu typu aplikacji. Odpowiedź zawiera manifest aplikacji XML jako ciąg.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --aplikacji typu nazwa-[wymagane] | Nazwa typu aplikacji. |
| --— Typ — wersja aplikacji [wymagane] | Wersja typu aplikacji. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-application-provision"></a>Zainicjuj obsługę aplikacji sfctl
Przepisy lub rejestrów aplikacji usługi Service Fabric typu z klastrem przy użyciu pakietu .sfpkg w zewnętrznym sklepie lub pakiet aplikacji w magazynie obrazu.

Inicjuje typem aplikacji usługi sieć szkieletowa z klastra. Jest to wymagane, zanim można utworzyć wystąpienia każdej nowej aplikacji. Operacja inicjowania obsługi administracyjnej można przeprowadzić na określona przez relativePathInImageStore lub za pomocą identyfikatora URI z zewnętrznego .sfpkg pakietu aplikacji. O ile — udostępniania zewnętrznych jest ustawiona, to polecenie będzie otrzymywania obrazu magazynu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --aplikacji pakiet pobierania uri | Ścieżka do pakietu aplikacji ".sfpkg", skąd można pobrać pakiet aplikacji za pomocą protokołów HTTP lub HTTPS. <br><br> Przechowywanie tylko dla rodzaju udostępniania zewnętrznego. Pakiet aplikacji mogą być przechowywane w zewnętrznym sklepie, który zawiera operację pobierania, aby pobrać plik. Są obsługiwane protokoły HTTP i HTTPS, a ścieżka muszą zezwalać na dostęp do odczytu. |
| --— Typ kompilacji — ścieżka aplikacji | Dla udostępniania tylko magazyn rodzaju obrazu. Ścieżka względna dla pakietu aplikacji w magazynie obraz określony podczas operacji wysyłania wcześniejszych. |
| — Nazwa typu aplikacji | Przechowywanie tylko dla rodzaju udostępniania zewnętrznego. Nazwa typu aplikacji reprezentuje nazwę typu aplikacji w manifeście aplikacji. |
| --Wersja typu aplikacji | Przechowywanie tylko dla rodzaju udostępniania zewnętrznego. Wersja typu aplikacji oznacza wersję tego typu aplikacji w manifeście aplikacji. |
| --udostępniania zewnętrznego | Lokalizacja, z gdzie pakiet aplikacji może być zarejestrowany lub udostępniane. Wskazuje, że świadczenia jest dla pakietu aplikacji, który został wcześniej przekazany do magazynu zewnętrznego. Pakiet aplikacji kończy się wyrazem *.sfpkg rozszerzenia. |
| --nie oczekiwania | Wskazuje, czy Inicjowanie obsługi administracyjnej powinna się odbyć asynchronicznie. <br><br> Jeśli równa PRAWDA, zwraca operacji inicjowania obsługi administracyjnej po zaakceptowaniu żądania przez system i operacji inicjowania obsługi administracyjnej będzie kontynuowana bez limitu czasu. Wartość domyślna to false. Dla dużych pakietów aplikacji zaleca się ustawiania wartości true. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-application-report-health"></a>Kondycja raport sfctl aplikacji
Wysyła raport o kondycji aplikacji sieci szkieletowej usług.

Raporty kondycji określonej aplikacji sieci szkieletowej usług. Raport musi zawierać informacje o źródle raport o kondycji i właściwości, na którym będzie zgłaszane. Raport jest wysyłany do bramy sieci szkieletowej usług aplikacji, która przekazuje do magazynu kondycji. Raport może być akceptowane przez bramę, ale odrzucone przez magazynu kondycji po dodatkowej weryfikacji. Na przykład magazynu kondycji może odrzucić raportu z powodu nieprawidłowego parametru, takich jak numer sekwencyjny przestarzałe. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, Pobierz kondycji aplikacji i sprawdź, czy raport jest wyświetlany.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. <br><br> Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --kondycji — właściwość [wymagane] | Właściwość informacje o kondycji. <br><br> Jednostka może mieć raportów o kondycji dla różnych właściwości. Właściwość jest ciągiem i nie stałej wyliczenia umożliwia elastyczność osoby zgłaszającej kategoryzację warunek stan raportu. Na przykład osoby zgłaszającej o ID "LocalWatchdog" można monitorować stan dysku w węźle, więc może raportować właściwości "AvailableDisk" w tym węźle. Tej samej osoby zgłaszającej można monitorować łączność węzła, więc może raportować właściwości "Łączności" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako zdarzenia oddzielne kondycji dla określonego węzła. Wraz z SourceId właściwość unikatowo identyfikuje informacje o kondycji. |
| --Kondycja [wymagane] | Możliwe wartości\: "Nieprawidłowe", "Ok", "Ostrzeżenie", "Błąd", "Nieznany". |
| — Identyfikator źródła [wymagane] | Nazwa źródła, która identyfikuje składnik klienta/programu alarmowego/systemu, który wygenerował informacji o kondycji. |
| — Opis | Opis informacji o kondycji. <br><br> Reprezentuje dowolny tekst służy do dodawania człowieka odczytać informacje na temat raportu. Maksymalna długość ciągu opisu jest znaków równą 4096. Jeśli podany string jest dłuższy, zostanie automatycznie obcięta. W przypadku obcięty, ostatnie znaki opisu zawiera znacznik "[Truncated]", a rozmiar całkowitą ciągu jest znaków równą 4096. Obecność znacznika wskazuje użytkownikom tego obcięcie wystąpił. Należy pamiętać, że gdy obcięty, opis ma mniej niż 4096 znaków z oryginalnego ciągu. |
| --bezpośrednim | Flaga wskazująca, czy mają być wysyłane raport natychmiast. <br><br> Raport o kondycji są wysyłane do aplikacji, która przekazuje do magazynu kondycji bramy sieci szkieletowej usług. Jeśli Immediate ma ustawioną wartość true, raport jest wysyłany bezpośrednio z bramy HTTP w magazynie kondycji, niezależnie od ustawień klienta sieci szkieletowej, których używa aplikacja bramy HTTP. Jest to przydatne w przypadku krytyczne raportów, które mają być wysyłane tak szybko, jak to możliwe. W zależności od czasu i innych warunków wysłaniem raportu może nadal się nie powieść, na przykład jeśli HTTP bramy został zamknięty lub komunikat nie nawiązać połączenia z bramą. Jeśli Immediate jest ustawiona na wartość false, raport jest wysyłany na podstawie kondycji ustawień klienta z bramy HTTP. W związku z tym go będzie można umieścić w partii zgodnie z konfiguracją HealthReportSendInterval. Jest to zalecane ustawienie ponieważ zezwala ona na kondycji klienta w celu zoptymalizowania wiadomości do magazynu kondycji, a także przetwarzania raportu kondycji raportowania kondycji. Domyślnie raporty nie są wysyłane bezpośrednio. |
| --ważność w przypadku usuwania | Wartość wskazująca, czy raport jest usuwany z magazynu kondycji po jego wygaśnięciu. <br><br> Jeśli ma wartość true, raport zostanie usunięty z magazynu kondycji po jego wygaśnięciu. Jeśli ma wartość false, raport jest traktowana jako błąd po wygaśnięciu. Wartość tej właściwości jest domyślnie false. Gdy klienci okresowo raport ustala RemoveWhenExpired FAŁSZ (ustawienie domyślne). W ten sposób jest reportera, który ma problemy (np. zakleszczenie) i nie można utworzyć raportu, obiekt jest oceniane w błąd, po wygaśnięciu raport o kondycji. Oznacza flagą jednostki jako błąd stanu kondycji. |
| --numer sekwencji | Numer sekwencji dla tego raportu kondycji jako ciąg numeryczny. <br><br> Numer sekwencyjny raportu jest używana przez magazynu kondycji do wykrywania starych raportów. Jeśli nie zostanie określony, numer kolejny został wygenerowany automatycznie przez klienta kondycji po dodaniu raportu. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |
| czas wygaśnięcia-- | Czas, przez jaki raport o kondycji są prawidłowe. To pole używa formatu ISO8601 służący do określania czasu trwania. <br><br> Gdy klienci okresowo raport one wysyłać raporty, z częstotliwością wyższe niż czas wygaśnięcia. Jeśli klienci raport dotyczący przejścia, ich ustawić czas wygaśnięcia do nieskończone. Po upływie czasu wygaśnięcia, zdarzenie kondycji, który zawiera informacje o kondycji jest usunięty z magazynu kondycji, jeśli RemoveWhenExpired jest true, lub na błąd, jeśli RemoveWhenExpired wartość false. Jeśli nie zostanie określony, czas wygaśnięcia wartości domyślnych do wartości nieskończonej. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-application-type"></a>Typ aplikacji sfctl
Pobiera listę typów aplikacji w klastrze usługi sieć szkieletowa dopasowania określonej nazwy.

Zwraca informacje o typach aplikacji, które są udostępniane lub właśnie zainicjowanie obsługi klastra sieci szkieletowej usług. Te wyniki są typów aplikacji, których nazwa pasuje dokładnie określonej w parametrze i które są zgodne z parametrami określonego zapytania. Wszystkie wersje typu aplikacji zgodnych z nazwą typu aplikacji są zwracane przez poszczególne wersje zwracane w postaci jednego typu aplikacji. Odpowiedź zawiera nazwę, wersję, stanu i inne szczegółowe informacje o typu aplikacji. Jest to stronicowane zapytanie, co oznacza, że jeśli nie wszystkie typy aplikacji mieści się na stronie jedną stronę wyników jest zwracana oraz token kontynuacji, który może służyć do pobrania następnej strony. Na przykład jeśli istnieją 10 typy aplikacji, ale strona dopasowuje tylko pierwszy typy aplikacji trzy lub wynik maksymalna jest ustawiona na 3, 3 jest zwracany. Aby uzyskać dostęp do pozostałej części wyników, należy pobrać stron sieci Web za pomocą token kontynuacji zwrócony w kwerendzie następny. Token kontynuacji pusty jest zwracany, gdy brak kolejnych stron.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --aplikacji typu nazwa-[wymagane] | Nazwa typu aplikacji. |
| --Wersja typu aplikacji | Wersja typu aplikacji. |
| --token kontynuacji | Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z niepustą wartość jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| — Wyklucz aplikacji parametrów | Flaga określająca, czy parametry aplikacji zostaną wykluczone z wyników. |
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

## <a name="sfctl-application-type-list"></a>Lista typów sfctl aplikacji
Pobiera listę typów aplikacji w klastrze usługi sieć szkieletowa usług.

Zwraca informacje o typach aplikacji, które są udostępniane lub właśnie zainicjowanie obsługi klastra sieci szkieletowej usług. Każda wersja programu typem aplikacji jest zwracana jako typ jedną aplikację. Odpowiedź zawiera nazwę, wersję, stanu i inne szczegółowe informacje o typu aplikacji. Jest to stronicowane zapytanie, co oznacza, że jeśli nie wszystkie typy aplikacji mieści się na stronie jedną stronę wyników jest zwracana oraz token kontynuacji, który może służyć do pobrania następnej strony. Na przykład jeśli istnieją 10 typy aplikacji, ale strona dopasowuje tylko pierwszy typy aplikacji trzy lub wynik maksymalna jest ustawiona na 3, 3 jest zwracany. Aby uzyskać dostęp do pozostałej części wyników, należy pobrać stron sieci Web za pomocą token kontynuacji zwrócony w kwerendzie następny. Token kontynuacji pusty jest zwracany, gdy brak kolejnych stron.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --aplikacji typu definicji rodzaj filtru | Używane do filtrowania na ApplicationTypeDefinitionKind, które to mechanizm służący do definiowania typu aplikacji sieci szkieletowej usług.  <br> -Domyślnie — wartość domyślna, która wykonuje tę samą funkcję co zaznaczenie "All". Wartość wynosi 0.  <br> -All - filtru pasującego do danych wejściowych z dowolną wartością ApplicationTypeDefinitionKind. Wartość jest 65535.  <br> -ServiceFabricApplicationPackage - filtru pasującego do danych wejściowych o wartości ApplicationTypeDefinitionKind ServiceFabricApplicationPackage. Wartość to 1.  <br> -Redaguj — filtr, który pasuje do danych wejściowych o wartości ApplicationTypeDefinitionKind tworzenia. Wartość jest równa 2. |
| --token kontynuacji | Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z niepustą wartość jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| — Wyklucz aplikacji parametrów | Flaga określająca, czy parametry aplikacji zostaną wykluczone z wyników. |
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

## <a name="sfctl-application-unprovision"></a>Wstrzymanie obsługi administracyjnej sfctl aplikacji
Usuwa lub wyrejestrowuje typu sieci szkieletowej usług aplikacji z klastra.

Usuwa lub wyrejestrowuje typu sieci szkieletowej usług aplikacji z klastra. Operację można wykonać tylko, jeśli zostały usunięte wszystkie wystąpienia aplikacji typu aplikacji. Gdy typem aplikacji jest zarejestrowany, nie nowych aplikacji można tworzyć jego wystąpień dla tego typu aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --aplikacji typu nazwa-[wymagane] | Nazwa typu aplikacji. |
| --— Typ — wersja aplikacji [wymagane] | Wersja typu aplikacji, zgodnie z definicją w manifeście aplikacji. |
| async — parametr | Flaga wskazująca, czy Cofnij aprowizację powinno nastąpić asynchronicznie. Jeśli równa PRAWDA i zwraca operacji Cofnij aprowizację po zaakceptowaniu żądania przez system i operacji Cofnij aprowizację będzie kontynuowana bez limitu czasu. Wartość domyślna to false. Jednak zaleca się ustawieniem dla niego wartość true dla dużych pakietów aplikacji udostępnionych. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-application-upgrade"></a>Uaktualnianie aplikacji sfctl
Uruchamia uaktualniania aplikacji w klastrze usługi sieć szkieletowa usług.

Sprawdza, parametry uaktualniania aplikacji dostarczony i rozpoczyna się uaktualnianie aplikacji, jeśli parametry są prawidłowe. Należy pamiętać, że opis uaktualniania zastępuje istniejące opis aplikacji. Oznacza to, że jeśli nie określono parametrów, istniejących parametrów w aplikacjach zostaną zastąpione listy parametrów puste. Spowoduje to aplikacji przy użyciu wartości domyślne parametrów w manifeście aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. <br><br> Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --Wersja aplikacji [wymagane] | Wersja aplikacji docelowej. |
| --parametrów [wymagane] | Zastępuje kodowany w formacie JSON lista parametrów aplikacji ma być stosowany podczas uaktualniania aplikacji. |
| --domyślne usług kondycji policy | Specyfikacja zasad dotyczących kondycji używany domyślnie do oceny kondycji usługi typu zakodowane JSON. |
| --Niepowodzenie akcji | Akcja do wykonania podczas uaktualniania monitorowanej napotka monitorowania kondycji lub zasad naruszenia zasad. |
| --force-restart | Wymuszone ponownie procesy podczas uaktualniania, nawet gdy wersja kodu nie została zmieniona. |
| --health-check-retry-timeout | Ilość czasu, aby ponowić próbę wykonania oceny kondycji aplikacji lub w klastrze jest zła przed Akcja błędu jest wykonywana. Mierzony w milisekundach.  Domyślna\: PT0H10M0S. |
| --health-check-stable-duration | Ilość czasu, że aplikacji lub w klastrze muszą pozostać dobrej kondycji przed uaktualnienia przechodzi do następnej domeny uaktualnienia. Mierzony w milisekundach.  Domyślna\: PT0H2M0S. |
| --health-check-wait-duration | Ilość czasu oczekiwania po zakończeniu uaktualnienia domeny przed zastosowaniem zasad dotyczących kondycji. Mierzony w milisekundach.  Domyślna\: 0. |
| --max-unhealthy-apps | Maksymalna dozwolona liczba procent zła wdrożone aplikacje. Reprezentowany jako liczbę z zakresu od 0 do 100. |
| --Tryb | Tryb służy do monitorowania kondycji w procesie uaktualnienia stopniowego.  Domyślna\: UnmonitoredAuto. |
| --replica-set-check-timeout | Maksymalną ilość czasu, aby zablokować przetwarzania domeny uaktualnienia i zapobiegania utracie dostępność, jeśli znajdują się nieoczekiwane problemy. Mierzony w sekundach. |
| --service-health-policy | Mapa z zasad dotyczących kondycji typu usługi na nazwy typu usługi zakodowane JSON. Mapy jest pusty jest domyślnie. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |
| — limit czasu domeny uaktualnienia | Ilość czasu każdej z domen musi zakończyć się przed wykonaniem FailureAction. Mierzony w milisekundach.  Domyślna\: P10675199DT02H48M05.4775807S. |
| — limit czasu uaktualniania | Ilość czasu ogólną uaktualnienia musi zakończyć się przed wykonaniem FailureAction. Mierzony w milisekundach.  Domyślna\: P10675199DT02H48M05.4775807S. |
| --warning-as-error | Traktuj ostrzeżenia oceny kondycji z tego samego ważność jako błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-application-upgrade-resume"></a>Uaktualnianie aplikacji sfctl-resume
Wznawia uaktualniania aplikacji w klastrze usługi sieć szkieletowa usług.

Wznawia uaktualnić niemonitorowane ręczne aplikacji sieci szkieletowej usług. Sieć szkieletowa usług uaktualnia domeny uaktualnienia pojedynczo. Niemonitorowane ręczne uaktualnień po domeny uaktualnienia sieci szkieletowej usług oczekuje można wywołać tego interfejsu API przed kontynuowaniem do następnej domeny uaktualnienia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --uaktualnienia — nazwa domeny [wymagane] | Nazwa domeny uaktualnień, w którym można wznowić uaktualnienia. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-application-upgrade-rollback"></a>Uaktualnianie aplikacji sfctl-wycofywania
Uruchamia wycofywanie uaktualnienia obecnie w toku aplikacji w klastrze usługi sieć szkieletowa usług.

Uruchamia wycofywanie bieżącej aplikacji uaktualnienie poprzedniej wersji. Ten interfejs API może służyć tylko do wycofać uaktualnienie w trakcie bieżącego, która wykonuje procedurę wycofywania do przodu do nowej wersji. Jeśli aplikacja nie jest obecnie trwa uaktualniony za pomocą interfejsu API StartApplicationUpgrade uaktualnić go do żądanej wersji, w tym wycofywanie poprzedniej wersji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-application-upgrade-status"></a>Stan uaktualnienia sfctl aplikacji
Pobiera szczegóły dla najnowszą aktualizację w tej aplikacji.

Zwraca informacje o stanie najnowszą aktualizację aplikacji oraz szczegóły, aby pomóc debugowania problemów kondycji aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-application-upload"></a>przekazywanie aplikacji sfctl
Skopiuj pakiet sieci szkieletowej usług aplikacji do magazynu obrazów.

Opcjonalnie można wyświetlić postępu przekazywania dla poszczególnych plików w pakiecie. Przekaż postępu są wysyłane do `stderr`.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Ścieżka [wymagane] | Ścieżka do pakietu aplikacji lokalnych. |
| --imagestore-string | Obraz docelowy magazynu do przekazania pakietu aplikacji do.  Domyślna\: sieci szkieletowej\:magazynu ImageStore. |
| — Pokaż postęp | Pokaż postęp przekazywania plików dla dużych pakietów. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |



## <a name="next-steps"></a>Kolejne kroki
- [Instalator](service-fabric-cli.md) sieci szkieletowej usług interfejsu wiersza polecenia.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).
