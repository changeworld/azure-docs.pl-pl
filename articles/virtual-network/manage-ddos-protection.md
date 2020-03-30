---
title: Zarządzanie standardem ochrony przed atakami DDoS azure przy użyciu portalu Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak używać standardowych danych telemetrycznych usługi Azure DDoS Protection w usłudze Azure Monitor w celu złagodzenia ataku.
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
ms.openlocfilehash: 786b21e7571ed173d2da90f587a5b76d8c92a13d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279730"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Zarządzanie standardem ochrony przed atakami DDoS azure przy użyciu portalu Azure

Dowiedz się, jak włączyć i wyłączyć ochronę przed rozproszoną odmową usługi (DDoS) i użyć danych telemetrycznych w celu złagodzenia ataku DDoS za pomocą standardu ochrony przed atakami DDoS azure. Standard ochrony przed atakami DDoS chroni zasoby platformy Azure, takie jak maszyny wirtualne, moduły równoważenia obciążenia i bramy aplikacji, które mają przypisany [publiczny adres IP](virtual-network-public-ip-address.md) platformy Azure. Aby dowiedzieć się więcej o standardzie ochrony przed atakami DDoS i jego możliwościach, zobacz [Omówienie standardu ochrony przed atakami DDoS](ddos-protection-overview.md).

Przed wykonaniem jakichkolwiek kroków w tym samouczku zaloguj się do https://portal.azure.com witryny Azure Portal przy użyciu konta przypisanego do roli [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do roli [niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) która jest przypisana odpowiednie akcje wymienione w uprawnienia [.](#permissions)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-a-ddos-protection-plan"></a>Tworzenie planu ochrony przed atakami DDoS

Plan ochrony przed atakami DDoS definiuje zestaw sieci wirtualnych, które mają włączony standard ochrony przed atakami DDoS w różnych subskrypcjach. Można skonfigurować jeden plan ochrony DDoS dla organizacji i połączyć sieci wirtualne z wielu subskrypcji z tym samym planem. Sam plan ochrony przed atakami DDoS jest również skojarzony z subskrypcją, którą można wybrać podczas tworzenia planu. Plan ochrony przed atakami DDoS działa w różnych regionach i subskrypcjach. Przykład -można utworzyć plan w regionie Wschodnio-AMERYKAŃSKI i łącze do #1 subskrypcji w dzierżawie. Ten sam plan może być połączony z sieciami wirtualnymi z innych subskrypcji w różnych regionach, w dzierżawie. Subskrypcja, z że plan jest powiązany z miesięcznym rachunkiem cyklicznym za plan, a także opłatami za nadwyżek, w przypadku gdy liczba chronionych publicznych adresów IP przekracza 100. Aby uzyskać więcej informacji na temat cen DDoS, zobacz [szczegóły cen](https://azure.microsoft.com/pricing/details/ddos-protection/).

