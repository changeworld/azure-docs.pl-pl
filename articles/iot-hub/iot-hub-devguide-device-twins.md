---
title: Zrozumienie twins urządzenia Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera - Użyj twins urządzenia do synchronizacji danych stanu i konfiguracji między centrum IoT i urządzeniami
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: c002685dfd3b8f86a8657b5d30dee29641cef932
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632880"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>W zrozumieniu i użytkowaniu twins urządzenie w Centrum IoT

*Urządzenie twins* są dokumentów JSON, w których są przechowywane informacje o stanie urządzenie tym metadanych, konfiguracji i warunki. Centrum IoT Azure obsługuje dwie urządzenia, dla każdego urządzenia, na którym jest nawiązywane połączenie z Centrum IoT. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

W tym artykule opisano:

* Struktura dwie urządzenia: *tagi*, *żądany* i *zgłosił właściwości*.
* Operacje, które aplikacje urządzenia i zaplecza, które można wykonywać na twins urządzenia.

Użyj twins urządzenia do:

* Przechowywane metadane dotyczące urządzeń w chmurze. Na przykład lokalizacja wdrożenia automaty maszyny.
* Bieżący stan informacji w raporcie, takie jak dostępne możliwości i warunków z aplikacją urządzenia. Na przykład urządzenie jest podłączone do Centrum IoT over komórkowej lub Wi-Fi.
* Synchronizuj stan przepływów pracy, długotrwałą między aplikacją urządzenia i aplikacji zaplecza. Na przykład podczas kopii rozwiązania zakończenia określa nowej wersji oprogramowania układowego do zainstalowania i aplikacji urządzenia raporty przez różne etapy procesu aktualizacji.
* Zapytanie z metadanych urządzeniami, konfiguracji lub stanu.

Zapoznaj się [wskazówki komunikację urządzenia do chmury] [ lnk-d2c-guidance] wskazówki dotyczące przy użyciu właściwości zgłoszone, wiadomości urządzenia do chmury lub przekazywania pliku.
Zapoznaj się [wskazówki dotyczące komunikacji chmury do urządzenia] [ lnk-c2d-guidance] wskazówki na temat używania żądanej właściwości, metody bezpośredniego lub komunikaty chmury do urządzenia.

## <a name="device-twins"></a>Twins urządzenia
Urządzenia twins przechowywać informacje dotyczące urządzeń który:

* Urządzenia i z powrotem kończy się służy do synchronizowania urządzenia warunki i konfiguracji.
* Zaplecze rozwiązania służy do zapytań i docelowego długotrwałą operacji.

Cykl życia dwie urządzenia jest połączony z odpowiadającego [tożsamości urządzenia][lnk-identity]. Urządzenie twins niejawnie są tworzone i usuwane po utworzeniu lub usunięciu w Centrum IoT tożsamości urządzenia.

Dwie urządzenia jest dokumentem JSON, który zawiera:

* **Tagi**. Sekcja zaplecza rozwiązania można odczytywać i zapisywać do dokumentu JSON. Tagi nie są widoczne dla aplikacji dla urządzeń.
* **Żądany właściwości**. Używać razem z właściwości zgłoszony do synchronizowania konfiguracji urządzenia lub warunków. Zaplecze rozwiązania można ustawić odpowiednie właściwości, oraz aplikacji urządzenia można je odczytać. Aplikacji urządzenia może również odbierać powiadomienia o zmianach w odpowiednich właściwościach.
* **Zgłoszone właściwości**. Umożliwia oraz odpowiednie właściwości synchronizacji konfiguracji urządzenia lub warunków. Aplikacji urządzenia można ustawić właściwości zgłoszone, a zaplecze rozwiązania może odczytywać i wyszukiwać w nich.
* **Właściwości tożsamości urządzenia**. Głównego dokumentu JSON dwie urządzenia zawiera właściwości tylko do odczytu z odpowiedniego tożsamości urządzenia przechowywany w [rejestru tożsamości][lnk-identity].

![][img-twin]

W poniższym przykładzie przedstawiono dwie urządzenia dokumentu JSON:

