---
title: Opis bliźniaczy modułów Usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — używanie bliźniąt modułów do synchronizowania danych o stanie i konfiguracji między usługą IoT Hub a urządzeniami
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: menchi
ms.openlocfilehash: 5ef6c4de288a764abbe434c5d84fc99e154f7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303600"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Opis i używanie bliźniąt modułów w Uirozie IoT Hub

W tym artykule przyjęto założenie, że najpierw przeczytałeś [artykuł Zrozum i użyj bliźniaczych urządzeń w Uorie IoT Hub.](iot-hub-devguide-device-twins.md) W Centrum IoT, w ramach każdej tożsamości urządzenia, można utworzyć maksymalnie 20 tożsamości modułu. Każda tożsamość modułu niejawnie generuje bliźniaczej reprezentacji modułu. Podobnie jak bliźniacze dane bliźniaczych reprezentacji urządzeń, bliźniacze moduły są dokumentami JSON, które przechowują informacje o stanie modułu, w tym metadane, konfiguracje i warunki. Usługa Azure IoT Hub przechowuje bliźniaczej reprezentacji modułu dla każdego modułu, który łączysz się z centrum IoT Hub. 

Po stronie urządzenia moduły SDK urządzenia usługi IoT Hub umożliwiają tworzenie modułów, w których każdy z nich otwiera niezależne połączenie z centrum IoT Hub. Ta funkcja umożliwia używanie oddzielnych obszarów nazw dla różnych składników na urządzeniu. Na przykład masz automat, który ma trzy różne czujniki. Każdy czujnik jest kontrolowany przez różne działy w Twojej firmie. Można utworzyć moduł dla każdego czujnika. W ten sposób każdy dział jest w stanie wysyłać zadania lub bezpośrednie metody tylko do kontrolowanego przez siebie czujnika, unikając konfliktów i błędów użytkownika.

 Tożsamość modułu i bliźniaczej reprezentacji modułu zapewniają takie same możliwości jak tożsamość urządzenia i bliźniaczej reprezentacji urządzenia, ale z większą szczegółowością. Ta drobna szczegółowość umożliwia urządzeniom, takim jak urządzenia oparte na systemie operacyjnym lub urządzenia układowe zarządzające wieloma składnikami, izolowanie konfiguracji i warunków dla każdego z tych składników. Bliźniacze dane bliźniacze tożsamości modułu i modułu zapewniają oddzielenie zarządzania problemów podczas pracy z urządzeniami IoT, które mają modułowe składniki oprogramowania. Naszym celem jest wspieranie wszystkich funkcji bliźniaczej reprezentacji urządzenia na poziomie bliźniaczej modułu przez podwójną ogólną dostępność modułu. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

W tym artykule opisano:

* Struktura modułu bliźniaczego: *tagi*, *pożądane* i *zgłoszone właściwości*.
* Operacje, które moduły i zaplecze można wykonać na bliźniaczych modułów.

Aby uzyskać wskazówki dotyczące korzystania ze zgłoszonych właściwości, komunikatów z urządzenia do chmury lub przekazywania plików, zapoznaj się [ze wskazówkami dotyczącymi komunikacji](iot-hub-devguide-d2c-guidance.md) między urządzeniami a chmurą.

Aby uzyskać wskazówki dotyczące korzystania z żądanych właściwości, metod bezpośrednich lub komunikatów z chmury do urządzenia, zapoznaj się [ze wskazówkami dotyczącymi komunikacji](iot-hub-devguide-c2d-guidance.md) z chmury do urządzenia.

## <a name="module-twins"></a>Bliźnięta modułu

Bliźniacze moduły przechowują informacje związane z modułem, które:

* Moduły na urządzeniu i IoT Hub można użyć do synchronizacji warunków modułu i konfiguracji.

* Zaplecza rozwiązania można użyć do kwerendy i docelowe długotrwałe operacje.

Cykl życia bliźniaczej reprezentacji modułu jest połączony z odpowiednią [tożsamością modułu](iot-hub-devguide-identity-registry.md). Moduły bliźniacze są niejawnie tworzone i usuwane, gdy tożsamość modułu jest tworzony lub usuwany w Centrum IoT Hub.

Bliźniacza reprezentacja modułu jest dokumentem JSON, który zawiera:

* **Tagi**. Sekcja dokumentu JSON, który rozwiązanie zaplecza można odczytać i zapisać do. Znaczniki nie są widoczne dla modułów na urządzeniu. Tagi są ustawiane do celów wykonywania zapytań.

* **Żądane właściwości**. Używane wraz ze zgłoszonymi właściwościami do synchronizowania konfiguracji lub warunków modułu. Zaplecze rozwiązania można ustawić żądane właściwości, a aplikacja modułu można je odczytać. Aplikacja modułu może również otrzymywać powiadomienia o zmianach w żądanych właściwościach.

