---
title: Chroń swoje maszyny i aplikacje
description: Ten dokument zawiera zalecenia w usłudze Security Center, które ułatwiają ochronę maszyn wirtualnych i komputerów oraz aplikacji sieci web i środowisk usługi App Service.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: 93dde2e873a00303dcb8563caed4d56dbf11cc12
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435635"
---
# <a name="protect-your-machines-and-applications"></a>Chroń swoje maszyny i aplikacje
Gdy usługa Azure Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które prowadzą użytkownika przez proces konfigurowania potrzebnych formantów w celu wzmocnienia i ochrony zasobów.

W tym **artykule** opisano stronę Obliczenia i aplikacje w sekcji zabezpieczeń zasobów usługi Security Center.

Aby uzyskać pełną listę zaleceń, które można znaleźć na tej stronie, zobacz [Zalecenia dotyczące obliczeń i aplikacji](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Wyświetlanie zabezpieczeń zasobów obliczeniowych i aplikacji

[![Pulpit nawigacyjny usługi Security Center](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

Aby wyświetlić stan zasobów obliczeniowych i aplikacji, z lewego okienka w u centrum zabezpieczeń wybierz pozycję **Oblicz & aplikacje**. Dostępne są następujące karty:

* **Omówienie**: wyświetla listę zaleceń dotyczących wszystkich zasobów obliczeniowych i aplikacji, a także ich bieżący stan zabezpieczeń 

* [**Maszyny wirtualne i serwery:**](#vms-and-computers)wyświetla listę zaleceń dotyczących maszyn wirtualnych, komputerów i bieżącego stanu zabezpieczeń każdego

* [**Zestawy skalowania maszyn wirtualnych:**](#vmscale-sets)wyświetla zalecenia dotyczące zestawów skalowania, 

* [**Usługi w chmurze:**](#cloud-services)wyświetla listę zaleceń dotyczących ról internetowych i pracowników monitorowanych przez usługę Security Center

* [**Usługi aplikacji:**](#app-services)wyświetla zalecenia dotyczące środowisk usługi aplikacji i bieżący stan zabezpieczeń każdego

* [**Kontenery:**](#containers)zawiera listę zaleceń dotyczących kontenerów i oceny bezpieczeństwa ich konfiguracji

* **Zasoby obliczeniowe:** wyświetla listę zaleceń dotyczących zasobów obliczeniowych, takich jak klastry sieci szkieletowej usług i centra zdarzeń

### <a name="whats-in-each-tab"></a>Co jest w każdej karcie?

Każda karta ma wiele sekcji, a w każdej sekcji można przejść do szczegółów, aby wyświetlić dodatkowe szczegóły dotyczące wyświetlanego elementu.

Na każdej karcie zostaną wyświetlone również zalecenia dotyczące odpowiednich zasobów w monitorowanym środowisku. Pierwsza kolumna zawiera listę zaleceń, druga pokazuje całkowitą liczbę zasobów, których dotyczy problem, a trzecia pokazuje ważność problemu.

Każde zalecenie ma zestaw akcji, które można wykonać po wybraniu go. Na przykład, jeśli wybierzesz **Brak aktualizacji systemu,** liczba maszyn wirtualnych i komputerów, na których brakuje poprawek, a także zostanie wyświetleń ważności brakującej aktualizacji.

> [!NOTE]
> Zalecenia dotyczące zabezpieczeń są takie same jak zalecenia na stronie **Zalecenia,** ale w tym miejscu są filtrowane do określonego typu zasobu, który został wybrany. Aby uzyskać więcej informacji na temat rozwiązywania zaleceń, zobacz [Implementowanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>Maszyny wirtualne i serwery
Sekcja Maszyny wirtualne i komputery zawiera omówienie wszystkich zaleceń dotyczących zabezpieczeń dla maszyn wirtualnych i komputerów. W zestawie znajdują się cztery typy maszyn:

![Komputer spoza platformy Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Komputer bez platformy Azure.

![Maszyna wirtualna usługi Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Maszyna wirtualna usługi Azure Resource Manager.

![Klasyczna maszyna wirtualna platformy Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Klasyczna maszyna wirtualna platformy Azure.

![Maszyny wirtualne zidentyfikowane z obszaru roboczego](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Maszyny wirtualne, które są identyfikowane tylko z obszaru roboczego będącego częścią przeglądanej subskrypcji. Obejmuje to maszyny wirtualne z innych subskrypcji, które raportują do obszaru roboczego w tej subskrypcji i maszyn wirtualnych, które zostały zainstalowane z agentem bezpośrednim programu Operations Manager i nie mają identyfikatora zasobu.

Ikona, która pojawia się w ramach każdego zalecenia pomaga szybko zidentyfikować maszynę wirtualną i komputer, który wymaga uwagi i typ zalecenia. Za pomocą filtrów można również przeszukiwać listę według **typu zasobu** i **ważności**.

Aby przejść do szczegółów zaleceń dotyczących zabezpieczeń dla każdej maszyny Wirtualnej, kliknij maszynę wirtualną.
W tym miejscu są widoczne szczegóły zabezpieczeń maszyny Wirtualnej lub komputera. Na dole widać zalecane działanie i ważność każdego problemu.

[![Usługi w chmurze](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>Zestawy skalowania maszyn wirtualnych
Usługa Security Center automatycznie wykrywa, czy masz zestawy skalowania i zaleca zainstalowanie na nich agenta usługi Log Analytics.

Aby zainstalować agenta usługi Log Analytics: 

1. Wybierz zalecenie **Zainstaluj agenta monitorowania w zestawie skalowania maszyny wirtualnej**. Otrzymujesz listę niemonitorowanych zestawów skalowania.

1. Wybierz zestaw skalnej w złej kondycji. Postępuj zgodnie z instrukcjami, aby zainstalować agenta monitorowania przy użyciu istniejącego wypełnionego obszaru roboczego lub utworzyć nowy. Upewnij się, że [ustawisz warstwę cenową](security-center-pricing.md) obszaru roboczego, jeśli nie jest ustawiona.

   ![Instalowanie wiadomości MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Aby ustawić nowe zestawy skalowania, aby automatycznie instalować agenta usługi Log Analytics:
1. Przejdź do witryny Zasad platformy Azure i kliknij pozycję **Definicje**.

1. Wyszukaj zasady **Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyny wirtualnej systemu Windows** i kliknij na niego.

1. Kliknij przycisk **Przypisz**.

1. Ustaw obszar **roboczy** **Zakres** i usługa Log Analytics i kliknij przycisk **Przypisz**.

Jeśli chcesz ustawić wszystkie istniejące zestawy skalowania, aby zainstalować agenta usługi Log Analytics, w usłudze Azure Policy przejdź do **korygowania** i zastosuj istniejące zasady do istniejących zestawów skalowania.





### <a name="cloud-services"></a><a name="cloud-services"></a>Usługi w chmurze
W przypadku usług w chmurze zalecenie jest tworzone, gdy wersja systemu operacyjnego jest nieaktualna.

![Usługi w chmurze](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

W scenariuszu, w którym masz zalecenie, wykonaj kroki opisane w zaleceniu, aby zaktualizować system operacyjny. Gdy aktualizacja jest dostępna, będziesz mieć alert (czerwony lub pomarańczowy - w zależności od ważności problemu). Aby uzyskać pełne wyjaśnienie tego zalecenia, kliknij pozycję **Aktualizuj wersję systemu operacyjnego** w kolumnie **OPIS.**






### <a name="app-services"></a><a name="app-services"></a>Usługi aplikacji
Aby wyświetlić informacje usługi app service, musisz być w standardowej warstwie cenowej usługi Security Center i włączyć usługę App Service w ramach subskrypcji. Aby uzyskać instrukcje dotyczące włączania tej funkcji, zobacz [Ochrona usługi App Service za pomocą usługi Azure Security Center](security-center-app-services.md).

W obszarze **Usługi aplikacji**znajdziesz listę środowisk usługi aplikacji i podsumowanie kondycji na podstawie przeprowadzonej oceny Centrum zabezpieczeń.

![Usługi aplikacji](./media/security-center-virtual-machine-recommendations/app-services.png)

Pokazano trzy typy usług aplikacji:

![Środowisko usług aplikacji](./media/security-center-virtual-machine-recommendations/ase.png) Środowisko usług aplikacji

![Aplikacja internetowa](./media/security-center-virtual-machine-recommendations/web-app.png) Aplikacja internetowa

![Aplikacja funkcyjna](./media/security-center-virtual-machine-recommendations/function-app.png) Aplikacja funkcyjna

Jeśli wybierzesz aplikację sieci Web, zostanie otwarty widok podsumowania z trzema kartami:

   - **Zalecenia:** na podstawie ocen przeprowadzonych przez centrum zabezpieczeń, które nie powiodły się.
   - **Zdanych ocen:** lista ocen przeprowadzonych przez Centrum zabezpieczeń, które przeszły.
   - **Oceny niedostępne:** lista ocen, które nie zostały uruchomione z powodu błędu lub zalecenie nie jest istotne dla konkretnej usługi aplikacji

   W obszarze **Zalecenia** znajduje się lista zaleceń dla wybranej aplikacji sieci web i ważność każdego zalecenia.

   ![Zalecenia dotyczące usług aplikacji](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Wybierz zalecenie, aby wyświetlić opis zalecenia i listę zasobów w złej kondycji, zdrowych zasobów i nieskanowanych zasobów.

   - Kolumna **Przejasce ocen** zawiera listę zdanych ocen. Dotkliwość tych ocen jest zawsze zielona.

   - Wybierz z listy zdań ocenę opis oceny, listę zasobów w złej kondycji i dobrej kondycji oraz listę nieskanowanych zasobów. Istnieje karta dla zasobów w złej kondycji, ale ta lista jest zawsze pusta od czasu wykonania oceny.





### <a name="containers"></a><a name="containers"></a>Kontenery

Po otwarciu **kontenerów** kartę, w zależności od środowiska, może być widoczny dowolny z trzech typów zasobów:

![Host kontenera](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Hosty kontenerów — maszyny wirtualne z systemem docker 

![Usługa Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Klastry usługi Azure Kubernetes (AKS)

![Rejestr kontenerów](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Rejestry usługi Azure Container Registry (ACR) — wyświetlane tylko wtedy, gdy korzystasz ze standardowej warstwy cenowej i gdy masz włączony pakiet rejestru kontenerów platformy Azure.

Aby uzyskać instrukcje dotyczące korzystania z funkcji zabezpieczeń kontenera, zobacz [Monitorowanie zabezpieczeń kontenerów](monitor-container-security.md).

Korzyści z pakietu rejestru kontenerów platformy Azure są wyjaśnione [tutaj](azure-container-registry-integration.md)

Korzyści z pakietu Usług Kubernetes są wyjaśnione [tutaj](azure-kubernetes-service-integration.md)

[![Karta Kontenery](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Aby wyświetlić zalecenia dotyczące określonego zasobu na liście, kliknij ten zasób.

#### <a name="visibility-into-container-registries"></a>Wgląd w rejestry kontenerów

Na przykład kliknięcie rejestru asc-demo ACR z listy pokazanej na powyższej grafice prowadzi do tej strony szczegółów:

[![Zalecenia dotyczące określonego rejestru ACR](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Wgląd w kontenery hostowane na komputerach IaaS Linux

Po kliknięciu jednej z maszyn wirtualnych z systemem docker, zobaczysz stronę szczegółów z informacjami dotyczącymi kontenerów na komputerze, takich jak wersja platformy Docker i liczba obrazów uruchomionych na hoście.

![Zalecenia dotyczące platformy dokowej z uruchomieniem maszyny Wirtualnej](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Zalecenia dotyczące zabezpieczeń oparte na benchmarku CIS dla platformy Docker

Usługa Security Center skanuje konfiguracje platformy Docker i zapewnia wgląd w błędy konfiguracji poprzez dostarczenie listy wszystkich ocenionych reguł zakończonych niepowodzeniem. Usługa Security Center zawiera wskazówki ułatwiające szybkie rozwiązywanie tych problemów i oszczędność czasu. Usługa Security Center stale ocenia konfiguracje platformy Docker i udostępnia Ci ich najnowszy stan.

![karta kontenera](./media/security-center-virtual-machine-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące artykuły:

* [Pełna lista odwołań do zaleceń dotyczących zabezpieczeń usługi Azure Security Center](recommendations-reference.md)
* [Monitorowanie tożsamości i dostępu w usłudze Azure Security Center](security-center-identity-access.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
* [Ochrona usługi SQL platformy Azure w usłudze Azure Security Center](security-center-sql-service-recommendations.md)