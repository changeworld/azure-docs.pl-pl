---
title: Obsługiwane języki
titleSuffix: Azure Data Science Virtual Machine
description: Obsługiwane języki programu i narzędzia pokrewne wstępnie zainstalowane w Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: 586cdd6dc06a7685f17c78fa4c4ea2f2ebf52f3d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802400"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Języki obsługiwane na maszynie wirtualnej do nauki o danych 

Data Science Virtual Machine (DSVM) zawiera kilka wstępnie utworzonych języków i narzędzi programistycznych do tworzenia aplikacji sztucznej inteligencji (AI). Poniżej przedstawiono niektóre z nich.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 Edition)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje językowe | Python 2,7 i 3,6 |
| Obsługiwane wersje DSVM      | Windows Server 2016     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Tworzone są dwa środowiska `conda` globalne: <br /> * Środowisko `root`e znajdujące się w `/anaconda/` jest Python 3,6. <br/> * Środowisko `python2`e znajdujące się w `/anaconda/envs/python2` jest Python 2,7.       |
| Zawiera linki do przykładów      | Dołączono przykładowe notesy Jupyter dla języka Python.     |
| Narzędzia pokrewne na DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> Kompilacje systemu Windows Server 2016, które zostały utworzone przed marca 2018, zawierają Python 3,5 i Python 2,7. Python 2,7 to środowisko **Główne** Conda, a **Py35** to środowisko Python 3,5.

### <a name="how-to-use-and-run-it"></a>Jak używać i uruchamiać    

