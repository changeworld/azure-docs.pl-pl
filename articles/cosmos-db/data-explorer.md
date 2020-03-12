---
title: Zarządzanie danymi za pomocą Eksploratora usługi Azure Cosmos DB
description: Eksplorator usługi Azure Cosmos DB jest autonomiczny opartego na sieci web interfejs, który umożliwia wyświetlanie i zarządzanie nimi danych przechowywanych w usłudze Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 57ba647ade45928f03cd7fb2b037642d5e4d52cc
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096819"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Praca z danymi za pomocą eksploratora usługi Azure Cosmos 

Eksplorator usługi Azure Cosmos DB jest autonomiczny opartego na sieci web interfejs, który umożliwia wyświetlanie i zarządzanie nimi danych przechowywanych w usłudze Azure Cosmos DB. Eksplorator Azure Cosmos DB jest odpowiednikiem istniejącej karty **Eksplorator danych** dostępnej w Azure Portal podczas tworzenia konta Azure Cosmos DB. Kluczowe zalety Eksploratora usługi Azure Cosmos DB za pośrednictwem istniejących Eksplorator danych są następujące:

* Masz pełną-powierzchnię ekranu do wyświetlania danych, wykonywania zapytań, procedury składowane, wyzwalacze i obejrzeć ich wyniki.  

* Można zapewnić dostęp do odczytu i zapisu lub odczytu tymczasowy lub stały Twojego konta bazy danych i jego kolekcjach do innych użytkowników, którzy nie mają dostępu do witryny Azure portal lub subskrypcji.  

* Wyniki zapytania można udostępniać innym użytkownikom, którzy nie mają dostępu do witryny Azure portal lub subskrypcji.  

## <a name="access-azure-cosmos-db-explorer"></a>Dostęp do usługi Azure Cosmos DB explorer

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/). 

2. W obszarze **wszystkie zasoby**Znajdź i przejdź do konta Azure Cosmos DB, wybierz pozycję klucze i skopiuj **podstawowe parametry połączenia**.  

3. Przejdź do https://cosmos.azure.com/, wklej parametry połączenia i wybierz pozycję **Połącz**. Za pomocą parametrów połączenia, mogą uzyskać dostęp do Eksploratora usługi Azure Cosmos DB bez żadnych ograniczeń czasowych.  

   Do innych udzielania tymczasowego dostępu do konta usługi Azure Cosmos DB, możesz to zrobić za pomocą odczytu / zapisu i odczytu adresów URL. 

4. Otwórz blok **Eksplorator danych** , wybierz pozycję **Otwórz pełny ekran**. W wyskakującym oknie dialogowym można wyświetlić dwa adresy URL dostępu — **Odczyt i zapis** i **Odczyt**. Te adresy URL umożliwiają udostępnianie Twojego konta usługi Azure Cosmos DB tymczasowo innym użytkownikom. Dostęp do konta wygasa w ciągu 24 godzin, po których można ponownie połączyć się przy użyciu nowego adresu URL dostępu lub parametry połączenia. 

   **Odczyt i zapis** — w przypadku udostępniania adresu URL do odczytu i zapisu innym użytkownikom użytkownicy mogą wyświetlać i modyfikować bazy danych, kolekcje, zapytania i inne zasoby skojarzone z tym konkretnym kontem.

   **Odczyt** — w przypadku udostępniania adresu URL tylko do odczytu innym użytkownikom mogą wyświetlać bazy danych, kolekcje, zapytania i inne zasoby skojarzone z tym konkretnym kontem. Na przykład jeśli użytkownik chce udostępnić wyniki zapytania z członkami zespołu, którzy nie mają dostępu do witryny Azure portal lub konta usługi Azure Cosmos DB, możesz zapewnić im tego adresu URL.

   Wybierz typ dostępu, za pomocą którego chcesz otworzyć konto, a następnie kliknij przycisk **Otwórz**. Po otwarciu Eksploratora, proces jest taka sama, jak związany z karty Eksplorator danych w witrynie Azure portal.   

   ![Otwórz Eksploratora usługi Azure Cosmos DB](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Znane problemy

Obecnie **otwarte pełne środowisko ekranu** , które umożliwia udostępnianie tymczasowego dostępu do odczytu lub zapisu, nie jest jeszcze obsługiwane w przypadku kont Azure Cosmos DB Gremlin i interfejs API tabel. Nadal można wyświetlać konta Gremlin i interfejsu API tabel, przekazując ciąg połączenia do Eksploratora usługi Azure Cosmos DB. 

Obecnie wyświetlanie dokumentów zawierających identyfikator UUID nie jest obsługiwane w Eksplorator danych. Nie ma to wpływu na ładowanie kolekcji, tylko Wyświetlanie pojedynczych dokumentów lub zapytań zawierających te dokumenty. Aby wyświetlić te dokumenty i zarządzać nimi, użytkownicy powinni nadal korzystać z narzędzia, które było pierwotnie używane do tworzenia tych dokumentów.

## <a name="next-steps"></a>Następne kroki
Teraz, gdy wiesz jak rozpocząć pracę z Eksploratorem usługi Azure Cosmos DB do zarządzania danymi, następnie możesz wykonywać następujące czynności:

* Rozpocznij Definiowanie [zapytań](sql-api-query-reference.md) przy użyciu składni SQL i przeprowadzanie [programowania po stronie serwera](stored-procedures-triggers-udfs.md) za pomocą procedur składowanych, UDF i wyzwalaczy. 
