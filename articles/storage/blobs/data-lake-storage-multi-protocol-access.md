---
title: Dostęp przez wiele protokołów w Azure Data Lake Storage | Microsoft Docs
description: Używaj interfejsów API obiektów blob i aplikacji, które używają interfejsów API obiektów blob z Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: normesta
ms.openlocfilehash: f384fb738fe719b8e622e8d61502e6acba2bbf31
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314372"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Dostęp z wieloprotokołem do Azure Data Lake Storage

Interfejsy API obiektów BLOB działają teraz z kontami, które mają hierarchiczną przestrzeń nazw. To odblokowuje cały ekosystem narzędzi, aplikacji i usług, a także wszystkie funkcje magazynu obiektów BLOB do kont, które mają hierarchiczną przestrzeń nazw.

Do tej pory mogło być konieczne przechowywanie oddzielnych rozwiązań magazynu dla magazynu obiektów i magazynu analizy. Dzieje się tak, ponieważ Azure Data Lake Storage Gen2 miała ograniczoną obsługę ekosystemu. Miał także ograniczony dostęp do funkcji Blob service, takich jak rejestrowanie diagnostyczne. Pofragmentowane rozwiązanie magazynu jest trudne do utrzymania, ponieważ trzeba przenieść dane między kontami, aby osiągnąć różne scenariusze. Nie musisz już tego robić.

> [!NOTE]
> Dostęp za pomocą protokołu wieloprotokołowego Data Lake Storage jest dostępny w publicznej wersji zapoznawczej, a dostęp się tylko w regionach **zachodnie stany USA 2** i **zachodnie stany USA** . Aby zapoznać się z ograniczeniami, zobacz artykuł [znane problemy](data-lake-storage-known-issues.md) . Aby zarejestrować się w wersji zapoznawczej, zobacz [Tę stronę](https://aka.ms/blobinteropsignup).

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>Korzystaj z całego ekosystemu aplikacji, narzędzi i usług

Jeśli zarejestrujesz się w wersji zapoznawczej wieloprotokołowego dostępu do Data Lake Storage, możesz współpracować ze wszystkimi danymi przy użyciu całego ekosystemu narzędzi, aplikacji i usług. Obejmuje to usługi platformy Azure, takie jak [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction), [IOT Hub](https://docs.microsoft.com/azure/iot-hub/), [Power BI](https://docs.microsoft.com/power-bi/desktop-data-sources)i wiele innych. 

Obejmuje to również narzędzia i aplikacje innych firm. Można wskazać te konta, które mają hierarchiczną przestrzeń nazw bez konieczności ich modyfikacji. Aplikacje te działają *podobnie jak* w przypadku wywoływania interfejsów API obiektów blob, ponieważ interfejsy API obiektów BLOB mogą teraz operować na danych na kontach z hierarchiczną przestrzenią nazw.

> [!NOTE]
> Aby zapoznać się z ograniczeniami, zobacz artykuł [znane problemy](data-lake-storage-known-issues.md) .

## <a name="use-all-blob-storage-features"></a>Użyj wszystkich funkcji magazynu obiektów BLOB

Funkcje magazynu obiektów blob, takie jak [Rejestrowanie diagnostyki](../common/storage-analytics-logging.md), [warstwy dostępu](storage-blob-storage-tiers.md)i [zasady zarządzania cyklem życia magazynu obiektów BLOB](storage-lifecycle-management-concepts.md) , działają teraz z kontami, które mają hierarchiczną przestrzeń nazw. W związku z tym można włączyć hierarchiczne przestrzenie nazw na kontach magazynu obiektów Blob bez utracie dostępu do tych ważnych funkcji. 

> [!NOTE]
> Aby zapoznać się z ograniczeniami, zobacz artykuł [znane problemy](data-lake-storage-known-issues.md) .

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Jak działa dostęp przez wiele protokołów w usłudze Data Lake Storage

Interfejsy API obiektów blob i interfejsy API Data Lake Storage Gen2 mogą działać na tych samych danych na kontach magazynu, które mają hierarchiczną przestrzeń nazw. Data Lake Storage Gen2 kieruje interfejsy API obiektów BLOB za pomocą hierarchicznej przestrzeni nazw, aby można było korzystać z zalet operacji katalogu pierwszej klasy i list kontroli dostępu zgodnych ze standardem POSIX (ACL). 

![Dostęp z wieloprotokołem do Data Lake Storage koncepcyjnych](./media/data-lake-storage-interop/interop-concept.png) 

Istniejące narzędzia i aplikacje korzystające z interfejsu API obiektów BLOB uzyskują te korzyści automatycznie. Deweloperzy nie będą musieli ich modyfikować. Data Lake Storage Gen2 konsekwentnie stosuje katalogi i listy ACL na poziomie plików, niezależnie od protokołu używanego przez narzędzia i aplikacje do uzyskiwania dostępu do danych.   

## <a name="next-steps"></a>Następne kroki

Zobacz [znane problemy](data-lake-storage-known-issues.md)




