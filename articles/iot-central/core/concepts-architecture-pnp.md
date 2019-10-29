---
title: Koncepcje architektury na platformie Azure IoT Central | Microsoft Docs
description: W tym artykule przedstawiono kluczowe pojęcia związane z architekturą IoT Central platformy Azure
author: dominicbetts
ms.author: dobett
ms.date: 10/15/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3dacea5c1d77bfead85e42ae312bfafbe70d8ca1
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990376"
---
# <a name="azure-iot-central-architecture-preview-features"></a>Architektura usługi Azure IoT Central (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ten artykuł zawiera omówienie architektury IoT Central Microsoft Azure.

![Architektura najwyższego poziomu](media/concepts-architecture-pnp/architecture.png)

## <a name="devices"></a>Urządzenia

Urządzenia wymieniają dane z aplikacją IoT Central platformy Azure. Urządzenie może:

- Wysyłaj pomiary, takie jak Telemetria.
- Zsynchronizuj ustawienia z aplikacją.

Na platformie Azure IoT Central dane, które urządzenie może wymieniać z aplikacją, są określone w szablonie urządzenia. Aby uzyskać więcej informacji na temat szablonów urządzeń, zobacz [Zarządzanie metadanymi](#metadata-management).

Aby dowiedzieć się więcej o tym, jak urządzenia nawiązują połączenie z aplikacją IoT Central platformy Azure, zobacz [łączność urządzeń](overview-iot-central-get-connected-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="cloud-gateway"></a>Brama chmury

Usługa Azure IoT Central korzysta z usługi Azure IoT Hub jako bramy w chmurze, która umożliwia łączność z urządzeniem. IoT Hub umożliwia:

- Pozyskiwanie danych na dużą skalę w chmurze.
- Zarządzanie urządzeniami.
- Zabezpieczanie łączności urządzeń.

Aby dowiedzieć się więcej na temat IoT Hub, zobacz [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Aby dowiedzieć się więcej o łączności urządzeń w usłudze Azure IoT Central, zobacz [łączność urządzeń](overview-iot-central-get-connected-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="data-stores"></a>Magazyny danych

Usługa Azure IoT Central przechowuje dane aplikacji w chmurze. Przechowywane dane aplikacji obejmują:

- Szablony urządzeń.
- Tożsamości urządzeń.
- Metadane urządzenia.
- Dane użytkowników i ról.

Usługa Azure IoT Central używa magazynu szeregów czasowych dla danych pomiarów wysyłanych z urządzeń. Dane szeregów czasowych z urządzeń używanych przez usługę analiz.

## <a name="analytics"></a>Analiza

Usługa analizy jest odpowiedzialna za generowanie niestandardowych danych raportowania wyświetlanych przez aplikację. Operator może [dostosować analizę](howto-create-analytics.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) wyświetlaną w aplikacji. Usługa Analytics została utworzona na podstawie [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) i przetwarza dane pomiarów wysyłane z urządzeń.

## <a name="rules-and-actions"></a>Reguły i akcje

[Reguły i akcje](howto-create-telemetry-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) ściśle współpracują ze sobą w celu zautomatyzowania zadań w aplikacji. Konstruktor może definiować reguły na podstawie danych telemetrycznych urządzenia, takich jak temperatura przekraczająca określony próg. Usługa Azure IoT Central używa procesora strumienia, aby określić, kiedy są spełnione warunki reguły. Gdy warunek reguły jest spełniony, wyzwala akcję zdefiniowaną przez konstruktora. Na przykład akcja może wysłać wiadomość e-mail, aby poinformować inżyniera, że temperatura urządzenia jest zbyt wysoka.

## <a name="metadata-management"></a>Zarządzanie metadanymi

W aplikacji IoT Central platformy Azure szablony urządzeń określają zachowanie i możliwość typów urządzeń. Na przykład szablon urządzenia chłodziarkd określa dane telemetryczne wysyłane przez chłodziarkę do aplikacji.

![Architektura szablonu](media/concepts-architecture-pnp/template-architecture.png)

W IoT Central szablonu urządzenia aplikacji w wersji zapoznawczej:

- **Modele możliwości urządzeń** określają możliwości urządzenia, takie jak dane telemetryczne, które wysyła, właściwości definiujące stan urządzenia i polecenia, na które odpowiada urządzenie. Możliwości urządzenia są zorganizowane w jeden lub więcej interfejsów. Aby uzyskać więcej informacji na temat modeli możliwości urządzeń, zobacz dokumentację usługi [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) .
- **Właściwości chmury** określają właściwości IoT Central magazynów dla urządzenia. Te właściwości są przechowywane tylko w IoT Central i nigdy nie są wysyłane do urządzenia.
- **Widoki** umożliwiają określanie pulpitów nawigacyjnych i formularzy tworzonych przez konstruktora, aby umożliwić operatorowi monitorowanie urządzeń i zarządzanie nimi.
- **Dostosowania** pozwalają konstruktorowi zastąpić niektóre definicje w modelu możliwości urządzenia, aby zwiększyć ich znaczenie do aplikacji IoT Central.

Aplikacja może mieć co najmniej jedno symulowane i rzeczywiste urządzenie w oparciu o każdy szablon urządzenia.

## <a name="data-export"></a>Eksport danych

W aplikacji IoT Central platformy Azure możesz [ciągle eksportować dane](howto-export-data-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) do własnych wystąpień platformy Azure Event Hubs i Azure Service Bus. Możesz również okresowo eksportować dane do konta usługi Azure Blob Storage. IoT Central mogą eksportować pomiary, urządzenia i szablony urządzeń.

## <a name="batch-device-updates"></a>Aktualizacje urządzeń wsadowych

W aplikacji IoT Central platformy Azure można [tworzyć i uruchamiać zadania](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) w celu zarządzania podłączonymi urządzeniami. Te zadania umożliwiają wykonywanie aktualizacji zbiorczych do właściwości lub ustawień urządzenia albo uruchamianie poleceń. Na przykład można utworzyć zadanie, aby zwiększyć szybkość wentylatorów dla wielu maszyn z systemem automatu.

## <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)

[Administrator może zdefiniować reguły dostępu](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) dla aplikacji IoT Central platformy Azure przy użyciu wstępnie zdefiniowanych ról. Administrator może przypisywać użytkowników do ról, które określają obszary aplikacji, do których użytkownik ma dostęp.

## <a name="security"></a>Zabezpieczenia

Funkcje zabezpieczeń w ramach usługi Azure IoT Central obejmują:

- Dane są szyfrowane podczas przesyłania i przechowywania.
- Uwierzytelnianie jest zapewnione przez Azure Active Directory lub konto Microsoft. Obsługiwane jest uwierzytelnianie dwuskładnikowe.
- Pełna izolacja dzierżawy.
- Zabezpieczenia na poziomie urządzenia.

## <a name="ui-shell"></a>Powłoka interfejsu użytkownika

Powłoka interfejsu użytkownika to nowoczesny, dynamiczny, oparty na przeglądarce HTML5 aplikację.
Administrator może dostosować interfejs użytkownika aplikacji, stosując niestandardowe motywy i modyfikując linki pomocy, aby wskazywały własne niestandardowe zasoby pomocy. Aby dowiedzieć się więcej o dostosowywaniu interfejsu użytkownika, zobacz Dostosowywanie artykułu dotyczącego [interfejsu użytkownika usługi Azure IoT Central](howto-customize-ui.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

Operator może tworzyć spersonalizowane pulpity nawigacyjne aplikacji. Można mieć kilka pulpitów nawigacyjnych, które wyświetlają różne dane i przełączają się między nimi.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już o architekturze platformy Azure IoT Central, sugerowanym następnym krokiem jest zapoznanie się z informacjami na temat [łączności urządzeń](overview-iot-central-get-connected-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) w usłudze Azure IoT Central.