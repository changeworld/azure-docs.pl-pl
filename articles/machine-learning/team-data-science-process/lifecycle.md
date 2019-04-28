---
title: Cykl życia zespołowego danych dla celów naukowych
description: Kroki niezbędne do wykonania swoje projekty do nauki o danych
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 40d1af6e6258b5026853532f7963a76d4fc389db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837902"
---
# <a name="the-team-data-science-process-lifecycle"></a>Cykl życia zespołowego danych dla celów naukowych

Team Data Science naukowych oferuje zalecane cyklu życia, który umożliwia tworzenie struktury projektów do nauki o danych. Cykl życia zawiera opis kroków, od początku do końca, że projekty zazwyczaj należy wykonać podczas są wykonywane. Jeśli używasz innego cyklu życia do nauki o danych, takich jak między branży standardowego procesu do wyszukiwania danych [(WYSOKĄ-DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), Knowledge Discovery w bazach danych [(KDD)](https://wikipedia.org/wiki/Data_mining#Process), lub proces niestandardowej w organizacji , nadal można korzystać z przetwarzania TDSP opartego na zadaniach. 

Ten cykl życia jest przeznaczona dla projektów do nauki o danych, które są przeznaczone do wysłania jako część inteligentnych aplikacji. Te aplikacje wdrażania modeli uczenia i sztucznej inteligencji maszynowego do analizy predykcyjnej. Poznawcze nauki o danych i analizy ad-hoc projektów mogą również skorzystać z użycia tego procesu. Jednak w tych projektach, niektóre kroki opisane w tym miejscu może być niepotrzebne. 

## <a name="five-lifecycle-stages"></a>Pięć etapów cyklu życia

Cykl życia przetwarzania TDSP składa się z pięć etapów głównych, które są wykonywane wielokrotnie. Etapy te obejmują:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i opis](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Oto wizualnej reprezentacji cyklu przetwarzania TDSP: 

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


Cykl życia przetwarzania TDSP w modelu za pomocą sekwencji iterowanej czynności, które zawiera wskazówek na temat zadań potrzebnych do korzystania z modeli predykcyjnych. Możesz wdrożyć modele predykcyjne w środowisku produkcyjnym, który ma być używany do tworzenia inteligentnych aplikacji. Celem tego cyklu życia procesu jest nadal można przenieść projektu do nauki o danych do punktu końcowego wyczyść zaangażowania. Do nauki o danych jest zadaniem w zakresie badań i odnajdywania. Możliwość komunikowania się zadania do Twojego zespołu i klientów za pomocą dobrze zdefiniowanych zestaw artefaktów, korzystających z szablonów standardowych pomaga uniknąć nieporozumień. Za pomocą tych szablonów również zwiększa prawdopodobieństwo pomyślnego ukończenia projektu nauki o danych złożonych.

Na każdym etapie firma Microsoft zapewnia następujące informacje:

   * **Cele**: Określonych celów.
   * **Jak to zrobić**: Zarys określonych zadań i wskazówki na temat sposobu ich ukończenia.
   * **Artefakty**: Elementy dostarczane i pomocy technicznej, aby je utworzyć.

## <a name="next-steps"></a>Kolejne kroki

Firma Microsoft oferuje instruktaży pełnej end-to-end, które przedstawiają wszystkie kroki w procesie dla konkretnych scenariuszy. [Przykładowe przewodniki](walkthroughs.md) artykuł zawiera listę scenariuszy wraz z linkami i opisy miniatur. Przewodniki pokazują, jak połączyć chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 

Przykłady sposób wykonywania kroków w TDSPs, które używają usługi Azure Machine Learning Studio, zobacz [przetwarzania TDSP za pomocą usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
