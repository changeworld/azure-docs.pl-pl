---
title: Samouczek bazy danych MongoDB, usługi Angular i języka Node dla platformy Azure — część 4 | Microsoft Doc
description: Część 4 z serii samouczków o tworzeniu aplikacji bazy danych MongoDB przy użyciu usługi Angular i języka Node dla usługi Azure Cosmos DB przy użyciu dokładnie tych samych interfejsów API, które były używane dla bazy danych MongoDB
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: e06a88ab54ffd87d1071136b53085c32bfcd8c78
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38667835"
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-4-create-an-azure-cosmos-db-account-using-the-azure-cli"></a>Tworzenie aplikacji bazy danych MongoDB przy użyciu usług Angular i Azure Cosmos DB — część 4: tworzenie konta usługi Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure

Ten wieloczęściowy samouczek pokazuje, jak utworzyć nową aplikację [interfejsu API bazy danych MongoDB](mongodb-introduction.md) napisaną w języku w Node.js przy użyciu programu Express oraz usług Angular i Azure Cosmos DB.

Część 4 samouczka jest oparta na [Części 3](tutorial-develop-mongodb-nodejs-part3.md) i obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie grupy zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
> * Tworzenie konta usługi Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure

## <a name="video-walkthrough"></a>Przewodnik wideo

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tej części samouczka upewnij się, że zostały wykonane czynności opisane w [części 3](tutorial-develop-mongodb-nodejs-part3.md) samouczka. 

W tej sekcji samouczka możesz użyć usługi Azure Cloud Shell (w przeglądarce internetowej) lub zainstalowanego lokalnie [interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure, pamiętaj, aby później uruchomić interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version` w wierszu polecenia, aby sprawdzić swoją wersję. 

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Ten samouczek przedstawia instrukcje tworzenia aplikacji krok po kroku. Jeśli chcesz pobrać gotowy projekt, ukończoną aplikację znajdziesz w [repozytorium angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) w witrynie GitHub.

## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Utwórz konto usługi Azure Cosmos DB za pomocą polecenia [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create).

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* Jako elementu `<cosmosdb-name>` użyj własnej unikatowej nazwy konta usługi Azure Cosmos DB. Nazwa musi być unikatowa w obrębie wszystkich nazw kont usługi Azure Cosmos DB na platformie Azure.
* Ustawienie `--kind MongoDB` umożliwia usłudze Azure Cosmos DB korzystanie z połączeń klienckich bazy danych MongoDB.

Wykonanie polecenia może potrwać minutę lub dwie. Po zakończeniu w oknie terminala zostaną wyświetlone informacje o nowej bazie danych. 

Po utworzeniu konta usługi Azure Cosmos DB:
1. Otwórz nowe okno przeglądarki i przejdź do witryny [https://portal.azure.com](https://portal.azure.com)
1. Kliknij logo usługi Azure Cosmos DB ![Ikona usługi Azure Cosmos DB w witrynie Azure Portal](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png) na pasku po lewej stronie. Zostaną wyświetlone wszystkie istniejące bazy danych Azure Cosmos DB.
1. Kliknij właśnie utworzone konto usługi Azure Cosmos DB, wybierz kartę **Omówienie** i przewiń w dół, aby na mapie wyświetlić lokalizację bazy danych. 

    ![Nowe konto usługi Azure Cosmos DB w witrynie Azure Portal](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png)

4. Przewiń w dół w lewym obszarze nawigacji i kliknij kartę **Replikuj dane globalnie**. Spowoduje to wyświetlenie mapy, na której można zobaczyć różne potencjalne docelowe obszary replikacji. Można na przykład kliknąć region Australia Południowo-Wschodnia lub Australia Wschodnia i replikować dane do klientów w Australii. Więcej informacji o replikacji globalnej można znaleźć w temacie [How to distribute data globally with Azure Cosmos DB (Jak dystrybuować dane globalnie przy użyciu usługi Azure Cosmos DB)](distribute-data-globally.md). Na razie zajmijmy się jednym wystąpieniem, które w razie potrzeby możemy zreplikować, ponieważ już znamy sposób.

    ![Nowe konto usługi Azure Cosmos DB w witrynie Azure Portal](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png)

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zostały wykonane następujące zadania:

> [!div class="checklist"]
> * Utworzono grupę zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
> * Utworzono konto usługi Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure

Możesz przejść do następnej części samouczka, aby połączyć usługę Azure Cosmos DB z aplikacją przy użyciu wtyczki Mongoose.

> [!div class="nextstepaction"]
> [Łączenie z usługą Azure Cosmos DB przy użyciu wtyczki Mongoose](tutorial-develop-mongodb-nodejs-part5.md)
