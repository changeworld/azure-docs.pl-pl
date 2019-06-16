---
title: Terminologii platformy Azure API Management | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera definicje dla terminów, które są specyficzne dla usługi API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: b99ca444532799d21850058eae0e3f40ed871135
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61074058"
---
# <a name="terminology"></a>Terminologia

Ten artykuł zawiera definicje dla terminów, przeznaczone dla protokołu API Management (APIM).

## <a name="term-definitions"></a>Definicje terminów

* **Interfejs API zaplecza** — usługa HTTP, która implementuje interfejs API i jego operacje. 
* **Interfejs API frontonu**/**interfejsu API usługi APIM** -interfejsu API usługi APIM nie obsługuje interfejsów API, tworzy fasad dla interfejsów API w celu Dostosowywanie fasady zgodnie z potrzebami bez dotykania tylnej wewnętrznego interfejsu API. Aby uzyskać więcej informacji, zobacz [importowanie i publikowanie interfejsu API](import-and-publish.md).
* **Produkt APIM** — produkt zawiera jeden lub więcej interfejsów API oraz limit przydziału użycia i warunki użytkowania. Można uwzględnić wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu dla deweloperów. Aby uzyskać więcej informacji, zobacz [tworzenie i publikowanie produktu](api-management-howto-add-products.md).
* **Operacja interfejsu API usługi APIM** -każdego interfejsu API usługi APIM reprezentuje zestaw operacji dostępnych dla deweloperów. Każdy interfejs API APIM zawiera odwołanie do usługi zaplecza, która implementuje interfejs API oraz mapę odwzorowań operacji operacje zaimplementowane w usłudze zaplecza. Aby uzyskać więcej informacji, zobacz [odpowiedzi testowanie interfejsu API](mock-api-responses.md).
* **Wersja** — czasami chcesz opublikować nowy lub inny interfejs API funkcji niektórym użytkownikom, podczas gdy inne korzystać przy użyciu interfejsu API, które obecnie są obsługiwane dla nich. Aby uzyskać więcej informacji, zobacz [publikowanie wielu wersji interfejsu API](api-management-get-started-publish-versions.md).
* **Poprawka** — w przypadku interfejsu API jest gotowe do go i zaczyna być używany przez deweloperów, zazwyczaj można trzeba zadbać o wprowadzanie zmian do tego interfejsu API i w tym samym czasie, aby nie przerywać pracy wywołującym interfejs API. Ponadto warto poinformować deweloperów o wprowadzanych zmianach. Aby uzyskać więcej informacji, zobacz [przy użyciu poprawek](api-management-get-started-revise-api.md).
* **Portal dla deweloperów** -klientów (deweloperzy) powinien umożliwia dostęp do interfejsów API portalu dla deweloperów. Można dostosować portal dla deweloperów. Aby uzyskać więcej informacji, zobacz [dostosowywania portalu deweloperów](api-management-customize-styles.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie wystąpienia usługi](get-started-create-service-instance.md)

