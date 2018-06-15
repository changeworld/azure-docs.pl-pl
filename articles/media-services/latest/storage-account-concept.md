---
title: Konta magazynu w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak usługi Media Services używa konta magazynu.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 6d4c21867b0b46508f348300ae2b9553a75d23b2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788067"
---
# <a name="storage-accounts"></a>Konta magazynu

Podczas tworzenia konta usługi Media Services, należy podać nazwę zasobów konta usługi Azure Storage. Podanego konta magazynu jest dołączony do konta usługi Media Services. 

Musi mieć jeden **głównej** konta magazynu i może zawierać dowolną liczbę **dodatkowej** kont magazynu skojarzonych z Twoim kontem usługi Media Services. Usługa Media Services obsługuje **v2 ogólnego przeznaczenia** (GPv2) lub **v1 ogólnego przeznaczenia** kont (GPv1). 

>[!NOTE]
> Konta tylko obiektów blob nie są dozwolone jako **głównej**. 

Zaleca się, że używasz GPv2, aby można było wykorzystać wybór między gorąco i cool warstw magazynowania. Aby dowiedzieć się więcej na temat kont magazynu, zobacz [opcje konta magazynu Azure](../../storage/common/storage-account-options.md). 

## <a name="assets-in-a-storage-account"></a>Zasoby na koncie magazynu

W usługi Media Services w wersji 3 z interfejsów API magazynu są używane do przekazywania plików. Aby zobaczyć, jak używać interfejsów API magazynu z usługi Media Services można przekazać pliki danych wejściowych, zapoznaj się [utworzyć wprowadzania zadania z pliku lokalnego](job-input-from-local-file-how-to.md). 

> [!Note]
> Nie należy próbować zmiany zawartości kontenerów obiektów blob, które zostały wygenerowane przez zestaw SDK usługi Media bez korzystania z interfejsów API usługi multimediów.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak dołączyć konta magazynu do konta usługi Media Services, zobacz [utworzyć konto](create-account-cli-quickstart.md).
