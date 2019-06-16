---
title: Omówienie pomocy technicznej usługi Azure IoT Hub MQTT | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — Obsługa urządzeń nawiązujących połączenie z punktem końcowym skierowaną do urządzenia usługi IoT Hub przy użyciu protokołu MQTT. Zawiera informacje o wbudowanych MQTT obsługi w zestawy SDK urządzeń Azure IoT.
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: rezas
ms.openlocfilehash: 1a0b6cf8ce272733c259283fdec9c215ac2b0fd8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61442566"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Komunikować się z Centrum IoT hub przy użyciu protokołu MQTT

Usługa IoT Hub udostępnia urządzenia do komunikowania się z punktami końcowymi urządzenia usługi IoT Hub przy użyciu:

* [MQTT 3.1.1](https://mqtt.org/) na porcie 8883
* MQTT 3.1.1 za pośrednictwem protokołu WebSocket na porcie 443.

Usługa IoT Hub nie jest brokerem MQTT w pełni funkcjonalne i nie obsługuje wszystkich zachowań, które są określone w standardzie MQTT 3.1.1. W tym artykule opisano, jak urządzenia za pomocą usługi obsługiwanej zachowania MQTT komunikować się z usługą IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Cała komunikacja urządzenia z usługą IoT Hub musi zostać zabezpieczony za pomocą protokołów TLS/SSL. W związku z tym usługi IoT Hub nie obsługuje niezabezpieczone połączenia za pośrednictwem portu 1883.

## <a name="connecting-to-iot-hub"></a>Łączenie z usługą IoT Hub

Urządzenia można użyć protokołu MQTT, połączyć się z Centrum IoT hub przy użyciu dowolnej z poniższych opcji.

* Biblioteki w [zestawami SDK Azure IoT](https://github.com/Azure/azure-iot-sdks).
* Protokołu MQTT bezpośrednio.

## <a name="using-the-device-sdks"></a>Za pomocą zestawów SDK urządzeń

[Zestawy SDK urządzeń](https://github.com/Azure/azure-iot-sdks) obsługujące protokołu MQTT są dostępne dla języka Java, Node.js, C, C#i Python. Zestawy SDK urządzeń umożliwia standardowe parametry połączenia Centrum IoT Hub nawiązać połączenie z Centrum IoT hub. Aby użyć protokołu MQTT, musi być równa parametr Protokół klienta **MQTT**. Domyślnie, połączenie urządzenia z zestawów SDK usługi IoT Hub przy użyciu **CleanSession** flaga jest ustawiona na **0** i użyj **QoS 1** w wymianie wiadomości z usługą IoT hub.

Gdy urządzenie jest podłączone do usługi IoT hub, zestawy SDK urządzeń zapewnia metody, które umożliwiają urządzeniu wymiana komunikatów z Centrum IoT hub.

Poniższa tabela zawiera linki do przykładów kodu dla każdego obsługiwanego języka i określa parametr, aby nawiązać połączenie z Centrum IoT przy użyciu protokołu MQTT.

| Język | Parametr protokołu |
| --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) |azure-iot-device-mqtt |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |IotHubClientProtocol.MQTT |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) |MQTT_Protocol |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) |TransportType.Mqtt |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples) |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrowanie aplikacji urządzenia z protokołu AMQP do protokołu MQTT

