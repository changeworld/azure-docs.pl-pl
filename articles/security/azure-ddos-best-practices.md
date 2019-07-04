---
title: Usługa Azure DDoS Protection najlepsze rozwiązania i architektury referencyjne | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu rejestrowania danych umożliwia uzyskiwanie szczegółowych informacji o aplikacji.
services: security
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: barclayn
ms.openlocfilehash: b8d5d1b3c3f505b66e07f7aa226cfa001af94af8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449339"
---
# <a name="azure-ddos-protection-best-practices-and-reference-architectures"></a>Usługa Azure DDoS Protection: Najlepsze rozwiązania i architektury referencyjne

Ten artykuł jest przeznaczony dla decydenci IT i bezpieczeństwa, personel. Oczekuje się, że jesteś zaznajomiony z platformą Azure, sieci i zabezpieczeń.

Projektowanie pod kątem rozproszona odmowa usługi (DDoS) odporności wymaga planowania i projektowania dla różnych trybów awarii. Ten artykuł zawiera najlepsze rozwiązania dotyczące projektowania aplikacji na platformie Azure pod kątem odporności przed atakami DDoS.

## <a name="types-of-attacks"></a>Rodzaje ataków

Przed atakami DDoS jest typem ataku, który próbuje wyczerpaniu zasobów aplikacji. Celem jest wpływ na dostępność aplikacji i jego zdolność do obsługi żądań uzasadnione. Ataki stają się coraz bardziej złożone i większy rozmiar i wpływu. Celem ataku DDoS może być dowolny punkt końcowy publicznie dostępny za pośrednictwem Internetu.

System Azure oferuje ciągłą ochronę przed atakami DDoS. Ta ochrona jest zintegrowana z platformą Azure, domyślnie, a także bez dodatkowych kosztów. 

