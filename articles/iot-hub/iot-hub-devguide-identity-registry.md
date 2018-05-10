---
title: Informacje w rejestrze tożsamości Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — opis w rejestrze tożsamości Centrum IoT i jak z niego korzystać do zarządzania urządzeniami. Zawiera informacje na temat importowania i eksportowania tożsamości urządzenia w partii.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 0706eccd-e84c-4ae7-bbd4-2b1a22241147
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dce0114c19f6153d2cba789cdc00920d42834b2d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Informacje w rejestrze tożsamości w Centrum IoT

Centrum IoT, co ma rejestru tożsamości, która przechowuje informacje dotyczące urządzeń i moduły łączenia się z Centrum IoT. Zanim urządzenie lub modułu można połączyć się z Centrum IoT, musi to być wpis dla tego urządzenia lub moduł w rejestrze tożsamości Centrum IoT. Urządzenie lub modułu muszą również uwierzytelniać z Centrum IoT na podstawie poświadczeń przechowywanych w rejestrze tożsamości.

Identyfikator urządzenia lub modułu przechowywane w rejestrze tożsamości jest rozróżniana wielkość liter.

Na wysokim poziomie w rejestrze tożsamości jest funkcją REST kolekcją zasobów tożsamość urządzenia lub modułu. Po dodaniu wpisu w rejestrze tożsamości Centrum IoT tworzy zestaw zasobów na urządzenie, takie jak kolejki, która zawiera komunikatów w locie chmury do urządzenia.

Należy używać w rejestrze tożsamości:

* Zainicjować obsługę administracyjną urządzeń lub moduły, które nawiązać połączenia z Centrum IoT.
* Kontrola dostępu na urządzenie/na module Twoje Centrum urządzenia lub punkty końcowe skierowane do modułu.

> [!NOTE]
> W rejestrze tożsamości nie zawiera żadnych metadane specyficzne dla aplikacji.

## <a name="identity-registry-operations"></a>Operacje rejestru tożsamości

W rejestrze tożsamości Centrum IoT udostępnia następujące operacje:

* Utwórz tożsamość urządzenia lub modułu
* Zaktualizuj tożsamość urządzenia lub modułu
* Pobrać tożsamości urządzenia lub moduł według Identyfikatora
* Usuń tożsamość urządzenia lub modułu
* Lista maksymalnie 1000 tożsamości
> Moduł dwie tożsamości i moduł znajduje się w publicznej wersji zapoznawczej. Poniżej funkcja będą obsługiwane na tożsamości modułu po ogólne dostępne.
* Eksportuj tożsamości urządzenia do magazynu obiektów blob platformy Azure
* Importuj tożsamości urządzenia z magazynu obiektów blob platformy Azure

Te operacje można użyć optymistycznej współbieżności, jak określono w [RFC7232][lnk-rfc7232].

> [!IMPORTANT]
> Jedynym sposobem pobrania wszystkich tożsamości w rejestrze tożsamości Centrum IoT jest użycie [wyeksportować] [ lnk-export] funkcji.

Centrum IoT rejestru tożsamości:

* Nie zawiera żadnych metadanych aplikacji.
* Możliwy jak słownika, za pomocą **deviceId** lub **moduleId** jako klucz.
* Nie obsługuje obszerne zapytań.

Rozwiązania IoT zwykle ma oddzielny magazyn określonego rozwiązania zawierającego metadane specyficzne dla aplikacji. Na przykład magazynu określonego rozwiązania w rozwiązaniu inteligentne budynku będzie rejestrować miejsca, w której jest wdrażane czujnik temperatury.

> [!IMPORTANT]
> W rejestrze tożsamości można używać tylko do zarządzania urządzeniami i Inicjowanie obsługi operacji. Wykonywanie operacji w rejestrze tożsamości nie należy uwzględniać wysokiej przepływności operacji w czasie wykonywania. Na przykład sprawdzanie stanu połączenia urządzenia przed wysłaniem polecenia nie jest obsługiwany wzorzec. Upewnij się sprawdzić [ograniczania szybkości] [ lnk-quotas] rejestru tożsamości i [pulsu urządzenia] [ lnk-guidance-heartbeat] wzorca.

## <a name="disable-devices"></a>Wyłącz urządzenia

