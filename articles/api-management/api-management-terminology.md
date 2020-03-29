---
title: Terminologia usługi Azure API Management | Dokumenty firmy Microsoft
description: W tym artykule podano definicje terminów, które są specyficzne dla zarządzania interfejsem API.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61074058"
---
# <a name="terminology"></a>Terminologia

W tym artykule podano definicje terminów, które są specyficzne dla api management (APIM).

## <a name="term-definitions"></a>Definicje terminów

* **Interfejs API wewnętrznej bazy danych** — usługa HTTP, która implementuje interfejs API i jego operacje. 
* **Interfejs APIM interfejsu**/**APIM** interfejsu API-u — interfejs APIM nie obsługuje interfejsów API, tworzy fasady dla interfejsów API w celu dostosowania elewacji do potrzeb bez dotykania interfejsu API zaplecza. Aby uzyskać więcej informacji, zobacz [Importowanie i publikowanie interfejsu API](import-and-publish.md).
* **Produkt APIM** — produkt zawiera co najmniej jeden interfejs API, a także przydział użycia i warunki użytkowania. Możesz dołączyć wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu dla deweloperów. Aby uzyskać więcej informacji, zobacz [Tworzenie i publikowanie produktu](api-management-howto-add-products.md).
* **Działanie apim API API** — każdy interfejs APIM apim reprezentuje zestaw operacji dostępnych dla deweloperów. Każdy interfejs APIM APIM zawiera odwołanie do usługi zaplecza, która implementuje interfejs API, a jego operacje są mapowane na operacje implementowane przez usługę zaplecza. Aby uzyskać więcej informacji, zobacz [Mock odpowiedzi interfejsu API](mock-api-responses.md).
* **Wersja** — czasami chcesz opublikować nowe lub różne funkcje interfejsu API dla niektórych użytkowników, podczas gdy inni chcą trzymać się interfejsu API, który obecnie działa dla nich. Aby uzyskać więcej informacji, zobacz [Publikowanie wielu wersji interfejsu API](api-management-get-started-publish-versions.md).
* **Wersja** — gdy interfejs API jest gotowy do pracy i zaczyna być używany przez deweloperów, zwykle należy zadbać o wprowadzanie zmian w tym interfejsie API, a jednocześnie nie zakłócać wywoływania interfejsu API. Ponadto warto poinformować deweloperów o wprowadzanych zmianach. Aby uzyskać więcej informacji, zobacz [Używanie poprawek](api-management-get-started-revise-api.md).
* **Portal dla deweloperów** — Twoi klienci (deweloperzy) powinni korzystać z portalu dla deweloperów, aby uzyskać dostęp do interfejsów API. Portal dla deweloperów można dostosować. Aby uzyskać więcej informacji, zobacz [Dostosowywanie portalu dla deweloperów](api-management-customize-styles.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie wystąpienia](get-started-create-service-instance.md)

