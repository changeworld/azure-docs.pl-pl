---
title: Łączenie się z usługą Azure Cosmos DB za pomocą programu Robo 3T
description: Dowiedz się, jak połączyć się z usługą Azure Cosmos DB przy użyciu interfejsu API usługi Robo 3T i usługi Azure Cosmos DB dla usługi MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 627849acb902a721c80b98adbd16103b4729591b
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114189"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Używanie programu Robo 3T za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB

Aby połączyć się z kontem usługi Cosmos przy użyciu robo 3T, należy:

* Pobierz i zainstaluj [Robo 3T](https://robomongo.org/)
* Mieć informacje o [ciągu połączenia usługi](connect-mongodb-account.md) Cosmos DB

> [!NOTE]
> Obecnie Robo 3T v1.2 i niższe wersje są obsługiwane przez interfejs API Usługi Cosmos DB dla MongoDB.

## <a name="connect-using-robo-3t"></a>Łączenie za pomocą programu Robo 3T

Aby dodać konto usługi Cosmos do menedżera połączeń Robo 3T, wykonaj następujące czynności:

1. Pobierz informacje o połączeniu dla konta usługi Cosmos skonfigurowane za pomocą usługi Azure Cosmos DB interfejsu API MongoDB przy użyciu instrukcji [tutaj](connect-mongodb-account.md).

    ![Zrzut ekranu przedstawiający blok ciągu połączenia](./media/mongodb-robomongo/connectionstringblade.png)
2. Uruchom aplikację *Robomongo.*

3. Kliknij przycisk połączenia w obszarze **Plik,** aby zarządzać połączeniami. Następnie kliknij przycisk **Utwórz** w oknie **Połączenia MongoDB,** które otworzy okno **Ustawienia połączenia.**

4. W oknie **Ustawienia połączenia** wybierz nazwę. Następnie znajdź **hosta** i **port** na podstawie informacji o połączeniu w kroku 1 i wprowadź je odpowiednio do **adresu** i **portu.**

    ![Zrzut ekranu przedstawiający robomongo zarządzanie połączeniami](./media/mongodb-robomongo/manageconnections.png)
5. Na karcie **Uwierzytelnianie** kliknij pozycję **Wykonaj uwierzytelnianie**. Następnie wprowadź bazę danych (domyślnie jest *to Administrator),* **Nazwa użytkownika** i **Hasło**.
Zarówno **nazwa użytkownika,** jak i **hasło** można znaleźć w informacjach o połączeniu w kroku 1.

    ![Zrzut ekranu przedstawiający kartę uwierzytelniania Robomongo](./media/mongodb-robomongo/authentication.png)
6. Na karcie **SSL** zaznacz pole wyboru **Użyj protokołu SSL**, a następnie zmień **metodę uwierzytelniania** na **Certyfikat z podpisem własnym**.

    ![Zrzut ekranu przedstawiający kartę SSL Robomongo](./media/mongodb-robomongo/SSL.png)
7. Na koniec kliknij przycisk **Testuj,** aby sprawdzić, czy można się połączyć, a następnie **zapisz**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