Urządzenia można wyłączyć, aktualizując **stan** właściwość identity w rejestrze tożsamości. Zazwyczaj korzystają z tej właściwości, w przypadku dwóch scenariuszy:

* Podczas inicjowania obsługi administracyjnej procesu aranżacji. Aby uzyskać więcej informacji, zobacz [Inicjowanie obsługi administracyjnej urządzeń][lnk-guidance-provisioning].
* Jeśli z jakiegokolwiek powodu, uważasz, że zostanie naruszony lub przestał nieautoryzowanego urządzenia.

Ta funkcja nie jest dostępne dla modułów.

## <a name="import-and-export-device-identities"></a>Importowanie i eksportowanie tożsamości urządzenia

Użyj operacji asynchronicznych w [punktu końcowego dostawcy zasobów Centrum IoT] [ lnk-endpoints] eksportowania tożsamości urządzenia w trybie zbiorczym z rejestru tożsamości Centrum IoT. Eksporty są długotrwałych zadań, które Użyj blobcontainer dostarczonych przez klienta, aby zapisać dane tożsamości urządzenia odczytać z rejestru tożsamości.

Użyj operacji asynchronicznych w [punktu końcowego dostawcy zasobów Centrum IoT] [ lnk-endpoints] do zaimportowania tożsamości urządzenia w trybie zbiorczym w rejestrze tożsamości Centrum IoT. Importy są długotrwałych zadań, korzystających z danych w kontenerze blob dostarczonych przez klienta do zapisania danych tożsamości urządzenia w rejestrze tożsamości.

Aby uzyskać więcej informacji na temat Importowanie i eksportowanie interfejsów API, zobacz [interfejsy API REST dostawcy zasobów Centrum IoT][lnk-resource-provider-apis]. Aby dowiedzieć się więcej o uruchamianiu importowanie i eksportowanie zadań, zobacz [zbiorcze Zarządzanie tożsamościami urządzenia IoT Hub][lnk-bulk-identity].

## <a name="device-provisioning"></a>Inicjowanie obsługi administracyjnej urządzeń

Dane urządzenie, które przechowuje danego rozwiązania IoT zależy od konkretnych wymogów obowiązujących w tym rozwiązania. Ale co najmniej rozwiązanie musi przechowywać tożsamości urządzenia i klucze uwierzytelniania. Centrum IoT Azure obejmuje rejestru tożsamości, które można przechowywać wartości dla każdego urządzenia, takie jak nazwy, klucze uwierzytelniania i kodów stanu. Rozwiązanie można użyć innych usług Azure, takich jak magazyn tabel, magazyn obiektów blob lub rozwiązania Cosmos bazy danych do przechowywania wszelkich danych dodatkowych urządzeń.

*Inicjowanie obsługi administracyjnej urządzeń* jest proces dodawania dane urządzenie początkowe sklepów w rozwiązaniu. Aby włączyć nowe urządzenia w celu nawiązania połączenia z koncentratorem, należy dodać identyfikator urządzenia i kluczy rejestru tożsamości Centrum IoT. W ramach procesu inicjowania obsługi administracyjnej może być konieczne Inicjuj dane specyficzne dla urządzenia w innych magazynach rozwiązania. Usługa udostępniania urządzeń Centrum IoT Azure umożliwia również włączyć bezobsługową, just-in-time alokacji dla co najmniej jeden centra IoT bez udziału człowieka. Aby dowiedzieć się więcej, zobacz [inicjowania obsługi usługi dokumentacji][lnk-dps].

## <a name="device-heartbeat"></a>Urządzenie pulsu

Centrum IoT rejestru tożsamości zawiera pole o nazwie **element connectionState**. Należy używać tylko **element connectionState** pole w czasie projektowania i debugowania. Rozwiązania IoT nie powinien zapytania dotyczącego pola w czasie wykonywania. Na przykład nie zapytanie **element connectionState** pola, aby sprawdzić, czy urządzenie jest podłączone, aby wysłać wiadomość SMS lub wiadomości chmury do urządzenia.

Jeśli rozwiązania IoT musi wiedzieć, jeśli urządzenie jest podłączone, należy zaimplementować *wzorzec pulsu*.

