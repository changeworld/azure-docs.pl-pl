---
title: Partycjonowanie w usłudze Azure Cosmos DB
description: Dowiedz się więcej o partycjonowaniu w usłudze Azure Cosmos DB, najlepszych praktykach przy wyborze klucza partycji oraz o zarządzaniu partycjami logicznymi
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251871"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partycjonowanie w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB używa partycjonowania do skalowania poszczególnych kontenerów w bazie danych, aby spełnić potrzeby w zakresie wydajności aplikacji. W partycjonowaniu elementy w kontenerze są podzielone na odrębne podzbiory zwane *partycjami logicznymi*. Partycje logiczne są tworzone na podstawie wartości *klucza partycji,* który jest skojarzony z każdym elementem w kontenerze. Wszystkie elementy w partycji logicznej mają tę samą wartość klucza partycji.

Na przykład kontener przechowuje elementy. Każdy element ma unikatową `UserID` wartość dla właściwości. Jeśli `UserID` służy jako klucz partycji dla elementów w kontenerze i `UserID` istnieje 1000 unikatowych wartości, 1000 partycji logicznych są tworzone dla kontenera.

Oprócz klucza partycji, który określa partycję logiczną elementu, każdy element w kontenerze ma *identyfikator elementu* (unikatowy w ramach partycji logicznej). Połączenie klucza partycji i identyfikatora elementu tworzy *indeks*elementu , który jednoznacznie identyfikuje element.

[Wybór klucza partycji](partitioning-overview.md#choose-partitionkey) jest ważną decyzją, która wpłynie na wydajność aplikacji.

## <a name="managing-logical-partitions"></a>Zarządzanie partycjami logicznymi

Usługa Azure Cosmos DB w sposób przejrzysty i automatycznie zarządza rozmieszczeniem partycji logicznych na partycjach fizycznych, aby skutecznie zaspokoić potrzeby dotyczące skalowalności i wydajności kontenera. Wraz ze wzrostem wymagań dotyczących przepływności i magazynowania aplikacji usługa Azure Cosmos DB przenosi partycje logiczne, aby automatycznie rozłożyć obciążenie na większą liczbę serwerów. 

Usługa Azure Cosmos DB używa partycjonowania opartego na mieszaniu do rozmieszczania partycji logicznych na partycjach fizycznych. Usługa Azure Cosmos DB skróty wartość klucza partycji elementu. Wynik mieszania określa partycję fizyczną. Następnie usługa Azure Cosmos DB przydziela równomiernie skróty kluczowego miejsca klucza partycji między partycjami fizycznymi.

Kwerendy, które uzyskują dostęp do danych w ramach jednej partycji logicznej są bardziej opłacalne niż kwerendy, które uzyskują dostęp do wielu partycji. Transakcje (w procedurach składowanych lub wyzwalaczy) są dozwolone tylko dla elementów w jednej partycji logicznej.

Aby dowiedzieć się więcej o tym, jak usługa Azure Cosmos DB zarządza partycjami, zobacz [Partycje logiczne](partition-data.md). (Nie jest konieczne, aby zrozumieć szczegóły wewnętrzne do tworzenia lub uruchamiania aplikacji, ale dodane tutaj dla ciekawskiego czytelnika.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Wybieranie klucza partycji

Poniżej przedstawiono dobre wskazówki dotyczące wybierania klucza partycji:

* Pojedyncza partycja logiczna ma górny limit 20 GB miejsca.  

* Kontenery usługi Azure Cosmos mają minimalną przepływność 400 jednostek żądań na sekundę (RU/s). Gdy przepływność jest aprowizowana w bazie danych, minimalna jednostki ru na kontener wynosi 100 jednostek żądań na sekundę (RU/s). Żądania do tego samego klucza partycji nie może przekroczyć przepływności, która jest przydzielona do partycji. Jeśli żądania przekraczają przydzieloną przepływność, żądania są ograniczone szybkością. Dlatego ważne jest, aby wybrać klucz partycji, który nie powoduje "hot spotów" w aplikacji.

* Wybierz klucz partycji, który ma szeroki zakres wartości i wzorców dostępu, które są równomiernie rozłożone na partycje logiczne. Pomaga to rozłożyć dane i działania w kontenerze na zestaw partycji logicznych, dzięki czemu zasoby do przechowywania danych i przepływności mogą być dystrybuowane między partycjami logicznymi.

* Wybierz klucz partycji, który rozkłada obciążenie równomiernie na wszystkie partycje i równomiernie w czasie. Wybór klucza partycji należy zrównoważyć potrzebę efektywnych zapytań partycji i transakcji w stosunku do celu dystrybucji elementów na wielu partycjach, aby osiągnąć skalowalność.

* Kandydaci do kluczy partycji może zawierać właściwości, które są często wyświetlane jako filtr w zapytaniach. Zapytania mogą być skutecznie kierowane przez dołączenie klucza partycji w predykacie filtru.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [partycjonowaniu i skalowaniu w poziomie w usłudze Azure Cosmos DB](partition-data.md).
* Dowiedz się więcej o [aprowizowanej przepływności w usłudze Azure Cosmos DB.](request-units.md)
* Dowiedz się więcej o [dystrybucji globalnej w usłudze Azure Cosmos DB](distribute-data-globally.md).
