---
title: Ochrona przed atakami DDoS Azure najlepsze rozwiązania i referencyjne architektury | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 042dd4876a63e5881e67456b449570b01cb967a5
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="azure-ddos-protection-best-practices-and-reference-architectures"></a>Ochrona przed atakami DDoS Azure: Najlepsze rozwiązania i referencyjne architektury

Ten artykuł dotyczy IT inne osoby podejmujące decyzje i personel zabezpieczeń. Oczekuje się, że znasz Azure, sieci i zabezpieczeń.

Projektowanie dla rozproszonych odmowa usługi (DDoS) odporności wymaga planowania i projektowania w różnych trybach awarii. Ten artykuł zawiera wskazówki dotyczące projektowania aplikacji na platformie Azure zapewnia odporność na awarie przed atakami DDoS.

## <a name="types-of-attacks"></a>Typy ataków

Przed atakami DDoS jest typem ataku, który próbuje na wyczerpanie zasobów aplikacji. Celem jest wpływ na dostępność aplikacji i uprawnionych żądań obsługi. Ataki stają się coraz bardziej zaawansowane i większy rozmiar i wpływu. Może być celem ataków DDoS dowolnego punktu końcowego, który jest publicznie dostępny za pośrednictwem Internetu.

Platforma Azure udostępnia ciągłej ochrony przed atakami DDoS. Ta ochrona jest zintegrowany platformy Azure, domyślnie i bez dodatkowych kosztów. 

