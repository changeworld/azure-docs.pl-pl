---
title: Centrum zabezpieczeń platformy migracji — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania odpowiedzi na pytania dotyczące migracji platform Centrum zabezpieczeń Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2018
ms.author: terrylan
ms.openlocfilehash: 6a88fbadd8fbf05a4942e42b535770f6f068af28
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527029"
---
# <a name="security-center-platform-migration-faq"></a>Centrum zabezpieczeń platformy migracji — często zadawane pytania
W wczesne 2017 czerwca Centrum zabezpieczeń Azure rozpoczęło się przy użyciu programu Microsoft Monitoring Agent do gromadzenia i przechowywania danych. Aby dowiedzieć się więcej, zobacz [migracji Platform Centrum zabezpieczeń Azure](security-center-platform-migration.md). Często zadawane pytania odpowiedzi na pytania dotyczące migracji platform.

## <a name="data-collection-agents-and-workspaces"></a>Zbieranie danych, agenci i obszary robocze

### <a name="how-is-data-collected"></a>Jak zbierane są dane?
Centrum zabezpieczeń używa programu Microsoft Monitoring Agent zbierania danych zabezpieczeń z maszyn wirtualnych. Dane zabezpieczeń zawiera informacje dotyczące następujących:

- konfiguracje zabezpieczeń — umożliwia identyfikowanie luk w zabezpieczeniach
- zdarzenia zabezpieczeń — używane do wykrywania zagrożeń

Dane zebrane przez agenta są przechowywane w istniejącym obszarem roboczym analizy dzienników podłączony do maszyny Wirtualnej lub nowy obszar roboczy utworzony przez Centrum zabezpieczeń. W Centrum zabezpieczeń tworzy nowy obszar roboczy, używanie funkcji geolokalizacji maszyny wirtualnej jest brana pod uwagę.

> [!NOTE]
> Microsoft Monitoring Agent jest tego samego agenta używany przez usługę Analiza dzienników i System Center Operations Manager (SCOM).
>
>

Włączenie automatycznego inicjowania obsługi administracyjnej (poprzednia nazwa zbierania dzienników) lub podczas migracji subskrypcji, Centrum zabezpieczeń sprawdza, czy program Microsoft Monitoring Agent jest już zainstalowany jako rozszerzenie Azure na każdym z maszyn wirtualnych. Jeśli nie zainstalowano programu Microsoft Monitoring Agent, następnie domyślnie Centrum zabezpieczeń będą:

- Na maszynie Wirtualnej, należy zainstalować rozszerzenie programu Microsoft Monitoring Agent.

   - Obszar roboczy utworzony przez Centrum zabezpieczeń już istnieje w tej samej używanie funkcji geolokalizacji jako maszyny Wirtualnej, agent jest podłączony do tego obszaru roboczego.
   - Obszar roboczy nie istnieje, Centrum zabezpieczeń tworzy nowy zasób grupy i domyślny obszar roboczy, w tym używanie funkcji geolokalizacji, a połączenie agenta z tego obszaru roboczego. Konwencja nazewnictwa dla grupy obszaru roboczego i zasobów jest:

       Obszar roboczy: DefaultWorkspace-[identyfikator subskrypcji]-[geograficznie]

       Grupa zasobów: DefaultResouceGroup-[geograficznie]

