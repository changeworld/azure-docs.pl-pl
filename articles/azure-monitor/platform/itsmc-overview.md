---
title: Łącznik zarządzania usługami IT w usłudze Azure Log Analytics | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie programu IT Service Management Connector (ITSMC) oraz informacje dotyczące sposobu korzystania z tego rozwiązania do centralnego monitorowania elementów roboczych ITSM i zarządzania nimi w usłudze Azure Log Analytics oraz szybkiego rozwiązywania problemów.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 50bab4c26046059b993c19a030a8f840ae336ef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274543"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Łączenie platformy Azure z narzędziami ITSM przy użyciu łącznika zarządzania usługami IT

![Symbol łącznika zarządzania usługami IT](media/itsmc-overview/itsmc-symbol.png)

Łącznik zarządzania usługami IT (ITSMC) umożliwia połączenie platformy Azure z obsługiwanym produktem/usługą zarządzania usługami IT (ITSM).

Usługi platformy Azure, takie jak usługa Log Analytics i Usługa Azure Monitor, zapewniają narzędzia do wykrywania, analizowania i rozwiązywania problemów z zasobami platformy Azure i innych niż azure. Jednak elementy robocze związane z problemem zazwyczaj znajdują się w produkcie/usłudze ITSM. Łącznik ITSM zapewnia dwukierunkowe połączenie między platformą Azure a narzędziami ITSM, które ułatwia szybsze rozwiązywanie problemów.

ITSMC obsługuje połączenia z następującymi narzędziami ITSM:

-   ServiceNow
-   Menedżer serwisowy centrum systemu
-   Provance (Provance)
-   Cherwell

Dzięki ITSMC możesz:

-  Tworzenie elementów roboczych w narzędziu ITSM na podstawie alertów platformy Azure (alerty metryki, alerty dziennika aktywności i alerty usługi Log Analytics).
-  Opcjonalnie można zsynchronizować zdarzenie i zmienić dane żądania z narzędzia ITSM do obszaru roboczego usługi Azure Log Analytics.

