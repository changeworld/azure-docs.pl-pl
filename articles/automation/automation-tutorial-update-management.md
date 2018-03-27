---
title: Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows na platformie Azure
description: Ten artykuł zawiera omówienie sposobu korzystania z usługi Azure Automation — Update Management w celu zarządzania aktualizacjami i poprawkami dla własnych maszyn wirtualnych systemu Windows platformy Azure.
services: automation
author: zjalexander
ms.service: automation
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: bded1621dc56a6e621408e567ce39a3107bec7c9
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
---
# <a name="manage-windows-updates-with-azure-automation"></a>Zarządzanie aktualizacjami systemu Windows za pomocą usługi Azure Automation

Rozwiązanie Update Management pozwala zarządzać aktualizacjami i poprawkami dla maszyn wirtualnych.
W tym samouczku dowiesz się, jak szybko ocenić stan dostępnych aktualizacji, zaplanować instalację wymaganych aktualizacji i przejrzeć wyniki wdrażania, aby sprawdzić, czy aktualizacje zostały zastosowane pomyślnie.

Aby uzyskać informacje o cenach, zobacz [cennik usługi Automation dla rozwiązania Update Management](https://azure.microsoft.com/pricing/details/automation/)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dołączanie maszyny wirtualnej dla zarządzania aktualizacjami
> * Wyświetlanie oceny aktualizacji
> * Planowanie wdrożenia aktualizacji
> * Wyświetlanie wyników wdrożenia

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto automatyzacji](automation-offering-get-started.md) do przechowywania obserwatora i elementów Runbook akcji oraz zadania obserwatora.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md) do dołączenia.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal na stronie http://portal.azure.com.

## <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

Najpierw należy włączyć rozwiązanie Update Management dla maszyny wirtualnej w tym samouczku. Jeśli inne rozwiązanie automatyzacji zostało wcześniej włączone dla maszyny wirtualnej, ten krok nie jest konieczny.

1. W menu po lewej stronie wybierz pozycję **Maszyny wirtualne** i wybierz z listy maszynę wirtualną
2. W menu po lewej stronie w obszarze **Operacje** kliknij pozycję **Update Management**. Zostanie wyświetlona strona **Włączanie rozwiązania Update Management**.

Jest przeprowadzana walidacja w celu ustalenia, czy rozwiązanie Update Management jest włączone dla tej maszyny wirtualnej.
Walidacja obejmuje kontrole obszaru roboczego usługi Log Analytics i powiązanego konta usługi Automation i tego, czy rozwiązanie znajduje się w obszarze roboczym.

Obszar roboczy usługi [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) służy do zbierania danych generowanych przez funkcje i usługi, takie jak rozwiązanie Update Management.
Obszar roboczy zawiera pojedynczą lokalizację do przeglądania i analizowania danych z wielu źródeł.
Aby wykonać dodatkowe akcje na maszynach wirtualnych, które wymagają aktualizacji, usługa Azure Automation pozwala na uruchamianie elementów Runbook dla maszyn wirtualnych, takich jak pobieranie i stosowanie aktualizacji.

Proces walidacji sprawdza również, czy maszyna wirtualna jest aprowizowana za pomocą programu Microsoft Monitoring Agent (MMA) i hybrydowego procesu roboczego elementu Runbook usługi Automation.
Ten agent jest używany do komunikacji z maszyną wirtualną i uzyskiwania informacji dotyczących stanu aktualizacji.

Wybierz obszar roboczy usługi Log Analytics i konto usługi Automation, a następnie kliknij przycisk **Włącz**, aby włączyć to rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

Jeśli którekolwiek z następujących wymagań wstępnych nie będzie występować podczas dołączania, zostanie ono automatycznie dołączone:

* Obszar roboczy usługi [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Automatyzacja](./automation-offering-get-started.md)
* [Hybrydowy proces roboczy elementu Runbook](./automation-hybrid-runbook-worker.md) jest włączony na maszynie wirtualnej

