---
title: Zrozumienie twins modułu Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera - twins moduł używany do synchronizacji danych stanu i konfiguracji między centrum IoT i urządzeniami
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 71d762b6f1c199db17058ac107aad7a0b3260ae7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633499"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>W zrozumieniu i użytkowaniu twins modułu w Centrum IoT

W tym artykule przyjęto przeczytane [w zrozumieniu i użytkowaniu twins urządzenie w Centrum IoT] [ lnk-devguide-device-twins] pierwszy. W Centrum IoT w ramach każdej tożsamości urządzenia można utworzyć maksymalnie 20 tożsamości modułu. Każda tożsamość moduł generuje niejawnie dwie modułu. Bardzo podobne do twins urządzenia, są twins modułu dokumentów JSON, które przechowują metadane, konfiguracji i warunki w tym informacje o stanie modułu. Centrum IoT Azure obsługuje dwie moduł, dla każdego modułu podłączoną do Centrum IoT. 

Na stronie urządzenia zestawy SDK urządzenia IoT Hub umożliwiają tworzenie modułów, których każda otwiera niezależnie od połączenia z Centrum IoT. Pozwala na użycie oddzielnych przestrzeni nazw dla różnych składników na urządzeniu. Na przykład masz na komputerze automaty, który ma trzy różne czujników. Każdy czujnik jest kontrolowana przez różnych działów w firmie. Można utworzyć modułu dla każdej czujnika. W ten sposób każdy dział jest tylko możliwość wysyłania zadań lub metody bezpośredniego czujnika decydować, co pozwala uniknąć konfliktów i błędy użytkownika.

 Moduł tożsamości i moduł dwie zapewnia te same możliwości jak tożsamości urządzenia i dwie urządzenia, ale dokładniej. To bardziej szczegółowy umożliwia urządzeń obsługujących, takie jak system operacyjny na podstawie urządzeń lub urządzeń oprogramowania układowego zarządzania wiele składników, aby odizolować konfiguracji i warunki dla każdego z tych składników. Moduł tożsamości i twins modułu zapewniają separacji zarządzania, podczas pracy z urządzeniami IoT, które składniki oprogramowania modułowych. Firma Microsoft celem obsługujący wszystkie funkcje dwie urządzenia na poziomie dwie modułu przez moduł dwie ogólnej dostępności. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

W tym artykule opisano:

* Struktura dwie modułu: *tagi*, *żądany* i *zgłosił właściwości*.
* Operacje, które moduły i zaplecza, które mogą wykonywać na twins modułu.

Zapoznaj się [wskazówki komunikację urządzenia do chmury] [ lnk-d2c-guidance] wskazówki dotyczące przy użyciu właściwości zgłoszone, wiadomości urządzenia do chmury lub przekazywania pliku.
Zapoznaj się [wskazówki dotyczące komunikacji chmury do urządzenia] [ lnk-c2d-guidance] wskazówki na temat używania żądanej właściwości, metody bezpośredniego lub komunikaty chmury do urządzenia.

## <a name="module-twins"></a>Moduł twins
Moduł twins przechowywać informacje związane z modułu który:

* Modułów na urządzeniu i Centrum IoT umożliwia synchronizowanie modułu warunków i konfiguracji.
* Zaplecze rozwiązania służy do zapytań i docelowego długotrwałą operacji.

Cykl życia dwie modułu jest połączony z odpowiadającego [tożsamości modułu][lnk-identity]. Moduły twins niejawnie są tworzone i usuwane po utworzeniu lub usunięciu w Centrum IoT tożsamości modułu.

Dwie modułu jest dokumentem JSON, który zawiera:

* **Tagi**. Sekcja zaplecza rozwiązania można odczytywać i zapisywać do dokumentu JSON. Tagi nie są widoczne dla modułów na urządzeniu. Tagi są ustawione na potrzeby zapytań o celu.
* **Żądany właściwości**. Używać razem z właściwości zgłoszony do synchronizowania warunki lub konfiguracji modułu. Zaplecza rozwiązania można ustawić odpowiednie właściwości, oraz moduł aplikacji można je odczytać. Moduł aplikacji może również odbierać powiadomienia o zmianach w odpowiednich właściwościach.
* **Zgłoszone właściwości**. Umożliwia oraz odpowiednie właściwości synchronizacji konfiguracji modułu lub warunków. Moduł aplikacji można ustawić właściwości zgłoszone, a zaplecze rozwiązania może odczytywać i wyszukiwać w nich.
* **Właściwości tożsamości modułu**. Głównego dokumentu JSON dwie modułu zawiera właściwości tylko do odczytu z odpowiedniego tożsamości modułu przechowywane w [rejestru tożsamości][lnk-identity].

![][img-module-twin]

W poniższym przykładzie przedstawiono dwie modułu dokumentu JSON:

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
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

W obiekcie głównym są moduł właściwości tożsamości i kontener obiektów na `tags` i oba `reported` i `desired` właściwości. `properties` Kontener zawiera niektóre elementy tylko do odczytu (`$metadata`, `$etag`, i `$version`) opisano w [metadanych dwie] [ lnk-module-twin-metadata] i [ Optymistycznej współbieżności] [ lnk-concurrency] sekcje.

### <a name="reported-property-example"></a>Przykład zgłoszony właściwości
W poprzednim przykładzie zawiera dwie modułu `batteryLevel` właściwości zgłaszane przez moduł aplikacji. Ta właściwość umożliwia zapytania i działają na modułów na podstawie ostatniego poziomu zgłoszone baterii. Przykładami innych możliwości modułu raportowania modułu aplikacji lub opcji łączności.

> [!NOTE]
> Właściwości zgłoszone uprościć scenariuszy, w którym zaplecza rozwiązania jest zainteresowana ostatniej znanej wartości właściwości. Użyj [wiadomości urządzenia do chmury] [ lnk-d2c] zaplecza rozwiązania musi przetworzyć moduł telemetrii w postaci sekwencji zdarzeń oznaczony znacznikiem czasowym, takich jak szeregów czasowych.

### <a name="desired-property-example"></a>Przykład żądanej właściwości
W poprzednim przykładzie `telemetryConfig` potrzebne dwie modułu i zgłoszone właściwości są używane przez zaplecze rozwiązania i aplikacji modułu zsynchronizować konfiguracji dane telemetryczne dla tego modułu. Na przykład:

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

2. Powiadomienie do aplikacji modułu zmian natychmiast, jeśli połączone lub przy pierwszym próba ponownego połączenia. Aplikacja modułu następnie raporty zaktualizowanej konfiguracji (lub warunek błędu przy użyciu `status` właściwości). Poniżej przedstawiono część zgłoszonych właściwości:

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

3. Zaplecze rozwiązania można śledzić wyniki operacji konfiguracji we wszystkich modułach wiele przez [badania] [ lnk-query] twins modułu.

> [!NOTE]
> Poprzedni fragmenty kodu są przykłady, zoptymalizowana pod kątem czytelności, z jednym ze sposobów kodowania konfigurację modułu i jego stan. Centrum IoT nie nakłada określonego schematu dwie modułu potrzeby i podać właściwości w twins modułu.
> 
> 

## <a name="back-end-operations"></a>Operacje zaplecza
Zaplecze rozwiązania działa na dwie modułu przy użyciu następujących operacji niepodzielnych za pośrednictwem protokołu HTTPS:

* **Pobieranie modułu dwie według Identyfikatora**. Ta operacja zwraca dokument dwie modułu, tym tagów i właściwości żądaną i podać systemu.
* **Częściowego zaktualizowania dwie modułu**. Ta operacja umożliwia zaplecza rozwiązania celu częściowego zaktualizowania znaczników i odpowiednie właściwości w dwie modułu. Częściowej aktualizacji jest wyrażona jako dokument JSON, który dodaje lub aktualizuje dowolną właściwość. Wartość właściwości `null` zostaną usunięte. Poniższy przykład tworzy nową właściwość odpowiednią wartością `{"newProperty": "newValue"}`, spowoduje zastąpienie istniejącej wartości `existingProperty` z `"otherNewValue"`i usuwa `otherOldProperty`. Nie zmian do istniejących żądanej właściwości bądź tagi:

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
    moduleId | Identyfikator modułu |
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

