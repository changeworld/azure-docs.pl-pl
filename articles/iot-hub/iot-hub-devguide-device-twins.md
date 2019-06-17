---
title: Opis usługi Azure IoT Hub bliźniaczych reprezentacji urządzeń | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — Użyj bliźniaczych reprezentacji urządzeń do synchronizacji danych stanu i konfiguracji między centrum IoT Hub i urządzeniach
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: fbc68b551db1f68fe253a833ad26c88de1b92f30
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055375"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Zrozumienie i używają bliźniaczych reprezentacji urządzeń w usłudze IoT Hub

*Bliźniacze reprezentacje urządzeń* to dokumenty JSON, które przechowują informacje o stanie urządzenia w tym metadane, konfiguracje i warunki. Usługa Azure IoT Hub utrzymuje bliźniaczą reprezentację urządzenia dla każdego urządzenia, na którym jest nawiązywane połączenie usługi IoT Hub. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

W tym artykule opisano:

* Struktura bliźniaczej reprezentacji urządzenia: *tagi*, *żądaną* i *zgłaszanych właściwości*.
* Operacje, które aplikacje urządzeń i zapleczy, które mogą wykonywać na bliźniaczych reprezentacji urządzeń.

Używają bliźniaczych reprezentacji urządzeń do:

* Store metadanych dla określonego urządzenia w chmurze. Na przykład lokalizacja wdrożenia Automat.

* Raport bieżące informacje o stanie, takie jak dostępne możliwości i warunków z aplikacji urządzenia. Na przykład urządzenie jest podłączone do usługi IoT hub w sieci komórkowej lub Wi-Fi.

* Synchronizuj stan długotrwałe przepływy pracy między aplikacją urządzenia i aplikacji zaplecza. Na przykład podczas kopii rozwiązania end określa nowej wersji oprogramowania układowego do zainstalowania i aplikacji urządzenia raporty na różnych etapach procesu aktualizacji.

* Zapytanie z metadanych urządzeń, konfiguracji lub stanu.

Zapoznaj się [wskazówki dotyczące komunikacji urządzenia do chmury](iot-hub-devguide-d2c-guidance.md) wskazówki dotyczące za pomocą zgłoszonych właściwości, komunikaty z urządzenia do chmury lub przekazywanie pliku.

Zapoznaj się [wskazówki dotyczące komunikacji chmury do urządzenia](iot-hub-devguide-c2d-guidance.md) wskazówki na temat korzystania z żądanych właściwości, metod bezpośrednich lub komunikatów z chmury do urządzeń.

## <a name="device-twins"></a>Bliźniacze reprezentacje urządzeń

Bliźniacze reprezentacje urządzeń przechowywania informacji związanych z urządzeniami który:

* Urządzenia i z powrotem kończy się umożliwia synchronizowanie warunków urządzenia i konfiguracji.

* Zaplecze rozwiązania służy do zapytań i docelowy długotrwałych operacji.

Cykl życia w bliźniaczej reprezentacji urządzenia jest połączony z odpowiednich [tożsamości urządzenia](iot-hub-devguide-identity-registry.md). Bliźniacze reprezentacje urządzeń są niejawnie tworzone i usunięte po utworzeniu lub usunięciu w usłudze IoT Hub tożsamości urządzenia.

Bliźniacza reprezentacja urządzenia to dokument JSON, który zawiera:

* **Tagi**. Sekcja dokumentu JSON, który zaplecze rozwiązania może odczytywać i zapisywać. Tagi nie są widoczne dla aplikacji dla urządzeń.

* **Żądane właściwości**. Używać razem z zgłaszanych właściwości, aby zsynchronizować konfiguracji urządzenia lub warunki. Zaplecze rozwiązania może ustawić żądanych właściwości i może je odczytać aplikacji urządzenia. W aplikacji urządzenia może również odbierać powiadomienia o zmianach w odpowiednich właściwości.

* **Zgłaszane właściwości**. Używać razem z żądanych właściwości, aby zsynchronizować konfiguracji urządzenia lub warunki. W aplikacji urządzenia można ustawić zgłaszanych właściwości, a zaplecze rozwiązania może odczytywać i wyszukiwać w nich.

* **Właściwości tożsamości urządzenia**. Katalog główny dokumentów JSON bliźniaczej reprezentacji urządzenia zawiera właściwości tylko do odczytu z odpowiedniej tożsamości urządzenia, przechowywane w [rejestr tożsamości](iot-hub-devguide-identity-registry.md).

![Zrzut ekranu z właściwości bliźniaczych reprezentacji urządzeń](./media/iot-hub-devguide-device-twins/twin.png)

W bliźniaczej reprezentacji urządzenia dokument JSON można znaleźć w poniższym przykładzie:

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
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

