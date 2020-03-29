---
title: Dostęp do wielu protokołów w usłudze Azure Data Lake Storage | Dokumenty firmy Microsoft
description: Użyj interfejsów API obiektów Blob i aplikacji korzystających z interfejsów API obiektów Blob z usługą Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914862"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Dostęp do wielu protokołów w usłudze Azure Data Lake Storage

Interfejsy API obiektów blob działają teraz z kontami, które mają hierarchiczną przestrzeń nazw. Spowoduje to odblokowanie ekosystemu narzędzi, aplikacji i usług, a także kilka funkcji magazynu obiektów Blob na kontach, które mają hierarchiczną przestrzeń nazw.

Do niedawna być może trzeba było utrzymywać oddzielne rozwiązania pamięci masowej dla magazynu obiektów i magazynu analitycznego. Dzieje się tak dlatego, że usługa Azure Data Lake Storage Gen2 miała ograniczoną obsługę ekosystemu. Miał również ograniczony dostęp do funkcji usługi obiektów Blob, takich jak rejestrowanie diagnostyczne. Pofragmentowane rozwiązanie magazynu jest trudne do utrzymania, ponieważ trzeba przenieść dane między kontami, aby wykonać różne scenariusze. Nie musisz już tego robić.

Dzięki wieloprotokołowym dostępowi do usługi Data Lake Storage możesz pracować z danymi przy użyciu ekosystemu narzędzi, aplikacji i usług. Obejmuje to również narzędzia i aplikacje innych firm. Można wskazać je na konta, które mają hierarchiczną przestrzeń nazw bez konieczności ich modyfikowania. Te aplikacje działają *tak, jak jest,* nawet jeśli wywołują interfejsy API obiektów Blob, ponieważ interfejsy API obiektów Blob mogą teraz działać na danych na kontach, które mają hierarchiczny obszar nazw.

Funkcje magazynu obiektów blob, takie jak rejestrowanie [diagnostyczne, warstwy dostępu](storage-blob-storage-tiers.md)i zasady [zarządzania cyklem](../common/storage-analytics-logging.md) [życia magazynu obiektów blob,](storage-lifecycle-management-concepts.md) działają teraz z kontami, które mają hierarchiczny obszar nazw. W związku z tym można włączyć hierarchiczne przestrzenie nazw na kontach magazynu obiektów blob bez utraty dostępu do tych ważnych funkcji. 

> [!NOTE]
> Dostęp do wielu protokołów w magazynie usługi Data Lake jest ogólnie dostępny i jest dostępny we wszystkich regionach. Niektóre usługi platformy Azure lub funkcje magazynu obiektów Blob włączone przez dostęp z wieloma protokołami pozostają w wersji zapoznawczej.  W tych artykułach podsumowano bieżącą obsługę funkcji magazynu obiektów Blob i integracji usług platformy Azure. 
>
> [Funkcje magazynu obiektów Blob dostępne w usłudze Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
>
>[Usługi platformy Azure obsługujące usługę Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Jak działa wieloprotokołowy dostęp do przechowywania danych w jeziorze danych

Interfejsy API obiektów BLOB i interfejsy API usługi Data Lake Storage Gen2 mogą działać na tych samych danych na kontach magazynu, które mają hierarchiczną przestrzeń nazw. Magazyn danych Lake Gen2 trasy interfejsów API obiektów Blob za pośrednictwem hierarchicznej przestrzeni nazw, dzięki czemu można uzyskać korzyści z pierwszej klasy operacji katalogowych i list kontroli dostępu (Listy kontroli dostępu zgodne ze standardem POSIX). 

![Dostęp wieloprotokołowy w koncepcyjnym uchodźczy danych Lake Storage](./media/data-lake-storage-interop/interop-concept.png) 

Istniejące narzędzia i aplikacje korzystające z interfejsu API obiektów Blob automatycznie uzyskują te korzyści. Deweloperzy nie będą musieli ich modyfikować. Data Lake Storage Gen2 konsekwentnie stosuje listy ACL na poziomie katalogu i pliku, niezależnie od protokołu używanego przez narzędzia i aplikacje do uzyskiwania dostępu do danych. 

## <a name="see-also"></a>Zobacz też

- [Funkcje magazynu obiektów Blob dostępne w usłudze Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Usługi platformy Azure obsługujące usługę Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Platformy open source obsługujące usługę Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Znane problemy z usługą Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)




