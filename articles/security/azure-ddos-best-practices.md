---
title: Najlepsze rozwiązania ochrony przed atakami DDoS Azure & architektura referencyjna | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposób rejestrowania danych można użyć w celu uzyskania szczegółowych informacji o aplikacji.
services: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: barclayn
ms.openlocfilehash: 4b2d785f5b9095a2decfc65ec46808ff6f65c38e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="azure-ddos-protection-best-practices-and-reference-architecture"></a>Ochrona przed atakami DDoS Azure: Najlepsze rozwiązania i referencyjne architektury

Ten dokument jest przeznaczone dla IT inne osoby podejmujące decyzje i personelu zabezpieczeń. Ponadto przy użyciu platformy Azure, sieci i zabezpieczeń są oczekiwane.

Projektowanie dla rozproszonych odmowa usługi (DDoS) odporności wymaga planowania i projektowania w różnych trybach awarii. Ten dokument zawiera wskazówki dotyczące projektowania aplikacji na platformie Azure zapewnia odporność na awarie przed atakami DDoS.

## <a name="types-of-attacks"></a>Typy ataków

Przed atakami DDoS jest typem ataku, używane w ramach działań zmierzających na wyczerpanie zasobów aplikacji. Celem jest wpływ na dostępność i możliwość obsługi uprawnionych żądań aplikacji. Ataki stają się coraz bardziej zaawansowane i większy rozmiar i wpływu. Może być celem ataków DDoS dowolnego punktu końcowego, który jest publicznie dostępny za pośrednictwem Internetu.

Platforma Azure udostępnia ciągłej ochrony przed atakami DDoS. Ta ochrona jest zintegrowana platforma Azure domyślnie i bez ponoszenia dodatkowych kosztów. Oprócz podstawowych ochrony przed atakami DDoS w platformie mamy także nową ofertę o nazwie "[standardowe ochrony przed atakami DDoS Azure](https://azure.microsoft.com/services/ddos-protection/)", która zapewnia zaawansowane możliwości środki zaradcze DDoS przed atakami sieciowymi oraz jest automatycznie dopasowane do ochrony określonych zasobów na platformie Azure. Ochrona jest proste włączyć podczas tworzenia nowej sieci wirtualnej. Można również wykonać po utworzeniu początkowej, a nie wymaga żadnych zmian w aplikacji lub zasobie.

![](media/azure-ddos-best-practices/image1.png)

Atakami DDoS mogą być klasyfikowane szeroko w trzech (3) w różne kategorie

### <a name="volumetric-attacks"></a>Ataki pomiarowej

Ataki pomiarowej są najbardziej typowych rodzajów ataków DDoS. Ataki pomiarowej są zamachów metodą siłową, które odnoszą się do warstwy transportu i sieci. Próbują na wyczerpanie zasobów takich łączy sieciowych. Tego rodzaju ataki często używanych wiele systemów zainfekowanych do wypełniania warstwy sieci z rozległe pozornie potrzebnego ruchu. Różnych protokołów warstwy sieciowej, takie jak protokół komunikat sterowania Internetem (ICMP), protokół UDP (User Datagram) i protokołu Transmission Control Protocol (TCP) są używane w ten typ ataku.

