---
title: Ochrona przed zagrożeniami w usłudze Azure Security Center
description: W tym temacie opisano zasoby chronione przez funkcje ochrony przed zagrożeniami usługi Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: e348180eb94c1703ceecf2f2b00ab942ba5ff0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536330"
---
# <a name="threat-protection-in-azure-security-center"></a>Ochrona przed zagrożeniami w usłudze Azure Security Center

Gdy Usługa Security Center wykryje zagrożenie w dowolnym obszarze środowiska, generuje alert. Te alerty opisują szczegóły zasobów, których dotyczy problem, sugerowane kroki korygowania, a w niektórych przypadkach opcję wyzwalania aplikacji logiki w odpowiedzi.

Ochrona przed zagrożeniami usługi Azure Security Center zapewnia kompleksową ochronę środowiska:

* **Ochrona przed zagrożeniami dla zasobów obliczeniowych platformy Azure:** maszyny z systemem Windows, komputery z systemem Linux, usługa Azure App Service i kontenery platformy Azure

* **Ochrona przed zagrożeniami dla zasobów danych platformy Azure:** baza danych SQL i magazyn danych SQL, usługa Azure Storage i usługa Azure Cosmos DB

* **Ochrona przed zagrożeniami dla warstw usług platformy Azure:** warstwa sieciowa platformy Azure, warstwa zarządzania platformy Azure (usługa Azure Resource Manager) (wersja zapoznawcza) i usługa Azure Key Vault (wersja zapoznawcza)

Niezależnie od tego, czy alert jest generowany przez centrum zabezpieczeń, czy odbierany przez program Security Center z innego produktu zabezpieczającego, można go wyeksportować. Aby wyeksportować alerty do usługi Azure Sentinel (lub siem innej firmy) lub innego narzędzia zewnętrznego, postępuj zgodnie z instrukcjami [w eksportowanie alertów do SIEM](continuous-export.md). 




## <a name="threat-protection-for-windows-machines"></a>Ochrona przed zagrożeniami dla komputerów z systemem Windows<a name="windows-machines"></a>

Usługa Azure Security Center integruje się z usługami platformy Azure w celu monitorowania i ochrony maszyn z systemem Windows. Usługa Security Center przedstawia alerty i sugestie korygowania ze wszystkich tych usług w łatwym w użyciu formacie.

