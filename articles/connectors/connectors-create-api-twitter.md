---
title: Informacje o sposobie korzystania z łącznika usługi Twitter w aplikacjach logiki | Dokumentacja firmy Microsoft
description: Omówienie łącznik Twitter z parametrami interfejsu API REST
services: ''
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: b44a973a94043f71f2fd9803abca47652363d8a1
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296547"
---
# <a name="get-started-with-the-twitter-connector"></a>Rozpoczynanie pracy z łącznikiem usługi Twitter
Łącznik Twitter można:

* Publikowanie tweetów i tweetów get
* Dostęp do osi czasu, znajomych i adherentami
* Wykonaj jedną z innych działań i wyzwalaczy opisane w tym artykule

Aby użyć [każdy łącznik](apis-list.md), należy najpierw utworzyć aplikację logiki. Możesz rozpocząć pracę przez [teraz tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).  

## <a name="connect-to-twitter"></a>Łączenie z usługą Twitter
Zanim aplikację logiki można uzyskać dostęp do dowolnej usługi, należy najpierw utworzyć *połączenia* do usługi. A [połączenia](connectors-overview.md) udostępnia łączność między aplikacji logiki i innej usługi.  

### <a name="create-a-connection-to-twitter"></a>Utwórz połączenie z serwisem Twitter
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>Użyj wyzwalacz Twitter
Wyzwalacz to zdarzenie służy do uruchomienia przepływu pracy zdefiniowanych w aplikacji logiki. [Dowiedz się więcej o wyzwalaczy](../logic-apps/logic-apps-overview.md#logic-app-concepts).

W tym przykładzie używamy **po jest przesyłana z nowego tweet** trigger, aby wyszukać #Seattle. I jeśli zostanie znaleziony #Seattle, zaktualizuj plik w skrzynki tekstem z tweet. W przykładzie przedsiębiorstwa można wyszukać nazwę swojej firmy i zaktualizować bazę danych SQL z tekstem z tweet.

1. Wprowadź *twitter* w polu wyszukiwania w Projektancie aplikacji logiki wybiorą **Twitter — gdy nowy tweet jest przesyłana** wyzwalacza   
   ![Obraz wyzwalacza Twitter 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. Wprowadź *#Seattle* w **tekst do wyszukania** formantu  
   ![Wyzwalacz Twitter — obraz 2](./media/connectors-create-api-twitter/trigger-2.png) 

W tym momencie aplikację logiki została skonfigurowana z wyzwalaczy, które rozpocznie się do wykonywania innych wyzwalacze i akcje w przepływie pracy. 

> [!NOTE]
> Dla aplikacji logiki działała musi ona zawierać przynajmniej jeden wyzwalacz i jedną akcję. Wykonaj kroki w następnej sekcji, aby dodać akcję.

## <a name="add-a-condition"></a>Dodaj warunek
Tylko Dbamy o tweetów od użytkowników więcej niż 50 użytkowników. Tak warunek, który potwierdza liczba adherentami najpierw została dodana do aplikacji logiki.  

1. Wybierz **+ nowy krok** Aby dodać akcję ma zostać podjęta po #Seattle znajduje się w nowej tweet  
   ![Obraz akcji Twitter 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. Wybierz **Dodaj warunek** łącza.  
   ![Obraz warunku Twitter 1](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   Spowoduje to otwarcie **warunku** kontrolki, w którym można sprawdzić warunków takich jak *jest równa*, *jest mniejsza niż*, *jest większa niż*, *zawiera*itp.  
   ![Warunek Twitter — obraz 2](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. Wybierz **wybierz wartość** formantu. W tym formancie można wybrać jedną lub więcej właściwości z poprzedniej akcji lub wyzwalaczy. Warunek wartość tej właściwości jest obliczane do wartości true lub false.
   ![Obraz warunku Twitter 3](../../includes/media/connectors-create-api-twitter/condition-3.png)   
4. Wybierz **...**  rozwiń listę właściwości, dzięki czemu wszystkie właściwości, które są dostępne.        
   ![Stan obrazu 4 w usłudze Twitter](../../includes/media/connectors-create-api-twitter/condition-4.png)   
5. Wybierz **liczba adherentami** właściwości.    
   ![Obraz warunku Twitter 5](../../includes/media/connectors-create-api-twitter/condition-5.png)   
6. Zwróć uwagę, adherentami właściwość count jest teraz w formancie wartość.    
   ![Stan obrazu 6 w usłudze Twitter](../../includes/media/connectors-create-api-twitter/condition-6.png)   
7. Wybierz **jest większa niż** z listy operatorów.    
   ![Stan obrazu 7 w usłudze Twitter](../../includes/media/connectors-create-api-twitter/condition-7.png)   
8. Wprowadź 50 jako argument *jest większa niż* operatora.  
   Dodawany jest warunek. Zapisz swoją pracę przy użyciu **zapisać** łącze w menu.    
   ![Stan obrazu 8 w usłudze Twitter](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>Za pomocą akcji usługi Twitter
Akcja jest przeprowadzane przez przepływ pracy zdefiniowanych w aplikacji logiki operacji. [Dowiedz się więcej o akcjach](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

Teraz, że istnieje wyzwalacz, Dodaj akcję, która zapisuje nowe tweet z zawartością tweetów znalezione przez wyzwalacz. W tym przewodniku tylko tweetów od użytkowników więcej niż 50 adherentami są publikowane.  

W następnym kroku należy dodać akcję Twitter, która zapisuje tweet przy użyciu niektórych właściwości każdego tweet publikowanego przez użytkownika z więcej niż 50 adherentami.  

1. Wybierz **Dodaj akcję**. Ten krok powoduje otwarcie formantu wyszukiwania, których można wyszukiwać innych działań i wyzwalaczy.  
   ![Obraz warunku Twitter 9](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. Wprowadź *twitter* w polu wyszukiwania wybierz **Twitter — Opublikuj tweet** akcji. Ten krok powoduje otwarcie **Opublikuj tweet** kontroli, których wprowadź wszystkie szczegóły dla tweet ogłaszany.      
   ![Obraz akcji 1 do 5 w usłudze Twitter](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. Wybierz **Tweetować tekst** formantu. Widoczne są wszystkie dane wyjściowe z poprzedniego działania i jest wyzwalane w aplikacji logiki. Można wybrać dowolny z tych danych wyjściowych i używać ich jako część tekst tweet tweet nowe.     
   ![Obraz akcji Twitter 2](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. Wybierz **nazwy użytkownika**   
5. Natychmiast po nazwie użytkownika, wprowadź *mówi:* w formancie tweet tekstu.
6. Wybierz *Tweetować tekstu*.       
   ![Obraz akcji Twitter 3](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. Aby aktywować przepływu pracy, należy zapisać pracę i publikowała tweet z hasztagiem #Seattle.


## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger i zobacz też żadnych limitów w [szczegóły łącznika](/connectors/twitterconnector/). 

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)