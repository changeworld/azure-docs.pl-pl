---
title: Zarządzanie Standard platformy Azure przed atakami DDoS Protection w witrynie Azure portal
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak korzystanie z telemetrii usługi Standard platformy Azure przed atakami DDoS Protection w usłudze Azure Monitor złagodzić ataku.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 53185caa6a0492702035041a893f20a78cf1ea4d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65911239"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Zarządzanie Standard platformy Azure przed atakami DDoS Protection w witrynie Azure portal

Dowiedz się, jak włączyć i wyłączyć rozproszona odmowa usługi (DDoS) ochronę i korzystanie z telemetrii w celu złagodzenia ataków DDoS, za pomocą usługi Azure DDoS Protection wersji Standard. Standard ochrony przed atakami DDoS chroni zasoby platformy Azure, takie jak maszyny wirtualne, usługi równoważenia obciążenia i bramy application Gateway, które mają platformy Azure [publiczny adres IP](virtual-network-public-ip-address.md) do niej przypisany. Aby dowiedzieć się więcej na temat standardowych ochrony przed atakami DDoS i jego możliwości, zobacz [Standard ochrony przed atakami DDoS — omówienie](ddos-protection-overview.md).

Przed wykonaniem dowolnej kroki opisane w tym samouczku, zaloguj się do witryny Azure portal pod https://portal.azure.com przy użyciu konta przypisanego [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiedni Akcje wymienione w [uprawnienia](#permissions).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-ddos-protection-plan"></a>Tworzenie planu ochrony przed atakami DDoS

Plan ochrony przed atakami DDoS definiuje zbiór sieci wirtualnych, które mają włączone w subskrypcjach standardu ochrony przed atakami DDoS. Można skonfigurować jeden plan ochrony przed atakami DDoS w organizacji, a łącze sieci wirtualne z wieloma subskrypcjami ten sam plan. Samego planu ochrony przed atakami DDoS jest również skojarzony z subskrypcją, który wybierzesz podczas tworzenia planu. Planem usługi DDoS Protection działa w różnych regionach i subskrypcji. Przykład — możesz utworzyć plan w regionie East-US i link do subskrypcji #1 w dzierżawie. Ten sam plan, może być połączony z siecią wirtualną z innych subskrypcji w różnych regionach, w dzierżawie. Subskrypcja planu jest skojarzona z ponosi miesięczny rachunek cyklicznych dla planu, a także naliczeniem dodatkowych opłat w przypadku, gdy liczba chronionych publicznych adresów IP przekracza 100. Aby uzyskać więcej informacji na temat cen przed atakami DDoS, zobacz [cennik](https://azure.microsoft.com/pricing/details/ddos-protection/).

