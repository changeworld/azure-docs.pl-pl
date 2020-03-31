---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70737495"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Komunikat o błędzie ConditionHeadersNotSupportowane z aplikacji sieci Web przy użyciu plików platformy Azure z przeglądarki

ConditionHeadersNotSupported błąd występuje podczas uzyskiwania dostępu do zawartości hostowane w usłudze Azure Files za pośrednictwem aplikacji, która korzysta z nagłówków warunkowych, takich jak przeglądarka sieci web, dostęp kończy się niepowodzeniem. Błąd stwierdza, że nagłówki warunku nie są obsługiwane.

![Błąd nagłówków warunkowych plików platformy Azure](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Przyczyna

Nagłówki warunkowe nie są jeszcze obsługiwane. Aplikacje implementujące je będą musiały zażądać pełnego pliku za każdym razem, gdy plik jest dostępny.

### <a name="workaround"></a>Obejście

Po przekazaniu nowego pliku właściwość kontroli pamięci podręcznej domyślnie jest "no-cache". Aby wymusić aplikację do żądania pliku za każdym razem, właściwość kontroli pamięci podręcznej pliku musi zostać zaktualizowana z "no-cache" do "no-cache, no-store, must-revalidate". Można to osiągnąć za pomocą [Eksploratora usługi Azure Storage.](https://azure.microsoft.com/features/storage-explorer/)

![Modyfikacja pamięci podręcznej magazynu eksploratora dla nagłówków warunkowych plików Platformy Azure](media/storage-files-condition-headers/storage-explorer-cache.png)