- Włącz rozwiązanie Centrum zabezpieczeń, w obszarze roboczym na Maszynie wirtualnej powiązanych warstwy cenowej w Centrum zabezpieczeń. Aby uzyskać więcej informacji o cenach, zobacz [cennik Centrum zabezpieczeń](https://azure.microsoft.com/pricing/details/security-center/).
- Dla subskrypcji na zmigrowanych Centrum zabezpieczeń spowoduje również usunięcie poprzednich agenta platformy Azure.

> [!NOTE]
> Można zastąpić automatyczną instalację programu Microsoft Monitoring Agent i użyć własnych obszaru roboczego.  Zobacz [jak zatrzymać agenta automatycznego tworzenia instalacji i obszar roboczy](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation) i [sposobu korzystania z istniejącym obszarem roboczym](#how-can-i-use-my-existing-log-analytics-workspace).
>
>

Lokalizacja obszaru roboczego jest oparta na lokalizacji maszyny wirtualnej. Aby dowiedzieć się więcej, zobacz [bezpieczeństwo danych](security-center-data-security.md). Jeśli subskrypcja zawiera maszyny wirtualne z wieloma geolocations, Centrum zabezpieczeń tworzy wiele obszarów roboczych. Wiele obszarów roboczych są tworzone do obsługi zasady zachowania poufności danych.

> [!NOTE]
> Przed migracją platformy Centrum zabezpieczeń zbierane dane z maszyn wirtualnych przy użyciu agenta monitorowania Azure, a dane są przechowywane na koncie magazynu. Po zakończeniu migracji platform Centrum zabezpieczeń korzysta z programu Microsoft Monitoring Agent obszaru roboczego do gromadzenia i przechowywania tych samych danych. Po zakończeniu migracji można usunąć konta magazynu.  Centrum zabezpieczeń spowoduje również usunięcie wcześniej zainstalowanych agentów monitorowania Azure po zakończeniu migracji platform.
>
>

### <a name="am-i-billed-for-log-analytics-on-the-workspaces-created-by-security-center"></a>Jestem rozliczane dla analizy dzienników na obszarów roboczych, utworzonych przez Centrum zabezpieczeń?
Nie. Obszary robocze tworzone przez Centrum zabezpieczeń, konfigurować dla analizy dzienników dla poszczególnych węzła rozliczeń, nie wiąże się z analizy dzienników opłatami. Rozliczeń Centrum zabezpieczeń jest zawsze na podstawie zasad zabezpieczeń Centrum zabezpieczeń i rozwiązań zainstalowanym obszaru roboczego:

- **Warstwa bezpłatna** — Centrum zabezpieczeń umożliwia rozwiązanie "SecurityCenterFree" na domyślny obszar roboczy. Nie są opłaty naliczane w warstwie bezpłatna.
- **Warstwy standardowa** — Centrum zabezpieczeń umożliwia rozwiązanie "Zabezpieczenia" na domyślny obszar roboczy.

Aby uzyskać więcej informacji o cenach, zobacz [cennik Centrum zabezpieczeń](https://azure.microsoft.com/pricing/details/security-center/). Stronie cen adresów zmiany do magazynu danych zabezpieczeń i proporcjonalnie rozliczeń począwszy od czerwca 2017 r.

> [!NOTE]
> Analiza dzienników cenowym obszarów roboczych utworzonych przez Centrum zabezpieczeń nie ma wpływu na rozliczenia Centrum zabezpieczeń.
>
>

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Co kwalifikuje się maszyny Wirtualnej do automatycznego inicjowania obsługi instalacji programu Microsoft Monitoring Agent?
Systemu Windows lub maszyn wirtualnych systemu Linux IaaS kwalifikuje się, jeśli:

- Rozszerzenia Microsoft Monitoring Agent nie jest obecnie zainstalowany na maszynie Wirtualnej.
- Maszyna wirtualna jest w stanie uruchomienia.
- Systemu Windows lub Linux VM Agent jest zainstalowany.
- Maszyna wirtualna nie jest używana jako urządzenie, takie jak Zapora aplikacji sieci web lub Zapora nowej generacji.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Czy można usunąć domyślnego obszarów roboczych, utworzonych przez Centrum zabezpieczeń?
**Usuwanie domyślny obszar roboczy nie jest zalecane.** Centrum zabezpieczeń używa domyślne obszary robocze do przechowywania danych zabezpieczeń z maszyn wirtualnych.  W przypadku usunięcia obszaru roboczego, Centrum zabezpieczeń nie będzie mógł zbierać dane i niektóre zalecenia dotyczące zabezpieczeń i alertów są niedostępne.

Aby odzyskać, Usuń program Microsoft Monitoring Agent na maszynach wirtualnych podłączone do usuniętego obszaru roboczego. Centrum zabezpieczeń ponownie instaluje agenta i tworzy nowy domyślny obszarów roboczych.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Jak używać Mój istniejący obszar roboczy analizy dzienników?

Możesz wybrać istniejący obszar roboczy analizy dzienników do przechowywania danych zbieranych przez Centrum zabezpieczeń. Aby użyć istniejącego obszaru roboczego analizy dzienników:

- Obszar roboczy musi być skojarzony z wybranej subskrypcji Azure.
- Co najmniej użytkownik musi mieć uprawnienia odczytu dostępu do obszaru roboczego.

Aby wybrać istniejący obszar roboczy analizy dzienników:

1. W obszarze **zasady zabezpieczeń — zbieranie danych**, wybierz pozycję **Użyj innego obszaru roboczego**.

   ![Użyj innego obszaru roboczego][5]

2. Z menu rozwijanego wybierz obszar roboczy do zapisywania zebranych danych.

   > [!NOTE]
   > W ściągania menu rozwijane są wyświetlane tylko obszarów roboczych, które mają dostęp do i znajdują się w ramach subskrypcji platformy Azure.
   >
   >

3. Wybierz pozycję **Zapisz**.
4. Po wybraniu **zapisać**, użytkownik zostanie poproszony, jeśli chcesz monitorować ponownej konfiguracji maszyn wirtualnych.

   - Wybierz **nr** Jeśli chcesz, aby nowe ustawienia obszaru roboczego **zastosować na nowych maszynach wirtualnych tylko**. Nowe ustawienia obszaru roboczego dotyczą tylko nowe instalacje agentów; nowo wykryte maszyny wirtualne, które nie mają zainstalowany program Microsoft Monitoring Agent.
   - Wybierz **tak** Jeśli chcesz, aby nowe ustawienia obszaru roboczego **zastosować na wszystkich maszynach wirtualnych**. Ponadto każdej maszyny Wirtualnej podłączone do Centrum zabezpieczeń, obszar roboczy utworzony ponownie nawiązał połączenie nowy docelowy obszar roboczy.

   > [!NOTE]
   > Jeśli wybierzesz tak, nie można usuwać obszarów roboczych, utworzonych przez Centrum zabezpieczeń, dopóki wszystkie maszyny wirtualne mają został ponownie podłączony do nowego docelowego obszaru roboczego. Ta operacja nie powiedzie się, zbyt wcześnie usuniętego obszaru roboczego.
   >
   >

   - Wybierz **anulować** Aby anulować operację.

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Co w przypadku programu Microsoft Monitoring Agent została już zainstalowana jako rozszerzenie na maszynie Wirtualnej?
Centrum zabezpieczeń nie zastępują istniejące połączenia do użytkownika obszarów roboczych. Centrum zabezpieczeń przechowuje dane zabezpieczeń z maszyny Wirtualnej w obszarze roboczym już połączone. Centrum zabezpieczeń aktualizuje wersję rozszerzenia, aby uwzględnić identyfikator maszyny Wirtualnej do obsługi użycia Centrum zabezpieczeń zasobów platformy Azure.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>Co zrobić, jeśli użytkownik miał Microsoft Monitoring Agent zainstalowany na komputerze, ale nie jako rozszerzenie?
Po zainstalowaniu programu Microsoft Monitoring Agent bezpośrednio na Maszynie wirtualnej (nie jako rozszerzenie Azure) Centrum zabezpieczeń nie instaluje program Microsoft Monitoring Agent oraz monitorowanie zabezpieczeń jest ograniczony.

Aby uzyskać więcej informacji, zobacz następną sekcję [co się stanie w przypadku SCOM lub OMS bezpośrednie agent został już zainstalowany na maszynie Wirtualnej?](security-center-platform-migration-faq.md#what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm)

### <a name="what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm"></a>Co się stanie w przypadku SCOM lub OMS bezpośrednie agent jest już zainstalowana na maszynie Wirtualnej?
Centrum zabezpieczeń nie może zidentyfikować wcześniej zainstalowano agenta.  Centrum zabezpieczeń próbuje zainstalować rozszerzenie programu Microsoft Monitoring Agent i kończy się niepowodzeniem z powodu istniejących zainstalowanego agenta.  Ten błąd uniemożliwia Zastępowanie ustawienia połączenia agenta do obszaru roboczego i pozwala uniknąć tworzenia wielu.

> [!NOTE]
> Wersja agenta zostało zaktualizowane do najnowszej wersji agenta pakietu OMS.  Dotyczy to SCOM użytkowników również.
>
>

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Jaki jest wpływ usunięcia tych rozszerzeń?
Jeśli usuniesz rozszerzenia monitorowania firmy Microsoft, Centrum zabezpieczeń nie będzie mógł zbierać dane zabezpieczeń z maszyny Wirtualnej, a niektóre zalecenia dotyczące zabezpieczeń i alertów są niedostępne. W ciągu 24 godzin Centrum zabezpieczeń określa, że maszyna wirtualna nie ma rozszerzenia i ponownie instaluje rozszerzenia.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Jak zatrzymać agenta automatycznego tworzenia instalacji i obszar roboczy?
Możesz wyłączyć automatyczne Inicjowanie obsługi administracyjnej dla subskrypcji w zasadach zabezpieczeń, ale nie jest to zalecane. Wyłączenie automatycznego inicjowania obsługi administracyjnej limity zaleceń Centrum zabezpieczeń i alertów. Automatyczne inicjowanie obsługi administracyjnej jest wymagane dla subskrypcji na warstwa cenowa standardowa. Aby wyłączyć automatyczne Inicjowanie obsługi administracyjnej:

1. Jeśli subskrypcja jest skonfigurowany do warstwy standardowa, otworzyć przystawkę Zasady zabezpieczeń dla tej subskrypcji i wybierz **wolne** warstwy.

   ![Warstwa cenowa][1]

2. Następnie wyłącz automatyczne udostępnianie wybierając **poza** na **zasady zabezpieczeń — zbieranie danych** bloku.
   ![Zbieranie danych][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Należy zrezygnować z agentami automatycznymi instalacji i tworzenie obszaru roboczego?

> [!NOTE]
> Należy przejrzeć sekcje [jakie są skutki rezygnację?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) i [zalecane kroki w przypadku rezygnację](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) Jeśli chcesz zrezygnować z automatycznego inicjowania obsługi administracyjnej.
>
>

Można zrezygnować z automatycznego inicjowania obsługi administracyjnej, jeśli poniższe informacje dotyczą możesz:

- Instalacja agenta automatyczne przez Centrum zabezpieczeń ma zastosowanie do całej subskrypcji.  Nie można zastosować automatycznej instalacji do podzbioru maszyn wirtualnych. Jeśli istnieją krytyczne maszyny wirtualne, których nie można zainstalować z programu Microsoft Monitoring Agent, należy zrezygnować z automatycznego inicjowania obsługi administracyjnej.
- Instalacja rozszerzenia usługi Microsoft Monitoring Agent aktualizacji wersji agenta. Dotyczy to bezpośredniego agenta i agenta programu SCOM. Jeśli zainstalowanego agenta programu SCOM jest w wersji 2012 i jest uaktualniony, możliwości zarządzania mogą zostać utracone, gdy serwer SCOM jest także wersja 2012. Należy rozważyć Rezygnacja z automatycznego inicjowania obsługi administracyjnej, jeśli jest zainstalowany agent programu SCOM w wersji 2012.
- Jeśli masz niestandardowego obszaru roboczego zewnętrznych do subskrypcji (scentralizowanych obszarów roboczych) należy zrezygnować z automatycznego inicjowania obsługi administracyjnej. Ręcznie zainstaluj rozszerzenie programu Microsoft Monitoring Agent i połącz go z obszaru roboczego bez Centrum zabezpieczeń zastępowanie połączenia.
- Jeśli chce się uniknąć tworzenia wiele obszarów roboczych na subskrypcję i mieć własne niestandardowe obszar roboczy w ramach subskrypcji, dostępne są dwie opcje:

   1. Możesz zrezygnować z automatycznego inicjowania obsługi administracyjnej. Po migracji, ustawić domyślny obszar roboczy ustawienia zgodnie z opisem w [jak używać Mój istniejący obszar roboczy analizy dzienników?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Lub, umożliwiają migracji zakończyć, programu Microsoft Monitoring Agent jest zainstalowany na maszynach wirtualnych, a maszyny wirtualne podłączone do utworzonego obszaru roboczego. Następnie wybierz własnego niestandardowego obszaru roboczego przez ustawienie domyślnego ustawienia obszaru roboczego o zgody na korzystanie z ponownej konfiguracji już zainstalowani agenci. Aby uzyskać więcej informacji, zobacz [jak używać Mój istniejący obszar roboczy analizy dzienników?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Jakie są skutki Rezygnacja z automatycznego inicjowania obsługi?
Po zakończeniu migracji Centrum zabezpieczeń nie będzie mógł zbierać dane zabezpieczeń z maszyny Wirtualnej, a niektóre zalecenia dotyczące zabezpieczeń i alertów są niedostępne. Jeśli możesz zrezygnować, należy zainstalować ręcznie programu Microsoft Monitoring Agent. Zobacz [zalecane kroki w przypadku rezygnację](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Co to są zalecane kroki podczas Rezygnacja z automatycznego inicjowania obsługi?
Należy ręcznie zainstalować agenta monitorowania firmy Microsoft, Centrum zabezpieczeń można zbierać dane z maszyn wirtualnych i zaleceń i alertami. Zobacz [komputery Windows połączenia z usługą analizy dzienników na platformie Azure](../log-analytics/log-analytics-windows-agent.md) wskazówki dotyczące instalacji.

Połącz agenta z istniejącego niestandardowego obszaru roboczego lub obszar roboczy utworzony Centrum zabezpieczeń. Jeśli niestandardowego obszaru roboczego nie ma rozwiązania "Zabezpieczenia" lub "SecurityCenterFree" włączone, a następnie należy zastosować rozwiązanie. Aby zastosować, wybierz niestandardowego obszaru roboczego lub subskrypcji i zastosować warstwy cenowej za pośrednictwem **zasady zabezpieczeń — warstwa cenowa** bloku.

   ![Warstwa cenowa][1]

Centrum zabezpieczeń umożliwi właściwym rozwiązaniem w obszarze roboczym oparte na wybranej warstwie cenowej.

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Jak usunąć rozszerzenia OMS zainstalowane przez Centrum zabezpieczeń?
Można ręcznie usunąć agenta monitorowania firmy Microsoft. Nie jest to zalecane, ponieważ ogranicza zaleceń Centrum zabezpieczeń i alertów.

> [!NOTE]
> Po włączeniu funkcji zbierania danych Centrum zabezpieczeń będzie ponownie zainstalować agenta po jej usunięciu.  Należy wyłączyć zbieranie danych przed ręczne usunięcie agenta. Zobacz [jak Zatrzymaj agenta automatycznego tworzenia instalacji i obszar roboczy?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?) instrukcje dotyczące wyłączenia zbierania danych.
>
>

Aby ręcznie usunąć agenta:

1.  W portalu, otwórz **analizy dzienników**.
2.  W bloku analizy dzienników wybierz obszar roboczy:
3.  Wybierz każdej maszyny Wirtualnej, których nie chcesz, aby monitorować i wybierz **rozłączenia**.

   ![Usuń agenta][3]

> [!NOTE]
> Jeśli Maszynę wirtualną systemu Linux jest już agent pakietu OMS bez rozszerzeń, usunięcie rozszerzenia spowoduje usunięcie również agenta i klient ma zainstalować go ponownie.
>
>

## <a name="existing-log-analytics-customers"></a>Istniejących klientów usługi analizy dzienników

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Centrum zabezpieczeń zastąpienie istniejących połączeń między maszynami wirtualnymi i obszary robocze?
Jeśli maszyna wirtualna ma już zainstalowany jako rozszerzenie Azure Microsoft Monitoring Agent, Centrum zabezpieczeń nie zastępują istniejące połączenie obszaru roboczego. Zamiast tego Centrum zabezpieczeń korzysta z istniejącym obszarem roboczym.

Rozwiązanie Centrum zabezpieczeń jest zainstalowany w obszarze roboczym Jeśli nie znajduje się już i rozwiązanie jest stosowane tylko do odpowiednich maszyn wirtualnych. Po dodaniu rozwiązania jest automatycznie wdrażane domyślnie do wszystkich agentów systemu Windows i Linux podłączone do obszaru roboczego analizy dzienników. [Rozwiązanie docelowych](../operations-management-suite/operations-management-suite-solution-targeting.md) pozwala zastosować zakres do rozwiązań.

Po zainstalowaniu programu Microsoft Monitoring Agent bezpośrednio na Maszynie wirtualnej (nie jako rozszerzenie Azure) Centrum zabezpieczeń nie instaluje program Microsoft Monitoring Agent oraz monitorowanie zabezpieczeń jest ograniczony.

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>Co należy zrobić w przypadku podejrzeń, że migracja danych platformy spowodowało przerwanie połączenia między jedną z maszyn wirtualnych i obszar Mój obszar roboczy?
To nie powinno się zdarzyć. Jeśli to się zdarzyć, następnie [utworzyć żądanie pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md) i podaj następujące informacje:

- Identyfikator zasobu Azure wpływ na maszynie Wirtualnej
- Identyfikator zasobów platformy Azure w obszarze roboczym skonfigurowane na rozszerzenia przed połączenie zostało przerwane
- Agent i wersji, który został wcześniej zainstalowany

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Centrum zabezpieczeń instaluje rozwiązań na mój istniejących obszarów roboczych analizy dzienników Jakie są skutki rozliczeniowym?
Gdy Centrum zabezpieczeń rozpozna, że maszyna wirtualna jest już połączona z obszaru roboczego, który został utworzony, Centrum zabezpieczeń umożliwia rozwiązań na ten obszar roboczy zgodnie z warstwy cenowej. Rozwiązania są stosowane tylko do odpowiednich maszynach wirtualnych platformy Azure za pośrednictwem [przeznaczonych dla rozwiązania](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting), więc rozliczenia jest taka sama.

- **Warstwa bezpłatna** — Centrum zabezpieczeń instaluje rozwiązanie "SecurityCenterFree" w obszarze roboczym. Nie są opłaty naliczane w warstwie bezpłatna.
- **Warstwy standardowa** — Centrum zabezpieczeń instaluje rozwiązanie "Zabezpieczenia" w obszarze roboczym.

   ![Rozwiązania na domyślny obszar roboczy][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Mam już obszarów roboczych w mojej środowisku, można z nich korzystać do zbierania danych zabezpieczeń?
Jeśli maszyna wirtualna ma już zainstalowany jako rozszerzenie Azure Microsoft Monitoring Agent, Centrum zabezpieczeń używa istniejącego połączenia obszaru roboczego. Rozwiązanie Centrum zabezpieczeń jest zainstalowany w obszarze roboczym Jeśli nie znajduje się już i rozwiązanie jest stosowane tylko do odpowiednich maszyn wirtualnych za pośrednictwem [przeznaczonych dla rozwiązania](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting).

Podczas instalowania programu Microsoft Monitoring Agent na maszynach wirtualnych Centrum zabezpieczeń używa domyślnej obszarów roboczych, utworzonych przez Centrum zabezpieczeń. Wkrótce klienci będą mogli jej konfigurować na które obszarów roboczych są używane.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Mam już rozwiązanie z zakresu zabezpieczeń na mój obszarów roboczych. Jakie są skutki rozliczeniowym?
Aby włączyć funkcje warstwy standardowa Centrum zabezpieczeń dla maszyn wirtualnych platformy Azure jest używane rozwiązanie zabezpieczające i inspekcji. Jeśli rozwiązania zabezpieczeń i inspekcji jest już zainstalowany w obszarze roboczym, Centrum zabezpieczeń używa istniejącego rozwiązania. Nie została zmieniona w rozliczeń.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat migracji platform Centrum zabezpieczeń, zobacz

- [Centrum zabezpieczeń Azure platformy migracji](security-center-platform-migration.md)
- [Przewodnik dotyczący rozwiązywania problemów w Centrum zabezpieczeń Azure](security-center-troubleshooting-guide.md)

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
