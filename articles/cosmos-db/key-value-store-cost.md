---
title: Azure DB rozwiązania Cosmos jako wartość klucza magazynu — omówienie koszt | Dokumentacja firmy Microsoft
description: Więcej informacji o niskich kosztów korzystania z bazy danych Azure rozwiązania Cosmos jako magazyn wartości klucza.
keywords: magazyn kluczy i wartości
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: ''
documentationcenter: ''
ms.assetid: 7f765c17-8549-4509-9475-46394fc3a218
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 9854ed30672ea16650fbdd98e991fb3916afef07
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure DB rozwiązania Cosmos jako magazyn wartości klucza — koszt — omówienie

Azure DB rozwiązania Cosmos jest usługą bazy danych globalnie rozproszone i wiele modeli łatwe tworzenie aplikacji na dużą skalę, wysoko dostępne. Domyślnie program Azure DB rozwiązania Cosmos automatycznie indeksuje wszystkie dane, które go wysyła strumień, wydajnie. Umożliwia to szybkie i spójne [SQL](sql-api-sql-query.md) (i [JavaScript](programming.md)) kwerendy dla każdego typu danych. 

Tym artykule opisano koszt bazy danych Azure rozwiązania Cosmos do zapisu proste i operacje odczytu, gdy jest używany jako magazyn kluczy i wartości. Zapisu operacji zalicza się wstawienia, zastępuje, usuwa i upserts dokumentów. Oprócz zagwarantowania 99,99% dostępności umowy SLA dla wszystkich kont w pojedynczym regionie i wszystkich kont w przypadku swobodna spójności i 99,999% odczytu dostępności na wszystkich kontach w przypadku bazy danych, bazy danych rozwiązania Cosmos Azure oferuje gwarantowane < opóźnienie 10 ms odczytuje i < 15 ms opóźnień (indeksowane), zapisuje na 99-ty percentyl. 

## <a name="why-we-use-request-units-rus"></a>Dlaczego używamy jednostek żądania (RUs)

Azure wydajności bazy danych rozwiązania Cosmos jest oparta na ilość elastycznie [jednostek żądania](request-units.md) (RU) dla partycji. Udostępnianie w drugiego stopnia szczegółowości i została zakupiona w RUs na sekundę ([nie należy mylić z rozliczeniami co godzinę](https://azure.microsoft.com/pricing/details/cosmos-db/)). RUs należy traktować jako walutę upraszczającym udostępniania przepływności wymagane dla aplikacji. Klientów nie trzeba myśleć o rozróżnianie między odczytu i zapisu pojemności jednostki. Model jednej waluty RUs tworzy wydajności, aby udostępnić elastycznie pojemności między odczyty i zapisy. Ten model elastycznie pojemności umożliwia usłudze zapewniają przewidywalną i spójny przepływności gwarancji, małych opóźnień i wysokiej dostępności. Na koniec używamy RU produktywnością modelu, ale każdy elastycznie RU ma także zdefiniowanych ilość zasobów (pamięć, Core). RU/s nie jest tylko IOPS.

Jako system globalnie rozproszoną bazę danych DB rozwiązania Cosmos jest tylko Azure usługa, która zapewnia SLA na opóźnienia, przepływności i spójności oprócz wysokiej dostępności. Przepływność, które należy udostępnić jest stosowany do poszczególnych regionów skojarzonych z Twoim kontem bazy danych DB rozwiązania Cosmos. Dla odczytów, DB rozwiązania Cosmos oferuje wiele dobrze zdefiniowany [poziomy spójności](consistency-levels.md) dla można wybrać. 

Poniższa tabela przedstawia liczbę RUs wymaganych do wykonania odczytu i zapisu transakcji na podstawie rozmiaru dokumentu o rozmiarze 1 KB i 100 KB/s.

|Rozmiar elementu|Odczyt 1|1 zapisu|
|-------------|------|-------|
|1 KB|1 RU|5 RUs|
|100 KB|10 RUs|50 RUs|

## <a name="cost-of-reads-and-writes"></a>Koszt odczyty i zapisy

Jeśli dostarczasz 1 000 RU/s, ta ilość 3,6 m RU na godzinę i będzie koszt $0.08 godzinę (w Stanach Zjednoczonych i Europie). Dla dokumentu o rozmiarze 1 KB, oznacza to, czy będzie można korzystać z odczyty 3,6 m lub zapisuje 0.72-m (m 3,6 RU / 5) przy użyciu sieci udostępnionej przepływności. Znormalizowane do milionów odczyty i zapisy, kosztów byłoby odczyty /m 0,022 $ (0.08 $ / 3,6) i zapisuje 0.111 $/ m (0.08 $ / 0,72). Koszt milionów staje się minimalny, jak pokazano w poniższej tabeli.

|Rozmiar elementu|1-m odczytu|1-m zapisu|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


Większość podstawowych obiektów blob lub obiekt Magazyny usług opłatą $0,40 na milionów transakcji odczytu i 5 USD na transakcję milionów zapisu. Jeśli używane optymalnie, rozwiązania Cosmos bazy danych może być 98% tańszy niż te rozwiązania, (dla transakcji 1 KB).

## <a name="next-steps"></a>Kolejne kroki

Wkrótce nowe artykuły dotyczące optymalizacji Inicjowanie obsługi zasobów bazy danych Azure rozwiązania Cosmos. W międzyczasie możesz użyć naszych [Kalkulator RU](https://www.documentdb.com/capacityplanner).