Oprócz podstawowych ochrona przed atakami DDoS na platformie [standardowe ochrony przed atakami DDoS Azure](https://azure.microsoft.com/services/ddos-protection/) zapewnia zaawansowane możliwości środki zaradcze DDoS przed atakami sieciowymi. Jest on automatycznie dostosowana do ochrony określonych zasobów na platformie Azure. Ochrona jest proste włączyć podczas tworzenia nowej sieci wirtualnej. Można również wykonać po utworzeniu, a nie wymaga żadnych zmian w aplikacji lub zasobie.

![Rolę w ochronie klientów i sieci wirtualnej od osoby atakującej ochrona przed atakami DDoS Azure](media/azure-ddos-best-practices/image1.png)

Atakami DDoS mogą być klasyfikowane na trzy kategorie: pomiarowej, protokół i zasobów.

### <a name="volumetric-attacks"></a>Ataki pomiarowej

Ataki pomiarowej są najczęściej spotykanym typem ataków DDoS. Ataki pomiarowej są zamachów metodą siłową, które odnoszą się do warstwy transportu i sieci. Próbują na wyczerpanie zasobów, takich jak łączy sieciowych. 

Tego rodzaju ataki są często używane wiele systemów zainfekowanych do wypełniania warstwy sieci z pozornie potrzebnego ruchu. Używają protokołów warstwy sieciowej takie jak protokół komunikat sterowania Internetem (ICMP), protokół UDP (User Datagram) i Transmission Control Protocol (TCP).

Atakami DDoS najczęściej używane warstwy sieci są SYN TCP zalewania echa protokołu ICMP, UDP zalewania DNS, i ataków wzmocnienia NTP. Ten typ ataku umożliwia nie tylko zakłócić działanie usługi, ale także jako smokescreen dla bardziej nefarious i docelowej sieci nieautoryzowanego dostępu. Na przykład ostatnie ataku pomiarowej [wykorzystać Memcached](https://www.wired.com/story/github-ddos-memcached/) który wpływa GitHub. Atak UDP port 11211 i do wygenerowane 1.35 Tb/s woluminu ataku.

### <a name="protocol-attacks"></a>Protokół ataków

Protokół ataków protokołów aplikacji docelowej. Próbują użyć wszystkich dostępnych zasobów w urządzeniach infrastruktury, takie jak zapory i serwery aplikacji i usługi równoważenia obciążenia. Ataki protokołu Użyj pakietów, które są źle sformułowane lub zawierać protokół nieprawidłowości. Tego rodzaju ataki działają, wysyłając dużą liczbę otwartych żądań serwerów i innych urządzeń komunikatu odpowiedzi i oczekiwania na odpowiedź pakietu. Element docelowy próbuje odpowiadania na żądania otwarte, powodując systemu do awarii.

Najbardziej typowym przykładem opartych na protokole takiego ataku jest powódź TCP SYN. W tym ataku kolejne żądania TCP SYN próbuje przeciąży obiektu docelowego. Celem jest zapewnienie element docelowy nie odpowiada. Awaria Dyn 2016, oprócz trwa atak warstwy aplikacji, obejmował TCP SYN floods tego portu docelowego 53 Dyn na serwerach DNS.

### <a name="resource-attacks"></a>Ataki zasobów

Ataki zasobu docelowego warstwy aplikacji. W ramach działań zmierzających do przeciąży system one wyzwalanie procesów zaplecza. Zasób ataków nadużycia ruchu, który wygląda normalne, ale ten wykonuje obciążające Procesor zapytań do serwera. Wielkość ruchu sieciowego potrzebne na wyczerpanie zasobów jest mniejszy niż inne rodzaje ataków. Ruch w atak zasobów jest można odróżnić od wiarygodnego ruchu, utrudniając wykryć. Najbardziej typowe ataki zasobów są na usługi DNS i HTTP/HTTPS.

## <a name="shared-responsibility-in-the-cloud"></a>Odpowiedzialność udostępnionego w chmurze

Strategii obrony zabezpieczeń pomaga zwalczania różnych rosnący i złożoności ataków. Zabezpieczenia są udostępnione odpowiedzialność między klientem a firmy Microsoft. Microsoft wywołuje to [modelu udostępnionego odpowiedzialność](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). Poniższa ilustracja przedstawia podział odpowiedzialności:

![Obowiązki klienta i Azure](media/azure-ddos-best-practices/image2.png)

Azure klienci korzystają z przeglądu najlepszych rozwiązań firmy Microsoft i tworzenie globalnie rozproszone aplikacje, które są zaprojektowane i sprawdzane pod kątem awarii.

## <a name="fundamental-best-practices"></a>Podstawowe najlepsze rozwiązania

W poniższych sekcjach znajdują się wskazówki do tworzenia usług DDoS odporne na platformie Azure.

### <a name="design-for-security"></a>Projektowanie z uwzględnieniem zabezpieczeń

Upewnij się, że zabezpieczeń jest priorytet w całym cyklu ich życia aplikacji projekt i implementację, wdrażania i operacji. Aplikacje mogą mieć usterki, które umożliwiają stosunkowo mały rozmiar żądań, aby używać długi ilości zasobów, co powoduje awarię usług. 

Aby lepiej chronić w usłudze działającej w systemie Microsoft Azure, należy dysponować dobrą znajomością architektury aplikacji i skoncentrować się na [pięć słupków jakości oprogramowania](https://docs.microsoft.com/azure/architecture/guide/pillars).
Typowy ruch woluminów, należy wiedzieć modelu łączności między aplikacją i innymi aplikacjami i punktów końcowych usługi, które są dostępne do publicznego Internetu.

Najbardziej ważne jest zapewnienie, że aplikacja jest odporność obsługi odmowę usługi, która jest skierowana na samej aplikacji. Zabezpieczenia i prywatność są wbudowane w platformę Azure, począwszy od [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). SDL adresów zabezpieczeń na każdym etapie programowania i zapewnia, że Azure jest stale uaktualnione, aby jeszcze bardziej bezpieczne.

### <a name="design-for-scalability"></a>Projektowanie pod kątem skalowalności

Skalowalność to, jak można zapewnienia obsługi zwiększonego obciążenia systemu. Należy projektować aplikacje [skalowanie w poziomie](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) aby sprostać zapotrzebowaniu namnożonego obciążenia, w szczególności w przypadku takiego ataku. Jeśli aplikacja jest zależna od jednego wystąpienia usługi, tworzy pojedynczy punkt awarii. Inicjowanie obsługi wielu wystąpień powoduje, że system bardziej odporne i skalowalność.

Dla [usłudze Azure App Service](../app-service/app-service-value-prop-what-is.md), wybierz pozycję [planu usługi aplikacji](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) oferująca wiele wystąpień. Usługi w chmurze Azure, skonfiguruj każdej z ról użytkownika do używania [wiele wystąpień](../cloud-services/cloud-services-choose-me.md). Aby uzyskać [maszyny wirtualne Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), upewnij się, że architektury maszyny wirtualnej (VM) zawiera więcej niż jedną maszynę Wirtualną i że każda maszyna wirtualna znajduje się w [zestawu dostępności](../virtual-machines/virtual-machines-windows-manage-availability.md). Firma Microsoft zaleca używanie [zestawy skalowania maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) funkcji Skalowanie automatyczne.

### <a name="defense-in-depth"></a>Bardziej

W założeniu bardziej jest zarządzanie ryzykiem przy użyciu różnych obrony strategii. Układanie warstwowo zabezpieczenia w aplikacji zmniejsza prawdopodobieństwo udanego ataku. Zaleca się wdrożenie bezpiecznego projektów dla aplikacji za pomocą wbudowanych możliwości platformy Azure.

Na przykład zwiększa ryzyko ataku z rozmiarem (*powierzchnia*) aplikacji. Powierzchni można zmniejszyć przy użyciu listę dozwolonych podobnej do zamknięcia narażonych przestrzeń adresów IP i nasłuchiwać portów, które nie są wymagane na usługi równoważenia obciążenia ([moduł równoważenia obciążenia Azure](../load-balancer/load-balancer-get-started-internet-portal.md) i [brama aplikacji w usłudze Azure](../application-gateway/application-gateway-create-probe-portal.md)). [Sieciowe grupy zabezpieczeń (NSG)](../virtual-network/virtual-networks-nsg.md) można zmniejszyć obszar ataków.
Można użyć [usługi tagi](/virtual-network/security-overview.md) i [grup zabezpieczeń aplikacji](/virtual-network/security-overview.md) Aby zmniejszyć złożoność do tworzenia reguł zabezpieczeń i konfigurowania zabezpieczeń sieciowych jako stanowi naturalne rozszerzenie struktury aplikacji.

Należy wdrożyć usług platformy Azure w [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) zawsze, gdy jest to możliwe. Takie rozwiązanie pozwala zasobów usługi do komunikacji przy użyciu prywatnych adresów IP. Domyślnie usługa Azure ruch z sieci wirtualnej używa publiczne adresy IP jako źródłowych adresów IP. Przy użyciu [punkty końcowe usługi](../virtual-network/virtual-network-service-endpoints-overview.md) nastąpi przełączenie ruchu w ramach usługi do korzystania wirtualnych sieci prywatnych adresów źródłowych adresów IP przy ich uzyskujesz dostęp do usługi Azure z sieci wirtualnej.

Często widzimy klientów z lokalnych zasobów pobierania ataków wraz z ich zasobami na platformie Azure. Jeśli łączysz środowiska lokalnego do platformy Azure, firma Microsoft zaleca, aby zminimalizować narażenie zasobów lokalnych do publicznej sieci internet. Wdrażając dobrze znane obiekty publiczne na platformie Azure, można użyć skali i zaawansowane funkcje ochrony przed atakami DDoS platformy Azure. Ponieważ te publicznie jednostki są często cel ataków DDoS, umieszczania ich w usłudze Azure pozwala ograniczyć wpływ na zasoby lokalne.

## <a name="azure-offerings-for-ddos-protection"></a>Oferty Azure do ochrony przed atakami DDoS

Platforma Azure ma dwa DDoS oferty usług, które zapewniają ochronę przed atakami sieciowymi (warstwy 3 i 4): ochrona przed atakami DDoS — podstawowe i standardowe ochrony przed atakami DDoS. 

### <a name="ddos-protection-basic"></a>Basic ochrony przed atakami DDoS

Ochrona podstawowa jest zintegrowany Azure domyślnie bez ponoszenia dodatkowych kosztów. Skalowanie i pojemność globalnie wdrożone sieci platformy Azure udostępnia obrony przed wspólnej ataków warstwy sieci za pośrednictwem ruchu zawsze na ograniczenie monitorowania i w czasie rzeczywistym. Podstawowa ochrona przed atakami DDoS nie wymagają żadnych zmian konfiguracji lub aplikacji użytkownika. Podstawowa ochrona przed atakami DDoS ułatwia ochronę wszystkich usług platformy Azure, w tym PaaS usług, takich jak usługi Azure DNS.

![Mapowanie reprezentację sieć platformy Azure, tekst "globalne DDoS środki zaradcze obecności" i "Początkowe DDoS ograniczenie pojemności"](media/azure-ddos-best-practices/image3.png)

Podstawowa ochrona przed atakami DDoS na platformie Azure zawiera składniki sprzętu i oprogramowania. Płaszczyzny kontroli oprogramowania decyduje o tym, kiedy, gdzie, i jakiego rodzaju ruchu należy kierowane za pośrednictwem urządzeń sprzętowych, które analizowanie i usuwanie ruchu ataku. Ta decyzja oparta na ochrona przed atakami DDoS całej infrastruktury sprawia, że płaszczyzny sterowania *zasad*. Ta zasada jest statycznie ustawić i powszechnie stosowane do wszystkich klientów platformy Azure.

Na przykład zasady ochrony przed atakami DDoS Określa, jakie głośność ruchu ochrony powinny być *wyzwolone.* (Oznacza to, ruch dzierżawcy powinny być kierowane w za pomocą czyszczenia urządzenia.) Zasady następnie określa, jak kontrola urządzeń ma *ograniczenia* ataku.

Usługi Azure DDoS Ochrona podstawowa jest przeznaczona dla ochrony infrastruktury i ochrony z platformy Azure. Zmniejsza go ruchu, gdy przekracza ona szybkość, z której jest to istotne, że może mieć wpływ na wielu klientów w środowisku wielodostępnym. Nie udostępnia alerty lub na klienta dostosowane zasady.

### <a name="ddos-protection-standard"></a>Standard ochrony przed atakami DDoS

Standardowa ochrony zawiera ulepszone funkcje środki zaradcze DDoS. Jest on automatycznie dopasowane do ochrony określonych zasobów na platformie Azure w sieci wirtualnej. Ochrona jest proste, Włącz żadnej nowej lub istniejącej sieci wirtualnej, a nie wymaga to aplikacji lub zasobie zmian. Ma kilka zalet w porównaniu podstawowe usługi, w tym rejestrowanie, alerty i telemetrii. W poniższych sekcjach opisano najważniejsze funkcje usługi Azure DDoS ochrony standardowa.

#### <a name="adaptive-real-time-tuning"></a>Adaptacyjne dostrajanie w czasie rzeczywistym

Usługi Azure DDoS Ochrona podstawowa pomaga chronić klientów i zapobiec wpływa na innym klientom. Na przykład, jeśli usługa zostanie zainicjowana dla typowych woluminu uzasadnionych przychodzącego ruchu, który jest mniejszy niż *szybkość wyzwalacza* zasad ochrony przed atakami DDoS całej infrastruktury, może przejść takiego ataku na zasoby tego klienta niezauważalnie. Ogólnie rzecz biorąc złożoność ostatnie ataków (na przykład wielu wektor DDoS) i zachowania specyficzne dla aplikacji dzierżawcy wymagają na odbiorcy, ochrona dostosowane zasady. Usługa umożliwia zrealizowanie to dostosowanie za pomocą dwóch insights:

- Automatyczne uczenie się ich wzorce ruchu (IP) klienta dla warstwy 3 i 4.

- Minimalizowanie fałszywych alarmów, biorąc pod uwagę skali Azure umożliwia go do przyjęcia znaczną ilość ruchu sieciowego.

![Diagram ochrony przed atakami DDoS — standardowe działania "Zasady generacji" kółku](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Ochrona przed atakami DDoS telemetrii, monitorowanie i alerty

Standardowa ochrony przed atakami DDoS udostępnia rozbudowane dane telemetryczne za pośrednictwem [Azure Monitor](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) na czas trwania takiego ataku. Można skonfigurować alerty dotyczące poszczególnych metryki Azure monitorowanie używa ochrona przed atakami DDoS. Rejestrowanie można zintegrować z Splunk (Azure Event Hubs), analiza dzienników Azure i usługi Azure Storage dla zaawansowanej analizy za pośrednictwem interfejsu diagnostyki Azure monitora.

##### <a name="ddos-mitigation-policies"></a>Zasady ograniczające DDoS

W portalu Azure wybierz **Monitor** > **metryki**. W **metryki** okienku, wybierz grupę zasobów, wybierz typ zasobu **publicznego adresu IP**i wybierz pozycję Azure publicznego adresu IP. Metryki DDoS są widoczne w **dostępne metryki** okienka.

Standardowa ochrony przed atakami DDoS stosuje trzy zasady środki zaradcze strojoną (TCP SYN, TCP i UDP) dla każdego publicznego adresu IP zasobu chronionego, w sieci wirtualnej, które ma włączone przed atakami DDoS. Progi zasad można wyświetlić, wybierając Metryka **przychodzących pakietów do wyzwolenia ograniczenie DDoS**.

![Dostępne metryki i wykres metryk](media/azure-ddos-best-practices/image7.png)

Progi zasad są konfigurowane automatycznie przez machine learning sieci ruchu profilowanie. Środki zaradcze DDoS występuje adresu IP zaatakowane tylko wtedy, gdy zostanie przekroczony próg zasad.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metryki dla adresu IP w obszarze ataków DDoS

Jeśli jest publiczny adres IP pod ataku, wartość metryki **DDoS w ramach ataku lub nie** zmienia się 1 ochrona przed atakami DDoS wykonuje ograniczenie na ruch ataku.

![Metryka "W obszarze DDoS ataku lub nie" i wykres](media/azure-ddos-best-practices/image8.png)

Zaleca się Konfigurowanie alertów w tej metryki. Możesz następnie wyświetlone powiadomienie, gdy jest wykonywana na publicznego adresu IP aktywne środki zaradcze DDoS.

Aby uzyskać więcej informacji, zobacz [zarządzania Azure DDoS ochrony standardowe przy użyciu portalu Azure](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="web-application-firewall-for-resource-attacks"></a>Zapora aplikacji sieci Web do ataków zasobów

Specyficzne dla zasobu ataków na warstwie aplikacji, należy skonfigurować zapory aplikacji sieci web (WAF), bezpieczne sieci web aplikacji. Zapory aplikacji sieci Web sprawdza ruch przychodzący sieci web do bloków iniekcji SQL, krzyżowych skryptów przed atakami DDoS i inne ataki warstwy 7. Platforma Azure udostępnia [zapory aplikacji sieci Web jako funkcję bramy aplikacji](../application-gateway/application-gateway-web-application-firewall-overview.md) scentralizowane ochrony aplikacji sieci web z najczęściej luki w zabezpieczeniach i luk w zabezpieczeniach. Brak dostępnych ofert innych zapory aplikacji sieci Web Azure partnerów, które mogą być bardziej odpowiednie do potrzeb za pośrednictwem [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Nawet zapór aplikacji sieci web jest narażony na ataki wyczerpania pojemności i stanu. Protokół atakami i zdecydowanie zaleca się włączenie ochrony przed atakami DDoS — standardowe w sieci wirtualnej zapory aplikacji sieci Web w celu ochrony przed pomiarowej. Aby uzyskać więcej informacji, zobacz [architektur referencyjnych ochrona przed atakami DDoS](#ddos-protection-reference-architectures) sekcji.

### <a name="protection-planning"></a>Planowanie ochrony

Planowanie i przygotowanie są niezwykle istotne zrozumieć, jak system będzie wykonywać podczas takiego ataku. Projektowanie plan zarządzania zdarzeniami w odpowiedzi jest częścią tej pracy.

Jeśli ochrona przed atakami DDoS — Standard, upewnij się, czy jest włączona w sieci wirtualnej internetowy punktów końcowych. Konfigurowanie alertów DDoS pomaga stale obserwować wszelkich potencjalnych ataków na infrastrukturę. 

Należy monitorować aplikacje niezależnie. Zrozumienie normalne zachowanie aplikacji. Przygotowanie do działania, jeśli aplikacja nie zachowuje się zgodnie z oczekiwaniami podczas takiego ataku.

#### <a name="testing-through-simulations"></a>Testowanie za pomocą symulacje

Jest dobrą praktyką jest z założenia dotyczące sposobu usług reaguje na atak symulacje okresowe przeprowadzanie testów. Podczas testowania, zweryfikuj, czy z usługi lub aplikacji w dalszym ciągu działać zgodnie z oczekiwaniami oraz nie przerw w działaniu środowiska użytkownika nie istnieje. Identyfikowanie luk z punktu widzenia technologii i procesu i je uwzględnić w strategii odpowiedzi przed atakami DDoS. Zaleca się wykonanie tych testów środowiska przejściowe lub podczas godziny poza szczytem, aby zminimalizować wpływ do środowiska produkcyjnego.

Firma Microsoft ma we współpracy z [chmury firmy BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) do tworzenia interfejsu, gdzie klientów platformy Azure mogą generować ruch przed włączona ochrona przed atakami DDoS publiczne punkty końcowe dla symulacji. Można użyć [chmury firmy BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) symulację do:

- Sprawdź, jak ochrona przed atakami DDoS Azure ułatwia ochronę przed atakami DDoS zasobów platformy Azure.

- Optymalizuj procesu odpowiedzi na zdarzenia w obszarze takiego ataku.

- Następnie należy udokumentować DDoS zgodności.

- Uczenia zespołów zabezpieczeń sieci.

Bezpieczeństwa wymaga stałej innowacji w ochronę. Azure ochrony przed atakami DDoS Standard jest stan systemu — najnowszą oferty z efektywne rozwiązanie w osłabianiu ataków DDoS coraz bardziej złożone.

## <a name="components-of-a-ddos-response-strategy"></a>Składniki strategii odpowiedzi przed atakami DDoS

Takiego ataku, przeznaczonego dla zasobów Azure zwykle wymaga minimalnej interwencji z punktu widzenia użytkownika. Nadal zawierające DDoS środki zaradcze w ramach strategii odpowiedzi na zdarzenia minimalizuje wpływ na ciągłość prowadzenia działalności biznesowej.

### <a name="microsoft-threat-intelligence"></a>Microsoft analizy zagrożeń

Firma Microsoft udostępnia szeroką gamę zagrożeń analizy sieci. Ta sieć używa wspólną wiedzą społeczności zabezpieczeń obsługującego usługi online firmy Microsoft, partnerzy firmy Microsoft i relacje w Internecie zabezpieczeń. 

Funkcję dostawcy infrastruktury krytyczne Microsoft odbiera wczesnego ostrzegania o zagrożeniach. Microsoft zbiera analizy zagrożeń z jego usług online i jej klientów globalnych. Microsoft uwzględniono wszystkie tego analizy zagrożeń do produktów ochrona przed atakami DDoS Azure.

Strategie obraźliwe przed zakłócanych wykonuje także, jednostki ds. przestępstw cyfrowych (DCU) firmy Microsoft. Zakłócanych są wspólne źródło polecenia i kontroli do ataków DDoS.

### <a name="risk-evaluation-of-your-azure-resources"></a>Ocena ryzyka zasobów platformy Azure

należy bezwzględnie zrozumieć zakres ryzyko przed atakami DDoS, w sposób ciągły. Okresowo zadać sobie: 
- Jakie nowe publicznie dostępnych zasobów Azure wymagają ochrony?

- Jest pojedynczym punktem awarii w usłudze? 

- Jak można odizolowaną w celu ograniczenia wpływu ataku podczas nadal udostępnianie usług klientom prawidłowe usług?

- Czy istnieją sieci wirtualnych, gdy standardowe ochrony przed atakami DDoS powinno być włączone, ale nie ma? 

- Czy Moje usług aktywny/aktywny z trybem failover w różnych regionach?

### <a name="customer-ddos-response-team"></a>Zespół klientów przed atakami DDoS

Tworzenie zespołu odpowiedzi przed atakami DDoS jest krokiem klucza w odpowiedzi na atak, szybkie i skuteczne. Zidentyfikuj kontakty w organizacji, którzy nadzoruje planowania i wykonywania. Ten zespół DDoS należy dokładnie zapoznać się z usługi Azure DDoS ochrony standardowa. Upewnij się, że zespół może identyfikowanie i usuwanie atak kontaktując się z klientów wewnętrznych i zewnętrznych, w tym zespół pomocy technicznej firmy Microsoft.

Dla swojego zespołu odpowiedzi przed atakami DDoS zalecane jest użycie symulacji ćwiczeń w ramach normalnych dostępności usługi i ciągłości planowania. Ćwiczenia te powinny obejmować testowania skali.

### <a name="alerts-during-an-attack"></a>Alerty w podczas atak

Standardowa ochrony przed atakami DDoS Azure identyfikuje i zmniejsza zagrożenie atakami DDoS bez interwencji użytkownika. Aby uzyskać powiadomienie, gdy istnieje aktywne środki zaradcze chronionych publicznego adresu IP, możesz [skonfigurować alert](../virtual-network/ddos-protection-manage-portal.md) na Metryka **DDoS w ramach ataku lub nie**. Możesz tworzyć alerty dla innych metryk DDoS zrozumienie skali ataku, ruch utracona i inne szczegóły.

#### <a name="when-to-contact-microsoft-support"></a>Kiedy skontaktuj się z pomocą techniczną firmy Microsoft

- Podczas takiego ataku możesz znaleźć, czy jest znacznie obniżeniem wydajności chronionego zasobu lub zasób nie jest dostępny.

- Uważasz, że usługa Ochrona przed atakami DDoS nie zachowuje się zgodnie z oczekiwaniami. 

  Ochrona przed atakami DDoS usługa jest uruchamiana ograniczenia tylko wtedy, gdy wartość metryki **zasady do wyzwalania ograniczenie DDoS (SYN TCP/TCP/UDP)** jest niższa niż ruchu odbieranego przez zasobu chronionego publicznego adresu IP.

- Planowane jest wirusa zdarzenie, które znacznie wzrośnie ruchu sieciowego.

- Aktor jest zagrożone do uruchomienia przed atakami DDoS atak zasobów.

Ataków, które mają wpływ krytycznym znaczeniu dla firmy, Utwórz ważność A [obsługuje biletu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>Czynności po ataku.

Zawsze jest dobre rozwiązanie typu postmortem po przeprowadzeniu ataku i dostosować DDoS strategii odpowiedzi zgodnie z potrzebami. Kwestie do rozważenia:

- Został istnieje żadnych przerw w działaniu usługi lub użytkownikowi wystąpić z powodu braku Skalowalna architektura?

- Które aplikacji lub usługi w trakcie najbardziej?

- Jak skuteczne było strategii odpowiedzi przed atakami DDoS i jak można je ulepszyć?

Jeśli podejrzewasz, że jesteś w obszarze takiego ataku, przekazać normalne kanałami pomocą techniczną platformy Azure.

## <a name="ddos-protection-reference-architectures"></a>Ochrona przed atakami DDoS odwołanie architektury

Standardowa ochrony przed atakami DDoS zaprojektowano [dla usług, które zostały wdrożone w sieci wirtualnej](../virtual-network/virtual-network-for-azure-services.md). W przypadku innych usług ma zastosowanie domyślna usługa ochrony przed atakami DDoS — podstawowe. Następujące architektury odwołania ułożone w scenariuszach, wzorami architektura grupowane.

### <a name="virtual-machine-windowslinux-workloads"></a>Obciążenia maszyny wirtualnej (z systemem Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplikacja uruchomiona na maszynach wirtualnych z równoważeniem obciążenia

Informacje o architekturze wzorcowej zawiera zestaw sprawdzonych rozwiązań dotyczących wielu maszyn wirtualnych systemu Windows w skali ustawiona za modułem równoważenia obciążenia w celu zwiększenia dostępności i skalowalności. Taka architektura może służyć do dowolnego bezstanowego obciążenia, takich jak serwer sieci web.

![Diagram architektury odwołania dla aplikacji działających na maszynach wirtualnych z równoważeniem obciążenia](media/azure-ddos-best-practices/image9.png)

W tej architekturze obciążenie jest rozkładane na wiele wystąpień maszyny wirtualnej. Istnieje jeden publiczny adres IP, a ruch internetowy jest dystrybuowana do maszyn wirtualnych za pośrednictwem usługi równoważenia obciążenia. Ochrona przed atakami DDoS — standardowe jest włączona w sieci wirtualnej usługi równoważenia obciążenia Azure (internet), który ma publiczny adres IP skojarzone z nim.

Moduł równoważenia obciążenia rozdziela przychodzące żądania Internetu do wystąpień maszyn wirtualnych. Zestawy skalowania maszyny wirtualnej umożliwia liczby maszyn wirtualnych do skalowania, lub ręcznie lub automatycznie na podstawie wstępnie zdefiniowanych reguł. Jest to ważne, jeśli zasób znajduje się w obszarze takiego ataku. Aby uzyskać więcej informacji o architekturze wzorcowej, zobacz [w tym artykule](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Aplikacja uruchomiona na warstwowe systemu Windows

Istnieje wiele sposobów implementowania architektury n-warstwowej. Na poniższym diagramie przedstawiono aplikacji Typowa trójwarstwowa sieci web. Taka architektura kompilacje w artykule [Uruchom równoważeniem obciążenia maszyn wirtualnych na skalowalność i dostępność](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Warstwy internetowa i biznesowa używają maszyn wirtualnych z równoważeniem obciążenia.

![Diagram architektury odwołania dla aplikacji działających na warstwowe systemu Windows](media/azure-ddos-best-practices/image10.png)

W ramach tej architektury standardowe ochrony przed atakami DDoS jest włączona w sieci wirtualnej. Wszystkie publiczne adresy IP w sieci wirtualnej uzyskać ochronę przed atakami DDoS warstwy 3 i 4. W przypadku ochrony warstwy 7 należy wdrożyć brama aplikacji w jednostce SKU zapory aplikacji sieci Web. Aby uzyskać więcej informacji o architekturze wzorcowej, zobacz [w tym artykule](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>PaaS aplikacji sieci web

Informacje o architekturze wzorcowej przedstawia uruchamianie aplikacji usługi Azure App Service w pojedynczym regionie. Taka architektura zawiera zestaw sprawdzonych rozwiązań dotyczących aplikacji sieci web, która używa [usłudze Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) i [bazy danych SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/).
Rezerwy region jest skonfigurowany dla trybu failover scenariuszy.

![Diagram architektury odwołania dla aplikacji sieci web PaaS](media/azure-ddos-best-practices/image11.png)

Usługi Azure Traffic Manager kieruje żądania przychodzące do brama aplikacji w jednym z regionów. Podczas wykonywania normalnych operacji kieruje żądania do bramy aplikacji na aktywnym regionem. Jeśli region jest niedostępny, nie Menedżera ruchu za pośrednictwem brama aplikacji w regionie wstrzymania.

Cały ruch z Internetu, przeznaczonych do aplikacji sieci web jest kierowany do [publiczny adres IP bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) za pośrednictwem Menedżera ruchu. W tym scenariuszu usługi app service (aplikacja sieci web) sam nie jest bezpośrednio zewnętrznie ukierunkowane i jest chroniony przez bramę aplikacji. 

Zaleca się skonfigurowanie zapory aplikacji sieci Web SKU bramy aplikacji (Zapobiegaj tryb) w celu ochrony przed atakami warstwy 7 (WebSocket-HTTP/HTTPS). Ponadto aplikacje sieci web są skonfigurowane do [akceptować tylko ruch z bramy aplikacji](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) adresu IP.

Aby uzyskać więcej informacji o architekturze wzorcowej, zobacz [w tym artykule](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Środki zaradcze dla usługi PaaS w sieci web

#### <a name="hdinsight-on-azure"></a>HDInsight w systemie Azure

Informacje o architekturze wzorcowej pokazuje Konfigurowanie DDoS ochrony Standard [klaster Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/). Upewnij się, że klaster usługi HDInsight jest połączony z siecią wirtualną i ochrona przed atakami DDoS są włączone w sieci wirtualnej.

!["HDInsight" i "Zaawansowane ustawienia" okienka, przy użyciu ustawień sieci wirtualnej](media/azure-ddos-best-practices/image12.png)

![Wybór potrzeby włączania ochrony przed atakami DDoS](media/azure-ddos-best-practices/image13.png)

W ramach tej architektury ruch kierowany do klastra usługi HDInsight z Internetu jest kierowany do publicznego adresu IP, skojarzone z modułem równoważenia obciążenia bramy usługi HDInsight. Moduł równoważenia obciążenia bramy przesyła ruchu do węzłów głównych lub węzłów procesu roboczego bezpośrednio. Standardowa ochrony przed atakami DDoS jest włączony w sieci wirtualnej w usłudze HDInsight, wszystkich publicznych adresów IP w sieci wirtualnej uzyskać ochronę przed atakami DDoS warstwy 3 i 4. Informacje o architekturze wzorcowej można łączyć z N-warstwowa i odwołanie do regionu wielu architektur.

Aby uzyskać więcej informacji o architekturze wzorcowej, zobacz [rozszerzenie Azure HDInsight przy użyciu sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) dokumentacji.

### <a name="azure-api-management"></a>Usługa Azure API Management

Informacje o architekturze wzorcowej pomaga w ochronie publiczny punkt końcowy z [Azure API Management](../api-management/api-management-key-concepts.md) publikowania interfejsów API dla klientów znajdujących się poza organizacją zasobów. Zarządzanie interfejsami API należy wdrożyć w zewnętrznej sieci wirtualnej, aby włączyć ochronę przed atakami DDoS.

![Diagram architektury odwołanie do interfejsu API zarządzania](media/azure-ddos-best-practices/image15.png)

Po skonfigurowaniu zewnętrzną sieć wirtualną API Management portal bramy i deweloperów są dostępne z publicznego Internetu za pośrednictwem publiczny moduł równoważenia obciążenia. W ramach tej architektury standardowe ochrony przed atakami DDoS jest włączona na zewnętrzną sieć wirtualną do interfejsu API zarządzania. Ruch jest kierowany z Internetu do publicznego adresu IP interfejsu API zarządzania, które jest chronione przed atakami sieciowymi w warstwie 3 i 4. Aby chronić przed atakami warstwy 7 HTTP/HTTPS, można skonfigurować bramę aplikacji w trybie zapory aplikacji sieci Web.

Lista dodatkowych usług, które są wdrażane w sieci wirtualnej i mogą być skonfigurowane dla ochrony przed atakami DDoS — Standard, zobacz [w tym artykule](../virtual-network/virtual-network-for-azure-services.md). Ochrona przed atakami DDoS — standardowa obsługuje tylko zasoby usługi Azure Resource Manager. 

> [!NOTE]
> Wprowadzony wdrożenia środowiska usługi aplikacji dla rozwiązania PowerApps w sieci wirtualnej z publicznym adresem IP nie jest obsługiwany natywnie. Aby uzyskać szczegółowe informacje o ochronie środowiska usługi aplikacji Zobacz w tej sekcji.

## <a name="next-steps"></a>Kolejne kroki

* [Strona produktu usługi Azure ochrona przed atakami DDoS](https://azure.microsoft.com/services/ddos-protection/)

* [Blog Azure ochrona przed atakami DDoS](http://aka.ms/ddosblog)

* [Dokumentację platformy Azure ochrona przed atakami DDoS](../virtual-network/ddos-protection-overview.md)
