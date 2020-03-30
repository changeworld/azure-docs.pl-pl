---
title: Koncepcje architektoniczne w usłudze Azure IoT Central | Dokumenty firmy Microsoft
description: W tym artykule przedstawiono kluczowe pojęcia dotyczące architektury usługi Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 12ad231d81b6c134ebb8d4902b3f95c978e9622d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271644"
---
# <a name="azure-iot-central-architecture"></a>Architektura usługi Azure IoT Central



Ten artykuł zawiera omówienie architektury Microsoft Azure IoT Central.

![Architektura najwyższego poziomu](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Urządzenia

Urządzenia wymieniają dane z aplikacją Azure IoT Central. Urządzenie może:

- Wysyłaj pomiary, takie jak dane telemetryczne.
- Synchronizuj ustawienia z aplikacją.

W usłudze Azure IoT Central dane, które urządzenie może wymieniać z aplikacją, są określone w szablonie urządzenia. Aby uzyskać więcej informacji o szablonach urządzeń, zobacz [Zarządzanie metadanymi](#metadata-management).

Aby dowiedzieć się więcej o tym, jak urządzenia łączą się z aplikacją Azure IoT Central, zobacz [Łączność z urządzeniem](concepts-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Urządzenia usługi Azure IoT Edge

Oprócz urządzeń utworzonych przy użyciu [zestawów SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdks)można również połączyć [urządzenia usługi Azure IoT Edge](../../iot-edge/about-iot-edge.md) z aplikacją IoT Central. Usługa IoT Edge umożliwia uruchamianie analizy chmury i niestandardowej logiki bezpośrednio na urządzeniach IoT zarządzanych przez IoT Central. Środowisko wykonawcze IoT Edge umożliwia:

- Instaluje i aktualizuje pakiety robocze na urządzeniu.
- Obsługa standardów zabezpieczeń IoT Edge na urządzeniu.
- Zapewnia nieprzerwane działanie modułów usługi IoT Edge.
- Przesyła raporty o kondycji modułów do chmury na potrzeby zdalnego monitorowania.
- Zarządza komunikacją między podrzędnymi urządzeniami liścia usługi IoT Edge, między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem usługi IoT Edge a chmurą.

![Usługa Azure IoT Central z usługą Azure IoT Edge](./media/concepts-architecture/iotedge.png)

Funkcja IoT Central umożliwia następujące funkcje dla urządzeń Z krawędzią IoT:

- Szablony urządzeń opisujące możliwości urządzenia usługi IoT Edge, takie jak:
  - Funkcja przekazywania manifestu wdrażania, która ułatwia zarządzanie manifestem dla floty urządzeń.
  - Moduły uruchamiane na urządzeniu IoT Edge.
  - Telemetria każdego modułu wysyła.
  - Właściwości każdego modułu raportuje.
  - Polecenia, na które odpowiada każdy moduł.
  - Relacje między modelem możliwości urządzenia bramy usługi IoT Edge a modelem możliwości urządzenia podrzędnego.
  - Właściwości chmury, które nie są przechowywane na urządzeniu usługi IoT Edge.
  - Dostosowania, pulpity nawigacyjne i formularze, które są częścią aplikacji IoT Central.

  Aby uzyskać więcej informacji, zobacz [connect urządzeń usługi Azure IoT Edge do aplikacji Usługi Azure IoT Central](./concepts-iot-edge.md) artykułu.

- Możliwość aprowizowania urządzeń usługi IoT Edge na dużą skalę przy użyciu usługi inicjowania obsługi administracyjnej urządzeń Azure IoT
- Zasady i działania.
- Niestandardowe pulpity nawigacyjne i analizy.
- Ciągły eksport danych telemetrycznych z urządzeń usługi IoT Edge.

### <a name="iot-edge-device-types"></a>Typy urządzeń IoT Edge

IoT Central klasyfikuje typy urządzeń IoT Edge w następujący sposób:

- Urządzenia typu Leaf. Urządzenie usługi IoT Edge może mieć urządzenia typu liść niższego rzędu, ale te urządzenia nie są aprowiowane w u użytkownika IoT Central.
- Urządzenia bramy z urządzeniami podrzędnymi. Zarówno urządzenia bramy, jak i urządzenia podrzędne są aprowizowane w ucho

![Omówienie IoT Central z ioT edge](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>Wzory krawędzi IoT

Funkcja IoT Central obsługuje następujące wzorce urządzeń IoT Edge:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge jako urządzenie liściowe

![IoT Edge jako urządzenie liściowe](./media/concepts-architecture/edgeasleafdevice.png)

Urządzenie Usługi IoT Edge jest aprowizować w centrum IoT Central i wszystkie urządzenia podrzędne i ich dane telemetryczne jest reprezentowana jako pochodzące z urządzenia usługi IoT Edge. Urządzenia podrzędne podłączone do urządzenia IoT Edge nie są aprowiedzone w centrum IoT.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>Urządzenie bramy usługi IoT Edge podłączone do urządzeń podrzędnych z tożsamością

![Usługa IoT Edge z tożsamością urządzenia podrzędnego](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

Urządzenie IoT Edge jest aprowizować w UIOT Central wraz z podrzędnych urządzeń podłączonych do urządzenia IoT Edge. Obsługa środowiska uruchomieniowego dla inicjowania obsługi administracyjnej urządzeń podrzędnych za pośrednictwem bramy nie jest obecnie obsługiwana.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>Urządzenie bramy usługi IoT Edge podłączone do urządzeń podrzędnych z tożsamością zapewnianą przez bramę usługi IoT Edge

![IoT Edge z urządzeniem bez tożsamości](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

Urządzenie IoT Edge jest aprowizować w UIOT Central wraz z podrzędnych urządzeń podłączonych do urządzenia IoT Edge. Obsługa środowiska uruchomieniowego bramy zapewniającej tożsamość do urządzeń podrzędnych i inicjowania obsługi administracyjnej urządzeń podrzędnych nie jest obecnie obsługiwana. Jeśli wprowadzisz własny moduł tłumaczenia tożsamości, usługa IoT Central może obsługiwać ten wzorzec.

## <a name="cloud-gateway"></a>Brama w chmurze

Usługa Azure IoT Central używa usługi Azure IoT Hub jako bramy w chmurze, która umożliwia łączność z urządzeniem. Centrum IoT umożliwia:

- Połknienia danych na dużą skalę w chmurze.
- Zarządzanie urządzeniami.
- Bezpieczna łączność z urządzeniem.

Aby dowiedzieć się więcej o Usłudze IoT Hub, zobacz [Centrum Usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Aby dowiedzieć się więcej o łączności z urządzeniami w usłudze Azure IoT Central, zobacz [Łączność z urządzeniem](concepts-get-connected.md).

## <a name="data-stores"></a>Magazyny danych

Usługa Azure IoT Central przechowuje dane aplikacji w chmurze. Przechowywane dane aplikacji obejmują:

- Szablony urządzeń.
- Tożsamości urządzeń.
- Metadane urządzenia.
- Dane użytkownika i roli.

Usługa Azure IoT Central używa magazynu szeregów czasowych dla danych pomiarowych wysyłanych z twoich urządzeń. Dane szeregów czasowych z urządzeń używanych przez usługę analityczną.

## <a name="analytics"></a>Analiza

Usługa analityczna jest odpowiedzialna za generowanie niestandardowych danych raportowania wyświetlanych przez aplikację. Operator może [dostosować analizy](howto-create-analytics.md) wyświetlane w aplikacji. Usługa analityczna jest oparta na usłudze [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) i przetwarza dane pomiarowe wysyłane z twoich urządzeń.

## <a name="rules-and-actions"></a>Reguły i akcje

[Reguły i akcje](tutorial-create-telemetry-rules.md) ściśle ze sobą współpracują, aby zautomatyzować zadania w aplikacji. Konstruktor może definiować reguły na podstawie danych telemetrycznych urządzenia, takich jak temperatura przekraczająca zdefiniowany próg. Usługa Azure IoT Central używa procesora strumienia do określenia, kiedy spełnione są warunki reguły. Po spełnieniu warunku reguły wyzwala akcję zdefiniowaną przez konstruktora. Na przykład akcja może wysłać wiadomość e-mail, aby powiadomić inżyniera, że temperatura w urządzeniu jest zbyt wysoka.

## <a name="metadata-management"></a>Zarządzanie metadanymi

W aplikacji Azure IoT Central szablony urządzeń definiują zachowanie i możliwości typów urządzeń. Na przykład szablon urządzenia lodówki określa dane telemetryczne, które lodówka wysyła do aplikacji.

![Architektura szablonu](media/concepts-architecture/template-architecture.png)

W szablonie urządzenia aplikacji IoT Central zawiera:

- **Modele możliwości urządzenia** określają możliwości urządzenia, takie jak dane telemetryczne, które wysyła, właściwości, które definiują stan urządzenia, oraz polecenia, na które urządzenie odpowiada. Funkcje urządzenia są zorganizowane w jeden lub więcej interfejsów. Aby uzyskać więcej informacji na temat modeli możliwości urządzenia, zobacz dokumentację [IoT Plug and Play (wersja zapoznawcza).](../../iot-pnp/overview-iot-plug-and-play.md)
- **Właściwości chmury** określają właściwości magazynów IoT Central dla urządzenia. Te właściwości są przechowywane tylko w centrum IoT i nigdy nie są wysyłane do urządzenia.
- **Widoki** określają pulpity nawigacyjne i formularze tworzone przez konstruktora, aby umożliwić operatorowi monitorowanie urządzeń i zarządzanie nimi.
- **Dostosowania** umożliwiają konstruktorowi zastąpienie niektórych definicji w modelu możliwości urządzenia, aby uczynić je bardziej odpowiednimi dla aplikacji IoT Central.

Aplikacja może mieć jeden lub więcej symulowanych i rzeczywistych urządzeń opartych na każdym szablonie urządzenia.

## <a name="data-export"></a>Eksport danych

W aplikacji Azure IoT Central można [stale eksportować dane](howto-export-data.md) do własnych usług Azure Event Hubs i wystąpień usługi Azure Service Bus. Można również okresowo eksportować dane do konta magazynu obiektów Blob platformy Azure. IoT Central może eksportować pomiary, urządzenia i szablony urządzeń.

## <a name="batch-device-updates"></a>Aktualizacje urządzeń wsadowych

W aplikacji Azure IoT Central można [tworzyć i uruchamiać zadania](howto-run-a-job.md) do zarządzania połączonymi urządzeniami. Te zadania umożliwiają zbiorcze aktualizowanie właściwości urządzenia lub ustawień lub uruchamianie poleceń. Na przykład można utworzyć zadanie, aby zwiększyć prędkość wentylatora dla wielu automatów chłodniczych.

## <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)

[Administrator może zdefiniować reguły dostępu](howto-manage-users-roles.md) dla aplikacji Azure IoT Central przy użyciu jednej ze wstępnie zdefiniowanych ról lub tworząc rolę niestandardową. Role określają, do jakich obszarów aplikacji użytkownik ma dostęp i jakie akcje mogą wykonywać.

## <a name="security"></a>Zabezpieczenia

Funkcje zabezpieczeń w usłudze Azure IoT Central obejmują:

- Dane są szyfrowane podczas przesyłania i odpoczynku.
- Uwierzytelnianie jest dostarczane przez usługę Azure Active Directory lub konto Microsoft. Uwierzytelnianie dwuskładnikowe jest obsługiwane.
- Pełna izolacja dzierżawy.
- Zabezpieczenia na poziomie urządzenia.

## <a name="ui-shell"></a>Powłoka interfejsu użytkownika

Powłoka interfejsu użytkownika to nowoczesna, responsywna aplikacja oparta na przeglądarce HTML5.
Administrator może dostosować interfejs użytkownika aplikacji, stosując niestandardowe motywy i modyfikując łącza pomocy, aby wskazać własne niestandardowe zasoby pomocy. Aby dowiedzieć się więcej na temat dostosowywania interfejsu użytkownika, zobacz Dostosowywanie artykułu [interfejsu użytkownika centralnego usługi Azure IoT.](howto-customize-ui.md)

Operator może tworzyć spersonalizowane pulpity nawigacyjne aplikacji. Możesz mieć kilka pulpitów nawigacyjnych, które wyświetlają różne dane i przełączają się między nimi.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiedziałeś się o architekturze usługi Azure IoT Central, sugerowanym następnym krokiem jest zapoznanie się z [łącznością urządzeń](concepts-get-connected.md) w usłudze Azure IoT Central.
