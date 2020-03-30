---
title: Instalowanie programu TmaxSoft OpenFrame na maszynach wirtualnych platformy Azure
description: Hostuj ponownie obciążenia komputerów mainframe IBM z/OS przy użyciu środowiska TmaxSoft OpenFrame na maszynach wirtualnych platformy Azure(VM).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 1ad6e52c421d9cfec4640d3a330b5507d6ed3e9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72436051"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Instalowanie programu TmaxSoft OpenFrame na platformie Azure

Dowiedz się, jak skonfigurować środowisko OpenFrame na platformie Azure odpowiednie do tworzenia, tworzenia, testowania lub obciążeń produkcyjnych. Ten samouczek przeprowadzi Cię przez każdy krok.

OpenFrame zawiera wiele składników, które tworzą środowisko emulacji mainframe na platformie Azure. Na przykład usługi online OpenFrame zastępują oprogramowanie pośredniczące typu mainframe, takie jak IBM Customer Information Control System (CICS), a OpenFrame Batch, komponentem TJES, zastępuje podsystem wejścia zadań (JES) firmy IBM mainframe.

OpenFrame współpracuje z dowolną relacyjną bazą danych, w tym Bazą danych Oracle Database, Microsoft SQL Server, IBM Db2 i MySQL. Ta instalacja OpenFrame wykorzystuje tmaxsoft Tibero relacyjnej bazy danych. Zarówno OpenFrame, jak i Tibero działają w systemie operacyjnym Linux. Ten samouczek instaluje CentOS 7.3, chociaż można użyć innych obsługiwanych dystrybucji Linuksa. Serwer aplikacji OpenFrame i baza danych Tibero są instalowane na jednej maszynie wirtualnej (VM).

Samouczek przechodzi przez instalację składników pakietu OpenFrame. Niektóre z nich muszą być zainstalowane oddzielnie.

Główne składniki OpenFrame:

- Wymagane pakiety instalacyjne.
- Bazy danych Tibero.
- Open Database Connectivity (ODBC) jest używany przez aplikacje w OpenFrame do komunikowania się z bazą danych Tibero.
- OpenFrame Base, oprogramowanie pośredniczące, które zarządza całym systemem.
- OpenFrame Batch, rozwiązanie, które zastępuje systemy wsadowe mainframe.
- TACF, moduł usługi, który kontroluje dostęp użytkowników do systemów i zasobów.
- ProSort, narzędzie sortowania dla transakcji wsadowych.
- OFCOBOL, kompilator, który interpretuje programy COBOL mainframe.
- OFASM, kompilator, który interpretuje programy asemblera mainframe.
- OpenFrame Server Type C (OSC), rozwiązanie zastępujące oprogramowanie pośredniczące mainframe i IBM CICS.
- Java Enterprise User Solution (JEUS), serwer aplikacji sieci web, który jest certyfikowany dla Java Enterprise Edition 6.
- OFGW, składnik bramy OpenFrame, który zapewnia odbiornik 3270.
- OFManager, rozwiązanie, które zapewnia działanie i funkcje zarządzania OpenFrame w środowisku internetowym.

Inne wymagane składniki OpenFrame:

- OSI, rozwiązanie, które zastępuje mainframe middleware i IMS DC.
- TJES, rozwiązanie, które zapewnia środowisko JES komputera mainframe.
- OFTSAM, rozwiązanie, które umożliwia (V)SAM plików do wykorzystania w systemie otwartym.
- OFHiDB, rozwiązanie, które zastępuje mainframe IMS DB.
- OFPLI, kompilator, który interpretuje mainframe PL / I programów.
- PROTRIEVE, rozwiązanie, które wykonuje język mainframe CA-Easytrieve.
- OFMiner, rozwiązanie, które analizuje zasoby komputerów mainframe, a następnie migruje je na platformę Azure.

## <a name="architecture"></a>Architektura

Poniższy rysunek zawiera omówienie składników architektonicznych OpenFrame 7.0 zainstalowanych w tym samouczku:

