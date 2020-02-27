---
title: Partycjonowanie w Azure Cosmos DB
description: Więcej informacji na temat partycjonowania Azure Cosmos DB, najlepszych rozwiązań w przypadku wybierania klucza partycji oraz zarządzania partycjami logicznymi
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621882"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partycjonowanie w Azure Cosmos DB

Azure Cosmos DB używa partycjonowania do skalowania poszczególnych kontenerów w bazie danych w celu spełnienia wymagań dotyczących wydajności aplikacji. W przypadku partycjonowania elementy w kontenerze są podzielone na odrębne podzestawy o nazwie *partycje logiczne*. Partycje logiczne są tworzone na podstawie wartości *klucza partycji* , który jest skojarzony z każdym elementem w kontenerze. Wszystkie elementy w partycji logicznej mają tę samą wartość klucza partycji.

Na przykład kontener zawiera elementy. Każdy element ma unikatową wartość właściwości `UserID`. Jeśli `UserID` służy jako klucz partycji dla elementów w kontenerze i istnieją 1 000 wartości unikatowych `UserID`, dla kontenera są tworzone partycje logiczne 1 000.

Oprócz klucza partycji, który określa partycję logiczną elementu, każdy element w kontenerze ma *Identyfikator elementu* (unikatowy w obrębie partycji logicznej). Połączenie klucza partycji i identyfikatora elementu tworzy *indeks*elementu, który jednoznacznie identyfikuje element.

[Wybór klucza partycji](partitioning-overview.md#choose-partitionkey) to ważna decyzja, która będzie miała wpływ na wydajność aplikacji.

## <a name="managing-logical-partitions"></a>Zarządzanie partycjami logicznymi

Azure Cosmos DB w sposób przezroczysty i automatycznie zarządza umieszczaniem partycji logicznych w partycjach fizycznych, aby skutecznie spełnić wymagania dotyczące skalowalności i wydajności kontenera. W miarę wzrostu wymagań dotyczących przepływności i magazynowania aplikacji Azure Cosmos DB przenosi partycje logiczne, aby automatycznie rozłożyć obciążenie na większą liczbę serwerów. 

Azure Cosmos DB używa partycjonowania opartego na skrócie, aby rozłożyć partycje logiczne między partycjami fizycznymi. Azure Cosmos DB skrót wartości klucza partycji elementu. Wynik zmieszany Określa partycję fizyczną. Następnie Azure Cosmos DB przydzieli kluczowe miejsce skrótów kluczy partycji równomiernie między partycjami fizycznymi.

Zapytania, które uzyskują dostęp do danych w ramach jednej partycji logicznej, są bardziej ekonomiczne niż zapytania, które uzyskują dostęp do wielu partycji. Transakcje (w procedurach składowanych lub wyzwalaczach) są dozwolone tylko dla elementów w pojedynczej partycji logicznej.

Aby dowiedzieć się więcej o tym, jak Azure Cosmos DB zarządza partycjami, zobacz [partycje logiczne](partition-data.md). (Nie trzeba zrozumieć wewnętrznych szczegółów, aby kompilować lub uruchamiać aplikacje, ale dodano je do czytnika chcesz wiedzieć).

## <a id="choose-partitionkey"></a>Wybieranie klucza partycji

Poniżej przedstawiono dobre wskazówki dotyczące wybierania klucza partycji:

* Jedna partycja logiczna ma górny limit 20 GB miejsca w magazynie.  

* Kontenery usługi Azure Cosmos mają minimalną przepływność wynoszącą 400 jednostek żądań na sekundę (RU/s). Gdy w bazie danych jest obsługiwana przepływność, minimalna jednostek ru na kontener to 100 jednostek żądań na sekundę (RU/s). Żądania kierowane do tego samego klucza partycji nie mogą przekroczyć przepływności przydzielonego do partycji. Jeśli żądania przekraczają przydzieloną przepływność, żądania są ograniczone proporcjonalnie. Dlatego ważne jest, aby wybrać klucz partycji, który nie powoduje "gorąca" w aplikacji.

* Wybierz klucz partycji, który ma szeroką gamę wartości i wzorców dostępu, które są równomiernie rozłożone na partycje logiczne. Dzięki temu można rozłożyć dane i działanie w kontenerze między zestawem partycji logicznych, aby zasoby na potrzeby przechowywania danych i przepływności mogły być dystrybuowane między partycjami logicznymi.

* Wybierz klucz partycji, który rozkłada obciążenie równomiernie we wszystkich partycjach, a nawet w czasie. Wybór klucza partycji powinien zrównoważyć potrzebę wykonywania wydajnych zapytań dotyczących partycji i transakcji w celu dystrybucji elementów w wielu partycjach w celu zapewnienia skalowalności.

* Kandydaci dla kluczy partycji mogą zawierać właściwości, które często pojawiają się jako filtr w zapytaniach. Zapytania mogą być efektywnie kierowane przez dołączenie klucza partycji do predykatu filtru.

## <a name="next-steps"></a>Następne kroki

* Informacje o [partycjonowaniu i skalowaniu w poziomie Azure Cosmos DB](partition-data.md).
* Informacje o [aprowizacji przepływności w Azure Cosmos DB](request-units.md).
* Informacje o [dystrybucji globalnej w Azure Cosmos DB](distribute-data-globally.md).
