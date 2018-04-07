---
title: Użyj Robomongo dla rozwiązania Cosmos Azure DB | Dokumentacja firmy Microsoft
description: 'Dowiedz się, jak korzystać z bazy danych Azure rozwiązania Cosmos Robomongo: interfejsu API dla konta bazy danych MongoDB'
keywords: robomongo
services: cosmos-db
author: AndrewHoh
manager: kfile
documentationcenter: ''
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: anhoh
ms.openlocfilehash: 7d318880b7b0078e4c03acb66885f4aed5534ba1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Użyj Robomongo z bazy danych Azure rozwiązania Cosmos: interfejsu API dla konta bazy danych MongoDB
Do nawiązania połączenia bazy danych Azure rozwiązania Cosmos: interfejs API dla konta bazy danych MongoDB przy użyciu Robomongo, musi:

* Pobierz i zainstaluj [Robomongo](https://robomongo.org/)
* Z bazy danych rozwiązania Cosmos Azure ma: interfejsu API dla konta bazy danych MongoDB [ciąg połączenia](connect-mongodb-account.md) informacji

## <a name="connect-using-robomongo"></a>Połącz przy użyciu Robomongo
Aby dodać bazy danych programu Azure rozwiązania Cosmos: interfejsu API dla konta bazy danych MongoDB do połączeń bazy danych MongoDB Robomongo, wykonaj następujące kroki.

1. Pobieranie programu Azure DB rozwiązania Cosmos: interfejsu API, bazy danych MongoDB informacji o koncie połączenia przy użyciu instrukcji [tutaj](connect-mongodb-account.md).

    ![Zrzut ekranu przedstawiający blok ciągu połączenia](./media/mongodb-robomongo/connectionstringblade.png)
2. Uruchom *Robomongo.exe*

3. Kliknij przycisk połączenia, w obszarze **pliku** do zarządzania połączeniami. Następnie kliknij przycisk **Utwórz** w **połączenia bazy danych MongoDB** okna, które spowoduje to otwarcie **ustawienia połączenia** okna.

4. W **ustawienia połączenia** okna, wybierz nazwę. Następnie, Znajdź **hosta** i **portu** z informacje o połączeniu w kroku 1, a następnie wprowadź je do **adres** i **portu**odpowiednio.

    ![Zrzut ekranu przedstawiający Robomongo Zarządzanie połączeniami](./media/mongodb-robomongo/manageconnections.png)
5. Na **uwierzytelniania** , kliknij pozycję **uwierzytelniania wykonaj**. Następnie wprowadź bazy danych (domyślna to *Admin*), **nazwy użytkownika** i **hasło**.
Zarówno **nazwy użytkownika** i **hasło** znajdują się informacje o połączeniu w kroku 1.

    ![Zrzut ekranu: karta uwierzytelniania Robomongo](./media/mongodb-robomongo/authentication.png)
6. Na **SSL** karcie wyboru **protokołu SSL używany**, następnie zmienić **metodę uwierzytelniania** do **certyfikatu z podpisem własnym**.

    ![Zrzut ekranu: karta SSL Robomongo](./media/mongodb-robomongo/SSL.png)
7. Na koniec kliknij **testu** do Sprawdź, czy można nawiązać połączenia, następnie **zapisać**.

## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się z rozwiązania Cosmos Azure DB: Interfejs API, bazy danych mongodb [przykłady](mongodb-samples.md).
