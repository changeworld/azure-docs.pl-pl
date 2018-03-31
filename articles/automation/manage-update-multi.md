---
title: Zarządzanie aktualizacjami dla wielu maszyn wirtualnych platformy Azure
description: W tym temacie przedstawiono sposób zarządzania aktualizacjami dla maszyn wirtualnych platformy Azure.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: c227af1afa95243390152918a6b81015dbaceaeb
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="manage-updates-for-multiple-machines"></a>Zarządzanie aktualizacjami dla wielu maszyn

Przy użyciu rozwiązania Update Management można zarządzać aktualizacjami i poprawkami dla maszyn wirtualnych z systemami Windows i Linux. Korzystając z konta usługi [Azure Automation](automation-offering-get-started.md), można wykonywać następujące czynności:

- Dołączanie maszyn wirtualnych.
- Ocenianie stanu dostępnych aktualizacji.
- Planowanie instalacji wymaganych aktualizacji.
- Przeglądanie wyników wdrożenia w celu sprawdzenia, czy aktualizacje zostały pomyślnie zastosowane na wszystkich maszynach wirtualnych, dla których włączono rozwiązanie Update Management.

## <a name="prerequisites"></a>Wymagania wstępne

Do korzystania z rozwiązania Update Management są wymagane następujące elementy:

* Konto Uruchom jako usługi Azure Automation. Aby uzyskać instrukcje tworzenia takiego konta, zobacz [Wprowadzenie do usługi Azure Automation](automation-offering-get-started.md).

* Maszyna wirtualna lub komputer z zainstalowanym obsługiwanym systemem operacyjnym.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Rozwiązanie Update Management jest obsługiwane w następujących systemach operacyjnych:

### <a name="windows"></a>Windows

* Windows Server 2008 lub nowszy i wdrożenia aktualizacji dla systemu Windows Server 2008 R2 z dodatkiem SP1 lub nowszego. System Nano Server nie jest obsługiwany.

  Obsługa wdrażania aktualizacji dla systemu Windows Server 2008 R2 z dodatkiem SP1 wymaga programu .NET Framework 4.5 i programu Windows Management Framework 5.0 lub nowszego.

* Systemy operacyjne klienta systemu Windows nie są obsługiwane.

Agenci dla systemu Windows muszą być skonfigurowani do komunikowania się z serwerem Windows Server Update Services (WSUS) albo mieć dostęp do usługi Microsoft Update.

> [!NOTE]
> Program System Center Configuration Manager nie może zarządzać współbieżnie agentem dla systemu Windows.
>

### <a name="linux"></a>Linux

* CentOS 6 (x86/x64) i 7 (x64)  
* Red Hat Enterprise 6 (x86/x64) i 7 (x64)  
* SUSE Linux Enterprise Server 11 (x86/x64) i 12 (x64)  
* Ubuntu 12.04 LTS i nowsze (x86/x64)   

