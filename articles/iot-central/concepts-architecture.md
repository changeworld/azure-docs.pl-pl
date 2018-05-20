---
title: Koncepcje architektury w Azure IoT centralnej | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono podstawowe pojęcia dotyczące architektury centralnej IoT Azure
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: c97db3eb2c0fe1a5ec3c743ca75c595ec127823e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="azure-iot-central-architecture"></a>Architektura centralnego IoT Azure

Ten artykuł zawiera omówienie architektury IoT Centrum Microsoft Azure.

![Architektura najwyższego poziomu](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Urządzenia

Urządzenia wymiany danych z aplikacji Azure IoT centralnej. Urządzenie można:

- Wyślij pomiarów, takich jak telemetrii.
- Synchronizacja ustawień z aplikacją.

W usłudze Azure IoT Central dane, które urządzenia mogą wymieniać z aplikacji określono w szablonie urządzenia. Aby uzyskać więcej informacji o szablonach urządzenia, zobacz [zarządzania metadanych](#metadata-management).

Aby dowiedzieć się, jak łączyć się urządzenia do aplikacji Azure IoT centralnej, zobacz [łączność urządzeń](concepts-connectivity.md).

## <a name="cloud-gateway"></a>Brama chmury

Azure IoT centralnego używa Centrum IoT Azure jako bramy chmury, która umożliwia łączność urządzeń. Centrum IoT umożliwia:

- Wprowadzanie danych na dużą skalę w chmurze.
- Zarządzanie urządzeniami.
- Bezpieczna komunikacja urządzenia.

Aby dowiedzieć się więcej na temat Centrum IoT, zobacz [Centrum IoT Azure](https://docs.microsoft.com/azure/iot-hub/).

Aby dowiedzieć się więcej na temat połączenia urządzenia w centralnym IoT Azure, zobacz [łączność urządzeń](concepts-connectivity.md).

## <a name="data-stores"></a>Magazyny danych

Azure IoT centralnego przechowuje dane aplikacji w chmurze. Zawiera przechowywane dane aplikacji:

- Szablony urządzenia.
- Tożsamości urządzenia.
- Metadane urządzenia.
- Dane użytkownika i roli.

Azure IoT centralnego używa czas przechowywania serii danych pomiaru przesyłanych z urządzeń. Czas serii danych z urządzeń używanych przez usługi analytics.

## <a name="analytics"></a>Analiza

Usługa analizy jest odpowiedzialny za wygenerowanie danych raportowania niestandardowych wyświetlane w aplikacji. Operator może [dostosować analytics](howto-create-analytics.md) wyświetlany w aplikacji. Usługa analizy jest oparty na [Azure czas serii Insights](https://azure.microsoft.com/services/time-series-insights/) i przetwarza dane pomiarowe wysyłane z urządzenia.

## <a name="rules-and-actions"></a>Reguły i akcje

[Reguły i akcje](howto-create-telemetry-rules.md) ściśle współpracują w celu zautomatyzowania zadań w aplikacji. Konstruktora można zdefiniować reguły oparte na telemetrii urządzenia, takie jak temperatura przekracza określoną wartość progową. Azure IoT centralnego używa procesora strumienia w celu określenia, gdy warunki reguły są spełnione. Po spełnieniu warunku reguły wyzwala czynność zdefiniowana przez producenta. Na przykład akcji można wysłać wiadomość e-mail do powiadamiania inżyniera, że temperatura na urządzeniu jest zbyt duża.

## <a name="metadata-management"></a>Zarządzanie metadanych

W aplikacji Azure IoT centralnej szablony urządzenia określają zachowanie i możliwości typów urządzeń. Na przykład szablon urządzenia lodówce Określa dane telemetryczne, które lodówce wysyła do aplikacji.

![Architektura szablonu](media/concepts-architecture/template_architecture.png)

W szablonie urządzenia:

- **Pomiary** Określ telemetrii urządzenia wysyła do aplikacji.
- **Ustawienia** Określ konfiguracje, które można ustawić operatora.
- **Właściwości** Określ metadanych, które można ustawić operatora.
- **Reguły** zautomatyzować zachowania w aplikacji na podstawie danych wysyłane z urządzenia.
- **Pulpity nawigacyjne** są Konfigurowalne widoki urządzenia w aplikacji.

Aplikacja może mieć co najmniej jedno urządzenie symulowanych i rzeczywistych na podstawie szablonu każdego urządzenia.

## <a name="rbac"></a>Kontrola dostępu oparta na rolach

[Administrator może zdefiniować reguły dostępu](howto-administer.md) Azure IoT centralnej aplikacji przy użyciu wstępnie zdefiniowanych ról. Administrator może przypisać użytkowników do ról, które określają obszary aplikacji użytkownik ma dostęp do.

## <a name="security"></a>Bezpieczeństwo

Funkcje zabezpieczeń w ramach centralnej IoT Azure:

- Dane są szyfrowane podczas przesyłania i magazynowane.
- Uwierzytelnianie jest obsługiwane przez usługi Azure Active Directory lub Account firmy Microsoft. Uwierzytelnianie dwuskładnikowe jest obsługiwana.
- Izolacja pełne dzierżawy.
- Zabezpieczenia na poziomie urządzenia.

## <a name="ui-shell"></a>Powłoki interfejsu użytkownika

Powłoki interfejsu użytkownika to nowoczesny, reakcje, HTML5 przeglądarki aplikacja.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz już o architekturze Azure IoT centralnej, sugerowane następnym krokiem jest Dowiedz się więcej o [łączność urządzeń](concepts-connectivity.md) w Azure IoT centralnej.