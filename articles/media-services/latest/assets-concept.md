---
title: Zasoby w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera opis zasoby są i jak są używane przez usługi Azure Media Services.
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
ms.openlocfilehash: 928d88d51503350abe7df847ce58ff066b987c8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="assets"></a>Elementy zawartości

**Zasobów** zawiera (w tym wideo, audio, obrazy, kolekcje miniatur, ścieżek tekstu i pliki napisów) pliki cyfrowe i metadane dotyczące tych plików. Po pliki cyfrowe są przekazywane do elementu zawartości, mogą być używane w Media Services, kodowania i przesyłania strumieniowego przepływów pracy.

Zasób jest zamapowana do kontenera obiektów blob w [konta magazynu Azure](storage-account-concept.md) i pliki w elementach zawartości są przechowywane jako blokowych obiektów blob w tym kontenerze. Możesz użyć plików zasobów w kontenerach przy użyciu zestawu SDK usługi Magazyn klientów.

Usługa Azure Media Services obsługuje warstw obiektów Blob, gdy konto używa ogólnego przeznaczenia v2 (GPv2) magazynu. Z GPv2 można przenieść plików do cool lub chłodni. Cold storage nadaje się do archiwizacji mezzanine plików, gdy nie są już potrzebne (na przykład po ich ma został zakodowany.)

W wersji 3 usługi Media Services dane wejściowe zadania mogą być tworzone z zasobów lub adresy URL HTTP (s). Aby utworzyć zasób, który może służyć jako dane wejściowe dla zadania, zobacz [utworzyć wprowadzania zadania z pliku lokalnego](job-input-from-local-file-how-to.md).

Ponadto należy przeczytać o [kont magazynu w usłudze Media Services](storage-account-concept.md) i [transformacji i zadania](transform-concept.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Strumień pliku](stream-files-dotnet-quickstart.md)
