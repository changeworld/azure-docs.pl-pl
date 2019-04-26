---
title: Nawiązać połączenie z Dynamics 365 — Azure Logic Apps
description: Tworzenie i zarządzanie nimi przy użyciu interfejsów API REST (online) Dynamics 365 i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: b81efba0ce860bea5fd68dd99ce52980e6816b7e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60313750"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Zarządzanie rekordami Dynamics 365 z usługą Azure Logic Apps

Korzystając z usługi Azure Logic Apps i łącznika usługi Dynamics 365 możesz utworzyć zautomatyzowanym zadaniom i przepływów pracy opartych na rekordy w Dynamics 365. Przepływy pracy można utworzyć rekordów, aktualizacja elementów, zwracane rekordy i inne w ramach konta Dynamics 365. Możesz dołączyć akcje w aplikacjach logiki, które uzyskać odpowiedzi od Dynamics 365 i udostępnić dane wyjściowe do innych działań. Na przykład gdy element zostanie zaktualizowany w Dynamics 365, możesz wysłać wiadomość e-mail przy użyciu usługi Office 365.

Ten artykuł pokazuje, jak tworzyć aplikację logiki, która tworzy zadanie w Dynamics 365, gdy nowy rekord potencjalny klient zostanie utworzony w Dynamics 365.
Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* A [konta Dynamics 365](https://dynamics.microsoft.com)

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta usługi Dynamics 365. Aby uruchomić aplikację logiki z wyzwalaczem Dynamics 365, musisz mieć [pusta aplikacja logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-dynamics-365-trigger"></a>Dodawanie wyzwalacza Dynamics 365

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Najpierw dodaj wyzwalacz Dynamics 365, która jest uruchamiana, gdy nowy rekord potencjalny klient, który pojawia się w Dynamics 365.

1. W [witryny Azure portal](https://portal.azure.com), Otwórz swoje pustej aplikacji logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. W polu wyszukiwania wprowadź "Dynamics 365" jako filtr. Na przykład w obszarze listy wyzwalaczy wybierz następujący wyzwalacz: **Gdy rekord zostanie utworzony.**

   ![Wybierz wyzwalacz](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Jeśli zostanie wyświetlony monit, aby zarejestrować się w usłudze Dynamics 365, zarejestruj się teraz.

1. Podaj te szczegóły wyzwalacza:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa organizacji** | Yes | Nazwa wystąpienia Dynamics 365 Twojej organizacji do monitorowania, na przykład "Contoso" |
   | **Nazwa jednostki** | Yes | Nazwa jednostkę którą chcesz monitorować, na przykład "prowadzi" | 
   | **Częstotliwość** | Yes | Jednostka czasu do użycia z interwałami podczas sprawdzania dostępności aktualizacji związanych z wyzwalacza |
   | **Interwał** | Yes | Liczba sekund, minuty, godziny, dni, tygodni lub miesięcy, które upłynąć przed następnym wyboru |
   ||| 

   ![Szczegóły wyzwalacza](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Dodawanie akcji Dynamics 365

Teraz Dodaj akcję Dynamics 365, która tworzy rekord zadania dla nowego rekordu potencjalnego klienta.

1. W obszarze wyzwalacza, wybierz opcję **nowy krok**.

1. W polu wyszukiwania wprowadź "Dynamics 365" jako filtr. Z listy akcji wybierz następującą akcję: **Utwórz nowy rekord**

   ![Wybierz akcję](./media/connectors-create-api-crmonline/select-action.png)

1. Podaj te szczegóły działań:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa organizacji** | Yes | W tym przykładzie wystąpienia Dynamics 365, w którym chcesz utworzyć rekord, który nie musi to być to samo wystąpienie w wyzwalacza, ale jest "Contoso" |
   | **Nazwa jednostki** | Yes | Jednostki, której chcesz utworzyć rekord, na przykład "Zadania" |
   | | |

   ![Szczegóły akcji](./media/connectors-create-api-crmonline/action-details.png)

1. Gdy **podmiotu** pojawi się okno w akcji, kliknij wewnątrz **podmiotu** polu, aby wyświetlić listę zawartości dynamicznej. Na tej liście wybierz wartości pól, które mają zostać objęte rekord zadania skojarzonego z rekordem nowego potencjalnego klienta:

   | Pole | Opis |
   |-------|-------------|
   | **Nazwisko** | Nazwisko z potencjalnego klienta jako głównej osoby kontaktowej w rekordzie |
   | **Temat** | Opisowa nazwa potencjalnego klienta w rekordzie |
   | | |

   ![Szczegóły rekordu zadania](./media/connectors-create-api-crmonline/create-record-details.png)

1. Na pasku narzędzi Projektanta wybierz **Zapisz** aplikacji logiki. 

1. Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta, wybierz **Uruchom**.

   ![Uruchamianie aplikacji logiki](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Teraz Utwórz nowego potencjalnego klienta w Dynamics 365, dzięki czemu możesz wyzwolić przepływ pracy aplikacji logiki.

## <a name="add-filter-or-query"></a>Dodaj filtr lub zapytania

Aby określić sposób filtrowania danych w ramach akcji Dynamics 365, wybierz **Pokaż opcje zaawansowane** w tym działaniu. Następnie można dodać filtru lub ustalania kolejności przez zapytanie.
Na przykład można użyć zapytania filtru uzyskać tylko aktywne konta i kolejność tych rekordów według nazwy konta. W tym celu wykonaj następujące kroki:

1. W obszarze **zapytanie filtru**, wpisz to zapytanie filtru OData: `statuscode eq 1`

2. W obszarze **Order By**, gdy zostanie wyświetlona lista zawartości dynamicznej, wybierz **nazwa konta**. 

   ![Określ filtru i ustalania kolejności](./media/connectors-create-api-crmonline/advanced-options.png)

Aby uzyskać więcej informacji zobacz te opcje zapytań systemu Dynamics 365 Customer Engagement Web API:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Najlepsze rozwiązania dotyczące opcji zaawansowanych

Po określeniu wartości dla pola w akcję lub wyzwalacz typu danych wartość musi odpowiadać typowi pola, czy ręcznie wprowadź wartość lub wybierz wartość z listy zawartości dynamicznej.

W tej tabeli opisano niektóre typy pól oraz typy danych wymaganych do ich wartości.

| Typ pola | Wymagany typ danych | Opis | 
|------------|--------------------|-------------|
| Pola tekstowe | Pojedynczy wiersz tekstu | Pola te wymagają jednego wiersza tekstu lub zawartości dynamicznej, która ma typ tekstu. <p><p>*Przykład pola*: **Opis** i **kategorii** | 
| Pola liczb całkowitych | Liczba całkowita | Niektóre pola wymagają liczby całkowitej lub zawartości dynamicznej, która ma typ liczby całkowitej. <p><p>*Przykład pola*: **Procent wykonania** i **czas trwania** | 
| Pola daty | Data i godzina | Niektóre pola wymagają daty z formatem mm/dd/rrrr lub zawartości dynamicznej, która ma typ date. <p><p>*Przykład pola*: **Utworzone na**, **Data rozpoczęcia**, **Rzeczywiste rozpoczęcie**, **Rzeczywiste zakończenie**, i **Data ukończenia** | 
| Typ pola wymagające Identyfikatora rekordu i wyszukiwania | Klucz podstawowy | Niektóre pola odwołujące się do innego rekordu jednostki wymagają zarówno Identyfikatora rekordu i typu wyszukiwania. | 
||||

Rozszerzając te typy pól, poniżej przedstawiono przykładowe pola w Dynamics 365 wyzwalacze i akcje, które wymagają Identyfikatora rekordu i typu wyszukiwania. Wymaganie to oznacza, że wartości, które należy wybrać z listy dynamicznej nie będzie działać.

| Pole | Opis |
|-------|-------------|
| **Właściciel** | Musi to być identyfikator prawidłowego użytkownika lub zespołu identyfikator rekordu. |
| **Typ właściciela** | Musi być albo **użytkownicy systemowi** lub **zespoły**. |
| **Dotyczy** | Musi być prawidłowym Identyfikatorem rekordu, np. identyfikator konta, lub skontaktuj się z identyfikator rekordu. |
| **Typ obiektu dotyczy** | Musi być typem wyszukiwania, takie jak **kont** lub **kontakty**. |
| **Odbiorcy** | Musi być prawidłowym Identyfikatorem rekordu, np. identyfikator konta, lub skontaktuj się z identyfikator rekordu. |
| **Typ klienta** | Musi być typem wyszukiwania, takie jak **kont** lub **kontakty**. |
|||

W tym przykładzie akcji o nazwie **Utwórz nowy rekord** tworzy nowy rekord zadania:

![Tworzenie rekordu zadania przy użyciu identyfikatorów rekordów i wyszukiwania typów](./media/connectors-create-api-crmonline/create-record-advanced.png)

Ta akcja przypisuje rekord zadania określony identyfikator użytkownika lub identyfikator rekordu zespołu, w oparciu o identyfikator rekordu w **właściciela** pola lub wyszukiwania wpisz **typ właściciela** pola:

![Wpisz identyfikator rekordu właściciela i wyszukiwania](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Ta akcja spowoduje dodanie rekord konta który jest skojarzony z rekordem Identyfikator dodany w **dotyczące** pola lub wyszukiwania wpisz **typ obiektu dotyczy** pola:

![Wpisz dotyczące Identyfikatora rekordu i wyszukiwania](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Znajdowanie Identyfikatora rekordu

Aby znaleźć identyfikator rekordu, wykonaj następujące kroki:

1. Dynamics 365 otwórz rekord, np. rekord konta.

2. Na pasku narzędzi Akcje wybierz jedną z następujących czynności:

   * Wybierz **w okienku wyskakującym**. ![otwórz rekord](./media/connectors-create-api-crmonline/popout-record.png) 
   * Wybierz **Wyślij LINK pocztą e-mail** tak, możesz skopiować pełny adres URL do domyślnego programu poczty e-mail.

   Rekord, identyfikator, który będzie widoczny w adresie URL między `%7b` i `%7d` kodowanie znaków:

   ![Znajdowanie Identyfikatora rekordu](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Rozwiązywanie problemów z uruchomieniami nie powiodło się

Aby znaleźć i przejrzyj kroki zakończone niepowodzeniem w aplikacji logiki, możesz wyświetlić historię uruchomień aplikacji logiki, stanu, danych wejściowych, danych wyjściowych i tak dalej.

1. W witrynie Azure portal w menu głównym aplikację logiki, wybierz **Przegląd**. W **Historia przebiegów** sekcję, która zawiera wszystkie stany przebiegu aplikacji logiki, wybierz przebieg nie powiodło się, aby uzyskać więcej informacji.

   ![Stan uruchomienia aplikacji logiki](./media/connectors-create-api-crmonline/run-history.png)

1. Rozwiń krok zakończony niepowodzeniem, aby można było wyświetlić więcej szczegółów.

   ![Rozwiń krok zakończony niepowodzeniem](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Szczegółowe informacje na ten krok, takie jak wejść i wyjść, które mogą pomóc ustalić przyczynę za błąd.

   ![Krok zakończony niepowodzeniem — dane wejściowe i wyjściowe](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Aby uzyskać więcej informacji na temat rozwiązywania problemów z aplikacjami logiki, zobacz [diagnozowanie błędów aplikacji logiki](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegóły techniczne, takich jak wyzwalacze, akcje i ograniczeń, zgodnie z opisem w łącznika interfejsu OpenAPI (dawniej Swagger) plików, zobacz [strona referencyjna łącznika](/connectors/dynamicscrmonline/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