> [!NOTE]  
> Aby w przypadku systemu Ubuntu uniknąć stosowania aktualizacji poza oknem obsługi, zmień konfigurację pakietu Unattended-Upgrade tak, aby wyłączyć automatyczne aktualizacje. Aby uzyskać więcej informacji, zobacz [temat poświęcony aktualizacjom automatycznym w podręczniku systemu Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.

To rozwiązanie nie obsługuje agenta pakietu OMS Linux skonfigurowany pod kątem raportowania do wielu obszarów roboczych analizy dzienników.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Włączanie rozwiązania Update Management dla maszyn wirtualnych platformy Azure

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. W lewym okienku wybierz pozycję **Zarządzanie aktualizacjami**.
3. W górnej części okna wybierz pozycję **Dodaj maszynę wirtualną Azure**.
   ![Karta Dodaj maszynę wirtualną Azure](./media/manage-update-multi/update-onboard-vm.png)
4. Wybierz maszynę wirtualną, którą chcesz dołączyć. Zostanie wyświetlone okno dialogowe **Włączanie rozwiązania Update Management**.
5. Wybierz pozycję **Włącz**.

   ![Okno dialogowe Włączanie rozwiązania Update Management](./media/manage-update-multi/update-enable.png)

Rozwiązanie Update Management zostanie włączone dla maszyny wirtualnej.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Włączanie rozwiązania Update Management dla maszyn wirtualnych i komputerów spoza platformy Azure

Aby uzyskać instrukcje dotyczące włączania rozwiązania Update Management dla maszyn wirtualnych i komputerów z systemem Windows spoza platformy Azure, zobacz [Łączenie komputerów z systemem Windows z usługą Log Analytics na platformie Azure](../log-analytics/log-analytics-windows-agent.md).

Aby uzyskać instrukcje dotyczące włączania rozwiązania Update Management dla maszyn wirtualnych i komputerów z systemem Linux spoza platformy Azure, zobacz [Łączenie komputerów z systemem Linux z usługą Log Analytics](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Wyświetlanie komputerów dołączonych do konta usługi Automation
Po włączeniu zarządzania aktualizacjami dla maszyn, informacje o maszynach można wyświetlić, klikając pozycję **Komputery**. Dostępne są takie informacje o komputerach, jak *Nazwa*, *Zgodność*, *Środowisko*, *Typ systemu operacyjnego*, *Aktualizacje krytyczne i aktualizacje zabezpieczeń* oraz *Inne aktualizacje*. 

  ![Karta z wyświetlonymi komputerami](./media/manage-update-multi/update-computers-tab.png)

Komputery, dla których niedawno włączono zarządzanie aktualizacjami, mogą nie być jeszcze ocenione. Stan zgodności tych komputerów będzie mieć wartość *Nie oceniono*.  Oto lista wartości stanu zgodności:
* Zgodne —komputery z wszystkimi aktualizacjami krytycznymi i aktualizacjami zabezpieczeń.
* Niezgodne —komputery, na których brakuje co najmniej jednej aktualizacji krytycznej lub aktualizacji zabezpieczeń.
* Nie oceniono — dane oceny aktualizacji nie zostały wysłane z komputera w oczekiwanym czasie.  W przypadku komputerów z systemem Linux okres ten obejmuje ostatnie trzy godziny, a w przypadku komputerów z systemem Windows — ostatnie 12 godzin.  

## <a name="view-an-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu rozwiązania Update Management zostanie wyświetlone okno dialogowe **Update Management**. Możesz wyświetlić listę brakujących aktualizacji na karcie **Brakujące aktualizacje**.

## <a name="collect-data"></a>Zbieranie danych

Agenci zainstalowani na maszynach wirtualnych i komputerach na potrzeby zbierania danych o aktualizacjach i wysyłania ich do rozwiązania Azure Update Management.

### <a name="supported-agents"></a>Obsługiwani agenci

W poniższej tabeli opisano połączone źródła obsługiwane przez to rozwiązanie:

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| Agenci dla systemu Windows |Yes |Rozwiązanie Update Management zbiera informacje o aktualizacjach systemu z agentów dla systemu Windows i inicjuje instalowanie wymaganych aktualizacji. |
| Agenci dla systemu Linux |Yes |Rozwiązanie Update Management zbiera informacje o aktualizacjach systemu z agentów dla systemu Linux i inicjuje instalowanie wymaganych aktualizacji w obsługiwanych dystrybucjach. |
| Grupa zarządzania programu Operations Manager |Yes |Rozwiązanie Update Management zbiera informacje o aktualizacjach systemu z agentów w połączonej grupie zarządzania. |
| Konto magazynu Azure |Nie |Magazyn Azure nie zawiera informacji o aktualizacjach systemu. |

### <a name="collection-frequency"></a>Częstotliwość zbierania

Skanowanie każdego zarządzanego komputera z systemem Windows jest uruchamiane dwa razy dziennie. Co 15 minut wywoływany jest interfejs API systemu Windows, aby wykonać zapytanie o czas ostatniej aktualizacji w celu sprawdzenia, czy stan się zmienił. Jeśli tak, rozpoczyna się skanowanie pod kątem zgodności. Skanowanie każdego zarządzanego komputera z systemem Linux jest uruchamiane co 3 godziny.

Wyświetlenie zaktualizowanych danych z zarządzanych komputerów na pulpicie nawigacyjnym może potrwać od 30 minut do 6 godzin.

## <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Aby zainstalować aktualizacje, zaplanuj wdrożenie zgodnie z harmonogramem wydawania i oknem obsługi.
Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

Zaplanuj nowe wdrożenie aktualizacji dla co najmniej jednej maszyny wirtualnej, wybierając pozycję **Zaplanuj wdrażanie aktualizacji** w górnej części okna dialogowego **Update Management**. W okienku **Nowe wdrożenie aktualizacji** podaj następujące informacje:

* **Nazwa**: wprowadź unikatową nazwę identyfikującą wdrożenie aktualizacji.
* **Typ systemu operacyjnego**: wybierz system Windows lub Linux.
* **Komputery do zaktualizowania**: wybierz maszyny wirtualne, które chcesz zaktualizować.

  ![Okienko „Nowe wdrożenie aktualizacji”](./media/manage-update-multi/update-select-computers.png)

* **Klasyfikacja aktualizacji**: wybierz typy oprogramowania, które zostaną uwzględnione we wdrożeniu aktualizacji. Dostępne są następujące typy klasyfikacji:
  * Aktualizacje krytyczne
  * Aktualizacje zabezpieczeń
  * Pakiety zbiorcze aktualizacji
  * Pakiety funkcji
  * Dodatki Service Pack
  * Aktualizacje definicji
  * Narzędzia
  * Aktualizacje
* **Ustawienia harmonogramu**: możesz zaakceptować domyślną datę i godzinę, czyli 30 minut po bieżącej godzinie. Możesz też określić inny czas.
   Możesz też określić, czy wdrożenie ma występować raz, czy zgodnie z harmonogramem cyklicznym. Aby skonfigurować harmonogram cykliczny, wybierz opcję **Cyklicznie** w obszarze **Cykl**.

   ![Okno dialogowe Ustawienia harmonogramu](./media/manage-update-multi/update-set-schedule.png)

* **Okno obsługi (minuty)**: podaj okres, w którym ma zostać przeprowadzone wdrażanie aktualizacji. To ustawienie pozwala zagwarantować, że zmiany będą wprowadzane w ramach zdefiniowanych okien obsługi.

Po ukończeniu konfigurowania harmonogramu wrócić do pulpitu nawigacyjnego stanu, wybierając przycisk **Utwórz**. Tabela **Zaplanowane** pokaże utworzony właśnie harmonogram wdrożenia.

> [!WARNING]
> W przypadku aktualizacji wymagających ponownego uruchomienia maszyna wirtualna zostanie automatycznie uruchomiona ponownie.

## <a name="view-results-of-an-update-deployment"></a>Wyświetlanie wyników wdrażania aktualizacji

Po rozpoczęciu zaplanowanego wdrażania stan tego wdrożenia można sprawdzić na karcie **Wdrożenia aktualizacji** w oknie dialogowym **Update Management**.
Jeśli wdrożenie jest aktualnie uruchomione, wyświetlany jest stan **W toku**. Po pomyślnym zakończeniu wdrożenia stan zmienia się na **Powodzenie**.
W przypadku błędu co najmniej jednej aktualizacji w ramach wdrożenia jest wyświetlany stan **Częściowe niepowodzenie**.

![Stan wdrożenia aktualizacji](./media/manage-update-multi/update-view-results.png)

Aby wyświetlić pulpit nawigacyjny wdrożenia aktualizacji, wybierz ukończone wdrożenie.

Okienko **Wyniki aktualizacji** zawiera łączną liczbę aktualizacji i wyniki wdrożenia na maszynie wirtualnej.
Tabela po prawej stronie zawiera szczegółowy podział każdej aktualizacji i wyniki instalacji. Wyniki instalacji mogą mieć jedną z następujących wartości:

* Nie podjęto próby: nie zainstalowano aktualizacji z powodu niewystarczającego czasu w zdefiniowanym oknie konserwacji.
* Powodzenie: aktualizacja powiodła się.
* Niepowodzenie: aktualizacja nie powiodła się.

Aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie, wybierz pozycję **Wszystkie dzienniki**.

Aby wyświetlić strumień zadań elementu runbook, który zarządza wdrożeniem aktualizacji na docelowej maszynie wirtualnej, wybierz kafelek **Dane wyjściowe**.

Aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem, wybierz pozycję **Błędy**.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat zarządzania aktualizacjami — takie jak dzienniki, dane wyjściowe i komunikaty o błędach, zobacz [rozwiązania do zarządzania aktualizacji w usłudze Azure](../operations-management-suite/oms-solution-update-management.md).

