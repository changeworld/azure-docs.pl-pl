---
title: Odbiorcy akceptacji etapie cyklu życia procesu nauki danych zespołu - Azure | Dokumentacja firmy Microsoft
description: Cele, zadań i elementów dostarczanych w etapie akceptacji klienta projektów analizy danych
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 82819ab59d9bab6f256cd01a376f62ac8dac26b3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837144"
---
# <a name="customer-acceptance"></a>Akceptacja klienta

W tym artykule omówiono cele, zadań i elementów dostarczanych skojarzone z etapu akceptacji klienta Team danych nauki procesu (TDSP). Ten proces obejmuje zalecane cykl służy do struktury projektów analizy danych. Cykl życia przedstawiono główne etapy, które projekty zazwyczaj wykonywane, często wielokrotnie powtarzane:

   1. **Opis biznesowa**
   2. **Uzyskiwanie danych i zrozumienie**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacji klienta**

W tym miejscu jest wizualną reprezentacją życia TDSP: 

![Cykl życia TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cel
**Finalizuj elementy dostarczane**: Upewnij się, że potok, modelu i ich wdrażania w środowisku produkcyjnym spełnia cele klienta.

## <a name="how-to-do-it"></a>Jak to zrobić
Istnieją dwa główne zadania zostały omówione w tym etapie:

   * **System sprawdzania poprawności**: Upewnij się, że wdrożone modelu i potoku potrzeb klienta.
   * **Projekt ręcznie wyłączyć**: przekazują projektu do jednostki, który ma służyć do zapewnienia działania systemu w środowisku produkcyjnym.

Klienta należy zweryfikować, że system spełnia ich potrzeb biznesowych i odpowiedzi pytania, z dokładnością dopuszczalne do wdrożenia systemu do środowiska produkcyjnego do użycia przez aplikację ich klienta. Cała dokumentacja sfinalizowana, a sprawdzone. Projekt jest przekazywany — wyłączone na jednostka odpowiedzialna za operacje. Ta jednostka może być na przykład IT lub zespół nauki danych klienta lub agenta klienta, który odpowiada za uruchamianie systemu w środowisku produkcyjnym. 

## <a name="artifacts"></a>Artefakty
Głównym artefaktu wygenerowane w tym ostatnim krokiem jest **zamknąć raport projektu klienta**. Ten raport techniczne zawiera wszystkie szczegóły projektu, które są przydatne podczas nauki o sposobie pracy systemu. Udostępnia TDSP [zamknąć raport](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) szablonu. Można użyć szablonu, ponieważ jest lub dostosować go na potrzeby określonego klienta. 


## <a name="next-steps"></a>Kolejne kroki

Oto łącza do każdego kroku w cyklu TDSP:

   1. [Opis biznesowa](lifecycle-business-understanding.md)
   2. [Uzyskiwanie danych i zrozumienie](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacji klienta](lifecycle-acceptance.md)

Firma Microsoft udostępnia pełnej end-to-end wskazówki, które pokazują wszystkie kroki w procesie w określonych scenariuszach. [Wskazówki przykład](walkthroughs.md) artykuł zawiera listę scenariuszy łącza i opisy miniatur. Wskazówki dotyczące ilustrują sposób łączenia chmury, narzędzia lokalnych i usług w przepływie pracy lub potoku, aby utworzyć aplikację inteligentnego. 

Przykłady tego, jak wykonać kroki opisane w TDSPs, które używają usługi Azure Machine Learning Studio, zobacz [TDSP za pomocą usługi Azure Machine Learning](http://aka.ms/datascienceprocess).
