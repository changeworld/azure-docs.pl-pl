---
title: Korzystanie z narzędzia Robomongo dla usługi Azure Cosmos DB
description: 'Dowiedz się, jak korzystać z narzędzia Robomongo za pomocą usługi Azure Cosmos DB: interfejs API dla konta bazy danych MongoDB'
keywords: robomongo
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: sngun
ms.openlocfilehash: 78f0158c9a80a60717b81b4788531c7efd979111
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863809"
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Korzystanie z narzędzia Robomongo za pomocą usługi Azure Cosmos DB: interfejs API dla konta bazy danych MongoDB
Aby nawiązać połączenie z usługą Azure Cosmos DB: interfejs API dla konta bazy danych MongoDB, korzystanie z programu Robomongo, musi:

* Pobierz i zainstaluj [z narzędzia Robomongo](https://robomongo.org/)
* Masz usługi Azure Cosmos DB: interfejs API dla konta bazy danych MongoDB [parametry połączenia](connect-mongodb-account.md) informacji

## <a name="connect-using-robomongo"></a>Łączenie za pomocą programu Robomongo
Aby dodać usługi Azure Cosmos DB: interfejs API dla konta bazy danych MongoDB z połączeniami bazy danych MongoDB z narzędzia Robomongo, wykonaj następujące kroki.

1. Pobieranie usługi Azure Cosmos DB: interfejs API, aby uzyskać informacje o połączeniu konta bazy danych MongoDB przy użyciu instrukcji [tutaj](connect-mongodb-account.md).

    ![Zrzut ekranu przedstawiający blok parametrów połączenia](./media/mongodb-robomongo/connectionstringblade.png)
2. Uruchom *Robomongo.exe*

3. Kliknij przycisk połączenie w obszarze **pliku** do zarządzania połączeniami. Następnie kliknij przycisk **Utwórz** w **połączenia bazy danych MongoDB** okno, które zostanie otwarty **ustawienia połączenia** okna.

4. W **ustawienia połączenia** okna, wybierz nazwę. Następnie, Znajdź **hosta** i **portu** z informacje o połączeniu w kroku 1, a następnie wprowadź je do **adres** i **portu**odpowiednio.

    ![Zrzut ekranu przedstawiający Zarządzanie połączeniami z narzędzia Robomongo](./media/mongodb-robomongo/manageconnections.png)
5. Na **uwierzytelniania** kliknij pozycję **Przeprowadź uwierzytelnianie**. Następnie wprowadź bazy danych (wartość domyślna to *administratora*), **nazwa_użytkownika** i **hasło**.
Zarówno **nazwa_użytkownika** i **hasło** znajdują się informacje o połączeniu w kroku 1.

    ![Zrzut ekranu: karta uwierzytelniania z programu Robomongo](./media/mongodb-robomongo/authentication.png)
6. Na **SSL** karcie wyboru **Użyj protokołu SSL**, następnie zmienić **metodę uwierzytelniania** do **certyfikatu z podpisem własnym**.

    ![Zrzut ekranu: karta SSL z narzędzia Robomongo](./media/mongodb-robomongo/SSL.png)
7. Na koniec kliknij **testu** Aby zweryfikować, że jesteś w stanie nawiązać połączenie, następnie **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki
* Poznaj usługę Azure Cosmos DB: Interfejs API systemu MongoDB [przykłady](mongodb-samples.md).
