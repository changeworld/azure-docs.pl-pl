---
title: Co to jest Azure Security Center? | Microsoft Docs
description: Uzyskaj informacje na temat Centrum zabezpieczeń Azure, jego kluczowych możliwości i sposobu działania.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 682291cfe0e7a06aca1d4b1bef48ba36c72f48d9
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839878"
---
# <a name="what-is-azure-security-center"></a>Co to jest Centrum zabezpieczeń Azure?
Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami na potrzeby różnych obciążeń chmury hybrydowej. Usługa Security Center umożliwia stosowanie zasad zabezpieczeń do różnych obciążeń, ograniczanie podatności na zagrożenia i wykrywanie ataków oraz reagowanie na nie.

Dlaczego warto korzystać z usługi Security Center?

- **Scentralizowane zarządzanie zasadami** — zapewnianie zgodności z firmowymi lub prawnymi wymaganiami dotyczącymi zabezpieczeń przez centralne zarządzanie zasadami zabezpieczeń we wszystkich obciążeniach chmury hybrydowej.
- **Ciągła ocena zabezpieczeń** — monitorowanie poziomu bezpieczeństwa maszyn, sieci, magazynu i usług danych oraz aplikacji pod kątem potencjalnych problemów z zabezpieczeniami.
- **Praktyczne zalecenia** — korygowanie luk w zabezpieczeniach, zanim zostaną wykorzystane przez osoby atakujące, dzięki zaleceniom dotyczącym zabezpieczeń z określonymi priorytetami i możliwością wykonywania akcji.
- **Alerty i zdarzenia uszeregowane według priorytetów** — zdarzenia i alerty zabezpieczeń uszeregowane według priorytetów pozwalają koncentrować się najpierw na krytycznych zagrożeniach.
- **Zaawansowane zabezpieczenia chmury** — ograniczenie zagrożeń dzięki dostępowi dokładnie na czas do portów zarządzania i funkcji adaptacyjnego sterowania aplikacjami działających na maszynach wirtualnych.
- **Zintegrowane rozwiązania w zakresie zabezpieczeń** — zbieranie, wyszukiwanie i analizowanie danych zabezpieczeń z różnych źródeł, w tym połączonych rozwiązań partnerskich.

Okno **Security Center — Przegląd** zapewnia szybki wgląd w stan zabezpieczeń platformy Azure i obciążeń spoza platformy Azure, dzięki czemu można odnajdywać i oceniać zabezpieczenia obciążeń oraz wykrywać i ograniczać ryzyko. Wbudowany pulpit nawigacyjny zapewnia błyskawiczny wgląd w alerty zabezpieczeń i luki w zabezpieczeniach, które wymagają uwagi.

![Omówienie][1]

## <a name="centralized-policy-management"></a>Scentralizowane zarządzanie zasadami
Sekcja **Zasady i zgodność** (pokazana powyżej) zawiera skrócone informacje na temat pokrycia subskrypcji, zgodności z zasadami i poziomu bezpieczeństwa.

### <a name="subscription-coverage"></a>Pokrycie subskrypcji
W tej sekcji jest wyświetlana łączna liczba subskrypcji, do których masz dostęp (do odczytu lub zapisu), oraz poziom pokrycia usługi Security Center (warstwa Standardowa lub Bezpłatna), w ramach której jest uruchamiana subskrypcja:

- **Pokryte (warstwa Standardowa)** — pokryte subskrypcje są uruchamiane z maksymalnym poziomem ochrony oferowanym przez usługę Security Center
- **Pokryte (warstwa Bezpłatna)** — pokryte subskrypcje są uruchamiane z ograniczonym, bezpłatnym poziomem ochrony oferowanym przez usługę Security Center
- **Nieobjęte** — subskrypcje w tym stanie nie są monitorowane przez usługę Security Center

Wybranie wykresu powoduje otwarcie okna **Pokrycie**. Wybranie karty (**Nieobjęte**, **Pokrycie podstawowe** lub **Pokrycie standardowe**) powoduje wyświetlenie listy subskrypcji w poszczególnych stanach. Wybranie subskrypcji na jednej z tych kart powoduje wyświetlenie dodatkowych informacji o subskrypcji. Te informacje pozwalają na zidentyfikowanie właściciela subskrypcji i skontaktowanie się z nim w celu włączenia usługi Security Center lub zwiększenia pokrycia subskrypcji.

![Security Center — pokrycie][9]

