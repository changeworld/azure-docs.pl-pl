---
title: Odpowiadanie na zdarzenia przy użyciu alertów usługi Azure Log Analytics | Microsoft Docs
description: Ten samouczek pomaga w zrozumieniu alertów w usłudze Log Analytics, które pomagają identyfikować ważne informacje w obszarze roboczym oraz aktywnie powiadamiają użytkownika o problemach lub wywołują akcje w celu ich skorygowania.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/30/2018
ms.author: magoedte
ms.custom: mvc
ms.component: na
ms.openlocfilehash: c6c7b3f897e38fbd67098c9f881380bc073f13da
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432654"
---
# <a name="respond-to-events-with-azure-monitor-alerts"></a>Odpowiadanie na zdarzenia przy użyciu alertów platformy Azure Monitor
Reguły przechowywania dzienników są tworzone przez usługę Azure Alerts w celu automatycznego wykonywania określonych zapytań dotyczących dzienników w regularnych odstępach czasu.  Jeśli wyniki zapytania pasują do określonych kryteriów, jest tworzony rekord alertu. Reguła może wtedy automatycznie uruchomić jedną lub więcej akcji przy użyciu [grup akcji](../monitoring-and-diagnostics/monitoring-action-groups.md).  Ten samouczek jest kontynuacją samouczka [Tworzenie i udostępnianie pulpitów nawigacyjnych z danymi usługi Log Analytics](log-analytics-tutorial-dashboards.md).   

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie reguły alertu
> * Konfigurowanie grupy akcji w celu wysyłania powiadomień e-mail

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna [połączona z obszarem roboczym usługi Log Analytics](log-analytics-quick-collect-azurevm.md).

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com).

## <a name="create-alerts"></a>Tworzenie alertów
Alerty są tworzone na podstawie reguł alertów na platformie Azure Monitor i mogą automatycznie uruchamiać zapisane zapytania lub niestandardowe wyszukiwania dziennika w regularnych odstępach czasu.  Możesz utworzyć alerty w oparciu o konkretne metryki wydajności lub w momencie, w którym zostają utworzone określone zdarzenia, w przypadku braku zdarzenia, a także w sytuacji, w której wiele zdarzeń zostaje utworzonych w danym przedziale czasu.  Na przykład alertów można używać do powiadamiania użytkownika, gdy średnie użycie procesora CPU przekroczy określony próg, gdy zostanie wykryty brak aktualizacji lub gdy zostanie wygenerowane zdarzenie po wykryciu, że określona usługa systemu Windows lub demon systemu Linux nie są uruchomione.  Jeśli wyniki przeszukiwania dzienników pasują do określonych kryteriów, zostaje utworzony alert. Następnie reguła może automatycznie uruchomić jedną lub kilka akcji, np. powiadomić użytkownika o alercie lub wywołać inny proces.

W poniższym przykładzie tworzona jest reguła alertu pomiaru metryki na podstawie zapytania *Maszyny wirtualne platformy Azure — użycie procesora* znajdującego się w [samouczku Wizualizacja danych](log-analytics-tutorial-dashboards.md). Alert jest tworzony dla każdej maszyny wirtualnej, która przekracza próg 90%.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Monitor**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Monitor**.
1. W okienku po lewej stronie wybierz pozycję **Alerty**, a następnie kliknij przycisk **Nowa reguła alertu** w górnej części strony, aby utworzyć nowy alert.

    ![Tworzenie nowej reguły alertu](./media/log-analytics-tutorial-response/alert-rule-02.png)

1. W pierwszym kroku w sekcji **Utwórz alert** wybierzesz jako zasób obszar roboczy usługi Log Analytics, ponieważ jest to sygnał alertu oparty na dzienniku.  Filtruj wyniki: jeśli masz więcej subskrypcji, wybierz z listy rozwijanej **Subskrypcję** zawierającą maszynę wirtualną oraz obszar roboczy usługi Log Analytics, które zostały utworzone wcześniej.  Filtruj **Typ zasobu**, wybierając z listy rozwijanej pozycję **Log Analytics**.  Na końcu wybierz kolejno opcje **Zasób** **DefaultLAWorkspace**, a następnie kliknij przycisk **Gotowe**.

    ![Zadanie tworzenia alertu — krok 1](./media/log-analytics-tutorial-response/alert-rule-03.png)

1. W sekcji **Kryteria alertu** kliknij przycisk **Dodaj kryteria**, aby zdefiniować zapytanie, a następnie określ logikę, zgodnie z którą działa reguła alertu. W okienku **Konfigurowanie logiki sygnału** wybierz pozycję **Przeszukiwanie dzienników niestandardowych** jako nazwę sygnału i wprowadź zapytanie w obszarze **Zapytanie wyszukiwania**.

    Na przykład:
    ```
    Perf
    | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total"
    | summarize AggregatedValue=avg(CounterValue) by bin(TimeGenerated, 1m)
    ```

    Okienko jest aktualizowane i zawiera ustawienia konfiguracji dla alertu.  W górnej części znajdują się wyniki z ostatnich 30 minut dla wybranego sygnału.

