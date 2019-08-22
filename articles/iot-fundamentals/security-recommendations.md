---
title: Zalecenia dotyczące zabezpieczeń dla usługi Azure IoT | Microsoft Docs
description: Ten artykuł zawiera podsumowanie dodatkowych kroków zapewniających bezpieczeństwo rozwiązań IoT Hub platformy Azure.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 55ca189d5f4622a395ffe603d7f0d6764db82f3d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877221"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Zalecenia dotyczące zabezpieczeń dla wdrożenia usługi Azure Internet rzeczy (IoT)

Ten artykuł zawiera zalecenia dotyczące zabezpieczeń usług Azure IoT. Wdrożenie tych zaleceń pomoże Ci zrealizować zobowiązania z zakresu bezpieczeństwa jako klienta usługi Azure IoT i poprawi ogólne zabezpieczenia twoich rozwiązań IoT. Aby uzyskać więcej informacji na temat wewnętrznych funkcji zabezpieczeń udostępnianych przez usługę Azure IoT, zapoznaj [się z artykułem ochrona IoT od podstaw](iot-security-ground-up.md).

## <a name="general"></a>Ogólne

| Zalecenie | Komentarze |
|-|-|
| Bądź na bieżąco | Używaj najnowszych wersji obsługiwanych platform, języków programowania, protokołów i struktur. |
| Zabezpieczanie kluczy uwierzytelniania | Przechowuj identyfikatory urządzeń i ich klucze uwierzytelniania fizycznie bezpiecznie po wdrożeniu. Pozwoli to uniknąć powstania złośliwego urządzenia jako zarejestrowanego urządzenia. |
| Użyj zestawów SDK urządzeń, gdy jest to możliwe | Zestawy SDK urządzeń implementują różne funkcje zabezpieczeń, takie jak szyfrowanie, uwierzytelnianie i tak dalej, aby pomóc w opracowaniu niezawodnej i bezpiecznej aplikacji urządzenia. Aby uzyskać więcej informacji [, zobacz Omówienie zestawów SDK platformy IoT Hub Azure i ich używanie](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) . |


## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Zalecenie | Komentarze |
|-|-|
| Definiowanie kontroli dostępu do centrum | [Zrozumienie i Definiowanie typu dostępu](iot-security-deployment.md#securing-the-cloud) każdego składnika, który będzie znajdował się w rozwiązaniu IoT Hub, na podstawie jego funkcjonalności. Dozwolonymi uprawnieniami są *odczyty rejestru*, *RegistryReadWrite*, serviceconnect i *DeviceConnect*. Domyślne [zasady dostępu współdzielonego w usłudze IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) mogą także ułatwić Definiowanie uprawnień dla każdego składnika na podstawie jego roli. |
| Definiowanie kontroli dostępu dla usług zaplecza | Dane pozyskane przez rozwiązanie IoT Hub mogą być używane przez inne usługi platformy Azure, takie jak [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [App Service](https://docs.microsoft.com/azure/app-service/), [Logic Apps](https://docs.microsoft.com/azure/logic-apps/)i [Magazyn obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Upewnij się, że rozumiesz i zezwolisz na odpowiednie uprawnienia dostępu zgodnie z opisem dla tych usług. |


## <a name="data-protection"></a>Ochrona danych

| Zalecenie | Komentarze |
|-|-|
| Bezpieczne uwierzytelnianie urządzeń | Zapewnianie bezpiecznej komunikacji między urządzeniami i centrum IoT Hub przy użyciu unikatowego [klucza tożsamości lub tokenu zabezpieczającego](iot-security-deployment.md#iot-hub-security-tokens)lub [certyfikatu X. 509](iot-security-deployment.md#x509-certificate-based-device-authentication) dla każdego urządzenia. Użyj odpowiedniej metody, aby [użyć tokenów zabezpieczających na podstawie wybranego protokołu (MQTT, AMQP lub https)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). |
| Zabezpieczanie komunikacji urządzeń | IoT Hub zabezpiecza połączenie z urządzeniami przy użyciu standardu Transport Layer Security (TLS), obsługując wersje 1,2 i 1,0. Aby zapewnić maksymalne bezpieczeństwo, użyj [protokołu TLS 1,2](https://tools.ietf.org/html/rfc5246) . |
| Bezpieczna komunikacja z usługą | IoT Hub udostępnia punkty końcowe do nawiązywania połączeń z usługami zaplecza, takimi jak [usługa Azure Storage](/azure/storage/) , lub [Event Hubs](/azure/event-hubs) przy użyciu tylko protokołu TLS, a punkt końcowy nie jest ujawniany na nieszyfrowanym kanale. Gdy te dane osiągną te usługi zaplecza do przechowywania lub analizy, należy zastosować odpowiednie metody zabezpieczeń i szyfrowania dla tej usługi oraz chronić poufne informacje w zapleczu. |


## <a name="networking"></a>Networking

| Zalecenie | Komentarze |
|-|-|
| Ochrona dostępu do urządzeń | Aby uniknąć niepożądanego dostępu, przechowuj porty sprzętowe na urządzeniach jako minimum systemu operacyjnego. Ponadto mechanizmy kompilacji, aby zapobiec lub wykryć fizyczne manipulowanie urządzeniem. Przeczytaj temat [najlepsze rozwiązania z zakresu zabezpieczeń IoT](iot-security-best-practices.md) , aby uzyskać szczegółowe informacje. |
| Tworzenie bezpiecznego sprzętu | Włączenie funkcji zabezpieczeń, takich jak zaszyfrowany magazyn lub moduł TPM (TPM), aby zapewnić lepszą ochronę urządzeń i infrastruktury. Zadbaj, aby system operacyjny i sterowniki sterownika były uaktualnione do najnowszych wersji, a jeśli miejsce zezwala, zainstaluj program antywirusowy i oprogramowanie chroniące przed złośliwym kodem. Przeczytaj temat [Architektura zabezpieczeń IoT](iot-security-architecture.md) , aby zrozumieć, jak może to pomóc w eliminowaniu kilku zagrożeń bezpieczeństwa. |


## <a name="monitoring"></a>Monitorowanie

| Zalecenie | Komentarze |
|-|-|
| Monitoruj nieautoryzowany dostęp do urządzeń |  Funkcja rejestrowania systemu operacyjnego urządzenia służy do monitorowania wszelkich naruszeń zabezpieczeń lub fizycznego manipulowania urządzeniem lub jego portów. |
| Monitoruj rozwiązanie IoT w chmurze | Monitoruj ogólną kondycję rozwiązania IoT Hub przy użyciu [metryk w Azure monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). |
| Konfigurowanie diagnostyki | Uważnie Monitoruj operacje przez rejestrowanie zdarzeń w rozwiązaniu, a następnie wysyłanie dzienników diagnostycznych do Azure Monitor, aby uzyskać wgląd w wydajność. Aby uzyskać więcej informacji, przeczytaj artykuł [monitorowanie i diagnozowanie problemów w centrum IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) . |

## <a name="next-steps"></a>Następne kroki

W przypadku zaawansowanych scenariuszy dotyczących usługi Azure IoT konieczne może być uwzględnienie dodatkowych wymagań w zakresie zabezpieczeń. Zobacz [Architektura zabezpieczeń IoT](iot-security-architecture.md) , aby uzyskać więcej wskazówek.

