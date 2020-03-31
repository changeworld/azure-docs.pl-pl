---
title: Obsługiwane języki
titleSuffix: Azure Data Science Virtual Machine
description: Obsługiwane języki programu i powiązane narzędzia są wstępnie zainstalowane na maszynie wirtualnej do nauki o danych.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: e7b32579712e89c0d5595303ee7e03d8b2462607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283656"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Języki obsługiwane na maszynie wirtualnej do nauki o danych 

Maszyna wirtualna do nauki o danych (DSVM) jest wyposażona w kilka wstępnie utworzonych języków i narzędzi programistycznych do tworzenia aplikacji sztucznej inteligencji (AI). Oto niektóre z tych znaczących.

## <a name="python-windows-server-2016-edition"></a>Python (wersja systemu Windows Server 2016)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje językowe | Python 2.7 i 3.7 |
| Obsługiwane wersje DSVM      | Windows Server 2016     |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | Tworzone `conda` są dwa środowiska globalne: <br /> * `root` Środowisko znajduje `/anaconda/` się w Pythonie 3.7. <br/> * `python2` Środowisko znajduje `/anaconda/envs/python2` się w Pythonie 2.7.       |
| Odnośniki do próbek      | Przykładowe notesy Jupyter dla języka Python są dołączone.     |
| Powiązane narzędzia w systemie DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> Kompilacje systemu Windows Server 2016 utworzone przed marcem 2018 r. zawierają python 3.5 i Python 2.7. Python 2.7 jest środowiskiem **głównym** conda, a **py37** jest środowiskiem Pythona 3.7.

### <a name="how-to-use-and-run-it"></a>Jak go używać i uruchamiać    

* Uruchom w wierszu polecenia:

  Otwórz wiersz polecenia i użyj jednej z następujących metod, w zależności od wersji języka Python, którą chcesz uruchomić:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Użyj w IDE:

  Użyj narzędzia Python dla programu Visual Studio (PTVS), zainstalowane w wersji społeczności programu Visual Studio. Domyślnie jedynym środowiskiem, które jest automatycznie skonfigurowane w PTVS jest Python 3.6. 

    > [!NOTE]
    > Aby wskazać PTVS w języku Python 2.7, należy utworzyć środowisko niestandardowe w PTVS. Aby ustawić tę ścieżkę środowiska w programie Visual Studio Community Edition, przejdź do **środowiska Narzędzia** -> **Python** -> **Python i** wybierz pozycję + **Niestandardowe**. Następnie ustaw lokalizację na **c:\anaconda\envs\python2** i wybierz opcję **Automatyczne wykrywanie**.

* Zastosowanie w jupirze:

  Otwórz pozycję Jupyter i wybierz pozycję **Nowy,** aby utworzyć nowy notes. Typ jądra można ustawić jako _Python [Conda Root]_ dla Pythona 3.7 i _Pythona [Conda env:python2]_ dla Języka Python 2.7.

* Zainstaluj pakiety Pythona:

  Domyślne środowiska Języka Python w systemie DSVM są środowiskami globalnymi, które są czytelne dla wszystkich użytkowników. Ale tylko administratorzy mogą pisać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, należy aktywować `activate` w środowisku głównym lub python2 za pomocą polecenia jako administrator. Następnie można użyć menedżera pakietów, takich jak `conda` lub `pip` zainstalować lub zaktualizować pakiety.

## <a name="python-linux-edition"></a>Python (edycja Linuksa)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje językowe | Python 2.7 i 3.5 |
| Obsługiwane wersje DSVM      | Linux   |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | Tworzone `conda` są dwa środowiska globalne: <br /> * `root`środowiska znajdującego się w pythonie `/anaconda/` 2.7. <br/> * `py35`środowiska znajdującego się w pythonie `/anaconda/envs/py35`3.5.       |
| Odnośniki do próbek      | Przykładowe notesy Jupyter dla języka Python są dołączone.     |
| Powiązane narzędzia w systemie DSVM      | PySpark, R, Julia      |
### <a name="how-to-use-and-run-it"></a>Jak go używać i uruchamiać    

* Uruchom w terminalu:

  Otwórz terminal i wykonaj jedną z następujących czynności, w zależności od wersji języka Python, którą chcesz uruchomić:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Użyj w IDE:

  Użyj PyCharm, zainstalowany w wersji społeczności programu Visual Studio. 

* Zastosowanie w jupirze:

  Otwórz pozycję Jupyter i wybierz pozycję **Nowy,** aby utworzyć nowy notes. Typ jądra można ustawić jako **Python [Conda Root]** dla Pythona 2.7 i **Pythona [Conda env:py35]** dla środowiska Python 3.5. 

* Zainstaluj pakiety Pythona:

  Domyślne środowiska Języka Python w systemie DSVM są środowiskami globalnymi czytelnymi dla wszystkich użytkowników. Ale tylko administratorzy mogą pisać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, aktywuj w środowisku `source activate` głównym lub py35 za pomocą polecenia jako administrator lub jako użytkownik z uprawnieniami sudo. Następnie można użyć menedżera pakietów, takich jak `conda` lub `pip` zainstalować lub zaktualizować pakiety.


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje językowe | Microsoft R Open 3.x (100% kompatybilny z CRAN-R)<br /> Microsoft R Server 9.x Developer edition (skalowalna platforma R gotowa do pracy)|
| Obsługiwane wersje DSVM      | Linux, Windows     |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | Windows:`C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux:`/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Odnośniki do próbek      | Przykładowe notesy Jupyter dla R są dołączone.     |
| Powiązane narzędzia w systemie DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>Jak go używać i uruchamiać    

