---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 04/24/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b952763378de562f35c2e1ecaf49c56f0145c559
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183345"
---
# <a name="security-for-internet-of-things-iot-from-the-ground-up"></a>Zabezpieczenia dla Internetu rzeczy (IoT) od podstaw

Internet rzeczy (IoT) stanowi specyficzne wyzwania zabezpieczeń, ochrony prywatności i zgodności dla firm na całym świecie. W przeciwieństwie do tradycyjnych cybernetycznymi technologii gdzie tych problemów koncentrują się wokół oprogramowania oraz sposób implementacji IoT dotyczy, co się stanie po zbiegają się tym i cech fizycznych. Ochrona rozwiązań IoT wymaga zapewnienia bezpieczne Inicjowanie obsługi urządzeń, bezpieczną łączność między tymi urządzeniami i chmurą i ochronę danych w chmurze podczas przetwarzania i przechowywania. Działa z takich funkcji, są jednak ograniczonych zasobach urządzeń, rozkład geograficzny wdrożeń i dużą liczbę urządzeń, w ramach rozwiązania.

W tym artykule przedstawiono, w jaki akceleratorów rozwiązań IoT zapewnia bezpieczeństwo i prywatność Internet of Things rozwiązaniem w chmurze. Akceleratory rozwiązań dostarczaj kompletnego rozwiązania end-to-end, dzięki zabezpieczeniom wbudowanym w każdym etapie, od podstaw. W firmie Microsoft, tworzenia bezpiecznego oprogramowania jest częścią praktyki inżynierii oprogramowania, osadzonego w dekad firmy Microsoft długo środowisko tworzenia bezpiecznego oprogramowania. Aby to zapewnić cykl projektowania zabezpieczeń (SDL) jest metodologię programowania podstawowe, w połączeniu z hostem usługi zabezpieczeń na poziomie infrastruktury, takie jak Operational Security Assurance (OSA) i Microsoft Digital Crimes Unit, Microsoft Centrum zabezpieczeń i Microsoft Malware Protection Center.

Akceleratory rozwiązań dają unikatowych funkcji, udostępniania tego upewnij, nawiązywania połączenia i przechowywania danych z urządzeń IoT, łatwe i przejrzystości, a najbardziej, bezpieczna. W tym artykule sprawdza funkcje zabezpieczeń akceleratorów rozwiązań Azure IoT i strategie wdrażania zapewnienie bezpieczeństwa, prywatności i zgodności problemy zostały rozwiązane.

## <a name="introduction"></a>Wprowadzenie

Internet rzeczy (IoT) jest wave w przyszłości, oferuje firmom natychmiastowego i rzeczywistych możliwości w celu obniżenia kosztów, zwiększanie przychodów i Przekształć swoją działalność. Wiele firm są jednak wątpliwości do wdrożenia IoT w organizacji z powodu problemów dotyczących zabezpieczeń, ochrony prywatności i zgodności. Punkt główną kwestią pochodzi z unikatowości infrastruktury IoT, która łączy cybernetycznych i cech fizycznych ze sobą, łączenia poszczególnych ryzyka związanego z używaniem w tych dwóch światów. Zabezpieczenia IoT odnoszą się do zapewnienia integralności kodu uruchamianych na urządzeniach, uwierzytelniania użytkowników i urządzeń, definiując wyczyść własności urządzenia (a także danych generowanych przez te urządzenia) i jest odporna na cybernetycznych oraz ataki fizyczne.

Następnie jest problem ochrony prywatności. Firmach przezroczystości dotyczące zbierania danych, w jaki są gromadzone i do czego, którzy mogą zobaczyć, jak to, którzy kontrolują dostęp i tak dalej. Ponadto istnieją problemy ogólne bezpieczeństwo urządzeń wraz z osób, ich działania i obsługi standardów zgodności.

Biorąc pod uwagę zabezpieczeń, ochrony prywatności, przejrzystości i zgodności wątpliwości, wybór właściwego dostawcę rozwiązania IoT pozostaje wyzwanie. Łącząc pojedynczych oprogramowania IoT i usługi dostarczane przez wielu producentów wprowadza luki w zabezpieczeń, ochrony prywatności, przejrzystości i zgodności, które mogą okazać się trudne do wykrycia, nie mówiąc naprawić. Wybór prawo IoT dostawcy oprogramowania i usług opiera się na wyszukiwanie dostawców, którzy mają rozbudowane środowisko uruchamiania usług, które obejmują różnych branżowych i różnych lokalizacji geograficznych, ale są również możliwość skalowania w sposób bezpieczny i przejrzystości. Podobnie pomaga dla wybranego dostawcy mieć wieloletnim doświadczeniu programować bezpiecznego oprogramowanie, w systemie miliardów maszyn na całym świecie i mieć możliwość doceniają zagrożeniach powodowanego przez całkiem nowym świecie Internetu rzeczy.

