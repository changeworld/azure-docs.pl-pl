---
title: Monitorowanie odzyskiwania witryny platformy Azure | Dokumenty firmy Microsoft
description: Monitorowanie i rozwiązywanie problemów z replikacją usługi Azure Site Recovery przy użyciu portalu
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: aa9d776df50306ab1705426c923413b5a5d545a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68717346"
---
# <a name="monitor-site-recovery"></a>Monitorowanie usługi Site Recovery

W tym artykule dowiesz się, jak monitorować usługę Azure [Site Recovery](site-recovery-overview.md), korzystając z wbudowanego monitorowania usługi Site Recovery.  Możesz monitorować:

- Kondycja i stan maszyn replikowanych przez usługę Site Recovery
- Test stanu pracy awaryjnej maszyn.
- Problemy i błędy wpływające na konfigurację i replikację.
- Składniki infrastruktury, takie jak serwery lokalne.


## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem warto przejrzeć [typowe pytania dotyczące monitorowania.](monitoring-common-questions.md)

## <a name="monitor-in-the-dashboard"></a>Monitor w pulpicie nawigacyjnym

1. W przechowalni kliknij pozycję **Przegląd**. Pulpit nawigacyjny usług odzyskiwania konsoliduje wszystkie informacje o monitorowaniu przechowalni w jednej lokalizacji. Istnieją strony dla usługi Site Recovery i usługi Azure Backup i można przełączać się między nimi.

    ![Pulpit nawigacyjny odzyskiwania witryny](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. Na pulpicie nawigacyjnym przejdź do różnych obszarów. 

    ![Pulpit nawigacyjny odzyskiwania witryny](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. W **folderze Elementy replikowane**kliknij pozycję **Wyświetl wszystko,** aby wyświetlić wszystkie serwery w przechowalni.
4. Kliknij szczegóły stanu w każdej sekcji, aby przejść do szczegółów.
5. W **widoku Infrastruktura**sortuj informacje o monitorowaniu według typu replikowanej maszyny.

## <a name="monitor-replicated-items"></a>Monitorowanie elementów replikowanych

W **przypadku elementów replikowanych**należy monitorować kondycję wszystkich komputerów w przechowalni, na których włączona jest replikacja.

**Stan** | **Szczegóły**
--- | ---
W dobrej kondycji | Replikacja postępuje normalnie. Nie wykryto żadnych objawów błędu ani ostrzeżenia.
Ostrzeżenie | Wykryto co najmniej jeden objaw ostrzegawczy, który może mieć wpływ na replikację.
Krytyczny | Wykryto co najmniej jeden krytyczny błąd replikacji.<br/><br/> Te symptomy błędu są zazwyczaj wskaźniki, które replikacji zatrzymany lub nie postępuje tak szybko, jak szybkość zmiany danych.
Nie dotyczy | Serwery, które obecnie nie powinny być replikowane. Może to obejmować maszyny, które zostały przejęte awaryjnie.

## <a name="monitor-test-failovers"></a>Monitorowanie pracy awaryjnej testu

W **przypadku niepowodzenia testu trybu failover**należy monitorować stan trybu failover dla maszyn w magazynie.

- Zaleca się uruchomienie testowego trybu failover na zreplikowanych komputerach co najmniej raz na sześć miesięcy. Jest to sposób, aby sprawdzić, czy praca awaryjna działa zgodnie z oczekiwaniami, bez zakłócania środowiska produkcyjnego. 
- Test pracy awaryjnej jest uważany za pomyślny dopiero po pomyślnym zakończeniu oczyszczania po pracy awaryjnej i po zakończeniu pracy awaryjnej.

**Stan** | **Szczegóły**
--- | ---
Zalecany test | Maszyny, które nie przeszły testu w pracy awaryjnej, ponieważ ochrona została włączona.
Wykonano pomyślnie | Maszyny z lub bardziej udanych testów awaryjnych.
Nie dotyczy | Maszyny, które obecnie nie kwalifikują się do testu w pracy awaryjnej. Na przykład maszyny, które są w pracy awaryjnej, mają początkowej replikacji/testu pracy awaryjnej/pracy awaryjnej w toku.

## <a name="monitor-configuration-issues"></a>Monitorowanie problemów z konfiguracją

W **przypadku problemów z konfiguracją**należy monitorować wszelkie problemy, które mogą mieć wpływ na pomyślne przejęcie konta awaryjnego.

- Problemy z konfiguracją (z wyjątkiem dostępności aktualizacji oprogramowania) są wykrywane przez okresową operację walidatora, która jest domyślnie uruchamiana co 12 godzin. Operację walidatora można wymusić natychmiastowe uruchomienie, klikając ikonę odświeżania obok nagłówka sekcji **Problemy z konfiguracją.**
- Kliknij linki, aby uzyskać więcej informacji. W przypadku problemów dotyczących określonych maszyn **kliknięcie wymagają uwagi** w kolumnie **Konfiguracje docelowe.** Szczegóły obejmują zalecenia dotyczące korygowania.

**Stan** | **Szczegóły**
--- | ---
Brakujące konfiguracje | Brakuje niezbędnego ustawienia, takiego jak sieć odzyskiwania lub grupa zasobów.
Brakujące zasoby | Określonego zasobu nie można odnaleźć lub nie jest dostępny w ramach subskrypcji. Na przykład zasób został usunięty lub zmigrowany. Monitorowane zasoby obejmowały docelową grupę zasobów, docelową sieć wirtualną/podsieć, konto magazynu dziennika/docelowego, zestaw dostępności docelowej, docelowy adres IP.
Przydział subskrypcji |  Saldo przydziału dostępnego zasobu subskrypcji jest porównywane z saldem potrzebnym do awaryjnego nad wszystkimi komputerami w przechowalni.<br/><br/> Jeśli nie ma wystarczającej ilości zasobów, zgłaszane jest niewystarczające saldo przydziału.<br/><br/> Przydziały są monitorowanie liczby rdzeni maszyny Wirtualnej, liczba rdzeni rodziny maszyn wirtualnych, karty interfejsu sieciowego (NIC) liczba.
Aktualizacje oprogramowania | Dostępność nowych aktualizacji oprogramowania oraz informacje o wygasających wersjach oprogramowania.


## <a name="monitor-errors"></a>Monitorowanie błędów

W **podsumowaniu błędów**należy monitorować aktualnie aktywne objawy błędu, które mogą mieć wpływ na replikację serwerów w przechowalni, i monitorować liczbę komputerów, których dotyczy problem.

- Błędy wpływające na składniki infrastruktury lokalnej są wyświetlane na początku sekcji. Na przykład nieotrzymanie pulsu od dostawcy odzyskiwania witryny azure na lokalnym serwerze konfiguracji lub hosta funkcji Hyper-V.
- Następnie wyświetlane są symptomy błędu replikacji wpływające na replikowane serwery.
- Wpisy tabeli są sortowane według kolejności zmniejszania ważności błędu, a następnie przez zmniejszenie kolejności zliczania komputerów, których dotyczy problem.
- Liczba serwerów, których dotyczy problem, jest użytecznym sposobem zrozumienia, czy pojedynczy podstawowy problem może mieć wpływ na wiele komputerów. Na przykład usterka sieci może potencjalnie mieć wpływ na wszystkie maszyny, które replikują się na platformie Azure. 
- Na jednym serwerze może wystąpić wiele błędów replikacji. W takim przypadku każdy symptom błędu zlicza ten serwer na liście serwerów, których dotyczy problem. Po rozwiązaniu problemu parametry replikacji poprawiają się, a błąd jest usuwany z komputera.

## <a name="monitor-the-infrastructure"></a>Monitoruj infrastrukturę.

W **widoku infrastruktura**należy monitorować składniki infrastruktury zaangażowane w replikację i kondycję łączności między serwerami a usługami platformy Azure.

- Zielona linia wskazuje, że połączenie jest w dobrej kondycji.
- Czerwona linia z ikoną nałożonegospodarze wskazuje na istnienie jednego lub więcej symptomów błędu, które wpływają na łączność.
-  Umieść wskaźnik myszy na ikonie błędu, aby wyświetlić błąd i liczbę elementów, na które ma wpływ. Kliknij ikonę filtruj listę elementów, na które ma wpływ.

    ![Widok infrastruktury odzyskiwania lokacji (przechowalnia)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Wskazówki dotyczące monitorowania infrastruktury

- Upewnij się, że składniki infrastruktury lokalnej (serwer konfiguracji, serwery przetwarzania, serwery VMM, hosty funkcji Hyper-V, maszyny VMware) są uruchomione w najnowszych wersjach dostawcy odzyskiwania witryny i/lub agentów.
- Aby użyć wszystkich funkcji w widoku infrastruktury, należy uruchomić [update pakiet zbiorczy 22](https://support.microsoft.com/help/4072852) dla tych składników.
- Aby użyć widoku infrastruktury, wybierz odpowiedni scenariusz replikacji w swoim środowisku. Można przejść do szczegółów w widoku, aby uzyskać więcej informacji. W poniższej tabeli przedstawiono, które scenariusze są reprezentowane.

    **Scenariusz** | **Stan**  | **Widok dostępny?**
    --- |--- | ---
    **Replikacja między lokacjami lokalnymi** | Wszystkie stany | Nie 
    **Replikacja maszyny Wirtualnej platformy Azure między regionami platformy Azure**  | Replikacja włączona/replikacja początkowa w toku | Tak
    **Replikacja maszyny Wirtualnej platformy Azure między regionami platformy Azure** | Niepowodzenie po awarii/powrót po awarii | Nie   
    **Replikacja z programu VMware do platformy Azure** | Replikacja włączona/replikacja początkowa w toku | Tak     
    **Replikacja z programu VMware do platformy Azure** | Niepowodzenie/niepowodzenie z powrotem | Nie      
    **Replikacja funkcji Hyper-V do platformy Azure** | Niepowodzenie/niepowodzenie z powrotem | Nie

- Aby wyświetlić widok infrastruktury dla pojedynczego urządzenia replikującego, w menu przechowalni kliknij polecenie **Elementy replikowane**i wybierz serwer.  




## <a name="monitor-recovery-plans"></a>Monitorowanie planów odzyskiwania

W **planach odzyskiwania**monitoruj liczbę planów, twórz nowe plany i modyfikuj istniejące.  

## <a name="monitor-jobs"></a>Monitorowanie zadań

W **zadaniach**monitoruj stan operacji odzyskiwania lokacji.

- Większość operacji w usłudze Azure Site Recovery są wykonywane asynchronicznie, z zadania śledzenia tworzone i używane do śledzenia postępu operacji. 
- Obiekt zadania ma wszystkie informacje potrzebne do śledzenia stanu i postępu operacji. 

Monitoruj zadania w następujący sposób:

1. W sekcji pulpit nawigacyjny > **zadania** można wyświetlić podsumowanie zadań, które zostały ukończone, są w toku lub oczekują na dane wejściowe w ciągu ostatnich 24 godzin. Możesz kliknąć dowolny stan, aby uzyskać więcej informacji na temat odpowiednich zadań.
2. Kliknij **pozycję Wyświetl wszystkie,** aby wyświetlić wszystkie zadania w ciągu ostatnich 24 godzin.

    > [!NOTE]
    > Informacje o zadaniach można również uzyskać z menu przechowalni > **zadania odzyskiwania witryny**. 

2. Na **liście Zadania odzyskiwania lokacji** wyświetlana jest lista zadań. W górnym menu można uzyskać szczegółowe informacje o błędach dla określonych zadań, filtrować listę zadań na podstawie określonych kryteriów i eksportować wybrane szczegóły zadania do programu Excel.
3. Można przejść do szczegółów zadania, klikając je. 

## <a name="monitor-virtual-machines"></a>Monitorowanie maszyn wirtualnych

W **przypadku elementów replikowanych**pobierz listę zreplikowanych maszyn. 
    ![Widok listy elementów replikowanych w odzyskiwaniu witryny](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Można wyświetlać i filtrować informacje. W menu akcji u góry można wykonywać akcje dla określonego komputera, w tym uruchamianie pracy awaryjnej testu lub wyświetlanie określonych błędów.
3. Kliknij **kolumny,** aby wyświetlić dodatkowe kolumny, Na przykład, aby wyświetlić celowy, problemy z konfiguracją docelowych i błędy replikacji.
4. Kliknij **przycisk Filtr,** aby wyświetlić informacje na podstawie określonych parametrów, takich jak kondycja replikacji lub określone zasady replikacji.
5. Kliknij prawym przyciskiem myszy komputer, aby zainicjować operacje, takie jak test pracy awaryjnej dla niego lub wyświetlić określone szczegóły błędu skojarzone z nim.
6. Kliknij maszynę, aby przejść do szczegółów. Szczegóły obejmują:
   - **Informacje o replikacji**: Bieżący stan i kondycja urządzenia.
   - **Cel punktu** odzyskiwania (punkt odzyskiwania): bieżący cel punktu odzyskiwania dla maszyny wirtualnej i czas, w którym cel punktu odzyskiwania został ostatnio obliczony.
   - **Punkty odzyskiwania:** Najnowsze dostępne punkty odzyskiwania dla maszyny.
   - **Gotowość do pracy awaryjnej:** Wskazuje, czy test pracy awaryjnej został uruchomiony dla komputera, wersja agenta uruchomiona na komputerze (dla maszyn z uruchomione usługi mobilności) i wszelkie problemy z konfiguracją.
   - **Błędy**: Lista symptomów błędu replikacji aktualnie obserwowanych na komputerze oraz możliwe przyczyny/działania.
   - **Zdarzenia**: Chronologiczna lista ostatnich zdarzeń wpływających na maszynę. Szczegóły błędu pokazuje aktualnie obserwowalne objawy błędu, podczas gdy zdarzenia są historycznym zapisem problemów, które miały wpływ na maszynę.
   - **Widok infrastruktury:** pokazuje stan infrastruktury dla scenariusza, gdy maszyny są replikowane na platformę Azure.

     ![Szczegóły/omówienie elementu replikowanego w odzyskiwaniu witryny](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Subskrybowanie powiadomień e-mail

Możesz subskrybować otrzymywanie powiadomień e-mail o tych krytycznych zdarzeniach:
 
- Stan krytyczny dla maszyny replikowanej.
- Brak łączności między składnikami infrastruktury lokalnej a usługą Site Recovery. Łączność między funkcją Odzysk lokacji a serwerami lokalnymi zarejestrowanymi w przechowalni jest wykrywana przy użyciu mechanizmu pulsu.
- Awarie trybu failover.

Zapisz się w następujący sposób:

W sekcji **Monitorowanie** > przechowalni kliknij pozycję **Zdarzenia odzyskiwania witryny**.
1. Kliknij pozycję **Powiadomienia e-mail**.
1. W **obszarze Powiadomienie e-mail**włącz powiadomienia i określ, do kogo chcesz wysłać. Możesz wysyłać do wszystkich administratorów subskrypcji powiadomienia i opcjonalnie określone adresy e-mail.

    ![Powiadomienia e-mail](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o](monitor-log-analytics.md) monitorowaniu odzyskiwania witryny za pomocą usługi Azure Monitor.
