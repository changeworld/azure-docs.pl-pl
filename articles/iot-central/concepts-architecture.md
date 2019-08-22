---
title: Koncepcje architektury na platformie Azure IoT Central | Microsoft Docs
description: W tym artykule przedstawiono kluczowe pojęcia związane z architekturą IoT Central platformy Azure
author: dominicbetts
ms.author: dobett
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 43357bdeb444fed20f29107d10dc31a61857fccf
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877508"
---
# <a name="azure-iot-central-architecture"></a>Architektura usługi Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Ten artykuł zawiera omówienie architektury IoT Central Microsoft Azure.

![Architektura najwyższego poziomu](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Urządzenia

Urządzenia wymieniają dane z aplikacją IoT Central platformy Azure. Urządzenie może:

- Wysyłaj pomiary, takie jak Telemetria.
- Zsynchronizuj ustawienia z aplikacją.

Na platformie Azure IoT Central dane, które urządzenie może wymieniać z aplikacją, są określone w szablonie urządzenia. Aby uzyskać więcej informacji na temat szablonów urządzeń, zobacz [Zarządzanie metadanymi](#metadata-management).

Aby dowiedzieć się więcej o tym, jak urządzenia nawiązują połączenie z aplikacją IoT Central platformy Azure, zobacz [łączność urządzeń](concepts-connectivity.md).

## <a name="cloud-gateway"></a>Brama chmury

Usługa Azure IoT Central korzysta z usługi Azure IoT Hub jako bramy w chmurze, która umożliwia łączność z urządzeniem. IoT Hub umożliwia:

- Pozyskiwanie danych na dużą skalę w chmurze.
- Zarządzanie urządzeniami.
- Zabezpieczanie łączności urządzeń.

Aby dowiedzieć się więcej na temat IoT Hub, zobacz [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Aby dowiedzieć się więcej o łączności urządzeń w usłudze Azure IoT Central, zobacz [łączność urządzeń](concepts-connectivity.md).

## <a name="data-stores"></a>Magazyny danych

Usługa Azure IoT Central przechowuje dane aplikacji w chmurze. Przechowywane dane aplikacji obejmują:

- Szablony urządzeń.
- Tożsamości urządzeń.
- Metadane urządzenia.
- Dane użytkowników i ról.

Usługa Azure IoT Central używa magazynu szeregów czasowych dla danych pomiarów wysyłanych z urządzeń. Dane szeregów czasowych z urządzeń używanych przez usługę analiz.

## <a name="analytics"></a>Analiza

Usługa analizy jest odpowiedzialna za generowanie niestandardowych danych raportowania wyświetlanych przez aplikację. Operator może [dostosować analizę](howto-create-analytics.md) wyświetlaną w aplikacji. Usługa Analytics została utworzona na podstawie [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) i przetwarza dane pomiarów wysyłane z urządzeń.

## <a name="rules-and-actions"></a>Reguły i akcje

[Reguły i akcje](howto-create-telemetry-rules.md) ściśle współpracują ze sobą w celu zautomatyzowania zadań w aplikacji. Konstruktor może definiować reguły na podstawie danych telemetrycznych urządzenia, takich jak temperatura przekraczająca określony próg. Usługa Azure IoT Central używa procesora strumienia, aby określić, kiedy są spełnione warunki reguły. Gdy warunek reguły jest spełniony, wyzwala akcję zdefiniowaną przez konstruktora. Na przykład akcja może wysłać wiadomość e-mail, aby poinformować inżyniera, że temperatura urządzenia jest zbyt wysoka.

## <a name="metadata-management"></a>Zarządzanie metadanymi

W aplikacji IoT Central platformy Azure szablony urządzeń określają zachowanie i możliwość typów urządzeń. Na przykład szablon urządzenia chłodziarkd określa dane telemetryczne wysyłane przez chłodziarkę do aplikacji.

![Architektura szablonu](media/concepts-architecture/template_architecture.png)

W szablonie urządzenia:

- **Pomiary** określają dane telemetryczne wysyłane przez urządzenie do aplikacji.
- **Ustawienia** określają konfiguracje, które mogą być ustawione przez operatora.
- **Właściwości** określają metadane, które mogą zostać ustawione przez operatora.
- **Reguły** automatyzują zachowanie aplikacji w oparciu o dane wysyłane z urządzenia.
- **Pulpity nawigacyjne** są dostosowywalnymi widokami urządzenia w aplikacji.

Aplikacja może mieć co najmniej jedno symulowane i rzeczywiste urządzenie w oparciu o każdy szablon urządzenia.

## <a name="data-export"></a>Eksport danych

W aplikacji IoT Central platformy Azure możesz [ciągle eksportować dane](howto-export-data-event-hubs-service-bus.md) do własnych Event Hubs platformy azure i Azure Service Bus wystąpień. Możesz również okresowo eksportować dane do konta usługi Azure Blob Storage. IoT Central mogą eksportować pomiary, urządzenia i szablony urządzeń.

## <a name="batch-device-updates"></a>Aktualizacje urządzeń wsadowych

W aplikacji IoT Central platformy Azure można [tworzyć i uruchamiać zadania](howto-run-a-job.md) w celu zarządzania podłączonymi urządzeniami. Te zadania umożliwiają wykonywanie aktualizacji zbiorczych do właściwości lub ustawień urządzenia albo uruchamianie poleceń. Na przykład można utworzyć zadanie, aby zwiększyć szybkość wentylatorów dla wielu maszyn z systemem automatu.

## <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)

[Administrator może zdefiniować reguły dostępu](howto-administer.md) dla aplikacji IoT Central platformy Azure przy użyciu wstępnie zdefiniowanych ról. Administrator może przypisywać użytkowników do ról, które określają obszary aplikacji, do których użytkownik ma dostęp.

## <a name="security"></a>Bezpieczeństwo

Funkcje zabezpieczeń w ramach usługi Azure IoT Central obejmują:

- Dane są szyfrowane podczas przesyłania i przechowywania.
- Uwierzytelnianie jest zapewnione przez Azure Active Directory lub konto Microsoft. Obsługiwane jest uwierzytelnianie dwuskładnikowe.
- Pełna izolacja dzierżawy.
- Zabezpieczenia na poziomie urządzenia.

## <a name="ui-shell"></a>Powłoka interfejsu użytkownika

Powłoka interfejsu użytkownika to nowoczesny, dynamiczny, oparty na przeglądarce HTML5 aplikację.
Administrator może dostosować interfejs użytkownika aplikacji, stosując niestandardowe motywy i modyfikując linki pomocy, aby wskazywały własne niestandardowe zasoby pomocy. Aby dowiedzieć się więcej o dostosowywaniu interfejsu użytkownika, zobacz Dostosowywanie artykułu dotyczącego [interfejsu użytkownika usługi Azure IoT Central](howto-customize-ui.md) .

Operator może tworzyć spersonalizowane pulpity nawigacyjne aplikacji. Można mieć kilka pulpitów nawigacyjnych, które wyświetlają różne dane i przełączają się między nimi.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już o architekturze platformy Azure IoT Central, sugerowanym następnym krokiem jest zapoznanie się z informacjami na temat [łączności urządzeń](concepts-connectivity.md) w usłudze Azure IoT Central.