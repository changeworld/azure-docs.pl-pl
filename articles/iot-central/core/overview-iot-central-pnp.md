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
ms.openlocfilehash: f8493794df2ec0c6123a85cc5405b18fbe1f6786
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957811"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Co to jest usługa Azure IoT Central (funkcje w wersji zapoznawczej)?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Możliwości [Plug and Play IoT](../../iot-pnp/overview-iot-plug-and-play.md) na platformie Azure IoT Central są obecnie dostępne w publicznej wersji zapoznawczej. Nie należy używać Plug and Play IoT włączonych IoT Central aplikacji dla obciążeń produkcyjnych. W przypadku środowisk produkcyjnych używana jest aplikacja IoT Central utworzona na podstawie bieżącego, ogólnie dostępnego szablonu aplikacji.

Azure IoT Central to platforma aplikacji IoT, która zmniejsza obciążenie i koszty tworzenia i obsługi rozwiązań IoT klasy korporacyjnej oraz zarządzania nimi. Wybranie opcji kompilowania za pomocą usługi Azure IoT Central umożliwia skoncentrowanie się na czasie, pieniędzy i energii na transformacje swojej firmy za pomocą danych IoT, a nie jedynie utrzymywanie i aktualizowanie złożonej i ciągle rosnącej infrastruktury IoT.

Interfejs użytkownika sieci Web umożliwia monitorowanie warunków urządzenia, tworzenie reguł oraz zarządzanie milionami urządzeń i ich danych w całym cyklu życia. Ponadto umożliwia ona działanie w usłudze Device Insights poprzez rozszerzenie usługi IoT Intelligence na aplikacje biznesowe.

Poniższy klip wideo zawiera omówienie usługi Azure IoT Central:

>[!VIDEO https://azure.microsoft.com/resources/videos/iot-central-updates/]

Ten artykuł zawiera opis dla IoT Central platformy Azure:

- Typowe osoby skojarzone z projektem
- Tworzenie aplikacji
- Łączenie urządzeń z aplikacją
- Zarządzanie aplikacją
- Przegląd możliwości IoT Edge w programie IoT Central
- Jak podłączyć urządzenia obsługiwane przez środowisko uruchomieniowe Azure IoT Edge do aplikacji.


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
- Jest on dostępny tylko w regionach Europy Północnej i środkowe stany USA.
- Modele możliwości urządzeń muszą mieć wszystkie interfejsy zdefiniowane wewnętrznie w tym samym pliku.

## <a name="personas"></a>Osoby

Dokumentacja usługi Azure IoT Central obejmuje cztery osób, które współpracują z aplikacją IoT Central platformy Azure:

- _Konstruktor rozwiązań_ jest odpowiedzialny za Definiowanie typów urządzeń łączących się z aplikacją i dostosowywania aplikacji dla operatora.
- _Operator_ zarządza urządzeniami połączonymi z aplikacją.
- _Administrator_ jest odpowiedzialny za zadania administracyjne, takie jak zarządzanie [rolami i uprawnieniami użytkownika](howto-administer.md) w aplikacji.
- _Deweloper urządzenia_ tworzy kod, który działa na urządzeniu połączonym z aplikacją.
- _Deweloper urządzenia/modułu_ tworzy kod/moduł, który jest uruchamiany na urządzeniu podłączonym do aplikacji.

## <a name="create-your-azure-iot-central-application"></a>Tworzenie aplikacji usługi Azure IoT Central

Konstruktor używa usługi Azure IoT Central, aby utworzyć dla organizacji niestandardowe rozwiązanie IoT hostowane w chmurze. Niestandardowe rozwiązanie IoT zwykle obejmuje następujące elementy:

- Aplikacja oparta na chmurze, która odbiera dane telemetryczne z urządzeń i umożliwia zarządzanie tymi urządzeniami.
- Wiele urządzeń z uruchomionym niestandardowym kodem połączonych z aplikacją opartą na chmurze.

Możesz szybko wdrożyć nową aplikację IoT Central platformy Azure, a następnie dostosować ją do określonych wymagań w przeglądarce. Jako Konstruktor należy użyć narzędzi sieci Web do utworzenia _szablonu urządzenia_ dla urządzeń, które łączą się z Twoją aplikacją. Szablon urządzenia to plan, który definiuje charakterystykę i zachowanie typu urządzenia, takiego jak:

- Dane telemetryczne wysyłane przez nią.
- Właściwości biznesowe, które może modyfikować operator.
- Właściwości urządzenia ustawiane przez urządzenie, które są tylko do odczytu w aplikacji.
- Właściwości, które są ustawiane przez operator, które określają zachowanie urządzenia.

Ten szablon urządzenia obejmuje następujące:

- _Model możliwości urządzenia_ , który opisuje możliwości implementowane przez urządzenie, takie jak dane telemetryczne, które wysyła, oraz właściwości, które zgłasza.
- Właściwości chmury, które nie są przechowywane na urządzeniu.
- Dostosowania, pulpity nawigacyjne i formularze, które są częścią aplikacji IoT Central.

### <a name="create-device-templates"></a>Tworzenie szablonów urządzeń

[Plug and Play IoT](../../iot-pnp/overview-iot-plug-and-play.md) umożliwia IoT Central integrację urządzeń bez konieczności pisania kodu urządzenia osadzonego. Na początku Plug and Play IoT jest schemat modelu możliwości urządzenia, który opisuje możliwości urządzeń. W aplikacji IoT Central w wersji zapoznawczej szablony urządzeń używają tych modeli możliwości urządzeń Plug and Play IoT.

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

## <a name="connect-your-devices"></a>Podłącz swoje urządzenia

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


## <a name="what-is-azure-iot-central-with-azure-iot-edge-preview-features"></a>Co to jest usługa Azure IoT Central z Azure IoT Edge (funkcje w wersji zapoznawczej)

IoT Central rozszerza swoje portfolio przez obsługę Azure IoT Edge urządzeń. 

Firmy mogą teraz uruchamiać analizy chmurowe bezpośrednio na urządzeniach IoT zarządzanych przez usługę Azure IoT Central. Ta nowa funkcja ułatwia firmom łączenie Azure IoT Edge urządzeń z systemem Azure IoT Edge Runtime oraz zarządzanie nimi, wdrażanie modułów oprogramowania, publikowanie szczegółowych informacji i podejmowanie działań na skalę — wszystko to w IoT Central. 

[Przegląd Azure IoT Edge](../../iot-edge/about-iot-edge.md)

### <a name="overview-of-iot-edge-capabilities-in-iot-central"></a>Przegląd możliwości IoT Edge w programie IoT Central

Środowisko uruchomieniowe usługi Azure IoT Edge umożliwia stosowanie logiki niestandardowej i logiki w chmurze na urządzeniach usługi IoT Edge. Urządzenie IoT Edge jest obsługiwane przez środowisko uruchomieniowe oraz wykonuje operacje zarządzania i komunikacji. 

Środowisko uruchomieniowe Azure IoT Edge wykonuje następujące funkcje:

- Instaluje i aktualizuje pakiety robocze na urządzeniu.
- Utrzymuje standardy zabezpieczeń usługi Azure IoT Edge na urządzeniu.
- Zapewnia nieprzerwane działanie modułów usługi IoT Edge.
- Przesyła raporty o kondycji modułów do chmury na potrzeby zdalnego monitorowania.
- Zarządza komunikacją między podrzędnymi urządzeniami liścia usługi IoT Edge, między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem usługi IoT Edge a chmurą.

![IoT Central z Azure IoT Edge przegląd](./media/overview-iot-central-pnp-edge/iotedge.png)

Usługa Azure IoT Central wykonuje następujące funkcje: 

- Obsługa szablonów urządzeń Azure IoT Edge, która opisuje możliwości, które urządzenie Azure IoT Edge powinno zaimplementować, takie jak 
  1. Funkcja przekazywania manifestu wdrożenia, która ułatwi zarządzanie manifestem dla floty urządzeń
  2. moduły, które będą uruchamiane na urządzeniu Azure IoT Edge
  3. dane telemetryczne wysyłane przez każdy moduł
  4. Każdy raport modułu i 
  5. polecenie każdego modułu odpowiada
  6. Ustanów relacje między modelem możliwości urządzenia bramy Azure IoT Edge i modelem możliwości urządzenia podrzędnego
  7. Właściwości chmury, które nie są przechowywane na urządzeniu Azure IoT Edge
  8. Dostosowania, pulpity nawigacyjne i formularze, które są częścią aplikacji IoT Central

  [Utwórz szablon urządzenia Azure IoT Edge](./tutorial-define-edge-device-type-pnp.md)
   
- Inicjowanie obsługi administracyjnej urządzeń Azure IoT Edge na dużą skalę przy użyciu usługi Azure IoT Device Provisioning
- Wyzwalanie reguł i podejmowanie działań na Azure IoT Edge urządzeniach
- Tworzenie pulpitów nawigacyjnych i analiz 
- Ciągły eksport danych telemetrycznych z urządzeń Azure IoT Edge

### <a name="azure-iot-edge-device-types-in-iot-central"></a>Azure IoT Edge typów urządzeń w IoT Central

Usługa Azure IoT Central klasyfikuje Azure IoT Edge typy urządzeń w następujący sposób:

- Urządzenie Azure IoT Edge jako urządzenie liścia. Urządzenie Azure IoT Edge może mieć urządzenia podrzędne, ale nie zainicjowano obsługi administracyjnej urządzeń podrzędnych w programie IoT Central
- Urządzenie Azure IoT Edge jako urządzenie bramy z urządzeniami podrzędnymi. Obsługiwane są zarówno urządzenia bramy, jak i urządzenia podrzędne w IoT Central

![IoT Central z Azure IoT Edge przegląd](./media/overview-iot-central-pnp-edge/gatewayedge.png)

### <a name="azure-iot-edge-patterns-supported-in-iot-central"></a>Wzorce Azure IoT Edge obsługiwane w IoT Central

- Azure IoT Edge jako urządzenie typu liść ![Azure IoT Edge jako urządzenie liścia](./media/overview-iot-central-pnp-edge/edgeasleafdevice.png) Azure IoT Edge urządzenie zostanie zainicjowane w IoT Central, a wszystkie urządzenia podrzędne i jego Telemetria będą reprezentowane jako pochodzące z urządzenia Azure IoT Edge. Urządzenia podrzędne, jeśli podłączone do urządzenia Azure IoT Edge nie będą obsługiwane w programie IoT Central. 

- Azure IoT Edge urządzenie bramy połączone z urządzeniami podrzędnymi z tożsamością ![Azure IoT Edge z tożsamością urządzenia podrzędnego](./media/overview-iot-central-pnp-edge/edgewithdownstreamdeviceidentity.png) Azure IoT Edge urządzenie zostanie zainicjowane w IoT Central wraz z urządzeniami podrzędnymi połączonymi z Azure IoT Edge pliku. Obsługa administracyjna urządzeń podrzędnych przez bramę jest planowana w przyszłości. W IoT Central zostanie wyjasne wstępne Inicjowanie obsługi administracyjnej urządzeń podrzędnych w chmurze, a poświadczenia są zarządzane ręcznie na urządzeniu podrzędnym. Urządzenie, które zaplanowano po raz pierwszy podczas aprowizacji urządzeń podrzędnych w przyszłości. 

- Azure IoT Edge urządzenie bramy połączone z urządzeniami podrzędnymi o tożsamości udostępnionej przez bramę graniczną ![Azure IoT Edge z urządzeniem podrzędnym bez tożsamości](./media/overview-iot-central-pnp-edge/edgewithoutdownstreamdeviceidentity.png) Azure IoT Edge urządzenie zostanie udostępnione IoT Central wraz z podrzędnymi urządzenia połączone z urządzeniem Azure IoT Edge. Obsługa bramy zapewniającej tożsamość na urządzeniach podrzędnych oraz zaplanowano zainicjowanie obsługi urządzeń podrzędnych w przyszłości. Możesz przenieść własny moduł tłumaczenia tożsamości i IoT Central będzie obsługiwał ten wzorzec. 

## <a name="next-steps"></a>Następne kroki

Oto następne sugerowane kroki po zapoznaniu się z omówieniem usługi Azure IoT Central:

- Poznanie różnic między [usługą Azure IoT Central i akceleratorami rozwiązań Azure IoT](overview-iot-options.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Zapoznanie się z [interfejsem użytkownika usługi Azure IoT Central](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Rozpoczęcie pracy przez [utworzenie aplikacji usługi Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Dowiedz się więcej o usłudze [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md)
- Dowiedz się, jak [utworzyć szablon urządzenia Azure IoT Edge](./tutorial-define-edge-device-type-pnp.md)
