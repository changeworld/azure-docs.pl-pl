---
title: Użyj urządzenia usługi Azure IoT Edge jako bramy | Dokumentacja firmy Microsoft
description: Użyj usługi Azure IoT Edge do utworzenia się przezroczyste, nieprzezroczyste lub urządzenie bramy serwera proxy, który wysyła dane z wielu podrzędnych urządzeń do chmury lub przetwarza je lokalnie.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e1825bcdd8dbb06ef027919416b2d0532a7df9c2
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47422834"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Jak używać urządzenia usługi IoT Edge jako bramy

Celem bramy w rozwiązaniach IoT jest specyficzne dla rozwiązania i połączyć łączność urządzeń za pomocą analiz. Usługa Azure IoT Edge może służyć do zaspokojenia potrzeb w przypadku bramy usługi IoT, niezależnie od tego, czy są one związane z analiz krawędziowych łączności lub tożsamości. Wzorce bramy w tym artykule odwoływać się tylko do właściwości łączności między podrzędnymi urządzeniami i tożsamości urządzenia nie jak przetwarzanie danych urządzenia w ramach bramy.

## <a name="patterns"></a>Wzorce
Istnieją trzy wzorce dotyczące korzystania z urządzenia usługi IoT Edge jako bramy: przezroczyste, protokołu, tłumaczenia i tłumaczenie tożsamości:
* **Przezroczysty** — urządzeń, które teoretycznie można połączyć się z Centrum IoT Hub można zamiast tego łączenie się z urządzeniem bramy. Oznacza to, że podrzędnym urządzenia mają własne tożsamości usługi IoT Hub i korzystania z protokołów MQTT, AMQP lub HTTP. Brama po prostu przekazuje komunikację między urządzeniami a Centrum IoT Hub. Urządzenia są świadomości, że komunikują się z chmurą za pośrednictwem bramy, a użytkownik interakcji z urządzeniami w usłudze IoT Hub niezależnych od urządzenia bramy pośredniej. W związku z tym brama jest przezroczysty. Zapoznaj się [tworzenie przezroczystej bramy] [ lnk-iot-edge-as-transparent-gateway] instrukcjami, aby uzyskać szczegółowe informacje na temat korzystania z urządzenia usługi IoT Edge rolę przezroczystej bramy.
* **Protokół tłumaczenia** — znanej również jako wzorzec nieprzezroczyste bramy, urządzeń, które nie obsługują protokołu MQTT, AMQP lub HTTP użyj urządzenia bramy w celu wysyłania danych do usługi IoT Hub. Brama jest inteligentnego zrozumieć ten protokół używany przez urządzenia podrzędnego; jednak jest tylko urządzenie, które ma tożsamości w usłudze IoT Hub. Wszystkie informacje prawdopodobnie pochodzi on z jednego urządzenia i bramy. Oznacza to, że podrzędnym urządzenia należy osadzić dodatkowe informacje identyfikacyjne w ich wiadomości, jeśli chcesz, aby aplikacje w chmurze przeglądanie informacji o danych w poszczególnych urządzeń. Ponadto usługa IoT Hub w nim elementów podstawowych, takich jak bliźniaczej reprezentacji i metody są dostępne tylko dla urządzenia bramy, a nie podrzędnym urządzenia.
* **Tłumaczenie tożsamości** — urządzeń, których nie można nawiązać połączenia z Centrum IoT Hub łączenie się z urządzeniem bramy, oferujący usługi IoT Hub, tożsamość i protocol tłumaczenia w imieniu urządzeń podrzędnego. Brama jest inteligentne, zrozumieć protokół używany przez urządzenia podrzędnego, potwierdzenie ich tożsamości i tłumaczenie podstawowych usługi IoT Hub. Urządzenia podrzędne są wyświetlane w usłudze IoT Hub jako najwyższej jakości urządzeń za pomocą twins i metody. Użytkownik może wchodzić w interakcje z urządzeniami w usłudze IoT Hub i nie rozpoznaje urządzenie bramy pośredniej.

![Diagramy wzorców bramy][1]

