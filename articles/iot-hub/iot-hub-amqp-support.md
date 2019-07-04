---
title: Omówienie pomocy technicznej usługi Azure IoT Hub AMQP | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — Obsługa urządzeń nawiązujących połączenie z Centrum IoT Hub przeznaczonych dla urządzeń i przeznaczonych dla usługi punktów końcowych, za pomocą protokołu AMQP. Zawiera informacje o wbudowanych AMQP obsługi w zestawy SDK urządzeń Azure IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.openlocfilehash: e0c7b6aa9745beaf7a7d336e8308d12348bb274b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432610"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Komunikować się z Centrum IoT hub przy użyciu protokołu AMQP

Usługa Azure IoT Hub obsługuje [OASIS zaawansowane komunikat Queuing Protocol (AMQP) w wersji 1.0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) dostarczać różne funkcje za pośrednictwem punktów końcowych przeznaczonych dla urządzeń i przeznaczonych dla usługi. W tym dokumencie opisano korzystanie z protokołu AMQP klientów do łączenia z usługą IoT hub przy użyciu funkcji usługi IoT Hub.

## <a name="service-client"></a>Klient usługi

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Łączenie i Uwierzytelnij się do usługi IoT hub (klient usługi)

Aby połączyć się z Centrum IoT hub przy użyciu protokołu AMQP, klient może używać [zabezpieczenia oparte na oświadczeniach (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) lub [uwierzytelnianie proste uwierzytelnianie i warstwy zabezpieczeń (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Wymagane dla klienta usługi są następujące informacje:

| Informacje | Wartość |
|-------------|--------------|
| Hosta Centrum IoT | `<iot-hub-name>.azure-devices.net` |
| Nazwa klucza | `service` |
| Klucz dostępu | Klucz podstawowy lub pomocniczy, który jest skojarzony z usługą |
| Sygnatura dostępu współdzielonego | Sygnatury dostępu współdzielonego krótkotrwałe w następującym formacie: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Aby uzyskać kod do generowania podpisu, zobacz [kontrolować dostęp do usługi IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

Poniższy kod używa fragment [uAMQP biblioteki w języku Python](https://github.com/Azure/azure-uamqp-python) połączyć się z Centrum IoT hub za pomocą linku nadawcy.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: 
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Wywoływanie komunikatów z chmury do urządzeń (klient usługi)

Aby dowiedzieć się o wymianie wiadomości chmury do urządzeń, między usługą i Centrum IoT hub i między urządzeniem i Centrum IoT hub, zobacz [wysyłanie komunikatów z chmury do urządzeń z usługi IoT hub](iot-hub-devguide-messages-c2d.md). Usługa klienta używa dwóch łącza do wysyłanie komunikatów oraz odbieranie opinii na temat wcześniej wysłane wiadomości z urządzeń, zgodnie z opisem w poniższej tabeli:

| Utworzone przez | Typ łącza | Ścieżka łącza | Opis |
|------------|-----------|-----------|-------------|
| Usługa | Link nadawcy | `/messages/devicebound` | Komunikatów z chmury do urządzeń, które są przeznaczone dla urządzeń są wysyłane do tego linku przez usługę. Komunikaty wysyłane za pośrednictwem tego łącza mają swoje `To` właściwość, Ustaw ścieżkę łącza odbiornika na urządzeniu docelowym, `/devices/<deviceID>/messages/devicebound`. |
| Usługa | Link odbiorcy | `/messages/serviceBound/feedback` | Uzupełnianie, odrzucenie i porzucania komunikatów zwrotnych, które pochodzą z urządzeń, które otrzymał ten Link przez usługę. Aby uzyskać więcej informacji na temat komunikatów zwrotnych, zobacz [wysyłanie komunikatów z chmury do urządzeń z usługi IoT hub](./iot-hub-devguide-messages-c2d.md#message-feedback). |

Poniższy fragment kodu przedstawia sposób utworzenia komunikatu chmura urządzenie i wyślij go do urządzenia za pomocą [uAMQP biblioteki w języku Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Aby otrzymać opinię, klient usługi tworzy łącze odbiorcy. Poniższy fragment kodu przedstawia sposób tworzenia łącza za pomocą [uAMQP biblioteki w języku Python](https://github.com/Azure/azure-uamqp-python):

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
  print('received a message')
  # Check content_type in message property to identify feedback messages coming from device
  if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
    msg_body_raw = msg.get_data()
    msg_body_str = ''.join(msg_body_raw)
    msg_body = json.loads(msg_body_str)
    print(json.dumps(msg_body, indent=2))
    print('******************')
    for feedback in msg_body:
      print('feedback received')
      print('\tstatusCode: ' + str(feedback['statusCode']))
      print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
      print('\tdeviceId: ' + str(feedback['deviceId']))
      print
  else:
    print('unknown message:', msg.properties.content_type)
```

Jak pokazano w poprzednim kodzie, chmury do urządzenia wiadomość ma typ zawartości *application/vnd.microsoft.iothub.feedback.json*. Wywnioskowania stan dostarczenia oryginalnej wiadomości, można użyć właściwości w treść JSON:

* Klucz `statusCode` opinii jednostka ma jeden z następujących wartości: *Powodzenie*, *wygasła*, *DeliveryCountExceeded*, *odrzucone*, lub *przeczyścić*.

* Klucz `deviceId` opinii treść zawiera identyfikator urządzenia docelowego.

* Klucz `originalMessageId` opinii treść ma identyfikator oryginalnego komunikatu chmury do urządzenia, która została wysłana przez usługę. Ten stan dostarczenia służy do skorelowania opinię, aby komunikaty z chmury do urządzenia.

### <a name="receive-telemetry-messages-service-client"></a>Odbieranie danych telemetrycznych komunikatów (klient usługi)

Domyślnie usługi IoT hub przechowuje komunikaty telemetryczne pozyskiwane urządzenia w Centrum zdarzeń wbudowanej. Klienta usługi można użyć protokołu AMQP do odbierania przechowywanych zdarzeń.

W tym celu klienta usługi najpierw musi nawiązać połączenie z punktu końcowego Centrum IoT i odbieranie adresu przekierowania do wbudowanych event hubs. Klient usługi następnie używa podany adres połączyć się z Centrum zdarzeń wbudowanej.

W każdym kroku klient musi przedstawić następujących rodzajów informacji:

* Prawidłowe poświadczenia (token sygnatury dostępu współdzielonego usługi).

* Ścieżka prawidłowo sformatowaną partycję grupy konsumentów, który zamierza pobierać komunikaty z. Identyfikator grupy i partycja danego konsumenta, ścieżka ma następujący format: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (domyślna grupa odbiorców stanowi `$Default`).

* Opcjonalny predykat filtrowania wyznaczyć punkt początkowy w partycji. Ten predykat może być w postaci sygnatury czasowej numer, offset lub umieszczonych w kolejce sekwencji.

Poniższy kod używa fragment [uAMQP biblioteki w języku Python](https://github.com/Azure/azure-uamqp-python) aby zademonstrować powyższych kroków:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI
def set_endpoint_filter(uri, endpoint_filter=''):
  source_uri = uamqp.address.Source(uri)
  source_uri.set_filter(endpoint_filter)
  return source_uri

receive_client = uamqp.ReceiveClient(set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
  batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
  # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
  receive_client.close()

  sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(redirect.address, policy_name, access_key)
  receive_client = uamqp.ReceiveClient(set_endpoint_filter(redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
  print('*** received a message ***')
  print(''.join(msg.get_data()))
  print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
  print('\t: ' + str(msg.annotations['x-opt-offset']))
  print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Identyfikatora danego urządzenia usługi IoT hub używa wartości skrótu Identyfikatora urządzenia do określenia partycji do przechowywania jego komunikatów. Poprzednim fragmencie kodu pokazano, jak zdarzenia są odbierane z jednej takiej partycji. Należy jednak zauważyć, że Typowa aplikacja często musi pobierać zdarzenia, które są przechowywane w wszystkich partycji Centrum zdarzeń.

## <a name="device-client"></a>Klient urządzenia

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Łączenie i Uwierzytelnij się do usługi IoT hub (od klienta urządzenia)

Aby połączyć się z Centrum IoT hub przy użyciu protokołu AMQP, urządzenie może używać [zabezpieczeń (CBS) na oświadczeniach](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) lub [proste uwierzytelnianie i warstwy zabezpieczeń (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) uwierzytelniania.

Wymagane dla klienta urządzenia są następujące informacje:

| Informacje | Wartość |
|-------------|--------------|
| Hosta Centrum IoT | `<iot-hub-name>.azure-devices.net` |
| Klucz dostępu | Klucz podstawowy lub pomocniczy, który jest skojarzony z urządzeniem |
| Sygnatura dostępu współdzielonego | Sygnatury dostępu współdzielonego krótkotrwałe w następującym formacie: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Aby uzyskać kod do generowania podpisu, zobacz [kontrolować dostęp do usługi IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

Poniższy kod używa fragment [uAMQP biblioteki w języku Python](https://github.com/Azure/azure-uamqp-python) połączyć się z Centrum IoT hub za pomocą linku nadawcy.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here: 
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(hostname=hostname, device_id=device_id), access_key, None)

operation = '<operation-link-name>' # e.g., '/devices/{device_id}/messages/devicebound'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

Następujące ścieżki łącza są obsługiwane jako operacje dotyczące urządzenia:

| Utworzone przez | Typ łącza | Ścieżka łącza | Opis |
|------------|-----------|-----------|-------------|
| Urządzenia | Link odbiorcy | `/devices/<deviceID>/messages/devicebound` | Komunikatów z chmury do urządzeń, które są przeznaczone dla urządzeń są odbierane na tego linku przez poszczególne urządzenia docelowego. |
| Urządzenia | Link nadawcy | `/devices/<deviceID>messages/events` | Komunikaty z urządzenia do chmury, wysyłanych z urządzenia są wysyłane za pośrednictwem tego łącza. |
| Urządzenia | Link nadawcy | `/messages/serviceBound/feedback` | Opinia komunikatów z chmury do urządzenia wysyłane do usługi za pośrednictwem tego łącza przez urządzenia. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Odbiera polecenia z chmury do urządzenia (urządzenia klienta)

Polecenia chmury do urządzenia, które są wysyłane do urządzenia dostarczania `/devices/<deviceID>/messages/devicebound` łącza. Urządzenia, aby otrzymywać te wiadomości w partiach oraz używać ładunek danych komunikatu, właściwości wiadomości, adnotacje lub właściwości aplikacji w komunikacie zgodnie z potrzebami.

Poniższy kod używa fragment [uAMQP biblioteki w języku Python](https://github.com/Azure/azure-uamqp-python)) do odbierania komunikatów z chmury do urządzeń za pomocą urządzenia.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
  batch = receive_client.receive_message_batch(max_batch_size=5)
  for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))

    # Property 'to' is set to: '/devices/device1/messages/devicebound',
    print('\tto:                     ' + str(msg.properties.to))

    # Property 'message_id' is set to value provided by the service
    print('\tmessage_id:             ' + str(msg.properties.message_id))

    # Other properties are present if they were provided by the service
    print('\tcreation_time:          ' + str(msg.properties.creation_time))
    print('\tcorrelation_id:         ' + str(msg.properties.correlation_id))
    print('\tcontent_type:           ' + str(msg.properties.content_type))
    print('\treply_to_group_id:      ' + str(msg.properties.reply_to_group_id))
    print('\tsubject:                ' + str(msg.properties.subject))
    print('\tuser_id:                ' + str(msg.properties.user_id))
    print('\tgroup_sequence:         ' + str(msg.properties.group_sequence))
    print('\tcontent_encoding:       ' + str(msg.properties.content_encoding))
    print('\treply_to:               ' + str(msg.properties.reply_to))
    print('\tabsolute_expiry_time:   ' + str(msg.properties.absolute_expiry_time))
    print('\tgroup_id:               ' + str(msg.properties.group_id))

    # Message sequence number in the built-in event hub
    print('\tx-opt-sequence-number:  ' + str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Wyślij komunikaty telemetryczne (klient urządzenia)

Możesz również wysłać komunikaty telemetryczne z urządzenia, za pomocą protokołu AMQP. Urządzenia, opcjonalnie możesz podać słownika właściwości aplikacji lub komunikatu różne właściwości, takie jak identyfikator komunikatu.

Poniższy kod używa fragment [uAMQP biblioteki w języku Python](https://github.com/Azure/azure-uamqp-python) wysyłać komunikaty urządzenie chmura z urządzenia.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Uwagi dodatkowe

* Połączenia AMQP, może zostać przerwane ze względu na błąd sieci lub utrata ważności uwierzytelnienia tokenu (generowane w kodzie). Klient usługi musi obsługiwać te okoliczności i ponownie ustanowić połączenia i łącza, jeśli to konieczne. Jeśli wygaśnięcia ważności tokenu uwierzytelniania, klient uniknąć zrzutu połączenia, proaktywnie odnawianie tokenu przed jego wygaśnięciem.

* Twój klient musi być od czasu do czasu mógł poprawnie obsłużyć przekierowań łącza. Aby zrozumieć takie działanie, zajrzyj do dokumentacji klienta protokołu AMQP.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat protokołu AMQP, zobacz [protokołu AMQP 1.0 specyfikacji](https://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Aby dowiedzieć się więcej na temat obsługi komunikatów w Centrum IoT Hub, zobacz:

* [Komunikaty z chmury do urządzenia](./iot-hub-devguide-messages-c2d.md)
* [Obsługa dodatkowych protokołów](iot-hub-protocol-gateway.md)
* [Obsługa protokołu transportu (MQTT) Telemetrii usługi kolejkowania w wiadomości](./iot-hub-mqtt-support.md)