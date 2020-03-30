---
title: Przewodnik dla deweloperów - IoT Plug and Play Preview | Dokumenty firmy Microsoft
description: Opis modelowania urządzeń dla deweloperów IoT Plug and Play
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605223"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>Przewodnik dla deweloperów programów IoT Plug and Play Preview

Usługa IoT Plug and Play Preview umożliwia tworzenie urządzeń, które anonsują swoje możliwości do aplikacji Azure IoT. Urządzenia IoT Plug and Play nie wymagają ręcznej konfiguracji, gdy klient łączy je z aplikacjami obsługującymi technologię IoT Plug and Play. Usługa IoT Central jest przykładem aplikacji obsługującej funkcję IoT Plug and Play.

Aby utworzyć urządzenie Typu Plug and Play IoT, należy utworzyć opis urządzenia. Opis odbywa się za pomocą prostego języka definicji o nazwie Digital Twins Definition Language (DTDL).

## <a name="device-capability-model"></a>Model możliwości urządzenia

Za pomocą DTDL tworzysz _model możliwości urządzenia_ do opisywania części urządzenia. Typowe urządzenie IoT składa się z:

- Części niestandardowe, które sprawiają, że urządzenie jest wyjątkowe.
- Części standardowe, które są rzeczy, które są wspólne dla wszystkich urządzeń.

Te części są nazywane _interfejsami_ w modelu możliwości urządzenia. Interfejsy definiują szczegóły każdej części, które implementuje urządzenie.

W poniższym przykładzie przedstawiono model możliwości urządzenia dla urządzenia termostatu:

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Model możliwości ma kilka wymaganych pól:

- `@id`: unikatowy identyfikator w postaci prostej jednolitej nazwy zasobu.
- `@type`: deklaruje, że ten obiekt jest modelem możliwości.
- `@context`: określa wersję DTDL używaną dla modelu możliwości.
- `implements`: wyświetla listę interfejsów implementuujnych przez urządzenie.

Każdy wpis na liście interfejsów w sekcji implementuje ma:

- `name`: nazwa programowania interfejsu.
- `schema`: interfejs, który implementuje model możliwości.

Istnieją dodatkowe pola opcjonalne, których można użyć, aby dodać więcej szczegółów do modelu możliwości, takie jak wyświetlana nazwa i opis. Interfejsy, które są zadeklarowane w ramach modelu możliwości można traktować jako składniki urządzenia. W przypadku publicznej wersji zapoznawczej lista interfejsu może mieć tylko jeden wpis na schemat.

## <a name="interface"></a>Interface

Za pomocą DTDL opisano możliwości urządzenia za pomocą interfejsów. Interfejsy opisują _właściwości,_ _dane telemetryczne_i _polecenia,_ które implementuje część urządzenia:

- `Properties`. Właściwości są polami danych, które reprezentują stan urządzenia. Właściwości należy używać do reprezentowania trwałego stanu urządzenia, takich jak stan włączania i wyłączania pompy chłodziwa. Właściwości mogą również reprezentować podstawowe właściwości urządzenia, takie jak wersja oprogramowania układowego urządzenia. Można zadeklarować właściwości jako tylko do odczytu lub zapisywalne.
- `Telemetry`. Pola telemetryczne reprezentują pomiary z czujników. Za każdym razem, gdy urządzenie wykonuje pomiar czujnika, powinno wysłać zdarzenie telemetryczne zawierające dane z czujnika.
- `Commands`. Polecenia reprezentują metody, które użytkownicy urządzenia mogą wykonywać na urządzeniu. Na przykład polecenie resetowania lub polecenie włączania lub wyłączania wentylatora.

W poniższym przykładzie pokazano interfejs urządzenia termostatu:

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Interfejs ma kilka wymaganych pól:

