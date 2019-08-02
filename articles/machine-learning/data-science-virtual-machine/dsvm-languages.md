---
title: Obsługiwane języki dla maszyny wirtualnej do nauki o danych
titleSuffix: Azure
description: Więcej informacji na temat języków programu i pokrewnych narzędzi, które są wstępnie zainstalowanych na maszynie wirtualnej do nauki o danych.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: vijetaj
ms.openlocfilehash: 1df3c986132de2a3e7ec9547a996abef60ea04ac
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558118"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Języki obsługiwane na maszynie wirtualnej do nauki o danych 

Maszyna wirtualna do nauki o danych (DSVM) zawiera kilka wbudowanych języki i narzędzia programistyczne do tworzenia aplikacji sztucznej Inteligencji. Poniżej przedstawiono niektóre najważniejsze z nich. 

## <a name="python-windows-server-2016-edition"></a>Python (wersja systemu Windows Server 2016)

|    |           |
| ------------- | ------------- |
| Wersje językowe obsługiwane | 2.7, jak i 3.6 |
| Wersje maszyny DSVM obsługiwane      | Windows Server 2016     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Dwa globalnego `conda` środowiska są tworzone. <br /> * `root` środowisku znajdującym się w `/anaconda/` jest środowisko Python 3.6. <br/> * `python2` środowisku znajdującym się w `/anaconda/envs/python2`jest język Python 2.7       |
| Zawiera linki do przykładów      | Znajdują się przykładowe notesów programu Jupyter dla języka Python     |
| Pokrewne narzędzia na maszyny DSVM      | PySpark, R, Julia      |

> [!NOTE]
> System Windows Server 2016, utworzonych przed marca 2018 r. zawiera język Python 3.5 i środowisko Python 2.7. Środowisko Python 2.7 jest również conda **głównego** środowiska i **py35** to środowisko Python 3.5. 

### <a name="how-to-use--run-it"></a>Jak używać / ją uruchomić?    

* W wierszu polecenia

Otwórz wiersz polecenia, a następnie wykonaj następujące kroki w zależności od wersji środowiska Python, które chcesz uruchomić. 

```
# To run Python 2.7
activate python2
python --version

# To run Python 3.6
activate 
python --version

```
* Za pomocą w środowisku IDE

Użyj narzędzi Python Tools for Visual Studio (PTVS) zainstalowany w wersji programu Visual Studio Community. Konfiguracja środowiska privonly automatycznie w narzędziach PTVS domyślnie jest środowisko Python 3.6. 

> [!NOTE]
> Aby wskazywała szablon PTVS na środowisko Python 2.7, musisz utworzyć środowiska niestandardowego w narzędziach PTVS. Aby ustawić tej ścieżki środowiska Visual Studio Community Edition, przejdź do **narzędzia** -> **narzędzi Python Tools** -> **środowiska Python** a następnie kliknij przycisk **+ niestandardowe**. Następnie ustaw lokalizację na `c:\anaconda\envs\python2` a następnie kliknij przycisk _Autowykrywanie_. 

* Używanie w aplikacji Jupyter

Otwórz Jupyter i kliknij `New` przycisk, aby utworzyć nowy notes. W tym momencie można wybrać typ jądra jako _Python [Conda główny]_ dla środowiska Python 3.6 i _Python [Conda env:python2]_ dla środowiska Python 2.7. 

* Instalowanie pakietów języka Python

Środowiska Python domyślne na maszyny DSVM są globalne środowiska do odczytu dla wszystkich użytkowników. Jednak tylko administratorzy mogą zapisu / instalowanie pakietów globalnego. Aby można było zainstalować pakiet środowisku globalnym, Aktywuj do katalogu głównego lub przy użyciu środowiska python2 `activate` polecenia jako Administrator. Następnie można użyć Menedżera pakietów, takich jak `conda` lub `pip` do instalowania lub aktualizowania pakietów. 

## <a name="python-linux-and-windows-server-2012-edition"></a>Python (Linux i Windows Server 2012 wersja)

|    |           |
| ------------- | ------------- |
| Wersje językowe obsługiwane | 2.7 i 3.5 |
| Wersje maszyny DSVM obsługiwane      | Linux i Windows Server 2012    |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Dwa globalnego `conda` środowiska są tworzone. <br /> * `root` środowisku znajdującym się w `/anaconda/` jest język Python 2.7. <br/> * `py35` środowisku znajdującym się w `/anaconda/envs/py35`jest język Python 3.5       |
| Zawiera linki do przykładów      | Znajdują się przykładowe notesów programu Jupyter dla języka Python     |
| Pokrewne narzędzia na maszyny DSVM      | PySpark, R, Julia      |
### <a name="how-to-use--run-it"></a>Jak używać / ją uruchomić?    

