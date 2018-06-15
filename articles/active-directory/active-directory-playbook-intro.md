---
title: Wprowadzenie fazy weryfikacji koncepcji podręcznika dotyczącego usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Eksploruj i szybkie rozpoczęcie scenariusze Zarządzanie tożsamościami i dostępem
services: active-directory
keywords: Usługa Azure active directory, podręcznika dotyczącego koncepcji, aby zapewnić
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 80b7ba9507b85621e7b0623362774edc0c8cc729
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26601212"
---
# <a name="azure-active-directory-proof-of-concept-playbook-introduction"></a>Dowód koncepcji podręcznika dotyczącego usługi Azure Active Directory: wprowadzenie

Ten artykuł zawiera wskazówki, aby zapoznać się z różnych Azure AD możliwości w koncepcji (PoC). Docelowa grupa odbiorców tego dokumentu jest architektów tożsamości, specjalistów IT i integratorów systemów. platforma

## <a name="how-to-use-this-playbook"></a>Jak używać tego podręcznika dotyczącego

1. Użyj [motyw](active-directory-playbook-ingredients.md#theme) sekcji, a następnie wybierz tła zainteresowania na podstawie Twoich potrzeb.  
2. Zakres fazy weryfikacji koncepcji, wybierając scenariusze, które umożliwia im dopasowanie celów biznesowych. Krótszą tym lepiej. Zaleca się zrobienie go jako krótko- i zwięzła, jak to możliwe, przekazując wartość osobom zainteresowanym przy jednoczesnym zmniejszeniu złożoności do osiągnięcia go.  
3. Użyj [implementacji](active-directory-playbook-implementation.md) sekcji, aby zrozumieć scenariusze, a także ich oznaczałoby dla danego środowiska. W każdym scenariuszu opisano sposób skonfigurować ją (zwany [bloków konstrukcyjnych](active-directory-playbook-building-blocks.md)) i jak przechodzić w scenariuszach. 
4. Poszczególne bloki konstrukcyjne wyjaśniono wymagań wstępnych, a także przybliżony czas do ukończenia. Może to pomóc w procesie planowania. 
5. W oparciu o 1-3 powyżej, zdefiniuj [środowiska](active-directory-playbook-ingredients.md#environment) w którym ma być wykonane. Firma Microsoft zachęca do środowiska produkcyjnego uzyskać dobrą działania środowisko dla użytkowników. 
6. Jeśli występuje konflikt wymagania, użyj tej macierzy przydatne zależnościami 
   * Skoncentrowane na motywu przedstawiający wartości  
   * Wygładzania, aby przygotować się, aby skonfigurować i do wykonania w scenariuszach 
   * Minimalny czas wykonywania scenariusze docelowe 
   * Jak blisko produkcji, jak to możliwe, zgodnie z ograniczeniami 

>[!NOTE]
> W tym artykule pojawi się niektóre określonego działania aplikacji innych producentów i produktów wymienione jako przykłady dla wygody. Usługi Azure AD obsługuje tysiące aplikacji w naszym [galerii aplikacji](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) których można używać na podstawie Twoich potrzeb i środowiska. 



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]