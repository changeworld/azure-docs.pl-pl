---
title: Zarządzanie danymi za pomocą eksploratora usługi Azure Cosmos DB
description: Eksplorator usługi Azure Cosmos DB to autonomiczny interfejs oparty na sieci Web, który umożliwia wyświetlanie danych przechowywanych w usłudze Azure Cosmos DB i zarządzanie nimi.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 57ba647ade45928f03cd7fb2b037642d5e4d52cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096819"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Praca z danymi za pomocą eksploratora usługi Azure Cosmos 

Eksplorator usługi Azure Cosmos DB to autonomiczny interfejs oparty na sieci Web, który umożliwia wyświetlanie danych przechowywanych w usłudze Azure Cosmos DB i zarządzanie nimi. Eksplorator usługi Azure Cosmos DB jest odpowiednikiem istniejącej karty **Eksploratora danych,** która jest dostępna w witrynie Azure portal podczas tworzenia konta usługi Azure Cosmos DB. Najważniejsze zalety eksploratora bazy danych usługi Azure Cosmos DB w stosunku do istniejącego Eksploratora danych to:

* Masz pełnoekranową nieruchomość do wyświetlania danych, uruchamiania zapytań, procedur przechowywanych, wyzwalaczy i wyświetlania ich wyników.  

* Możesz zapewnić tymczasowy lub stały dostęp do odczytu lub odczytu do konta bazy danych i jego kolekcji innym użytkownikom, którzy nie mają dostępu do witryny Azure portal lub subskrypcji.  

* Wyniki kwerendy można udostępnić innym użytkownikom, którzy nie mają dostępu do witryny Azure portal lub subskrypcji.  

## <a name="access-azure-cosmos-db-explorer"></a>Dostęp do eksploratora usługi Azure Cosmos DB

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/). 

2. Z **opcji Wszystkie zasoby**znajdź konto usługi Azure Cosmos DB i przejdź do niego, wybierz pozycję Klucze i skopiuj podstawowy ciąg **połączenia**.  

3. Przejdź https://cosmos.azure.com/do , wklej ciąg połączenia i wybierz **pozycję Połącz**. Za pomocą ciągu połączenia, można uzyskać dostęp do eksploratora usługi Azure Cosmos DB bez żadnych ograniczeń czasowych.  

   Jeśli chcesz zapewnić innym użytkownikom tymczasowy dostęp do konta usługi Azure Cosmos DB, możesz to zrobić przy użyciu adresów URL dostępu do odczytu i odczytu. 

4. Otwórz blok **Eksploratora danych,** wybierz pozycję **Otwórz pełny ekran**. W wyskakującym oknie dialogowym można wyświetlić dwa adresy URL dostępu — **Odczyt, zapis** i **odczyt.** Te adresy URL umożliwiają tymczasowe udostępnianie konta usługi Azure Cosmos DB innym użytkownikom. Dostęp do konta wygasa w ciągu 24 godzin, po czym można ponownie połączyć się przy użyciu nowego adresu URL dostępu lub ciągu połączenia. 

   **Odczyt i zapis** — gdy udostępniasz adres URL odczytu i zapisu innym użytkownikom, mogą wyświetlać i modyfikować bazy danych, kolekcje, kwerendy i inne zasoby skojarzone z tym kontem.

   **Odczyt** — gdy udostępniasz adres URL tylko do odczytu innym użytkownikom, mogą oni wyświetlać bazy danych, kolekcje, kwerendy i inne zasoby skojarzone z tym kontem. Na przykład jeśli chcesz udostępnić wyniki kwerendy swoim kolegom z zespołu, którzy nie mają dostępu do witryny Azure portal lub konta usługi Azure Cosmos DB, możesz podać im ten adres URL.

   Wybierz typ dostępu, z którego chcesz otworzyć konto, i kliknij przycisk **Otwórz**. Po otwarciu eksploratora środowisko jest takie samo, jak w zakładce Eksplorator danych w witrynie Azure portal.   

   ![Otwórz eksploratora usługi Azure Cosmos DB](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Znane problemy

Obecnie **środowisko Otwórz pełny ekran,** które umożliwia udostępnianie tymczasowego dostępu do odczytu i zapisu lub odczytu, nie jest jeszcze obsługiwane dla kont Azure Cosmos DB Gremlin i table API. Nadal można wyświetlić konta interfejsu API Gremlin i tabeli, przekazując ciąg połączenia do Eksploratora bazy danych usługi Azure Cosmos DB. 

Obecnie wyświetlanie dokumentów zawierających UUID nie jest obsługiwane w Eksploratorze danych. Nie ma to wpływu na ładowanie kolekcji, tylko wyświetlanie pojedynczych dokumentów lub zapytań, które zawierają te dokumenty. Aby wyświetlić te dokumenty i zarządzać nimi, użytkownicy powinni nadal używać narzędzia, które zostało pierwotnie użyte do utworzenia tych dokumentów.

## <a name="next-steps"></a>Następne kroki
Teraz, gdy dowiesz się, jak rozpocząć pracę z Eksploratorem usługi Azure Cosmos DB w celu zarządzania danymi, możesz następnie:

* Rozpocznij definiowanie [zapytań](sql-api-query-reference.md) przy użyciu składni SQL i [wykonuj programowanie po stronie serwera](stored-procedures-triggers-udfs.md) przy użyciu procedur przechowywanych, plików UDF, wyzwalaczy. 
