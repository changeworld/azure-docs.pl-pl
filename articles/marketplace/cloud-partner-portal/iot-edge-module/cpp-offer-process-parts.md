---
title: Przegląd udostępniania oferty modułu Azure IoT Edge Portal Azure Marketplace
description: Przegląd procesu publikowania oferty modułu IoT Edge w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: pabutler
ms.openlocfilehash: 97df9a61d15e0d90e81f42cef327aea23873ffa0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814321"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>Przegląd udostępniania oferty modułu IoT Edge

<table> <tr> <td>W tej sekcji opisano sposób publikowania nowej oferty modułu Azure IoT Edge w <a href="https://azuremarketplace.microsoft.com">portalu Microsoft Azure Marketplace</a>. Moduł IoT Edge jest kontenerem zgodnym z platformą Docker, który jest uruchamiany na urządzeniu IoT Edge. Moduły Azure IoT Edge są najmniejszą jednostką obliczeniową zarządzaną przez IoT Edge i mogą zawierać usługi platformy Azure lub niestandardowy kod rozwiązania. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>Proces publikowania

Poniżej przedstawiono ogólne kroki publikowania oferty modułu IoT Edge:

1. Tworzenie oferty<br> Podaj szczegółowe informacje o ofercie. Te informacje obejmują: opis oferty, materiały marketingowe, informacje o pomocy technicznej i specyfikacje zasobów.

2. Tworzenie zasobów firmowych i technicznych<br> Utwórz zasoby biznesowe (dokumenty prawne i materiały marketingowe) i zasoby techniczne dla skojarzonego rozwiązania (IoT Edge obrazy modułu hostowane w Azure Container Registry.

3. Tworzenie jednostki SKU<br> Utwórz jednostki SKU skojarzone z ofertą. Dla każdego obrazu, który ma zostać opublikowany, wymagana jest unikatowa jednostka SKU.

4. Certyfikowanie i publikowanie oferty <br>Po zakończeniu oferty i zasobów technicznych można przesłać ofertę. To przesyłanie rozpocznie proces publikowania. W trakcie tego procesu rozwiązanie jest testowane, weryfikowane, certyfikowane, a następnie "trafia na żywo" w portalu Azure Marketplace.

## <a name="parts-of-an-offer"></a>Części oferty

Poniższe artykuły obejmują podstawowe elementy oferty modułu IoT Edge.

- [Wymagania wstępne](./cpp-prerequisites.md) <br>W tym artykule wymieniono wymagania techniczne i biznesowe przed utworzeniem lub opublikowaniem oferty modułu IoT Edge.
- [Przygotuj zasoby techniczne modułu IoT Edge](./cpp-create-technical-assets.md) <br>W tym artykule opisano sposób przygotowania zasobów technicznych dla modułu IoT Edge. Te zasoby muszą spełniać wszystkie wymagane kryteria techniczne przed opublikowaniem modułu IoT Edge w portalu Azure Marketplace.
- [Tworzenie oferty modułu usługi IoT Edge](./cpp-create-offer.md) <br>W tym artykule wymieniono kroki wymagane do utworzenia nowego wpisu oferty modułu IoT Edge przy użyciu [Portal Cloud partner](https://cloudpartner.azure.com).
- [Publikowanie oferty modułu usługi IoT Edge](./cpp-publish-offer.md)<br> W tym artykule opisano sposób przesyłania oferty do opublikowania w portalu Azure Marketplace.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [wymaganiami technicznymi i biznesowymi](./cpp-prerequisites.md) dotyczącymi publikowania modułu IoT Edge w Microsoft Azure Marketplace.
