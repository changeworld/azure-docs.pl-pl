---
title: Dowiedz się więcej o operacjach usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: 'Elastyczność usługi SQL Data Warehouse pozwala powiększać, zmniejszać lub wstrzymywać moc obliczeniową przy użyciu ruchomej skali jednostek magazynu danych (jednostki DWU). W tym artykule opisano metryki magazynu danych i ich relacje z jednostkami DWU. '
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
origin.date: 04/17/2018
ms.date: 11/12/2018
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: f0489d9c018abac380cd0f8b1aa51fa5bb81707a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531174"
---
# <a name="data-warehouse-workload"></a>Obciążenie magazynu danych
Obciążenie magazynu danych odwołuje się do wszystkich operacji wykonywanych w magazynie danych. Obciążenie magazynu danych uwzględnia cały proces ładowania danych do magazynu, wykonywanie analiz i raportów odnoszących się do magazynu danych, zarządzanie danymi w magazynie oraz eksportowanie danych z magazynu. Stopień zaawansowania i zasięgu tych składników często wynika bezpośrednio z poziomu dojrzałości magazynu danych.

## <a name="new-to-data-warehousing"></a>Dopiero zaczynasz korzystać z magazynowania danych?
Magazyn danych to kolekcja danych, które są ładowane z co najmniej jednego źródła. Jest on używany do wykonywania zadań analizy biznesowej, takich jak raportowanie i analiza danych.

Charakterystyczną cechą magazynów danych są zapytania, które skanują dużą liczbę wierszy, uwzględniają szerokie zakresy danych i mogą zwracać stosunkowo duże wyniki na potrzeby tworzenia analiz oraz raportów. Charakterystyczne dla magazynów danych jest również ładowanie stosunkowo dużej ilości danych w porównaniu z operacjami wstawiania/aktualizowania/usuwania na niskim poziomie transakcji.

* Magazyn danych sprawdza się najlepiej, jeśli dane są przechowywane w sposób, który optymalizuje zapytania skanujące duże liczby wierszy lub duże zakresy danych. Ten typ skanowania działa najlepiej, gdy dane są przechowywane i przeszukiwane według kolumn, a nie według wierszy.

> [!NOTE]
> Indeks magazynu kolumn w pamięci, który używa magazynu kolumn, umożliwia nawet dziesięciokrotne zyski w zakresie kompresji i stokrotny wzrost wydajności zapytań w stosunku do tradycyjnych drzew binarnych raportowania i zapytań analiz. Indeksy magazynu kolumn są traktowane przez firmę Microsoft jako standard przechowywania i skanowania dużej ilości danych w magazynie danych.
> 
> 

* Magazyn danych ma inne wymagania niż system, który jest zoptymalizowany do przetwarzania transakcji online (OLTP). System OLTP obsługuje wiele operacji wstawiania, aktualizowania i usuwania. Te operacje dążą do określonych wierszy w tabeli. Działania wyszukiwania tabeli są wykonywane najlepiej, jeśli dane są przechowywane wiersz po wierszu. Dane można sortować i szybko przeszukiwać, korzystając ze strategii dzielenia o nazwie „drzewo binarne” lub „wyszukiwanie drzewa btree”.

## <a name="data-loading"></a>Ładowanie danych
Ładowanie danych stanowi dużą część obciążenia magazynu danych. Firmy zwykle mają zajęty system OLTP, który śledzi zmiany w ciągu dnia, gdy klienci generują transakcje biznesowe. Okresowo (często w nocy, podczas trwania okna obsługi) transakcje są przenoszone lub kopiowane do magazynu danych. Gdy dane znajdą się w magazynie danych, analitycy mogą wykonywać analizy i podejmować decyzje biznesowe dotyczące danych.

* Tradycyjnie proces ładowania jest nazywany procesem wyodrębniania (Extract), transformacji (Transform) i ładowania (Load) — w skrócie ETL. Zwykle dane muszą zostać przekształcone, aby były zgodne z innymi danymi w magazynie. Wcześniej w firmach używano dedykowanych serwerów ETL do wykonywania transformacji. Teraz dzięki szybkiemu, równoległemu przetwarzaniu masowemu można najpierw załadować dane do usługi SQL Data Warehouse, a następnie wykonać transformacje. Ten proces — nazywany skrótowo procesem ELT (Extract, Load, Transform) — staje się nowym standardem obsługi obciążeń w magazynie danych.

