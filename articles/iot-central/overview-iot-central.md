---
title: Co to jest usługa Azure IoT Central | Microsoft Docs
description: Usługa Azure IoT Central to kompleksowe rozwiązanie SaaS, które umożliwia utworzenie niestandardowego rozwiązania IoT i zarządzanie nim. Ten artykuł zawiera omówienie funkcji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 8c369ab05059e57f2e2a98339052c27292ac7c0d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628780"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Co to jest usługa Azure IoT Central?

Usługa Microsoft Azure IoT Central to w pełni zarządzane rozwiązanie IoT typu „oprogramowanie jako usługa”, które ułatwia tworzenie produktów łączących świat fizyczny i cyfrowy. Wizję połączonych produktów można realizować w następujący sposób:

- Przez uzyskiwanie nowych szczegółowych danych z połączonych urządzeń w celu oferowania klientom lepszych produktów i zwiększenia jakości obsługi
- Przez tworzenie nowych możliwości biznesowych dla organizacji

Usługa Azure IoT Central eliminuje wiele problemów z zarządzaniem rozwiązaniem IoT znanych z typowych projektów IoT przez:

- zmniejszenie obciążenia związanego z zarządzaniem,
- obniżenie kosztów i nakładów operacyjnych,
- ułatwienie dostosowywania aplikacji przy użyciu następujących elementów:
  - Wiodące w branży technologie, takie jak [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) i [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)
  - Funkcje zabezpieczeń klasy korporacyjnej, takie jak kompleksowe szyfrowanie

Poniższy klip wideo zawiera omówienie usługi Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

W dalszej części tego artykułu przedstawiono następujące kwestie związane z usługą Azure IoT Central:

- Typowe osoby skojarzone z projektem
- Tworzenie aplikacji
- Łączenie urządzeń z aplikacją
- Zarządzanie aplikacją

## <a name="personas"></a>Osoby

W dokumentacji usługi Azure IoT Central wyszczególniono cztery typowe osoby, które korzystają z aplikacji usługi Azure IoT Central:

- _Konstruktor_ jest odpowiedzialny za definiowanie typów urządzeń, które łączą się z aplikacją, i dostosowywanie aplikacji dla operatora.
- _Operator_ zarządza urządzeniami połączonymi z aplikacją.
- _Administrator_ jest odpowiedzialny za zadania administracyjne, takie jak zarządzanie użytkownikami i rolami w aplikacji.
- _Deweloper urządzenia_ tworzy kod, który działa na urządzeniu połączonym z aplikacją.

## <a name="create-your-azure-iot-central-application"></a>Tworzenie aplikacji usługi Azure IoT Central

Konstruktor używa usługi Azure IoT Central, aby utworzyć dla organizacji niestandardowe rozwiązanie IoT hostowane w chmurze. Niestandardowe rozwiązanie IoT zwykle obejmuje następujące elementy:

- Aplikacja oparta na chmurze, która odbiera dane telemetryczne z urządzeń i umożliwia zarządzanie tymi urządzeniami.
- Wiele urządzeń z uruchomionym niestandardowym kodem połączonych z aplikacją opartą na chmurze.

Możesz szybko wdrożyć nową aplikację usługi Azure IoT Central i dostosować ją do określonych wymagań w przeglądarce. Konstruktor usługi Azure IoT Central może utworzyć _szablon urządzenia_ za pomocą narzędzi internetowych dla urządzeń łączących się z aplikacją. Szablon urządzenia to plan modelu urządzenia współdzielony przez wszystkie urządzenia utworzone za pomocą szablonu. Szablon urządzenia definiuje cechy i zachowanie typu urządzenia, takie jak:

- Wysyłane dane telemetryczne.
- Właściwości biznesowe, które może modyfikować operator.
- Właściwości urządzenia ustawiane przez urządzenie, które są tylko do odczytu w aplikacji.
- Progi odpowiedzi aplikacji.
- Ustawienia określające zachowanie urządzenia.

Możesz od razu testować szablony urządzeń i aplikacje, używając symulowanych danych generowanych przez usługę Azure IoT Central.

Konstruktor może też dostosować interfejs użytkownika aplikacji usługi Azure IoT Central dla operatorów, którzy są odpowiedzialni za bieżące korzystanie z aplikacji. Konstruktor może wprowadzać m.in. następujące dostosowania:

- Definiowanie układu właściwości i ustawień w szablonie urządzenia.
- Konfigurowanie niestandardowych pulpitów nawigacyjnych ułatwiających operatorom odnajdywanie szczegółowych informacji i szybsze rozwiązywanie problemów.
- Konfigurowanie niestandardowych analiz w celu eksplorowania danych serii czasu na połączonych urządzeniach.

## <a name="connect-your-devices"></a>Łączenie urządzeń

Po zdefiniowaniu przez konstruktora typów urządzeń, które mogą łączyć się z aplikacją, deweloper urządzenia tworzy kod do uruchamiania na urządzeniach. Deweloper urządzenia tworzy kod urządzenia przy użyciu zestawów [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) typu „open source” firmy Microsoft. Te zestawy SDK oferują szeroką obsługę języków, platform i protokołów, aby umożliwić łączenie urządzeń z aplikacją usługi Azure IoT Central. Zestawy SDK ułatwiają wykonywanie następujących zadań na urządzeniu połączonym z usługą Azure IoT Central:

- Tworzenie bezpiecznego połączenia
- Wysyłanie danych telemetrycznych
- Zgłaszanie stanu
- Odbieranie aktualizacji konfiguracji

Aby uzyskać więcej informacji, zobacz wpis w blogu [Benefits of using the Azure IoT SDKs, and pitfalls to avoid if you don't](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="manage-your-application"></a>Zarządzanie aplikacją

Aplikacje usługi Azure IoT Central są w pełni hostowane przez firmę Microsoft, co zmniejsza obciążenie administracyjne związane z zarządzaniem aplikacjami.

Operator używa aplikacji usługi Azure IoT Central do zarządzania urządzeniami w rozwiązaniu usługi Azure IoT Central. Operatorzy mogą wykonywać m.in. następujące zadania:

- Monitorowanie urządzeń połączonych z aplikacją
- Rozwiązywanie i korygowanie problemów z urządzeniami
- Aprowizowanie nowych urządzeń

Konstruktor może definiować niestandardowe reguły i akcje dla danych przesyłanych strumieniowo na poziomie szablonu urządzenia. Operator może włączyć lub wyłączyć te reguły na poziomie urządzenia, aby kontrolować i automatyzować zadania w aplikacji.

Administratorzy mogą zarządzać dostępem do aplikacji za pomocą [uprawnień i ról użytkowników](howto-administer.md).

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
