---
title: IT Service Management Connector w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie IT Service Management Connector (ITSMC) i informacje o tym, jak używać tego rozwiązania centralne monitorowanie i zarządzanie nimi narzędzia ITSM elementów roboczych w usłudze Azure Log Analytics i szybkie rozwiązywanie wszelkich problemów.
services: log-analytics
documentationcenter: ''
author: jyothirmaisuri
manager: riyazp
editor: ''
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: v-jysur
ms.openlocfilehash: abbd26779cefaf52c6f2247a5d27db25f280c930
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60395874"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Azure nawiązać połączenie narzędzia ITSM za pomocą łącznika zarządzania usługami IT

![Symbol łącznika zarządzania usługami IT](media/itsmc-overview/itsmc-symbol.png)

IT Service Management Connector (ITSMC) umożliwia łączenie z platformy Azure i obsługiwanych produktów zarządzania usługi IT (ITSM) / usług.

Usługi platformy Azure, takich jak usługa Log Analytics i Azure Monitor zapewnia narzędzia umożliwiające wykrywanie, analizowanie i rozwiązywanie problemów z usługą platformy Azure i zasobów nienależących do platformy Azure. Jednak elementy robocze, zazwyczaj związane z problemem znajdują się w ITSM produkt/usługę. Łącznik ITSM zapewnia dwukierunkowe połączenie między platformą Azure i ITSM narzędzia ułatwiające szybsze rozwiązywanie problemów.

ITSMC obsługuje połączenia za pomocą następujących narzędziami ITSM:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

Za pomocą ITSMC możesz

-  Utwórz elementy robocze w narzędziu ITSM, w oparciu o alerty platformy Azure (alerty metryki, alerty dziennika aktywności i alertów usługi Log Analytics).
-  Opcjonalnie możesz zsynchronizować zdarzenia i zmienić dane żądania z narzędziem ITSM do obszaru roboczego usługi Azure Log Analytics.


Aby zacząć korzystać łącznik ITSM, wykonując następujące czynności:

