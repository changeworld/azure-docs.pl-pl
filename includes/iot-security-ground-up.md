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
ms.openlocfilehash: 128a2e8b200f1323b88aad635f27c1b686ecbed2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72789714"
---
Internet rzeczy (IoT) stanowi wyjątkowe wyzwania w zakresie bezpieczeństwa, prywatności i zgodności z przepisami dla firm na całym świecie. W przeciwieństwie do tradycyjnej technologii cybernetycznej, w której problemy te koncentrują się wokół oprogramowania i sposobu jego wdrażania, IoT dotyczy tego, co się dzieje, gdy światy cybernetyczne i fizyczne zbiegają się. Ochrona rozwiązań IoT wymaga zapewnienia bezpiecznego inicjowania obsługi administracyjnej urządzeń, bezpiecznej łączności między tymi urządzeniami a chmurą oraz bezpiecznej ochrony danych w chmurze podczas przetwarzania i przechowywania. Działanie z takimi funkcjami to jednak urządzenia ograniczone zasobami, geograficzne rozmieszczenie wdrożeń i duża liczba urządzeń w ramach rozwiązania.

W tym artykule opisano, jak akceleratory rozwiązań IoT zapewniają bezpieczne i prywatne rozwiązanie w chmurze Internetu rzeczy. Akceleratory rozwiązań zapewniają kompleksowe rozwiązanie, z zabezpieczeniami wbudowanymi w każdy etap od podstaw. W firmie Microsoft tworzenie bezpiecznego oprogramowania jest częścią praktyki inżynierii oprogramowania, zakorzenionej w wieloletnim doświadczeniu firmy Microsoft w opracowywaniu bezpiecznego oprogramowania. Aby to zapewnić, cykl rozwoju zabezpieczeń (SDL) jest podstawową metodologią rozwoju, w połączeniu z wieloma usługami zabezpieczeń na poziomie infrastruktury, takimi jak Operational Security Assurance (OSA) i Microsoft Digital Crimes Unit, Microsoft Centrum odpowiedzi na zagrożenia i Centrum ochrony przed złośliwym oprogramowaniem firmy Microsoft.

Akceleratory rozwiązań oferują unikalne funkcje, które ułatwiają i przechowują dane z urządzeń IoT, a przede wszystkim są bezpieczne. W tym artykule ocieka się o funkcje zabezpieczeń i strategie wdrażania akceleratorów rozwiązań Azure IoT, aby zapewnić rozwiązania problemów związanych z bezpieczeństwem, prywatnością i zgodnością.

## <a name="introduction"></a>Wprowadzenie

Internet rzeczy (IoT) to fala przyszłości, oferująca firmom natychmiastowe i rzeczywiste możliwości obniżenia kosztów, zwiększenia przychodów i przekształcenia ich działalności. Wiele firm waha się jednak wdrożyć IoT w swoich organizacjach ze względu na obawy dotyczące zabezpieczeń, prywatności i zgodności. Głównym problemem jest wyjątkowość infrastruktury IoT, która łączy światy cybernetyczne i fizyczne, potęgując indywidualne zagrożenia związane z tymi dwoma światami. Bezpieczeństwo IoT odnosi się do zapewnienia integralności kodu uruchomionego na urządzeniach, zapewnienia uwierzytelniania urządzeń i użytkowników, definiowania wyraźnej własności urządzeń (a także danych generowanych przez te urządzenia) oraz odporności na ataki cybernetyczne i fizyczne.

Następnie pojawia się kwestia prywatności. Firmy chcą przejrzystości w zakresie gromadzenia danych, jak w tym, co jest zbierane i dlaczego, kto może je zobaczyć, kto kontroluje dostęp i tak dalej. Wreszcie, istnieją ogólne kwestie bezpieczeństwa sprzętu wraz z osobami je obsługującymi, a także kwestie utrzymania branżowych standardów zgodności.

Biorąc pod uwagę kwestie bezpieczeństwa, prywatności, przejrzystości i zgodności, wybór odpowiedniego dostawcy rozwiązań IoT pozostaje wyzwaniem. Łączenie poszczególnych elementów oprogramowania i usług IoT dostarczanych przez różnych dostawców wprowadza luki w bezpieczeństwie, prywatności, przejrzystości i zgodności, które mogą być trudne do wykrycia, nie mówiąc już o naprawieniu. Wybór odpowiedniego oprogramowania i dostawcy usług IoT opiera się na znalezieniu dostawców, którzy mają duże doświadczenie w obsłudze usług, które obejmują branże i regiony geograficzne, ale są również w stanie skalować w bezpieczny i przejrzysty sposób. Podobnie, to pomaga dla wybranego dostawcy mieć wieloletnie doświadczenie w opracowywaniu bezpiecznego oprogramowania działającego na miliardy maszyn na całym świecie, i mają zdolność do doceniania krajobraz zagrożenia stwarzane przez ten nowy świat Internetu rzeczy.

