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
ms.openlocfilehash: e5acb8e0f8805da7f14bbce58b4bfd2acdc24f23
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183887"
---
# <a name="secure-your-internet-of-things-iot-deployment"></a>Zabezpieczanie wdrożenia Internetu rzeczy (IoT)

Ten artykuł zawiera następny poziom szczegółów dotycząca zabezpieczania infrastruktury opartej na usłudze Azure IoT Internetu rzeczy (IoT). Łączy się on do poziomu szczegółów implementacji do konfigurowania i wdrażania poszczególnych składników. Umożliwia także porównania i opcji, między różnymi metodami konkurencyjnych.

Zabezpieczanie wdrożenia IoT platformy Azure można podzielić na następujące obszary zabezpieczeń trzy:

* **Zasady zabezpieczeń urządzeń**: Zabezpieczanie urządzeń IoT, gdy jest wdrożony w środowisku naturalnym.

* **Zabezpieczenia połączeń**: Zapewnienie wszystkie dane przesyłane między urządzeniami IoT i Centrum IoT Hub jest poufny i odporną na manipulacje.

* **Cloud Security**: Zapewnienie środków do zabezpieczania danych, podczas gdy przechodzi przez i przechowywane w chmurze.

![Trzy obszary zabezpieczeń](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Zabezpieczanie device provisioning i uwierzytelniania

Akceleratory rozwiązań IoT Zabezpieczanie urządzeń IoT za pomocą następujących dwóch metod:

* Udostępniając klucz tożsamości (tokeny zabezpieczające) dla każdego urządzenia, która może służyć przez urządzenie do komunikowania się z usługą IoT Hub.

* Za pomocą na urządzeniach [certyfikat X.509](https://www.itu.int/rec/T-REC-X.509-201210-S) i klucza prywatnego w celu uwierzytelnienia urządzenia do usługi IoT Hub. Ta metoda uwierzytelniania gwarantuje, że klucz prywatny na urządzeniu nie jest znany poza urządzenie w dowolnym momencie zapewnia wyższy poziom zabezpieczeń.

Metoda tokenu zabezpieczeń zapewnia uwierzytelnianie dla każdego wywołania przez urządzenie do Centrum IoT Hub, kojarząc klucz symetryczny do każdego wywołania. Uwierzytelnianie oparte na X.509 pozwala na uwierzytelnianie urządzeń IoT w warstwie fizycznej podczas ustanawiania połączenia protokołu TLS. Metoda bezpieczeństwa tokenów może służyć bez uwierzytelniania X.509, który jest mniej bezpieczna opcja wzorca. Wybór między obiema metodami zależy przede wszystkim jak bezpieczne uwierzytelnianie urządzenie musi być i dostępność bezpieczny magazyn na urządzeniu (bezpiecznie przechowywać klucz prywatny).

## <a name="iot-hub-security-tokens"></a>Tokeny zabezpieczeń usługi IoT Hub

Usługa IoT Hub przy użyciu tokenów zabezpieczających do uwierzytelniania urządzeń i usług, aby uniknąć przesyłanie kluczy w sieci. Ponadto tokeny zabezpieczające są ograniczony czas ważności i zakresu. Usługa Azure IoT SDKs automatycznego generowania tokenów bez konieczności żadnej specjalnej konfiguracji. Niektóre scenariusze wymagają jednak użytkownika do generowania i używania tokenów zabezpieczających bezpośrednio. Te scenariusze obejmują bezpośredniemu wykorzystaniu powierzchnie MQTT, AMQP lub HTTP lub implementacji wzorca usługi tokenu.

Szczegółowe informacje na temat struktury tokenu zabezpieczeń i sposób jej użycia można znaleźć w następujących artykułach:

* [Struktura tokenu zabezpieczeń](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [Za pomocą tokenów sygnatur dostępu Współdzielonego jako urządzenie](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Każde Centrum IoT Hub ma [rejestr tożsamości](../articles/iot-hub/iot-hub-devguide-identity-registry.md) mogą służyć do tworzenia zasobów na poziomie urządzenia w usłudze, takich jak kolejki, która zawiera aktywne komunikaty z chmury do urządzenia i umożliwia dostęp do punktów końcowych przeznaczonych dla urządzenia. Rejestr tożsamości usługi IoT Hub udostępnia bezpieczny magazyn tożsamości urządzeń i klucze zabezpieczeń dla rozwiązania. Osoby lub grupy, tożsamości urządzenia można dodać do listy dozwolonych lub zablokowanych, umożliwiające pełną kontrolę nad ich dostępem. Poniższe artykuły zawierają szczegółowe informacje na temat struktury rejestr tożsamości i obsługiwane operacje.

[Centrum IoT Hub obsługuje protokoły, takie jak MQTT, AMQP i HTTP](../articles//iot-hub/iot-hub-devguide-security.md). Każda z tych protokołów inaczej używa tokenów zabezpieczających z urządzenia IoT do usługi IoT Hub:

* AMQP: ZWYKŁY SASL i zabezpieczenia oparte na oświadczeniach AMQP (`{policyName}@sas.root.{iothubName}` za pomocą tokenów poziom Centrum IoT. `{deviceId}` z tokenami urządzenia o określonym zakresie).

* MQTT: Używa pakietów CONNECT `{deviceId}` jako `{ClientId}`, `{IoThubhostname}/{deviceId}` w **Username** pola i sygnatury dostępu Współdzielonego token w **hasło** pola.

* HTTP: Jest prawidłowy token w nagłówku żądania autoryzacji.

Rejestr tożsamości usługi IoT Hub może służyć do konfigurowania poświadczeń zabezpieczeń na poziomie urządzenia i kontrola dostępu. Jednak jeśli rozwiązania IoT już znaczących inwestycji [schemat rejestru i/lub uwierzytelnianie tożsamości urządzeń niestandardowych](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication), mogła zostać włączona w istniejącej infrastruktury za pomocą usługi IoT Hub, tworząc usługi tokenu.

### <a name="x509-certificate-based-device-authentication"></a>Uwierzytelnianie urządzeń na podstawie certyfikatu X.509

Korzystanie z [opartego na urządzeniach certyfikat X.509](../articles/iot-hub/iot-hub-devguide-security.md) i jego skojarzone prywatnymi i publicznymi pary kluczy umożliwia dodatkowe uwierzytelnianie w warstwie fizycznej. Klucz prywatny jest bezpiecznie przechowywana w urządzeniu i nie jest stała się wykrywalna poza urządzenie. Certyfikat X.509 zawiera informacje o urządzeniu, takie jak identyfikator urządzenia i inne szczegóły organizacji. Podpis certyfikatu jest generowany przy użyciu klucza prywatnego.

Przepływ inicjowania obsługi administracyjnej urządzeń wysokiego poziomu:

* Kojarzenie identyfikator, aby urządzenie fizyczne — tożsamość urządzenia i/lub certyfikatu X.509 skojarzony z urządzeniem podczas produkcji lub oddanie urządzenia.

* Utwórz odpowiadający mu wpis tożsamości w usłudze IoT Hub — tożsamość urządzenia i informacje o urządzeniach skojarzonych w rejestrze tożsamości Centrum IoT Hub.

* Bezpieczne przechowywanie odcisk palca certyfikatu X.509 w rejestrze tożsamości Centrum IoT Hub.

### <a name="root-certificate-on-device"></a>Certyfikat główny na urządzeniu

Podczas ustanawiania bezpiecznego połączenia TLS z usługą IoT Hub, IoT device uwierzytelnia Centrum IoT przy użyciu certyfikatu głównego, który jest częścią zestawu SDK urządzenia. Dla języka C zestaw SDK klienta, certyfikat znajduje się w folderze "\\c\\certyfikatów" w katalogu głównym repozytorium. Chociaż te certyfikaty główne są długotrwałe, są nadal mogą wygasnąć lub zostać odwołane. Jeśli nie istnieje sposób aktualizowania certyfikatu na urządzeniu, urządzenie może nie móc później połączyć się z Centrum IoT Hub (lub inne usługi w chmurze). Posiadanie oznacza, że aby zaktualizować certyfikat główny, efektywne wdrożenie urządzenia IoT zmniejsza to ryzyko.

## <a name="securing-the-connection"></a>Zabezpieczanie połączenia

Połączenie z Internetem urządzeń IoT i Centrum IoT Hub jest zabezpieczony przy użyciu standardu zabezpieczeń TLS (Transport Layer). Platforma Azure obsługuje IoT [protokołu TLS 1.2](https://tools.ietf.org/html/rfc5246), TLS 1.1 i TLS 1.0, w następującej kolejności. Obsługa protokołu TLS 1.0 znajduje się tylko w przypadku zgodności z poprzednimi wersjami. Jeśli to możliwe należy używać protokołu TLS 1.2, ponieważ zapewnia najwyższy poziom zabezpieczeń.

## <a name="securing-the-cloud"></a>Zabezpieczanie chmury

Usługa Azure IoT Hub umożliwia określenie [zasady kontroli dostępu](../articles/iot-hub/iot-hub-devguide-security.md) dla każdego klucza zabezpieczeń. Aby udzielić dostępu do wszystkich punktów końcowych usługi IoT Hub używa następujący zestaw uprawnień. Uprawnienia ograniczają dostęp do usługi IoT Hub w oparciu o funkcjonalność.

* **RegistryRead**. Przyznaje dostęp do rejestru tożsamości. Aby uzyskać więcej informacji, zobacz [rejestr tożsamości](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **RegistryReadWrite**. Przyznaje uprawnienia odczytu i zapisu w rejestrze tożsamości. Aby uzyskać więcej informacji, zobacz [rejestr tożsamości](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **ServiceConnect**. Przyznaje dostęp do chmury usługi komunikacji i monitorowania punktów końcowych. Na przykład przyznaje uprawnienia do usług w chmurze zaplecza do odbierania komunikatów z urządzenia do chmury, wysyłanie komunikatów z chmury do urządzeń i pobrać odpowiedni potwierdzeń dostarczenia.

* **DeviceConnect**. Zapewnia dostęp do punktów końcowych przeznaczonych dla urządzenia. Na przykład przyznaje uprawnienia do wysyłania komunikatów z urządzenia do chmury i odbierania komunikatów z chmury do urządzeń. To uprawnienie jest używany przez urządzenia.

Istnieją dwa sposoby uzyskania **DeviceConnect** uprawnień za pomocą usługi IoT Hub przy użyciu [tokenów zabezpieczających](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app): przy użyciu klucza tożsamości urządzenia lub klucza dostępu współdzielonego. Ponadto należy pamiętać, że wszystkie funkcje dostępne z urządzenia jest udostępniona zgodnie z projektem w punktach końcowych z prefiksem jest `/devices/{deviceId}`.

[Składniki usługi może generować jedynie tokenów zabezpieczających](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) używająca współdzielonej zasad dostępu, przyznawanie odpowiednich uprawnień.

Usługa Azure IoT Hub i innych usług, które mogą być częścią rozwiązania Zezwalaj na zarządzanie użytkownikami przy użyciu usługi Azure Active Directory.

Dane pozyskane przez usługę Azure IoT Hub, mogą być wykorzystywane za pomocą różnych usług, takich jak usługi Azure Stream Analytics i Azure blob storage. Usługi te umożliwiają dostęp do funkcji zarządzania. Dowiedz się więcej na temat tych usług i dostępne opcje:

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): Skalowalne, w pełni indeksowane usługa bazy danych dla danych z częściową strukturą, zarządzającą metadanych dla urządzeń, które można udostępnić, takie jak atrybuty, konfiguracji i właściwości zabezpieczeń. Usługa Azure Cosmos DB oferuje przetwarzanie o wysokiej wydajności i dużej przepustowości, niezależny od schematów indeksowanie danych i interfejs zaawansowanych zapytań SQL.

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): Przetwarzanie w chmurze, która pozwala na szybkie tworzenie i wdrażanie rozwiązania analizy ekonomiczne zdobywania w czasie rzeczywistym z urządzeń, czujników, infrastruktury i aplikacji w czasie rzeczywistym strumienia. Dane z tej w pełni zarządzanej usługi można skalować do żadnego woluminu przy zachowaniu wysokiej przepływności, małego opóźnienia i elastyczność.

* [Usługa Azure App Services](https://azure.microsoft.com/services/app-service/): Platforma usług w chmurze do tworzenia zaawansowanych aplikacji internetowych i mobilnych łączących się z danymi przechowywanymi; w chmurze lub lokalnie. Twórz interesujące aplikacje mobilne dla systemów iOS, Android i Windows. Integracja z oprogramowaniem jako usługa (SaaS) oraz aplikacje dla przedsiębiorstw z łącznością poza pole do wielu usług w chmurze i aplikacje dla przedsiębiorstw. Możesz pisać kod w ulubionym języku i środowisku IDE (.NET, Node.js, PHP, Python lub Java) do tworzenia aplikacji sieci web i interfejsy API szybciej niż kiedykolwiek wcześniej.

* [Aplikacje logiki](https://azure.microsoft.com/services/app-service/logic/): Funkcja Logic Apps w usłudze Azure App Service ułatwia integrowanie rozwiązania IoT do istniejących systemów line-of-business oraz automatyzowanie procesów przepływu pracy. Usługa Logic Apps umożliwia deweloperom projektowanie przepływów pracy rozpoczynających się od wyzwalacza, a następnie wykonanie serii czynności — reguły i akcje, które umożliwia zaawansowane łączniki integracji z procesów biznesowych. Usługa Logic Apps oferuje out-of--box łączności z obszernym ekosystemem SaaS i opartych na chmurze i aplikacji lokalnych.

* [Usługa Azure Blob storage](https://azure.microsoft.com/services/storage/): Magazyn w chmurze niezawodnego i ekonomicznego dla danych, które urządzenia są wysyłane do chmury.

## <a name="conclusion"></a>Podsumowanie

Ten artykuł zawiera omówienie wdrożenia poziomu szczegółów dotyczących projektowania i wdrażania infrastruktury IoT przy użyciu usługi Azure IoT. Konfigurowanie każdej części w celu zabezpieczenia jest klucz w zabezpieczaniu całej infrastruktury IoT. Projekt opcje dostępne w usłudze Azure IoT zapewniać pewien poziom elastyczność i szersze; Jednak każdy wybór może mieć wpływ na zabezpieczenia. Zaleca się, że każda z tych opcji obliczony przez ocenę ryzyka i kosztów.
