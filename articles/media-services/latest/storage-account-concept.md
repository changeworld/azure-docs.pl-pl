---
title: Przekazywanie i magazynu przy użyciu usługi Azure Media Services chmurze | Dokumentacja firmy Microsoft
description: Ten artykuł chmury przekazywania i kwestie dotyczące magazynu.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/21/2019
ms.author: juliako
ms.openlocfilehash: 471bc34272b8e141c8640bd218bdafd840850d24
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672274"
---
# <a name="cloud-upload-and-storage"></a>Przekazywanie do chmury i magazynowanie w niej

Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto usługi Media Services. Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. 

Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zdecydowanie zaleca się używanie kont magazynu w tej samej lokalizacji co konto usługi Media Services, aby uniknąć dodatkowych kosztów opóźnienia i danych wychodzących

Musisz mieć jedno **główne** konto magazynu i możesz mieć dowolną liczbę **dodatkowych** kont magazynu skojarzonych z Twoim kontem usługi Media Services. Usługa Media Services obsługuje konta **Ogólnego przeznaczenia, wersja 2** (GPv2) i **Ogólnego przeznaczenia, wersja 1** (GPv1). 

>[!NOTE]
> Konta tylko obiektów blob nie są dozwolone jako **główne**. 

Firma Microsoft zaleca korzystanie z kont GPv2, aby można było korzystać z zalet wybór między gorąca i warstwy magazynowania chłodna. Aby dowiedzieć się więcej na temat kont magazynu, zobacz [Przegląd konta usługi Azure Storage](../../storage/common/storage-account-overview.md). 

Istnieją różne jednostki SKU, można wybrać konta magazynu. Aby uzyskać więcej informacji, zobacz [kont magazynu](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Jeśli chcesz poeksperymentować z kontami magazynu, użyj `--sku Standard_LRS`. Jednak podczas wybierania jednostki SKU w środowisku produkcyjnym należy rozważyć, `--sku Standard_RAGRS`, zapewniającą replikacji geograficznej dla ciągłości działania. 

## <a name="assets-in-a-storage-account"></a>Zasoby w ramach konta magazynu

W wersji 3 usługa Media Services interfejsy API magazynu są używane do przekazywania plików.

> [!Note]
> Nie należy próbować zmienić zawartość kontenerów obiektów blob, które zostały wygenerowane przez zestaw SDK usług Media Services bez korzystania z interfejsów API usług Media Services.

Aby zobaczyć, jak używać interfejsów API Storage z usługą Media Services do przekazywanie plików wejściowych, zapoznaj się z [utworzyć dane wejściowe zadania z pliku lokalnego](job-input-from-local-file-how-to.md). 
 
## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak dołączyć konto magazynu do konta usługi Media Services, zobacz [Tworzenie konta usługi](create-account-cli-quickstart.md).
