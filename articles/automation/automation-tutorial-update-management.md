---
title: Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows na platformie Azure
description: Ten artykuł zawiera omówienie sposobu korzystania z usługi Azure Automation — Update Management w celu zarządzania aktualizacjami i poprawkami dla własnych maszyn wirtualnych systemu Windows platformy Azure.
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 84ec2a5852e6aaeb4b9fe6ef11924209d03fb54b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="manage-windows-updates-with-azure-automation"></a>Zarządzanie aktualizacjami systemu Windows za pomocą usługi Azure Automation

Rozwiązanie Update Management pozwala zarządzać aktualizacjami i poprawkami dla maszyn wirtualnych.
W tym samouczku dowiesz się, jak szybko ocenić stan dostępnych aktualizacji, zaplanować instalację wymaganych aktualizacji, przejrzeć wyniki wdrożenia oraz utworzyć alert sprawdzający, czy aktualizacje zostały pomyślnie zastosowane.

Aby uzyskać informacje o cenach, zobacz [cennik usługi Automation dla rozwiązania Update Management](https://azure.microsoft.com/pricing/details/automation/)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dołączanie maszyny wirtualnej dla zarządzania aktualizacjami
> * Wyświetlanie oceny aktualizacji
> * Konfigurowanie alertów
> * Planowanie wdrożenia aktualizacji
> * Wyświetlanie wyników wdrożenia

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować miesięczne środki na korzystanie z systemu Azure dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto automatyzacji](automation-offering-get-started.md) do przechowywania obserwatora i elementów Runbook akcji oraz zadania obserwatora.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md) do dołączenia.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

Najpierw na maszynie wirtualnej używanej w tym samouczku należy włączyć opcję Zarządzanie aktualizacjami.

1. W witrynie Azure Portal z menu po lewej stronie wybierz pozycję **Maszyny wirtualne** i wybierz z listy maszynę wirtualną
2. Na stronie maszyny wirtualnej w obszarze **Operacje** kliknij pozycję **Zarządzanie aktualizacjami**. Zostanie wyświetlona strona **Włączanie zarządzania aktualizacjami**.

Jest przeprowadzana walidacja w celu ustalenia, czy Zarządzanie aktualizacjami jest włączone dla tej maszyny wirtualnej. Ta walidacja obejmuje kontrole obszaru roboczego usługi Log Analytics i powiązanego konta usługi Automation i tego, czy Zarządzanie aktualizacjami znajduje się w obszarze roboczym.

Obszar roboczy usługi [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) służy do zbierania danych generowanych przez funkcje i usługi, takie jak Zarządzanie aktualizacjami. Obszar roboczy zawiera pojedynczą lokalizację do przeglądania i analizowania danych z wielu źródeł.

Proces walidacji sprawdza również, czy maszyna wirtualna jest aprowizowana za pomocą programu Microsoft Monitoring Agent (MMA) i hybrydowego procesu roboczego elementu Runbook usługi Automation.
Ten agent jest używany do komunikacji z usługą Azure Automation i uzyskiwania informacji dotyczących stanu aktualizacji. Agent wymaga otwartego portu 443 w celu komunikowania się z usługą Azure Automation oraz pobierania aktualizacji.

Jeśli którekolwiek z następujących wymagań wstępnych nie będzie występować podczas dołączania, zostanie ono automatycznie dołączone:

* Obszar roboczy usługi [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Konto usługi Automation](./automation-offering-get-started.md)
* [Hybrydowy proces roboczy elementu Runbook](./automation-hybrid-runbook-worker.md) jest włączony na maszynie wirtualnej

Zostanie otwarty ekran **Rozwiązanie Update Management**. Skonfiguruj lokalizację, obszar roboczy usługi Log Analytics i konto usługi Automation, a następnie kliknij pozycję **Włącz**. Jeśli pola są wygaszone, oznacza to, że inne rozwiązanie automatyzacji jest włączone dla maszyny wirtualnej, a tym samym należy użyć tego samego obszaru roboczego i konta automatyzacji.

