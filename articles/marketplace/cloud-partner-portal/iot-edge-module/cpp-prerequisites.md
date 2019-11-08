---
title: Wymagania wstępne dotyczące Azure IoT Edge modułu | Portal Azure Marketplace
description: Wymagania wstępne dotyczące publikowania modułu IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 610f9bc8d87ac75e2868e247fe1332574480b498
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813874"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Wymagania wstępne dotyczące publikowania modułu IoT Edge

W tym artykule opisano wymagania wstępne dotyczące publikowania oferty modułu IoT Edge.  Jeśli jeszcze tego nie zrobiono, zapoznaj się z [przewodnikiem publikowania modułów IoT Edge](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Publikowanie wymagań wstępnych

Aby opublikować moduł IoT Edge w portalu Azure Marketplace, należy spełnić następujące wymagania wstępne:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Dostęp do [Portal Cloud partner](https://cloudpartner.azure.com/). Aby uzyskać więcej informacji, zobacz artykuł [Azure Marketplace i Podręcznik publikowania AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Umowa dotycząca [warunków korzystania z witryny Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Hostowanie zasobów technicznych modułu IoT Edge w Azure Container Registry.  Aby uzyskać więcej informacji, zobacz [jak przygotować zasób techniczny modułu IoT Edge](./cpp-create-technical-assets.md)
- Przygotuj metadane modułu IoT Edge do użycia. Na przykład Przygotuj następujące zasoby:
    - Tytuł
    - Opis (w formacie HTML)
    - Obraz logo (format PNG i stałe rozmiary obrazu, w tym 40x40px, 90x90px, 115x115px, 255x115px)
    - Warunki użytkowania i zasady zachowania poufności informacji
    - Domyślna konfiguracja modułu obejmująca: trasy, odpowiednie właściwości, opcje i zmienne środowiskowe.
    - Dokumentacja modułu
    - Skontaktuj się z pomocą techniczną


## <a name="next-steps"></a>Następne kroki

Po przywróceniu [zasobów technicznych modułu IoT Edge](./cpp-create-technical-assets.md)będziesz mieć możliwość [utworzenia oferty modułu IoT Edge](./cpp-create-offer.md). 
