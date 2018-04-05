---
title: Informacje pomocy technicznej usługi Azure IoT Hub MQTT | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — Obsługa urządzeń nawiązujących połączenie z punktu końcowego Centrum IoT uwzględniającym urządzenia przy użyciu protokołu MQTT. Zawiera informacje o wbudowanych MQTT obsługuje urządzenia Azure IoT zestawów SDK.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: 1d71c27c-b466-4a40-b95b-d6550cf85144
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 23f98d4e9f711496480d5e02b4d5b23cd8abab0c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Komunikować się z Centrum IoT przy użyciu protokołu MQTT

Centrum IoT umożliwia urządzeniom komunikować się z punkty końcowe Centrum IoT urządzenia przy użyciu:

* [MQTT v3.1.1] [ lnk-mqtt-org] na porcie 8883
* V3.1.1 MQTT za pośrednictwem protokołu WebSocket na porcie 443.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Cała komunikacja urządzenie z Centrum IoT musi być zabezpieczona za pomocą protokołu TLS/SSL. W związku z tym Centrum IoT nie obsługuje niezabezpieczonego połączenia za pośrednictwem portu 1883.

## <a name="connecting-to-iot-hub"></a>Łączenie z Centrum IoT

Urządzenie może korzystać z protokołu MQTT nawiązywania połączenia z Centrum IoT przy użyciu:

* Bibliotek w [Azure IoT SDK][lnk-device-sdks].
* Lub bezpośrednio za pomocą protokołu MQTT.

## <a name="using-the-device-sdks"></a>Za pomocą urządzenia zestawów SDK

[Zestawy SDK urządzenia] [ lnk-device-sdks] obsługujące protokół MQTT są dostępne dla języka Java, Node.js, C, C# i Python. Zestawy SDK urządzenia umożliwia standardowe parametry połączenia Centrum IoT nawiązać połączenie z Centrum IoT. Do korzystania z protokołu MQTT, parametr Protokół klienta musi mieć ustawioną **MQTT**. Domyślnie urządzenia zestawów SDK połączyć się z Centrum IoT z **CleanSession** ustawić flagi **0** i użyj **QoS 1** w wymianie wiadomości z Centrum IoT.

Gdy urządzenie jest podłączone do Centrum IoT, urządzenia zestawy SDK zapewniają metod, które umożliwiają urządzeniu do wymiany wiadomości z Centrum IoT.

Poniższa tabela zawiera linki do przykłady kodu dla każdego z obsługiwanych języków oraz określa parametr, aby nawiązać połączenie przy użyciu protokołu MQTT Centrum IoT.

| Język | Parametr protokołu |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrowanie aplikacji urządzenia z protokołu AMQP do MQTT

Jeśli używasz [urządzenia zestawów SDK][lnk-device-sdks], przełączania przy użyciu protokołu AMQP do MQTT konieczna jest zmiana parametru protokołu podczas inicjowania klienta, jak wspomniano wcześniej.

Po tej czynności upewnij się sprawdzić następujące elementy:

* Protokół AMQP zwraca błędy wiele warunków, gdy MQTT przerywa połączenie. W związku z tym wyjątku obsługi logiki ewentualnej konieczności wprowadzenia zmian.
* Nie obsługuje MQTT *Odrzuć* operacji podczas odbierania [wiadomości chmury do urządzenia][lnk-messaging]. Jeśli Twoja aplikacja zaplecza powinna można odebrać odpowiedzi od aplikacji urządzenia, należy rozważyć użycie [bezpośrednie metody][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>Przy użyciu protokołu MQTT bezpośrednio

Jeśli urządzenia nie można używać urządzeń zestawów SDK, nadal można połączyć do urządzeń publicznych punktów końcowych przy użyciu protokołu MQTT na porcie 8883. W **CONNECT** pakietów urządzenia należy używać następujących wartości:

* Aby uzyskać **ClientId** użyj **deviceId**.

* Dla **Username** użyj `{iothubhostname}/{device_id}/api-version=2016-11-14`, gdzie `{iothubhostname}` jest pełna CName z Centrum IoT.

    Na przykład jeśli nazwą Centrum IoT jest **contoso.azure devices.net** i jeśli nazwa urządzenia jest **MyDevice01**, pełny **Username** pole powinno zawierać:

    `contoso.azure-devices.net/MyDevice01/api-version=2016-11-14`

* Aby uzyskać **hasło** Użyj tokenu sygnatury dostępu Współdzielonego. Format tokenu sygnatury dostępu Współdzielonego jest taka sama jak w przypadku protokołów HTTPS i protokołu AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Jeśli używane jest uwierzytelnianie certyfikatu X.509, hasła tokenu sygnatury dostępu Współdzielonego nie są wymagane. Aby uzyskać więcej informacji, zobacz [Konfigurowanie X.509 zabezpieczeń w Centrum IoT Azure][lnk-x509]

  Aby uzyskać więcej informacji na temat generowania tokeny sygnatury dostępu Współdzielonego, zobacz sekcję urządzenia [tokeny zabezpieczające przy użyciu Centrum IoT][lnk-sas-tokens].

  Podczas testowania, można również użyć [explorer urządzenia] [ lnk-device-explorer] narzędzia do szybkiego generowania tokenu sygnatury dostępu Współdzielonego, który możesz skopiować i wkleić w swoim własnym kodem:

  1. Przejdź do **zarządzania** karcie **Explorer urządzenia**.
  2. Kliknij przycisk **tokenu sygnatury dostępu Współdzielonego** (z góry po prawej).
  3. Na **SASTokenForm**, wybierz swoje urządzenie w **DeviceID** listy rozwijanej. Ustaw użytkownika **TTL**.
  4. Kliknij przycisk **Generuj** utworzyć token.

     Token sygnatury dostępu Współdzielonego, który jest generowany ma następującą strukturę:

     `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

     Część ten token do użycia jako **hasło** pole nawiązywanie połączeń za pomocą MQTT jest:

     `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

MQTT łączyć i Odłącz pakietów, Centrum IoT problemy zdarzenia na **operacje monitorowania** kanału. To zdarzenie zawiera dodatkowe informacje, które pomaga rozwiązywać problemy z połączeniem.

Można określić aplikacji urządzenia **będzie** komunikatów w **CONNECT** pakietów. Należy użyć aplikacji urządzenia `devices/{device_id}/messages/events/{property_bag}` lub `devices/{device_id}/messages/events/{property_bag}` jako **będzie** nazwę tematu, aby zdefiniować **będzie** mają być przekazywane jako wiadomość telemetrii wiadomości. W tym przypadku jeśli połączenie sieciowe jest zamknięty, ale **ROZŁĄCZENIA** pakietów nie otrzymano wcześniej z urządzenia, a następnie wysyła Centrum IoT **będzie** wiadomość dostarczona w **CONNECT** pakietów w kanale danych telemetrycznych. Kanał danych telemetrycznych może być albo domyślnie **zdarzenia** punktu końcowego lub punkt końcowy niestandardowe zdefiniowane przez Centrum IoT routingu. Komunikat ma **MessageType Centrum iothub** właściwość z wartością **będzie** przypisane do niej.

### <a name="tlsssl-configuration"></a>Konfiguracja protokołu TLS/SSL

Aby użyć MQTT protokołu bezpośrednio, klient *musi* nawiązywać połączenia za pomocą protokołu TLS/SSL. Próby, Pomiń ten krok się niepowodzeniem z błędami połączenia.

Aby możliwe było nawiązanie połączenia TLS, konieczne może być pobieranie i odwołania certyfikatu głównego Baltimore DigiCert. Ten certyfikat jest ten, który używa Azure do zabezpieczenia połączenia. Możesz znaleźć tego certyfikatu w [Azure-iot-sdk-c] [ lnk-sdk-c-certs] repozytorium. Więcej informacji na temat tych certyfikatów można znaleźć w [witryny sieci Web firmy Digicert][lnk-digicert-root-certs].

Przykładem wdrożyć za pomocą wersji języka Python [biblioteki Paho MQTT] [ lnk-paho] przez Eclipse Foundation może wyglądać następująco.

Najpierw zainstaluj biblioteki Paho ze środowiska wiersza polecenia:

```cmd/sh
pip install paho-mqtt
```

Następnie należy zaimplementować klienta skrypt w języku Python. Zastąp symbole zastępcze w następujący sposób:

* `<local path to digicert.cer>` to ścieżka do pliku lokalnego, który zawiera certyfikat główny Baltimore DigiCert. Można utworzyć ten plik, kopiując informacji o certyfikacie z [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) w zestawie SDK IoT Azure dla C. obejmują wiersze `-----BEGIN CERTIFICATE-----` i `-----END CERTIFICATE-----`, Usuń `"` znaki na początku i na końcu każdego wiersza i Usuń `\r\n` znaki na końcu każdej linii.
* `<device id from device registry>` jest to identyfikator urządzenia, które można dodać do Centrum IoT.
* `<generated SAS token>` jest token sygnatury dostępu Współdzielonego dla urządzenia utworzone, jak opisano wcześniej w tym artykule.
* `<iot hub name>` Nazwa centrum IoT.

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

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" + device_id, password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

### <a name="sending-device-to-cloud-messages"></a>Wysyłanie wiadomości urządzenia do chmury

Po wprowadzeniu udane połączenie, urządzenie może wysyłać wiadomości do Centrum IoT przy użyciu `devices/{device_id}/messages/events/` lub `devices/{device_id}/messages/events/{property_bag}` jako **nazwa tematu**. `{property_bag}` Element umożliwia urządzeniu wysyłać wiadomości z dodatkowych właściwości w formacie zakodowane w adresie url. Na przykład:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> To `{property_bag}` element używa tego samego kodu dla ciągów zapytań w protokole HTTPS.

Poniżej przedstawiono listę zachowania konkretnej implementacji Centrum IoT:

* Centrum IoT nie obsługuje komunikaty QoS 2. Jeśli aplikacja urządzenia publikuje komunikat z **QoS 2**, Centrum IoT zamyka połączenie sieciowe.
* Centrum IoT nie jest trwały Zachowaj wiadomości. Jeśli urządzenie wysyła wiadomość z **Zachowaj** Flaga ustawiona na 1, dodaje Centrum IoT **x-opt — Zachowaj** właściwości aplikacji do wiadomości. W takim przypadku zamiast utrwalanie komunikat Zachowaj, Centrum IoT przekazuje ją do wewnętrznej bazy danych aplikacji.
* Centrum IoT obsługuje tylko jedno aktywne połączenie MQTT na urządzenie. Centrum IoT można usunąć istniejące połączenie powoduje, że wszystkie nowe połączenie MQTT imieniu ten sam identyfikator urządzenia.

Aby uzyskać więcej informacji, zobacz [wiadomości przewodnik dewelopera][lnk-messaging].

### <a name="receiving-cloud-to-device-messages"></a>Odbieranie komunikatów chmury do urządzenia

Do odbierania wiadomości z Centrum IoT, urządzenie powinno Subskrybuj przy użyciu `devices/{device_id}/messages/devicebound/#` jako **filtru tematu**. Symbol wieloznaczny wielopoziomowe `#` w filtrze tematu służą tylko do Zezwalaj urządzeniu na otrzymywanie dodatkowe właściwości Nazwa tematu. Centrum IoT nie zezwala na użycie `#` lub `?` symboli wieloznacznych dla filtrowanie tematy podrzędne. Ponieważ Centrum IoT nie jest typu ogólnego przeznaczenia pub-sub brokera obsługi komunikatów, jego obsługuje tylko nazwy udokumentowane tematów i filtry tematu.

Urządzenie nie odbiera komunikaty z Centrum IoT, aż pomyślnie subskrybowanych do punktu końcowego specyficzne dla urządzenia, reprezentowany przez `devices/{device_id}/messages/devicebound/#` filtr tematu. Po ustanowieniu subskrypcji urządzenie odbiera wiadomości chmury do urządzenia, które zostały wysłane do niej po godzinie subskrypcji. Jeśli urządzenie łączy się z **CleanSession** ustawić flagi **0**, subskrypcja jest trwała dla różnych sesji. W takim przypadku przy następnym urządzenie łączy się z usługą **CleanSession 0** odbiera komunikaty oczekujące wysyłane do niego odłączeniu. Jeśli urządzenie korzysta **CleanSession** ustawić flagi **1** , nie otrzyma komunikaty z Centrum IoT dopóki subskrybuje punktu końcowego urządzenia.

Centrum IoT dostarcza wiadomości z **nazwa tematu** `devices/{device_id}/messages/devicebound/`, lub `devices/{device_id}/messages/devicebound/{property_bag}` przypadku właściwości wiadomości. `{property_bag}` zawiera pary klucz wartość zakodowane w adresie url właściwości komunikatu. Tylko właściwości aplikacji i użytkownika można ustawić właściwości (takie jak **messageId** lub **correlationId**) znajdują się w zbiorze właściwości. Nazwy właściwości systemu posiada przypisany odpowiedni prefiks **$**, właściwości aplikacji za pomocą oryginalna nazwa właściwości nie ma prefiksu.

Gdy aplikacja urządzenia subskrybuje temat z **QoS 2**, Centrum IoT przyznaje maksymalny poziom QoS 1 w **SUBACK** pakietów. Po tym Centrum IoT dostarcza wiadomości na urządzenie, używając QoS 1.

### <a name="retrieving-a-device-twins-properties"></a>Podczas pobierania właściwości dwie urządzenia

Po pierwsze urządzenie subskrybuje `$iothub/twin/res/#`, aby otrzymywać odpowiedzi operacji. Następnie wysyła wiadomość pusty do tematu `$iothub/twin/GET/?$rid={request id}`, z wypełnione wartością **identyfikator żądania**. Usługa wysyła następnie odpowiedź urządzenia dwie dane na temat `$iothub/twin/res/{status}/?$rid={request id}`, korzystającej z tego samego **identyfikator żądania** jako żądania.

Identyfikator żądania może być wszystkie prawidłowe wartości wartość właściwości komunikatu zgodnie [Centrum IoT wiadomości przewodnik dewelopera][lnk-messaging], i stan zostanie zweryfikowany jako liczba całkowita.

Treść odpowiedzi zawiera sekcja właściwości dwie urządzenia. Poniższy fragment kodu przedstawia na przykład treść wpisu rejestru tożsamości ograniczone do elementu "właściwości":

```json
{
    "properties": {
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
}
```

Kody stanu możliwe są następujące:

|Stan | Opis |
| ----- | ----------- |
| 200 | Powodzenie |
| 429 | Za dużo żądań (ograniczony), jak na [ograniczania Centrum IoT][lnk-quotas] |
| 5** | Błędy serwera |

Aby uzyskać więcej informacji, zobacz [urządzenia twins przewodnik dewelopera][lnk-devguide-twin].

### <a name="update-device-twins-reported-properties"></a>Aktualizacja właściwości zgłoszone dwie urządzenia

Następująca sekwencja opisano, jak urządzenia aktualizuje właściwości zgłoszone w dwie urządzenie w Centrum IoT:

1. Urządzenie, musi najpierw zasubskrybować `$iothub/twin/res/#` tematu, aby otrzymywać odpowiedzi operacji centrum IoT.

1. Urządzenie wysyła komunikat, który zawiera aktualizację dwie urządzenia do `$iothub/twin/PATCH/properties/reported/?$rid={request id}` tematu. Ten komunikat zawiera **identyfikator żądania** wartość.

1. Usługa wysyła następnie komunikat odpowiedzi, która zawiera nową wartość ETag kolekcji właściwości zgłoszone w temacie `$iothub/twin/res/{status}/?$rid={request id}`. Ten komunikat odpowiedzi wykorzystuje takie same **identyfikator żądania** jako żądania.

Treść żądania zawiera dokument JSON, który zawiera nowe wartości dla właściwości zgłoszony. Każdy element członkowski w dokumencie JSON aktualizacji lub Dodaj odpowiadającego mu członka w dokumencie dwie urządzenia. Zestaw elementów członkowskich `null`, usuwa element członkowski z zawierającego go obiektu. Na przykład:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Kody stanu możliwe są następujące:

|Stan | Opis |
| ----- | ----------- |
| 200 | Powodzenie |
| 400 | Nieprawidłowe żądanie. Nieprawidłowo sformatowany kod JSON |
| 429 | Za dużo żądań (ograniczony), jak na [ograniczania Centrum IoT][lnk-quotas] |
| 5** | Błędy serwera |

Aby uzyskać więcej informacji, zobacz [urządzenia twins przewodnik dewelopera][lnk-devguide-twin].

### <a name="receiving-desired-properties-update-notifications"></a>Odbieranie powiadomienia o aktualizacji odpowiednie właściwości

Gdy urządzenie jest podłączone, Centrum IoT wysyła powiadomienia do tematu `$iothub/twin/PATCH/properties/desired/?$version={new version}`, które zawierają zawartość aktualizacji z zastosowaniem zaplecza rozwiązania. Na przykład:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null
}
```

Podobnie jak w przypadku aktualizacji właściwości `null` wartości oznacza, że element członkowski obiektu JSON jest usuwany.

> [!IMPORTANT]
> Centrum IoT generuje powiadomienia o zmianie tylko wtedy, gdy urządzenia są połączone. Upewnij się, że wdrożenie [przepływu ponowne łączenie urządzenia] [ lnk-devguide-twin-reconnection] Aby zachować odpowiednie właściwości, które są synchronizowane między centrum IoT i aplikacjami urządzenia.

Aby uzyskać więcej informacji, zobacz [urządzenia twins przewodnik dewelopera][lnk-devguide-twin].

### <a name="respond-to-a-direct-method"></a>Odpowiadanie na bezpośrednie — metoda

Po pierwsze urządzenie ma subskrybować `$iothub/methods/POST/#`. Centrum IoT wysyła żądania metody do tematu `$iothub/methods/POST/{method name}/?$rid={request id}`, poprawne dane JSON lub pustej treści.

