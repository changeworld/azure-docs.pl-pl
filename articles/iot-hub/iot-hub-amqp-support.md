---
title: Omówienie pomocy technicznej usługi Azure IoT Hub AMQP | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — Obsługa urządzeń nawiązujących połączenie z Centrum IoT Hub przeznaczonych dla urządzeń i przeznaczonych dla usługi punktów końcowych, za pomocą protokołu AMQP. Zawiera informacje o wbudowanych AMQP obsługi w zestawy SDK urządzeń Azure IoT.
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: rezas
ms.openlocfilehash: 703e2c842fb42bad8aa112d84c516a29c2327378
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473509"
---
# <a name="communicate-with-your-iot-hub-using-the-amqp-protocol"></a>Komunikować się z Centrum IoT hub przy użyciu protokołu AMQP

Centrum IoT Hub obsługuje [protokołu AMQP 1.0 w wersji](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) dostarczać różne funkcje za pośrednictwem punktów końcowych przeznaczonych dla urządzeń i przeznaczonych dla usługi. W tym dokumencie opisano korzystanie z protokołu AMQP klientom nawiązywanie połączenia z Centrum IoT Hub, aby korzystać z funkcji usługi IoT Hub.

## <a name="service-client"></a>Klient usługi

### <a name="connection-and-authenticating-to-iot-hub-service-client"></a>Połączenia i uwierzytelniania w usłudze IoT Hub (klient usługi)
Aby połączyć z usługą IoT Hub za pomocą protokołu AMQP, klient może używać [oświadczenia na podstawie zabezpieczeń (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) lub [uwierzytelnianie proste uwierzytelnianie i warstwy zabezpieczeń (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Wymagane dla klienta usługi są następujące informacje:

| Informacje | Wartość | 
|-------------|--------------|
| Nazwa hosta usługi IoT Hub | `<iot-hub-name>.azure-devices.net` |
| Nazwa klucza | `service` |
| Klucz dostępu | Klucz podstawowy lub pomocniczy skojarzony z usługą |
| Sygnatura dostępu współdzielonego | Krótkotrwałe sygnatury dostępu Współdzielonego w następującym formacie: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` (znajduje się kod, aby wygenerować ten podpis [tutaj](./iot-hub-devguide-security.md#security-token-structure)).


Fragment kodu poniżej został użyty [uAMQP biblioteki w języku Python](https://github.com/Azure/azure-uamqp-python) połączyć się z Centrum IoT hub za pomocą linku nadawcy.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # e.g., '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoking-cloud-to-device-messages-service-client"></a>Wywoływanie komunikatów z chmury do urządzeń (klient usługi)
Wymiana komunikatów z chmury do urządzeń między usługą i Centrum IoT, a także między urządzeniem i Centrum IoT Hub jest opisany [tutaj](iot-hub-devguide-messages-c2d.md). Klient usługi używa dwóch łączy opisane poniżej, aby wysyłać komunikaty i otrzymywanie informacji zwrotnych wcześniej wysłanych komunikatów z urządzeń.

| Utworzone przez | Typ linku | Ścieżka łącza | Opis |
|------------|-----------|-----------|-------------|
| Usługa | Link nadawcy | `/messages/devicebound` | Wiadomości C2D kierowany do urządzenia są wysyłane do tego łącza przez usługę. Komunikaty wysyłane za pośrednictwem tego łącza mają ich `To` właściwość, Ustaw ścieżkę łącza odbiorcy urządzenie docelowe: czyli `/devices/<deviceID>/messages/devicebound`. |
| Usługa | Link odbiorcy | `/messages/serviceBound/feedback` | Uzupełnianie, odrzucenie i porzucania komunikatów zwrotnych pochodzące z urządzeń odebrane ten Link przez usługę. Zobacz [tutaj](./iot-hub-devguide-messages-c2d.md#message-feedback) Aby uzyskać więcej informacji na temat komunikatów zwrotnych. |

Poniższy fragment kodu pokazuje, jak utworzyć komunikat C2D oraz wysyłać je do urządzenia za pomocą [uAMQP biblioteki w języku Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the devicebound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message using the send client created and connected IoT Hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if not needed
send_client.close()
```

Aby otrzymać opinię, klient usługi tworzy łącze odbiorcy. Poniższy fragment kodu pokazuje, jak to zrobić za pomocą [uAMQP biblioteki w języku Python](https://github.com/Azure/azure-uamqp-python).

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Recreate the URI using the feedback path above and authenticate
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

Jak wspomniano powyżej, C2D wiadomość ma typ zawartości `application/vnd.microsoft.iothub.feedback.json` i właściwości w jej treści JSON można wywnioskować stan dostarczenia oryginalnej wiadomości:
* Klucz `statusCode` w opinii treść ma jedną z następujących wartości: `['Success', 'Expired', 'DeliveryCountExceeded', 'Rejected', 'Purged']`.
* Klucz `deviceId` opinii treść zawiera identyfikator urządzenia docelowego.
* Klucz `originalMessageId` opinii treść ma identyfikator oryginalnego komunikatu C2D wysłanych przez usługę. To może służyć do skorelowania opinii C2D komunikatów.

### <a name="receive-telemetry-messages-service-client"></a>Odbieranie danych telemetrycznych komunikatów (klient usługi)


### <a name="additional-notes"></a>Uwagi dodatkowe
* Połączenia AMQP może zostać przerwane ze względu na sieciowy, błąd lub wygaśnięcia tokenu uwierzytelniania (generowane w kodzie). Klient usługi musi obsługiwać te okoliczności i ponownego nawiązania połączenia i linki, jeśli to konieczne. W przypadku wygaśnięcia tokenu uwierzytelniania klienta może również proaktywnie odnawiania tokenu przed jego wygaśnięciem, aby uniknąć zrzutu połączenia.
* W niektórych przypadkach Twój klient musi być w stanie prawidłowo obsłużyć przekierowań łącza. Zapoznaj się z dokumentacją klienta protokołu AMQP, jak to zrobić.

### <a name="receive-cloud-to-device-messages-device-and-module-client"></a>Odbieranie komunikatów z chmury do urządzenia (urządzenia i moduł klienta)
Linki AMQP używane po stronie urządzenia są następujące:

| Utworzone przez | Typ linku | Ścieżka łącza | Opis |
|------------|-----------|-----------|-------------|
| Urządzenia | Link odbiorcy | `/devices/<deviceID>/messages/devicebound` | Komunikaty C2D kierowany do urządzenia są odbierane ten Link przez poszczególne urządzenia docelowego. |
| Urządzenia | Link nadawcy | `/messages/serviceBound/feedback` | Opinia komunikat C2D wysyłane do usługi za pośrednictwem tego łącza przez urządzenia. |
| Moduły | Link odbiorcy | `/devices/<deviceID>/modules/<moduleID>/messages/devicebound` | Komunikaty C2D skierowany do modułów są odbierane ten Link przez każdy moduł docelowego. |
| Moduły | Link nadawcy | `/messages/serviceBound/feedback` | Opinia komunikat C2D wysyłane do usługi za pośrednictwem tego łącza przez moduły. |


## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat protokołu AMQP, zobacz [protokołu AMQP 1.0 specyfikacji](http://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Aby dowiedzieć się więcej na temat obsługi komunikatów w Centrum IoT Hub, zobacz:

* [Komunikaty z chmury do urządzenia](./iot-hub-devguide-messages-c2d.md)
* [Obsługa dodatkowych protokołów](iot-hub-protocol-gateway.md)
* [Obsługa protokołu MQTT](./iot-hub-mqtt-support.md)
