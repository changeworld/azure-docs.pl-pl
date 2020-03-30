---
title: Instalowanie pakietów w notesach jupyter — usługa Azure Notebooks Preview
description: Dowiedz się, jak zainstalować pakiety Języka Python, R i F# z poziomu notesu Jupyter uruchomionego na platformie Azure.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: f6359b4c010834ffaee00b6208d309997339f36e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646232"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Instalowanie pakietów z poziomu usługi Azure Notebooks Preview

Chociaż można skonfigurować [środowisko notesu na poziomie projektu,](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)można zainstalować pakiety bezpośrednio w obrębie pojedynczego notesu.

Pakiety zainstalowane z notesu dotyczą tylko bieżącej sesji serwera. Instalacje pakietów nie są zachowywane po zamknięciu serwera.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="python"></a>Python

Pakiety w Pythonie mogą być instalowane za pomocą pip lub conda za pomocą poleceń w komórkach kodu:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Jeśli dane wyjściowe polecenia wskazuje, że wymaganie jest już spełnione, a następnie notesy platformy Azure może zawierać pakiet domyślnie. Pakiet może być również zainstalowany za pomocą [kroku konfiguracji środowiska projektu](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Pakiety w językach R można zainstalować `install.packages` z CRAN lub GitHub przy użyciu funkcji w komórce kodu:

```r
install.packages("package_name")
```

Można również zainstalować wersje wstępne i inne pakiety programistyczne z gitHub przy użyciu biblioteki devtools:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Pakiety w języku F# można zainstalować z [nuget.org,](https://www.nuget.org) wywołując menedżera zależności Paket z poziomu komórek kodu. Najpierw załaduj menedżera Paket:

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

Następnie załaduj generator Paket:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Otwórz bibliotekę:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Następne kroki

- [Jak: Konfigurowanie projektów i zarządzanie nimi](configure-manage-azure-notebooks-projects.md)
- [Jak: Prezentowanie pokazu slajdów](present-jupyter-notebooks-slideshow.md)
