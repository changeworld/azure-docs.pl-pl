---
title: Wywołania funkcji platformy Azure z Microsoft Flow | Dokumentacja firmy Microsoft
description: Tworzenie niestandardowego łącznika, a następnie wywołaj funkcję za pomocą tego łącznika.
services: functions
keywords: aplikacje w chmurze, cloud services, Microsoft Flow, procesów biznesowych, aplikacja biznesowa
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: 57d80ad836a16b8821ba0cce42c822728c654dfd
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234805"
---
# <a name="call-a-function-from-microsoft-flow"></a>Wywoływanie funkcji z usługi Microsoft Flow

[Microsoft Flow](https://flow.microsoft.com/) można łatwo automatyzować przepływy pracy i procesy biznesowe między ulubionych aplikacji i usług. Professional deweloperzy mogą używać usługi Azure Functions rozszerzyć możliwości Microsoft Flow podczas osłaniania konstruktorów przepływ z szczegółowe informacje techniczne.

Należy utworzyć przepływ w tym temacie oparta na scenariuszu konserwacji dla turbin knie. W tym temacie przedstawiono sposób wywołania funkcji, który został zdefiniowany w [utworzyć definicję OpenAPI dla funkcji](functions-openapi-definition.md). Funkcja określa, czy awaryjnego naprawiania na turbiny knie jest ekonomiczne. Jeśli jest ekonomiczne, przepływ wysyła wiadomość e-mail do zaleca naprawy.

Aby uzyskać informacje na wywoływanie tej samej funkcji z rozwiązania PowerApps, zobacz [wywołać funkcję w rozwiązaniu PowerApps](functions-powerapps-scenario.md).

W tym temacie dowiesz się, jak:

> [!div class="checklist"]
> * Utwórz listę w programie SharePoint.
> * Eksportowanie definicji interfejsu API.
> * Dodaj połączenie do interfejsu API.
> * Utwórz strumień do wysyłania wiadomości e-mail, jeśli naprawa jest ekonomiczne.
> * Uruchamianie przepływu.

## <a name="prerequisites"></a>Wymagania wstępne

+ Aktywny [konta Microsoft Flow](https://flow.microsoft.com/documentation/sign-up-sign-in/) przy użyciu tego samego konta w poświadczeniach jako konto platformy Azure. 
+ Program SharePoint, które zostaną użyte jako źródło danych dla tego przepływu. Zaloguj się do [wersji próbnej usługi Office 365](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1) Jeśli nie masz jeszcze programu SharePoint.
+ Ukończ samouczek [utworzyć definicję OpenAPI dla funkcji](functions-openapi-definition.md).

## <a name="create-a-sharepoint-list"></a>Utwórz listę programu SharePoint
Możesz rozpocząć od tworzenia listy używanej jako źródło danych dla przepływu. Lista zawiera następujące kolumny.

| Kolumna listy     | Typ danych           | Uwagi                                    |
|-----------------|---------------------|------------------------------------------|
| **Tytuł**           | Pojedynczy wiersz tekstu | Nazwa turbiny                      |
| **LastServiceDate** | Date                |                                          |
| **MaxOutput**       | Liczba              | Dane wyjściowe turbiny, w KwH            |
| **ServiceRequired** | Tak/Nie              |                                          |
| **EstimatedEffort** | Liczba              | Szacowany czas do naprawy, w godzinach |

1. W witrynie programu SharePoint, kliknij lub naciśnij polecenie **nowy**, następnie **listy**.

    ![Tworzenie nowej listy programu SharePoint](./media/functions-flow-scenario/new-list.png)

2. Wprowadź nazwę `Turbines`, kliknij lub naciśnij polecenie **Utwórz**.

    ![Określanie nazwy nowej listy](./media/functions-flow-scenario/create-list.png)

    **Turbin** listy jest tworzony przy użyciu domyślnego **tytuł** pola.

    ![Lista turbin](./media/functions-flow-scenario/initial-list.png)

3. Kliknij lub naciśnij polecenie ![ikonę nowego elementu](./media/functions-flow-scenario/icon-new.png) następnie **data**.

    ![Dodawanie pola pojedynczego wiersza tekstu](./media/functions-flow-scenario/add-column.png)

4. Wprowadź nazwę `LastServiceDate`, kliknij lub naciśnij polecenie **Utwórz**.

    ![Utwórz kolumnę LastServiceDate](./media/functions-flow-scenario/date-column.png)

5. Powtórz ostatnie dwa kroki dla trzech kolumn na liście:

    1. **Numer** > "MaxOutput"

    2. **Tak/nie** > "ServiceRequired"

    3. **Numer** > "EstimatedEffort"

To teraz — powinien mieć pustą listę, która wygląda podobnie do poniższej ilustracji. Możesz dodać dane do listy, po utworzeniu przepływu.

![Lista jest pusta](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Dodaj połączenie do interfejsu API
Niestandardowy interfejs API (znanej także jako łącznik niestandardowy) jest dostępna w Flow firmy Microsoft, ale należy utworzyć połączenie z interfejsem API przed użyciem w strumieniu.

1. W [flow.microsoft.com](https://flow.microsoft.com), kliknij koło zębate ikonę (w prawym górnym rogu), a następnie kliknij przycisk **połączenia**.

    ![Połączenia o szybkości przepływu](media/functions-flow-scenario/flow-connections.png)

1. Kliknij przycisk **Utwórz połączenie**, przewiń w dół do **naprawy turbiny** łącznika i kliknij ją.

    ![Tworzenie połączenia](media/functions-flow-scenario/create-connection.png)

1. Wprowadź klucz interfejsu API, a następnie kliknij przycisk **utworzyć połączenie**.

    ![Wprowadź klucz interfejsu API i utworzyć](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> Udostępniane z przepływu, każda osoba, która działa na lub korzysta przepływ należy także podać klucz interfejsu API, aby nawiązać połączenie z interfejsu API. To zachowanie może ulec zmianie w przyszłości, a aby odzwierciedlał, które będą aktualizowane.


## <a name="create-a-flow"></a>Utwórz strumień

Teraz możesz przystąpić do tworzenia przepływu, który używa niestandardowego łącznika i listy programu SharePoint, który został utworzony.

### <a name="add-a-trigger-and-specify-a-condition"></a>Dodaj wyzwalacza i określ warunek

Najpierw utwórz przepływem na podstawie pustego (bez szablonu) i Dodaj *wyzwalacza* generowane, gdy zostanie utworzony element na liście programu SharePoint. Następnie dodaj *warunku* ustalenie co dalej.

1. W [flow.microsoft.com](https://flow.microsoft.com), kliknij przycisk **Moje przepływa**, następnie **Utwórz z puste**.

    ![Utwórz na podstawie puste](media/functions-flow-scenario/create-from-blank.png)

2. Kliknij przycisk wyzwalacza SharePoint **po utworzeniu elementu**.

    ![Wybierz wyzwalacz](media/functions-flow-scenario/choose-trigger.png)

    Jeśli użytkownik nie jest jeszcze zarejestrowany do programu SharePoint, pojawi się monit Aby to zrobić.

3. Dla **adres witryny**, wprowadź nazwę witryny programu SharePoint i **Nazwa listy**, wprowadź listy, która zawiera dane turbiny.

    ![Wybierz wyzwalacz](media/functions-flow-scenario/site-list.png)

4. Kliknij przycisk **nowy krok**, następnie **Dodaj warunek**.

    ![Dodaj warunek](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow dodaje dwie gałęzie z przepływem: **tak** i **Jeśli żadne**. Kroki należy dodać do jednej lub obu gałęzi, po zdefiniowaniu warunek, który ma zostać znaleziona.

    ![Warunek gałęzi](media/functions-flow-scenario/condition-branches.png)

5. Na **warunku** kart, kliknij pierwsze pole, a następnie wybierz **ServiceRequired** z **zawartości dynamicznej** okno dialogowe.

    ![Wybierz pola dla warunku](media/functions-flow-scenario/condition1-field.png)

6. Wprowadź wartość `True` dla warunku.

    ![Wprowadź wartość PRAWDA dla warunku](media/functions-flow-scenario/condition1-yes.png)

    Wartość jest wyświetlana jako `Yes` lub `No` w programie SharePoint lista, lecz jest przechowywana jako *logiczna*, albo `True` lub `False`. 

7. Kliknij przycisk **utworzyć przepływ** w górnej części strony. Należy kliknąć przycisk **przebiegu aktualizacji** okresowo.

Elementy utworzone na liście, aby uzyskać przepływ sprawdza, czy **ServiceRequired** pole jest ustawione na `Yes`, przejdzie do **tak** gałęzi lub **Jeśli żadne** gałęzi jako odpowiednie. Aby zaoszczędzić czas, w tym temacie, można określić tylko akcje **tak** gałęzi.

### <a name="add-the-custom-connector"></a>Dodawanie łącznika niestandardowego

Teraz Dodaj niestandardowy łącznik, który wywołuje funkcję na platformie Azure. Dodanie łącznika niestandardowego przepływu, podobnie jak standardowy łącznika. 

1. W **tak** gałęzi, kliknij przycisk **Dodaj akcję**.

    ![Dodawanie akcji](media/functions-flow-scenario/condition1-yes-add-action.png)

2. W **wybierz akcję** okno dialogowe, wyszukaj `Turbine Repair`, wybierz akcję **naprawy turbiny — oblicza koszty**.

    ![Wybierz akcję](media/functions-flow-scenario/choose-turbine-repair.png)

    Na poniższej ilustracji przedstawiono karty, który zostanie dodany do przepływu. Opisy i pola pochodzą z definicji OpenAPI dla łącznika.

    ![Oblicza domyślne kosztów](media/functions-flow-scenario/calculates-costs-default.png)

3. Na **oblicza koszty** kart, użyj **zawartości dynamicznej** okno dialogowe, aby wybrać dane wejściowe dla funkcji. Microsoft Flow pokazuje pól liczbowych, ale nie pole daty, ponieważ definicja OpenAPI Określa, że **godziny** i **pojemności** są liczbowych.

    Dla **godziny**, wybierz pozycję **EstimatedEffort**oraz **pojemności**, wybierz pozycję **MaxOutput**.

    ![Wybierz akcję](media/functions-flow-scenario/calculates-costs-fields.png)

     Teraz możesz dodać inny warunek, w oparciu o dane wyjściowe funkcji.

4. W dolnej części **tak** gałęzi, kliknij przycisk **więcej**, następnie **Dodaj warunek**.

    ![Dodaj warunek](media/functions-flow-scenario/condition2-add.png)

5. Na **2 warunku** kart, kliknij pierwsze pole, a następnie wybierz **komunikat** z **zawartości dynamicznej** okno dialogowe.

    ![Wybierz pola dla warunku](media/functions-flow-scenario/condition2-field.png)

6. Wprowadź wartość `Yes`. Przepływ przechodzi do następnego **tak** gałęzi lub **Jeśli żadne** gałęzi zależnie od tego, czy komunikat zwrócony przez funkcję ma tak (utworzyć naprawy) lub nie (nie należy naprawy). 

    ![Wprowadź tak dla warunku](media/functions-flow-scenario/condition2-yes.png)

Przepływ powinna wyglądać podobnie jak na poniższej ilustracji.

![Wprowadź tak dla warunku](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>Wyślij wiadomość e-mail na podstawie wyników — funkcja

W tym momencie przepływu, wartość zwrócona przez funkcję ma **komunikat** wartość `Yes` lub `No` z funkcji, jak również inne informacje dotyczące przychodów kosztów i możliwości. W **tak** gałęzi drugi warunek spowoduje wysłanie wiadomości e-mail, ale może wykonać dowolną liczbę czynności, takie jak zapisywanie powrót do listy programu SharePoint i uruchamiania [procesu zatwierdzania](https://flow.microsoft.com/documentation/modern-approvals/).

1. W **tak** gałęzi drugi warunek, kliknij przycisk **Dodaj akcję**.

    ![Dodawanie akcji](media/functions-flow-scenario/condition2-yes-add-action.png)

2. W **wybierz akcję** okno dialogowe, wyszukaj `email`, następnie wybierz akcją wysyłania wiadomości e-mail z systemem poczty e-mail użycia (w tym przypadku Outlook).

    ![Outlook, Wyślij wiadomość e-mail](media/functions-flow-scenario/outlook-send-email.png)

3. Na **wysłać wiadomość e-mail** kart, napisz wiadomość e-mail. Wprowadź prawidłową nazwę w swojej organizacji w celu **do** pola. Na poniższym obrazie przedstawiono pozostałe pola są kombinacją tekstu i tokenów z **zawartości dynamicznej** okno dialogowe. 

    ![Pola wiadomości e-mail](media/functions-flow-scenario/email-fields.png)

    **Tytuł** token jest dostarczany z listy programu SharePoint i **CostToFix** i **RevenueOpportunity** są zwracane przez funkcję.

    Ukończone przepływu powinno wyglądać podobnie do poniższej ilustracji (możemy pozostać poza pierwszy **Jeśli żadne** gałąź, aby zaoszczędzić miejsce).

    ![Pełnego przepływu](media/functions-flow-scenario/complete-flow.png)

4. Kliknij przycisk **przebiegu aktualizacji** w górnej części strony, następnie kliknij przycisk **gotowe**.

## <a name="run-the-flow"></a>Uruchamianie przepływu

Teraz, gdy strumień jest zakończone, Dodaj wiersz na liście programu SharePoint i zobacz, jak reaguje przepływu.

1. Wróć do listy programu SharePoint, a następnie kliknij przycisk **Edytuj szybkie**.

    ![Szybkie edycji](media/functions-flow-scenario/quick-edit.png)

2. Wprowadź następujące wartości w siatce edycji.

    | Kolumna listy     | Wartość           |
    |-----------------|---------------------|
    | **Tytuł**           | Turbiny 60 |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | Yes |
    | **EstimatedEffort** | 10 |

3. Kliknij przycisk **Gotowe**.

    ![Szybka edycja — gotowe](media/functions-flow-scenario/quick-edit-done.png)

    Po dodaniu elementu wyzwala przepływ, który można spojrzeć na dalej.

4. W [flow.microsoft.com](https://flow.microsoft.com), kliknij przycisk **Moje przepływa**, następnie kliknij przycisk przepływ został utworzony.

    ![Moje przepływów](media/functions-flow-scenario/my-flows.png)

5. W obszarze **Uruchom HISTORII**, kliknij przycisk Uruchom przepływ.

    ![Historia uruchamiania](media/functions-flow-scenario/run-history.png)

    Jeśli uruchomienie zakończyło się pomyślnie, można przejrzeć operacji przepływu na następnej stronie. Jeśli uruchomienie nie powiodło się dla jakiejkolwiek przyczyny, Następna strona zawiera informacje dotyczące rozwiązywania problemów.

6. Rozwiń kart, aby zobaczyć, jakie wystąpiły podczas przepływu. Na przykład rozwiń folder **oblicza koszty** karty, aby zobaczyć dane wejściowe, aby i dane wyjściowe z funkcji. 

    ![Oblicza koszty wejścia i wyjścia](media/functions-flow-scenario/calculates-costs-outputs.png)

7. Sprawdź konto e-mail osoby określone w **do** pole **wysłać wiadomość e-mail** karty. Wiadomości e-mail wysyłanych z przepływem powinien wyglądać poniższej ilustracji.

    ![Przepływu poczty e-mail](media/functions-flow-scenario/flow-email.png)

    Widać, jak zostały zastąpione tokeny przy użyciu prawidłowych wartości z listy programu SharePoint i funkcji.

## <a name="next-steps"></a>Kolejne kroki
W tym temacie przedstawiono sposób:

> [!div class="checklist"]
> * Utwórz listę w programie SharePoint.
> * Eksportowanie definicji interfejsu API.
> * Dodaj połączenie do interfejsu API.
> * Utwórz strumień do wysyłania wiadomości e-mail, jeśli naprawa jest ekonomiczne.
> * Uruchamianie przepływu.

Aby dowiedzieć się więcej na temat Flow firmy Microsoft, zobacz [wprowadzenie Microsoft Flow](https://flow.microsoft.com/documentation/getting-started/).

Aby dowiedzieć się więcej o innych interesujące scenariuszy korzystających z usługi Azure Functions, zobacz [wywołać funkcję w rozwiązaniu PowerApps](functions-powerapps-scenario.md) i [tworzy funkcję, która integruje się z usługą Azure Logic Apps](functions-twitter-email.md).