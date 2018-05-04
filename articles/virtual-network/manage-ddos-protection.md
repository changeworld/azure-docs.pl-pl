---
title: Zarządzanie Azure DDoS ochrony standardowe przy użyciu portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ograniczyć atak za pomocą standardowych ochrony przed atakami DDoS Azure telemetrii w Azure Monitor.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: 81f26dc72e7a1194cd7c0a5c4997e64b0f61d444
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Zarządzanie Azure DDoS ochrony standardowe przy użyciu portalu Azure

Dowiedz się, jak włączyć i wyłączyć atakach typu protection service (DDoS) i użyj telemetrii, aby ograniczyć takiego ataku z standardowe ochrony przed atakami DDoS Azure. Standardowa ochrony przed atakami DDoS chroni zasobów platformy Azure, takich jak maszyny wirtualne, usługi równoważenia obciążenia i bram aplikacji, które mają Azure [publicznego adresu IP](virtual-network-public-ip-address.md) przypisane do niej. Aby dowiedzieć się więcej na temat ochrony przed atakami DDoS — standardowe i jego możliwości, zobacz [standardowe ochrony przed atakami DDoS — omówienie](ddos-protection-overview.md).

Przed wykonaniem dowolnej kroków w tym samouczku, zaloguj się do portalu Azure pod adresem https://portal.azure.com. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-ddos-protection-plan"></a>Tworzenie planu ochrony przed atakami DDoS

Plan ochrony przed atakami DDoS definiuje zestaw sieci wirtualnych, które mają włączone w subskrypcjach standard ochrony przed atakami DDoS. Można skonfigurować jeden plan ochrony przed atakami DDoS w organizacji, a łącze wirtualne sieci z wieloma subskrypcjami do tego samego planu. Sam Plan ochrony przed atakami DDoS jest także powiązany z subskrypcją, wybierz podczas tworzenia planu. Subskrypcja plan jest skojarzona z ponosi miesięczne cyklicznego kwota rachunku dla planu, a także nadwyżkowe opłat, w przypadku, gdy liczba chronionych publicznych adresów IP przekracza 100. Aby uzyskać więcej informacji o cenach DDoS, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/ddos-protection/).

