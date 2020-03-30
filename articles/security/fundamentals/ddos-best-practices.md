---
title: Projektowanie elastycznych rozwiązań za pomocą usługi Azure DDoS Protection
description: Dowiedz się, jak korzystać z danych rejestrowania w celu uzyskania szczegółowych informacji na temat aplikacji.
services: security
author: barclayn
manager: RKarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: barclayn
ms.openlocfilehash: 8d3fc809999508bf3d49c3765c90017e89e80fa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624050"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Ochrona przed atakami DDoS azure — projektowanie odpornych rozwiązań

Ten artykuł jest przeznaczony dla decydentów IT i pracowników ochrony. Oczekuje, że jesteś zaznajomiony z platformy Azure, sieci i zabezpieczeń.
DDoS jest rodzajem ataku, który próbuje wyczerpywać zasoby aplikacji. Celem jest wpłynąć na dostępność aplikacji i jej zdolność do obsługi uzasadnionych żądań. Ataki stają się coraz bardziej wyrafinowane i większe pod względem wielkości i wpływu. Celem ataku DDoS może być dowolny punkt końcowy publicznie dostępny za pośrednictwem Internetu. Projektowanie odporności typu ddos (distributed denial of service) wymaga planowania i projektowania dla różnych trybów awarii. Platforma Azure zapewnia ciągłą ochronę przed atakami DDoS. Ta ochrona jest domyślnie zintegrowana z platformą Azure i bez dodatkowych kosztów.

