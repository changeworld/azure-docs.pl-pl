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
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: dbd8dea32bbd4cacefd64e91541ab20b1056731e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49379038"
---
# <a name="storage-accounts"></a>Konta magazynu

Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. 

Musisz mieć jedno **główne** konto magazynu i możesz mieć dowolną liczbę **dodatkowych** kont magazynu skojarzonych z Twoim kontem usługi Media Services. Usługa Media Services obsługuje konta **Ogólnego przeznaczenia, wersja 2** (GPv2) i **Ogólnego przeznaczenia, wersja 1** (GPv1). 

>[!NOTE]
> Konta tylko obiektów blob nie są dozwolone jako **główne**. 

Firma Microsoft zaleca korzystanie z kont GPv2, aby można było korzystać z zalet wybór między gorąca i warstwy magazynowania chłodna. Aby dowiedzieć się więcej na temat kont magazynu, zobacz [Przegląd konta usługi Azure Storage](../../storage/common/storage-account-overview.md). 

## <a name="assets-in-a-storage-account"></a>Zasoby w ramach konta magazynu

W wersji 3 usługa Media Services interfejsy API magazynu są używane do przekazywania plików. Aby zobaczyć, jak używać interfejsów API Storage z usługą Media Services do przekazywanie plików wejściowych, zapoznaj się z [utworzyć dane wejściowe zadania z pliku lokalnego](job-input-from-local-file-how-to.md). 

> [!Note]
> Nie należy próbować zmienić zawartość kontenerów obiektów blob, które zostały wygenerowane przez zestaw SDK usług Media Services bez korzystania z interfejsów API usług Media Services.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak dołączyć konto magazynu do konta usługi Media Services, zobacz [Tworzenie konta usługi](create-account-cli-quickstart.md).