Zostanie otwarty ekran **Rozwiązanie Update Management**. Skonfiguruj lokalizację, obszar roboczy usługi Log Analytics i konto usługi Automation, a następnie kliknij pozycję **Włącz**. Jeśli pola są wygaszone, oznacza to, że inne rozwiązanie automatyzacji jest włączone dla maszyny wirtualnej, a tym samym należy użyć tego samego obszaru roboczego i konta automatyzacji.

![Okno włączania rozwiązania Update Management](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

Włączanie rozwiązania może potrwać do 15 minut. W tym czasie nie należy zamykać okna przeglądarki.
Po włączeniu rozwiązania informacje dotyczące brakujących aktualizacji maszyn wirtualnych są przekazywane do usługi Log Analytics.
Udostępnienie danych do analizy może potrwać od 30 minut do 6 godzin.

## <a name="view-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu rozwiązania **Update Management** zostanie wyświetlony ekran **Update Management**.
Jeśli brakuje jakichkolwiek aktualizacji możesz zobaczyć listę brakujących aktualizacji na karcie **Brakujące aktualizacje**.

Wybierz pozycję **LINK INFORMACJI** w aktualizacji, aby otworzyć artykuł pomocy technicznej w celu aktualizacji w nowym oknie. Tutaj możesz poznać ważne informacje dotyczące aktualizacji.

![Wyświetlanie stanu aktualizacji](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Kliknięcie w dowolnym innym miejscu aktualizacji spowoduje otwarcie okna **Przeszukiwanie dzienników** dla wybranej aktualizacji. Zapytanie dotyczące przeszukiwania dzienników jest wstępnie zdefiniowane dla tej konkretnej aktualizacji. Możesz zmodyfikować to zapytanie lub utworzyć własne zapytanie, aby wyświetlić szczegółowe informacje o wdrożonych aktualizacjach lub ich braku we własnym środowisku.

![Wyświetlanie stanu aktualizacji](./media/automation-tutorial-update-management/logsearch.png)

## <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Teraz wiesz, że brakuje aktualizacji maszyny wirtualnej. Aby zainstalować aktualizacje, zaplanuj wdrożenie zgodnie z harmonogramem wydawania i oknem obsługi.
Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu.
Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

> [!WARNING]
> Gdy aktualizacje wymagają ponownego uruchomienia systemu, maszyna wirtualna zostanie automatycznie ponownie uruchomiona.

Zaplanuj nowe wdrożenie aktualizacji dla maszyny wirtualnej, przechodząc z powrotem do pozycji **Rozwiązanie Update Management** i wybierając pozycję **Zaplanuj wdrożenie aktualizacji** w górnej części ekranu.

Na ekranie **Nowe wdrożenie aktualizacji** podaj następujące informacje:

* **Nazwa** — wprowadź unikatową nazwę wdrożenia aktualizacji.
* **Klasyfikacja aktualizacji** — wybierz typy oprogramowania, które zostaną uwzględnione we wdrożeniu aktualizacji. W tym samouczku pozostaw wszystkie typy wybranymi.

  Dostępne są następujące typy klasyfikacji:

  * Aktualizacje krytyczne
  * Aktualizacje zabezpieczeń
  * Pakiety zbiorcze aktualizacji
  * Pakiety funkcji
  * Dodatki Service Pack
  * Aktualizacje definicji
  * Narzędzia
  * Aktualizacje

* **Ustawienia harmonogramu** — ustaw czas na 5 minut w przyszłości. Możesz także zaakceptować wartość domyślną, która jest równa 30 minut od bieżącego czasu.
Możesz też określić, czy wdrożenie ma występować raz, czy zgodnie z ustawionym harmonogramem cyklicznym.
Wybierz pozycję **Cyklicznie** w obszarze **Cykl**. Pozostaw wartość domyślną równą 1 dzień, a następnie kliknij przycisk **OK**. Konfiguruje to harmonogram cykliczny.

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

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dołączanie maszyny wirtualnej dla zarządzania aktualizacjami
> * Wyświetlanie oceny aktualizacji
> * Planowanie wdrożenia aktualizacji
> * Wyświetlanie wyników wdrożenia

Kontynuowanie omawiania rozwiązania Update Management.

> [!div class="nextstepaction"]
> [Update Management solution](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json) (Rozwiązanie Update Management)