## <a name="secure-infrastructure-from-the-ground-up"></a>Bezpieczną infrastrukturę od podstaw

[Microsoft Cloud](https://azure.microsoft.com) infrastruktury obsługuje ponad miliard klientów w krajach/regionach 127. Opierając się na firmy Microsoft wieloletnie doświadczenie w tworzeniu oprogramowania korporacyjnego i obsługiwaniu jednych z największych usług online na świecie, Microsoft Cloud przedstawiono wyższego poziomu zwiększonych zabezpieczeń, ochrony prywatności, zgodności i zagrożeń rozwiązania środki zaradcze niż większość klientów może osiągnąć własnych.

[Cykl projektowania zabezpieczeń (SDL)](https://www.microsoft.com/sdl/) udostępnia proces obowiązkowe rozwoju w firmie, który uwzględnia wymagania dotyczące bezpieczeństwa w cały cykl życia oprogramowania. Aby upewnić się, że działania operacyjne są wykonywane postępuj zgodnie z tym samym poziomie rozwiązania w zakresie zabezpieczeń, SDL korzysta z rygorystycznymi wytycznymi dotyczącymi zabezpieczeń w procesie Operational Security Assurance (OSA) firmy Microsoft. Firma Microsoft współpracuje również z innych firm audytorów trwającą weryfikacji, że spełnia ona swoich zobowiązań zgodności i Microsoft angażuje się w działaniach związanych z zabezpieczeniami szerokiego poprzez utworzenie centra doskonałości, w tym Microsoft Digital Crimes Unit, Microsoft Security Response Center i Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure — bezpiecznej infrastruktury IoT dla swojej firmy

Platforma Microsoft Azure oferuje rozwiązania w chmurze pełną, który łączy rosnący zbiór zintegrowanych usług w chmurze — analiza, uczenie maszynowe, magazynu, zabezpieczenia, sieci i sieci web — za pomocą zobowiązanie wiodące w branży ochronę i prywatność Twoich danych. Firmy Microsoft [założenie złamania zabezpieczeń](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) strategia używa dedykowanego *zespół* z dziedziny zabezpieczeń oprogramowania, którzy symulować ataków, testowanie możliwość wykrywania usługi Azure, ochrona przed narastającymi zagrożeniami i odzyskiwanie przed naruszeniami. Firmy Microsoft [reagowania na zdarzenia globalne](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) zespołu działa przez całą dobę w celu złagodzenia skutków ataków i złośliwych działań. Zespół następuje określone procedury zarządzania zdarzeniami, komunikacji i odzyskiwania i używa przewidywalny i prostsze do odnalezienia interfejsów wewnętrznych i zewnętrznych partnerów.

Systemy firmy Microsoft zapewniają ciągłe włamań i zapobiegania, zapobieganie atakom usługi, regularne testy penetracyjne i narzędzia informatyki śledczej pomagają identyfikować i minimalizować zagrożenia. [Uwierzytelnianie wieloskładnikowe](../articles/active-directory/authentication/multi-factor-authentication.md) zapewnia dodatkową warstwę zabezpieczeń dla użytkowników końcowych do dostępu do sieci. I dla aplikacji i dostawcy hosta, firma Microsoft oferuje kontroli dostępu, monitorowanie, ochrony przed złośliwym kodem, skanowanie luk w zabezpieczeniach, poprawki i zarządzanie konfiguracją.

Akceleratory rozwiązań zalet zabezpieczeń i ochrony prywatności wbudowane w platformę Azure oraz SDL i OSA procesów związanych z bezpieczeństwem programowania i działania oprogramowania ewaluacyjnego firmy Microsoft. Te procedury zapewniają ochronę infrastruktury, sieci ochronę i funkcje zarządzania tożsamościami i podstawowych zabezpieczeń dowolnego rozwiązania.

[Usługi Azure IoT Hub](../articles/iot-hub/about-iot-hub.md) w ramach [akceleratorów rozwiązań IoT](../articles/iot-fundamentals/iot-introduction.md) oferuje w pełni zarządzana usługa, która umożliwia bezpieczną i niezawodną komunikacji dwukierunkowej między urządzeniami IoT a usługami platformy Azure, takich jak [Usługi azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) i [usługi Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) przy użyciu poświadczeń zabezpieczeń na poziomie urządzenia i kontrola dostępu.

Aby komunikować się najlepiej w funkcje zabezpieczeń i prywatności wbudowane w akceleratorów rozwiązań Azure IoT, w tym artykule dzieli suite na trzy obszary głównych zabezpieczeń.

![Akceleratory rozwiązań Azure IoT](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Zabezpieczanie device provisioning i uwierzytelniania

Akceleratory rozwiązań zabezpieczania urządzeń, gdy znajdują się w polu, podając unikatową tożsamość klucza dla każdego urządzenia, która może służyć przez infrastrukturę IoT do komunikowania się z urządzeniem, gdy znajduje się w ramach operacji. Ten proces jest szybkie i łatwe do skonfigurowania. Wygenerowany klucz o identyfikatorze urządzenia wybrane przez użytkownika stanowi podstawę tokenu używane w całej komunikacji między urządzeniem i usługi Azure IoT Hub.

Identyfikatory urządzeń może być skojarzony z urządzeniem podczas produkcji, (który jest świeci w module zaufania sprzętu) lub użyć istniejącego stała tożsamość jako serwer proxy (na przykład numery seryjne Procesora). Ponieważ zmiana ta informacje identyfikacyjne na urządzeniu nie jest proste, należy wprowadzić identyfikatory urządzenia logicznego w przypadku podstawowej zmiany sprzętowe urządzenia, ale urządzenie logiczne pozostają bez zmian. W niektórych przypadkach możliwe skojarzenie tożsamości urządzenia w czasie wdrażania urządzenia (na przykład uwierzytelnionego specjalistą fizycznie konfiguruje nowe urządzenie podczas komunikowania się z zapleczem rozwiązania). [Rejestr tożsamości usługi Azure IoT Hub](../articles/iot-hub/iot-hub-devguide.md) zapewnia bezpieczny magazyn tożsamości urządzeń i klucze zabezpieczeń dla rozwiązania. Osoby lub grupy, tożsamości urządzenia można dodać do listy dozwolonych lub zablokowanych, umożliwiające pełną kontrolę nad ich dostępem.

Zasady kontroli dostępu Centrum IoT platformy Azure w chmurze Włącz aktywacji i wyłączanie dowolnej tożsamości urządzenia, dzięki czemu można usunąć skojarzenie urządzenia z wdrożenia usługi IoT, gdy jest to wymagane. To skojarzenie i usunięcia urządzenia opiera się na każdej tożsamości urządzenia.

Funkcje zabezpieczeń urządzeń:

* Urządzenia nie akceptują niechcianej komunikacji z sieci połączeń. Wszystkie połączenia i trasy one ustanowić w sposób umożliwiający tylko ruchu wychodzącego. Dla urządzenia, aby odebrać polecenie z zaplecza urządzenia muszą inicjować połączenia, aby sprawdzić, czy istnieją oczekujące polecenia do przetworzenia. Po bezpiecznie nawiązaniu połączenia między urządzeniem i Centrum IoT Hub komunikatów z chmury do urządzenia i urządzenia do chmury mogą być wysyłane sposób niewidoczny dla użytkownika.

* Urządzenia tylko połączyć się z lub ustanowić trasy, aby dobrze znane usługi, z którymi one równorzędne, takie jak usługi Azure IoT Hub.

* Uwierzytelnianie na poziomie systemu i uwierzytelniania używają tożsamościach poszczególnych urządzeń, dzięki czemu poświadczenia dostępu i uprawnień w pobliżu — natychmiast odwoływalny.

### <a name="secure-connectivity"></a>Łączność z protokołem Secure

Trwałość komunikatów jest ważną funkcją każde rozwiązanie IoT. Konieczność trwale dostarczać poleceń i/lub odbieranie danych z urządzeń jest podkreślony faktem, że urządzenia IoT są połączone za pośrednictwem Internetu lub innych podobnych sieci, które mogą być zawodne. Usługa Azure IoT Hub udostępnia niezawodności systemu obsługi komunikatów między chmurą i urządzeniami za pośrednictwem systemu potwierdzenia w odpowiedzi na wiadomości. Niezawodności na wypadek wystąpienia do obsługi komunikatów odbywa się przez buforowanie wiadomości w usłudze IoT Hub dla maksymalnie siedem dni dla danych telemetrycznych i dwa dni na potrzeby poleceń.

Wydajność ważne jest zapewnienie ochrony zasobów i operacji w środowisku o ograniczonych zasobach. HTTPS (HTTP bezpieczne), będące standardami branżowymi, bezpieczna wersja protokołu http popularne jest obsługiwana przez usługę Azure IoT Hub, umożliwiając sprawną komunikację. Zaawansowane komunikat (QUEUING Protocol) i komunikatów usługi kolejkowania MQTT (Telemetry Transport), obsługiwane przez usługę Azure IoT Hub są przeznaczone nie tylko w celu zwiększenia wydajności pod kątem wykorzystania zasobów, ale także niezawodne dostarczanie komunikatów. 

Skalowalność wymaga możliwości bezpiecznie współpracować z szerokiej gamy urządzeń. Usługa Azure IoT hub umożliwia bezpieczne nawiązywanie połączenia z adresu IP i włączony IP urządzeniami. Urządzenia z włączoną obsługą IP mają możliwość bezpośredniego łączenia oraz komunikacji z usługą IoT Hub za pośrednictwem bezpiecznego połączenia. Urządzenia — włączony IP są ograniczone przez zasób i Połącz tylko za pośrednictwem protokołów komunikacyjnych krótką odległość, takich jak Zwave ZigBee i Bluetooth. Bramy w terenie służy do agregacji tych urządzeń i dokonuje translacji protokołu, aby komunikacja zabezpieczona dwukierunkowych w chmurze.

Funkcje zabezpieczeń dodatkowego połączenia:

* Ścieżka komunikacji między urządzeniami i usługi Azure IoT Hub lub między bramami i usługi Azure IoT Hub, jest zabezpieczony za pomocą usługi Azure IoT Hub dokonało uwierzytelnienia przy użyciu protokołu X.509 przy użyciu standardowych zabezpieczeń TLS (Transport Layer).

* W celu ochrony urządzeń przed niechciane połączenia przychodzące, usługi Azure IoT Hub nie otworzy wszystkie połączenia z urządzeniem. Urządzenie inicjuje wszystkie połączenia.

* Usługa Azure IoT Hub trwale przechowuje komunikaty dla urządzeń i czeka na podłączenie urządzenia. Te polecenia są przechowywane przez dwa dni, konfigurowanie urządzeń, które sporadycznie, łączenie się ze względu na wątpliwości zasilaniem lub łącznością, aby otrzymać te polecenia. Usługa Azure IoT Hub utrzymuje kolejki na urządzenie, dla każdego urządzenia.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Zabezpieczanie przetwarzania i magazynowania w chmurze

Z szyfrowania komunikacji do przetwarzania danych w chmurze akceleratorów rozwiązań zabezpieczania danych. Zapewnia elastyczność, aby zaimplementować dodatkowe szyfrowanie i zarządzanie kluczami zabezpieczeń.

Przy użyciu usługi Azure Active Directory (AAD) dla uwierzytelniania i autoryzacji użytkowników, akceleratorów rozwiązań Azure IoT może zapewnić model na podstawie zasad autoryzacji dla danych w chmurze, umożliwiając łatwy dostęp do zarządzania, które mogą być poddany przeglądowi. Ten model umożliwia także niemal natychmiastowe odwoływanie dostępu do danych w chmurze i urządzeń podłączonych do akceleratorów rozwiązań Azure IoT.

Po umieszczeniu danych w chmurze, mogą przetwarzania i przechowywania w przepływie pracy zdefiniowanych przez użytkownika. Dostęp do każdej części danych jest kontrolowany przy użyciu usługi Azure Active Directory, w zależności od usługi storage używane.

Wszystkie klucze używane przez infrastrukturę IoT są przechowywane w chmurze w bezpiecznym magazynie, z możliwością przechodzą w przypadku, gdy klucze, należy je ponownie aprowizować. Dane mogą być przechowywane w [usługi Azure Cosmos DB](../articles/cosmos-db/introduction.md) lub [baz danych SQL](../articles/sql-database/sql-database-faq.md), włączenie definicji poziom zabezpieczeń żądanego. Ponadto system Azure oferuje możliwość monitorowania i inspekcji cały dostęp do danych o dowolnym włamań i nieautoryzowanym dostępem.

## <a name="conclusion"></a>Podsumowanie

Internet of Things zaczyna się od Twoich rzeczy, rzeczy, które są najważniejsze dla firm. IoT może dostarczać wspaniałe wartość do firmy przy jednoczesnym zmniejszeniu kosztów, zwiększanie przychodów i transformacji biznesowych. Powodzenie to przekształcenie dużym stopniu zależy od wybierania właściwego dostawcę oprogramowania i usług IoT. Oznacza to znajdowanie dostawcę, który nie tylko catalyzes tej transformacji, zrozumienie potrzeb biznesowych i wymagania, ale udostępnia również usług i oprogramowania utworzonych za pomocą zabezpieczeń, ochrony prywatności, przejrzystości i zgodności jako główne zagadnienia. Firma Microsoft ma rozbudowane środowisko programowania i wdrażania bezpiecznego oprogramowania i usług i należeć do tej nowej ery Internetu rzeczy za lidera w dalszym ciągu.

Akceleratory rozwiązań kompilacja w środki bezpieczeństwa, zgodnie z projektem, włączania bezpiecznego monitorowania zasobów, aby poprawić wydajność, wydajność operacyjnej dysku, aby włączyć innowacji i stosować zaawansowanej analizy danych do przekształcenia firmy. Dzięki warstwowego podejścia do zabezpieczeń, wielu funkcji zabezpieczeń i wzorce projektowe Akceleratory rozwiązań pomocy, wdrożyć infrastrukturę, która jest zaufany do przekształcania wszystkich firm.

## <a name="additional-information"></a>Dodatkowe informacje

Każdy akcelerator rozwiązań tworzy wystąpienia usług platformy Azure, takich jak:

* [**Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): Bramy łączącej z chmury do urządzeń. Dzięki obsłudze uwierzytelniania poszczególnych urządzeń pomaga zabezpieczyć rozwiązanie sieci, można skalować do milionów połączeń dla jednej Centrum i przetwarzania dużych ilości danych.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): Skalowalne, w pełni indeksowane usługa bazy danych dla danych z częściową strukturą, zarządzającą metadanych dla urządzeń, które można udostępnić, takie jak atrybuty, konfiguracji i właściwości zabezpieczeń. Usługa Azure Cosmos DB oferuje przetwarzanie o wysokiej wydajności i dużej przepustowości, niezależny od schematów indeksowanie danych i interfejs zaawansowanych zapytań SQL.

* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): Przetwarzanie w chmurze, która pozwala na szybkie tworzenie i wdrażanie rozwiązania analizy ekonomiczne zdobywania w czasie rzeczywistym z urządzeń, czujników, infrastruktury i aplikacji w czasie rzeczywistym strumienia. Dane z tej w pełni zarządzanej usługi można skalować do żadnego woluminu przy zachowaniu wysokiej przepływności, małego opóźnienia i elastyczność.

* [**Usługa Azure App Services**](https://azure.microsoft.com/services/app-service/): Platforma usług w chmurze do tworzenia zaawansowanych aplikacji internetowych i mobilnych łączących się z danymi przechowywanymi; w chmurze lub lokalnie. Twórz interesujące aplikacje mobilne dla systemów iOS, Android i Windows. Integracja z oprogramowaniem jako usługa (SaaS) oraz aplikacje dla przedsiębiorstw z łącznością poza pole do wielu usług w chmurze i aplikacje dla przedsiębiorstw. Kod w ulubionym języku i środowisku IDE — .NET, Node.js, PHP, Python lub Java — tworzenie aplikacji sieci web i interfejsy API szybciej niż kiedykolwiek wcześniej.

* [**Aplikacje logiki**](https://azure.microsoft.com/services/app-service/logic/): Funkcja Logic Apps w usłudze Azure App Service ułatwia integrowanie rozwiązania IoT do istniejących systemów line-of-business oraz automatyzowanie procesów przepływu pracy. Usługa Logic Apps umożliwia deweloperom projektowanie przepływów pracy rozpoczynających się od wyzwalacza, a następnie wykonanie serii czynności — reguły i akcje, które umożliwia zaawansowane łączniki integracji z procesów biznesowych. Usługa Logic Apps oferuje out-of--box łączności z obszernym ekosystemem SaaS i opartych na chmurze i aplikacji lokalnych.

* [**Usługa Azure Blob storage**](https://azure.microsoft.com/services/storage/): Magazyn w chmurze niezawodnego i ekonomicznego dla danych, które urządzenia są wysyłane do chmury.