W głównym obiekcie są urządzenia właściwości tożsamości i obiekty kontenera dla `tags` i wartościami `reported` i `desired` właściwości. `properties` Kontener zawiera niektóre elementy tylko do odczytu (`$metadata`, `$etag`, i `$version`) opisanego w [metadanych bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md#device-twin-metadata) i [optymistycznej współbieżności](iot-hub-devguide-device-twins.md#optimistic-concurrency) sekcje.

### <a name="reported-property-example"></a>Przykład zgłaszanej właściwości

W poprzednim przykładzie, bliźniacza reprezentacja urządzenia zawiera `batteryLevel` właściwości, który jest zgłaszany przez aplikację urządzenia. Ta właściwość umożliwia zapytania i działają na urządzeniach, na podstawie ostatniego poziomu zgłoszone baterii. Przykłady innych opcji łączności lub możliwości raportowania urządzenia aplikacji urządzenia.

> [!NOTE]
> Zgłaszane właściwości uproszczenia scenariuszy, w których zaplecze rozwiązania jest zainteresowany ostatniej znanej wartości właściwości. Użyj [komunikatów z urządzenia do chmury](iot-hub-devguide-messages-d2c.md) zaplecze rozwiązania musi przetworzyć danych telemetrycznych z urządzenia w formie sekwencje zdarzenia oznaczony sygnaturą czasową, takie jak szeregów czasowych.

### <a name="desired-property-example"></a>Przykład żądanej właściwości

W poprzednim przykładzie `telemetryConfig` żądanego bliźniacza reprezentacja urządzenia i zgłaszane właściwości są używane przez zaplecze rozwiązania i aplikacji urządzenia do synchronizacji konfiguracji telemetrii dla tego urządzenia. Na przykład:

1. Zaplecze rozwiązania ustawia żądaną właściwość z wartością żądaną konfiguracją. Poniżej przedstawiono część dokumentu z odpowiednią właściwością:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. Powiadomienie do aplikacji urządzenia zmian natychmiast, jeśli połączone lub na pierwszym próba ponownego połączenia. W aplikacji urządzenia funkcję następnie raportów zaktualizowanej konfiguracji (lub warunek błędu przy użyciu `status` właściwości). Poniżej przedstawiono część zgłaszanych właściwości:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. Zaplecze rozwiązania może śledzić wyniki operacja konfiguracji różnych urządzeniach przez [zapytań](iot-hub-devguide-query-language.md) bliźniaczych reprezentacji urządzeń.

> [!NOTE]
> Poprzedzających fragmentów są przykładami, zoptymalizowane pod kątem czytelności jednym ze sposobów kodowanie konfigurację urządzenia i jego stan. Bliźniacza reprezentacja urządzenia żądanego i zgłaszane właściwości bliźniaczych reprezentacji urządzeń usługi IoT Hub nie nakłada określonego schematu.
> 

Za pomocą twins do synchronizowania długotrwałych operacji, takich jak aktualizacje oprogramowania układowego. Aby uzyskać więcej informacji o tym, jak korzystać z właściwości synchronizacji i śledzenie długotrwałej operacji na urządzeniach, zobacz [Użyj żądane właściwości, aby skonfigurować urządzenia](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Operacje zaplecza

Zaplecze rozwiązania działa w bliźniaczej reprezentacji urządzenia przy użyciu następujących niepodzielne operacje udostępniane za pośrednictwem protokołu HTTPS:

* **Pobieranie bliźniaczej reprezentacji urządzenia za pomocą Identyfikatora**. Ta operacja zwraca dokumentu bliźniaczej reprezentacji urządzenia, tym tagów i właściwości żądaną i podać systemu.

* **Częściowo zaktualizować bliźniaczej reprezentacji urządzenia**. Ta operacja umożliwia zapleczu rozwiązania celu częściowego zaktualizowania tagi i żądane właściwości w bliźniaczej reprezentacji urządzenia. Częściową aktualizację jest wyrażona jako dokument JSON, który dodaje lub aktualizuje dowolną właściwość. Właściwości ustawione na `null` są usuwane. Poniższy przykład tworzy nowy żądaną właściwość z wartością `{"newProperty": "newValue"}`, zastępuje istniejącą wartość z `existingProperty` z `"otherNewValue"`i usuwa `otherOldProperty`. Żadne inne zmiany zostaną wprowadzone do istniejącego żądane właściwości lub tagi:

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

* **Otrzymuj powiadomienia bliźniaczej reprezentacji**. Ta operacja umożliwia zapleczu rozwiązania otrzymywać powiadomienia po zmodyfikowaniu bliźniaczej reprezentacji. Aby to zrobić, rozwiązanie IoT musi utworzyć trasę i ustaw źródło danych jest równa *twinChangeEvents*. Domyślnie nie takie trasy wstępnie istnieje, więc są wysyłane żadne powiadomienia bliźniaczej reprezentacji. Jeśli szybkość zmian jest zbyt duża lub z innych powodów, takich jak wewnętrzne błędy, usługa IoT Hub może wysłać tylko jedno powiadomienie, który zawiera wszystkie zmiany. W związku z tym jeśli aplikacja wymaga inspekcji i rejestrowania dla wszystkich pośrednich stanów niezawodne, należy użyć komunikatów z urządzenia do chmury. Bliźniacza reprezentacja komunikat powiadomienia zawiera właściwości i treść.

  - Właściwości

    | Name (Nazwa) | Wartość |
    | --- | --- |
    $content — typ | application/json |
    $iothub-enqueuedtime |  Czas wysłania powiadomienia |
    $iothub-message-source | twinChangeEvents |
    $content — kodowanie | UTF-8 |
    deviceId | Identyfikator urządzenia |
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

Obsługa wszystkich poprzednich operacji [optymistycznej współbieżności](iot-hub-devguide-device-twins.md#optimistic-concurrency) i wymagają **ServiceConnect** uprawnienia, zgodnie z definicją w [kontrolować dostęp do usługi IoT Hub](iot-hub-devguide-security.md).

Oprócz tych operacji zaplecze rozwiązania może:

* Zapytań bliźniaczych reprezentacji urządzeń przy użyciu przypominającego SQL [język zapytań usługi IoT Hub](iot-hub-devguide-query-language.md).

* Wykonywanie operacji w dużych zestawach bliźniaczych reprezentacji urządzeń przy użyciu [zadań](iot-hub-devguide-jobs.md).

## <a name="device-operations"></a>Operacje dotyczące urządzenia

Aplikacji urządzenie działa w bliźniaczej reprezentacji urządzenia przy użyciu następujących niepodzielne operacje:

* **Pobieranie bliźniaczej reprezentacji urządzenia**. Ta operacja zwraca dokument bliźniaczej reprezentacji urządzenia (w tym tagów i właściwości żądaną i podać system) dla aktualnie podłączonego urządzenia.

* **Częściowo aktualizowania zgłoszonych właściwości**. Ta operacja umożliwia częściową aktualizację zgłoszonych właściwości aktualnie podłączonego urządzenia. Ta operacja używa tego samego formatu aktualizacji JSON, że rozwiązanie kopię końcowych zastosowań częściową aktualizację żądane właściwości.

* **Obserwuj żądane właściwości**. Obecnie podłączone urządzenie można otrzymywać powiadomienia o aktualizacjach, żądanych właściwości, gdy wystąpią. Urządzenie otrzyma tego samego formularza update (wymiana częściowego lub pełnego) wykonywana przez zaplecze rozwiązania.

Wymagaj wszystkich poprzednich operacji **DeviceConnect** uprawnienia, zgodnie z definicją w [kontroli dostępu do usługi IoT Hub](iot-hub-devguide-security.md).

[Zestawy SDK urządzeń Azure IoT](iot-hub-devguide-sdks.md) ułatwiają używać poprzedniej operacji z wielu języków i platform. Aby uzyskać więcej informacji na potrzeby szczegółów elementów podstawowych usługi IoT Hub dla żądanych właściwości synchronizacji, zobacz [przepływ ponownego łączenia urządzeń](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="tags-and-properties-format"></a>Format właściwości i tagów

Tagi, żądanych właściwości i zgłaszane właściwości są obiektami JSON z następującymi zastrzeżeniami:

* Wszystkie klucze w obiekty JSON jest rozróżniana wielkość liter 64 bajtów ciągów UNICODE UTF-8. Dozwolone znaki wykluczenia znaków kontrolnych UNICODE (segmenty C0 i C1) i `.`, `$`i SP.

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

## <a name="device-twin-size"></a>Rozmiar bliźniaczej reprezentacji urządzenia

Usługa IoT Hub wymusza ograniczenie rozmiaru 8KB na wszystkich odpowiednich wartości całkowitej `tags`, `properties/desired`, i `properties/reported`, z wyłączeniem elementów tylko do odczytu.

Rozmiar jest obliczany od policzenia wszystkich znaków, z wyjątkiem znaków kontrolnych UNICODE (segmenty C0 i C1) i miejsca do magazynowania, które znajdują się poza stałe typu string.

Usługa IoT Hub z powodu błędu odrzuca wszystkie operacje, które spowoduje zwiększenie rozmiaru dokumentów, które przekracza limit.

## <a name="device-twin-metadata"></a>Metadane bliźniaczej reprezentacji urządzenia

Usługa IoT Hub utrzymuje sygnaturę czasową ostatniej aktualizacji dla każdego obiektu JSON w bliźniaczej reprezentacji urządzenia żądanego i zgłaszanych właściwości. Sygnatury czasowe są w UTC i zakodowane w [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) format `YYYY-MM-DDTHH:MM:SS.mmmZ`.

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

Bliźniacza reprezentacja urządzenia żądanego i zgłaszanych właściwości nie ma elementów etag, ale `$version` wartość, która może być przyrostowe. Podobnie na element ETag wersji może służyć przez stronę aktualizacji do wymuszenia spójności aktualizacji. Na przykład aplikacji urządzenia dla zgłaszanych właściwości lub zaplecze rozwiązania dla żądanej właściwości.

Wersje są również przydatne, gdy observing agenta (np. aplikacji urządzenia, żądanych właściwości obserwowania) należy uzgodnić sam między wynik operacji pobierania i powiadomienie o aktualizacji. [Sekcji przepływu ponowne nawiązanie połączenia urządzenia](iot-hub-devguide-device-twins.md#device-reconnection-flow) zawiera więcej informacji.

## <a name="device-reconnection-flow"></a>Przepływ ponownego łączenia urządzeń

Usługa IoT Hub nie zostaną zachowane żądane właściwości powiadomienia o aktualizacji dla urządzeń o odłączony. Wynika, że urządzenia, które nawiązuje połączenie musi pobrać pełną żądane właściwości dokumentu, oprócz subskrypcji powiadomień o aktualizacji. Biorąc pod uwagę możliwość sam między powiadomienia o aktualizacji i pełne pobieranie, należy zapewnić następujący przepływ:

1. Łączy się z Centrum IoT hub z aplikacji urządzenia.
2. Subskrybuje aplikacji urządzenia dla żądanych właściwości powiadomienia o aktualizacji.
3. Aplikacja urządzenia pobiera pełny dokument dla odpowiednich właściwości.

Zignorować wszystkie powiadomienia z aplikacji urządzenia `$version` mniejsza lub równa wersji pełny dokument pobrany. Takie podejście jest możliwe, ponieważ usługa IoT Hub gwarantuje wersje zawsze zwiększyć.

> [!NOTE]
> Tę logikę został już zaimplementowany w [zestawy SDK urządzeń Azure IoT](iot-hub-devguide-sdks.md). Ten opis jest przydatny tylko wtedy, gdy aplikacji urządzenia nie mogą używać żadnych zestawów SDK urządzeń Azure IoT i należy prawidłowo programu interfejsu MQTT.
> 

## <a name="additional-reference-material"></a>Dodatkowe materiały

Inne tematy referencyjne w przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punktów końcowych usługi IoT Hub](iot-hub-devguide-endpoints.md) artykule opisano różne punkty końcowe, które każde Centrum IoT hub udostępnia dla operacji zarządzania i środowiska wykonawczego.

* [Przydziału i ograniczanie przepływności](iot-hub-devguide-quotas-throttling.md) artykule opisano przydziały, które mają zastosowanie do usługi IoT Hub i zachowanie ograniczania przepływności można oczekiwać, gdy korzystasz z usługi.

* [Zestawy SDK urządzeń i usługi Azure IoT](iot-hub-devguide-sdks.md) artykule wymieniono języka różnych zestawów SDK, można użyć podczas tworzenia aplikacji usług i urządzeń, które współdziałają z usługą IoT Hub.

* [Język zapytań usługi IoT Hub dla bliźniaczych reprezentacji urządzeń, zadań i routingu wiadomości](iot-hub-devguide-query-language.md) artykule języka zapytań usługi IoT Hub, można użyć, aby pobrać informacje z usługi IoT Hub o bliźniaczych reprezentacji urządzeń i zadań.

* [Obsługa protokołu MQTT Centrum IoT](iot-hub-mqtt-support.md) artykuł zawiera więcej informacji na temat usługi IoT Hub, obsługa protokołu MQTT.

## <a name="next-steps"></a>Kolejne kroki

Teraz wiesz o bliźniaczych reprezentacji urządzeń, może zainteresować się w następujących tematach przewodnik dla deweloperów usługi IoT Hub:

* [Zrozumieniu i użytkowaniu bliźniaczych reprezentacjach modułów usługi IoT Hub](iot-hub-devguide-module-twins.md)
* [Wywoływanie metody bezpośredniej o urządzeniu](iot-hub-devguide-direct-methods.md)
* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md)

Aby wypróbować pojęcia opisane w tym artykule, zobacz następujące samouczki usługi IoT Hub:

* [Jak używać bliźniaczej reprezentacji urządzenia](iot-hub-node-node-twin-getstarted.md)
* [Jak korzystać z właściwości bliźniaczych reprezentacji urządzeń](tutorial-device-twins.md)
* [Zarządzanie urządzeniami za pomocą narzędzia IoT platformy Azure dla programu VS Code](iot-hub-device-management-iot-toolkit.md)