* **Microsoft Defender ATP** <a name="windows-atp"></a> — Centrum zabezpieczeń rozszerza swoje platformy ochrony obciążenia chmury, integrując się z usługą Microsoft Defender Advanced Threat Protection (ATP). Razem zapewniają kompleksowe możliwości wykrywania punktów końcowych i reagowania (EDR).

    > [!IMPORTANT]
    > Czujnik Narzędzia Microsoft Defender ATP jest automatycznie włączany na serwerach systemu Windows korzystających z Centrum zabezpieczeń.

    Gdy usługa Microsoft Defender ATP wykryje zagrożenie, wyzwala alert. Alert jest wyświetlany na pulpicie nawigacyjnym centrum zabezpieczeń. Z pulpitu nawigacyjnego można przestawić się na konsolę Microsoft Defender ATP i przeprowadzić szczegółowe badanie, aby odkryć zakres ataku. Aby uzyskać więcej informacji na temat narzędzia Microsoft Defender ATP, zobacz [Wbudowane serwery usługi Microsoft Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Analiza zrzutu awaryjnego** <a name="windows-dump"></a> — po awarii oprogramowania zrzut awaryjny przechwytuje część pamięci w momencie awarii.

    Awaria mogła być spowodowana przez złośliwe oprogramowanie lub zawierać złośliwe oprogramowanie. Aby uniknąć wykrycia przez produkty zabezpieczające, różne formy złośliwego oprogramowania używają ataku bez plików, który pozwala uniknąć zapisu na dysku lub szyfrowania składników oprogramowania zapisanych na dysku. Ten typ ataku jest trudne do wykrycia przy użyciu tradycyjnych metod opartych na dysku.

    Jednak za pomocą analizy pamięci, można wykryć tego rodzaju ataku. Analizując pamięć w zrzucie awaryjnym, usługa Security Center może wykryć techniki, z wyzywające atak. Na przykład atak może próbować wykorzystać luki w oprogramowaniu, uzyskać dostęp do poufnych danych i ukradkiem utrzymać się na zainfekowanym komputerze. Usługa Security Center wykonuje tę pracę przy minimalnym wpływie na wydajność hostów.

    Aby uzyskać szczegółowe informacje na temat alertów analizy zrzutu awaryjnego, zobacz [tabelę odwołania alertów](alerts-reference.md#alerts-windows).

* **Wykrywanie ataków** <a name="windows-fileless"></a> bez plików — ataki bez plików kierowane na punkty końcowe są powszechne. Aby uniknąć wykrycia, ataki bez plików wstrzykują złośliwe ładunki do pamięci. Ładunki atakujące utrzymują się w pamięci zainfekowanych procesów i wykonują szeroki zakres złośliwych działań.

    Dzięki bezpłtowej detekcji ataków zautomatyzowane techniki kryminalistyczne w pamięci identyfikują zestawy narzędzi, techniki i zachowania bezpłtowe. To rozwiązanie okresowo skanuje komputer w czasie wykonywania i wyodrębnia szczegółowe informacje bezpośrednio z pamięci procesów o krytycznym znaczeniu dla zabezpieczeń.

    Znajduje dowody wykorzystania, iniekcji kodu i wykonywania szkodliwych ładunków. Wykrywanie ataków bez plików generuje szczegółowe alerty zabezpieczeń, aby przyspieszyć klasyfikowanie alertów, korelację i czas odpowiedzi podrzędnej. Takie podejście uzupełnia oparte na zdarzeniach rozwiązania EDR, zapewniając większy zasięg wykrywania.

    Szczegółowe informacje na temat alertów wykrywania ataków bez plików można znaleźć w [tabeli Alerty odwołania](alerts-reference.md#alerts-windows).

> [!TIP]
> Alerty systemu Windows można symulować, pobierając [podręcznik usługi Azure Security Center Playbook: Alerty zabezpieczeń](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).






## <a name="threat-protection-for-linux-machines"></a>Ochrona przed zagrożeniami dla maszyn z systemem Linux<a name="linux-machines"></a>

Usługa Security Center zbiera rekordy inspekcji z komputerów z systemem Linux przy użyciu **inspekcji**, jednej z najpopularniejszych struktur inspekcji systemu Linux. w jądrze linii głównej. 

* **Alerty poddane inspekcji w systemie Linux i integracja** <a name="linux-auditd"></a> z programem Microsoft Monitoring Agent (MMA) — skontrolowany system składa się z podsystemu na poziomie jądra, który jest odpowiedzialny za monitorowanie wywołań systemowych. Filtruje je według określonego zestawu reguł i zapisuje dla nich wiadomości do gniazda. Usługa Security Center integruje funkcje z pakietu inspekcji w programie Microsoft Monitoring Agent (MMA). Ta integracja umożliwia zbieranie zdarzeń inspekcji we wszystkich obsługiwanych dystrybucjach systemu Linux, bez żadnych wymagań wstępnych.  

    skontrolowane rekordy są zbierane, wzbogacane i agregowane w zdarzenia przy użyciu agenta LINUX MMA. Usługa Security Center stale dodaje nowe analizy, które używają sygnałów systemu Linux do wykrywania złośliwych zachowań na komputerach z systemem Linux w chmurze i lokalnie. Podobnie jak w przypadku systemu Windows, te analizy obejmują podejrzane procesy, wątpliwe próby logowania, ładowanie modułu jądra i inne działania. Te działania mogą wskazywać, że maszyna jest atakowana lub została naruszona.  

    Aby uzyskać listę alertów systemu Linux, zobacz [tabelę odwołania alertów](alerts-reference.md#alerts-linux).

> [!TIP]
> Alerty systemu Linux można symulować, pobierając [podręcznik usługi Azure Security Center Playbook: Linux Detections](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## <a name="threat-protection-for-azure-app-service"></a>Ochrona przed zagrożeniami dla usługi Azure App Service<a name="app-services"></a>

> [!NOTE]
> Ta usługa nie jest obecnie dostępna w obszarze administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji 100-100 platformy Azure i

Usługa Security Center używa skali chmury do identyfikowania ataków kierowanych na aplikacje działające za pomocą usługi App Service. Osoby atakujące sondują aplikacje internetowe, aby znaleźć i wykorzystać słabe strony. Przed kierowaniem do określonych środowisk żądania do aplikacji uruchomionych na platformie Azure przechodzą przez kilka bram, gdzie są kontrolowane i rejestrowane. Te dane są następnie używane do identyfikowania exploitów i atakujących oraz do uczenia się nowych wzorców, które zostaną użyte później.

Korzystając z widoczności platformy Azure jako dostawcy chmury, Usługa Security Center analizuje wewnętrzne dzienniki usługi App Service w celu zidentyfikowania metodologii ataku na wiele obiektów docelowych. Na przykład metodologia obejmuje powszechne skanowanie i ataki rozproszone. Ten typ ataku zazwyczaj pochodzi z małego podzbioru adresów IP i pokazuje wzorce indeksowania do podobnych punktów końcowych na wielu hostach. Ataki szukają strony lub wtyczki, na które nie ma zagrożenia i nie można ich zidentyfikować z punktu widzenia pojedynczego hosta.

Jeśli korzystasz z planu usługi app service opartej na systemie Windows, Usługa Security Center ma również dostęp do podstawowych piaskowców i maszyn wirtualnych. Wraz z danymi dziennika, o których mowa powyżej, infrastruktura może opowiedzieć historię, od nowego ataku krążącego w środowisku naturalnym do kompromisów w maszynach klientów. W związku z tym nawet jeśli usługa Security Center jest wdrażana po wykorzystaniu aplikacji sieci web, może być w stanie wykryć trwające ataki.

Aby uzyskać listę alertów usługi Azure App Service, zobacz [tabelę odwołań alertów](alerts-reference.md#alerts-azureappserv).

Aby uzyskać więcej informacji na temat planów usługi app service, zobacz [Plany usługi app service](https://azure.microsoft.com/pricing/details/app-service/plans/).





## <a name="threat-protection-for-azure-containers"></a>Ochrona przed zagrożeniami dla kontenerów platformy Azure<a name="azure-containers"></a>

> [!NOTE]
> Ta usługa nie jest obecnie dostępna w obszarze administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji 100-100 platformy Azure i

Usługa Security Center zapewnia ochronę przed zagrożeniami w czasie rzeczywistym dla środowisk konteneryzowanych i generuje alerty o podejrzanych działaniach. Te informacje pozwalają na szybkie rozwiązywanie problemów dotyczących zabezpieczeń i poprawę bezpieczeństwa kontenerów.

Usługa Security Center zapewnia ochronę przed zagrożeniami na różnych poziomach: 

* **Poziom hosta** — agent centrum zabezpieczeń (dostępny w warstwie Standardowa, zobacz [cennik, aby](security-center-pricing.md) uzyskać szczegółowe informacje) monitoruje system Linux pod kątem podejrzanych działań. Agent wyzwala alerty dla podejrzanych działań pochodzących z węzła lub kontenera uruchomionego na nim. Przykładami takich działań są wykrywanie powłoki internetowej i połączenie ze znanymi podejrzanymi adresami IP.

    Aby uzyskać lepszy wgląd w bezpieczeństwo środowiska konteneryzowanego, agent monitoruje analizy specyficzne dla kontenera. Spowoduje to wyzwolenie alertów dla zdarzeń, takich jak tworzenie kontenerów uprzywilejowanych, podejrzany dostęp do serwerów interfejsu API i serwery Secure Shell (SSH) działające wewnątrz kontenera platformy Docker.

    >[!IMPORTANT]
    > Jeśli zdecydujesz się nie instalować agentów na hostach, otrzymasz tylko podzbiór korzyści z ochrony przed zagrożeniami i alertów zabezpieczeń. Nadal będziesz otrzymywać alerty związane z analizą sieci i komunikacją ze złośliwymi serwerami.

    Aby uzyskać listę alertów poziomu hosta, zobacz [tabelę odwołania alertów](alerts-reference.md#alerts-containerhost).


* Na **poziomie klastra AKS**ochrona przed zagrożeniami jest oparta na analizie dzienników inspekcji firmy Kubernetes. Aby włączyć monitorowanie **bez agenta,** dodaj opcję Kubernetes do subskrypcji na stronie **Ustawienia & cennika** (patrz [cennik).](security-center-pricing.md) Aby wygenerować alerty na tym poziomie, Usługa Security Center monitoruje usługi zarządzane przez AKS przy użyciu dzienników pobranych przez usługi AKS. Przykłady zdarzeń na tym poziomie obejmują narażonych pulpitów nawigacyjnych kubernetes, tworzenie ról o wysokich uprawnieniach i tworzenie poufnych instalacji.

    >[!NOTE]
    > Usługa Security Center generuje alerty zabezpieczeń dla akcji i wdrożeń usługi Azure Kubernetes występujących po włączeniu opcji Kubernetes w ustawieniach subskrypcji. 

    Aby uzyskać listę alertów na poziomie klastra AKS, zobacz [tabelę odwołania alertów](alerts-reference.md#alerts-akscluster).

Ponadto nasz globalny zespół badaczy bezpieczeństwa stale monitoruje krajobraz zagrożeń. Dodają alerty specyficzne dla kontenera i luki w zabezpieczeniach po ich wykryciu.

> [!TIP]
> Alerty kontenerów można symulować, postępując zgodnie z instrukcjami zawartymi w [tym wpisie](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)w blogu .








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Ochrona przed zagrożeniami dla bazy danych SQL i magazynu danych SQL<a name="data-sql"></a>

Zaawansowana ochrona przed zagrożeniami dla usługi Azure SQL Database wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Alerty będą widoczne, gdy istnieją podejrzane działania bazy danych, potencjalne luki w zabezpieczeniach lub ataki iniekcji SQL oraz nietypowe wzorce dostępu do bazy danych i zapytań.

Zaawansowana ochrona przed zagrożeniami dla bazy danych SQL i SQL platformy Azure jest częścią ujednoliconego pakietu [zaawansowanego bezpieczeństwa danych (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) dla zaawansowanych funkcji zabezpieczeń SQL, obejmującego bazy danych SQL Azure, wystąpienia zarządzanej usługi Azure SQL Database, bazy danych usługi Azure SQL Data Warehouse i serwery SQL na maszynach wirtualnych platformy Azure.

Aby uzyskać więcej informacji, zobacz:

* [Jak włączyć zaawansowaną ochronę przed zagrożeniami dla bazy danych SQL usługi Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Jak włączyć zaawansowaną ochronę przed zagrożeniami dla serwerów SQL na maszynach wirtualnych platformy Azure](security-center-iaas-advanced-data.md)
* [Lista alertów ochrony przed zagrożeniami dla bazy danych SQL i magazynu danych SQL](alerts-reference.md#alerts-sql-db-and-warehouse)




## <a name="threat-protection-for-azure-storage"></a>Ochrona przed zagrożeniami dla usługi Azure Storage<a name="azure-storage"></a>

> [!NOTE]
> Ta usługa jest dostępna w chmurach instytucji rządowych w STANACH Zjednoczonych, ale nie ma innych suwerennych lub chmur rządowych platformy Azure.

Zaawansowana ochrona przed zagrożeniami dla magazynu (obecnie dostępna tylko dla magazynu obiektów Blob) wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub ich wykorzystania. Ta warstwa ochrony umożliwia rozwiązywanie zagrożeń bez konieczności bycia ekspertem w dziedzinie zabezpieczeń i pomaga zarządzać systemami monitorowania zabezpieczeń.

Aby uzyskać więcej informacji, zobacz:

* [Jak włączyć zaawansowaną ochronę przed zagrożeniami dla usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Lista alertów ochrony przed zagrożeniami dla usługi Azure Storage](alerts-reference.md#alerts-azurestorage)


> [!TIP]
> Alerty usługi Azure Storage można symulować, postępując zgodnie z instrukcjami zawartymi w [tym wpisie](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)w blogu.




## <a name="threat-protection-for-azure-cosmos-db"></a>Ochrona przed zagrożeniami dla usługi Azure Cosmos DB<a name="cosmos-db"></a>

Alerty usługi Azure Cosmos DB są generowane przez nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont usługi Azure Cosmos DB lub ich wykorzystania.

Aby uzyskać więcej informacji, zobacz:

* [Zaawansowana ochrona przed zagrożeniami dla usługi Azure Cosmos DB (wersja zapoznawcza)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Lista alertów ochrony przed zagrożeniami dla usługi Azure Cosmos DB (Wersja zapoznawcza)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Ochrona przed zagrożeniami dla warstwy sieciowej platformy Azure<a name="network-layer"></a>

Analizy warstw sieciowych usługi Security Center są oparte na przykładowych [danych IPFIX,](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)które są nagłówkami pakietów zebranymi przez routery podstawowe platformy Azure. Na podstawie tego zestawienia danych usługa Security Center używa modeli uczenia maszynowego do identyfikowania i oznaczania złośliwych działań związanych z ruchem drogowym. Usługa Security Center używa również bazy danych analizy zagrożeń firmy Microsoft do wzbogacania adresów IP.

Niektóre konfiguracje sieci mogą ograniczać program Security Center do generowania alertów o podejrzanej aktywności sieciowej. Aby usługa Security Center wygenerowała alerty sieciowe, upewnij się, że:

- Maszyna wirtualna ma publiczny adres IP (lub znajduje się na modułu równoważenia obciążenia z publicznym adresem IP).

- Ruch wychodzący na komputerze wirtualnym nie jest blokowany przez rozwiązanie zewnętrzne IDS.

- Maszynie wirtualnej przypisano ten sam adres IP przez całą godzinę, podczas której wystąpiła podejrzana komunikacja. Dotyczy to również maszyn wirtualnych utworzonych jako część usługi zarządzanej (na przykład AKS, Databricks).

Aby uzyskać listę alertów warstwy sieciowej platformy Azure, zobacz [tabelę odwołania alertów](alerts-reference.md#alerts-azurenetlayer).

Aby uzyskać szczegółowe informacje o tym, jak usługa Security Center może używać sygnałów związanych z siecią do stosowania ochrony przed zagrożeniami, zobacz [Heurystyczne wykrywanie DNS w Centrum zabezpieczeń](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Ochrona przed zagrożeniami dla warstwy zarządzania platformy Azure (Usługa Azure Resource Manager) (wersja zapoznawcza)<a name ="management-layer"></a>

Warstwa ochrony usługi Security Center oparta na usłudze Azure Resource Manager jest obecnie w wersji zapoznawczej.

Usługa Security Center oferuje dodatkową warstwę ochrony przy użyciu zdarzeń usługi Azure Resource Manager, która jest uważana za płaszczyznę sterowania dla platformy Azure. Analizując rekordy usługi Azure Resource Manager, Usługa Security Center wykrywa nietypowe lub potencjalnie szkodliwe operacje w środowisku subskrypcji platformy Azure.

Aby uzyskać listę alertów usługi Azure Resource Manager (Preview), zobacz [tabelę odwołań alertów](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Kilka z poprzednich analiz są obsługiwane przez microsoft cloud app security. Aby skorzystać z tych analiz, należy aktywować licencję cloud app security. Jeśli masz licencję cloud app security, te alerty są domyślnie włączone. Aby wyłączyć alerty:
>
> 1. W **bloku Centrum zabezpieczeń** wybierz pozycję **Zasady zabezpieczeń**. Aby uzyskać subskrypcję, którą chcesz zmienić, wybierz pozycję **Edytuj ustawienia**.
> 2. Wybierz **opcję Wykrywanie zagrożeń**.
> 3. W obszarze **Włącz integracje wyczyść**pozycję Zezwalaj na **dostęp do moich danych w usłudze Microsoft Cloud App Security,** a następnie wybierz pozycję **Zapisz**.

>[!NOTE]
>Usługa Security Center przechowuje dane klientów związanych z zabezpieczeniami w tym samym pliku geograficznym co jego zasób. Jeśli firma Microsoft nie wdrożyła jeszcze centrum zabezpieczeń w pliku geograficznym zasobu, przechowuje dane w Stanach Zjednoczonych. Gdy usługa Cloud App Security jest włączona, te informacje są przechowywane zgodnie z regułami lokalizacji geograficznych usługi Cloud App Security. Aby uzyskać więcej informacji, zobacz [Przechowywanie danych dla usług nieregionalnych](https://azuredatacentermap.azurewebsites.net/).








## <a name="threat-protection-for-azure-key-vault-preview"></a>Ochrona przed zagrożeniami dla usługi Azure Key Vault (wersja zapoznawcza)<a name="azure-keyvault"></a>

> [!NOTE]
> Ta usługa nie jest obecnie dostępna w obszarze administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji administracji 100-100 platformy Azure i

Usługa Azure Key Vault to usługa w chmurze, która chroni klucze szyfrowania i wpisy tajne, takie jak certyfikaty, parametry połączenia i hasła. 

Usługa Azure Security Center zawiera natywną, zaawansowaną ochronę przed zagrożeniami dla usługi Azure Key Vault, zapewniając dodatkową warstwę analizy zabezpieczeń. Usługa Security Center wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont usługi Key Vault lub ich wykorzystania. Ta warstwa ochrony umożliwia rozwiązywanie zagrożeń bez bycia ekspertem w dziedzinie zabezpieczeń i bez konieczności zarządzania systemami monitorowania zabezpieczeń innych firm.  

Gdy występują nietypowe działania, Usługa Security Center wyświetla alerty i opcjonalnie wysyła je pocztą e-mail do administratorów subskrypcji. Alerty te zawierają szczegółowe informacje o podejrzanej aktywności i zalecenia dotyczące sposobu badania i korygować zagrożenia. 

Aby uzyskać listę alertów usługi Azure Key Vault, zobacz [tabelę odwołań alertów](alerts-reference.md#alerts-azurekv).





## <a name="threat-protection-for-other-microsoft-services"></a>Ochrona przed zagrożeniami dla innych usług firmy Microsoft<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Ochrona przed zagrożeniami dla platformy Azure WAF<a name="azure-waf"></a>

Usługa Azure Application Gateway oferuje zaporę aplikacji internetowej (WAF), która zapewnia scentralizowaną ochronę aplikacji internetowych przed typowymi programami wykorzystującymi luki i lukami w zabezpieczeniach.

Aplikacje sieci Web są coraz częściej celem złośliwych ataków, które wykorzystują powszechnie znane luki w zabezpieczeniach. Brama aplikacji WAF jest oparta na core rule set 3.0 lub 2.2.9 z projektu open web application security project. WAF jest aktualizowany automatycznie w celu ochrony przed nowymi lukami w zabezpieczeniach. 

Jeśli masz licencję na usługę Azure WAF, alerty WAF są przesyłane strumieniowo do usługi Security Center bez konieczności dodatkowej konfiguracji. Aby uzyskać więcej informacji na temat alertów generowanych przez WAF, zobacz [Grupy reguł i reguły crs zapory aplikacji sieci Web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Ochrona przed zagrożeniami dla usługi Azure DDoS Protection<a name="azure-ddos"></a>

Rozproszone ataki typu "odmowa usługi" (DDoS) są znane jako łatwe do wykonania. Stały się one dużym problemem bezpieczeństwa, szczególnie w przypadku przenoszenia aplikacji do chmury. 

Atak DDoS próbuje wyczerpać zasoby aplikacji, dzięki czemu aplikacja jest niedostępna dla legalnych użytkowników. Ataki DDoS mogą być kierowane na dowolny punkt końcowy, do którego można uzyskać dostęp za pośrednictwem Internetu.

Aby chronić się przed atakami DDoS, kup licencję dla usługi Azure DDoS Protection i upewnij się, że przestrzegasz najlepszych rozwiązań dotyczących projektowania aplikacji. Ochrona przed atakami DDoS udostępnia różne warstwy usług. Aby uzyskać więcej informacji, zobacz [omówienie usługi Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Aby uzyskać listę alertów ochrony przed atakami DDoS platformy Azure, zobacz [tabelę odwołań alertów](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o alertach zabezpieczeń z tych funkcji ochrony przed zagrożeniami, zobacz następujące artykuły:

* [Tabela odwołań dla wszystkich alertów usługi Azure Security Center](alerts-reference.md)
* [Alerty zabezpieczeń w usłudze Azure Security Center](security-center-alerts-overview.md)
* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Eksportowanie alertów i zaleceń dotyczących zabezpieczeń (wersja zapoznawcza)](continuous-export.md)