Tworzenie więcej niż jeden plan nie jest wymagana w przypadku większości organizacji. Plan nie można przenosić między subskrypcjami. Jeśli chcesz zmienić subskrypcję, plan jest konieczne [Usuń istniejący plan](#work-with-ddos-protection-plans) i Utwórz nowe.
 
1. Wybierz **Utwórz zasób** w lewym górnym rogu portalu Azure.
2. Wyszukaj *DDoS*. Gdy **plan ochrony przed atakami DDos** pojawia się w wynikach wyszukiwania, wybierz go.
3. Wybierz pozycję **Utwórz**.
4. Wprowadź lub wybierz własne wartości lub wprowadź lub wybierz następujące przykładowe wartości, a następnie wybierz **Utwórz**:

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Name (Nazwa)     | myDdosProtectionPlan         |
    |Subskrypcja     | Wybierz subskrypcję.        |
    |Grupa zasobów     | Wybierz **Utwórz nowy** , a następnie wprowadź *myResourceGroup*        |
    |Lokalizacja     | Wschodnie stany USA        |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Włącz DDoS dla nowej sieci wirtualnej

1. Wybierz **Utwórz zasób** w lewym górnym rogu portalu Azure.
2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Sieć wirtualna**.
3. Wprowadź lub wybierz własne wartości, wprowadź lub wybierz następujące przykładowe wartości, Zaakceptuj pozostałe wartości domyślne, a następnie wybierz **Utwórz**:
 
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Name (Nazwa)     | myVirtualNetwork         |
    |Subskrypcja     | Wybierz subskrypcję.        |
    |Grupa zasobów     | Wybierz **Użyj istniejącego**, a następnie wybierz **myResourceGroup**        |
    |Lokalizacja     | Wschodnie stany USA        |
    |Ochrona przed atakami DDos| Wybierz **standardowe** , a następnie w obszarze **ochrona przed atakami DDoS**, wybierz pozycję **myDdosProtectionPlan**. Wybrany plan może być w subskrypcji tego samego lub innego niż sieci wirtualnej, ale obie subskrypcje musi być skojarzony z tej samej dzierżawy usługi Azure Active Directory.|

Nie można przenieść sieci wirtualnej do innej grupy zasobów lub subskrypcji, gdy DDoS Standard jest włączona dla sieci wirtualnej. Jeśli musisz przenieść sieć wirtualną ze standardowym DDoS włączone, najpierw wyłącz DDoS Standard Przenieś sieci wirtualnej i włączysz DDoS standard. Po przeniesieniu są resetowane progi automatycznie dopasowane zasady dla wszystkich chronionych publiczne adresy IP w sieci wirtualnej.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Włącz DDoS dla istniejącej sieci wirtualnej 

1. Tworzenie planu ochrony przed atakami DDoS, wykonując kroki opisane w [utworzenie planu ochrony przed atakami DDoS](#create-a-ddos-protection-plan), jeśli nie masz istniejący plan ochrony przed atakami DDoS.
2. Wybierz **Utwórz zasób** w lewym górnym rogu portalu Azure.
3. Wprowadź nazwę sieci wirtualnej, która ma zostać włączony przed atakami DDoS ochrony Standard w **wyszukiwania zasobów, usługi i pole docs** w górnej części portalu. Gdy nazwa sieci wirtualnej jest wyświetlana w wynikach wyszukiwania, zaznacz go.
4. Wybierz **ochrona przed atakami DDoS**w obszarze **ustawienia**.
5. Wybierz **standardowe**. W obszarze **plan ochrony przed atakami DDoS**, wybierz istniejący plan ochrony przed atakami DDoS lub plan utworzony w kroku 1, a następnie wybierz **zapisać**. Wybrany plan może być w subskrypcji tego samego lub innego niż sieci wirtualnej, ale obie subskrypcje musi być skojarzony z tej samej dzierżawy usługi Azure Active Directory.

## <a name="disable-ddos-for-a-virtual-network"></a>Wyłącz DDoS sieci wirtualnej

1. Wprowadź nazwę sieci wirtualnej, aby wyłączyć standardu ochrony przed atakami DDoS dla w **wyszukiwania zasobów, usługi i pole docs** w górnej części portalu. Gdy nazwa sieci wirtualnej jest wyświetlana w wynikach wyszukiwania, zaznacz go.
2. Wybierz **ochrona przed atakami DDoS**w obszarze **ustawienia**.
3. Wybierz **podstawowe** w obszarze **plan ochrony przed atakami DDoS** , a następnie wybierz **zapisać**.

## <a name="work-with-ddos-protection-plans"></a>Praca z planami ochrony przed atakami DDoS

1. Wybierz **wszystkie usługi** u góry po lewej portalu.
2. Wprowadź *DDoS* w **filtru** pole. Gdy **planów ochrony przed atakami DDoS** są wyświetlane w wynikach, wybierz ją.
3. Wybierz plan ochrony, które mają być wyświetlane na liście.
4. Są wyświetlane wszystkie sieci wirtualne związane z planem.
5. Jeśli chcesz usunąć plan, należy najpierw skojarzenie wszystkie sieci wirtualne od niego. Aby usunąć skojarzenie planu z sieci wirtualnej, zobacz [wyłączyć DDoS sieci wirtualnej](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Skonfiguruj alerty dla metryki ochrony przed atakami DDoS

Można wybrać jedną z dostępnymi metrykami ochrony przed atakami DDoS do wysyłania alertów, gdy istnieje aktywne środki zaradcze w przypadku ataków przy użyciu konfiguracji alertu Azure Monitor. Po spełnieniu warunków określony adres otrzymuje wiadomość e-mail alertów:

1. Wybierz **wszystkie usługi** u góry po lewej portalu. 
2. Wprowadź *Monitor* w **filtru** pole. Gdy **Monitor** pojawia się w wynikach, wybierz go.
3. Wybierz **metryki** w obszarze **wspólne usługi**.
4. Wprowadź, lub wybrać własne wartości lub wprowadź następujące przykładowe wartości, Zaakceptuj pozostałe wartości domyślne, a następnie wybierz **OK**:

    |Ustawienie  |Wartość |
    |---------|---------|
    |Name (Nazwa)     | myDdosAlert        |
    |Subskrypcja     | Wybierz subskrypcję, która zawiera publicznego adresu IP, które chcesz otrzymywać alerty dotyczące.        |
    |Grupa zasobów     |  Wybierz grupę zasobów, która zawiera publicznego adresu IP, które chcesz otrzymywać alerty dotyczące.       |
    |Zasób     |    Wybierz publiczny adres IP, który zawiera publicznego adresu IP, które chcesz otrzymywać alerty dotyczące. Przed atakami DDoS monitoruje publiczne adresy IP przypisane do zasobów w sieci wirtualnej. Jeśli nie masz żadnych zasobów z publicznych adresów IP w sieci wirtualnej, należy najpierw utworzyć zasobu z publicznym adresem IP. Można monitorować publicznego adresu IP wszystkie zasoby wdrażane za pomocą Menedżera zasobów (nie klasycznego) na liście [sieć wirtualną dla usług Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), z wyjątkiem środowiska usługi Azure App Service i Brama sieci VPN platformy Azure. Aby kontynuować pracę w tym samouczku, można szybko utworzyć [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maszyny wirtualnej.    |
    |Metryka     | W obszarze DDoS ataku lub nie |
    |Próg     |1 - **1** oznacza podlegają ataku. **0** oznacza, że nie jesteś zaatakowane.     |
    |Okres     | Wybierz niezależnie od wartości, możesz wybrać. |
    |Powiadomienia pocztą E-mail     |  Zaznacz pole wyboru       |
    |Dodatkowe administratora | Wprowadź adres e-mail, jeśli nie masz adres e-mail właściciela, współautora lub czytelnika subskrypcji.|

    W ciągu kilku minut wykrywanie ataków otrzymasz wiadomość e-mail z metryki Azure Monitor, która wygląda podobnie do poniższej ilustracji:

    ![Alert ataku](./media/manage-ddos-protection/ddos-alert.png)


Aby symulować takiego ataku, aby sprawdzić poprawność alertu, zobacz [wykrywania zweryfikować DDoS](#validate-ddos-detection).

Możesz także dowiedzieć się więcej [konfigurowaniu elementów webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [aplikacje logiki](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do tworzenia alertów.

## <a name="configure-logging-for-ddos-protection-metrics"></a>Konfigurowanie rejestrowania dla metryki ochrony przed atakami DDoS

1. Wybierz **wszystkie usługi** u góry po lewej portalu.
2. Wprowadź *Monitor* w **filtru** pole. Gdy **Monitor** pojawia się w wynikach, wybierz go.
3. W obszarze **ustawienia**, wybierz pozycję **ustawień diagnostycznych**.
4. Wybierz **subskrypcji** i **grupy zasobów** zawierające publiczny adres IP, które mają być rejestrowane.
5. Wybierz **publicznego adresu IP** dla **typu zasobu**, następnie wybierz określony publiczny adres IP należy rejestrować metryki.
6. Wybierz **Włącz diagnostykę, aby zbierać dane następujących** , a następnie wybrać dowolną liczbę następujące opcje, ile potrzebujesz:

    - **Archiwum na konto magazynu**: dane są zapisywane na koncie usługi Magazyn Azure. Aby dowiedzieć się więcej na temat tej opcji, zobacz [archiwum dzienników diagnostycznych](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Strumień do Centrum zdarzeń**: umożliwia odbiornik dziennika do pobrania dzienników przy użyciu usługi Azure Event Hub. Centra zdarzeń Włącz integrację z Splunk lub innych systemów SIEM. Aby dowiedzieć się więcej na temat tej opcji, zobacz [strumienia dzienników diagnostycznych do Centrum zdarzeń](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Wyślij do analizy dzienników**: zapisuje dzienniki z usługą Analiza dzienników Azure OMS. Aby dowiedzieć się więcej na temat tej opcji, zobacz [zbierania dzienników do użycia w analizy dzienników](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Aby symulować takiego ataku, aby sprawdzić poprawność rejestrowania, zobacz [wykrywania zweryfikować DDoS](#validate-ddos-detection).

## <a name="use-ddos-protection-telemetry"></a>Użyj telemetrii ochrona przed atakami DDoS

Dane telemetryczne dotyczące ataku jest zapewniana za pomocą monitora Azure w czasie rzeczywistym. Dane telemetryczne jest dostępna tylko na czas trwania, która publicznego adresu IP podlega środki zaradcze. Nie widzisz dane telemetryczne przed lub po skuteczność została osłabiona atak.

1. Wybierz **wszystkie usługi** u góry po lewej portalu.
2. Wprowadź *Monitor* w **filtru** pole. Gdy **Monitor** pojawia się w wynikach, wybierz go.
3. Wybierz **metryki**w obszarze **wspólne usługi**.
4. Wybierz **subskrypcji** i **grupy zasobów** zawierające publiczny adres IP, który ma dane telemetryczne dla.
5. Wybierz **publicznego adresu IP** dla **typu zasobu**, następnie wybierz określony publiczny adres IP ma dane telemetryczne dotyczące.
6. Szereg **dostępne metryki** są wyświetlane po lewej stronie ekranu. Te metryki, gdy zaznaczone, są wyświetlone na wykresie w **Azure Monitor metryki wykresu** na ekran Przegląd.

Nazwy metryki ona różnych typów pakietów i bajtów a pakiety, podstawowe konstrukcja nazw tagów na wszystkie metryki w następujący sposób:

- **Nazwa tagu porzucone** (na przykład **ruchu przychodzącego DDoS porzuconych pakietów**): Liczba porzuconych/wyczyszczona przez system ochrony przed atakami DDoS pakietów.
- **Nazwa tagu przekazane** (na przykład **przychodzących pakietów przekazywanych DDoS**): liczba pakietów przekazywanych przez system DDoS do docelowego adresu VIP — ruch, który nie został przefiltrowany.
- **Brak nazwy tagu** (na przykład **przed atakami DDoS pakiety przychodzące**): Całkowita liczba pakietów, które są dołączone do systemu kontrola — reprezentujący sumę pakiety porzucony i zostały przekazane.

Aby symulować takiego ataku, aby sprawdzić poprawność danych telemetrii, zobacz [wykrywania zweryfikować DDoS](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Przeglądanie DDoS ograniczenia zasad

Ochrona przed atakami DDoS — standardowe stosuje trzy zasady automatycznie dopasowane środki zaradcze (TCP SYN, TCP i UDP) dla każdego publicznego adresu IP chronionych zasobów w sieci wirtualnej, które ma włączone przed atakami DDoS. Progi zasad można wyświetlić, wybierając **pakiety przychodzące TCP do wyzwolenia ograniczenie DDoS** i **pakietów UDP ruchu przychodzącego do wyzwolenia ograniczenie DDoS** metryki, jak pokazano na poniższej ilustracji:

![Wyświetl zasady środki zaradcze](./media/manage-ddos-protection/view-mitigation-policies.png)

Progi zasad są konfigurowane za pomocą usługi Azure machine learning sieci ruchu profilowania. Tylko wtedy, gdy naruszenia progu zasad jest ograniczenie DDoS realizowana dla adresu IP w obszarze ataku.

## <a name="validate-ddos-detection"></a>Sprawdź poprawność DDoS wykrywania

Microsoft współpracuje z [chmury firmy BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) do tworzenia interfejsu, których można wygenerować ruchu względem włączona ochrona przed atakami DDoS symulacje publicznych adresów IP. Symulacji punktu przerwania chmury umożliwia:

- Sprawdź poprawność sposób ochrona przed atakami DDoS Microsoft Azure przed atakami DDoS ochrony zasobów na platformie Azure
- Optymalizacja procesu odpowiedzi na zdarzenia w obszarze ataków DDoS
- Zgodność DDoS dokumentu
- Uczenia zespołów zabezpieczeń sieci