We wzorcu pulsu urządzenie wysyła komunikaty urządzenia do chmury co najmniej raz co stałej ilości czasu (na przykład co najmniej raz na godzinę). W związku z tym nawet jeśli urządzenie nie ma żadnych danych do wysyłania, nadal wysyła komunikat pusty urządzenia do chmury (zazwyczaj z właściwością, która identyfikuje ją jako pulsu). Na stronie usługi rozwiązania przechowuje mapy z ostatniego pulsu dla poszczególnych urządzeń. Rozwiązanie nie odbierze komunikatu pulsu w oczekiwanym czasie z urządzenia, zakłada, że występuje problem z urządzeniem.

Bardziej złożone wdrożenia mogą zawierać informacje z [operacje monitorowania] [ lnk-devguide-opmon] Aby zidentyfikować urządzenia, które są próby nawiązania połączenia lub komunikowania się niepowodzeniem. Podczas implementowania wzorzec pulsu, upewnij się, że sprawdzanie [IoT Hub przydziały i limity][lnk-quotas].

> [!NOTE]
> Jeśli rozwiązania IoT używa stanu połączenia wyłącznie w celu określenia, czy wysyłać chmury do urządzenia, a nie emitowanych wiadomości do dużych zestawów urządzeń, należy wziąć pod uwagę przy użyciu prostszych *krótki czas wygaśnięcia* wzorca. Ten wzorzec osiąga ten sam rezultat jako utrzymania rejestru stanu połączenia urządzenia przy użyciu wzorca pulsu, będąc bardziej wydajne. Jeśli żądanie jest komunikat potwierdzenia, Centrum IoT może powiadomić o tym, które urządzenia są może odbierać wiadomości, które nie są.

## <a name="device-and-module-lifecycle-notifications"></a>Powiadomienia dotyczące cyklu życia urządzenia i modułu

Centrum IoT można powiadomić rozwiązania IoT, po utworzeniu lub usunięciu, wysyłając powiadomienia dotyczące cyklu życia tożsamości. Aby to zrobić, rozwiązania IoT musi utworzyć trasę i ustaw źródło danych *DeviceLifecycleEvents* lub *ModuleLifecycleEvents*. Domyślnie są wysyłane nie powiadomienia dotyczące cyklu życia, oznacza to, że istnieje wstępnie ma takie tras. Komunikat powiadomienia zawiera właściwości, oraz i treść.

Właściwości: Właściwości systemu wiadomości są poprzedzane prefiksem `'$'` symbolu.

Powiadomienie dla urządzenia:

| Name (Nazwa) | Wartość |
| --- | --- |
|$content — typ | application/json |
|$iothub-enqueuedtime |  Czas wysłania powiadomienia |
|$iothub-message-source | deviceLifecycleEvents |
|$content-kodowania | UTF-8 |
|opType | **createDeviceIdentity** lub **deleteDeviceIdentity** |
|hubName | Nazwa centrum IoT |
|deviceId | Identyfikator urządzenia |
|operationTimestamp | Sygnatura czasowa ISO8601 operacji |
|iothub-message-schema | deviceLifecycleNotification |

Treść: W tej sekcji jest w formacie JSON i przedstawia dwie tożsamości utworzonego urządzenia. Na przykład:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
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
Powiadomienie dla modułu:

| Name (Nazwa) | Wartość |
| --- | --- |
$content — typ | application/json |
$iothub-enqueuedtime |  Czas wysłania powiadomienia |
$iothub-message-source | moduleLifecycleEvents |
$content-kodowania | UTF-8 |
opType | **createModuleIdentity** lub **deleteModuleIdentity** |
hubName | Nazwa centrum IoT |
moduleId | Identyfikator modułu |
operationTimestamp | Sygnatura czasowa ISO8601 operacji |
iothub-message-schema | moduleLifecycleNotification |

Treść: Ta sekcja jest w formacie JSON i przedstawia dwie tożsamości utworzony moduł. Na przykład:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
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

## <a name="device-identity-properties"></a>Właściwości tożsamości urządzenia

Tożsamości urządzenia są reprezentowane jako dokumenty JSON z następującymi właściwościami:

| Właściwość | Opcje | Opis |
| --- | --- | --- |
| deviceId |wymagane, tylko do odczytu na aktualizacje |Ciąg z uwzględnieniem wielkości liter (maksymalnie 128 znaków) znaki alfanumeryczne ASCII 7-bitowego oraz niektórych znaków specjalnych: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| Identyfikator generacji |wymagane, tylko do odczytu |IoT generowanych przez koncentrator, z uwzględnieniem wielkości liter ciąg maksymalnie 128 znaków. Ta wartość jest używana do rozróżniania urządzeń o takiej samej **deviceId**, gdy został usunięty i utworzony ponownie. |
| Element etag |wymagane, tylko do odczytu |Ciąg reprezentujący słaby element ETag dla tożsamości tego urządzenia zgodnie [RFC7232][lnk-rfc7232]. |
| uwierzytelniania |opcjonalne |Obiekt złożony zawierające materiały informacji i zabezpieczeń uwierzytelniania. |
| auth.symkey |opcjonalne |Złożony obiekt zawierający podstawowy i klucz pomocniczy, są przechowywane w formacie base64. |
| status |wymagane |Wskaźnik dostępu. Może być **włączone** lub **wyłączone**. Jeśli **włączone**, urządzenie może nawiązać połączenie. Jeśli **wyłączone**, to urządzenie nie ma dostępu do dowolnego punktu końcowego skierowane do urządzenia. |
| statusReason |opcjonalne |128 ciąg znaków długości, który przechowuje Przyczyna stanu tożsamości urządzenia. Dozwolone są wszystkie znaki UTF-8. |
| statusUpdateTime |tylko do odczytu |Wskaźnik danych czasowych, przedstawiający Data i godzina ostatniej aktualizacji stanu. |
| Element connectionState |tylko do odczytu |Pole wskazujący stan połączenia: albo **połączony** lub **Rozłączono**. To pole reprezentuje Widok Centrum IoT dla stanu połączenia urządzenia. **Ważne**: w tym polu należy używać tylko na potrzeby tworzenia/debugowania. Stan połączenia jest aktualizowany tylko w przypadku urządzeń przy użyciu MQTT lub AMQP. Ponadto jest oparty na poziomie protokołu ping (ping MQTT lub polecenia ping protokołu AMQP) i może mieć Maksymalne opóźnienie tylko 5 minut. Z tego względu mogą być fałszywych alarmów, takie jak urządzenia zgłoszone jako połączona, ale które zostały odłączone. |
| connectionStateUpdatedTime |tylko do odczytu |Zaktualizowano danych czasowych wskaźnik, przedstawiający datę i godzinę ostatniego stanu połączenia. |
| lastActivityTime |tylko do odczytu |Wskaźnik danych czasowych, przedstawiający datę i godzinę ostatniego urządzenia podłączone, odebranych lub wysłanych wiadomości. |

> [!NOTE]
> Stan połączenia można tylko reprezentuje Widok Centrum IoT dla stanu połączenia. Aktualizacje tego stanu może być opóźniona w zależności od warunków sieciowych i konfiguracji.

## <a name="module-identity-properties"></a>Właściwości tożsamości modułu

Moduł tożsamości są reprezentowane jako dokumenty JSON z następującymi właściwościami:

