---
title: Usługa połączona programu Visual Studio dla usługi Azure Cosmos DB
description: Umożliwia deweloperom łatwe podłączenie swojego konta usługi Azure Cosmos DB i zarządzanie zasobami za pomocą usług połączonych programu Visual Studio
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jejiang
ms.openlocfilehash: d496d80f1d87b234e0c94333b01ad2c23cc037ab
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2018
---
# <a name="azure-cosmos-db-visual-studio-connected-service-preview"></a>Azure Cosmos DB: usługa połączona programu Visual Studio (wersja zapoznawcza)

Usługi połączone programu Visual Studio umożliwiają deweloperom łatwe podłączenie swojego konta usługi Azure Cosmos DB i zarządzanie zasobami.

Eksplorator danych w usługach połączonych umożliwia również tworzenie procedur składowanych, funkcji zdefiniowanych przez użytkownika i wyzwalaczy w celu wykonania logiki biznesowej po stronie serwera. Eksplorator danych udostępnia wszystkie wbudowane programowe procedury dostępu do danych w interfejsach API, ale umożliwia łatwy dostęp do danych.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że masz:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/). 
* Konto usługi Azure Cosmos DB. Jeśli jeszcze go nie masz, wykonaj kroki opisane w sekcji [Tworzenie bazy danych Azure Cosmos DB](create-sql-api-dotnet.md), aby utworzyć je w witrynie Azure Portal, lub zobacz sekcję [Tworzenie konta Azure Cosmos DB w narzędziu usługi połączonej](#Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool). 
* Jeśli chcesz używać lokalnego środowiska do celów programistycznych, możesz skorzystać z [emulatora usługi Azure Cosmos DB](local-emulator.md). Środowisko emuluje usługę Azure Cosmos DB.
* Program [Visual Studio](http://www.visualstudio.com/).
* Najnowszą usługę bits usługi połączonej Azure Cosmos DB. Usługę połączoną usługi Azure Cosmos DB można pobrać z witryny Visual Studio Marketplace, jak pokazano na poniższym zrzucie ekranu. Otwórz program **Visual Studio** na komputerze. W menu **Narzędzia** wybierz opcję **Rozszerzenia i aktualizacje...**, a następnie wybierz pozycję **Online** / **Visual Studio Marketplace**. Wprowadź ciąg **cosmosdb**, aby wyszukać usługę bits.

    Można także zainstalować usługę połączoną usługi Azure Cosmos DB z portalu [Visual Studio Marketplace](https://go.microsoft.com/fwlink/?linkid=858709).

    ![Zrzut ekranu przedstawiający pobieranie pliku bits.png usługi połączonej](./media/connected-service/connected-service-downloadbits.png) 

Po pobraniu rozszerzenia usługi połączonej dla usługi Azure Cosmos DB zamknij program Visual Studio i zainstaluj rozszerzenie.

## <a id="SetupVS"></a>Konfigurowanie rozwiązania programu Visual Studio
1. Otwórz program **Visual Studio** na komputerze.
2. W menu **Plik** wybierz polecenie **Nowy**, a następnie wybierz pozycję **Projekt**.
3. W oknie dialogowym **Nowy projekt** wybierz pozycję **Visual C#** / **Aplikacja konsoli (.NET Framework)** lub **Aplikacja WPF (.NET Framework)**, podaj nazwę projektu, a następnie kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający okno Nowy projekt](./media/connected-service/connected-service-new-project.png)
    
## <a name="add-connected-service-and-add-account"></a>Dodawanie usługi połączonej i dodawanie konta
1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy węzeł projektu, a następnie wybierz pozycje **Dodaj** / **Usługa połączona**. Lub kliknij menu **Projekt**, a następnie wybierz pozycję **Dodaj usługę połączoną**.

    ![Zrzut ekranu przedstawiający okno dodawania usługi połączonej](./media/connected-service/connected-service-add-connectedservice-rightclick.png)
2. Na stronie usługi połączonej kliknij pozycje **Usługi połączone** / **Azure Cosmos DB**, aby otworzyć stronę usługi **Azure Cosmos DB**.

    ![Zrzut ekranu przedstawiający okno usługi Azure Cosmos DB](./media/connected-service/connected-service-choose-azure-cosmosdb.png)
3. Kliknij strzałkę w dół, aby zalogować się po raz pierwszy lub dodać konto. Po zalogowaniu się wszystkie konta usługi Azure Cosmos DB będą wyświetlane w pustym obszarze. Wybierz jedno konto usługi Azure Cosmos DB, które ma zostać dodane do projektu.

    ![Zrzut ekranu przedstawiający okno logowania i listy kont bazy danych](./media/connected-service/connected-service-add-db-account.png)
4. Po dodaniu konta usługi Azure Cosmos DB folder usługi połączonej konta Azure Cosmos DB jest dodawany do projektu. Możesz dodać więcej niż jedno konto usługi Azure Cosmos DB, powtarzając kroki od 1 do 3.

    ![Zrzut ekranu przedstawiający okno folderu usługi połączonej](./media/connected-service/connected-service-add-connectedservice-folder.png)

5. Po dodaniu usługi połączonej usługi Azure Cosmos DB zmodyfikuj swój projekt, aby umożliwić dostęp do usługi Azure Cosmos DB w jeden z następujących sposobów:

    * Instalowane są niektóre pakiety NuGet, wymagane przez klienta usługi Azure Cosmos DB. Widać je w pliku konfiguracji pakietów. 

        ![Nowa strona konfiguracji pakietów usługi Azure Cosmos DB](./media/connected-service/connected-service-packages-config.png)   
    
    * Identyfikator URI i klucz połączenia usługi Azure Cosmos DB są dodawane do pliku konfiguracji projektu, w tym przypadku App.config. 

        ![Nowa konfiguracja aplikacji usługi Azure Cosmos DB](./media/connected-service/connected-service-app-config.png) 

## <a name="open-azure-cosmos-db-explorer"></a>Otwieranie eksploratora usługi Azure Cosmos DB
1. Kliknij prawym przyciskiem myszy węzeł projektu i wybierz pozycję **Open Cosmos DB Explorer...** (Otwórz eksplorator usługi Cosmos DB).

    ![Zrzut ekranu przedstawiający próbę otwarcia okna Eksploratora danych](./media/connected-service/connected-service-right-click-open-data-exporer.png)
2. Na stronie **Wybieranie konta usługi Cosmos DB** kliknij listę rozwijaną, aby wybrać jedno konto usługi Azure Cosmos DB.

    ![Zrzut ekranu przedstawiający okno usługi połączonej dodanego konta](./media/connected-service/connected-service-open-explorer.png)
3. Kliknij przycisk **Otwórz**, aby wyświetlić okno Eksploratora danych.

## <a id="Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool"></a>Tworzenie konta usługi Azure Cosmos DB w narzędziu usługi połączonej
1. Na stronie usługi połączonej, w lewym dolnym rogu okienka kliknij pozycję **Utwórz nowe konto usługi Cosmos DB**, aby otworzyć stronę **Tworzenie konta usługi Cosmos DB**.

    ![Zrzut ekranu przedstawiający otwarte okno punktu wejścia tworzenia konta usługi Azure Cosmos DB](./media/connected-service/connected-service-click-new-db-account.png)
2. Na stronie **Tworzenie konta usługi Cosmos DB** określ żądaną konfigurację dla tego konta usługi Azure Cosmos DB.

    Wypełnij pola na stronie **Tworzenie konta usługi Cosmos DB**, korzystając z informacji przedstawionych na poniższym zrzucie ekranu jako wskazówki. 
 
    ![Nowa strona usługi Azure Cosmos DB](./media/connected-service/connected-service-create-new-account.png)        
3. Kliknij przycisk **Utwórz**, aby utworzyć konto.

## <a name="use-data-explorer"></a>Korzystanie z Eksploratora danych

Po otwarciu Eksploratora danych możesz wykonywać następujące czynności:
* Tworzenie i usuwanie baz danych
* Tworzenie i usuwanie kolekcji
* Tworzenie, usuwanie i filtrowanie dokumentów
* Tworzenie i usuwanie procedur składowanych
* Tworzenie i usuwanie wyzwalaczy i funkcji zdefiniowanych przez użytkownika do wykonywania logiki biznesowej po stronie serwera. 

![Nowa strona usługi Azure Cosmos DB](./media/connected-service/connected-service-dataexplorerui.png)

## <a name="demo"></a>Demonstracja

Obejrzyj następujące filmy wideo, aby zobaczyć, jak używać usługi połączonej usługi Azure Cosmos DB w programie Visual Studio: [Korzystanie z usługi połączonej usługi Azure Cosmos DB w programie Visual Studio](https://go.microsoft.com/fwlink/?linkid=858711)

## <a name="next-steps"></a>Następne kroki
W tym dokumencie omówiono następujące procedury:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos DB
> * Dodawanie usługi połączonej i dodawanie konta
> * Otwieranie eksploratora usługi Azure Cosmos DB
> * Korzystanie z Eksploratora danych

Teraz, gdy masz już działające usługi połączone z Twoim kontem usługi Azure Cosmos DB, przejdź do jednego z samouczków, aby rozpocząć tworzenie rozwiązania:

* [Tworzenie przy użyciu interfejsu API SQL w .NET](tutorial-develop-sql-api-dotnet.md).
* [Azure Cosmos DB: Rozpoczęcie pracy z interfejsem API SQL — samouczek](sql-api-get-started.md).
* Czy chcesz wykonać testowanie wydajnościowe i skalowania przy użyciu usługi Azure Cosmos DB? Zobacz [Performance and Scale Testing with Azure Cosmos DB](performance-testing.md) (Testowanie wydajności i skali w usłudze Azure Cosmos DB)
* Dowiedz się, jak [monitorować konto usługi Azure Cosmos DB](monitor-accounts.md).

