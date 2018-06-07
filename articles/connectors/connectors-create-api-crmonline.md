---
title: Nawiązać Dynamics 365 - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie i zarządzanie nimi rekordy z interfejsów API REST (online) Dynamics 365 i Azure Logic Apps
author: Mattp123
manager: cfowler
ms.author: matp
ms.date: 02/10/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 015f587257603330eab9840edf661ba9675f4ba8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34609899"
---
# <a name="connect-to-dynamics-365-from-logic-app-workflows"></a>Nawiązywanie połączenia z przepływów pracy aplikacji logiki do Dynamics 365

Dzięki aplikacjom logiki można łączenie z 365 Dynamics (online) i tworzenie przepływów firm przydatne tworzenie rekordów, aktualizowania elementów lub powrócić do listy rekordów. Za pomocą łącznika programu Dynamics 365 można:

* Tworzenie sieci przepływu biznesowe na podstawie danych otrzymywanych z 365 Dynamics (online).
* Użyj działań, które odpowiedzi, a następnie udostępnić dane wyjściowe do innych działań. Na przykład po zaktualizowaniu elementu w 365 Dynamics (online), możesz wysłać wiadomość e-mail przy użyciu usługi Office 365.

W tym temacie przedstawiono sposób tworzenia aplikacji logiki, która tworzy zadanie w Dynamics 365 zawsze, gdy nowy realizacji jest tworzony w Dynamics 365.

## <a name="prerequisites"></a>Wymagania wstępne
* Konto platformy Azure.
* Dynamics 365 konta (online).

## <a name="create-a-task-when-a-new-lead-is-created-in-dynamics-365"></a>Utwórz zadania, podczas tworzenia nowego potencjalnego klienta w Dynamics 365

