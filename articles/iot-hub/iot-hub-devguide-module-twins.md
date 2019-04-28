---
title: Zrozumienie bliźniaczych reprezentacjach modułów usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — bliźniaczych reprezentacjach modułów Użyj do synchronizacji danych stanu i konfiguracji między centrum IoT Hub i urządzeniach
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: cd0a9a66f3014a39a73cf04badfc67cd2ff4c3de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363650"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Zrozumieniu i użytkowaniu bliźniaczych reprezentacjach modułów usługi IoT Hub

W tym artykule założono, użytkownik przeczytał [poznawanie i używanie bliźniaczych reprezentacji urządzeń w usłudze IoT Hub](iot-hub-devguide-device-twins.md) pierwszy. W usłudze IoT Hub w ramach każdej tożsamości urządzenia można utworzyć maksymalnie 20 tożsamości modułu. Każda tożsamość moduł generuje niejawnie bliźniaczą reprezentację modułu. Podobnie jak bliźniacze reprezentacje urządzeń, dokumenty JSON, które przechowują informacje o stanie modułu w tym metadane, konfiguracje i warunki są bliźniaczych reprezentacjach modułów. Usługa Azure IoT Hub utrzymuje bliźniaczą reprezentację modułu, dla każdego modułu, w którym jest nawiązywane połączenie usługi IoT Hub. 

Zestawy SDK urządzeń IoT Hub po stronie urządzenia umożliwiają tworzenie modułów, gdzie każdy z nich zostanie otwarty niezależnie od połączenia usługi IoT Hub. Ta funkcja umożliwia użycie oddzielnych przestrzeni nazw dla różnych składników na urządzeniu. Na przykład masz Automat, która ma trzy różne czujniki. Każdy czujnik jest kontrolowana przez różne działy w Twojej firmie. Można utworzyć modułu dla każdego czujnika. W ten sposób każdy dział jest tylko możliwość wysyłania zadań lub metod bezpośrednich do czujnika, które mogą kontrolować, unikanie konfliktów i błędy użytkowników.

 Moduł tożsamości i bliźniaczą reprezentację modułu zapewniają takie same możliwości jak tożsamość urządzenia i bliźniaczej reprezentacji urządzenia, ale w bardziej szczegółowy. To bardziej szczegółowy umożliwia zdolne do urządzeń, takich jak urządzenia z systemem operacyjnym lub oprogramowania układowego urządzenia zarządzania wiele składników, aby odizolować konfiguracji i warunki dla każdego z tych składników. Moduł tożsamości i bliźniaczych reprezentacjach modułów zapewniają separacji zarządzania, podczas pracy z urządzeń IoT, które mają składniki modułowe oprogramowanie. Firma Microsoft mają na celu wspieranie wszystkie funkcje bliźniaczej reprezentacji urządzenia na poziomie bliźniaczej reprezentacji modułu wg ogólnej dostępności bliźniaczej reprezentacji modułu. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

W tym artykule opisano:

* Struktura bliźniaczą reprezentację modułu: *tagi*, *żądaną* i *zgłaszanych właściwości*.
* Operacje, które moduły i zapleczy, które mogą wykonywać na bliźniaczych reprezentacjach modułów.

Zapoznaj się [wskazówki dotyczące komunikacji urządzenia do chmury](iot-hub-devguide-d2c-guidance.md) wskazówki dotyczące za pomocą zgłoszonych właściwości, komunikaty z urządzenia do chmury lub przekazywanie pliku.

Zapoznaj się [wskazówki dotyczące komunikacji chmury do urządzenia](iot-hub-devguide-c2d-guidance.md) wskazówki na temat korzystania z żądanych właściwości, metod bezpośrednich lub komunikatów z chmury do urządzeń.

## <a name="module-twins"></a>Bliźniaczych reprezentacjach modułów

Bliźniaczych reprezentacjach modułów przechowywać informacje dotyczące modułu który:

* Moduły na urządzeniu i Centrum IoT Hub można służy do synchronizowania modułu warunków i konfiguracji.

* Zaplecze rozwiązania służy do zapytań i docelowy długotrwałych operacji.

Cykl życia bliźniaczą reprezentację modułu jest połączony z odpowiednich [tożsamości modułu](iot-hub-devguide-identity-registry.md). Moduły twins niejawnie są tworzone i usunięte po utworzeniu lub usunięciu w usłudze IoT Hub z modułu tożsamości.

Bliźniacza reprezentacja modułu jest dokumentem JSON, który zawiera:

* **Tagi**. Sekcja dokumentu JSON, który zaplecze rozwiązania może odczytywać i zapisywać. Tagi nie są widoczne dla modułów na urządzeniu. Tagi są ustawiane podczas wykonywania zapytań w celu.

* **Żądane właściwości**. Używać razem z zgłaszanych właściwości, aby zsynchronizować konfiguracji modułu lub warunki. Zaplecze rozwiązania może ustawić żądanych właściwości, a aplikacja moduł może je odczytać. Aplikacja moduł może również odbierać powiadomienia o zmianach w odpowiednich właściwości.

* **Zgłaszane właściwości**. Używać razem z żądanych właściwości, aby zsynchronizować konfiguracji modułu lub warunki. Aplikacja moduł można ustawić zgłaszanych właściwości, a zaplecze rozwiązania może odczytywać i wyszukiwać w nich.

* **Właściwości tożsamości modułu**. Katalog główny dokumentów JSON bliźniaczej reprezentacji modułu zawiera właściwości tylko do odczytu z odpowiedniej tożsamości modułu, przechowywane w [rejestr tożsamości](iot-hub-devguide-identity-registry.md).

![Architektury reprezentacji bliźniaczej reprezentacji urządzenia](./media/iot-hub-devguide-device-twins/module-twin.jpg)

Bliźniacza reprezentacja modułu dokument JSON można znaleźć w poniższym przykładzie:

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
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

W głównym obiekcie są moduł właściwości tożsamości i obiekty kontenera dla `tags` i wartościami `reported` i `desired` właściwości. `properties` Kontener zawiera niektóre elementy tylko do odczytu (`$metadata`, `$etag`, i `$version`) opisanego w [metadane bliźniaczej reprezentacji modułu](iot-hub-devguide-module-twins.md#module-twin-metadata) i [optymistycznej współbieżności](iot-hub-devguide-device-twins.md#optimistic-concurrency) sekcje.

### <a name="reported-property-example"></a>Przykład zgłaszanej właściwości

W poprzednim przykładzie zawiera bliźniaczą reprezentację modułu `batteryLevel` właściwości, który jest zgłaszany przez aplikację moduł. Ta właściwość umożliwia zapytania i działają na moduły na podstawie ostatniego poziomu zgłoszone baterii. Przykłady innych opcji łączności lub moduł funkcji modułu raportowania aplikacji.

> [!NOTE]
> Zgłaszane właściwości uproszczenia scenariuszy, w których zaplecze rozwiązania jest zainteresowany ostatniej znanej wartości właściwości. Użyj [komunikatów z urządzenia do chmury](iot-hub-devguide-messages-d2c.md) zaplecze rozwiązania musi przetwarzać telemetrię modułu w postaci sekwencje zdarzenia oznaczony sygnaturą czasową, takie jak szeregów czasowych.

### <a name="desired-property-example"></a>Przykład żądanej właściwości

W poprzednim przykładzie `telemetryConfig` żądanego bliźniaczą reprezentację modułu oraz zgłaszane właściwości są używane przez zaplecze rozwiązania i aplikacja moduł zsynchronizować konfiguracji dane telemetryczne dla tego modułu. Na przykład:

1. Zaplecze rozwiązania ustawia żądaną właściwość z wartością żądaną konfiguracją. Poniżej przedstawiono część dokumentu z odpowiednią właściwością:

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

2. Powiadomienie do aplikacji modułu zmian natychmiast, jeśli połączone lub na pierwszym próba ponownego połączenia. Aplikacja moduł następnie raportów zaktualizowanej konfiguracji (lub warunek błędu przy użyciu `status` właściwości). Poniżej przedstawiono część zgłaszanych właściwości:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. Zaplecze rozwiązania może śledzić wyniki operacja konfiguracji między wiele modułów przez [zapytań](iot-hub-devguide-query-language.md) bliźniaczych reprezentacjach modułów.

> [!NOTE]
> Poprzedzających fragmentów są przykładami, zoptymalizowane pod kątem czytelności jednym ze sposobów kodowanie konfiguracji modułu i jego stan. Bliźniacza reprezentacja modułu żądanego i zgłoszonych właściwości w bliźniaczych reprezentacjach modułów usługi IoT Hub nie nakłada określonego schematu.
> 
> 

## <a name="back-end-operations"></a>Operacje zaplecza
Zaplecze rozwiązania operuje na bliźniaczą reprezentację modułu przy użyciu następujących niepodzielne operacje udostępniane za pośrednictwem protokołu HTTPS:

* **Pobieranie bliźniaczą reprezentację modułu za pomocą Identyfikatora**. Ta operacja zwraca dokumentu bliźniaczej reprezentacji modułu, tym tagów i właściwości żądaną i podać systemu.

* **Częściowo aktualizował bliźniaczą reprezentację modułu**. Ta operacja umożliwia zapleczu rozwiązania celu częściowego zaktualizowania tagi i żądane właściwości w bliźniaczej reprezentacji modułu. Częściową aktualizację jest wyrażona jako dokument JSON, który dodaje lub aktualizuje dowolną właściwość. Właściwości ustawione na `null` są usuwane. Poniższy przykład tworzy nowy żądaną właściwość z wartością `{"newProperty": "newValue"}`, zastępuje istniejącą wartość z `existingProperty` z `"otherNewValue"`i usuwa `otherOldProperty`. Żadne inne zmiany zostaną wprowadzone do istniejącego żądane właściwości lub tagi:

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

* **Zastąp żądane właściwości**. Ta operacja umożliwia zaplecza rozwiązania całkowicie zastąpienie wszystkich istniejących żądane właściwości i Wstaw nowy dokument JSON dla `properties/desired`.

* **Zastąp tagi**. Ta operacja umożliwia zaplecza rozwiązania całkowicie zastąpienie wszystkich istniejących tagów i Wstaw nowy dokument JSON dla `tags`.

* **Otrzymuj powiadomienia bliźniaczej reprezentacji**. Ta operacja umożliwia zapleczu rozwiązania otrzymywać powiadomienia po zmodyfikowaniu bliźniaczej reprezentacji. Aby to zrobić, rozwiązanie IoT musi utworzyć trasę i ustaw źródło danych jest równa *twinChangeEvents*. Domyślnie są wysyłane żadne powiadomienia bliźniaczej reprezentacji, oznacza to, że istnieje wstępnie nie takich tras. Jeśli szybkość zmian jest zbyt duża lub z innych powodów, takich jak wewnętrzne błędy, usługa IoT Hub może wysłać tylko jedno powiadomienie, który zawiera wszystkie zmiany. W związku z tym jeśli aplikacja wymaga inspekcji i rejestrowania dla wszystkich pośrednich stanów niezawodne, należy użyć komunikatów z urządzenia do chmury. Bliźniacza reprezentacja komunikat powiadomienia zawiera właściwości i treść.

  - Właściwości

    | Name (Nazwa) | Wartość |
    | --- | --- |
    $content — typ | application/json |
    $iothub-enqueuedtime |  Czas wysłania powiadomienia |
    $iothub-message-source | twinChangeEvents |
    $content — kodowanie | UTF-8 |
    deviceId | Identyfikator urządzenia |
    moduleId | Identyfikator modułu |
    hubName | Nazwa centrum IoT Hub |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) sygnatura czasowa operacji |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" lub "updateTwin" |

    Właściwości systemu komunikat mają prefiks `$` symboli.

  - Treść
        
    Ta sekcja zawiera wszystkie zmiany bliźniaczą reprezentację w formacie JSON. Używa tego samego formatu poprawek, z tą różnicą, że może zawierać wszystkie sekcje bliźniaczej reprezentacji: tagi, properties.reported, properties.desired i czy zawiera on elementy "$metadata". Na przykład:

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