### <a name="policy-compliance"></a>Zgodność zasad
Zgodność z zasadami jest określana przez czynniki zgodności wszystkich przypisanych zasad. Ogólny wynik zgodności dla grupy zarządzania, subskrypcji lub obszaru roboczego jest średnią ważoną przypisań. Średnia ważona jest uwzględniania w liczbie zasad pojedynczego przypisania i liczbie zasobów, których dotyczy to przypisanie.

Na przykład jeśli Twoja subskrypcja obejmuje dwie maszyny wirtualne i inicjatywę z przypisanymi do niej pięcioma zasadami, to masz w tej subskrypcji dziesięć ocen. Jeśli jedna z maszyn wirtualnych nie jest zgodna z dwiema zasadami, to ogólny wynik zgodności przypisania Twojej subskrypcji wynosi 80%.

W tej sekcji jest wyświetlany współczynnik zgodności ogólnej i subskrypcje o najmniejszym stopniu zgodności. Wybranie pozycji **Pokaż zgodność środowiska z zasadami** powoduje otwarcie okna **Zarządzanie zasadami**. W oknie **Zarządzanie zasadami** jest wyświetlana struktura hierarchiczna grup zarządzania, subskrypcji i obszarów roboczych. W tym miejscu możesz zarządzać zasadami zabezpieczeń, wybierając subskrypcję lub grupę zarządzania.

![Zarządzanie zasadami][10]

Zasady zabezpieczeń definiują pożądaną konfigurację Twoich obciążeń oraz pomagają zapewnić zgodność z wymaganiami dotyczącymi zabezpieczeń określonymi przez firmę lub przepisy. Usługa Security Center umożliwia definiowanie zasad i dostosowywanie ich do danego typu obciążenia lub poziomu poufności danych przez określanie kontrolek monitorowanych i zalecanych przez tę usługę. Zasady zabezpieczeń możesz edytować w usłudze Security Center, klikając grupę zarządzania lub subskrypcję. Możesz również tworzyć nowe definicje, definiować dodatkowe zasady i przypisywać zasady w grupach zarządzania przy użyciu usługi Azure Policy.

Wybierz pozycję **Edytuj ustawienia >**, aby przeprowadzić edycję następujących ustawień usługi Security Center na poziomie subskrypcji, grupy zarządzania, grupy zasobów lub obszaru roboczego:

- **Zbieranie danych**: umożliwia określenie ustawień aprowizacji agenta i [zbierania danych](security-center-enable-data-collection.md) zabezpieczeń.
- **Wiadomości e-mail z powiadomieniami**: umożliwia określenie ustawień kontaktów i [powiadomień e-mail](security-center-provide-security-contact-details.md) dotyczących zabezpieczeń.
- **Warstwa cenowa**: umożliwia zdefiniowanie [warstwy cenowej](security-center-pricing.md) Bezpłatna lub Standardowa. Wybrana warstwa określa, które funkcje usługi Security Center są dostępne dla zasobów w zakresie.
- **Edytowanie konfiguracji zabezpieczeń**: umożliwia wyświetlenie i zmodyfikowanie konfiguracji systemu operacyjnego, które zostały ocenione przez usługę Security Center, aby zidentyfikować potencjalne luki w zabezpieczeniach.

Aby uzyskać więcej informacji, zobacz temat [Integracja zasad zabezpieczeń w usłudze Security Center z usługą Azure Policy](security-center-azure-policy.md).

### <a name="manage-and-govern-your-security-posture"></a>Zarządzanie ogólnym poziomem zabezpieczeń
Po prawej stronie pulpitu nawigacyjnego w obszarze **Zasady i zgodność** znajdują się szczegółowe informacje, z których możesz natychmiast skorzystać, aby zwiększyć ogólny poziom zabezpieczeń. Oto przykłady:

- Zdefiniowanie i przypisanie zasad usługi Security Center w celu przeglądania i śledzenia zgodności ze standardami zabezpieczeń
- Udostępnienie alertów zabezpieczeń usługi Security Center łącznikowi rozwiązania SIEM
- Zgodność z zasadami na przestrzeni czasu

## <a name="continuous-security-assessment"></a>Ciągła ocena zabezpieczeń
Sekcja Higiena zabezpieczeń zasobów w obszarze **Security Center — przegląd** zawiera szybki przegląd higieny zabezpieczeń zasobów, wyświetlając liczbę zidentyfikowanych problemów i stan zabezpieczeń dla każdego typu zasobu. Ciągła ocena ułatwia wykrywanie potencjalnych problemów z zabezpieczeniami, takich jak systemy bez aktualizacji zabezpieczeń lub narażone porty sieciowe.

