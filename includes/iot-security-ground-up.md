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
ms.openlocfilehash: 103cdd24ca5f47ee12196cd153d528f19bb35b20
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="internet-of-things-security-from-the-ground-up"></a>Zabezpieczeń Internetu rzeczy od podstaw

Internet rzeczy (IoT) stanowi wyjątkowe wyzwanie zabezpieczeń, prywatności i zgodności dla firm na całym świecie. W przeciwieństwie do tradycyjnych przez technologii gdzie te problemy koncentrują się wokół oprogramowania i jak jest implementowane IoT dotyczy, co się stanie po zbieżne ataków i względem fizycznych. Ochrona rozwiązania IoT wymaga zapewnienia bezpiecznego inicjowania obsługi urządzeń i bezpieczna łączność między tymi urządzeniami i chmurą i ochronę danych w chmurze podczas przetwarzania i przechowywania. Jednak działać z takich funkcji, są ograniczone zasobów urządzeń, rozmieszczenie geograficzne wdrożeń i dużej liczby urządzeń w ramach rozwiązania.

Ten artykuł opisuje, w jaki akceleratorów rozwiązania IoT zapewnia bezpieczeństwo i prywatność Internetu rzeczy rozwiązania w chmurze. Akceleratorów rozwiązania dostarczania kompletnego rozwiązania end-to-end z zabezpieczeniami wbudowanych w każdym etapie od podstaw. Firma Microsoft opracowywania oprogramowania bezpiecznego jest częścią praktyki engineering oprogramowania, umieszczone w dekad firmy Microsoft długo środowisko rozwoju oprogramowania bezpieczne. Aby to zapewnić, Security Development Lifecycle (SDL) jest metodologii programowanie podstawowych, w połączeniu z hostem usługi zabezpieczeń na poziomie infrastruktury, takie jak operacyjnej gwarancji bezpieczeństwa (OSA) i Microsoft jednostki, Microsoft Centrum zabezpieczeń, a w Centrum ochrony przed złośliwym oprogramowaniem firmy Microsoft.

Akceleratorów rozwiązania oferują unikatowe funkcje tego programu, inicjowanie obsługi tej upewnij, nawiązywania połączenia i przechowywanie danych z urządzeń IoT łatwe i przejrzysty i najbardziej, bezpieczna. W tym artykule sprawdza funkcje zabezpieczeń akceleratorów rozwiązania Azure IoT i są opisane strategie wdrażania, aby upewnić się, zabezpieczeń, prywatności i zgodności wyzwania.

## <a name="introduction"></a>Wprowadzenie

Internet rzeczy (IoT) jest wave przyszłości oferty firmy natychmiastowego i możliwości rzeczywistych obniżenie kosztów, zwiększyć przychody i przekształcenie firmy. Wiele firm są jednak wątpliwości dotyczące wdrażania IoT w organizacjach z powodu problemów dotyczących zabezpieczeń, prywatności i zgodności. Główne punktu potencjalnie pochodzi z unikatowości infrastruktury IoT, które łączy ataków i względem fizyczne razem łączenia poszczególnych ryzyko związane z tych dwóch względem. Zabezpieczenia IoT odnoszą się do zapewnienia integralności kodu uruchomionego na urządzeniach, zapewniając uwierzytelniania użytkowników i urządzeń, definiowanie wyczyść własność urządzeń (a także dane generowane przez te urządzenia) i jest odporność na ataków i fizycznym.

Następnie jest problem zasad zachowania poufności informacji. Firmach przezroczystość dotyczące zbierania danych, w jakie zbierane i do czego, którzy mogą przeglądać go, która kontroluje dostęp i tak dalej. Ponadto istnieją problemów występujących w ogólne bezpieczeństwo urządzeń oraz osób ich działania i utrzymania standardy branżowe zgodności.

Podana zabezpieczeń, ochrony prywatności, przezroczystość i dotyczy zgodności, wybierając prawo dostawcy rozwiązania IoT pozostaje żądanie. Łączenia ze sobą poszczególne IoT oprogramowania i usług świadczonych przez różnych dostawców wprowadza luk w zabezpieczeń, ochrony prywatności, przezroczystość i zgodności, które mogą być trudne do wykrycia nawet rozwiązać. Wybór prawo IoT dostawcy oprogramowania i usługa jest oparta na wyszukiwanie dostawców, którzy mają zbiorczych doświadczeń uruchomione usługi, które obejmują w przypadkach i lokalizacji geograficznych, ale mogą również skalować w sposób bezpieczny i przezroczysty. Podobnie pomaga wybranego dostawcy dekad dzięki opracowywania oprogramowania bezpiecznego uruchomionego na ogromną maszyn na całym świecie, a mają możliwość doceniają zagrożeń związanego z tego nowego świata Internetu rzeczy.