* Zapytanie twins modułu przy użyciu przypominającego SQL [język zapytań Centrum IoT][lnk-query].

## <a name="module-operations"></a>Operacje modułu
Moduł aplikacji działa na dwie modułu przy użyciu operacji niepodzielnych w następujących:

* **Pobieranie modułu dwie**. Ta operacja zwraca dokument dwie moduł (w tym znaczniki i właściwości żądaną i podać system) dla aktualnie podłączonego modułu.
* **Częściowego zaktualizowania właściwości zgłoszone**. Ta operacja umożliwia częściowej aktualizacji właściwości zgłoszone aktualnie połączonych modułu. Ta operacja używa tego samego formatu aktualizacji JSON, że rozwiązanie kopię celu zastosowania częściowej aktualizacji żądanej właściwości.
* **Sprawdź odpowiednie właściwości**. Aktualnie połączonych modułu można otrzymywać powiadomienia o aktualizacjach do żądanej właściwości, gdy wystąpią. Moduł odbiera tego samego formularza aktualizacji (pełnych lub wymiana) wykonywane przez zaplecza rozwiązania.

Wszystkie poprzednie operacje wymagają **ModuleConnect** uprawnienia, zgodnie z definicją w [zabezpieczeń] [ lnk-security] artykułu.

[Urządzenia Azure IoT SDK] [ lnk-sdks] ułatwiają używać poprzedniej operacji z wielu języków i platform.

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

## <a name="module-twin-size"></a>Rozmiar dwie modułu
Centrum IoT wymusza ograniczenie rozmiaru 8KB na wszystkich odpowiednich wartości całkowitej `tags`, `properties/desired`, i `properties/reported`, z wyjątkiem elementów tylko do odczytu.
Rozmiar jest obliczany poprzez zliczanie wszystkie znaki oprócz znaków sterujących UNICODE (segmenty C0 i C1) i spacje, które znajdują się poza stałe typu string.
Centrum IoT z powodu błędu odrzuca wszystkie operacje, które spowoduje zwiększenie rozmiaru tych dokumentów powyżej limitu.

## <a name="module-twin-metadata"></a>Moduł dwie metadanych
Centrum IoT przechowuje sygnatura czasowa ostatniej aktualizacji dla każdego obiektu JSON w module dwie potrzeby i podać właściwości. Sygnatury czasowe są w UTC i kodowany w [ISO8601] format `YYYY-MM-DDTHH:MM:SS.mmmZ`.
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

Dwie modułu potrzeby i podać właściwości nie ma elementy etag, ale `$version` wartość, która może być przyrostowe. Podobnie do tag ETag wersji mogą być używane przez stronę aktualizacji do wymuszania zgodności aktualizacji. Na przykład Moduł aplikacji dla zgłoszonego właściwości lub zaplecza rozwiązania dla żądanej właściwości.

Wersje są także przydatne, gdy observing agenta (np. aplikacji modułu obserwowania odpowiednie właściwości) należy uzgodnić szczepy między wynik operacji pobierania i powiadomienie o aktualizacji. Sekcja [przepływu ponowne łączenie urządzenia] [ponownego łączenia lnk] zawiera więcej informacji. 

## <a name="next-steps"></a>Kolejne kroki
Aby wypróbować pojęcia opisane w tym artykule, zobacz następujące samouczki Centrum IoT:

* [Rozpoczynanie pracy z Centrum IoT modułu tożsamości i moduł dwie przy użyciu kopii zapasowej .NET i .NET urządzenia][lnk-module-twin-tutorial]

<!-- links and images -->

[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232

[lnk-module-twin-tutorial]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-module-twin-metadata]: iot-hub-devguide-module-twins.md#module-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[img-module-twin]: media/iot-hub-devguide-device-twins/module-twin.jpg
