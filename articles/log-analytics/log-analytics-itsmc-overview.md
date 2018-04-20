---
title: IT usługi łącznika zarządzania w programie Azure Log Analytics | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie IT usługi zarządzania łącznika (ITSMC) i informacje o sposobie używania tego rozwiązania, aby centralnie monitorować i zarządzać nimi Zarządzanie usługami IT — elementów roboczych w Azure Log Analytics i szybkie rozwiązywanie problemów.
services: log-analytics
documentationcenter: ''
author: JYOTHIRMAISURI
manager: riyazp
editor: ''
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: v-jysur
ms.openlocfilehash: c39cf464a7e838fecf7ebd4a3cbb08612388a5fa
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Połącz Azure narzędzia Zarządzanie usługami IT — za pomocą łącznika zarządzania usługi IT

![Symbol łącznika usługi zarządzania IT](./media/log-analytics-itsmc/itsmc-symbol.png)

Łącznik zarządzania usługi IT (ITSMC) umożliwia łączenie z platformy Azure i obsługiwanych produktu zarządzania usługi IT (zarządzanie, usługami IT —) lub usługi.

Usług Azure, takich jak analizy dzienników i Azure Monitor zapewniają narzędzia do wykrywania, analizowanie i rozwiązywanie problemów z platformy Azure i zasobów innych niż Azure. Jednak elementów pracy związanych z problemu zwykle znajdują się w zarządzanie usługami IT — produktu lub usługi. Zarządzanie usługami IT — łącznika zawiera dwukierunkowe połączenie między usługą platformy Azure i zarządzanie usługami IT — narzędzia ułatwiające rozwiązywanie problemów dotyczących szybciej.

ITSMC obsługuje połączenia z następujących narzędzi Zarządzanie usługami IT —:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

Za pomocą ITSMC można

-  Tworzenie elementów roboczych w narzędziu Zarządzanie usługami IT —, oparte na alerty Azure (metryki alertów, alerty dziennika aktywności i analizy dzienników alertów).
-  Opcjonalnie można zsynchronizować zdarzenie i zmienić dane żądania z własnych narzędzi Zarządzanie usługami IT — z obszarem roboczym usługi Analiza dzienników Azure.


Możesz rozpocząć używać łącznika Zarządzanie usługami IT —, wykonując następujące czynności:

