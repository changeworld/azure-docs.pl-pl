---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: a91c42ca32fb356b418dcd412c0690b01ff85789
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183772"
---
Teraz możesz użyć narzędzia Eksplorator danych w witrynie Azure Portal, aby utworzyć bazę danych i tabelę. 

1. Kliknij pozycję **Eksplorator danych** > **Nowa tabela**. 
    
    Obszar **Dodaj tabelę** zostanie wyświetlony po prawej stronie. Aby go zobaczyć, być może trzeba będzie przewinąć w prawo.

    ![Eksplorator danych w witrynie Azure Portal](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. Na stronie **Dodawanie tabeli** wprowadź ustawienia dla nowej tabeli.

    Ustawienie|Sugerowana wartość|Opis
    ---|---|---
    Identyfikator tabeli|sample-table|Identyfikator nowej tabeli. W przypadku nazw tabel obowiązują takie same wymagania dotyczące znaków, jak dla identyfikatorów baz danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków `/ \ # ?` ani mieć spacji na końcu.
    Przepływność|400 jednostek żądania|Zmień przepływność na 400 jednostek żądania na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę.

3. Kliknij przycisk **OK**.

4. W Eksploratorze danych zostanie wyświetlona nowa baza danych i tabela.

   ![Eksplorator danych w witrynie Azure Portal z widoczną nową bazą danych i kolekcją](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)