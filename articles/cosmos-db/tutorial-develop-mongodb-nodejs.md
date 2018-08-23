---
title: Samouczek bazy danych MongoDB, usługi Angular i języka Node dla platformy Azure | Microsoft Doc
description: Dowiedz się, jak utworzyć aplikację bazy danych MongoDB w usłudze Angular i języku Node dla usługi Azure Cosmos DB przy użyciu tych samych interfejsów API, co używane w usłudze MongoDB, oglądając tę serię samouczków opartą na filmach wideo.
services: cosmos-db
author: johnpapa
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: jopapa
ms.custom: mvc
ms.openlocfilehash: 1a9d608e7f959b3fc164f87d408ccd268e8d2568
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "41919953"
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db"></a>Tworzenie aplikacji bazy danych MongoDB przy użyciu usług Angular i Azure Cosmos DB 

Ten wieloczęściowy samouczek pokazuje, jak utworzyć nową aplikację [interfejsu API bazy danych MongoDB](mongodb-introduction.md) za pomocą programu Express, usługi Angular i języka Node.js (stos MEAN), a następnie podłączyć ją do bazy danych usługi Azure Cosmos DB. Usługa Azure Cosmos DB obsługuje połączenia klienta bazy danych MongoDB, dlatego można używać usługi Azure Cosmos DB zamiast bazy danych MongoDB, ale stosując ten sam kod już używany w dla aplikacji bazy danych MongoDB. Zyskuje się w ten sposób dodatkowe korzyści związane z usługą Azure Cosmos DB, takie jak proste wdrażanie w chmurze, skalowanie, dane replikowane globalne, obsługa wielu modeli oraz niezwykle szybkie operacje odczytu i zapisu. 

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu dystrybucji globalnej i możliwości skalowania poziomego opartego na usłudze Azure Cosmos DB można szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość i grafów. 

W tym wieloczęściowym samouczku opisano następujące zadania:

> [!div class="checklist"]
> * [Tworzenie aplikacji Node.js Express za pomocą interfejsu wiersza polecenia usługi Angular](tutorial-develop-mongodb-nodejs-part2.md)
> * [Tworzenie interfejsu użytkownika przy użyciu usługi Angular](tutorial-develop-mongodb-nodejs-part3.md)
> * [Tworzenie konta usługi Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure](tutorial-develop-mongodb-nodejs-part4.md) 
> * [Łączenie z usługą Azure Cosmos DB przy użyciu wtyczki Mongoose](tutorial-develop-mongodb-nodejs-part5.md)
> * [Dodawanie funkcji Post, Put i Delete do aplikacji](tutorial-develop-mongodb-nodejs-part6.md)

Chcesz skompilować tę samą aplikację na platformie React? Zobacz [samouczek platformy React w postaci serii filmów wideo](tutorial-develop-mongodb-react.md).

## <a name="video-walkthrough"></a>Przewodnik wideo

> [!VIDEO https://www.youtube.com/embed/vlZRP0mDabM]

## <a name="finished-project"></a>Zakończony projekt 

Ten samouczek przedstawia instrukcje tworzenia aplikacji krok po kroku. Jeśli chcesz pobrać gotowy projekt, ukończoną aplikację znajdziesz w [repozytorium angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) w witrynie GitHub.

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zostały wykonane następujące zadania:

> [!div class="checklist"]
> * Zapoznanie się z omówieniem kroków wymaganych do utworzenia aplikacji MEAN.js przy użyciu usługi Azure Cosmos DB. 

Możesz przejść do następnej części samouczka, aby utworzyć aplikację Node.js Express.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji Node.js Express za pomocą interfejsu wiersza polecenia usługi Angular](tutorial-develop-mongodb-nodejs-part2.md)
