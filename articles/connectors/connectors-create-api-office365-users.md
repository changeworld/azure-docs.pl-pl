---
title: Nawiązywanie połączenia z użytkownikami pakietu Office 365
description: Zarządzanie profilami użytkowników przy użyciu interfejsów API REST użytkowników pakietu Office 365 i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: addb64a9b43c51af8363caa6f0fb3261a618e893
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789527"
---
# <a name="get-started-with-the-office-365-users-connector"></a>Wprowadzenie do łącznika użytkowników pakietu Office 365
Połącz się z użytkownikami pakietu Office 365, aby uzyskać profile, przeszukiwać użytkowników i nie tylko. Użytkownicy korzystający z pakietu Office 365 mogą:

* Utwórz przepływ biznesowy na podstawie danych uzyskanych od użytkowników pakietu Office 365. 
* Użyj akcji, które uzyskują bezpośrednie raporty, uzyskaj profil użytkownika Menedżera i nie tylko. Te akcje odbierają odpowiedź, a następnie udostępniają dane wyjściowe dla innych akcji. Na przykład Pobierz raporty bezpośrednie osoby, a następnie podejmij te informacje i zaktualizuj bazę danych SQL Azure. 

Teraz możesz zacząć od utworzenia aplikacji logiki, zobacz [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Utwórz połączenie z użytkownikami pakietu Office 365
Po dodaniu tego łącznika do aplikacji logiki należy zalogować się do konta użytkowników pakietu Office 365 i zezwolić aplikacjom logiki na łączenie się ze swoim kontem.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Po utworzeniu połączenia należy wprowadzić właściwości użytkowników pakietu Office 365, takie jak identyfikator użytkownika. **Dokumentacja interfejsu API REST** w tym artykule opisuje te właściwości.

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze Swagger, a także wszystkich ograniczeń w [szczegółach łącznika](/connectors/officeusers/).

## <a name="more-connectors"></a>Więcej łączników
Wróć do [listy interfejsów API](apis-list.md).