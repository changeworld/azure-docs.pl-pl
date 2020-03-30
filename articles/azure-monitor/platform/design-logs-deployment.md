---
title: Projektowanie wdrożenia dzienników usługi Azure Monitor | Dokumenty firmy Microsoft
description: W tym artykule opisano zagadnienia i zalecenia dla klientów przygotowujących się do wdrożenia obszaru roboczego w usłudze Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/20/2019
ms.openlocfilehash: e493b07814821496f941a4b81402ba0b49acbede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248829"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>Projektowanie wdrożenia dzienników usługi Azure Monitor

Usługa Azure Monitor przechowuje dane [dziennika](data-platform-logs.md) w obszarze roboczym usługi Log Analytics, który jest zasobem platformy Azure i kontenerem, w którym dane są zbierane, agregowane i służy jako granica administracyjna. Chociaż można wdrożyć jeden lub więcej obszarów roboczych w ramach subskrypcji platformy Azure, istnieje kilka zagadnień, które należy zrozumieć, aby upewnić się, że początkowe wdrożenie jest zgodne z naszymi wytycznymi, aby zapewnić Ci opłacalne, łatwe w zarządzaniu i skalowalne wdrażania spełniania potrzeb organizacji.

Dane w obszarze roboczym są zorganizowane w tabele, z których każda przechowuje różne rodzaje danych i ma własny unikatowy zestaw właściwości na podstawie zasobu generującego dane. Większość źródeł danych będzie zapisywać do własnych tabel w obszarze roboczym usługi Log Analytics.

![Przykładowy model danych obszaru roboczego](./media/design-logs-deployment/logs-data-model-01.png)

Obszar roboczy usługi Log Analytics zapewnia:

