---
title: Wywoływanie funkcji platformy Azure z Microsoft Flow | Dokumentacja firmy Microsoft
description: Tworzenie łącznika niestandardowego, a następnie wywołać funkcję za pomocą tego łącznika.
services: functions
keywords: aplikacje w chmurze, cloud services, Microsoft Flow procesów biznesowych, aplikacji biznesowych
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: d3e777b5611dec382dc4eaaac5ec1594abcdab31
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787688"
---
# <a name="call-a-function-from-microsoft-flow"></a>Wywoływanie funkcji z usługi Microsoft Flow

[Microsoft Flow](https://flow.microsoft.com/) ułatwia Automatyzowanie przepływów pracy i procesów biznesowych między ulubionymi aplikacjami i usługami. Profesjonalni deweloperzy umożliwia rozszerzają możliwości programu Microsoft Flow podczas osłaniania twórcy przepływu z szczegóły techniczne usługi Azure Functions.

Możesz utworzyć przepływ w tym temacie, oparte na scenariuszu konserwacji dla turbiny wiatru. W tym temacie przedstawiono sposób wywołania funkcji, które zostały zdefiniowane w [Tworzenie definicji interfejsu OpenAPI dla funkcji](functions-openapi-definition.md). Funkcja określa, czy naprawa na turbiny wiatru ekonomiczne. Jeśli jest to ekonomiczne, przepływ wysyła wiadomość e-mail, aby zalecić naprawy.

Aby uzyskać informacje na wywoływanie tej samej funkcji z usługi PowerApps, zobacz [wywoływanie funkcji z usługi PowerApps](functions-powerapps-scenario.md).

W tym temacie dowiesz się, jak:

> [!div class="checklist"]
> * Utwórz listę w programie SharePoint.
> * Eksportowanie definicji interfejsu API.
> * Dodaj połączenie z interfejsem API.
> * Utwórz przepływ do wysyłania wiadomości e-mail, jeśli naprawy jest ekonomiczne.
> * Uruchamianie przepływu.

[!INCLUDE [functions-openapi-note](../../includes/functions-openapi-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

+ Aktywne [konta Microsoft Flow](https://flow.microsoft.com/documentation/sign-up-sign-in/) przy użyciu tego samego konta w poświadczeniach jako konta platformy Azure. 
+ Program SharePoint, można użyć jako źródła danych dla tego przepływu. Zaloguj się w celu [wersji próbnej usługi Office 365](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1) Jeśli nie masz jeszcze programu SharePoint.
+ Ukończ samouczek [Tworzenie definicji interfejsu OpenAPI dla funkcji](functions-openapi-definition.md).

## <a name="create-a-sharepoint-list"></a>Utwórz listę programu SharePoint
Należy rozpocząć od utworzenia listy używanej jako źródło danych dla przepływu. Lista zawiera następujące kolumny.

| Kolumna listy     | Typ danych           | Uwagi                                    |
|-----------------|---------------------|------------------------------------------|
| **Tytuł**           | Pojedynczy wiersz tekstu | Nazwa turbiny                      |
| **LastServiceDate** | Date                |                                          |
| **MaxOutput**       | Liczba              | Dane wyjściowe turbiny, w KwH            |
| **ServiceRequired** | Tak/Nie              |                                          |
| **EstimatedEffort** | Liczba              | Szacowany czas naprawy, w godzinach |

1. W witrynie programu SharePoint kliknij lub naciśnij **New**, następnie **listy**.

    ![Tworzenie nowej listy programu SharePoint](./media/functions-flow-scenario/new-list.png)

2. Wprowadź nazwę `Turbines`, a następnie kliknij lub naciśnij pozycję **Utwórz**.

    ![Określanie nazwy nowej listy](./media/functions-flow-scenario/create-list.png)

    **Turbiny** lista jest tworzona przy użyciu domyślnego **tytuł** pola.

    ![Lista turbiny](./media/functions-flow-scenario/initial-list.png)

3. Kliknij lub naciśnij pozycję ![ikonę nowego elementu](./media/functions-flow-scenario/icon-new.png) następnie **data**.

    ![Dodawanie pola pojedynczego wiersza tekstu](./media/functions-flow-scenario/add-column.png)

4. Wprowadź nazwę `LastServiceDate`, a następnie kliknij lub naciśnij pozycję **Utwórz**.

    ![Tworzenie kolumny LastServiceDate](./media/functions-flow-scenario/date-column.png)

5. Powtórz ostatnie dwa kroki dla trzech kolumn na liście:

    1. **Liczba** > "MaxOutput"

    2. **Tak/nie** > "ServiceRequired"

    3. **Liczba** > "EstimatedEffort"

To teraz — powinien mieć pustą listę, która wygląda podobnie do poniższej ilustracji. Możesz dodać dane do listy, po utworzeniu przepływu.

![Jeśli lista jest pusta](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Dodaj połączenie z interfejsem API
Niestandardowy interfejs API (znany także jako łącznik niestandardowy) jest dostępna w Microsoft Flow, ale przed jego użyciem w przepływie, należy ustanowić połączenie z interfejsem API.

1. W [flow.microsoft.com](https://flow.microsoft.com), kliknij ikonę koła zębatego (w prawym górnym rogu), a następnie kliknij przycisk **połączeń**.

    ![Połączeń przepływu](media/functions-flow-scenario/flow-connections.png)

1. Kliknij przycisk **Utwórz połączenie**, przewiń w dół do **naprawy turbiny** łącznika i kliknij ją.

    ![Tworzenie połączenia](media/functions-flow-scenario/create-connection.png)

1. Wprowadź klucz interfejsu API, a następnie kliknij przycisk **utworzyć połączenie**.

    ![Wprowadź klucz interfejsu API i Utwórz](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> Jeśli udostępnisz przepływ z innymi osobami, każdy, kto pracuje lub korzysta z przepływu należy także podać klucz interfejsu API, aby nawiązać połączenie z interfejsu API. To zachowanie może ulec zmianie w przyszłości i będą aktualizowane w tym temacie w celu odzwierciedlenia zmiany.


## <a name="create-a-flow"></a>Tworzenie przepływu

Teraz możesz przystąpić do tworzenia przepływu, który używa łącznika niestandardowego i listy programu SharePoint, który został utworzony.

### <a name="add-a-trigger-and-specify-a-condition"></a>Dodawanie wyzwalacza i określony warunek

Najpierw Utwórz przepływ od podstaw (bez szablonu) i Dodaj *wyzwalacza* generowane, gdy element zostanie utworzony na liście programu SharePoint. Następnie dodaj *warunek* Aby ustalić, co dzieje się potem.

1. W [flow.microsoft.com](https://flow.microsoft.com), kliknij przycisk **moje przepływy**, następnie **Utwórz z pustego**.

    ![Utwórz z pustego](media/functions-flow-scenario/create-from-blank.png)

2. Kliknij pozycję Wyzwól SharePoint **po utworzeniu elementu**.

    ![Wybierz wyzwalacz](media/functions-flow-scenario/choose-trigger.png)

    Jeszcze nie zostało to zrobione w programie SharePoint, zostanie wyświetlony monit zrobić.

3. Aby uzyskać **adres witryny**, wprowadź nazwę witryny programu SharePoint oraz **Nazwa listy**, wprowadź listę, która zawiera dane turbiny.

    ![Wybierz wyzwalacz](media/functions-flow-scenario/site-list.png)

4. Kliknij przycisk **nowy krok**, następnie **Dodaj warunek**.

    ![Dodaj warunek](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow dodaje dwie gałęzie do przepływu: **Jeśli tak** i **Jeśli**. Dodaj kroki do jednego lub obu gałęzi po zdefiniowaniu warunek, który chcesz dopasować.

    ![Warunek gałęzi](media/functions-flow-scenario/condition-branches.png)

5. Na **warunek** karty kliknij pierwsze pole, a następnie wybierz **ServiceRequired** z **zawartości dynamicznej** okno dialogowe.

    ![Wybierz pole dla warunku](media/functions-flow-scenario/condition1-field.png)

6. Wprowadź wartość `True` dla warunku.

    ![Wprowadź wartość PRAWDA dla warunku](media/functions-flow-scenario/condition1-yes.png)

    Wartość jest wyświetlana jako `Yes` lub `No` w programie SharePoint listy, ale są przechowywane jako *logiczna*, albo `True` lub `False`. 

7. Kliknij przycisk **Utwórz przepływ** w górnej części strony. Należy kliknąć przycisk **przebiegu aktualizacji** okresowo.

Dla wszelkich elementów utworzonych w liście, przepływ sprawdza, czy **ServiceRequired** pole jest ustawione na `Yes`, następnie przechodzi do **tak** gałęzi lub **Jeśli** gałęzi jako jest to właściwe. Aby zaoszczędzić czas, w tym temacie, można określić tylko akcje w przypadku **tak** gałęzi.

### <a name="add-the-custom-connector"></a>Dodawanie łącznika niestandardowego

Teraz Dodaj łącznik niestandardowy, który wywołuje funkcję platformy Azure. Dodaj łącznik niestandardowy do przepływu, podobnie jak łącznika standardowego. 

1. W **tak** gałęzi, kliknij przycisk **Dodaj akcję**.

    ![Dodawanie akcji](media/functions-flow-scenario/condition1-yes-add-action.png)

2. W **wybierz akcję** okno dialogowe, wyszukaj `Turbine Repair`, następnie wybierz akcję **naprawy turbiny — oblicza koszty**.

    ![Wybierz akcję](media/functions-flow-scenario/choose-turbine-repair.png)

    Na poniższej ilustracji przedstawiono karty, który jest dodawany do przepływu. Opisy i pola pochodzą z definicji interfejsu OpenAPI dla łącznika.

    ![Oblicza wartości domyślne kosztów](media/functions-flow-scenario/calculates-costs-default.png)

3. Na **oblicza koszty** karty, należy użyć **zawartości dynamicznej** okno dialogowe, aby wybrać dane wejściowe dla funkcji. Microsoft Flow pokazuje pola numeryczne, ale nie pole daty, ponieważ definicja interfejsu OpenAPI Określa, że **godzin** i **pojemności** liczbowe.

    Dla **godzin**, wybierz opcję **EstimatedEffort**oraz **pojemności**, wybierz opcję **MaxOutput**.

    ![Wybierz akcję](media/functions-flow-scenario/calculates-costs-fields.png)

     Teraz możesz dodać inny warunek, w oparciu o dane wyjściowe funkcji.

4. W dolnej części **tak** gałęzi, kliknij przycisk **więcej**, następnie **Dodaj warunek**.

    ![Dodaj warunek](media/functions-flow-scenario/condition2-add.png)

5. Na **warunek 2** karty kliknij pierwsze pole, a następnie wybierz **komunikat** z **zawartości dynamicznej** okno dialogowe.

    ![Wybierz pole dla warunku](media/functions-flow-scenario/condition2-field.png)

6. Wprowadź wartość `Yes`. Przepływ przechodzi do następnego **tak** gałęzi lub **Jeśli** gałęzi na podstawie informacji o tego, czy komunikat zwrócony przez funkcję tak (utworzyć naprawy) lub nie (nie należy naprawy). 

    ![Wprowadzić wartość Tak dla warunku](media/functions-flow-scenario/condition2-yes.png)

Przepływ powinien teraz wyglądać podobnie do następującego.

![Wprowadzić wartość Tak dla warunku](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>Wyślij wiadomość e-mail na podstawie wyników — funkcja

W tym momencie w usłudze flow, wartość zwrócona przez funkcję ma **komunikat** wartość `Yes` lub `No` z funkcji, a także inne informacje dotyczące kosztów i potencjalne przychody. W **tak** gałęzi z drugi warunek, wyśle wiadomość e-mail, ale można wykonać dowolną liczbę elementów, takich jak zapisania zwrotnego do listy programu SharePoint lub uruchamianie [proces zatwierdzania](https://flow.microsoft.com/documentation/modern-approvals/).

1. W **tak** gałęzi drugi warunek, kliknij przycisk **Dodaj akcję**.

    ![Dodawanie akcji](media/functions-flow-scenario/condition2-yes-add-action.png)

2. W **wybierz akcję** okno dialogowe, wyszukaj `email`, następnie Wybieranie akcji Wyślij wiadomość e-mail, oparty na systemie poczty e-mail, użyj (w tym przypadku Outlook).

    ![Program Outlook, Wyślij wiadomość e-mail](media/functions-flow-scenario/outlook-send-email.png)

3. Na **Wyślij wiadomość e-mail** kart, redagowania wiadomości e-mail. Wprowadź prawidłową nazwę w swojej organizacji w celu **do** pola. Na poniższej ilustracji widać, inne pola są kombinację tekstu i tokenów z **zawartości dynamicznej** okno dialogowe. 

    ![Pola wiadomości e-mail](media/functions-flow-scenario/email-fields.png)

    **Tytuł** token pochodzi z listy programu SharePoint i **CostToFix** i **RevenueOpportunity** są zwracane przez funkcję.

    Ukończony przepływ powinien wyglądać podobnie do następującego (pozostawiliśmy pierwszy **Jeśli** gałęzi, aby zaoszczędzić miejsce na).

    ![Pełnego przepływu](media/functions-flow-scenario/complete-flow.png)

4. Kliknij przycisk **przebiegu aktualizacji** w górnej części strony, następnie kliknij przycisk **gotowe**.

## <a name="run-the-flow"></a>Uruchamianie przepływu

Teraz, gdy przepływ zostanie zakończona, możesz dodać wiersz do listy programu SharePoint i reakcji przepływu.

1. Wróć do listy programu SharePoint, a następnie kliknij przycisk **szybka edycja**.

    ![Szybka edycja](media/functions-flow-scenario/quick-edit.png)

2. Wprowadź następujące wartości w siatce edycji.

    | Kolumna listy     | Wartość           |
    |-----------------|---------------------|
    | **Tytuł**           | Turbiny 60 |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | Tak |
    | **EstimatedEffort** | 10 |

3. Kliknij przycisk **Gotowe**.

    ![Szybka edycja — gotowe](media/functions-flow-scenario/quick-edit-done.png)

    Podczas dodawania elementu wyzwala przepływ, który możesz Przyjrzyj się dalej.

4. W [flow.microsoft.com](https://flow.microsoft.com), kliknij przycisk **moje przepływy**, następnie kliknij utworzony przepływ.

    ![Moje przepływy](media/functions-flow-scenario/my-flows.png)

5. W obszarze **HISTORII URUCHAMIANIA**, kliknij przycisk przebiegu przepływu.

    ![Historia uruchamiania](media/functions-flow-scenario/run-history.png)

    Jeśli uruchomienie zakończyło się pomyślnie, można przejrzeć przepływ operacji na następnej stronie. Jeśli działanie nie powiodło się dla jakiegokolwiek powodu, następnej strony zawiera informacje dotyczące rozwiązywania problemów.

6. Rozwiń kart, aby zobaczyć, co wydarzyło się podczas przepływu. Na przykład rozwiń folder **oblicza koszty** karty, aby wyświetlić dane wejściowe i dane wyjściowe z tej funkcji. 

    ![Oblicza koszty wejściami i wyjściami](media/functions-flow-scenario/calculates-costs-outputs.png)

7. Sprawdź konto e-mail osoby, określone w **do** pole **Wyślij wiadomość e-mail** karty. Wiadomości e-mail wysyłanej z przepływ powinien wyglądać podobnie do następującego.

    ![Przepływ poczty e-mail](media/functions-flow-scenario/flow-email.png)

    Aby zobaczyć, jak tokeny zostały zamienione na poprawne wartości z listy programu SharePoint i funkcji.

## <a name="next-steps"></a>Kolejne kroki
W tym temacie przedstawiono sposób:

> [!div class="checklist"]
> * Utwórz listę w programie SharePoint.
> * Eksportowanie definicji interfejsu API.
> * Dodaj połączenie z interfejsem API.
> * Utwórz przepływ do wysyłania wiadomości e-mail, jeśli naprawy jest ekonomiczne.
> * Uruchamianie przepływu.

Aby dowiedzieć się więcej na temat Microsoft Flow, zobacz [Rozpoczynanie pracy z usługą Microsoft Flow](https://flow.microsoft.com/documentation/getting-started/).

Aby dowiedzieć się o innych ciekawych scenariuszy, które używają usługi Azure Functions, zobacz [wywoływanie funkcji z usługi PowerApps](functions-powerapps-scenario.md) i [Tworzenie funkcji integrującej się z usługą Azure Logic Apps](functions-twitter-email.md).