- `@id`: unikatowy identyfikator w postaci prostej jednolitej nazwy zasobu.
- `@type`: deklaruje, że ten obiekt jest interfejsem.
- `@context`: określa wersję DTDL używaną dla interfejsu.
- `contents`: wyświetla listę właściwości, danych telemetrycznych i poleceń, które składają się na urządzenie.

W tym prostym przykładzie istnieje tylko jedno pole telemetrii. Minimalny opis pola ma:

- `@type`: określa typ możliwości: `Telemetry`, `Property`, `Command`lub .
- `name`: podaje nazwę wartości telemetrii.
- `schema`: określa typ danych dla telemetrii. Ta wartość może być typem pierwotnym, takim jak double, integer, boolean lub string. Obsługiwane są również złożone typy obiektów, tablice i mapy.

Inne pola opcjonalne, takie jak wyświetlana nazwa i opis, umożliwiają dodanie dodatkowych szczegółów do interfejsu i możliwości.

### <a name="properties"></a>Właściwości

Domyślnie właściwości są tylko do odczytu. Właściwości tylko do odczytu oznaczają, że urządzenie zgłasza aktualizacje wartości właściwości do centrum IoT Hub. Centrum IoT nie można ustawić wartości właściwości tylko do odczytu.

Można również oznaczyć właściwość jako zapisywalną w interfejsie. Urządzenie może odbierać aktualizację do właściwości zapisywalne z usługi IoT Hub, a także raportowanie aktualizacji wartości właściwości do centrum.

Urządzenia nie muszą być podłączone do ustawiania wartości właściwości. Zaktualizowane wartości są przesyłane, gdy urządzenie następnie łączy się z koncentratorem. To zachowanie dotyczy właściwości tylko do odczytu i zapisywalne.

Nie używaj właściwości do wysyłania danych telemetrycznych z urządzenia. Na przykład readonly właściwość, takie jak `temperatureSetting=80` powinna oznaczać, że temperatura urządzenia została ustawiona na 80, a urządzenie próbuje dostać się do lub pozostać w tej temperaturze.

W przypadku właściwości zapisywalnych aplikacja urządzenia zwraca żądany kod stanu, wersję i opis, aby wskazać, czy odebrano i zastosowało wartość właściwości.

### <a name="telemetry"></a>Telemetria

Domyślnie usługa IoT Hub kieruje wszystkie komunikaty telemetryczne z urządzeń do [wbudowanego punktu końcowego skierowanego do usługi **(wiadomości/zdarzeń),**](../iot-hub/iot-hub-devguide-messages-read-builtin.md) który jest zgodny z [centrum zdarzeń.](https://azure.microsoft.com/documentation/services/event-hubs/)

Można użyć [niestandardowych punktów końcowych usługi IoT Hub i reguł routingu](../iot-hub/iot-hub-devguide-messages-d2c.md) do wysyłania danych telemetrycznych do innych miejsc docelowych, takich jak magazyn obiektów blob lub inne centra zdarzeń. Reguły routingu używają właściwości wiadomości do wybierania wiadomości.

### <a name="commands"></a>Polecenia

Polecenia są synchroniczne lub asynchroniczne. Polecenie synchroniczne musi zostać wykonane domyślnie w ciągu 30 sekund, a urządzenie musi być połączone po odebraniu polecenia. Jeśli urządzenie reaguje w czasie lub urządzenie nie jest podłączone, polecenie nie powiedzie się.

Użyj poleceń asynchronicznych dla długotrwałych operacji. Urządzenie wysyła informacje o postępie przy użyciu komunikatów telemetrycznych. Te komunikaty postępu mają następujące właściwości nagłówka:

- `iothub-command-name`: na przykład `UpdateFirmware`nazwa polecenia .
- `iothub-command-request-id`: identyfikator żądania wygenerowany po stronie serwera i wysłany do urządzenia w wywołaniu początkowym.
- `iothub-interface-id`: Identyfikator interfejsu, na który zdefiniowano to `urn:example:AssetTracker:1`polecenie, na przykład .
 `iothub-interface-name`: nazwa wystąpienia tego interfejsu, `myAssetTracker`na przykład .
- `iothub-command-statuscode`: kod stanu zwrócony z `202`urządzenia, na przykład .

## <a name="register-a-device"></a>Rejestrowanie urządzenia

IoT Plug and Play ułatwia reklamowanie możliwości urządzenia. Dzięki uszczypnieniu IoT po podłączeniu urządzenia do usługi IoT Hub należy zarejestrować model możliwości urządzenia. Rejestracja umożliwia klientom korzystanie z funkcji IoT Plug and Play urządzenia.

W tym przewodniku pokazano, jak zarejestrować urządzenie przy użyciu zestawu SDK urządzenia usługi Azure IoT dla języka C.

Dla każdego interfejsu urządzenia implementuje, należy utworzyć interfejs i połączyć go z jego implementacji.

Dla interfejsu termostatu pokazanego wcześniej, za pomocą C SDK, należy utworzyć i podłączyć interfejs termostatu do jego implementacji:

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

Powtórz ten kod dla każdego interfejsu, który implementuje urządzenie.

Po utworzeniu interfejsu zarejestruj model możliwości urządzenia i interfejsy za pomocą centrum IoT Hub:

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaces, 2,
    null, null);
