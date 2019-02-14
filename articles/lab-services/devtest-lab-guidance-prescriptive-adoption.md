---
title: Wdrażanie usługi Azure DevTest Labs w firmie.
description: Ten artykuł zawiera wskazówki dotyczące wdrażania usługi Azure DevTest Labs w przedsiębiorstwie.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: f16bdb8b89c2ce72c2a31a320d2dedbc4df09c81
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242791"
---
# <a name="set-up-azure-devtest-labs-infrastructure-in-your-enterprise"></a>Konfigurowanie infrastruktury usługi Azure DevTest Labs w przedsiębiorstwie
Przedsiębiorstwom adaptują chmurę szybko z powodu jego korzyści z elastyczności, elastyczności i oszczędnościami, jakie daje perspektywy. Zwykle pierwszym krokiem do chmury to migrację obciążeń tworzenia i testowania. Brak wspólnego zestawu problemy podczas migracji tych obciążeń, takich jak poniższych: 

- Zabezpieczanie zasobów tworzenia/testowania
- Jednoznacznie odseparować deweloperskich/testowych i produkcyjnych
- Dzielenie zasobów między IT, aplikacji i zespołów projektu
- Zarządzanie kosztami
- Dzięki czemu samoobsługi konfigurowania i testowania infrastruktury bez uszczerbku dla zabezpieczeń

## <a name="intended-audience"></a>Docelowi odbiorcy
Ten dokument jest przeznaczony dla planistów IT, architektów i menedżerów, którzy są odpowiedzialni za ustanowienie i przeglądanie ogólnej wdrożeń i nadzorowanie rozwiązania w zakresie operacji. W rezultacie w tym dokumencie kładzie nacisk całego procesu i zalecane zasad projektowania, aby wspierać środowisku programistycznym bezpieczne i stabilne, ostatecznie zależy wdrożenie usługi Azure DevTest Labs w organizacji.

## <a name="next-steps"></a>Kolejne kroki
- [Rozpoczynanie pracy z usługą za pomocą usługi Azure DevTest Labs](devtest-lab-guidance-get-started.md)