```json
{
    "deviceId": "devA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

W obiekcie głównym są urządzenia właściwości tożsamości i kontener obiektów na `tags` i oba `reported` i `desired` właściwości. `properties` Kontener zawiera niektóre elementy tylko do odczytu (`$metadata`, `$etag`, i `$version`) opisano w [metadane dwie urządzenia] [ lnk-twin-metadata] i [ Optymistycznej współbieżności] [ lnk-concurrency] sekcje.

### <a name="reported-property-example"></a>Przykład zgłoszony właściwości
W poprzednim przykładzie zawiera dwie urządzenia `batteryLevel` właściwość, która jest zgłoszony przez aplikację urządzenia. Ta właściwość umożliwia zapytania i działają na urządzeniach, na podstawie ostatniego poziomu zgłoszone baterii. Przykładami innych możliwości raportowania urządzenia aplikacji urządzenia lub opcji łączności.

> [!NOTE]
> Właściwości zgłoszone uprościć scenariuszy, w którym zaplecza rozwiązania jest zainteresowana ostatniej znanej wartości właściwości. Użyj [wiadomości urządzenia do chmury] [ lnk-d2c] zaplecza rozwiązania musi przetworzyć telemetrii urządzenia w postaci sekwencji zdarzeń oznaczony znacznikiem czasowym, takich jak szeregów czasowych.

### <a name="desired-property-example"></a>Przykład żądanej właściwości
W poprzednim przykładzie `telemetryConfig` potrzebne dwie urządzeń i właściwości zgłoszone są używane przez zaplecza rozwiązania i aplikacji urządzenia zsynchronizować konfiguracji dane telemetryczne dla tego urządzenia. Na przykład:

1. Zaplecze rozwiązania ustawia żądanej właściwości na wartość wymaganą konfiguracją. Poniżej przedstawiono fragment dokumentu przy użyciu zestawu żądanej właściwości:

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. Powiadomienie do aplikacji urządzenia zmiany natychmiast, jeśli połączone lub przy pierwszym próba ponownego połączenia. Aplikacji urządzenia następnie raporty zaktualizowanej konfiguracji (lub warunek błędu przy użyciu `status` właściwości). Poniżej przedstawiono część zgłoszonych właściwości:

    ```json
    ...
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ...
    ```

3. Zaplecze rozwiązania można śledzić wyniki operacji konfiguracji na wielu urządzeniach przez [badania] [ lnk-query] twins urządzenia.

> [!NOTE]
> Poprzedni fragmenty kodu są przykłady, zoptymalizowana pod kątem czytelności, z jednym ze sposobów kodowania Konfiguracja urządzenia i jego stan. Centrum IoT nie nakłada określonego schematu dla dwie urządzenia żądanego i podać właściwości w twins urządzenia.
> 
> 

Twins służy do synchronizowania długotrwałe operacje, takie jak aktualizacje oprogramowania układowego. Aby uzyskać więcej informacji na temat sposobu synchronizacji i śledzenia operacją wymagającą dużo czasu na urządzeniach za pomocą właściwości, zobacz [Użyj żądanego właściwości, aby skonfigurować urządzenia][lnk-twin-properties].

## <a name="back-end-operations"></a>Operacje zaplecza
Zaplecze rozwiązania działa na dwie urządzenia przy użyciu następujących operacji niepodzielnych za pośrednictwem protokołu HTTPS:

* **Pobrać dwie urządzenia za pomocą Identyfikatora**. Ta operacja zwraca dokument dwie urządzenia, tym tagów i właściwości żądaną i podać systemu.
* **Częściowego zaktualizowania dwie urządzenia**. Ta operacja umożliwia zaplecza rozwiązania celu częściowego zaktualizowania znaczników i odpowiednie właściwości w dwie urządzenia. Częściowej aktualizacji jest wyrażona jako dokument JSON, który dodaje lub aktualizuje dowolną właściwość. Wartość właściwości `null` zostaną usunięte. Poniższy przykład tworzy nową właściwość odpowiednią wartością `{"newProperty": "newValue"}`, spowoduje zastąpienie istniejącej wartości `existingProperty` z `"otherNewValue"`i usuwa `otherOldProperty`. Nie zmian do istniejących żądanej właściwości bądź tagi:

    ```json
    {
        "properties": {
            "desired": {
                "newProperty": {
                    "nestedProperty": "newValue"
                },
                "existingProperty": "otherNewValue",
                "otherOldProperty": null
            }
        }
    }
    ```

* **Zastąp odpowiednie właściwości**. Ta operacja umożliwia zaplecza rozwiązania całkowicie zastąpić wszystkie istniejące odpowiednie właściwości i Zastąp nowy dokument JSON dla `properties/desired`.
* **Zastąp znaczniki**. Ta operacja umożliwia zaplecza rozwiązania całkowicie zastąpić wszystkie istniejące znaczniki i Zastąp nowy dokument JSON dla `tags`.
* **Odbieranie powiadomień dwie**. Ta operacja pozwala zaplecza rozwiązania otrzymać powiadomienie, gdy dwie jest modyfikowany. Aby to zrobić, rozwiązania IoT musi utworzyć trasę i ustaw źródło danych *twinChangeEvents*. Domyślnie są wysyłane żadne powiadomienia dwie, oznacza to, że istnieje wstępnie ma takie tras. Jeśli szybkość zmian jest zbyt duża lub z innych powodów, takich jak wewnętrzne błędy, Centrum IoT może wysłać tylko jedno powiadomienie, który zawiera wszystkie zmiany. W związku z tym jeśli aplikacja wymaga niezawodnej inspekcji i rejestrowania wszystkich stanów pośredniego, należy użyć wiadomości urządzenia do chmury. Komunikat powiadomienia dwie zawiera właściwości, oraz i treść.

    - Właściwości

    | Name (Nazwa) | Wartość |
    | --- | --- |
    $content — typ | application/json |
    $iothub-enqueuedtime |  Czas wysłania powiadomienia |
    $iothub-message-source | twinChangeEvents |
    $content-kodowania | UTF-8 |
    deviceId | Identyfikator urządzenia |
    hubName | Nazwa centrum IoT |
    operationTimestamp | [ISO8601] sygnatury czasowej operacji |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" lub "updateTwin" |

    Właściwości systemu wiadomości są poprzedzane prefiksem `'$'` symbolu.

    - Treść
        
    Ta sekcja zawiera wszystkie zmiany dwie w formacie JSON. Używa tego samego formatu poprawek, z tą różnicą, że może zawierać wszystkie dwie sekcje: tagi, properties.reported properties.desired i czy zawiera on elementy "$metadata". Na przykład:

    ```json
    {
        "properties": {
            "desired": {
                "$metadata": {
                    "$lastUpdated": "2016-02-30T16:24:48.789Z"
                },
                "$version": 1
            },
            "reported": {
                "$metadata": {
                    "$lastUpdated": "2016-02-30T16:24:48.789Z"
                },
                "$version": 1
            }
        }
    }
    ```

Obsługuje wszystkie poprzednie operacje [optymistycznej współbieżności] [ lnk-concurrency] i wymagają **ServiceConnect** uprawnienia, zgodnie z definicją w [zabezpieczeń] [ lnk-security] artykułu.

Oprócz tych operacji można zaplecza rozwiązania:

* Zapytanie twins urządzenia przy użyciu przypominającego SQL [język zapytań Centrum IoT][lnk-query].
* Wykonywanie operacji na dużych zestawów twins urządzenia przy użyciu [zadania][lnk-jobs].

## <a name="device-operations"></a>Operacje urządzenia
Aplikacja urządzenie działa na dwie urządzenia przy użyciu operacji niepodzielnych w następujących:

* **Pobrać dwie urządzenia**. Ta operacja zwraca dokument dwie urządzenia (w tym znaczniki i właściwości żądaną i podać system) dla aktualnie podłączonego urządzenia.
* **Częściowego zaktualizowania właściwości zgłoszone**. Ta operacja umożliwia częściową aktualizację zgłoszone właściwości podłączonego urządzenia. Ta operacja używa tego samego formatu aktualizacji JSON, że rozwiązanie kopię celu zastosowania częściowej aktualizacji żądanej właściwości.
* **Sprawdź odpowiednie właściwości**. Aktualnie podłączonego urządzenia można otrzymywać powiadomienia o aktualizacjach do żądanej właściwości, gdy wystąpią. Urządzenie odbiera tego samego formularza aktualizacji (pełnych lub wymiana) wykonywane przez zaplecza rozwiązania.

Wszystkie poprzednie operacje wymagają **DeviceConnect** uprawnienia, zgodnie z definicją w [zabezpieczeń] [ lnk-security] artykułu.

[Urządzenia Azure IoT SDK] [ lnk-sdks] ułatwiają używać poprzedniej operacji z wielu języków i platform. Aby uzyskać więcej informacji, w szczegółach Centrum IoT w nim elementów podstawowych synchronizacji żądanej właściwości, zobacz [przepływu ponowne łączenie urządzenia][lnk-reconnection].

## <a name="tags-and-properties-format"></a>Format znaczników i właściwości
Znaczniki, odpowiednie właściwości i zgłoszone właściwości są obiektów JSON z następującymi ograniczeniami:

* Wszystkie klucze w obiektów JSON jest rozróżniana wielkość liter 64 bajtów ciągów UNICODE UTF-8. Dozwolone znaki kontrolne UNICODE (segmenty C0 i C1), Wyklucz znaków i `'.'`, `' '`, i `'$'`.
* Wszystkie wartości w formacie JSON obiekty mogą być następujące typy JSON: wartość logiczna, liczba, ciąg, obiekt. Tablice nie są dozwolone. Maksymalna wartość dla liczb całkowitych jest 4503599627370495 i-4503599627370496 jest minimalny liczb całkowitych.
* Wszystkie obiekty JSON w tagach, żądane i podać właściwości mogą mieć maksymalną głębokość 5. Na przykład następujący obiekt jest prawidłowy:

    ```json
    {
        ...
        "tags": {
            "one": {
                "two": {
                    "three": {
                        "four": {
                            "five": {
                                "property": "value"
                            }
                        }
                    }
                }
            }
        },
        ...
    }
    ```

* Wszystkie wartości ciągu może mieć maksymalnie 4 KB długości.

## <a name="device-twin-size"></a>Rozmiar dwie urządzenia
Centrum IoT wymusza ograniczenie rozmiaru 8KB na wszystkich odpowiednich wartości całkowitej `tags`, `properties/desired`, i `properties/reported`, z wyjątkiem elementów tylko do odczytu.
Rozmiar jest obliczany poprzez zliczanie wszystkie znaki oprócz znaków sterujących UNICODE (segmenty C0 i C1) i spacje, które znajdują się poza stałe typu string.
Centrum IoT z powodu błędu odrzuca wszystkie operacje, które spowoduje zwiększenie rozmiaru tych dokumentów powyżej limitu.

## <a name="device-twin-metadata"></a>Metadane dwie urządzenia
Centrum IoT przechowuje sygnatura czasowa ostatniej aktualizacji dla każdego obiektu JSON w dwie urządzenia żądanego i podać właściwości. Sygnatury czasowe są w UTC i kodowany w [ISO8601] format `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Na przykład:

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                }
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Ta informacja jest przechowywana na każdym poziomie (nie tylko liście strukturze JSON) do zachowania aktualizacji, które usunąć obiekt kluczy.

