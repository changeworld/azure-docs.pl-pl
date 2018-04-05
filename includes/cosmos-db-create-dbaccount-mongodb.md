---
title: Tworzenie konta interfejsu API MongoDB usługi Azure Cosmos DB
description: W tym artykule opisano sposób tworzenia konta interfejsu API MongoDB usługi Azure Cosmos DB w witrynie Azure Portal
services: cosmos-db
author: mimig1
ms.service: cosmos-db
ms.topic: include
ms.date: 03/20/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 02ea0e011642313b885bc48ec48104fa2789da81
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
1. W nowym oknie zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie kliknij pozycję **Utwórz zasób**, kliknij pozycję **Bazy danych**, a następnie w obszarze **Azure Cosmos DB** kliknij pozycję **Utwórz**.
   
   ![Zrzut ekranu przedstawiający witrynę Azure Portal z wyróżnionymi poleceniami Więcej usług i Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. W bloku **Nowe konto** określ bazę danych **MongoDB** jako interfejs API i wprowadź żądaną konfigurację dla konta usługi Azure Cosmos DB.
 
    ![Zrzut ekranu bloku Nowa usługa Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-2.png)

    * **Identyfikator** musi być unikatową nazwą, której chcesz użyć do identyfikowania swojego konta usługi Azure Cosmos DB. Może on zawierać tylko małe litery, cyfry i znak „-” oraz musi mieć długość od 3 do 50 znaków.
    * **Subskrypcja** to Twoja subskrypcja platformy Azure. Zostanie ona wypełniona automatycznie.
    * **Grupa zasobów** to nazwa grupy zasobów dla Twojego konta usługi Azure Cosmos DB.
    * **Lokalizacja** to lokalizacja geograficzna, gdzie znajduje się wystąpienie usługi Azure Cosmos DB. Wybierz lokalizację znajdującą się najbliżej użytkowników.

4. Kliknij przycisk **Utwórz**, aby utworzyć konto.
5. Na pasku narzędzi kliknij pozycję **Powiadomienia**, aby monitorować proces wdrażania.

    ![Powiadomienie Wdrażanie rozpoczęte](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-nosql-notification.png)

6.  Po zakończeniu wdrażania otwórz nowe konto przy użyciu kafelka Wszystkie zasoby. 

    ![Konto usługi Azure Cosmos DB na kafelku Wszystkie zasoby](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-all-resources.png)