### <a name="secure-score"></a>Wskaźnik bezpieczeństwa
Wskaźnik bezpieczeństwa usługi Azure Security Center zawiera przegląd zaleceń dotyczących bezpieczeństwa i nadaje im priorytety, aby poinformować Cię, które zalecenia należy wprowadzić w pierwszej kolejności, ułatwiając w ten sposób znalezienie najpoważniejszych luk w zabezpieczeniach, dzięki czemu można określić priorytety badania. Wskaźnik bezpieczeństwa to narzędzie pomiarowe, które pomaga wzmocnić bezpieczeństwo w celu zabezpieczenia obciążenia. Aby uzyskać więcej informacji, zobacz [Wskaźnik bezpieczeństwa w usłudze Azure Security Center](security-center-secure-score.md).

### <a name="health-monitoring"></a>Monitorowanie kondycji
Wybranie typu zasobu w obszarze **Monitorowanie kondycji zasobu** powoduje wyświetlenie listy zasobów i wszystkich zidentyfikowanych luk w zabezpieczeniach. Typy zasobów należą do następujących kategorii: obliczenia i aplikacje, sieć, dane i magazyn oraz tożsamość i dostęp.

Wybraliśmy typ **Obliczenia i aplikacje**. W obszarze **Obliczenia** znajdują się cztery karty:

- **Omówienie**: monitorowanie i zalecenia zidentyfikowane przez usługę Security Center.
- **Maszyny wirtualne i komputery**: lista maszyn wirtualnych i komputerów oraz ich bieżący stan zabezpieczeń.
- **Usługi w chmurze**: lista ról internetowych i procesów roboczych monitorowanych przez usługę Security Center.
- **Usługi App Services (wersja zapoznawcza)**: lista aplikacji internetowych i środowisk usługi App Service oraz ich bieżący stan zabezpieczeń.

![Monitorowanie kondycji zabezpieczeń][3]

Aby uzyskać więcej informacji, zobacz [Monitorowanie kondycji zabezpieczeń](security-center-monitoring.md).

## <a name="actionable-recommendations"></a>Zalecenia z możliwością wykonywania akcji
Usługa Security Center analizuje stan zabezpieczeń zasobów platformy Azure i spoza niej w celu identyfikowania potencjalnych luk w zabezpieczeniach. Wybranie pozycji **Zalecenia** w obszarze **Zasoby** powoduje wyświetlenie listy zaleceń dotyczących zabezpieczeń z określonymi priorytetami, która przeprowadzi Cię przez proces rozwiązywania problemów z zabezpieczeniami.

![Zalecenia][4]

Aby uzyskać więcej informacji, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń](security-center-recommendations.md).

### <a name="most-prevalent-recommendations"></a>Najczęstsze zalecenia
Po prawej stronie pulpitu nawigacyjnego w obszarze **Zasoby** znajduje się lista najczęstszych zaleceń, które dotyczą największej liczby zasobów. Najczęstsze zalecenia wskazują miejsca, na których musisz skoncentrować swoją uwagę. Wybranie strzałki w prawo powoduje wyświetlenie zalecenia o największym wpływie.

![Najczęstsze zalecenia][11]

Jest to pojedyncze zalecenie, które ma największy wpływ w danym środowisku. Rozpoznanie tego zalecenia w największym stopniu poprawi zgodność. W tym przykładzie zalecenie to „Zastosuj szyfrowanie dysków”. Wybranie pozycji **Popraw zgodność** powoduje wyświetlenie opisu zalecenia i listy zasobów, których ono dotyczy.

![Zastosuj szyfrowanie dysków][12]

## <a name="threat-protection"></a>Ochrona przed zagrożeniami
Ten obszar zapewnia wgląd w alerty zabezpieczeń wykryte w Twoich zasobach oraz poziom ważności tych alertów.

### <a name="prioritized-alerts-and-incidents"></a>Alerty i zdarzenia uszeregowane według priorytetów
Usługa Security Center wykrywa przychodzące ataki i działania po naruszeniu zabezpieczeń za pomocą zaawansowanych funkcji analizy i globalnej analizy zagrożeń. Alerty są uszeregowane według priorytetów i pogrupowane według zdarzeń, aby ułatwić skoncentrowanie się najpierw na najbardziej krytycznych zagrożeniach. Możesz też tworzyć własne niestandardowe alerty zabezpieczeń.

