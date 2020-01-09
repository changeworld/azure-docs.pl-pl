---
title: Ochrona maszyn i aplikacji
description: Tym dokumencie przedstawiono zalecenia w usłudze Security Center, które ułatwiają ochronę maszyn wirtualnych i komputerów i aplikacji sieci web i środowisk usługi App Service.
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
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: b7e5b0286cdd15834b84e4fd3e619c6555054823
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553005"
---
# <a name="protect-your-machines-and-applications"></a>Ochrona maszyn i aplikacji
Gdy Azure Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek do zabezpieczania i zabezpieczania zasobów.

W tym artykule opisano stronę **obliczenia i aplikacje** w sekcji zabezpieczenia zasobów Security Center.

Aby zapoznać się z pełną listą zaleceń, które mogą zostać wyświetlone na tej stronie, zobacz [zalecenia dotyczące danych i magazynu](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Wyświetlanie zabezpieczeń zasobów obliczeniowych i aplikacji

[Pulpit nawigacyjny Security Center ![](./media/security-center-virtual-machine-recommendations/overview.png)](./media/security-center-virtual-machine-recommendations/overview.png#lightbox)

Aby wyświetlić stan zasobów obliczeniowych i aplikacji, wybierz pozycję **oblicz & aplikacje** w obszarze **zasoby** na pasku bocznym Security Center. Dostępne są następujące karty:

* **Przegląd**: zawiera listę zaleceń dotyczących wszystkich zasobów obliczeniowych i aplikacji, a także ich bieżący stan zabezpieczeń 

* [**Maszyny wirtualne i komputery**](#vms-and-computers): zawiera listę zaleceń dotyczących maszyn wirtualnych, komputerów i bieżącego stanu zabezpieczeń każdego

* [**VM Scale Sets**](#vmscale-sets): zawiera listę zaleceń dla zestawów skalowania, 

* [**Cloud Services**](#cloud-services): zawiera listę zaleceń dotyczących ról sieci Web i procesów roboczych monitorowanych przez Security Center

* [**App Services**](#app-services): zawiera listę zaleceń dla środowisk usługi App Service i bieżący stan zabezpieczeń każdego

* **Kontenery**: zawiera listę zaleceń dotyczących kontenerów i oceny zabezpieczeń ich konfiguracji

* **Zasoby obliczeniowe**: zawiera listę zaleceń dotyczących zasobów obliczeniowych, takich jak klastry Service Fabric i centra zdarzeń

### <a name="whats-in-each-tab"></a>Co znajduje się na każdej karcie?

Każda karta zawiera wiele sekcji, a w każdej sekcji można przejść do szczegółów, aby wyświetlić dodatkowe szczegóły dotyczące wyświetlanego elementu.

Na każdej karcie zobaczysz również zalecenia dotyczące odpowiednich zasobów w monitorowanym środowisku. Pierwsza kolumna zawiera listę zaleceń, drugi pokazuje łączną liczbę zasobów, a trzecia pokazuje ważność problemu.

Każdy zalecenie obejmuje zestaw akcji, które można wykonać po jej wybraniu. Na przykład w przypadku wybrania opcji **brakujące aktualizacje systemu**zostanie wyświetlona liczba maszyn wirtualnych i komputerów, na których brakuje poprawek, oraz ważność brakującej aktualizacji.

> [!NOTE]
> Zalecenia dotyczące zabezpieczeń są takie same jak te na stronie **zalecenia** , ale w tym miejscu są one filtrowane według wybranego typu zasobu. Aby uzyskać więcej informacji o sposobach rozwiązywania zaleceń, zobacz [implementowanie zaleceń dotyczących zabezpieczeń w Azure Security Center](security-center-recommendations.md).
>

### <a name="vms-and-computers"></a>Maszyny wirtualne i komputery
Sekcja maszyny wirtualne i komputery zawiera przegląd wszystkich zaleceń dotyczących zabezpieczeń dla maszyn wirtualnych i komputerów. Uwzględniono cztery typy maszyn:

![Komputer spoza platformy Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Komputer bez platformy Azure.

![Maszyna wirtualna platformy Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Maszyna wirtualna platformy Azure Resource Manager.

![Klasyczne maszyny Wirtualnej platformy Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Klasyczna maszyna wirtualna platformy Azure.

![Maszyny wirtualne wskazane w obszarze roboczym](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Maszyny wirtualne, które są identyfikowane tylko z obszaru roboczego będącego częścią przeglądanej subskrypcji. Obejmuje to maszyny wirtualne z innych subskrypcji, które są raportowane do obszaru roboczego w tej subskrypcji, oraz maszyny wirtualne, które zostały zainstalowane przy użyciu agenta Operations Manager bezpośredniego, i nie mają identyfikatora zasobu.

Ikona wyświetlana pod każdym zaleceniem pomaga szybko zidentyfikować maszyny Wirtualnej, a komputer, który wymaga uwagi oraz jakiego typu zalecenie ma do nich. Możesz również użyć filtrów, aby przeszukać listę według **typu zasobów** i według **ważności**.

Aby przejść do szczegółów zaleceń dotyczących zabezpieczeń dla każdej maszyny wirtualnej, kliknij maszynę wirtualną.
W tym miejscu wyświetlane szczegóły zabezpieczeń dotyczące maszyny Wirtualnej lub komputera. Na dole można zobaczyć zalecaną akcję i ważność poszczególnych problemów.

[![Cloud Services](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)

### <a name="cloud-services"></a>Cloud Services
W przypadku usług w chmurze zalecenie jest tworzone, gdy wersja systemu operacyjnego jest nieaktualna.

![Korzystaj z zalet usług w chmurze](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

W scenariuszu, w którym masz zalecenie, postępuj zgodnie z instrukcjami w zaleceniu, aby zaktualizować system operacyjny. Po udostępnieniu aktualizacji zostanie wystawiony alert (czerwony lub pomarańczowy — w zależności od wagi problemu). Aby uzyskać pełne wyjaśnienie tego zalecenia, kliknij pozycję **Aktualizuj wersję systemu operacyjnego** w kolumnie **Opis** .

### <a name="app-services"></a>App Services
Aby wyświetlić informacje o App Service, musisz mieć Security Center standardowej warstwy cenowej i włączyć App Service w ramach subskrypcji. Aby uzyskać instrukcje dotyczące włączania tej funkcji, zobacz [ochrona App Service przy użyciu Azure Security Center](security-center-app-services.md).


W obszarze **usług App services**Znajdź listę środowisk App service Environment i wykonywane podsumowanie kondycji opartego na ocenie usługi Security Center.

![Usługi aplikacji](./media/security-center-virtual-machine-recommendations/app-services.png)

Są wyświetlane trzy typy usług aplikacji:

![Środowiska usług aplikacji](./media/security-center-virtual-machine-recommendations/ase.png) Środowiska usług aplikacji

![Aplikacja internetowa](./media/security-center-virtual-machine-recommendations/web-app.png) Aplikacja internetowa

![Aplikacja funkcji](./media/security-center-virtual-machine-recommendations/function-app.png) Aplikacja funkcji

W przypadku wybrania aplikacji sieci Web zostanie otwarty widok podsumowania z trzema kartami:

   - **Zalecenia**: na podstawie ocen wykonanych przez Security Center zakończonych niepowodzeniem.
   - **Przekazano ocen**: Lista ocen wykonywane przez usługę Security Center, które przekazane.
   - **Oceny niedostępne**: Lista ocen, którzy nie uruchomili z powodu błędu lub zalecenie nie jest odpowiednie dla określonej usługi App service

   W obszarze **zalecenia** listę zaleceń dla wybranej aplikacji sieci web i ważność poszczególne zalecenia.

   ![Zalecenia dotyczące App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Wybierz zalecenie, aby wyświetlić opis zalecenia i listę zasobów w złej kondycji, zdrowe zasoby i niezeskanowane zasoby.

   - W kolumnie **przekazana oceny** jest wyświetlana lista zakończonych ocen. Ważność te oceny zawsze ma kolor zielony.

   - Wybierz ocenę przekazany z listy opis oceny, lista zasobów w złej kondycji i w dobrej kondycji i listę nieprzeskanowane zasoby. Ma kartę zasoby w złej kondycji, ale ta lista jest zawsze pusta, ponieważ przekazana oceny.

### <a name="vmscale-sets"></a>Zestawy skalowania maszyn wirtualnych
Security Center automatycznie wykrywa, czy masz zestawy skalowania i zaleca się zainstalowanie na nich Microsoft Monitoring Agent.

Aby zainstalować Microsoft Monitoring Agent: 

1. Wybierz zalecenie **Zainstaluj agenta monitorowania w zestawie skalowania maszyn wirtualnych**. Otrzymujesz listę niemonitorowanych zestawów skalowania.

1. Wybierz zestaw skalowania w złej kondycji. Postępuj zgodnie z instrukcjami, aby zainstalować agenta monitorowania przy użyciu istniejącego wypełnionego obszaru roboczego lub utworzyć nowy. Upewnij się, że ustawiono [warstwę cenową](security-center-pricing.md) obszaru roboczego, jeśli nie została ustawiona.

   ![Instalowanie MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Aby ustawić nowe zestawy skalowania tak, aby automatycznie instalowały Microsoft Monitoring Agent:
1. Przejdź do Azure Policy i kliknij pozycję **definicje**.

1. Wyszukaj zasady **wdróż log Analytics Agent dla systemu Windows Virtual Machine Scale Sets** i kliknij je.

1. Kliknij przycisk **Przypisz**.

1. Ustaw **zakres** i **obszar roboczy log Analytics** i kliknij przycisk **Przypisz**.

Jeśli chcesz ustawić wszystkie istniejące zestawy skalowania w celu zainstalowania Microsoft Monitoring Agent, w Azure Policy, przejdź do **korygowania** i Zastosuj istniejące zasady do istniejących zestawów skalowania.


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące artykuły:

* [Pełna lista referencyjna zaleceń dotyczących zabezpieczeń Azure Security Center](recommendations-reference.md)
* [Monitorowanie tożsamości i dostępu w usłudze Azure Security Center](security-center-identity-access.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
* [Ochrona usługi Azure SQL w usłudze Azure Security Center](security-center-sql-service-recommendations.md)