---
title: Alerty zabezpieczeń w Azure Security Center | Microsoft Docs
description: W tym temacie opisano alerty zabezpieczeń i różne typy dostępne w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 514de1435519282335124bfd67bac82669240b78
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245514"
---
# <a name="security-alerts-in-azure-security-center"></a>Alerty zabezpieczeń w Centrum zabezpieczeń Azure

W Azure Security Center istnieje wiele alertów dla wielu różnych typów zasobów. Security Center generuje alerty dotyczące zasobów wdrożonych na platformie Azure, a także dla zasobów wdrożonych w środowiskach lokalnych i hybrydowych w chmurze.

Alerty zabezpieczeń są wyzwalane przez zaawansowane wykrywania i są dostępne tylko w warstwie Standardowa Azure Security Center. Dostępna jest bezpłatna wersja próbna. Uaktualnienie usługi jest możliwe po przejściu do wyboru warstwy cenowej na stronie [Zasady zabezpieczeń](security-center-pricing.md). Odwiedź [stronę usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/), aby dowiedzieć się więcej na temat cen.

## Reagowanie na współczesne <a name="respond-threats"></a> zagrożenia

W ciągu ostatnich 20 lat miały miejsce istotne zmiany dotyczące charakteru występujących zagrożeń. W przeszłości firmy martwiły się tylko atakami skutkującymi zmianą zawartości witryny internetowej, a sami atakujący chcieli przede wszystkim przekonać się, co potrafią zrobić. Obecnie osoby atakujące są bardziej wyrafinowane i zorganizowane. Często mają określone cele finansowe i strategiczne. Ponadto dysponują większą ilością zasobów, ponieważ mogą być finansowane przez państwa lub zorganizowane grupy przestępcze.

Te zmiany mogą doprowadziły do niewcześniejszego poziomu profesjonalizmu w rangach osoby atakującej. Nie wystarcza im już wpływ na zawartość witryn internetowych. Są one teraz zainteresowane kradzieżą informacji, kont finansowych i danych prywatnych, z których mogą korzystać w celu wygenerowania środków pieniężnych na otwartym rynku lub do korzystania z określonych pozycji gospodarczych, politycznych lub wojskowych. Bardziej niepokojący od osób o motywach finansowych są atakujący, którzy naruszają bezpieczeństwo sieci, aby uszkodzić infrastrukturę lub zaszkodzić ludziom.

W odpowiedzi na takie działania organizacje często wdrażają różne rozwiązania punktowe, które poprzez wyszukiwanie znanych sygnatur ataku koncentrują się na obronie obwodów lub punktów końcowych przedsiębiorstwa. Tego typu rozwiązania zazwyczaj generują duże ilości alertów niskiej jakości, które wymagają dokonania klasyfikacji i przeprowadzenia badań przez analityka zabezpieczeń. Większość organizacji nie dysponuje czasem i niezbędną wiedzą, które umożliwiłyby odpowiednie zareagowanie na te ostrzeżenia — wiele z nich nie jest rozwiązywanych.  

