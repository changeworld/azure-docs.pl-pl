---
title: Co to jest usługa Azure IoT Central | Microsoft Docs
description: Azure IoT Central to platforma aplikacji IoT, która upraszcza tworzenie rozwiązań IoT i pomaga w zmniejszeniu obciążenia i kosztów operacji zarządzania IoT oraz rozwoju. Ten artykuł zawiera omówienie funkcji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 27862ce11785f1b264d9f0238a9bae2684c87d02
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989930"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Co to jest usługa Azure IoT Central (funkcje w wersji zapoznawczej)?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Możliwości [Plug and Play IoT](../../iot-pnp/overview-iot-plug-and-play.md) na platformie Azure IoT Central są obecnie dostępne w publicznej wersji zapoznawczej. Nie należy używać [szablonu aplikacji](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) IoT Central Plug and Play IoT na potrzeby obciążeń produkcyjnych. W przypadku środowisk produkcyjnych używana jest aplikacja IoT Central utworzona na podstawie bieżącego, ogólnie dostępnego [szablonu aplikacji](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).

IoT Central to platforma aplikacji IoT, która zmniejsza obciążenie i koszty tworzenia i obsługi rozwiązań IoT klasy korporacyjnej oraz zarządzania nimi. Wybranie opcji kompilowania przy użyciu IoT Central umożliwia skoncentrowanie się na czasie, pieniędzy i energii na transformacje swojej firmy z danymi IoT, a nie tylko utrzymaniem i aktualizacją złożonej i ciągle rosnącej infrastruktury IoT.

Interfejs użytkownika sieci Web umożliwia monitorowanie warunków urządzenia, tworzenie reguł oraz zarządzanie milionami urządzeń i ich danych w całym cyklu życia. Ponadto umożliwia ona działanie w usłudze Device Insights poprzez rozszerzenie usługi IoT Intelligence na aplikacje biznesowe.

Ten artykuł zawiera opis IoT Central:

- Typowe osoby skojarzone z projektem
- Tworzenie aplikacji
- Łączenie urządzeń z aplikacją
- Zarządzanie aplikacją
- Możliwości Azure IoT Edge w IoT Central.
- Jak podłączyć urządzenia obsługiwane przez środowisko uruchomieniowe Azure IoT Edge do aplikacji.

## <a name="known-issues"></a>Znane problemy

> [!Note]
> Te znane problemy dotyczą tylko aplikacji IoT Central w wersji zapoznawczej.

- Ciągły eksport danych nie obsługuje formatu Avro (niezgodność).
- GEOJSON nie jest obecnie obsługiwany.
- Kafelek mapy nie jest obecnie obsługiwany.
- Zadania nie obsługują typów złożonych.
- Typy schematów tablicy nie są obsługiwane.
- Obsługiwane są tylko zestawy SDK dla urządzeń C i Node. js.
- Jest on dostępny tylko w lokalizacjach Stany Zjednoczone i Europy.
- Modele możliwości urządzeń muszą mieć wszystkie interfejsy zdefiniowane wewnętrznie w tym samym pliku.

## <a name="personas"></a>Osoby

Dokumentacja IoT Central obejmuje cztery osób, którzy współpracują z aplikacją IoT Central:

- _Konstruktor rozwiązań_ jest odpowiedzialny za Definiowanie typów urządzeń łączących się z aplikacją i dostosowywania aplikacji dla operatora.
- _Operator_ zarządza urządzeniami połączonymi z aplikacją.
- _Administrator_ jest odpowiedzialny za zadania administracyjne, takie jak zarządzanie [rolami i uprawnieniami użytkownika](howto-administer.md) w aplikacji.
- _Deweloper urządzenia_ tworzy kod, który jest uruchamiany na urządzeniu lub IoT Edge module podłączonym do aplikacji.

## <a name="create-your-iot-central-application"></a>Tworzenie aplikacji IoT Central

Jako Konstruktor rozwiązań możesz utworzyć niestandardowe rozwiązanie IoT hostowane w chmurze dla swojej organizacji za pomocą IoT Central. Niestandardowe rozwiązanie IoT zwykle obejmuje następujące elementy:

- Aplikacja oparta na chmurze, która odbiera dane telemetryczne z urządzeń i umożliwia zarządzanie tymi urządzeniami.
- Wiele urządzeń z uruchomionym niestandardowym kodem połączonych z aplikacją opartą na chmurze.