Przeczytaj więcej o [warunkach prawnych i polityce prywatności](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Można rozpocząć korzystanie z łącznika ITSM, wykonując następujące czynności:

1.  [Dodaj rozwiązanie złącza ITSM](#adding-the-it-service-management-connector-solution)
2.  Tworzenie połączenia ITSM
3.  [Korzystanie z połączenia](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Dodawanie rozwiązania złącza do zarządzania usługami IT

Przed utworzeniem połączenia należy dodać rozwiązanie złącza ITSM.

1. W witrynie Azure portal kliknij + **Nową** ikonę.

   ![Nowy zasób platformy Azure](media/itsmc-overview/azure-add-new-resource.png)

2. Wyszukaj **łącznik zarządzania usługami IT** w portalu Marketplace i kliknij przycisk **Utwórz**.

   ![Dodaj rozwiązanie ITSMC](media/itsmc-overview/add-itsmc-solution.png)

3. W sekcji **Obszar roboczy pakietu OMS** wybierz obszar roboczy usługi Azure Log Analytics, w którym chcesz zainstalować rozwiązanie.
   >[!NOTE]
   > * W ramach trwającego przejścia z pakietu Microsoft Operations Management Suite (OMS) do usługi Azure Monitor obszary robocze pakietu OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.
   > * Łącznik ITSM connector można zainstalować tylko w obszarach roboczych usługi Log Analytics w następujących regionach: East US, West US2, South Central US, West Central US, Central Canada, Europa Zachodnia, Południowa Wielka Brytania, Azja Południowo-Wschodnia, Wschodnia Japonia, Indie Środkowe, Australia Południowo-Wschodnia.

4. W sekcji **Ustawienia obszaru roboczego systemu OMS** wybierz grupę zasobów, w której chcesz utworzyć zasób rozwiązania.

   ![Obszar roboczy ITSMC](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >W ramach trwającego przejścia z pakietu Microsoft Operations Management Suite (OMS) do usługi Azure Monitor obszary robocze pakietu OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.

5. Kliknij przycisk **Utwórz**.

Po wdrożeniu zasobu rozwiązania w prawym górnym rogu okna pojawi się powiadomienie.


## <a name="creating-an-itsm--connection"></a>Tworzenie połączenia ITSM

Po zainstalowaniu rozwiązania można utworzyć połączenie.

Aby utworzyć połączenie, należy przygotować narzędzie ITSM, aby umożliwić połączenie z rozwiązania ITSM Connector.  

W zależności od produktu ITSM, z którego się łączysz, należy wykonać następujące czynności:

- [Menedżer usług centrum systemu (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance (Provance)](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Po przygotowaniu narzędzi ITSM wykonaj poniższe czynności, aby utworzyć połączenie:

1. Przejdź do **witryny Wszystkie zasoby**, poszukaj **servicedesk(YourWorkspaceName)**.
2. W **obszarze ŹRÓDŁA DANYCH OBSZARU ROBOCZEGO** w lewym okienku kliknij pozycję Połączenia **ITSM**.
   ![Połączenia ITSM](media/itsmc-overview/itsm-connections.png)

   Na tej stronie zostanie wyświetlona lista połączeń.
3. Kliknij **pozycję Dodaj połączenie**.

   ![Dodaj połączenie ITSM](media/itsmc-overview/add-new-itsm-connection.png)

4. Określ ustawienia połączenia zgodnie z opisem w [artykule Konfigurowanie połączenia ITSMC z produktami/usługami ITSM.](../../azure-monitor/platform/itsmc-connections.md)

   > [!NOTE]
   >
   > Domyślnie ITSMC odświeża dane konfiguracyjne połączenia raz na 24 godziny. Aby natychmiast odświeżyć dane połączenia w celu uzyskania wszelkich zmian lub aktualizacji szablonów, kliknij przycisk **Synchronizuj** na bloku połączenia.

   ![Odświeżanie połączenia](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Użycie rozwiązania
   Za pomocą rozwiązania ŁĄCZNIKA ITSM można tworzyć elementy robocze z alertów platformy Azure, alertów usługi Log Analytics i rekordów dziennika usługi Log Analytics.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Tworzenie elementów roboczych ITSM z alertów platformy Azure

Po utworzeniu połączenia ITSM można utworzyć elementy robocze w narzędziu ITSM na podstawie alertów platformy Azure, korzystając z **akcji ITSM** w **grupach akcji.**

Grupy akcji zapewniają modułowy i wielokrotnego użytku sposób wyzwalania akcji dla alertów platformy Azure. Grupy akcji można używać z alertami metryk, alertami dziennika aktywności i alertami usługi Azure Log Analytics w witrynie Azure portal.

Postępuj zgodnie z następującą procedurą:

1. W witrynie Azure portal kliknij pozycję **Monitor**.
2. W lewym okienku kliknij pozycję **Grupy akcji**. Zostanie **wyświetlene okno Dodaj grupę akcji.**

    ![Grupy akcji](media/itsmc-overview/action-groups.png)

3. Podaj **nazwę** i **skróconą nazwę** dla grupy akcji. Wybierz **grupę zasobów** i **subskrypcję,** w której chcesz utworzyć grupę akcji.

    ![Szczegóły grup akcji](media/itsmc-overview/action-groups-details.png)

4. Z menu rozwijanego **Dla typu akcji**wybierz polecenie **ITSM.** Podaj **nazwę** akcji i kliknij pozycję **Edytuj szczegóły**.
5. Wybierz **subskrypcję,** w której znajduje się obszar roboczy usługi Log Analytics. Wybierz nazwę **połączenia** (nazwę łącznika ITSM), po której następuje nazwa obszaru roboczego. Na przykład "MyITSMMConnector(MyWorkspace)".

    ![SZCZEGÓŁY AKCJI ITSM](media/itsmc-overview/itsm-action-details.png)

6. Z menu rozwijanego wybierz typ **elementu pracy.**
   Wybierz, aby użyć istniejącego szablonu lub wypełnić pola wymagane przez produkt ITSM.
7. Kliknij przycisk **OK**.

Podczas tworzenia/edytowania reguły alertu platformy Azure należy użyć grupy akcji, która ma akcję ITSM. Po wyzwoleniu alertu element pracy jest tworzony/aktualizowany w narzędziu ITSM.

> [!NOTE]
>
> Aby uzyskać informacje na temat cen akcji ITSM, zobacz [stronę z cenami](https://azure.microsoft.com/pricing/details/monitor/) grup akcji.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Wizualizuj i analizuj dane dotyczące zdarzeń i zmian

Na podstawie konfiguracji podczas konfigurowania połączenia łącznik ITSM może synchronizować do 120 dni danych żądania zdarzenia i zmiany. Schemat rekordu dziennika dla tych danych znajduje się w [następnej sekcji](#additional-information).

Dane dotyczące zdarzeń i zmian można wizualizować za pomocą pulpitu nawigacyjnego łącznika ITSM w rozwiązaniu.

![Ekran Analizy dzienników](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Pulpit nawigacyjny zawiera również informacje o stanie łącznika, które mogą służyć jako punkt wyjścia do analizowania wszelkich problemów z połączeniami.

Można również wizualizować zdarzenia zsynchronizowane z komputerami, których dotyczy problem, w rozwiązaniu Mapy usług.

Mapa usługi automatycznie odnajduje składniki aplikacji w systemach Windows i Linux i mapuje komunikację między usługami. Umożliwia ona wyświetlenie Twoich serwerów tak, jak o nich myślisz — jako wzajemnie połączonych systemów dostarczających krytycznych usług. Usługa Service Map pokazuje połączenia między serwerami, procesami i portami w dowolnej architekturze połączeń TCP bez konieczności konfiguracji innej niż instalacja agenta. [Dowiedz się więcej](../../azure-monitor/insights/service-map.md).

Jeśli używasz rozwiązania Mapy usług, możesz wyświetlić elementy punktu obsługi utworzone w rozwiązaniach ITSM, jak pokazano w poniższym przykładzie:

![Ekran Analizy dzienników](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Więcej informacji: [Mapa usług](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>Dodatkowe informacje

### <a name="data-synced-from-itsm-product"></a>Dane zsynchronizowane z produktu ITSM
Zdarzenia i żądania zmian są synchronizowane z produktu ITSM do obszaru roboczego usługi Log Analytics na podstawie konfiguracji połączenia.

Poniżej przedstawiono przykłady danych zebranych przez ITSMC:

> [!NOTE]
>
> W zależności od typu elementu pracy zaimportowanego do usługi Log Analytics **ServiceDesk_CL** zawiera następujące pola:

**Element roboczy:** **Incydenty**  
ServiceDeskWorkItemType_s="Incydent"

**Pola**

- Nazwa usługi ServiceDeskConnection
- Identyfikator punktu serwisowego
- Stan
- Pilność
- Wpływ
- Priorytet
- Eskalacja
- Utworzony przez
- Rozwiązany przez
- Zamknięte przez
- Element źródłowy
- Przypisany do
- Kategoria
- Tytuł
- Opis
- Data utworzenia
- Data zamknięcia
- Data rozwiązania
- Data ostatniej modyfikacji
- Computer (Komputer)


**Element pracy:** **Żądania zmian**

ServiceDeskWorkItemType_s="ChangeRequest"

**Pola**
- Nazwa usługi ServiceDeskConnection
- Identyfikator punktu serwisowego
- Utworzony przez
- Zamknięte przez
- Element źródłowy
- Przypisany do
- Tytuł
- Typ
- Kategoria
- Stan
- Eskalacja
- Stan konfliktu
- Pilność
- Priorytet
- Ryzyko
- Wpływ
- Przypisany do
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

## <a name="output-data-for-a-servicenow-incident"></a>Dane wyjściowe dla incydentu ServiceNow

| Pole Usługa Log Analytics | ServiceNow, w tym celu |
|:--- |:--- |
| ServiceDeskId_s| Liczba |
| IncidentState_s | Stan |
| Urgency_s |Pilność |
| Impact_s |Wpływ|
| Priority_s | Priorytet |
| CreatedBy_s | Otwarty przez |
| ResolvedBy_s | Rozwiązany przez|
| ClosedBy_s  | Zamknięte przez |
| Source_s| Typ kontaktu |
| AssignedTo_s | Przypisano do  |
| Category_s | Kategoria |
| Title_s|  Krótki opis |
| Description_s|  Uwagi |
| CreatedDate_t|  Otwarte |
| ClosedDate_t| zamknięte|
| ResolvedDate_t|Resolved|
| Computer (Komputer)  | Pozycja konfiguracji |

## <a name="output-data-for-a-servicenow-change-request"></a>Dane wyjściowe dla żądania zmiany usługi ServiceNow

| Log Analytics | ServiceNow, w tym celu |
|:--- |:--- |
| ServiceDeskId_s| Liczba |
| CreatedBy_s | Na wniosek |
| ClosedBy_s | Zamknięte przez |
| AssignedTo_s | Przypisano do  |
| Title_s|  Krótki opis |
| Type_s|  Typ |
| Category_s|  Kategoria |
| CRState_s|  Stan|
| Urgency_s|  Pilność |
| Priority_s| Priorytet|
| Risk_s| Ryzyko|
| Impact_s| Wpływ|
| RequestedDate_t  | Wymagane według daty |
| ClosedDate_t | Data zamknięcia |
| PlannedStartDate_t  |     Planowana data rozpoczęcia |
| PlannedEndDate_t  |   Planowana data zakończenia |
| WorkStartDate_t  | Rzeczywista data rozpoczęcia |
| WorkEndDate_t | Rzeczywista data zakończenia|
| Description_s | Opis |
| Computer (Komputer)  | Element konfiguracji |


## <a name="troubleshoot-itsm-connections"></a>Rozwiązywanie problemów z połączeniami ITSM
1. Jeśli połączenie nie powiedzie się z interfejsu użytkownika połączonego źródła **z komunikatem o błędzie podczas zapisywania połączenia,** należy wykonać następujące kroki:
   - W przypadku połączeń ServiceNow, Cherwell i Provance  
   - upewnij się, że poprawnie wprowadzono nazwę użytkownika, hasło, identyfikator klienta i klucz tajny klienta dla każdego z połączeń.  
   - sprawdź, czy masz wystarczające uprawnienia w odpowiednim produkcie ITSM, aby nawiązać połączenie.  
   - W przypadku połączeń z menedżerem serwisowym  
   - upewnij się, że aplikacja sieci Web została pomyślnie wdrożona i zostanie utworzone połączenie hybrydowe. Aby sprawdzić, czy połączenie zostanie pomyślnie nawiązane za pomocą lokalnego komputera programu Service Manager, odwiedź adres URL aplikacji sieci Web, jak opisano w dokumentacji umożliwiającej nawiązanie [połączenia hybrydowego.](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection)  

2. Jeśli dane z ServiceNow nie jest coraz zsynchronizowane z usługi Log Analytics, upewnij się, że ServiceNow wystąpienie nie jest w stanie uśpienia. Wystąpienia deweloperów ServiceNow czasami przechodzą w tryb uśpienia, gdy są bezczynne przez długi czas. W przeciwnym razie zgłoś problem.
3. Jeśli alerty usługi Log Analytics są uruchamiane, ale elementy robocze nie są tworzone w produktach LUB elementach konfiguracji ITSM nie są tworzone/połączone z elementami roboczymi lub innymi ogólnymi informacjami, poszukaj w następujących miejscach:
   -  ITSMC: Rozwiązanie pokazuje podsumowanie połączeń / elementów roboczych / komputerów itp. Kliknij kafelek z wyświetlenym **stanem łącznika**, który umożliwia **wyświetlenie funkcji Wyszukiwanie dzienników** z odpowiednią kwerendą. Aby uzyskać więcej informacji, przyjrzyj się rekordom dziennika z LogType_S jako BŁĄD.
   - **Strona wyszukiwania dziennika:** wyświetl błędy/powiązane informacje bezpośrednio `*`za pomocą`*`ServiceDeskLog_CL kwerendy .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Rozwiązywanie problemów z wdrażaniem aplikacji sieci Web programu Service Manager
1.  W przypadku jakichkolwiek problemów z wdrażaniem aplikacji sieci web upewnij się, że masz wystarczające uprawnienia w subskrypcji wymienione do tworzenia/wdrażania zasobów.
2.  Jeśli podczas uruchamiania [skryptu](itsmc-service-manager-script.md)zostanie wyświetlony błąd **"Odwołanie do obiektu nie jest ustawione na wystąpienie obiektu",** upewnij się, że wprowadzono prawidłowe wartości w sekcji **Konfiguracja użytkownika.**
3.  Jeśli nie uda się utworzyć obszaru nazw przekaźnika magistrali usług, upewnij się, że dostawca wymaganych zasobów jest zarejestrowany w subskrypcji. Jeśli nie jest zarejestrowany, ręcznie utwórz obszar nazw przekaźnika magistrali usług z witryny Azure portal. Można również utworzyć go podczas [tworzenia połączenia hybrydowego](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection) z witryny Azure portal.


## <a name="contact-us"></a>Skontaktuj się z nami

W przypadku jakichkolwiek pytań lub opinii dotyczących łącznika zarządzania usługami IT skontaktuj się z nami pod adresem [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Następne kroki
[Dodaj produkty/usługi ITSM do łącznika zarządzania usługami IT](../../azure-monitor/platform/itsmc-connections.md).
