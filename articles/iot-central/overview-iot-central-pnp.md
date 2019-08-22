---
title: Co to jest usługa Azure IoT Central | Microsoft Docs
description: Usługa Azure IoT Central to kompleksowe rozwiązanie SaaS, którego można użyć do kompilowania niestandardowego rozwiązania IoT i zarządzania nim. Ten artykuł zawiera omówienie funkcji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 0675be988e7e9560560dd07338563c9fa22fcb4c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878292"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Co to jest usługa Azure IoT Central (funkcje w wersji zapoznawczej)?

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Możliwości [Plug and Play IoT](https://aka.ms/iot-pnp-docs) na platformie Azure IoT Central są obecnie dostępne w publicznej wersji zapoznawczej. Nie należy używać Plug and Play IoT włączonych IoT Central aplikacji dla obciążeń produkcyjnych. W przypadku środowisk produkcyjnych używana jest aplikacja IoT Central utworzona na podstawie bieżącego, ogólnie dostępnego szablonu aplikacji.

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

## <a name="known-issues"></a>Znane problemy

> [!Note]
> Te znane problemy dotyczą tylko aplikacji IoT Central w wersji zapoznawczej.

- Reguły nie obsługują wszystkich akcji (tylko wiadomości e-mail).
- W przypadku typów złożonych — reguły, analiza i grupy urządzeń nie są obsługiwane.
- Ciągły eksport danych nie obsługuje formatu Avro (niezgodność).
- Symulowane urządzenia nie obsługują wszystkich typów złożonych.
- GEOJSON nie jest obecnie obsługiwany.
- Kafelek mapy nie jest obecnie obsługiwany.
- Zadania nie obsługują typów złożonych.
- Typy schematów tablicy nie są obsługiwane.
- Eksportowanie szablonu aplikacji i kopiowanie aplikacji nie są obsługiwane.
- Obsługiwane są tylko zestawy SDK dla urządzeń C i Node. js.
- Jest on dostępny tylko w wybranych regionach.

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
- Właściwości ustawione przez operatora, który określa zachowanie urządzenia.

Ten szablon urządzenia obejmuje następujące:

- _Model możliwości urządzenia_ , który opisuje możliwości implementowane przez urządzenie, takie jak dane telemetryczne, które wysyła, oraz właściwości, które zgłasza.
- Właściwości chmury, które nie są przechowywane na urządzeniu.
- Dostosowania, pulpity nawigacyjne i formularze, które są częścią aplikacji IoT Central.

### <a name="create-device-templates"></a>Tworzenie szablonów urządzeń

[Plug and Play IoT](https://aka.ms/iot-pnp-docs) umożliwia IoT Central integrację urządzeń bez konieczności pisania kodu urządzenia osadzonego. Podstawą Plug and Play IoT jest schemat modelu możliwości urządzenia, który opisuje możliwości urządzeń. W aplikacji IoT Central w wersji zapoznawczej szablony urządzeń używają tych modeli możliwości urządzeń Plug and Play IoT.

Jako Konstruktor można utworzyć szablony urządzeń za pomocą kilku opcji:

- Zaprojektuj szablon urządzenia w IoT Central a następnie Zaimplementuj jego model możliwości urządzenia w kodzie urządzenia.
- Zaimportuj model możliwości urządzenia z [wykazu urządzeń z certyfikatem platformy Azure dla IoT](https://aka.ms/iotdevcat) , a następnie Dodaj wszystkie właściwości, dostosowania i pulpity nawigacyjne, których potrzebuje IoT Central aplikacji.
- Utwórz model możliwości urządzenia przy użyciu programu Visual Studio Code. Zaimplementuj swój kod urządzenia z modelu i Połącz urządzenie z aplikacją IoT Central. IoT Central odnajduje model możliwości urządzenia z repozytorium i tworzy prosty szablon urządzenia.
- Utwórz model możliwości urządzenia przy użyciu programu Visual Studio Code. Zaimplementuj swój kod urządzenia z modelu. Ręcznie zaimportuj model możliwości urządzenia do aplikacji IoT Central, a następnie Dodaj wszystkie właściwości, dostosowania i pulpity nawigacyjne, których potrzebuje aplikacja IoT Central.

Jako Konstruktor można użyć IoT Central do wygenerowania kodu dla urządzeń testowych, aby sprawdzić poprawność szablonów urządzeń.

### <a name="customize-the-ui"></a>Dostosowywanie interfejsu użytkownika

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

Administratorzy zarządzają dostępem do aplikacji przy użyciu [ról i uprawnień użytkownika](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Następne kroki

Oto następne sugerowane kroki po zapoznaniu się z omówieniem usługi Azure IoT Central:

- Poznanie różnic między [usługą Azure IoT Central i akceleratorami rozwiązań Azure IoT](overview-iot-options.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Zapoznanie się z [interfejsem użytkownika usługi Azure IoT Central](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Rozpoczęcie pracy przez [utworzenie aplikacji usługi Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Wykonanie sekwencji samouczków dotyczących następujących kwestii:
  - [Tworzenie szablonu urządzenia jako konstruktor](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Dodawanie reguł w celu zautomatyzowania rozwiązania jako konstruktor](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Monitorowanie urządzeń jako operator](tutorial-monitor-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Jako operator Dodaj urządzenie do rozwiązania](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- Dowiedz się więcej o usłudze [IoT Plug and Play](https://aka.ms/iot-pnp-docs)
