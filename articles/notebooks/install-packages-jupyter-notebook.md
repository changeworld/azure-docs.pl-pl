---
title: Instalowanie pakietów w notesie Jupyter na platformie Azure
description: Zainstaluj język Python, R, jak i F# pakietów z w ramach notesu programu Jupyter, działające na platformie Azure.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: 5d85c8e936ce7c8bf38ec7bc9c27d9066cc8b155
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277547"
---
# <a name="install-packages-from-within-a-notebook"></a>Instalowanie pakietów z w ramach notesu

Wprawdzie możliwe jest skonfigurowanie [środowisko notesu na poziomie projektu](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), warto zainstalować pakiety bezpośrednio z poziomu pojedynczych notesu.

Zainstalowane pakiety z notesu dotyczą tylko bieżącej sesji serwera. Instalacji pakietów nie są utrwalane, gdy serwer jest zamykany.

## <a name="python"></a>Python

Pakiety języka Python można zainstalować przy użyciu narzędzia pip lub conda przy użyciu poleceń w komórkach kodu:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Jeśli dane wyjściowe polecenia wskazuje, że wymaganie już jest spełniony, a następnie notesów usługi Azure może obejmować pakiet domyślnie. Można także zainstalować pakiet za pośrednictwem [kroku konfiguracji środowiska projektu](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

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

Pakiety w F# mogą być instalowane z [nuget.org](https://www.nuget.org) przez wywołanie zależności Paket menedżera z w komórkach kodu. Najpierw załadować Menedżera Paket:

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

- [Porady: Konfigurowanie projektów i zarządzanie nimi](configure-manage-azure-notebooks-projects.md)
- [Porady: obecne pokaz slajdów](present-jupyter-notebooks-slideshow.md)