* Uruchom w wierszu polecenia:

  Otwórz wiersz polecenia i użyj jednej z następujących metod, w zależności od wersji języka Python, który chcesz uruchomić:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.6
    activate 
    python --version 
    ```
    
* Użyj w środowisku IDE:

  Użyj Python Tools for Visual Studio (PTVS) zainstalowanej w programie Visual Studio Community Edition. Domyślnie jedynym środowiskiem, które jest konfigurowane automatycznie w PTVS, jest Python 3,6. 

    > [!NOTE]
    > Aby wskazać PTVS w języku Python 2,7, należy utworzyć środowisko niestandardowe w PTVS. Aby ustawić tę ścieżkę środowiska w programie Visual Studio Community Edition, przejdź do pozycji **narzędzia** -> **Python Tools** -> **środowiska Python** i wybierz pozycję **+ niestandardowy**. Następnie Ustaw lokalizację na **c:\anaconda\envs\python2** i wybierz pozycję **Autowykrywanie**.

* Użyj w Jupyter:

  Otwórz Jupyter i wybierz pozycję **New (nowy** ), aby utworzyć nowy Notes. Typ jądra można ustawić jako _Python [Conda root]_ dla języka Python 3,6 i _Python [Conda env: python2]_ dla języka Python 2,7.

* Zainstaluj pakiety języka Python:

  Domyślne środowiska języka Python na DSVM są środowiskami globalnymi, które są odczytywane przez wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, Aktywuj je w środowisku głównym lub python2 za pomocą polecenia `activate` jako administrator. Następnie można zainstalować lub zaktualizować pakiety przy użyciu Menedżera pakietów, takiego jak `conda` lub `pip`.

## <a name="python-linux-edition"></a>Python (wersja Linux)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje językowe | Python 2,7 i 3,5 |
| Obsługiwane wersje DSVM      | Linux   |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Tworzone są dwa środowiska `conda` globalne: <br /> środowisko `root` * znajdujące się w `/anaconda/` to Python 2,7. <br/> środowisko `py35` * znajdujące się w `/anaconda/envs/py35`to Python 3,5.       |
| Zawiera linki do przykładów      | Dołączono przykładowe notesy Jupyter dla języka Python.     |
| Narzędzia pokrewne na DSVM      | PySpark, R, Julia      |
### <a name="how-to-use-and-run-it"></a>Jak używać i uruchamiać    

* Uruchom w terminalu:

  Otwórz Terminal i wykonaj jedną z następujących czynności, w zależności od wersji języka Python, który chcesz uruchomić:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Użyj w środowisku IDE:

  Użyj platformy PyCharm itd zainstalowanego w programie Visual Studio Community Edition. 

* Użyj w Jupyter:

  Otwórz Jupyter i wybierz pozycję **New (nowy** ), aby utworzyć nowy Notes. Typ jądra można ustawić jako **Python [Conda root]** dla języka Python 2,7 i **Python [Conda env: py35]** dla środowiska Python 3,5. 

* Zainstaluj pakiety języka Python:

  Środowiska Python domyślne na maszyny DSVM są globalne środowiska do odczytu dla wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, Aktywuj je w środowisku głównym lub py35 za pomocą polecenia `source activate` jako administrator lub użytkownik z uprawnieniami sudo. Następnie można zainstalować lub zaktualizować pakiety przy użyciu Menedżera pakietów, takiego jak `conda` lub `pip`.


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje językowe | Microsoft R Open 3. x (100% zgodne z CRAN-R)<br /> Microsoft R Server 9. x Developer Edition (skalowalna platforma języka R oparta na przedsiębiorstwie)|
| Obsługiwane wersje DSVM      | Linux, Windows     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Zawiera linki do przykładów      | Dołączono przykładowe notesy Jupyter dla języka R.     |
| Narzędzia pokrewne na DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>Jak używać i uruchamiać    

**Windows**:

* Uruchom w wierszu polecenia:

  Otwórz wiersz polecenia i wpisz `R`.

* Użyj w środowisku IDE:

  Użyj RTools dla programu Visual Studio (RTVS) zainstalowanej w Visual Studio Community edition lub programu RStudio. Są one dostępne w menu Start lub jako ikona pulpitu. 

* Użyj w Jupyter

  Otwórz Jupyter i wybierz pozycję **New (nowy** ), aby utworzyć nowy Notes. Typ jądra można ustawić jako **R** , aby używać jądra Jupyter R (IRKernel).

* Zainstaluj pakiety języka R:

  Język R jest instalowany na DSVM w środowisku globalnym, który jest możliwy do odczytania przez wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, uruchom polecenie R przy użyciu jednej z powyższych metod. Następnie można uruchomić `install.packages()` Menedżera pakietów R, aby zainstalować lub zaktualizować pakiety.

**Linux**:

* Uruchom w terminalu:

  Otwórz Terminal i uruchom `R`.  

* Użyj w środowisku IDE:

  Użyj RStudio zainstalowanego w systemie Linux DSVM.  

* Użyj w Jupyter:

  Otwórz Jupyter i wybierz pozycję **New (nowy** ), aby utworzyć nowy Notes. Typ jądra można ustawić jako **R** , aby używać jądra Jupyter R (IRKernel). 

* Zainstaluj pakiety języka R:

  Język R jest instalowany na DSVM w środowisku globalnym, który jest możliwy do odczytania przez wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, uruchom polecenie R przy użyciu jednej z powyższych metod. Następnie można uruchomić `install.packages()` Menedżera pakietów R, aby zainstalować lub zaktualizować pakiety.


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje językowe | Update 0.6 |
| Obsługiwane wersje DSVM      | Linux, Windows     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Windows: Instalować `C:\JuliaPro-VERSION`<br /> Systemu Linux: Instalować `/opt/JuliaPro-VERSION`    |
| Zawiera linki do przykładów      | Dołączono przykładowe notesy Jupyter dla Julia.     |
| Narzędzia pokrewne na DSVM      | Python, R      |
### <a name="how-to-use-and-run-it"></a>Jak używać i uruchamiać    

**Windows**:

* Uruchom z wiersza polecenia

  Otwórz wiersz polecenia i uruchom `julia`.
* Użyj w środowisku IDE:

  Użyj `Juno` z Julia IDE zainstalowanym w DSVM i dostępne jako skrót na pulpicie.

* Użyj w Jupyter:

  Otwórz Jupyter i wybierz pozycję **New (nowy** ), aby utworzyć nowy Notes. Typ jądra można ustawić jako **wersję Julia**.

* Zainstaluj pakiety Julia:

  Domyślną lokalizacją Julia jest środowisko globalne, które jest odczytywane przez wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, uruchom program Julia przy użyciu jednej z powyższych metod. Następnie można uruchomić polecenia Menedżera pakietów Julia, takie jak `Pkg.add()`, aby zainstalować lub zaktualizować pakiety.


**Linux**:
* Uruchom w terminalu:

  Otwórz Terminal i uruchom `julia`.
* Użyj w środowisku IDE:

  Użyj `Juno`, z Julia IDE zainstalowanym w DSVM i dostępne jako skrót menu **aplikacji** .

* Użyj w Jupyter:

  Otwórz Jupyter i wybierz pozycję **New (nowy** ), aby utworzyć nowy Notes. Typ jądra można ustawić jako **wersję Julia**.

* Zainstaluj pakiety Julia:

  Domyślną lokalizacją Julia jest środowisko globalne, które jest odczytywane przez wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, uruchom program Julia przy użyciu jednej z powyższych metod. Następnie można uruchomić polecenia Menedżera pakietów Julia, takie jak `Pkg.add()`, aby zainstalować lub zaktualizować pakiety.

## <a name="other-languages"></a>Inne języki

**C#** : Dostępne w systemie Windows i dostępne za pomocą programu Visual Studio Community Edition lub `Developer Command Prompt for Visual Studio`, w którym można uruchomić polecenie `csc`.

**Java**: OpenJDK jest dostępny zarówno w wersjach systemu Linux, jak i Windows DSVM i są ustawione na ścieżce. Aby użyć języka Java, wpisz polecenie `javac` lub `java` w wierszu polecenia w systemie Windows lub w powłoce bash na komputerze z systemem Linux.

**Node. js**: Node. js jest dostępny zarówno w wersjach systemu Linux, jak i Windows DSVM i są ustawione na ścieżce. Aby uzyskać dostęp do środowiska Node. js, wpisz `node` lub `npm` polecenie w wierszu polecenia w systemie Windows lub w powłoce bash na komputerze z systemem Linux. W systemie Windows jest zainstalowane rozszerzenie programu Visual Studio dla narzędzi Node. js, aby zapewnić graficzne środowisko IDE do tworzenia aplikacji node. js.

**F#** : Dostępne w systemie Windows i dostępne za pomocą programu Visual Studio Community Edition lub `Developer Command Prompt for Visual Studio`, w którym można uruchomić polecenie `fsc`.
