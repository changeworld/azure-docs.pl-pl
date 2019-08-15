---
title: Informacje o pomocy technicznej dotyczącej usługi Azure IoT Hub MQTT | Microsoft Docs
description: Przewodnik dla deweloperów — obsługa urządzeń łączących się z IoT Hub punktem końcowym dostępnym na urządzeniu przy użyciu protokołu MQTT. Zawiera informacje o wbudowanej obsłudze MQTT w zestawach SDK urządzeń Azure IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 9a6b3a538304f2d09941650e3087130c21422dc0
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946345"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Komunikacja z Centrum IoT Hub przy użyciu protokołu MQTT

IoT Hub umożliwia urządzeniom komunikowanie się z punktami końcowymi IoT Hub Device przy użyciu:

* [MQTT v 3.1.1](https://mqtt.org/) na porcie 8883
* MQTT v 3.1.1 przy użyciu protokołu WebSocket na porcie 443.

IoT Hub nie jest w pełni funkcjonalnym brokerem MQTT i nie obsługuje wszystkich zachowań określonych w standardzie MQTT v 3.1.1. W tym artykule opisano sposób, w jaki urządzenia mogą używać obsługiwanych zachowań MQTT do komunikowania się z IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Cała komunikacja urządzeń z IoT Hub musi być zabezpieczona przy użyciu protokołu TLS/SSL. W związku z tym IoT Hub nie obsługuje połączeń niezabezpieczonych przez port 1883.

## <a name="connecting-to-iot-hub"></a>Nawiązywanie połączenia z IoT Hub

Urządzenie może korzystać z protokołu MQTT do nawiązywania połączenia z usługą IoT Hub przy użyciu dowolnej z poniższych opcji.

* Biblioteki w zestawach [SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdks).
* Protokół MQTT bezpośrednio.

## <a name="using-the-device-sdks"></a>Korzystanie z zestawów SDK urządzeń

[Zestawy SDK urządzeń](https://github.com/Azure/azure-iot-sdks) obsługujące protokół MQTT są dostępne dla języków Java, Node. js, C C#, i Python. Zestawy SDK urządzeń używają standardowego ciągu połączenia IoT Hub, aby nawiązać połączenie z usługą IoT Hub. Aby można było korzystać z protokołu MQTT, parametr protokołu klienta musi być ustawiony na **MQTT**. Domyślnie zestawy SDK urządzenia łączą się z IoT Hubą z flagą **CleanSession** ustawioną na **0** i używają **usługi QoS 1** na potrzeby wymiany komunikatów z usługą IoT Hub.

Gdy urządzenie jest połączone z usługą IoT Hub, zestawy SDK urządzeń udostępniają metody, które umożliwiają urządzeniu wymianę komunikatów z Centrum IoT Hub.

Poniższa tabela zawiera linki do przykładów kodu dla każdego obsługiwanego języka i określa parametr, który ma zostać użyty do nawiązania połączenia z IoT Hub przy użyciu protokołu MQTT.

| Język | Parametr protokołu |
| --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) |azure-iot-device-mqtt |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |IotHubClientProtocol.MQTT |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) |MQTT_Protocol |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) |TransportType.Mqtt |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples) |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrowanie aplikacji urządzenia z usługi AMQP do usługi MQTT

