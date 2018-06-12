---
title: Łączenie z usługą Office 365 użytkownicy — aplikacje logiki platformy Azure | Dokumentacja firmy Microsoft
description: Zarządzanie profilami użytkowników z interfejsami API REST użytkowników usługi Office 365 i Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 08/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: f030ac07dc1615c435c1a110836d7a03ab8a8546
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295612"
---
# <a name="get-started-with-the-office-365-users-connector"></a>Rozpoczynanie pracy z łącznikiem użytkowników usługi Office 365
Podłącz do użytkowników usługi Office 365 można pobrać profilów, Wyszukaj użytkowników i inne. Użytkowników usługi Office 365 możesz:

* Tworzenie sieci przepływu biznesowe na podstawie danych, który można uzyskać od użytkowników usługi Office 365. 
* Użyj akcji, które uzyskać bezpośrednich podwładnych pobrać menedżera profilu użytkownika i inne. Te akcje uzyskać odpowiedzi, a następnie udostępnić dane wyjściowe do innych działań. Na przykład uzyskać osoby bezpośrednich podwładnych, a następnie pobierać te informacje i zaktualizować bazę danych SQL Azure. 

Rozpoczynanie pracy przez teraz tworzenie aplikacji logiki, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Utwórz połączenie użytkowników usługi Office 365
Po dodaniu tego łącznika do aplikacji logiki, należy zalogować się do konta użytkowników usługi Office 365 i Zezwalaj aplikacjom logiki do łączenia się z kontem.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Po utworzeniu połączenie, wprowadź właściwości użytkowników usługi Office 365, takich jak identyfikator użytkownika. **Dokumentacji interfejsu API REST** w tym artykule opisano te właściwości.

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger i zobacz też żadnych limitów w [szczegóły łącznika](/connectors/officeusers/).

## <a name="more-connectors"></a>Więcej łączników
Wróć do [listy interfejsów API](apis-list.md).