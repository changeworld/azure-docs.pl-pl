---
title: Obsługiwane języki
titleSuffix: Azure Data Science Virtual Machine
description: Obsługiwane języki programu i narzędzia pokrewne wstępnie zainstalowane w Data Science Virtual Machine.
keywords: narzędzia do nauki dotyczące danych, maszyna wirtualna do nauki o danych, narzędzia do nauki o danych, nauka danych systemu Linux
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
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Języki obsługiwane na Data Science Virtual Machine 

Data Science Virtual Machine (DSVM) zawiera kilka wstępnie utworzonych języków i narzędzi programistycznych do tworzenia aplikacji sztucznej inteligencji (AI). Poniżej przedstawiono niektóre z nich.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 Edition)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje językowe | Python 2,7 i 3,6 |
| Obsługiwane wersje DSVM      | Windows Server 2016     |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Tworzone są dwa globalne środowiska `conda`: <br /> * Środowisko `root` w `/anaconda/` jest Python 3,6. <br/> * Środowisko `python2` w `/anaconda/envs/python2` jest Python 2,7.       |
| Linki do przykładów      | Dołączono przykładowe notesy Jupyter dla języka Python.     |
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
| Jak jest on skonfigurowany/instalowany w DSVM?  | Tworzone są dwa globalne środowiska `conda`: <br /> środowisko *  @ no__t-1 znajduje się w `/anaconda/` to Python 2,7. <br/> środowisko *  @ no__t-1 znajduje się w `/anaconda/envs/py35`IS Python 3,5.       |
| Linki do przykładów      | Dołączono przykładowe notesy Jupyter dla języka Python.     |
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

  Domyślne środowiska języka Python na DSVM są środowiskami globalnymi możliwymi do odczytania przez wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, Aktywuj je w środowisku głównym lub py35 za pomocą polecenia `source activate` jako administrator lub jako użytkownik z uprawnieniami sudo. Następnie można zainstalować lub zaktualizować pakiety przy użyciu Menedżera pakietów, takiego jak `conda` lub `pip`.


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje językowe | Microsoft R Open 3. x (100% zgodne z CRAN-R)<br /> Microsoft R Server 9. x Developer Edition (skalowalna platforma języka R oparta na przedsiębiorstwie)|
| Obsługiwane wersje DSVM      | Linux, Windows     |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Linki do przykładów      | Dołączono przykładowe notesy Jupyter dla języka R.     |
| Narzędzia pokrewne na DSVM      | Spark, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>Jak używać i uruchamiać    

**System Windows**:

* Uruchom w wierszu polecenia:

  Otwórz wiersz polecenia i wpisz `R`.

* Użyj w środowisku IDE:

  Użyj programu RTools for Visual Studio (RTVS) zainstalowanego w programie Visual Studio Community Edition lub RStudio. Są one dostępne w menu Start lub jako ikona pulpitu. 

* Użyj w Jupyter

  Otwórz Jupyter i wybierz pozycję **New (nowy** ), aby utworzyć nowy Notes. Typ jądra można ustawić jako **R** , aby używać jądra Jupyter R (IRKernel).

* Zainstaluj pakiety języka R:

  Język R jest instalowany na DSVM w środowisku globalnym, który jest możliwy do odczytania przez wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, uruchom polecenie R przy użyciu jednej z powyższych metod. Następnie można uruchomić Menedżera pakietów R `install.packages()`, aby zainstalować lub zaktualizować pakiety.

**Linux**:

* Uruchom w terminalu:

  Otwórz Terminal i uruchom `R`.  

* Użyj w środowisku IDE:

  Użyj RStudio zainstalowanego w systemie Linux DSVM.  

* Użyj w Jupyter:

  Otwórz Jupyter i wybierz pozycję **New (nowy** ), aby utworzyć nowy Notes. Typ jądra można ustawić jako **R** , aby używać jądra Jupyter R (IRKernel). 

* Zainstaluj pakiety języka R:

  Język R jest instalowany na DSVM w środowisku globalnym, który jest możliwy do odczytania przez wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, uruchom polecenie R przy użyciu jednej z powyższych metod. Następnie można uruchomić Menedżera pakietów R `install.packages()`, aby zainstalować lub zaktualizować pakiety.


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje językowe | 0,6 |
| Obsługiwane wersje DSVM      | Linux, Windows     |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Windows: zainstalowano w `C:\JuliaPro-VERSION`<br /> Linux: zainstalowano w `/opt/JuliaPro-VERSION`    |
| Linki do przykładów      | Dołączono przykładowe notesy Jupyter dla Julia.     |
| Narzędzia pokrewne na DSVM      | Python, R      |
### <a name="how-to-use-and-run-it"></a>Jak używać i uruchamiać    

**System Windows**:

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

  Użyj `Juno` z Julia IDE zainstalowanym w DSVM i dostępne jako skrót menu **aplikacji** .

* Użyj w Jupyter:

  Otwórz Jupyter i wybierz pozycję **New (nowy** ), aby utworzyć nowy Notes. Typ jądra można ustawić jako **wersję Julia**.

* Zainstaluj pakiety Julia:

  Domyślną lokalizacją Julia jest środowisko globalne, które jest odczytywane przez wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, uruchom program Julia przy użyciu jednej z powyższych metod. Następnie można uruchomić polecenia Menedżera pakietów Julia, takie jak `Pkg.add()`, aby zainstalować lub zaktualizować pakiety.

## <a name="other-languages"></a>Inne języki

**C#** : Dostępne w systemie Windows i dostępne za pomocą programu Visual Studio Community Edition lub `Developer Command Prompt for Visual Studio`, gdzie można uruchomić polecenie `csc`.

**Java**: OpenJDK jest dostępny zarówno w wersjach systemu Linux, jak i Windows DSVM i są ustawione na ścieżce. Aby użyć języka Java, wpisz polecenie `javac` lub `java` w wierszu polecenia w systemie Windows lub w powłoce bash na komputerze z systemem Linux.

**Node. js**: Node. js jest dostępny zarówno w wersjach systemu Linux, jak i Windows DSVM i są ustawione na ścieżce. Aby uzyskać dostęp do środowiska Node. js, wpisz polecenie `node` lub `npm` w wierszu polecenia w systemie Windows lub w powłoce bash na komputerze z systemem Linux. W systemie Windows jest zainstalowane rozszerzenie programu Visual Studio dla narzędzi Node. js, aby zapewnić graficzne środowisko IDE do tworzenia aplikacji node. js.

**F#** : Dostępne w systemie Windows i dostępne za pomocą programu Visual Studio Community Edition lub `Developer Command Prompt for Visual Studio`, gdzie można uruchomić polecenie `fsc`.
