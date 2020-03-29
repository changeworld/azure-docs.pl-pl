---
title: Cykl życia procesu nauki o danych zespołu
description: Proces nauki o danych zespołu (TDSP) zapewnia zalecany cykl życia, którego można użyć do struktury projektów nauki o danych.
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
ms.openlocfilehash: a043a1655950f3ed7688e59352f8a912146e12c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720456"
---
# <a name="the-team-data-science-process-lifecycle"></a>Cykl życia procesu nauki o danych zespołu

Proces nauki o danych zespołu (TDSP) zapewnia zalecany cykl życia, którego można użyć do struktury projektów nauki o danych. Cykl życia przedstawia pełne kroki, które po pomyślnym zakończeniu projektów. Jeśli używasz innego cyklu życia nauki o danych, takiego jak Cross Industry Standard Process for Data Mining [(CRISP-DM),](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)Knowledge Discovery in Databases [(KDD)](https://wikipedia.org/wiki/Data_mining#Process)lub własny proces niestandardowy organizacji, nadal można użyć tdsp opartego na zadaniach. 

Ten cykl życia jest przeznaczony dla projektów nauki o danych, które są przeznaczone do wysyłki w ramach inteligentnych aplikacji. Te aplikacje wdrażają modele uczenia maszynowego lub sztucznej inteligencji do analizy predykcyjnej. Z wykorzystania tego procesu mogą również skorzystać eksploracyjne projekty w zakresie analizy danych i improwizowane projekty analityczne. Ale w przypadku tych projektów niektóre kroki opisane w tym miejscu mogą nie być potrzebne. 

## <a name="five-lifecycle-stages"></a>Pięć etapów cyklu życia

Cykl życia TDSP składa się z pięciu głównych etapów, które są wykonywane iteracyjnie. Etapy te obejmują:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i ich analiza](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [wdrażania](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Oto wizualna reprezentacja cyklu życia TDSP: 

![Cykl życia TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


Cykl życia TDSP jest modelowany jako sekwencja iteracyjnych kroków, które zawierają wskazówki dotyczące zadań potrzebnych do korzystania z modeli predykcyjnych. Wdrażanie modeli predykcyjnych w środowisku produkcyjnym, które mają być używane do tworzenia inteligentnych aplikacji. Celem tego cyklu życia procesu jest kontynuowanie przenoszenia projektu nauki o danych w kierunku wyraźnego punktu końcowego zaangażowania. Analityka danych jest ćwiczeniem w badaniach i odkryciach. Możliwość komunikowania zadań do zespołu i klientów przy użyciu dobrze zdefiniowanego zestawu artefaktów, które wykorzystują standardowe szablony pomaga uniknąć nieporozumień. Korzystanie z tych szablonów zwiększa również szansę na pomyślne ukończenie złożonego projektu nauki o danych.

Dla każdego etapu dostarczamy następujące informacje:

   * **Cele**: Cele szczegółowe.
   * **Jak to zrobić:** Zarys konkretnych zadań i wskazówki dotyczące ich wykonania.
   * **Artefakty:** Rezultaty i wsparcie, aby je wyprodukować.

## <a name="next-steps"></a>Następne kroki

Zapewniamy pełne instrukcje end-to-end, które pokazują wszystkie kroki w procesie dla określonych scenariuszy. [Przykładowy](walkthroughs.md) artykuł zawiera listę scenariuszy z łączami i opisami miniatur. W instruktażu pokazano, jak połączyć chmurę, narzędzia lokalne i usługi w przepływie pracy lub potoku w celu utworzenia inteligentnej aplikacji. 

Aby zapoznać się z przykładami wykonywania kroków w usługach TDSP korzystających z usługi Azure Machine Learning Studio, zobacz [Korzystanie z usługi TDSP z usługą Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)