## <a name="optimistic-concurrency"></a>Optymistyczna współbieżność
Tagi, potrzeby i podać właściwości wszystkich optymistycznej współbieżności pomocy technicznej.
Tagi przypada tag ETag jako [RFC7232], reprezentujący reprezentacja JSON znacznika. Elementy etag w operacjach aktualizowania warunkowego z zaplecza rozwiązania służy do zapewnienia spójności.

Dwie urządzenia żądanego i podać właściwości nie ma elementy etag, ale `$version` wartość, która może być przyrostowe. Podobnie do tag ETag wersji mogą być używane przez stronę aktualizacji do wymuszania zgodności aktualizacji. Na przykład aplikacji urządzenia zgłoszonego właściwości lub zaplecza rozwiązania dla żądanej właściwości.

Wersje są także przydatne, gdy observing agenta (np. aplikacji urządzenia obserwowania odpowiednie właściwości) należy uzgodnić szczepy między wynik operacji pobierania i powiadomienie o aktualizacji. Sekcja [przepływu ponowne łączenie urządzenia] [ lnk-reconnection] zawiera więcej informacji.

## <a name="device-reconnection-flow"></a>Przepływ ponowne nawiązanie połączenia urządzenia
Centrum IoT nie zachowa powiadomienia o aktualizacji odpowiednie właściwości dla urządzeń bez połączenia. Wynika, że urządzenia, które nawiązuje połączenie musi pobrać pełną odpowiednie właściwości dokumentu, oprócz subskrypcji powiadomienia o aktualizacji. Możliwość szczepy między powiadomienia o aktualizacji i pobieranie pełnej, należy zapewnić następujący przepływ:

