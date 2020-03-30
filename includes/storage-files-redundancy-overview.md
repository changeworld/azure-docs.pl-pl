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
ms.openlocfilehash: 87457bb103f49be4ca3e7bf9f463c5bf63f3a119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597841"
---
Aby chronić dane w udziałach plików platformy Azure przed utratą lub uszkodzeniem danych, wszystkie udziały plików platformy Azure przechowują wiele kopii każdego pliku w miarę ich zapisu. W zależności od wymagań obciążenia można wybrać dodatkowe stopnie nadmiarowości. Usługa Azure Files obsługuje obecnie następujące opcje nadmiarowości danych:

- **Lokalnie nadmiarowy:** Lokalnie nadmiarowy magazyn, często nazywany LRS, oznacza, że każdy plik jest przechowywany trzy razy w klastrze magazynu platformy Azure. Chroni to przed utratą danych z powodu usterek sprzętowych, takich jak zły dysk.
- **Nadmiarowa strefa:** Magazyn strefowy, często nazywany ZRS, oznacza, że każdy plik jest przechowywany trzy razy w trzech różnych klastrach magazynu platformy Azure. Trzy różne klastry magazynu platformy Azure przechowują plik trzy razy, podobnie jak w lokalnym nadmiarowym magazynie i są fizycznie odizolowane w różnych *strefach dostępności*platformy Azure. Strefy dostępności są unikatowymi lokalizacjami fizycznymi w regionie platformy Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. Zapis do magazynu nie jest akceptowany, dopóki nie zostanie zapisany w klastrach magazynu we wszystkich trzech strefach dostępności. 
- **Geo-nadmiarowy:** Magazyn geograficznie nadmiarowy, często nazywany GRS, jest jak magazyn lokalnie nadmiarowy, ponieważ plik jest przechowywany trzy razy w klastrze magazynu platformy Azure w regionie podstawowym. Wszystkie zapisy są następnie asynchronicznie replikowane do regionu pomocniczego zdefiniowanego przez firmę Microsoft. Magazyn geograficznie nadmiarowy udostępnia 6 kopii danych rozłożonych między dwoma regionami platformy Azure. W przypadku poważnej awarii, takiej jak trwała utrata regionu platformy Azure z powodu klęski żywiołowej lub innego podobnego zdarzenia, firma Microsoft wykona pracę awaryjną, aby w efekcie wtórny stał się podstawowym, obsługującym wszystkie operacje. Ponieważ replikacja między regionami podstawowymi i pomocniczymi są asynchroniczne, w przypadku poważnej awarii dane, które nie zostały jeszcze zreplikowane do regionu pomocniczego, zostaną utracone. Można również wykonać ręczną pracę awaryjną konta magazynu geograficznie nadmiarowego.
- **Geo-zone redundant**: Magazyn nadmiarowy strefy geograficznej, często nazywany GZRS, jest jak magazyn strefowy nadmiarowy, ponieważ plik jest przechowywany dziewięć razy w 3 różnych klastrach magazynu w regionie podstawowym. Wszystkie zapisy są następnie asynchronicznie replikowane do regionu pomocniczego zdefiniowanego przez firmę Microsoft. Proces pracy awaryjnej dla magazynu nadmiarowego strefy geograficznej działa tak samo, jak w przypadku magazynu geograficznie nadmiarowego.

Udziały plików platformy Azure standard obsługuje wszystkie cztery typy nadmiarowości, podczas gdy udziały plików platformy Azure premium obsługują tylko lokalnie nadmiarowy i strefowy nadmiarowy magazyn.

Konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2) zapewniają dwie dodatkowe opcje nadmiarowości, które nie są obsługiwane przez usługi Azure Files: odczyt dostępnego magazynu geograficznego, często określanego jako RA-GRS, i często odczytywania dostępnego magazynu geograficznego ze strefą nadmiarową, często ra-GZRS. Można aprowizować udziały plików platformy Azure na kontach magazynu z tych opcji zestawu, jednak usługa Azure Files nie obsługuje odczytu z regionu pomocniczego. Udziały plików platformy Azure wdrożone na kontach magazynu nadmiarowego dostępnego do odczytu geograficznego lub geograficznego strefy będą rozliczane odpowiednio jako magazyn geograficznie nadmiarowy lub magazyn geograficznie nadmiarowy.