---
title: Klient akceptacji etap cyklu życia zespołowego danych dla celów naukowych
description: Cele, zadania i elementy dostarczane dla etapu akceptacji klienta projektów do nauki o danych
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720524"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Klient akceptacji etap cyklu życia zespołowego danych dla celów naukowych

W tym artykule opisano cele, zadania i cele do zrealizowania skojarzone z etapem akceptacji klienta procesu do nauki o danych zespołu (TDSP). Ten proces obejmuje zalecane cyklu życia, który umożliwia tworzenie struktury projektów do nauki o danych. Cykl życia przedstawia główne etapy, które projekty zazwyczaj są wykonywane, często iteracyjne:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i opis**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacja klienta**

Oto wizualnej reprezentacji cyklu przetwarzania TDSP: 

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cel
**Finalizowanie elementów dostarczanych projektu**: Upewnij się, że potok, model i ich wdrożenie w środowisku produkcyjnym spełniają cele klienta.

## <a name="how-to-do-it"></a>Jak to zrobić
Istnieją dwa główne zadania, które zostały rozwiązane podczas tego etapu:

   * **Sprawdzanie poprawności systemu**: Upewnij się, że wdrożony model i potoku potrzeb klienta.
   * **Projekt ręcznie wyłączyć**: przekazują projektu do jednostki, która ma wejść do zapewnienia działania systemu w środowisku produkcyjnym.

Klienta należy zweryfikować, czy system spełnia ich potrzeby biznesowe i odpowiedzi pytania, z dokładnością dopuszczalne do wdrożenia systemu do środowiska produkcyjnego do użycia przez aplikację swoich klientów. Cała dokumentacja sfinalizowana, a przeglądane. Projekt jest przekazywane wyłączenia go z jednostką odpowiedzialny za operacje. Ta jednostka może być na przykład IT lub zespół do nauki o danych klientów lub agenta klienta, który jest odpowiedzialny za działanie systemu w środowisku produkcyjnym. 

## <a name="artifacts"></a>Artifacts
Główne artefakt, utworzone w tym ostatnim krokiem jest **wyjść raportów programu projekt klienta**. Ten raport techniczny zawiera wszystkie szczegóły projektu, które są przydatne do nauki o tym, jak korzystać z systemu. Przetwarzanie TDSP zapewnia [Zamknij raport](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) szablonu. Możesz użyć szablonu, ponieważ jest lub można go dostosować do potrzeb określonego klienta. 


## <a name="next-steps"></a>Następne kroki

Poniżej podano linki do każdego kroku w cyklu życia przetwarzania TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i opis](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Oferujemy pełne instruktaże, które pokazują wszystkie kroki procesu dla konkretnych scenariuszy. [Przykładowe przewodniki](walkthroughs.md) artykuł zawiera listę scenariuszy wraz z linkami i opisy miniatur. Przewodniki pokazują, jak połączyć chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 

Przykłady sposób wykonywania kroków w TDSPs, które używają usługi Azure Machine Learning Studio, zobacz [przetwarzania TDSP za pomocą usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