```

## <a name="use-a-device"></a>Korzystanie z urządzenia

IoT Plug and Play umożliwia korzystanie z urządzeń, które zarejestrowały swoje możliwości w centrum IoT Hub. Na przykład można uzyskać dostęp do właściwości i poleceń urządzenia bezpośrednio.

Aby użyć urządzenia Typu Plug and Play IoT podłączonego do centrum IoT hub, użyj interfejsu API REST usługi IoT Hub lub jednego z pakietów SDK języka IoT. W poniższych przykładach użyj interfejsu API REST usługi IoT Hub. Bieżąca wersja interfejsu `2019-07-01-preview`API to . Dołącz `?api-version=2019-07-01-preview` do wywołań REST PI.

Aby uzyskać wartość właściwości urządzenia, takiej jak wersja`fwVersion`oprogramowania `DeviceInformation` układowego ( ) w interfejsie w termostatze, należy użyć interfejsu API REST cyfrowych bliźniąt.

Jeśli urządzenie termostatu `t-123`jest wywoływane, otrzymasz wszystkie właściwości na wszystkich interfejsach zaimplementowanych przez urządzenie z wywołania REST API GET:

```REST
GET /digitalTwins/t-123/interfaces
```

Ogólnie rzecz biorąc, wszystkie właściwości we wszystkich interfejsach są `{device-id}` dostępne za pomocą tego szablonu interfejsu API REST, gdzie jest identyfikator urządzenia:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Jeśli znasz nazwę interfejsu, takich `deviceInformation`jak , i chcesz uzyskać właściwości dla tego określonego interfejsu, zakres żądania do określonego interfejsu według nazwy:

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

Bardziej ogólnie rzecz biorąc, właściwości określonego interfejsu są dostępne za `device-id` pośrednictwem tego szablonu `{interface-name}` interfejsu API REST, gdzie jest identyfikatorem urządzenia i jest nazwą interfejsu:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

Polecenia urządzenia IoT Plug and Play można wywołać bezpośrednio. Jeśli `Thermostat` interfejs w `t-123` urządzeniu `restart` ma polecenie, można wywołać go za pomocą wywołania REST API POST:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

Bardziej ogólnie, polecenia mogą być wywoływane za pośrednictwem tego szablonu interfejsu API REST:

- `device-id`: identyfikator urządzenia.
- `interface-name`: nazwa interfejsu z sekcji implements w modelu możliwości urządzenia.
- `command-name`: nazwa polecenia.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiedziałeś się o modelowaniu urządzeń, oto kilka dodatkowych zasobów:

- [Cyfrowy język podwójnej definicji (DTDL)](https://aka.ms/DTDL)
- [Zestaw SDK urządzenia dla języka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
