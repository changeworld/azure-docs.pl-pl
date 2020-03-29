---
title: Omówienie publikowania oferty usługi Azure IoT Edge Module | Azure Marketplace
description: Omówienie procesu publikowania oferty modułów usługi IoT Edge w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: dsindona
ms.openlocfilehash: 6b44d9fd0bdd6e4b41013373472b5882a7ca1434
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286595"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>Omówienie publikowania modułów IoT Edge

<table> <tr> <td>W tej sekcji wyjaśniono, jak opublikować nową ofertę modułu Usługi Azure IoT Edge w portalu Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace.</a> Moduł usługi IoT Edge to kontener zgodny z docker, który jest uruchamiany na urządzeniu usługi IoT Edge. Moduły usługi Azure IoT Edge są najmniejszą jednostką obliczeń zarządzaną przez usługę IoT Edge i mogą zawierać usługi platformy Azure lub niestandardowy kod rozwiązania. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>Proces publikowania

Kroki wysokiego poziomu publikowania oferty modułów IoT Edge to:

1. Tworzenie oferty<br> Podaj szczegółowe informacje na temat oferty. Informacje te obejmują: opis oferty, materiały marketingowe, informacje o pomocy technicznej i specyfikacje zasobów.

2. Tworzenie zasobów biznesowych i technicznych<br> Utwórz zasoby biznesowe (dokumenty prawne i materiały marketingowe) oraz zasoby techniczne skojarzonego rozwiązania (obrazy modułów usługi IoT Edge hostowane w rejestrze kontenerów platformy Azure.

3. Tworzenie jednostki SKU<br> Utwórz jednostki SKU skojarzone z ofertą. Unikatowa jednostka SKU jest wymagana dla każdego obrazu, który planujesz opublikować.

4. Certyfikuj i publikuj ofertę <br>Po zakończeniu oferty i zasobów technicznych można złożyć ofertę. To przesłanie rozpoczyna proces publikowania. Podczas tego procesu rozwiązanie jest testowane, sprawdzane, certyfikowane, a następnie "przechodzi na rynek" w portalu Azure Marketplace.

## <a name="parts-of-an-offer"></a>Części oferty

Poniższe artykuły obejmują kluczowe części oferty modułu IoT Edge.

- [Wymagania wstępne](./cpp-prerequisites.md) <br>W tym artykule wymieniono wymagania techniczne i biznesowe przed utworzeniem lub opublikowaniem oferty modułów usługi IoT Edge.
- [Przygotowywanie zasobów technicznych modułu IoT Edge](./cpp-create-technical-assets.md) <br>W tym artykule opisano sposób przygotowania zasobów technicznych dla modułu usługi IoT Edge. Te zasoby muszą spełniać wszystkie wymagane kryteria techniczne, zanim moduł usługi IoT Edge będzie mógł zostać opublikowany w portalu Azure Marketplace.
- [Tworzenie oferty modułu usługi IoT Edge](./cpp-create-offer.md) <br>W tym artykule wymieniono kroki wymagane do utworzenia nowego wpisu oferty modułu usługi IoT Edge przy użyciu [portalu Cloud Partner Portal](https://cloudpartner.azure.com).
- [Publikowanie oferty modułu usługi IoT Edge](./cpp-publish-offer.md)<br> W tym artykule opisano sposób przesyłania oferty publikowania w portalu Azure Marketplace.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [wymaganiami technicznymi i biznesowymi dotyczącymi](./cpp-prerequisites.md) publikowania modułu usługi IoT Edge w portalu Microsoft Azure Marketplace.
