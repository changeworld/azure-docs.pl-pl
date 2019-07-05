---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: a7e543dcad9ad1b016d1244451cd87cda5ad7492
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440216"
---
1. Pobierz klienta SDK przewodników Szybki Start dla następujących platform:
    
    [dla systemu iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Jeśli używasz projektu systemu iOS należy pobrać "azuresdk-iOS -\*.zip" z [najnowszej wersji usługi GitHub](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Rozpakuj i Dodaj `MicrosoftAzureMobile.framework` plik do katalogu głównego projektu.
    >

2. Musisz dodać połączenie z bazą danych lub Połącz z istniejącego połączenia. Po pierwsze należy ustalić, czy będzie Utwórz magazyn danych lub użyj istniejącej.

    - **Utwórz nowy magazyn danych**: Jeśli zamierzasz utworzyć magazyn danych, należy użyć poniższej procedury szybkiego startu:

        [Szybki start: Wprowadzenie do pojedynczych baz danych w usłudze Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Istniejące źródło danych**: Postępuj zgodnie z instrukcjami poniżej, jeśli chcesz użyć istniejącego połączenia bazy danych

        1. Format parametrów połączenia bazy danych SQL — `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}**  Nazwę serwera, to można znaleźć na stronie Przegląd usługi dla bazy danych i jest zwykle w formie "server_name.database.windows.net".
            **{port}**  zwykle 1433.
            **{your_catalogue}**  Nazwę bazy danych.
            **{your_username}**  Nazwę użytkownika, dostęp do bazy danych.
            **{your_password}**  Hasło, aby dostęp do bazy danych.

            [Dowiedz się więcej o format parametrów połączenia SQL](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Dodaj parametry połączenia do Twojej **aplikacji mobilnej** w usłudze App Service możesz określić parametry połączenia dla aplikacji przy użyciu **konfiguracji** opcji w menu.

            Aby dodać parametry połączenia:

            1. Kliknij pozycję **ustawienia aplikacji** kartę.

            2. Kliknij pozycję **[+] nowe parametry połączenia**.

            3. Należy podać **nazwa**, **wartość** i **typu** dla parametrów połączenia.

            4. Typ **nazwa** jako `MS_TableConnectionString`

            5. Wartość powinna być w parametrach połączenia, które utworzone w kroku przed.

            6. W przypadku dodawania parametrów połączenia z bazą danych SQL Azure wybierz **SQLAzure** w obszarze **typu**.

3. Usługa Azure Mobile Apps zawiera zestawy SDK dla zaplecza .NET i Node.js.

   - **Zaplecze środowiska Node.js**
    
     Jeśli zamierzasz używać aplikacji szybkiego startu Node.js, postępuj zgodnie z poniższymi instrukcjami.

     1. W witrynie Azure portal przejdź do **łatwych tabel**, zostanie wyświetlony następujący ekran.
      
        ![Węzeł łatwych tabel](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. Upewnij się, że parametry połączenia SQL został już dodany do **konfiguracji** kartę. Zaznacz pole wyboru **potwierdzam, że spowoduje to zastąpienie całej zawartości witryny** i kliknij przycisk **Utwórz tabelę TodoItem** przycisku.
     
        ![Konfiguracja łatwych tabel węzła](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. W **łatwych tabel**, kliknij przycisk **+ Dodaj** przycisku.
    
        ![Przycisk Dodaj węzeł łatwych tabel](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. Utwórz `TodoItem` tabelę z dostęp anonimowy.
      
        ![Łatwe tabele węźle Dodaj tabelę](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **Zaplecze platformy .NET**
    
        Jeśli zamierzasz używać aplikacji szybkiego startu platformy .NET, postępuj zgodnie z poniższymi instrukcjami.

        1. Pobierz Projekt serwera platformy .NET dla aplikacji mobilnych platformy Azure z [repozytorium azure-mobile-apps-quickstarts](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Tworzenie projektu serwera .NET lokalnie w programie Visual Studio.

        3. W programie Visual Studio, otwórz Solution Explorer, kliknij prawym przyciskiem myszy `ZUMOAPPNAMEService` projektu, kliknij przycisk **Publikuj**, zostanie wyświetlony `Publish to App Service` okna. Jeśli pracujesz na komputerze Mac, zapoznaj się z innych sposobów wdrażania aplikacji [tutaj](https://docs.microsoft.com/azure/app-service/deploy-local-git).
        
           ![Publikowanie z programu Visual studio](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Wybierz **usługi App Service** publikować docelowego, następnie kliknij przycisk **wybierz istniejącą**, następnie kliknij przycisk **Publikuj** znajdujący się u dołu okna.

        5. Musisz zalogować się do programu Visual Studio z subskrypcją platformy Azure najpierw. Wybierz `Subscription`, `Resource Group`, a następnie wybierz nazwę swojej aplikacji. Gdy wszystko będzie gotowe, kliknij przycisk **OK**, spowoduje to wdrożenie projektu serwera platformy .NET, czy masz lokalnie do zaplecza usługi App Service. Po zakończeniu wdrażania nastąpi przekierowanie do `http://{zumoappname}.azurewebsites.net/` w przeglądarce.                   