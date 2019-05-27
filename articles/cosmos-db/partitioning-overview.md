---
title: Partycjonowanie w usłudze Azure Cosmos DB
description: Przegląd partycjonowanie w usłudze Azure Cosmos DB.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.openlocfilehash: 7d252aed830e24719be2112391f0e77d9a6ff9c5
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65953715"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partycjonowanie w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB używa partycjonowanie skalowania poszczególnych kontenerów w bazie danych w celu spełnienia potrzeb dotyczących wydajności aplikacji. W przypadku użycia partycjonowania elementów w kontenerze są podzielone na różne podzbiory o nazwie *partycjami logicznymi*. Partycje logiczne są tworzone na podstawie na wartości *klucza partycji* skojarzonego z każdego elementu w kontenerze. Wszystkie elementy w partycji logicznej mają taką samą wartość klucza partycji.

Na przykład kontener zawiera elementy. Każdy element ma unikatową wartość dla `UserID` właściwości. Jeśli `UserID` służy jako partycję kluczy dla elementów w kontenerze i są unikatowe 1000 `UserID` wartości 1000 partycje logiczne są tworzone dla kontenera.

Oprócz klucza partycji, który określa partycji logicznej elementu, każdy element w kontenerze ma *identyfikator elementu* (unikatowe w ramach partycji logicznej). Łącząc klucza partycji i Identyfikatora elementu spowoduje utworzenie elementu *indeksu*, który unikatowo identyfikuje element.

[Wybór klucza partycji](partitioning-overview.md#choose-partitionkey) jest ważnym krokiem, który będzie mieć wpływ na wydajność aplikacji.

## <a name="managing-logical-partitions"></a>Zarządzanie partycjami logicznymi

Usługa Azure Cosmos DB, przejrzysty i automatyczny zarządza umieszczania partycji logicznej na partycje fizyczne, aby efektywnie zaspokoić potrzeby skalowalności i wydajności kontenera. Jak zwiększyć przepływność oraz Magazyn wymagania aplikacji, usługi Azure Cosmos DB przenosi partycjami logicznymi, aby automatycznie rozłożenie obciążenia na większą liczbę serwerów. 

Usługa Azure Cosmos DB używa bazujących na skrótach partycjonowania, aby rozłożyć partycjami logicznymi na partycje fizyczne. Usługa Azure Cosmos DB miesza wartość klucza partycji elementu. Skróconego wyniku określa fizyczną partycję. Następnie usługi Azure Cosmos DB przydziela miejsce klucza partycji skróty kluczy równomiernie na fizyczne partycje.

Kwerendy, uzyskujących dostęp do danych w ramach jednej partycji logicznej są bardziej opłacalna niż zapytanie realizowane dostęp do wielu partycji. Transakcje (w procedury składowane i wyzwalacze) są dozwolone wyłącznie w odniesieniu do elementów w jednej partycji logicznej.

Aby dowiedzieć się więcej o sposobie zarządzania partycjami w usłudze Azure Cosmos DB, zobacz [partycjami logicznymi](partition-data.md). (Go nie jest niezbędne do zrozumienia szczegółami wewnętrznymi twórz lub uruchamiaj swoje aplikacje, ale dodany tutaj dla czytnika wiedzieć).

## <a id="choose-partitionkey"></a>Wybór klucza partycji

Dobrą wskazówkę do wybierania klucza partycji jest następująca:

* Jedna partycja logiczny ma górny limit 10 GB miejsca do magazynowania.  

* Kontenery usługi Azure Cosmos mają minimalna przepływność z 400 jednostek żądań na sekundę (RU/s). Żądania do tego samego klucza partycji nie może przekraczać przepływności, który jest przydzielony do partycji. Żądania przekroczy przydzielone przepływności, żądania są ograniczone szybkości. Tak koniecznie wybierz taki klucz partycji, który nie powoduje "aktywne", w ramach aplikacji.

* Wybór klucza partycji, który oferuje szeroki zakres wartości i wzorce dostępu, które zostały rozmieszczone równomiernie w obrębie partycjami logicznymi. Pomaga to rozkłada zbiór partycjami logicznymi, danych i działania w Twoim kontenerze, aby zasoby dla magazynu danych i przepływności mogą być rozproszone między partycjami logicznymi.

* Wybór klucza partycji, który równomiernie rozdziela obciążenia wszystkich partycji i równomiernie wraz z upływem czasu. Wybór klucza partycji należy zrównoważyć potrzebę partycji wydajne zapytania i transakcje w odniesieniu do założonego celu dystrybucji elementów na wielu partycjach w celu zapewnienia skalowalności.

* Kandydaci do kluczy partycji może zawierać właściwości, które są często wyświetlane jako filtr zapytania. Zapytania można skutecznie kierować przez uwzględnienie klucza partycji w predykacie filtru.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB](partition-data.md).
* Dowiedz się więcej o [aprowizowanej przepływności w usłudze Azure Cosmos DB](request-units.md).
* Dowiedz się więcej o [dystrybucję globalną w usłudze Azure Cosmos DB](distribute-data-globally.md).