W przypadku korzystania z [zestawów SDK urządzeń](https://github.com/Azure/azure-iot-sdks)przełączanie z używania AMQP do MQTT wymaga zmiany parametru protokołu w inicjacji klienta zgodnie z opisem wcześniej.

W tym celu należy sprawdzić następujące elementy:

* AMQP zwraca błędy dla wielu warunków, podczas gdy MQTT przerywa połączenie. W efekcie logika obsługi wyjątków może wymagać pewnych zmian.

* MQTT nie obsługuje operacji odrzucania podczas otrzymywania [komunikatów z chmury do urządzenia](iot-hub-devguide-messaging.md). Jeśli Twoja aplikacja zaplecza musi odebrać odpowiedź z aplikacji urządzenia, należy rozważyć użycie [metod bezpośrednich](iot-hub-devguide-direct-methods.md).

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Bezpośrednie używanie protokołu MQTT (jako urządzenia)

Jeśli urządzenie nie może użyć zestawów SDK urządzeń, nadal może nawiązać połączenie z punktami końcowymi urządzeń publicznych przy użyciu protokołu MQTT na porcie 8883. W pakiecie **Connect** urządzenie powinno używać następujących wartości:

* Dla pola **ClientId** Użyj elementu **deviceId**.

* W polu **username (nazwa użytkownika** ) `{iothubhostname}/{device_id}/?api-version=2018-06-30`Użyj `{iothubhostname}` elementu, gdzie to pełny rekord CNAME Centrum IoT Hub.

    Jeśli na przykład nazwa Twojego centrum IoT jest **contoso.Azure-Devices.NET** i jeśli nazwa urządzenia to **MyDevice01**, pełne pole **nazwy użytkownika** powinno zawierać:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* W polu **hasło** Użyj tokenu SAS. Format tokenu sygnatury dostępu współdzielonego jest taki sam jak dla protokołów HTTPS i AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Jeśli używasz uwierzytelniania certyfikatu X. 509, hasła tokenu sygnatury dostępu współdzielonego nie są wymagane. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub](iot-hub-security-x509-get-started.md) i postępuj zgodnie z [poniższymi](#tlsssl-configuration)instrukcjami.

  Więcej informacji o sposobach generowania tokenów SAS znajduje się w sekcji Device of [Using IoT Hub tokeny zabezpieczające](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  Podczas testowania można także użyć wieloplatformowych [narzędzi Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) lub narzędzia [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) , aby szybko wygenerować token SAS, który można skopiować i wkleić do własnego kodu:

### <a name="for-azure-iot-tools"></a>Dla narzędzi Azure IoT Tools

1. Rozwiń kartę **urządzenia usługi Azure IoT Hub** w lewym dolnym rogu Visual Studio Code.
  
2. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz polecenie **Generuj token SAS dla urządzenia**.
  
3. Ustaw **czas wygaśnięcia** i naciśnij klawisz ENTER.
  
4. Token sygnatury dostępu współdzielonego został utworzony i skopiowany do Schowka.

### <a name="for-device-explorer"></a>Dla Device Explorer

1. Przejdź do karty **Zarządzanie** w **Device Explorer**.

2. Kliknij pozycję **token SAS** (prawy górny).

3. Na stronie **SASTokenForm**wybierz urządzenie z listy rozwijanej **DeviceID** . Ustaw wartość parametru **TTL**.

4. Kliknij przycisk **Generuj** , aby utworzyć token.

   Wygenerowany token sygnatury dostępu współdzielonego ma następującą strukturę:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Częścią tego tokenu używaną jako pole **hasła** do nawiązywania połączenia przy użyciu MQTT jest:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

W przypadku pakietów MQTT Connect i Disconnect IoT Hub wystawić zdarzenie w kanale **monitorowania operacji** . To zdarzenie zawiera dodatkowe informacje, które mogą pomóc w rozwiązywaniu problemów z łącznością.

Aplikacja urządzenia może określić komunikat w pakiecie **Connect** . Aplikacja urządzenia powinna używać `devices/{device_id}/messages/events/` lub jako `devices/{device_id}/messages/events/{property_bag}` nazwa tematu, aby określić, czy komunikaty będą przekazywane jako komunikat telemetrii. W takim przypadku, jeśli połączenie sieciowe zostało zamknięte, ale pakiet rozłączenia nie został wcześniej odebrany z urządzenia, IoT Hub wyśle komunikat o podanej w pakiecie **Connect** do kanału telemetrii. Kanał telemetrii może być domyślnym punktem końcowym **zdarzeń** lub niestandardowym punktem końcowym zdefiniowanym przez IoT Hub Routing. Komunikat ma właściwość **iothub-MessageType** o wartości, która **zostanie** do niej przypisana.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Bezpośrednie używanie protokołu MQTT (jako modułu)

Nawiązywanie połączenia z IoT Hub za pośrednictwem usługi MQTT za pomocą tożsamości modułu jest podobne do urządzenia (opisanego [powyżej](#using-the-mqtt-protocol-directly-as-a-device)), ale należy użyć następujących opcji:

* Ustaw identyfikator klienta na `{device_id}/{module_id}`.

* W przypadku uwierzytelniania przy użyciu nazwy użytkownika i hasła Ustaw nazwę użytkownika `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` na i użyj tokenu sygnatury dostępu współdzielonego skojarzonego z tożsamością modułu jako hasłem.

* Użyj `devices/{device_id}/modules/{module_id}/messages/events/` jako tematu do publikowania danych telemetrycznych.

* Użycie `devices/{device_id}/modules/{module_id}/messages/events/` jako będzie tematem.

* Tematy dotyczące pobierania i stosowania przędzy są identyczne w przypadku modułów i urządzeń.

* Temat o stanie przędzy jest identyczny w przypadku modułów i urządzeń.

## <a name="tlsssl-configuration"></a>Konfiguracja protokołu TLS/SSL

Aby bezpośrednio korzystać z protokołu MQTT, klient *musi* nawiązać połączenie za pośrednictwem protokołu TLS/SSL. Próba pominięcia tego kroku kończy się niepowodzeniem z błędami połączenia.

Aby można było nawiązać połączenie TLS, może być konieczne pobranie i odwołanie certyfikatu głównego DigiCert Baltimore. Ten certyfikat jest wykorzystywany przez platformę Azure do zabezpieczenia połączenia. Ten certyfikat można znaleźć w repozytorium [Azure-IoT-SDK-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) . Więcej informacji o tych certyfikatach można znaleźć w [witrynie sieci Web DigiCert](https://www.digicert.com/digicert-root-certificates.htm).

Przykładem sposobu implementacji tego przy użyciu wersji języka Python [biblioteki PAHO MQTT](https://pypi.python.org/pypi/paho-mqtt) przez przeszukiwanie podstawy może wyglądać podobnie do poniższego.

Najpierw zainstaluj bibliotekę PAHO z poziomu środowiska wiersza polecenia:

```cmd/sh
pip install paho-mqtt
```

Następnie Zaimplementuj klienta w skrypcie języka Python. Zastąp symbole zastępcze w następujący sposób:

* `<local path to digicert.cer>`jest ścieżką do pliku lokalnego, który zawiera certyfikat główny DigiCert Baltimore. Ten plik można utworzyć, kopiując informacje o certyfikacie z [certyfikatów. c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) w zestawie SDK usługi Azure IoT dla języka c. Dołącz wiersze `-----BEGIN CERTIFICATE-----` `"` i `-----END CERTIFICATE-----`, usuń znaczniki na początku i końcu każdego wiersza i Usuń `\r\n`znaki na końcu każdego wiersza.

* `<device id from device registry>`to identyfikator urządzenia dodanego do centrum IoT Hub.

* `<generated SAS token>`jest tokenem sygnatury dostępu współdzielonego dla urządzenia utworzonego zgodnie z opisem w tym artykule.

* `<iot hub name>`Nazwa Centrum IoT.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Poniżej przedstawiono instrukcje instalacji dotyczące wymagań wstępnych.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

Aby uwierzytelnić się przy użyciu certyfikatu urządzenia, zaktualizuj Powyższy fragment kodu z następującymi zmianami (Zobacz artykuł [jak uzyskać certyfikat certyfikatu X. 509 urzędu certyfikacji](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) , aby przygotować się do uwierzytelniania opartego na certyfikatach):

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Wysyłanie komunikatów z urządzenia do chmury

Po pomyślnym nawiązaniu połączenia urządzenie może wysyłać komunikaty do IoT Hub przy `devices/{device_id}/messages/events/` użyciu `devices/{device_id}/messages/events/{property_bag}` lub jako **nazwy tematu**. `{property_bag}` Element umożliwia urządzeniu wysyłanie komunikatów z dodatkowymi właściwościami w formacie zakodowanym w adresie URL. Przykład:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Ten `{property_bag}` element używa tego samego kodowania co ciągi zapytań w protokole https.

Poniżej znajduje się lista zachowań IoT Hub specyficznych dla implementacji:

* IoT Hub nie obsługuje komunikatów QoS 2. Jeśli aplikacja urządzenia opublikuje komunikat z opcją **QoS 2**, IoT Hub zamyka połączenie sieciowe.

* IoT Hub nie utrwala zachowywanych komunikatów. Jeśli urządzenie wysyła komunikat z flagą **Zachowaj** ustawioną na 1, IoT Hub dodaje do wiadomości Właściwość aplikacji **x-opt-utrzymują** . W takim przypadku zamiast utrwalania komunikatu o zatrzymaniu IoT Hub przekazuje go do aplikacji zaplecza.

* IoT Hub obsługuje tylko jedno aktywne połączenie MQTT dla każdego urządzenia. Każde nowe połączenie MQTT w imieniu tego samego identyfikatora urządzenia powoduje, że IoT Hub porzucić istniejące połączenie.

Aby uzyskać więcej informacji, zobacz [przewodnik dewelopera obsługi komunikatów](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Otrzymywanie komunikatów z chmury do urządzenia

Aby odbierać komunikaty z IoT Hub, urządzenie powinno subskrybować korzystanie `devices/{device_id}/messages/devicebound/#` z programu jako **filtru tematu**. Wielopoziomowy symbol `#` wielowymiarowy w filtrze tematu służy tylko do zezwalania urządzeniu na odbieranie dodatkowych właściwości w nazwie tematu. IoT Hub nie zezwala na użycie `#` symboli wieloznacznych lub `?` do filtrowania tematów podrzędnych. Ponieważ IoT Hub nie jest brokerem wysyłania komunikatów ogólnego przeznaczenia, obsługuje tylko udokumentowane nazwy tematów i filtry tematów.

Urządzenie nie odbiera żadnych komunikatów z IoT Hub, dopóki nie zostanie pomyślnie zasubskrybowana do jego punktu końcowego określonego dla urządzenia reprezentowanego przez `devices/{device_id}/messages/devicebound/#` filtr tematu. Po nawiązaniu subskrypcji urządzenie odbiera wysłane do niego komunikaty z chmury do urządzenia po upływie czasu subskrypcji. Jeśli urządzenie połączy się z flagą **CleanSession** o wartości **0**, subskrypcja jest utrwalana w różnych sesjach. W takim przypadku następnym razem, gdy urządzenie połączy się z **CleanSession 0** odbierze wszelkie zaległe komunikaty wysyłane do niego, gdy zostanie odłączony. Jeśli urządzenie używa flagi **CleanSession** ustawionej na **1** , nie odbiera żadnych komunikatów z IoT Hub, dopóki nie zasubskrybuje on swojego punktu końcowego urządzenia.

IoT Hub dostarcza wiadomości z **nazwą** `devices/{device_id}/messages/devicebound/`tematu lub `devices/{device_id}/messages/devicebound/{property_bag}` w przypadku, gdy są właściwości komunikatów. `{property_bag}`zawiera pary kluczy/wartości kodowane w adresie URL właściwości komunikatów. W zbiorze właściwości są uwzględniane tylko właściwości aplikacji i właściwości systemu użytkownika (takie jak **MessageID** lub **Identyfikator korelacji**). Nazwy właściwości systemu mają prefiks **$** , właściwości aplikacji używają oryginalnej nazwy właściwości bez prefiksu.

Gdy aplikacja urządzenia subskrybuje temat z zasadą **QoS 2**, IoT Hub przyznaje maksymalny poziom jakości usług (QoS) 1 w pakiecie **SUBACK** . Następnie IoT Hub dostarcza komunikaty do urządzenia przy użyciu usługi QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Pobieranie właściwości sznurka urządzenia

Najpierw urządzenie subskrybuje `$iothub/twin/res/#`, aby odebrać odpowiedzi operacji. Następnie wysyła pusty komunikat do tematu `$iothub/twin/GET/?$rid={request id}`z wypełnioną wartością dla **identyfikatora żądania**. Następnie usługa wysyła komunikat odpowiedzi zawierający dane dotyczące sznurka urządzenia w temacie `$iothub/twin/res/{status}/?$rid={request id}`przy użyciu tego samego **identyfikatora żądania** co żądanie.

Identyfikator żądania może być dowolną prawidłową wartością dla wartości właściwości wiadomości, zgodnie z opisem w [przewodniku dewelopera obsługi komunikatów IoT Hub](iot-hub-devguide-messaging.md), a stan jest sprawdzony jako liczba całkowita.

Treść odpowiedzi zawiera sekcję właściwości sznurka urządzenia, jak pokazano w poniższym przykładzie odpowiedzi:

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

Możliwe kody stanu to:

|Stan | Opis |
| ----- | ----------- |
| 204 | Powodzenie (nie jest zwracana żadna zawartość) |
| 429 | Zbyt wiele żądań (z ograniczeniami), zgodnie z [ograniczeniami IoT Hub](iot-hub-devguide-quotas-throttling.md) |
| 5** | Błędy serwera |

Aby uzyskać więcej informacji, zobacz [przewodnik dewelopera urządzenia bliźniaczych reprezentacji](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Aktualizowanie raportowanych właściwości przędzy urządzenia

Aby zaktualizować raportowane właściwości, urządzenie wysyła żądanie IoT Hub za pośrednictwem publikacji na wyznaczoną MQTT tematu. Po przetworzeniu żądania IoT Hub odpowiada na stan powodzenia lub niepowodzenia operacji aktualizacji za pośrednictwem publikacji w innym temacie. Ten temat może być subskrybowany przez urządzenie w celu powiadomienia go o wyniku żądania aktualizacji z przędzą. W celu zaimplementowania tego typu interakcji żądania/odpowiedzi w programie MQTT wykorzystujemy pojęcie identyfikatora żądania (`$rid`) dostarczonego początkowo przez urządzenie w jego żądaniu aktualizacji. Ten identyfikator żądania jest również uwzględniony w odpowiedzi z IoT Hub, aby umożliwić urządzeniu skorelowanie odpowiedzi z konkretnym wcześniejszym żądaniem.

Poniższa sekwencja zawiera opis sposobu aktualizowania przez urządzenie raportowanych właściwości w postaci sznurka urządzenia w IoT Hub:

1. Urządzenie musi najpierw subskrybować `$iothub/twin/res/#` temat, aby otrzymywać odpowiedzi operacji z IoT Hub.

2. Urządzenie wysyła komunikat z aktualizacją przędzy urządzenia do `$iothub/twin/PATCH/properties/reported/?$rid={request id}` tematu. Ten komunikat zawiera wartość **identyfikatora żądania** .

3. Następnie usługa wysyła komunikat odpowiedzi zawierający nową wartość ETag dla raportowanych kolekcji właściwości w temacie `$iothub/twin/res/{status}/?$rid={request id}`. Ten komunikat odpowiedzi używa tego samego **identyfikatora żądania** co żądanie.

Treść komunikatu żądania zawiera dokument JSON, który zawiera nowe wartości raportowanych właściwości. Każdy element członkowski w dokumencie JSON aktualizuje lub dodaje odpowiadający mu element członkowski w dokumencie przędzy urządzenia. Element członkowski, który `null`jest ustawiony na, powoduje usunięcie elementu członkowskiego z zawierającego go obiektu. Przykład:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Możliwe kody stanu to:

|Stan | Opis |
| ----- | ----------- |
| 200 | Powodzenie |
| 400 | Nieprawidłowe żądanie. Źle sformułowany kod JSON |
| 429 | Zbyt wiele żądań (z ograniczeniami), zgodnie z [ograniczeniami IoT Hub](iot-hub-devguide-quotas-throttling.md) |
| 5** | Błędy serwera |

Poniższy fragment kodu w języku Python pokazuje, że raport o raportowanych właściwościach jest przetwarzany przez MQTT (przy użyciu PAHO MQTT Client):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Po pomyślnym przekroczeniu przez nią powyższej operacji aktualizacji właściwości, komunikat publikacji z IoT Hub będzie `$iothub/twin/res/204/?$rid=1&$version=6`miał następujący `204` temat:, gdzie jest kodem stanu `$rid=1` wskazującym powodzenie, odpowiada identyfikatorowi żądania dostarczone przez urządzenie w kodzie i `$version` odpowiada wersji sekcji zgłoszonych właściwości urządzenia bliźniaczych reprezentacji po aktualizacji.

Aby uzyskać więcej informacji, zobacz [przewodnik dewelopera urządzenia bliźniaczych reprezentacji](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>Otrzymywanie powiadomień o aktualizacji żądanych właściwości

Gdy urządzenie jest połączone, IoT Hub wysyła powiadomienia do tematu `$iothub/twin/PATCH/properties/desired/?$version={new version}`, który zawiera zawartość aktualizacji wykonywanej przez zaplecze rozwiązania. Przykład:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Jak w przypadku aktualizacji właściwości `null` , wartości oznaczają, że element członkowski obiektu JSON jest usuwany. Należy również pamiętać, `$version` że wskazuje nową wersję odpowiedniej sekcji Właściwości sznurka.

> [!IMPORTANT]
> IoT Hub generuje powiadomienia o zmianach tylko wtedy, gdy urządzenia są połączone. Upewnij się, że wdrożono [przepływ ponownego połączenia urządzenia](iot-hub-devguide-device-twins.md#device-reconnection-flow) , aby zachować odpowiednie właściwości zsynchronizowane między IoT Hub a aplikacją urządzenia.

Aby uzyskać więcej informacji, zobacz [przewodnik dewelopera urządzenia bliźniaczych reprezentacji](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Odpowiedz na metodę bezpośrednią

Najpierw należy zasubskrybować `$iothub/methods/POST/#`urządzenie. IoT Hub wysyła żądania metod do tematu `$iothub/methods/POST/{method name}/?$rid={request id}`z prawidłowym elementem JSON lub pustą treścią.

Aby można było odpowiedzieć, urządzenie wysyła do tematu `$iothub/methods/res/{status}/?$rid={request id}`komunikat z prawidłowym kodem JSON lub pustą treścią. W tym komunikacie **Identyfikator żądania** musi być zgodny z jednym z komunikatów żądania, a **stan** musi być liczbą całkowitą.

Aby uzyskać więcej informacji, zobacz [przewodnik dewelopera metody bezpośredniej](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

W związku z tym jeśli trzeba będzie dostosować zachowanie protokołu MQTT po stronie chmury, należy zapoznać się z [bramą protokołu Azure IoT](iot-hub-protocol-gateway.md). To oprogramowanie umożliwia wdrożenie niestandardowej bramy protokołu o wysokiej wydajności, która jest bezpośrednio oparta na IoT Hub. Brama protokołu Azure IoT umożliwia dostosowanie protokołu urządzenia w celu uwzględnienia wdrożeń brownfield MQTT lub innych niestandardowych protokołów. To podejście wymaga jednak uruchomienia i obsługi niestandardowej bramy protokołu.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat protokołu MQTT można znaleźć w [dokumentacji MQTT](https://mqtt.org/documentation).

Aby dowiedzieć się więcej o planowaniu wdrożenia IoT Hub, zobacz:

* [Certyfikat platformy Microsoft Azure dla IoT — wykaz urządzeń](https://catalog.azureiotsolutions.com/)
* [Obsługa dodatkowych protokołów](iot-hub-protocol-gateway.md)
* [Porównaj z Event Hubs](iot-hub-compare-event-hubs.md)
* [Skalowanie, HA i DR](iot-hub-scaling.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Przewodnik dla deweloperów IoT Hub](iot-hub-devguide.md)
* [Wdrażanie AI na urządzeniach brzegowych za pomocą Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