1.  [Dodaj rozwiązanie łącznika Zarządzanie usługami IT —](#adding-the-it-service-management-connector-solution)
2.  [Utwórz połączenie Zarządzanie usługami IT —](#creating-an-itsm-connection)
3.  [Użyj połączenia](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Dodawanie IT usługi rozwiązania do zarządzania łącznika

Przed utworzeniem połączenia, należy dodać Zarządzanie usługami IT — rozwiązanie łącznika.

1.  W portalu Azure kliknij **+ nowy** ikony.

    ![Nowy zasób platformy Azure](./media/log-analytics-itsmc/azure-add-new-resource.png)

2.  Wyszukaj **łącznika zarządzania usługi IT** Marketplace i kliknij przycisk **Utwórz**.

    ![Dodaj rozwiązanie ITSMC](./media/log-analytics-itsmc/add-itsmc-solution.png)

3.  W **obszarem roboczym pakietu OMS** wybierz obszar roboczy analizy dzienników Azure, której chcesz zainstalować rozwiązania.
4.  W **ustawień obszaru roboczego OMS** wybierz grupa zasobów, której chcesz utworzyć rozwiązanie zasobu.

    ![Obszar roboczy ITSMC](./media/log-analytics-itsmc/itsmc-solution-workspace.png)

5.  Kliknij przycisk **Utwórz**.

Po wdrożeniu rozwiązania zasobu powiadomienie ma być wyświetlany u góry po prawej części okna.


## <a name="creating-an-itsm--connection"></a>Tworzenie połączenia Zarządzanie usługami IT —

Po zainstalowaniu rozwiązania, można utworzyć połączenia.

Do utworzenia połączenia, konieczne będzie przygotowywanie zezwalały na połączenie z rozwiązania łącznika Zarządzanie usługami IT — zarządzanie usługami IT — narzędzie.  

W zależności od produkt Zarządzanie usługami IT —, z którym jest nawiązywane wykonaj następujące kroki:

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Gdy ma prepped narzędziami Zarządzanie usługami IT —, wykonaj poniższe kroki, aby utworzyć połączenie:

1.  Przejdź do **wszystkie zasoby**, wyszukaj **ServiceDesk(YourWorkspaceName)**.
2.  W obszarze **źródeł danych obszaru roboczego** w okienku po lewej stronie kliknij **połączeń Zarządzanie usługami IT —**.
    ![Zarządzanie usługami IT — połączenia](./media/log-analytics-itsmc/itsm-connections.png)

    Ta strona zawiera listę połączeń.
3.  Kliknij przycisk **Dodaj połączenie**.

    ![Dodaj połączenie Zarządzanie usługami IT —](./media/log-analytics-itsmc/add-new-itsm-connection.png)

4.  Określ ustawienia połączenia, zgodnie z opisem w [konfiguracji połączenia ITSMC z artykułu produktów i usług Zarządzanie usługami IT —](log-analytics-itsmc-connections.md).

    > [!NOTE]

    > Domyślnie ITSMC odświeża dane konfiguracji połączenia raz w co 24 godziny. Aby odświeżyć tego połączenia danych natychmiast dla wszystkich edycji lub szablonu aktualizacje, które zostaną wprowadzone, kliknij przycisk "Odśwież", wyświetlane obok połączenia.

    ![Odśwież połączenie](./media/log-analytics-itsmc/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Użycie rozwiązania
   Dzięki rozwiązaniu Zarządzanie usługami IT — łącznik można utworzyć elementów roboczych z Azure alertów, alerty analizy dzienników i rekordów dziennika analizy dzienników.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Tworzenie elementów roboczych Zarządzanie usługami IT — Azure alertów

Po utworzeniu połączenia Zarządzanie usługami IT — utworzone, możesz utworzyć elementy robocze w narzędzie Zarządzanie usługami IT — na podstawie Azure alertów, za pomocą **Zarządzanie usługami IT — Akcja** w **grupy akcji**.

Grupy akcji umożliwiają moduły i wielokrotnego użytku z wyzwalają akcje dla alerty Azure. Można użyć grup akcji z metryki alertów, alerty dziennika aktywności i alerty usługi Analiza dzienników Azure w portalu Azure.

Postępuj zgodnie z następującą procedurą:

1. W portalu Azure kliknij **Monitor**.
2. W okienku po lewej stronie kliknij **grupy akcji**. **Dodaj grupę akcji** zostanie wyświetlone okno.

    ![Grupy akcji](media/log-analytics-itsmc/action-groups.png)

3. Podaj **nazwa** i **nazwa_skrócona** grupy działań. Wybierz **grupy zasobów** i **subskrypcji** której chcesz utworzyć grupy działań.

    ![Szczegóły grupy akcji](media/log-analytics-itsmc/action-groups-details.png)

4. Na liście akcji wybierz **Zarządzanie usługami IT —** z menu rozwijanego dla **typ akcji**. Podaj **nazwa** dla akcji, a następnie kliknij przycisk **Edytuj szczegóły**.
5. Wybierz **subskrypcji** którym znajduje się obszar roboczy analizy dzienników. Wybierz **połączenia** name (nazwa łącznika Zarządzanie usługami IT —) następuje nazwa obszaru roboczego. Na przykład "MyITSMMConnector(MyWorkspace)".

    ![Akcja Zarządzanie usługami IT — szczegóły](./media/log-analytics-itsmc/itsm-action-details.png)

6. Wybierz **elementu roboczego** typu z menu rozwijanego.
   Wybierz korzystać z istniejących szablonów lub wypełnienie pól wymaganych przez produkt Zarządzanie usługami IT —.
7. Kliknij przycisk **OK**.

Podczas tworzenia/edytowania Azure reguły alertu, należy użyć grupy akcji, która ma akcję Zarządzanie usługami IT —. Gdy alert jest wyzwalane, element roboczy jest utworzone/zaktualizowane w narzędziu Zarządzanie usługami IT —.

>[!NOTE]

> Aby uzyskać informacje o cenach Zarządzanie usługami IT — działania, zobacz [cennikiem](https://azure.microsoft.com/pricing/details/monitor/) dla grup działań.


## <a name="create-itsm-work-items-from-log-analytics-alerts"></a>Tworzenie elementów roboczych Zarządzanie usługami IT — alertów analizy dzienników

Reguły alertów można skonfigurować w portalu Azure Log Analytics do tworzenia elementów roboczych w narzędziu Zarządzanie usługami IT — przy użyciu poniższej procedury.

1. Z **wyszukiwania dziennika** okna, uruchom zapytania wyszukiwania dziennika, aby wyświetlić dane. Wyniki zapytania są źródłem dla elementów roboczych.
2. W **wyszukiwania dziennika**, kliknij przycisk **alertu** otworzyć **Dodaj regułę alertu** strony.

    ![Ekran analiza dziennika](./media/log-analytics-itsmc/itsmc-work-items-for-azure-alerts.png)

3. Na **Dodaj regułę alertu** okna, podaj wymagane szczegóły dotyczące **nazwa**, **ważność**, **zapytania wyszukiwania**, i **alertu kryteria** (pomiar Metryka okno czasu).
4. Wybierz **tak** dla **akcje Zarządzanie usługami IT —**.
5. Wybierz połączenie Zarządzanie usługami IT — od **połączenia wybierz** listy.
6. Podaj szczegóły zgodnie z potrzebami.
7. Aby utworzyć element roboczy osobne dla każdego wpisu dziennika tego alertu, wybierz **utworzyć poszczególnych elementach roboczych dla każdego wpisu dziennika** wyboru.

    Lub

    Pozostaw to pole wyboru niezaznaczone można utworzyć tylko jeden element roboczy dla dowolnej liczby wpisów dziennika w ramach tego alertu.

7. Kliknij pozycję **Zapisz**.

Można wyświetlić alertu analizy dzienników, który został utworzony w obszarze **Ustawienia > alerty**. Zarządzanie usługami IT — połączenie odpowiednich elementów roboczych są tworzone po spełnieniu warunku określony alert.


## <a name="create-itsm-work-items-from-log-analytics-log-records"></a>Tworzenie elementów roboczych Zarządzanie usługami IT — z rekordów dziennika analizy dzienników

Można też utworzyć elementów roboczych w połączonych źródeł Zarządzanie usługami IT — bezpośrednio z rekordu dziennika. Może to służyć do testowania, jeśli połączenie działa poprawnie.


1. Z **wyszukiwania dziennika**, wyszukaj wymagane dane, wybierz szczegóły, a następnie kliknij przycisk **Utwórz element roboczy**.

    **Tworzenie elementu roboczego Zarządzanie usługami IT —** zostanie wyświetlone okno:

    ![Ekran analiza dziennika](media/log-analytics-itsmc/itsmc-work-items-from-azure-logs.png)

2.   Dodaj następujące informacje:

  - **Tytuł elementu pracy**: tytuł dla elementu roboczego.
  - **Opis elementu roboczego**: opis nowego elementu roboczego.
  - **Wpływ na komputerze**: Nazwa komputera, na którym zostało znalezione te dane dziennika.
  - **Wybierz połączenie**: Zarządzanie usługami IT — połączenie, w którym chcesz utworzyć ten element roboczy.
  - **Element roboczy**: typ elementu roboczego.

3. Aby korzystać z istniejących szablonów elementów pracy zdarzenia, kliknij przycisk **tak** w obszarze **element roboczy Generowanie na podstawie szablonu** opcji, a następnie kliknij przycisk **Utwórz**.

    Lub:

    Kliknij przycisk **nr** Jeśli chcesz podać dostosowanych wartości.

4. Podaj odpowiednie wartości w **typ**, **wpływ**, **pilność**, **kategorii**, i **podkategorii** pola tekstowe, a następnie kliknij przycisk **Utwórz**.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Wizualizuj i analizować zdarzenia i dane żądania zmiany

Podczas konfigurowania połączenia zgodnie z konfiguracją, zarządzanie usługami IT — łącznik mogą synchronizować maksymalnie 120 dni zdarzenia i zmiany danych żądania. Schemat rekordu dziennika dla tych danych jest dostępne w [następnej sekcji](#additional-information).

Można można zwizualizować dane zdarzenia i Zmień na żądanie przy użyciu pulpitu nawigacyjnego Zarządzanie usługami IT — łącznika w rozwiązaniu.

![Ekran analiza dziennika](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

Pulpit nawigacyjny zawiera także informacje o stan łącznika, który może służyć jako punktu wyjścia do analizowania problemów z połączeniami.

Można również wizualizacji incydenty zsynchronizowano przed wpływ na komputerach, w ramach rozwiązania mapy usługi.

Mapa usług automatycznie wykrywa składniki aplikacji w systemach Windows i Linux i mapuje komunikacji między usługami. Umożliwia ona przeglądać serwery jako traktować ich — jako połączonych systemy, które dostarczają usług krytycznych. Mapy usługi pokazuje połączeń między serwerami, procesów, i portów w dowolnej architekturze połączenia TCP z konfiguracji wymagane inne niż instalacji agenta. [Dowiedz się więcej](../operations-management-suite/operations-management-suite-service-map.md).

Jeśli korzystasz z rozwiązania mapy usług, można wyświetlić elementy technicznej usług utworzone w rozwiązaniach Zarządzanie usługami IT —, jak pokazano w poniższym przykładzie:

![Ekran analiza dziennika](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)

Więcej informacji: [mapy usług](../operations-management-suite/operations-management-suite-service-map.md)


## <a name="additional-information"></a>Dodatkowe informacje

### <a name="data-synced-from-itsm-product"></a>Synchronizowane z produktu Zarządzanie usługami IT — danych
Zdarzenia i żądania zmiany są synchronizowane z produktu Zarządzanie usługami IT — do obszaru roboczego analizy dzienników na podstawie konfiguracji tego połączenia.

Poniższe informacje przedstawiono przykłady danych zbieranych przez ITSMC:

> [!NOTE]

> W zależności od typu elementu roboczego zaimportowane do analizy dzienników **ServiceDesk_CL** zawiera następujące pola:

**Element pracy:** **zdarzenia**  
ServiceDeskWorkItemType_s="Incident"

**Pola**

- ServiceDeskConnectionName
- Identyfikator technicznej usługi
- Stan
- Pilności
- Wpływ
- Priorytet
- Eskalacja
- Utworzone przez
- Rozwiązany przez
- Zamknięte przez
- Element źródłowy
- Przypisano do
- Kategoria
- Stanowisko
- Opis
- Data utworzenia
- Data zamknięcia
- Data rozwiązania
- Data ostatniej modyfikacji
- Computer (Komputer)


**Element pracy:** **żądania zmiany**

ServiceDeskWorkItemType_s="ChangeRequest"

**Pola**
- ServiceDeskConnectionName
- Identyfikator technicznej usługi
- Utworzone przez
- Zamknięte przez
- Element źródłowy
- Przypisano do
- Stanowisko
- Typ
- Kategoria
- Stan
- Eskalacja
- Konflikt stanu
- Pilności
- Priorytet
- Ryzyko
- Wpływ
- Przypisano do
- Data utworzenia
- Data zamknięcia
- Data ostatniej modyfikacji
- Żądana data
- Planowana data rozpoczęcia
- Planowana data zakończenia
- Data rozpoczęcia pracy
- Data zakończenia pracy
- Opis
- Computer (Komputer)

## <a name="output-data-for-a-servicenow-incident"></a>Dane wyjściowe dla zdarzenia usługi ServiceNow

| Pole analizy dzienników | Pole ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Liczba |
| IncidentState_s | Stan |
| Urgency_s |Pilności |
| Impact_s |Wpływ|
| Priority_s | Priorytet |
| CreatedBy_s | Otwarty przez |
| ResolvedBy_s | Rozwiązane przez|
| ClosedBy_s  | Zamknięte przez |
| Source_s| Skontaktuj się z typu |
| AssignedTo_s | Przypisane do  |
| Category_s | Kategoria |
| Title_s|  Krótki opis |
| Description_s|  Uwagi |
| CreatedDate_t|  Otwarte |
| ClosedDate_t| zamknięte|
| ResolvedDate_t|Rozwiązany|
| Computer (Komputer)  | element konfiguracji |

## <a name="output-data-for-a-servicenow-change-request"></a>Dane wyjściowe dla usługi ServiceNow żądania zmiany

| Log Analytics | Pole ServieNow |
|:--- |:--- |
| ServiceDeskId_s| Liczba |
| CreatedBy_s | Żądane przez |
| ClosedBy_s | Zamknięte przez |
| AssignedTo_s | Przypisane do  |
| Title_s|  Krótki opis |
| Type_s|  Typ |
| Category_s|  Kategoria |
| CRState_s|  Stan|
| Urgency_s|  Pilności |
| Priority_s| Priorytet|
| Risk_s| Ryzyko|
| Impact_s| Wpływ|
| RequestedDate_t  | Żądana według daty |
| ClosedDate_t | Data zamknięcia |
| PlannedStartDate_t  |     Planowana data rozpoczęcia |
| PlannedEndDate_t  |   Planowana data zakończenia |
| WorkStartDate_t  | Rzeczywista data rozpoczęcia |
| WorkEndDate_t | Rzeczywista data zakończenia|
| Description_s | Opis |
| Computer (Komputer)  | Element konfiguracji |


## <a name="troubleshoot-itsm-connections"></a>Rozwiązywanie problemów z połączeniami Zarządzanie usługami IT —
1.  Jeśli połączenie nie powiedzie się z poziomu interfejsu użytkownika połączenia źródła z **błąd podczas zapisywania połączenia** wiadomości, wykonaj następujące czynności:
- W przypadku połączeń usługi ServiceNow, Cherwell i Provance  
           -Upewnij się, poprawnie wprowadzono nazwę użytkownika, hasło, identyfikator klienta i klucz tajny klienta dla każdego połączenia.  
           -Sprawdź, czy masz wystarczające uprawnienia w produktu Zarządzanie usługami IT — do nawiązania połączenia.  
- W przypadku połączeń programu Service Manager  
           -Sprawdź, czy aplikacja sieci Web zostanie pomyślnie wdrożona i utworzeniu połączenia hybrydowego. Aby sprawdzić pomyślnym nawiązaniu połączenia z komputera lokalnego programu Service Manager, odwiedź adres URL aplikacji sieci Web zgodnie z opisem w dokumentacji dotyczącej wprowadzania [połączenia hybrydowego](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).  

2.  Jeśli dane z usługi ServiceNow jest wprowadzenie nie zostały zsynchronizowane z analizy dzienników, upewnij się, że usługi ServiceNow, wystąpienie nie jest uśpiony. Wystąpień deweloperów usługi ServiceNow czasami Przejdź w stan uśpienia podczas bezczynności przez dłuższy okres. W przeciwnym wypadku zgłosić problem.
3.  Jeśli alerty OMS wyzwalać, ale działa elementy nie są tworzone w produkcie Zarządzanie usługami IT — lub elementy konfiguracji nie są utworzone/połączone elementy robocze lub inne informacje ogólne, można znaleźć w następujących miejscach:
 -  ITSMC: Rozwiązanie zawiera podsumowanie połączeń/pracy elementów/komputerów itp. Kliknij przycisk przedstawiający Kafelek **stan łącznika**, która powoduje przejście do **wyszukiwania dziennika** z odpowiednimi zapytania. Przyjrzyj się rekordy dziennika z LogType_S jako błąd Aby uzyskać więcej informacji.
 - **Zaloguj się wyszukiwania** strony: umożliwia wyświetlanie informacji powiązanych z/błędy bezpośrednio za pomocą zapytania `*`ServiceDeskLog_CL`*`.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Rozwiązywanie problemów z wdrażaniem aplikacji sieci Web programu Service Manager
1.  W przypadku wystąpienia jakichkolwiek problemów z wdrażania aplikacji sieci web upewnij się, że masz wystarczające uprawnienia w wymienionych do tworzenia/wdrożenie zasobów subskrypcji.
2.  Jeśli otrzymasz **"Obiekt odwołanie nie jest ustawione na wystąpienie obiektu"** błąd podczas uruchamiania [skryptu](log-analytics-itsmc-service-manager-script.md), upewnij się, że wprowadzono prawidłowe wartości w obszarze **Konfiguracja użytkownika** sekcji .
3.  Nie można utworzyć przestrzeń nazw przekaźnik magistrali usług, upewnij się, że dostawca wymagany zasób jest zarejestrowany w subskrypcji. Jeśli nie jest zarejestrowany, należy ręcznie utworzyć przestrzeń nazw przekaźnik magistrali usług przy użyciu portalu Azure. Można również utworzyć go podczas [Tworzenie połączenia hybrydowego](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) z portalu Azure.


## <a name="contact-us"></a>Skontaktuj się z nami

Dla zapytania lub opinii na temat łącznika zarządzania usługi IT, skontaktuj się z nami pod adresem [ omsitsmfeedback@microsoft.com ](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Kolejne kroki
[Dodaj do łącznika zarządzania usługi IT zarządzanie usługami IT — produktów/usług](log-analytics-itsmc-connections.md).
