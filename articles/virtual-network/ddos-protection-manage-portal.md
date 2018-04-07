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
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 5cc03189124dbea56535af2fed84f5ca74aac6cd
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Zarządzanie Azure DDoS ochrony standardowe przy użyciu portalu Azure

Dowiedz się, jak włączyć i wyłączyć atakach typu protection service (DDoS) i użyj telemetrii, aby ograniczyć takiego ataku z standardowe ochrony przed atakami DDoS Azure. Standardowa ochrony przed atakami DDoS chroni zasobów platformy Azure, takich jak maszyny wirtualne, usługi równoważenia obciążenia i bram aplikacji, które mają Azure [publicznego adresu IP](virtual-network-public-ip-address.md) przypisane do niej. Aby dowiedzieć się więcej na temat ochrony przed atakami DDoS — standardowe i jego możliwości, zobacz [standardowe ochrony przed atakami DDoS — omówienie](ddos-protection-overview.md). 

>[!IMPORTANT]
>Azure DDoS ochrony Standard (Ochrona przed atakami DDoS) jest obecnie w wersji zapoznawczej. Ochrona przed atakami DDoS obsługują ograniczoną liczbę zasobów platformy Azure i jest dostępna tylko w niektórych regionach. Aby uzyskać listę dostępnych regionów, zobacz [standardowe ochrony przed atakami DDoS — omówienie](ddos-protection-overview.md). Musisz [zarejestrować usługi](http://aka.ms/ddosprotection) ograniczone wersji zapoznawczej można pobrać ochrona przed atakami DDoS włączona dla Twojej subskrypcji. Po zarejestrowaniu, otrzymasz wiadomość przez zespół Azure DDoS, który przeprowadzi Cię przez proces aktywacji. 

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>Włącz Standard ochrony przed atakami DDoS — nowej sieci wirtualnej

1. Zaloguj się do witryny Azure Portal na stronie http://portal.azure.com. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
2. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
3. Wybierz **sieci**, a następnie wybierz **sieci wirtualnej**.
4. Tworzenie sieci wirtualnej z wybranego ustawienia. Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnych, zobacz [utworzyć sieć wirtualną](manage-virtual-network.md#create-a-virtual-network). W obszarze **ochrona przed atakami DDoS**, kliknij przycisk **włączone**, a następnie kliknij przycisk **Utwórz**. Jeśli nie widzisz **ochrona przed atakami DDoS**, prawdopodobną przyczyną jest, że subskrypcja nie jest zarejestrowany dla funkcji. Należy wykonać [rejestracji](http://aka.ms/ddosprotection), otrzymasz powiadomienie, które subskrypcji została włączona dla funkcji, przed **ochrona przed atakami DDoS** pojawi się.

    ![Tworzenie sieci wirtualnej](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

    > [!WARNING]
    > Po wybraniu regionu, wybierz region obsługiwanych z listy w [standardowe ochrony przed atakami DDoS Azure — omówienie](ddos-protection-overview.md). Jeśli nie wybierzesz obsługiwanym regionie, tworzenie sieci wirtualnej nie powiedzie się.

    Ostrzeżenie informuje, że włączenie ochrony przed atakami DDoS zostaną naliczone opłaty powiązane. Nie ochrony przed atakami DDoS jest obciążany wersji zapoznawczej. Opłaty będą naliczane ogólnie dostępna. Otrzymasz powiadomienie 30 dni, przed rozpoczęciem opłat i ogólnej dostępności.

## <a name="enable-ddos-protection-standard---existing-virtual-network"></a>Włącz Standard ochrony przed atakami DDoS — istniejącej sieci wirtualnej 

1. Kliknij przycisk **sieci wirtualnych** w menu portalu Azure, a następnie wybierz sieci wirtualnej.
2. Kliknij przycisk **ochrona przed atakami DDoS**, kliknij przycisk **włączone** na *ochrona przed atakami DDoS* ekranu, a następnie kliknij przycisk **zapisać**. Jeśli nie widzisz **ochrona przed atakami DDoS**, prawdopodobną przyczyną jest, że subskrypcja nie jest zarejestrowany dla funkcji. Należy wykonać [rejestracji](http://aka.ms/ddosprotection), otrzymasz powiadomienie, które subskrypcji została włączona dla funkcji, przed **ochrona przed atakami DDoS** pojawi się. 

    > [!WARNING]
    > Sieć wirtualna musi istnieć w obsługiwanym regionie. Aby uzyskać listę obsługiwanych regionów, zobacz [standardowe ochrony przed atakami DDoS Azure — omówienie](ddos-protection-overview.md).

    Ostrzeżenie informuje, że włączenie ochrony przed atakami DDoS zostaną naliczone opłaty powiązane. Nie ochrony przed atakami DDoS jest obciążany wersji zapoznawczej. Opłaty będą naliczane ogólnie dostępna. Otrzymasz powiadomienie 30 dni, przed rozpoczęciem opłat i ogólnej dostępności.

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Wyłącz ochronę przed atakami DDoS w sieci wirtualnej

1. Kliknij przycisk **sieci wirtualnych** w menu portalu Azure, a następnie wybierz sieci wirtualnej.
2. Kliknij przycisk **ochrona przed atakami DDoS**, kliknij przycisk **wyłączone** na *ochrona przed atakami DDoS* ekranu, a następnie kliknij przycisk **zapisać**.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>Skonfiguruj alerty na metryki ochrona przed atakami DDoS

Można wybrać jedną z dostępnymi metrykami ochrona przed atakami DDoS alertu, jeśli istnieje aktywne środki zaradcze podczas atak przy użyciu konfiguracji alertu Azure Monitor. Po spełnieniu warunków określony adres otrzymuje wiadomość e-mail alertów.

1. Kliknij przycisk **Monitor**, a następnie kliknij przycisk **metryki**.
2. Na *metryki* ekranu, wybierz grupę zasobów, a typ zasobu **publicznego adresu IP**i Azure publicznego adresu IP.
3. Kliknij, aby skonfigurować alerty e-mail dla metryki **Dodaj alert metryki**. Alerty w wiadomościach e-mail mogą być tworzone na wszystkie metryki, ale jest najbardziej oczywisty Metryka **DDoS w ramach ataku lub nie**. Jest to wartość logiczna, 1 lub 0. A **1** oznacza podlegają ataku. A **0** oznacza, że nie jesteś zaatakowane.
4. W celu przesłania pocztą e-mail w ramach ataku, ustawienia metryki dla **DDoS w ramach ataku lub nie** i **warunek na wartość większą niż zero (0) w ciągu ostatnich 5 minut**. Podobne alerty można skonfigurować dla innych metryk.

    ![Konfigurowanie metryk](./media/ddos-protection-manage-portal/ddos-metrics.png)

    W ciągu kilku minut wykrywanie ataków użytkownik jest powiadamiany, przy użyciu metryk Azure Monitor.

    ![Alert ataku](./media/ddos-protection-manage-portal/ddos-alert.png) 

Możesz także dowiedzieć się więcej [konfigurowaniu elementów webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) i [aplikacje logiki](../logic-apps/logic-apps-overview.md) do tworzenia alertów.

## <a name="configure-logging-on-ddos-protection-standard-metrics"></a>Konfiguruj rejestrowanie na standardowe ochrony przed atakami DDoS metryk

1. Kliknij przycisk **Monitor**, a następnie kliknij przycisk **ustawień diagnostycznych**.
2. Na *metryki* ekranu, wybierz grupę zasobów, a typ zasobu **publicznego adresu IP**i Azure publicznego adresu IP.
3. Kliknij przycisk **Włącz diagnostykę, aby zbierać dane następujących**.

Rejestrowanie dostępne są trzy opcje:

- **Archiwum na konto magazynu**: zapisuje dzienniki na koncie magazynu.
- **Strumień do Centrum zdarzeń**: umożliwia odbiornik dziennika do pobrania dzienników przy użyciu Centrum zdarzeń. To umożliwia integrację z Splunk lub innych systemów SIEM.
- **Wyślij do analizy dzienników**: zapisuje dzienniki z usługą Analiza dzienników Azure.

## <a name="use-ddos-protection-telemetry"></a>Użyj telemetrii ochrona przed atakami DDoS

Dane telemetryczne dotyczące ataku jest zapewniana za pomocą monitora Azure w czasie rzeczywistym. Dane telemetryczne jest dostępna tylko na czas trwania, która publicznego adresu IP podlega środki zaradcze. Dane telemetryczne nie będą widzieć przed lub po skuteczność została osłabiona atak.

1. Kliknij przycisk **Monitor**, a następnie kliknij przycisk **metryki**. 
2. Na *metryki* ekranu, wybierz grupę zasobów, a typ zasobu **publicznego adresu IP**i Azure publicznego adresu IP. Szereg **dostępne metryki** pojawia się po lewej stronie ekranu. Te metryki, gdy zaznaczone, są wyświetlone na wykresie w **Azure Monitor metryki wykresu** na ekran Przegląd. 

Nazwy metryki ona różnych typów pakietów i bajtów a pakiety, podstawowe konstrukcja nazw tagów na wszystkie metryki w następujący sposób:

- **Nazwa tagu porzucone (np. liczba przychodzących pakietów porzucony DDoS)**: Liczba porzuconych/wyczyszczona przez system ochrony przed atakami DDoS pakietów.
- **Nazwa tagu przekazane (np.: przychodzących pakietów przekazywanych DDoS)**: liczba pakietów przekazywanych przez system DDoS do docelowego adresu VIP — ruch, który nie został przefiltrowany.
- **Brak nazwy tagu (np.: przed atakami DDoS pakietów ruchu przychodzącego):** całkowita liczba pakietów, które są dołączone do systemu kontrola — reprezentujący sumę pakiety porzucony i zostały przekazane.

## <a name="next-steps"></a>Kolejne kroki

- [Więcej informacji na temat dzienników diagnostycznych platformy Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Analizowanie dzienników z usługi Azure storage z analizy dzienników](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)