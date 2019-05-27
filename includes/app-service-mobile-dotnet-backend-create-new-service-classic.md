---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: e087a1db008422aeec8fd4e073a7476eebe4d54b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140622"
---
1. Zaloguj się do [Azure Portal].
2. Wybierz **+ nowy** > **sieci Web i mobilność** > **aplikacji mobilnej**, a następnie podaj nazwę dla zaplecza usługi Mobile Apps.
3. Aby uzyskać **grupy zasobów**, wybierz istniejącą grupę zasobów lub Utwórz nową (przy użyciu tej samej nazwie co aplikacja). 
4. Aby uzyskać **planu usługi App Service**, domyślny plan (w [w warstwie standardowa](https://azure.microsoft.com/pricing/details/app-service/)) jest zaznaczona. Możesz również wybrać inny plan lub [utworzyć nowy](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   Ustawienia planu usługi App Service określają [lokalizację, funkcje, koszt i zasoby obliczeniowe](https://azure.microsoft.com/pricing/details/app-service/) skojarzone z aplikacją. Aby uzyskać więcej informacji na temat usługi App Service, plany i sposób tworzenia nowego planu w różnych cenach warstwy i w preferowanej lokalizacji, zobacz [szczegółowe omówienie planów usługi Azure App Service](../articles/app-service/overview-hosting-plans.md).
   
5. Wybierz pozycję **Utwórz**. Spowoduje to utworzenie zaplecze funkcji Mobile Apps. 
6. W **ustawienia** okienko nową funkcję Mobile Apps zaplecze wybierz **— szybki start** > platforma aplikacji klienta > **łączenie bazy danych**. 
   
   ![Opcje do łączenia z bazą danych](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. W **Dodaj połączenie danych** okienku wybierz **bazy danych SQL** > **Utwórz nową bazę danych**. Wprowadź nazwę bazy danych, wybierz warstwę cenową, a następnie wybierz **serwera**. Możesz użyć tej nowej bazy danych ponownie. Jeśli masz już bazę danych w tej samej lokalizacji, możesz zamiast tego wybrać opcję **Użyj istniejącej bazy danych**. Nie zaleca się korzystanie z bazy danych w innej lokalizacji, z powodu kosztów przepustowości i większego opóźnienia.
   
   ![Wybieranie bazy danych](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. W **nowy serwer** okienku, wprowadź unikatową nazwę serwera w **nazwy serwera** Podaj login i hasło, wybierz **Zezwalaj na usługi platformy Azure na dostęp do serwera**i wybierz  **OK**. Spowoduje to utworzenie nowej bazy danych.
9. Ponownie **Dodaj połączenie danych** okienku wybierz **parametry połączenia**, a następnie wprowadź wartości identyfikator logowania i hasło dla bazy danych i wybierz **OK**. 

   Poczekaj kilka minut dla bazy danych została wdrożona pomyślnie przed kontynuowaniem.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/