## <a name="use-cases"></a>Przypadki zastosowań
Wszystkie wzorce brama zapewnia następujące korzyści:
* **Krawędzi analytics** — użycie sztucznej Inteligencji usług lokalnie w celu przetwarzania danych pochodzących z urządzeń podrzędnego bez wysyłania pełnej wierności telemetryczne do chmury. Znajdź i reagować na lokalnie szczegółowe informacje i wysyłać tylko podzbiór danych do usługi IoT Hub. 
* **Izolacja podrzędnym urządzenia** — urządzenie bramy można włączyć osłony dla podrzędnego urządzeniom narażenia na internet. Może się znajdować między siecią OT, która nie ma łączności i sieci IT, która zapewnia dostęp do sieci web. 
* **Połączenie Multipleksowanie** — wszystkie urządzenia, łączenie z usługą IoT Hub za pomocą usługi IoT Edge urządzenia będzie używać tego samego podstawowego połączenia.
* **Wygładzanie ruchu** — urządzenie usługi IoT Edge automatycznie wdroży wykładniczego wycofywania w przypadku usługi IoT Hub, ograniczanie przepustowości, podczas utrwalania wiadomości lokalnie. Dzięki temu rozwiązaniu odporne na błędy do skoków ruchu.
* **Ograniczona obsługa trybu offline** — urządzenie bramy będą przechowywane lokalnie wiadomości i aktualizacji bliźniaczej reprezentacji, które nie może zostać dostarczona do usługi IoT Hub.

Tłumaczenie protokołów ma bramy można również wykonywać analizy krawędziowe, izolacji urządzenia, wygładzanie ruchu i Obsługa trybu offline na urządzeniach istniejących i nowych urządzeń, które są ograniczone zasobów. Wiele istniejących urządzeń eksportują danych, który może obsługiwać wgląd w prowadzoną działalność; jednak nie zostało zaprojektowane z łączność z chmurą na uwadze. Nieprzezroczysty bram Zezwalaj na tych danych można odblokować i wykorzystywane do kompleksowego rozwiązania IoT.

Bramy, który wykonuje translację tożsamości zapewnia korzyści translację protokołu i dodatkowo umożliwiają pełne możliwości zarządzania podrzędnym urządzenia w chmurze. Wszystkie urządzenia z usługi IoT rozwiązania pojawią się w Centrum IoT Hub niezależnie od tego protokołu, przy użyciu ich głosu.

## <a name="cheat-sheet"></a>Ściągawka
Poniżej przedstawiono szybki cheat-sheet, zawierająca porównanie podstawowych usługi IoT Hub, korzystając z przezroczyste, bramami nieprzezroczystego (protocol) i serwera proxy.

| &nbsp; | Przezroczystej bramy | Translację protokołu | Tłumaczenie tożsamości |
|--------|-------------|--------|--------|
| Tożsamości przechowywane w rejestrze tożsamości Centrum IoT Hub | Tożsamości wszystkie połączone urządzenia | Tożsamość urządzenia bramy | Tożsamości wszystkie połączone urządzenia |
| Bliźniak urządzenia | Każde urządzenie połączone ma swój własny bliźniaczej reprezentacji urządzenia | Tylko bramy ma bliźniaków urządzeń i modułu | Każde urządzenie połączone ma swój własny bliźniaczej reprezentacji urządzenia |
| Metody bezpośrednie i komunikaty z chmury do urządzenia | Chmury indywidualnie rozwiązywania każdego podłączonego urządzenia | Chmura tylko rozwiązywania przy użyciu urządzenia bramy | Chmury indywidualnie rozwiązywania każdego podłączonego urządzenia |
| [Przydziału i ograniczenia usługi IoT Hub][lnk-iothub-throttles-quotas] | Zastosuj do każdego urządzenia | Zastosuj do urządzenia bramy | Zastosuj do każdego urządzenia |

Udostępnianie tej samej kolejki chmury do urządzenia, który może zawierać maksymalnie 50 wiadomości, korzystając z wzorca nieprzezroczyste bramy (translację protokołu) wszystkich urządzeń, łącząc się za pośrednictwem tej bramy. Wynika, że wzorzec nieprzezroczyste bramy należy używać tylko wtedy, gdy bardzo mało urządzenia nawiązują połączenie za pomocą każdej bramy w terenie, a ruch chmury do urządzenia jest niska.

## <a name="next-steps"></a>Kolejne kroki
Użyj urządzenia usługi IoT Edge jako [przezroczystej bramy][lnk-iot-edge-as-transparent-gateway] 

[lnk-iot-edge-as-transparent-gateway]: ./how-to-create-transparent-gateway-linux.md
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md

[1]: ./media/iot-edge-as-gateway/edge-as-gateway.png