Ponadto osoby atakujące rozwinęły swoje metody w celu podwyższenia liczby zabezpieczeń opartych na sygnaturach i [adaptacji do środowisk chmurowych](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Wymagane jest nowe podejście do problemu zabezpieczeń, które pozwoliłoby na szybszą identyfikację potencjalnych zagrożeń, a także przyspieszone wykrywanie ich i reagowanie na nie.

## <a name="what-are-security-alerts"></a>Czym są alerty zabezpieczeń?

Alerty są powiadomieniami generowanymi przez Security Center w przypadku wykrycia zagrożeń dotyczących zasobów. Security Center priorytetów i wyświetla listę alertów wraz z informacjami wymaganymi do szybkiego zbadania problemu. Security Center również zawiera zalecenia dotyczące sposobu korygowania ataku.

## Jak Security Center wykrywać zagrożenia? <a name="detect-threats"> </a>

Pracownicy naukowo-badawczy firmy Microsoft stale poszukują nowych zagrożeń. Ze względu na globalną obecność firmy Microsoft w chmurze i lokalnie mają dostęp do zestawu danych telemetrycznych rozległych. Szeroka i różnorodna kolekcja zestawów danych umożliwia odnalezienie nowych wzorców ataków i trendów w lokalnych produktach konsumenckich i firmowych, a także Usługi online. W związku z tym usługa Security Center może szybko zaktualizować swoje algorytmy wykrywania, w miarę jak atakujący wprowadzają nowe i coraz bardziej zaawansowane metody. Takie podejście pomaga sprostać wymaganiom szybko zmieniającego się środowiska zagrożenia.

Aby wykrywać rzeczywiste zagrożenia i zmniejszyć liczbę fałszywych wyników, Security Center gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure i sieci. Współpracuje również z rozwiązaniami partnerskimi, takimi jak zapora i rozwiązania programu Endpoint Protection. Security Center analizuje te informacje, często skorelowane informacje z wielu źródeł, aby identyfikować zagrożenia.

![Prezentacja i zbieranie danych przez usługę Security Center](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Usługa Security Center wykorzystuje zaawansowane narzędzia analizy zabezpieczeń, które wykraczają daleko poza metody bazujące na sygnaturze. Przełomowe rozwiązania dotyczące danych big data i technologii [uczenia maszynowego](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) są używane do oceny zdarzeń zachodzących w całej sieci szkieletowej chmury. Wykrywane są zagrożenia, które byłyby niemożliwe do wykrycia przy użyciu ręcznych metod, i przewidywany jest kierunek ewolucji ataków. Do narzędzi analizy zabezpieczeń należą:

* **Zintegrowana analiza zagrożeń**: szuka znanych nieprawidłowych aktorów, wykorzystując globalne analizy zagrożeń pochodzące z produktów i usług firmy Microsoft, jednostki Microsoft Digital zbrodnis (DCU), centrum Microsoft Security Response Center (MSRC) i zewnętrznych źródeł danych.
* **Analiza zachowań**: stosuje znane wzorce w celu odnalezienia złośliwego zachowania.
* **Wykrywanie anomalii**: używa profilowania statystycznego do tworzenia historycznej linii bazowej. Narzędzie to alarmuje o odchyleniach od ustalonych linii bazowych zgodnych z wektorem potencjalnego ataku.

W poniższych sekcjach szczegółowo omówiono każdą z tych analiz.

### <a name="integrated-threat-intelligence"></a>Zintegrowana analiza zagrożeń

Firma Microsoft dysponuje ogromną ilością danych do globalnej analizy zagrożeń. Dane telemetryczne są przepływów z wielu źródeł, takich jak Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Zbrodnis Unit (DCU) i Microsoft Security Response Center (MSRC). Badacze otrzymują również informacje o analizie zagrożeń, które są współużytkowane przez głównych dostawców usług w chmurze i źródła danych od innych stron trzecich. Usługa Azure Security Center może użyć tych informacji, aby ostrzec użytkownika przed zagrożeniami ze strony znanych złośliwych podmiotów.

### <a name="behavioral-analytics"></a>Analiza behawioralna

Analiza behawioralna to metoda, która polega na analizie danych i porównywaniu ich z kolekcją znanych wzorców. Wzorce te nie są jednak prostymi sygnaturami. Określa się je za pośrednictwem złożonych algorytmów uczenia maszynowego, które są stosowane w przypadku wielkich zestawów danych. Są one również określane przez specjalistów od analizy, którzy dokonują dokładnej analizy złośliwych zachowań. Azure Security Center może korzystać z analizy behawioralnej w celu identyfikowania zasobów narażonych na podstawie analizy dzienników maszyn wirtualnych, dzienników urządzeń sieci wirtualnej, dzienników sieci szkieletowej, zrzutów awaryjnych i innych źródeł.

Ponadto istnieje korelacja z innymi sygnałami w celu sprawdzenia, czy są dostępne dodatkowe dowody na szeroką kampanię. Ta korelacja pomaga w identyfikacji zdarzeń, które są zgodne ze sprawdzonymi wskaźnikami naruszenia bezpieczeństwa. 

### <a name="anomaly-detection"></a>Wykrywanie anomalii

Usługa Azure Security Center używa również wykrywania anomalii do identyfikowania zagrożeń. W przeciwieństwie do analizy behawioralnej (która zależy od znanych wzorców wyprowadzonych z dużych zestawów danych), wykrywanie anomalii jest bardziej „spersonalizowane” i koncentruje się na liniach bazowych, które są specyficzne dla przeprowadzonych wdrożeń. Uczenie maszynowe jest stosowane do określania normalnego działania wdrożeń, a następnie generowania reguł definiujących odstające warunki, które mogą reprezentować zdarzenie związane z zabezpieczeniami.

## <a name="how-are-alerts-classified"></a>Jak są klasyfikowane alerty?

Security Center przypisuje ważność do alertów, ułatwiając określanie priorytetów kolejności, w której uczestniczy każdy alert, dzięki czemu w przypadku naruszenia zabezpieczeń zasobów można od razu przejść do niego. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

> [!NOTE]
> Ważność alertu jest wyświetlana w różny sposób w portalu i wersjach interfejsu API REST, który jest przedstawiony jako 01-01-2019. Jeśli używasz starszej wersji interfejsu API, Uaktualnij środowisko pod kątem spójnego środowiska opisanego poniżej.

- **Wysoka:** Istnieje duże prawdopodobieństwo naruszenia bezpieczeństwa zasobu. Należy od razu przyjrzeć się do niego. Security Center ma wysoki poziom zaufania zarówno w złośliwym zamiarach, jak i w ustaleniach używanych do wystawiania alertu. Na przykład alert, który wykrywa wykonywanie znanego złośliwego narzędzia, takiego jak program mimikatz, typowego narzędzia używanego do kradzieży poświadczeń.
- **Średni:** Prawdopodobnie podejrzane działanie może wskazywać na naruszenie bezpieczeństwa zasobu.
Stopień zaufania Security Center w analitycznym lub wyszukiwaniu jest średni, a wiarygodność złośliwego celu jest wysoka. Zazwyczaj mogą to być Uczenie maszynowe lub wykryte anomalie. Na przykład próba logowania z nietypowej lokalizacji.
- **Niska:** Może to być niegroźne pozytywne lub zablokowany atak.
   * Security Center nie ma wystarczającej pewności, że zamiar jest złośliwy, a działanie może być nieszkodliwe. Na przykład dziennik czyszczenie jest akcją, która może wystąpić, gdy osoba atakująca próbuje ukryć swoje ścieżki, ale w wielu przypadkach jest to procedura wykonywana przez administratorów.
   * Security Center zazwyczaj nie informuje użytkownika o zablokowaniu ataków, chyba że jest to interesujący przypadek, który sugerujemy. 
- **Informacje:** Podczas przechodzenia do szczegółów zdarzenia związanego z bezpieczeństwem będą wyświetlane tylko alerty informacyjne lub użycie interfejsu API REST z określonym IDENTYFIKATORem alertu. Zdarzenie zwykle składa się z wielu alertów, które mogą być wyświetlane na własne potrzeby tylko w celu uzyskania informacji, ale w kontekście innych alertów może być zaufanego bliższego wyglądu. 
 

## <a name="continuous-monitoring-and-assessments"></a>Ciągłe monitorowanie i oceny

Azure Security Center korzyści ze stosowania przez firmę Microsoft zespołów badawczych i naukowych dotyczących zabezpieczeń, którzy stale monitorują zmiany w poziomie zagrożeń. Obejmuje to następujące inicjatywy:

* **Monitorowanie analizy zagrożeń**: analiza zagrożeń obejmuje mechanizmy, wskaźniki, implikacje i porady umożliwiające podejmowanie działań dotyczących istniejących lub powstających zagrożeń. Te informacje są udostępniane w branży zabezpieczeń, a firma Microsoft stale monitoruje zagrożenia płynące z wewnętrznych i zewnętrznych źródeł.
* **Udostępnianie sygnału**: uwagi zespołów ds. zabezpieczeń formułowane na podstawie informacji zebranych z szerokiej gamy produktów firmy Microsoft, takich jak usługi w chmurze i usługi lokalne, serwery i urządzenia klienckie w punkcie końcowym, są udostępniane i analizowane.
* **Specjaliści ds. zabezpieczeń firmy Microsoft**: ciągła współpraca zespołów firmy Microsoft w zakresie wyspecjalizowanych zabezpieczeń, takich jak analiza śledcza i wykrywanie ataków w sieci Web.
* **Dostrajanie wykrywania zagrożeń**: algorytmy są uruchamiane na rzeczywistych zestawach danych klienta, a pracownicy naukowo-badawczy z zakresu zabezpieczeń pracują z klientami w celu weryfikacji otrzymanych wyników. Wyniki prawdziwie i fałszywie dodatnie są używane w celu udoskonalania algorytmów uczenia maszynowego.

Wszystkie te połączone wysiłki skutkują nowymi i ulepszonymi sposobami wykrywania zagrożeń, z których można korzystać od razu — użytkownik nie musi w tym kierunku nic robić.

## Typy <a name="security-alert-types"></a> alertów zabezpieczeń

Poniższe tematy przeprowadzą Cię przez różne alerty, zgodnie z typami zasobów:

* [Alerty dla maszyn IaaS z systemem Windows](threat-protection.md#windows-machines)
* [Alerty dla maszyn z systemem IaaS Linux](threat-protection.md#linux-machines)
* [Alerty dla Azure App Service](threat-protection.md#app-services)
* [Alerty dla kontenerów platformy Azure](threat-protection.md#azure-containers)
* [Alerty dla SQL Database i SQL Data Warehouse](threat-protection.md#data-sql)
* [Alerty dotyczące usługi Azure Storage](threat-protection.md#azure-storage)
* [Alerty dla Cosmos DB](threat-protection.md#cosmos-db)

W poniższych tematach opisano, jak Security Center używa różnych danych telemetrycznych zbieranych z integracji z infrastrukturą platformy Azure w celu zastosowania dodatkowych warstw ochrony dla zasobów wdrożonych na platformie Azure:

* [Alerty dla warstwy zarządzania Azure (Azure Resource Manager) (wersja zapoznawcza)](threat-protection.md#management-layer)
* [Alerty dla Azure Key Vault (wersja zapoznawcza)](threat-protection.md#azure-keyvault)
* [Alerty dla warstwy sieci platformy Azure](threat-protection.md#network-layer)
* [Alerty z innych usług](threat-protection.md#alerts-other)

## <a name="what-are-security-incidents"></a>Co to są zdarzenia związane z bezpieczeństwem?

Zdarzenie zabezpieczeń to kolekcja powiązanych alertów, a nie każdego pojedynczego alertu. Security Center używa [korelacji alertów inteligentnych w chmurze](security-center-alerts-cloud-smart.md) w celu skorelowania różnych alertów i niskiej dokładności sygnałów do zdarzeń zabezpieczeń.

Korzystając z incydentów, Security Center zapewnia jeden widok kampanii ataku i wszystkich powiązanych alertów. Ten widok umożliwia szybkie zrozumienie działań podejmowanych przez osobę atakującą oraz zasobów, których dotyczy. Aby uzyskać więcej informacji, zobacz [korelacja alertów inteligentnych w chmurze](security-center-alerts-cloud-smart.md).

## <a name="security-alerts-in-azure-activity-log"></a>Alerty zabezpieczeń w dzienniku aktywności platformy Azure

Oprócz udostępniania w Azure Portal lub programowo, alerty zabezpieczeń i zdarzenia są poddawane inspekcji jako zdarzenia w [dzienniku aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view). Aby uzyskać więcej informacji o schemacie zdarzeń, zobacz [alerty zabezpieczeń w dzienniku aktywności platformy Azure](https://go.microsoft.com/fwlink/?linkid=2114113).

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o różnych typach alertów dostępnych w Security Center. Aby uzyskać więcej informacji, zobacz:

* [Przewodnik planowania i obsługi usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center — często zadawane pytania](https://docs.microsoft.com/azure/security-center/security-center-faq)

