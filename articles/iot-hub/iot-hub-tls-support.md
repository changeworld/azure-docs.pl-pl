---
title: Obsługa usługi Azure IoT Hub TLS
description: Najważniejsze wskazówki dotyczące używania bezpiecznych połączeń TLS dla urządzeń i usług komunikujących się z Centrum IoT
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 7ab3b48d22f116a707f68cbf6284928c7d2557e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409503"
---
# <a name="tls-support-in-iot-hub"></a>Obsługa protokołu TLS w Uilec ioT Hub

Usługa IoT Hub używa zabezpieczeń TLS (Transport Layer Security) do zabezpieczania połączeń z urządzeń i usług IoT. Obecnie obsługiwane są trzy wersje protokołu TLS, a mianowicie wersje 1.0, 1.1 i 1.2.

TLS 1.0 i 1.1 są uważane za starsze i są planowane do wycofania. Aby uzyskać więcej informacji, zobacz [Przestarzałe TLS 1.0 i 1.1 dla Usługi IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Zdecydowanie zaleca się używanie protokołu TLS 1.2 jako preferowanej wersji protokołu TLS podczas łączenia się z centrum IoT Hub.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Ograniczanie połączeń do protokołu TLS 1.2 w zasobie usługi IoT Hub

Aby zwiększyć bezpieczeństwo, zaleca się skonfigurowanie centrów IoT, aby zezwalały *tylko* na połączenia klientów korzystające z protokołu TLS w wersji 1.2 i wymuszały użycie [zalecanych szyfrów](#recommended-ciphers).

W tym celu aprowizować nowe Centrum IoT w dowolnym `minTlsVersion` z `1.2` [obsługiwanych regionów](#supported-regions) i ustawić właściwość w specyfikacji zasobów usługi Azure Resource Manager centrum Usługi IoT:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

Utworzony zasób Usługi IoT Hub przy użyciu tej konfiguracji spowoduje odrzucenie klientów urządzeń i usług, którzy próbują połączyć się przy użyciu protokołu TLS w wersjach 1.0 i 1.1. Podobnie uzgadnianie TLS zostanie odrzucone, jeśli komunikat HELLO klienta nie zawiera żadnej z [zalecanych szyfrów](#recommended-ciphers).

> [!NOTE]
> Właściwość `minTlsVersion` jest tylko do odczytu i nie można zmienić po utworzeniu zasobu Usługi IoT Hub. Dlatego ważne jest, aby prawidłowo przetestować i sprawdzić, czy *wszystkie* urządzenia i usługi IoT są zgodne z TLS 1.2 i [zalecane szyfry](#recommended-ciphers) z wyprzedzeniem.

### <a name="supported-regions"></a>Obsługiwane regiony

Centra IoT, które wymagają użycia protokołu TLS 1.2, można utworzyć w następujących regionach:

* Wschodnie stany USA
* Południowo-środkowe stany USA
* Zachodnie stany USA 2
* US Gov Arizona
* US Gov Wirginia

> [!NOTE]
> Po przemijeniu `minTlsVersion` awaryjnym właściwość usługi IoT Hub pozostanie skuteczna w regionie połączonym z węzłem geograficznym po przemiń awaryjnych.

### <a name="recommended-ciphers"></a>Zalecane szyfry

Centra IoT, które są skonfigurowane do akceptowania tylko protokołu TLS 1.2, będą również wymuszać użycie następujących zalecanych szyfrów:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>Używanie protokołu TLS 1.2 w szafach SDK usługi IoT Hub

Skorzystaj z poniższych łączy, aby skonfigurować TLS 1.2 i dozwolone szyfry w sdkach klienta usługi IoT Hub.

| Język | Wersje obsługujące TLS 1.2 | Dokumentacja |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 lub nowszy            | [Link](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Wersja 2.0.0 lub nowsza             | [Link](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Wersja 1.21.4 lub nowsza            | [Link](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Wersja 1.19.0 lub nowsza            | [Link](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Wersja 1.12.2 lub nowsza            | [Link](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>Używanie protokołu TLS 1.2 w konfiguracji usługi IoT Edge

Urządzenia usługi IoT Edge można skonfigurować do używania protokołu TLS 1.2 podczas komunikowania się z centrum IoT Hub. W tym celu należy użyć [strony dokumentacji ioT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).