**Linux**
* W terminalu

Otwórz terminal i wykonaj następujące kroki w zależności od wersji środowiska Python, które chcesz uruchomić. 

```
# To run Python 2.7
source activate 
python --version

# To run Python 3.5
source activate py35
python --version

```
* Za pomocą w środowisku IDE

Użyj platformy PyCharm zainstalowana wersja programu Visual Studio Community. 

* Używanie w aplikacji Jupyter

Otwórz Jupyter i kliknij `New` przycisk, aby utworzyć nowy notes. W tym momencie można wybrać typ jądra jako _Python [Conda główny]_ for Python 2.7 i _Python [Conda env:py35]_ dla środowiska Python 3.5. 

* Instalowanie pakietów języka Python

Środowiska Python domyślne na maszyny DSVM są globalne środowiska do odczytu dla wszystkich użytkowników. Jednak tylko administratorzy mogą zapisu / instalowanie pakietów globalnego. Aby można było zainstalować pakiet środowisku globalnym, Aktywuj do katalogu głównego lub przy użyciu środowiska py35 `source activate` polecenia jako Administrator lub użytkownik z uprawnieniami "sudo". Następnie można użyć Menedżera pakietów, takich jak `conda` lub `pip` do instalowania lub aktualizowania pakietów. 

**Windows 2012**
* W wierszu polecenia

Otwórz wiersz polecenia, a następnie wykonaj następujące kroki w zależności od wersji środowiska Python, które chcesz uruchomić. 

```
# To run Python 2.7
activate 
python --version

# To run Python 3.5
activate py35
python --version

```
* Za pomocą w środowisku IDE

Użyj narzędzi Python Tools for Visual Studio (PTVS) zainstalowany w wersji programu Visual Studio Community. Ustawienia środowiska privonly automatycznie w narzędziach PTVS w środowisko Python 2.7. 
> [!NOTE]
> Aby wskazywała szablon PTVS na język Python 3.5, należy utworzyć środowiska niestandardowego w narzędziach PTVS. Aby ustawić tej ścieżki środowiska Visual Studio Community Edition, przejdź do **narzędzia** -> **narzędzi Python Tools** -> **środowiska Python** a następnie kliknij przycisk **+ niestandardowe**. Następnie ustaw lokalizację na `c:\anaconda\envs\py35` a następnie kliknij przycisk _Autowykrywanie_. 

* Używanie w aplikacji Jupyter

Otwórz Jupyter i kliknij `New` przycisk, aby utworzyć nowy notes. W tym momencie można wybrać typ jądra jako _Python [Conda główny]_ for Python 2.7 i _Python [Conda env:py35]_ dla środowiska Python 3.5. 

* Instalowanie pakietów języka Python

