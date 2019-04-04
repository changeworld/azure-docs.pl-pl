---
title: Zainstaluj TmaxSoft OpenFrame na maszynach wirtualnych platformy Azure
description: Ponowne hostowanie obciążeń mainframe firmy IBM z/OS przy użyciu środowiska TmaxSoft OpenFrame w usłudze Azure Virtual Machines (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 6b109f347ee7a917b57acfc56ab4418755295bc5
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896518"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Instalowanie TmaxSoft OpenFrame na platformie Azure

Dowiedz się, jak skonfigurować środowisko OpenFrame na platformie Azure, nadające się do rozwoju, pokazów, testów lub obciążeń produkcyjnych. Ten samouczek przeprowadzi Cię przez każdego kroku.

OpenFrame zawiera wiele składników, tworzonych w środowisku emulacji mainframe na platformie Azure. Na przykład usług online OpenFrame Zastąp mainframe oprogramowanie pośredniczące takie jak IBM klienta informacji kontroli systemu (CICS), a OpenFrame Batch z jego składników TJES zastępuje mainframe firmy IBM zadania wpis podsystemu (JES).

OpenFrame współpracuje z dowolnym relacyjnej bazy danych, w tym Oracle Database, programu Microsoft SQL Server, IBM Db2 i MySQL. Ta instalacja OpenFrame używa TmaxSoft Tibero relacyjnej bazy danych. OpenFrame i Tibero uruchomić w systemie operacyjnym Linux. W tym samouczku instaluje CentOS 7.3, chociaż można używać innych obsługiwanych dystrybucjach systemu Linux. Serwer aplikacji OpenFrame i Tibero bazy danych są instalowane na jednej maszyny wirtualnej (VM).

Samouczek przeprowadzi Cię przez instalację składników pakietu OpenFrame. Niektóre muszą być zainstalowane oddzielnie.

OpenFrame główne składniki:

- Wymagane pakiety instalacyjne.
- Tibero bazy danych.
- Open Database Connectivity (ODBC) jest używany przez aplikacje w OpenFrame komunikację z bazą danych Tibero.
- Podstawa OpenFrame, oprogramowanie pośredniczące, która zarządza całego systemu.
- OpenFrame Batch, rozwiązanie, które zastępuje dużych systemach komputerowych usługi batch.
- TACF, moduł usługi, która kontroluje dostęp użytkowników do systemów i zasobów.
- ProSort, narzędzie sortowania dla partii transakcji.
- OFCOBOL, kompilator, który interpretuje mainframe COBOL programów.
- OFASM, kompilatora, który interpretuje mainframe asemblera programy.
- OpenFrame serwera typu C (OSC), rozwiązanie, które zastępuje mainframe oprogramowanie pośredniczące i CICS firmy IBM.
- Java Enterprise użytkownika rozwiązania (JEUS), serwer aplikacji sieci web, który posiada certyfikat dla oprogramowania Java Enterprise Edition 6.
- OFGW, składnik bramy OpenFrame, który zawiera odbiornik 3270.
- OFManager, rozwiązanie, które firmy OpenFrame operacji i funkcji zarządzania w środowisku sieci web.

Inne wymagane składniki OpenFrame:

- OSI, rozwiązanie, które zastępuje oprogramowanie pośredniczące mainframe i ISP kontrolera domeny.
- TJES, rozwiązanie, które oferują środowisko JES mainframe.
- OFTSAM, rozwiązanie, które umożliwia pliki SAM (V) do użycia w systemie open.
- OFHiDB, rozwiązanie, które zastępuje mainframe firmy ISP bazy danych.
- OFPLI, kompilator interpretuje słowa kluczowe mainframe firmy PL / I programy.
- PROTRIEVE, to rozwiązanie, które wykonuje języka mainframe Easytrieve urzędu certyfikacji.
- OFMiner, to rozwiązanie, które analizuje zasoby mainframe i przeprowadza ich migrację do platformy Azure.

## <a name="architecture"></a>Architektura

Poniższa ilustracja zawiera omówienie składników architektury OpenFrame 7.0 zainstalowane w ramach tego samouczka:

![Składniki OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Wymagania dotyczące systemu Azure

Poniższa lista zawiera wymagania dotyczące instalacji na platformie Azure.
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
Red Hat 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Sprzęt
</td>
<td>Liczba rdzeni: 2 (minimum)<br/>
Pamięć: 4 GB (minimum)<br/>
Obszar wymiany: 1 GB (minimum)<br/>
Dysk twardy: 100 GB (minimum)<br/>
</td>
</tr>
<tr><td>Opcjonalne oprogramowania dla użytkowników Windows
</td>
<td>PuTTY: Używane w tym przewodniku, aby skonfigurować funkcje maszyn wirtualnych<br/>
WinSCP: Popularne SFTP klienta i klienta FTP można użyć<br/>
Eclipse dla Windows: Platforma programistyczna obsługiwane przez TmaxSoft<br/>
(Program Microsoft Visual Studio nie jest obsługiwany w tej chwili)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Wymagania wstępne

Należy zaplanować na wystawie przez kilka dni, kompletowany jest wymagane oprogramowanie i wszystkie procesy ręczne.

Przed rozpoczęciem pracy, wykonaj następujące czynności:

- Uzyskiwanie nośnika instalacyjnego OpenFrame z TmaxSoft. Jeśli jesteś istniejącym klientem TmaxSoft, skontaktuj się z przedstawicielem TmaxSoft licencjonowanej kopii. W przeciwnym razie żądania z wersji próbnej [TmaxSoft](http://www.tmaxsoft.com/contact/).

- Żądanie dokumentacji OpenFrame, wysyłając wiadomość e-mail do <support@tmaxsoft.com>.

- Uzyskaj subskrypcję systemu Azure, jeśli nie masz jeszcze jeden. Można również utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed przystąpieniem do wykonywania.

- Opcjonalny. Konfigurowanie tunelu sieci VPN typu lokacja lokacja lub serwera przesiadkowego, która ogranicza dostęp do maszyny Wirtualnej platformy Azure dla dozwolonych użytkowników w Twojej organizacji. Ten krok nie jest wymagana, ale jest najlepszym rozwiązaniem.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Konfigurowanie maszyny Wirtualnej na platformie Azure dla OpenFrame i Tibero

Możesz skonfigurować środowisko OpenFrame przy użyciu różnych wzorców wdrożenia, ale Poniższa procedura pokazuje, jak wdrożyć serwer aplikacji OpenFrame i Tibero bazy danych na jednej maszynie Wirtualnej. W dużych środowisk i duże obciążenia najlepszym rozwiązaniem jest wdrożenie bazy danych oddzielnie na własnej maszyny Wirtualnej w celu zapewnienia lepszej wydajności.

**Tworzenie maszyny Wirtualnej**

1. Przejdź do witryny Azure portal pod <http://portal.azure.com> i zaloguj się do swojego konta.

2. Kliknij przycisk **maszyn wirtualnych**.

    ![Lista zasobów w witrynie Azure portal](media/vm-01.png)

3. Kliknij pozycję **Add** (Dodaj).

    ![Dodaj opcję w witrynie Azure portal](media/vm-02.png)

4. Po prawej stronie **systemów operacyjnych**, kliknij przycisk **więcej**.

     ![Więcej opcji w witrynie Azure portal](media/vm-03.png)

5. Kliknij przycisk **opartych na systemie CentOS 7.3** z tego przewodnika, lub wybrać inny obsługiwanych dystrybucji systemu Linux.

     ![Opcje systemu operacyjnego w witrynie Azure portal](media/vm-04.png)

6. W **podstawy** ustawień, wprowadź **nazwa**, **nazwa_użytkownika**, **typ uwierzytelniania**, **subskrypcji** (Płatność za rzeczywiste użycie jest styl AWS płatności), a **grupy zasobów** (istniejącą lub Utwórz grupę TmaxSoft).

7. Po zakończeniu (łącznie z pary kluczy publiczny/prywatny dla **typ uwierzytelniania**), kliknij przycisk **przesyłania**.

> [!NOTE]
> Jeśli przy użyciu klucza publicznego SSH dla **typ uwierzytelniania**, zobacz kroki w następnej sekcji, aby wygenerować parę kluczy publiczny/prywatny, a następnie Wznów opisane w tym miejscu.

### <a name="generate-a-publicprivate-key-pair"></a>Wygeneruj parę kluczy publiczny/prywatny

Jeśli używasz systemu operacyjnego Windows, potrzebujesz PuTTYgen można wygenerować pary kluczy publiczny/prywatny.

Klucz publiczny może być swobodnie udostępniany, ale klucz prywatny powinien być trzymane w tajemnicy całkowicie i nigdy nie powinny być udostępniane innej strony. Po wygenerowaniu kluczy, możesz wkleić **klucz publiczny SSH** do konfiguracji — w efekcie przekazać go do maszyny Wirtualnej systemu Linux. Jest on przechowywany autoryzacji wewnątrz\_klucze w ramach \~/.ssh katalogu katalog macierzysty dla konta użytkownika. Maszyny Wirtualnej systemu Linux będzie mogła rozpoznać i sprawdzanie poprawności połączenia po podaniu skojarzonego **prywatny klucz SSH** klienta SSH (w naszym przypadku PuTTY).

Podczas wyświetlania nowych osobom dostęp do maszyny Wirtualnej: 

- Poszczególnym nowe generuje własnych kluczy publiczny/prywatny, przy użyciu narzędzia PuTTYgen.
- Osoby przechowują swoje własne klucze prywatne oddzielnie i wysyłać informacje o kluczu publicznym do administratora maszyny wirtualnej.
- Administrator Wkleja zawartość klucza publicznego do \~/.ssh/authorized\_pliku kluczy.
- Nowe osoby łączy się za pomocą programu PuTTY.

**Aby wygenerować parę kluczy publiczny/prywatny**

1.  Pobierz program PuTTYgen z <https://www.putty.org/> i zainstaluj go przy użyciu domyślnych ustawień.

2.  Aby otworzyć program PuTTYgen, znajdź katalog instalacyjny programu PuTTY w C:\\Program Files\\programu PuTTY.

    ![Interfejs programu puTTY](media/puttygen-01.png)

3.  Kliknij przycisk **Generowanie**.

    ![Generator kluczy puTTY, okno dialogowe](media/puttygen-02.png)

4.  Po generacji należy zapisać klucz publiczny i klucz prywatny. Wklej zawartość klucza publicznego w **klucz publiczny SSH** części **Utwórz maszynę wirtualną \> podstawy** okienko (pokazane w kroku 6 i 7 w poprzedniej sekcji).

    ![Generator kluczy puTTY, okno dialogowe](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Konfigurowanie funkcji maszyny Wirtualnej

1. W witrynie Azure portal w **wybierz rozmiar** bloku wybierz ma ustawienia sprzętu systemu Linux maszyny. *Minimalne* wymagania dotyczące instalowania Tibero i OpenFrame są 2 procesory CPU i 4 GB pamięci RAM, jak pokazano w tym przykładzie instalacji:

    ![Tworzenie maszyny wirtualnej — podstawy](media/create-vm-01.png)

2. Kliknij przycisk **3 ustawienia** i użyj ustawień domyślnych w celu skonfigurowania funkcji opcjonalnych.
3. Sprawdź informacje o płatności.

    ![Tworzenie maszyny wirtualnej — zakupu](media/create-vm-02.png)

4. Przedstawia opcje. Azure rozpoczyna się wdrożyć maszynę Wirtualną. Ten proces zwykle trwa kilka minut.

5. Po wdrożeniu maszyny Wirtualnej zostanie wyświetlona jego pulpit nawigacyjny, przedstawiający wszystkie ustawienia, które zostały wybrane podczas konfiguracji. Zwróć uwagę na **publiczny adres IP**.

    ![Tmax na pulpicie nawigacyjnym platformy Azure](media/create-vm-03.png)

6. Otwórz program PuTTY.

7. Dla **nazwy hosta**, wpisz nazwę użytkownika i publiczny adres IP zostanie skopiowany. Na przykład **username\@publicip**.

    ![Okno dialogowe Konfiguracja programu puTTY](media/putty-01.png)

8. W **kategorii** kliknij **połączenia \> SSH \> uwierzytelniania**. Podaj ścieżkę do Twojej **klucza prywatnego** pliku.

    ![Okno dialogowe Konfiguracja programu puTTY](media/putty-02.png)

9. Kliknij przycisk **Otwórz** Aby uruchomić okno programu PuTTY. Jeśli to się powiedzie, są połączone do nowej maszyny Wirtualnej CentOS na platformie Azure.

10. Aby zalogować się jako użytkownik root, wpisz **bash "sudo"**.

    ![Logowanie użytkownika głównego w oknie polecenia](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Konfigurowanie środowiska i pakietów

Teraz, gdy maszyna wirtualna jest tworzona i użytkownik jest zalogowany, należy wykonać kilka kroków instalacji i zainstaluj wymagane pakiety przed instalacją.

1. Mapowanie nazwy **ofdemo** do lokalnego adresu IP przy użyciu serwera vi do edytowania pliku hosts (`vi /etc/hosts`). Zakładając, że IP jest 192.168.96.148 ofdemo, to przed zmianą:

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

3. Zmień hasło użytkownika oframe7:

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

5. Odśwież parametry jądra dynamicznie bez ponownego uruchomienia:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Pobierz wymagane pakiety: Upewnij się, że serwer jest połączony z Internetem, pobierz następujące pakiety, a następnie zainstaluj je:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - ncurses

          > [!NOTE]
          > Po zainstalowaniu pakietu ncurses, należy utworzyć następujące linki symboliczne:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - strace
     - ltrace
     - gdb

7. W przypadku instalacji Java RPM wykonaj następujące czynności:

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

Tibero zawiera kilka kluczowych funkcji, w środowisku OpenFrame na platformie Azure:

- Tibero służy do przechowywania danych wewnętrznych OpenFrame dla różnych funkcji systemu.
- Pliki VSAM, w tym KSDS RRDS i ESDS, korzystanie z bazy danych Tibero do przechowywania danych.
- Repozytorium danych TACF są przechowywane w Tibero.
- Informacje katalogu OpenFrame są przechowywane w Tibero.
- Baza danych Tibero może służyć jako zamiennika IBM Db2 do przechowywania danych aplikacji.

**Aby zainstalować Tibero**

1. Sprawdź, czy plik binarny Instalatora Tibero jest obecny i sprawdź numer wersji.
2. Skopiuj oprogramowania Tibero Tibero konta użytkownika (oframe). Na przykład:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Otwórz .bash\_profilu w vi (`vi .bash_profile`) i wklej następujący kod w nim:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Aby wykonać profilu powłoki bash, wpisz w wierszu polecenia:

    ```
    source .bash_profile
    ```

5. Generowanie pliku tip (plik konfiguracji dla Tibero), a następnie otwórz go w vi. Na przykład:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Modyfikowanie \$TB\_HOME/client/config/tbdsn.tbr i zamiast tego umieść 127.0.0.1 oflocalhost, jak pokazano:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Tworzenie bazy danych. Zostanie wyświetlone następujące dane wyjściowe:

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

8. Aby odzyskać Tibero, najpierw zamknij go przy użyciu `tbdown` polecenia. Na przykład:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Teraz uruchomić za pomocą Tibero `tbboot`. Na przykład:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Aby utworzyć obszar tabel, dostęp do bazy danych, używając SYS użytkownika (sys/tmax), następnie utworzyć wymaganych tabel domyślnym woluminem i TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Wpisz poniższe polecenia SQL:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Rozruch Tibero, a następnie sprawdź, czy są uruchomione procesy Tibero:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Dane wyjściowe:

![Dane wyjściowe Tibero](media/tibero-01.png)

## <a name="install-odbc"></a>Instalowanie ODBC

Aplikacje w OpenFrame komunikować się z bazą danych Tibero przy użyciu interfejsu API ODBC, dostarczone przez projekt typu open-source unixODBC.

Aby zainstalować ODBC:

1. Sprawdź obecność pliku Instalatora unixODBC 2.3.4.tar.gz lub użyj `wget unixODBC-2.3.4.tar.gz` polecenia. Na przykład:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Rozpakuj plik binarny. Na przykład:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Przejdź do katalogu unixODBC 2.3.4 i wygenerowania pliku reguł programu make przy użyciu sprawdzanie, czy informacje o maszynie. Na przykład:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Domyślnie unixODBC jest zainstalowany w/usr/local, więc `--prefix` przekazuje wartość, aby zmienić lokalizację. Podobnie, pliki konfiguracji są zainstalowane w etc domyślnie, więc `--sysconfdir` przekazuje wartość żądanej lokalizacji.

4. Wykonaj pliku reguł programu make: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Skopiuj plik wykonywalny w katalogu program po kompilacji. Na przykład:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Użyj vi, aby dokonać edycji profilu powłoki bash (`vi ~/.bash_profile`) i Dodaj następujący kod:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Zastosuj ODBC. W związku z tym edytować następujących plików. Na przykład:

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

8. Utwórz link symboliczny i sprawdzanie poprawności połączenia z bazą danych Tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Są wyświetlane następujące dane wyjściowe:

![Dane wyjściowe ODBC przedstawiający podłączonych do bazy danych SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>OpenFrame baza instalacji

Serwer podstawowy aplikacji została zainstalowana przed poszczególnych usług, których OpenFrame używa do zarządzania na platformie Azure, w tym transakcji, obsługi procesów serwera.

**Aby zainstalować podstawowy OpenFrame**

1. Upewnić się, że instalacja Tibero zakończyła się pomyślnie, a następnie upewnij się, że następujące OpenFrame\_Base7\_0\_Linux\_x86\_pliku Instalatora bin 64. i pliku konfiguracji base.properties są obecne.

2. Aktualizowanie profilu powłoki bash, używając następujących informacji specyficznych dla Tibero:

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

3. Wykonaj profilu powłoki bash:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Upewnij się, że są uruchomione procesy Tibero. Na przykład:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Podstawowa](media/base-01.png)

     > [!IMPORTANT]
     > Upewnij się, że uruchomieniu Tibero przed rozpoczęciem instalacji.

5. Generowanie licencji na [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) i PRZEŁĄCZYĆ bazę OpenFrame, Batch, TACF, OSC licencji w odpowiednim folderze:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Pobieranie plików binarnych i base.properties OpenFrame Base:

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

7. Wykonaj Instalatora przy użyciu pliku base.properties. Na przykład:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Po zakończeniu instalacji pełny komunikat jest wyświetlonej.

8. Zweryfikować OpenFrame Base katalogu struktury przy użyciu `ls -ltr` polecenia. Na przykład:

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

9. Podstawowa OpenFrame rozpoczęcia:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![dane wyjściowe polecenia tmboot](media/base-02.png)

10. Sprawdź, czy stan procesu jest gotowy, za pomocą polecenia tmadmin w si. RDY jest wyświetlany w **stan** kolumnę dla każdego procesu:

     ![dane wyjściowe polecenia tmadmin](media/base-03.png)

11. Zamknij OpenFrame Base:

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

## <a name="install-openframe-batch"></a>Zainstaluj OpenFrame usługi Batch

OpenFrame Batch składa się kilka składników, które symulowania środowisk przetwarzania wsadowego mainframe i służy do uruchamiania zadań wsadowych na platformie Azure.

**Aby zainstalować usługi Batch**

1. Upewnij się, że podstawowa instalacja zakończyła się pomyślnie, a następnie upewnij się, że OpenFrame\_Batch7\_0\_Fix2\_MVS\_Linux\_x86\_pliku Instalatora bin 64. i plik konfiguracji Batch.Properties znajdują się:

2. W wierszu polecenia wpisz `vi batch.properties` do edycji plik batch.properties przy użyciu serwera vi.

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

4. Aby uruchomić Instalatora usługi batch, wpisz w wierszu polecenia:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Po zakończeniu instalacji uruchom zainstalowanych zestawów OpenFrame, wpisując `tmboot` w wierszu polecenia.

    ![dane wyjściowe tmboot](media/tmboot-01.png)

6. Typ `tmadmin` w wierszu polecenia, aby sprawdzić procesu OpenFrame.

    ![Ekran Tmax administratora](media/tmadmin-01.png)

7. Wykonaj następujące polecenia:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Użyj `tmdown` polecenie Uruchom i zamknij usługi Batch:

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

## <a name="install-tacf"></a>Zainstaluj TACF

Menedżer TACF jest moduł usługi OpenFrame, która kontroluje dostęp użytkownika do systemów i zasobów za pomocą zabezpieczeń RACF.

**Aby zainstalować TACF**

1. Upewnij się, że OpenFrame\_Tacf7\_0\_Fix2\_Linux\_x86\_pliku Instalatora bin 64. i pliku konfiguracji tacf.properties są obecne.
2. Upewnij się, że instalacja przetwarzania wsadowego zakończyła się pomyślnie, a następnie otwórz plik tacf.properties za pomocą vi (`vi tacf.properties`).
3. Modyfikowanie parametrów TACF:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Po zakończeniu pracy Instalatora TACF, zastosuj TACF zmiennych środowiskowych. W wierszu polecenia wpisz polecenie:

     ```
     source \~/.bash\_profile
     ```

5. Należy uruchomić Instalatora TACF. W wierszu polecenia wpisz polecenie:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     Dane wyjściowe wyglądają następująco:

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

6. W wierszu polecenia wpisz `tmboot` ponownego uruchomienia OpenFrame. Dane wyjściowe wyglądają następująco:

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

7. Sprawdź, czy stan procesu jest gotowy, za pomocą `tmadmin` w `si` polecenia. Na przykład:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     W **stan** pojawia się RDY kolumny:

    ![RDY w kolumnie Stan](media/tmboot-02.png)

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

9. Zamknij serwer przy użyciu `tmdown` polecenia. Dane wyjściowe wyglądają następująco:

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

## <a name="install-prosort"></a>Zainstaluj ProSort

ProSort to narzędzie używane w partie transakcji składające się na potrzeby sortowania danych.

**Aby zainstalować ProSort**

1. Upewnij się, że instalacja przetwarzania wsadowego zakończyła się pomyślnie, a następnie sprawdź, czy **prosort bin-prosort\_2sp3-linux64-2123-opt.tar.gz** plik Instalatora jest obecny.

2. Wykonaj Instalatora za pomocą pliku właściwości. W wierszu polecenia wpisz polecenie:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Przenieś prosort katalog macierzysty lokalizacji. W wierszu polecenia wpisz polecenie:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Tworzenie podkatalogu licencji, a następnie skopiuj plik licencji ma. Na przykład:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Otwórz bash.profile w vi (`vi .bash_profile`) i zaktualizować go w następujący sposób:

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

6. Aby wykonać profilu powłoki bash, w wierszu polecenia wpisz: ` . .bash_profile`

7. Utwórz plik konfiguracji. Na przykład:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Utwórz łącze symboliczne. Na przykład:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Zweryfikuj ProSort instalację, wykonując `prosort -h` polecenia. Na przykład:

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

OFCOBOL jest kompilatora OpenFrame, który interpretuje mainframe COBOL programów. 

**Aby zainstalować OFCOBOL**

1. Upewnij się, że instalacji partii/Online zakończyła się pomyślnie, a następnie upewnij się, że OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_pliku Instalatora bin 64. jest obecny.

2. Aby uruchomić Instalatora OFCOBOL, w wierszu polecenia wpisz:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Przeczytaj umowę licencyjną, a następnie naciśnij klawisz Enter, aby kontynuować.

4. Zaakceptuj Umowę licencyjną. Po zakończeniu instalacji, pojawią się następujące informacje:

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

5. Otwieranie profilu powłoki bash w vi (`vi .bash_profile`) i sprawdź, który jest aktualizowany za pomocą zmiennych OFCOBOL.
6. Wykonaj profilu powłoki bash. W wierszu polecenia wpisz polecenie:

     ```
      source ~/.bash_profile
     ```

7. Skopiuj licencji OFCOBOL do zainstalowanego folderu. Na przykład:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Przejdź do pliku konfiguracji tjclrun.conf OpenFrame, a następnie otwórz go w vi. Na przykład:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Poniżej przedstawiono sekcję SYSLIB przed zmianą:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Poniżej przedstawiono sekcję SYSLIB po zmianie:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Przejrzyj OpenFrame\_COBOL\_InstallLog.log w vi i sprawdzić, czy nie wystąpiły żadne błędy. Na przykład:
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
10. Użyj `ofcob --version ` poleceń i sprawdź numer wersji, aby zweryfikować instalację. Na przykład:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Ponowny rozruch przy użyciu OpenFrame `tmdown/tmboot` polecenia.

## <a name="install-ofasm"></a>Zainstaluj OFASM

OFASM jest kompilatora OpenFrame, który interpretuje mainframe asemblera programów.

**Aby zainstalować OFASM**

1. Upewnij się, że instalacji partii/Online zakończyła się pomyślnie, a następnie upewnij się, że OpenFrame\_ASM3\_0\_Linux\_x86\_pliku Instalatora bin 64. jest obecny.

2. Należy uruchomić Instalatora. Na przykład:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Przeczytaj umowę licencyjną, a następnie naciśnij klawisz Enter, aby kontynuować.
4. Zaakceptuj Umowę licencyjną.
5. Sprawdź, czy profil bash jest aktualizowany za pomocą zmiennych OFASM. Na przykład:

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

6. Otwórz plik konfiguracyjny tjclrun.conf OpenFrame w vi i edytuj go w następujący sposób:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Poniżej znajduje się w sekcji [SYSLIB] *przed* zmiany:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Poniżej znajduje się w sekcji [SYSLIB] *po* zmiany:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Otwórz OpenFrame\_ASM\_InstallLog.log w vi i sprawdzić, czy nie wystąpiły żadne błędy. Na przykład:

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

8. Ponowne uruchomienie OpenFrame przez jedno z następujących poleceń:

     ```
     tmdown / tmboot
     ```

     — lub —

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Zainstaluj OSC

OSC to środowisko OpenFrame, podobnie jak CICS IBM, obsługującego o dużej szybkości transakcji OLTP i innych funkcji zarządzania.

**Aby zainstalować OSC**

1. Upewnij się, że podstawowa instalacja zakończyła się pomyślnie, a następnie upewnij się, że OpenFrame\_OSC7\_0\_Fix2\_Linux\_x86\_pliku Instalatora bin 64. i pliku konfiguracji osc.properties obecne.
2. Edytuj następujące parametry w pliku osc.properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Uruchom Instalatora za pomocą pliku właściwości, jak pokazano:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Gdy skończysz, wyświetlany jest komunikat "Instalacja ukończony".

4. Upewnij się, że profil bash został zaktualizowany o OSC zmiennych.
5. Przejrzyj OpenFrame\_OSC7\_0\_Fix2\_InstallLog.log pliku. Powinno to wyglądać następująco:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Aby otworzyć plik konfiguracyjny ofsys.seq, należy użyć vi. Na przykład:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. W \#BASE i \#BATCH sekcji, edytowanie parametrów, jak pokazano.

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

8. Skopiuj plik licencji. Na przykład:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Aby uruchomić i zamknąć OSC, zainicjować pamięci region udostępnione CICS, wpisując `osctdlinit OSCOIVP1` w wierszu polecenia.

10. Uruchom `oscboot` do rozruchu OSC. Dane wyjściowe wyglądają następująco:

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

11. Aby sprawdzić, czy stan procesu jest gotowy, użyj `tmadmin` polecenia w pliku si. Wszystkie procesy powinna zostać wyświetlona RDY w **stan** kolumny.

    ![Wyświetlanie RDY procesów](media/tmadmin-02.png)

12. Zamknij system OSC za pomocą `oscdown` polecenia.

## <a name="install-jeus"></a>Zainstaluj JEUS

JEUS (Java użytkownika przedsiębiorstwa) zapewnia warstwę prezentacji OpenFrame serwer aplikacji sieci web.

Przed zainstalowaniem JEUS należy zainstalować pakiet Apache Ant, który udostępnia biblioteki i narzędzia wiersza polecenia niezbędne do zainstalowania JEUS.

**Aby zainstalować Apache Ant**

1. Używając binarne Ant pobierania `wget` polecenia. Na przykład:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Użyj `tar` narzędzie, aby wyodrębnić plik binarny i przenieś go do odpowiedniej lokalizacji. Na przykład:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Utwórz link symboliczny w celu zwiększenia wydajności:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Otwieranie profilu powłoki bash w vi (`vi .bash_profile`) i zaktualizuj go przy użyciu następujących zmiennych:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Zastosuj ustawienia zmiennej środowiskowej zmodyfikowane. Na przykład:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Aby zainstalować JEUS**

1. Rozwiń Instalatora za pomocą `tar` narzędzia. Na przykład:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Tworzenie **jeus** folder (`mkdir jeus7`) i rozpakuj plik binarny.
3. Zmień **Instalatora** katalogu (lub użyj parametru JEUS dla Twojego środowiska). Na przykład:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Wykonaj `ant clean-all` przed wykonaniem instrukcji w kompilacji. Dane wyjściowe wyglądają następująco:

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

5.  Tworzenie kopii zapasowych pliku domeny config-template.properties. Na przykład:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Otwórz plik domeny config-template.properties w vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Zmiana `jeus.password=jeusadmin nodename=Tmaxsoft` do `jeus.password=tmax1234 nodename=ofdemo`

8. Wykonaj `ant install` polecenie, aby skompilować JEUS.
9.  Aktualizuj .bash\_pliku profilu ze zmiennymi JEUS, jak pokazano:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Wykonaj profilu powłoki bash. Na przykład:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Opcjonalnie*. Utwórz alias łatwe zamykania i rozruchu JEUS składników:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Aby zweryfikować instalację, uruchom serwer administratora domeny, jak pokazano:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Zweryfikować logowania w sieci web przy użyciu składni:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Na przykład <http://192.168.92.133:9736/webadmin/login.> zostanie wyświetlony ekran logowania:
    
     ![Ekran logowania JEUS WebAdmin](media/jeus-01.png)

     > [!NOTE]
     > Jeśli napotkasz jakiekolwiek problemy z zabezpieczeniami portu otworzyć port 9736 lub wyłącz zaporę (`systemctl stop firewall`).

14. Aby zmienić nazwę hosta dla komputera serwer1, kliknij przycisk **zablokować & Edytuj**, następnie kliknij przycisk **serwer1**. W oknie serwera Zmień nazwę hosta w następujący sposób:

    1.  Zmiana **Nodename** do **ofdemo**.
    2.  Kliknij przycisk **OK** po prawej stronie okna.
    3.  Kliknij przycisk **Zastosuj zmiany** w lewej dolnej części okna i opis, wprowadź *zmiany nazwy hosta*.

    ![Ekran JEUS WebAdmin](media/jeus-02.png)

15. Sprawdź, czy konfiguracja jest pomyślne na ekranie potwierdzenia.

    ![ekran serwera jeus_domain](media/jeus-03.png)

16. Rozpocznij proces serwera zarządzanego "server1", korzystając z następującego polecenia:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Zainstaluj OFGW

OFGW jest bramą OpenFrame, który obsługuje komunikację między emulatora terminala 3270 i podstawowej OSI i zarządza sesji między emulatora terminala i OSI.

**Aby zainstalować OFGW**

1. Upewnij się, że JEUS został pomyślnie zainstalowany, a następnie upewnij się, że OFGW7\_0\_1\_Generic.bin plik Instalatora jest obecny.
2. Należy uruchomić Instalatora. Na przykład:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Użyj następujących lokalizacji dla odpowiedniego monity:
     -   Katalog macierzysty JEUS
     -   Nazwa domeny JEUS
     -   Nazwa serwera JEUS
     -   Tibero Driver
     -   Identyfikator węzła Tmax ofdemo

4. Zaakceptuj pozostałe wartości domyślne, a następnie naciśnij klawisz Enter, aby zakończyć pracę Instalatora.

5. Sprawdź, czy adres URL OFGW działa zgodnie z oczekiwaniami:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Pojawi się następujący ekran:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Zainstaluj OFManager

OFManager przewiduje działania i funkcjach zarządzania OpenFrame w środowisku sieci web.

**Aby zainstalować OFManager**

1. Upewnij się, że OFManager7\_Generic.bin plik Instalatora jest obecny.
2. Należy uruchomić Instalatora. Na przykład:

     ```
     OFManager7_Generic.bin
     ```

3.  Naciśnij klawisz Enter, aby kontynuować, a następnie zaakceptuj umowę licencyjną.
4.  Wybierz folder instalacji.
5.  Zaakceptuj wartości domyślne.
6.  Wybierz Tibero jako bazy danych.
7.  Naciśnij klawisz Enter, aby zakończyć pracę Instalatora.
8.  Sprawdź, czy adres URL OFManager działa zgodnie z oczekiwaniami:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Zostanie wyświetlony na ekranie startowym:

![Ekran logowania Tmax OpenFrame Menedżera](media/ofmanager-01.png)

Instalacja składników OpenFrame zostanie ukończona.

## <a name="next-steps"></a>Kolejne kroki

Jeśli rozważasz migracji komputera mainframe nasz powiększających ekosystem partnerów jest dostępna dla pomóc. Aby uzyskać szczegółowe wskazówki dotyczące wybierania rozwiązania partnerskiego, zapoznaj się [Alliance modernizacji platformy](https://www.platformmodernization.org/pages/mainframe.aspx).

-   [Rozpoczynanie pracy z platformą Azure](https://docs.microsoft.com/azure/)
-   [Dokumentacja usługi Host Integration Server (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Przewodnik Lift-and-Shift Azure wirtualnego centrum danych](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