* Lokalizacja geograficzna do przechowywania danych.
* Izolacja danych przez przyznanie różnym użytkownikom praw dostępu zgodnie z jedną z naszych zalecanych strategii projektowania.
* Zakres konfiguracji ustawień, takich jak [warstwa cenowa,](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier) [przechowywanie](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)i [ograniczanie danych](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#manage-your-maximum-daily-data-volume).

Ten artykuł zawiera szczegółowe omówienie zagadnień dotyczących projektowania i migracji, omówienie kontroli dostępu oraz zrozumienie implementacji projektu, które zaleca się dla organizacji IT.



## <a name="important-considerations-for-an-access-control-strategy"></a>Ważne kwestie dotyczące strategii kontroli dostępu

Na określenie liczby potrzebnych obszarów roboczych ma wpływ co najmniej jedno z następujących wymagań:

* Jesteś firmą globalną i potrzebujesz danych dziennika przechowywanych w określonych regionach ze względu na suwerenność danych lub zgodność.
* Korzystasz z platformy Azure i chcesz uniknąć naliczania opłat za transfer danych wychodzących przez umieszczenie obszaru roboczego w tym samym regionie co zarządzane przez niego zasoby platformy Azure.
* Zarządzasz wieloma działami lub grupami biznesowymi i chcesz, aby każdy z nich widział własne dane, ale nie dane pochodzące od innych. Ponadto nie ma wymogu biznesowego dla skonsolidowanego widoku międzydziałów lub grup biznesowych.

Organizacje IT są dziś modelowane po scentralizowanej, zdecentralizowanej lub między hybrydą obu struktur. W rezultacie następujące modele wdrażania obszaru roboczego były powszechnie używane do mapowania do jednej z tych struktur organizacyjnych:

* **Scentralizowane:** wszystkie dzienniki są przechowywane w centralnym obszarze roboczym i administrowane przez jeden zespół, z usługą Azure Monitor zapewniającą zróżnicowany dostęp dla każdego zespołu. W tym scenariuszu jest łatwe do zarządzania, wyszukiwania między zasobami i cross-correlate dzienników. Obszar roboczy może znacznie wzrosnąć w zależności od ilości danych zebranych z wielu zasobów w ramach subskrypcji, z dodatkowym obciążeniem administracyjnym, aby zachować kontrolę dostępu dla różnych użytkowników. Ten model jest znany jako "piasta i szprycha".
* **Zdecentralizowane:** Każdy zespół ma swój własny obszar roboczy utworzony w grupie zasobów, których jest właścicielem i zarządzają, a dane dziennika są segregowane według zasobu. W tym scenariuszu obszar roboczy może być bezpieczne i kontroli dostępu jest zgodne z dostępem do zasobów, ale jest trudne do cross-skorelowania dzienników. Użytkownicy, którzy potrzebują szerokiego widoku wielu zasobów, nie mogą analizować danych w znaczący sposób.
* **Hybrydowy:** Wymagania dotyczące zgodności inspekcji zabezpieczeń dodatkowo komplikują ten scenariusz, ponieważ wiele organizacji implementuje oba modele wdrażania równolegle. To często powoduje złożone, kosztowne i trudne do utrzymania konfiguracji z przerwami w zasięgu dzienników.

Podczas korzystania z agentów usługi Log Analytics do zbierania danych, należy zrozumieć następujące, aby zaplanować wdrożenie agenta:

* Aby zbierać dane od agentów systemu Windows, można [skonfigurować każdego agenta do raportowania do jednego lub więcej obszarów roboczych,](../../azure-monitor/platform/agent-windows.md)nawet jeśli jest on raportować grupę zarządzania programu System Center Operations Manager. Agent systemu Windows może zgłaszać maksymalnie cztery obszary robocze.
* Agent systemu Linux nie obsługuje multi-homing i może raportować tylko do jednego obszaru roboczego.

Jeśli używasz programu System Center Operations Manager 2012 R2 lub nowszej:

* Każdą grupę zarządzania programem Operations Manager można [podłączyć tylko do jednego obszaru roboczego](../platform/om-agents.md). 
* Komputery z systemem Linux, które zgłaszają się do grupy zarządzania, muszą być skonfigurowane do raportowania bezpośrednio do obszaru roboczego usługi Log Analytics. Jeśli komputery z systemem Linux już zgłaszają się bezpośrednio do obszaru roboczego i chcesz je monitorować za pomocą programu Operations Manager, wykonaj następujące kroki, aby [zgłosić raport do grupy zarządzania programu Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group). 
* Agent systemu Windows usługi Log Analytics można zainstalować na komputerze z systemem Windows i zgłosić go zarówno do programu Operations Manager, jak i do innego obszaru roboczego.

## <a name="access-control-overview"></a>Omówienie kontroli dostępu

Za pomocą kontroli dostępu opartej na rolach (RBAC) można przyznać użytkownikom i grupom tylko ilość dostępu, jakiej potrzebują do pracy z danymi monitorowania w obszarze roboczym. Dzięki temu można wyrównać z modelem operacyjnym organizacji IT przy użyciu jednego obszaru roboczego do przechowywania zebranych danych włączonych we wszystkich zasobach. Na przykład udzielisz dostępu do zespołu odpowiedzialnego za usługi infrastruktury hostowane na maszynach wirtualnych platformy Azure (maszyny wirtualne) i w rezultacie będą one miały dostęp tylko do dzienników generowanych przez maszyny wirtualne. Jest to następujące nasz nowy model dziennika kontekstu zasobów. Podstawą tego modelu jest dla każdego rekordu dziennika emitowanego przez zasób platformy Azure, jest automatycznie skojarzony z tym zasobem. Dzienniki są przekazywane do centralnego obszaru roboczego, który szanuje zakres i RBAC na podstawie zasobów.

Dane, do których użytkownik ma dostęp, są określane przez kombinację czynników wymienionych w poniższej tabeli. Każdy z nich jest opisany w poniższych sekcjach.

| Czynnikiem | Opis |
|:---|:---|
| [Tryb dostępu](#access-mode) | Metoda używana przez użytkownika do uzyskiwania dostępu do obszaru roboczego.  Określa zakres dostępnych danych i tryb kontroli dostępu, który jest stosowany. |
| [Tryb kontroli dostępu](#access-control-mode) | Ustawienie w obszarze roboczym, który określa, czy uprawnienia są stosowane na poziomie obszaru roboczego lub zasobu. |
| [Uprawnienia](manage-access.md) | Uprawnienia stosowane do poszczególnych lub grup użytkowników dla obszaru roboczego lub zasobu. Określa, do jakich danych użytkownik będzie miał dostęp. |
| [Poziom tabeli RBAC](manage-access.md#table-level-rbac) | Opcjonalne szczegółowe uprawnienia, które mają zastosowanie do wszystkich użytkowników, niezależnie od ich trybu dostępu lub trybu kontroli dostępu. Określa, do których typów danych użytkownik może uzyskać dostęp. |

## <a name="access-mode"></a>Tryb dostępu

*Tryb dostępu* odnosi się do sposobu, w jaki użytkownik uzyskuje dostęp do obszaru roboczego usługi Log Analytics i definiuje zakres danych, do których ma dostęp. 

Użytkownicy mają dwie opcje dostępu do danych:

* **Kontekst obszaru roboczego:** Można wyświetlić wszystkie dzienniki w obszarze roboczym, do którego masz uprawnienia. Zapytania w tym trybie są ograniczone do wszystkich danych we wszystkich tabelach w obszarze roboczym. Jest to tryb dostępu używany, gdy dzienniki są dostępne z obszaru roboczego jako zakres, takich jak po wybraniu **dzienników** z menu **Usługi Azure Monitor** w witrynie Azure portal.

    ![Kontekst usługi Log Analytics z obszaru roboczego](./media/design-logs-deployment/query-from-workspace.png)

* **Kontekst zasobu:** Podczas uzyskiwania dostępu do obszaru roboczego dla określonego zasobu, grupy zasobów lub subskrypcji, na przykład po wybraniu **dzienników** z menu zasobów w witrynie Azure portal, można wyświetlać dzienniki tylko dla zasobów we wszystkich tabelach, do których masz dostęp. Kwerendy w tym trybie są ograniczone tylko do danych skojarzonych z tym zasobem. Ten tryb umożliwia również granulowany RBAC.

    ![Kontekst usługi Log Analytics z zasobu](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > Dzienniki są dostępne dla zapytań kontekstu zasobów tylko wtedy, gdy zostały prawidłowo skojarzone z odpowiednim zasobem. Obecnie następujące zasoby mają ograniczenia:
    > - Komputery poza platformą Azure
    > - Service Fabric
    > - Application Insights
    >
    > Można sprawdzić, czy dzienniki są poprawnie skojarzone z ich zasobu, uruchamiając kwerendę i inspekcji rekordów, które Cię interesują. Jeśli prawidłowy identyfikator zasobu znajduje się we właściwości [_ResourceId,](log-standard-properties.md#_resourceid) dane są dostępne dla zapytań zorientowanych na zasoby.

Usługa Azure Monitor automatycznie określa odpowiedni tryb w zależności od kontekstu, z którego wykonujesz wyszukiwanie w dzienniku. Zakres jest zawsze prezentowany w lewej górnej części usługi Log Analytics.

### <a name="comparing-access-modes"></a>Porównywanie trybów dostępu

W poniższej tabeli podsumowano tryby dostępu:

| | Kontekst obszaru roboczego | Kontekst zasobu |
|:---|:---|:---|
| Dla kogo przeznaczony jest każdy model? | Administracja centralna. Administratorzy, którzy muszą skonfigurować zbieranie danych i użytkowników, którzy potrzebują dostępu do szerokiej gamy zasobów. Obecnie wymagane również dla użytkowników, którzy muszą uzyskać dostęp do dzienników zasobów poza platformą Azure. | Zespoły aplikacyjne. Administratorzy monitorowanych zasobów platformy Azure. |
| Czego potrzebuje użytkownik do wyświetlania dzienników? | Uprawnienia do obszaru roboczego. Zobacz **Uprawnienia obszaru roboczego** w [obszarze Zarządzanie dostępem przy użyciu uprawnień obszaru roboczego](manage-access.md#manage-access-using-workspace-permissions). | Dostęp do odczytu do zasobu. Zobacz **Uprawnienia zasobów** w obszarze Zarządzanie [dostępem przy użyciu uprawnień platformy Azure](manage-access.md#manage-access-using-azure-permissions). Uprawnienia mogą być dziedziczone (na przykład z zawierającej grupy zasobów) lub bezpośrednio przypisane do zasobu. Uprawnienie do dzienników zasobu zostanie automatycznie przypisane. |
| Jaki jest zakres uprawnień? | Obszaru roboczego. Użytkownicy z dostępem do obszaru roboczego mogą wysyłać zapytania do wszystkich dzienników w obszarze roboczym z tabel, do których mają uprawnienia. Zobacz [Kontrola dostępu do tabeli](manage-access.md#table-level-rbac) | Zasobów platformy Azure. Użytkownik może wysyłać zapytania do dzienników dla określonych zasobów, grup zasobów lub subskrypcji, do których ma dostęp z dowolnego obszaru roboczego, ale nie może wysyłać zapytań o dzienniki innych zasobów. |
| W jaki sposób można uzyskać dostęp do dzienników użytkowników? | <ul><li>Uruchom **dzienniki** z menu **Usługi Azure Monitor.**</li></ul> <ul><li>Uruchamianie **dzienników** z **obszarów roboczych usługi Log Analytics**.</li></ul> <ul><li>Ze [skoroszytów](../visualizations.md#workbooks)usługi Azure Monitor .</li></ul> | <ul><li>Uruchamianie **dzienników** z menu zasobu platformy Azure</li></ul> <ul><li>Uruchom **dzienniki** z menu **Usługi Azure Monitor.**</li></ul> <ul><li>Uruchamianie **dzienników** z **obszarów roboczych usługi Log Analytics**.</li></ul> <ul><li>Ze [skoroszytów](../visualizations.md#workbooks)usługi Azure Monitor .</li></ul> |

## <a name="access-control-mode"></a>Tryb kontroli dostępu

*Tryb kontroli dostępu* jest ustawieniem dla każdego obszaru roboczego, które definiuje sposób określania uprawnień dla obszaru roboczego.

* **Wymagaj uprawnień obszaru roboczego:** Ten tryb sterowania nie zezwala na szczegółowy RBAC. Aby użytkownik miał dostęp do obszaru roboczego, należy im przyznać uprawnienia do obszaru roboczego lub do określonych tabel.

    Jeśli użytkownik uzyskuje dostęp do obszaru roboczego zgodnie z trybem kontekstu obszaru roboczego, mają dostęp do wszystkich danych w dowolnej tabeli, do których uzyskał dostęp. Jeśli użytkownik uzyskuje dostęp do obszaru roboczego zgodnie z trybem kontekstu zasobów, mają dostęp tylko do danych dla tego zasobu w dowolnej tabeli, do których uzyskał dostęp.

    Jest to ustawienie domyślne dla wszystkich obszarów roboczych utworzonych przed marcem 2019 r.

* **Użyj uprawnień zasobów lub obszaru roboczego:** Ten tryb sterowania umożliwia szczegółowy RBAC. Użytkownikom można przyznać dostęp tylko do danych skojarzonych z `read` zasobami, które mogą wyświetlać, przypisując uprawnienia platformy Azure. 

    Gdy użytkownik uzyskuje dostęp do obszaru roboczego w trybie kontekstowym obszaru roboczego, mają zastosowanie uprawnienia obszaru roboczego. Gdy użytkownik uzyskuje dostęp do obszaru roboczego w trybie kontekstu zasobu, tylko uprawnienia zasobów są weryfikowane, a uprawnienia obszaru roboczego są ignorowane. Włącz rbac dla użytkownika, usuwając je z uprawnień obszaru roboczego i zezwalając na ich uprawnienia zasobów do rozpoznania.

    Jest to ustawienie domyślne dla wszystkich obszarów roboczych utworzonych po marcu 2019 r.

    > [!NOTE]
    > Jeśli użytkownik ma tylko uprawnienia zasobów do obszaru roboczego, może uzyskać dostęp do obszaru roboczego tylko przy użyciu trybu kontekstu zasobu przy założeniu, że tryb dostępu do obszaru roboczego jest ustawiony na **Użyj uprawnień zasobu lub obszaru roboczego**.

Aby dowiedzieć się, jak zmienić tryb kontroli dostępu w portalu za pomocą programu PowerShell lub szablonu Menedżera zasobów, zobacz [Konfigurowanie trybu kontroli dostępu](manage-access.md#configure-access-control-mode).

## <a name="ingestion-volume-rate-limit"></a>Limit szybkości spożycia

Usługa Azure Monitor to usługa danych na dużą skalę, która obsługuje tysiące klientów wysyłających terabajty danych każdego miesiąca w rosnącym tempie. Domyślny próg szybkości pozyskiwania jest ustawiony na **6 GB/min** na obszar roboczy. Jest to wartość przybliżona, ponieważ rzeczywisty rozmiar może się różnić między typami danych w zależności od długości dziennika i jego współczynnika kompresji. Ten limit nie dotyczy danych wysyłanych z agentów lub [interfejsu API modułu zbierającego dane](data-collector-api.md).

Jeśli wysyłasz dane z wyższą szybkością do jednego obszaru roboczego, niektóre dane są odrzucane, a zdarzenie jest wysyłane do tabeli *Operacja* w obszarze roboczym co 6 godzin, podczas gdy próg jest nadal przekraczany. Jeśli wolumin pozyskiwania nadal przekracza limit szybkości lub oczekujesz, że osiągniesz go w najbliższym czasie, możesz poprosić o zwiększenie obszaru roboczego, otwierając żądanie pomocy technicznej.
 
Aby otrzymywać powiadomienia o takim zdarzeniu w obszarze roboczym, należy utworzyć [regułę alertu dziennika](alerts-log.md) przy użyciu następującej kwerendy z logiką alertu na podstawie liczby wyników na tarce niż zero.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


## <a name="recommendations"></a>Zalecenia

![Przykład projektu kontekstu zasobu](./media/design-logs-deployment/workspace-design-resource-context-01.png)

W tym scenariuszu opisano projekt pojedynczego obszaru roboczego w subskrypcji organizacji IT, który nie jest ograniczony przez suwerenność danych lub zgodność z przepisami lub musi mapować do regionów, w których są wdrażane zasoby. Umożliwia to organizacjom bezpieczeństwa i zespołom administracyjnym IT korzystanie z ulepszonej integracji z zarządzaniem dostępem platformy Azure i bardziej bezpiecznej kontroli dostępu.

Wszystkie zasoby, rozwiązania do monitorowania i usługi Insights, takie jak usługa Application Insights i usługa Azure Monitor dla maszyn wirtualnych, infrastruktura i aplikacje obsługiwane przez różne zespoły są skonfigurowane do przekazywania zebranych danych dziennika do organizacji IT scentralizowanego udostępnionego obszaru roboczego. Użytkownicy w każdym zespole mają dostęp do dzienników zasobów, do których otrzymali dostęp.

Po wdrożeniu architektury obszaru roboczego można wymusić to na zasobach platformy Azure za pomocą [usługi Azure Policy.](../../governance/policy/overview.md) Zapewnia sposób definiowania zasad i zapewnienia zgodności z zasobami platformy Azure, dzięki czemu wysyłają wszystkie dzienniki zasobów do określonego obszaru roboczego. Na przykład za pomocą maszyn wirtualnych platformy Azure lub zestawów skalowania maszyny wirtualnej można użyć istniejących zasad, które oceniają zgodność obszaru roboczego i wyniki raportów lub dostosowują, aby korygować, jeśli są niezgodne.  

## <a name="workspace-consolidation-migration-strategy"></a>Strategia migracji konsolidacji obszarów roboczych

W przypadku klientów, którzy wdrożyli już wiele obszarów roboczych i są zainteresowani konsolidacją do modelu dostępu kontekstu zasobów, zalecamy podjęcie stopniowego podejścia w celu migracji do zalecanego modelu dostępu i nie podejmujesz próby osiągnięcia tego celu. szybko lub agresywnie. Po stopniowym podejściu do planowania, migracji, sprawdzania poprawności i wycofywania po rozsądnej osi czasu pomoże uniknąć nieplanowanych zdarzeń lub nieoczekiwanego wpływu na operacje w chmurze. Jeśli nie masz zasad przechowywania danych ze względów zgodności lub biznesowych, musisz ocenić odpowiedni czas przechowywania danych w obszarze roboczym, z którego przeprowadzasz migrację podczas procesu. Podczas ponownego konfigurowania zasobów do raportowania do udostępnionego obszaru roboczego, nadal można analizować dane w oryginalnym obszarze roboczym w razie potrzeby. Po zakończeniu migracji, jeśli masz prawo do przechowywania danych w oryginalnym obszarze roboczym przed końcem okresu przechowywania, nie usuwaj ich.

Planując migrację do tego modelu, należy wziąć pod uwagę następujące kwestie:

* Dowiedz się, jakie przepisy branżowe i wewnętrzne zasady dotyczące przechowywania danych muszą być przestrzegane.
* Upewnij się, że zespoły aplikacji mogą pracować w ramach istniejących funkcji kontekstu zasobów.
* Zidentyfikuj dostęp przyznany do zasobów dla zespołów aplikacji i przetestuj w środowisku programistycznym przed wdrożeniem w środowisku produkcyjnym.
* Skonfiguruj obszar roboczy, aby włączyć **uprawnienia użyj zasobu lub obszaru roboczego**.
* Usuń uprawnienia zespołów aplikacji do odczytu i kwerendy obszaru roboczego.
* Włącz i skonfiguruj wszystkie rozwiązania do monitorowania, insights, takie jak Usługa Azure Monitor dla kontenerów i/lub usługa Azure Monitor dla maszyn wirtualnych, konta automatyzacji i rozwiązania do zarządzania, takie jak zarządzanie aktualizacjami, maszyny wirtualne Start/Stop itp., które zostały wdrożone w oryginalnym obszarze roboczym.

## <a name="next-steps"></a>Następne kroki

Aby zaimplementować uprawnienia i formanty zabezpieczeń zalecane w tym przewodniku, zapoznaj [się z zarządzaniem dostępem do dzienników](manage-access.md).
