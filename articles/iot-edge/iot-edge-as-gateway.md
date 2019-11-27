---
title: Bramy na potrzeby transmisji urządzeń — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Użyj usługi Azure IoT Edge, aby utworzyć przezroczyste, nieprzezroczyste lub serwer proxy urządzenie bramy, które wysyła dane z wielu podrzędnych urządzeń do chmury i przetwarza je lokalnie.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ec8b6cf61f9fb92f888642d1de7d4d1b9b7ac3df
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456642"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Jak używać urządzenia usługi IoT Edge jako bramy

Bramy w IoT Edge rozwiązania zapewniają łączność urządzeń z urządzeniami i analizą graniczną na urządzeniach IoT, które w przeciwnym razie nie będą miały tych możliwości. Usługa Azure IoT Edge może służyć do zaspokojenia potrzeb w przypadku bramy usługi IoT, niezależnie od tego, czy są one związane z analiz krawędziowych łączności lub tożsamości. Wzorce bramy w tym artykule odwoływać się tylko do właściwości łączności między podrzędnymi urządzeniami i tożsamości urządzenia nie jak przetwarzanie danych urządzenia w ramach bramy.

## <a name="patterns"></a>Wzorce

Istnieją trzy wzorce dotyczące korzystania z urządzenia usługi IoT Edge jako bramy: przezroczyste, protokołu, tłumaczenia i tłumaczenie tożsamości:
* **Przezroczyste** — urządzenia, które teoretycznie mogą połączyć się z IoT Hub mogą łączyć się z urządzeniem bramy. Podrzędne urządzenia mają własne tożsamości usługi IoT Hub i korzystania z protokołów MQTT, AMQP lub HTTP. Brama po prostu przekazuje komunikację między urządzeniami a Centrum IoT Hub. Urządzenia nie wiedzą, że komunikują się z chmurą za pośrednictwem bramy, a użytkownik korzystający z urządzeń w IoT Hub nie rozpoznaje pośredniego urządzenia bramy. W związku z tym brama jest przezroczysty. Zapoznaj się z tematem [Tworzenie przezroczystej bramy](how-to-create-transparent-gateway.md) , aby uzyskać szczegółowe informacje na temat używania urządzenia IoT Edge jako przezroczystej bramy.
* **Translacja protokołów** — znana także jako wzorzec nieprzezroczystej bramy, urządzenia, które nie obsługują MQTT, AMQP lub http, mogą używać urządzenia bramy do wysyłania danych do IoT Hub w ich imieniu. Brama rozumie protokół używany przez urządzenia podrzędne i jest jedynym urządzeniem, które ma tożsamość w IoT Hub. Wszystkie informacje prawdopodobnie pochodzi on z jednego urządzenia i bramy. Podrzędne urządzeń należy osadzić dodatkowe informacje identyfikacyjne w ich wiadomości, jeśli chcesz, aby aplikacje w chmurze do analizowania danych w poszczególnych urządzeń. Ponadto usługa IoT Hub w nim elementów podstawowych, takich jak bliźniaczych reprezentacji i metody są dostępne tylko dla urządzenia bramy, a nie podrzędnym urządzenia.
* **Tłumaczenie tożsamości** — urządzenia, które nie mogą połączyć się z IoT Hub mogą łączyć się z urządzeniem bramy. Brama zapewnia usługi IoT Hub, tożsamość i protocol tłumaczenia w imieniu urządzeń podrzędnego. Brama jest inteligentne, zrozumieć protokół używany przez urządzenia podrzędnego, potwierdzenie ich tożsamości i tłumaczenie podstawowych usługi IoT Hub. Urządzenia podrzędne są wyświetlane w usłudze IoT Hub jako najwyższej jakości urządzeń za pomocą twins i metody. Użytkownik może wchodzić w interakcje z urządzeniami w usłudze IoT Hub i nie rozpoznaje urządzenie bramy pośredniej.