**Okna**:

* Uruchom w wierszu polecenia:

  Otwórz wiersz polecenia i wpisz `R`.

* Użyj w IDE:

  Użyj RTools for Visual Studio (RTVS) zainstalowanego w wersji społeczności programu Visual Studio lub RStudio. Są one dostępne w menu Start lub jako ikona pulpitu. 

* Zastosowanie w Jupyter

  Otwórz pozycję Jupyter i wybierz pozycję **Nowy,** aby utworzyć nowy notes. Typ jądra można ustawić jako **R,** aby używał jądra Jupyter R (IRKernel).

* Zainstaluj pakiety R:

  R jest zainstalowany na DSVM w środowisku globalnym, który jest czytelny dla wszystkich użytkowników. Ale tylko administratorzy mogą pisać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, uruchom R przy użyciu jednej z powyższych metod. Następnie można uruchomić Menedżera pakietów `install.packages()` R, aby zainstalować lub zaktualizować pakiety.

**Linux**:

* Uruchom w terminalu:

  Otwórz terminal i `R`uruchom .  

* Użyj w IDE:

  Użyj RStudio, zainstalowany na Linux DSVM.  

* Zastosowanie w jupirze:

  Otwórz pozycję Jupyter i wybierz pozycję **Nowy,** aby utworzyć nowy notes. Typ jądra można ustawić jako **R,** aby używał jądra Jupyter R (IRKernel). 

* Zainstaluj pakiety R:

  R jest zainstalowany na DSVM w środowisku globalnym, który jest czytelny dla wszystkich użytkowników. Ale tylko administratorzy mogą pisać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, uruchom R przy użyciu jednej z powyższych metod. Następnie można uruchomić Menedżera pakietów `install.packages()` R, aby zainstalować lub zaktualizować pakiety.


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje językowe | 0,6 |
| Obsługiwane wersje DSVM      | Linux, Windows     |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | Windows: Zainstalowany w`C:\JuliaPro-VERSION`<br /> Linux: Zainstalowany w`/opt/JuliaPro-VERSION`    |
| Odnośniki do próbek      | Przykładowe zeszyty Jupyter dla Julii są dołączone.     |
| Powiązane narzędzia w systemie DSVM      | Python, R      |
### <a name="how-to-use-and-run-it"></a>Jak go używać i uruchamiać    

**Okna**:

* Uruchamianie w wierszu polecenia

  Otwórz wiersz polecenia `julia`i uruchom polecenie .
* Użyj w IDE:

  Użyj `Juno` z Julia IDE zainstalowany na DSVM i dostępne jako skrót pulpitu.

* Zastosowanie w jupirze:

  Otwórz pozycję Jupyter i wybierz pozycję **Nowy,** aby utworzyć nowy notes. Typ jądra można ustawić jako **Julia VERSION**.

* Zainstaluj pakiety Julia:

  Domyślna lokalizacja Julia to środowisko globalne, które jest czytelne dla wszystkich użytkowników. Ale tylko administratorzy mogą pisać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, uruchom Julię przy użyciu jednej z powyższych metod. Następnie można uruchomić polecenia Menedżera pakietów Julia, takie jak `Pkg.add()` instalowanie lub aktualizowanie pakietów.


**Linux**:
* Uruchom w terminalu:

  Otwórz terminal i `julia`uruchom .
* Użyj w IDE:

  Użyj `Juno`, z Julia IDE zainstalowany na DSVM i dostępne jako skrót menu **aplikacji.**

* Zastosowanie w jupirze:

  Otwórz pozycję Jupyter i wybierz pozycję **Nowy,** aby utworzyć nowy notes. Typ jądra można ustawić jako **Julia VERSION**.

* Zainstaluj pakiety Julia:

  Domyślna lokalizacja Julia to środowisko globalne, które jest czytelne dla wszystkich użytkowników. Ale tylko administratorzy mogą pisać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, uruchom Julię przy użyciu jednej z powyższych metod. Następnie można uruchomić polecenia Menedżera pakietów Julia, takie jak `Pkg.add()` instalowanie lub aktualizowanie pakietów.

## <a name="other-languages"></a>Inne języki

**C#**: Dostępne w systemie Windows i dostępne `Developer Command Prompt for Visual Studio`za pośrednictwem wersji `csc` Visual Studio Community lub w programie , gdzie można uruchomić polecenie.

**Java:** OpenJDK jest dostępny zarówno w wersjach SYSTEMU Linux, jak i Windows dsvm i jest ustawiony na ścieżce. Aby użyć oprogramowania `javac` `java` Java, wpisz polecenie lub polecenie w wierszu polecenia w systemie Windows lub na powłoce bash w systemie Linux.

**Node.js:** Node.js jest dostępny zarówno w wersjach Systemu Linux, jak i Windows dsvm i jest ustawiony na ścieżce. Aby uzyskać dostęp do pliku `node` `npm` Node.js, wpisz polecenie lub polecenie w wierszu polecenia w systemie Windows lub w powłoce bash w systemie Linux. W systemie Windows jest zainstalowane rozszerzenie programu Visual Studio dla narzędzi Node.js w celu zapewnienia graficznego ide do tworzenia aplikacji Node.js.

**F#**: Dostępne w systemie Windows i dostępne `Developer Command Prompt for Visual Studio`za pośrednictwem wersji `fsc` Visual Studio Community lub w programie , gdzie można uruchomić polecenie.