| Właściwość | Opcje | Opis |
| --- | --- | --- |
| deviceId |wymagane, tylko do odczytu na aktualizacje |Ciąg z uwzględnieniem wielkości liter (maksymalnie 128 znaków) znaki alfanumeryczne ASCII 7-bitowego oraz niektórych znaków specjalnych: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| moduleId |wymagane, tylko do odczytu na aktualizacje |Ciąg z uwzględnieniem wielkości liter (maksymalnie 128 znaków) znaki alfanumeryczne ASCII 7-bitowego oraz niektórych znaków specjalnych: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| Identyfikator generacji |wymagane, tylko do odczytu |IoT generowanych przez koncentrator, z uwzględnieniem wielkości liter ciąg maksymalnie 128 znaków. Ta wartość jest używana do rozróżniania urządzeń o takiej samej **deviceId**, gdy został usunięty i utworzony ponownie. |
| Element etag |wymagane, tylko do odczytu |Ciąg reprezentujący słaby element ETag dla tożsamości tego urządzenia zgodnie [RFC7232][lnk-rfc7232]. |
| uwierzytelniania |opcjonalne |Obiekt złożony zawierające materiały informacji i zabezpieczeń uwierzytelniania. |
| auth.symkey |opcjonalne |Złożony obiekt zawierający podstawowy i klucz pomocniczy, są przechowywane w formacie base64. |
| status |wymagane |Wskaźnik dostępu. Może być **włączone** lub **wyłączone**. Jeśli **włączone**, urządzenie może nawiązać połączenie. Jeśli **wyłączone**, to urządzenie nie ma dostępu do dowolnego punktu końcowego skierowane do urządzenia. |
| statusReason |opcjonalne |128 ciąg znaków długości, który przechowuje Przyczyna stanu tożsamości urządzenia. Dozwolone są wszystkie znaki UTF-8. |
| statusUpdateTime |tylko do odczytu |Wskaźnik danych czasowych, przedstawiający Data i godzina ostatniej aktualizacji stanu. |
| Element connectionState |tylko do odczytu |Pole wskazujący stan połączenia: albo **połączony** lub **Rozłączono**. To pole reprezentuje Widok Centrum IoT dla stanu połączenia urządzenia. **Ważne**: w tym polu należy używać tylko na potrzeby tworzenia/debugowania. Stan połączenia jest aktualizowany tylko w przypadku urządzeń przy użyciu MQTT lub AMQP. Ponadto jest oparty na poziomie protokołu ping (ping MQTT lub polecenia ping protokołu AMQP) i może mieć Maksymalne opóźnienie tylko 5 minut. Z tego względu mogą być fałszywych alarmów, takie jak urządzenia zgłoszone jako połączona, ale które zostały odłączone. |
| connectionStateUpdatedTime |tylko do odczytu |Zaktualizowano danych czasowych wskaźnik, przedstawiający datę i godzinę ostatniego stanu połączenia. |
| lastActivityTime |tylko do odczytu |Wskaźnik danych czasowych, przedstawiający datę i godzinę ostatniego urządzenia podłączone, odebranych lub wysłanych wiadomości. |

## <a name="additional-reference-material"></a>Odwołanie dodatkowe materiały

Inne tematy referencyjne w Podręczniku dewelopera Centrum IoT obejmują:

* [Punkty końcowe Centrum IoT] [ lnk-endpoints] opisano różne punkty końcowe, które udostępnia każdego centrum IoT dla operacji zarządzania i środowiska wykonawczego.
* [Ograniczenia przepustowości i przydziały] [ lnk-quotas] opisano przydziały i ograniczenia przepustowości zachowania, które dotyczą usługi Centrum IoT.
* [Zestawy Azure IoT urządzenia i usługi SDK] [ lnk-sdks] wymieniono języka różnych zestawów SDK, można użyć podczas opracowywania aplikacji usług i urządzeń, które współdziałają z Centrum IoT.
* [Język zapytań Centrum IoT] [ lnk-query] opisuje język kwerendy można pobrać z Centrum IoT informacji o twins urządzenia i zadania.
* [Obsługa MQTT Centrum IoT] [ lnk-devguide-mqtt] zapewnia więcej informacji na temat Centrum IoT obsługi protokołu MQTT.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz jak korzystać z Centrum IoT rejestru tożsamości, mogą być zainteresowane w następujących tematach przewodnik dewelopera Centrum IoT:

* [Kontrola dostępu do Centrum IoT][lnk-devguide-security]
* [Użyj twins urządzenia, aby zsynchronizować stanu i konfiguracji][lnk-devguide-device-twins]
* [Wywoływanie metody bezpośrednio na urządzeniu][lnk-devguide-directmethods]
* [Planowanie zadań na wielu urządzeniach][lnk-devguide-jobs]

Aby wypróbować pojęcia opisane w tym artykule, zobacz następujące samouczek Centrum IoT:

* [Rozpoczynanie pracy z Centrum IoT Azure][lnk-getstarted-tutorial]

Aby zapoznać się z usługi IoT Centrum urządzeń inicjowania obsługi administracyjnej Włącz bezobsługową, w czasie inicjowania obsługi, zobacz: 

* [Usługi inicjowania obsługi administracyjnej urządzeniu Centrum IoT Azure][lnk-dps]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
