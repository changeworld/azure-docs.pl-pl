---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: e087a1db008422aeec8fd4e073a7476eebe4d54b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183913"
---
1. Zaloguj się do [witryny Azure Portal].
2. Wybierz **+NOWA** > **sieć + mobilna** > **aplikacja mobilna,** a następnie podaj nazwę zaplecza aplikacji mobilnych.
3. W przypadku **grupy zasobów**wybierz istniejącą grupę zasobów lub utwórz nową (używając tej samej nazwy co aplikacja). 
4. W przypadku **planu usługi app service**zaznaczony jest plan domyślny (w [warstwie Standardowa).](https://azure.microsoft.com/pricing/details/app-service/) Możesz również wybrać inny plan lub [utworzyć nowy](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   Ustawienia planu usługi app service określają [lokalizację, funkcje, koszt i zasoby obliczeniowe skojarzone](https://azure.microsoft.com/pricing/details/app-service/) z aplikacją. Aby uzyskać więcej informacji o planach usługi App Service i o tym, jak utworzyć nowy plan w innej warstwie cenowej i w żądanej lokalizacji, zobacz [Szczegółowe omówienie planów usługi Azure App Service.](../articles/app-service/overview-hosting-plans.md)
   
5. Wybierz **pozycję Utwórz**. Ten krok tworzy zaplecza aplikacji mobilnych. 
6. W okienku **Ustawienia** nowego zaplecza aplikacji mobilnych wybierz pozycję **Szybki start** > platformę aplikacji klienckiej > **Połącz bazę danych**. 
   
   ![Wybory do łączenia bazy danych](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. W okienku **Dodawanie połączenia danych** wybierz pozycję Baza danych >  **SQL****Utwórz nową bazę danych**. Wprowadź nazwę bazy danych, wybierz warstwę cenową, a następnie wybierz pozycję **Serwer**. Możesz użyć tej nowej bazy danych ponownie. Jeśli masz już bazę danych w tej samej lokalizacji, możesz zamiast tego wybrać opcję **Użyj istniejącej bazy danych**. Nie zaleca się korzystania z bazy danych w innej lokalizacji, ze względu na koszty przepustowości i większe opóźnienia.
   
   ![Wybieranie bazy danych](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. W okienku **Nowy serwer** wprowadź unikatową nazwę serwera w polu **Nazwa serwera,** podaj login i hasło, wybierz pozycję **Zezwalaj usługom platformy Azure na dostęp do serwera**i wybierz przycisk **OK**. Ten krok tworzy nową bazę danych.
9. W okienku **Dodawanie połączenia danych** wybierz pozycję Parametry **połączenia**, wprowadź wartości logowania i hasła bazy danych i wybierz przycisk **OK**. 

   Zaczekaj kilka minut, aż baza danych zostanie pomyślnie wdrożona przed kontynuowaniem.

<!-- URLs. -->
[Portal Azure]: https://portal.azure.com/
