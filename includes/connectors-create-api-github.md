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
ms.openlocfilehash: ec5b3ca9ccd139cbdf17768056eb1d835336e7a7
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
ms.locfileid: "29764181"
---
1. W [portalu Azure](https://portal.azure.com), tworzenie aplikacji logiki puste. 

2. W Projektancie aplikacji logiki wprowadź "github" jako filtr. 

3. Wybierz łącznik usługi GitHub i wyzwalaczy, które chcesz użyć.

   ![Wybierz łącznik usługi GitHub i wyzwalacz](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Wszystkie przepływy pracy aplikacji logiki musi rozpoczynać się od wyzwalacza. Akcje można wybrać tylko wtedy, gdy przepływ pracy logiki już rozpoczyna się od wyzwalacza. 

4. Jeśli wcześniej nie utworzono połączenia, wybierz **Zaloguj** , możesz podać swoje poświadczenia usługi GitHub po wyświetleniu monitu.  

   ![Zaloguj się przy użyciu poświadczeń usługi GitHub](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Aplikację logiki używa tych poświadczeń do autoryzacji połączenie i dostęp do danych na koncie usługi GitHub. 

5. Podaj swoją nazwę użytkownika usługi GitHub i hasło, a następnie potwierdź autoryzację.

   ![Podaj poświadczenia, a następnie potwierdź autoryzacji](./media/connectors-create-api-github/github-connector-authorize.png)   

   Połączenie jest utworzone w portalu Azure i jest gotowy do użycia.

6. Nadal definiujący przepływ pracy aplikacji logiki.

   ![Dodaj więcej działań do przepływu pracy aplikacji logiki](./media/connectors-create-api-github/github-connector-logic-app.png)

