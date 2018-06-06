---
title: Azure Usługa sieci szkieletowej interfejsu wiersza polecenia sfctl węzłami | Dokumentacja firmy Microsoft
description: Informacje dotyczące poleceń węzła sfctl interfejsu wiersza polecenia usługi sieci szkieletowej.
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
ms.openlocfilehash: fb8a310a131938e95f3d21b3962dbbd1944a57ed
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763429"
---
# <a name="sfctl-node"></a>sfctl node
Zarządzanie węzły, które tworzą klaster.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| wyłącz | Dezaktywowanie z docelowa dezaktywacji określonego węzła klastra sieci szkieletowej usług. |
| włącz | Aktywowanie usługi sieć szkieletowa węzła klastra, który jest obecnie wyłączona. |
| kondycja | Pobiera stan węzła sieci szkieletowej usług. |
| informacje | Pobiera informacje dotyczące określonego węzła w klastrze usługi sieć szkieletowa usług. |
| lista | Pobiera listę węzłów w klastrze usługi sieć szkieletowa usług. |
| ładowanie | Pobiera informacje o obciążenia węzła sieci szkieletowej usług. |
| remove-state | Powiadamia sieci szkieletowej usług, że stan utrwalony na węźle trwale ani nie usunięto utracone. |
| report-health | Wysyła raport o kondycji w węźle sieci szkieletowej usług. |
| Ponowne uruchomienie | Ponowne uruchomienie węzła klastra sieci szkieletowej usług. |
| przejścia | Uruchomienia lub zatrzymania węzła klastra. |
| stan przejścia | Pobiera postęp operacji uruchomiony przy użyciu StartNodeTransition. |

## <a name="sfctl-node-disable"></a>Wyłącz węzeł sfctl
Dezaktywowanie z docelowa dezaktywacji określonego węzła klastra sieci szkieletowej usług.

