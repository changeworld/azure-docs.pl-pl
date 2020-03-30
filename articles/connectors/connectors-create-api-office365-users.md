---
title: Łączenie się z użytkownikami usługi Office 365
description: Automatyzuj zadania i przepływy pracy, które otrzymują profile i zarządzają nimi w profilach użytkowników usługi Office 365 przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666860"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Pobieranie profili w usługach Office 365 i zarządzanie nimi przy użyciu aplikacji Azure Logic Apps

Połącz się z użytkownikami usługi Office 365, aby uzyskać profile, wyszukiwanie użytkowników i nie tylko. Dzięki użytkownikom usługi Office 365 możesz:

* Twórz przepływ biznesowy na podstawie danych, które otrzymujesz od użytkowników usługi Office 365. 
* Użyj akcji, które otrzymują bezpośrednie raporty, uzyskaj profil użytkownika menedżera i nie tylko. Te akcje uzyskać odpowiedź, a następnie udostępnić dane wyjściowe dla innych akcji. Na przykład pobierz bezpośrednie raporty danej osoby, a następnie weź te informacje i zaktualizuj bazę danych platformy SQL Azure. 

Możesz rozpocząć, tworząc aplikację logiki teraz, zobacz [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Tworzenie połączenia z użytkownikami usługi Office 365

Po dodaniu tego łącznika do aplikacji logiki należy zalogować się do konta użytkowników usługi Office 365, aby usługa Azure Logic Apps mogła połączyć się z Twoim kontem.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Po utworzeniu połączenia należy wprowadzić właściwości Użytkownicy usługi Office 365, takie jak identyfikator użytkownika. **Odwołanie interfejsu API REST** w tym artykule opisuje te właściwości.

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie Swagger łącznika, przejrzyj [stronę referencyjną łącznika](/connectors/officeusers/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](apis-list.md)