1. Łączy się z Centrum IoT z aplikacji urządzenia.
2. Subskrybuje aplikacji urządzenia dla żądanej właściwości powiadomienia o aktualizacji.
3. Aplikacji urządzenia pobiera pełnego dokumentu dla żądanej właściwości.

Zignorować wszystkie powiadomienia z aplikacji urządzenia `$version` mniejsza lub równa niż wersja pełnego dokumentu pobrane. Takie podejście jest możliwa, ponieważ gwarantuje Centrum IoT wersje zawsze zwiększyć.

> [!NOTE]
> Istotą takiej logiki jest już zaimplementowany w [urządzenia Azure IoT SDK][lnk-sdks]. Ten opis jest przydatne tylko w przypadku aplikacji urządzenia nie można użyć dowolnego urządzenia Azure IoT zestawy SDK i należy zaprogramować interfejsu MQTT bezpośrednio.
> 
> 

## <a name="additional-reference-material"></a>Odwołanie dodatkowe materiały
Inne tematy referencyjne w Podręczniku dewelopera Centrum IoT obejmują:

* [Punkty końcowe Centrum IoT] [ lnk-endpoints] artykule opisano różne punkty końcowe, które udostępnia każdego centrum IoT dla operacji zarządzania i środowiska wykonawczego.
* [Ograniczenia przepustowości i przydziały] [ lnk-quotas] artykule przydziałów, które dotyczą usługi IoT Hub i ograniczania przepustowości zachowania można oczekiwać podczas korzystania z usługi.
* [Zestawów SDK urządzeń i usług Azure IoT] [ lnk-sdks] artykule wymieniono języka różnych zestawów SDK, można użyć podczas opracowywania aplikacji usług i urządzeń, które współdziałają z Centrum IoT.
* [Centrum IoT zapytania języka twins urządzenia, zadania i rozsyłania wiadomości] [ lnk-query] artykule język zapytań Centrum IoT można pobrać z Centrum IoT informacji o twins urządzenia i zadania.
* [Obsługa MQTT Centrum IoT] [ lnk-devguide-mqtt] artykuł zawiera więcej informacji na temat obsługi protokołu MQTT Centrum IoT.

## <a name="next-steps"></a>Kolejne kroki
Teraz wiesz już, o twins urządzenie, może Cię zainteresować następujące tematy przewodnik dewelopera Centrum IoT:

* [W zrozumieniu i użytkowaniu twins modułu w Centrum IoT][lnk-module-twins]
* [Wywoływanie metody bezpośrednio na urządzeniu][lnk-methods]
* [Planowanie zadań na wielu urządzeniach][lnk-jobs]

Aby wypróbować pojęcia opisane w tym artykule, zobacz następujące samouczki Centrum IoT:

* [Jak używać dwie urządzenia][lnk-twin-tutorial]
* [Sposób użycia właściwości dwie urządzenia][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#device-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-module-twins]:iot-hub-devguide-module-twins.md

[img-twin]: media/iot-hub-devguide-device-twins/twin.png
