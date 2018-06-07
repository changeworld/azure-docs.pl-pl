---
title: Użyj urządzenia Azure IoT brzegowe jako bramy | Dokumentacja firmy Microsoft
description: Użyj Azure IoT krawędzi stworzenie przejrzyste, nieprzezroczyste lub urządzenie bramy serwera proxy, który wysyła dane z różnych urządzeń podrzędne w chmurze i przetwarza je lokalnie.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/27/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ff5e50df1f7a9059bf37fe99d89050a48aa6e64e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631602"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway---preview"></a>W jaki sposób urządzenia IoT może służyć jako brama - preview

Celem bramy w rozwiązania IoT jest przeznaczony do rozwiązania i łączenie urządzenia łączności z analytics krawędzi. Usługa Azure IoT krawędzi może służyć do zaspokojenia potrzeb bramy IoT niezależnie od tego, czy są związane z łączności, tożsamość lub analityka krawędzi. Wzorce bramy w tym artykule odwoływać się tylko do właściwości łączności podrzędne urządzenia i tożsamości urządzenia nie sposób przetwarzania danych urządzenia w bramie.

## <a name="patterns"></a>Wzorce
Istnieją trzy wzorce dla jako bramy przy użyciu urządzenia IoT: przezroczyste, protokołu, tłumaczenia i translację tożsamości:
* **Przezroczysty** — urządzenia, które teoretycznie można połączyć się z Centrum IoT można zamiast tego łączenie się z urządzeniem bramy. Oznacza to, że podrzędne urządzenia ma własną tożsamość Centrum IoT i korzystania z protokołów HTTP MQTT lub AMQP. Brama po prostu przekazuje komunikację między urządzeniami a Centrum IoT. Urządzenia są wie, że komunikują się w chmurze za pośrednictwem bramy i interakcji z urządzeniami w Centrum IoT użytkownik nie zna urządzenia bramy pośredniej. W związku z tym bramy jest niewidoczny. Zapoznaj się [utworzyć przezroczystego bramy] [ lnk-iot-edge-as-transparent-gateway] porad dla szczegółowych informacji na temat używania urządzenia IoT jako przezroczysty bramy.
* **Protokół tłumaczenia** — urządzenia, które nie obsługują MQTT AMQP i HTTP umożliwia wysyłanie danych do Centrum IoT urządzenia bramy. Brama jest zrozumienie protokołu używany przez urządzenia podrzędne; jednak jest tylko urządzenia, które ma tożsamość w Centrum IoT. Wszystkie informacje wygląda ona pochodzi jedno urządzenie, bramy. Oznacza to, że podrzędne urządzeń należy osadzić dodatkowe informacje identyfikacyjne w ich komunikatach, jeśli chcesz, aby aplikacje w chmurze przeglądanie informacji o danych na podstawie na urządzenie. Ponadto Centrum IoT w nim elementów podstawowych, takich jak dwie i metody są dostępne tylko dla urządzenie bramy nie podrzędne urządzeń.
* **Tłumaczenie tożsamości** — urządzenia, których nie można nawiązać połączenia z Centrum IoT łączyć się urządzenie bramy, która zapewnia Centrum IoT tożsamości i protokół tłumaczenia imieniu podrzędne urządzeń. Brama jest zrozumienie protokół używany przez urządzenia podrzędne, podaj je tożsamości i przetłumaczyć elementów podstawowych Centrum IoT. Urządzenia podrzędne są wyświetlane w Centrum IoT jako najwyższej jakości urządzeniami przy użyciu twins i metod. Użytkownik mogą współdziałać z urządzeniami w Centrum IoT i nie rozpoznaje urządzenia bramy pośredniej.

![Diagramy wzorców bramy][1]

