---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/17/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0486b595bffd18b06d54e8377b24deab04e2aa93
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159868"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Błąd ConditionHeadersNotSupported z aplikacji sieci Web przy użyciu usługi Azure Files za pomocą przeglądarki

Uzyskiwanie dostępu do zawartości hostowanej w usłudze Azure Files za pośrednictwem aplikacji, która sprawia, że korzystanie z warunkowego nagłówków, takie jak przeglądarki sieci web access nie powiedzie się, błąd ConditionHeadersNotSupported.

![Błąd ConditionHeaderNotSupported](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Przyczyna

Warunkowe nagłówki nie są jeszcze obsługiwane. Aplikacje wdrożone, należy poprosić cały plik, za każdym razem, gdy plik jest dostępny.

### <a name="workaround"></a>Obejście

Gdy nowy plik zostanie przekazany, właściwość kontrola pamięci podręcznej domyślnie to "no-cache". Aby wymusić stosowanie do żądania plik każdym razem pliku nagłówka cache-control właściwość musi zostać zaktualizowany z "no-cache" do "no-cache, no-store muszą revalidate". Można to osiągnąć przy użyciu [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

![Modyfikowanie zawartości pamięci podręcznej Eksploratora magazynu](media/storage-files-condition-headers/storage-explorer-cache.png)