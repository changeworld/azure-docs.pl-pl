---
title: Kopiowanie lub klonowanie fabrykę danych w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kopiowanie lub klonowanie fabrykę danych w usłudze Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 96ea8142e2f7794d3c15c6efb436eafa585bc8fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60780935"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Kopiowanie lub klonowanie fabrykę danych w usłudze Azure Data Factory

W tym artykule opisano, jak mają być kopiowane lub sklonować fabrykę danych w usłudze Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Zastosowań klonowania fabryki danych

Poniżej przedstawiono okoliczności, w których może okazać się przydatne kopiowanie lub klonowanie fabrykę danych:

-   **Zmiana nazwy zasobów**. Platforma Azure nie obsługuje zmiany nazw zasobów. Jeśli chcesz zmienić nazwę fabryki danych, możesz sklonować z usługi data factory pod inną nazwą, a następnie usuń istniejącą grupę.

-   **Debugowanie zmian** po funkcji debugowania nie są wystarczające. Czasami Aby przetestować zmiany, możesz testować wprowadzane zmiany w różnych ustawień fabrycznych przed zastosowaniem ich do swojej jednego głównego. W większości przypadków można użyć debugowania. Zmiany w wyzwalacze, jednak takie jak sposób zmiany działają w przypadku wyzwalacza jest wywoływane automatycznie, lub przedziale czasu, może nie być sprawdzalnego działa zgodnie łatwo bez zaewidencjonowania. W takich przypadkach klonowania fabryki i stosowania zmian sprawia, że jest bardzo rozsądne. Ponieważ przede wszystkim Liczba przebiegów opłaty za usługę Azure Data Factory, drugi fabryki nie prowadzi do żadnych dodatkowych kosztów.

## <a name="how-to-clone-a-data-factory"></a>Jak sklonować fabryki danych

1. Interfejs użytkownika usługi Data Factory w witrynie Azure portal umożliwia wyeksportowanie ładunku całej fabryki danych do szablonu usługi Resource Manager oraz plik parametrów, który pozwala zmienić wszelkie wartości, które chcesz zmienić podczas klonowania fabryką.

1. Jako warunek wstępny należy utworzyć fabrykę danych docelowych w witrynie Azure portal.

1. Jeśli masz SelfHosted IntegrationRuntime w fabryce źródła, musisz ponownie go utworzyć o tej samej nazwie w fabryce docelowego. Jeśli chcesz udostępnić IRs SelfHosted między różnych fabryk, możesz użyć wzorca opublikowane [tutaj](author-visually.md#best-practices-for-git-integration).

1. Jeśli jesteś w trybie GIT, za każdym razem, gdy publikowanie z poziomu portalu, fabryka jest szablon usługi Resource Manager są zapisywane do repozytorium GIT w gałęzi adf_publish repozytorium.

1. W innych sytuacjach można pobrać szablon usługi Resource Manager, klikając **szablonu usługi Resource Manager wyeksportować** przycisk w portalu.

1. Po pobraniu szablonu usługi Resource Manager, można ją wdrożyć za pomocą standardowej metody wdrażania szablonu usługi Resource Manager.

1. Ze względów bezpieczeństwa wygenerowany szablon usługi Resource Manager nie zawiera żadnych poufne informacje, takie jak hasła dla połączonej usługi. W rezultacie należy podać te hasła jako parametrów wdrożenia. Podanie parametrów nie jest pożądane, musisz uzyskać parametry połączenia i hasła usługi połączone z usługi Azure Key Vault.

## <a name="next-steps"></a>Kolejne kroki

Przejrzyj wskazówki dotyczące tworzenia fabryki danych w witrynie Azure portal w [tworzenie fabryki danych przy użyciu interfejsu użytkownika usługi Azure Data Factory](quickstart-create-data-factory-portal.md).