## <a name="secure-infrastructure-from-the-ground-up"></a>Bezpieczna infrastruktura od podstaw

Infrastruktura [Microsoft Cloud](https://azure.microsoft.com) obsługuje ponad miliard klientów w 127 krajach/regionach. Opierając się na wieloletnim doświadczeniu firmy Microsoft w tworzeniu oprogramowania dla przedsiębiorstw i uruchamianiu jednych z największych usług online na świecie, chmura Microsoft cloud zapewnia wyższy poziom zwiększonych zabezpieczeń, prywatności, zgodności i praktyk ograniczania zagrożeń niż większość klientów może osiągnąć samodzielnie.

[Cykl rozwoju zabezpieczeń (SDL)](https://www.microsoft.com/sdl/) zapewnia obowiązkowy proces programowania w całej firmie, który osadza wymagania dotyczące zabezpieczeń w całym cyklu życia oprogramowania. Aby zapewnić, że działania operacyjne są zgodne z tymi samymi praktykami dotyczącymi zabezpieczeń, SDL używa rygorystycznych wytycznych dotyczących zabezpieczeń określonych w procesie osa (Operational Security Assurance) firmy Microsoft. Firma Microsoft współpracuje również z zewnętrznymi firmami audytorzymi w celu bieżącej weryfikacji, czy spełnia swoje zobowiązania w zakresie zgodności, a firma Microsoft angażuje się w szeroko zakrojone działania w zakresie bezpieczeństwa poprzez tworzenie centrów doskonałości, w tym Microsoft Digital Crimes Unit, Centrum odpowiedzi zabezpieczeń firmy Microsoft i Centrum ochrony przed złośliwym oprogramowaniem firmy Microsoft.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure — bezpieczna infrastruktura IoT dla Twojej firmy

Platforma Microsoft Azure oferuje kompletne rozwiązanie chmurowe, które łączy stale rosnącą kolekcję zintegrowanych usług w chmurze — analityki, uczenia maszynowego, pamięci masowej, zabezpieczeń, sieci i internetu — z wiodącym w branży zobowiązaniem do ochrony i prywatności twoich danych. Strategia firmy Microsoft [zakłada, że naruszenia](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) wykorzystuje dedykowany *czerwony zespół* ekspertów w dziedzinie bezpieczeństwa oprogramowania, którzy symulują ataki, testując zdolność platformy Azure do wykrywania, ochrony przed pojawiającymi się zagrożeniami i odzyskiwania po naruszeniach. Globalny zespół [reagowania na incydenty](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) firmy Microsoft pracuje przez całą dobę, aby złagodzić skutki ataków i złośliwych działań. Zespół stosuje ustalone procedury zarządzania zdarzeniami, komunikacji i odzyskiwania i korzysta z wykrywalnych i przewidywalnych interfejsów z partnerami wewnętrznymi i zewnętrznymi.

Systemy firmy Microsoft zapewniają ciągłe wykrywanie włamań i zapobieganie im, zapobieganie atakom serwisowym, regularne testy penetracyjne i narzędzia kryminalistyczne, które pomagają identyfikować i ograniczać zagrożenia. [Uwierzytelnianie wieloskładnikowe](../articles/active-directory/authentication/multi-factor-authentication.md) zapewnia dodatkową warstwę zabezpieczeń dla użytkowników końcowych w celu uzyskania dostępu do sieci. A dla aplikacji i dostawcy hosta firma Microsoft oferuje kontrolę dostępu, monitorowanie, ochrona przed złośliwym oprogramowaniem, skanowanie luk w zabezpieczeniach, poprawki i zarządzanie konfiguracją.

Akceleratory rozwiązań wykorzystują zabezpieczenia i prywatność wbudowane w platformę Azure wraz z procesami SDL i OSA w celu bezpiecznego tworzenia i działania całego oprogramowania firmy Microsoft. Procedury te zapewniają ochronę infrastruktury, ochronę sieci oraz funkcje tożsamości i zarządzania podstawowe dla bezpieczeństwa każdego rozwiązania.

[Usługa Azure IoT Hub](../articles/iot-hub/about-iot-hub.md) w ramach [akceleratorów rozwiązań IoT](../articles/iot-fundamentals/iot-introduction.md) oferuje w pełni zarządzaną usługę, która umożliwia niezawodną i bezpieczną dwukierunkową komunikację między urządzeniami IoT a usługami platformy Azure, takimi jak [azure machine learning](../articles/machine-learning/studio/what-is-machine-learning.md) i usługa Azure [Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) przy użyciu poświadczeń zabezpieczeń na urządzeniu i kontroli dostępu.

Aby najlepiej komunikować funkcje zabezpieczeń i prywatności wbudowane w akceleratory rozwiązań Azure IoT, w tym artykule dzieli pakiet na trzy podstawowe obszary zabezpieczeń.

![Akceleratory rozwiązań Azure IoT](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Bezpieczne inicjowanie obsługi administracyjnej i uwierzytelnianie urządzeń

Akceleratory rozwiązań zabezpieczają urządzenia, gdy znajdują się w terenie, udostępniając unikatowy klucz tożsamości dla każdego urządzenia, który może być używany przez infrastrukturę IoT do komunikowania się z urządzeniem podczas jego pracy. Proces jest szybki i łatwy w konfiguracji. Wygenerowany klucz z identyfikatorem urządzenia wybranego przez użytkownika stanowi podstawę tokenu używanego we wszystkich komunikacji między urządzeniem a centrum Azure IoT Hub.

Identyfikatory urządzeń mogą być skojarzone z urządzeniem podczas produkcji (czyli migane w module zaufania sprzętu) lub mogą używać istniejącej tożsamości stałej jako serwera proxy (na przykład numerów seryjnych procesora CPU). Ponieważ zmiana tych informacji identyfikujących w urządzeniu nie jest prosta, ważne jest wprowadzenie identyfikatorów urządzeń logicznych w przypadku zmiany sprzętu urządzenia źródłowego, ale urządzenie logiczne pozostaje takie samo. W niektórych przypadkach skojarzenie tożsamości urządzenia może się zdarzyć w czasie wdrażania urządzenia (na przykład uwierzytelniony inżynier pola fizycznie konfiguruje nowe urządzenie podczas komunikowania się z zapleczem rozwiązania). [Rejestr tożsamości usługi Azure IoT Hub](../articles/iot-hub/iot-hub-devguide.md) zapewnia bezpieczne przechowywanie tożsamości urządzeń i kluczy zabezpieczeń dla rozwiązania. Do listy dozwolonych lub listy zablokowanych można dodać poszczególne lub grupy tożsamości urządzeń, co umożliwia pełną kontrolę nad dostępem do urządzenia.

Zasady kontroli dostępu usługi Azure IoT Hub w chmurze umożliwiają aktywację i wyłączenie dowolnej tożsamości urządzenia, zapewniając sposób odłączania urządzenia od wdrożenia IoT, gdy jest to wymagane. To skojarzenie i rozłączenie urządzeń opiera się na każdej tożsamości urządzenia.

Dodatkowe funkcje zabezpieczeń urządzenia obejmują:

* Urządzenia nie akceptują niechcianych połączeń sieciowych. Ustanawiają wszystkie połączenia i trasy w sposób tylko wychodzący. Aby urządzenie otrzymało polecenie z wewnętrznej bazy danych, urządzenie musi zainicjować połączenie, aby sprawdzić, czy nie ma żadnych oczekujących poleceń do przetworzenia. Po nawiązaniu połączenia między urządzeniem a centrum IoT Hub wiadomości z chmury do urządzenia i urządzenia do chmury mogą być wysyłane w sposób przezroczysty.

* Urządzenia łączą się tylko z dobrze znanymi usługami, z którymi są równorzędne, takimi jak usługa Azure IoT Hub, lub ustanawiają trasy do dobrze znanych usług, z którymi są równorzędne.

* Autoryzacja na poziomie systemu i uwierzytelnianie używają tożsamości na urządzeniu, dzięki czemu poświadczenia dostępu i uprawnienia są niemal natychmiast odwołalne.

### <a name="secure-connectivity"></a>Bezpieczna łączność

Trwałość wiadomości jest ważną cechą każdego rozwiązania IoT. Konieczność trwałego dostarczania poleceń i/lub odbierania danych z urządzeń jest podkreślona przez fakt, że urządzenia IoT są połączone przez Internet lub inne podobne sieci, które mogą być zawodne. Usługa Azure IoT Hub oferuje trwałość obsługi wiadomości między chmurą a urządzeniami za pośrednictwem systemu potwierdzeń w odpowiedzi na wiadomości. Dodatkową trwałość wiadomości uzyskuje się przez buforowanie wiadomości w Centrum IoT przez maksymalnie siedem dni dla telemetrii i dwa dni dla poleceń.

Wydajność jest ważna dla zapewnienia ochrony zasobów i działania w środowisku ograniczonym zasobami. Protokół HTTPS (HTTP Secure), standardowa w branży bezpieczna wersja popularnego protokołu http, jest obsługiwana przez usługę Azure IoT Hub, umożliwiając wydajną komunikację. Zaawansowany protokół amqp (Advanced Message Usłudze kolejkowania) i transport telemetrii usługi kolejkowania wiadomości (MQTT), obsługiwany przez usługę Azure IoT Hub, są zaprojektowane nie tylko z myślą o wydajności pod względem wykorzystania zasobów, ale także niezawodnego dostarczania wiadomości. 

Skalowalność wymaga możliwości bezpiecznego współdziałania z szeroką gamą urządzeń. Usługa Azure IoT hub umożliwia bezpieczne połączenie z urządzeniami obsługującymi protokół IP i nieobjętymi funkcjami IP. Urządzenia z obsługą protokołu IP mogą bezpośrednio łączyć się z centrum IoT Hub i komunikować się z nią za pomocą bezpiecznego połączenia. Urządzenia nieobjęte protokołami z obsługą protokołu IP są ograniczone zasobami i łączą się tylko za pośrednictwem protokołów komunikacji na krótkich dystansach, takich jak Zwave, ZigBee i Bluetooth. Brama polowa służy do agregowania tych urządzeń i wykonuje translację protokołu, aby umożliwić bezpieczną dwukierunkową komunikację z chmurą.

Dodatkowe funkcje zabezpieczeń połączenia obejmują:

* Ścieżka komunikacji między urządzeniami i usługą Azure IoT Hub lub między bramami a usługą Azure IoT Hub jest zabezpieczona przy użyciu standardowych zabezpieczeń warstwy transportu (TLS) standardu transportu (TLS) z usługą Azure IoT Hub uwierzytelnioną przy użyciu protokołu X.509.

* Aby chronić urządzenia przed niechcianymi połączeniami przychodzącymi, usługa Azure IoT Hub nie otwiera żadnego połączenia z urządzeniem. Urządzenie inicjuje wszystkie połączenia.

* Usługa Azure IoT Hub trwale przechowuje komunikaty dla urządzeń i czeka na połączenie urządzenia. Polecenia te są przechowywane przez dwa dni, umożliwiając urządzeniom łączącym się sporadycznie, ze względu na problemy z zasilaniem lub łącznością, odbieranie tych poleceń. Usługa Azure IoT Hub utrzymuje kolejkę na urządzenie dla każdego urządzenia.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Bezpieczne przetwarzanie i przechowywanie w chmurze

Od szyfrowanej komunikacji do przetwarzania danych w chmurze, akceleratory rozwiązań pomagają chronić dane. Zapewnia elastyczność implementacji dodatkowego szyfrowania i zarządzania kluczami zabezpieczeń.

Za pomocą usługi Azure Active Directory (AAD) do uwierzytelniania i autoryzacji użytkowników, akceleratory rozwiązań Usługi Azure IoT może zapewnić model autoryzacji oparty na zasadach dla danych w chmurze, umożliwiając zarządzanie łatwy dostęp, które mogą być poddane inspekcji i przeglądowi. Ten model umożliwia również niemal natychmiastowe cofnięcie dostępu do danych w chmurze i urządzeń połączonych z akceleratorami rozwiązań Azure IoT.

Gdy dane są w chmurze, mogą być przetwarzane i przechowywane w dowolnym przepływie pracy zdefiniowanym przez użytkownika. Dostęp do każdej części danych jest kontrolowany za pomocą usługi Azure Active Directory, w zależności od używanej usługi magazynu.

Wszystkie klucze używane przez infrastrukturę IoT są przechowywane w chmurze w bezpiecznym magazynie, z możliwością przerzucenia w przypadku, gdy klucze muszą zostać ponownie aprowizowane. Dane mogą być przechowywane w [usłudze Azure Cosmos DB](../articles/cosmos-db/introduction.md) lub w [bazach danych SQL,](../articles/sql-database/sql-database-faq.md)umożliwiając określenie żądanego poziomu zabezpieczeń. Ponadto platforma Azure umożliwia monitorowanie i inspekcję całego dostępu do danych w celu ostrzegania o wszelkich włamaniach lub nieautoryzowanym dostępie.

## <a name="conclusion"></a>Podsumowanie

Internet rzeczy zaczyna się od twoich rzeczy — rzeczy, które mają największe znaczenie dla firm. IoT może zapewnić niesamowitą wartość firmie, zmniejszając koszty, zwiększając przychody i przekształcając działalność. Powodzenie tej transformacji w dużej mierze zależy od wyboru odpowiedniego oprogramowania i dostawcy usług IoT. Oznacza to znalezienie dostawcy, który nie tylko katali tej transformacji poprzez zrozumienie potrzeb biznesowych i wymagań, ale także świadczy usługi i oprogramowanie zbudowane z bezpieczeństwa, prywatności, przejrzystości i zgodności jako główne zagadnienia projektowe. Firma Microsoft ma duże doświadczenie w opracowywaniu i wdrażaniu bezpiecznego oprogramowania i usług i nadal jest liderem w nowej epoce Internetu rzeczy.

Akceleratory rozwiązań opierają się na projektowaniu środków bezpieczeństwa, umożliwiając bezpieczne monitorowanie zasobów w celu poprawy wydajności, zwiększenia wydajności operacyjnej w celu umożliwienia innowacji i wykorzystania zaawansowanej analizy danych do przekształcania firm. Dzięki warstwowemu podejściu do zabezpieczeń, wielu funkcjom zabezpieczeń i wzorcom projektowym akceleratory rozwiązań pomagają wdrożyć infrastrukturę, którym można zaufać, aby przekształcić każdą firmę.

## <a name="additional-information"></a>Dodatkowe informacje

Każdy akcelerator rozwiązań tworzy wystąpienia usług platformy Azure, takich jak:

* [**Usługa Azure IoT Hub:**](https://azure.microsoft.com/services/iot-hub/)Brama łącząca chmurę z urządzeniami. Możesz skalować do milionów połączeń na koncentrator i przetwarzać ogromne ilości danych za pomocą obsługi uwierzytelniania na urządzeniu, która pomaga zabezpieczyć rozwiązanie.

* [**Usługa Azure Cosmos DB:**](https://azure.microsoft.com/services/cosmos-db/)skalowalna, w pełni indeksowana usługa bazy danych dla danych częściowo ustrukturyzowanych, która zarządza metadanymi dla urządzeń, które aprowizujesz, takich jak atrybuty, konfiguracja i właściwości zabezpieczeń. Usługa Azure Cosmos DB oferuje wydajne i wydajne przetwarzanie, indeksowanie danych w programie agnostycznym i bogaty interfejs zapytań SQL.

* [**Usługa Azure Stream Analytics:**](https://azure.microsoft.com/services/stream-analytics/)przetwarzanie strumienia w czasie rzeczywistym w chmurze, które umożliwia szybkie opracowywanie i wdrażanie taniego rozwiązania analitycznego w celu wykrywania szczegółowych informacji w czasie rzeczywistym z urządzeń, czujników, infrastruktury i aplikacji. Dane z tej w pełni zarządzanej usługi można skalować do dowolnego woluminu, jednocześnie osiągając wysoką przepustowość, małe opóźnienia i odporność.

* [**Usługi Azure App Services:**](https://azure.microsoft.com/services/app-service/)platforma w chmurze do tworzenia zaawansowanych aplikacji internetowych i mobilnych, które łączą się z danymi w dowolnym miejscu; w chmurze lub lokalnie. Twórz atrakcyjne aplikacje mobilne dla systemów iOS, Android i Windows. Integracja z oprogramowaniem jako usługą (SaaS) i aplikacjami dla przedsiębiorstw z nieojską łącznością z dziesiątkami usług chmurowych i aplikacji korporacyjnych. Koduj w ulubionym języku i IDE-.NET, Node.js, PHP, Python lub Java — aby tworzyć aplikacje internetowe i interfejsy API szybciej niż kiedykolwiek wcześniej.

* [**Aplikacje logiki:**](https://azure.microsoft.com/services/app-service/logic/)Funkcja aplikacji logiki usługi Azure App Service pomaga zintegrować rozwiązanie IoT z istniejącymi systemami biznesowymi i zautomatyzować procesy przepływu pracy. Aplikacja logika umożliwia deweloperom projektowanie przepływów pracy, które rozpoczynają się od wyzwalacza, a następnie wykonywanie serii kroków — reguł i akcji, które używają zaawansowanych łączników do integracji z procesami biznesowymi. Logic Apps oferuje gotowe połączenie z rozległym ekosystemem SaaS, aplikacji opartych na chmurze i lokalnych.

* [**Magazyn obiektów Blob platformy Azure:**](https://azure.microsoft.com/services/storage/)niezawodne, ekonomiczne miejsce w chmurze dla danych wysyłanych przez urządzenia do chmury.