![Okno włączania rozwiązania Update Management](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

Włączanie rozwiązania może potrwać kilka minut. W tym czasie nie należy zamykać okna przeglądarki.
Po włączeniu rozwiązania informacje dotyczące brakujących aktualizacji maszyn wirtualnych są przekazywane do usługi Log Analytics.
Udostępnienie danych do analizy może potrwać od 30 minut do 6 godzin.

## <a name="view-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu rozwiązania **Update Management** zostanie wyświetlony ekran **Update Management**.
Jeśli brakuje jakichkolwiek aktualizacji możesz zobaczyć listę brakujących aktualizacji na karcie **Brakujące aktualizacje**.

Wybierz pozycję **LINK INFORMACJI** w aktualizacji, aby otworzyć artykuł pomocy technicznej w celu aktualizacji w nowym oknie. Tutaj możesz poznać ważne informacje dotyczące aktualizacji.

![Wyświetlanie stanu aktualizacji](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Kliknięcie w dowolnym innym miejscu aktualizacji spowoduje otwarcie okna **Przeszukiwanie dzienników** dla wybranej aktualizacji. Zapytanie dotyczące przeszukiwania dzienników jest wstępnie zdefiniowane dla tej konkretnej aktualizacji. Możesz zmodyfikować to zapytanie lub utworzyć własne zapytanie, aby wyświetlić szczegółowe informacje o wdrożonych aktualizacjach lub ich braku we własnym środowisku.

![Wyświetlanie stanu aktualizacji](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerting"></a>Konfigurowanie alertów

W tym kroku skonfigurujesz alert z powiadomieniem o pomyślnym wdrożeniu aktualizacji. Działanie tworzonego alertu polega na zapytaniach kierowanych do usługi Log Analytics. Można napisać dowolne zapytanie niestandardowe dotyczące dodatkowych alertów, odpowiadające różnym scenariuszom. W witrynie Azure Portal przejdź do obszaru **Monitorowanie** i kliknij przycisk **Utwórz alert**. Spowoduje to otwarcie strony **Tworzenie reguły**.

W obszarze **1. Zdefiniuj warunek alertu**, kliknij przycisk **+ Wybierz docelowy**. W obszarze **Filtruj według typu zasobu** wybierz pozycję **Log Analytics**. Wybierz swój obszar roboczy Log Analytics, a następnie kliknij przycisk **Gotowe**.

![utwórz alert](./media/automation-tutorial-update-management/create-alert.png)

Kliknij przycisk **+ Dodaj kryteria**, aby otworzyć stronę **Konfigurowanie logiki sygnału**. W tabeli wybierz pozycję **Niestandardowe wyszukiwanie w dziennikach**. W polu tekstowym **Zapytanie wyszukiwania** wpisz poniższe zapytanie. To zapytanie zwraca nazwę komputerów i przebiegu aktualizacyjnego, który został ukończony podczas określonej aktualizacji.

```loganalytics
UpdateRunProgress
| where InstallationStatus == 'Succeeded'
| where TimeGenerated > now(-10m)
| summarize by UpdateRunName, Computer
```

Dla logiki alertu wprowadź **1** jako wartość **Próg**. Na koniec kliknij przycisk **Gotowe**.

![Konfigurowanie logiki sygnału](./media/automation-tutorial-update-management/signal-logic.png)

W obszarze **2. Zdefiniuj szczegóły alertu**, nadaj mu przyjazną nazwę i podaj opis. W polu **Ważność** wybierz **Informacyjny (ważność 2)**, ponieważ alert dotyczy pomyślnego uruchomienia.

![Konfigurowanie logiki sygnału](./media/automation-tutorial-update-management/define-alert-details.png)

W obszarze **3. Zdefiniuj grupę akcji** kliknij przycisk **+ Nowa grupa akcji**. Grupa akcji to grupa składająca się z akcji, których można używać w wielu alertach. Mogą one obejmować powiadomienia e-mail, elementy runbook i webhook oraz wiele innych. Aby dowiedzieć się więcej o grupach akcji, zobacz [Tworzenie i zarządzanie grupami akcji](../monitoring-and-diagnostics/monitoring-action-groups.md)

W polu **Nazwa grupy akcji** wpisz przyjazną nazwę i nazwę krótką. Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.

W obszarze **Akcje** dla przyjaznej nazwy akcji, takiej jak **Powiadomienia e-mail** w obszarze **TYP AKCJI** wybierz **E-mail/SMS/Push/Głos**. W obszarze **SZCZEGÓŁY** wybierz pozycję **Edytuj szczegóły**.

Na stronie **E-mail/SMS/Push/Głos** nadaj nazwę. Zaznacz pole wyboru **E-mail** i wpisz prawidłowy adres e-mail, który ma być używany.

![Skonfiguruj grupę akcji poczty e-mail](./media/automation-tutorial-update-management/configure-email-action-group.png)

Kliknij przycisk **OK** na stronie **E-mail/SMS/Push/Głos**, aby ją zamknąć, a następnie kliknij przycisk **OK**, aby zamknąć stronę **Dodawanie grupy akcji**.

Możesz dostosować temat wysyłanej wiadomości e-mail, klikając przycisk **Temat wiadomości e-mail** w obszarze **Dostosuj akcje** na stronie **Tworzenie reguły**. Na koniec kliknij pozycję **Utwórz regułę alertu**. Spowoduje to utworzenie reguły, która informuje użytkownika o pomyślnym wdrożeniu aktualizacji oraz o komputerach będących elementami danego uruchomienia wdrożenia aktualizacji.

## <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Po skonfigurowaniu tego alertu zaplanuj wdrożenie zgodnie z harmonogramem i oknem obsługi, aby zainstalować aktualizacje.
Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu.
Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

> [!WARNING]
> Gdy aktualizacje wymagają ponownego uruchomienia systemu, maszyna wirtualna zostanie automatycznie ponownie uruchomiona.

Zaplanuj nowe wdrożenie aktualizacji dla maszyny wirtualnej, przechodząc z powrotem do pozycji **Rozwiązanie Update Management** i wybierając pozycję **Zaplanuj wdrożenie aktualizacji** w górnej części ekranu.

Na ekranie **Nowe wdrożenie aktualizacji** podaj następujące informacje:

* **Nazwa** — wprowadź unikatową nazwę wdrożenia aktualizacji.

* **System operacyjny** — wybierz docelowy system operacyjny do wdrażania aktualizacji.

* **Klasyfikacja aktualizacji** — wybierz typy oprogramowania, które zostaną uwzględnione we wdrożeniu aktualizacji. W tym samouczku pozostaw wszystkie typy wybranymi.

  Dostępne są następujące typy klasyfikacji:

   |System operacyjny  |Typ  |
   |---------|---------|
   |Windows     | Aktualizacje krytyczne</br>Aktualizacje zabezpieczeń</br>Pakiety zbiorcze aktualizacji</br>Pakiety funkcji</br>Dodatki Service Pack</br>Aktualizacje definicji</br>Narzędzia</br>Aktualizacje        |
   |Linux     | Aktualizacje krytyczne i zabezpieczeń</br>Inne aktualizacje       |

   Opis typów klasyfikacji znajduje się w [klasyfikacjach aktualizacji](automation-update-management.md#update-classifications).

* **Ustawienia harmonogramu** — spowoduje to otwarcie strony Ustawienia harmonogramu. Domyślny czas rozpoczęcia to 30 minut po bieżącej godzinie. Tę wartość można ustawić dowolnie, zaczynając od 10 minut po bieżącej godzinie.

   Możesz też określić, czy wdrożenie ma występować raz, czy zgodnie z ustawionym harmonogramem cyklicznym.
   Wybierz **Raz** w obszarze **Cykl**. Pozostaw wartość domyślną równą 1 dzień, a następnie kliknij przycisk **OK**. Konfiguruje to harmonogram cykliczny.

* **Okno konserwacji (w minutach)** — pozostaw tę wartość równą wartości domyślnej. Możesz podać okres, w którym ma zostać przeprowadzone wdrażanie aktualizacji. To ustawienie pozwala zagwarantować, że zmiany będą wprowadzane w ramach zdefiniowanych okien obsługi.

![Ekran ustawień harmonogramu aktualizacji](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Po zakończeniu konfigurowania harmonogramu, kliknij przycisk **Utwórz**. Nastąpi powrót do pulpitu nawigacyjnego stanu. Wybierz pozycję **Wdrożenia zaplanowanych aktualizacji**, aby pokazać utworzony harmonogram wdrażania.

## <a name="view-results-of-an-update-deployment"></a>Wyświetlanie wyników wdrażania aktualizacji

Po rozpoczęciu zaplanowanego wdrażania stan tego wdrożenia można sprawdzić na karcie **Wdrożenia aktualizacji** na ekranie rozwiązania **Update Management**.
Jeśli wdrażanie trwa, wdrożenie ma stan **W toku**.
Po pomyślnym ukończeniu wdrażania stan zmienia się na **Powodzenie**.
Gdy istnieją błędy w co najmniej jednej aktualizacji w ramach wdrożenia, jest wyświetlany stan **Częściowe niepowodzenie**.
Kliknij ukończone wdrożenie aktualizacji, aby wyświetlić pulpit nawigacyjny tego wdrożenia.

![Pulpit nawigacyjny stanu wdrożenia aktualizacji dla określonego wdrożenia](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Kafelek **Wyniki aktualizacji** zawiera podsumowanie z łączną liczbą aktualizacji i wynikami wdrożenia na maszynie wirtualnej.
Tabela po prawej stronie pokazuje szczegółowy podział każdej aktualizacji i wyniki instalacji.
Na poniższej liście przedstawiono dostępne wartości:

* **Nie podjęto próby** — nie zainstalowano aktualizacji z powodu niewystarczającego czasu w zdefiniowanym oknie konserwacji.
* **Powodzenie** — aktualizacja powiodła się.
* **Niepowodzenie** — aktualizacja nie powiodła się.

Kliknij pozycję **Wszystkie dzienniki**, aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie.

Kliknij kafelek **Dane wyjściowe**, aby wyświetlić strumień zadań elementu runbook odpowiedzialnego za zarządzanie wdrożeniem aktualizacji na docelowej maszynie wirtualnej.

Kliknij pozycję **Błędy**, aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem.

Po pomyślnym wdrożeniu aktualizacji zostanie wysłane odpowiednie powiadomienie e-mail, podobne do poniższego.

![Konfigurowanie grupy akcji dla poczty e-mail](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dołączanie maszyny wirtualnej dla zarządzania aktualizacjami
> * Wyświetlanie oceny aktualizacji
> * Konfigurowanie alertów
> * Planowanie wdrożenia aktualizacji
> * Wyświetlanie wyników wdrożenia

Kontynuowanie omawiania rozwiązania Update Management.

> [!div class="nextstepaction"]
> [Update Management solution](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json) (Rozwiązanie Update Management)
