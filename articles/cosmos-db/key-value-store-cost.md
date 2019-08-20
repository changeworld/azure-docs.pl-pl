---
title: Usługi Azure Cosmos DB do przechowywania wartości klucza, opłat za jednostki żądań
description: Więcej informacji na temat opłat za jednostki żądań w usłudze Azure Cosmos DB dla prostych zapisu, a operacje odczytu, gdy jest ona używana jako magazyn klucz/wartość.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 3758766b1051acb9321ec67727eecef249971065
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615100"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Usługa Azure Cosmos DB jako wartości klucza magazynu — omówienie kosztów

Usługa Azure Cosmos DB to usługa globalnie dystrybuowanej, wielomodelowej bazy danych do tworzenia aplikacji o wysokiej dostępności, na dużą skalę w prosty sposób. Domyślnie usługa Azure Cosmos DB automatycznie indeksuje wszystkie dane, które pozyskuje wydajnie. Umożliwia to szybkie i spójne [SQL](how-to-sql-query.md) (i [JavaScript](stored-procedures-triggers-udfs.md)) kwerendy dla każdego typu danych. 

W tym artykule opisano koszt usługi Azure Cosmos DB proste zapisu oraz operacje odczytu, gdy jest ona używana jako magazyn klucz/wartość. Operacje obejmują operacje wstawiania, zastępuje, usuwa i wykonuje operację UPSERT dokumentów zapisu. Oprócz zagwarantowania, że umowa SLA dotycząca dostępności na 99,99% dla wszystkich kont jednego regionu i wszystkich kont wieloregionowych o spójności swobodnej oraz 99,999% dostępności na wszystkich wieloregionowych kontach baz danych, Azure Cosmos DB oferuje gwarantowane < 10 ms dla odczytuje odpowiednio i dla (indeksowane) zapisów w 99 percentylu. 

## <a name="why-we-use-request-units-rus"></a>Dlaczego używamy jednostek żądań (ru)

Wydajność usługi Azure Cosmos DB jest oparty na ilość aprowizowanej [jednostek żądań](request-units.md) (RU) dla partycji. Inicjowanie obsługi administracyjnej drugiego stopnia szczegółowości i nie jest sprzedawana w wielkości jednostek ru na sekundę ([nie należy mylić przy naliczaniu godzinowym](https://azure.microsoft.com/pricing/details/cosmos-db/)). (RUS) powinny być uważane za waluty, która upraszcza aprowizację przepustowość wymagana dla aplikacji. Naszych klientów nie trzeba myśleć o rozróżnianiu odczytu i zapisu jednostek pojemności. Model pojedynczej waluty (RUS) tworzy efektywność udostępnianie zaprowizowaną pojemnością między operacji odczytu i zapisu. Ten model zaprowizowaną pojemnością włącza usługę w celu zapewnienia przewidywalnych i spójne przepływności, gwarantowanych małych opóźnień i wysokiej dostępności. Na koniec używamy jednostek Zarezerwowanych do modelu przepływności, ale każdy aprowizowanych jednostek RU również ma określoną ilość zasobów (pamięci, Core). Jednostek żądań na sekundę nie jest tylko operacje We/Wy.

Jako system globalnie rozproszonej bazy danych Cosmos DB to usługa tylko Azure, która zawiera umowy SLA dotyczące opóźnienia, przepływność i spójność oprócz wysokiej dostępności. Wybrana przepustowość jest stosowana do każdego regionu skojarzonego z kontem bazy danych Cosmos. Dla odczytów, Cosmos DB oferuje wiele dobrze zdefiniowanych [poziomów spójności](consistency-levels.md) służących do wyboru. 

W poniższej tabeli przedstawiono liczbę jednostek zarezerwowanych, wymagane do wykonywania odczytu i zapisu transakcji na podstawie rozmiaru dokumentu o rozmiarze 1 KB i 100 KB/s.

|Rozmiar elementu|Odczyt 1|1 zapisu|
|-------------|------|-------|
|1 KB|1 JEDNOSTKA ŻĄDANIA|5 jednostek ru|
|100 KB|10 jednostek RU|50 (RUS)|

## <a name="cost-of-reads-and-writes"></a>Koszt odczyty i zapisy

Jeśli aprowizować 1000 jednostek żądań na sekundę, ta ilość jednostek RU/godz. i będzie 3.6 m kosztów 0,08 USD za godzinę (w Stanach Zjednoczonych i Europie). Dla dokumentu o rozmiarze 1 KB, oznacza to, że mogą wykorzystywać odczyty 3.6-m lub m 0,72 zapisuje (3.6 mln jednostek RU / 5) przy użyciu aprowizowanej przepływności. Znormalizowane do milionów operacji odczytu i zapisu, opłata wyniesie odczyty /m 0,022 $ (0,08 USD / 3.6) i zapisuje 0.111 $/ m (0,08 USD / 0,72). Koszt za mln staje się minimalny, jak pokazano w poniższej tabeli.

|Rozmiar elementu|Odczyt 1-m|1 mln zapisu|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


Większość podstawowych obiektów blob lub obiekt Magazyny usług opłaty 0,40 USD za milion transakcji odczytu, a 5 USD za milion zapisu transakcji. Jeśli używany optymalnie, Cosmos DB można 98% tańsze niż te inne rozwiązania (w przypadku transakcji 1 KB).

## <a name="next-steps"></a>Kolejne kroki

Wkrótce podamy nowe artykuły dotyczące optymalizacji, inicjowanie obsługi zasobu usługi Azure Cosmos DB. W międzyczasie możesz użyć naszych [kalkulatora jednostek RU](https://www.documentdb.com/capacityplanner).

