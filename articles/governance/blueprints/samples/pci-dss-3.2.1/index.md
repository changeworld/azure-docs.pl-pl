---
title: Przykład strategii PCI-DSS v3.2.1 — omówienie
description: Omówienie przykładu strategii Payment Card Industry Data Security Standard 3.2.1. Ten przykład strategii pomaga klientom ocenić określone mechanizmy kontroli.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 4dbfd750d8a12e954eea981e24c4255218c4af72
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72297129"
---
# <a name="overview-of-the-pci-dss-v321-blueprint-sample"></a>Omówienie przykładu strategii PCI-DSS 3.2.1

Przykładowa strategia PCI-DSS 3.2.1 to zestaw zasad, które pomagają zapewnić zgodność ze standardem PCI-DSS 3.2.1. Ułatwia ona klientom zarządzanie środowiskami chmurowymi z obciążeniami PCI-DSS.
Strategia PCI-DSS umożliwia wdrożenie podstawowego zestawu zasad dla architektury wdrożonej przez usługę Azure, która wymaga tej akredytacji.

## <a name="control-mapping"></a>Mapowanie kontrolek

Sekcja mapowania kontrolek zawiera szczegółowe informacje dotyczące zasad w ramach tej inicjatywy i o tym, jak te zasady pomagają spełnić wymagania różnych kontrolek zdefiniowanych zgodnie ze standardem PCI-DSS 3.2.1. Po przypisaniu do architektury zasoby są oceniane przez usługę Azure Policy pod względem braku zgodności z przypisanymi zasadami.

Po przypisaniu tej strategii wyświetl poziom zgodności środowisk platformy Azure na pulpicie nawigacyjnym zgodności usługi Azure Policy.

## <a name="next-steps"></a>Następne kroki

Przeczytano już omówienie przykładu strategii PCI-DSS 3.2.1. Następnie przeczytaj następujące artykuły, aby poznać mapowanie kontrolek i sposób wdrażania tego przykładu:

> [!div class="nextstepaction"]
> [Strategia PCI-DSS 3.2.1 — mapowanie kontrolek](./control-mapping.md)
> [Strategia PCI-DSS 3.2.1 — procedura wdrażania](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).