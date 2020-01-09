---
title: Przekształć — Omówienie
description: Dowiedz się, jak przekształcać
author: viv-liu
ms.author: viviali
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 3df839738aaf28de3b32eee6e30f15376a00cc0d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434886"
---
# <a name="transform-your-iot-data-preview-features"></a>Przekształcanie danych IoT (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Jako platforma aplikacji IoT Central udostępnia kilka najważniejszych aspektów, które ułatwiają Przekształcanie danych IoT w szczegółowe informacje biznesowe, które umożliwiają wykonanie akcji. IoT Central zapewnia sposoby zwiększania danych IoT do systemów zewnętrznych w celu tworzenia integracji z aplikacjami biznesowymi i aplikacjami niestandardowymi. Możesz monitorować kondycję i operacje związane z urządzeniami, tworząc reguły, które powiadamiają techników za pośrednictwem usługi mobilnej. Możesz generować szczegółowe informacje biznesowe z użyciem analizy niestandardowej i uczenia maszynowego, eksportując dane danych telemetrycznych do usług na platformie Azure. Możesz tworzyć usługi i narzędzia, które monitorują i kontrolują urządzenia oraz zarządzają IoT Central aplikacji przy użyciu publicznych interfejsów API. 

![Przekształcanie w IoT Central przegląd](media/overview-iot-central-transform/transform.png)

## <a name="monitor-device-health-and-operations-using-rules"></a>Monitorowanie kondycji i operacji urządzenia przy użyciu reguł
Gdy maszyny są połączone i wysyłające dane, można zidentyfikować, na których maszynach występują problemy, lub wysłać komunikaty o błędach, aby można było je naprawić do normalnego działania z minimalnym przestojem. Możesz tworzyć reguły w aplikacji IoT Central, aby monitorować dane telemetryczne wysyłane przez urządzenia i ostrzegać o przekroczeniu progu lub wysłaniu określonego komunikatu o zdarzeniu. Można skonfigurować akcje, takie jak akcje poczty e-mail i elementy webhook dla reguł, aby poinformować odpowiednie osoby i właściwe systemy podrzędne. Więcej informacji o regułach znajdziesz tutaj.

## <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Uruchamianie analiz niestandardowych i przetwarzanie na wyeksportowanych danych
Można generować wysoce niestandardowe informacje biznesowe, takie jak określanie trendów wydajności maszyn lub przewidywanie przyszłego użycia energii w fabryce fabryki, tworząc niestandardowe potoki analizy w celu przetworzenia nieprzetworzonej telemetrii IoT i przechowywania wyniku końcowego. Możesz tworzyć eksporty danych w aplikacji IoT Central, aby eksportować dane telemetryczne, właściwości urządzenia i cykl życia oraz informacje o zmianach w szablonie urządzenia do innych usług platformy Azure, dzięki czemu można analizować, przechowywać i wizualizować dane w narzędziach, które są najbardziej cenne dla użytkownika. Więcej informacji na temat eksportu danych znajdziesz tutaj.

## <a name="build-custom-iot-solutions-and-integrations-with-apis"></a>Tworzenie niestandardowych rozwiązań IoT i integracji z interfejsami API
Możesz tworzyć rozwiązania IoT, takie jak aplikacje Mobile Companion, które mogą zdalnie sterować urządzeniami i konfigurować nowe urządzenia, lub integrację niestandardową z istniejącymi aplikacjami biznesowymi w celu współdziałania z urządzeniami IoT i danymi, które są dostosowane do Twojej firmy. IoT Central jako szkieletu można użyć do modelowania urządzeń, dołączania, zarządzania i dostępu do danych. Dowiedz się więcej o publicznym interfejsie API w tym module nauki.
