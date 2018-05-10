---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 7d7cd8a197a89781a75f47bb4b4e2ec8fe7c3cb4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="secure-your-iot-deployment"></a>Zabezpieczanie wdrożenia IoT

Ten artykuł zawiera następny poziom szczegółów dotycząca zabezpieczania infrastruktury opartej na usłudze Azure IoT Internetu rzeczy (IoT). Łączy z poziomu szczegóły wdrożenia dotyczące konfigurowania i wdrażania poszczególnych składników. Umożliwia także porównania i dostępnych wyborów między różnymi metodami konkurencyjnych.

Zabezpieczanie wdrożenia Azure IoT można podzielić na następujące obszary trzy zabezpieczeń:

* **Zasady zabezpieczeń urządzeń**: Zabezpieczanie urządzenia IoT, gdy jest wdrożony w symbole.
* **Zabezpieczenia połączeń**: zapewnienie wszystkie dane przesyłane między urządzenia IoT i Centrum IoT jest poufny i odporne.
* **Chmury zabezpieczeń**: zapewnienie środków do zabezpieczania danych, podczas gdy przechodzi przez i są przechowywane w chmurze.

![Trzy obszary zabezpieczeń][img-overview]

## <a name="secure-device-provisioning-and-authentication"></a>Zabezpieczenia, inicjowanie obsługi administracyjnej urządzeń i uwierzytelniania

Akceleratorów rozwiązania IoT zabezpieczania urządzeń IoT za pomocą następujących dwóch metod:

* Zapewniając klucz unikatową tożsamość (tokeny zabezpieczające) dla każdego urządzenia, którego można użyć do komunikowania się z Centrum IoT przez urządzenie.
* Za pomocą na urządzeniach [certyfikatu X.509] [ lnk-x509] i klucza prywatnego w celu uwierzytelnienia urządzenia z Centrum IoT. Ta metoda uwierzytelniania zapewnia, że klucz prywatny na urządzeniu nie jest znany poza urządzenie w dowolnym momencie zapewnia wyższy poziom zabezpieczeń.

Metoda tokenu zabezpieczeń zapewnia uwierzytelnianie dla każdego wywołania przez urządzenie do Centrum IoT można skojarzyć klucz symetryczny do każdego wywołania. Uwierzytelnianie oparte na X.509 pozwala na uwierzytelnianie urządzenia IoT w warstwie fizycznej, w ramach ustanawianie połączenia TLS. Metody opartej na tokenie zabezpieczeń może służyć bez uwierzytelniania X.509, który jest mniej bezpieczne wzorca. Wybór między obiema metodami zależy przede wszystkim bezpieczny sposób wymaga uwierzytelniania urządzenia i dostępności bezpieczny magazyn na urządzeniu (bezpiecznie przechowywać klucz prywatny).

## <a name="iot-hub-security-tokens"></a>Tokeny zabezpieczeń usługi IoT Hub

Centrum IoT korzysta z tokenów zabezpieczających do uwierzytelniania urządzeń i usług, aby uniknąć wysyłania kluczy w sieci. Ponadto tokeny zabezpieczające są ograniczone w czas ważności i zakres. Usługa Azure IoT zestawy SDK automatycznie generować tokeny bez konieczności żadnej specjalnej konfiguracji. Niektóre scenariusze, jednak użytkownik generowania i użytkowania bezpośrednio tokenów zabezpieczających. Te scenariusze obejmują bezpośredniego użycia powierzchni MQTT AMQP i HTTP lub implementacji wzorca usługi tokenu.

Więcej informacji na temat struktury tokenu zabezpieczeń i jego użycia można znaleźć w następujących artykułach:

* [Struktura tokenu zabezpieczeń][lnk-security-tokens]
* [Tokeny sygnatury dostępu Współdzielonego jako urządzenie][lnk-sas-tokens]

Każdy Centrum IoT ma [rejestru tożsamości] [ lnk-identity-registry] które mogą być używane do tworzenia zasobów na urządzenie w usłudze, takich jak kolejki, która zawiera komunikatów w locie chmury do urządzenia i zezwolić na dostęp do punkty końcowe skierowane do urządzenia. W rejestrze tożsamości Centrum IoT zapewnia bezpieczne przechowywanie tożsamości urządzenia i kluczy zabezpieczeń dla rozwiązania. Osoby lub grupy tożsamości urządzenia można dodać do listy dozwolonych lub zablokowanych, umożliwiające pełną kontrolę nad uzyskiwania dostępu do urządzenia. Poniższe artykuły zawierają więcej szczegółowych informacji na temat struktury rejestru tożsamości i obsługiwane operacje.

[Centrum IoT obsługuje protokoły, takie jak MQTT, AMQP i HTTP][lnk-protocols]. Każda z tych protokołów inaczej używa tokeny zabezpieczające na urządzeniu IoT z Centrum IoT:

* AMQP: SASL zwykłe i opartego na oświadczeniach AMQP zabezpieczeń (`{policyName}@sas.root.{iothubName}` z tokenami poziomie Centrum IoT; `{deviceId}` z tokenami zakres urządzenia).
* MQTT: POŁĄCZ używa pakietów `{deviceId}` jako `{ClientId}`, `{IoThubhostname}/{deviceId}` w **Username** pola i sygnatury dostępu Współdzielonego token w **hasło** pola.
* HTTP: Nieprawidłowy token jest w nagłówku żądania autoryzacji.

Centrum IoT tożsamości rejestru może służyć do konfigurowania poświadczeń zabezpieczeń urządzenia i kontrola dostępu. Jednak jeśli rozwiązania IoT ma już znaczącą inwestycję w [schemat rejestru i/lub uwierzytelnianie tożsamości urządzeń niestandardowych][lnk-custom-auth], może być zintegrowany istniejącej infrastruktury z Centrum IoT Tworząc usługi tokenu.

### <a name="x509-certificate-based-device-authentication"></a>Uwierzytelnianie urządzenia na podstawie certyfikatu X.509

Korzystanie z [oparta na urządzeniach certyfikatu X.509] [ lnk-use-x509] i jego skojarzony prywatnych i publicznych pary kluczy umożliwia dodatkowe uwierzytelnianie w warstwie fizycznej. Klucz prywatny jest bezpiecznie przechowywana w urządzeniu, a nie wykrywalny poza urządzenie. Certyfikat X.509 zawiera informacje dotyczące urządzenia, takich jak identyfikator urządzenia i inne szczegóły organizacji. Podpis certyfikatu jest generowany przy użyciu klucza prywatnego.

Przepływ inicjowania obsługi administracyjnej urządzeniu wysokiego poziomu:

* Kojarzenie identyfikator, aby urządzenia fizycznego — tożsamości urządzenia i/lub certyfikatu X.509 skojarzone z urządzeniem podczas produkcji lub oddanie urządzenia.
* Utwórz tożsamość odpowiadającego mu wpisu w Centrum IoT — tożsamości urządzenia i informacje o urządzeniach skojarzonych w rejestrze tożsamości Centrum IoT.
* Bezpiecznie przechowywać odcisk palca certyfikatu X.509 w rejestrze tożsamości Centrum IoT.

### <a name="root-certificate-on-device"></a>Certyfikat główny na urządzeniu

Podczas ustanawiania bezpiecznego połączenia TLS z Centrum IoT, urządzenia IoT uwierzytelnia Centrum IoT przy użyciu certyfikatu głównego, który jest częścią zestawu SDK urządzenia. Dla klienta programu C zestawu SDK, certyfikat znajduje się w folderze "\\c\\certyfikaty" w katalogu głównym repozytorium. Chociaż te certyfikaty główne są długotrwałe, nadal mogą wygaśnięcia lub zostać odwołane. Jeśli nie istnieje sposób aktualizowania certyfikatu na urządzeniu, urządzenie może nie można następnie połączyć się z Centrum IoT (lub inne usługi w chmurze). O oznacza, aby zaktualizować certyfikat główny, po wdrożeniu urządzenia IoT skutecznie zmniejsza to zagrożenie.

## <a name="securing-the-connection"></a>Zabezpieczanie połączenia

Połączenie z Internetem urządzenia IoT i Centrum IoT jest zabezpieczone przy użyciu standardowego zabezpieczeń TLS (Transport Layer). Usługa Azure IoT obsługuje [protokołu TLS 1.2][lnk-tls12], protokołu TLS 1.1 i TLS 1.0, w tej kolejności. Obsługa protokołu TLS 1.0 jest zapewniana tylko zgodności z poprzednimi wersjami. Jeśli to możliwe należy użyć protokołu TLS 1.2, ponieważ zapewnia najwyższy poziom zabezpieczeń.

## <a name="securing-the-cloud"></a>Zabezpieczanie chmury

Centrum IoT Azure umożliwia określenie [zasady kontroli dostępu w] [ lnk-protocols] dla każdego klucza zabezpieczeń. Aby udzielić dostępu do poszczególnych punkty końcowe Centrum IoT używa następujący zestaw uprawnień. Uprawnienia ograniczyć dostęp do Centrum IoT, w oparciu o funkcje.

* **RegistryRead**. Przyznaje dostęp do odczytu w rejestrze tożsamości. Aby uzyskać więcej informacji, zobacz [rejestru tożsamości][lnk-identity-registry].
* **RegistryReadWrite**. Przyznaje uprawnienia odczytu i zapisu w rejestrze tożsamości. Aby uzyskać więcej informacji, zobacz [rejestru tożsamości][lnk-identity-registry].
* **ServiceConnect**. Udziela dostępu do komunikacji i monitorowania punktów końcowych połączonej usługi w chmurze. Na przykład udziela uprawnień do usług w chmurze zaplecza do odbierania komunikatów urządzenia do chmury, wysyłać chmury do urządzenia i pobrać odpowiedni potwierdzeń dostarczenia.
* **DeviceConnect**. Zapewnia dostęp do urządzeń połączonych z punktów końcowych. Na przykład udziela uprawnień do urządzenia do chmury wysyłać i odbierać wiadomości chmury do urządzenia. To uprawnienie jest używany przez urządzenia.

