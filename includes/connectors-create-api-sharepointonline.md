---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 0cabc58d856c09accd9b1924fe63d6518b1cb9ef
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205778"
---
Aby można było nawiązać **usługi SharePoint Online**, należy podać swoją tożsamość (nazwa użytkownika i hasło, inteligentne poświadczeń karty itp.) do usługi SharePoint Online. Gdy uwierzytelnienie powiodło się, możesz przejść do korzystania z łącznika usługi SharePoint Online w aplikacji logiki. 

Znajduje się w Projektancie aplikacji logiki, wykonaj następujące kroki, aby zalogować się do programu SharePoint do tworzenia **połączenia** do użycia w aplikacji logiki:

1. W polu wyszukiwania wprowadź programu SharePoint i poczekaj na wyszukiwanie, aby zwrócić wszystkie wyzwalacze i akcje związane z usługą SharePoint Online:   
   ![Konfigurowanie programu SharePoint][1]  
2. Wybierz **usługi SharePoint Online — po utworzeniu pliku** wyzwalacza  
3. Wybierz **logowanie do usługi SharePoint Online**:   
   ![Konfigurowanie programu SharePoint][2]    
4. Podaj poświadczenia programu SharePoint, aby zalogować się do uwierzytelniania za pomocą programu SharePoint   
   ![Konfigurowanie programu SharePoint][3]     
5. Po zakończeniu uwierzytelniania nastąpi przekierowanie do aplikacji logiki. To wszystko, utworzeniu połączenia. Należy zauważyć komunikat wyświetlany u dołu, która wskazuje, czy masz teraz połączenie do programu SharePoint.  
   ![Konfigurowanie programu SharePoint][4]  
6. Następnie można dodać inne wyzwalacze i akcje, które należy wykonać aplikację logiki.   

[1]: ./media/connectors-create-api-sharepointonline/connectionconfig1.png
[2]: ./media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ./media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ./media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ./media/connectors-create-api-sharepointonline/connectionconfig5.png