Tworzenie więcej niż jednego planu nie jest wymagane dla większości organizacji. Planu nie można przenosić między subskrypcjami. Jeśli chcesz zmienić subskrypcję, w której znajduje się plan, musisz [usunąć istniejący plan](#work-with-ddos-protection-plans) i utworzyć nowy.

1. Wybierz **pozycję Utwórz zasób** w lewym górnym rogu witryny Azure portal.
2. Wyszukaj *DDoS*. Gdy **plan ochrony DDos** pojawia się w wynikach wyszukiwania, wybierz go.
3. Wybierz **pozycję Utwórz**.
4. Wprowadź lub wybierz własne wartości, wprowadź lub wybierz następujące przykładowe wartości, a następnie wybierz pozycję **Utwórz:**

    |Ustawienie        |Wartość                                              |
    |---------      |---------                                          |
    |Nazwa           | myDdosProtectionPlan                              |
    |Subskrypcja   | Wybierz subskrypcję.                         |
    |Grupa zasobów | Wybierz **pozycję Utwórz nowy** i wprowadź *myResourceGroup* |
    |Lokalizacja       | Wschodnie stany USA                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Włączanie DDoS dla nowej sieci wirtualnej

1. Wybierz **pozycję Utwórz zasób** w lewym górnym rogu witryny Azure portal.
2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Sieć wirtualna**.
3. Wprowadź lub wybierz własne wartości, wprowadź lub wybierz następujące przykładowe wartości, zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Utwórz:**

    | Ustawienie         | Wartość                                                        |
    | ---------       | ---------                                                    |
    | Nazwa            | myVirtualNetwork                                             |
    | Subskrypcja    | Wybierz subskrypcję.                                    |
    | Grupa zasobów  | Wybierz **pozycję Użyj istniejącego**, a następnie wybierz **myResourceGroup** |
    | Lokalizacja        | Wschodnie stany USA                                                      |
    | Ochrona przed atakami DDos | Wybierz **pozycję Standardowy,** a następnie w obszarze **Ochrona przed atakami DDoS**wybierz **myDdosProtectionPlan**. Wybrany plan może być w tej samej lub innej subskrypcji niż sieć wirtualna, ale obie subskrypcje muszą być skojarzone z tą samą dzierżawą usługi Azure Active Directory.|

Nie można przenieść sieci wirtualnej do innej grupy zasobów lub subskrypcji, gdy standard DDoS jest włączony dla sieci wirtualnej. Jeśli chcesz przenieść sieć wirtualną z włączoną funkcją DDoS Standard, najpierw wyłącz DDoS Standard, przenieś sieć wirtualną, a następnie włącz standard DDoS. Po przeprowadzce automatycznie dostrojone progi zasad dla wszystkich chronionych publicznych adresów IP w sieci wirtualnej są resetowane.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Włączanie DDoS dla istniejącej sieci wirtualnej

1. Utwórz plan ochrony przed atakami DDoS, wykonując kroki opisane w [aplikacji Utwórz plan ochrony przed atakami DDoS](#create-a-ddos-protection-plan), jeśli nie masz istniejącego planu ochrony przed atakami DDoS.
2. Wybierz **pozycję Utwórz zasób** w lewym górnym rogu witryny Azure portal.
3. Wprowadź nazwę sieci wirtualnej, dla której chcesz włączyć standard ochrony przed atakami DDoS w **polu Wyszukaj zasoby, usługi i dokumenty** u góry portalu. Gdy nazwa sieci wirtualnej pojawi się w wynikach wyszukiwania, wybierz ją.
4. Wybierz **ochronę przed atakami DDoS**w obszarze **USTAWIENIA**.
5. Wybierz **opcję Standardowy**. W obszarze **Plan ochrony DDoS**wybierz istniejący plan ochrony DDoS lub plan utworzony w kroku 1, a następnie wybierz pozycję **Zapisz**. Wybrany plan może być w tej samej lub innej subskrypcji niż sieć wirtualna, ale obie subskrypcje muszą być skojarzone z tą samą dzierżawą usługi Azure Active Directory.

**Polecenia** 
- Interfejs wiersza polecenia platformy Azure: [tworzenie ochrony przed atakami ddos sieci az](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-create)
- Powershell: [Nowy Plan Ochrony AzDdos](https://docs.microsoft.com/powershell/module/Az.Network/New-AzDdosProtectionPlan?view=azps-2.8.0)
 

## <a name="disable-ddos-for-a-virtual-network"></a>Wyłączanie DDoS dla sieci wirtualnej

1. Wprowadź nazwę sieci wirtualnej, dla której chcesz wyłączyć standard ochrony przed atakami DDoS, w **polu Szukaj zasobów, usług i dokumentów** u góry portalu. Gdy nazwa sieci wirtualnej pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz **ochronę przed atakami DDoS**w obszarze **USTAWIENIA**.
3. Wybierz **pozycję Podstawowe** w obszarze Plan ochrony przed atakami **DDoS,** a następnie wybierz pozycję **Zapisz**.

**Polecenia** 
- Interfejs wiersza polecenia platformy Azure: [usuwanie ochrony przed ddos sieci az](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-delete)
- Powershell: [Usuń-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/az.network/remove-azddosprotectionplan?view=azps-3.2.0)

## <a name="work-with-ddos-protection-plans"></a>Praca z planami ochrony DDoS

1. Wybierz **pozycję Wszystkie usługi** u góry, po lewej stronie portalu.
2. Wprowadź *DDoS* w polu **Filtr.** Gdy w wynikach pojawią się **plany ochrony DDoS,** wybierz je.
3. Wybierz plan ochrony, który chcesz wyświetlić z listy.
4. Wszystkie sieci wirtualne skojarzone z planem są wymienione.
5. Jeśli chcesz usunąć plan, musisz najpierw odłączyć od niego wszystkie sieci wirtualne. Aby odłączyć plan od sieci wirtualnej, zobacz [Wyłączanie DDoS dla sieci wirtualnej](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Konfigurowanie alertów dla metryk ochrony przed atakami DDoS

Można wybrać dowolną z dostępnych metryk ochrony DDoS, aby ostrzec, gdy istnieje aktywne ograniczenie podczas ataku, przy użyciu konfiguracji alertu usługi Azure Monitor. Po spełnieniu warunków podany adres odbiera wiadomość e-mail z alertem:

1. Wybierz **pozycję Wszystkie usługi** u góry, po lewej stronie portalu.
2. Wprowadź *monitor* w polu **Filtr.** Gdy **Monitor** monitor pojawi się w wynikach, wybierz go.
3. Wybierz **pozycję Metryki** w obszarze **USŁUGI UDOSTĘPNIONE**.
4. Wprowadź lub wybierz własne wartości lub wprowadź następujące przykładowe wartości, zaakceptuj pozostałe wartości domyślne, a następnie wybierz **przycisk OK:**

    |Ustawienie                  |Wartość                                                                                               |
    |---------                |---------                                                                                           |
    |Nazwa                     | myDdosAlert ( myDdosAlert )                                                                                        |
    |Subskrypcja             | Wybierz subskrypcję zawierającą publiczny adres IP, dla którego chcesz otrzymywać alerty.        |
    |Grupa zasobów           | Wybierz grupę zasobów zawierającą publiczny adres IP, dla którego chcesz otrzymywać alerty.      |
    |Zasób                 | Wybierz publiczny adres IP zawierający publiczny adres IP, dla którego chcesz otrzymywać alerty. DDoS monitoruje publiczne adresy IP przypisane do zasobów w sieci wirtualnej. Jeśli nie masz żadnych zasobów z publicznymi adresami IP w sieci wirtualnej, należy najpierw utworzyć zasób z publicznym adresem IP. Można monitorować publiczny adres IP wszystkich zasobów wdrożonych za pośrednictwem Menedżera zasobów (nie klasycznego) wymienionych w [sieci wirtualnej dla usług platformy Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), z wyjątkiem środowisk usługi Azure App Service i bramy sieci VPN platformy Azure. Aby kontynuować ten samouczek, można szybko utworzyć maszynę wirtualną [systemu Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)                   |
    |Metryka                   | W ramach ataku DDoS lub nie                                                                            |
    |Próg                | 1 - **1** oznacza, że jesteś atakowany. **0** oznacza, że nie jesteś atakowany.                         |
    |Okres                   | Wybierz dowolną wartość.                                                                   |
    |Powiadamiaj za pośrednictwem poczty e-mail         | Zaznacz to pole wyboru.                                                                                  |
    |Dodatkowy administrator | Wprowadź swój adres e-mail, jeśli nie jesteś właścicielem poczty e-mail, współautorem lub czytelnikiem subskrypcji. |

    W ciągu kilku minut od wykrycia ataków otrzymasz wiadomość e-mail z metryk usługi Azure Monitor, która wygląda podobnie do poniższego obrazu:

    ![Alert o ataku](./media/manage-ddos-protection/ddos-alert.png)


Aby symulować atak DDoS w celu zweryfikowanie alertu, zobacz [Sprawdzanie poprawności wykrywania DDoS](#validate-ddos-detection).

Możesz również dowiedzieć się więcej o [konfigurowaniu elementów webhook](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [aplikacji logiki](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do tworzenia alertów.

## <a name="use-ddos-protection-telemetry"></a>Korzystanie z danych telemetrycznych ochrony przed atakami DDoS

Dane telemetryczne dla ataku są dostarczane za pośrednictwem usługi Azure Monitor w czasie rzeczywistym. Dane telemetryczne są dostępne tylko przez czas trwania, przez który publiczny adres IP jest w ramach łagodzenia. Nie widać danych telemetrycznych przed lub po ataku jest złagodzone.

1. Wybierz **pozycję Wszystkie usługi** u góry, po lewej stronie portalu.
2. Wprowadź *monitor* w polu **Filtr.** Gdy **Monitor** monitor pojawi się w wynikach, wybierz go.
3. Wybierz **pozycję Metryki**w obszarze **USŁUGI UDOSTĘPNIONE**.
4. Wybierz grupę **Subskrypcja** i **Zasób,** które zawierają publiczny adres IP, dla którego chcesz uzyskać dane telemetryczne.
5. Wybierz **publiczny adres IP** dla typu **zasobu,** a następnie wybierz określony publiczny adres IP, dla którego chcesz uzyskać dane telemetryczne.
6. Po lewej stronie ekranu pojawi się seria **dostępnych danych.** Te metryki, po wybraniu opcji, są na wykresie na **wykresie na wykresie metryki usługi Azure Monitor** na ekranie przeglądu.
7. Wybierz typ **agregacji** jako **Maks.**

Nazwy metryk przedstawiają różne typy pakietów oraz bajty i pakiety, z podstawową konstrukcją nazw znaczników na każdej metryce w następujący sposób:

- **Porzucona nazwa znacznika** (na przykład **porzucone pakiety przychodzące DDoS):** Liczba pakietów porzuconych/usuniętych przez system ochrony DDoS.
- **Nazwa tagu przesyłanego dalej** (na przykład **przychodzące pakiety przesyłane dalej DDoS):** Liczba pakietów przekazanych przez system DDoS do docelowego adresu VIP — ruch, który nie został odfiltrowany.
- **Brak nazwy znacznika** (na przykład **DDoS pakietów przychodzących):** Całkowita liczba pakietów, które weszły do systemu oczyszczania - reprezentująca sumę pakietów porzuconych i przekazanych dalej.

Aby symulować atak DDoS w celu sprawdzenia poprawności danych telemetrycznych, zobacz [Sprawdzanie poprawności wykrywania DDoS](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Wyświetlanie zasad ograniczania ddos

Standard ochrony przed atakami DDoS stosuje trzy automatycznie dostrojone zasady łagodzenia (TCP SYN, TCP & UDP) dla każdego publicznego adresu IP chronionego zasobu w sieci wirtualnej z włączonym DDoS. Progi zasad można wyświetlić, wybierając **przychodzące pakiety TCP, aby wyzwolić środki zaradcze DDoS** i **przychodzące pakiety UDP, aby wyzwolić** metryki ograniczające zagrożenie DDoS z typem **agregacji** jako "Max", jak pokazano na poniższym rysunku:

![Wyświetlanie zasad ograniczania ryzyka](./media/manage-ddos-protection/view-mitigation-policies.png)

Progi zasad są konfigurowane automatycznie za pomocą profilowania ruchu sieciowego opartego na uczeniu maszynowym platformy Azure. Tylko wtedy, gdy próg zasad zostanie naruszony, środki zaradcze ddos występują dla adresów IP atakowanych.

## <a name="configure-ddos-attack-analytics"></a>Konfigurowanie analizy ataków DDoS
Standard usługi Azure DDoS Protection zapewnia szczegółowe szczegółowe informacje o atakach i wizualizację za pomocą usługi DDoS Attack Analytics. Klienci chroniący swoje sieci wirtualne przed atakami DDoS mają szczegółowy wgląd w ruch ataków i akcje podejmowane w celu złagodzenia ataku za pomocą raportów ograniczających ataki & dzienników przepływu ograniczającego zagrożenie. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Konfigurowanie raportów ograniczania ryzyka ataków DDoS
Raporty ograniczające zagrożenie atakiem używają danych protokołu przepływa netto, który jest agregowany w celu zapewnienia szczegółowych informacji o ataku na zasób. Za każdym razem, gdy publiczny zasób IP jest atakowany, generowanie raportu rozpocznie się natychmiast po rozpoczęciu łagodzenia. Będzie raport przyrostowy generowany co 5 minut i raport po łagodzeniu dla całego okresu łagodzenia. Ma to na celu zapewnienie, że w przypadku ataku DDoS będzie kontynuowana przez dłuższy czas, będzie można wyświetlić najbardziej bieżącą migawkę raportu łagodzenia co 5 minut i pełne podsumowanie po zakończeniu łagodzenia ataków. 

1. Wybierz **pozycję Wszystkie usługi** u góry, po lewej stronie portalu.
2. Wprowadź *monitor* w polu **Filtr.** Gdy **Monitor** monitor pojawi się w wynikach, wybierz go.
3. W obszarze **USTAWIENIA**wybierz pozycję **Ustawienia diagnostyczne**.
4. Wybierz grupę **Subskrypcja** i **Zasób,** które zawierają publiczny adres IP, który chcesz zarejestrować.
5. Wybierz **publiczny adres IP** dla typu **zasobu,** a następnie wybierz określony publiczny adres IP, dla którego chcesz rejestrować metryki.
6. Wybierz **włącz diagnostykę, aby zebrać dziennik DDoSMitigationReports,** a następnie wybierz dowolną liczbę następujących opcji:

    - **Archiwum na koncie magazynu:** Dane są zapisywane na koncie usługi Azure Storage. Aby dowiedzieć się więcej o tej opcji, zobacz [Archiwizuj dzienniki diagnostyczne](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Strumień do centrum zdarzeń:** Umożliwia odbiorcy dziennika, aby odebrać dzienniki przy użyciu usługi Azure Event Hub. Koncentratory zdarzeń umożliwiają integrację ze splunkiem lub innymi systemami SIEM. Aby dowiedzieć się więcej o tej opcji, zobacz [Przesyłanie strumieniowe dzienników diagnostycznych do centrum zdarzeń](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Wyślij do usługi Log Analytics:** Zapisuje dzienniki do usługi Azure Monitor. Aby dowiedzieć się więcej o tej opcji, zobacz [Zbieranie dzienników do użycia w dziennikach usługi Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Zarówno przyrostowe, jak & raporty ograniczające łagodzenie ataków obejmują następujące pola:
- Wektory ataku
- Statystyki ruchu
- Przyczyna porzuconych pakietów
- Protokoły, o które chodzi
- 10 najlepszych krajów lub regionów źródłowych
- Top 10 źródło ASN

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Konfigurowanie dzienników przepływu ograniczającego ataki DDoS
Dzienniki przepływu łagodzenia ataków umożliwiają przeglądanie porzuconego ruchu, ruchu przesyłanego dalej i innych interesujących punktów danych podczas aktywnego ataku DDoS w czasie zbliżonym do rzeczywistego. Możesz pozyskiwania stałego strumienia tych danych do systemów SIEM za pośrednictwem centrum zdarzeń do monitorowania w czasie zbliżonym do rzeczywistego, podejmować potencjalne działania i zaspokajać potrzeby operacji obronnych. 

1. Wybierz **pozycję Wszystkie usługi** u góry, po lewej stronie portalu.
2. Wprowadź *monitor* w polu **Filtr.** Gdy **Monitor** monitor pojawi się w wynikach, wybierz go.
3. W obszarze **USTAWIENIA**wybierz pozycję **Ustawienia diagnostyczne**.
4. Wybierz grupę **Subskrypcja** i **Zasób,** które zawierają publiczny adres IP, który chcesz zarejestrować.
5. Wybierz **publiczny adres IP** dla typu **zasobu,** a następnie wybierz określony publiczny adres IP, dla którego chcesz rejestrować metryki.
6. Wybierz **włącz diagnostykę, aby zebrać dziennik DDoSMitigationFlowLogs,** a następnie wybierz dowolną liczbę następujących opcji:

    - **Archiwum na koncie magazynu:** Dane są zapisywane na koncie usługi Azure Storage. Aby dowiedzieć się więcej o tej opcji, zobacz [Archiwizuj dzienniki diagnostyczne](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Strumień do centrum zdarzeń:** Umożliwia odbiorcy dziennika, aby odebrać dzienniki przy użyciu usługi Azure Event Hub. Koncentratory zdarzeń umożliwiają integrację ze splunkiem lub innymi systemami SIEM. Aby dowiedzieć się więcej o tej opcji, zobacz [Przesyłanie strumieniowe dzienników diagnostycznych do centrum zdarzeń](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Wyślij do usługi Log Analytics:** Zapisuje dzienniki do usługi Azure Monitor. Aby dowiedzieć się więcej o tej opcji, zobacz [Zbieranie dzienników do użycia w dziennikach usługi Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Aby wyświetlić dane dzienników przepływu na pulpicie nawigacyjnym analizy platformy Azure, można zaimportować przykładowy pulpit nawigacyjny zhttps://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

Dzienniki przepływu będą miały następujące pola: 
- Źródłowy adres IP
- Docelowy adres IP
- Port źródłowy 
- Port docelowy 
- Typ protokołu 
- Działania podjęte podczas łagodzenia zmiany klimatu



## <a name="validate-ddos-detection"></a>Sprawdzanie poprawności wykrywania DDoS

Firma Microsoft nawiązała współpracę z [usługą BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) w celu utworzenia interfejsu, w którym można generować ruch na publicznych adresach IP z obsługą ochrony przed atakami DDoS dla symulacji. Symulacja chmury programu BreakPoint umożliwia:

- Sprawdzanie poprawności, w jaki sposób program DDoS Protection platformy Microsoft Azure chroni zasoby platformy Azure przed atakami DDoS
- Optymalizacja procesu reagowania na incydenty w przypadku ataku DDoS
- Zgodność z dokumentami DDoS
- Szkolenie zespołów bezpieczeństwa sieci

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Wyświetlanie alertów ochrony przed atakami DDoS w usłudze Azure Security Center

Usługa Azure Security Center udostępnia listę [alertów zabezpieczeń,](/azure/security-center/security-center-managing-and-responding-alerts)z informacjami ułatwiające badanie i rozwiązywanie problemów. Dzięki tej funkcji otrzymujesz ujednolicony widok alertów, w tym alertów związanych z atakami DDoS i działań podjętych w celu złagodzenia ataku w najbliższym czasie.
Istnieją dwa określone alerty, które zostaną wyświetlone dla wykrywania i łagodzenia ataków DDoS:

- **Atak DDoS wykryty dla publicznego adresu IP:** Ten alert jest generowany, gdy usługa ochrony DDoS wykryje, że jeden z publicznych adresów IP jest celem ataku DDoS.
- **Atak DDoS złagodzony dla publicznego adresu IP:** Ten alert jest generowany, gdy atak na publiczny adres IP został złagodzony.
Aby wyświetlić alerty, otwórz **Centrum zabezpieczeń** w witrynie Azure portal. W obszarze **Ochrona przed zagrożeniami**wybierz pozycję **Alerty zabezpieczeń**. Poniższy zrzut ekranu przedstawia przykład alertów ataku DDoS.

![Alert DDoS w usłudze Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Alerty zawierają ogólne informacje o publicznym adresie IP, który jest atakowany, informacje o geo i analizy zagrożeń oraz kroki korygowania.

## <a name="permissions"></a>Uprawnienia

Aby pracować z planami ochrony przed atakami DDoS, konto musi być przypisane do roli [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do roli [niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) która ma przypisane odpowiednie akcje wymienione w poniższej tabeli:

| Akcja                                            | Nazwa                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Przeczytaj plan ochrony przed atakami DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Tworzenie lub aktualizowanie planu ochrony przed atakami DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Usuwanie planu ochrony przed atakami DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Dołączanie do planu ochrony przed atakami DDoS              |

Aby włączyć ochronę przed atakami DDoS dla sieci wirtualnej, należy również przypisać do niego odpowiednie [akcje dla sieci wirtualnych.](manage-virtual-network.md#permissions)

## <a name="next-steps"></a>Następne kroki

- Tworzenie i stosowanie [zasad platformy Azure](policy-samples.md) dla sieci wirtualnych