Istnieją dwa sposoby uzyskania **DeviceConnect** uprawnienia z Centrum IoT z [tokeny zabezpieczające][lnk-sas-tokens]: przy użyciu klucza tożsamość urządzenia lub klucza dostępu współdzielonego. Ponadto należy pamiętać, że wszystkie funkcje dostępne z urządzeń jest udostępniany przez projekt w punktach końcowych z prefiksem jest `/devices/{deviceId}`.

[Składniki usługi można generować tylko tokeny zabezpieczające] [ lnk-service-tokens] przy użyciu udostępnionych zasad dostępu, przyznawanie odpowiednich uprawnień.

Centrum IoT Azure i innych usług, które mogą być częścią rozwiązania umożliwiają zarządzanie użytkownikami przy użyciu usługi Azure Active Directory.

Dane pozyskanych przez Centrum IoT Azure mogą być używane w wielu różnych usług, takich jak usługi Azure Stream Analytics i magazynu obiektów blob platformy Azure. Usługi te umożliwiają dostęp do funkcji zarządzania. Więcej informacji na temat tych usług i dostępne opcje:

* [Azure DB rozwiązania Cosmos][lnk-cosmosdb]: Usługa skalowalne, pełni indeksowana bazy danych dla częściowo ustrukturyzowanych danych, która zarządza metadanych dla urządzeń udostępnieniem, takich jak atrybuty, konfiguracji i właściwości zabezpieczeń. Azure DB rozwiązania Cosmos oferuje przetwarzanie wysokiej wydajności i wysokiej przepustowości, niezależny od schematu indeksowania danych i interfejs zaawansowanych zapytań SQL.
* [Usługa Azure Stream Analytics][lnk-asa]: przetwarzanie w chmurze, która umożliwia szybkie opracowywanie i wdrożyć rozwiązanie analytics ekonomicznych do ujawniania wgląd w czasie rzeczywistym z urządzeń, czujników, infrastruktury, strumienia w czasie rzeczywistym i aplikacji. Dane z tego pełni zarządzaną usługę można skalować na każdym woluminie jednocześnie uzyskanie wysokiej przepływności, małych opóźnień i elastyczność.
* [Usługa Azure App Service][lnk-appservices]: platformy w chmurze do tworzenia zaawansowanych sieci web i aplikacji mobilnych, które nawiązać połączenia z dowolnego miejsca danych; w chmurze lub lokalnie. Twórz interesujące aplikacje mobilne dla systemów iOS, Android i Windows. Integracja z oprogramowanie jako usługa (SaaS) i aplikacji przedsiębiorstwa poza pole łączności z wielu usług w chmurze i aplikacje przedsiębiorstwa. Kod w języku ulubionych i IDE (.NET, Node.js, PHP, Python lub Java) do tworzenia aplikacji sieci web i interfejsów API szybciej niż kiedykolwiek.
* [Logic Apps][lnk-logicapps]: funkcja Logic Apps usługi Azure App Service ułatwia integrację rozwiązania IoT w istniejących systemach — biznesowych i automatyzację procesów przepływu pracy. Logic Apps umożliwia deweloperom projektowanie przepływów pracy uruchamianych wyzwalaczami i wykonujących serie kroków, reguł i akcji, które umożliwia wydajne łączniki integracji z procesów biznesowych. Logic Apps zapewnia łączność poza pole przeważająca ekosystemu SaaS, oparte na chmurze i lokalnych aplikacji.
* [Magazyn obiektów blob platformy Azure][lnk-blob]: magazynu w chmurze niezawodnych i ekonomiczny dla danych z urządzenia wysyłają do chmury.

## <a name="conclusion"></a>Podsumowanie

Ten artykuł zawiera omówienie wdrożenia poziomu szczegółów dotyczących projektowania i wdrażania infrastruktury IoT przy użyciu usługi Azure IoT. Konfigurowanie każdego składnika do zabezpieczenia jest kluczem do zabezpieczania całej infrastruktury IoT. Projekt opcje dostępne w usłudze Azure IoT Podaj pewnego poziomu elastyczność i możliwość wyboru; Jednak każdy wybór może mieć wpływ na bezpieczeństwo. Zaleca się każdy z tych opcji oceniane za pośrednictwem na podstawie oceny ryzyka/kosztów.

[img-overview]: media/iot-secure-your-deployment/overview.png

[lnk-security-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure
[lnk-sas-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-identity-registry]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-protocols]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-custom-auth]: ../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication
[lnk-x509]: http://www.itu.int/rec/T-REC-X.509-201210-I/en
[lnk-use-x509]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-tls12]: https://tools.ietf.org/html/rfc5246
[lnk-service-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components
[lnk-cosmosdb]: https://azure.microsoft.com/services/cosmos-db/
[lnk-asa]: https://azure.microsoft.com/services/stream-analytics/
[lnk-appservices]: https://azure.microsoft.com/services/app-service/
[lnk-logicapps]: https://azure.microsoft.com/services/app-service/logic/
[lnk-blob]: https://azure.microsoft.com/services/storage/
