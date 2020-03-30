---
title: Żądaj opłat jednostkowych dla usługi Azure Cosmos DB jako magazynu wartości klucza
description: Dowiedz się więcej o opłatach jednostkowych żądania usługi Azure Cosmos DB dla prostych operacji zapisu i odczytu, gdy jest używany jako magazyn kluczy/wartości.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5b2ee8b5bf19f16d7f7f04e9515fe591db7132f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647508"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Usługa Azure Cosmos DB jako magazyn kluczowych wartości — omówienie kosztów

Usługa Azure Cosmos DB to globalnie rozproszona, wielomodelowa usługa bazy danych do łatwego tworzenia aplikacji o wysokiej dostępności na dużą skalę. Domyślnie usługa Azure Cosmos DB automatycznie i skutecznie indeksuje wszystkie dane, które jest pozyskiwania. Umożliwia to szybkie i spójne zapytania [SQL](how-to-sql-query.md) (i [JavaScript)](stored-procedures-triggers-udfs.md)na danych. 

W tym artykule opisano koszt usługi Azure Cosmos DB dla prostych operacji zapisu i odczytu, gdy jest używany jako magazyn klucz/wartość. Operacje zapisu obejmują wstawia, zastępuje, usuwa i upserts elementów danych. Oprócz zagwarantowania 99,999% dostępności SLA dla wszystkich kont w wielu regionach, usługa Azure Cosmos DB oferuje gwarantowane opóźnienie <10 ms dla odczytów i (indeksowane) zapisy, na 99 percentylu. 

## <a name="why-we-use-request-units-rus"></a>Dlaczego używamy jednostek żądających (RUs)

Wydajność usługi Azure Cosmos DB jest oparta na ilości aprowizowanej przepływności wyrażonej w [jednostkach żądań](request-units.md) (RU/s). Inicjowanie obsługi administracyjnej jest na drugim poziomie szczegółowości i jest kupowane w RU/s[(nie mylić z rozliczeniami godzinowymi).](https://azure.microsoft.com/pricing/details/cosmos-db/) Jednostki RU należy uznać za abstrakcję logiczną (walutę), która upraszcza inicjowanie obsługi administracyjnej wymaganej przepływności dla aplikacji. Użytkownicy nie muszą myśleć o rozróżnianiu przepływności odczytu i zapisu. Model wspólnej waluty jednostek produkcyjnych tworzy wydajność do udostępniania aprowizowanych zdolności produkcyjnych między odczytami i zapisami. Ten model aprowizowanej pojemności umożliwia usłudze zapewnienie **przewidywalnej i spójnej przepływności, gwarantowanego niskiego opóźnienia i wysokiej dostępności.** Na koniec, podczas gdy model RU jest używany do przedstawiania przepływności, każdy aprowiowany RU ma również określoną ilość zasobów (np. pamięć, rdzenie/PROCESOR i IOPS).

Jako globalnie rozproszony system baz danych usługa Cosmos DB jest jedyną usługą platformy Azure, która zapewnia kompleksowe ławy SLA obejmujące opóźnienia, przepływność, spójność i wysoką dostępność. Aprowizowana przepływność jest stosowana do każdego z regionów skojarzonych z kontem usługi Cosmos. W przypadku odczytów usługa Cosmos DB oferuje wiele, dobrze zdefiniowanych [poziomów spójności](consistency-levels.md) do wyboru. 

W poniższej tabeli przedstawiono liczbę procesorów operacyjnych wymaganych do wykonywania operacji odczytu i zapisu na podstawie elementu danych o rozmiarze 1 KB i 100 KB z wyłączonym domyślnym indeksowania automatycznego. 

|Rozmiar przedmiotu|1 Przeczytaj|1 Zapis|
|-------------|------|-------|
|1 KB|1 ru|5 r.r.|
|100 KB|10 jednostek RU|50 r./|

## <a name="cost-of-reads-and-writes"></a>Koszt odczytów i zapisów

Jeśli udostępnisz 1000 RU/s, będzie to 3,6 mln RU/godz. W przypadku elementu danych o rozmiarze 1 KB oznacza to, że można zużywać 3,6 miliona odczytów lub 0,72 mln zapisów (3,6 mln RU / 5) przy użyciu aprowizowanej przepływności. Znormalizowane do milionów odczytów i zapisów, koszt będzie $0.022 /milion odsłon ($0.08 / 3.6) i $0.111/milion zapisów ($0.08 / 0.72). Koszt miliona staje się minimalny, jak pokazano w poniższej tabeli.

|Rozmiar przedmiotu|Koszt 1 miliona odsłon|Koszt 1 miliona zapisów|
|-------------|-------|--------|
|1 KB|zł.|zł.|
|100 KB|zł.|zł.|


Większość podstawowych usług w obiekcie blob lub object store pobiera opłatę w wysokości 0,40 USD za milion transakcji odczytu i 5 USD za milion transakcji zapisu. W przypadku optymalnego użycia usługa Cosmos DB może być nawet o 98% tańsza niż te inne rozwiązania (dla transakcji o wartości 1 KB).

## <a name="next-steps"></a>Następne kroki

* Użyj [kalkulatora RU,](https://cosmos.azure.com/capacitycalculator/) aby oszacować przepływność dla obciążeń.