Tworzenie więcej niż jeden plan nie jest wymagana w przypadku większości organizacji. Plan nie można przenosić między subskrypcjami. Jeśli chcesz zmienić subskrypcję planu jest konieczne [Usuń istniejący plan](#work-with-ddos-protection-plans) i Utwórz nowe.

1. Wybierz **Utwórz zasób** w lewym górnym rogu witryny Azure portal.
2. Wyszukaj *przed atakami DDoS*. Gdy **planu ochrony przed atakami DDos** pojawi się w wynikach wyszukiwania, wybierz ją.
3. Wybierz pozycję **Utwórz**.
4. Wprowadź lub wybierz własne wartości lub wprowadź lub wybierz następujące przykładowe wartości, a następnie wybierz **Utwórz**:

    |Ustawienie        |Wartość                                              |
    |---------      |---------                                          |
    |Name (Nazwa)           | myDdosProtectionPlan                              |
    |Subskrypcja   | Wybierz subskrypcję.                         |
    |Grupa zasobów | Wybierz **Utwórz nową** i wprowadź *myResourceGroup* |
    |Lokalizacja       | Wschodnie stany USA                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Włącz przed atakami DDoS dla sieci wirtualnej

1. Wybierz **Utwórz zasób** w lewym górnym rogu witryny Azure portal.
2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Sieć wirtualna**.
3. Wprowadź lub wybierz własne wartości, wprowadź lub wybierz następujące wartości w przykładzie, Zaakceptuj pozostałe wartości domyślne, a następnie wybierz **Utwórz**:

    | Ustawienie         | Wartość                                                        |
    | ---------       | ---------                                                    |
    | Name (Nazwa)            | myVirtualNetwork                                             |
    | Subskrypcja    | Wybierz subskrypcję.                                    |
    | Grupa zasobów  | Wybierz pozycję **Użyj istniejącej** i wybierz grupę **myResourceGroup**. |
    | Lokalizacja        | Wschodnie stany USA                                                      |
    | Ochrona przed atakami DDos | Wybierz **standardowa** a następnie w obszarze **ochrona przed atakami DDoS**, wybierz opcję **myDdosProtectionPlan**. Plan, którą wybierzesz mogą znajdować się w tej samej lub innej subskrypcji niż sieć wirtualną, ale obie subskrypcje muszą być skojarzone z tą samą dzierżawą usługi Azure Active Directory.|

Nie można przenieść sieci wirtualnej do innej grupy zasobów lub subskrypcji, po włączeniu Standard przed atakami DDoS dla sieci wirtualnej. Jeśli musisz przenieść sieć wirtualną przy użyciu standardu przed atakami DDoS jest włączona, najpierw wyłącz Standard przed atakami DDoS, przenieść sieci wirtualnej, a następnie Włącz standard przed atakami DDoS. Po przeniesieniu zresetowanie wartości progowe automatycznie dopasowane zasady dla wszystkich chronionych publicznych adresów IP w sieci wirtualnej.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Włącz przed atakami DDoS dla istniejącej sieci wirtualnej

1. Tworzenie planu ochrony przed atakami DDoS, wykonując kroki opisane w [Tworzenie planu ochrony przed atakami DDoS](#create-a-ddos-protection-plan), jeśli nie masz istniejącego planu ochrony przed atakami DDoS.
2. Wybierz **Utwórz zasób** w lewym górnym rogu witryny Azure portal.
3. Wprowadź nazwę sieci wirtualnej, który chcesz włączyć przed atakami DDoS Protection w warstwie standardowa dla w **Szukaj zasobów, usług i pole docs** w górnej części portalu. Gdy nazwa sieci wirtualnej pojawi się w wynikach wyszukiwania, wybierz ją.
4. Wybierz **ochrona przed atakami DDoS**w obszarze **ustawienia**.
5. Wybierz opcję **Standardowa**. W obszarze **planu ochrony przed atakami DDoS**, wybierz istniejący plan ochrony przed atakami DDoS lub plan utworzony w kroku 1, a następnie wybierz **Zapisz**. Plan, którą wybierzesz mogą znajdować się w tej samej lub innej subskrypcji niż sieć wirtualną, ale obie subskrypcje muszą być skojarzone z tą samą dzierżawą usługi Azure Active Directory.

## <a name="disable-ddos-for-a-virtual-network"></a>Wyłącz przed atakami DDoS dla sieci wirtualnej

1. Wprowadź nazwę sieci wirtualnej, aby wyłączyć standardu ochrony przed atakami DDoS dla w **Szukaj zasobów, usług i pole docs** w górnej części portalu. Gdy nazwa sieci wirtualnej pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz **ochrona przed atakami DDoS**w obszarze **ustawienia**.
3. Wybierz **podstawowe** w obszarze **planu ochrony przed atakami DDoS** , a następnie wybierz **Zapisz**.

## <a name="work-with-ddos-protection-plans"></a>Praca z planów ochrony przed atakami DDoS

1. Wybierz **wszystkich usług** na górnym lewym rogu portalu.
2. Wprowadź *przed atakami DDoS* w **filtru** pole. Gdy **planów ochrony przed atakami DDoS** są wyświetlane w wynikach, wybierz ją.
3. Wybierz plan ochrony, który chcesz wyświetlić z listy.
4. Wyświetlane są wszystkie sieci wirtualne skojarzone z planem.
5. Jeśli chcesz usunąć plan, musisz najpierw skojarzenie wszystkie sieci wirtualne, które od niego. Aby usunąć skojarzenie planu z sieci wirtualnej, zobacz [wyłączyć przed atakami DDoS dla sieci wirtualnej](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Konfigurowanie alertów dotyczących metryk ochrony przed atakami DDoS

Można wybrać dowolny z dostępnych metryk ochrony przed atakami DDoS do wysyłania alertów, gdy istnieje aktywne środki zaradcze podczas ataku przy użyciu konfiguracji alertów usługi Azure Monitor. Po spełnieniu warunków adres podany otrzymuje wiadomość e-mail:

1. Wybierz **wszystkich usług** na górnym lewym rogu portalu.
2. Wprowadź *Monitor* w **filtru** pole. Gdy **Monitor** pojawi się w wynikach, wybierz ją.
3. Wybierz **metryki** w obszarze **wspólne usługi**.
4. Wprowadź lub wybierz własne wartości lub wprowadź następujące wartości w przykładzie, Zaakceptuj pozostałe wartości domyślne, a następnie wybierz **OK**:

    |Ustawienie                  |Wartość                                                                                               |
    |---------                |---------                                                                                           |
    |Name (Nazwa)                     | myDdosAlert                                                                                        |
    |Subskrypcja             | Wybierz subskrypcję, która zawiera publiczny adres IP, które chcesz otrzymywać alerty.        |
    |Grupa zasobów           | Wybierz grupę zasobów, która zawiera publiczny adres IP, które chcesz otrzymywać alerty.      |
    |Resource                 | Wybierz publiczny adres IP, który zawiera publiczny adres IP, które chcesz otrzymywać alerty. Przed atakami DDoS monitoruje publiczne adresy IP przypisane do zasobów w ramach sieci wirtualnej. Jeśli nie masz żadnych zasobów mających publiczne adresy IP w sieci wirtualnej, należy najpierw utworzyć zasobu z publicznym adresem IP. Możesz monitorować publiczny adres IP wszystkich zasobów wdrożonych przy użyciu usługi Resource Manager (nie klasycznej) na liście [sieć wirtualna dla usług platformy Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), z wyjątkiem środowiska usługi Azure App Service i Azure VPN Gateway. Aby kontynuować z tego samouczka, możesz szybko utworzyć [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maszyny wirtualnej.                   |
    |Metryka                   | W obszarze przed atakami DDoS ataku lub nie                                                                            |
    |Próg                | 1 — **1** oznacza, że są celem ataku. **0** oznacza, że nie są celem ataku.                         |
    |Okres                   | Wybierz dowolną wartość, możesz wybrać.                                                                   |
    |Powiadom za pośrednictwem poczty E-mail         | Zaznacz pole wyboru                                                                                  |
    |Dodatkowe administratora | Wprowadź swój adres e-mail, jeśli nie masz wiadomości e-mail właściciela, współautora lub czytelnika subskrypcji. |

    W ciągu kilku minut wykrywanie ataków otrzymasz wiadomość e-mail z metryk usługi Azure Monitor, który wygląda podobnie do poniższej ilustracji:

    ![Alert ataku](./media/manage-ddos-protection/ddos-alert.png)


Aby zasymulować ataków DDoS, aby sprawdzić poprawność alert, zobacz [wykrywania weryfikacji przed atakami DDoS](#validate-ddos-detection).

Możesz także dowiedzieć się więcej [konfigurowania elementów webhook](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [aplikacje logiki](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do tworzenia alertów.

## <a name="use-ddos-protection-telemetry"></a>Korzystanie z telemetrii ochrony przed atakami DDoS

Usługi Azure Monitor udostępnia dane telemetryczne dotyczące ataku w czasie rzeczywistym. Dane telemetryczne jest dostępna tylko na czas trwania, publiczny adres IP objętego środki zaradcze. Nie widzisz danych telemetrycznych, przed lub po zminimalizowaniu wpływu ataku.

1. Wybierz **wszystkich usług** na górnym lewym rogu portalu.
2. Wprowadź *Monitor* w **filtru** pole. Gdy **Monitor** pojawi się w wynikach, wybierz ją.
3. Wybierz **metryki**w obszarze **wspólne usługi**.
4. Wybierz **subskrypcji** i **grupy zasobów** zawierające publiczny adres IP, który chcesz, aby dane telemetryczne dotyczące.
5. Wybierz **publiczny adres IP** dla **typ zasobu**, następnie wybierz określony publiczny adres IP ma dane telemetryczne dotyczące.
6. Szereg **dostępne metryki** pojawiają się po lewej stronie ekranu. Te metryki, po wybraniu wykresie przedstawia możliwościom w **wykres metryk usługi Azure Monitor** na ekran Przegląd.
7. Wybierz **agregacji** wpisać jako **maksymalna**

Nazwy metryk pokażesz różnych typów pakietów i bajtów i pakietów, podstawowe konstrukcji nazw tagów na wszystkie metryki w następujący sposób:

- **Nazwa tagu porzuconego** (na przykład **dla ruchu przychodzącego DDoS porzuconych pakietów**): Liczba pakietów porzucony/wyczyszczona przez system ochrony przed atakami DDoS.
- **Nazwa tagu przesłanym** (na przykład **dla ruchu przychodzącego przed atakami DDoS pakiety przesyłane dalej**): Liczba pakietów przesyłanych dalej przez system przed atakami DDoS do miejsca docelowego adresu VIP — ruch, który nie został przefiltrowany.
- **Brak nazwy tagu** (na przykład **przed atakami DDoS pakiety przychodzące**): Całkowita liczba pakietów, które weszły w szybkiej kontroli system — reprezentuje sumę pakiety usunięty i przesyłane dalej.

Aby zasymulować ataków DDoS, aby sprawdzić poprawność danych telemetrycznych, zobacz [wykrywania weryfikacji przed atakami DDoS](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Wyświetl zasady ograniczania ryzyka ataków DDoS

Standard ochrony przed atakami DDoS zasadom trzy automatycznie dopasowane środki zaradcze (TCP SYN, TCP i UDP) za każdy publiczny adres IP z chronionych zasobów, w sieci wirtualnej, która została włączona przed atakami DDoS. Progi zasad można wyświetlić, wybierając **pakiety przychodzące TCP do wyzwolenia ataki DDOS** i **pakietów UDP dla ruchu przychodzącego, aby wyzwolić ataki DDOS** metryki za pomocą polecenia **agregacji** wpisać "Max", jak pokazano na poniższej ilustracji:

![Wyświetl zasady ograniczania ryzyka](./media/manage-ddos-protection/view-mitigation-policies.png)

Progi zasad są konfigurowane przez profilowanie ruchu sieci oparte na nauce maszynowej w usłudze Azure machine automatycznie. Tylko wtedy, gdy naruszenia progu zasad ma ataki DDOS miejsca dla adresu IP w ramach ataku.

## <a name="configure-ddos-attack-analytics"></a>Konfiguruj analizę ataków DDoS
Standard platformy Azure DDoS Protection zapewnia szczegółowe informacje szczegółowe ataku i wizualizacji przy użyciu analizy ataku DDoS. Klienci ochrony sieciach wirtualnych przed atakami DDoS ma szczegółowy wgląd w atak ruchu oraz działania podjęte w celu zminimalizowania skuteczności ataku przy użyciu dzienników przepływu środki zaradcze & l raportów ograniczania ryzyka ataków. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Konfigurowanie raportów ograniczania ryzyka ataków DDoS
Raporty ograniczania ryzyka ataków używa danych protokołu Netflow, które mają charakter zbiorczy zawiera szczegółowe informacje dotyczące ataków na zasób. W dowolnym momencie publicznego zasobu adresu IP jest atakowana, Generowanie raportu rozpocznie się zaraz po uruchomieniu środki zaradcze. Nastąpi przyrostowe wygenerowany raport co 5 minut, a raportem ograniczenia po okresie całego środki zaradcze. To upewnienie się, że w przypadku ataków DDoS jest powtarzany przez dłuższy czas, będzie można wyświetlić najbardziej aktualne migawki raportu ograniczenia co 5 minut i zapoznać się ze wszystkimi raz ograniczaniu ryzyka ataków znajduje się nad. 

1. Wybierz **wszystkich usług** na górnym lewym rogu portalu.
2. Wprowadź *Monitor* w **filtru** pole. Gdy **Monitor** pojawi się w wynikach, wybierz ją.
3. W obszarze **ustawienia**, wybierz opcję **ustawień diagnostycznych**.
4. Wybierz **subskrypcji** i **grupy zasobów** zawierające publiczny adres IP, które mają być rejestrowane.
5. Wybierz **publiczny adres IP** dla **typ zasobu**, następnie wybierz określony publiczny adres IP mają być rejestrowane metryki.
6. Wybierz **Włącz diagnostykę, aby zebrać dziennika DDoSMitigationReports** a następnie wybierz pozycję jak najwięcej poniższych opcji, ile potrzebujesz:

    - **Zarchiwizuj na koncie magazynu**: Dane są zapisywane na koncie usługi Azure Storage. Aby dowiedzieć się więcej na temat tej opcji, zobacz [archiwizowanie dzienników diagnostycznych](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Stream do usługi event hub**: Umożliwia odbiorcy dziennika pobrać dzienniki przy użyciu usługi Azure Event Hub. Centra zdarzeń umożliwiają integrację z usługą Splunk lub innych systemów SIEM. Aby dowiedzieć się więcej na temat tej opcji, zobacz [Stream dzienników diagnostycznych do usługi event hub](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Wysyłanie do usługi Log Analytics**: Zapisuje dzienniki w usłudze Azure Monitor. Aby dowiedzieć się więcej na temat tej opcji, zobacz [zbieranie dzienników do użytku w dziennikach w usłudze Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Oba raporty przyrostowych i po atak środki zaradcze zawierają następujące pola
- Ataków
- Statystyki ruchu
- Przyczyna porzuconych pakietów
- Protokoły związane
- Top 10 źródła krajów lub regionów
- 10 najważniejszych źródła numery ASN

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Konfigurowanie dzienników przepływu ograniczania ryzyka ataków DDoS
Dzienniki przepływu ograniczania ryzyka ataków umożliwiają przeglądanie porzuconych ruchu, przekazywane ruchu sieciowego i inne interesujące punktów danych podczas aktywnego ataków DDoS w czasie niemal rzeczywistym. Możesz pozyskiwać stały strumień danych w systemach SIEM za pośrednictwem Centrum zdarzeń monitorowania niemal w czasie rzeczywistym, potencjalne akcje i zaspokoić potrzebę operacji obrony. 

1. Wybierz **wszystkich usług** na górnym lewym rogu portalu.
2. Wprowadź *Monitor* w **filtru** pole. Gdy **Monitor** pojawi się w wynikach, wybierz ją.
3. W obszarze **ustawienia**, wybierz opcję **ustawień diagnostycznych**.
4. Wybierz **subskrypcji** i **grupy zasobów** zawierające publiczny adres IP, które mają być rejestrowane.
5. Wybierz **publiczny adres IP** dla **typ zasobu**, następnie wybierz określony publiczny adres IP mają być rejestrowane metryki.
6. Wybierz **Włącz diagnostykę, aby zebrać dziennika DDoSMitigationFlowLogs** a następnie wybierz pozycję jak najwięcej poniższych opcji, ile potrzebujesz:

    - **Zarchiwizuj na koncie magazynu**: Dane są zapisywane na koncie usługi Azure Storage. Aby dowiedzieć się więcej na temat tej opcji, zobacz [archiwizowanie dzienników diagnostycznych](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Stream do usługi event hub**: Umożliwia odbiorcy dziennika pobrać dzienniki przy użyciu usługi Azure Event Hub. Centra zdarzeń umożliwiają integrację z usługą Splunk lub innych systemów SIEM. Aby dowiedzieć się więcej na temat tej opcji, zobacz [Stream dzienników diagnostycznych do usługi event hub](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Wysyłanie do usługi Log Analytics**: Zapisuje dzienniki w usłudze Azure Monitor. Aby dowiedzieć się więcej na temat tej opcji, zobacz [zbieranie dzienników do użytku w dziennikach w usłudze Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Aby wyświetlić dane dzienników przepływów na pulpicie nawigacyjnym analizy usługi Azure, należy zaimportować przykładowy pulpit nawigacyjny z https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

Dzienniki przepływów będzie zawierać następujące pola: 
- Źródłowy adres IP
- Docelowy adres IP
- Port źródłowy 
- Port docelowy 
- Typ protokołu 
- Akcję wykonywaną podczas środki zaradcze



## <a name="validate-ddos-detection"></a>Sprawdź poprawność wykrywanie ataków DDoS

Firma Microsoft nawiązała współpracę z [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) do tworzenia interfejsu, gdzie można wygenerować ruchu względem włączona ochrona przed atakami DDoS publicznych adresów IP dla symulacji. Symulacja chmury punktu przerwania pozwala na:

- Sprawdzić, jak Microsoft Azure przed atakami DDoS Protection chroni zasoby platformy Azure przed atakami DDoS
- Optymalizowanie procesu reagowania na zdarzenia w ramach ataków DDoS
- Dokumentowanie zgodności przed atakami DDoS
- Szkolenie zespołom zabezpieczeń sieci

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Wyświetl alerty usługi DDoS protection w usłudze Azure Security Center

Usługa Azure Security Center zawiera listę [alerty zabezpieczeń](/azure/security-center/security-center-managing-and-responding-alerts), informacje w celu ułatwienia badania i Korygowanie problemów. Dzięki tej funkcji możesz Uzyskaj ujednolicony widok alertów, w tym alertów dotyczących ataku DDoS oraz działania podjęte w celu zminimalizowania skuteczności ataku w czasie zbliżonym do.
Istnieją dwa konkretnych alertów, zobaczysz dla dowolnego przed atakami DDoS ataki wykrywania i łagodzenia skutków:

- **Wykryto ataków DDoS, publiczny adres IP na**: Ten alert jest generowany, gdy usługi DDoS protection wykryje, że jeden z publicznymi adresami IP celem ataków DDoS.
- **Skorygowane ataków DDoS, publiczny adres IP na**: Ten alert jest generowany, gdy atak na publiczny adres IP zostały skorygowane.
Aby wyświetlić alerty, otwórz **usługi Security Center** w witrynie Azure portal. W obszarze **ochrony przed zagrożeniami**, wybierz opcję **alerty zabezpieczeń**. Poniższy zrzut ekranu przedstawia przykład alertów ataku DDoS.

![Przed atakami DDoS alertu w usłudze Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Alerty zawierają ogólne informacje o publicznym adresie IP, która podlega ataku, geograficzne i informacje dotyczące analizy zagrożeń i kroki korygowania funkcję.

## <a name="permissions"></a>Uprawnienia

Aby pracować z planów ochrony przed atakami DDoS, Twoje konto musi mieć przypisaną do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowe](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, którą przypisano odpowiednie działania wymienione w poniższej tabeli:

| action                                            | Name (Nazwa)                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Odczyt planu ochrony przed atakami DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Tworzenie lub aktualizowanie planu ochrony przed atakami DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Usuwanie planu ochrony przed atakami DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Dołącz do planu ochrony przed atakami DDoS              |

Aby włączyć ochronę przed atakami DDoS dla sieci wirtualnej, Twoje konto musi być przypisany odpowiedni [akcje w przypadku sieci wirtualnych](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>Kolejne kroki

- Tworzenie i stosowanie [usługa Azure policy](policy-samples.md) dla sieci wirtualnych