Wybranie pozycji **Alerty zabezpieczeń według ważności** lub **Alerty zabezpieczeń w czasie** powoduje wyświetlenie szczegółowych informacji o alertach.

![Alerty i zdarzenia uszeregowane według priorytetów][7]

Możesz szybko ocenić zakres i wpływ ataku przy użyciu wizualnego i interakcyjnego środowiska badawczego oraz użyć wstępnie zdefiniowanych zapytań lub zapytań ad hoc na potrzeby bardziej zaawansowanej eksploracji danych zabezpieczeń.

Aby uzyskać więcej informacji, zobacz [Reagowanie na alerty zabezpieczeń i zarządzanie nimi](security-center-managing-and-responding-alerts.md).

Po prawej stronie pulpitu nawigacyjnego znajdują się informacje, które ułatwiają określenie kolejności reagowania na alerty oraz omawiają lokalizacje typowych luk w zabezpieczeniach:

- **Najczęściej atakowane zasoby**: określone zasoby, które powodują największą liczbę alertów
- **Najczęstsze alerty**: typy alertów, które wpływają na największą liczbę zasobów

## <a name="just-in-time-vm-access"></a>Dostęp do maszyny wirtualnej dokładnie na czas
Zmniejsz obszar narażony na ataki sieciowe dzięki kontrolowanemu dostęp dokładnie na czas do portów zarządzania na maszynach wirtualnych platformy Azure, aby znacząco ograniczyć narażenie na ataki metodą pełnego przeglądu i inne ataki sieciowe.

![Dostęp do maszyny wirtualnej dokładnie na czas][5]

Możesz określać reguły dotyczące sposobu łączenia się użytkowników z maszynami wirtualnymi. W razie potrzeby możesz żądać dostępu za pomocą usługi Security Center lub programu PowerShell. Dopóki żądanie jest zgodne z zasadami, dostęp jest udzielany automatycznie na żądany czas.

Aby uzyskać więcej informacji, zobacz [Manage virtual machine access using just in time](security-center-just-in-time.md) (Zarządzanie dostępem maszyny wirtualnej przy użyciu funkcji „dokładnie na czas”).

## <a name="adaptive-application-controls"></a>Funkcje adaptacyjnego sterowania aplikacjami
Blokuj złośliwe oprogramowanie i inne niechciane aplikacje, stosując zalecenia dotyczące listy dozwolonych dostosowane do konkretnych obciążeń platformy Azure i obsługiwane przez uczenie maszynowe.

![Funkcje adaptacyjnego sterowania aplikacjami][6]

Przejrzyj i kliknij, aby zastosować zalecane reguły listy dozwolonych aplikacji wygenerowane przez usługę Security Center lub edytować skonfigurowane już reguły.

Aby uzyskać więcej informacji, zobacz [Funkcje adaptacyjnego sterowania aplikacjami](security-center-adaptive-application.md).

## <a name="integrate-your-security-solutions"></a>Integrowanie rozwiązań w zakresie zabezpieczeń
W usłudze Security Center możesz zbierać, wyszukiwać i analizować dane zabezpieczeń z różnych źródeł, w tym połączonych rozwiązań partnerskich, takich jak zapory sieciowe i inne usługi firmy Microsoft.

![Integrowanie rozwiązań w zakresie zabezpieczeń][8]

Aby uzyskać więcej informacji, zobacz [Integrowanie rozwiązań w zakresie zabezpieczeń](security-center-partner-integration.md).

## <a name="next-steps"></a>Następne kroki

- Do rozpoczęcia pracy z usługą Security Center jest wymagana subskrypcja platformy Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Usługa Security Center w warstwie cenowej Bezpłatna jest włączona w ramach subskrypcji platformy Azure. Aby móc korzystać z zaawansowanych możliwości wykrywania zagrożeń i zarządzania zabezpieczeniami, musisz przeprowadzić uaktualnienie do warstwy cenowej Standardowa. Warstwę Standardowa można wypróbować bezpłatnie. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/).
- Jeśli możesz już włączyć usługę Security Center w warstwie Standardowa, skorzystaj z przewodnika [Szybki start: dołączanie subskrypcji platformy Azure do standardowej usługi Security Center](security-center-get-started.md), który przeprowadzi Cię przez ten proces.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
[9]: ./media/security-center-intro/coverage.png
[10]: ./media/security-center-intro/policy-management.png
[11]: ./media/security-center-intro/highest-impact.png
[12]: ./media/security-center-intro/apply-disk-encryption.png
