---
title: Zarządzanie aktualizacjami dla wielu maszyn wirtualnych platformy Azure
description: W tym artykule opisano sposób zarządzania aktualizacjami dla maszyn wirtualnych platformy Azure i innych niż platformy Azure.
services: automation
ms.subservice: update-management
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: de7171d3807540ae7d5f09c3a877031631248e49
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76168048"
---
# <a name="manage-updates-for-multiple-machines"></a>Zarządzanie aktualizacjami dla wielu maszyn

Możesz użyć rozwiązania Update Management do zarządzania aktualizacjami i poprawkami dla maszyn wirtualnych z systemami Windows i Linux. Korzystając z konta usługi [Azure Automation](automation-offering-get-started.md), można wykonywać następujące czynności:

- Dołączanie maszyn wirtualnych
- Ocenianie stanu dostępnych aktualizacji
- Zaplanuj instalację wymaganych aktualizacji
- Przejrzyj wyniki wdrożenia, aby sprawdzić, czy aktualizacje zostały pomyślnie zastosowane do wszystkich maszyn wirtualnych, dla których Update Management jest włączona

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z Update Management, potrzebne są:

- Maszyna wirtualna lub komputer z zainstalowanym obsługiwanym systemem operacyjnym.

- Dostęp do repozytorium aktualizacji dla maszyn wirtualnych z systemem Linux dołączanych do rozwiązania.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Update Management jest obsługiwana w następujących systemach operacyjnych:

|System operacyjny  |Uwagi  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Obsługuje tylko oceny aktualizacji.         |
|Windows Server 2008 R2 z dodatkiem SP1 lub nowszym     |Wymagany jest program Windows PowerShell w wersji 4,0 lub nowszej. ([Pobierz zawmf 4,0](https://www.microsoft.com/download/details.aspx?id=40855))</br> W celu zwiększenia niezawodności zaleca się używanie programu Windows PowerShell 5,1. ([Pobierz zawmf 5,1](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) i 7 (x64)      | |
|Red Hat Enterprise 6 (x86/x64) i 7 (x64)     | |
|SUSE Linux Enterprise Server 11 (x86/x64) i 12 (x64)     | |
|Ubuntu 14,04 LTS, 16,04 LTS i 18,04 LTS (x86/x64)      | |

> [!NOTE]
> Aby w przypadku systemu Ubuntu uniknąć stosowania aktualizacji poza oknem obsługi, zmień konfigurację pakietu Unattended-Upgrade tak, aby wyłączyć automatyczne aktualizacje. Aby uzyskać więcej informacji, zobacz [temat poświęcony aktualizacjom automatycznym w podręczniku systemu Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

## <a name="enable-update-management-for-azure-virtual-machines"></a>Włącz Update Management dla usługi Azure Virtual Machines

W Azure Portal Otwórz konto usługi Automation, a następnie wybierz pozycję **Update Management**.

Wybierz pozycję **Dodaj maszyny wirtualne platformy Azure**.

![Karta Dodawanie maszyny wirtualnej platformy Azure](./media/manage-update-multi/update-onboard-vm.png)

Wybierz maszynę wirtualną, którą chcesz dołączyć.

W obszarze **włącz Update Management**wybierz pozycję **Włącz** , aby dołączyć maszynę wirtualną.

![Okno dialogowe Włączanie rozwiązania Update Management](./media/manage-update-multi/update-enable.png)

Po zakończeniu dołączania Update Management jest włączona dla swojej maszyny wirtualnej.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Włącz Update Management dla maszyn wirtualnych i komputerów spoza platformy Azure

Log Analytics agenta dla systemów Windows i Linux należy zainstalować na maszynach wirtualnych działających w sieci firmowej lub w innym środowisku chmury, aby umożliwić ich korzystanie z Update Management. Aby poznać wymagania systemowe i obsługiwane metody wdrażania agenta na maszynach hostowanych poza platformą Azure, zobacz [Omówienie agenta log Analytics](../azure-monitor/platform/log-analytics-agent.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Wyświetl komputery dołączone do konta usługi Automation

Po włączeniu Update Management dla maszyn można wyświetlić informacje o maszynie, wybierając pozycję **komputery**. Można wyświetlić informacje na temat *nazwy komputera*, *stanu zgodności*, *środowiska*, *typu systemu operacyjnego*, *zainstalowanych aktualizacji krytycznych i zabezpieczeń*, *innych zainstalowanych aktualizacji*oraz *aktualizacji gotowości agenta* dla komputerów.

  ![Karta z wyświetlonymi komputerami](./media/manage-update-multi/update-computers-tab.png)

Komputery, na których ostatnio włączono obsługę Update Management, mogły nie zostać jeszcze ocenione. **Nie oceniono**stanu stanu zgodności dla tych komputerów. Poniżej znajduje się lista możliwych wartości stanu zgodności:

- **Zgodne**: komputery, na których nie brakuje aktualizacji krytycznych lub zabezpieczeń.

- **Niezgodne**: komputery, na których brakuje co najmniej jednej aktualizacji krytycznej lub zabezpieczeń.

- **Nieocenione**: dane oceny aktualizacji nie zostały odebrane z komputera w oczekiwanym przedziale czasu. W przypadku komputerów z systemem Linux czas oczekiwania wynosi w ciągu ostatniej godziny. W przypadku komputerów z systemem Windows oczekiwany przedział czasu jest w ciągu ostatnich 12 godzin.

Aby wyświetlić stan agenta, wybierz łącze w kolumnie **Aktualizowanie gotowości agenta** . Wybranie tej opcji powoduje otwarcie okienka **hybrydowego procesu roboczego** i wyświetlenie stanu hybrydowego procesu roboczego. Na poniższej ilustracji przedstawiono przykład agenta, który nie został podłączony do Update Management przez dłuższy czas:

![Karta z wyświetlonymi komputerami](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu rozwiązania Update Management zostanie otwarte okienko **Update Management**. Możesz wyświetlić listę brakujących aktualizacji na karcie **Brakujące aktualizacje**.

## <a name="collect-data"></a>Zbieranie danych

Agenci zainstalowani na maszynach wirtualnych i komputerach zbierają dane o aktualizacjach. Agenci wysyłają dane do usługi Azure Update Management.

### <a name="supported-agents"></a>Obsługiwani agenci

W poniższej tabeli opisano połączone źródła obsługiwane przez to rozwiązanie:

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| Agenci dla systemu Windows |Tak |Update Management zbiera informacje o aktualizacjach systemu z agentów systemu Windows, a następnie inicjuje instalację wymaganych aktualizacji. |
| Agenci dla systemu Linux |Tak |Update Management zbiera informacje o aktualizacjach systemu z agentów z systemem Linux, a następnie inicjuje instalację wymaganych aktualizacji w obsługiwanych dystrybucjach. |
| Grupa zarządzania programu Operations Manager |Tak |Update Management zbiera informacje o aktualizacjach systemu z agentów w połączonej grupie zarządzania. |
| Konto usługi Azure Storage |Nie |Usługa Azure Storage nie zawiera informacji o aktualizacjach systemu. |

### <a name="collection-frequency"></a>Częstotliwość zbierania

Gdy komputer ukończy skanowanie pod kątem zgodności aktualizacji, Agent przekaże informacje zbiorczo do dzienników Azure Monitor. Na komputerze z systemem Windows skanowanie zgodności jest domyślnie uruchamiane co 12 godzin.

Oprócz harmonogramu skanowania skanowanie pod kątem zgodności z aktualizacjami jest inicjowane w ciągu 15 minut od ponownego uruchomienia MMA, przed instalacją aktualizacji i po zainstalowaniu aktualizacji.

W przypadku komputera z systemem Linux skanowanie zgodności jest wykonywane co godzinę domyślnie. Jeśli Agent MMA zostanie ponownie uruchomiony, skanowanie zgodności zostanie zainicjowane w ciągu 15 minut.

Wyświetlenie zaktualizowanych danych z zarządzanych komputerów na pulpicie nawigacyjnym może potrwać od 30 minut do 6 godzin.

## <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Aby zainstalować aktualizacje, Zaplanuj wdrożenie, które jest wyrównane z harmonogramem wydania i oknem usługi. Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

>[!NOTE]
>Zaplanowanie wdrożenia aktualizacji powoduje utworzenie zasobu [harmonogramu](shared-resources/schedules.md) połączonego z elementem Runbook **MicrosoftOMSComputers poprawek** , który obsługuje wdrożenie aktualizacji na komputerach docelowych. W przypadku usunięcia zasobu harmonogramu z Azure Portal lub przy użyciu programu PowerShell po utworzeniu wdrożenia zostanie ono przerwane i zostanie wyświetlony komunikat o błędzie podczas próby ponownego skonfigurowania go z poziomu portalu. Zasób harmonogramu można usunąć tylko przez usunięcie odpowiedniego harmonogramu wdrażania.
>

Aby zaplanować nowe wdrożenie aktualizacji dla co najmniej jednej maszyny wirtualnej, w obszarze **Zarządzanie aktualizacjami**wybierz pozycję **Zaplanuj wdrożenie aktualizacji**.

W okienku **nowe wdrożenie aktualizacji** podaj następujące informacje:

- **Nazwa**: Wprowadź unikatową nazwę identyfikującą wdrożenie aktualizacji.
- **System operacyjny**: Wybierz **system Windows** lub **Linux**.
- **Grupy do zaktualizowania**: Zdefiniuj zapytanie w oparciu o kombinację subskrypcji, grup zasobów, lokalizacji i tagów, aby utworzyć dynamiczną grupę maszyn wirtualnych platformy Azure, które mają zostać uwzględnione we wdrożeniu. W przypadku maszyn wirtualnych spoza platformy Azure zapisane wyszukiwania są używane do tworzenia grupy dynamicznej do uwzględnienia w danym wdrożeniu. Aby dowiedzieć się więcej, zobacz [grupy dynamiczne](automation-update-management-groups.md).
- **Maszyny do zaktualizowania**: Wybierz zapisane wyszukiwanie, zaimportowaną grupę lub wybierz maszyny, aby wybrać maszyny, które chcesz zaktualizować.

   >[!NOTE]
   >Wybranie zapisanej opcji wyszukiwania nie zwraca tożsamości komputera, tylko ich nazwy. Jeśli masz kilka maszyn wirtualnych o tej samej nazwie w wielu grupach zasobów, są one zwracane w wynikach. Zaleca się dołączenie unikatowych maszyn wirtualnych spełniających kryteria przy użyciu opcji **grupy do aktualizacji** .

   Jeśli wybierzesz pozycję **Maszyny**, gotowość maszyny będzie wyświetlana w kolumnie **AKTUALIZUJ GOTOWOŚĆ AGENTA**. Stan kondycji maszyny można sprawdzić przed zaplanowaniem wdrożenia aktualizacji. Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach usługi Azure Monitor, zobacz [Computer groups in Azure Monitor logs (Grupy komputerów w dziennikach usługi Azure Monitor)](../azure-monitor/platform/computer-groups.md)

  ![Nowe okienko wdrożenia aktualizacji](./media/manage-update-multi/update-select-computers.png)

- **Klasyfikacja aktualizacji**: Wybierz typy oprogramowania, które mają zostać uwzględnione we wdrożeniu aktualizacji. Opis typów klasyfikacji zawiera temat [Aktualizowanie klasyfikacji](automation-view-update-assessments.md#update-classifications). Dostępne są następujące typy klasyfikacji:
  - Aktualizacje krytyczne
  - Aktualizacje zabezpieczeń
  - Pakiety zbiorcze aktualizacji
  - Pakiety funkcji
  - Dodatki Service Pack
  - Aktualizacje definicji
  - narzędzia
  - Aktualizacje

- **Aktualizacje do uwzględnienia/wykluczenia** — spowoduje to otwarcie strony **Uwzględnij/Wyklucz**. Aktualizacje, które mają zostać uwzględnione lub wykluczone, znajdują się na osobnych kartach. Aby uzyskać dodatkowe informacje na temat obsługi dołączania, zobacz [Planowanie wdrożenia aktualizacji](automation-tutorial-update-management.md#schedule-an-update-deployment).

> [!NOTE]
> Ważne jest, aby wiedzieć, że wykluczenia zastępują dołączenia. Na przykład, jeśli zdefiniujesz regułę wykluczania `*`, żadne poprawki ani pakiety nie zostaną zainstalowane, ponieważ są wykluczone. Wykluczone poprawki nadal są wyświetlane jako brakujące na komputerze. W przypadku maszyn z systemem Linux, jeśli pakiet został uwzględniony, ale jego pakiet zależny został wykluczony, pakiet nie jest zainstalowany.

> [!NOTE]
> Nie można określić aktualizacji, które zostały zastąpione w celu włączenia ich do wdrożenia aktualizacji.
>

- **Ustawienia harmonogramu**: możesz zaakceptować domyślną datę i godzinę, czyli 30 minut po bieżącej godzinie. Możesz również określić inny czas.

   Możesz też określić, czy wdrożenie ma występować raz, czy zgodnie z harmonogramem cyklicznym. Aby skonfigurować harmonogram cykliczny, w obszarze **cykl**wybierz pozycję **cykliczne**.

   ![Okno dialogowe Ustawienia harmonogramu](./media/manage-update-multi/update-set-schedule.png)

- **Skrypty wstępne i końcowe**: wybierz skrypty do uruchomienia przed i po wdrożeniu. Aby dowiedzieć się więcej, zobacz [Zarządzanie skryptami wstępnymi i końcowymi](pre-post-scripts.md).
- **Okno obsługi (minuty)** : Określ okres, w którym ma wystąpić wdrożenie aktualizacji. To ustawienie pozwala zagwarantować, że zmiany będą wprowadzane w ramach zdefiniowanych okien obsługi.

- **Kontrola ponownego uruchamiania** — to ustawienie określa sposób obsługi ponownych uruchomień dla wdrożenia aktualizacji.

   |Opcja|Opis|
   |---|---|
   |Uruchom ponownie, jeśli jest to wymagane| **(Ustawienie domyślne)** W razie potrzeby zostanie zainicjowany ponowny rozruch, jeśli zezwoli na okno obsługi.|
   |Zawsze uruchamiaj ponownie|Inicjowany jest ponowny rozruch bez względu na to, czy jest to wymagane. |
   |Nigdy nie uruchamiaj ponownie|Niezależnie od tego, czy jest wymagany ponowny rozruch, ponowne uruchomienia są pomijane.|
   |Tylko ponowne uruchomienie — aktualizacje nie zostaną zainstalowane|Ta opcja ignoruje Instalowanie aktualizacji i inicjuje ponowny rozruch.|

Po zakończeniu konfigurowania harmonogramu wybierz przycisk **Utwórz** , aby powrócić do pulpitu nawigacyjnego stanu. W **planowanej** tabeli przedstawiono utworzony harmonogram wdrożenia.

> [!NOTE]
> Rozwiązanie Update Management obsługuje wdrażanie aktualizacji tej samej firmy i wstępne pobieranie poprawek. Wymaga to zmian w systemach z poprawkami, zobacz [pierwsza firma i pomoc wstępna pobierania](automation-configure-windows-update.md#pre-download-updates) , aby dowiedzieć się, jak skonfigurować te ustawienia w systemach.

## <a name="view-results-of-an-update-deployment"></a>Wyświetlanie wyników wdrażania aktualizacji

Po rozpoczęciu zaplanowanego wdrażania stan tego wdrożenia można sprawdzić na karcie **Wdrożenia aktualizacji** w obszarze rozwiązania **Update Management**.

Jeśli wdrożenie jest aktualnie uruchomione, wyświetlany jest stan **W toku**. Po pomyślnym zakończeniu wdrożenia stan zmieni się na **powodzenie**.

W przypadku błędu co najmniej jednej aktualizacji w ramach wdrożenia jest wyświetlany stan **Częściowe niepowodzenie**.

![Stan wdrożenia aktualizacji](./media/manage-update-multi/update-view-results.png)

Aby wyświetlić pulpit nawigacyjny wdrożenia aktualizacji, wybierz ukończone wdrożenie.

Okienko **wyniki aktualizacji** zawiera łączną liczbę aktualizacji i wyniki wdrożenia maszyny wirtualnej. Tabela po prawej stronie zawiera szczegółowy podział każdej aktualizacji i wyniki instalacji. Wyniki instalacji mogą mieć jedną z następujących wartości:

- **Nie podjęto próby**: aktualizacja nie została zainstalowana, ponieważ na podstawie zdefiniowanego okna obsługi była dostępna niewystarczająca ilość czasu.
- **Powodzenie**: aktualizacja powiodła się.
- **Niepowodzenie**: aktualizacja nie powiodła się.

Aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie, wybierz pozycję **Wszystkie dzienniki**.

Aby wyświetlić strumień zadań elementu Runbook, który zarządza wdrożeniem aktualizacji na docelowej maszynie wirtualnej, wybierz kafelek dane wyjściowe.

Aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem, wybierz pozycję **Błędy**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o Update Management, w tym o dziennikach, danych wyjściowych i błędach, zobacz [Update Management rozwiązanie na platformie Azure](../operations-management-suite/oms-solution-update-management.md).