Dezaktywowanie z docelowa dezaktywacji określonego węzła klastra sieci szkieletowej usług. Po dezaktywacji jest w toku, docelowa dezaktywacji mogą być zwiększona, ale nie zmniejszyć (na przykład węzeł, który jest dezaktywowana z zamiarem Wstrzymaj można dezaktywować dalsze o ponowne uruchomienie, ale nie odwrotnie. Węzły mogą ponownie aktywować przy użyciu Aktywuj operacji węzła po ich zostaną wyłączone. Jeśli dezaktywację nie zostało zakończone, spowoduje to anulowanie dezaktywację. Węzeł ulegnie awarii, a następnie wróci do sprawności podczas dezaktywowane nadal będzie konieczne będzie ponownie aktywować przed usług zostaną umieszczone w tym węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| --docelowa dezaktywacji | Opisuje zamierzone lub powód dezaktywacji węzła. Możliwe wartości są następujące. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-node-enable"></a>Włącz węzła sfctl
Aktywowanie usługi sieć szkieletowa węzła klastra, który jest obecnie wyłączona.

Aktywuje węzłem klastra usługi sieć szkieletowa, który jest obecnie wyłączona. Po aktywacji, węzeł ponownie staną się działało celu wprowadzania nowych replik i będzie ponownie aktywować wszystkie repliki dezaktywowane na węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-node-health"></a>sfctl węzła kondycji
Pobiera stan węzła sieci szkieletowej usług.

Pobiera stan węzła sieci szkieletowej usług. Filtr EventsHealthStateFilter umożliwia filtrowanie zbierania zdarzeń kondycji zgłoszonych w węźle na podstawie stanu kondycji. Jeśli węzeł, który jest określany na podstawie nazwy nie istnieje w magazynie kondycji, to zwróci błąd.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenie, może to być kombinacją te wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero.  <br> -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2.  <br> — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4.  <br> -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8.  <br> -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-node-info"></a>informacje o węźle sfctl
Pobiera informacje dotyczące określonego węzła w klastrze usługi sieć szkieletowa usług.

Pobiera informacje dotyczące określonego węzła w klastrze usługi sieć szkieletowa. Odpowiedź zawiera nazwę, stan, identyfikator, kondycji, czas pracy i inne szczegóły dotyczące węzła.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-node-list"></a>sfctl węzła listy
Pobiera listę węzłów w klastrze usługi sieć szkieletowa usług.

Pobiera listę węzłów w klastrze usługi sieć szkieletowa usług. Odpowiedź zawiera nazwę, stan, identyfikator, kondycji, czas pracy i inne szczegóły dotyczące węzła.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --token kontynuacji | Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z niepustą wartość jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| --node-status-filter | Umożliwia filtrowanie oparte na NodeStatus węzłów. Tylko węzły, które są zgodne z wartością określony filtr zostanie zwrócony. Wartość filtru może być jedną z następujących czynności.  Domyślne\: domyślne. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-node-load"></a>sfctl węzła obciążenia
Pobiera informacje o obciążenia węzła sieci szkieletowej usług.

Pobiera informacje o obciążenia węzła sieci szkieletowej usług dla wszystkich metryki obciążenia lub zdefiniowano zdolności produkcyjnych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-node-remove-state"></a>Usuń węzeł sfctl stanu
Powiadamia sieci szkieletowej usług, że stan utrwalony na węźle trwale ani nie usunięto utracone.

Powiadamia sieci szkieletowej usług, że stan utrwalony na węźle trwale ani nie usunięto utracone.  Oznacza to, że nie jest możliwe jest odzyskanie stanu utrwalonego tego węzła. Zazwyczaj dzieje się tak, jeśli dysk twardy została wyczyszczona czystą lub jeśli wystąpiła awaria dysku twardego. Węzeł musi być wyłączony dla tej operacji do pomyślnego. Ta operacja pozwala replik w tym węźle nie jest już istnieje, a oczekiwanie na tych replik przywracane powinna zostać przerwana w tej sieci szkieletowej usług sieci szkieletowej usług. Nie uruchamiaj tego polecenia cmdlet, jeśli w węźle stan nie został usunięty i węzeł może przywracane jej stanem bez zmian.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-node-report-health"></a>węzeł sfctl Raport kondycji
Wysyła raport o kondycji w węźle sieci szkieletowej usług.

Raporty kondycji określonego węzła sieci szkieletowej usług. Raport musi zawierać informacje o źródle raport o kondycji i właściwości, na którym będzie zgłaszane. Raport jest wysyłany do węzła bramy sieci szkieletowej usług, który przesyła dalej w magazynie kondycji. Raport może być akceptowane przez bramę, ale odrzucone przez magazynu kondycji po dodatkowej weryfikacji. Na przykład magazynu kondycji może odrzucić raportu z powodu nieprawidłowego parametru, takich jak numer sekwencyjny przestarzałe. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, sprawdź, czy raport jest wyświetlany w sekcji HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --kondycji — właściwość [wymagane] | Właściwość informacje o kondycji. <br><br> Jednostka może mieć raportów o kondycji dla różnych właściwości. Właściwość jest ciągiem i nie stałej wyliczenia umożliwia elastyczność osoby zgłaszającej kategoryzację warunek stan raportu. Na przykład osoby zgłaszającej o ID "LocalWatchdog" można monitorować stan dysku w węźle, więc może raportować właściwości "AvailableDisk" w tym węźle. Tej samej osoby zgłaszającej można monitorować łączność węzła, więc może raportować właściwości "Łączności" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako zdarzenia oddzielne kondycji dla określonego węzła. Wraz z SourceId właściwość unikatowo identyfikuje informacje o kondycji. |
| --Kondycja [wymagane] | Możliwe wartości\: "Nieprawidłowe", "Ok", "Ostrzeżenie", "Błąd", "Nieznany". |
| --Nazwa węzła [wymagane] | Nazwa węzła do raportowania. |
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

## <a name="sfctl-node-restart"></a>ponowne uruchomienie węzła sfctl
Ponowne uruchomienie węzła klastra sieci szkieletowej usług.

Uruchamia ponownie usługi sieć szkieletowa węzła klastra, który jest już uruchomiony.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| — Utwórz — sieci szkieletowej — zrzutu | Określ wartość True, aby utworzyć zrzutu procesu węzła sieci szkieletowej. To jest rozróżniana wielkość liter.  Domyślna\: wartość False. |
| --node-instance-id | Identyfikator wystąpienia węzła docelowego. Jeśli identyfikator wystąpienia jest określony węzeł jest uruchomiony ponownie tylko wtedy, gdy jest on zgodny z bieżącym wystąpieniem węzła. Wartość domyślna "0" spowoduje dopasowanie dowolnej identyfikatora wystąpienia. Identyfikator wystąpienia można uzyskać za pomocą get węzła zapytania.  Domyślna\: 0. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-node-transition"></a>Przejście węzła sfctl
Uruchomienia lub zatrzymania węzła klastra.

Uruchomienia lub zatrzymania węzła klastra.  Węzeł klastra jest procesem, nie wystąpienie systemu operacyjnego, samej siebie.  Do uruchomienia węzła, podaj "Start" dla parametru NodeTransitionType. Aby zatrzymać węzła, podaj "Stop" dla parametru NodeTransitionType. Ten interfejs API rozpoczyna operację — gdy węzeł może nie mieć zakończyło się jeszcze przejście zwraca interfejsu API. Wywołanie GetNodeTransitionProgress z tej samej OperationId uzyskać postęp operacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --węzeł-— identyfikator wystąpienia [wymagane] | Identyfikator wystąpienia węzła węzeł docelowy. Można to ustalić za pośrednictwem interfejsu API GetNodeInfo. |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| --przejścia typu węzła [wymagane] | Wskazuje typ przejścia do wykonania.  NodeTransitionType.Start spowoduje uruchomienie zatrzymanego węzła. NodeTransitionType.Stop spowoduje zatrzymanie węzła, który jest włączony. |
| — Identyfikator operacji [wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  To jest przekazywany do odpowiedniego interfejsu API GetProgress. |
| --stop-czas trwania w — sekund [wymagane] | Czas w sekundach, aby zachować węzeł zatrzymana.  Wartość minimalna wynosi 600, wartość maksymalna to 14400.  Po upływie tego czasu, węzeł zostanie automatycznie przywracane. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-node-transition-status"></a>węzeł sfctl przejścia — stan
Pobiera postęp operacji uruchomiony przy użyciu StartNodeTransition.

Pobiera postęp operacji wprowadzenie StartNodeTransition przy użyciu podanego OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| — Identyfikator operacji [wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  To jest przekazywany do odpowiedniego interfejsu API GetProgress. |
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
- [Instalator](service-fabric-cli.md) sieci szkieletowej usług interfejsu wiersza polecenia.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).