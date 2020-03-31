---
title: Historia wersji
titleSuffix: Microsoft Genomics
description: Historia wydania aktualizacji klienta Microsoft Genomics Python dla poprawek i nowych funkcji.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 20475e2cde1b42790740889cf341b3a0a0afccc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76991086"
---
# <a name="version-release-history"></a>Historia wersji
Zespół Microsoft Genomics regularnie aktualizuje klienta Microsoft Genomics Python pod kątem poprawek i nowych funkcji. 

## <a name="latest-release"></a>Najnowsza wersja
Bieżący klient języka Python jest w wersji 0.9.0. Został wydany 6 lutego 2019 i obsługuje uruchamianie przepływów pracy z GATK 3.5 i GATK4. Obsługuje wyjście gVCF i może zaakceptować opcjonalny argument kompresji wyjściowej.


## <a name="release-history"></a>Historia wydania 
Nowe wersje klienta Microsoft Genomics Python są wydawane mniej więcej raz w roku. Wraz z wydaniem nowych wersji klienta Microsoft Genomics Python, lista poprawek i funkcji jest aktualizowana tutaj. Po wydaniu nowych wersji wcześniejsze wersje powinny być nadal obsługiwane przez co najmniej 90 dni. Gdy wcześniejsze wersje nie są już obsługiwane, zostanie to wskazane na tej stronie. 

### <a name="version-090"></a>Wersja 0.9.0
Wersja 0.9.0 zawiera obsługę kompresji wyjściowej. Jest to równoważne z systemem, `-bgzip` po którym następuje `-tabix` wyjście vcf lub gvcf. Aby uzyskać więcej informacji, zobacz [Często zadawane pytania](frequently-asked-questions-genomics.md). 

### <a name="version-081"></a>Wersja 0.8.1
Wersja 0.8.1 zawiera drobne poprawki błędów.  

### <a name="version-080"></a>Wersja 0.8.0
Wersja 0.8.0 zawiera obsługę GATK4 i wyprowadzania plików gVCF.  

### <a name="version-074"></a>Wersja 0.7.4
Wersja 0.7.4 zawiera obsługę akceptowania tokenów sygnatury `config.txt` dostępu Współdzielonego zamiast kluczy konta w danych wejściowych. Aby uzyskać więcej informacji, zobacz [Szybki start tokenów Sygnatury dostępu Współdzielonego wejściowego](quickstart-input-sas.md). 

### <a name="version-073"></a>Wersja 0.7.3
Wersja 0.7.3 zawiera drobne poprawki błędów.

### <a name="version-072"></a>Wersja 0.7.2
Wersja 0.7.2 jest wersją początkową. Został wydany 1 listopada 2017 roku.
