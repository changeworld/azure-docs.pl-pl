---
title: Etap wdrażania cyklu życia procesu nauki o danych zespołu
description: Cele, zadania i rezultaty na etapie wdrażania projektów nauki o danych
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
ms.openlocfilehash: 1138c95274c769186a9a29aa4d35517e378baeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720490"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Etap wdrażania cyklu życia procesu nauki o danych zespołu

W tym artykule opisano cele, zadania i rezultaty skojarzone z wdrożeniem procesu nauki o danych zespołu (TDSP). Ten proces zapewnia zalecany cykl życia, którego można użyć do struktury projektów nauki o danych. Cykl życia przedstawia główne etapy, które projekty zazwyczaj wykonują, często iteracyjne:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i ich analiza**
   3. **Modelowanie**
   4. **wdrażania**
   5. **Akceptacja klienta**

Oto wizualna reprezentacja cyklu życia TDSP: 

![Cykl życia TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cel
Wdrażanie modeli z potokiem danych w środowisku produkcyjnym lub produkcyjnym w celu akceptacji użytkownika końcowego. 

## <a name="how-to-do-it"></a>Jak to zrobić
Główne zadanie, do którego skierowano na tym etapie:

**Operacjonalizacja modelu:** Wdrażanie modelu i potoku w środowisku produkcyjnym lub produkcyjnym do użycia aplikacji.

### <a name="operationalize-a-model"></a>Operacjonalizowanie modelu
Po zestaw modeli, które działają dobrze, można operacjonalizacji ich dla innych aplikacji do korzystania. W zależności od wymagań biznesowych prognozy są dokonywane w czasie rzeczywistym lub na podstawie partii. Aby wdrożyć modele, można udostępnić je za pomocą otwartego interfejsu interfejsu API. Interfejs umożliwia łatwe korzystanie z modelu z różnych aplikacji, takich jak:

   * Strony internetowe
   * Arkusze kalkulacyjne 
   * Pulpity nawigacyjne
   * Aplikacje biznesowe 
   * Aplikacje zaplecza 

Przykłady operacjonalizacji modelu za pomocą usługi sieci Web usługi Azure Machine Learning można znaleźć w części [Wdrażanie usługi sieci Web usługi Azure Machine Learning.](../studio/deploy-a-machine-learning-web-service.md) Jest najlepszym rozwiązaniem do tworzenia danych telemetrycznych i monitorowania w modelu produkcyjnym i potoku danych, który można wdrożyć. Ta praktyka pomaga w kolejnych raportowania stanu systemu i rozwiązywania problemów.  

## <a name="artifacts"></a>Artefakty

* Pulpit nawigacyjny stanu, na który wyświetlane są metryki kondycji systemu i kluczowych
* Końcowy raport modelowania ze szczegółami wdrożenia
* Dokument architektury rozwiązania końcowego


## <a name="next-steps"></a>Następne kroki

Oto łącza do każdego kroku w cyklu życia TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie i rozumienie danych](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [wdrażania](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Zapewniamy pełne wskazówki, które pokazują wszystkie kroki w procesie dla określonych scenariuszy. [Przykładowy](walkthroughs.md) artykuł zawiera listę scenariuszy z łączami i opisami miniatur. W instruktażu pokazano, jak połączyć chmurę, narzędzia lokalne i usługi w przepływie pracy lub potoku w celu utworzenia inteligentnej aplikacji. 

Aby zapoznać się z przykładami wykonywania kroków w usługach TDSP korzystających z usługi Azure Machine Learning Studio, zobacz [Korzystanie z usługi TDSP z usługą Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)
