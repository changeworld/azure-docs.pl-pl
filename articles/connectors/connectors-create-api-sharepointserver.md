---
title: Używanie łącznika serwera programu SharePoint w aplikacji logiki | Dokumentacja firmy Microsoft
description: Rozpoczęcie korzystania z łącznika serwera programu SharePoint w aplikacji logiki
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 3ac3105231017cdbf8bfcf143b26451a68da5283
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868705"
---
# <a name="get-started-with-the-sharepoint-connector"></a>Rozpoczynanie pracy z łącznikiem programu SharePoint
Łącznik programu SharePoint udostępnia sposób pracy z listami w programie SharePoint.

Zacznij od utworzenia aplikacji logiki; zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-sharepoint"></a>Utwórz połączenie programu SharePoint
Do korzystania z łącznika programu SharePoint, należy najpierw utworzyć **połączenia** następnie podaj szczegóły tych właściwości: 

| Właściwość | Wymagane | Opis |
| --- | --- | --- |
| Token |Yes |Podaj poświadczenia programu SharePoint |

Aby nawiązać połączenie **SharePoint**, wprowadź swoją tożsamość (nazwa użytkownika i hasło, poświadczeń karty inteligentnej i tak dalej). Po użytkownik został uwierzytelniony, możesz przejść do korzystania z łącznika programu SharePoint w aplikacji logiki. 

Znajduje się w Projektancie aplikacji logiki, wykonaj następujące kroki, aby logowanie i tworzenie **połączenia** do użycia w aplikacji logiki:

1. W polu wyszukiwania wprowadź programu SharePoint i poczekaj na wyszukiwanie, aby zwrócić wszystkie wpisy z programem SharePoint w imieniu:   
   ![Konfigurowanie programu SharePoint][1]  
2. Wybierz **SharePoint — po utworzeniu pliku**   
3. Wybierz **logowanie w programie SharePoint**:   
   ![Konfigurowanie programu SharePoint][2]    
4. Podaj poświadczenia programu SharePoint, aby zalogować się do uwierzytelniania za pomocą programu SharePoint   
   ![Konfigurowanie programu SharePoint][3]     
5. Po ukończeniu uwierzytelniania są przekierowywane w do aplikacji logiki do jego wykonania, konfigurując dla programu SharePoint **po utworzeniu pliku** okna dialogowego.          
   ![Konfigurowanie programu SharePoint][4]  
6. Następnie można dodać inne wyzwalacze i akcje, które należy wykonać aplikację logiki.   
7. Zapisz swoją pracę, wybierając **Zapisz** menu (u góry).

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze swagger i zobacz też jakiekolwiek ograniczenia w [szczegóły łącznika](/connectors/sharepoint/).

## <a name="more-connectors"></a>Więcej łączników
Wróć do [listy interfejsów API](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
