---
title: Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows na platformie Azure
description: Ten artykuł zawiera omówienie sposobu korzystania z rozwiązania Update Management w usłudze Azure Automation w celu zarządzania aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure z systemem Windows.
services: automation
author: zjalexander
ms.service: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 12/04/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: fbca620fca1aeb53acc9bd70561e783b49ff1a60
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61306368"
---
# <a name="manage-windows-updates-by-using-azure-automation"></a>Zarządzanie aktualizacjami systemu Windows przy użyciu usługi Azure Automation

Przy użyciu rozwiązania Update Management można zarządzać aktualizacjami i poprawkami dla maszyn wirtualnych. Z tym samouczku dowiesz się, jak szybko ocenić stan dostępnych aktualizacji, zaplanować instalację wymaganych aktualizacji, przejrzeć wyniki wdrożenia oraz utworzyć alert sprawdzający, czy aktualizacje zostały pomyślnie zastosowane.

Aby uzyskać informacje o cenach, zobacz [cennik usługi Automation dla rozwiązania Update Management](https://azure.microsoft.com/pricing/details/automation/).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dołączanie maszyny wirtualnej dla rozwiązania Update Management
> * Wyświetlanie oceny aktualizacji
> * Konfigurowanie alertów
> * Planowanie wdrożenia aktualizacji
> * Wyświetlanie wyników wdrożenia

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować miesięczne środki na korzystanie z platformy Azure dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Azure Automation](automation-offering-get-started.md) do przechowywania obserwatora i elementów runbook akcji oraz zadania obserwatora.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md) do dołączenia.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

Najpierw na maszynie wirtualnej używanej w tym samouczku włącz rozwiązanie Update Management:

1. W menu witryny Azure Portal po lewej stronie wybierz pozycję **Maszyny wirtualne**. Wybierz maszynę wirtualną z listy.
2. Na stronie maszyny wirtualnej w obszarze **OPERACJE** wybierz pozycję **Update Management**. Zostanie wyświetlone okienko **Włączanie rozwiązania Update Management**.

Jest przeprowadzana walidacja w celu ustalenia, czy rozwiązanie Update Management zostało włączone dla tej maszyny wirtualnej. Ta walidacja obejmuje kontrole obszaru roboczego usługi Azure Log Analytics i powiązanego konta usługi Automation i tego, czy rozwiązanie Update Management znajduje się w obszarze roboczym.

Obszar roboczy usługi [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) służy do zbierania danych generowanych przez funkcje i usługi, takie jak rozwiązanie Update Management. Obszar roboczy zawiera pojedynczą lokalizację do przeglądania i analizowania danych z wielu źródeł.

Proces walidacji sprawdza również, czy maszyna wirtualna jest aprowizowana za pomocą programu Microsoft Monitoring Agent (MMA) i hybrydowego procesu roboczego elementu Runbook usługi Automation. Ten agent jest używany do komunikacji z usługą Azure Automation i uzyskiwania informacji dotyczących stanu aktualizacji. Agent wymaga otwartego portu 443 w celu komunikowania się z usługą Azure Automation oraz pobierania aktualizacji.

Jeśli którekolwiek z następujących wymagań wstępnych nie będzie występować podczas dołączania, zostanie ono automatycznie dołączone:

* Obszar roboczy usługi [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Konto usługi Automation](./automation-offering-get-started.md)
* [Hybrydowy proces roboczy elementu Runbook](./automation-hybrid-runbook-worker.md) (włączony na maszynie wirtualnej)

W rozwiązaniu **Update Management** ustaw lokalizację, obszar roboczy usługi Log Analytics i konto usługi Automation do użycia. Następnie wybierz pozycję **Włącz**. Jeśli te opcje są niedostępne, oznacza to, że na maszynie wirtualnej włączono inne rozwiązanie do automatyzacji. W takim przypadku należy użyć tego samego obszaru roboczego i konta usługi Automation.

