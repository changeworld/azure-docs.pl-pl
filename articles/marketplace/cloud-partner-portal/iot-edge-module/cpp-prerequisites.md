---
title: Wymagania wstępne modułu usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Wymagania wstępne dotyczące publikowania moduł usługi IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 7271a97dc7ab9de1840d809ded0ba1c2940ed83f
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431654"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Publikowanie wymagań wstępnych modułu usługi IoT Edge

W tym artykule opisano wymagania wstępne programu stawiane ofertom moduł usługi IoT Edge.

Aby dowiedzieć się więcej na temat moduły usługi IoT Edge i zalety publikowania moduł w portalu Azure Marketplace, zobacz [moduły usługi IoT Edge Podręcznik publikowania](https://docs.microsoft.com/azure/marketplace/iot-edge-module).

## <a name="publishing-prerequisites"></a>Wymagania wstępne publikowania

Aby opublikować moduł usługi IoT Edge w portalu Azure Marketplace, należy spełnić następujące wymagania wstępne:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Dostęp do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/). Aby uzyskać więcej informacji, zobacz [Podręcznik publikowania w portalu Azure Marketplace i AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Umowy [warunki witryny Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Hosta usługi IoT Edge modułu techniczne elementów zawartości w usłudze Azure Container Registry.  Aby uzyskać więcej informacji, zobacz [jak przygotować zasobów technicznych moduł usługi IoT Edge](./cpp-create-technical-assets.md)
- Ma metadane modułu usługi IoT Edge gotowe do użycia. Na przykład (niepełna lista):
    - Tytuł
    - Opis (format HTML)
    - Obraz logo (PNG format i rozmiary Stały obraz, w tym 40x40px 90x90px, 115x115px, 255x115px)
    - Okres użycia i zasady zachowania poufności
    - Domyślna konfiguracja modułu, która obejmuje: twin tras, żądanych właściwości, CreateOptions, można żądań i zmiennych środowiskowych.
    - Dokumentacja
    - Kontakt z pomocą techniczną

## <a name="next-steps"></a>Kolejne kroki

- [Przygotowanie zasobów technicznych moduł usługi IoT Edge](./cpp-create-technical-assets.md)
- [Utwórz ofertę moduł usługi IoT Edge](./cpp-create-offer.md)