Możesz szybko wdrożyć nową aplikację IoT Central, a następnie dostosować ją do określonych wymagań w przeglądarce. Jako Konstruktor rozwiązań można utworzyć _szablon urządzenia_ dla urządzeń, które łączą się z aplikacją za pomocą narzędzi sieci Web. Szablon urządzenia to plan, który definiuje charakterystykę i zachowanie typu urządzenia, takiego jak:

- Dane telemetryczne wysyłane przez nią.
- Właściwości biznesowe, które może modyfikować operator.
- Właściwości urządzenia ustawiane przez urządzenie, które są tylko do odczytu w aplikacji.
- Właściwości, które są ustawiane przez operator, które określają zachowanie urządzenia.

Ten szablon urządzenia obejmuje następujące:

- _Model możliwości urządzenia_ , który opisuje możliwości implementowane przez urządzenie, takie jak dane telemetryczne, które wysyła, oraz właściwości, które zgłasza.
- Właściwości chmury, które nie są przechowywane na urządzeniu.
- Dostosowania, pulpity nawigacyjne i formularze, które są częścią aplikacji IoT Central.

### <a name="pricing"></a>Cennik

Aplikację IoT Central można utworzyć przy użyciu 7-dniowej bezpłatnej wersji próbnej lub użyć subskrypcji standardowej.

- Aplikacje tworzone za pomocą planu **bezpłatnego** są bezpłatne przez siedem dni i obsługują maksymalnie pięć urządzeń. Możesz przekonwertować je tak, aby używały standardowego planu cenowego w dowolnym momencie przed wygaśnięciem.
- W przypadku aplikacji tworzonych przy użyciu planu w **warstwie Standardowa** są naliczane opłaty za poszczególne urządzenia. możesz wybrać plan cenowy w **warstwie Standardowa 1** lub **standardowa 2** z dwoma bezpłatnymi urządzeniami. Dowiedz się więcej o planach cen bezpłatnych i standardowych na [stronie cennika usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).


### <a name="create-device-templates"></a>Tworzenie szablonów urządzeń

[Plug and Play IoT](../../iot-pnp/overview-iot-plug-and-play.md) umożliwia IoT Central integrację urządzeń bez konieczności pisania kodu urządzenia osadzonego. Na początku Plug and Play IoT jest schemat modelu możliwości urządzenia, który opisuje możliwości urządzeń. W aplikacji IoT Central w wersji zapoznawczej szablony urządzeń używają tych modeli możliwości urządzeń Plug and Play IoT.

Jako Konstruktor rozwiązań masz kilka opcji tworzenia szablonów urządzeń:

- Zaprojektuj szablon urządzenia w IoT Central a następnie Zaimplementuj jego model możliwości urządzenia w kodzie urządzenia.
- Zaimportuj model możliwości urządzenia z [wykazu urządzeń z certyfikatem platformy Azure dla IoT](https://aka.ms/iotdevcat) , a następnie Dodaj wszystkie właściwości, dostosowania i pulpity nawigacyjne, których potrzebuje IoT Central aplikacji.
- Utwórz model możliwości urządzenia przy użyciu programu Visual Studio Code. Zaimplementuj swój kod urządzenia z modelu i Połącz urządzenie z aplikacją IoT Central. IoT Central odnajduje model możliwości urządzenia z repozytorium i tworzy prosty szablon urządzenia.
- Utwórz model możliwości urządzenia przy użyciu programu Visual Studio Code. Zaimplementuj swój kod urządzenia z modelu. Ręcznie zaimportuj model możliwości urządzenia do aplikacji IoT Central, a następnie Dodaj wszystkie właściwości, dostosowania i pulpity nawigacyjne, których potrzebuje aplikacja IoT Central.

Jako Konstruktor rozwiązań możesz użyć IoT Central do wygenerowania kodu dla urządzeń testowych, aby sprawdzić poprawność szablonów urządzeń.

### <a name="customize-the-ui"></a>Dostosowywanie interfejsu użytkownika

Jako Konstruktor rozwiązań można także dostosować interfejs użytkownika aplikacji IoT Central dla operatorów, którzy są odpowiedzialni za codzienne korzystanie z aplikacji. Dostępne są dostosowania dostępne w konstruktorze rozwiązań:

- Definiowanie układu właściwości i ustawień w szablonie urządzenia.
- Konfigurowanie niestandardowych pulpitów nawigacyjnych ułatwiających operatorom odnajdywanie szczegółowych informacji i szybsze rozwiązywanie problemów.
- Konfigurowanie niestandardowych analiz w celu eksplorowania danych serii czasu na połączonych urządzeniach.

## <a name="connect-your-devices"></a>Podłącz swoje urządzenia

Po zdefiniowaniu przez konstruktora typów urządzeń, które mogą łączyć się z aplikacją, deweloper urządzenia tworzy kod do uruchamiania na urządzeniach. Deweloper urządzenia tworzy kod urządzenia przy użyciu zestawów [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) typu „open source” firmy Microsoft. Te zestawy SDK mają szeroką obsługę języków, platform i protokołów, aby sprostać potrzebom do łączenia urządzeń z aplikacją IoT Central. Zestawy SDK ułatwiają zaimplementowanie następujących możliwości urządzeń:

- Tworzenie bezpiecznego połączenia
- Wysyłanie danych telemetrycznych
- Zgłaszanie stanu
- Odbieranie aktualizacji konfiguracji

Aby uzyskać więcej informacji, zobacz wpis w blogu [Benefits of using the Azure IoT SDKs, and pitfalls to avoid if you don't](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

### <a name="azure-iot-edge-devices"></a>Urządzenia w usłudze Azure IoT Edge

A także urządzeń utworzonych przy użyciu [zestawów SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdks), można również łączyć [Azure IoT Edge urządzeń](../../iot-edge/about-iot-edge.md) z aplikacją IoT Central. Azure IoT Edge umożliwia uruchamianie analizy chmurowej i logiki niestandardowej bezpośrednio na urządzeniach IoT zarządzanych przez IoT Central. Środowisko uruchomieniowe IoT Edge umożliwia:

- Instaluje i aktualizuje pakiety robocze na urządzeniu.
- Utrzymuje standardy zabezpieczeń usługi Azure IoT Edge na urządzeniu.
- Zapewnia nieprzerwane działanie modułów usługi IoT Edge.
- Przesyła raporty o kondycji modułów do chmury na potrzeby zdalnego monitorowania.
- Zarządza komunikacją między podrzędnymi urządzeniami liścia usługi IoT Edge, między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem usługi IoT Edge a chmurą.

Aby uzyskać więcej informacji, zobacz [Azure IoT Edge urządzeń i IoT Central](./concepts-architecture.md#azure-iot-edge-devices).

## <a name="manage-your-application"></a>Zarządzanie aplikacją

Aplikacje IoT Central są w pełni hostowane przez firmę Microsoft, co zmniejsza koszty administracyjne związane z zarządzaniem aplikacjami.

Jako operator korzystasz z aplikacji IoT Central do zarządzania urządzeniami w rozwiązaniu IoT Central. Operatory wykonywania zadań, takich jak:

- Monitorowanie urządzeń połączonych z aplikacją
- Rozwiązywanie i korygowanie problemów z urządzeniami
- Aprowizowanie nowych urządzeń

Jako Konstruktor rozwiązań można definiować niestandardowe reguły i akcje, które działają w ramach przesyłania strumieniowego danych z połączonych urządzeń. Operator może włączyć lub wyłączyć te reguły na poziomie urządzenia, aby kontrolować i automatyzować zadania w aplikacji.

Administratorzy zarządzają dostępem do aplikacji przy użyciu [ról i uprawnień użytkownika](howto-administer.md).

## <a name="quotas"></a>Przydziały

Każda subskrypcja platformy Azure ma domyślne przydziały, które mogą mieć wpływ na zakres rozwiązania IoT. Obecnie IoT Central ogranicza liczbę aplikacji, które można wdrożyć w subskrypcji do 10. Jeśli musisz zwiększyć ten limit, skontaktuj się z [pomocą techniczną firmy Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz przegląd IoT Central, poniżej przedstawiono sugerowane następne kroki:

- Zapoznaj się z dostępnymi [technologiami i usługami platformy Azure służącymi do tworzenia rozwiązań IoT](../../iot-fundamentals/iot-services-and-technologies.md).
- Zapoznanie się z [interfejsem użytkownika usługi Azure IoT Central](overview-iot-central-tour.md).
- Rozpoczęcie pracy przez [utworzenie aplikacji usługi Azure IoT Central](quick-deploy-iot-central.md).
- Dowiedz się więcej o usłudze [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md)
- Dowiedz się, jak [utworzyć szablon urządzenia Azure IoT Edge](./tutorial-define-edge-device-type.md)
