---
title: Co to jest usługa Azure IoT Central | Microsoft Docs
description: Usługa Azure IoT Central to kompleksowe rozwiązanie SaaS, którego można użyć do kompilowania niestandardowego rozwiązania IoT i zarządzania nim. Ten artykuł zawiera omówienie funkcji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 04/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: ff212e77b6fcaaa057c22c420cce82e7ea6c82e6
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877290"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Co to jest usługa Azure IoT Central?

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Azure IoT Central to w pełni zarządzane rozwiązanie typu "oprogramowanie jako usługa" IoT, które ułatwia tworzenie produktów łączących fizyczne i cyfrowe środowiska światowe. Wizję połączonych produktów można realizować w następujący sposób:

- Przez uzyskiwanie nowych szczegółowych danych z połączonych urządzeń w celu oferowania klientom lepszych produktów i zwiększenia jakości obsługi
- Przez tworzenie nowych możliwości biznesowych dla organizacji

Platforma Azure IoT Central w porównaniu do typowego projektu IoT:

- Zmniejsza obciążenie związane z zarządzaniem.
- Zmniejsza koszty operacyjne i rozrzuty.
- Ułatwia dostosowanie aplikacji podczas pracy z:
  - Wiodące w branży technologie, takie jak [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) i [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)
  - Funkcje zabezpieczeń klasy korporacyjnej, takie jak kompleksowe szyfrowanie

Poniższy klip wideo zawiera omówienie usługi Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

Ten artykuł zawiera opis dla IoT Central platformy Azure:

- Typowe osoby skojarzone z projektem
- Tworzenie aplikacji
- Łączenie urządzeń z aplikacją
- Zarządzanie aplikacją

## <a name="personas"></a>Osoby

Dokumentacja usługi Azure IoT Central obejmuje cztery osób, które współpracują z aplikacją IoT Central platformy Azure:

- _Konstruktor_ jest odpowiedzialny za definiowanie typów urządzeń, które łączą się z aplikacją, i dostosowywanie aplikacji dla operatora.
- _Operator_ zarządza urządzeniami połączonymi z aplikacją.
- _Administrator_ jest odpowiedzialny za zadania administracyjne, takie jak zarządzanie użytkownikami i rolami w aplikacji.
- _Deweloper urządzenia_ tworzy kod, który działa na urządzeniu połączonym z aplikacją.

## <a name="create-your-azure-iot-central-application"></a>Tworzenie aplikacji usługi Azure IoT Central

Konstruktor używa usługi Azure IoT Central, aby utworzyć dla organizacji niestandardowe rozwiązanie IoT hostowane w chmurze. Niestandardowe rozwiązanie IoT zwykle obejmuje następujące elementy:

- Aplikacja oparta na chmurze, która odbiera dane telemetryczne z urządzeń i umożliwia zarządzanie tymi urządzeniami.
- Wiele urządzeń z uruchomionym niestandardowym kodem połączonych z aplikacją opartą na chmurze.

Możesz szybko wdrożyć nową aplikację IoT Central platformy Azure, a następnie dostosować ją do określonych wymagań w przeglądarce. Jako Konstruktor należy użyć narzędzi sieci Web do utworzenia _szablonu urządzenia_ dla urządzeń, które łączą się z Twoją aplikacją. Szablon urządzenia to plan, który definiuje charakterystykę i zachowanie typu urządzenia, takiego jak:

- Dane telemetryczne wysyłane przez nią.
- Właściwości biznesowe, które może modyfikować operator.
- Właściwości urządzenia ustawiane przez urządzenie, które są tylko do odczytu w aplikacji.
- Progi, do której reaguje aplikacja.
- Ustawienia określające zachowanie urządzenia.

Możesz od razu testować szablony urządzeń i aplikacje, używając symulowanych danych generowanych przez usługę Azure IoT Central.

Konstruktor może też dostosować interfejs użytkownika aplikacji usługi Azure IoT Central dla operatorów, którzy są odpowiedzialni za bieżące korzystanie z aplikacji. Konstruktor może wprowadzać m.in. następujące dostosowania:

- Definiowanie układu właściwości i ustawień w szablonie urządzenia.
- Konfigurowanie niestandardowych pulpitów nawigacyjnych ułatwiających operatorom odnajdywanie szczegółowych informacji i szybsze rozwiązywanie problemów.
- Konfigurowanie niestandardowych analiz w celu eksplorowania danych serii czasu na połączonych urządzeniach.

## <a name="connect-your-devices"></a>Łączenie urządzeń

Po zdefiniowaniu przez konstruktora typów urządzeń, które mogą łączyć się z aplikacją, deweloper urządzenia tworzy kod do uruchamiania na urządzeniach. Deweloper urządzenia tworzy kod urządzenia przy użyciu zestawów [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) typu „open source” firmy Microsoft. Te zestawy SDK oferują szeroką obsługę języków, platform i protokołów, aby umożliwić łączenie urządzeń z aplikacją usługi Azure IoT Central. Zestawy SDK ułatwiają zaimplementowanie następujących możliwości urządzeń:

- Tworzenie bezpiecznego połączenia
- Wysyłanie danych telemetrycznych
- Zgłaszanie stanu
- Odbieranie aktualizacji konfiguracji

Aby uzyskać więcej informacji, zobacz wpis w blogu [Benefits of using the Azure IoT SDKs, and pitfalls to avoid if you don't](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="manage-your-application"></a>Zarządzanie aplikacją

Aplikacje usługi Azure IoT Central są w pełni hostowane przez firmę Microsoft, co zmniejsza obciążenie administracyjne związane z zarządzaniem aplikacjami.

Operator używa aplikacji usługi Azure IoT Central do zarządzania urządzeniami w rozwiązaniu usługi Azure IoT Central. Operatory wykonywania zadań, takich jak:

- Monitorowanie urządzeń połączonych z aplikacją
- Rozwiązywanie i korygowanie problemów z urządzeniami
- Aprowizowanie nowych urządzeń

Jako Konstruktor można definiować niestandardowe reguły i akcje, które działają w ramach przesyłania strumieniowego danych z połączonych urządzeń. Operator może włączyć lub wyłączyć te reguły na poziomie urządzenia, aby kontrolować i automatyzować zadania w aplikacji.

Administratorzy zarządzają dostępem do aplikacji przy użyciu [ról i uprawnień użytkownika](howto-administer.md).

## <a name="next-steps"></a>Następne kroki

Oto następne sugerowane kroki po zapoznaniu się z omówieniem usługi Azure IoT Central:

- Poznanie różnic między [usługą Azure IoT Central i akceleratorami rozwiązań Azure IoT](overview-iot-options.md).
- Zapoznanie się z [interfejsem użytkownika usługi Azure IoT Central](overview-iot-central-tour.md).
- Rozpoczęcie pracy przez [utworzenie aplikacji usługi Azure IoT Central](quick-deploy-iot-central.md).
- Wykonanie sekwencji samouczków dotyczących następujących kwestii:
  - [Tworzenie szablonu urządzenia jako konstruktor](tutorial-define-device-type.md)
  - [Dodawanie reguł w celu zautomatyzowania rozwiązania jako konstruktor](tutorial-configure-rules.md)
  - [Dostosowywanie aplikacji dla operatorów jako konstruktor](tutorial-customize-operator.md)
  - [Monitorowanie urządzeń jako operator](tutorial-monitor-devices.md)
  - [Dodawanie rzeczywistego urządzenia do rozwiązania jako operator](tutorial-add-device.md)
  - [Tworzenie kodu dla urządzeń jako deweloper urządzenia](tutorial-add-device.md#prepare-the-client-code)
