---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597864"
---
Usługa Azure Files oferuje dwie różne warstwy pamięci masowej, premium i standard, aby umożliwić dostosowanie udziałów do wymagań dotyczących wydajności i ceny w scenariuszu:

- **Udziały plików premium:** Udziały plików premium są wspierane przez dyski PÓŁPRZEWODNIKOWE (SSD) i są wdrażane w typie **konta magazynu FileStorage.** Udziały plików w udziale w udziale w udziale w wersji premium zapewniają stałą wysoką wydajność i małe opóźnienia w ciągu jednocyfrowych milisekund dla większości operacji we/wy dla obciążeń intensywnie korzystających z operacji we/wy. Dzięki temu nadają się do różnych obciążeń, takich jak bazy danych, hosting witryn sieci web i środowiska programistyczne. Udziały plików premium są dostępne tylko w modelu rozliczeń aprowizacji. Aby uzyskać więcej informacji na temat aprowizacji modelu rozliczeń dla udziałów plików premium, zobacz [Opis inicjowania obsługi administracyjnej udziałów plików premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares).
- **Standardowe udziały plików:** Standardowe udziały plików są wspierane przez dyski twarde (HDD) i są wdrażane w **typie konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2).** Standardowe udziały plików zapewniają niezawodną wydajność dla obciążeń we/wy, które są mniej wrażliwe na zmienność wydajności, takie jak udziały plików ogólnego przeznaczenia i środowiska deweloperów/testów. Standardowe udziały plików są dostępne tylko w modelu rozliczeń płatności zgodnie z rzeczywistymu po podróży.