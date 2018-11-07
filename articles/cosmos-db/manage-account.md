---
title: Zarządzanie kontem usługi Azure Cosmos DB w witrynie Azure Portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać kontem usługi Azure Cosmos DB w witrynie Azure Portal. Przewodnik na temat korzystania z witryny Azure Portal do wyświetlania, kopiowania, usuwania i dostęp do kont.
keywords: Azure Portal, azure, Microsoft azure
services: cosmos-db
author: kirillg
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: kirillg
ms.openlocfilehash: abcf51c6bd196c2ffb0bb35e2df161531a53972d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229394"
---
# <a name="how-to-manage-an-azure-cosmos-db-account"></a>Jak zarządzać kontem usługi Azure Cosmos DB
Dowiedz się, jak ustawienie globalne spójności, pracować z kluczami i usunąć konto usługi Azure Cosmos DB w witrynie Azure portal.

## <a id="consistency"></a>Zarządzanie ustawieniami spójności usługi Azure Cosmos DB
Wybór poziomu spójności prawo zależy od semantyki aplikacji. Zapoznaj się z poziomów spójności dostępne w usłudze Azure Cosmos DB, czytając [maksymalizowanie dostępności i wydajności w usłudze Azure Cosmos DB za pomocą poziomów spójności][consistency]. Usługa Azure Cosmos DB zapewnia spójnością, dostępnością i gwarancji wydajności, poziomu spójności, co jest dostępny dla Twojego konta bazy danych. Konfigurowanie Twojego konta bazy danych z poziomem silnej spójności wymaga, że Twoje dane są zamkniętej w pojedynczym regionie platformy Azure i nie jest dostępna globalnie. Z drugiej strony, poziomy rozluźnionej spójności — powiązana nieaktualność, sesja lub ostateczna Włącz można skojarzyć dowolną liczbę regionów platformy Azure z Twojego konta bazy danych. Następujące proste kroki pokazują, jak wybrać domyślny poziom spójności dla Twojego konta bazy danych.