> [!NOTE]
> Za pomocą serwera SQL Server 2016 można teraz wykonywać analizy w czasie rzeczywistym w tabeli OLTP. To rozwiązanie nie eliminuje potrzeby przechowywania i analizowania danych w magazynie danych, ale udostępnia sposób wykonywania analizy w czasie rzeczywistym.
> 
> 

### <a name="reporting-and-analysis-queries"></a>Zapytania raportowe i analityczne
Zapytania raportowe i analityczne są często dzielone na kategorie (małe, średnie i duże) na podstawie różnych kryteriów, ale najczęściej na podstawie kryterium czasu. W większości magazynów danych obciążenie ma charakter mieszany i zawiera zarówno zapytania krótkie, jak i długotrwałe. W każdym przypadku należy określić proporcje tej mieszanki oraz jej częstotliwość (co godzinę, codziennie, koniec miesiąca, koniec kwartału itp.). Należy pamiętać, że obciążenie mieszanymi zapytaniami w połączeniu z współbieżnością umożliwia właściwe planowanie pojemności magazynu danych.

* Planowanie pojemności może być złożonym zadaniem w przypadku obciążenia mieszanymi zapytaniami, szczególnie gdy zwiększanie wydajności magazynu danych wymaga długiego czasu realizacji. Usługa SQL Data Warehouse eliminuje pilną potrzebę planowania pojemności, ponieważ wydajność obliczeniową można zwiększyć lub zmniejszyć w dowolnym momencie, a pojemność magazynu oraz wydajność obliczeniowa są dopasowywane niezależnie.

### <a name="data-management"></a>Zarządzanie danymi
Zarządzanie danymi jest istotne — szczególnie wtedy, gdy wiadomo, że w najbliższej przyszłości może zabraknąć miejsca na dysku. Magazyny danych zazwyczaj dzielą dane na istotne zakresy, które są przechowywane jako partycje w tabeli. Wszystkie produkty oparte na oprogramowaniu SQL Server pozwalają przenosić partycje do tabeli i z tabeli. Przełączanie partycji umożliwia przenoszenie starszych danych do tańszego magazynu i udostępnianie najnowszych danych w magazynie online.

* Indeksy magazynu kolumn obsługują tabele partycjonowane. W indeksach magazynu kolumn partycjonowane tabele są używane na potrzeby zarządzania danymi oraz archiwizacji. W tabelach przechowywanych wiersz po wierszu partycje pełnią ważniejszą rolę w zakresie wydajności zapytania.  
* Technologia PolyBase odgrywa ważną rolę w zarządzaniu danymi. Przy użyciu technologii PolyBase można archiwizować starsze dane, korzystając z usługi Hadoop i usługi Azure Blob Storage.  W ten sposób udostępnianych jest wiele opcji, ponieważ dane są nadal w trybie online.  Pobieranie danych z usługi Hadoop może trwać dłużej, ale czas pobierania może przeważać nad kosztem magazynowania.

### <a name="exporting-data"></a>Eksportowanie danych
Jednym ze sposobów udostępniania danych na potrzeby tworzenia raportów i analiz jest wysłanie ich z magazynu na serwery dedykowane do uruchamiania raportów i analiz. Serwery te są nazywane składnicami danych. Można na przykład wstępnie przetworzyć dane raportu i wyeksportować je z magazynu danych na wiele serwerów na całym świecie, aby były szeroko dostępne dla klientów oraz analityków.

* Aby generować raporty, każdej nocy można przesyłać migawki codziennych danych na serwery raportowania tylko do odczytu. W ten sposób klienci uzyskują większą przepustowość, natomiast zapotrzebowanie na zasoby obliczeniowe magazynu danych ulega ograniczeniu. W aspekcie bezpieczeństwa składnice danych umożliwiają zmniejszenie liczby użytkowników, którzy mają dostęp do magazynu danych.
* W celu wykonywania analiz można skonstruować moduł analizy w magazynie danych i przeprowadzać analizy w magazynie danych lub wstępnie przetworzyć dane oraz wyeksportować je na serwer analiz do dalszej analizy.

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy masz już podstawową wiedzę na temat usługi SQL Data Warehouse, możesz dowiedzieć się, jak szybko [utworzyć bazę danych w usłudze SQL Data Warehouse][create a SQL Data Warehouse] i [ładowanie danych przykładowych][load sample data].

<!--Image references-->

<!--Article references-->
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->