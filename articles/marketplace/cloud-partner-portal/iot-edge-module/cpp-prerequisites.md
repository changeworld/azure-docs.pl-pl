---
title: Wymagania wstępne modułu usługi Azure IoT Edge | Azure Marketplace
description: Wymagania wstępne dotyczące publikowania modułu IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 93e0418004ea65f93ad436eec1ce523424a194ba
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744969"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Wymagania wstępne dotyczące publikowania modułu IoT Edge

>[!Important]
>Od 30 marca 2020 r. rozpoczniemy przenoszenie zarządzania ofertami modułu IoT Edge do Centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [tworzenie oferty modułu IoT Edge,](https://aka.ms/AzureCreateIoT) aby zarządzać zmigrowanymi ofertami.

W tym artykule opisano wymagania wstępne dotyczące publikowania oferty modułu IoT Edge.  Jeśli jeszcze tego nie zrobiono, zapoznaj się z [przewodnikiem publikowania modułów IoT Edge](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Wymagania wstępne publikowania

Aby opublikować moduł usługi IoT Edge w portalu Azure Marketplace, musisz spełnić następujące wymagania wstępne:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Dostęp do [portalu cloud partnerów](https://cloudpartner.azure.com/). Aby uzyskać więcej informacji, zobacz [Przewodnik po publikacjach w portalu Azure Marketplace i AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Umowa z [warunkami portalu Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Hostuj zasób techniczny modułu usługi IoT Edge w rejestrze kontenerów platformy Azure.  Aby uzyskać więcej informacji, [zobacz, jak przygotować zasób techniczny modułu IoT Edge](./cpp-create-technical-assets.md)
- Przygotuj metadane modułu IoT Edge do użycia. Na przykład przygotuj następujące zasoby:
    - Tytuł
    - Opis (w formacie HTML)
    - Obraz logo (format PNG i stałe rozmiary obrazów, w tym 40x40px, 90x90px, 115x115px, 255x115px)
    - Termin użytkowania i polityka prywatności
    - Domyślna konfiguracja modułu, która obejmuje: trasy, dwie wartości żądanych właściwości, createOptions i zmienne środowiskowe.
    - Dokumentacja modułu
    - Kontakt z pomocą techniczną


## <a name="next-steps"></a>Następne kroki

Po [przygotowaniu zasobu technicznego modułu IoT Edge](./cpp-create-technical-assets.md)będziesz gotowy do [utworzenia oferty modułów IoT Edge.](./cpp-create-offer.md) 
