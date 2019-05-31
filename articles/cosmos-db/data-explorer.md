---
title: Zarządzanie danymi za pomocą Eksploratora usługi Azure Cosmos DB
description: Eksplorator usługi Azure Cosmos DB jest autonomiczny opartego na sieci web interfejs, który umożliwia wyświetlanie i zarządzanie nimi danych przechowywanych w usłudze Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 04cfdd1f96f83898710b6f292116f0afddc8df96
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237233"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Praca z danymi za pomocą eksploratora usługi Azure Cosmos 

Eksplorator usługi Azure Cosmos DB jest autonomiczny opartego na sieci web interfejs, który umożliwia wyświetlanie i zarządzanie nimi danych przechowywanych w usłudze Azure Cosmos DB. Eksplorator usługi Azure Cosmos DB jest odpowiednikiem istniejące **Eksplorator danych** karta, która jest dostępna w witrynie Azure portal podczas tworzenia konta usługi Azure Cosmos DB. Kluczowe zalety Eksploratora usługi Azure Cosmos DB za pośrednictwem istniejących Eksplorator danych są następujące:

* Masz pełną-powierzchnię ekranu do wyświetlania danych, wykonywania zapytań, procedury składowane, wyzwalacze i obejrzeć ich wyniki.  

* Można zapewnić dostęp do odczytu i zapisu lub odczytu tymczasowy lub stały Twojego konta bazy danych i jego kolekcjach do innych użytkowników, którzy nie mają dostępu do witryny Azure portal lub subskrypcji.  

* Wyniki zapytania można udostępniać innym użytkownikom, którzy nie mają dostępu do witryny Azure portal lub subskrypcji.  

## <a name="access-azure-cosmos-db-explorer"></a>Dostęp do usługi Azure Cosmos DB explorer

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/). 

2. Z **wszystkie zasoby**, Znajdź i przejdź do konta usługi Azure Cosmos DB, wybierz opcję klucze i kopiowania **podstawowe parametry połączenia**.  

3. Przejdź do https://cosmos.azure.com/, Wklej parametry połączenia i wybierz **Connect**. Za pomocą parametrów połączenia, mogą uzyskać dostęp do Eksploratora usługi Azure Cosmos DB bez żadnych ograniczeń czasowych.  

   Do innych udzielania tymczasowego dostępu do konta usługi Azure Cosmos DB, możesz to zrobić za pomocą odczytu / zapisu i odczytu adresów URL. 

4. Otwórz **Eksplorator danych** bloku wybierz **Otwórz pełny ekran**. W wyskakującym oknie dialogowym można wyświetlić dwa adresy URL — dostępu do **odczytu i zapisu** i **odczytu**. Te adresy URL umożliwiają udostępnianie Twojego konta usługi Azure Cosmos DB tymczasowo innym użytkownikom. Dostęp do konta wygasa w ciągu 24 godzin, po których można ponownie połączyć się przy użyciu nowego adresu URL dostępu lub parametry połączenia. 

   **Odczytu i zapisu** — w przypadku adresu URL odczytu i zapisu są udostępniane innym użytkownikom, mogą wyświetlać i modyfikować baz danych, kolekcji, zapytań i innych zasobów skojarzonych z tego konta.

   **Odczyt** — możesz udostępniać innym użytkownikom, adres URL tylko do odczytu mogą wyświetlać baz danych, kolekcje, kwerendy i innych zasobów skojarzonych z tym kontem określonym. Na przykład jeśli użytkownik chce udostępnić wyniki zapytania z członkami zespołu, którzy nie mają dostępu do witryny Azure portal lub konta usługi Azure Cosmos DB, możesz zapewnić im tego adresu URL.

   Wybierz typ dostępu, które chcesz otworzyć konto za pomocą, a następnie kliknij przycisk **Otwórz**. Po otwarciu Eksploratora, proces jest taka sama, jak związany z karty Eksplorator danych w witrynie Azure portal.   

   ![Otwórz Eksploratora usługi Azure Cosmos DB](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Znane problemy

Obecnie **Otwórz pełny ekran** środowiska, która umożliwia udostępnianie tymczasowe odczytu i zapisu lub dostęp do odczytu nie jest jeszcze obsługiwana dla kont usługi Azure Cosmos DB w języku Gremlin i interfejs API tabel. Nadal można wyświetlać konta Gremlin i interfejsu API tabel, przekazując ciąg połączenia do Eksploratora usługi Azure Cosmos DB. 

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy wiesz jak rozpocząć pracę z Eksploratorem usługi Azure Cosmos DB do zarządzania danymi, następnie możesz wykonywać następujące czynności:

* Rozpoczyna się [zapytania](sql-api-query-reference.md) przy użyciu składni SQL i wykonywanie [programowanie po stronie serwera](stored-procedures-triggers-udfs.md) za pomocą procedury składowane, funkcje zdefiniowane przez użytkownika, wyzwala. 
