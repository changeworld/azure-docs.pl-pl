---
title: Azure Service Fabric interfejsu wiersza polecenia sfctl repliki | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi sieć szkieletowa sfctl repliki.
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
ms.openlocfilehash: cd09fe906f77bb06f0ac7afaa6c6cce326dbfa5c
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763582"
---
# <a name="sfctl-replica"></a>sfctl replica
Zarządzanie replik, które należą do partycji usługi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Wdrożony | Pobiera szczegóły repliki wdrożone w węźle sieci szkieletowej usług. |
| wdrożone listy | Pobiera listę replik wdrożone w węźle sieci szkieletowej usług. |
| kondycja | Pobiera kondycji sieci szkieletowej usług repliki usługi stanowej lub wystąpienia usługi bezstanowej. |
| informacje | Pobiera informacje o repliki partycji usługi sieć szkieletowa usług. |
| lista | Pobiera informacje o replik partycji usługi sieć szkieletowa usług. |
| usuń | Usuwa replikę usługi uruchomione w węźle. |
| report-health | Wysyła raport o kondycji w replice sieci szkieletowej usług. |
| Ponowne uruchomienie | Uruchamia ponownie usługi repliki usługi utrwalonego uruchomionej w węźle. |

## <a name="sfctl-replica-deployed"></a>Replika sfctl wdrożony
Pobiera szczegóły repliki wdrożone w węźle sieci szkieletowej usług.

Pobiera szczegóły repliki wdrożone w węźle sieci szkieletowej usług. Informacje obejmują typ usługi, nazwę usługi, bieżącą operację usługi, uruchom bieżącą operację usługi Data i godzina, identyfikator partycji: identyfikator repliki i wystąpienia, zgłoszone obciążenia i inne informacje.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| — identyfikator repliki [wymagane] | Identyfikator repliki. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-replica-deployed-list"></a>Replika sfctl wdrożone — listy
Pobiera listę replik wdrożone w węźle sieci szkieletowej usług.

Pobiera listę zawierającą informacje o replik wdrożone w węźle sieci szkieletowej usług. Informacje zawierają identyfikator partycji, identyfikator repliki, stan repliki, nazwę usługi, nazwa typu usługi oraz inne informacje. Parametry PartitionId lub elementy ServiceManifestName zapytania do zwracania informacji dotyczących wdrożonych replik dopasowania określonej wartości tych parametrów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| — Identyfikator partycji | Tożsamość partycji. |
| --nazwa_usługi manifestu | Nazwa manifestu usługi, w zarejestrowany jako część typu aplikacji w klastrze usługi sieć szkieletowa usług. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-replica-health"></a>sfctl repliki kondycji
Pobiera kondycji sieci szkieletowej usług repliki usługi stanowej lub wystąpienia usługi bezstanowej.

Pobiera kondycji sieci szkieletowej usług repliki. Filtr EventsHealthStateFilter umożliwia filtrowanie zbierania zdarzeń kondycji zgłoszonych repliki na podstawie stanu kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| — identyfikator repliki [wymagane] | Identyfikator repliki. |
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

## <a name="sfctl-replica-info"></a>informacje o repliki sfctl
Pobiera informacje o repliki partycji usługi sieć szkieletowa usług.

Odpowiedź zawiera identyfikator, roli, stanu, kondycji, nazwa węzła, czas pracy i inne szczegółowe informacje o repliki.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| — identyfikator repliki [wymagane] | Identyfikator repliki. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-replica-list"></a>Lista repliki sfctl
Pobiera informacje o replik partycji usługi sieć szkieletowa usług.

Punkt końcowy GetReplicas zwraca informacje na temat replik określonej partycji. Odpowiedź zawiera identyfikator, roli, stanu, kondycji, nazwa węzła, czas pracy i inne szczegółowe informacje o repliki.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --token kontynuacji | Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z niepustą wartość jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-replica-remove"></a>Usuń replikę sfctl
Usuwa replikę usługi uruchomione w węźle.

Ten interfejs API symuluje awarii repliki usługi sieć szkieletowa poprzez usunięcie repliki z klastra sieci szkieletowej usług. Usunięcie zamyka repliki, przejścia do roli repliki None, a następnie usuwa wszystkie informacje stanu repliki z klastra. Ten interfejs API testów ścieżki usunięcie stanu repliki i symuluje ścieżka trwały błąd raportu za pośrednictwem interfejsów API klienta. Ostrzeżenie - dostępne są wykonywane, gdy jest używany ten interfejs API nie są sprawdzane bezpieczeństwa. Nieprawidłowe użycie tego interfejsu API może prowadzić do utraty danych dla usług stanowych. Ponadto flagi forceRemove ma wpływ na inne replik hostowanych w tym samym procesie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| — identyfikator repliki [wymagane] | Identyfikator repliki. |
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

## <a name="sfctl-replica-report-health"></a>Raport repliki sfctl-kondycji
Wysyła raport o kondycji w replice sieci szkieletowej usług.