1. Skonfiguruj alert przy użyciu następujących informacji:  
   a. Z listy rozwijanej **Na podstawie* wybierz pozycję **Pomiar metryki**.  Pomiar metryki utworzy alert dla każdego obiektu w zapytaniu z wartością, która przekracza określony próg.  
   b. W polu **Warunek** wybierz wartość **Większe niż**, a następnie wprowadź **90** w polu **Próg**.  
   d. W sekcji Wyzwalaj alert w oparciu o wybierz opcję **Kolejne naruszenia**, z listy rozwijanej wybierz opcję **Większe niż** i wprowadź wartość 3.  
   d. W sekcji Ocena w oparciu o, zaakceptuj ustawienia domyślne. Reguła będzie uruchamiana co pięć minut i będzie zwracać rekordy utworzone w ciągu ostatnich 5 minut.  
1. Kliknij przycisk **Gotowe**, aby zakończyć tworzenie reguły alertu.

    ![Konfigurowanie sygnału alertu](./media/log-analytics-tutorial-response/alert-signal-logic-02.png)

1. Teraz przejdź do drugiego kroku: podaj nazwę alertu w polu **Nazwa reguły alertu**, np. **Procent procesora przekracza 90 procent**.  Wpisz **Opis**, podając szczegóły alertu, a następnie wybierz z dostępnych opcji pozycję **Critical(Sev 0)** (Krytyczny (ważność 0)) jako wartość pola **Ważność**.

    ![Konfigurowanie szczegółów alertu](./media/log-analytics-tutorial-response/alert-signal-logic-04.png)

1. Aby aktywować regułę alertu od razu po utworzeniu, zaakceptuj wartość domyślną w polu **Włącz regułę po utworzeniu**.  
1. W trzecim i ostatnim kroku określ **Grupę akcji**, która gwarantuje, że za każdym razem, gdy zostaje wywołany alert, wykonywane są te same akcje, i która może być używana dla każdej zdefiniowanej reguły.  Skonfiguruj nową grupę akcji, wprowadzając następujące informacje:  
   a. Wybierz pozycję **Nowa grupa akcji**, zostanie wyświetlone okienko **Dodaj grupę akcji**.  
   b. W polu **Nazwa grupy akcji** wpisz nazwę, np. **Dział operacji IT — powiadom**, a w polu **Krótka nazwa** wpisz skrót, np. **itops-n**.  
   d. Sprawdź, czy wartości domyślne w polach **Subskrypcja** i **Grupa zasobów** są poprawne. Jeśli nie, wybierz je z listy rozwijanej.  
   d. W sekcji Akcje wpisz nazwę akcji, np. **Wyślij wiadomość e-mail**, a w obszarze **Typ akcji** wybierz z listy rozwijanej pozycję **Wiadomość e-mail/SMS/wypychana/głosowa**. Po prawej stronie zostanie otwarte okienko właściwości **Wiadomość e-mail/SMS/wypychana/głosowa**, w którym podać dodatkowe informacje.  
   e. W okienku **Wiadomość e-mail/SMS/wypychana/głosowa** włącz opcję **E-mail** i podaj prawidłowy adres e-mail SMTP, na który mają być wysyłane wiadomości.  
   f. Kliknij przycisk **OK**, aby zapisać zmiany.  
       ![Tworzenie nowej grupy akcji](./media/log-analytics-tutorial-response/action-group-properties-01.png)

1. Kliknij przycisk **OK**, aby zakończyć definiowanie grupy akcji.
1. Kliknij przycisk **Utwórz regułę alertu**, aby zdefiniować regułę alertu. Rozpoczyna ona działanie od razu.

    ![Zakończenie tworzenia nowej reguły alertu](./media/log-analytics-tutorial-response/alert-rule-01.png)

## <a name="view-your-alerts-in-azure-portal"></a>Wyświetlanie alertów w witrynie Azure Portal
Po utworzeniu alertu możesz wyświetlać alerty Azure w jednym okienku i zarządzać wszystkimi regułami alertów w subskrypcjach Azure. Jest w nim wyświetlana lista wszystkich reguł alertów (włączonych lub wyłączonych), którą można porządkować na podstawie zasobów docelowych, grup zasobów, nazwy reguły lub stanu. Znajduje się tu również podsumowanie wszystkich wyzwolonych alertów oraz wszystkich skonfigurowanych/włączonych reguł alertów.

![Strona stanu alertów platformy Azure](./media/log-analytics-tutorial-response/azure-alerts-02.png)

Po wyzwoleniu alertu tabela zawiera warunek alertu oraz liczbę jego wystąpień w wybranym okresie (domyślnie w ciągu ostatnich sześciu godzin).  W skrzynce odbiorczej powinna się znajdować odpowiednia wiadomość e-mail zawierająca informacje podobne do tych w poniższym przykładzie: maszynę wirtualną, której alert dotyczy, oraz najważniejsze wyniki pasujące do zapytania wyszukiwania w tym przypadku.

![Przykład akcji alertu przy użyciu wiadomości e-mail](./media/log-analytics-tutorial-response/azure-alert-email-notification-01.png)

## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono sposób aktywnego identyfikowania i reagowania reguł alertów na problemy podczas uruchamiania przeszukiwania dzienników w zaplanowanych odstępach czasu i w przypadku dopasowania do określonych kryteriów.

Użyj tego linku, aby wyświetlić przykłady wstępnie utworzonych skryptów usługi Log Analytics.

> [!div class="nextstepaction"]
> [Przykłady skryptów usługi Log Analytics](powershell-samples.md)