![Diagram — przezroczyste, protokół i wzorce bramy tożsamości](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Przypadki zastosowań
Wszystkie wzorce brama zapewnia następujące korzyści:
* **Analiza na brzegu** — korzystaj z usług AI lokalnie, aby przetwarzać dane pochodzące z urządzeń podrzędnych bez wysyłania danych telemetrycznych o pełnej wierności do chmury. Znajdź i reagować na lokalnie szczegółowe informacje i wysyłać tylko podzbiór danych do usługi IoT Hub. 
* **Izolacja urządzenia podrzędnego** — urządzenie bramy może osłonować wszystkie urządzenia podrzędne przed narażeniem na Internet. Może się znajdować między siecią OT, który nie ma łączności i sieci IT, który zapewnia dostęp do sieci web. 
* **Multiplekser połączenia** — wszystkie urządzenia łączące się z IoT Hub za pomocą bramy IoT Edge używają tego samego połączenia podstawowego.
* **Wygładzanie ruchu** — urządzenie IoT Edge automatycznie implementuje wykładniczą wycofywania, jeśli IoT Hub ogranicza ruch, przy jednoczesnym zachowaniu komunikatów lokalnie. Ta korzyść udostępnia rozwiązania odporne na błędy skoków ruchu.
* **Obsługa offline** — urządzenie bramy przechowuje komunikaty i aktualizacje bliźniaczye, których nie można dostarczyć do IoT Hub.

Bramy protokołu tłumaczenia można również wykonywać analizy krawędziowe, izolacji urządzenia, wygładzanie ruchu i Obsługa trybu offline na urządzeniach istniejących i nowych urządzeń, które są ograniczone zasobów. Wiele istniejących urządzeń eksportują danych, który może obsługiwać wgląd w prowadzoną działalność; jednak nie zostało zaprojektowane z łączność z chmurą na uwadze. Nieprzezroczyste bramy umożliwiają odblokowywanie tych danych i używanie ich w rozwiązaniu IoT.

Bramy, który wykonuje translację tożsamości zalety translację protokołu, a dodatkowo umożliwia pełne możliwości zarządzania podrzędnym urządzenia w chmurze. Wszystkie urządzenia w rozwiązaniu IoT są wyświetlane w Centrum IoT Hub niezależnie od używanego protokołu.

## <a name="cheat-sheet"></a>Ściągawka
Poniżej przedstawiono szybki cheat-sheet, zawierająca porównanie podstawowych usługi IoT Hub, korzystając z przezroczyste, bramami nieprzezroczystego (protocol) i serwera proxy.

| &nbsp; | Przezroczystej bramy | Translację protokołu | Tłumaczenie tożsamości |
|--------|-------------|--------|--------|
| Tożsamości przechowywane w rejestrze tożsamości Centrum IoT Hub | Tożsamości wszystkie połączone urządzenia | Tożsamość urządzenia bramy | Tożsamości wszystkie połączone urządzenia |
| Bliźniak urządzenia | Każde urządzenie połączone ma swój własny bliźniaczej reprezentacji urządzenia | Tylko bramy ma bliźniaków urządzeń i modułu | Każde urządzenie połączone ma swój własny bliźniaczej reprezentacji urządzenia |
| Metody bezpośrednie i komunikaty z chmury do urządzenia | Chmury indywidualnie rozwiązywania każdego podłączonego urządzenia | Chmura tylko rozwiązywania przy użyciu urządzenia bramy | Chmury indywidualnie rozwiązywania każdego podłączonego urządzenia |
| [IoT Hub ograniczenia i limity przydziału](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Zastosuj do każdego urządzenia | Zastosuj do urządzenia bramy | Zastosuj do każdego urządzenia |

Udostępnianie tej samej kolejki chmury do urządzenia, który może zawierać maksymalnie 50 wiadomości, korzystając z wzorca nieprzezroczyste bramy (translację protokołu) wszystkich urządzeń, łącząc się za pośrednictwem tej bramy. Wynika, że wzorzec nieprzezroczyste bramy należy używać tylko wtedy, gdy kilka urządzenia nawiązują połączenie za pomocą każdej bramy w terenie, a ruch chmury do urządzenia jest niska.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować niejawną bramę: 

* [Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama](how-to-create-transparent-gateway.md)
* [Uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Łączenie urządzenia podrzędnego z bramą usługi Azure IoT Edge](how-to-connect-downstream-device.md)