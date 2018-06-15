---
title: Monitorowanie zabezpieczeń w usłudze Azure Security Center | Microsoft Docs
description: Informacje zawarte w tym artykule ułatwiają rozpoczęcie korzystania z funkcji monitorowania w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2018
ms.author: yurid
ms.openlocfilehash: 330a12f851ef0191adc4dc46102b798f1b752589
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32777717"
---
# <a name="security-health-monitoring-in-azure-security-center"></a>Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure
Informacje zawarte w tym artykule ułatwiają korzystanie z funkcji monitorowania w usłudze Azure Security Center w celu monitorowania zgodności z zasadami.

## <a name="what-is-security-health-monitoring"></a>Czym jest monitorowanie kondycji zabezpieczeń?
Monitorowanie jest często postrzegane jako obserwowanie i oczekiwanie na wystąpienie zdarzenia, aby można było zareagować na zaistniałą sytuację. Monitorowanie zabezpieczeń polega na wdrożeniu aktywnej strategii przeprowadzania inspekcji zasobów w celu zidentyfikowania systemów, które nie spełniają standardów organizacji lub są niezgodne z najlepszymi rozwiązaniami.

## <a name="monitoring-security-health"></a>Monitorowanie kondycji zabezpieczeń
Po włączeniu [zasad zabezpieczeń](security-center-policies.md) dla zasobów subskrypcji usługa Security Center analizuje zabezpieczenia zasobów, aby zidentyfikować potencjalne luki. Informacje o konfiguracji sieci są dostępne natychmiast. W zależności od liczby maszyn wirtualnych i komputerów z zainstalowanym agentem, zebranie informacji o konfiguracji maszyn wirtualnych i komputerów, takich jak stan aktualizacji zabezpieczeń i konfiguracja systemu operacyjnego, może potrwać godzinę lub więcej, zanim zostaną one udostępnione. Stan zabezpieczeń zasobów oraz informacje o problemach można wyświetlić w sekcji **Zapobieganie**. Listę tych problemów można również wyświetlić na kafelku **Zalecenia**.

