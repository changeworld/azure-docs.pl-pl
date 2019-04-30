---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: logic-apps
author: MandiOhlinger
ms.service: logic-apps
ms.topic: include
ms.date: 03/02/2018
ms.author: mandia
ms.custom: include file
ms.openlocfilehash: 11280e1678f52ede928cb2a85ea83add222e15fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61462592"
---
1. W [witryny Azure portal](https://portal.azure.com), tworzenie pustej aplikacji logiki. 

2. W Projektancie aplikacji logiki wprowadź "github" jako filtr. 

3. Wybierz łącznik usługi GitHub i wyzwalacz, który chcesz użyć.

   ![Wybierz łącznik usługi GitHub i wyzwalacza](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Wszystkie przepływy pracy aplikacji logiki musi rozpoczynać się od wyzwalacza. Można wybrać akcje, tylko wtedy, gdy przepływ pracy logiki już rozpoczyna się od wyzwalacza. 

4. Jeśli wcześniej nie utworzono połączenia, wybierz opcję **Zaloguj** można wyrazić swoje poświadczenia usługi GitHub, po wyświetleniu monitu.  

   ![Zaloguj się przy użyciu poświadczeń usługi GitHub](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Twoja aplikacja logiki używa tych poświadczeń do autoryzowania łączenia i uzyskiwania dostępu do danych dla Twojego konta usługi GitHub. 

5. Podaj nazwę użytkownika usługi GitHub i hasło, a następnie potwierdź Twojej autoryzacji.

   ![Podaj poświadczenia, a następnie potwierdź autoryzacji](./media/connectors-create-api-github/github-connector-authorize.png)   

   Połączenie jest tworzony w witrynie Azure portal i jest gotowa do użycia.

6. Kontynuuj, definiowanie przepływu pracy aplikacji logiki.

   ![Dodaj więcej akcji do przepływu pracy aplikacji logiki](./media/connectors-create-api-github/github-connector-logic-app.png)

