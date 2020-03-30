---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67182982"
---
**1. W jaki sposób klienci zostaną powiadomieni o wycofanym SDK?**

Firma Microsoft zapewni powiadomienie z 12-miesięcznym wyprzedzeniem po zakończeniu obsługi wycofanego sdk w celu ułatwienia płynnego przejścia do obsługiwanego sdk. Ponadto klienci będą powiadamiani za pośrednictwem różnych kanałów komunikacji — Azure Management Portal, Developer Center, wpis w blogu i bezpośredniej komunikacji z przypisanymi administratorami usług.

**2. Czy klienci mogą tworzyć aplikacje przy użyciu wycofanego sdk usługi Azure Cosmos DB w okresie 12 miesięcy?** 

Tak, klienci będą mieli pełny dostęp do autora, wdrażania i modyfikowania aplikacji przy użyciu wycofanych sdk usługi Azure Cosmos DB w okresie prolongaty 12 miesięcy. Podczas 12-miesięcznego okresu prolongaty zaleca się klientom migrację do nowszej obsługiwanej wersji sdk usługi Azure Cosmos DB, stosownie do przypadku.

**3. Czy klienci mogą tworzyć i modyfikować aplikacje przy użyciu wycofanego sdk usługi Azure Cosmos DB po 12-miesięcznym okresie powiadamiania?**

Po upływie 12 miesięcy okresu powiadomienia sdk zostanie wycofany. Każdy dostęp do usługi Azure Cosmos DB przez aplikacje przy użyciu wycofanego zestawu SDK nie będą dozwolone przez platformę usługi Azure Cosmos DB. Ponadto firma Microsoft nie zapewni obsługi klienta w wycofanym sdku.

**4. Co się stanie z uruchomionymi aplikacjami Klienta, które korzystają z nieobsługiconej wersji SDK usługi Azure Cosmos DB?**

Wszelkie próby połączenia się z usługą Azure Cosmos DB z wycofaną wersją SDK zostaną odrzucone. 

**5. Czy nowe funkcje i funkcje będą stosowane do wszystkich nienasyconych sdk?**

Nowe funkcje i funkcje zostaną dodane tylko do nowych wersji. Jeśli używasz starej, nieudzielonejedna, wersja SDK żądania do usługi Azure Cosmos DB będzie nadal działać jako poprzedni, ale nie będzie miał dostępu do żadnych nowych możliwości.  

**6. Co należy zrobić, jeśli nie mogę zaktualizować zgłoszenia przed datą daty daty daty daty daty daty ścięcia?**

Zaleca się uaktualnienie do najnowszego sdk jak najszybciej. Po oznaczeniu SDK na emeryturę będziesz miał 12 miesięcy na zaktualizowanie aplikacji. Jeśli z jakiegokolwiek powodu nie można ukończyć aktualizacji aplikacji w tym przedziale czasowym, skontaktuj się z [zespołem usługi Cosmos DB](mailto:askcosmosdb@microsoft.com) i poproś o pomoc przed datą odcięcia.