Oprócz podstawowych ochrona przed atakami DDoS na platformie [Standard platformy Azure przed atakami DDoS Protection](https://azure.microsoft.com/services/ddos-protection/) zapewnia zaawansowane możliwości zapobiegania atakom DDoS przed atakami z sieci. Jest on automatycznie dostosowany do ochrony określonych zasobów platformy Azure. Ochrona jest proste włączyć podczas tworzenia nowej sieci wirtualnej. Jego można również wykonać po utworzeniu i nie wymaga żadnych zmian w aplikacji lub zasobu.

![Rola usługi Azure DDoS Protection w zakresie ochrony klientów i sieci wirtualnej od osoby atakującej](media/azure-ddos-best-practices/image1.png)

Ataki DDoS, które można podzielić na trzy kategorie: pomiarowej, protokół i zasobów.

### <a name="volumetric-attacks"></a>Ataki pojemności

Ataki pomiarowej są najczęściej spotykanym typem ataków DDoS. Ataki pomiarowej są zamachów atak metodą siłową, przeznaczonych dla warstwy sieci i mechanizm transportu. Użytkownik próbuje wyczerpaniu zasobów, takich jak linki sieci. 

Te ataki często używają wielu systemów zainfekowanych by zalać warstwy sieci z ruchem pozornie uzasadnione. Używają protokołów warstwy sieciowej, takich jak kontrolki komunikat ICMP (Internet Protocol), protokołu UDP (User Datagram) i Transmission Control Protocol (TCP).

Ataki DDoS najczęściej używanych warstwy sieci są TCP SYN przepełnieniu echa protokołu ICMP, UDP, przepełnieniu DNS, i ataki wzmocnienia NTP. Tego rodzaju ataków można nie tylko zakłócić działanie usługi, ale także jako smokescreen dla bardziej szkodliwa, ukierunkowanymi włamania do sieci. Na przykład ostatnie ataku pomiarowej [wykorzystać Memcached](https://www.wired.com/story/github-ddos-memcached/) rozwiązała usługi GitHub. Ten rodzaj ataku na wybranym UDP port 11211 i generowane 1.35 Tb/s dla woluminu ataku.

### <a name="protocol-attacks"></a>Protokół ataków

Protokół ataki protokołów aplikacji docelowej. Użytkownik próbuje użycie wszystkie zasoby dostępne w urządzeniach infrastruktury, takich jak zapory i serwery aplikacji i moduły równoważenia obciążenia. Ataki protokołu Użyj pakietów, które są źle sformułowane lub zawierają protokołu nieprawidłowości. Działania te ataki, wysyłając dużą liczbę otwartych żądań, serwerów i innych urządzeń komunikatu odpowiedzi i oczekiwania na odpowiedź pakietu. Element docelowy próbuje odpowiadanie na żądania otwarte, może sprawić, że system ulega awarii.

Najbardziej typowym przykładem ataków DDoS oparte na protokole jest TCP SYN powódź. W tym ataków kolejnych żądań TCP SYN próbuje przeciąży obiektu docelowego. Celem jest zapewnienie element docelowy nie odpowiada. Awaria Dyn 2016, oprócz trwa atak warstwy aplikacji, obejmowało TCP SYN floods tego portu docelowego 53 serwerów DNS firmy Dyn.

### <a name="resource-attacks"></a>Ataki zasobów

Ataki zasobów docelowych warstwy aplikacji. Procesów zaplecza mogą wyzwalać w ramach działań zmierzających do przeciąży systemu. Zasób ataki ruchu nadużyć, który wygląda normalne, jednak, że wykonuje mocy procesora CPU wysyła zapytanie do serwera. Wielkość ruchu sieciowego potrzebne na wyczerpanie zasobów jest niższa niż w przypadku innych typów ataków. Ruch przychodzący na atak zasobu nie można odróżnić od wiarygodnego ruchu, dzięki czemu jest trudny do wykrycia. Najbardziej typowymi atakami zasobów znajdują się na usług DNS i protokołu HTTP/HTTPS.

## <a name="shared-responsibility-in-the-cloud"></a>Wspólna odpowiedzialność w chmurze

Ochronę w głębi rekompilacji zwalczania różnych rosnące i coraz bardziej wyszukanymi ataków. Zabezpieczenia są wspólnej odpowiedzialności klienta i firmy Microsoft. Microsoft wywołuje to [modelu odpowiedzialności](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). Na poniższej ilustracji przedstawiono podział odpowiedzialności:

![Obowiązki klienta i platformy Azure](media/azure-ddos-best-practices/image2.png)

Klienci platformy Azure mogli korzystać z przeglądanie najlepszych rozwiązań firmy Microsoft i tworzenie globalnie dystrybuowanych aplikacji, które są zaprojektowane i sprawdzane pod kątem błędów.

## <a name="fundamental-best-practices"></a>Podstawowe najlepsze rozwiązania

W poniższych sekcjach znajdują się wskazówki, aby tworzyć odporne na błędy przed atakami DDoS usługi na platformie Azure.

### <a name="design-for-security"></a>Projektowanie z uwzględnieniem zabezpieczeń

Upewnij się, że bezpieczeństwo to priorytet w całym cyklu życia aplikacji, od projektowania i implementacji, wdrażania i operacji. Aplikacje mogą mieć usterki, które umożliwiają stosunkowo małą liczbą żądań, aby używać dużej ilości zasobów, co powoduje przerwa w działaniu usługi. 

Aby zabezpieczyć usługi uruchomionej na Microsoft Azure, należy dysponować dobrą znajomością architektury aplikacji i skoncentrować się na [pięciu filarów jakości oprogramowania](https://docs.microsoft.com/azure/architecture/guide/pillars).
Typowe ruchu woluminów, należy wiedzieć modelu łączności między aplikacją i innymi aplikacjami i punktów końcowych usługi, które są dostępne do publicznego Internetu.

Zapewnia, że aplikacja jest odporność obsługi typu "odmowa usługi, która jest przeznaczona dla aplikacji" jest dla Ciebie najważniejsza. Bezpieczeństwo i ochrona prywatności są wbudowane w platformy Azure, począwszy od [cykl projektowania zabezpieczeń (SDL)](https://www.microsoft.com/sdl/default.aspx). Proces SDL, zapewnia bezpieczeństwo na każdym etapie programowania i gwarantuje, że Azure jest stale aktualizowana się jeszcze bardziej bezpieczne.

### <a name="design-for-scalability"></a>Projektowanie pod kątem skalowalności

Skalowalność to, jak system może obsłużyć zwiększonego obciążenia. Należy zaprojektować aplikacje do [skalowanie w poziomie](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) do spełnienia określonych wymagań obciążenia namnożonego, szczególnie w przypadku ataków DDoS. Jeśli aplikacja jest zależna od pojedynczego wystąpienia usługi, tworzy pojedynczy punkt awarii. Inicjowanie obsługi administracyjnej wielu wystąpień sprawia, że system bardziej skalowalny i bardziej odporne na błędy.

Aby uzyskać [usługi Azure App Service](../app-service/app-service-value-prop-what-is.md), wybierz pozycję [planu usługi App Service](../app-service/overview-hosting-plans.md) oferująca wiele wystąpień. W przypadku usług Azure Cloud Services skonfigurować każdej z ról w taki sposób, aby użyć [wiele wystąpień](../cloud-services/cloud-services-choose-me.md). Aby uzyskać [maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), upewnij się, że architektury maszynę wirtualną (VM) zawiera więcej niż jednej maszyny Wirtualnej i że każda maszyna wirtualna znajduje się w [zestaw dostępności](../virtual-machines/virtual-machines-windows-manage-availability.md). Firma Microsoft zaleca używanie [zestawy skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) funkcji skalowania automatycznego.

### <a name="defense-in-depth"></a>Ochrona w głębi systemu

Idei ochrona w głębi systemu jest zarządzanie ryzykiem przy użyciu różnych strategii obrony. Układanie warstwowo poziom ochrony w aplikacji zmniejsza prawdopodobieństwo udanego ataku. Firma Microsoft zaleca zaimplementowanie bezpiecznego projekty dla aplikacji przy użyciu wbudowanych możliwości platformy Azure.

Na przykład, zwiększa ryzyko ataku z rozmiarem (*obszar powierzchni*) aplikacji. Można zmniejszyć obszar powierzchni przy użyciu listy dozwolonych zamknięcia w narażonych przestrzeń adresów IP i nasłuchiwać portów, które nie są wymagane na usług równoważenia obciążenia ([usługi Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) i [usługi Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)). [Sieciowe grupy zabezpieczeń (NSG)](../virtual-network/security-overview.md) to inny sposób, aby zmniejszyć obszar narażony na ataki.
Możesz użyć [tagów usług](../virtual-network/security-overview.md#service-tags) i [grupy zabezpieczeń aplikacji](../virtual-network/security-overview.md#application-security-groups) aby zminimalizować złożoność tworzenia reguły zabezpieczeń i konfigurowania zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji.

Należy wdrożyć usług platformy Azure w [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) zawsze, gdy jest to możliwe. To rozwiązanie umożliwia zasobów usługi do komunikacji przy użyciu prywatnych adresów IP. Domyślnie ruch usługi platformy Azure z sieci wirtualnej używa publicznych adresów IP jako źródłowych adresów IP. Za pomocą [punkty końcowe usługi](../virtual-network/virtual-network-service-endpoints-overview.md) spowoduje przełączenie ruchu w ramach usługi na używanie prywatnych adresów sieci wirtualnej jako źródłowych adresów IP w przypadku ich uzyskujesz dostęp do usługi platformy Azure z sieci wirtualnej.

Widzimy często klientów lokalnych zasobów wprowadzenie zaatakowane wraz z ich zasobów na platformie Azure. Jeśli łączysz się w środowisku lokalnym, na platformie Azure, firma Microsoft zaleca, można zminimalizować narażenie zasobów lokalnych do publicznej sieci internet. Umożliwia skalowanie i zaawansowane możliwości ochrony przed atakami DDoS platformy Azure, wdrażając publiczny dobrze znanych jednostek na platformie Azure. Ponieważ te publicznie jednostki są często cel ataków DDoS, umieszczając je na platformie Azure zmniejsza wpływ na zasoby w środowisku lokalnym.

## <a name="azure-offerings-for-ddos-protection"></a>Oferty platformy Azure do ochrony przed atakami DDoS

Platforma Azure ma dwie oferty usług DDoS, które zapewniają ochronę przed atakami sieciowymi (warstwa 3 i 4): Przed atakami DDoS Protection w warstwach podstawowa i standardowa ochrony przed atakami DDoS. 

### <a name="ddos-protection-basic"></a>Podstawowa ochrona przed atakami DDoS

Podstawowa ochrona jest integrowany Azure domyślnie bez ponoszenia dodatkowych kosztów. Skalowanie i pojemność wdrożonej globalnie sieci platformy Azure zapewnia obrony przed typowymi atakami warstwy sieci poprzez ograniczenie monitorowania i w czasie rzeczywistym zawsze w ruchu. Podstawowa ochrona przed atakami DDoS nie wymaga żadnych zmian konfiguracji lub aplikacji użytkownika. Podstawowa ochrona przed atakami DDoS pomaga w ochronie wszystkich usług platformy Azure, w tym usług PaaS, takich jak usługi Azure DNS.

![Mapowanie reprezentacja sieci platformy Azure, tekst "globalne przed atakami DDoS środki zaradcze obecności" i "Początkowe ograniczania ryzyka ataków DDOS"](media/azure-ddos-best-practices/image3.png)

Podstawowa ochrona przed atakami DDoS na platformie Azure zawiera składniki sprzętu i oprogramowania. Płaszczyzna kontroli oprogramowania decyduje, kiedy, gdzie, i jakiego rodzaju ruch należy kierowanych przez sprzęt urządzeń, które analizowanie i usuwanie ruchu ataku. Tę decyzję oparciu o usługę DDoS Protection całej infrastruktury sprawia, że na płaszczyźnie kontroli *zasad*. Ta zasada jest statycznie zestawu i powszechnie stosowane do wszystkich klientów platformy Azure.

Na przykład zasady ochrony przed atakami DDoS Określa, w jaki natężeniem ruchu ochrony powinny być *wyzwolone.* (Oznacza to, ruch dzierżawcy powinien być kierowany w za pomocą czyszczenia danych urządzenia.) Zasady określa, jak powinny szybkiej kontroli urządzeń *eliminowanie* ataku.

Usługa Azure DDoS Protection w warstwie podstawowa jest przeznaczona dla ochrony infrastruktury i ochrony platformy Azure. Po przekroczeniu szybkość, z której jest na tyle znaczące, że mogą mieć wpływ na wielu klientów w środowisku wielodostępnym, zmniejsza ruch. Nie zapewnia alerty, lub na klienta dostosowane zasady.

### <a name="ddos-protection-standard"></a>Standard ochrony przed atakami DDoS

Standard ochrony zawiera ulepszone funkcje ograniczania ryzyka ataków DDoS. Jest on automatycznie dostosowany do ochrony określonych zasobów platformy Azure w sieci wirtualnej. Ochrona jest proste włączyć w dowolnej nowej lub istniejącej sieci wirtualnej i jej nie wymaga żadnych zmian aplikacji lub zasobu. Ma kilka zalet w stosunku podstawowe usługi, w tym rejestrowanie, alertów i danych telemetrycznych. W poniższych sekcjach opisano kluczowe funkcje usługi Azure DDoS Protection Standard.

#### <a name="adaptive-real-time-tuning"></a>Adaptacyjne dostrajanie w czasie rzeczywistym

Usługa Azure DDoS Protection w warstwie podstawowa ułatwia ochronę klientów i zapobiegają wpływa na innym klientom. Na przykład, jeśli usługa jest aprowizowana dla typowych woluminu uzasadnione przychodzącego ruchu sieciowego, który jest mniejszy niż *szybkość wyzwalacza* zasad ochrony przed atakami DDoS całej infrastruktury, może przejść ataków DDoS na zasobach tego klienta niezauważona. Bardziej ogólnie rzecz biorąc złożoność ostatnich ataków (na przykład wielu wektor DDoS) i zachowania specyficzne dla aplikacji dzierżaw wywołania dla poszczególnych klientów, zasady ochrony dostosowane. Usługi w ramach to dostosowanie za pomocą dwóch szczegółowe informacje:

- Funkcja automatycznej nauki wzorców ruchu (IP) klientów warstwy 3 i 4.

- Minimalizacja liczby wyników fałszywie dodatnich, biorąc pod uwagę skali platformy Azure umożliwia go do ochrony przed rozproszonymi znacznej ilości ruchu sieciowego.

![Diagram przedstawiający Standard ochrony przed atakami DDoS działania, za pomocą "Generowanie zasad" w kółkach](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Ochrona przed atakami DDoS telemetrii, monitorowania i alertów

Standard ochrony przed atakami DDoS udostępnia bogate dane telemetryczne za pośrednictwem [usługi Azure Monitor](../azure-monitor/overview.md) na czas trwania ataków DDoS. Można skonfigurować alerty dotyczące wszystkich metryk usługi Azure Monitor, który używa usługi DDoS Protection. Rejestrowanie można zintegrować z Splunk (usługa Azure Event Hubs), dzienniki usługi Azure Monitor i Azure Storage w celu zaawansowanej analizy za pośrednictwem interfejsu usługi Azure Monitor Diagnostics.

##### <a name="ddos-mitigation-policies"></a>Zasady ograniczania ryzyka ataków DDoS

W witrynie Azure portal wybierz **Monitor** > **metryki**. W **metryki** okienku, wybierz grupę zasobów, wybierz typ zasobu **publiczny adres IP**i wybierz pozycję Azure publicznego adresu IP. Przed atakami DDoS metryki są widoczne w **dostępne metryki** okienka.

Standard ochrony przed atakami DDoS stosuje trzy zasady ograniczania ryzyka strojoną (TCP SYN, TCP i UDP) dla każdego publiczny adres IP chronionego zasobu, w sieci wirtualnej, która została włączona przed atakami DDoS. Progi zasad można wyświetlić, wybierając metryki **liczba przychodzących pakietów do wyzwolenia ataki DDOS**.

![Dostępne metryki i wykres metryk](media/azure-ddos-best-practices/image7.png)

Progi zasad są konfigurowane automatycznie przy użyciu maszyny sieci oparte na nauce maszynowej ruchu profilowania. Ataki DDOS pojawia się dla adresu IP w ramach ataku, tylko wtedy, gdy zostanie przekroczony próg zasad.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metryki dla adresu IP w ramach ataków DDoS

Jeśli publiczny adres IP znajduje się w ataki, wartość metryki **ataki DDoS w obszarze, czy nie** zmienia się na 1, ponieważ Ochrona przed atakami DDoS wykonuje ograniczania ryzyka ataków ruchu sieciowego.

![Metryka "W obszarze przed atakami DDoS ataki lub nie" i wykres](media/azure-ddos-best-practices/image8.png)

Firma Microsoft zaleca skonfigurowanie alertu na tę metrykę. Możesz następnie wyświetlone powiadomienie, gdy jest wykonywane na swój publiczny adres IP active ograniczania ryzyka ataków DDoS.

Aby uzyskać więcej informacji, zobacz [Zarządzanie Standard platformy Azure przed atakami DDoS Protection w witrynie Azure portal](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="web-application-firewall-for-resource-attacks"></a>Zapora aplikacji sieci Web do ataków na zasób

Specyficzne dla zasobów atakami w warstwie aplikacji, należy skonfigurować zapory aplikacji sieci web (WAF), aby pomóc w zabezpieczaniu aplikacji internetowych. Zapora aplikacji sieci Web sprawdza przychodzący ruch internetowy do blokowania wstrzyknięć kodu SQL, skryptów, przed atakami DDoS i inne ataki warstwy 7 między witrynami. Platforma Azure udostępnia [zapory aplikacji sieci Web jako funkcja usługi Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md) dla scentralizowaną ochronę aplikacji sieci web z typowe luki w zabezpieczeniach i luk w zabezpieczeniach. Dostępnych ofert zapory aplikacji sieci Web od partnerów systemu Azure, które mogą być bardziej odpowiednie do potrzeb, za pośrednictwem [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Nawet zapory aplikacji sieci web są podatne na ataki wyczerpania pojemności i stanu. Zdecydowanie zalecamy włączenie usługi DDoS Protection standardowego w sieci wirtualnej zapory aplikacji sieci Web w celu ochrony pomiarowej i ataki protokołu. Aby uzyskać więcej informacji, zobacz [architektury referencyjne ochrona przed atakami DDoS](#ddos-protection-reference-architectures) sekcji.

### <a name="protection-planning"></a>Planowanie ochrony

Planowanie i przygotowanie są niezwykle istotne zrozumieć działanie systemu podczas ataków DDoS. Projektowanie planu reagowania na zdarzenia zarządzania jest częścią tego procesu.

W przypadku ochrony przed atakami DDoS — Standard, upewnij się, że jest włączone w sieci wirtualnej z punktów końcowych dostępnego z Internetu. Konfigurowanie alertów przed atakami DDoS pomaga stale poszukaj wszelkich potencjalnych ataków na infrastrukturę. 

Należy monitorować aplikacje niezależnie. Dowiedz się, normalnego zachowania aplikacji. Przygotuj, która będzie działać, jeśli aplikacja nie zachowuje się zgodnie z oczekiwaniami podczas ataków DDoS.

#### <a name="testing-through-simulations"></a>Testowanie za pomocą symulacji

Jest dobrą praktyką do przetestowania założeń o jak usług będą odpowiadać na atak, prowadząc okresowe symulacji. Podczas testowania, sprawdź, czy swoje aplikacje lub usługi w dalszym ciągu działać zgodnie z oczekiwaniami, i nie ma żadnych przerw w działaniu do środowiska użytkownika. Identyfikowanie luk z punktu widzenia technologii i procesów i dołączać je do strategii odpowiedzi przed atakami DDoS. Firma Microsoft zaleca wykonanie takich testów w przejściowe lub podczas godziny poza szczytem, aby zminimalizować wpływ na środowisko produkcyjne.

Firma Microsoft wspólnie opracowały z [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) do tworzenia interfejsu, gdzie klienci platformy Azure może generować ruchu względem włączona ochrona przed atakami DDoS publicznych punktów końcowych na potrzeby symulacji. Możesz użyć [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) symulację do:

- Sprawdź, jak usługi Azure DDoS Protection pozwala chronić zasoby platformy Azure przed atakami DDoS.

- Zoptymalizuj proces reagowania na zdarzenia w ramach ataków DDoS.

- Udokumentuj zgodności przed atakami DDoS.

- Szkolenie zespołom zabezpieczeń sieci.

Cyberbezpieczeństwa wymaga stałej innowacji w obrony. Usługa Azure Standard przed atakami DDoS protection jest stan systemu — najnowocześniejsze z efektywne rozwiązanie w celu zmniejszenia coraz bardziej złożonych ataków DDoS.

## <a name="components-of-a-ddos-response-strategy"></a>Składniki strategii odpowiedzi przed atakami DDoS

Ataków DDoS, który jest przeznaczony dla zasobów platformy Azure, zwykle wymaga minimalnej interwencji z punktu widzenia użytkownika. Mimo dołączanie przed atakami DDoS środki zaradcze w ramach strategii reagowania na zdarzenia pomaga zminimalizować wpływ na ciągłość prowadzenia działalności biznesowej.

### <a name="microsoft-threat-intelligence"></a>Analizy zagrożeń firmy Microsoft

Firma Microsoft dysponuje siecią analizy zagrożeń rozbudowane. Ta sieć używa wiedzy zbiorczej o społeczności rozszerzone zabezpieczenia, który obsługuje usługi online firmy Microsoft, partnerów firmy Microsoft i relacje w branży zabezpieczeń internet. 

Jako dostawca infrastruktury krytycznej firma Microsoft odbierze wczesne ostrzeżenia o zagrożeniach. Microsoft zbiera analizy zagrożeń, z jego usług online i jej klientów globalnych. Microsoft uwzględniono wszystkie tej analizy zagrożeń do produktów Azure DDoS Protection.

Ponadto jednostki przestępstw cyfrowych (DCU) firmy Microsoft przeprowadza obraźliwe strategie przed botnetami. Botnetami są wspólne źródło poleceń i kontroli do ataków DDoS.

### <a name="risk-evaluation-of-your-azure-resources"></a>Ocena ryzyka zasobów platformy Azure

Należy bezwzględnie, aby zrozumieć zakres ryzyko przed atakami DDoS, w sposób ciągły. Okresowo zadać sobie: 
- Jakie nowe publicznie dostępne zasoby platformy Azure wymagają ochrony?

- Czy istnieje pojedynczy punkt awarii w usłudze? 

- Jak można wyizolować ograniczyć wpływ ataku podczas nadal udostępnienie usług klientom prawidłowe usług?

- Czy istnieją sieci wirtualne, których Standard ochrony przed atakami DDoS powinno być włączone, ale nie jest? 

- Czy Moje usług aktywny/aktywny z trybem failover w wielu regionach?

### <a name="customer-ddos-response-team"></a>Zespół ds. reakcji klientów przed atakami DDoS

Tworzenie zespołu odpowiedzi przed atakami DDoS jest krokiem wykonywanym w odpowiedzi na ataki, szybko i skutecznie. Identyfikacji kontaktów w Twojej organizacji, którzy nadzoruje planowania i wykonywania. Ten zespół ds. reakcji przed atakami DDoS dokładnie zrozumieć usługę Azure DDoS Protection Standard. Upewnij się, że zespół można zidentyfikować i rozwiązać ataku, kontaktując się z wewnętrznych i zewnętrznych klientów, w tym zespołem pomocy technicznej firmy Microsoft.

Dla Twojego zespołu odpowiedzi przed atakami DDoS zaleca się użyć symulacji ćwiczeń w ramach normalnych dostępność usług i planowania ciągłości działania. Ćwiczeń powinno obejmować testy skalowania.

### <a name="alerts-during-an-attack"></a>Alerty w przypadku ataków

Standard ochrony przed atakami DDoS Azure identyfikuje i odpiera ataki DDoS, bez jakiejkolwiek interwencji użytkownika. Otrzymaj powiadomienie, gdy istnieje aktywne środki zaradcze dla chronionych publiczny adres IP, możesz [skonfigurowanie alertu powodującego](../virtual-network/ddos-protection-manage-portal.md) na metrykę **ataki DDoS w obszarze, czy nie**. Możesz utworzyć alerty dotyczące innych metryk przed atakami DDoS zrozumieć skali ataku, ruchu sieciowego pomijanego i inne szczegóły.

#### <a name="when-to-contact-microsoft-support"></a>Kiedy należy skontaktować się z pomocą techniczną firmy Microsoft

- Podczas ataku DDoS możesz znaleźć, że wydajność chronionego zasobu jest znacznie obniżonej wydajności lub zasób nie jest dostępny.

- Uważasz, że usługa DDoS Protection nie zachowuje się zgodnie z oczekiwaniami. 

  Usługa DDoS Protection rozpoczyna się środki zaradcze, tylko wtedy, gdy wartość metryki **zasady do wyzwalania ograniczania ryzyka ataków DDoS (TCP/TCP SYN/UDP)** jest niższa niż ruchu odbieranego na chronionym publicznego zasobu adresu IP.

- Planowane jest wirusowe zdarzenia, które zwiększający w znacznym stopniu ruch w sieci.

- Aktor ma zagrożone można uruchomić ataków DDoS dotyczących poszczególnych zasobów.

- Jeśli potrzebujesz do listy dozwolonych adresów IP lub zakres adresów IP z usługi Azure DDoS Protection wersji Standard. Typowy scenariusz to do listy dozwolonych adresów IP, jeśli ruch jest przekierowywany z chmury zewnętrznej zapory aplikacji sieci Web na platformie Azure. 

Ataki, które ma krytyczny wpływ na działalność, Utwórz ważność A [bilet pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>Kroki po ataku

Zawsze jest dobra strategii typu postmortem po ataku i Dostosuj strategię odpowiedzi przed atakami DDoS, zgodnie z potrzebami. Co należy wziąć pod uwagę:

- Został istnieje żadnych przerw w działaniu usługi lub użytkownika środowiska ze względu na brak Skalowalna architektura?

- Które aplikacje lub usługi poniesionych najczęściej?

- Jak efektywna został strategii odpowiedzi przed atakami DDoS i jak można je ulepszyć?

Jeśli podejrzewasz, że masz mniej niż ataków DDoS, należy przekazać przy użyciu normalnych kanałów pomocy technicznej systemu Azure.

## <a name="ddos-protection-reference-architectures"></a>Architektury referencyjne ochrona przed atakami DDoS

Standard ochrony przed atakami DDoS jest przeznaczona [dla usług, które są wdrażane w sieci wirtualnej](../virtual-network/virtual-network-for-azure-services.md). W przypadku innych usług, domyślnej usługi DDoS Protection podstawowe stosowana. Następujące architektury referencyjne są uporządkowane według scenariuszy, w ze wzorców architektury zgrupowane.

### <a name="virtual-machine-windowslinux-workloads"></a>Obciążenia maszyn wirtualnych (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplikacja działająca na maszynach wirtualnych z równoważeniem obciążenia

Ta architektura referencyjna przedstawia zestaw sprawdzonych rozwiązań dotyczących uruchamiania wielu maszyn wirtualnych Windows w zestawie skalowania za modułem równoważenia obciążenia służących do zwiększania dostępności i skalowalności. Ta architektura może służyć do dowolnego obciążenia bezstanowego, na przykład serwer sieci web.

![Diagram architektury referencyjnej dla aplikacji uruchomionej na maszynach wirtualnych z równoważeniem obciążenia](media/azure-ddos-best-practices/image9.png)

W tej architekturze obciążenie jest rozkładane na wiele wystąpień maszyny wirtualnej. Istnieje jeden publiczny adres IP, a ruch internetowy jest dystrybuowany do maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Standard ochrony przed atakami DDoS jest włączone w sieci wirtualnej z modułu równoważenia obciążenia platformy Azure (internet), która ma publiczny adres IP skojarzony z nim.

Moduł równoważenia obciążenia rozdziela przychodzące żądania internetowe do wystąpień maszyn wirtualnych. Zestawy skalowania maszyn wirtualnych umożliwiają wielu maszyn wirtualnych skalowania wewnątrz lub na zewnątrz ręcznie lub automatycznie na podstawie wstępnie zdefiniowanych reguł. Jest to ważne, jeśli zasób jest celem ataku DDoS. Aby uzyskać więcej informacji na temat tej architektury referencyjnej, zobacz [w tym artykule](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Jak działa aplikacja na Windows N-warstwowej

Istnieje wiele sposobów implementowania architektury n-warstwowej. Na poniższym diagramie przedstawiono typowy 3 warstwową aplikację internetową. Ta architektura bazuje na artykule [uruchamianie ze zrównoważonym obciążeniem maszyn wirtualnych w celu zapewnienia skalowalności i dostępności](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Warstwy internetowa i biznesowa używają maszyn wirtualnych z równoważeniem obciążenia.

![Diagram architektury referencyjnej dla aplikacji uruchomionej na Windows N-warstwowej](media/azure-ddos-best-practices/image10.png)

W ramach tej architektury Standard ochrony przed atakami DDoS jest włączone w sieci wirtualnej. Wszystkie publiczne adresy IP w sieci wirtualnej, Uzyskaj ochronę przed atakami DDoS dla warstwy 3 i 4. W warstwie 7 Zapora wdrożyć usługę Application Gateway w ramach jednostki SKU zapory aplikacji sieci Web. Aby uzyskać więcej informacji na temat tej architektury referencyjnej, zobacz [w tym artykule](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>Aplikacja internetowa PaaS

Ta architektura referencyjna pokazuje, uruchomienie aplikacji usługi Azure App Service w jednym regionie. W tej architekturze przedstawiono zestaw sprawdzonych rozwiązań dotyczących aplikacji sieci web, która używa [usługi Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) i [usługi Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
Region rezerwy jest skonfigurowany dla scenariuszy pracy awaryjnej.

![Diagram architektury referencyjnej dla aplikacji sieci web PaaS](media/azure-ddos-best-practices/image11.png)

Usługa Azure Traffic Manager kieruje żądania przychodzące do usługi Application Gateway w jeden z regionów. Podczas wykonywania zwykłych operacji kieruje żądania do usługi Application Gateway w aktywnym regionem. Jeśli ten region staje się niedostępny, Traffic Manager awaryjnie do usługi Application Gateway w regionie wstrzymania.

Cały ruch z Internetu, przeznaczony dla aplikacji sieci web jest kierowany do [publiczny adres IP bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) przez usługę Traffic Manager. W tym scenariuszu usługi app service (aplikacja sieci web) nie jest bezpośrednio zewnętrznie połączonego z i jest chroniony przez usługę Application Gateway. 

Firma Microsoft zaleca skonfigurowanie jednostka SKU zapory aplikacji sieci Web bramy aplikacji (Zapobiegaj tryb) w celu ochrony przed atakami warstwy 7 (HTTP/HTTPS/WebSocket). Ponadto aplikacje sieci web są skonfigurowane do [akceptować tylko na ruch z bramy aplikacji](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) adresu IP.

Aby uzyskać więcej informacji na temat tej architektury referencyjnej, zobacz [w tym artykule](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Środki zaradcze dla usług PaaS w sieci web

#### <a name="hdinsight-on-azure"></a>HDInsight na platformie Azure

Ta architektura referencyjna pokazuje Konfigurowanie przed atakami DDoS Protection Standard [klastra Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/). Upewnij się, że klaster HDInsight jest połączony z siecią wirtualną i włączenia usługi DDoS Protection w sieci wirtualnej.

!["HDInsight" i "Zaawansowane ustawienia" okienek, przy użyciu ustawień sieci wirtualnej](media/azure-ddos-best-practices/image12.png)

![Wybór na potrzeby włączania ochrony przed atakami DDoS](media/azure-ddos-best-practices/image13.png)

W tej architekturze ruch kierowany do klastra HDInsight z Internetu jest kierowany do publicznego adresu IP skojarzone z modułem równoważenia obciążenia bramy HDInsight. Moduł równoważenia obciążenia bramy przesyła ruch do węzłów głównych lub węzłów procesu roboczego bezpośrednio. Standard ochrony przed atakami DDoS jest włączony w sieci wirtualnej HDInsight, publiczne adresy IP w sieci wirtualnej, Uzyskaj ochrona przed atakami DDoS dla warstwy 3 i 4. W tej architekturze referencyjnej można łączyć z N-warstwowe i architektury referencyjne w wielu regionach.

Aby uzyskać więcej informacji na temat tej architektury referencyjnej, zobacz [rozszerzyć usłudze Azure HDInsight przy użyciu usługi Azure Virtual Network](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) dokumentacji.


> [!NOTE]
> Usługa Azure App Service Environment w przypadku usługi PowerApps lub interfejsu API zarządzania w sieci wirtualnej z publicznym adresem IP zarówno nie obsługiwane.

## <a name="next-steps"></a>Kolejne kroki

* [Strona produktu usługi Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)

* [Blog dotyczący platformy Azure DDoS Protection](https://aka.ms/ddosblog)

* [Dokumentacja usługi Azure DDoS Protection](../virtual-network/ddos-protection-overview.md)