![Okno włączania rozwiązania Update Management](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

Włączanie rozwiązania może potrwać kilka minut. W tym czasie nie należy zamykać okna przeglądarki. Po włączeniu rozwiązania informacje dotyczące brakujących aktualizacji maszyny wirtualnej są przekazywane do dzienników usługi Azure Monitor. Udostępnienie danych do analizy może potrwać od 30 minut do 6 godzin.

## <a name="view-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu rozwiązania Update Management zostanie otwarte okienko **Update Management**. Jeśli brakuje jakichkolwiek aktualizacji, ich listę można znaleźć na karcie **Brakujące aktualizacje**.

W obszarze **LINK DO INFORMACJI** wybierz link do aktualizacji, aby otworzyć artykuł pomocy technicznej dotyczący aktualizacji w nowym oknie. W tym oknie możesz znaleźć ważne informacje dotyczące aktualizacji.

![Wyświetlanie stanu aktualizacji](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Kliknij w dowolnym innym miejscu aktualizacji, aby otworzyć okno **Przeszukiwanie dzienników** dla wybranej aktualizacji. Zapytanie dotyczące przeszukiwania dzienników jest wstępnie zdefiniowane dla tej określonej aktualizacji. Możesz zmodyfikować to zapytanie lub utworzyć własne zapytanie, aby wyświetlić szczegółowe informacje o aktualizacjach wdrożonych lub brakujących we własnym środowisku.

![Wyświetlanie stanu aktualizacji](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Konfigurowanie alertów

W tym kroku dowiesz się, jak skonfigurować alert powiadamiający o stanie wdrożenia aktualizacji.

### <a name="alert-conditions"></a>Warunki alertu

Na koncie usługi Automation w obszarze **Monitorowanie** przejdź do pozycji **Alerty**, a następnie kliknij przycisk **+ Nowa reguła alertu**.

Konto usługi Automation zostało już wybrane jako zasób. Jeśli chcesz je zmienić, możesz kliknąć pozycję **Wybierz**, a następnie na stronie **Wybierz zasób** wybrać pozycję **Konta usługi Automation** na liście rozwijanej **Filtruj według typu zasobu**. Wybierz swoje konto usługi Automation, a następnie wybierz pozycję **Gotowe**.

Kliknij pozycję **Dodaj warunek**, aby wybrać odpowiedni sygnał dla wdrożenia aktualizacji. W poniższej tabeli przedstawiono szczegółowe informacje o dwóch dostępnych sygnałach dla wdrożeń aktualizacji:

|Nazwa sygnału|Wymiary|Opis|
|---|---|---|
|**Łączna liczba przebiegów wdrażania aktualizacji**|— Nazwa wdrażania aktualizacji</br>— Stan|Sygnał ten jest używany, aby poinformować o ogólnym stanie wdrożenia aktualizacji.|
|**Łączna liczba przebiegów wdrażania aktualizacji maszyny**|— Nazwa wdrażania aktualizacji</br>— Stan</br>— Komputer docelowy</br>— Identyfikator przebiegu wdrożenia aktualizacji|Ten sygnał jest używany, aby poinformować o stanie wdrożenia aktualizacji dotyczącej konkretnych maszyn|

Ab uzyskać wartości wymiarów, wybierz prawidłową wartość na liście. Jeśli wartość, której szukasz, nie znajduje się na liście, kliknij znak **\+** obok wymiaru i wpisz nazwę niestandardową. Następnie możesz wybrać wartość, którą chcesz znaleźć. Jeśli chcesz wybrać wszystkie wartości w ramach wymiaru, kliknij przycisk **Wybierz\***. Jeśli nie wybierzesz wartości wymiaru, ten wymiar nie będzie brany pod uwagę podczas oceny.

![Konfigurowanie logiki sygnału](./media/automation-tutorial-update-management/signal-logic.png)

W obszarze **Logika alertu** w polu **Próg** wprowadź **1**. Po zakończeniu wybierz pozycję **Gotowe**.

### <a name="alert-details"></a>Szczegóły alertu

W obszarze **2. Zdefiniuj szczegóły alertu** wprowadź nazwę i opis alertu. Ustaw opcję **Ważność** na **Informacyjny (ważność 2)** w przypadku pomyślnego przebiegu lub **Informacyjny (ważność 1)** w przypadku nieudanego przebiegu.

![Konfigurowanie logiki sygnału](./media/automation-tutorial-update-management/define-alert-details.png)

W obszarze **Grupy akcji** wybierz pozycję **Utwórz nową**. Grupa akcji to grupa składająca się z akcji, których można używać w wielu alertach. Akcje mogą obejmować powiadomienia e-mail, elementy runbook i webhook oraz wiele innych. Aby dowiedzieć się więcej o grupach akcji, zobacz [Create and manage action groups (Tworzenie grup akcji i zarządzanie nimi)](../azure-monitor/platform/action-groups.md).

W polu **Nazwa grupy akcji** wprowadź nazwę alertu oraz krótką nazwę. Krótka nazwa jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.

W obszarze **Akcje** wprowadź nazwę akcji, taką jak **Powiadomienia e-mail**. W obszarze **TYP AKCJI** wybierz pozycję **E-mail/SMS/Push/Głos**. W obszarze **SZCZEGÓŁY** wybierz pozycję **Edytuj szczegóły**.

W okienku **E-mail/SMS/Push/Głos** wprowadź nazwę. Zaznacz pole wyboru **E-mail**, a następnie wprowadź prawidłowy adres e-mail.

![Konfigurowanie grupy akcji dla poczty e-mail](./media/automation-tutorial-update-management/configure-email-action-group.png)

W okienku **E-mail/SMS/Push/Głos** wybierz przycisk **OK**. W okienku **Dodawanie grupy akcji** wybierz przycisk **OK**.

Aby dostosować temat wiadomości e-mail alertu, w oknie **Tworzenie reguły** w obszarze **Dostosowywanie akcji** wybierz pozycję **Temat wiadomości e-mail**. Po zakończeniu wybierz pozycję **Utwórz regułę alertu**. Alert informuje użytkownika o pomyślnym wdrożeniu aktualizacji oraz o maszynach będących elementami danego uruchomienia wdrożenia aktualizacji.

## <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Następnie zaplanuj wdrożenie zgodnie z harmonogramem wydawania i oknem obsługi, aby zainstalować aktualizacje. Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

Aby zaplanować nowe wdrożenie aktualizacji dla maszyny wirtualnej, przejdź do rozwiązania **Update Management**, a następnie wybierz pozycję **Zaplanuj wdrażanie aktualizacji**.

W obszarze **Nowe wdrożenie aktualizacji** podaj następujące informacje:

* **Nazwa**: Wprowadź unikatową nazwę wdrożenia aktualizacji.

* **System operacyjny**: Wybierz docelowy system operacyjny do wdrażania aktualizacji.

* **Grupy do zaktualizowania (wersja zapoznawcza)**: Zdefiniuj zapytanie na podstawie kombinacji subskrypcji, grup zasobów, lokalizacji i tagów, aby utworzyć dynamiczną grupę maszyn wirtualnych platformy Azure, które chcesz uwzględnić w swoim wdrożeniu. Aby dowiedzieć się więcej, zobacz [Grupy dynamiczne](automation-update-management.md#using-dynamic-groups)

* **Maszyny do zaktualizowania**: Wybierz zapisane wyszukiwanie bądź zaimportowaną grupę lub wybierz maszynę z listy rozwijanej, a następnie wybierz poszczególne maszyny. Jeśli wybierzesz pozycję **Maszyny**, gotowość maszyny będzie wyświetlana w kolumnie **AKTUALIZUJ GOTOWOŚĆ AGENTA**. Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach usługi Azure Monitor, zobacz [Computer groups in Azure Monitor logs (Grupy komputerów w dziennikach usługi Azure Monitor)](../azure-monitor/platform/computer-groups.md)

* **Klasyfikacja aktualizacji**: Wybierz typy oprogramowania, które zostaną uwzględnione we wdrożeniu aktualizacji. W tym samouczku pozostaw wszystkie typy wybranymi.

  Dostępne są następujące typy klasyfikacji:

   |System operacyjny  |Type  |
   |---------|---------|
   |Windows     | Aktualizacje krytyczne</br>Aktualizacje zabezpieczeń</br>Pakiety zbiorcze aktualizacji</br>Pakiety funkcji</br>Dodatki Service Pack</br>Aktualizacje definicji</br>Narzędzia</br>Aktualizacje        |
   |Linux     | Aktualizacje krytyczne i zabezpieczeń</br>Inne aktualizacje       |

   Opis typów klasyfikacji znajduje się w [klasyfikacjach aktualizacji](automation-update-management.md#update-classifications).

* **Aktualizacje do uwzględnienia/wykluczenia** — spowoduje to otwarcie strony **Uwzględnij/Wyklucz**. Aktualizacje, które mają zostać uwzględnione lub wykluczone, znajdują się na osobnych kartach. Aby uzyskać więcej informacji na temat sposobu obsługi dołączania, zobacz [zachowanie dołączania](automation-update-management.md#inclusion-behavior)

* **Ustawienia harmonogramu**: Zostanie otwarte okienko **Ustawienia harmonogramu**. Domyślny czas rozpoczęcia to 30 minut po bieżącej godzinie. Czas rozpoczęcia można ustawić na dowolny czas od 10 minut w przyszłości.

   Możesz też określić, czy wdrożenie ma występować raz, czy zgodnie z ustawionym harmonogramem cyklicznym. W obszarze **Cykl** wybierz pozycję **Raz**. Pozostaw wartość domyślną jako 1 dzień, a następnie wybierz przycisk **OK**. Konfiguruje to harmonogram cykliczny.

* **Skrypty wstępne i końcowe**: Wybierz skrypty do uruchomienia przed i po wdrożeniu. Aby dowiedzieć się więcej, zobacz [Zarządzanie skryptami wstępnymi i końcowymi](pre-post-scripts.md).
* **Okno obsługi (minuty)**: Pozostaw wartość domyślną. Możesz ustawić przedział czasu, w którym ma zostać przeprowadzone wdrażanie aktualizacji. To ustawienie pozwala zagwarantować, że zmiany będą wprowadzane w ramach zdefiniowanych okien obsługi.

* **Opcje ponownego uruchomienia**: To ustawienie określa sposób obsługi ponownego uruchamiania. Dostępne opcje:
  * Ponowne uruchomienie, jeśli jest to wymagane (ustawienie domyślne)
  * Zawsze uruchamiaj ponownie
  * Nigdy nie uruchamiaj ponownie
  * Tylko ponowne uruchomienie — aktualizacje nie zostaną zainstalowane

Po zakończeniu konfigurowania harmonogramu wybierz pozycję **Utwórz**.

![Okienko ustawień harmonogramu aktualizacji](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Nastąpi powrót do pulpitu nawigacyjnego stanu. Wybierz pozycję **Wdrożenia zaplanowanych aktualizacji**, aby pokazać utworzony harmonogram wdrażania.

> [!NOTE]
> Rozwiązanie Update Management obsługuje wdrażanie aktualizacji tej samej firmy i wstępne pobieranie poprawek. Wymaga to zmian w systemach, w których są stosowane poprawki. Zobacz sekcję [First party and pre-download support (Obsługa poprawek tej samej firmy i pobierania wstępnego)](automation-update-management.md#firstparty-predownload), aby dowiedzieć się, jak skonfigurować te ustawienia w swoich systemach.

## <a name="view-results-of-an-update-deployment"></a>Wyświetlanie wyników wdrażania aktualizacji

Po rozpoczęciu zaplanowanego wdrażania stan tego wdrożenia można sprawdzić na karcie **Wdrożenia aktualizacji** w obszarze rozwiązania **Update Management**. Wyświetlany stan **W toku** oznacza, że wdrożenie jest aktualnie uruchomione. Jeśli wdrożenie zakończy się pomyślnie, jego stan zmieni się na **Powodzenie**. Gdy istnieją błędy w co najmniej jednej aktualizacji w ramach wdrożenia, jest wyświetlany stan **Częściowe niepowodzenie**.

Kliknij ukończone wdrożenie aktualizacji, aby wyświetlić pulpit nawigacyjny tego wdrożenia.

![Pulpit nawigacyjny stanu wdrożenia aktualizacji dla określonego wdrożenia](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Obszar **Wyniki aktualizacji** zawiera podsumowanie z łączną liczbą aktualizacji i wynikami wdrożenia na maszynie wirtualnej. Tabela po prawej stronie pokazuje szczegółowy podział każdej aktualizacji i wyniki instalacji.

Na poniższej liście przedstawiono dostępne wartości:

* **Nie podjęto próby**: Nie zainstalowano aktualizacji z powodu niewystarczającego czasu w zdefiniowanym oknie konserwacji.
* **Powodzenie**: Aktualizacja powiodła się.
* **Niepowodzenie**: Aktualizacja nie powiodła się.

Aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie, wybierz opcję **Wszystkie dzienniki**.

Wybierz kafelek **Dane wyjściowe**, aby wyświetlić strumień zadań elementu runbook odpowiedzialnego za zarządzanie wdrożeniem aktualizacji na docelowej maszynie wirtualnej.

Aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem, wybierz pozycję **Błędy**.

Po pomyślnym wdrożeniu aktualizacji zostanie wysłana wiadomość e-mail podobna do poniższego przykładu z informacją o powodzeniu tego wdrożenia:

![Konfigurowanie grupy akcji dla poczty e-mail](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dołączanie maszyny wirtualnej dla rozwiązania Update Management
> * Wyświetlanie oceny aktualizacji
> * Konfigurowanie alertów
> * Planowanie wdrożenia aktualizacji
> * Wyświetlanie wyników wdrożenia

Kontynuowanie omawiania rozwiązania Update Management.

> [!div class="nextstepaction"]
> [Update Management solution](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json) (Rozwiązanie Update Management)

