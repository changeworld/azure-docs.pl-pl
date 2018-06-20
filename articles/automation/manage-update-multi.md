---
title: Zarządzanie aktualizacjami dla wielu maszyn wirtualnych platformy Azure
description: W tym artykule opisano sposób zarządzania aktualizacjami maszyn wirtualnych platformy Azure.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 66d50c94f2aad15e0d4a1b7500e8a4aeb45b1742
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214257"
---
# <a name="manage-updates-for-multiple-machines"></a>Zarządzanie aktualizacjami dla wielu maszyn

Rozwiązanie do zarządzania aktualizacji umożliwia zarządzanie aktualizacje i poprawki dla maszyn wirtualnych systemu Windows i Linux. Korzystając z konta usługi [Azure Automation](automation-offering-get-started.md), można wykonywać następujące czynności:

- Dodaj maszyny wirtualne
- Ocenić stan dostępne aktualizacje
- Harmonogram instalacji wymaganych aktualizacji
- Przejrzyj wyniki wdrożenia, aby sprawdzić, czy aktualizacje zostały pomyślnie zastosowane do wszystkich maszyn wirtualnych, dla których włączono zarządzanie aktualizacjami

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć zarządzania aktualizacjami, potrzebne są:

- Konto Uruchom jako usługi Azure Automation. Aby dowiedzieć się, jak utworzyć, zobacz [wprowadzenie do korzystania z usługi Automatyzacja Azure](automation-offering-get-started.md).
- Maszyna wirtualna lub komputer z zainstalowanym obsługiwanym systemem operacyjnym.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Zarządzanie aktualizacjami jest obsługiwane w następujących systemach operacyjnych:

|System operacyjny  |Uwagi  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Obsługuje tylko zaktualizować oceny.         |
|Windows Server 2008 R2 z dodatkiem SP1 lub nowszy     |Środowisko Windows PowerShell w wersji 4.0 lub nowszy jest wymagany. ([Pobierz WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))</br> Windows PowerShell w wersji 5.1 zaleca się zwiększenie niezawodności. ([Pobierz WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) i 7 (x64)      | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|Red Hat Enterprise 6 (x86/x64) i 7 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|SUSE Linux Enterprise Server 11 (x86/x64) i 12 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|Ubuntu 12.04 LTS, 14.04 LTS i 16.04 LTS (x86/x64)      |Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.         |

> [!NOTE]
> Aby w przypadku systemu Ubuntu uniknąć stosowania aktualizacji poza oknem obsługi, zmień konfigurację pakietu Unattended-Upgrade tak, aby wyłączyć automatyczne aktualizacje. Aby uzyskać więcej informacji, zobacz [temat poświęcony aktualizacjom automatycznym w podręczniku systemu Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.

To rozwiązanie nie obsługuje agenta Operations Management Suite (OMS) dla systemu Linux, który jest skonfigurowany pod kątem raportowania do wielu obszarów roboczych usługi Analiza dzienników Azure.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Włącz zarządzanie aktualizacji dla maszyn wirtualnych platformy Azure

W portalu Azure Otwórz Twoje konto usługi Automatyzacja, a następnie wybierz **zarządzanie aktualizacjami**.

Wybierz **dodać maszyny Wirtualnej Azure**.

![Dodaj kartę maszyny Wirtualnej Azure](./media/manage-update-multi/update-onboard-vm.png)

Wybierz maszynę wirtualną, którą chcesz dołączyć. 

W obszarze **włączyć zarządzanie aktualizacjami**, wybierz pozycję **włączyć** dołączyć maszyny wirtualnej.

![Okno dialogowe Włączanie rozwiązania Update Management](./media/manage-update-multi/update-enable.png)

Po zakończeniu operacji przechodzenia do zarządzania aktualizacjami jest włączona dla maszyny wirtualnej.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Włącz zarządzanie aktualizacji dla maszyn wirtualnych innych niż Azure i komputerów

Informacje na temat włączania zarządzania aktualizacjami w maszynach wirtualnych Azure Windows i komputerami, zobacz [komputery Windows połączenia z usługą analizy dzienników na platformie Azure](../log-analytics/log-analytics-windows-agent.md).

Informacje na temat włączania zarządzania aktualizacjami dla maszyn wirtualnych z systemem innym niż Azure Linux i komputerów, zobacz [połączenie komputerów Linux Log Analytics](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Wyświetl komputery dołączonych do konta automatyzacji

Po włączeniu zarządzania aktualizacjami dla maszyn, wybierając można wyświetlić informacji o maszynie **komputerów**. Można przeglądać informacje o *Nazwa maszyny*, *stanu zgodności*, *środowiska*, *typ systemu operacyjnego*, *krytyczne i zainstalowane aktualizacje zabezpieczeń*, *inne aktualizacje zainstalowane*, i *zaktualizować agenta gotowości* dla komputerów.

  ![Karta z wyświetlonymi komputerami](./media/manage-update-multi/update-computers-tab.png)

Komputery, na których niedawno została włączona do zarządzania aktualizacjami może nie został jeszcze ocenie. Stan stanu zgodności dla tych komputerów jest **nie oceniane**. Poniżej przedstawiono listę możliwych wartości dla stanu zgodności:

- **Zgodne**: komputery, które są nie brakuje krytycznych lub aktualizacji zabezpieczeń.

- **Niezgodne**: komputery, których brakuje co najmniej jeden krytyczny lub aktualizacji zabezpieczeń.

- **Nie są oceniane**: danych oceny aktualizacji nie zostały odebrane z komputera w oczekiwanym czasie. W przypadku komputerów z systemem Linux przedział czasu oczekiwania jest w ciągu ostatnich 3 godzin. W przypadku komputerów z systemem Windows oczekiwanego przedział czasu jest w ciągu ostatnich 12 godzin.

Aby wyświetlić stan agenta, wybierz łącze w **aktualizacji AGENTA gotowości** kolumny. Wybranie tej opcji otwiera **hybrydowy proces roboczy** okienko i wyświetla stan hybrydowy proces roboczy. Na poniższej ilustracji przedstawiono przykład agenta, która nie została podłączona do zarządzania aktualizacjami przez dłuższy czas:

![Karta z wyświetlonymi komputerami](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu zarządzania aktualizacjami **zarządzanie aktualizacjami** zostanie otwarte okienko. Możesz wyświetlić listę brakujących aktualizacji na karcie **Brakujące aktualizacje**.

## <a name="collect-data"></a>Zbieranie danych

Agenci, którzy są instalowane na maszynach wirtualnych i komputerach zbiera dane dotyczące aktualizacji. Agenci wysyłają dane do usługi Azure Management aktualizacji.

### <a name="supported-agents"></a>Obsługiwani agenci

W poniższej tabeli opisano połączone źródła obsługiwane przez to rozwiązanie:

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| Agenci dla systemu Windows |Yes |Zarządzanie aktualizacjami zbiera informacje o aktualizacjach systemu z agentów systemu Windows i inicjuje instalacji wymaganych aktualizacji. |
| Agenci dla systemu Linux |Yes |Zarządzanie aktualizacjami zbiera informacje o aktualizacjach systemu z agentów systemów Linux i inicjuje instalacji wymaganych aktualizacji na obsługiwanych dystrybucjach. |
| Grupa zarządzania programu Operations Manager |Yes |Zarządzanie aktualizacjami zbiera informacje o aktualizacji systemu przez agentów w dołączonej grupy zarządzania. |
| Konto usługi Azure Storage |Nie |Usługa Azure Storage nie zawiera informacji o aktualizacjach systemu. |

### <a name="collection-frequency"></a>Częstotliwość zbierania

Skanowanie jest przeprowadzane dwa razy dziennie dla każdego zarządzanego komputera z systemem Windows. Co 15 minut, wywołuje interfejs API systemu Windows można wyszukiwać czas ostatniej aktualizacji określić, czy stan został zmieniony. Zmiana stanu uruchamiania skanowania zgodności. Skanowanie jest przeprowadzane co 3 godziny dla każdego zarządzanego komputera z systemem Linux.

Może upłynąć od 30 minut do 6 godzin dla pulpitu nawigacyjnego wyświetlić zaktualizowane dane z zarządzanych komputerów.

## <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Do zainstalowania aktualizacji, należy zaplanować wdrożenie, aby była zgodna z wersji okna Harmonogram i usługi. Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

Aby zaplanować nowe wdrożenie aktualizacji dla co najmniej jednej maszyny wirtualnej, w obszarze **zarządzanie aktualizacjami**, wybierz pozycję **harmonogram wdrożenia aktualizacji**.

W **nowego wdrożenia aktualizacji** okienka, podaj następujące informacje:

- **Nazwa**: wprowadź unikatową nazwę identyfikującą wdrożenia aktualizacji.
- **System operacyjny**: Wybierz **Windows** lub **Linux**.
- **Maszyny, aby zaktualizować**: Wybierz maszyny wirtualne, które chcesz zaktualizować. Gotowość maszyny jest wyświetlany w obszarze **aktualizacji AGENTA gotowości** kolumny. Widać stanu kondycji komputera, aby zaplanować wdrożenie aktualizacji.

  ![Nowe okienko wdrożenia aktualizacji](./media/manage-update-multi/update-select-computers.png)

- **Klasyfikacja aktualizacji**: Wybierz typy oprogramowania do uwzględnienia w wdrożenia aktualizacji. Opis typów klasyfikacji, zobacz [klasyfikacje aktualizacji](automation-update-management.md#update-classifications). Dostępne są następujące typy klasyfikacji:
  - Aktualizacje krytyczne
  - Aktualizacje zabezpieczeń
  - Pakiety zbiorcze aktualizacji
  - Pakiety funkcji
  - Dodatki Service Pack
  - Aktualizacje definicji
  - Narzędzia
  - Aktualizacje

- **Aktualizacje, które mają zostać wykluczone**: Wybranie tej opcji otwiera **wykluczyć** strony. Wprowadź artykułów bazy wiedzy lub nazwy pakietu do wykluczenia.

- **Ustawienia harmonogramu**: możesz zaakceptować domyślną datę i godzinę, czyli 30 minut po bieżącej godzinie. Można również określić inną godzinę.

   Możesz też określić, czy wdrożenie ma występować raz, czy zgodnie z harmonogramem cyklicznym. Aby skonfigurować Harmonogram cykliczny, w obszarze **cyklu**, wybierz pozycję **cykliczny**.

   ![Okno dialogowe Ustawienia harmonogramu](./media/manage-update-multi/update-set-schedule.png)
- **Okno konserwacji (w minutach)**: Określ czas, który ma nastąpić wdrożenia aktualizacji. To ustawienie pozwala zagwarantować, że zmiany będą wprowadzane w ramach zdefiniowanych okien obsługi.

Po zakończeniu konfigurowania harmonogramu wybierz **Utwórz** przycisk, aby wrócić do pulpitu nawigacyjnego stanu. **Zaplanowane** tabeli przedstawiono harmonogram wdrożenia utworzony.

> [!WARNING]
> Aktualizacje, które wymagają ponownego uruchomienia maszyny wirtualnej do automatycznego uruchomienia.

## <a name="view-results-of-an-update-deployment"></a>Wyświetlanie wyników wdrażania aktualizacji

Po uruchomieniu zaplanowanego wdrożenia, możesz wyświetlać stan tego wdrożenia na **wdrożeń aktualizacji** w obszarze **zarządzanie aktualizacjami**.

Jeśli wdrożenie jest aktualnie uruchomione, wyświetlany jest stan **W toku**. Po zakończeniu wdrożenia pomyślnie stan zmienia się na **zakończyło się pomyślnie**.

W przypadku błędu co najmniej jednej aktualizacji w ramach wdrożenia jest wyświetlany stan **Częściowe niepowodzenie**.

![Stan wdrożenia aktualizacji](./media/manage-update-multi/update-view-results.png)

Aby wyświetlić pulpit nawigacyjny wdrożenia aktualizacji, wybierz ukończone wdrożenie.

**Wyniki aktualizacji** okienko zawiera całkowitą liczbę aktualizacji oraz wyniki wdrażania dla maszyny wirtualnej. W tabeli po prawej stronie przedstawiono szczegółowe podział każdej aktualizacji i wyniki instalacji. Wyniki instalacji mogą mieć jedną z następujących wartości:

- **Nie podjęto**: Aktualizacja nie została zainstalowana, ponieważ mała były dostępne okno obsługi zdefiniowanych w oparciu.
- **Pomyślnie**: Aktualizacja zakończyło się pomyślnie.
- **Nie powiodło się**: Aktualizacja nie powiodła się.

Aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie, wybierz pozycję **Wszystkie dzienniki**.

Aby wyświetlić strumień zadań elementu runbook, który zarządza wdrożenia aktualizacji na docelowej maszynie wirtualnej, wybierz fragment danych wyjściowych.

Aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem, wybierz pozycję **Błędy**.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat zarządzania aktualizacjami, w tym dzienniki, danych wyjściowych i błędów, zobacz [rozwiązania do zarządzania aktualizacji w usłudze Azure](../operations-management-suite/oms-solution-update-management.md).