Obsługa wszystkich poprzednich operacji [optymistycznej współbieżności](iot-hub-devguide-device-twins.md#optimistic-concurrency) i wymagają **ServiceConnect** uprawnienia, zgodnie z definicją w [kontroli dostępu do usługi IoT Hub](iot-hub-devguide-security.md) artykułu.

Oprócz tych operacji zaplecze rozwiązania może zapytań bliźniaczych reprezentacjach modułów przy użyciu przypominającego SQL [język zapytań usługi IoT Hub](iot-hub-devguide-query-language.md).

## <a name="module-operations"></a>Operacje modułu

Aplikacja moduł operuje na bliźniaczą reprezentację modułu przy użyciu następujących niepodzielne operacje:

* **Pobieranie bliźniaczą reprezentację modułu**. Ta operacja zwraca dokument bliźniaczej reprezentacji modułu (w tym tagów i właściwości żądaną i podać system) dla aktualnie połączonych modułu.

* **Częściowo aktualizowania zgłoszonych właściwości**. Ta operacja umożliwia częściową aktualizację zgłoszone właściwości aktualizuje aktualnie połączonych modułu. Ta operacja używa tego samego formatu aktualizacji JSON, że rozwiązanie kopię końcowych zastosowań częściową aktualizację żądane właściwości.

* **Obserwuj żądane właściwości**. Moduł aktualnie połączonych można otrzymywać powiadomienia o aktualizacjach, żądanych właściwości, gdy wystąpią. Moduł odbiera tego samego formularza update (wymiana częściowego lub pełnego) wykonywana przez zaplecze rozwiązania.

Wymagaj wszystkich poprzednich operacji **ModuleConnect** uprawnienia, zgodnie z definicją w [kontroli dostępu do usługi IoT Hub](iot-hub-devguide-security.md) artykułu.

[Zestawy SDK urządzeń Azure IoT](iot-hub-devguide-sdks.md) ułatwiają używać poprzedniej operacji z wielu języków i platform.

## <a name="tags-and-properties-format"></a>Format właściwości i tagów

Tagi, żądanych właściwości i zgłaszane właściwości są obiektami JSON z następującymi zastrzeżeniami:

* Wszystkie klucze w obiekty JSON jest rozróżniana wielkość liter 64 bajtów ciągów UNICODE UTF-8. Dozwolone znaki wykluczenia znaków kontrolnych UNICODE (segmenty C0 i C1) i `.`, SP, i `$`.

* Wszystkie wartości w obiekty JSON może być następujących typów JSON: atrybut typu wartość logiczna, liczba, ciąg, obiekt. Tablice są niedozwolone. Maksymalna wartość dla liczb całkowitych jest 4503599627370495, a wartość minimalna dla liczb całkowitych jest-4503599627370496.

* Wszystkie obiekty JSON w tagi i żądane i zgłaszanych właściwości może mieć maksymalną głębokość 5. Na przykład następujący obiekt jest prawidłowy:

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

* Wszystkie ciągi mogą mieć maksymalnie 512 bajtów długości.

## <a name="module-twin-size"></a>Rozmiar bliźniaczej reprezentacji modułu

Usługa IoT Hub wymusza ograniczenie rozmiaru 8KB na wszystkich odpowiednich wartości całkowitej `tags`, `properties/desired`, i `properties/reported`, z wyłączeniem elementów tylko do odczytu.

Rozmiar jest obliczany od policzenia wszystkich znaków, z wyjątkiem znaków kontrolnych UNICODE (segmenty C0 i C1) i miejsca do magazynowania, które znajdują się poza stałe typu string.

Usługa IoT Hub z powodu błędu odrzuca wszystkie operacje, które spowoduje zwiększenie rozmiaru dokumentów, które przekracza limit.

## <a name="module-twin-metadata"></a>Metadane bliźniaczej reprezentacji modułu

Usługa IoT Hub utrzymuje sygnaturę czasową ostatniej aktualizacji dla każdego obiektu JSON w bliźniaczej reprezentacji modułu żądanego i zgłaszanych właściwości. Sygnatury czasowe są w UTC i zakodowane w [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) format `YYYY-MM-DDTHH:MM:SS.mmmZ`.
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
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
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

Te informacje są przechowywane na każdym poziomie (nie tylko liście strukturze JSON), aby zachować aktualizacje, które usunąć klucze obiektów.

## <a name="optimistic-concurrency"></a>Optymistyczna współbieżność

Tagi, żądane i zgłaszanych właściwości wszystkich Obsługa optymistycznej współbieżności.
Tagi mają element ETag zgodnie [RFC7232](https://tools.ietf.org/html/rfc7232), reprezentujący reprezentacji JSON znacznika. W celu zapewnienia spójności, można użyć elementów etag w operacjach aktualizowania warunkowe z zapleczem rozwiązania.

Bliźniacza reprezentacja modułu żądanego i zgłaszanych właściwości nie ma elementów etag, ale `$version` wartość, która może być przyrostowe. Podobnie na element ETag wersji może służyć przez stronę aktualizacji do wymuszenia spójności aktualizacji. Na przykład moduł aplikacją zgłaszanych właściwości lub zaplecze rozwiązania dla żądanej właściwości.

Wersje są również przydatne, gdy observing agenta (np. aplikacji modułu obserwowania żądane właściwości), należy uzgodnić sam między wynik operacji pobierania i powiadomienie o aktualizacji. Sekcja [przepływ ponownego łączenia urządzeń](iot-hub-devguide-device-twins.md#device-reconnection-flow) zawiera więcej informacji. 

## <a name="next-steps"></a>Kolejne kroki

Aby wypróbować pojęcia opisane w tym artykule, zobacz następujące samouczki usługi IoT Hub:

* [Rozpoczynanie pracy z usługą IoT Hub tożsamości i moduł bliźniaczą reprezentację modułu przy użyciu zaplecza platformy .NET i .NET urządzenia](iot-hub-csharp-csharp-module-twin-getstarted.md)
