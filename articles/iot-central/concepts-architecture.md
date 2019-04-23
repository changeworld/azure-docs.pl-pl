---
title: Architektury pojęcia w usłudze Azure IoT Central | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono podstawowe pojęcia dotyczące architektury usługi Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 4f4b917808f4973dc83294391f58d7e0e2d01c4c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798814"
---
# <a name="azure-iot-central-architecture"></a>Architektura usługi Azure IoT Central

Ten artykuł zawiera omówienie architektury systemu Microsoft Azure IoT Central.

![Architektura najwyższego poziomu](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Urządzenia

Urządzenia wymiany danych z aplikacją usługi Azure IoT Central. Urządzenie wykonywać następujące czynności:

- Wysyłanie pomiarów, takich jak dane telemetryczne.
- Synchronizacja ustawień z aplikacją.

W usłudze Azure IoT Central dane, które urządzenia mogą wymieniać się ze swoją aplikacją określono w szablonie urządzenia. Aby uzyskać więcej informacji na temat szablonów urządzeń zobacz [zarządzania metadanych](#metadata-management).

Aby dowiedzieć się więcej na temat sposób łączenia urządzeń z aplikacją usługi Azure IoT Central, zobacz [łączność urządzeń](concepts-connectivity.md).

## <a name="cloud-gateway"></a>Brama chmury

Usługa Azure IoT Central używa usługi Azure IoT Hub jako brama chmury, który umożliwia łączność urządzeń. Usługa IoT Hub umożliwia:

- Pozyskiwanie danych na dużą skalę w chmurze.
- Zarządzanie urządzeniami.
- Zabezpiecz łączność urządzeń.

Aby dowiedzieć się więcej na temat usługi IoT Hub, zobacz [usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Aby dowiedzieć się więcej na temat łączność urządzeń w usłudze Azure IoT Central, zobacz [łączność urządzeń](concepts-connectivity.md).

## <a name="data-stores"></a>Magazyny danych

Usługa Azure IoT Central przechowuje dane aplikacji w chmurze. Aplikacja — dane przechowywane obejmuje:

- Szablony urządzenia.
- Tożsamości urządzeń.
- Metadane urządzenia.
- Dane użytkownika i roli.

Azure IoT Central używa czas przechowywania serii danych pomiaru wysyłanych z urządzenia. Dane szeregów czasowych z urządzeń używanych w usłudze analytics.

## <a name="analytics"></a>Analiza

Usługa analizy jest odpowiedzialny za wygenerowanie niestandardowe dane raportowania, które zawiera aplikację. Operator może [dostosować analizami](howto-create-analytics.md) wyświetlaną w aplikacji. Usługa analizy jest wbudowana w górnej części [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) i przetwarza dane pomiarowe wysyłane z urządzeń.

## <a name="rules-and-actions"></a>Reguły i akcje

[Reguły i akcje](howto-create-telemetry-rules.md) ściśle współpracują w celu zautomatyzowania zadań w ramach aplikacji. Konstruktor można zdefiniować reguły na podstawie danych telemetrycznych urządzenia, takich jak temperatury przekracza określoną wartość progową. Azure IoT Central używa procesora strumienia, aby określić, gdy są spełnione warunki reguły. Po spełnieniu warunku reguły wyzwala akcję zdefiniowany przez konstruktora. Na przykład akcji można Wyślij wiadomość e-mail do powiadomienie inżyniera, że temperatura w urządzeniu jest zbyt duży.

## <a name="metadata-management"></a>Zarządzanie metadanych

W aplikacji usługi Azure IoT Central szablonów urządzeń definiują zachowanie i możliwości typów urządzeń. Na przykład szablon urządzenia lodówki określa danych telemetrycznych, jakie lodówki wysyła do aplikacji.

![Architektura szablonu](media/concepts-architecture/template_architecture.png)

W szablonie urządzenia:

- **Pomiary** określić dane telemetryczne urządzenia wysyła do aplikacji.
- **Ustawienia** Określ konfiguracje, które można ustawić operatora.
- **Właściwości** Określ metadanych, które można ustawić operatora.
- **Reguły** zautomatyzować zachowania w aplikacji na podstawie danych wysyłanych z urządzenia.
- **Pulpity nawigacyjne** są możliwe do dostosowania widoków urządzenia w aplikacji.

Aplikacja może mieć co najmniej jedno urządzenie symulowane i rzeczywistego na podstawie szablonu każdego urządzenia.

## <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)

[Administrator może zdefiniować reguły dostępu](howto-administer.md) usługi Azure IoT Central aplikacji za pomocą wstępnie zdefiniowanych ról. Administrator może przypisać użytkowników do ról, które określają, jakie obszary aplikacji użytkownik ma dostęp do.

## <a name="security"></a>Bezpieczeństwo

Funkcje zabezpieczeń w usłudze Azure IoT Central:

- Dane są szyfrowane podczas przesyłania i przechowywanych.
- Uwierzytelnianie jest obsługiwane przez usługę Azure Active Directory lub Account firmy Microsoft. Uwierzytelnianie dwuskładnikowe jest obsługiwane.
- Izolacja pełną dzierżawy.
- Zabezpieczenia na poziomie urządzenia.

## <a name="ui-shell"></a>Powłoka interfejsu użytkownika

Powłoka interfejsu użytkownika jest nowoczesnych, interaktywnych, oparte na przeglądarce aplikacji HTML5.

## <a name="next-steps"></a>Kolejne kroki

Skoro wiesz już o architekturze usługi Azure IoT Central, sugerowane następnym krokiem jest więcej informacji na temat [łączność urządzeń](concepts-connectivity.md) w usłudze Azure IoT Central.