Środowiska Python domyślne na maszyny DSVM są globalne środowiska do odczytu dla wszystkich użytkowników. Jednak tylko administratorzy mogą zapisu / instalowanie pakietów globalnego. Aby można było zainstalować pakiet środowisku globalnym, Aktywuj do katalogu głównego lub przy użyciu środowiska py35 `activate` polecenia jako Administrator. Następnie można użyć Menedżera pakietów, takich jak `conda` lub `pip` do instalowania lub aktualizowania pakietów. 

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Wersje językowe obsługiwane | Microsoft R Open 3.x (100% zgodny z sieci CRAN-R<br /> Microsoft R Server 9.x Developer edition (Enterprise skalowalne gotowe platforma R)|
| Wersje maszyny DSVM obsługiwane      | Linux, Windows     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Zawiera linki do przykładów      | Znajdują się przykładowe notesów programu Jupyter dla języka R     |
| Pokrewne narzędzia na maszyny DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use--run-it"></a>Jak używać / ją uruchomić?    

**Windows**:

* W wierszu polecenia

Otwórz wiersz polecenia i po prostu wpisz `R`.

* Za pomocą w środowisku IDE

Użyj RTools dla programu Visual Studio (RTVS) zainstalowanej w Visual Studio Community edition lub programu RStudio. Są dostępne w start menu lub jako ikony pulpitu. 

* Używanie w aplikacji Jupyter

Otwórz Jupyter i kliknij `New` przycisk, aby utworzyć nowy notes. W tym momencie można wybrać typ jądra jako _R_ służące Jupyter R jądra (IRKernel). 

* Instalowanie pakietów języka R

Język R jest zainstalowany na DSVM w środowisku globalnym do odczytu dla wszystkich użytkowników. Jednak tylko administratorzy mogą zapisu / instalowanie pakietów globalnego. Aby można było zainstalować pakiet środowisku globalnym, uruchamiaj język R, przy użyciu jednej z powyższych metod. Następnie możesz uruchomić Menedżera pakietów języka R `install.packages()` do instalowania lub aktualizowania pakietów. 

**Linux**:

* W terminalu

Otwórz terminal i po prostu uruchom `R`.  

* Za pomocą w środowisku IDE

Użyj programu RStudio w systemie Linux maszyny wirtualnej DSVM.  

* Używanie w aplikacji Jupyter

Otwórz Jupyter i kliknij `New` przycisk, aby utworzyć nowy notes. W tym momencie można wybrać typ jądra jako _R_ służące Jupyter R jądra (IRKernel). 

* Instalowanie pakietów języka R

Język R jest zainstalowany na DSVM w środowisku globalnym do odczytu dla wszystkich użytkowników. Jednak tylko administratorzy mogą zapisu / instalowanie pakietów globalnego. Aby można było zainstalować pakiet środowisku globalnym, uruchamiaj język R, przy użyciu jednej z powyższych metod. Następnie możesz uruchomić Menedżera pakietów języka R `install.packages()` do instalowania lub aktualizowania pakietów. 


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Wersje językowe obsługiwane | Update 0.6 |
| Wersje maszyny DSVM obsługiwane      | Linux, Windows     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | W systemie Windows: Zainstalowana o`C:\JuliaPro-VERSION`<br /> W systemie Linux: Zainstalowana o`/opt/JuliaPro-VERSION`    |
| Zawiera linki do przykładów      | Znajdują się przykładowe notesów programu Jupyter dla Julia     |
| Pokrewne narzędzia na maszyny DSVM      | Python, R      |
### <a name="how-to-use--run-it"></a>Jak używać / ją uruchomić?    

**Windows**:

* W wierszu polecenia

Otwórz wiersz polecenia i uruchom `julia`. 
* Za pomocą w środowisku IDE

Użyj `Juno` IDE Julia zainstalowane na maszyny DSVM i dostępne jako skrót na pulpicie.

* Używanie w aplikacji Jupyter

Otwórz Jupyter i kliknij `New` przycisk, aby utworzyć nowy notes. W tym momencie można wybrać typ jądra jako `Julia VERSION` 

* Instalowanie pakietów Julia

Domyślna lokalizacja Julia jest środowisku globalnym do odczytu dla wszystkich użytkowników. Jednak tylko administratorzy mogą zapisu / instalowanie pakietów globalnego. Aby można było zainstalować pakiet środowisku globalnym, uruchom Julia przy użyciu jednej z powyższych metod. Następnie możesz uruchomić pakiet Julia Menedżera poleceń, takich jak `Pkg.add()` do instalowania lub aktualizowania pakietów. 


**Linux**:
* Uruchamiany w terminalu.

Otwórz terminal i po prostu uruchom `julia`. 
* Za pomocą w środowisku IDE

Użyj `Juno` Julia IDE zainstalowanego w DSVM i dostępne jako skrót menu aplikacji.

* Używanie w aplikacji Jupyter

Otwórz Jupyter i kliknij `New` przycisk, aby utworzyć nowy notes. W tym momencie można wybrać typ jądra jako `Julia VERSION` 

* Instalowanie pakietów Julia

Domyślna lokalizacja Julia jest środowisku globalnym do odczytu dla wszystkich użytkowników. Jednak tylko administratorzy mogą zapisu / instalowanie pakietów globalnego. Aby można było zainstalować pakiet środowisku globalnym, uruchom Julia przy użyciu jednej z powyższych metod. Następnie możesz uruchomić pakiet Julia Menedżera poleceń, takich jak `Pkg.add()` do instalowania lub aktualizowania pakietów. 

## <a name="other-languages"></a>Inne języki

**C#** : Dostępne w systemie Windows i dostępne za pomocą programu Visual Studio Community Edition lub `Developer Command Prompt for Visual Studio` w miejscu, w którym `csc` można po prostu uruchomić polecenie. 

**Język Java**: OpenJDK jest dostępna w systemie Linux i Windows w wersji DSVM i ustawiona na ścieżce. Możesz wpisać `javac` lub `java` polecenia w wierszu polecenia w Windows lub w powłoce bash w systemie Linux, aby użyć platformy Java. 

**node.js**: node.js jest dostępna w wersji systemu Linux i Windows maszyny wirtualnej DSVM i zestawu w ścieżce. Możesz wpisać `node` lub `npm` polecenia w wierszu polecenia w Windows lub w powłoce bash w systemie Linux do dostępu do środowiska node.js. Na Windows narzędzia Node.js dla rozszerzenia programu Visual Studio jest zainstalowany na zapewnienie graficznego środowiska IDE do tworzenia aplikacji node.js. 

**F#** : Dostępne w systemie Windows i dostępne za pomocą programu Visual Studio Community Edition lub `Developer Command Prompt for Visual Studio` w miejscu, w którym `fsc` można po prostu uruchomić polecenie. 


