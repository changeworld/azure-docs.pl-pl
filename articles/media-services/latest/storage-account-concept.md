---
title: Konta magazynu w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: W tym artykule omówiono, jak usługa Media Services używa konta magazynu.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 90e01f39fa6b31095d76d0dfae2f700b4fa2ca3f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182871"
---
# <a name="storage-accounts"></a>Konta magazynu

Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. 

Musisz mieć jedno **główne** konto magazynu i możesz mieć dowolną liczbę **dodatkowych** kont magazynu skojarzonych z Twoim kontem usługi Media Services. Usługa Media Services obsługuje konta **Ogólnego przeznaczenia, wersja 2** (GPv2) i **Ogólnego przeznaczenia, wersja 1** (GPv1). 

>[!NOTE]
> Konta tylko obiektów blob nie są dozwolone jako **główne**. 

Firma Microsoft zaleca korzystanie z kont GPv2, aby można było korzystać z zalet wybór między gorąca i warstwy magazynowania chłodna. Aby dowiedzieć się więcej na temat kont magazynu, zobacz [Przegląd konta usługi Azure Storage](../../storage/common/storage-account-overview.md). 

Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zdecydowanie zaleca się używanie kont magazynu w tej samej lokalizacji co konto usługi Media Services, aby uniknąć dodatkowych kosztów opóźnienia i danych wychodzących

## <a name="assets-in-a-storage-account"></a>Zasoby w ramach konta magazynu

W wersji 3 usługa Media Services interfejsy API magazynu są używane do przekazywania plików. Aby zobaczyć, jak używać interfejsów API Storage z usługą Media Services do przekazywanie plików wejściowych, zapoznaj się z [utworzyć dane wejściowe zadania z pliku lokalnego](job-input-from-local-file-how-to.md). 

> [!Note]
> Nie należy próbować zmienić zawartość kontenerów obiektów blob, które zostały wygenerowane przez zestaw SDK usług Media Services bez korzystania z interfejsów API usług Media Services.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak dołączyć konto magazynu do konta usługi Media Services, zobacz [Tworzenie konta usługi](create-account-cli-quickstart.md).
