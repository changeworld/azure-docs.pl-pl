---
title: Interakcja z urządzeniem IoT Plug and Play Preview z rozwiązania IoT platformy Azure | Dokumenty firmy Microsoft
description: Jako deweloper rozwiązania dowiedz się, jak używać zestawu SDK usługi do interakcji z urządzeniami IoT Plug and Play.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e349aadfd629202b1c8cdb5c53a88e0a6c2e06de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159221"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Łączenie się z urządzeniem IoT Plug and Play Preview i interakcję z nim

W tym przewodniku przedstawiono sposób korzystania z przykładów w SDK usługi węzłów, które pokazują, jak rozwiązanie IoT może wchodzić w interakcje z urządzeniami IoT Plug and Play Preview.

Jeśli urządzenie Podłącz do rozwiązania nie zostało [ukończone z urządzeniem](quickstart-connect-pnp-device-solution-node.md) Szybki start, należy to zrobić teraz. Przewodnik Szybki start pokazuje, jak pobrać i zainstalować zestaw SDK i uruchomić niektóre przykłady.

Przed uruchomieniem przykładów usługi otwórz nowy terminal, przejdź do folderu głównego sklonowanego repozytorium, przejdź do folderu **digitaltwins/quickstarts/service,** a następnie uruchom następujące polecenie, aby zainstalować zależności:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>Uruchamianie przykładów usługi

Poniższe przykłady można zapoznać się z możliwościami zestawu SDK usługi Node.js. Upewnij się, `IOTHUB_CONNECTION_STRING` że zmienna środowiskowa jest ustawiona w używanej powłoce:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Pobierz cyfrowy bliźniak i wymień interfejsy

**get_digital_twin.js** pobiera cyfrowy bliźniak skojarzony z urządzeniem i drukuje jego komponent w wierszu polecenia. Nie wymaga próbki uruchomionego urządzenia, aby odnieść sukces.

**get_digital_twin_interface_instance.js** pobiera pojedyncze wystąpienie interfejsu cyfrowego bliźniaczego skojarzonego z urządzeniem i drukuje go w wierszu polecenia. Nie wymaga próbki urządzenia do uruchomienia.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Pobierz i ustaw właściwości przy użyciu zestawu SDK usługi Node

**update_digital_twin.js aktualizuje** zapisywalną właściwość na urządzeniu cyfrowego bliźniaka za pomocą pełnej poprawki. Można zaktualizować wiele właściwości na wielu interfejsach, jeśli chcesz. Aby to się udało, próbka urządzenia musi być uruchomiona w tym samym czasie. Sukces wygląda jak próbka urządzenia jest drukowanie coś o aktualizowanie właściwości próbki usługi drukowania zaktualizowanego bliźniaka cyfrowego w terminalu.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Wyślij polecenie i pobierz odpowiedź przy użyciu SDK usługi Node

**invoke_command.js** wywołuje polecenie synchroniczne na urządzeniu cyfrowym bliźniaczej reprezentacji. Aby to się udało, próbka urządzenia musi być uruchomiona w tym samym czasie. Sukces wygląda jak próbka urządzenia drukuje coś o potwierdzaniu polecenia, a klient serwisu drukuje wynik polecenia w terminalu.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Łączenie się z publicznym repozytorium i pobieranie definicji modelu przy użyciu sdk usługi node

Korzystając z tych samych instrukcji, co w przypadku próbek usługi i urządzenia, należy ustawić następującą zmienną środowiskową:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Ten ciąg połączenia można znaleźć w [portalu Certyfikat platformy Azure dla IoT](https://preview.catalog.azureiotsolutions.com) na karcie Parametry **połączenia** dla **repozytorium firmy.**

Ciąg połączenia wygląda następująco:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

Po ustawieniu tych czterech zmiennych środowiskowych uruchom próbkę w taki sam sposób, w jaki uruchomiono inne przykłady:

```cmd/sh
node model_repo.js
```

W tym przykładzie pobiera interfejs **ModelDiscovery** i drukuje ten model w terminalu.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Uruchamianie zapytań w Centrum IoT na podstawie modeli i interfejsów

Język zapytań Usługi IoT Hub obsługuje `HAS_INTERFACE` i `HAS_CAPABILITYMODEL` jak pokazano w następujących przykładach:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Tworzenie cyfrowych tras bliźniaczych

Rozwiązanie może otrzymywać powiadomienia o zdarzeniach cyfrowej zmiany bliźniaczej reprezentacji. Aby subskrybować te powiadomienia, użyj [funkcji routingu Usługi IoT Hub,](../iot-hub/iot-hub-devguide-endpoints.md) aby wysłać powiadomienia do punktu końcowego, takiego jak magazyn obiektów blob, centra zdarzeń lub kolejka usługi Service Bus.

Aby utworzyć cyfrową trasę bliźniaczej:

1. W witrynie Azure portal przejdź do zasobu usługi IoT Hub.
1. Wybierz **pozycję Routing wiadomości**.
1. Na karcie **Trasy** wybierz pozycję **Dodaj**.
1. Wprowadź wartość w polu **Nazwa** i wybierz **punkt końcowy**. Jeśli punkt końcowy nie został skonfigurowany, wybierz pozycję **Dodaj punkt końcowy**.
1. Z **listy** rozwijanej Źródło danych wybierz pozycję **Digital Twin Change Events**.
1. Wybierz **pozycję Zapisz**.

Poniżej JSON przedstawiono przykład zdarzenia cyfrowej zmiany bliźniaczej reprezentacji:

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiedziałeś się o rozwiązaniach usługowych, które wchodzą w interakcję z urządzeniami IoT Plug and Play, następnym krokiem jest zapoznanie się z [odnajdywania modelu.](concepts-model-discovery.md)
