---
title: Łączenie się z dynamics 365
description: Tworzenie rekordów i zarządzanie nimi za pomocą interfejsów API REST Dynamics 365 (online) i aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: 9837b68fbfba783a468712d8ba1883b198af4954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789890"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Zarządzanie rekordami usługi Dynamics 365 za pomocą usługi Azure Logic Apps

Dzięki usłudze Azure Logic Apps i łącznikowi usługi Dynamics 365 możesz tworzyć automatyczne zadania i przepływy pracy na podstawie rekordów w usłudze Dynamics 365. Przepływy pracy mogą w ramach konta Dynamics 365 tworzyć rekordy, aktualizować elementy, zwracać rekordy i wykonywać wiele innych akcji. W aplikacjach logiki można uwzględnić akcje, które otrzymują odpowiedzi z dynamics 365 i udostępniają dane wyjściowe dla innych akcji. Na przykład po zaktualizowaniu elementu w dynamics 365 można wysłać wiadomość e-mail przy użyciu usługi Office 365.

W tym artykule pokazano, jak można utworzyć aplikację logiki, która tworzy zadanie w Dynamics 365, gdy nowy rekord potencjalnego klienta jest tworzony w Dynamics 365.
Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Konto Dynamics 365](https://dynamics.microsoft.com)

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta Dynamics 365. Aby uruchomić aplikację logiki za pomocą wyzwalacza Dynamics 365, potrzebna jest [pusta aplikacja logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-dynamics-365-trigger"></a>Dodaj wyzwalacz Dynamics 365

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Najpierw dodaj wyzwalacz Dynamics 365, który jest uruchamiany, gdy w dynamics 365 pojawi się nowy rekord potencjalnego klienta.

1. W [witrynie Azure portal](https://portal.azure.com)otwórz pustą aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. W polu wyszukiwania wpisz "Dynamics 365" jako filtr. W tym przykładzie na liście wyzwalaczy wybierz ten wyzwalacz: **Po utworzeniu rekordu**

   ![Wybierz wyzwalacz](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Jeśli zostanie wyświetlony monit o zalogowanie się do Dynamics 365, zaloguj się teraz.

1. Podaj następujące szczegóły wyzwalacza:

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa organizacji** | Tak | Nazwa wystąpienia Dynamics 365 organizacji do monitorowania, na przykład "Contoso" |
   | **Nazwa encji** | Tak | Nazwa encji do monitorowania, na przykład "Potencjalni klienci" | 
   | **Częstotliwości** | Tak | Jednostka czasu do użycia z interwałami podczas sprawdzania dostępności aktualizacji związanych z wyzwalaczem |
   | **Interwał** | Tak | Liczba sekund, minut, godzin, dni, tygodni lub miesięcy, które mijają przed następnym sprawdzeniem |
   ||| 

   ![Szczegóły wyzwalania](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Akcja Dodaj dynamics 365

Teraz dodaj akcję Dynamics 365, która tworzy rekord zadania dla nowego rekordu potencjalnego klienta.

1. W obszarze wyzwalacza wybierz pozycję **Nowy krok**.

1. W polu wyszukiwania wpisz "Dynamics 365" jako filtr. Z listy akcji wybierz tę akcję: **Utwórz nowy rekord**

   ![Wybierz akcję](./media/connectors-create-api-crmonline/select-action.png)

1. Podaj następujące szczegóły akcji:

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa organizacji** | Tak | Dynamics 365 wystąpienie, w którym chcesz utworzyć rekord, który nie musi być tym samym wystąpieniem w wyzwalaczu, ale jest "Contoso" w tym przykładzie |
   | **Nazwa encji** | Tak | Encja, w której chcesz utworzyć rekord, na przykład "Zadania" |
   | | |

   ![Szczegóły akcji](./media/connectors-create-api-crmonline/action-details.png)

1. Gdy w akcji pojawi się pole **Temat,** kliknij wewnątrz pola **Temat,** aby wyświetlić dynamiczną listę zawartości. Z tej listy wybierz wartości pól, które mają być uwzględnione w rekordzie zadania skojarzonym z nowym rekordem potencjalnego klienta:

   | Pole | Opis |
   |-------|-------------|
   | **Nazwisko** | Nazwisko z potencjalnego klienta jako głównego kontaktu w rekordzie |
   | **Temat** | Opisowa nazwa potencjalnego klienta w rekordzie |
   | | |

   ![Szczegóły rekordu zadania](./media/connectors-create-api-crmonline/create-record-details.png)

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz** dla aplikacji logiki. 

1. Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   ![Uruchamianie aplikacji logiki](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Teraz utwórz rekord potencjalnego klienta w Dynamics 365, aby wyzwolić przepływ pracy aplikacji logiki.

## <a name="add-filter-or-query"></a>Dodawanie filtru lub kwerendy

Aby określić sposób filtrowania danych w akcji Dynamics 365, wybierz pozycję **Pokaż opcje zaawansowane** w tej akcji. Następnie można dodać filtr lub kolejność według kwerendy.
Na przykład można użyć kwerendy filtrującej, aby uzyskać tylko aktywne konta i zamówić te rekordy według nazwy konta. W przypadku tego zadania wykonaj następujące kroki:

1. W **obszarze Kwerenda filtru**wprowadź tę kwerendę filtru OData:`statuscode eq 1`

2. W obszarze **Kolejność według**po wyświetleniu dynamicznej listy zawartości wybierz pozycję **Nazwa konta**. 

   ![Określanie filtru i kolejności](./media/connectors-create-api-crmonline/advanced-options.png)

Aby uzyskać więcej informacji, zobacz te opcje kwerend interfejsu API interfejsu API klienta Dynamics 365:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Najlepsze rozwiązania dotyczące opcji zaawansowanych

Po określeniu wartości pola w akcji lub wyzwalaczu typ danych wartości musi być zgodny z typem pola, niezależnie od tego, czy wartość zostanie ręcznie wprowadzona, czy wybrana z dynamicznej listy zawartości.

W tej tabeli opisano niektóre typy pól i wymagane typy danych dla ich wartości.

| Typ pola | Wymagany typ danych | Opis | 
|------------|--------------------|-------------|
| Pola tekstowe | Pojedynczy wiersz tekstu | Te pola wymagają pojedynczego wiersza tekstu lub zawartości dynamicznej, która ma typ tekstu. <p><p>*Przykładowe pola:* **Opis** i **kategoria** | 
| Pola liczb całkowitych | Liczba całkowita | Niektóre pola wymagają zawartości całkowitej lub dynamicznej, która ma typ liczby całkowitej. <p><p>*Przykładowe pola:* **Procent ukończenia** i **Czas trwania** | 
| Pola daty | Data i godzina | Niektóre pola wymagają daty w formacie mm/dd/yyyy lub zawartości dynamicznej, która ma typ daty. <p><p>*Przykładowe pola:* **Utworzone w dniu**, Data **rozpoczęcia,** **Rozpoczęcie rzeczywiste,** **Koniec rzeczywisty**i **Data ukończenia** | 
| Pola wymagające zarówno identyfikatora rekordu, jak i typu odnośnika | Klucz podstawowy | Niektóre pola odwołują się do innego rekordu encji wymagają zarówno identyfikatora rekordu, jak i typu odnośnika. | 
||||

Rozszerzając te typy pól, oto przykładowe pola w wyzwalaczach Dynamics 365 i akcje, które wymagają zarówno identyfikatora rekordu, jak i typu odnośnika. To wymaganie oznacza, że wartości wybrane z listy dynamicznej nie będą działać.

| Pole | Opis |
|-------|-------------|
| **Właściciel** | Musi być prawidłowym identyfikatorem użytkownika lub identyfikatorem rekordu zespołu. |
| **Typ właściciela** | Musi być `systemusers` albo `teams`albo . |
| **Dotyczy** | Musi być prawidłowym identyfikatorem rekordu, takim jak identyfikator konta lub identyfikator rekordu kontaktu. |
| **W odniesieniu do typu** | Musi być typem odnośnika, takim jak `accounts` lub `contacts`. |
| **Klient** | Musi być prawidłowym identyfikatorem rekordu, takim jak identyfikator konta lub identyfikator rekordu kontaktu. |
| **Typ klienta** | Musi być typem odnośnika, takim jak `accounts` lub `contacts`. |
|||

W tym przykładzie akcja o nazwie **Utwórz nowy rekord** tworzy nowy rekord zadania:

![Tworzenie rekordu zadań z identyfikatorami rekordów i typami odnośnych](./media/connectors-create-api-crmonline/create-record-advanced.png)

Ta akcja przypisuje rekord zadania do określonego identyfikatora użytkownika lub identyfikatora rekordu zespołu na podstawie identyfikatora rekordu w polu **Właściciel** i typu odnośnego w polu **Typ właściciela:**

![Identyfikator rekordu właściciela i typ wyszukiwania](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Ta akcja dodaje również rekord konta skojarzony z identyfikatorem rekordu dodanym w polu **Dotyczące** i typ odnośnika w polu **Dotyczące typu:**

![W odniesieniu do identyfikatora rekordu i typu odnośnika](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Znajdź identyfikator rekordu

Aby znaleźć identyfikator rekordu, wykonaj następujące czynności:

1. W dynamics 365 otwórz rekord, taki jak rekord konta.

2. Na pasku narzędzi akcje wybierz jedną z następujących czynności:

   * Wybierz **opcję Wyskakuj**. ![popout rekord](./media/connectors-create-api-crmonline/popout-record.png) 
   * Wybierz **e-mail A LINK,** dzięki czemu można skopiować pełny adres URL do domyślnego programu poczty e-mail.

   Identyfikator rekordu pojawia się w `%7b` adresie URL między znakami kodu i `%7d` kodowania:

   ![Znajdź identyfikator rekordu](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Rozwiązywanie problemów z nieudanymi uruchomieniami

Aby znaleźć i przejrzeć nieudane kroki w aplikacji logiki, można wyświetlić historię uruchamiania aplikacji logiki, stan, dane wejściowe, dane wyjściowe i tak dalej.

1. W witrynie Azure portal w menu głównym aplikacji logiki wybierz pozycję **Przegląd**. W sekcji **Historia przebiegów,** która pokazuje wszystkie stany uruchamiania aplikacji logiki, wybierz nieudane uruchomienie, aby uzyskać więcej informacji.

   ![Stan uruchomienia aplikacji logiki](./media/connectors-create-api-crmonline/run-history.png)

1. Rozwiń krok nie powiodły się, aby wyświetlić więcej szczegółów.

   ![Rozwiń krok nie powiódł się](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Przejrzyj szczegóły kroku, takie jak dane wejściowe i wyjściowe, które mogą pomóc w znalezieniu przyczyny błędu.

   ![Krok nie powiódł się — wejścia i wyjścia](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Aby uzyskać więcej informacji na temat rozwiązywania problemów z aplikacjami logiki, zobacz [Diagnozowanie błędów aplikacji logiki](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem pliku OpenAPI łącznika (dawniej Swagger), zobacz [stronę odwołania łącznika](/connectors/dynamicscrmonline/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