* **Zgłoszone właściwości**. Używane wraz z żądanymi właściwościami do synchronizowania konfiguracji lub warunków modułu. Aplikacja modułu można ustawić zgłaszane właściwości, a zaplecze rozwiązania można je odczytać i zbadać je.

* **Właściwości tożsamości modułu**. Katalog główny dokumentu JSON bliźniaczej modułu zawiera właściwości tylko do odczytu z odpowiedniej tożsamości modułu przechowywane w [rejestrze tożsamości](iot-hub-devguide-identity-registry.md).

![Reprezentacja architektoniczna bliźniaczej reprezentacji urządzenia](./media/iot-hub-devguide-device-twins/module-twin.jpg)

W poniższym przykładzie pokazano bliźniaczej reprezentacji modułu JSON dokumentu:

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

W obiekcie głównym są właściwości tożsamości modułu `tags` i `reported` `desired` obiekty kontenera dla i obu i właściwości. `properties` Kontener zawiera niektóre elementy tylko`$metadata` `$etag`do `$version`odczytu ( , i ) opisane w [module bliźniaczych metadanych](iot-hub-devguide-module-twins.md#module-twin-metadata) i [optymistyczne współbieżności](iot-hub-devguide-device-twins.md#optimistic-concurrency) sekcje.

### <a name="reported-property-example"></a>Przykład zgłoszonej właściwości

W poprzednim przykładzie bliźniaczej `batteryLevel` reprezentacji modułu zawiera właściwość, która jest zgłaszana przez aplikację modułu. Ta właściwość umożliwia wykonywanie zapytań i działanie na modułach na podstawie ostatnio zgłoszonego poziomu naładowania baterii. Inne przykłady obejmują możliwości modułu raportowania aplikacji modułu lub opcje łączności.

> [!NOTE]
> Zgłoszone właściwości upraszczają scenariusze, w których zaplecze rozwiązania jest zainteresowany ostatnią znaną wartością właściwości. Użyj [komunikatów z urządzenia do chmury,](iot-hub-devguide-messages-d2c.md) jeśli zaplecze rozwiązania musi przetwarzać dane telemetryczne modułu w postaci sekwencji zdarzeń sygnatury czasowej, takich jak szeregi czasowe.

### <a name="desired-property-example"></a>Przykład żądanej właściwości

W poprzednim przykładzie `telemetryConfig` bliźniaczej reprezentacji modułu żądane i zgłaszane właściwości są używane przez zaplecza rozwiązania i aplikacji modułu do synchronizacji konfiguracji telemetrii dla tego modułu. Przykład:

1. Zaplecze rozwiązania ustawia żądaną właściwość z żądaną wartością konfiguracji. Oto część dokumentu z żądanym zestawem właściwości:

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

2. Aplikacja modułu jest powiadamiany o zmianie natychmiast, jeśli podłączony lub przy pierwszym ponownym połączeniu. Następnie aplikacja modułu zgłasza zaktualizowaną konfigurację `status` (lub warunek błędu przy użyciu właściwości). Oto część zgłoszonych właściwości:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. Zaplecza rozwiązania można śledzić wyniki operacji konfiguracji w wielu modułach, przez [wykonywanie zapytań](iot-hub-devguide-query-language.md) bliźniaczych modułu.

> [!NOTE]
> Powyższe fragmenty kodu są przykłady, zoptymalizowane pod kątem czytelności, jeden sposób kodowania konfiguracji modułu i jego stanu. Centrum IoT hub nie nakłada określonego schematu dla modułu twin pożądane i zgłaszane właściwości w bliźniaczych reprezentacji modułu.
> 
> 

## <a name="back-end-operations"></a>Operacje zaplecza
Zaplecze rozwiązania działa na bliźniaczej reprezentacji modułu przy użyciu następujących operacji niepodzielnych, udostępniane za pośrednictwem protokołu HTTPS:

* **Pobierz bliźniaczej reprezentacji modułu przez ID**. Ta operacja zwraca dokument bliźniaczej reprezentacji modułu, w tym znaczniki i żądane i zgłaszane właściwości systemu.

* **Częściowo aktualizacja modułu bliźniaczego**. Ta operacja umożliwia zapleczu rozwiązania do częściowej aktualizacji tagów lub żądanych właściwości w bliźniaczej reprezentacji modułu. Częściowa aktualizacja jest wyrażona jako dokument JSON, który dodaje lub aktualizuje dowolną właściwość. Właściwości ustawione `null` na są usuwane. Poniższy przykład tworzy nową żądaną właściwość z `{"newProperty": "newValue"}`wartością, zastępuje istniejącą wartość `existingProperty` z `"otherNewValue"`, i usuwa . `otherOldProperty` Żadne inne zmiany nie są wprowadzane do istniejących żądanych właściwości lub tagów:

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

* **Zamień żądane właściwości**. Ta operacja umożliwia rozwiązaniu zaplecze całkowicie zastąpić wszystkie istniejące żądane właściwości `properties/desired`i zastąpić nowy dokument JSON dla .

* **Zamień znaczniki**. Ta operacja umożliwia rozwiązaniu zaplecze całkowicie zastąpić wszystkie istniejące znaczniki i `tags`zastąpić nowy dokument JSON.

* **Otrzymuj podwójne powiadomienia**. Ta operacja umożliwia rozwiązanie zaplecza, aby otrzymywać powiadomienia, gdy bliźniaczej reprezentacji jest modyfikowany. Aby to zrobić, rozwiązanie IoT musi utworzyć trasę i ustawić źródło danych równe *twinChangeEvents*. Domyślnie nie są wysyłane żadne podwójne powiadomienia, oznacza to, że nie istnieją takie trasy. Jeśli szybkość zmiany jest zbyt wysoka lub z innych powodów, takich jak błędy wewnętrzne, Centrum IoT może wysłać tylko jedno powiadomienie, które zawiera wszystkie zmiany. W związku z tym jeśli aplikacja wymaga niezawodnej inspekcji i rejestrowania wszystkich stanów pośrednich, należy użyć komunikatów z urządzenia do chmury. Komunikat o powiadomieniu bliźniaczej zawiera właściwości i treść.

  - Właściwości

    | Nazwa | Wartość |
    | --- | --- |
    Typ $content | application/json |
    $iothub-enqueuedtime |  Czas wysłania powiadomienia |
    źródło $iothub-message | twinChangeZdowyzowie |
    Kodowanie $content | utf-8 |
    deviceId | Identyfikator urządzenia |
    identyfikator modułu | Identyfikator modułu |
    nazwa centrum | Nazwa Centrum IoT |
    operationStamp | Sygnatura czasowa [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) |
    schemat iothub-message | twinChangeNotification |
    Optype | "replaceTwin" lub "updateTwin" |

    Właściwości systemu wiadomości są poprzedzone `$` symbolem.

  - Treść
        
    Ta sekcja zawiera wszystkie zmiany bliźniaczej reprezentacji w formacie JSON. Używa tego samego formatu co łatka, z tą różnicą, że może zawierać wszystkie sekcje bliźniacze: tagi, properties.reported, properties.desired i że zawiera elementy "$metadata". Na przykład:

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

Wszystkie poprzednie operacje obsługują [optymistyczną współbieżność](iot-hub-devguide-device-twins.md#optimistic-concurrency) i wymagają uprawnienia **ServiceConnect,** zgodnie z definicją w artykule [Control Access to IoT Hub.](iot-hub-devguide-security.md)

Oprócz tych operacji zaplecza rozwiązania można zbadać bliźniacze modułu przy użyciu [języka zapytań IoT Hub](iot-hub-devguide-query-language.md)podobne do JĘZYKA SQL.

## <a name="module-operations"></a>Operacje modułów

Aplikacja moduł działa na bliźniaczej reprezentacji modułu przy użyciu następujących operacji atomowych:

* **Pobierz bliźniaczej reprezentacji modułu**. Ta operacja zwraca dokument bliźniaczej reprezentacji modułu (w tym znaczniki i żądane i zgłoszone właściwości systemu) dla aktualnie podłączonego modułu.

* **Częściowo zaktualizować zgłoszone właściwości**. Ta operacja umożliwia częściową aktualizację zgłoszonych właściwości aktualnie podłączonego modułu. Ta operacja używa tego samego formatu aktualizacji JSON, który rozwiązanie zaplecza używa do częściowej aktualizacji żądanych właściwości.

* **Obserwować żądane właściwości**. Aktualnie podłączony moduł można wybrać, aby otrzymywać powiadomienia o aktualizacjach żądanych właściwości, gdy się pojawią. Moduł otrzymuje taką samą formę aktualizacji (częściowej lub pełnej wymiany) wykonywane przez zaplecza rozwiązania.

Wszystkie poprzednie operacje wymagają uprawnienia **ModuleConnect,** zgodnie z definicją w artykule [Control Access to IoT Hub.](iot-hub-devguide-security.md)

ZestawY [SDK urządzeń IoT platformy Azure](iot-hub-devguide-sdks.md) ułatwiają korzystanie z poprzednich operacji z wielu języków i platform.

## <a name="tags-and-properties-format"></a>Format znaczników i właściwości

Znaczniki, żądane właściwości i zgłaszane właściwości są obiektami JSON z następującymi ograniczeniami:

* **Klawisze:** Wszystkie klucze w obiektach JSON są rozróżniane 64 bajtów CIĄGI UNICODE UTF-8. Dozwolone znaki wykluczają znaki kontrolne UNICODE (segmenty `.`C0 `$`i C1) oraz , SP i .

* **Wartości:** Wszystkie wartości w obiektach JSON mogą być następujące typy JSON: logiczne, liczba, ciąg, obiekt. Tablice nie są dozwolone.

    * Liczby całkowite mogą mieć minimalną wartość -4503599627370496 i maksymalną wartość 4503599627370495.

    * Wartości ciągów są zakodowane w utrwalone w utrwalone utf-8 i mogą mieć maksymalną długość 512 bajtów.

* **Głębokość:** Wszystkie obiekty JSON w znacznikach, żądane i zgłaszane właściwości mogą mieć maksymalną głębokość 5. Na przykład prawidłowy jest następujący obiekt:

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

## <a name="module-twin-size"></a>Podwójny rozmiar modułu

Centrum IoT Hub wymusza limit rozmiaru `tags`8 KB wartości i limit rozmiaru 32 KB na wartość `properties/desired` i `properties/reported`. Sumy te nie zawierają elementów `$etag` `$version`tylko `$metadata/$lastUpdated`do odczytu, takich jak , i .

Rozmiar bliźniaczej reprezentacji oblicza się w następujący sposób:

* Dla każdej właściwości w dokumencie JSON Usługa IoT Hub oblicza zbiorczo i dodaje długość klucza i wartości właściwości.

* Klucze właściwości są traktowane jako ciągi zakodowane w u. UTF8.

* Proste wartości właściwości są traktowane jako ciągi kodowane zgodnie z UTF8, wartości liczbowe (8 bajtów) lub wartości logiczne (4 bajty).

* Rozmiar ciągów zakodowanych w uiczonych w uiczonych w ułowieniu jest obliczany przez zliczanie wszystkich znaków, z wyjątkiem znaków kontrolnych UNICODE (segmenty C0 i C1).

* Złożone wartości właściwości (obiekty zagnieżdżone) są obliczane na podstawie agregacji rozmiaru kluczy właściwości i wartości właściwości, które zawierają.

Centrum IoT hub odrzuca z błędem wszystkie operacje, które mogłyby zwiększyć rozmiar tych dokumentów powyżej limitu.

## <a name="module-twin-metadata"></a>Podwójne metadane modułu

Usługa IoT Hub zachowuje sygnaturę czasową ostatniej aktualizacji dla każdego obiektu JSON w modułu bliźniaczej reprezentacji żądanej i zgłoszonej właściwości. Sygnatury czasowe są w formacie UTC i zakodowane w formacie `YYYY-MM-DDTHH:MM:SS.mmmZ` [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) .
Przykład:

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

Te informacje są przechowywane na każdym poziomie (nie tylko po liściach struktury JSON), aby zachować aktualizacje, które usuwają klucze obiektów.

## <a name="optimistic-concurrency"></a>Optymistyczna współbieżność

Tagi, żądane i zgłaszane właściwości wszystkie obsługują optymistyczne współbieżności.
Tagi mają ETag, zgodnie z [RFC7232](https://tools.ietf.org/html/rfc7232), który reprezentuje reprezentację JSON tagu. Można użyć tagów ETags w operacjach aktualizacji warunkowej z zaplecza rozwiązania, aby zapewnić spójność.

Moduł bliźniaczej reprezentacji żądane i zgłaszane właściwości `$version` nie mają ETags, ale mają wartość, która jest gwarantowana przyrostowe. Podobnie jak eTag, wersja może służyć przez stronę aktualizacji, aby wymusić spójność aktualizacji. Na przykład aplikacja modułu dla zgłoszonej właściwości lub zaplecza rozwiązania dla żądanej właściwości.

Wersje są również przydatne, gdy agent obserwujący (na przykład aplikacja modułu obserwująca żądane właściwości) musi uzgodnić wyścigi między wynikiem operacji pobierania a powiadomieniem o aktualizacji. Sekcja [Przepływ ponownego połączenia urządzenia](iot-hub-devguide-device-twins.md#device-reconnection-flow) zawiera więcej informacji. 

## <a name="next-steps"></a>Następne kroki

Aby wypróbować niektóre pojęcia opisane w tym artykule, zobacz następujące samouczki Usługi IoT Hub:

* [Wprowadzenie do tożsamości modułu IoT Hub i bliźniaczej reprezentacji modułu przy użyciu zaplecza .NET i urządzenia .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)
