---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 08cca67455df4b2d28bba0a7410fccc11446fcdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76748823"
---
Ten artykuł zawiera następny poziom szczegółów dotyczących zabezpieczania infrastruktury Internetu rzeczy (IoT) opartej na Usłudze Azure IoT. Zawiera łącza do szczegółów poziomu implementacji do konfigurowania i wdrażania każdego składnika. Zapewnia również porównania i wybory między różnymi konkurencyjnymi metodami.

Zabezpieczanie wdrożenia usługi Azure IoT można podzielić na następujące trzy obszary zabezpieczeń:

* **Zabezpieczenia urządzeń:** zabezpieczanie urządzenia IoT, gdy jest ono wdrożone w środowisku naturalnym.

* **Zabezpieczenia połączeń:** zapewnienie, że wszystkie dane przesyłane między urządzeniem IoT i Centrum IoT są poufne i zabezpieczone przed manipulacją.

* **Zabezpieczenia w chmurze:** zapewnienie środków do zabezpieczania danych podczas ich przechodzenia i są przechowywane w chmurze.

![Trzy obszary bezpieczeństwa](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Bezpieczne inicjowanie obsługi administracyjnej i uwierzytelnianie urządzeń

Akceleratory rozwiązań IoT zabezpieczają urządzenia IoT przy użyciu następujących dwóch metod:

* Udostępniając unikatowy klucz tożsamości (tokeny zabezpieczające) dla każdego urządzenia, który może być używany przez urządzenie do komunikowania się z Centrum IoT Hub.

* Za pomocą [certyfikatu X.509](https://www.itu.int/rec/T-REC-X.509-201210-S) na urządzeniu i klucza prywatnego jako środka do uwierzytelniania urządzenia do Usługi IoT Hub. Ta metoda uwierzytelniania zapewnia, że klucz prywatny na urządzeniu nie jest znany poza urządzeniem w dowolnym momencie, zapewniając wyższy poziom zabezpieczeń.

Metoda tokenu zabezpieczającego zapewnia uwierzytelnianie dla każdego wywołania wykonanego przez urządzenie do Usługi IoT Hub przez skojarzenie klucza symetrycznego z każdym wywołaniem. Uwierzytelnianie oparte na architekturze X.509 umożliwia uwierzytelnianie urządzenia IoT w warstwie fizycznej jako część ustanowienia połączenia TLS. Metoda oparta na tokenie zabezpieczającym może być używana bez uwierzytelniania X.509, który jest mniej bezpieczny wzorzec. Wybór między tymi dwiema metodami jest przede wszystkim podyktowany tym, jak bezpieczne musi być uwierzytelnianie urządzenia i dostępność bezpiecznego magazynu na urządzeniu (aby bezpiecznie przechowywać klucz prywatny).

## <a name="iot-hub-security-tokens"></a>Tokeny zabezpieczające Usługi IoT Hub

Usługa IoT Hub używa tokenów zabezpieczających do uwierzytelniania urządzeń i usług, aby uniknąć wysyłania kluczy w sieci. Ponadto tokeny zabezpieczające są ograniczone w czasie ważności i zakresu. Zestaw SDK usługi Azure IoT automatycznie generuje tokeny bez konieczności konieczności specjalnej konfiguracji. Niektóre scenariusze wymagają jednak od użytkownika bezpośredniego generowania i używania tokenów zabezpieczających. Scenariusze te obejmują bezpośrednie użycie powierzchni MQTT, AMQP lub HTTP lub implementację wzorca usługi tokenu.

Więcej szczegółów na temat struktury tokenu zabezpieczającego i jego użycia można znaleźć w następujących artykułach:

* [Struktura tokenów zabezpieczających](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [Używanie tokenów sygnatury dostępu Współdzielonego jako urządzenia](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Każdy Usługa IoT Hub ma [rejestr tożsamości,](../articles/iot-hub/iot-hub-devguide-identity-registry.md) który może służyć do tworzenia zasobów na urządzenie w usłudze, takich jak kolejka, która zawiera komunikaty w locie chmury do urządzenia i aby umożliwić dostęp do punktów końcowych przeznaczonych dla urządzenia. Rejestr tożsamości usługi IoT Hub zapewnia bezpieczne przechowywanie tożsamości urządzeń i kluczy zabezpieczeń dla rozwiązania. Do listy dozwolonych lub listy zablokowanych można dodać poszczególne lub grupy tożsamości urządzeń, co umożliwia pełną kontrolę nad dostępem do urządzenia. Poniższe artykuły zawierają więcej szczegółów na temat struktury rejestru tożsamości i obsługiwanych operacji.

[Centrum IoT obsługuje protokoły, takie jak MQTT, AMQP i HTTP](../articles//iot-hub/iot-hub-devguide-security.md). Każdy z tych protokołów używa tokenów zabezpieczających z urządzenia IoT do Usługi IoT Hub w inny sposób:

* AMQP: SASL PLAIN i zabezpieczenia oparte`{policyName}@sas.root.{iothubName}` na oświadczeniach AMQP (z tokenami na poziomie centrum IoT; `{deviceId}` tokenów o zakresie urządzenia).

* MQTT: Pakiet CONNECT `{deviceId}` jest `{ClientId}` `{IoThubhostname}/{deviceId}` używany jako , w polu **Nazwa użytkownika** i token sygnatury dostępu Współdzielonego w polu **Hasło.**

* HTTP: Prawidłowy token znajduje się w nagłówku żądania autoryzacji.

Rejestru tożsamości usługi IoT Hub można używać do konfigurowania poświadczeń zabezpieczeń na urządzenie i kontroli dostępu. Jeśli jednak rozwiązanie IoT ma już znaczne inwestycje w [niestandardowy rejestr tożsamości urządzenia i/lub schemat uwierzytelniania,](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication)można go zintegrować z istniejącą infrastrukturą z Usługą IoT Hub, tworząc usługę tokenu.

### <a name="x509-certificate-based-device-authentication"></a>Uwierzytelnianie urządzeń oparte na certyfikatach X.509

Użycie [certyfikatu X.509 opartego na urządzeniu](../articles/iot-hub/iot-hub-devguide-security.md) i skojarzonej z nim pary kluczy prywatnych i publicznych umożliwia dodatkowe uwierzytelnianie w warstwie fizycznej. Klucz prywatny jest bezpiecznie przechowywany w urządzeniu i nie można go odnaleźć poza urządzeniem. Certyfikat X.509 zawiera informacje o urządzeniu, takie jak identyfikator urządzenia, i inne szczegóły organizacyjne. Podpis certyfikatu jest generowany przy użyciu klucza prywatnego.

Przepływ inicjowania obsługi administracyjnej urządzeń wysokiego poziomu:

* Skojarzyć identyfikator z urządzeniem fizycznym — tożsamości urządzenia i/lub certyfikatu X.509 skojarzonego z urządzeniem podczas produkcji lub uruchamiania urządzenia.

* Utwórz odpowiedni wpis tożsamości w Centrum IoT — tożsamość urządzenia i skojarzone informacje o urządzeniu w rejestrze tożsamości usługi IoT Hub.

* Bezpiecznie przechowuj odcisk palca certyfikatu X.509 w rejestrze tożsamości usługi IoT Hub.

### <a name="root-certificate-on-device"></a>Certyfikat główny na urządzeniu

Podczas ustanawiania bezpiecznego połączenia TLS z Usługą IoT Hub urządzenie IoT uwierzytelnia Centrum IoT przy użyciu certyfikatu głównego, który jest częścią sdk urządzenia. W przypadku SDK klienta języka C certyfikat\\znajduje\\się w folderze "c certs" w katalogu głównym repozytorium. Chociaż te certyfikaty główne są długotrwałe, nadal mogą wygasnąć lub zostać odwołane. Jeśli nie ma możliwości aktualizacji certyfikatu na urządzeniu, urządzenie może nie być w stanie następnie połączyć się z centrum IoT Hub (lub inną usługą w chmurze). Posiadanie środków do aktualizacji certyfikatu głównego po wdrożeniu urządzenia IoT skutecznie zmniejsza to ryzyko.

## <a name="securing-the-connection"></a>Zabezpieczanie połączenia

Połączenie internetowe między urządzeniem IoT a Centrum IoT jest zabezpieczone przy użyciu standardu TLS (Transport Layer Security). Usługa Azure IoT obsługuje [tls 1.2](https://tools.ietf.org/html/rfc5246), TLS 1.1 i TLS 1.0, w tej kolejności. Obsługa protokołu TLS 1.0 jest dostępna tylko w przypadku zgodności z powrotem. Sprawdź [obsługę protokołu TLS w centrum IoT Hub,](../articles/iot-hub/iot-hub-tls-support.md) aby zobaczyć, jak skonfigurować koncentrator do używania protokołu TLS 1.2, ponieważ zapewnia on największe bezpieczeństwo.

## <a name="securing-the-cloud"></a>Zabezpieczanie chmury

Usługa Azure IoT Hub umożliwia definiowanie [zasad kontroli dostępu](../articles/iot-hub/iot-hub-devguide-security.md) dla każdego klucza zabezpieczeń. Używa następującego zestawu uprawnień, aby udzielić dostępu do każdego punktu końcowego usługi IoT Hub. Uprawnienia ograniczają dostęp do usługi IoT Hub na podstawie funkcjonalności.

* **RegistryRead**. Udziela dostępu do odczytu do rejestru tożsamości. Aby uzyskać więcej informacji, zobacz [rejestr tożsamości](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **RegistryReadWrite**. Udziela dostępu do odczytu i zapisu do rejestru tożsamości. Aby uzyskać więcej informacji, zobacz [rejestr tożsamości](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **ServiceConnect**. Zapewnia dostęp do komunikacji i monitorowania punktów końcowych opartych na usługach w chmurze. Na przykład udziela uprawnień do usług w chmurze zaplecza do odbierania wiadomości z urządzenia do chmury, wysyłania komunikatów z chmury do urządzenia i pobierania odpowiednich potwierdzeń dostarczania.

* **DeviceConnect**. Udziela dostępu do punktów końcowych skierowanych do urządzenia. Na przykład udziela uprawnień do wysyłania wiadomości z urządzenia do chmury i odbierania komunikatów z chmury do urządzenia. To uprawnienie jest używane przez urządzenia.

Istnieją dwa sposoby uzyskiwania uprawnień **DeviceConnect** za pomocą usługi IoT Hub za pomocą [tokenów zabezpieczających:](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)przy użyciu klucza tożsamości urządzenia lub udostępnionego klucza dostępu. Ponadto ważne jest, aby pamiętać, że wszystkie funkcje dostępne z urządzeń są `/devices/{deviceId}`widoczne zgodnie z projektem na punktach końcowych z prefiksem .

[Składniki usługi mogą generować tylko tokeny zabezpieczające](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) przy użyciu zasad dostępu współdzielonego przyznającego odpowiednie uprawnienia.

Usługa Azure IoT Hub i inne usługi, które mogą być częścią rozwiązania, umożliwiają zarządzanie użytkownikami korzystającymi z usługi Azure Active Directory.

Dane przynajmowane przez usługę Azure IoT Hub mogą być używane przez różne usługi, takie jak usługa Azure Stream Analytics i magazyn obiektów blob platformy Azure. Te usługi umożliwiają dostęp do zarządzania. Dowiedz się więcej o tych usługach i dostępnych opcjach:

* [Usługa Azure Cosmos DB:](https://azure.microsoft.com/services/cosmos-db/)skalowalna, w pełni indeksowana usługa bazy danych dla danych częściowo ustrukturyzowanych, która zarządza metadanymi dla urządzeń, które aprowizujesz, takich jak atrybuty, konfiguracja i właściwości zabezpieczeń. Usługa Azure Cosmos DB oferuje wydajne i wydajne przetwarzanie, indeksowanie danych w programie agnostycznym i bogaty interfejs zapytań SQL.

* [Usługa Azure Stream Analytics:](https://azure.microsoft.com/services/stream-analytics/)przetwarzanie strumienia w czasie rzeczywistym w chmurze, które umożliwia szybkie opracowywanie i wdrażanie taniego rozwiązania analitycznego w celu wykrywania szczegółowych informacji w czasie rzeczywistym z urządzeń, czujników, infrastruktury i aplikacji. Dane z tej w pełni zarządzanej usługi można skalować do dowolnego woluminu, jednocześnie osiągając wysoką przepustowość, małe opóźnienia i odporność.

* [Usługi Azure App Services:](https://azure.microsoft.com/services/app-service/)platforma w chmurze do tworzenia zaawansowanych aplikacji internetowych i mobilnych, które łączą się z danymi w dowolnym miejscu; w chmurze lub lokalnie. Twórz atrakcyjne aplikacje mobilne dla systemów iOS, Android i Windows. Integracja z oprogramowaniem jako usługą (SaaS) i aplikacjami dla przedsiębiorstw z nieojską łącznością z dziesiątkami usług chmurowych i aplikacji korporacyjnych. Koduj w swoim ulubionym języku i IDE (.NET, Node.js, PHP, Python lub Java), aby tworzyć aplikacje internetowe i interfejsy API szybciej niż kiedykolwiek wcześniej.

* [Aplikacje logiki:](https://azure.microsoft.com/services/app-service/logic/)Funkcja aplikacji logiki usługi Azure App Service pomaga zintegrować rozwiązanie IoT z istniejącymi systemami biznesowymi i zautomatyzować procesy przepływu pracy. Aplikacja logika umożliwia deweloperom projektowanie przepływów pracy, które rozpoczynają się od wyzwalacza, a następnie wykonywanie serii kroków — reguł i akcji, które używają zaawansowanych łączników do integracji z procesami biznesowymi. Logic Apps oferuje gotowe połączenie z rozległym ekosystemem SaaS, aplikacji opartych na chmurze i lokalnych.

* [Magazyn obiektów Blob platformy Azure:](https://azure.microsoft.com/services/storage/)niezawodne, ekonomiczne miejsce w chmurze dla danych wysyłanych przez urządzenia do chmury.

## <a name="conclusion"></a>Podsumowanie

Ten artykuł zawiera omówienie szczegółów poziomu implementacji do projektowania i wdrażania infrastruktury IoT przy użyciu usługi Azure IoT. Konfigurowanie każdego składnika, który ma być bezpieczny, ma kluczowe znaczenie dla zabezpieczenia ogólnej infrastruktury IoT. Opcje projektowania dostępne w usłudze Azure IoT zapewniają pewien poziom elastyczności i wyboru; jednak każdy wybór może mieć wpływ na bezpieczeństwo. Zaleca się, aby każdy z tych wyborów był oceniany w drodze oceny ryzyka/kosztów.
