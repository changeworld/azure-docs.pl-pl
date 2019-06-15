---
title: Oferta Azure moduł usługi IoT Edge publikowania — omówienie | Portal Azure Marketplace
description: Omówienie procesu publikowania moduł usługi IoT Edge oferty w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: pabutler
ms.openlocfilehash: 319031ec99d449ea5866bb5234cc617145954173
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942612"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>Moduł usługi IoT Edge oferują publikowania — omówienie

<table> <tr> <td>W tej sekcji wyjaśniono, jak opublikować nową ofertę modułu usługi Azure IoT Edge Microsoft <a href="https://azuremarketplace.microsoft.com">portalu Azure Marketplace</a>. Moduł usługi IoT Edge to kontener zgodnego z platformą Docker, który wprowadził do uruchamiania na urządzeniu usługi IoT Edge. Moduły platformy Azure IoT Edge to najmniejsza obliczeń zarządzane przez usługi IoT Edge i może zawierać usług platformy Azure lub kodu niestandardowego rozwiązania. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>Proces publikowania

Dostępne są następujące ogólne kroki programu stawiane ofertom moduł usługi IoT Edge:

1. Tworzenie oferty<br> Podaj szczegółowe informacje na temat tej oferty. Informacje te obejmują: opis oferty, materiały marketingowe, informacje o pomocy technicznej i specyfikacje zasobów.

2. Tworzenie biznesowych i zasoby techniczne<br> Tworzenie zasobów biznesowych (dokumenty prawne i materiały marketingowe) i zasoby techniczne dotyczące rozwiązania skojarzone (obrazy modułu usługi IoT Edge hostowanej w usłudze Azure Container Registry.

3. Tworzenie jednostki SKU<br> Utwórz dla jednostek SKU, skojarzone z ofertą. Unikatowe jednostki SKU jest wymagana dla każdego obrazu, w którym planujesz opublikować.

4. Certyfikowanie i publikowanie oferty <br>Po ukończeniu oferta i zasoby techniczne mogą przesyłać oferty. Ten przesłany rozpoczyna się proces publikowania. W trakcie tego procesu to rozwiązanie jest testowany, zweryfikowany, certyfikowane, następnie "miejsce na żywo" w witrynie Azure Marketplace.

## <a name="parts-of-an-offer"></a>Części oferty

Następujące artykuły omówiono kluczowe elementy oferty modułu usługi IoT Edge.

- [Wymagania wstępne](./cpp-prerequisites.md) <br>W tym artykule wymieniono techniczne i oferty wymagania biznesowe można było utworzyć lub opublikować moduł usługi IoT Edge.
- [Przygotuj zasoby techniczne moduł usługi IoT Edge](./cpp-create-technical-assets.md) <br>W tym artykule opisano sposób przygotowania zasoby techniczne dla modułu usługi IoT Edge. Te zasoby muszą spełniać wszystkie wymagane kryteriów technicznych, moduł usługi IoT Edge można było opublikować w portalu Azure Marketplace.
- [Tworzenie oferty modułu usługi IoT Edge](./cpp-create-offer.md) <br>W tym artykule przedstawiono kroki wymagane do utworzenia nowej usługi IoT Edge modułu oferty wpisu, używając [portalu Cloud Partner](https://cloudpartner.azure.com).
- [Publikowanie oferty modułu usługi IoT Edge](./cpp-publish-offer.md)<br> W tym artykule opisano, jak w celu przesłania oferty do opublikowania w portalu Azure Marketplace.

## <a name="next-steps"></a>Kolejne kroki

Przegląd [wymagania techniczne i biznesowe](./cpp-prerequisites.md) publikowania moduł usługi IoT Edge w portalu Microsoft Azure Marketplace.