Najczęściej używane warstwy sieci przed atakami DDoS ataki są SYN TCP zalewania, echa ICMP zalewania UDP, DNS i protokołu NTP wzmocnienia ataku. Ten typ ataku umożliwia nie tylko zakłócić działanie usługi, ale także jako smokescreen dla bardziej nefarious i docelowej sieci nieautoryzowanego dostępu. Na przykład ostatnie ataku pomiarowej [wykorzystać Memcached](https://www.wired.com/story/github-ddos-memcached/) który wpływ na usługi GitHub. Atak UDP port 11211 i do wygenerowane 1.35 Tb/s woluminu ataku.

### <a name="protocol-attacks"></a>Protokół ataków

Protokół ataków protokołów aplikacji docelowej. Próbują użyć wszystkich dostępnych zasobów urządzeń infrastruktury, takich jak zapory, serwery aplikacji i usług równoważenia obciążenia. Ataki protokołu Użyj pakietów, które są źle sformułowane lub zawierać protokół nieprawidłowości. Tego rodzaju ataki działają, wysyłając większą liczbę otwartych żądań, serwerach i innych komunikacji urządzeń odpowiedzi i oczekiwania na odpowiedź pakietu. Element docelowy próbuje odpowiadania na żądania Otwórz może sprawić, że docelowy system awarię.

Najbardziej typowym przykładem opartych na protokole takiego ataku jest powódź SYN TCP. W tym ataku kolejne żądania TCP SYN jest skierowany do obiektu docelowego i może służyć do przeciąży ona. Celem jest zapewnienie element docelowy nie odpowiada. Awarii Dyn 2016, oprócz trwa atak warstwy aplikacji obejmował także powodzie TCP SYN docelowy port 53 Dyn na serwerach DNS.

### <a name="resource-attacks"></a>Ataki zasobów

Ataki zasobu docelowego warstwy aplikacji. Procesy zaplecza one wyzwalanie w ramach działań zmierzających do przeciąży system docelowy. Zasób ataków nadużycia ruchu, który wygląda normalne, ale ten wykonuje obciążające Procesor zapytań do serwera. Wielkość ruchu sieciowego potrzebne na wyczerpanie zasobów jest stosunkowo niższej niż inne rodzaje ataków. Ruch w atak zasobów jest można odróżnić od wiarygodnego ruchu, dzięki czemu trudne do wykrycia. Najbardziej typowe ataki zasobów są na usługi DNS i HTTP/HTTPS.

## <a name="shared-responsibility-in-the-cloud"></a>Odpowiedzialność udostępnionego w chmurze

Strategia szczegółowe obrony jest wymagana do zwalczania zwiększa typów i złożoności ataków. Zabezpieczenia są udostępnione odpowiedzialność między klientem a firmy Microsoft. Microsoft odwołuje się do jako [modelu udostępnionego odpowiedzialność](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). Na poniższym rysunku przedstawiono podział odpowiedzialności.

![](media/azure-ddos-best-practices/image2.png)

Azure klienci korzystają z przeglądu nasze wskazówki i tworzenia globalnie rozproszone aplikacje zaprojektowane i sprawdzane pod kątem awarii.

## <a name="fundamental-best-practices"></a>Podstawowe najlepsze rozwiązania

Ataków DDoS i rośnie. Poniższa sekcja zawiera wskazówki opracowywać DDoS odpornej usługi na platformie Azure.

### <a name="design-for-security"></a>Projektowanie z uwzględnieniem zabezpieczeń

Klienci powinien zapewnić, że zabezpieczenia są priorytet w całym cyklu ich życia aplikacji projekt i implementację, wdrażania i operacji. Aplikacje mogą mieć usterki, które umożliwia stosunkowo niska wielkość przygotowanego żądania użyj długi ilości zasobów, co powoduje awarię usług. Aby chronić w usłudze działającej w systemie Microsoft Azure, klienci powinien dysponować dobrą znajomością ich architektury aplikacji i należy skoncentrować się na [5 słupków jakości oprogramowania](https://docs.microsoft.com/azure/architecture/guide/pillars).
Klienci wiedzieć typowy ruch woluminów, modelu łączności między aplikacją i inne aplikacje i punktów końcowych usługi udostępniane do publicznego Internetu.

Ponadto zapewnienie, że aplikacja jest odporność obsługi do odmowy usługi celem sama aplikacja jest o najwyższej wadze. Zabezpieczenia i prywatność wbudowanych w platformy Azure, począwszy od [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). SDL adresów zabezpieczeń na każdym etapie programowania i zapewnia, że Azure jest stale uaktualnione, aby jeszcze bardziej bezpieczne.

### <a name="design-for-scalability"></a>Projektowanie pod kątem skalowalności

Skalowalność to zdolność systemu do obsługi zwiększonego obciążenia. Klienci muszą projektowania aplikacji i [skalowanie w poziomie](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) aby sprostać zapotrzebowaniu namnożonego obciążenia, w szczególności w przypadku takiego ataku. Jeśli aplikacja jest zależna od jednego wystąpienia usługi, tworzy pojedynczy punkt awarii. Inicjowanie obsługi wielu wystąpień poprawia zarówno elastyczność i skalowalność.

Dla [usłudze Azure App Service](../app-service/app-service-value-prop-what-is.md), wybierz pozycję [planu usługi App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) oferująca wiele wystąpień. Usługi w chmurze Azure, skonfiguruj każdej z ról użytkownika do używania [wiele wystąpień](../cloud-services/cloud-services-choose-me.md). Aby uzyskać [maszynach wirtualnych Azure (VM)](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), upewnij się, że architektury maszyny Wirtualnej zawiera więcej niż jedną maszynę Wirtualną i że każda maszyna wirtualna znajduje się w [zestawu dostępności](../virtual-machines/virtual-machines-windows-manage-availability.md). Firma Microsoft zaleca używanie [zestawy skalowania maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) funkcji Skalowanie automatyczne.

### <a name="defense-in-depth"></a>Bardziej

W założeniu bardziej jest zarządzanie ryzykiem z różnymi strategiami obrony. Układanie warstwowo zabezpieczenia w aplikacji zmniejsza prawdopodobieństwo udanego ataku. Zaleca się, że klienci wdrożyć bezpiecznego projektów dla swoich aplikacji za pomocą wbudowanych możliwości platformy Azure.

Na przykład ryzyko ataku zwiększa się rozmiar lub powierzchni aplikacji. Zaleca się zmniejszenie powierzchni przez listę dozwolonych podobnej Zamknij w dół dostępnego adresu IP adresów miejsca & porty nasłuchiwania, które nie są wymagane na usługi równoważenia obciążenia ([ALB](../load-balancer/load-balancer-get-started-internet-portal.md)/[bramy aplikacji](../application-gateway/application-gateway-create-probe-portal.md)) lub za pośrednictwem [sieciowej grupy zabezpieczeń (NSG).](../virtual-network/virtual-networks-nsg.md)
Można użyć [usługi tagi](/virtual-network/security-overview.md) i [grup zabezpieczeń aplikacji](/virtual-network/security-overview.md) zminimalizowanie złożoności tworzenia reguły zabezpieczeń i skonfigurować zabezpieczenia sieci jako stanowi naturalne rozszerzenie struktury aplikacji.

Powinni wdrożyć usług platformy Azure w [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) zawsze, gdy jest to możliwe. Dzięki temu usługa zasobów do komunikowania się przy użyciu prywatnych adresów IP. Domyślnie usługa Azure ruch z sieci wirtualnej używa publiczne adresy IP jako źródłowych adresów IP. Przy użyciu [punkty końcowe usługi](../virtual-network/virtual-network-service-endpoints-overview.md) nastąpi przełączenie ruchu w ramach usługi do użycia jako źródłowe adresy IP prywatnych adresów sieci wirtualnej, podczas uzyskiwania dostępu do usługi Azure z sieci wirtualnej.

Często widzimy klienta z lokalnych zasobów pobierania ataku równolegle z zasobami na platformie Azure. Jeśli łączysz środowiska lokalnego do platformy Azure, firma Microsoft zaleca, aby zminimalizować narażenie zasobów lokalnych do publicznej sieci Internet. Wdrażając dobrze znane obiekty publiczne na platformie Azure, można użyć skali i zaawansowanych możliwości ochrona przed atakami DDoS usług Azure. Ponieważ te publicznie jednostki są często cel ataków DDoS, umieszczania ich w usłudze Azure pozwala ograniczyć wpływ na zasoby lokalne.

## <a name="azure-ddos-protection"></a>Ochrona przed atakami DDoS Azure

Platforma Azure ma dwa DDoS oferty usług, które zapewniają ochronę przed Podstawowa ochrona przed atakami DDoS — ataków (warstwy 3 i 4) sieci i standardowa ochrona przed atakami DDoS. 

### <a name="azure-ddos-basic-protection"></a>Azure podstawową ochronę przed atakami DDoS

Ochrona podstawowa jest zintegrowany Azure domyślnie bez ponoszenia dodatkowych kosztów. Skali i pojemność globalnie wdrożone sieci platformy Azure udostępnia obrony przed wspólnej ataków warstwy sieci za pośrednictwem zawsze na ruch ograniczenie monitorowania i w czasie rzeczywistym. Ochrona przed atakami DDoS Basic nie wymagają żadnych zmian konfiguracji lub aplikacji użytkownika. Tym PaaS usług, takich jak usługi Azure DNS są chronione przez podstawowy ochrona przed atakami DDoS wszystkich usług platformy Azure.

![](media/azure-ddos-best-practices/image3.png)

Podstawowa ochrona przed atakami DDoS Azure zawiera składniki sprzętu i oprogramowania. Płaszczyzny kontroli oprogramowania decyduje o tym, kiedy, gdzie, i jakiego rodzaju ruchu należy kierowane za pośrednictwem urządzeń sprzętowych, które analizowanie i usuwanie ruchu ataku. Płaszczyzny sterowania sprawia, że ta decyzja oparta na całej infrastruktury ochrona przed atakami DDoS *zasad*, który jest ustawiony statycznie i powszechnie stosowane do wszystkich klientów platformy Azure.

Na przykład zasady ochrony przed atakami DDoS Określa, jakie głośność ruchu ochrony powinny być *wyzwalane* (czyli ruchu dzierżawcy powinny być kierowane w za pomocą czyszczenia urządzenia), a następnie, jak szybka kontrola urządzenia powinien *ograniczenia* ataku.

Ochrona przed atakami DDoS — podstawowe usługi platformy Azure jest celem i ochronę infrastruktury platformy Azure. Zmniejsza on ruchu, przekroczenia szybkość, z której jest znacząca tak, że może mieć wpływ wielu klientów w środowisku wielodostępnym. Nie udostępnia alerty lub na klienta dostosowane zasady.

### <a name="azure-ddos-standard-protection"></a>Azure ochrona standardowe przed atakami DDoS

Standardowa ochrony zawiera ulepszone funkcje środki zaradcze DDoS i jest automatycznie dopasowane do ochrony określonych zasobów na platformie Azure w sieci wirtualnej. Ochrona jest proste, Włącz żadnej nowej lub istniejącej sieci wirtualnej i nie musi podawać aplikacji i zmiany zasobu. Ma kilka zalet w porównaniu podstawowe usługi, w tym rejestrowanie, alerty i telemetrii. Przedstawione poniżej są klucza wyróżniającymi standardowe ochrony przed atakami DDoS Azure usługi.

#### <a name="adaptive-realtime-tuning"></a>Dostrajanie adaptacyjną w czasie rzeczywistym

Ochrona przed atakami DDoS — podstawowe usługi Azure pomaga chronić klientów, ale chroni tylko ich w celu uniknięcia wpływu na innych klientów. Na przykład, jeśli usługa zostanie zainicjowana dla typowych woluminu uzasadnionych przychodzącego ruchu, który jest mniejszy niż *szybkość wyzwalacza* zasad ochrony przed atakami DDoS całej infrastruktury, może przejść takiego ataku na zasoby tego klienta niezauważalnie. Ogólnie rzecz biorąc złożonych rodzaju ataki ostatnie (na przykład wielu wektor DDoS), a także zachowania specyficzne dla aplikacji dzierżawców wywołania dla na odbiorcy, ochrona dostosowane zasady.
Jest to zrobić za pomocą dwóch insights:

1. Automatyczna nauka (IP) klienta warstwy 3/4 wzorce ruchu, a
2. Biorąc pod uwagę, że skali platformy Azure pozwala do przyjęcia znaczną ilość ruchu, minimalizując fałszywych alarmów.

![](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring--alerting"></a>Ochrona przed atakami DDoS telemetrii, monitorowanie i alerty

Przy użyciu DDoS ochrony Standard, uwidaczniamy telemetrii sformatowany za pomocą [Azure Monitor](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) okresie obowiązywania takiego ataku. Alerty można skonfigurować dla każdego z udostępnianych przez ochrona przed atakami DDoS metryki Azure Monitor. Rejestrowanie można zintegrować z Splunk (Azure Event Hubs), analiza dzienników Azure i usługi Azure Storage dla zaawansowanej analizy za pośrednictwem interfejsu diagnostyki Azure monitora.

##### <a name="ddos-mitigation-policies"></a>Zasady ograniczające DDoS

W portalu Azure kliknij **Monitor** \> **metryki**. Na **metryki** ekranu, wybierz grupę zasobów, typ zasobu z publicznym adresem IP i Azure publicznego adresu IP. Metryki DDoS będą widoczne w okienku dostępne metryki.

Stosuje standardowe ochrony przed atakami DDoS **trzy zasady ograniczające strojoną (TCP SYN, TCP i UDP)** każdego publicznego adresu IP zasobu chronionego, w sieci Wirtualnej, które ma włączone przed atakami DDoS. Progi zasad można wyświetlić, wybierając Metryka **"Liczba przychodzących pakietów do wyzwolenia DDoS ograniczenie"**.

![](media/azure-ddos-best-practices/image7.png)

Progi zasad są konfigurowane automatycznie za pomocą naszych machine learning profilowania ruchu sieciowego oparte na. Środki zaradcze DDoS występuje adresu IP zaatakowane tylko wtedy, gdy zostanie przekroczony próg zasad.

##### <a name="under-ddos-attack"></a>W obszarze ataków DDoS

Jeśli publiczny adres IP jest atakowany, wartość metryki "w obszarze DDoS ataku lub nie" przełącza 1 możemy wykonywać ograniczenie ruchu ataku.

![](media/azure-ddos-best-practices/image8.png)

Zaleca się Konfigurowanie alertów w tej metryki, aby otrzymać powiadomienie, gdy istnieje aktywne środki zaradcze DDoS wykonywane na publicznego adresu IP.

Aby uzyskać więcej informacji, zobacz [zarządzania Azure DDoS ochrony standardowe przy użyciu portalu Azure](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="resource-attacks"></a>Ataki zasobów

Specyficzne dla zasobu ataków na warstwie aplikacji, klientów, należy skonfigurować zapory aplikacji sieci Web (WAF) do aplikacji sieci web bezpieczne. Zapory aplikacji sieci Web sprawdza ruch przychodzący sieci web do blokowania SQL iniekcji, skryptów krzyżowych DDoS i inne ataki warstwy 7. Platforma Azure udostępnia [zapory aplikacji sieci Web jako funkcję bramy aplikacji](../application-gateway/application-gateway-web-application-firewall-overview.md) scentralizowane ochrony aplikacji sieci web z najczęściej luki w zabezpieczeniach i luk w zabezpieczeniach. Brak dostępnych odmian ofert zapory aplikacji sieci Web Azure partnerów, które mogą być bardziej odpowiednie do potrzeb za pośrednictwem [witrynę Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Nawet zapory aplikacji sieci Web jest narażony na ataki wyczerpania pojemności i stanu. Zdecydowanie zaleca się włączenie ochrony przed atakami DDoS Standard w sieci wirtualnej zapory aplikacji sieci Web, aby zapewnić ochronę przed pomiarowej & protokołu ataków. Aby uzyskać więcej informacji zobacz sekcję architektura odwołania.

### <a name="protection-planning"></a>Planowanie ochrony

Planowanie i przygotowanie są niezwykle istotne zrozumieć, jak system będzie wykonywać podczas takiego ataku. To planowanie i przygotowanie będzie również ułatwić projektowanie plan odpowiedzi zarządzania zdarzeniami.

Klienci powinien standardowe ochrony przed atakami DDoS jest na włączone wirtualne sieci internet ukierunkowane punktów końcowych. Konfigurowanie alertów DDoS pomaga w zapewnieniu stałej oka watchful na wszelkich potencjalnych ataków na infrastrukturę. Klienci monitorować swoje aplikacje niezależnie. Należy zrozumieć normalne zachowanie aplikacji. Jeśli aplikacja nie zachowuje się zgodnie z oczekiwaniami podczas takiego ataku, należy podjąć kroki.

#### <a name="ddos-attacks-orchestration"></a>Orchestration atakami DDoS

Jest dobrym rozwiązaniem, aby przetestować założeń o jak usług odpowiadać na atak, nawet przed zdarza się to poprzez okresowe symulacji. Podczas testowania, sprawdź, czy z usługi lub aplikacji w dalszym ciągu działać zgodnie z oczekiwaniami, a nie przerywania środowisko użytkownika końcowego nie istnieje. Identyfikowanie luk z punktu widzenia zarówno technologii, jak i proces i uwzględnić w strategii odpowiedzi przed atakami DDoS. Ogólne zalecanie służy do wykonywania takich testów w środowisku przemieszczania lub podczas godziny poza szczytem, aby zminimalizować wpływ na środowisko produkcyjne.

Ma firma Microsoft we współpracy z [chmury firmy BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) do tworzenia interfejsu, gdzie klientów platformy Azure mogą generować ruch przed ochrona przed atakami DDoS włączone publiczne punkty końcowe dla symulacji. [Punkt przerwania chmury](https://www.ixiacom.com/products/breakingpoint-cloud) symulacji umożliwi:

- Sprawdź poprawność sposób ochrona przed atakami DDoS Microsoft Azure przed atakami DDoS ochrony zasobów na platformie Azure

- Optymalizacja procesu odpowiedzi na zdarzenia w obszarze ataków DDoS

- Zgodność DDoS dokumentu

- Uczenia zespołów zabezpieczeń sieci

Bezpieczeństwa jest bezlitosnym bitwy, wymagających stałej innowacji w ochronę. Ochrona przed atakami DDoS Standard platformy Azure jest najnowocześniejsze oferuje najnowocześniejsze oferty dla klientów z efektywne rozwiązanie w celu złagodzenia coraz bardziej złożone atakami DDoS.

## <a name="components-of-a-ddos-response-strategy"></a>Składniki strategii odpowiedzi przed atakami DDoS

W większości przypadków zasobów platformy Azure, celem ataków DDoS ma minimalnej interwencji wymagane z punktu widzenia użytkownika końcowego. Nadal implementując DDoS środki zaradcze w ramach strategii odpowiedzi na zdarzenia w organizacji zapewni minimalnym wpływie na ciągłość prowadzenia działalności biznesowej.

### <a name="microsoft-threat-intelligence"></a>Microsoft analizy zagrożeń

Firma Microsoft udostępnia szeroką gamę zagrożeń sieć analizy, która używa wspólną wiedzą społeczności zabezpieczeń obsługującego usługi online firmy Microsoft, partnerzy firmy Microsoft i relacje w Internecie zabezpieczeń. Funkcję dostawcy infrastruktury krytyczne Microsoft odbiera wczesnego ostrzegania o zagrożeniach, przyjmuje podstawowej analizy zagrożeń uzyskane z innych usług online firmy Microsoft i globalnych klienta firmy Microsoft. Wszystkie analizy zagrożeń firmy Microsoft jest włączona do swoich produktów ochrona przed atakami DDoS Azure.

Oprócz tego firmy Microsoft jednostki ds. przestępstw cyfrowych (DCU) wykonuje obraźliwe strategii przed zakłócanych wspólne źródło polecenia i kontroli do ataków DDoS.

### <a name="perform-a-risk-evaluation-of-your-azure-resources"></a>Przeprowadzenie oceny ryzyka, zasobów platformy Azure

należy bezwzględnie zrozumieć zakres ryzyko przed atakami DDoS, w sposób ciągły. Klienci okresowo zapytaj same: nowe publicznie dostępnych zasobów Azure wymagają ochrony? Jest pojedynczym punktem awarii usługi? Jak można odizolowaną w celu ograniczenia wpływu ataku podczas nadal udostępnianie usług klientom prawidłowe usług? Czy istnieją sieci wirtualnych, w którym standardowe ochrony przed atakami DDoS powinien być włączony, ale nie jest? Czy Moje usług aktywny/aktywny z trybem failover w różnych regionach?

### <a name="customer-ddos-response-team"></a>Zespół klientów przed atakami DDoS

Tworzenie zespołu odpowiedzi przed atakami DDoS jest krokiem klucza w celu zapewnienia skutecznego i szybkie odpowiedzi na ataki. Klienci, należy określić różne kontakty w organizacji, którzy nadzoruje planowania i wykonywania. Zespół odpowiedzi przed atakami DDoS powinien mieć dogłębnej wiedzy Azure DDoS ochrony standardowe usługi i powinna być w stanie w identyfikacji i zmniejszenia atak współpracę z różnych klientów wewnętrznych i zewnętrznych, w tym zespołem pomocy technicznej firmy Microsoft w razie potrzeby.

Firma Microsoft zaleca dołączanie ćwiczeń planowania i symulacji DDoS zespół, tym testowania, w ramach normalnych dostępności usługi i planowania ciągłości skali. 

### <a name="during-an-attack"></a>W przypadku ataków

Standardowa ochrony przed atakami DDoS Azure będzie identyfikowanie i usuwanie atakami DDoS bez interwencji użytkownika. Aby uzyskać powiadomienie, gdy istnieje aktywne środki zaradcze chronionych publicznego adresu IP, możesz [skonfigurować alert](../virtual-network/ddos-protection-manage-portal.md) na Metryka "w obszarze DDoS ataku lub nie". Można dalsze przeglądu i tworzyć alerty zgodnie z potrzebami dla innych metryk DDoS zrozumienie skalowania ataków, ruch utracona itp.

#### <a name="when-to-contact-microsoft-support"></a>Kiedy skontaktuj się z pomocą techniczną firmy Microsoft

- Podczas takiego ataku stwierdzisz, że znacznie zmniejsza wydajność zasobu chronionego, czy zasób jest niedostępny.

- Jeśli uważasz, że usługa Ochrona przed atakami DDoS nie zachowuje się zgodnie z oczekiwaniami. Ochrona przed atakami DDoS usługi zostanie zainicjowana tylko środki zaradcze, jeśli jest spełniony poniżej kryteria:

    - Wartość metryki "zasady do wyzwalania ograniczenie DDoS (SYN TCP/TCP/UDP) jest niższa niż ruchu odbieranego na chronionego zasobu publicznego adresu IP.

- Jeśli wiesz, ma mieć zdarzenie planowane wirusa, który spowoduje znaczne zwiększenie ruchu sieciowego.

- Jeśli aktora jest zagrożona do uruchomienia przed atakami DDoS atak zasobów.

Dla krytycznym wpływające na problemy, Utwórz ważność A [obsługuje biletu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>Kroki ataku POST

Zawsze jest dobre rozwiązanie, czy po przeprowadzeniu ataku typu postmortem i dopasuj strategii odpowiedzi przed atakami DDoS, zgodnie z potrzebami. Kwestie do rozważenia:

- Został istnieje zakłóceń do usługi lub przez użytkownika końcowego wystąpić z powodu braku Skalowalna architektura?

- Które aplikacji lub usługi w trakcie najbardziej?

- Jak skuteczne było strategii odpowiedzi przed atakami DDoS i jak można ją udoskonalić dalsze?

Jeśli podejrzewasz, że jesteś w obszarze takiego ataku skontaktuj się z pomocą normalne kanałami pomocą techniczną platformy Azure.

## <a name="ddos-protection-reference-architectures"></a>Architektur referencyjnych ochrony przed atakami DDoS

Standardowa ochrony przed atakami DDoS zaprojektowano [dla usług, które zostały wdrożone w sieci wirtualnej.](../virtual-network/virtual-network-for-azure-services.md) Dla innych usług wartość domyślna Podstawowa ochrona przed atakami DDoS będą stosowane. Architektur konkretnego odwołania przedstawione poniżej są rozmieszczane według scenariuszy, wzorami architektura zgrupowane razem.

### <a name="virtual-machine-windowslinux-workloads"></a>Obciążenia maszyny wirtualnej (z systemem Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplikacja uruchomiona na obciążenia zrównoważonym maszyny wirtualne

Ta architektura referencyjna przedstawia zestaw sprawdzonych rozwiązań dotyczących uruchamiania wielu maszyn wirtualnych (VM) z systemem Windows w zestawie skalowania za modułem równoważenia obciążenia służących do zwiększania dostępności i skalowalności. Taka architektura może służyć do dowolnego bezstanowego obciążenia, takich jak serwer sieci web.

![](media/azure-ddos-best-practices/image9.png)

W tej architekturze obciążenie jest rozkładane na wiele wystąpień maszyny wirtualnej. Istnieje jeden publiczny adres IP, a ruch internetowy jest dystrybuowany do maszyn wirtualnych przy użyciu modułu równoważenia obciążenia. Ochrona przed atakami DDoS — standardowe jest włączona w sieci wirtualnej usługi równoważenia obciążenia Azure (internet), który ma publiczny adres IP skojarzone z nim.

Moduł równoważenia obciążenia rozdziela przychodzące żądania Internetu do wystąpień maszyn wirtualnych. Zestawy skalowania maszyny Wirtualnej umożliwia liczby maszyn wirtualnych do skalowania, lub ręcznie lub automatycznie na podstawie wstępnie zdefiniowanych reguł. Jest to ważne, jeśli zasób znajduje się w obszarze takiego ataku. Odwołuje się do tego [artykułu](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm), aby uzyskać więcej informacji o architekturze wzorcowej.

#### <a name="applications-running-on-windows-n-tier"></a>Aplikacje działające na warstwowe systemu Windows

Istnieje wiele sposobów implementowania architektury n-warstwowej. Na diagramie przedstawiono typową 3-warstwową aplikację internetową. Taka architektura jest oparta ma [Uruchom równoważeniem obciążenia maszyn wirtualnych na skalowalność i dostępność](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Warstwy internetowa i biznesowa używają maszyn wirtualnych z równoważeniem obciążenia.

![](media/azure-ddos-best-practices/image10.png)

W architekturze powyżej standardowe ochrony przed atakami DDoS jest włączona w sieci wirtualnej. Wszystkie publiczne adresy IP w sieci wirtualnej otrzyma ochrona przed atakami DDoS Layer3/Layer4. 7 warstwy ochrony powinny zostać wdrożone brama aplikacji w jednostce SKU zapory aplikacji sieci Web. Zapoznaj się [to](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier) artykułu, aby uzyskać więcej informacji o architekturze wzorcowej.

#### <a name="paas-web-application"></a>PaaS aplikacji sieci web

Informacje o architekturze wzorcowej przedstawia uruchamianie aplikacji usługi Azure App Service w pojedynczym regionie. Zawiera zestaw sprawdzonych rozwiązań dla aplikacji sieci web, która używa tej architektury [usłudze Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) i [bazy danych SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/).
Rezerwy region jest skonfigurowany dla trybu failover scenariuszy.

![](media/azure-ddos-best-practices/image11.png)

Menedżer ruchu kieruje żądania przychodzące do brama aplikacji w jednym z regionów. Podczas wykonywania normalnych operacji kieruje żądania do bramy aplikacji na aktywnym regionem. Jeśli region jest niedostępny, nie Menedżera ruchu za pośrednictwem brama aplikacji w regionie wstrzymania.

Cały ruch z Internetu kierowanych do aplikacji sieci web jest kierowany do [publiczny adres IP bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) za pośrednictwem Menedżera ruchu. W tym scenariuszu usługi aplikacji (aplikacje sieci web) sam nie jest bezpośrednio zewnętrznie ukierunkowane i jest chroniony przez bramę aplikacji. Zalecane jest skonfigurowanie aplikacji bramy zapory aplikacji sieci Web SKU (Zapobiegaj tryb) do ochrony przez atakami warstwy 7 (HTTP/HTTPS/gniazda sieci Web). Ponadto aplikacje sieci Web są skonfigurowane do [akceptuje tylko ruch z bramy aplikacji](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) adresu IP.

Zapoznaj się [to](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) artykułu, aby uzyskać więcej informacji o architekturze wzorcowej.

### <a name="mitigation-for-non-web-paas-services"></a>Środki zaradcze dla usługi PaaS w sieci web

#### <a name="hdinsight-on-azure"></a>HDInsight w systemie Azure

Przedstawia informacje o architekturze wzorcowej Konfigurowanie zasad ochrony przed atakami DDoS dla [klastra usługi HDInsight](https://docs.microsoft.com/azure/hdinsight/) na platformie Azure. Należy się upewnić, że klaster usługi HDInsight jest połączony z siecią wirtualną i ochrona przed atakami DDoS jest włączona w tej sieci wirtualnej.

![](media/azure-ddos-best-practices/image12.png)

![](media/azure-ddos-best-practices/image13.png)

W ramach tej architektury ruch kierowany do klastra usługi HDInsight z Internetu jest kierowany do publicznego adresu IP, skojarzone z modułem równoważenia obciążenia bramy usługi HDInsight. Moduł równoważenia obciążenia bramy przesyła ruchu do węzłów głównych lub węzłów procesu roboczego bezpośrednio. Podane standardowe ochrony przed atakami DDoS jest włączona w sieci wirtualnej w usłudze HDInsight, wszystkich publicznych adresów IP w sieci wirtualnej zostanie wyświetlony ochrona przed atakami DDoS Layer3/Layer4. Powyżej architektura referencyjna można łączyć z N-warstwy/kilku Region architektur referencyjnych.

Aby uzyskać więcej informacji o architekturze wzorcowej, zapoznaj się [rozszerzenie Azure HDInsight przy użyciu sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) dokumentacji.

### <a name="azure-api-management"></a>Zarządzanie interfejsami API Azure

Informacje o architekturze wzorcowej chroni publiczny punkt końcowy z [zarządzanie interfejsami API](../api-management/api-management-key-concepts.md) publikowania interfejsów API klientom spoza organizacji zasobów. APIM powinny zostać wdrożone w zewnętrznej sieci wirtualnej, aby włączyć ochronę przed atakami DDoS.

![](media/azure-ddos-best-practices/image15.png)

Konfigurując zewnętrzną sieć wirtualną, zarządzanie interfejsami API bramy & developer portal są dostępne z publicznego Internetu za pośrednictwem publiczny moduł równoważenia obciążenia. W ramach tej architektury standardowe ochrony przed atakami DDoS jest włączona na zewnętrzną sieć wirtualną APIM sieci wirtualnej. Ruch jest kierowany z Internetu do publicznego adresu IP APIM, które jest chronione przed atakami sieciowymi Layer3/Layer4. Aby chronić przed atakami warstwy 7 HTTP/HTTPs, można skonfigurować bramę aplikacji w trybie zapory aplikacji sieci Web.

Lista dodatkowych usług, które są wdrażane w sieci wirtualnej i można skonfigurować dla ochrony przed atakami DDoS — standardowe jest przechowywany [tutaj](../virtual-network/virtual-network-for-azure-services.md). Ochrona przed atakami DDoS — standardowa obsługuje tylko zasoby usługi Azure Resource Manager. *Wprowadzony wdrożenia środowiska usługi aplikacji (ASE) w sieci Wirtualnej z publicznym adresem IP nie jest obsługiwany natywnie.* Aby uzyskać szczegółowe informacje o ochronie ASE środowiska znajdują się w tej sekcji.

## <a name="next-steps"></a>Kolejne kroki

* [Azure stronę produktu ochrony przed atakami DDoS](https://azure.microsoft.com/services/ddos-protection/)

* [Blog ochrony przed atakami DDoS Azure](http://aka.ms/ddosblog)

* [Dokumentację platformy Azure ochrona przed atakami DDoS ](../virtual-network/ddos-protection-overview.md)