### <a name="to-specify-the-default-consistency-for-an-azure-cosmos-db-account"></a>Aby określić domyślna spójność konta usługi Azure Cosmos DB
1. W [witryny Azure portal](https://portal.azure.com/), dostęp do tego konta usługi Azure Cosmos DB.
2. Na stronie konta kliknij **domyślna spójność**.
3. W **ustawienie domyślna spójność** stronie, wybierz nowy poziom spójności i kliknij **Zapisz**.
    ![Domyślna spójność sesji][5]

## <a id="keys"></a>Wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu i haseł
Podczas tworzenia konta usługi Azure Cosmos DB, Usługa generuje dwa klucze dostępu do głównego (lub dwa hasła dla kont usługi interfejsu API usługi MongoDB) mogą służyć do uwierzytelniania podczas uzyskiwania dostępu do konta usługi Azure Cosmos DB. Zapewniając dwa klucze dostępu, usługi Azure Cosmos DB pozwala na ponowne generowanie kluczy nie zakłóceń z kontem usługi Azure Cosmos DB. 

W [witryny Azure portal](https://portal.azure.com/), dostępu **klucze** stronie z menu zasobów **konta usługi Azure Cosmos DB** strony, aby wyświetlić, skopiować lub ponownie wygenerować klucze dostępu, które są używane do dostęp do konta usługi Azure Cosmos DB. W przypadku interfejsu API usługi MongoDB kont dostępu **parametry połączenia** strony z menu zasobów, aby wyświetlić, skopiować lub ponownie wygenerować hasła, które są używane do uzyskania dostępu do konta.

![Azure portal zrzucie ekranu widać, strona klucze](./media/manage-account/keys.png)

> [!NOTE]
> **Klucze** strona zawiera także parametry połączenia podstawowego i pomocniczego, używane do łączenia się z kontem z [narzędzia migracji danych](import-data.md).
> 
> 

Klucze tylko do odczytu są również dostępne na tej stronie. Odczyty i zapytania w przypadku operacji tylko do odczytu podczas tworzy, usuwa, a nie są zastępowane.

### <a name="copy-an-access-key-or-password-in-the-azure-portal"></a>Skopiuj hasła lub klucza dostępu w witrynie Azure portal
Na **klucze** strony (lub **parametry połączenia** strona konta interfejsu API usługi MongoDB), kliknij przycisk **kopiowania** przycisk po prawej stronie klucza lub hasła, które chcesz skopiować.

![Wyświetlanie i kopiowanie klucza dostępu w witrynie Azure Portal, strona Klucze](./media/manage-account/copykeys.png)

### <a name="regenerate-access-keys-and-passwords"></a>Ponowne generowanie kluczy dostępu i hasła
Należy zmienić klawisze dostępu (i hasła dla konta interfejsu API usługi MongoDB) do swojego konta usługi Azure Cosmos DB okresowo do zabezpieczania połączeń. Dwa klucze dostępu/hasła są przypisywane umożliwia utrzymanie połączeń do konta usługi Azure Cosmos DB przy użyciu jednego klucza dostępu, jednocześnie ponownie generując drugi klucz dostępu.

> [!WARNING]
> Trwa ponowne generowanie kluczy dostępu ma wpływ na wszystkie aplikacje, które są zależne od bieżącego klucza. Wszyscy klienci, którzy używają klucza dostępu do uzyskania dostępu do konta usługi Azure Cosmos DB musi zostać zaktualizowany w celu używania nowego klucza.
> 
> 

Jeśli masz aplikacje lub usługi w chmurze przy użyciu konta usługi Azure Cosmos DB, utracisz połączenia w przypadku ponownego generowania kluczy, chyba że wdrożysz klucze. Poniższe kroki opisują proces stopniowego kluczy/haseł.

1. Aktualizuj klucz dostępu, w kodzie aplikacji, aby odwołać pomocniczy klucz dostępu konta usługi Azure Cosmos DB.
2. Ponownie wygenerować podstawowy klucz dostępu dla konta usługi Azure Cosmos DB. W [witryny Azure portal](https://portal.azure.com/), dostęp do tego konta usługi Azure Cosmos DB.
3. W **konta usługi Azure Cosmos DB** kliknij **klucze** (lub **parametry połączenia** dla konta bazy danych MongoDB **).
4. Na **klucze**/**parametry połączenia** stronie, kliknij przycisk Wygeneruj ponownie, a następnie kliknij przycisk **Ok** aby upewnić się, że chcesz wygenerować nowy klucz.
    ![Wygeneruj ponownie klucze dostępu](./media/manage-account/regenerate-keys.png)
5. Po upewnieniu się, że nowy klucz jest dostępny do użytku (około pięciu minut od ponownego wygenerowania), należy zaktualizować klucz dostępu w kodzie aplikacji, aby odwoływać się do nowego podstawowego klucza dostępu.
6. Wygeneruj ponownie pomocniczy klucz dostępu.
   
    ![Wygeneruj ponownie klucze dostępu](./media/manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Może upłynąć kilka minut, zanim nowo wygenerowane klucza może służyć do uzyskania dostępu do konta usługi Azure Cosmos DB.
> 
> 

## <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia
Aby pobrać parametry połączenia, wykonaj następujące czynności: 

1. W [witryny Azure portal](https://portal.azure.com), dostęp do tego konta usługi Azure Cosmos DB.
2. W menu zasobów kliknij pozycję **klucze** (lub **parametry połączenia** dla kont usługi interfejsu API usługi MongoDB).
3. Kliknij przycisk **kopiowania** znajdujący się obok **podstawowe parametry połączenia** lub **pomocnicze parametry połączenia** pole. 

Jeśli używane są parametry połączenia w [narzędzie migracji usługi Azure Cosmos DB bazy danych](import-data.md), Dodaj nazwę bazy danych do końca ciągu połączenia. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a id="delete"></a> Usuwanie konta usługi Azure Cosmos DB
Aby usunąć konto usługi Azure Cosmos DB w witrynie Azure Portal, który już nie korzystasz, kliknij prawym przyciskiem myszy nazwę konta, a następnie kliknij przycisk **Usuń konto**.

![Jak usunąć konto usługi Azure Cosmos DB w witrynie Azure portal](./media/manage-account/deleteaccount.png)

1. W [witryny Azure portal](https://portal.azure.com/), dostęp do konta usługi Azure Cosmos DB, które chcesz usunąć.
2. Na **konta usługi Azure Cosmos DB** stronie, kliknij prawym przyciskiem myszy konto, a następnie kliknij **Usuń konto**. 
3. Na stronie Potwierdzenie wynikowy wpisz nazwę konta usługi Azure Cosmos DB, aby upewnić się, że chcesz usunąć konto.
4. Kliknij przycisk **Usuń** przycisku.

![Jak usunąć konto usługi Azure Cosmos DB w witrynie Azure portal](./media/manage-account/delete-account-confirm.png)

## <a id="next"></a>Następne kroki
Dowiedz się, jak [Rozpoczynanie pracy z Twoim kontem usługi Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkId=402364).

<!--Image references-->
[5]: ./media/manage-account/documentdb_change_consistency-1.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