Aby odpowiedzieć, urządzenie wysyła wiadomość o poprawne dane JSON lub pustej treści do tematu `$iothub/methods/res/{status}/?$rid={request id}`. W tym komunikacie **identyfikator żądania** musi odpowiadać nazwie w komunikacie żądania i **stan** musi być liczbą całkowitą.

Aby uzyskać więcej informacji, zobacz [bezpośrednie przewodnik dewelopera metody][lnk-methods].

### <a name="additional-considerations"></a>Dodatkowe zagadnienia

Jako ostatecznego wchodzi w grę, jeśli musisz dostosować zachowanie protokołu MQTT po stronie chmury, należy przejrzeć [brama protokołu Azure IoT][lnk-azure-protocol-gateway]. To oprogramowanie umożliwia wdrażanie bramy protokołu niestandardowego wysokiej wydajności tej współpracuje bezpośrednio z Centrum IoT. Brama protokołu Azure IoT umożliwia dostosowanie protokołu urządzenia do uwzględnienia wdrożeń MQTT brownfield lub innymi protokołami niestandardowych. Takie podejście wymaga jednak uruchamiania i działać brama protokołu niestandardowego.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat protokołu MQTT, zobacz [dokumentacji MQTT][lnk-mqtt-docs].

Aby dowiedzieć się więcej na temat planowania wdrożenia Centrum IoT, zobacz:

* [Azure certyfikowane dla katalogu urządzenia IoT][lnk-devices]
* [Obsługa dodatkowych protokołów.][lnk-protocols]
* [Porównaj z usługi Event Hubs][lnk-compare]
* [Skalowanie, wysokiej dostępności i odzyskiwania po awarii][lnk-scaling]

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Przewodnik dewelopera Centrum IoT][lnk-devguide]
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-sas-tokens]: iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-x509]: iot-hub-security-x509-get-started.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md

[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-twin-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-devguide-twin]: iot-hub-devguide-device-twins.md
[lnk-sdk-c-certs]: https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c
[lnk-digicert-root-certs]: https://www.digicert.com/digicert-root-certificates.htm
[lnk-paho]: https://pypi.python.org/pypi/paho-mqtt
