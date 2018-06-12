---
title: Korzystanie z łącznika zapas czasu w aplikacjach logiki platformy Azure | Dokumentacja firmy Microsoft
description: Nawiązać zapas czasu w aplikacjach logiki
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 88b134a90ac385ad957d76f420fe85dc2dbbf751
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296227"
---
# <a name="get-started-with-the-slack-connector"></a>Rozpoczynanie pracy z Slack łącznika
Slack jest narzędziem do komunikacji zespołowej, które łączy całą komunikację zespołową w jednym miejscu. Umożliwia natychmiastowe wyszukiwanie i dostęp do komunikacji z dowolnego miejsca. 

Rozpoczynanie pracy przez tworzenie aplikacji logiki teraz; zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-slack"></a>Utwórz połączenie zapas czasu
Do korzystania z łącznika Slack, należy najpierw utworzyć **połączenia** następnie podaj szczegóły tych właściwości: 

| Właściwość | Wymagane | Opis |
| --- | --- | --- |
| Token |Yes |Podaj poświadczenia usługi Slack |

Zaloguj się do zapas czasu, wykonując następujące kroki i zakończyć konfigurację Slack **połączenia** w aplikacji logiki:

1. Wybierz **cyklu**
2. Wybierz **częstotliwość** , a następnie wprowadź **interwał**
3. Wybierz **Dodaj akcję**  
   ![Skonfiguruj zapas czasu][1]  
4. W polu wyszukiwania wprowadź zapas czasu i poczekaj, aż wyszukiwania zwrócić wszystkie wpisy z zapas czasu w nazwie
5. Wybierz **Slack - Post wiadomości**
6. Wybierz **Zaloguj się do zapas czasu**:  
   ![Skonfiguruj zapas czasu][2]
7. Podaj poświadczenia Slack logować się do autoryzowania aplikacji    
   ![Skonfiguruj zapas czasu][3]  
8. Użytkownik zostanie przekierowany do stronę logowania Twojej organizacji. **Autoryzowanie** zapas czasu wchodzić w interakcje z aplikacji logiki:      
   ![Skonfiguruj zapas czasu][5] 
9. Po zakończeniu autoryzacji są przekierowywane do aplikacji logiki do ukończenia jej przez skonfigurowanie **Slack — pobranie wszystkich wiadomości** sekcji. Dodawanie innych wyzwalacze i akcje, które są potrzebne.  
   ![Skonfiguruj zapas czasu][6]
10. Zapisz swoją pracę, wybierając **zapisać** menu (w kierunku do góry).

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger i zobacz też żadnych limitów w [szczegóły łącznika](/connectors/slack/).

## <a name="more-connectors"></a>Więcej łączników
Wróć do [listy interfejsów API](apis-list.md).

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