1.  [Dodaj rozwiązanie łącznik ITSM](#adding-the-it-service-management-connector-solution)
2.  Utwórz połączenie narzędzia ITSM
3.  [Użyj połączenia](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Dodawanie IT usługi rozwiązania do zarządzania łącznika

Przed utworzeniem połączenia należy dodać rozwiązanie łącznik ITSM.

1. W witrynie Azure portal kliknij **+ nowy** ikony.

   ![Usługa Azure nowy zasób](media/itsmc-overview/azure-add-new-resource.png)

2. Wyszukaj **łącznika zarządzania usługami IT** w portalu Marketplace i kliknij **Utwórz**.

   ![Dodaj rozwiązanie ITSMC](media/itsmc-overview/add-itsmc-solution.png)

3. W **obszaru roboczego pakietu OMS** wybierz obszar roboczy usługi Azure Log Analytics, którym chcesz zainstalować rozwiązanie.
   >[!NOTE]
   >W ramach ciągłego przejście z programu Microsoft Operations Management Suite (OMS) do usługi Azure Monitor obszarów roboczych pakietu OMS są teraz nazywane obszarów roboczych usługi Log Analytics.
4. W **ustawienia obszaru roboczego pakietu OMS** wybierz grupy zasobów, której chcesz utworzyć zasób rozwiązania.

   ![Obszar roboczy ITSMC](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >W ramach ciągłego przejście z programu Microsoft Operations Management Suite (OMS) do usługi Azure Monitor obszarów roboczych pakietu OMS są teraz nazywane obszarów roboczych usługi Log Analytics.

5. Kliknij pozycję **Utwórz**.

Podczas wdrażania zasobów rozwiązania powiadomienie pojawi się u góry po prawej krawędzi okna.


## <a name="creating-an-itsm--connection"></a>Tworzenie połączenia narzędzia ITSM

Po zainstalowaniu rozwiązania, można utworzyć połączenie.

Do utworzenia połączenia, konieczne będzie przeznaczonego do przygotowania z narzędziem ITSM, aby zezwolić na połączenia z rozwiązania łącznik ITSM.  

W zależności od produktu ITSM, do którego jest nawiązywane wykonaj następujące czynności:

- [System Center Service Manager (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Gdy masz przygotowany tak narzędziami ITSM, wykonaj poniższe kroki, aby utworzyć połączenie:

1. Przejdź do **wszystkie zasoby**, poszukaj **ServiceDesk(YourWorkspaceName)**.
2. W obszarze **źródła danych obszaru roboczego** w okienku po lewej stronie kliknij **połączenia narzędzia ITSM**.
   ![Połączenia narzędzia ITSM](media/itsmc-overview/itsm-connections.png)

   Ta strona zawiera listę połączeń.
3. Kliknij przycisk **Dodaj połączenie**.

   ![Dodawanie połączenia narzędzia ITSM](media/itsmc-overview/add-new-itsm-connection.png)

4. Określ ustawienia połączenia, zgodnie z opisem w [konfigurowania połączenia ITSMC z artykułu produktów i usług ITSM](../../azure-monitor/platform/itsmc-connections.md).

   > [!NOTE]
   > 
   > Domyślnie ITSMC odświeża dane konfiguracji połączenia raz w co 24 godziny. Aby odświeżyć dane tego połączenia, natychmiast wszelkie modyfikacje lub szablonu aktualizacji, które wprowadzisz, kliknij **synchronizacji** przycisku w bloku tego połączenia.

   ![Odświeżanie połączenia](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Użycie rozwiązania
   Za pomocą rozwiązania łącznik ITSM, można utworzyć elementy robocze z alertów platformy Azure, alertów usługi Log Analytics i rekordów dziennika usługi Log Analytics.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Utwórz elementy robocze ITSM z alertów platformy Azure

Po utworzeniu połączenia narzędzia ITSM utworzone, można utworzyć elementy robocze w oparciu o alerty platformy Azure przy użyciu narzędziu ITSM **akcji ITSM** w **grup akcji**.

Grupy akcji umożliwiają modułowego i wielokrotnego użytku programu wyzwolenie akcji w przypadku alertów platformy Azure. Można użyć grup akcji przy użyciu alertów dotyczących metryk i alertów dziennika aktywności i alertów usługi Azure Log Analytics w witrynie Azure portal.

Postępuj zgodnie z następującą procedurą:

1. W witrynie Azure portal kliknij **Monitor**.
2. W okienku po lewej stronie kliknij **grup akcji**. **Dodaj grupę akcji** zostanie wyświetlone okno.

    ![Grupy akcji](media/itsmc-overview/action-groups.png)

3. Podaj **nazwa** i **ShortName** grupy akcji. Wybierz **grupy zasobów** i **subskrypcji** gdzie chcesz utworzyć grupę akcji.

    ![Szczegóły grupy akcji](media/itsmc-overview/action-groups-details.png)

4. Z listy akcji wybierz **ITSM** do menu rozwijanego **typ akcji**. Podaj **nazwa** akcji, a następnie kliknij przycisk **Edytuj szczegóły**.
5. Wybierz **subskrypcji** gdzie znajduje się obszar roboczy usługi Log Analytics. Wybierz **połączenia** name (nazwa łącznik ITSM), następuje nazwy swojego obszaru roboczego. Na przykład "MyITSMMConnector(MyWorkspace)".

    ![Szczegóły akcji ITSM](media/itsmc-overview/itsm-action-details.png)

6. Wybierz **elementu roboczego** typu z menu rozwijanego.
   Wybrać opcję użycia istniejącego szablonu lub próbie wypełnienia pola związanej z produktu ITSM.
7. Kliknij przycisk **OK**.

Podczas tworzenia/edytowania regułę alertu z systemu Azure, należy użyć grupy akcji, która ma akcję ITSM. Po wyzwoleniu alertu, element roboczy jest utworzone/zaktualizowane w narzędziem ITSM.

> [!NOTE]
> 
> Aby uzyskać informacje dotyczące cen akcji ITSM, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/monitor/) dla grup akcji.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Wizualizuj i Analizuj zdarzenia i dane żądania zmiany

Łącznik ITSM, zgodnie z konfiguracją, podczas konfigurowania połączenia, można zsynchronizować maksymalnie 120 dni zdarzenia i zmiany danych żądania. Schemat rekordu dziennika dla tych danych jest podawany jako [następnej sekcji](#additional-information).

Dane żądania zdarzenia i zmiany mogą być wizualizowane w rozwiązaniu za pomocą pulpitu nawigacyjnego łącznik ITSM.

![Ekran analiza dziennika](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Pulpit nawigacyjny zawiera również informacje dotyczące stanu łącznika, który może służyć jako punkt wyjścia do analizowania problemów z połączeniami.

Można również wizualizować zdarzenia zsynchronizowane, względem których to dotyczy komputerów, w ramach rozwiązania mapy usługi.

Usługa Service Map automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Umożliwia ona wyświetlenie Twoich serwerów zgodnie z nich myślisz — jako wzajemnie połączonych systemów dostarczających krytycznych usług. Usługa Service Map Pokazuje połączenia między serwerami, procesami i portami w dowolnej architekturze połączenia TCP bez konieczności konfiguracji wymagane inne niż Instalacja agenta. [Dowiedz się więcej](../../azure-monitor/insights/service-map.md).

Jeśli używasz rozwiązania mapy usługi, można wyświetlić elementy działu usług, które są tworzone w rozwiązania ITSM, jak pokazano w poniższym przykładzie:

![Ekran analiza dziennika](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Więcej informacji: [Mapa usługi](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>Dodatkowe informacje

### <a name="data-synced-from-itsm-product"></a>Dane synchronizowane z produktu ITSM
Zdarzenia i żądania zmiany są synchronizowane z produktu ITSM, do obszaru roboczego usługi Log Analytics na podstawie konfiguracji tego połączenia.

Poniżej przedstawiono przykłady danych zbieranych przez ITSMC:

> [!NOTE]
> 
> W zależności od typu elementu roboczego importowane do usługi Log Analytics **ServiceDesk_CL** zawiera następujące pola:

**Element roboczy:** **Zdarzenia**  
ServiceDeskWorkItemType_s="Incident"

**Pola**

- ServiceDeskConnectionName
- Identyfikator pomocy technicznej
- Stan
- Pilność
- Wpływ
- Priorytet
- Eskalacja
- Utworzone przez
- Rozwiązany przez
- Zamknięte przez
- Element źródłowy
- Przypisano do
- Category
- Tytuł
- Opis
- Data utworzenia
- Data zamknięcia
- Data rozwiązania
- Data ostatniej modyfikacji
- Computer (Komputer)


**Element roboczy:** **Żądania zmiany**

ServiceDeskWorkItemType_s="ChangeRequest"

**Pola**
- ServiceDeskConnectionName
- Identyfikator pomocy technicznej
- Utworzone przez
- Zamknięte przez
- Element źródłowy
- Przypisano do
- Tytuł
- Type
- Category
- Stan
- Eskalacja
- Stan konfliktu
- Pilność
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

## <a name="output-data-for-a-servicenow-incident"></a>Dane wyjściowe dla incydentu usługi ServiceNow

| Pole analizy dziennika | Pole usługi ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Liczba |
| IncidentState_s | Stan |
| Urgency_s |Pilność |
| Impact_s |Wpływ|
| Priority_s | Priorytet |
| CreatedBy_s | Otwarte przez |
| ResolvedBy_s | Rozwiązane przez|
| ClosedBy_s  | Zamknięte przez |
| Source_s| Typ kontaktu |
| AssignedTo_s | Przypisane do  |
| Category_s | Category |
| Title_s|  Krótki opis |
| Description_s|  Uwagi |
| CreatedDate_t|  Otwierano |
| ClosedDate_t| Zamknięte|
| ResolvedDate_t|Rozwiązane|
| Computer (Komputer)  | Element konfiguracji |

## <a name="output-data-for-a-servicenow-change-request"></a>Żądanie zmiany w danych wyjściowych dla usługi ServiceNow

| Log Analytics | Pole usługi ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Liczba |
| CreatedBy_s | Żądane przez |
| ClosedBy_s | Zamknięte przez |
| AssignedTo_s | Przypisane do  |
| Title_s|  Krótki opis |
| Type_s|  Type |
| Category_s|  Category |
| CRState_s|  Stan|
| Urgency_s|  Pilność |
| Priority_s| Priorytet|
| Risk_s| Ryzyko|
| Impact_s| Wpływ|
| RequestedDate_t  | Zażądano według daty |
| ClosedDate_t | Data zamknięcia |
| PlannedStartDate_t  |     Planowana data rozpoczęcia |
| PlannedEndDate_t  |   Planowana data zakończenia |
| WorkStartDate_t  | Rzeczywista data rozpoczęcia |
| WorkEndDate_t | Rzeczywista data zakończenia|
| Description_s | Opis |
| Computer (Komputer)  | Element konfiguracji |


## <a name="troubleshoot-itsm-connections"></a>Rozwiązywanie problemów z połączeniami narzędzia ITSM
1. W przypadku utraty połączenia z poziomu interfejsu użytkownika połączone źródło, z **błąd podczas zapisywania połączenia** wiadomości, wykonaj następujące czynności:
   - W przypadku połączeń usługi ServiceNow, Cherwell i Provance  
   - Upewnij się, że poprawnie wprowadzono nazwę użytkownika, hasła, identyfikator klienta i klucz tajny klienta dla każdego połączenia.  
   - Sprawdź, że masz wystarczające uprawnienia w odpowiednich produkcie ITSM, aby nawiązać połączenie.  
   - W przypadku połączeń programu Service Manager  
   - Upewnij się, że aplikacja sieci Web została pomyślnie wdrożona, i zostanie utworzone połączenie hybrydowe. Aby sprawdzić, pomyślnie ustanawiane jest połączenie z maszyną programu Service Manager na lokalnych, odwiedź adres URL aplikacji sieci Web zgodnie z opisem w dokumentacji dotyczącej wprowadzania [połączenia hybrydowego](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection).  

2. Jeśli dane z usługi ServiceNow, nie podlega wprowadzenie do usługi Log Analytics, upewnij się, że nie jest uśpiony wystąpienia usługi ServiceNow. Wystąpienia deweloperów usługi ServiceNow czasami przechodzi w tryb uśpienia podczas bezczynności przez długi czas. W przeciwnym wypadku zgłosić problem.
3. Jeśli wyzwolenie alertów usługi Log Analytics, ale działają elementy nie są tworzone w produkcie ITSM lub elementy konfiguracji nie są tworzone/połączone elementy robocze lub inne ogólne informacje, można znaleźć w następujących miejscach:
   -  ITSMC: Rozwiązanie zawiera podsumowanie połączeń/pracy elementów/komputerów itp. Wyświetlanie kafelka kliknij **stan łącznika**, która spowoduje przejście do **przeszukiwania dzienników** przy użyciu odpowiednich zapytań. Przyjrzyj się rekordów dziennika z LogType_S jako błąd Aby uzyskać więcej informacji.
   - **Wyszukiwania w dzienniku** strony: wyświetlić informacje o powiązanych z/błędy bezpośrednio przy użyciu zapytań `*`ServiceDeskLog_CL`*`.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Rozwiązywanie problemów z wdrożenia aplikacji sieci Web programu Service Manager
1.  W przypadku problemów z wdrażaniem aplikacji sieci web upewnij się, że masz wystarczające uprawnienia w ramach subskrypcji, jak podczas tworzenia/wdrażanie zasobów.
2.  Jeśli otrzymasz **"Odwołanie nie zostały ustawione na wystąpienie obiektu do obiektu"** wystąpił błąd podczas uruchamiania [skryptu](itsmc-service-manager-script.md), upewnij się, czy wprowadzono prawidłowe wartości w obszarze **Konfiguracja użytkownika** sekcji .
3.  Jeśli nie utworzyć przestrzeń nazw usługi relay magistrali usług, upewnij się, że dostawca wymagany zasób jest zarejestrowany w ramach subskrypcji. Jeśli nie jest zarejestrowany, należy ręcznie utworzyć przestrzeń nazw usługi relay magistrali usług w witrynie Azure portal. Można również tworzyć je podczas [Tworzenie połączenia hybrydowego](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection) w witrynie Azure portal.


## <a name="contact-us"></a>Skontaktuj się z nami

Dla każdego zapytania lub opinie na temat łącznika zarządzania usługami IT, skontaktuj się z nami pod adresem [ omsitsmfeedback@microsoft.com ](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Kolejne kroki
[Dodawanie ITSM produktów/usług do łącznika zarządzania usługami IT](../../azure-monitor/platform/itsmc-connections.md).
