---
title: Przenoszenie zasobów platformy Azure do innego regionu
description: Zawiera omówienie przenoszenia zasobów platformy Azure w regionach platformy Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485210"
---
# <a name="moving-azure-resources-across-regions"></a>Przenoszenie zasobów platformy Azure między regionami

Ten artykuł zawiera informacje dotyczące przenoszenia zasobów platformy Azure w regionach platformy Azure.

Obszary geograficzne, regiony i strefy dostępności platformy Azure stanowią podstawę globalnej infrastruktury platformy Azure. Lokalizacje [geograficzne](https://azure.microsoft.com/global-infrastructure/geographies/) platformy Azure zazwyczaj zawierają co najmniej dwa [regiony platformy Azure.](https://azure.microsoft.com/global-infrastructure/regions/) Region jest obszarem w obrębie lokalizacji geograficznej, zawierającym strefy dostępności i wiele centrów danych. 

Po wdrożeniu zasobów w określonym regionie platformy Azure istnieje wiele powodów, dla których można przenieść zasoby do innego regionu.

- **Wyrównaj do uruchomienia regionu:** przenieś swoje zasoby do nowo wprowadzonego regionu platformy Azure, który nie był wcześniej dostępny.
- **Wyrównaj dla usług/funkcji:** Przenieś zasoby, aby korzystać z usług lub funkcji dostępnych w określonym regionie.
- **Reagowanie na rozwój firmy**: Przenoszenie zasobów do regionu w odpowiedzi na zmiany biznesowe, takie jak fuzje lub przejęcia.
- **Wyrównaj do bliskości:** przenieś zasoby do regionu lokalnego do swojej firmy.
- **Spełniaj wymagania dotyczące danych:** Przenieś zasoby w celu dostosowania ich do wymagań dotyczących rezydencji danych lub wymagań dotyczących klasyfikacji danych. [Dowiedz się więcej](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Odpowiadanie na wymagania dotyczące wdrażania:** Przenoszenie zasobów, które zostały wdrożone przez pomyłkę lub przenoszenie w odpowiedzi na potrzeby związane z pojemnością. 
- **Reagowanie na likwidację**: Przenoszenie zasobów z powodu likwidacji regionów.

## <a name="move-process"></a>Proces przenoszenia

Rzeczywisty proces przenoszenia zależy od przemieszczających się zasobów. Istnieją jednak pewne typowe kluczowe kroki:

- **Sprawdź wymagania wstępne:** Wymagania wstępne obejmują upewnienie się, że potrzebne zasoby są dostępne w regionie docelowym, sprawdzanie, czy masz wystarczającą ilość przydziału, oraz sprawdzanie, czy subskrypcja może uzyskać dostęp do regionu docelowego.
- **Analizowanie zależności:** Zasoby mogą mieć zależności od innych zasobów. Przed przeniesieniem, dowiedzieć się zależności, tak aby przeniesione zasoby nadal działać zgodnie z oczekiwaniami po przeprowadzce.
- **Przygotuj się do przeniesienia:** Są to kroki, które należy wykonać w regionie podstawowym przed przeniesieniem. Na przykład może być konieczne wyeksportowanie szablonu usługi Azure Resource Manager lub rozpoczęcie replikowania zasobów ze źródła do obiektu docelowego.
- **Przenoszenie zasobów:** sposób przenoszenia zasobów zależy od tego, czym są. Może być konieczne wdrożenie szablonu w regionie docelowym lub zasoby awaryjne w celu zapewnienia docelowych.
- **Odrzuć zasoby docelowe:** Po przeniesieniu zasobów możesz przyjrzeć się zasobom w regionie docelowym i zdecydować, czy jest coś, czego nie potrzebujesz.
- **Zatwierdzić przeniesienie:** Po sprawdzeniu zasobów w regionie docelowym niektóre zasoby mogą wymagać ostatecznej akcji zatwierdzenia. Na przykład w regionie docelowym, który jest teraz regionem podstawowym, może być konieczne skonfigurowanie odzyskiwania po awarii do nowego regionu pomocniczego. 
- **Oczyść źródło:** Wreszcie, po uruchomieniu wszystkiego w nowym regionie, można oczyścić i zlikwidować zasoby utworzone dla przeniesienia i zasobów w regionie podstawowym.



## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę zasobów, które obsługują przenoszenie między regionami, zobacz [Przenoszenie obsługi operacji dla zasobów](region-move-support.md).