## <a name="use-cases"></a>Przypadki zastosowań
Wszystkie bramy wzorce zapewniają następujące korzyści:
* **Krawędzi analytics** — usługi AI Użyj lokalnie w celu przetworzenia danych z urządzeń podrzędne bez wysyłania pełnej rozdzielczości telemetryczne do chmury. Znajdź i reagowania na insights lokalnie i wysyłać tylko podzestaw danych do Centrum IoT. 
* **Urządzenie podrzędne izolacji** — urządzenie bramy, które można włączyć osłony dla podrzędne urządzeniom narażenia z Internetem. Można go sit Between OT sieci, która nie ma łączności i sieć IT, która zapewnia dostęp w sieci Web. 
* **Połączenie Multipleksowanie** — wszystkich urządzeń nawiązujących połączenie z Centrum IoT za pośrednictwem krawędzi IoT urządzenie będzie używać tego samego podstawowego połączenia.
* **Wygładzanie ruchu** -urządzenie brzegowe IoT automatycznie wykona wykładniczego wycofywania w przypadku ograniczania przepustowości podczas utrwalanie komunikaty lokalnie Centrum IoT. Spowoduje to rozwiązania odporne na największego ruchu.
* **Ograniczona obsługa w trybie offline** — urządzenie bramy, które będą przechowywane lokalnie wiadomości i dwie aktualizacjami, których nie można dostarczyć do Centrum IoT.

Tłumaczenie protokołu ma bramy można również wykonywać analytics krawędzi, urządzenia izolacji ruchu wygładzanie i obsługi w trybie offline do urządzeń istniejących i nowych urządzeń, które są ograniczone zasobów. Wiele istniejących urządzeń są zwracające dane, które zasilanie biznesowych; jednak nie zostało zaprojektowane z łącznością chmury na uwadze. Nieprzezroczysta bram Zezwalaj tych danych można odblokować i używane w kompleksowe rozwiązania IoT.

Bramy, który wykonuje translację tożsamości zapewniają korzyści tłumaczenia protokołów i dodatkowo umożliwiają pełne możliwości zarządzania podrzędne urządzeń z chmury. Wszystkie urządzenia w Twojej IoT rozwiązania będą wyświetlane w Centrum IoT niezależnie od ich protokołu mowy.

## <a name="cheat-sheet"></a>Ściągawka
W tym miejscu jest szybkie cheat arkusz, który porównuje podstawowych Centrum IoT, korzystając z przezroczystym, nieprzezroczyste i bramy serwera proxy.

| &nbsp; | Przezroczysty bramy | Protokół tłumaczenia | Tłumaczenie tożsamości |
|--------|-------------|--------|--------|
| Tożsamości przechowywane w rejestrze tożsamości Centrum IoT | Tożsamości wszystkich połączonych urządzeń | Urządzenie bramy z tożsamością | Tożsamości wszystkich połączonych urządzeń |
| Bliźniak urządzenia | Każdy podłączonego urządzenia ma własną dwie urządzenia | Tylko bramy ma twins modułu i urządzenia | Każdy podłączonego urządzenia ma własną dwie urządzenia |
| Bezpośrednie metod i komunikaty chmury do urządzenia | Chmury mogą indywidualnie adresów każdego podłączonego urządzenia | Chmury można usunąć tylko z urządzeniem bramy | Chmury mogą indywidualnie adresów każdego podłączonego urządzenia |
| [Limity Centrum IoT i przydziały][lnk-iothub-throttles-quotas] | Zastosuj do każdego urządzenia | Zastosuj z urządzeniem bramy | Zastosuj do każdego urządzenia |

Gdy za pomocą wzorca nieprzezroczyste bramy (tłumaczenia protokołów) wszystkich urządzeń łączących się za pośrednictwem tej bramy współużytkują tę samą kolejkę chmury do urządzenia, który może zawierać maksymalnie 50 wiadomości. Wynika, że wzorzec nieprzezroczyste bramy należy używać, tylko wtedy, gdy tylko kilka urządzeń jest nawiązywane za pośrednictwem bramy każdego pola i ich ruch z chmury do urządzenia jest niska.

## <a name="next-steps"></a>Kolejne kroki
Użyj urządzenia IoT jako [przezroczysty bramy][lnk-iot-edge-as-transparent-gateway] 

[lnk-iot-edge-as-transparent-gateway]: ./how-to-create-transparent-gateway.md
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md

[1]: ./media/iot-edge-as-gateway/edge-as-gateway.png
