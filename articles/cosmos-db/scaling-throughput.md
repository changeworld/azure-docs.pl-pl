---
title: Skalowanie przepływności w usłudze Azure Cosmos DB
description: W tym artykule opisano, jak usługa Azure Cosmos DB skaluje przepływność w różnych regionach, w których jest aprowied konta usługi Azure Cosmos.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 440f23afcd08326261be30432ad1f0ecb16f55fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873509"
---
# <a name="globally-scale-provisioned-throughput"></a>Globalne skalowanie aprowizowanej przepływności 

W usłudze Azure Cosmos DB aprowizowana przepływność jest reprezentowana jako jednostki żądań/sekundę (RU/s lub liczba mnoga. Programy RU mierzą koszt operacji odczytu i zapisu względem kontenera usługi Cosmos, jak pokazano na poniższej ilustracji:

![Jednostki żądania](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Można aprowizować procesory WUS w kontenerze usługi Cosmos lub w bazie danych usługi Cosmos. Obiekty RU aprowizacji w kontenerze są dostępne wyłącznie dla operacji wykonywanych w tym kontenerze. Obiekty RU aprowizowane w bazie danych są współużytkowane przez wszystkie kontenery w tej bazie danych (z wyjątkiem kontenerów z wyłącznie przypisanymi procesorami RU).

W przypadku elastycznego skalowania aprowizowanej przepływności można zwiększyć lub zmniejszyć aprowizowaną platformę RU/s w dowolnym momencie. Aby uzyskać więcej informacji, zobacz [Instrukcje aprowizowania przepływności](set-throughput.md) i elastycznie skalować kontenery i bazy danych usługi Cosmos. W przypadku globalnej przepływności skalowania można w dowolnym momencie dodać lub usunąć regiony z konta usługi Cosmos. Aby uzyskać więcej informacji, zobacz [Dodawanie/usuwanie regionów z konta bazy danych](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Kojarzenie wielu regionów z kontem usługi Cosmos jest ważne w wielu scenariuszach — aby osiągnąć małe opóźnienia i [wysoką dostępność](high-availability.md) na całym świecie.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Sposób dystrybucji przepływności aprowizowanej w różnych regionach

Jeśli aprowizować *'R'* RUs w kontenerze usługi Cosmos (lub bazy danych), Usługi Cosmos DB zapewnia, że *"R"* RUS są dostępne w *każdym* regionie skojarzonym z kontem usługi Cosmos. Za każdym razem, gdy dodasz nowy region do swojego konta, usługa Cosmos DB automatycznie udostępnia *program RUs w* nowo dodanym regionie. Operacje wykonywane względem kontenera usługi Cosmos są gwarantowane, aby uzyskać *"R"* RUs w każdym regionie. Nie można selektywnie przypisać procesorów RU do określonego regionu. Programy administracyjne aprowizacji w kontenerze usługi Cosmos (lub bazy danych) są aprowizacji we wszystkich regionach skojarzonych z kontem usługi Cosmos.

Zakładając, że kontener usługi Cosmos jest skonfigurowany z *"R"* RUs i istnieją *regiony "N"* skojarzone z kontem usługi Cosmos, a następnie:

- Jeśli konto Cosmos jest skonfigurowane z jednym regionem zapisu, całkowita liczba jednostek RR dostępnych globalnie w kontenerze = *R* x *N*.

- Jeśli konto Usługi Cosmos jest skonfigurowane z wieloma regionami zapisu, całkowita liczba obiektów obiektów użytkownika dostępna globalnie w kontenerze = *R* x (*N*+1). Dodatkowe *jednostek RI* są automatycznie aprowizowane do przetwarzania konfliktów aktualizacji i ruchu anty-entropii w regionach.

Wybór [modelu spójności](consistency-levels.md) wpływa również na przepływność. Można uzyskać około 2 x odczytu przepływności dla bardziej zrelaksowany poziom spójności (np. *sesja,* *spójny prefiks* i *spójność ostateczna)* w porównaniu do silniejszych poziomów spójności (np. *ograniczone nieaktualność* lub *silne* spójności).

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się, jak skonfigurować przepływność w kontenerze lub bazie danych:

* [Pobierz i ustaw przepływność kontenerów i baz danych](set-throughput.md) 