## <a name="secure-infrastructure-from-the-ground-up"></a>Bezpiecznej infrastruktury od podstaw

[Microsoft Cloud](https://www.microsoft.com/enterprise/microsoftcloud/default.aspx#fbid=WzBsRQi6aGk) infrastruktury obsługuje więcej niż 1 miliard klientów w krajach 127. Opierając się na firmy Microsoft long dekad środowisko tworzenia oprogramowania korporacyjnego i uruchamiania niektórych największy usług online na świecie, Microsoft Cloud zawiera wyższego poziomu zwiększonych zabezpieczeń, ochrony prywatności, zgodności i zagrożeń wskazówki środki zaradcze niż większość klientów może osiągnąć samodzielnie.

[Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/) udostępnia proces obowiązkowe programowanie całej firmy, który osadza wymagania dotyczące zabezpieczeń w cyklu życia całego oprogramowania. Aby zapewnić, działań operacyjnych skorzystaj z tego samego poziomu rozwiązania w zakresie zabezpieczeń, SDL używa wytyczne dotyczące zabezpieczeń rygorystyczne, którego układ określa się w procesie operacyjnej gwarancji bezpieczeństwa (OSA) firmy Microsoft. Microsoft działa także w przypadku innych firm inspekcji przedsiębiorstwa bieżących weryfikacji, że spełnia on wymagania zgodności zobowiązań i Microsoft angażuje się w zakresie zabezpieczeń szerokie do tworzenia centrów doskonałości Microsoft jednostki, w tym Microsoft Security Response Center, a w Centrum ochrony przed złośliwym oprogramowaniem firmy Microsoft.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure - bezpiecznej infrastruktury IoT dla biznesu

Microsoft Azure oferuje rozwiązania pełnej chmury, który łączy stale rosnącego zbiór usług w chmurze zintegrowane — analytics, machine learning, magazynu, zabezpieczeń, sieci i sieci web — z branży zobowiązań do ochrony i poufność danych. Firmy Microsoft [założono naruszenia](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) strategii używa dedykowana *czerwony zespołu* ekspertów zabezpieczeń oprogramowania, którzy symulować ataków, testowanie Azure możliwość wykrywania, ochronę przed zagrożeniami i odzyskiwanie w przypadku naruszenia. Firmy Microsoft [odpowiedzi na zdarzenia globalne](https://www.microsoft.com/TrustCenter/Security/DesignOpSecurity) zespołu działa przez całą dobę w celu złagodzenia skutków ataków złośliwych działań. Zespół wykonuje określone procedury zarządzania zdarzeniami, komunikację i odzyskiwania, a następnie używa interfejsów wykrywalny i przewidywalne partnerom wewnętrznych i zewnętrznych.

Systemy firmy Microsoft zapewniają ciągłego włamań i zapobiegania, zapobieganie atakom usługi regularne penetracji testowania i śledczej narzędzi, które pomagają zidentyfikować i uniknięcie zagrożeń. [Uwierzytelnianie wieloskładnikowe](../articles/active-directory/authentication/multi-factor-authentication.md) zapewnia dodatkową warstwę zabezpieczeń umożliwiający użytkownikom końcowym dostęp do sieci. I aplikacji i dostawcy hosta, firma Microsoft oferuje kontroli dostępu, monitorowanie przed złośliwym oprogramowaniem, skanowanie luki w zabezpieczeniach, poprawki i zarządzanie konfiguracją.

Akceleratorów rozwiązania korzystać z zabezpieczeń i prywatności wbudowanych w platformy Azure oraz SDL i OSA procesów związanych z bezpieczeństwem programowania i działania oprogramowania firmy Microsoft. Te procedury zawierają funkcje tożsamość i zarządzanie podstawowych zabezpieczeń od wszelkich rozwiązań do ochrony infrastruktury, ochrony sieci i.

[Centrum IoT Azure](../articles/iot-hub/iot-hub-what-is-iot-hub.md) w [akceleratorów rozwiązania IoT](../articles/iot-suite/iot-suite-what-is-azure-iot.md) oferuje w pełni zarządzaną usługę, takich jak umożliwiającą i niezawodności dwukierunkową komunikację między urządzeniami IoT i usług Azure [Usługi azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) i [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) przy użyciu poświadczeń zabezpieczeń urządzenia i kontrola dostępu.

Najlepiej komunikację funkcje zabezpieczeń i prywatności wbudowane akceleratorów rozwiązania Azure IoT, w tym artykule dzieli pakiet na trzy obszary głównej zabezpieczeń.

![Akceleratory rozwiązań Azure IoT](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Zabezpieczenia, inicjowanie obsługi administracyjnej urządzeń i uwierzytelniania

Akceleratorów rozwiązania zabezpieczania urządzeń podczas znajdują się w polu podając unikatową tożsamość klucza dla każdego urządzenia, którego można użyć do komunikacji z urządzeniem podczas operacji przez infrastrukturę IoT. Ten proces jest szybka i łatwa do skonfigurowania. Wygenerowany klucz z Identyfikatorem urządzenia wybrane przez użytkownika stanowi podstawę tokenu używane w całej komunikacji między urządzeniem a Centrum IoT Azure.

Identyfikatory urządzeń mogą być skojarzone z urządzeniem podczas produkcji, (tj. zainstalowany w module zaufania sprzętu) lub można użyć istniejącego stałej tożsamości jako serwer proxy (na przykład numery seryjne Procesora). Ponieważ zmiana ta informacje identyfikacyjne w urządzeniu nie jest proste, należy wprowadzić nazwy urządzenia logicznego w przypadku podstawowej zmiany sprzętowe urządzenia, ale urządzenie logiczne jest taka sama. W niektórych przypadkach możliwe skojarzenie tożsamości urządzenia w czasie wdrażania urządzenia (na przykład inżyniera uwierzytelnionego pola fizycznie konfiguruje nowego urządzenia podczas komunikacji z zapleczem rozwiązania). [Rejestru tożsamości Centrum IoT Azure](../articles/iot-hub/iot-hub-devguide.md) zapewnia bezpieczne przechowywanie tożsamości urządzenia i kluczy zabezpieczeń dla rozwiązania. Osoby lub grupy tożsamości urządzenia można dodać do listy dozwolonych lub zablokowanych, umożliwiające pełną kontrolę nad uzyskiwania dostępu do urządzenia.

Zasady kontroli dostępu Centrum IoT Azure w chmurze umożliwiają aktywacji i wyłączanie tożsamości urządzenia, dzięki czemu może anulować skojarzenia urządzenia z wdrożenia IoT, gdy jest to wymagane. To skojarzenie i usuwanie skojarzeń urządzenia jest oparta na tożsamości każdego urządzenia.

Urządzenia dodatkowe funkcje zabezpieczeń:

* Urządzenia nie akceptują połączeń sieciowych niepożądane. Nawiązaniu wszystkie połączenia i tras w sposób tylko ruchu wychodzącego. Urządzenia odbierać polecenia z wewnętrznej bazy danych urządzenie musi inicjować połączenie Sprawdź, czy wszystkie oczekujące polecenia do przetworzenia. Gdy połączenie między urządzeniem a Centrum IoT bezpiecznie zostanie nawiązane, wiadomości z chmury do urządzenia i urządzenia do chmury mogą być wysyłane w sposób niewidoczny dla użytkownika.
* Urządzenia tylko nawiązania połączenia lub ustanawiania trasy do dobrze znane usługi, z których one są połączyć za pomocą, takich jak Azure IoT Hub.
* Poziom systemu autoryzację i uwierzytelnianie Użyj tożsamości na urządzeniu, dostęp do poświadczeń i uprawnień w pobliżu — natychmiastowe odwoływalny.

### <a name="secure-connectivity"></a>Bezpieczna komunikacja

Trwałość wiadomości jest ważna cecha każde rozwiązanie IoT. Konieczność trwale dostarczania poleceń i/lub odbieranie danych z urządzenia jest podkreślona faktem, że urządzenia IoT są połączone za pośrednictwem Internetu lub inne podobne sieci, które mogą być nieprawidłowe. Centrum IoT Azure oferuje trwałości komunikatów między chmurą i urządzeniami za pomocą systemu potwierdzenia w odpowiedzi na wiadomości. Dodatkowe trwałości wiadomości jest to osiągane przez buforowanie komunikaty w Centrum IoT na siedem dni, zbierania danych telemetrycznych i dwa dni na potrzeby poleceń.

Wydajność jest zapewnienie ochrony zasobów i operacji w środowisku ograniczonej zasobów. HTTPS (HTTP bezpieczne), standardowy bezpieczna wersja protokołu http popularnych jest obsługiwana przez Centrum IoT Azure, umożliwiające sprawną komunikację. Zaawansowane komunikatów usługi kolejkowania wiadomości protokołu (AMQP) i komunikatów usługi kolejkowania wiadomości Telemetrii transportu (MQTT), obsługiwane przez Centrum IoT Azure, są przeznaczone nie tylko w celu zwiększenia wydajności pod względem użycia zasobów, ale także dostarczanie komunikatów niezawodnej. 

Skalowalność wymaga możliwości bezpiecznie współpracować z szeroką gamą urządzeń. Centrum IoT Azure umożliwia bezpieczne połączenie z urządzeń obsługujących protokół IP, a także włączone IP. Adres IP włączony urządzenia będą mogły bezpośrednio połączyć i komunikują się z Centrum IoT za pośrednictwem bezpiecznego połączenia. Włączono IP urządzenia są ograniczone zasobów i łączyć tylko za pośrednictwem protokołów komunikacyjnych niedaleko, takich jak Zwave, ZigBee i Bluetooth. Brama pole służy do agregacji tych urządzeń i wykonuje translację protokołu, aby włączyć bezpieczny dwukierunkową komunikację z chmurą.

Funkcje zabezpieczeń dodatkowego połączenia:

* Ścieżki komunikacji między urządzeniami a Centrum IoT Azure lub między bramami Centrum IoT Azure, jest zabezpieczony za pomocą standardowych zabezpieczeń TLS (Transport Layer) z Centrum IoT Azure uwierzytelniony przy użyciu protokołu X.509.
* Centrum IoT Azure w celu ochrony urządzeń przed niechciane połączenia przychodzące, nie powoduje otwarcia dowolnego połączenia z urządzeniem. Urządzenie inicjuje wszystkie połączenia.
* Centrum IoT Azure trwale przechowuje komunikaty dla urządzeń i oczekuje na połączenie urządzenia. Te polecenia są przechowywane przez dwa dni, konfigurowanie urządzeń sporadycznie, łączenie się z powodu zasilania lub łączności zastrzeżenia co do odbierania tych poleceń. Centrum IoT Azure obsługuje kolejki na urządzenie, dla każdego urządzenia.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Bezpieczne przetwarzanie i magazynowanie w chmurze

Z szyfrowanie komunikacji do przetwarzania danych w chmurze akceleratory rozwiązanie pomaga chronić dane. Zapewnia możliwość wdrażania dodatkowego szyfrowania i zarządzania kluczy zabezpieczeń.

Za pomocą usługi Azure Active Directory (AAD) do uwierzytelniania i autoryzacji użytkowników, akceleratory rozwiązania Azure IoT zapewnia modelu autoryzacji na podstawie zasad dla danych w chmurze, włączania inspekcji i przejrzeć zarządzania łatwy dostęp. Ten model umożliwia także niemal natychmiastowe odwoływanie dostępu do danych w chmurze i urządzeń podłączonych do akceleratorów rozwiązania Azure IoT.

Gdy dane znajdują się w chmurze, można przetwarzania i przechowywania w przepływie pracy użytkownika. Dostęp do każdej części danych jest kontrolowany w usłudze Azure Active Directory, w zależności od usługi magazynu używane.

Wszystkich kluczy używanych przez infrastrukturę IoT są przechowywane w chmurze w bezpiecznym magazynie, z możliwością przerzucane w razie potrzeby klucze do ponownego udostępnienia. Dane mogą być przechowywane w [bazy danych Azure rozwiązania Cosmos](../articles/cosmos-db/introduction.md) lub [baz danych](../articles/sql-database/sql-database-faq.md), włączenie definicji poziom zabezpieczeń potrzebne. Ponadto platforma Azure udostępnia sposób monitorowania i kontroli dostępu do danych, aby ostrzec o dowolnym nieautoryzowanego dostępu lub nieautoryzowanego dostępu.

## <a name="conclusion"></a>Podsumowanie

Internet rzeczy rozpoczyna się od sieci rzeczy — co najważniejsze dla firm. IoT mogą dostarczać wartość niesamowite firmy przez zmniejszenie kosztów, zwiększenie przychodów i przekształcanie biznesowych. Powodzenie tej transformacji zależy przede wszystkim Wybieranie prawo dostawcy oprogramowania i usługi IoT. Oznacza to, znajdowanie dostawcę, który nie tylko catalyzes tej transformacji zrozumienie potrzeb biznesowych i wymagania dotyczące, ale udostępnia również wbudowane pomocą zabezpieczeń, prywatności i zgodności jako główne projektowania przezroczystości oprogramowania i usług. Firma Microsoft zbiorczych doświadczeń tworzenie i wdrażanie bezpiecznych oprogramowania i usług i pozostaje znaki wiodące w tej nowej wieku Internetu rzeczy.

Akceleratorów rozwiązania kompilacji w środki bezpieczeństwa zgodnie z projektem, włączanie bezpiecznego monitorowania zasobów w celu zwiększenia efektywności, wydajność operacyjnej dysku, aby włączyć innowacji i stosować analytics zaawansowanych danych do przekształcania firmy. Z warstwowego podejścia do zabezpieczeń, wiele funkcji zabezpieczeń i wzorce projektowe akceleratory rozwiązania pomagają wdrażania infrastruktury, który jest zaufany do przekształcania dowolnego rodzaju działalności.

## <a name="additional-information"></a>Dodatkowe informacje

Każdy akcelerator rozwiązań tworzy wystąpienia usług platformy Azure, takich jak:

* [**Centrum IoT Azure**](https://azure.microsoft.com/services/iot-hub/): bramy łączącej chmury do urządzenia. Z obsługą uwierzytelniania na urządzenie pomaga zabezpieczyć rozwiązanie sieci można skalować do milionów połączeń na proces i koncentrator bardzo dużych woluminów danych.
* [**Azure DB rozwiązania Cosmos**](https://azure.microsoft.com/services/cosmos-db/): Usługa skalowalne, pełni indeksowana bazy danych dla częściowo ustrukturyzowanych danych, która zarządza metadanych dla urządzeń udostępnieniem, takich jak atrybuty, konfiguracji i właściwości zabezpieczeń. Azure DB rozwiązania Cosmos oferuje przetwarzanie wysokiej wydajności i wysokiej przepustowości, niezależny od schematu indeksowania danych i interfejs zaawansowanych zapytań SQL.
* [**Usługa Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): strumienia w czasie rzeczywistym przetwarzania w chmurze, która umożliwia szybkie tworzenie i wdrażanie rozwiązania analizy ekonomicznych do ujawniania wgląd w czasie rzeczywistym z urządzeń, czujników, infrastruktury i aplikacji . Dane z tego pełni zarządzaną usługę można skalować na każdym woluminie jednocześnie uzyskanie wysokiej przepływności, małych opóźnień i elastyczność.
* [**Usługa Azure App Service**](https://azure.microsoft.com/services/app-service/): platformy w chmurze do tworzenia zaawansowanych sieci web i aplikacji mobilnych, które nawiązać połączenia z dowolnego miejsca danych; w chmurze lub lokalnie. Twórz interesujące aplikacje mobilne dla systemów iOS, Android i Windows. Integracja z oprogramowanie jako usługa (SaaS) i aplikacji przedsiębiorstwa poza pole łączności z wielu usług w chmurze i aplikacje przedsiębiorstwa. Kod w języku ulubionych i IDE — .NET, Node.js, PHP, Python lub Java — tworzenie aplikacji sieci web i interfejsów API szybciej niż kiedykolwiek.
* [**Logic Apps**](https://azure.microsoft.com/services/app-service/logic/): funkcja Logic Apps usługi Azure App Service ułatwia integrację rozwiązania IoT w istniejących systemach — biznesowych i automatyzację procesów przepływu pracy. Logic Apps umożliwia deweloperom projektowanie przepływów pracy uruchamianych wyzwalaczami i wykonujących serie kroków, reguł i akcji, które umożliwia wydajne łączniki integracji z procesów biznesowych. Logic Apps zapewnia łączność poza pole przeważająca ekosystemu SaaS, oparte na chmurze i lokalnych aplikacji.
* [**Magazyn obiektów blob platformy Azure**](https://azure.microsoft.com/services/storage/): magazynu w chmurze niezawodnych i ekonomiczny dla danych z urządzenia wysyłają do chmury.