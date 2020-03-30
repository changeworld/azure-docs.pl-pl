---
title: Dodawanie pętli do powtarzania akcji
description: Tworzenie pętli, które powtarzają akcje przepływu pracy lub tablice procesów w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: 5f6c04c9a57dc8c250d99f2fa944203d2d73c404
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270578"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Tworzenie pętli, które powtarzają akcje przepływu pracy lub tablice procesów w usłudze Azure Logic Apps

Aby przetworzyć tablicę w aplikacji logiki, można utworzyć [pętlę "Foreach".](#foreach-loop) Ta pętla powtarza jedną lub więcej akcji dla każdego elementu w tablicy. Aby uzyskać limity liczby elementów tablicy, które mogą przetwarzać pętle "Foreach", zobacz [Limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md). 

Aby powtarzać akcje, dopóki warunek nie zostanie spełniony lub stan się zmieni, można utworzyć [pętlę "Do"](#until-loop). Aplikacja logiki najpierw uruchamia wszystkie akcje wewnątrz pętli, a następnie sprawdza warunek lub stan. Jeśli warunek jest spełniony, pętla zatrzymuje się. W przeciwnym razie pętla się powtarza. Aby uzyskać limity liczby pętli "Do" w uruchomionej aplikacji logiki, zobacz [Limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP]
> Jeśli masz wyzwalacz, który odbiera tablicę i chcesz uruchomić przepływ pracy dla każdego elementu tablicy, możesz *dyskutować o* tej tablicy za pomocą [właściwości wyzwalacza **SplitOn** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Pętla "Foreach"

"Pętla Foreach" powtarza jedną lub więcej akcji dla każdego elementu tablicy i działa tylko na tablicach. Iteracje w pętli "Foreach" są uruchamiane równolegle. Można jednak uruchamiać iteracje po jednym naraz, konfigurując [kolejną pętlę "Foreach".](#sequential-foreach-loop) 

Oto kilka zagadnień podczas korzystania z pętli "Foreach":

* W zagnieżdżonych pętlach iteracje zawsze są uruchamiane sekwencyjnie, a nie równolegle. Aby uruchomić operacje równolegle dla elementów w zagnieżdżonej pętli, należy utworzyć i [wywołać aplikację logiki podrzędnej](../logic-apps/logic-apps-http-endpoint.md).

* Aby uzyskać przewidywalne wyniki z operacji na zmiennych podczas każdej iteracji pętli, uruchom te pętle sekwencyjnie. Na przykład po zakończeniu równocześnie uruchomionej pętli przyrost, dekrementacja i dołącz do operacji zmiennych zwracają przewidywalne wyniki. Jednak podczas każdej iteracji w pętli równocześnie uruchomionej, te operacje mogą zwracać nieprzewidywalne wyniki. 

* Akcje w pętli "Foreach"[`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
wyrażenie do odwoływania się i przetwarzania każdego elementu w tablicy. Jeśli określisz dane, które nie są w tablicy, przepływ pracy aplikacji logiki kończy się niepowodzeniem. 

W tym przykładzie aplikacja logiki wysyła codzienne podsumowanie dla kanału RSS witryny. Aplikacja używa pętli "Foreach", która wysyła wiadomość e-mail dla każdego nowego elementu.

1. [Utwórz tę przykładową aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) za pomocą konta Outlook.com lub programu Outlook usługi Office 365.

2. Między wyzwalaczem RSS i wyślij akcję e-mail dodaj pętlę "Foreach". 

   1. Aby dodać pętlę między krokami, przesuń wskaźnik myszy na strzałkę między tymi krokami. 
   Wybierz **wyświetlony** znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

      ![Wybierz "Dodaj akcję"](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. W polu wyszukiwania wybierz pozycję **Wszystko**. W polu wyszukiwania wpisz "dla każdego" jako filtr. Z listy akcji wybierz tę akcję: **Dla każdego - Kontrola**

      ![Dodaj pętlę "Dla każdego"](media/logic-apps-control-flow-loops/select-for-each.png)

3. Teraz zbuduj pętlę. W obszarze **Wybierz dane wyjściowe z poprzednich kroków** po wyświetleniu listy **Dodaj zawartość dynamiczną** wybierz tablicę **Łącza źródła danych,** która jest wyprowadzana z wyzwalacza RSS. 

   ![Wybieranie z dynamicznej listy zawartości](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Można wybrać *tylko* wyjścia tablicowe z poprzedniego kroku.

   W tym miejscu pojawi się wybrana tablica:

   ![Wybierz tablicę](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Aby uruchomić akcję dla każdego elementu tablicy, przeciągnij akcję **Wyślij wiadomość e-mail** do pętli. 

   Aplikacja logiki może wyglądać mniej więcej w tym przykładzie:

   ![Dodawanie kroków do pętli "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Zapisz aplikację logiki. Aby ręcznie przetestować aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definicja pętli "Foreach" (JSON)

Jeśli pracujesz w widoku kodu dla aplikacji logiki, możesz zdefiniować `Foreach` pętlę w definicji JSON aplikacji logiki, na przykład:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>Pętla "Foreach": Sekwencyjna

Domyślnie cykle w pętli "Foreach" są uruchamiane równolegle. Aby uruchomić każdy cykl sekwencyjnie, ustaw opcję **sekwencyjne** pętli. Pętle "Foreach" muszą być uruchamiane sekwencyjnie, gdy masz zagnieżdżone pętle lub zmienne wewnątrz pętli, w których można oczekiwać przewidywalnych wyników. 

1. W prawym górnym rogu pętli wybierz **elipsy** (**...**) > **Ustawienia**.

   ![W pętli "Foreach" wybierz "..." > "Ustawienia"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. W obszarze **Kontrola współbieżności**włącz ustawienie **Kontrola współbieżności** **.** Przesuń suwak **Stopień równoległości** do **1**i wybierz pozycję **Gotowe**.

   ![Włączanie kontroli współbieżności](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Jeśli pracujesz z definicją JSON aplikacji logiki, `Sequential` możesz użyć `operationOptions` tej opcji, dodając parametr, na przykład:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>Pętla "Aż"
  
Aby uruchamiać i powtarzać akcje, dopóki warunek nie zostanie spełniony lub stan się zmieni, umieść te akcje w pętli "Do". Aplikacja logiki najpierw uruchamia wszystkie akcje wewnątrz pętli, a następnie sprawdza warunek lub stan. Jeśli warunek jest spełniony, pętla zatrzymuje się. W przeciwnym razie pętla się powtarza.

Oto kilka typowych scenariuszy, w których można użyć pętli "Do":

* Wywołaj punkt końcowy, dopóki nie otrzymasz żądanej odpowiedzi.

* Tworzenie rekordu w bazie danych. Poczekaj, aż określone pole w tym rekordzie zostanie zatwierdzone. Kontynuuj przetwarzanie. 

Począwszy od 8:00 AM każdego dnia, w tym przykładzie logiki aplikacji zwiększa zmiennej, aż wartość zmiennej jest równa 10. Aplikacja logiki następnie wysyła wiadomość e-mail, która potwierdza bieżącą wartość. 

> [!NOTE]
> Te kroki są używane w usłudze Office 365 Outlook, ale można użyć dowolnego dostawcy poczty e-mail, który obsługuje aplikacje logiki. 
> [Sprawdź listę złączy tutaj](https://docs.microsoft.com/connectors/). Jeśli używasz innego konta e-mail, ogólne kroki pozostają takie same, ale interfejs użytkownika może wyglądać nieco inaczej. 

1. Tworzenia pustej aplikacji logiki. W logic app designer pod polem wyszukiwania wybierz pozycję **Wszystkie**. Wyszukaj hasło "cykl". 
   Z listy wyzwalaczy wybierz ten wyzwalacz: **Cykl - Harmonogram**

   ![Dodaj wyzwalacz "Cykl - Harmonogram"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Określ, kiedy wyzwalacz jest uruchamiany, ustawiając interwał, częstotliwość i godzinę dnia. Aby ustawić godzinę, wybierz pozycję **Pokaż opcje zaawansowane**.

   ![Konfigurowanie harmonogramu cyklu](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Właściwość | Wartość |
   | -------- | ----- |
   | **Interwał** | 1 | 
   | **Częstotliwości** | Day |
   | **W tych godzinach** | 8 |
   ||| 

1. W obszarze wyzwalacza wybierz pozycję **Nowy krok**. 
   Wyszukaj "zmienne" i wybierz tę akcję: **Inicjuj zmienną - Zmienne**

   ![Dodaj akcję "Inicjuj zmienną - zmienne"](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Skonfiguruj zmienną przy następujących wartościach:

   ![Ustawianie właściwości zmiennej](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Właściwość | Wartość | Opis |
   | -------- | ----- | ----------- |
   | **Nazwa** | Limit | Nazwa zmiennej | 
   | **Typ** | Liczba całkowita | Typ danych zmiennej | 
   | **Wartość** | 0 | Wartość początkowa zmiennej | 
   |||| 

1. W obszarze Akcja **Inicjuj zmienną** wybierz pozycję **Nowy krok**. 

1. W polu wyszukiwania wybierz pozycję **Wszystko**. Wyszukaj "do" i wybierz tę akcję: **Until - Control**

   ![Dodaj pętlę "Do"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Skompiluj warunek zakończenia pętli, wybierając **zmienną Limit** i **jest równy** operator. 
   Wprowadź **10** jako wartość porównania.

   ![Warunek wyjścia kompilacji dla pętli zatrzymania](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Wewnątrz pętli wybierz pozycję **Dodaj akcję**. 

1. W polu wyszukiwania wybierz pozycję **Wszystko**. Wyszukaj "zmienne" i wybierz tę akcję: **Zmienna przyrostowa - Zmienne**

   ![Dodaj akcję dla zmiennej przyrostowej](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. W obszarze **Nazwa**wybierz zmienną **Limit.** W polu **Wartość**wprowadź wartość "1". 

     ![Przyrost "Ogranicz" o 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Na zewnątrz i pod pętlą wybierz nowy **krok**. 

1. W polu wyszukiwania wybierz pozycję **Wszystko**. 
     Znajdź i dodaj akcję, która wysyła wiadomość e-mail, na przykład: 

     ![Dodawanie akcji, która wysyła wiadomość e-mail](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. W razie potrzeby zaloguj się do swojego konta e-mail.

1. Ustaw właściwości akcji e-mail. Dodaj zmienną **Limit** do obiektu. W ten sposób można potwierdzić, że bieżąca wartość zmiennej spełnia określony warunek, na przykład:

      ![Konfigurowanie właściwości poczty e-mail](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Właściwość | Wartość | Opis |
      | -------- | ----- | ----------- | 
      | **Do** | *\<>domeny\@adresu e-mail* | Adres e-mail odbiorcy. Do testowania użyj własnego adresu e-mail. | 
      | **Temat** | Bieżąca wartość dla "Limitu" to **Limit** | Określ temat wiadomości e-mail. W tym przykładzie upewnij się, że uwzględnisz zmienną **Limit.** | 
      | **Treść** | <*zawartość poczty e-mail*> | Określ zawartość wiadomości e-mail, którą chcesz wysłać. W tym przykładzie wprowadź dowolny tekst. | 
      |||| 

1. Zapisz aplikację logiki. Aby ręcznie przetestować aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

      Po uruchomieniu logiki otrzymasz wiadomość e-mail z określoną zawartością:

      ![Odebrana wiadomość e-mail](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Zapobiegaj niekończącym się pętlom

Pętla "Do" ma domyślne limity, które zatrzymują wykonywanie, jeśli którykolwiek z tych warunków się zdarzyć:

| Właściwość | Wartość domyślna | Opis | 
| -------- | ------------- | ----------- | 
| **Liczba** | 60 | Największa liczba pętli, które są uruchamiane przed zamknięciem pętli. Wartość domyślna to 60 cykli. | 
| **Limit czasu** | PT1H (polski) | Najwięcej czasu na uruchomienie pętli przed zamknięciem pętli. Wartość domyślna to jedna godzina i jest określona w formacie ISO 8601. <p>Wartość limitu czasu jest obliczana dla każdego cyklu pętli. Jeśli jakakolwiek akcja w pętli trwa dłużej niż limit czasu, bieżący cykl nie zatrzyma. Jednak następny cykl nie uruchamia się, ponieważ warunek limitu nie jest spełniony. | 
|||| 

Aby zmienić te domyślne limity, wybierz pozycję **Pokaż opcje zaawansowane** w kształcie akcji pętli.

<a name="until-json"></a>

## <a name="until-definition-json"></a>Definicja "Do" (JSON)

Jeśli pracujesz w widoku kodu dla aplikacji logiki, możesz zdefiniować pętlę `Until` w definicji JSON aplikacji logiki, na przykład:

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

W tym przykładzie "Do" pętli wywołuje punkt końcowy HTTP, który tworzy zasób. Pętla zatrzymuje się, gdy `Completed` treść odpowiedzi HTTP zwraca ze stanem. Aby zapobiec niekończącym się pętlom, pętla zatrzymuje się również, jeśli zdarzy się którykolwiek z tych warunków:

* Pętla została uruchomiony 10 `count` razy, jak określono w atrybucie. Wartość domyślna to 60 razy. 

* Pętla działała przez dwie `timeout` godziny, zgodnie z atrybutem w formacie ISO 8601. Wartość domyślna to jedna godzina.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać lub zagłosować na funkcje i sugestie, [witryna opinii użytkowników usługi Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie kroków na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Uruchamianie kroków na podstawie różnych wartości (instrukcje switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchamianie lub scalanie równoległych kroków (gałęzi)](../logic-apps/logic-apps-control-flow-branches.md)
* [Uruchamianie kroków na podstawie stanu akcji zgrupowanych (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