1.  [Logowanie do platformy Azure](https://portal.azure.com).

2.  W polu wyszukiwania Azure wpisz `Logic apps`, i naciśnij klawisz ENTER.

      ![Znajdź Logic Apps](./media/connectors-create-api-crmonline/find-logic-apps.png)

3.  W obszarze **Logic apps**, kliknij przycisk **Dodaj**.

      ![Dodaj LogicApp](./media/connectors-create-api-crmonline/add-logic-app.png)

4.  Aby utworzyć aplikację logiki, wykonaj **nazwa**, **subskrypcji**, **grupy zasobów**, i **lokalizacji** pola, a następnie kliknij przycisk **Utwórz**.

5.  Wybierz nową aplikację logiki. Po otrzymaniu **Zakończono pomyślnie wdrażanie** powiadomień, kliknij przycisk **Odśwież**.

6.  W obszarze **narzędzi programistycznych**, kliknij przycisk **projektanta aplikacji logiki**. Na liście szablonów, kliknij przycisk **pustą aplikację logiki**.

7.  W polu wyszukiwania wpisz `Dynamics 365`. Wybierz z listy wyzwalaczy Dynamics 365 **365 Dynamics — po utworzeniu rekordu**.

8.  Jeśli zostanie wyświetlony monit, aby zalogować się do usługi Dynamics 365, należy to zrobić teraz.

9.  W szczegółach wyzwalacza wprowadź następujące informacje:

  * **Nazwa organizacji**. Wybierz wystąpienie Dynamics 365 interesujące aplikacji logiki do nasłuchiwania.

  * **Nazwa jednostki**. Wybierz jednostki, która ma być nasłuchiwania. To zdarzenie działa jako wyzwalacz, aby uruchomić aplikację logiki. 
  W tym przewodniku **prowadzi** jest zaznaczone.

  * **Jak często chcesz sprawdzić dla elementów?** Te wartości ustawić częstotliwość sprawdzania aktualizacji związanych z wyzwalaczem aplikacji logiki. Ustawieniem domyślnym jest sprawdzanie aktualizacji co trzy minuty.

    * **Częstotliwość**. Wybierz sekundy, minuty, godziny lub dni.

    * **Interwał**. Wprowadź liczbę sekund, minuty, godziny lub dni, które mają być przekazywane przed następnym wyboru.

      ![Szczegóły wyzwalaczem aplikacji logiki](./media/connectors-create-api-crmonline/trigger-details.png)

10. Kliknij przycisk **nowy krok**, a następnie kliknij przycisk **Dodaj akcję**.

11. W polu wyszukiwania wpisz `Dynamics 365`. Wybierz z listy akcji **Dynamics 365 — Utwórz nowy rekord**.

12. Wprowadź następujące informacje:

    * **Nazwa organizacji**. Wybierz wystąpienie Dynamics 365 miejscu przepływu, aby utworzyć rekord. 
    Należy zauważyć, że nie musi być tym samym wystąpieniem, gdy zdarzenie zostanie wyzwolone z tego wystąpienia.

    * **Nazwa jednostki**. Wybierz obiekt, który chcesz utworzyć rekord, gdy zdarzenie zostanie wyzwolone. 
    W tym przewodniku **zadania** jest zaznaczone.

13. Kliknij **podmiotu** pojawi się okno. Z dynamicznej zawartości na wyświetlonej liście można wybrać jedną z tych pól:

    * **Nazwisko**. Zaznaczenie tego pola Wstawia nazwisko potencjalnego klienta do pole tematu dla zadania, gdy zostaje utworzony rekord zadania.
    * **Temat**. Zaznaczenie tego pola Wstawia pole temat potencjalnego klienta do pole tematu dla zadania, gdy zostaje utworzony rekord zadania. 
    Kliknij przycisk **tematu** dodać go do **podmiotu** pole.

      ![Logiki aplikacji Utwórz nowe szczegóły rekordu](./media/connectors-create-api-crmonline/create-record-details.png)

14. Na pasku narzędzi Projektanta aplikacji logiki, kliknij przycisk **zapisać**.

    ![Narzędzi Projektanta aplikacji logiki Zapisz](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15. Aby uruchomić aplikację logiki, kliknij przycisk **Uruchom**.

    ![Narzędzi Projektanta aplikacji logiki Zapisz](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. Teraz Utwórz rekord realizacji w 365 Dynamics działu sprzedaży i zobacz Twojej przepływu w akcji!

## <a name="set-advanced-options-for-a-logic-app-step"></a>Ustaw opcje zaawansowane kroku aplikacji logiki

Aby określić sposób filtrowania danych w kroku aplikacji logiki, kliknij przycisk **Pokaż zaawansowane opcje** w tym kroku, następnie dodać filtr lub kolejności przez zapytanie.

Na przykład można użyć zapytania filtra można uzyskać tylko aktywne konta i kolejność według nazwy konta. Aby wykonać to zadanie, wprowadź zapytanie filtru OData `statuscode eq 1`i wybierz **nazwa konta** na liście zawartości dynamicznej. Więcej informacji: [MSDN: $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) i [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2).

![Zaawansowane opcje aplikacji logiki](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>Najlepsze rozwiązania przy użyciu opcji zaawansowanych

Po dodaniu wartości do pola musi odpowiadać typowi pola, wpisz wartość lub wybierz wartość z listy zawartości dynamicznej.

Typ pola  |Jak stosować  |Gdzie można znaleźć  |Name (Nazwa)  |Typ danych  
---------|---------|---------|---------|---------
Pola tekstowe|Pola tekstowe wymagają pojedynczy wiersz tekstu lub zawartość dynamiczną, która jest polem typu tekst. Przykładami pola kategorii i podkategorii.|Ustawienia > dostosowania > dostosowywania systemu > jednostki > zadań > pól |category |Pojedynczy wiersz tekstu        
Pola Liczba całkowita | Niektóre pola wymagają liczbą całkowitą lub zawartość dynamiczną, która jest typu Integer. Przykładami procent wykonania i czasu trwania. |Ustawienia > dostosowania > dostosowywania systemu > jednostki > zadań > pól |ProcentWykonania |Liczby całkowitej         
Pola dat | Niektóre pola wymagają datę wprowadzenia w formacie mm/dd/rrrr lub zawartość dynamiczną, która jest pola typu daty. Przykładami utworzenia, datę rozpoczęcia, Rozpoczęcie rzeczywiste ostatnio na czas przechowywania, rzeczywiste zakończenie i terminu. | Ustawienia > dostosowania > dostosowywania systemu > jednostki > zadań > pól |pól createdon |Data i godzina
Typ pola, które wymagają zarówno Identyfikatora rekordu i wyszukiwania |Niektóre pola, które odwołują się do innego rekordu jednostki wymagają zarówno identyfikator rekordu, jak i typ wyszukiwania. |Ustawienia > dostosowania > dostosowywania systemu > jednostki > konto > pól  | AccountID  | Klucz podstawowy

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>Wpisz więcej przykładów dotyczących pola, które wymagają identyfikator rekordu i wyszukiwania
Rozszerzanie w poprzedniej tabeli, Oto przykłady więcej pól, które nie działają z wartościami wybrany z listy zawartości dynamicznej. Zamiast tego te pola wymagają zarówno identyfikator i wyszukiwania typ rekordu wprowadzany do pól w rozwiązaniu PowerApps.  
* Właściciel i typ właściciela. Pole właściciela musi zawierać prawidłowy identyfikator rekordu użytkownika lub zespołu Typ właściciela musi być albo **systemusers** lub **zespołów**.
* Klienta i typ klienta. Pole klienta musi być prawidłowym kontem lub skontaktuj się z identyfikator rekordu. Typ właściciela musi być albo **kont** lub **kontaktów**.
* Dotyczące oraz dotyczące typu. Pole dotyczy musi być prawidłowy rekord identyfikator, takie jak konto lub skontaktuj się z identyfikator rekordu. Typ dotyczące musi być typ wyszukiwania dla rekordu, takich jak **kont** lub **kontaktów**.

W poniższym przykładzie akcji zadania tworzenia dodaje rekord konta, która odpowiada identyfikatorowi rekordu dodanie go do pola dotyczące zadania.

![Konto przepływu recordId i typ](./media/connectors-create-api-crmonline/recordid-type-account.png)

W tym przykładzie przypisuje zadania do określonego użytkownika na podstawie identyfikatora użytkownika rekordu.

![Konto przepływu recordId i typ](./media/connectors-create-api-crmonline/recordid-type-user.png)

Aby znaleźć identyfikator rekordu, zobacz następującą sekcję: *Znajdź identyfikator rekordu*

## <a name="find-the-record-id"></a>Znajdź identyfikator rekordu

1. Otwórz rekord, takich jak konta.

2. Kliknij na pasku narzędzi Akcje **Pop limit** ![rekordu podręczne](./media/connectors-create-api-crmonline/popout-record.png).
Można również na pasku narzędzi Akcje, aby skopiować pełny adres URL do domyślnego programu poczty e-mail, kliknij przycisk **łącze E-mail**.

   Identyfikator rekordu jest wyświetlany Between d 7b i %7% kodowania znaków adresu URL.

   ![Konto przepływu recordId i typ](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Aby rozwiązać krok zakończony niepowodzeniem w aplikacji logiki, Wyświetl szczegóły stanu zdarzenia.

1. W obszarze **Logic Apps**, wybierz aplikację logiki, a następnie kliknij przycisk **omówienie**. 

   Obszar Podsumowanie zawiera stan wykonywania dla aplikacji logiki, jest wyświetlany. 

   ![Stan uruchomienia aplikacji logiki](./media/connectors-create-api-crmonline/tshoot1.png)

2. Aby wyświetlić więcej informacji na temat dowolnego uruchamia nie powiodło się, kliknij zdarzenie nie powiodło się. Aby rozszerzyć krok zakończony niepowodzeniem, kliknij ten krok.

   ![Rozwiń węzeł krok zakończony niepowodzeniem](./media/connectors-create-api-crmonline/tshoot2.png)

   Szczegółowe informacje krok wyświetlane i mogą pomóc rozwiązać przyczynę niepowodzenia.

   ![Nie można uzyskać szczegółowe informacje krok](./media/connectors-create-api-crmonline/tshoot3.png)

Aby uzyskać więcej informacji na temat rozwiązywania problemów z aplikacji logiki, zobacz [diagnozowanie błędów aplikacji logiki](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger i zobacz też żadnych limitów w [szczegóły łącznika](/connectors/crm/). 

## <a name="next-steps"></a>Kolejne kroki
Eksploruj dostępnych łączników w aplikacjach logiki w naszym [listy interfejsów API](apis-list.md).
