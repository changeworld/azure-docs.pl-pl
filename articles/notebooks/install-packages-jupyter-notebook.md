---
title: Instalowanie pakietów w notesie Jupyter na platformie Azure
description: Zainstaluj język Python, R, jak i F# pakietów z w ramach notesu programu Jupyter, działające na platformie Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: ed57cb458cc1f71c5d6fd2475c54cc712257dd84
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084876"
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

Można zainstalować pakietów w języku R z sieci CRAN lub Github przy użyciu `install.packages` funkcji w komórce kodu:

```r
install.packages("package_name")
```

Z usługi Github za pomocą biblioteki devtools, można zainstalować wersje wstępne i inne pakiety programowania:

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

## <a name="next-steps"></a>Kolejne kroki

- [Porady: Konfigurowanie projektów i zarządzanie nimi](configure-manage-azure-notebooks-projects.md)
- [Porady: obecne pokaz slajdów](present-jupyter-notebooks-slideshow.md)
