---
title: Instalowanie pakietów w notesie Jupyter na platformie Azure
description: Jak zainstalować język Python, R i F# pakiety z poziomu notesu Jupyter działającego na platformie Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: e6582bb21cb6d383e298bdf68bdb2f565147c20a
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969935"
---
# <a name="install-packages-from-within-a-notebook"></a>Instalowanie pakietów z poziomu notesu

Mimo że można skonfigurować [środowisko dla notesu na poziomie projektu](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), można zainstalować pakiety bezpośrednio w ramach danego notesu.

Pakiety zainstalowane w notesie dotyczą tylko bieżącej sesji serwera. Instalacje pakietów nie są utrwalane po zamknięciu serwera.

## <a name="python"></a>Python

Pakiety w języku Python można instalować przy użyciu PIP lub Conda przy użyciu poleceń w komórkach kodu:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Jeśli dane wyjściowe polecenia wskazują, że wymaganie jest już spełnione, Azure Notebooks może zawierać pakiet domyślnie. Pakiet może być również instalowany za pomocą [kroku konfiguracji środowiska projektu](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Pakiety w języku R można instalować z CRAN lub GitHub przy użyciu funkcji `install.packages` w komórce kodu:

```r
install.packages("package_name")
```

Możesz również zainstalować wersje wstępne i inne pakiety deweloperskie z usługi GitHub przy użyciu biblioteki devtools:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Pakiety w F# programie można instalować z [NuGet.org](https://www.nuget.org) przez wywołanie Menedżera zależności Paket z poziomu komórek kodu. Najpierw załaduj program Paket Manager:

```fsharp
#load "Paket.fsx"
```

Następnie zainstaluj pakiety:

```fsharp
Paket.Package
  [ "MathNet.Numerics"
    "MathNet.Numerics.FSharp"
  ]
```

Następnie załaduj Generator Paket:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Otwórz Library:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Następne kroki

- [Instrukcje: Konfigurowanie projektów i zarządzanie nimi](configure-manage-azure-notebooks-projects.md)
- [Instrukcje: prezentowanie pokazu slajdów](present-jupyter-notebooks-slideshow.md)
