---
title: Partycjonowanie w usłudze Azure Cosmos DB
description: Omówienie partycjonowanie w usłudze Azure Cosmos DB
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 20e869d30f9e7b8102b723870f0102e041e064d7
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042278"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partycjonowanie w usłudze Azure Cosmos DB

Partycjonowanie to technika używana przez usługi Cosmos DB do skalowania poszczególnych kontenerów w bazie danych w celu spełnienia potrzeb dotyczących wydajności aplikacji. Za pomocą partycjonowania, elementy w kontenerze są podzielone na różne podzbiory, o nazwie partycjami logicznymi. Partycje logiczne są tworzone na podstawie wartości właściwości klucza partycji, związane z każdym elementem.

Partycja logiczna jest unikatowy podzestaw elementów w kontenerze. Elementy w partycji logicznej są identyfikowane przez wartość klucza partycji, który jest współużytkowany przez wszystkie elementy w partycji logicznej.  Rozważmy na przykład kontener, który zawiera dokumenty, a każdy dokument ma `UserID` właściwości.  Jeśli `UserID` służy jako partycja dla elementów w kontenerze, a dla klucza są unikatowe 1000 `UserID` wartości 1000 partycjami logicznymi zostaną utworzone dla kontenera.

Każdy element w kontenerze ma **klucza partycji** określający elementu **partycji logicznej**, a każdy element ma również **identyfikator elementu** (która jest unikatowa w obrębie wartość logiczna partycja).  **Indeksu** o jednoznacznie identyfikuje element i jego jest tworzony przez połączenie klucza partycji i identyfikatora elementu.

Wybór klucza partycji jest ważnym krokiem, który będzie mieć wpływ na wydajność aplikacji.  Aby dowiedzieć się więcej, zobacz [wybór klucza partycji](partitioning-overview.md#choose-partitionkey) artykuł, aby uzyskać szczegółowe wskazówki.

## <a name="logical-partition-management"></a>Partycja logiczna zarządzania

Usługa cosmos DB, przejrzysty i automatyczny zarządza położenie partycjami logicznymi na partycje fizyczne (infrastruktury serwera), aby efektywnie zaspokoić potrzeby skalowalności i wydajności kontenera. Jak zwiększyć przepływność oraz Magazyn wymagań aplikacji, usługi Cosmos DB przenosi partycjami logicznymi, aby automatycznie rozłożenie obciążenia na większą liczbę serwerów. Aby dowiedzieć się więcej na temat sposobu zarządzania partycjami przez usługi Cosmos DB, zobacz [partycjami logicznymi](partition-data.md) artykułu. Nie jest to konieczne zrozumieć te informacje do kompilacji lub uruchamiaj swoje aplikacje.

Usługi cosmos DB używa bazujących na skrótach partycjonowania, aby rozłożyć partycjami logicznymi na partycje fizyczne.  Wartość klucza partycji elementu jest mieszany, Cosmos DB, a skróconego wyniku określa fizyczną partycję. Usługa cosmos DB przydziela miejsce klucza partycji skróty kluczy równomiernie na fizyczne partycje "n".

Kwerendy, uzyskujących dostęp do danych w ramach jednej partycji są bardziej opłacalna niż zapytanie realizowane dostęp do wielu partycji. Transakcje (w procedury składowane i wyzwalacze) są dozwolone tylko dla elementów w obrębie jednej partycji logicznej.  

## <a id="choose-partitionkey"></a>Wybór klucza partycji

Podczas wybierania klucza partycji, należy wziąć pod uwagę następujące informacje:

* Jednej partycji logicznej jest dozwolone górny limit 10 GB miejsca do magazynowania.  

* Partycjonowane kontenery są konfigurowane przy minimalnej przepływności równej 400 jednostek RU/s. Żądania do tego samego klucza partycji nie może przekraczać przepływności przydzielane do partycji. Jeśli przekraczają przydzielone przepływności, żądania będą ograniczone szybkość. Tak koniecznie wybierz taki klucz partycji, który nie powoduje "aktywne", w ramach aplikacji.

* Wybór klucza partycji, który równomiernie rozdziela obciążenia wszystkich partycji i równomiernie wraz z upływem czasu.  Wybór klucza partycji należy zrównoważyć potrzebę partycji wydajność zapytań i/lub transakcje w odniesieniu do założonego celu dystrybucji elementów na wielu partycjach w celu zapewnienia skalowalności.

* Wybór klucza partycji, który oferuje szeroki zakres wartości i wzorce dostępu, które zostały rozmieszczone równomiernie w obrębie partycjami logicznymi. Wygląda to w celu rozłożenia danych i działania w kontenerze w zestawie partycjami logicznymi tak, aby zasoby dla magazynu danych i przepływności mogą być rozproszone między partycjami logicznymi.

* Kandydaci do kluczy partycji może zawierać właściwości, które są często wyświetlane jako filtr zapytania. Zapytania można skutecznie kierować przez uwzględnienie klucza partycji w predykacie filtru.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [partycji](partition-data.md)
* Dowiedz się więcej o [aprowizowanej przepływności w usłudze Azure Cosmos DB](request-units.md)
* Dowiedz się więcej o [dystrybucję globalną w usłudze Azure Cosmos DB](distribute-data-globally.md)