Jeśli używasz [zestawy SDK urządzeń](https://github.com/Azure/azure-iot-sdks), przełączenie z za pomocą protokołu AMQP do MQTT konieczna jest zmiana parametru protokołu podczas inicjowania klienta, jak wspomniano wcześniej.

Po wykonaniu tej czynności upewnij się sprawdzić następujące elementy:

* Protokół AMQP zwraca informacje o błędach dla wielu warunków, gdy MQTT przerywa połączenie. W rezultacie logika obsługi wyjątku może wymagać pewnych zmian.

* Nie obsługuje protokołu MQTT *Odrzuć* operacje podczas odbierania [komunikatów z chmury do urządzeń](iot-hub-devguide-messaging.md). Jeśli serwer zaplecza w aplikacji musi czekać na odpowiedź z aplikacji urządzenia, należy wziąć pod uwagę przy użyciu [metody bezpośrednie](iot-hub-devguide-direct-methods.md).

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Przy użyciu protokołu MQTT bezpośrednio (jako urządzenie)

Jeśli urządzenia nie można użyć zestawów SDK urządzeń, nadal można połączyć do urządzeń publicznych punktów końcowych przy użyciu protokołu MQTT na porcie 8883. W **CONNECT** pakietów, urządzenia, należy użyć następujących wartości:

* Aby uzyskać **ClientId** pola, użyj **deviceId**.

* Aby uzyskać **Username** pola, użyj `{iothubhostname}/{device_id}/?api-version=2018-06-30`, gdzie `{iothubhostname}` jest pełny rekord CName usługi IoT hub.

    Na przykład jeśli nazwą Centrum IoT hub jest **contoso.azure-devices.net** i jeśli nazwa urządzenia jest **MyDevice01**, pełną **Username** pole powinno zawierać:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* Aby uzyskać **hasło** pola, użyj tokenu sygnatury dostępu Współdzielonego. Format tokenu sygnatury dostępu Współdzielonego jest taka sama, jak w przypadku protokołów HTTPS i AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Jeśli używasz uwierzytelniania certyfikatów X.509, hasła tokenu sygnatury dostępu Współdzielonego nie są wymagane. Aby uzyskać więcej informacji, zobacz [konfigurowania zabezpieczeń X.509 w usłudze Azure IoT Hub](iot-hub-security-x509-get-started.md)

  Aby uzyskać więcej informacji na temat generowania tokenów sygnatur dostępu Współdzielonego, zobacz sekcję urządzenia [tokenów zabezpieczających za pomocą usługi IoT Hub](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  Podczas testowania, możesz również użyć dla wielu platform [narzędzia usługi Azure IoT dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) lub [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) narzędzie, aby szybko wygenerować token sygnatury dostępu Współdzielonego, który można skopiować i wkleić do własnego kodu:

Aby uzyskać narzędzia usługi Azure IoT:

1. Rozwiń **AZURE IOT HUB DEVICES** kartę w lewym dolnym rogu programu Visual Studio Code.
  
2. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz pozycję **generowania tokenu sygnatury dostępu Współdzielonego dla urządzenia**.
  
3. Ustaw **czas wygaśnięcia** i naciśnij klawisz "Enter".
  
4. Token sygnatury dostępu Współdzielonego jest utworzony i skopiowany do Schowka.

Aby uzyskać Device Explorer:

1. Przejdź do **zarządzania** karcie **Device Explorer**.

2. Kliknij przycisk **tokenu sygnatury dostępu Współdzielonego** (w prawym górnym po prawej stronie).

3. Na **SASTokenForm**, wybierz urządzenie **DeviceID** listy rozwijanej. Ustaw użytkownika **TTL**.

4. Kliknij przycisk **Generuj** do utworzenia tokenu.

   Token sygnatury dostępu Współdzielonego, który jest generowany ma następującą strukturę:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Część tego tokenu do użycia jako **hasło** pole, aby nawiązać połączenie przy użyciu protokołu MQTT jest:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Dla protokołu MQTT połączenia i odłączyć pakietów, usługi IoT Hub wysyła zdarzenia w **monitorowanie operacji** kanału. To zdarzenie zawiera dodatkowe informacje, które mogą ułatwić rozwiązywanie problemów z łącznością.

Można określić aplikacji urządzenia **będzie** wiadomość **CONNECT** pakietów. Skorzystaj z aplikacji urządzenia `devices/{device_id}/messages/events/` lub `devices/{device_id}/messages/events/{property_bag}` jako **będzie** nazwy tematu, aby zdefiniować **będzie** mają być przekazywane jako komunikaty telemetryczne wiadomości. W tym przypadku Jeśli zamknięto połączenie sieciowe, ale **ROZŁĄCZENIA** pakietów nie otrzymano wcześniej z urządzenia, a następnie wysyła usługi IoT Hub **będzie** wiadomości podano w **CONNECT** pakietów do kanału danych telemetrycznych. Kanał danych telemetrycznych można albo domyślnie **zdarzenia** punkt końcowy lub niestandardowy punkt końcowy zdefiniowany przez usługę IoT Hub routingu. Komunikat ma **iothub MessageType** właściwość z wartością **będzie** do niej przypisany.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Przy użyciu protokołu MQTT bezpośrednio (jako moduł)

Połączenie z Centrum IoT Hub za pośrednictwem protokołu MQTT za pomocą tożsamości modułu jest podobny do urządzenia (opisane [powyżej](#using-the-mqtt-protocol-directly-as-a-device)), ale należy użyć następującej składni:

* Ustaw identyfikator klienta `{device_id}/{module_id}`.

* Jeśli uwierzytelnianie przy użyciu nazwy użytkownika i hasła, węzła username ustaw `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` i używają tokenu sygnatury dostępu Współdzielonego, skojarzone z tożsamością modułu jako hasło.

* Użyj `devices/{device_id}/modules/{module_id}/messages/events/` jako temat do publikowania danych telemetrycznych.

* Użyj `devices/{device_id}/modules/{module_id}/messages/events/` jako temat będzie.

* Bliźniaczego elementu GET i tematy poprawki są identyczne dla modułów i urządzeń.

* Temat stan bliźniaczej reprezentacji jest takie samo dla modułów i urządzeń.

### <a name="tlsssl-configuration"></a>Konfiguracja protokołu TLS/SSL

Aby użyć protokołu MQTT protokołu bezpośrednio klienta *musi* łączenie za pośrednictwem protokołu TLS/SSL. Aby pominąć ten krok zakończy się niepowodzeniem z błędami połączenia.

Aby możliwe było nawiązanie połączenia TLS, może być konieczne do pobrania i odwoływać się do certyfikatu głównego Baltimore firmy DigiCert. Ten certyfikat jest ten, który korzysta z platformy Azure do zabezpieczenia połączenia. Możesz znaleźć tego certyfikatu w [Azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) repozytorium. Więcej informacji o tych certyfikatach znajduje się na [witryny sieci Web firmy Digicert](https://www.digicert.com/digicert-root-certificates.htm).

Przykładowy sposób implementacji przy użyciu wersji języka Python [biblioteki Paho MQTT](https://pypi.python.org/pypi/paho-mqtt) przez Eclipse Foundation może wyglądać podobnie do poniższego.

Najpierw zainstaluj bibliotekę Paho ze środowiska wiersza polecenia:

```cmd/sh
pip install paho-mqtt
```

Następnie należy zaimplementować klienta skrypt w języku Python. Zastąp symbole zastępcze w następujący sposób:

* `<local path to digicert.cer>` jest to ścieżka do pliku lokalnego, który zawiera certyfikat główny Baltimore firmy DigiCert. Ten plik można utworzyć, kopiując informacje o certyfikacie z [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) w zestawie Azure IoT SDK dla języka C. zawierają wiersze `-----BEGIN CERTIFICATE-----` i `-----END CERTIFICATE-----`, Usuń `"` znaki na początku i końcu każdego wiersza i Usuń `\r\n` znaków na końcu każdego wiersza.

* `<device id from device registry>` jest to identyfikator urządzenia dodanego do usługi IoT hub.

* `<generated SAS token>` jest tokenem sygnatury dostępu Współdzielonego dla urządzenia, utworzony zgodnie z opisem w poprzedniej sekcji w niniejszym artykule.

* `<iot hub name>` Nazwa centrum IoT hub.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"

def on_connect(client, userdata, flags, rc):
  print ("Device connected with result code: " + str(rc))
def on_disconnect(client, userdata, rc):
  print ("Device disconnected with result code: " + str(rc))
def on_publish(client, userdata, mid):
  print ("Device sent message")

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" + device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

### <a name="sending-device-to-cloud-messages"></a>Wysyłanie komunikatów z urządzenia do chmury

Po wprowadzeniu pomyślnie nawiązać połączenie, urządzenie może wysyłać wiadomości do usługi IoT Hub przy użyciu `devices/{device_id}/messages/events/` lub `devices/{device_id}/messages/events/{property_bag}` jako **nazwy tematu**. `{property_bag}` Element umożliwia urządzeniu do wysyłania komunikatów za pomocą dodatkowych właściwości w formacie zakodowane w adresie url. Na przykład:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> To `{property_bag}` element używa tego samego kodu jako ciągi zapytań za pomocą protokołu HTTPS.

Oto lista zachowania specyficzne dla implementacji usługi IoT Hub:

* Usługi IoT Hub nie obsługuje wiadomości QoS 2. Jeśli aplikacja urządzenia publikuje wiadomość z **QoS 2**, usługi IoT Hub zamyka połączenie sieciowe.

* Usługa IoT Hub nie jest trwały Zachowaj wiadomości. Jeśli urządzenie wysyła komunikat z **Zachowaj** Flaga ustawiona na 1, dodaje usługę IoT Hub **x — zoptymalizowany pod kątem zachować** właściwość aplikacji do wiadomości. W tym przypadku zamiast przechowywanie komunikatów Zachowaj, usługi IoT Hub przekazuje go do aplikacji zaplecza.

* Usługa IoT Hub obsługuje tylko jedno aktywne połączenie MQTT na każdym urządzeniu. Wszelkie nowe połączenie MQTT imieniu takim samym Identyfikatorem urządzenia powoduje, że usługi IoT Hub w celu usunięcia istniejącego połączenia.

Aby uzyskać więcej informacji, zobacz [komunikatów przewodnik dewelopera](iot-hub-devguide-messaging.md).

### <a name="receiving-cloud-to-device-messages"></a>Odbieranie komunikatów z chmury do urządzeń

Aby odbierać komunikaty z Centrum IoT Hub, urządzenia powinien uzyskać subskrypcję za pomocą `devices/{device_id}/messages/devicebound/#` jako **filtr tematu**. Wielopoziomowe symbolu wieloznacznego `#` w filtrze tematu jest używana tylko po to, aby zezwolić na urządzenia otrzymać dodatkowe właściwości w nazwę tematu. Usługa IoT Hub nie zezwala na użycie `#` lub `?` symboli wieloznacznych do filtrowania podtematy. Ponieważ usługa IoT Hub nie jest ogólnego przeznaczenia pub-sub brokera obsługi komunikatów, go obsługuje tylko nazwy tematu udokumentowane i filtry tematów.

Urządzenie nie odbiera komunikaty z usługi IoT Hub, aż pomyślnie przyłącza się do jej punkt końcowy specyficzny dla urządzenia, reprezentowane przez `devices/{device_id}/messages/devicebound/#` filtru tematu. Po ustaleniu subskrypcji otrzymaniem przez nie tych komunikatów z chmury do urządzeń, które zostały wysłane do niej po upływie czasu subskrypcji. Jeśli urządzenie łączy się z **CleanSession** flaga jest ustawiona na **0**, subskrypcji są utrwalane w różnych sesjach. W takim przypadku podczas następnego urządzenie łączy się z **CleanSession 0** otrzymuje żadnych oczekujących wiadomości wysyłane do niej przy braku połączenia. Jeśli urządzenie korzysta **CleanSession** flaga jest ustawiona na **1** , nie otrzyma jakiekolwiek komunikaty z usługi IoT Hub, dopóki nie subskrybuje punktu końcowego urządzenia.

IoT Hub dostarczy komunikatów za pomocą **nazwy tematu** `devices/{device_id}/messages/devicebound/`, lub `devices/{device_id}/messages/devicebound/{property_bag}` przypadku właściwości komunikatu. `{property_bag}` zawiera pary klucz/wartość zakodowane w adresie url właściwości komunikatu. Tylko właściwości aplikacji i użytkownika można ustawić właściwości (takie jak **messageId** lub **correlationId**) są uwzględnione w zbiorze właściwości. System właściwości nazwy zawierają prefiks **$** , właściwości aplikacji za pomocą oryginalna nazwa właściwości żadnego prefiksu.

Gdy aplikacja urządzenia subskrybowanie tematu z **QoS 2**, usługi IoT Hub daje maksymalny poziom QoS 1 w **SUBACK** pakietów. Po utworzeniu tego Centrum IoT Hub dostarcza komunikaty do urządzenia przy użyciu QoS 1.

### <a name="retrieving-a-device-twins-properties"></a>Pobieranie właściwości bliźniaczej reprezentacji urządzenia

Po pierwsze urządzenie subskrybuje `$iothub/twin/res/#`, aby otrzymywać odpowiedzi operacji. Następnie wysyła pustego komunikatu do tematu `$iothub/twin/GET/?$rid={request id}`, z wypełnione wartością **identyfikator żądania**. Usługa wysyła następnie komunikat odpowiedzi zawierające dane bliźniaczej reprezentacji urządzenia, na temat `$iothub/twin/res/{status}/?$rid={request id}`, korzystając z tych samych **identyfikator żądania** jako żądania.

Identyfikator żądania może być dowolnego prawidłową wartość dla wartości właściwości komunikatu zgodnie [usługi IoT Hub komunikatów przewodnik dewelopera](iot-hub-devguide-messaging.md), i stan sprawdzania poprawności jako liczba całkowita.

Treść odpowiedzi zawiera sekcji właściwości w bliźniaczej reprezentacji urządzenia, jak pokazano w poniższym przykładzie odpowiedzi:

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

Możliwe kody są:

|Stan | Opis |
| ----- | ----------- |
| 204 | Powodzenie (Brak zawartości jest zwracana) |
| 429 | Za dużo żądań (ograniczone), jak na [usługi IoT Hub, ograniczanie przepustowości](iot-hub-devguide-quotas-throttling.md) |
| 5** | Błędy serwera |

Aby uzyskać więcej informacji, zobacz [— przewodnik dewelopera bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md).

### <a name="update-device-twins-reported-properties"></a>Zgłoszonych właściwości bliźniaka urządzenia aktualizacji

Do aktualizowania zgłoszonych właściwości, urządzenie generuje żądanie do usługi IoT Hub za pośrednictwem publikacji w wyznaczonym tematu MQTT. Po przetworzeniu żądania, usługi IoT Hub odpowiada stanie powodzenie lub Niepowodzenie operacji aktualizacji za pośrednictwem publikacji innego tematu. W tym temacie może być subskrybowana przez urządzenie aby powiadomić go o wyniku jego żądanie aktualizacji bliźniaczej reprezentacji. Aby zaimplementować tego typu żądanie/odpowiedź interakcji w MQTT, możemy wykorzystać pojęcie identyfikator żądania (`$rid`) udostępniane początkowo przez urządzenie w jego żądanie aktualizacji. Ten identyfikator żądania znajduje się również w odpowiedzi z usługi IoT Hub umożliwia skorelowanie odpowiedzi na żądanie wcześniej określonego urządzenia.

Poniższa sekwencja opisuje, jak urządzenie aktualizuje zgłoszonych właściwości w bliźniaczej reprezentacji urządzenia w usłudze IoT Hub:

1. Urządzenia muszą najpierw zasubskrybować `$iothub/twin/res/#` tematu, aby otrzymywać odpowiedzi operacji w usłudze IoT Hub.

2. Urządzenie wysyła komunikat zawierający aktualizacji bliźniaczej reprezentacji urządzenia, aby `$iothub/twin/PATCH/properties/reported/?$rid={request id}` tematu. Ten komunikat zawiera **identyfikator żądania** wartość.

3. Usługa wysyła następnie komunikat odpowiedzi, który zawiera nową wartość elementu ETag dla kolekcji zgłaszanych właściwości na temat `$iothub/twin/res/{status}/?$rid={request id}`. Ten komunikat odpowiedzi używa tych samych **identyfikator żądania** jako żądania.

Treść żądania zawiera dokument JSON, który zawiera nowe wartości zgłaszanych właściwości. Każdy element członkowski w dokumencie JSON aktualizacji lub Dodaj odpowiedniego elementu członkowskiego w dokumencie bliźniaczej reprezentacji urządzenia. Zestaw elementów członkowskich `null`, usuwa element członkowski z zawierającego go obiektu. Na przykład:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Możliwe kody są:

|Stan | Opis |
| ----- | ----------- |
| 200 | Powodzenie |
| 400 | Nieprawidłowe żądanie. Malformed JSON |
| 429 | Za dużo żądań (ograniczone), jak na [usługi IoT Hub, ograniczanie przepustowości](iot-hub-devguide-quotas-throttling.md) |
| 5** | Błędy serwera |

Fragment kodu języka python poniżej, przedstawiono bliźniaczą reprezentację zgłaszane właściwości proces aktualizacji za pośrednictwem protokołu MQTT (przy użyciu klienta protokołu MQTT Paho):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" + rid, twin_reported_property_patch, qos=0)
```

W razie powodzenia bliźniaczej reprezentacji zgłaszane właściwości operacji aktualizacji powyżej, komunikat publikacji z usługi IoT Hub będzie miał następujący temat: `$iothub/twin/res/204/?$rid=1&$version=6`, gdzie `204` jest kod stanu wskazujący Powodzenie. `$rid=1` odnosi się do Identyfikatora żądania udostępnianym przez urządzenie w kodzie i `$version` odnosi się do wersji sekcji zgłaszane właściwości bliźniaczych reprezentacji urządzeń po aktualizacji.

Aby uzyskać więcej informacji, zobacz [— przewodnik dewelopera bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md).

### <a name="receiving-desired-properties-update-notifications"></a>Odbieranie powiadomień o aktualizacji żądanych właściwości

Gdy urządzenie jest połączone, usługi IoT Hub wysyła powiadomienia do tematu `$iothub/twin/PATCH/properties/desired/?$version={new version}`, które zawierają zawartość aktualizacji wykonywana przez zaplecze rozwiązania. Na przykład:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Jak w przypadku aktualizacji właściwości `null` wartości oznacza, że elemencie członkowskim obiektu JSON jest usuwana. Ponadto należy pamiętać, że `$version` wskazuje nową wersję sekcji żądane właściwości bliźniaczej reprezentacji.

> [!IMPORTANT]
> Usługa IoT Hub generuje powiadomienia o zmianach, tylko wtedy, gdy urządzenia są podłączone. Upewnij się zaimplementować [przepływ ponownego łączenia urządzeń](iot-hub-devguide-device-twins.md#device-reconnection-flow) do zachowania żądanych właściwości synchronizowane między centrum IoT i aplikacji urządzenia.

Aby uzyskać więcej informacji, zobacz [— przewodnik dewelopera bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md).

### <a name="respond-to-a-direct-method"></a>Odpowiadanie na metody bezpośredniej

Po pierwsze, urządzenie może mieć do subskrybowania `$iothub/methods/POST/#`. Usługa IoT Hub wysyła żądania metody do tematu `$iothub/methods/POST/{method name}/?$rid={request id}`z prawidłowym kodem JSON lub pustą treść.

Aby odpowiedzieć, urządzenie wysyła komunikat z prawidłowym plikiem JSON lub pustą treść do tematu `$iothub/methods/res/{status}/?$rid={request id}`. W tym komunikacie **identyfikator żądania** musi pasować do w komunikacie żądania i **stan** musi być liczbą całkowitą.

Aby uzyskać więcej informacji, zobacz [bezpośrednie metody przewodnik dewelopera usługi](iot-hub-devguide-direct-methods.md).

### <a name="additional-considerations"></a>Dodatkowe zagadnienia

Jak jest ostatnim brany pod uwagę, jeśli trzeba dostosować zachowanie protokołu MQTT po stronie chmury, należy zapoznać się [brama protokołu Azure IoT](iot-hub-protocol-gateway.md). To oprogramowanie umożliwia wdrażanie bramy o wysokiej wydajności niestandardowego protokołu tego interfejsów bezpośrednio z usługą IoT Hub. Brama protokołu Azure IoT umożliwia dostosowanie protokołu urządzenia, aby pomieścić brownfield MQTT wdrożeń lub innych niestandardowych protokołów. Takie podejście wymaga jednak uruchamiasz i działają bramy protokołu niestandardowego.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat protokołu MQTT, zobacz [dokumentacji MQTT](https://mqtt.org/documentation).

Aby dowiedzieć się więcej na temat planowania wdrożenia usługi IoT Hub, zobacz:

* [Certyfikat platformy Microsoft Azure dla IoT — wykaz urządzeń](https://catalog.azureiotsolutions.com/)
* [Obsługa dodatkowych protokołów](iot-hub-protocol-gateway.md)
* [Porównanie z usługą Event Hubs](iot-hub-compare-event-hubs.md)
* [Skalowanie i wysoka dostępność i odzyskiwanie po awarii](iot-hub-scaling.md)

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych za pomocą usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