![Składniki OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Wymagania systemowe platformy Azure

W poniższej tabeli wymieniono wymagania dotyczące instalacji na platformie Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Wymaganie</th><th>Opis</th></tr>
</thead>
<tbody>
<tr><td>Obsługiwane dystrybucje systemu Linux na platformie Azure
</td>
<td>
Linux x86 2.6 (32-bitowy, 64-bitowy)<br/>
Czerwony Kapelusz 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Sprzęt
</td>
<td>Rdzenie: 2 (minimum)<br/>
Pamięć: 4 GB (minimum)<br/>
Miejsce wymiany: 1 GB (minimum)<br/>
Dysk twardy: 100 GB (minimum)<br/>
</td>
</tr>
<tr><td>Opcjonalne oprogramowanie dla użytkowników systemu Windows
</td>
<td>PuTTY: Używany w tym przewodniku do konfigurowania funkcji maszyny Wirtualnej<br/>
WinSCP: Popularny klient SFTP i klient FTP, którego można użyć<br/>
Eclipse dla Windows: Platforma deweloperska obsługiwana przez TmaxSoft<br/>
(Program Microsoft Visual Studio nie jest obecnie obsługiwany)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Wymagania wstępne

Zaplanuj spędzenie kilku dni na zebraniu całego wymaganego oprogramowania i ukończeniu wszystkich procesów ręcznych.

Przed rozpoczęciem wykonaj następujące czynności:

- Pobierz nośnik instalacyjny OpenFrame firmy TmaxSoft. Jeśli jesteś istniejącym klientem TmaxSoft, skontaktuj się z przedstawicielem TmaxSoft w celu uzyskania licencjonowanej kopii. W przeciwnym razie zażądaj wersji próbnej od [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Poproś o dokumentację OpenFrame, wysyłając wiadomość e-mail na adres <support@tmaxsoft.com>.

- Uzyskaj subskrypcję platformy Azure, jeśli jeszcze jej nie masz. Możesz również utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

- Element opcjonalny. Skonfiguruj tunel sieci VPN typu lokacja-lokacja lub pole szybkiego dostępu, które ogranicza dostęp do maszyny Wirtualnej platformy Azure do dozwolonych użytkowników w organizacji. Ten krok nie jest wymagane, ale jest najlepszym rozwiązaniem.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Konfigurowanie maszyny wirtualnej na platformie Azure dla openframe i Tibero

Środowisko OpenFrame można skonfigurować przy użyciu różnych wzorców wdrażania, ale w poniższej procedurze pokazano, jak wdrożyć serwer aplikacji OpenFrame i bazę danych Tibero na jednej maszynie wirtualnej. W większych środowiskach i dla dużych obciążeń najlepszym rozwiązaniem jest wdrożenie bazy danych oddzielnie na własnej maszynie wirtualnej w celu uzyskania lepszej wydajności.

**Aby utworzyć maszynę wirtualną**

1. Przejdź do witryny <https://portal.azure.com> Azure portal i zaloguj się na swoje konto.

2. Kliknij pozycję **Maszyny wirtualne**.

    ![Lista zasobów w witrynie Azure portal](media/vm-01.png)

3. Kliknij przycisk **Dodaj**.

    ![Opcja Dodaj w witrynie Azure portal](media/vm-02.png)

4. Po prawej stronie **systemów operacyjnych**kliknij pozycję **Więcej**.

     ![Więcej opcji w witrynie Azure portal](media/vm-03.png)

5. Kliknij **7.3 opartą na CentOS,** aby dokładnie wykonać ten spacer, lub możesz wybrać inną obsługiwaną dystrybucję Linuksa.

     ![Opcje systemu operacyjnego w witrynie Azure portal](media/vm-04.png)

6. W **ustawieniach Podstawowe** wprowadź **nazwę**, **nazwę użytkownika,** **typ uwierzytelniania**, **subskrypcję** (Płatność zgodnie z rzeczywistym użyciem to styl płatności AWS) i **grupę Zasobów** (użyj istniejącej lub utwórz grupę TmaxSoft).

7. Po zakończeniu (w tym pary kluczy publicznych/prywatnych dla **typu uwierzytelnianie)** kliknij przycisk **Prześlij**.

> [!NOTE]
> Jeśli przy użyciu klucza publicznego SSH dla **typu uwierzytelniania,** zobacz kroki w następnej sekcji, aby wygenerować parę klucza publicznego/prywatnego, a następnie wznowić kroki tutaj.

### <a name="generate-a-publicprivate-key-pair"></a>Generowanie pary kluczy publicznych/prywatnych

Jeśli używasz systemu operacyjnego Windows, musisz PuTTYgen do generowania pary kluczy publicznych /prywatnych.

Klucz publiczny może być swobodnie udostępniany, ale klucz prywatny powinien być całkowicie tajny i nigdy nie powinien być udostępniany innej stronie. Po wygenerowaniu kluczy należy wkleić **klucz publiczny SSH** do konfiguracji — w efekcie przekazać go do maszyny Wirtualnej systemu Linux. Jest on przechowywany\_wewnątrz autoryzowanych kluczy w \~katalogu /.ssh katalogu macierzystego konta użytkownika. Maszyna wirtualna z systemem Linux jest wtedy w stanie rozpoznać i zweryfikować połączenie po podaniu **skojarzonego klucza prywatnego SSH** w kliencie SSH (w naszym przypadku PuTTY).

Podczas udzielania nowym osobom dostępu do maszyny Wirtualnej: 

- Każda nowa osoba generuje własne klucze publiczne / prywatne za pomocą PuTTYgen.
- Osoby fizyczne przechowują własne klucze prywatne oddzielnie i wysyłają informacje o kluczu publicznym do administratora maszyny Wirtualnej.
- Administrator wkleja zawartość klucza publicznego do pliku \~/.ssh/authorized\_keys.
- Nowa osoba łączy się za pośrednictwem PuTTY.

**Aby wygenerować parę kluczy publicznych/prywatnych**

1.  Pobierz PuTTYgen <https://www.putty.org/> z i zainstaluj go przy użyciu wszystkich ustawień domyślnych.

2.  Aby otworzyć PuTTYgen, znajdź katalog instalacji PuTTY w języku C:\\Program Files\\PuTTY.

    ![Interfejs PuTTY](media/puttygen-01.png)

3.  Kliknij pozycję **Generate** (Generuj).

    ![Okno dialogowe Generator kluczy PuTTY](media/puttygen-02.png)

4.  Po pokoleniu zapisz klucz publiczny i klucz prywatny. Wklej zawartość klucza publicznego w sekcji **Klucz publiczny SSH** w okienku **Tworzenie podstaw maszyny wirtualnej \> ** (pokazano w krokach 6 i 7 w poprzedniej sekcji).

    ![Okno dialogowe Generator kluczy PuTTY](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Konfigurowanie funkcji maszyny Wirtualnej

1. W witrynie Azure portal w bloku **Wybierz rozmiar** wybierz żądane ustawienia sprzętowe maszyny z systemem Linux. *Minimalne* wymagania dotyczące instalacji zarówno Tibero, jak i OpenFrame to 2 procesory i 4 GB pamięci RAM, jak pokazano w tym przykładzie instalacji:

    ![Tworzenie maszyny wirtualnej — podstawy](media/create-vm-01.png)

2. Kliknij **3 Ustawienia** i użyj ustawień domyślnych, aby skonfigurować funkcje opcjonalne.
3. Przejrzyj szczegóły płatności.

    ![Tworzenie maszyny wirtualnej — zakup](media/create-vm-02.png)

4. Prześlij swoje wybory. Platforma Azure rozpoczyna wdrażanie maszyny Wirtualnej. Ten proces trwa zwykle kilka minut.

5. Po wdrożeniu maszyny Wirtualnej wyświetlany jest jej pulpit nawigacyjny, pokazujący wszystkie ustawienia, które zostały wybrane podczas konfiguracji. Zanotuj **publiczny adres IP**.

    ![tmax na pulpicie nawigacyjnym platformy Azure](media/create-vm-03.png)

6. Otwórz program PuTTY.

7. W przypadku **nazwy hosta**wpisz nazwę użytkownika i skopiowany publiczny adres IP. Na przykład **\@nazwa użytkownika publicip**.

    ![Okno dialogowe Konfiguracja putty](media/putty-01.png)

8. W polu **Kategoria** kliknij pozycję **Podłącz \> Auth SSH \> **. Podaj ścieżkę do pliku **klucza prywatnego.**

    ![Okno dialogowe Konfiguracja putty](media/putty-02.png)

9. Kliknij **przycisk Otwórz,** aby uruchomić okno PuTTY. Jeśli się powiedzie, masz połączenie z nową maszyną wirtualną CentOS działającą na platformie Azure.

10. Aby zalogować się jako użytkownik root, wpisz **sudo bash**.

    ![Logowanie użytkownika głównego w oknie polecenia](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Konfigurowanie środowiska i pakietów

Teraz, gdy maszyna wirtualna jest tworzona i jesteś zalogowany, należy wykonać kilka kroków konfiguracji i zainstalować wymagane pakiety preinstalacyjne.

1. Zamapuj nazwę **ofdemo** na lokalny adres IP za`vi /etc/hosts`pomocą vi do edycji pliku hosts ( ). Zakładając, że nasze IP to 192.168.96.148 ofdemo, jest to przed zmianą:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Jest to po zmianie:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Tworzenie grup i użytkowników:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Zmień hasło dla użytkownika oframe7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Zaktualizuj parametry jądra w /etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Dynamiczne odświeżanie parametrów jądra bez ponownego uruchamiania:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Pobierz wymagane pakiety: Upewnij się, że serwer jest połączony z Internetem, pobierz następujące pakiety, a następnie zainstaluj je:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - Ncurses

          > [!NOTE]
          > Po zainstalowaniu pakietu ncurses należy utworzyć następujące łącza symboliczne:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - Gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - Strace
     - ltrace (ltrace)
     - Gdb

7. W przypadku instalacji java rpm wykonaj następujące czynności:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Instalowanie bazy danych Tibero

Tibero udostępnia kilka kluczowych funkcji w środowisku OpenFrame na platformie Azure:

- Tibero jest używany jako wewnętrzny magazyn danych OpenFrame dla różnych funkcji systemowych.
- Pliki VSAM, w tym KSDS, RRDS i ESDS, używają bazy danych Tibero wewnętrznie do przechowywania danych.
- Repozytorium danych TACF jest przechowywane w Tibero.
- Informacje o katalogu OpenFrame są przechowywane w Tibero.
- Baza danych Tibero może służyć jako zamiennik dla IBM Db2 do przechowywania danych aplikacji.

**Aby zainstalować Tibero**

1. Sprawdź, czy plik instalatora binarnego Tibero jest obecny i przejrzyj numer wersji.
2. Skopiuj oprogramowanie Tibero na konto użytkownika Tibero (oframe). Przykład:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Otwórz profil\_.bash`vi .bash_profile`w vi ( ) i wklej w nim następujące elementy:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Aby wykonać profil bash, w wierszu polecenia typu:

    ```
    source .bash_profile
    ```

5. Wygeneruj plik końcówki (plik konfiguracyjny dla Tibero), a następnie otwórz go w vi. Przykład:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Zmodyfikuj \$TB\_HOME/client/config/tbdsn.tbr i umieść 127.0.0.1 zamiastlocalhost, jak pokazano na rysunku:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Utwórz bazę danych. Zostaną wyświetlone następujące dane wyjściowe:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Aby odtworzyć Tibero, najpierw zamknij `tbdown` go za pomocą polecenia. Przykład:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Teraz uruchomić Tibero za pomocą `tbboot`. Przykład:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Aby utworzyć obszar tabel, należy uzyskać dostęp do bazy danych przy użyciu użytkownika SYS (sys/tmax), a następnie utworzyć niezbędną przestrzeń tabel dla woluminu domyślnego i TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Teraz wpisz następujące polecenia SQL:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Uruchom Tibero i sprawdź, czy procesy Tibero są uruchomione:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Dane wyjściowe:

![Wyjście Tibero](media/tibero-01.png)

## <a name="install-odbc"></a>Zainstaluj ODBC

Aplikacje w OpenFrame komunikują się z bazą danych Tibero przy użyciu interfejsu API ODBC dostarczonego przez projekt unixODBC typu open source.

Aby zainstalować ODBC:

1. Sprawdź, czy plik instalatora unixODBC-2.3.4.tar.gz jest `wget unixODBC-2.3.4.tar.gz` obecny, lub użyj polecenia. Przykład:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Rozpaj binarny. Przykład:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Przejdź do katalogu unixODBC-2.3.4 i wygeneruj plik Makefile przy użyciu informacji o komputerze kontrolnym. Przykład:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Domyślnie unixODBC jest zainstalowany w /usr `--prefix` /local, więc przekazuje wartość, aby zmienić lokalizację. Podobnie pliki konfiguracyjne są domyślnie zainstalowane `--sysconfdir` w /etc, więc przekazuje wartość żądanej lokalizacji.

4. Wykonaj Plik Makefile:`[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Skopiuj plik wykonywalny w katalogu programu po kompilacji. Przykład:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Użyj vi, aby edytować profil bash (`vi ~/.bash_profile`) i dodać następujące elementy:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Zastosuj ODBC. Odpowiednio edytuj następujące pliki. Przykład:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Utwórz dowiązanie symboliczne i sprawdź poprawność połączenia bazy danych Tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Wyświetlane jest następujące wyjście:

![Wyjście ODBC pokazujące połączenie z SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Instalowanie bazy OpenFrame

Podstawowy serwer aplikacji jest instalowany przed poszczególnych usług, które OpenFrame używa do zarządzania systemem na platformie Azure, w tym procesów serwera obsługi transakcji.

**Aby zainstalować OpenFrame Base**

1. Upewnij się, że instalacja Tibero powiodła\_się,\_a\_\_następnie sprawdź, czy są obecne następujące pliki instalacyjne OpenFrame Base7 0 Linux x86\_64.bin i plik konfiguracyjny base.properties.

2. Zaktualizuj profil basha o następujące informacje specyficzne dla Tibero:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Wykonaj profil bash:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Upewnij się, że procesy Tibero są uruchomione. Przykład:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Base](media/base-01.png)

     > [!IMPORTANT]
     > Upewnij się, że uruchomisz Tibero przed instalacją.

5. Generowanie licencji w [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) i umieszczanie licencji OpenFrame Base, Batch, TACF, OSC w odpowiednim folderze:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Pobierz pliki binarne OpenFrame Base i base.properties:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Wykonaj instalator przy użyciu pliku base.properties. Przykład:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Po zakończeniu, komunikat Zakończenie instalacji jest diplayed.

8. Sprawdź strukturę katalogu OpenFrame `ls -ltr` Base za pomocą polecenia. Przykład:

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. Rozpocznij openframe base:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![wyjście polecenia tmboot](media/base-02.png)

10. Sprawdź, czy stan procesu jest gotowy za pomocą polecenia tmadmin w si. RDY jest wyświetlany w kolumnie **stanu** dla każdego z procesów:

     ![wyjście polecenia tmadmin](media/base-03.png)

11. Zamknij openframe base:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>Instalowanie partii OpenFrame

Usługa OpenFrame Batch składa się z kilku składników, które symulują środowiska wsadowe dla komputerów mainframe i są używane do uruchamiania zadań wsadowych na platformie Azure.

**Aby zainstalować usługę Batch**

1. Upewnij się, że instalacja podstawowa powiodła się,\_a\_następnie\_\_sprawdź, czy są obecne pliki konfiguracji OpenFrame\_\_Batch7\_0 Fix2 MVS Linux x86 64.bin i plik konfiguracyjny batch.properties:

2. W wierszu polecenia `vi batch.properties` wpisz, aby edytować plik batch.properties przy użyciu vi.

3. Zmodyfikuj parametry w następujący sposób:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Aby wykonać instalatora wsadowego, w wierszu polecenia typu:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Po zakończeniu instalacji uruchom zainstalowane pakiety OpenFrame, wpisując `tmboot` polecenie w wierszu polecenia.

    ![wyjście tmboot](media/tmboot-01.png)

6. Wpisz `tmadmin` w wierszu polecenia, aby sprawdzić proces OpenFrame.

    ![Ekran administratora programu Tmax](media/tmadmin-01.png)

7. Wykonaj następujące polecenia:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Użyj `tmdown` polecenia, aby uruchomić i zamknąć partię:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>Instalowanie TACF

TACF Manager to moduł usługi OpenFrame, który kontroluje dostęp użytkowników do systemów i zasobów za pomocą zabezpieczeń RACF.

**Aby zainstalować TACF**

1. Sprawdź, czy\_są obecne\_pliki\_konfiguracji\_OpenFrame Tacf7 0 Fix2 Linux\_x86\_64.bin i plik konfiguracyjny tacf.properties.
2. Upewnij się, że instalacja wsadowa powiodła się, a`vi tacf.properties`następnie użyj vi, aby otworzyć plik tacf.properties ( ).
3. Zmodyfikuj parametry TACF:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Po zakończeniu instalacji TACF zastosuj zmienne środowiskowe TACF. W wierszu polecenia wpisz polecenie:

     ```
     source \~/.bash\_profile
     ```

5. Wykonaj instalator TACF. W wierszu polecenia wpisz polecenie:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     Dane wyjściowe wyglądają mniej więcej tak:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. W wierszu polecenia `tmboot` wpisz, aby ponownie uruchomić openframe. Dane wyjściowe wyglądają mniej więcej tak:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Sprawdź, czy stan procesu `tmadmin` jest `si` gotowy za pomocą polecenia. Przykład:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     W kolumnie **stanu** pojawi się rdy:

    ![RDY w kolumnie stanu](media/tmboot-02.png)

8. Wykonaj następujące polecenia:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Zamknij serwer za `tmdown` pomocą polecenia. Dane wyjściowe wyglądają mniej więcej tak:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>Instalowanie prosort

ProSort jest narzędziem używanym w transakcjach wsadowych do sortowania danych.

**Aby zainstalować ProSort**

1. Upewnij się, że instalacja batch zakończyła się pomyślnie, a następnie sprawdź, czy plik instalatora **prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz** jest obecny.

2. Wykonaj instalator przy użyciu pliku właściwości. W wierszu polecenia wpisz polecenie:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Przenieś katalog prosort do lokalizacji głównej. W wierszu polecenia wpisz polecenie:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Utwórz podkatalog licencji i skopiuj tam plik licencji. Przykład:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Otwórz bash.profile w`vi .bash_profile`vi ( ) i zaktualizuj go w następujący sposób:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Aby wykonać profil bash, w wierszu polecenia wpisz:`. .bash_profile`

7. Utwórz plik konfiguracyjny. Przykład:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Utwórz dowiązanie symboliczne. Przykład:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Sprawdź instalację ProSort, `prosort -h` wykonując polecenie. Przykład:

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>Zainstaluj OFCOBOL

OFCOBOL to kompilator OpenFrame, który interpretuje programy COBOL komputera mainframe. 

**Aby zainstalować OFCOBOL**

1. Upewnij się, że instalacja w trybie batch/online powiodła się, a\_następnie\_sprawdź, czy plik instalatora OpenFrame\_COBOL3\_0\_40\_Linux x86 64.bin jest obecny.

2. Aby wykonać instalator OFCOBOL, w wierszu polecenia wpisz:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Przeczytaj umowę licencyjną i naciśnij klawisz Enter, aby kontynuować.

4. Zaakceptuj umowę licencyjną. Po zakończeniu instalacji pojawią się następujące informacje:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Otwórz profil bash w`vi .bash_profile`vi ( ) i sprawdź, czy jest on zaktualizowany za pomocą zmiennych OFCOBOL.
6. Wykonaj profil bash. W wierszu polecenia wpisz polecenie:

     ```
      source ~/.bash_profile
     ```

7. Skopiuj licencję OFCOBOL do zainstalowanego folderu. Przykład:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Przejdź do pliku konfiguracyjnego OpenFrame tjclrun.conf i otwórz go w vi. Przykład:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Oto sekcja SYSLIB przed zmianą:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Oto sekcja SYSLIB po zmianie:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Przejrzyj\_plik\_OpenFrame COBOL InstallLog.log w vi i sprawdź, czy nie ma żadnych błędów. Przykład:
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Użyj `ofcob --version` polecenia i przejrzyj numer wersji, aby zweryfikować instalację. Przykład:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Uruchom ponownie openframe za pomocą `tmdown/tmboot` polecenia.

## <a name="install-ofasm"></a>Zainstaluj OFASM

OFASM to kompilator OpenFrame, który interpretuje programy asemblera mainframe.

**Aby zainstalować OFASM**

1. Upewnij się, że instalacja w trybie batch/online powiodła się, a następnie sprawdź, czy plik instalatora\_OpenFrame ASM3\_0\_Linux\_x86\_64.bin jest obecny.

2. Wykonaj instalatora. Przykład:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Przeczytaj umowę licencyjną i naciśnij klawisz Enter, aby kontynuować.
4. Zaakceptuj umowę licencyjną.
5. Sprawdź, czy profil bash jest aktualizowany za pomocą zmiennych OFASM. Przykład:

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Otwórz plik konfiguracyjny OpenFrame tjclrun.conf w vi i edytuj go w następujący sposób:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Oto sekcja [SYSLIB] *przed* zmianą:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Oto sekcja [SYSLIB] *po* zmianie:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Otwórz plik\_OpenFrame\_ASM InstallLog.log w vi i sprawdź, czy nie ma żadnych błędów. Przykład:

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Uruchom ponownie openframe, wydając jedno z następujących poleceń:

     ```
     tmdown / tmboot
     ```

     —lub—

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Instalowanie OSC

OSC to środowisko OpenFrame podobne do IBM CICS, które obsługuje szybkie transakcje OLTP i inne funkcje zarządzania.

**Aby zainstalować OSC**

1. Upewnij się, że instalacja podstawowa powiodła się, a następnie sprawdź, czy są obecne pliki instalatora OpenFrame\_\_OSC7\_0 Fix2\_Linux\_x86\_64.bin i plik konfiguracyjny osc.properties.
2. Edytuj następujące parametry w pliku osc.properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Wykonaj instalator przy użyciu pliku właściwości, jak pokazano na rysunku:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Po zakończeniu zostanie wyświetlony komunikat "Instalacja zakończona".

4. Sprawdź, czy profil bash jest aktualizowany za pomocą zmiennych OSC.
5. Przejrzyj\_plik OpenFrame OSC7\_0\_\_InstallLog.log. Powinno to wyglądać następująco:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Użyj vi, aby otworzyć plik konfiguracyjny ofsys.seq. Przykład:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. W \#sekcjach \#BASE i BATCH edytuj parametry w sposób pokazany na rysunku.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Skopiuj plik licencji. Przykład:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Aby uruchomić i zamknąć OSC, zaizwualizuj `osctdlinit OSCOIVP1` pamięć współużytkującą regionu CICS, wpisując w wierszu polecenia.

10. Uruchom, `oscboot` aby uruchomić OSC. Dane wyjściowe wyglądają mniej więcej tak:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. Aby sprawdzić, czy stan procesu `tmadmin` jest gotowy, użyj polecenia w si. Wszystkie procesy powinny być wyświetlane RDY w kolumnie **stanu.**

    ![Procesy wyświetlania rdy](media/tmadmin-02.png)

12. Zamknij OSC za `oscdown` pomocą polecenia.

## <a name="install-jeus"></a>Zainstaluj JEUS

JEUS (Java Enterprise User Solution) udostępnia warstwę prezentacji serwera aplikacji sieci Web OpenFrame.

Przed zainstalowaniem jeus, należy zainstalować pakiet Apache Ant, który zapewnia biblioteki i narzędzia wiersza polecenia potrzebne do zainstalowania JEUS.

**Aby zainstalować Apache Ant**

1. Pobierz plik binarny Ant za `wget` pomocą polecenia. Przykład:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Użyj `tar` narzędzia, aby wyodrębnić plik binarny i przenieść go do odpowiedniej lokalizacji. Przykład:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Aby zwiększyć wydajność, utwórz dowiązanie symboliczne:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Otwórz profil bash w`vi .bash_profile`vi ( )i zaktualizuj go następującymi zmiennymi:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Zastosuj zmodyfikowaną zmienną środowiskową. Przykład:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Aby zainstalować JEUS**

1. Rozwiń instalator za `tar` pomocą narzędzia. Przykład:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Utwórz folder **jeus** (`mkdir jeus7`) i rozpatrzyj plik binarny.
3. Zmień katalog **konfiguracji** (lub użyj parametru JEUS dla własnego środowiska). Przykład:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Wykonaj `ant clean-all` przed wykonaniem kompilacji. Dane wyjściowe wyglądają mniej więcej tak:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Utwórz kopię zapasową pliku domain-config-template.properties. Przykład:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Otwórz plik domain-config-template.properties w vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Zmień `jeus.password=jeusadmin nodename=Tmaxsoft` na`jeus.password=tmax1234 nodename=ofdemo`

8. Wykonaj `ant install` polecenie, aby zbudować JEUS.
9.  Zaktualizuj plik profilu .bash\_za pomocą zmiennych JEUS, jak pokazano na rysunku:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Wykonaj profil bash. Przykład:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Opcjonalnie*. Utwórz alias dla łatwego zamykania i uruchamiania komponentów JEUS:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Aby zweryfikować instalację, uruchom serwer administratora domeny w sposób pokazany:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Sprawdź przez logowanie internetowe przy użyciu składni:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Na przykład <http://192.168.92.133:9736/webadmin/login.> zostanie wyświetlony ekran logowania:
    
     ![Ekran logowania jeus WebAdmin](media/jeus-01.png)

     > [!NOTE]
     > Jeśli wystąpią jakiekolwiek problemy z zabezpieczeniami portów, otwórz port`systemctl stop firewall`9736 lub wyłącz zaporę ( ).

14. Aby zmienić nazwę hosta serwera1, kliknij przycisk **Zablokuj & Edytuj,** a następnie kliknij pozycję **Server1**. W oknie Serwer zmień nazwy hosta w następujący sposób:

    1.  Zmień **nazwa węzła** **na ofdemo**.
    2.  Kliknij **przycisk OK** po prawej stronie okna.
    3.  Kliknij **przycisk Zastosuj zmiany** w lewym dolnym dolnej części okna i opis wpisz zmiana nazwy *hosta*.

    ![Ekran Jeus WebAdmin](media/jeus-02.png)

15. Sprawdź, czy konfiguracja jest pomyślna na ekranie potwierdzenia.

    ![Ekran jeus_domain serwera](media/jeus-03.png)

16. Uruchom proces serwera zarządzanego "server1" za pomocą następującego polecenia:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Zainstaluj OFGW

OFGW Jest bramą OpenFrame, która obsługuje komunikację między emulatorem terminala 3270 a bazą OSI i zarządza sesjami między emulatorem terminala a OSI.

**Aby zainstalować OFGW**

1. Upewnij się, że jeus został pomyślnie zainstalowany,\_a\_następnie\_sprawdź, czy plik instalatora OFGW7 0 1 Generic.bin jest obecny.
2. Wykonaj instalatora. Przykład:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Użyj następujących lokalizacji dla odpowiednich monitów:
     -   Katalog JEUS Home
     -   Nazwa domeny JEUS
     -   Nazwa serwera JEUS
     -   Kierowca Tibero
     -   Identyfikator węzła Tmax ofdemo

4. Zaakceptuj pozostałe wartości domyślne, a następnie naciśnij klawisz Enter, aby zamknąć instalator.

5. Sprawdź, czy adres URL OFGW działa zgodnie z oczekiwaniami:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Zostanie wyświetlony następujący ekran:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Zainstaluj OFManager

OFManager zapewnia funkcje obsługi i zarządzania dla OpenFrame w środowisku internetowym.

**Aby zainstalować OFManager**

1. Sprawdź, czy plik\_instalatora OFManager7 Generic.bin jest obecny.
2. Wykonaj instalatora. Przykład:

     ```
     OFManager7_Generic.bin
     ```

3.  Naciśnij klawisz Enter, aby kontynuować, a następnie zaakceptuj umowę licencyjną.
4.  Wybierz folder instalacyjny.
5.  Zaakceptuj wartości domyślne.
6.  Wybierz Tibero jako bazę danych.
7.  Naciśnij klawisz Enter, aby wyjść z instalatora.
8.  Sprawdź, czy adres URL ofmanager działa zgodnie z oczekiwaniami:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Zostanie wyświetlony ekran startowy:

![Ekran logowania programu Tmax OpenFrame Manager](media/ofmanager-01.png)

To kończy instalację składników OpenFrame.

## <a name="next-steps"></a>Następne kroki

Jeśli rozważasz migrację mainframe, nasz rozwijający się ekosystem partnerów jest dostępny, aby Ci pomóc. Szczegółowe wskazówki dotyczące wyboru rozwiązania partnerskiego można znaleźć w [platformie Modernizacyjny.](https://datamigration.microsoft.com/)

-   [Rozpoczynanie pracy z platformą Azure](https://docs.microsoft.com/azure/)
-   [Dokumentacja serwera integracji hostów (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Przewodnik dotyczący przenoszenia i zmiany biegów wirtualnego centrum danych platformy Azure](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