Więcej informacji dotyczących stosowania zaleceń można znaleźć w temacie [Wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

W sekcji **Zapobieganie** można monitorować stan zabezpieczeń zasobów. W poniższym przykładzie widać, że na kafelkach poszczególnych zasobów (Compute, Sieć, Storage i dane oraz Aplikacja) jest wyświetlana łączna liczba zidentyfikowanych problemów.

![Kafelek Kondycja zabezpieczeń zasobów](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute"></a>Monitorowanie mocy obliczeniowej
Po kliknięciu kafelka **Obliczenia** będą widoczne trzy karty:

- **Przegląd**: monitorowanie i zalecenia.
- **Maszyny wirtualne i komputery**: lista wszystkich maszyn wirtualnych i komputerów oraz ich bieżący stan zabezpieczeń.
- **Usługi w chmurze**: lista wszystkich ról sieci Web i procesów roboczych monitorowanych przez usługę Security Center.

![Brak aktualizacji systemu na maszynie wirtualnej](./media/security-center-monitoring/security-center-monitoring-fig1-sep2017.png)

Na każdej karcie znajduje się kilka sekcji, a w każdej sekcji można wybrać poszczególne opcje, aby wyświetlić więcej szczegółowych informacji dotyczących zalecanych kroków rozwiązania konkretnego problemu.

#### <a name="monitoring-recommendations"></a>Zalecenia dotyczące monitorowania
W tej sekcji przedstawiono całkowitą liczbę maszyn wirtualnych i komputerów, dla których zainicjowano automatyczną aprowizację, oraz ich bieżący stan. W tym przykładzie jest jedno zalecenie, **Monitorowanie problemów dotyczących kondycji agenta**.  Wybierz to zalecenie.

![Monitorowanie problemów dotyczących kondycji agenta](./media/security-center-monitoring/security-center-monitoring-fig1-new003-2017.png)

Zostanie otwarte okno **Monitorowanie problemów dotyczących kondycji agenta**. Zawiera ono listę maszyn wirtualnych i komputerów, których usługa Security Center nie mogła pomyślnie monitorować. Wybierz maszynę wirtualną lub komputer, aby uzyskać szczegółowe informacje. **STAN MONITOROWANIA** zapewnia powód, dla którego usługa Security Center nie mogła realizować monitorowania. Zobacz [Przewodnik rozwiązywania problemów w usłudze Security Center](security-center-troubleshooting-guide.md#monitoring-agent-health-issues), aby uzyskać listę wartości, opisów i rozwiązań dla opcji **STAN MONITOROWANIA**.

#### <a name="recommendations"></a>Zalecenia
Ta sekcja zawiera zestaw [zaleceń dla każdej maszyny wirtualnej i komputera](security-center-virtual-machine-recommendations.md) monitorowanych przez usługę Azure Security Center. W pierwszej kolumnie wyświetlane są zalecenia. Druga kolumna zawiera całkowitą liczbę maszyn wirtualnych i komputerów, na które dane zalecenie ma wpływ. Trzecia kolumna pokazuje wagę problemu, co przedstawiono na następującym zrzucie ekranu:

![Zalecenia dotyczące maszyny wirtualnej](./media/security-center-monitoring/security-center-monitoring-fig2-sep2017.png)

> [!NOTE]
> W obszarze **Kondycja sieci** na liście **Topologie sieci** wyświetlane są tylko maszyny wirtualne z co najmniej jednym publicznym punktem końcowym.
>

Każdy zalecenie obejmuje zestaw akcji możliwych do wykonania po jego kliknięciu. Na przykład jeśli klikniesz pozycję **Brak aktualizacji systemu**, zostanie wyświetlona lista maszyn wirtualnych i komputerów, na których brakuje poprawek, oraz ważność brakujących aktualizacji, jak pokazano na poniższym zrzucie ekranu:

![Brak aktualizacji systemu na maszynach wirtualnych](./media/security-center-monitoring/security-center-monitoring-fig9-sep2017.png)

Obszar **Brak aktualizacji systemu** zawiera podsumowanie krytycznych aktualizacji w postaci wykresu: jeden dla systemu Windows i jeden dla systemu Linux. Druga część zawiera tabelę z następującymi informacjami:

* **NAZWA**: nazwa brakującej aktualizacji.
* **LICZBA MASZYN WIRTUALNYCH I KOMPUTERÓW**: łączna liczba maszyn wirtualnych i komputerów, które nie mają tej aktualizacji.
* **STAN**: bieżący stan zalecenia:
  * **Otwarte**: nie rozpoczęto jeszcze wykonywania zalecenia.
  * **W toku**: zalecenie jest aktualnie stosowane do danych zasobów, żadna akcja użytkownika nie jest wymagana.
  * **Rozwiązane**: zalecenie zostało już zakończone. (Gdy problem został rozwiązany, wpis jest wyszarzony).
* **WAŻNOŚĆ**: opisuje ważność określonego zalecenia:
  * **Wysoka**: istnieje luka w zabezpieczeniach, która dotyczy istotnego zasobu (aplikacji, maszyny wirtualnej, sieciowej grupy zabezpieczeń) i wymaga uwagi.
  * **Średnia**: niekrytyczne lub dodatkowe kroki są wymagane w celu ukończenia procesu lub wyeliminowania luki w zabezpieczeniach.
  * **Niska**: luka w zabezpieczeniach powinna zostać usunięta, ale nie wymaga natychmiastowej uwagi. (Domyślnie zalecenia o niskiej ważności nie są prezentowane, ale możesz ustawić filtr umożliwiający wyświetlanie zaleceń o niskiej ważności, jeśli chcesz je przeglądać).

Aby wyświetlić szczegóły zalecenia, kliknij nazwę brakującej aktualizacji na liście.

![Brak aktualizacji systemu na określonej maszynie wirtualnej](./media/security-center-monitoring/security-center-monitoring-fig4-sep2017.png)

> [!NOTE]
> Przedstawione tutaj zalecenia dotyczące zabezpieczeń są takie same jak w opcji **Zalecenia**. Więcej informacji dotyczących stosowania zaleceń można znaleźć w artykule [Wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md). Dotyczy to nie tylko maszyn wirtualnych i komputerów, ale także wszystkich zasobów, które są dostępne za pośrednictwem kafelka **Kondycja zasobu**.
>

#### <a name="unmonitored-vms"></a>Niemonitorowane maszyny wirtualne
Maszyna wirtualna jest niemonitorowana przez usługę Security Center, jeśli na maszynie wirtualnej nie jest uruchomione rozszerzenie programu Microsoft Monitoring Agent. Na maszynie wirtualnej może już być zainstalowany lokalny agent, na przykład bezpośredni agent pakietu OMS lub agent SCOM. Maszyny wirtualne z tymi agentami są identyfikowane jako niemonitorowane, ponieważ ci agenci nie są w pełni obsługiwani w usłudze Security Center. Aby w pełni skorzystać ze wszystkich funkcji usługi Security Center, wymagane jest rozszerzenie programu Microsoft Monitoring Agent.

Rozszerzenie można zainstalować na niemonitorowanej maszynie wirtualnej obok już zainstalowanego lokalnego agenta. Obu agentów należy skonfigurować tak samo, łącząc ich z tym samym obszarem roboczym. Dzięki temu usługa Security Center może wchodzić w interakcje z rozszerzeniem programu Microsoft Monitoring Agent i zbierać dane.  Zobacz [Włączanie rozszerzenia maszyny wirtualnej](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension), aby uzyskać instrukcje dotyczące sposobu instalowania rozszerzenia programu Microsoft Monitoring Agent.

Zobacz [Monitorowanie problemów dotyczących kondycji agenta](security-center-troubleshooting-guide.md#monitoring-agent-health-issues), aby dowiedzieć się więcej na temat powodów, dla których usługa Security Center nie może prawidłowo monitorować maszyn wirtualnych i komputerów zainicjowanych do automatycznej aprowizacji.

#### <a name="vms--computers-section"></a>Sekcja Maszyny wirtualne i komputery
Sekcja Maszyny wirtualne i komputery zawiera przegląd wszystkich zaleceń dotyczących maszyn wirtualnych i komputerów. Każda kolumna reprezentuje jeden zestaw zaleceń, jak pokazano na poniższym zrzucie ekranu:

![Przegląd wszystkich maszyn wirtualnych i zaleceń](./media/security-center-monitoring/security-center-monitoring-fig5-sep2017.png)

Istnieją cztery typy ikon wyświetlanych na tej liście, zgodnie z objaśnieniem na tej liście:

![icon1](./media/security-center-monitoring/security-center-monitoring-icon1.png) Komputer bez platformy Azure.

![icon2](./media/security-center-monitoring/security-center-monitoring-icon2.png) Maszyna wirtualna usługi Azure Resource Manager.

![icon3](./media/security-center-monitoring/security-center-monitoring-icon3.png) Klasyczna maszyna wirtualna platformy Azure.

![icon4](./media/security-center-monitoring/security-center-monitoring-icon4.png) Maszyny wirtualne, które są identyfikowane tylko z obszaru roboczego będącego częścią przeglądanej subskrypcji. Obejmuje to maszyny wirtualne z innych subskrypcji, które podlegają obszarowi roboczemu w tej subskrypcji, oraz maszyny wirtualne, które zostały zainstalowane z agentem bezpośrednim programu SCOM i nie mają identyfikatora zasobu.

Ikona wyświetlana pod każdym zaleceniem pomaga szybko określić, które maszyny wirtualne i komputery wymagają uwagi oraz jakiego typu zalecenie ma do nich zastosowanie. Można także skorzystać z opcji **Filtr**, aby wybrać opcje, które będą wyświetlane na tym ekranie.

![Filtr](./media/security-center-monitoring/security-center-monitoring-fig6-sep2017.png)

W powyższym przykładzie dla jednej maszyny wirtualnej widoczne jest krytyczne zalecenie dotyczące ochrony punktów końcowych. Aby uzyskać więcej informacji na temat maszyny wirtualnej, kliknij ją:

![Szczegóły dotyczące zabezpieczeń maszyny wirtualnej](./media/security-center-monitoring/security-center-monitoring-fig7-sep2017.png)

W tym miejscu są wyświetlane szczegóły zabezpieczeń dotyczące maszyny wirtualnej lub komputera. W dolnej części widoczna jest zalecana akcja i ważność poszczególnych problemów.

#### <a name="cloud-services-section"></a>Sekcja Usługi w chmurze
W przypadku usług w chmurze zalecenie jest tworzone, gdy wersja systemu operacyjnego jest nieaktualna, jak pokazano na poniższym zrzucie ekranu:

![Stan kondycji dla usług w chmurze](./media/security-center-monitoring/security-center-monitoring-fig1-new006-2017.png)

W scenariuszu, w którym masz zalecenie (nieprzedstawionym w poprzednim przykładzie) należy wykonać kroki zalecenia, aby zaktualizować wersję systemu operacyjnego. Gdy aktualizacja jest dostępna, zostanie wyświetlony alert (czerwony lub pomarańczowy — w zależności od ważności problemu). Po kliknięciu tego alertu w roli internetowej (WebRole1) lub roli procesu roboczego (WorkerRole1) — w ramach której działa system Windows Server z aplikacją internetową automatycznie wdrożoną w usługach IIS — zostanie wyświetlone więcej szczegółów na temat tego zalecenia, co pokazano na poniższym zrzucie ekranu:

![Szczegóły dotyczące usługi w chmurze](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png)

Aby zobaczyć więcej normatywnych wyjaśnień dotyczących tego zalecenia, kliknij pozycję **Aktualizuj wersję systemu operacyjnego** w kolumnie **OPIS**.

![Zalecenia dotyczące usług w chmurze](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

### <a name="monitor-virtual-networks"></a>Monitorowanie sieci wirtualnych
Kliknięcie kafelka **Sieć** powoduje otwarcie bloku **Sieć** zawierającego więcej szczegółowych informacji, jak pokazano na poniższym zrzucie ekranu:

![Blok Sieć](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>Zalecenia dotyczące sieci
Podobnie jak w przypadku informacji o kondycji zasobów maszyny wirtualnej, w górnej części widoczna jest skrócona lista problemów, a w dolnej — lista monitorowanych sieci.

Sekcja ze szczegółowymi danymi o stanie sieci zawiera listę potencjalnych problemów z zabezpieczeniami oraz [zalecenia](security-center-network-recommendations.md). Mogą wystąpić następujące problemy:

* Zapora nowej generacji (NGFW) nie jest zainstalowana
* Sieciowe grupy zabezpieczeń w podsieciach nie są włączone
* Sieciowe grupy zabezpieczeń w maszynach wirtualnych nie są włączone
* Ograniczanie dostępu zewnętrznego za pośrednictwem publicznego zewnętrznego punktu końcowego
* Punkty końcowe mające połączenie z Internetem w dobrej kondycji

Po kliknięciu zalecenia zostanie wyświetlone więcej szczegółów na temat zalecenia, jak pokazano w poniższym przykładzie:

![Szczegóły dotyczące zalecenia w bloku Sieć](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

W tym przykładzie blok **Konfiguruj brakujące grupy zabezpieczeń sieci dla podsieci** zawiera listę podsieci i maszyn wirtualnych, dla których brakuje ochrony sieciowej grupy zabezpieczeń. Po kliknięciu podsieci, do której chcesz zastosować grupę zabezpieczeń sieci, zostanie otwarty blok **Wybieranie grupy zabezpieczeń sieci**. W tym miejscu możesz wybrać najbardziej odpowiednią sieciową grupę zabezpieczeń dla podsieci lub utworzyć nową sieciową grupę zabezpieczeń.

#### <a name="internet-facing-endpoints-section"></a>Sekcja Internet facing endpoints (Punkty końcowe umożliwiające dostęp do Internetu)
Sekcja **Punkty końcowe mające połączenie z Internetem** zawiera maszyny wirtualne, które są aktualnie skonfigurowane z użyciem punktu końcowego połączonego z Internetem, oraz ich bieżący stan.

![Maszyny wirtualne skonfigurowane z użyciem punktu końcowego połączonego z Internetem oraz stan](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

Ta tabela zawiera nazwę punktu końcowego, która reprezentuje maszynę wirtualną, adres IP umożliwiający dostęp do Internetu oraz bieżący stan ważności dla sieciowej grupy zabezpieczeń i zapory nowej generacji. Informacje w tabeli są posortowane według ważności:

* Kolor czerwony (u góry): wysoki priorytet, konieczne jest natychmiastowe rozwiązanie problemu
* Kolor pomarańczowy: średni priorytet, konieczne jest możliwie szybkie rozwiązanie problemu
* Kolor zielony (ostatni): stan prawidłowy

#### <a name="networking-topology-section"></a>Sekcja Topologia sieci
Sekcja **Topologia sieci** zawiera zasoby przedstawione według hierarchii, jak pokazano na poniższym zrzucie ekranu:

![Hierarchiczny widok zasobów w sekcji Topologia sieci](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

Informacje w tej tabeli (dotyczące maszyn wirtualnych i podsieci) zostały posortowane według ważności:

* Kolor czerwony (u góry): wysoki priorytet, konieczne jest natychmiastowe rozwiązanie problemu
* Kolor pomarańczowy: średni priorytet, konieczne jest możliwie szybkie rozwiązanie problemu
* Kolor zielony (ostatni): stan prawidłowy

W tym widoku topologii pierwszy poziom obejmuje pozycje [sieci wirtualne](../virtual-network/virtual-networks-overview.md), [bramy sieci wirtualnej](/vpn-gateway/vpn-gateway-site-to-site-create.md) i [sieci wirtualne (klasyczne)](/virtual-network/virtual-networks-create-vnet-classic-pportal.md). Drugi poziom obejmuje podsieci, natomiast trzeci poziom przedstawia maszyny wirtualne, które należą do tych podsieci. Prawa kolumna przedstawia bieżący stan sieciowej grupy zabezpieczeń dla tych zasobów, jak pokazano w poniższym przykładzie:

![Stan grupy zabezpieczeń sieci w sekcji Topologia sieci](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

W dolnej części tego bloku znajdują się zalecenia dotyczące tej maszyny wirtualnej, co jest podobne do powyższego opisu. Kliknij zalecenie, aby dowiedzieć się więcej i ewentualnie zastosować odpowiednie środki kontroli lub konfigurację zabezpieczeń.

### <a name="monitor-storage--data"></a>Monitorowanie usługi Storage i danych

Po kliknięciu pozycji **Magazyn i dane** w sekcji **Zapobieganie** zostanie otwarty blok **Zasoby danych** z zaleceniami dotyczącymi usług SQL i Storage. Przedstawia on także [zalecenia](security-center-sql-service-recommendations.md) dotyczące ogólnej kondycji bazy danych. Aby uzyskać więcej informacji dotyczących szyfrowania magazynu, przeczytaj artykuł [Enable encryption for Azure storage account in Azure Security Center](security-center-enable-encryption-for-storage-account.md) (Włączanie szyfrowania dla konta usługi Azure Storage w usłudze Azure Security Center).

![Zasoby danych](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

W obszarze **Zalecenia SQL** można kliknąć dowolne zalecenie i uzyskać więcej szczegółów na temat dalszych działań w celu rozwiązania problemu. Poniższy przykład pokazuje rozszerzenie zalecenia **Inspekcja bazy danych i wykrywanie zagrożeń w bazach danych SQL**.

![Szczegółowe informacje o zaleceniach SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Blok **Włączanie inspekcji i wykrywania zagrożeń dla baz danych SQL** zawiera następujące informacje:

* Lista baz danych SQL
* Serwer, na którym znajdują się bazy danych SQL
* Informacje o tym, czy to ustawienie jest dziedziczone z serwera lub czy jest unikatowe w tej bazie danych
* Bieżący stan
* Ważność problemu

Po kliknięciu bazy danych w celu zastosowania tego zalecenia zostanie otwarty blok **Inspekcja i wykrywanie zagrożeń**, jak pokazano na poniższym ekranie.

![Inspekcja i wykrywanie zagrożeń](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Aby włączyć funkcję inspekcji, zaznacz pole wyboru **WŁĄCZONE** poniżej opcji **Inspekcja**.

### <a name="monitor-applications"></a>Monitorowanie aplikacji

Jeśli obciążenie platformy Azure obejmuje aplikacje znajdujące się na [maszynach wirtualnych (utworzonych za pomocą usługi Azure Resource Manager)](../azure-resource-manager/resource-manager-deployment-model.md) z uwidocznionymi portami sieci Web (porty TCP 80 i 443), usługa Security Center może je monitorować w celu identyfikowania potencjalnych problemów z zabezpieczeniami oraz rekomendowania czynności naprawczych. Po kliknięciu kafelka **Aplikacje** zostanie otwarty blok **Aplikacje** z serią zaleceń w sekcji **Zalecenia dotyczące aplikacji**. Wyświetla on również podział aplikacji według hosta, adresu IP/domeny oraz obecności zainstalowanej zapory aplikacji internetowych:

![Kondycja zabezpieczeń aplikacji](./media/security-center-monitoring/security-center-monitoring-fig8-sep2017.png)

Podobnie jak w przypadku innych zaleceń, możesz je kliknąć, aby wyświetlić więcej szczegółów o problemie i sposobie jego rozwiązania. Na poniższym rysunku przedstawiono przykładową aplikację, która została zidentyfikowana jako niezabezpieczona aplikacja sieci Web. Po wybraniu aplikacji, która została uznana za niezabezpieczoną, dostępna będzie następująca opcja:

![Szczegóły](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

Tutaj będzie wyświetlana lista wszystkich zaleceń dla tej aplikacji. Po kliknięciu zalecenia **Dodaj zaporę aplikacji sieci Web** zostanie otwarty blok **Dodaj zaporę aplikacji sieci Web** z opcjami umożliwiającymi zainstalowanie zapory aplikacji internetowych innej firmy, jak pokazano na poniższym zrzucie ekranu.

![Okno dialogowe Dodaj zaporę aplikacji sieci Web](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## <a name="see-also"></a>Zobacz też
W tym artykule przedstawiono sposób korzystania z funkcji monitorowania w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania ustawień zabezpieczeń w usłudze Azure Security Center.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
