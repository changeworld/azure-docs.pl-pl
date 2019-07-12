---
title: Zalecenia dotyczące zabezpieczeń dla usługi Azure IoT | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera podsumowanie dodatkowe kroki w celu zapewnienia bezpieczeństwa w rozwiązaniu Azure IoT Hub.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 4416f3149c33a0c9a437b2fbd6a48729a5a7f044
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722856"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Zalecenia dotyczące zabezpieczeń dla wdrożenia usługi Azure Internet of Things (IoT)

Ten artykuł zawiera zalecenia dotyczące zabezpieczeń dla usługi Azure IoT. Wdrażanie tych zaleceń dotyczących pomocy, które spełniają Twoje obowiązki zabezpieczeń jako klient korzystający z usługi Azure IoT i będzie poprawi ogólnego stanu zabezpieczeń dla rozwiązań IoT. Aby uzyskać więcej informacji na temat funkcji wewnętrznych zabezpieczeń oferowanych przez usługi Azure IoT, przeczytaj [zabezpieczenia IoT od podstaw](iot-security-ground-up.md).

## <a name="general"></a>Ogólne

| Zalecenie | Komentarze |
|-|-|
| Najnowsze informacje | Użyj najnowszej wersji obsługiwanych platform, języków programowania, protokołów i struktur. |
| Zabezpieczanie klucze uwierzytelniania | Zabezpieczanie identyfikatorów urządzeń i ich klucze uwierzytelniania fizycznie po wdrożeniu. Pozwoli to uniknąć maskująca złośliwego urządzenia jako zarejestrowanego urządzenia. |
| Korzystanie z zestawów SDK urządzeń, gdy jest to możliwe | Zestawy SDK urządzeń implementacji różnych funkcji zabezpieczeń, takie jak szyfrowanie, uwierzytelniania i tak dalej, aby pomóc w projektowaniu aplikacji urządzenia niezawodne i bezpieczne. Zobacz [poznawanie i używanie usługi Azure IoT Hub SDKs](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) Aby uzyskać więcej informacji. |


## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Zalecenie | Komentarze |
|-|-|
| Definiowanie kontroli dostępu dla koncentratora | [Zrozumienie i zdefiniuj typ dostępu](iot-security-deployment.md#securing-the-cloud) każdego składnika mają w rozwiązaniu IoT Hub, oparta na funkcjach. Dozwolone uprawnienia są *odczytu rejestru*, *RegistryReadWrite*, *ServiceConnect*, i *DeviceConnect*. Domyślne [współużytkowane zasady dostępu w usłudze IoT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) pomaga też zdefiniować uprawnienia dla każdego składnika, na podstawie jego roli. |
| Definiowanie kontroli dostępu do usług zaplecza | Danych pozyskanych przez rozwiązania usługi IoT Hub mogą być używane przez inne usługi Azure takie jak [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [usługi Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [usługi App Service](https://docs.microsoft.com/azure/app-service/), [Logic Apps](https://docs.microsoft.com/azure/logic-apps/), i [magazynu obiektów Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Upewnij się zrozumieć i umożliwić odpowiednie uprawnienia dostępu, zgodnie z opisem w przypadku tych usług. |


## <a name="data-protection"></a>Ochrona danych

| Zalecenie | Komentarze |
|-|-|
| Bezpieczne uwierzytelnianie urządzeń | Zapewnienia bezpiecznej komunikacji między urządzeniami a Centrum IoT, przy użyciu [klucza unikatową tożsamość lub zabezpieczeń tokenu](iot-security-deployment.md#iot-hub-security-tokens), lub [certyfikat X.509 na urządzeniu](iot-security-deployment.md#x509-certificate-based-device-authentication) dla każdego urządzenia. Użyj odpowiedniej metody do [używać tokenów zabezpieczających, na podstawie wybranego protokołu (MQTT, AMQP lub HTTPS)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). |
| Bezpieczna komunikacja urządzenia | Usługi IoT Hub zabezpiecza połączenia na urządzeniach przy użyciu standardu zabezpieczeń TLS (Transport Layer), obsługa wersji 1.2, 1.2 i 1.0. Użyj [protokołu TLS 1.2](https://tools.ietf.org/html/rfc5246) zapewnienie zapewnienia maksymalnego poziomu bezpieczeństwa. |
| Bezpieczna komunikacja usług | IoT Hub udostępnia punktach końcowych pozwala na łączenie z usługami zaplecza, takich jak [usługi Azure Storage](/azure/storage/) lub [usługi Event Hubs](/azure/event-hubs) przy użyciu protokołu TLS i żaden punkt końcowy jest uwidaczniany w niezaszyfrowanego kanału. Po tych danych osiągnie tych usług zaplecza w celu przechowywania lub analizy, upewnij się stosować odpowiednich metod zabezpieczeń i szyfrowania za daną usługę i chronić poufne informacje w wewnętrznej bazie danych. |


## <a name="networking"></a>Networking

| Zalecenie | Komentarze |
|-|-|
| Ochrona dostępu do urządzeń | Zachowywanie sprzętu porty urządzeń absolutnego minimum, aby zapobiec nieautoryzowanemu dostępowi. Ponadto tworzyć mechanizmy, aby zapobiec lub naruszeniem fizycznego urządzenia. Odczyt [najlepsze rozwiązania dotyczące zabezpieczeń IoT](iot-security-best-practices.md) Aby uzyskać szczegółowe informacje. |
| Tworzenie bezpiecznego sprzętu | Włączenie funkcji zabezpieczeń, np. szyfrowany magazyn lub Trusted Platform Module (TPM), aby zwiększyć bezpieczeństwo urządzeń i infrastruktury. System operacyjny urządzenia i sterowniki uaktualniony do najnowszej wersji, a jeśli wystarczająca ilość miejsca, należy zainstalować przed wirusami i złośliwym możliwości. Odczyt [architekturę zabezpieczeń IoT](iot-security-architecture.md) Aby zrozumieć, jak może to ułatwić, Eliminuj zagrożenia bezpieczeństwa kilka. |


## <a name="monitoring"></a>Monitorowanie

| Zalecenie | Komentarze |
|-|-|
| Monitorowanie nieautoryzowanego dostępu do urządzenia |  Monitorowanie naruszenia zabezpieczeń lub naruszeniem fizyczne urządzenia lub jego portów, należy użyć funkcji rejestrowania system operacyjny urządzenia. |
| Monitorowanie rozwiązania IoT z chmury | Monitorowanie ogólnej kondycji usługi IoT Hub rozwiązania przy użyciu [metryk w usłudze Azure Monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). |
| Konfigurowanie diagnostyki | Ściśle Obejrzyj operacji, rejestrowanie zdarzeń w rozwiązaniu, a następnie wysyłając dzienników diagnostycznych do usługi Azure Monitor, aby uzyskać wgląd w wydajność. Odczyt [monitorowanie i diagnozowanie problemów w usłudze IoT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) Aby uzyskać więcej informacji. |

## <a name="next-steps"></a>Następne kroki

W przypadku zaawansowanych scenariuszy dotyczących usługi Azure IoT może być konieczne należy wziąć pod uwagę dodatkowe wymagania dotyczące zabezpieczeń. Zobacz [architekturę zabezpieczeń IoT](iot-security-architecture.md) Aby uzyskać więcej wskazówek.

