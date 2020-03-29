---
title: Etap akceptacji klienta w cyklu życia procesu nauki o danych zespołu
description: Cele, zadania i rezultaty na etapie akceptacji klientów twoich projektów data-science
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7224a7bb26ef491915df9fcb9b6b84ff171a9fc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720524"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Etap akceptacji klienta w cyklu życia procesu nauki o danych zespołu

W tym artykule opisano cele, zadania i rezultaty skojarzone z etapem akceptacji klienta procesu nauki o danych zespołu (TDSP). Ten proces zapewnia zalecany cykl życia, którego można użyć do struktury projektów nauki o danych. Cykl życia przedstawia główne etapy, które projekty zazwyczaj wykonują, często iteracyjne:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i ich analiza**
   3. **Modelowanie**
   4. **wdrażania**
   5. **Akceptacja klienta**

Oto wizualna reprezentacja cyklu życia TDSP: 

![Cykl życia TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cel
**Finalizuj elementy dostarczane projektu:** Upewnij się, że potok, model i ich wdrożenie w środowisku produkcyjnym spełniają cele klienta.

## <a name="how-to-do-it"></a>Jak to zrobić
Na tym etapie zajęto się dwoma głównymi zadaniami:

   * **Sprawdzanie poprawności systemu:** Upewnij się, że wdrożony model i potok spełniają potrzeby klienta.
   * **Przekazanie projektu:** Przekaż projekt jednostce, która uruchomi system w produkcji.

Klient powinien sprawdzić, czy system spełnia ich potrzeby biznesowe i że odpowiada na pytania z dopuszczalną dokładnością, aby wdrożyć system do produkcji do użytku przez aplikację klienta. Cała dokumentacja jest finalizowana i sprawdzana. Projekt jest przekazywany podmiotowi odpowiedzialnemu za operacje. Ta jednostka może być, na przykład, IT lub zespół analityki danych klienta lub agent klienta, który jest odpowiedzialny za uruchamianie systemu w produkcji. 

## <a name="artifacts"></a>Artefakty
Głównym artefaktem wyprodukowanym w tym końcowym etapie jest **raport Exit projektu dla klienta.** Ten raport techniczny zawiera wszystkie szczegóły projektu, które są przydatne do nauki o tym, jak obsługiwać system. TDSP udostępnia szablon [raportu zakończenia.](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) Można użyć szablonu, jak jest, lub można dostosować go do określonych potrzeb klienta. 


## <a name="next-steps"></a>Następne kroki

Oto łącza do każdego kroku w cyklu życia TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i ich analiza](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [wdrażania](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Zapewniamy pełne wskazówki, które pokazują wszystkie kroki w procesie dla określonych scenariuszy. [Przykładowy](walkthroughs.md) artykuł zawiera listę scenariuszy z łączami i opisami miniatur. W instruktażu pokazano, jak połączyć chmurę, narzędzia lokalne i usługi w przepływie pracy lub potoku w celu utworzenia inteligentnej aplikacji. 

Aby zapoznać się z przykładami wykonywania kroków w usługach TDSP korzystających z usługi Azure Machine Learning Studio, zobacz [Korzystanie z usługi TDSP z usługą Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)