Oprócz podstawowej ochrony przed atakami DDoS na platformie [standard ochrony przed atakami DDoS zapewnia](https://azure.microsoft.com/services/ddos-protection/) zaawansowane funkcje ograniczania ochrony przed atakami sieciowymi. Jest automatycznie dostrojony do ochrony określonych zasobów platformy Azure. Ochrona jest prosta do włączenia podczas tworzenia nowych sieci wirtualnych. Można to również zrobić po utworzeniu i nie wymaga żadnych zmian aplikacji lub zasobów.

![Rola usługi Azure DDoS Protection w ochronie klientów i sieci wirtualnej przed osobami atakującymi](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>Podstawowe najlepsze praktyki

W poniższych sekcjach przedstawiono wskazówki nakazowe dotyczące tworzenia usług odpornych na DDoS na platformie Azure.

### <a name="design-for-security"></a>Projektowanie pod kątem zabezpieczeń

Upewnij się, że zabezpieczenia są priorytetem w całym cyklu życia aplikacji, od projektowania i implementacji do wdrażania i operacji. Aplikacje mogą mieć błędy, które umożliwiają stosunkowo małą liczbę żądań do korzystania z nadmiernej ilości zasobów, co powoduje awarię usługi. 

Aby chronić usługę działającą na platformie Microsoft Azure, należy dobrze zrozumieć architekturę aplikacji i skupić się na [pięciu filarach jakości oprogramowania.](/azure/architecture/guide/pillars)
Należy znać typowe woluminy ruchu, model łączności między aplikacją i innymi aplikacjami oraz punkty końcowe usługi, które są narażone na publiczny internet.

Zapewnienie, że aplikacja jest wystarczająco odporna do obsługi typu "odmowa usługi", która jest przeznaczona dla samej aplikacji, jest najważniejsza. Zabezpieczenia i prywatność są wbudowane w platformę Azure, począwszy od [cyklu cyklu bezpieczeństwa rozwoju (SDL).](https://www.microsoft.com/sdl/default.aspx) SDL adresy zabezpieczeń na każdym etapie rozwoju i zapewnia, że platforma Azure jest stale aktualizowana, aby uczynić go jeszcze bardziej bezpieczne.

### <a name="design-for-scalability"></a>Konstrukcja dla skalowalności

Skalowalność to sposób, w jaki system może obsłużyć zwiększone obciążenie. Zaprojektuj aplikacje do [skalowania poziomego,](/azure/architecture/guide/design-principles/scale-out) aby sprostać zapotrzebowaniu wzmocnionego obciążenia, w szczególności w przypadku ataku DDoS. Jeśli aplikacja zależy od pojedynczego wystąpienia usługi, tworzy pojedynczy punkt awarii. Inicjowanie obsługi administracyjnej wielu wystąpień sprawia, że system jest bardziej odporny i bardziej skalowalny.

W przypadku [usługi Azure App Service](/azure/app-service/app-service-value-prop-what-is)wybierz plan usługi [aplikacji,](/azure/app-service/overview-hosting-plans) który oferuje wiele wystąpień. W przypadku usług Azure Cloud Services skonfiguruj każdą z ról, aby [używały wielu wystąpień.](/azure/cloud-services/cloud-services-choose-me) W przypadku [maszyn wirtualnych platformy Azure](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)upewnij się, że architektura maszyny wirtualnej zawiera więcej niż jedną maszynę wirtualną i że każda maszyna wirtualna jest uwzględniona w zestawie [dostępności.](/azure/virtual-machines/virtual-machines-windows-manage-availability) Zaleca się używanie [zestawów skalowania maszyny wirtualnej](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) do skalowania automatycznego.

### <a name="defense-in-depth"></a>Ochrona w głębi systemu

Ideą obrony w głębi jest zarządzanie ryzykiem za pomocą różnych strategii obronnych. Nakładanie warstw zabezpieczeń w aplikacji zmniejsza ryzyko pomyślnego ataku. Zaleca się implementowanie bezpiecznych projektów dla aplikacji przy użyciu wbudowanych możliwości platformy Azure.

Na przykład ryzyko ataku wzrasta wraz z rozmiarem (*powierzchnią*) aplikacji. Powierzchnię można zmniejszyć, korzystając z białej listy w celu zamknięcia narażonej przestrzeni adresowej IP i portów nasłuchujących, które nie są potrzebne w modułach równoważenia obciążenia[(Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) i [Azure Application Gateway).](/azure/application-gateway/application-gateway-create-probe-portal) [Sieciowe grupy zabezpieczeń (NSG)](/azure/virtual-network/security-overview) to kolejny sposób na zmniejszenie powierzchni ataku.
Za pomocą [tagów usług](/azure/virtual-network/security-overview#service-tags) i [grup zabezpieczeń aplikacji](/azure/virtual-network/security-overview#application-security-groups) można minimalizować złożoność tworzenia reguł zabezpieczeń i konfigurowania zabezpieczeń sieciowych jako naturalnego rozszerzenia struktury aplikacji.

Usługi platformy Azure należy wdrożyć w [sieci wirtualnej,](/azure/virtual-network/virtual-networks-overview) gdy tylko jest to możliwe. Ta praktyka umożliwia zasobów usługi do komunikowania się za pośrednictwem prywatnych adresów IP. Ruch usługi platformy Azure z sieci wirtualnej domyślnie używa publicznych adresów IP jako źródłowych adresów IP. Za pomocą [punktów końcowych usługi](/azure/virtual-network/virtual-network-service-endpoints-overview) przełączy ruch usługi do używania adresów prywatnych sieci wirtualnej jako źródłowych adresów IP, gdy uzyskujesz dostęp do usługi platformy Azure z sieci wirtualnej.

Często widzimy, że zasoby lokalne klientów są atakowane wraz z ich zasobami na platformie Azure. Jeśli łączysz środowisko lokalne z platformą Azure, zaleca się zminimalizowanie narażenia zasobów lokalnych na publiczny Internet. Można użyć skalowania i zaawansowanych funkcji ochrony przed atakami DDoS platformy Azure, wdrażając dobrze znane jednostki publiczne na platformie Azure. Ponieważ te publicznie dostępne jednostki są często celem ataków DDoS, umieszczenie ich na platformie Azure zmniejsza wpływ na zasoby lokalne.

## <a name="azure-offerings-for-ddos-protection"></a>Oferta platformy Azure dla ochrony przed atakami DDoS

Platforma Azure ma dwie oferty usług DDoS, które zapewniają ochronę przed atakami sieciowymi (warstwa 3 i 4): DDoS Protection Basic i Standard ochrony przed atakami DDoS. 

### <a name="ddos-protection-basic"></a>DDoS Protection Basic

Podstawowa ochrona jest domyślnie zintegrowana z platformą Azure bez dodatkowych kosztów. Skala i pojemność globalnie wdrożonej sieci platformy Azure zapewnia odporność na typowe ataki w warstwie sieciowej za pośrednictwem zawsze włączonego monitorowania ruchu i ograniczania ryzyka w czasie rzeczywistym. DDoS Protection Basic nie wymaga konfiguracji użytkownika ani zmian w aplikacji. DDoS Protection Basic pomaga chronić wszystkie usługi platformy Azure, w tym usługi PaaS, takie jak usługa Azure DNS.

![Reprezentacja map sieci platformy Azure z tekstem "Globalna obecność ograniczająca zagrożenie DDoS" i "Wiodąca zdolność ograniczania ddos"](./media/ddos-best-practices/image3.png)

Podstawowa ochrona przed atakami DDoS na platformie Azure składa się zarówno ze składników oprogramowania, jak i sprzętu. Płaszczyzna sterowania oprogramowaniem decyduje, kiedy, gdzie i jakiego rodzaju ruch powinien być kierowany za pomocą urządzeń sprzętowych, które analizują i usuwają ruch ataków. Płaszczyzna sterowania podejmuje tę decyzję w oparciu o ogólnoindyjniczą *politykę*ochrony przed atakami DDoS . Ta zasada jest statycznie ustawiona i powszechnie stosowana do wszystkich klientów platformy Azure.

Na przykład zasady ochrony przed atakami DDoS określają, na jakim woluminie ruchu powinna zostać *wyzwolona ochrona.* (Oznacza to, że ruch najemcy powinny być kierowane przez urządzenia szorowania.) Zasady następnie określa, jak urządzenia do czyszczenia powinny *ograniczać* atak.

Usługa Azure DDoS Protection Basic jest przeznaczona do ochrony infrastruktury i ochrony platformy Azure. Zmniejsza ruch, gdy przekracza szybkość, która jest tak znacząca, że może mieć wpływ na wielu klientów w środowisku wielodostępnym. Nie zapewnia alertów lub dostosowane zasady na klienta.

### <a name="ddos-protection-standard"></a>Standard ochrony DDoS

Standardowa ochrona zapewnia ulepszone funkcje ograniczające zagrożenie DDoS. Jest automatycznie dostrojony, aby chronić określone zasoby platformy Azure w sieci wirtualnej. Ochrona jest prosta do włączenia w każdej nowej lub istniejącej sieci wirtualnej i nie wymaga żadnych zmian aplikacji lub zasobów. Ma kilka zalet w stosunku do podstawowej usługi, w tym rejestrowania, alertów i telemetrii. W poniższych sekcjach opisano najważniejsze funkcje usługi Azure DDoS Protection Standard.

#### <a name="adaptive-real-time-tuning"></a>Adaptacyjne strojenie w czasie rzeczywistym

Usługa Azure DDoS Protection Basic pomaga chronić klientów i zapobiegać wpływom innych klientów. Na przykład jeśli usługa jest aprowizowana dla typowego woluminu legalnego ruchu przychodzącego, który jest mniejszy niż *szybkość wyzwalania* zasad ochrony przed atakami DDoS dla całej infrastruktury, atak DDoS na zasoby tego klienta może przejść niezauważony. Bardziej ogólnie złożoność ostatnich ataków (na przykład wielowymierwowe DDoS) i zachowania specyficzne dla aplikacji dzierżawców wymagają dla poszczególnych klientów, dostosowane zasady ochrony. Usługa realizuje to dostosowanie przy użyciu dwóch szczegółowych informacji:

- Automatyczne uczenie się wzorców ruchu na klienta (na ip) dla warstwy 3 i 4.

- Minimalizowanie fałszywych alarmów, biorąc pod uwagę, że skala platformy Azure umożliwia wchłonięcie znacznej ilości ruchu.

![Diagram działania standardu ochrony przed atakami DDoS z zakreśleniem "Generowanie zasad"](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Dane telemetryczne, monitorowanie i alerty ochrony przed atakami DDoS

Standard ochrony przed atakami DDoS udostępnia zaawansowane dane telemetryczne za pośrednictwem [usługi Azure Monitor](/azure/azure-monitor/overview) na czas trwania ataku DDoS. Alerty można skonfigurować dla dowolnej metryki usługi Azure Monitor używanej przez usługę DDoS Protection. Rejestrowanie można zintegrować ze splunkiem (usługa Azure Event Hubs), dziennikami usługi Azure Monitor i usługą Azure Storage w celu zaawansowanej analizy za pośrednictwem interfejsu diagnostyki usługi Azure Monitor.

##### <a name="ddos-mitigation-policies"></a>Zasady łagodzenia ddos

W witrynie Azure portal wybierz pozycję **Monitor** > **Metryki**. W okienku **Metryki** wybierz grupę zasobów, wybierz typ zasobu **Publiczny adres IP**i wybierz publiczny adres IP platformy Azure. Metryki DDoS są widoczne w okienku **Dostępne metryki.**

Standard ochrony przed atakami DDoS stosuje trzy automatycznie dostrojone zasady łagodzenia zmiany klimatu (TCP SYN, TCP i UDP) dla każdego publicznego adresu IP chronionego zasobu w sieci wirtualnej z włączonym DDoS. Progi zasad można wyświetlić, wybierając metrykę **Pakiety przychodzące, aby wyzwolić środki ograniczające DDoS**.

![Wykres dostępnych danych i danych](./media/ddos-best-practices/image7.png)

Progi zasad są automatycznie konfigurowane za pomocą profilowania ruchu sieciowego opartego na uczeniu maszynowym. Łagodzenie DDoS występuje dla adresu IP atakowanego tylko wtedy, gdy próg zasad zostanie przekroczony.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metryka adresu IP w ataku DDoS

Jeśli publiczny adres IP jest atakowany, wartość metryki **w obszarze ataku DDoS lub nie** zmienia się na 1 jako Ochrona przed atakami DDoS wykonuje łagodzenie ruchu ataku.

![Metryka i wykres "Pod atakiem DDoS lub nie"](./media/ddos-best-practices/image8.png)

Zaleca się skonfigurowanie alertu na tej metryki. Następnie zostaniesz powiadomiony, gdy istnieje aktywne środki zaradcze DDoS wykonywane na publiczny adres IP.

Aby uzyskać więcej informacji, zobacz [Zarządzanie standardem ochrony przed atakami DDoS platformy Azure przy użyciu witryny Azure portal](/azure/virtual-network/ddos-protection-manage-portal).

#### <a name="web-application-firewall-for-resource-attacks"></a>Zapora aplikacji sieci Web do ataków na zasoby

Specyficzne dla ataków zasobów w warstwie aplikacji, należy skonfigurować zaporę aplikacji sieci web (WAF), aby ułatwić bezpieczne aplikacje sieci web. WAF sprawdza przychodzący ruch internetowy, aby zablokować zastrzyki SQL, skrypty między lokacjami, DDoS i inne ataki warstwy 7. Platforma Azure udostępnia w ramach usługi [WAF jako funkcję bramy aplikacji](/azure/application-gateway/application-gateway-web-application-firewall-overview) do scentralizowanej ochrony aplikacji sieci web przed typowymi exploitami i lukami w zabezpieczeniach. Istnieją inne oferty WAF dostępne od partnerów platformy Azure, które mogą być bardziej odpowiednie dla Twoich potrzeb za pośrednictwem [portalu Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)

Nawet zapory aplikacji sieci web są podatne na ataki wolumetryczne i stan wyczerpania. Zdecydowanie zaleca się włączenie standardu ochrony przed atakami DDoS w sieci wirtualnej WAF w celu ochrony przed atakami wolumetrycznymi i protokołowymi. Aby uzyskać więcej informacji, zobacz sekcję [Architektury odwołań do ochrony przed atakami DDoS.](#ddos-protection-reference-architectures)

### <a name="protection-planning"></a>Planowanie ochrony

Planowanie i przygotowanie są niezbędne, aby zrozumieć, jak system będzie działać podczas ataku DDoS. Projektowanie planu reagowania na zdarzenia jest częścią tego wysiłku.

Jeśli masz standard ochrony przed atakami DDoS, upewnij się, że jest on włączony w sieci wirtualnej punktów końcowych skierowanych do Internetu. Konfigurowanie alertów DDoS pomaga stale obserwować wszelkie potencjalne ataki na infrastrukturę. 

Monitoruj aplikacje niezależnie. Zrozumienie normalnego zachowania aplikacji. Przygotuj się do działania, jeśli aplikacja nie zachowuje się zgodnie z oczekiwaniami podczas ataku DDoS.

#### <a name="testing-through-simulations"></a>Testowanie za pomocą symulacji

Dobrą praktyką jest testowanie założeń dotyczących reagowania usług na atak, przeprowadzając okresowe symulacje. Podczas testowania sprawdź, czy usługi lub aplikacje nadal działają zgodnie z oczekiwaniami i nie ma żadnych zakłóceń w doświadczeniu użytkownika. Identyfikowanie luk zarówno z punktu widzenia technologii, jak i procesu oraz uwzględnianie ich w strategii reagowania DDoS. Zaleca się wykonywanie takich testów w środowiskach przejściowych lub w godzinach poza szczytem, aby zminimalizować wpływ na środowisko produkcyjne.

Nawiązaliśmy współpracę z [breakingpoint cloud,](https://www.ixiacom.com/products/breakingpoint-cloud) aby utworzyć interfejs, w którym klienci platformy Azure mogą generować ruch względem publicznych punktów końcowych z obsługą ochrony przed atakami DDoS dla symulacji. Symulacja [chmury BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) można użyć do:

- Sprawdź, w jaki sposób usługa Azure DDoS Protection pomaga chronić zasoby platformy Azure przed atakami DDoS.

- Zoptymalizuj proces reagowania na incydenty w przypadku ataku DDoS.

- Zgodność z DDoS dokumentu.

- Szkolenie zespołów bezpieczeństwa sieci.

Cyberbezpieczeństwo wymaga ciągłych innowacji w obronie. Standardowa ochrona usług Azure DDoS to najnowocześniejsza oferta z skutecznym rozwiązaniem w celu ograniczenia coraz bardziej złożonych ataków DDoS.

## <a name="components-of-a-ddos-response-strategy"></a>Składniki strategii reagowania DDoS

Atak DDoS, który jest przeznaczony dla zasobów platformy Azure zwykle wymaga minimalnej interwencji z punktu widzenia użytkownika. Mimo to włączenie łagodzenia DDoS jako części strategii reagowania na incydenty pomaga zminimalizować wpływ na ciągłość działania.

### <a name="microsoft-threat-intelligence"></a>Analiza zagrożeń firmy Microsoft

Firma Microsoft posiada rozbudowaną sieć analizy zagrożeń. Ta sieć korzysta ze zbiorowej wiedzy o rozszerzonej społeczności zabezpieczeń, która obsługuje usługi online firmy Microsoft, partnerów firmy Microsoft i relacje w społeczności zabezpieczeń internetowych. 

Jako dostawca infrastruktury krytycznej firma Microsoft otrzymuje wczesne ostrzeżenia o zagrożeniach. Firma Microsoft gromadzi analizy zagrożeń ze swoich usług online i z globalnej bazy klientów. Firma Microsoft włącza wszystkie te analizy zagrożeń z powrotem do produktów ochrony przed atakami DDoS platformy Azure.

Ponadto Jednostka Microsoft Digital Crimes Unit (DCU) wykonuje obraźliwe strategie przeciwko botnetom. Botnety są częstym źródłem poleceń i kontroli ataków DDoS.

### <a name="risk-evaluation-of-your-azure-resources"></a>Ocena ryzyka zasobów platformy Azure

Konieczne jest, aby zrozumieć zakres ryzyka z ataku DDoS na bieżąco. Okresowo zadawaj sobie pytanie:

- Jakie nowe publicznie dostępne zasoby platformy Azure wymagają ochrony?

- Czy w usłudze występuje pojedynczy punkt awarii? 

- W jaki sposób można odizolować usługi, aby ograniczyć wpływ ataku, jednocześnie udostępniając usługi prawidłowym klientom?

- Czy istnieją sieci wirtualne, w których standard ochrony przed atakami DDoS powinien być włączony, ale nie jest? 

- Czy moje usługi są aktywne/aktywne w trybie failover w wielu regionach?

### <a name="customer-ddos-response-team"></a>Zespół odpowiedzi DDoS klienta

Tworzenie zespołu odpowiedzi DDoS jest kluczowym krokiem w reagowaniu na atak szybko i skutecznie. Identyfikowanie kontaktów w organizacji, które będą nadzorować zarówno planowanie, jak i wykonywanie. Ten zespół odpowiedzi DDoS powinien dokładnie zrozumieć usługę Standard ochrony przed atakami DDoS platformy Azure. Upewnij się, że zespół może zidentyfikować i złagodzić atak, koordynując z klientami wewnętrznymi i zewnętrznymi, w tym z zespołem pomocy technicznej firmy Microsoft.

Dla zespołu odpowiedzi DDoS zaleca się użycie ćwiczeń symulacyjnych jako normalnej części dostępności usługi i planowania ciągłości. Ćwiczenia te powinny obejmować testowanie skali.

### <a name="alerts-during-an-attack"></a>Alerty podczas ataku

Standard ochrony przed atakami DDoS azure identyfikuje i ogranicza ataki DDoS bez interwencji użytkownika. Aby otrzymywać powiadomienia o aktywnym łagodzeniu chronionego publicznego adresu IP, można [skonfigurować alert](/azure/virtual-network/ddos-protection-manage-portal) dotyczący metryki **W obszarze ataku DDoS lub nie**. Można utworzyć alerty dla innych metryk DDoS, aby zrozumieć skalę ataku, ruch jest usuwany i inne szczegóły.

#### <a name="when-to-contact-microsoft-support"></a>Kiedy skontaktować się z pomocą techniczną firmy Microsoft

- Podczas ataku DDoS okaże się, że wydajność chronionego zasobu jest poważnie obniżona lub zasób nie jest dostępny.

- Uważasz, że usługa Ochrony Przed atakami DDoS nie zachowuje się zgodnie z oczekiwaniami. 

  Usługa Ochrony przed atakami DDoS uruchamia łagodzenie skutków tylko wtedy, gdy wartość metryki **Policy wyzwalania łagodzenia DDoS (TCP/TCP SYN/UDP)** jest niższa niż ruch odebrany w chronionym publicznym zasobie IP.

- Planujesz zdarzenie wirusowe, które znacznie zwiększy ruch sieciowy.

- Aktor zagroził, że rozpocznie atak DDoS na twoje zasoby.

- Jeśli chcesz zezwolić na listę zakres ip lub IP z usługi Azure DDoS Protection Standard. Typowym scenariuszem jest zezwolenie na adres IP listy, jeśli ruch jest kierowany z zewnętrznego waf chmury do platformy Azure. 

W przypadku ataków, które mają krytyczny wpływ na działalność, utwórz [bilet pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)o ważności.

### <a name="post-attack-steps"></a>Kroki po ataku

Zawsze jest to dobra strategia, aby zrobić pośmiertnie po ataku i dostosować strategię odpowiedzi DDoS w razie potrzeby. Rzeczy do rozważenia:

- Czy wystąpiły jakiekolwiek zakłócenia w świadczeniu usług lub środowisko użytkownika z powodu braku skalowalnej architektury?

- Które aplikacje lub usługi ucierpiały najbardziej?

- Jak skuteczna była strategia reagowania DDoS i jak można ją ulepszyć?

Jeśli podejrzewasz, że jesteś w ataku DDoS, eskaluj za pośrednictwem normalnych kanałów pomocy technicznej platformy Azure.

## <a name="ddos-protection-reference-architectures"></a>Architektury referencyjne ochrony przed atakami DDoS

Standard ochrony przed atakami DDoS jest przeznaczony [dla usług wdrożonych w sieci wirtualnej.](/azure/virtual-network/virtual-network-for-azure-services) W przypadku innych usług obowiązuje domyślna usługa DDoS Protection Basic. Następujące architektury referencyjne są rozmieszczone według scenariuszy, z wzorców architektury zgrupowane razem.

### <a name="virtual-machine-windowslinux-workloads"></a>Obciążenia maszyn wirtualnych (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplikacja uruchomiona na maszynach wirtualnych ze zrównoważonym obciążeniem

Ta architektura referencyjna przedstawia zestaw sprawdzonych praktyk dotyczących uruchamiania wielu maszyn wirtualnych z systemem Windows w skali ustawionej za modułem równoważenia obciążenia, aby poprawić dostępność i skalowalność. Ta architektura może służyć do dowolnego obciążenia bezstanowego, takiego jak serwer sieci web.

![Diagram architektury referencyjnej dla aplikacji działającej na maszynach wirtualnych ze zrównoważonym obciążeniem](./media/ddos-best-practices/image9.png)

W tej architekturze obciążenie jest rozkładane na wiele wystąpień maszyny wirtualnej. Istnieje jeden publiczny adres IP, a ruch internetowy jest dystrybuowany do maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Standard ochrony przed atakami DDoS jest włączony w sieci wirtualnej modułu równoważenia obciążenia platformy Azure (internet), z który jest skojarzony publiczny adres IP.

Moduł równoważenia obciążenia dystrybuuje przychodzące żądania internetowe do wystąpień maszyn wirtualnych. Zestawy skalowania maszyny wirtualnej umożliwiają ręczne skalowanie lub automatyczne skalowanie liczby maszyn wirtualnych lub automatycznie na podstawie wstępnie zdefiniowanych reguł. Jest to ważne, jeśli zasób jest pod atakiem DDoS. Aby uzyskać więcej informacji na temat tej architektury referencyjnej, zobacz [ten artykuł](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Aplikacja uruchomiona w systemie Windows N-warstwa

Istnieje wiele sposobów implementowania architektury n-warstwowej. Na poniższym diagramie przedstawiono typową trójwarstwową aplikację sieci web. Ta architektura opiera się na artykule Uruchom maszyny [wirtualne z równoważenia obciążenia dla skalowalności i dostępności](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Warstwy internetowa i biznesowa używają maszyn wirtualnych z równoważeniem obciążenia.

![Diagram architektury referencyjnej dla aplikacji działającej w warstwie Windows N](./media/ddos-best-practices/image10.png)

W tej architekturze standard ochrony przed atakami DDoS jest włączony w sieci wirtualnej. Wszystkie publiczne adresy IP w sieci wirtualnej otrzymują ochronę przed atakami DDoS dla warstwy 3 i 4. W przypadku ochrony warstwy 7 należy wdrożyć bramę aplikacji w jednostce SKU WAF. Aby uzyskać więcej informacji na temat tej architektury referencyjnej, zobacz [ten artykuł](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>Aplikacja internetowa PaaS

Ta architektura referencyjna pokazuje uruchamianie aplikacji usługi Azure App Service w jednym regionie. Ta architektura przedstawia zestaw sprawdzonych praktyk dla aplikacji sieci web, która używa [usługi Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) i [usługi Azure SQL Database.](https://azure.microsoft.com/documentation/services/sql-database/)
Region czuwania jest skonfigurowany dla scenariuszy pracy awaryjnej.

![Diagram architektury referencyjnej dla aplikacji sieci Web PaaS](./media/ddos-best-practices/image11.png)

Usługa Azure Traffic Manager kieruje przychodzące żądania do bramy aplikacji w jednym z regionów. Podczas normalnych operacji kieruje żądania do bramy aplikacji w regionie aktywnym. Jeśli ten region stanie się niedostępny, usługa Traffic Manager przechodzi w tryb fail to Application Gateway w regionie wstrzymania.

Cały ruch z Internetu przeznaczony do aplikacji sieci web jest kierowany do [publicznego adresu IP bramy aplikacji](/azure/application-gateway/application-gateway-web-app-overview) za pośrednictwem usługi Traffic Manager. W tym scenariuszu sama usługa aplikacji (aplikacja sieci web) nie jest bezpośrednio skierowana na zewnątrz i jest chroniona przez bramę aplikacji. 

Zaleca się skonfigurowanie identyfikatora SKU bramy aplikacji (tryb zapobiegania) w celu ochrony przed atakami warstwy 7 (HTTP/HTTPS/WebSocket). Ponadto aplikacje sieci web są skonfigurowane do [akceptowania tylko ruchu z](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) adresu IP bramy aplikacji.

Aby uzyskać więcej informacji na temat tej architektury referencyjnej, zobacz [ten artykuł](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Łagodzenie dla usług PaaS innych niż www

#### <a name="hdinsight-on-azure"></a>Usługa HDInsight na platformie Azure

Ta architektura referencyjna przedstawia konfigurowanie standardu ochrony przed atakami DDoS dla [klastra usługi Azure HDInsight](/azure/hdinsight/). Upewnij się, że klaster HDInsight jest połączony z siecią wirtualną i że ochrona przed atakami DDoS jest włączona w sieci wirtualnej.

![Okienka "HDInsight" i "Ustawienia zaawansowane" z ustawieniami sieci wirtualnej](./media/ddos-best-practices/image12.png)

![Wybór włączania ochrony przed atakami DDoS](./media/ddos-best-practices/image13.png)

W tej architekturze ruch przeznaczony do klastra HDInsight z Internetu jest kierowany do publicznego adresu IP skojarzonego z modułem równoważenia obciążenia bramy HDInsight. Moduł równoważenia obciążenia bramy następnie wysyła ruch do węzłów głównego lub węzłów procesu roboczego bezpośrednio. Ponieważ standard ochrony przed atakami DDoS jest włączony w sieci wirtualnej HDInsight, wszystkie publiczne adresy IP w sieci wirtualnej otrzymują ochronę DDoS dla warstwy 3 i 4. Tę architekturę referencyjną można łączyć z architekturami referencyjnymi N-warstwowych i wieloregionowych.

Aby uzyskać więcej informacji na temat tej architektury referencyjnej, zobacz [rozszerzanie usługi Azure HDInsight przy użyciu dokumentacji sieci wirtualnej platformy Azure.](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json)


> [!NOTE]
> Środowisko usługi Azure App Service dla usługi PowerApps lub zarządzanie interfejsami API w sieci wirtualnej z publicznym adresem IP nie są obsługiwane natywnie.

## <a name="next-steps"></a>Następne kroki

* [Wspólna odpowiedzialność w chmurze](shared-responsibility.md)

* [Strona produktu usługi Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)

* [Dokumentacja usługi Azure DDoS Protection](/azure/virtual-network/ddos-protection-overview)