Stan kondycji raporty określona replika sieci szkieletowej usług. Raport musi zawierać informacje o źródle raport o kondycji i właściwości, na którym będzie zgłaszane. Raport jest wysyłany do bramy repliki, który przesyła dalej w magazynie kondycji sieci szkieletowej usług. Raport może być akceptowane przez bramę, ale odrzucone przez magazynu kondycji po dodatkowej weryfikacji. Na przykład magazynu kondycji może odrzucić raportu z powodu nieprawidłowego parametru, takich jak numer sekwencyjny przestarzałe. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, sprawdź, czy raport jest wyświetlany w sekcji zdarzenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --kondycji — właściwość [wymagane] | Właściwość informacje o kondycji. <br><br> Jednostka może mieć raportów o kondycji dla różnych właściwości. Właściwość jest ciągiem i nie stałej wyliczenia umożliwia elastyczność osoby zgłaszającej kategoryzację warunek stan raportu. Na przykład osoby zgłaszającej o ID "LocalWatchdog" można monitorować stan dysku w węźle, więc może raportować właściwości "AvailableDisk" w tym węźle. Tej samej osoby zgłaszającej można monitorować łączność węzła, więc może raportować właściwości "Łączności" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako zdarzenia oddzielne kondycji dla określonego węzła. Wraz z SourceId właściwość unikatowo identyfikuje informacje o kondycji. |
| --Kondycja [wymagane] | Możliwe wartości\: "Nieprawidłowe", "Ok", "Ostrzeżenie", "Błąd", "Nieznany". |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| — identyfikator repliki [wymagane] | Tożsamość partycji. |
| — Identyfikator źródła [wymagane] | Nazwa źródła, która identyfikuje składnik klienta/programu alarmowego/systemu, który wygenerował informacji o kondycji. |
| — Opis | Opis informacji o kondycji. <br><br> Reprezentuje dowolny tekst służy do dodawania człowieka odczytać informacje na temat raportu. Maksymalna długość ciągu opisu jest znaków równą 4096. Jeśli podany string jest dłuższy, zostanie automatycznie obcięta. W przypadku obcięty, ostatnie znaki opisu zawiera znacznik "[Truncated]", a rozmiar całkowitą ciągu jest znaków równą 4096. Obecność znacznika wskazuje użytkownikom tego obcięcie wystąpił. Należy pamiętać, że gdy obcięty, opis ma mniej niż 4096 znaków z oryginalnego ciągu. |
| --bezpośrednim | Flaga wskazująca, czy mają być wysyłane raport natychmiast. <br><br> Raport o kondycji są wysyłane do aplikacji, która przekazuje do magazynu kondycji bramy sieci szkieletowej usług. Jeśli Immediate ma ustawioną wartość true, raport jest wysyłany bezpośrednio z bramy HTTP w magazynie kondycji, niezależnie od ustawień klienta sieci szkieletowej, których używa aplikacja bramy HTTP. Jest to przydatne w przypadku krytyczne raportów, które mają być wysyłane tak szybko, jak to możliwe. W zależności od czasu i innych warunków wysłaniem raportu może nadal się nie powieść, na przykład jeśli HTTP bramy został zamknięty lub komunikat nie nawiązać połączenia z bramą. Jeśli Immediate jest ustawiona na wartość false, raport jest wysyłany na podstawie kondycji ustawień klienta z bramy HTTP. W związku z tym go będzie można umieścić w partii zgodnie z konfiguracją HealthReportSendInterval. Jest to zalecane ustawienie ponieważ zezwala ona na kondycji klienta w celu zoptymalizowania wiadomości do magazynu kondycji, a także przetwarzania raportu kondycji raportowania kondycji. Domyślnie raporty nie są wysyłane bezpośrednio. |
| --ważność w przypadku usuwania | Wartość wskazująca, czy raport jest usuwany z magazynu kondycji po jego wygaśnięciu. <br><br> Jeśli ma wartość true, raport zostanie usunięty z magazynu kondycji po jego wygaśnięciu. Jeśli ma wartość false, raport jest traktowana jako błąd po wygaśnięciu. Wartość tej właściwości jest domyślnie false. Gdy klienci okresowo raport ustala RemoveWhenExpired FAŁSZ (ustawienie domyślne). W ten sposób jest reportera, który ma problemy (np. zakleszczenie) i nie można utworzyć raportu, obiekt jest oceniane w błąd, po wygaśnięciu raport o kondycji. Oznacza flagą jednostki jako błąd stanu kondycji. |
| --numer sekwencji | Numer sekwencji dla tego raportu kondycji jako ciąg numeryczny. <br><br> Numer sekwencyjny raportu jest używana przez magazynu kondycji do wykrywania starych raportów. Jeśli nie zostanie określony, numer kolejny został wygenerowany automatycznie przez klienta kondycji po dodaniu raportu. |
| — Typ usługi | Rodzaj operacji repliki usługi (bezstanowych lub stateful), dla którego zgłaszania kondycji. Poniżej przedstawiono możliwe wartości\: "Bezstanowych", "Wartość".  Domyślna\: Stateful. |
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

## <a name="sfctl-replica-restart"></a>ponowne uruchomienie repliki sfctl
Uruchamia ponownie usługi repliki usługi utrwalonego uruchomionej w węźle.

Uruchamia ponownie usługi repliki usługi utrwalonego uruchomionej w węźle. Ostrzeżenie - dostępne są wykonywane, gdy jest używany ten interfejs API nie są sprawdzane bezpieczeństwa. Nieprawidłowe użycie tego interfejsu API może prowadzić do utraty dostępności usług stanowych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| — identyfikator repliki [wymagane] | Identyfikator repliki. |
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
