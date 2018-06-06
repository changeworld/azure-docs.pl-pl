---
title: Dostępność serwera SQL grupy - maszyn wirtualnych platformy Azure — samouczek | Dokumentacja firmy Microsoft
description: W tym samouczku przedstawiono sposób tworzenia programu SQL Server zawsze w grupie dostępności na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/09/2017
ms.author: mikeray
ms.openlocfilehash: 8796cd3224670c6d1c8b1b3c6da8d1c096b01d03
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716724"
---
# <a name="configure-always-on-availability-group-in-azure-vm-manually"></a>Konfigurowanie zawsze włączonej grupy dostępności w maszynie Wirtualnej platformy Azure ręcznie

W tym samouczku przedstawiono sposób tworzenia programu SQL Server zawsze w grupie dostępności na maszynach wirtualnych platformy Azure. Zakończenie samouczka tworzy grupy dostępności z repliki bazy danych na dwóch serwerach SQL.

**Szacowanie czasu**: trwa około 30 minut, aby ukończyć po spełnieniu wymagań wstępnych.

Diagram przedstawia kompilacji w samouczku.

![Grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Wymagania wstępne

Samouczka przyjęto założenie, że masz podstawową wiedzę na temat programu SQL Server zawsze włączonych grup dostępności. Aby uzyskać więcej informacji, zobacz [Omówienie programu zawsze włączonych grup dostępności (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).

W poniższej tabeli wymieniono wymagania wstępne, które należy wykonać przed rozpoczęciem tego samouczka:

|  |Wymaganie |Opis |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Dwa serwery SQL | — W zestawie dostępności Azure <br/> — W jednej domenie <br/> -Z zainstalowaną funkcją klastra trybu Failover |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Udział plików dla monitora klastra |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Konto usługi programu SQL Server | Konto domeny |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Konto usługi programu SQL Server Agent | Konto domeny |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Otwórz porty zapory | — Program SQL Server: **1433** dla domyślnego wystąpienia <br/> -Dublowania bazy danych punktu końcowego: **5022** lub dowolny dostępny port <br/> -Sondę modułu równoważenia obciążenia azure: **59999** lub dowolny dostępny port |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Dodawanie obsługi klastrów pracy awaryjnej | Oba serwery SQL wymagają tej funkcji |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Konto domeny instalacji | -Lokalnego administratora na każdym serwerze SQL <br/> -Członek roli serwera sysadmin programu SQL Server dla każdego wystąpienia programu SQL Server  |


Przed rozpoczęciem tego samouczka należy [ukończyć wymagania wstępne dotyczące tworzenia zawsze włączonych grup dostępności w maszynach wirtualnych platformy Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md). Jeśli te wymagania wstępne są już wypełnione, można przejść do [tworzenia klastrów](#CreateCluster).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## Tworzenie klastra

Po ukończeniu wymagania wstępne, pierwszym krokiem jest tworzenie klastra trybu Failover serwera systemu Windows, która zawiera dwa serwery SQL i serwer monitora.

1. RDP do pierwszego serwera SQL przy użyciu konta domeny, które jest kontem administratora na serwerach SQL i serwer monitora.

   >[!TIP]
   >Po wykonaniu [dokumentu z wymaganiami wstępnymi](virtual-machines-windows-portal-sql-availability-group-prereq.md), zostało utworzone konto o nazwie **CORP\Install**. Użyj tego konta.

2. W **Menedżera serwera** pulpitu nawigacyjnego, wybierz opcję **narzędzia**, a następnie kliknij przycisk **Menedżera klastra trybu Failover**.
3. W lewym okienku kliknij prawym przyciskiem myszy **Menedżera klastra trybu Failover**, a następnie kliknij przycisk **utworzyć klaster**.
   ![Tworzenie klastra](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. W kreatorze tworzenia klastra Tworzenie klastra z jednym węzłem przez krokowe stron z ustawieniami w poniższej tabeli:

   | Strona | Ustawienia |
   | --- | --- |
   | Przed rozpoczęciem |Użyj domyślnych |
   | Wybierz serwery |Wpisz nazwę pierwszego serwera SQL w **wprowadź nazwę serwera** i kliknij przycisk **Dodaj**. |
   | Ostrzeżenie dotyczące sprawdzania poprawności |Wybierz **nr nie jest wymagana obsługa firmy Microsoft dla tego klastra i z tego powodu nie chcesz uruchomić testy weryfikacyjne. Po kliknięciu przycisku dalej kontynuować tworzenie klastra**. |
   | Punkt dostępu do administrowania klastrem |Wpisz nazwę klastra, na przykład **SQLAGCluster1** w **nazwy klastra**.|
   | Potwierdzenie |Użyj wartości domyślnych, chyba że w przypadku korzystania z funkcji miejsca do magazynowania. Zobacz uwagi poniżej tej tabeli. |

### <a name="set-the-cluster-ip-address"></a>Ustaw adres IP klastra

1. W **Menedżera klastra trybu Failover**, przewiń w dół do **zasoby podstawowe klastra** i rozwiń listę szczegółów klastra. Powinny pojawić się zarówno **nazwa** i **adres IP** **zasobów** w stanu. Zasób adresu IP nie można przełączyć online, ponieważ klaster jest przypisany ten sam adres IP komputera samego w sobie, dlatego jest zduplikowany adres.

2. Kliknij prawym przyciskiem myszy nieudane **adres IP** zasobów, a następnie kliknij przycisk **właściwości**.

   ![Właściwości klastra](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Wybierz **statyczny adres IP** i określ adres z zakresu automatycznego adresowania prywatnego adresu IP (APIPA): 169.254.0.1 do 169.254.255.254 w polu tekstowym adres. W tym przykładzie można użyć dowolnego adresu w tym zakresie. Na przykład: `169.254.0.1`. Następnie kliknij przycisk **OK**.

4. W **zasoby podstawowe klastra** sekcji, kliknij prawym przyciskiem myszy nazwę klastra i kliknij przycisk **przejdź do trybu Online**. Następnie zaczekaj, aż oba zasoby są w trybie online. Gdy nazwa zasobu klastra do trybu online, aktualizuje serwera kontrolera domeny przy użyciu nowego konta komputera usługi AD. Umożliwia to konto AD później uruchomić usługę grupie dostępności w klastrze.

### <a name="addNode"></a>Dodaj do klastra programu SQL Server

SQL Server można dodać do klastra.

1. W drzewie przeglądarki, kliknij prawym przyciskiem myszy klaster, a następnie kliknij przycisk **Dodaj węzeł**.

    ![Dodaj węzeł do klastra](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. W **Kreatora dodawania węzłów**, kliknij przycisk **dalej**. W **Wybieranie serwerów** strony, dodać drugi serwer SQL. Wpisz nazwę serwera w **wprowadź nazwę serwera** , a następnie kliknij przycisk **Dodaj**. Gdy wszystko będzie gotowe, kliknij przycisk **dalej**.

1. W **ostrzeżenie dotyczące sprawdzania poprawności** kliknij przycisk **nr** (w środowisku produkcyjnym należy wykonać testy weryfikacyjne). Następnie kliknij przycisk **Dalej**.

8. W **potwierdzenie** strony, jeśli używasz funkcji miejsca do magazynowania, usuń zaznaczenie pola wyboru **Dodaj wszystkie odpowiednie magazyny do klastra.**

   ![Dodaj potwierdzenie węzła](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Jeśli używasz funkcji miejsca do magazynowania i nie Usuń zaznaczenie pola wyboru **Dodaj wszystkie odpowiednie magazyny do klastra**, Windows odłącza dysków wirtualnych podczas procesu klastrowania. W związku z tym są wyświetlane w Menedżerze dysków lub w Eksploratorze dopiero w funkcji miejsca do magazynowania są usuwane z klastra i ponownie nałożona przy użyciu programu PowerShell. Funkcja miejsca do magazynowania grupuje wiele dysków w pule magazynu. Aby uzyskać więcej informacji, zobacz [miejsca do magazynowania](https://technet.microsoft.com/library/hh831739).

1. Kliknij przycisk **Dalej**.

1. Kliknij przycisk **Zakończ**.

   Menedżer klastra trybu failover pokazuje, czy klaster ma nowy węzeł i wyświetla go w **węzłów** kontenera.

10. Wyloguj się z sesji usług pulpitu zdalnego.

### <a name="add-a-cluster-quorum-file-share"></a>Dodaj udział plików kworum klastra

W tym przykładzie klastra systemu Windows używa udziału plików w celu utworzenia kworum klastra. W tym samouczku używana kworum Większość węzłów i plików udziału. Aby uzyskać więcej informacji, zobacz [opis konfiguracji kworum w klastrze pracy awaryjnej](http://technet.microsoft.com/library/cc731739.aspx).

1. Nawiązać serwer członkowski monitora udziału plików z sesji usług pulpitu zdalnego.

1. Na **Menedżera serwera**, kliknij przycisk **narzędzia**. Otwórz **Zarządzanie komputerem**.

1. Kliknij przycisk **foldery udostępnione**.

1. Kliknij prawym przyciskiem myszy **udziałów**i kliknij przycisk **nowy udział...** .

   ![Nowy udział](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Użyj **udostępnionych Kreator tworzenia folderu** Aby utworzyć udział.

1. Na **ścieżka folderu**, kliknij przycisk **Przeglądaj** oraz znajdowanie lub utworzyć ścieżkę do folderu udostępnionego. Kliknij przycisk **Dalej**.

1. W **nazwę, opis i ustawienia** Sprawdź nazwy udziału i ścieżki. Kliknij przycisk **Dalej**.

1. Na **uprawnienia folderu udostępnionego** ustawić **Dostosuj uprawnienia**. Kliknij przycisk **niestandardowy...** .

1. Na **dostosowywanie uprawnień**, kliknij przycisk **Dodaj...** .

1. Upewnij się, że konto używane do utworzenia klastra ma pełną kontrolę.

   ![Nowy udział](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Kliknij przycisk **OK**.

1. W **uprawnienia folderu udostępnionego**, kliknij przycisk **Zakończ**. Kliknij przycisk **Zakończ** ponownie.  

1. Wyloguj się z serwera

### <a name="configure-cluster-quorum"></a>Konfigurowanie kworum klastra

Następnie ustaw kworum klastra.

1. Połącz w pierwszym węźle klastra przy użyciu pulpitu zdalnego.

1. W **Menedżera klastra trybu Failover**, kliknij prawym przyciskiem myszy klaster, wskaż polecenie **więcej akcji**i kliknij przycisk **Konfiguruj ustawienia kworum klastra...** .

   ![Nowy udział](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. W **Kreatora konfiguracji kworum klastra**, kliknij przycisk **dalej**.

1. W **Wybieranie opcji konfiguracji kworum**, wybierz **Wybieranie monitora kworum**i kliknij przycisk **dalej**.

1. Na **Wybieranie monitora kworum**, kliknij przycisk **Konfigurowanie monitora udziału plików**.

   >[!TIP]
   >Windows Server 2016 obsługuje monitora chmury. Jeśli wybierzesz tego typu monitora nie ma potrzeby pliku monitor udostępniania. Aby uzyskać więcej informacji, zobacz [wdrażania chmury monitora dla klastra trybu Failover](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Ten samouczek używa monitora udziału plików, który jest obsługiwany przez wcześniejsze systemy operacyjne.

1. Na **Konfigurowanie monitora udostępniania plików**, wpisz ścieżkę do udziału utworzonego. Kliknij przycisk **Dalej**.

1. Sprawdź ustawienia na **potwierdzenie**. Kliknij przycisk **Dalej**.

1. Kliknij przycisk **Zakończ**.

Zasoby podstawowe klastra są skonfigurowane z monitora udziału plików.

## <a name="enable-availability-groups"></a>Włącz grupy dostępności

Następnie włącz opcję **zawsze włączonych grup dostępności** funkcji. Wykonaj te kroki na obu serwerach SQL.

1. Z **Start** ekranu, uruchom **SQL Server Configuration Manager**.
2. W drzewie w przeglądarce kliknij **usług SQL Server**, kliknij prawym przyciskiem myszy **programu SQL Server (MSSQLSERVER)** usługi, a następnie kliknij przycisk **właściwości**.
3. Kliknij przycisk **wysokiej dostępności funkcji AlwaysOn** , a następnie wybierz **Włącz zawsze włączone grupy dostępności**w następujący sposób:

    ![Włącz zawsze włączone grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Kliknij przycisk **Zastosuj**. Kliknij przycisk **OK** w wyskakującym oknie dialogowym.

5. Uruchom ponownie usługę SQL Server.

Powtórz te czynności w programie SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Utwórz bazę danych na pierwszym serwerze SQL

1. Uruchom plik RDP do pierwszego programu SQL Server przy użyciu konta domeny, które jest członkiem stałej roli serwera sysadmin.
1. Otwórz program SQL Server Management Studio i Połącz z pierwszym serwerem SQL.
7. W **Eksplorator obiektów**, kliknij prawym przyciskiem myszy **baz danych** i kliknij przycisk **nową bazę danych**.
8. W **Nazwa bazy danych**, typ **MyDB1**, następnie kliknij przycisk **OK**.

### <a name="backupshare"></a> Tworzenie kopii zapasowej udziału

1. Na pierwszym serwerze SQL w **Menedżera serwera**, kliknij przycisk **narzędzia**. Otwórz **Zarządzanie komputerem**.

1. Kliknij przycisk **foldery udostępnione**.

1. Kliknij prawym przyciskiem myszy **udziałów**i kliknij przycisk **nowy udział...** .

   ![Nowy udział](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Użyj **udostępnionych Kreator tworzenia folderu** Aby utworzyć udział.

1. Na **ścieżka folderu**, kliknij przycisk **Przeglądaj** oraz znajdowanie lub utworzyć ścieżkę do udostępnionego folderu kopii zapasowej bazy danych. Kliknij przycisk **Dalej**.

1. W **nazwę, opis i ustawienia** Sprawdź nazwy udziału i ścieżki. Kliknij przycisk **Dalej**.

1. Na **uprawnienia folderu udostępnionego** ustawić **Dostosuj uprawnienia**. Kliknij przycisk **niestandardowy...** .

1. Na **dostosowywanie uprawnień**, kliknij przycisk **Dodaj...** .

1. Upewnij się, że konta usługi programu SQL Server i Agent serwera SQL dla obu serwerów mają pełną kontrolę.

   ![Nowy udział](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Kliknij przycisk **OK**.

1. W **uprawnienia folderu udostępnionego**, kliknij przycisk **Zakończ**. Kliknij przycisk **Zakończ** ponownie.  

### <a name="take-a-full-backup-of-the-database"></a>Podejmij pełnej kopii zapasowej bazy danych

Należy utworzyć kopię zapasową nową bazę danych, aby zainicjować łańcuch dziennika. Jeśli nie wykonuje kopię zapasową nową bazę danych, nie można uwzględnić w grupie dostępności.

1. W **Eksplorator obiektów**, kliknij prawym przyciskiem myszy bazę danych, wskaż polecenie **zadań...** , kliknij przycisk **kopię zapasową**.

1. Kliknij przycisk **OK** do wykonania pełnej kopii zapasowej do domyślnej lokalizacji kopii zapasowej.

## <a name="create-the-availability-group"></a>Utworzenie grupy dostępności
Teraz można przystąpić do konfigurowania grupy dostępności, wykonując następujące czynności:

* Utwórz bazę danych na pierwszym serwerze SQL.
* Przełączyć pełnej kopii zapasowej i kopii zapasowej dziennika transakcji bazy danych
* Przywróć pełnego i kopii zapasowych dziennika do drugiego serwera SQL z **NORECOVERY** opcji
* Utworzenie grupy dostępności (**AG1**) z zatwierdzanie synchroniczne, automatycznej pracy awaryjnej i do odczytu replikach pomocniczych

### <a name="create-the-availability-group"></a>Tworzenie grupy dostępności:

1. W sesji pulpitu zdalnego do pierwszego serwera SQL. W **Eksplorator obiektów** w programie SSMS, kliknij prawym przyciskiem myszy **wysokiej dostępności funkcji AlwaysOn** i kliknij przycisk **Kreatora nowej grupy dostępności**.

    ![Uruchom Kreatora nowej grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. W **wprowadzenie** kliknij przycisk **dalej**. W **Określ nazwę grupy dostępności** wpisz nazwę grupy dostępności, na przykład **AG1**w **Nazwa grupy dostępności**. Kliknij przycisk **Dalej**.

    ![Kreator nowego AG, określ nazwę grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. W **wybierz baz danych** , wybierz bazę danych i kliknij przycisk **dalej**.

   >[!NOTE]
   >Bazy danych spełnia wymagania wstępne grupy dostępności, ponieważ co najmniej jedną pełną kopię zapasową wykonano w zamierzonej replice podstawowej.

   ![Kreatora nowej grupy dostępności, wybierz baz danych](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. W **Określanie replik** kliknij przycisk **dodać repliki**.

   ![Kreator nowego AG, określ repliki](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. **Połącz z serwerem** wyskakującej okna dialogowego. Wpisz nazwę drugiego serwera w **nazwy serwera**. Kliknij przycisk **Połącz**.

   W **Określanie replik** strony, powinien zostać wyświetlony drugi serwer na liście **replik dostępności**. Konfigurowanie replik w następujący sposób.

   ![Kreator nowego AG, określ repliki (pełną)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Kliknij przycisk **punkty końcowe** wyświetlić punktu końcowego dublowania dla tej grupy dostępności bazy danych. Użyj tego samego portu, który został użyty podczas ustawiania [reguły zapory dla punktów końcowych dublowania bazy danych](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Kreatora nowej grupy dostępności, wybierz początkową synchronizację danych](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. W **Wybierz początkową synchronizację danych** wybierz **pełne** i określ udostępnionej lokalizacji sieciowej. Dla lokalizacji, należy użyć [udziale kopii zapasowej utworzonego](#backupshare). W tym przykładzie został **\\\\\<pierwszy serwer SQL\>\Backup\**. Kliknij przycisk **Dalej**.

   >[!NOTE]
   >Pełna synchronizacja przyjmuje pełną kopię zapasową bazy danych w pierwszym wystąpieniu programu SQL Server i przywrócenie go do drugiego wystąpienia. Pełna synchronizacja w przypadku dużych baz danych nie jest zalecane, ponieważ może zająć dużo czasu. Teraz można zmniejszyć ręcznie wykonywanie kopii zapasowej bazy danych i przywrócenie go przy użyciu `NO RECOVERY`. Jeśli baza danych jest już przywrócona z `NO RECOVERY` na drugim serwerze SQL przed rozpoczęciem konfigurowania grupy dostępności, wybierz **tylko Dołącz**. Jeśli chcesz wykonać kopię zapasową po skonfigurowaniu grupy dostępności, wybierz **Pomiń początkową synchronizację danych**.

    ![Kreatora nowej grupy dostępności, wybierz początkową synchronizację danych](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. W **weryfikacji** kliknij przycisk **dalej**. Ta strona powinien wyglądać podobnie do poniższej ilustracji:

    ![Kreator nowego AG, sprawdzanie poprawności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Jest ostrzeżenie w konfiguracji odbiornika, ponieważ nie skonfigurowano odbiornika grupy dostępności. Można zignorować to ostrzeżenie, ponieważ na maszynach wirtualnych Azure utworzenie obiektu nasłuchującego po utworzeniu usługi równoważenia obciążenia Azure.

10. W **Podsumowanie** kliknij przycisk **Zakończ**, następnie zaczekaj, aż Kreator skonfiguruje nową grupę dostępności. W **postępu** strony, możesz kliknąć **szczegółowe** Aby wyświetlić szczegółowe informacje o postępie. Po zakończeniu działania kreatora, należy sprawdzić **wyniki** stronę, aby sprawdzić, czy grupa dostępności jest pomyślnie utworzony.

     ![Wyniki w Kreatorze nowej grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Kliknij przycisk **Zamknij** aby zakończyć pracę kreatora.

### <a name="check-the-availability-group"></a>Sprawdzanie grupy dostępności

1. W **Eksplorator obiektów**, rozwiń węzeł **wysokiej dostępności funkcji AlwaysOn**, następnie rozwiń węzeł **grup dostępności**. Powinna zostać wyświetlona nowej grupy dostępności w tym kontenerze. Kliknij prawym przyciskiem myszy grupę dostępności, a następnie kliknij przycisk **Pokaż pulpit nawigacyjny**.

   ![Pokaż AG pulpitu nawigacyjnego](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   Twoje **pulpitu nawigacyjnego AlwaysOn** powinna wyglądać podobnie do poniższego.

   ![Pulpit nawigacyjny grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Widać replik, tryb pracy awaryjnej każdej repliki i stan synchronizacji.

2. W **Menedżera klastra trybu Failover**, kliknij przycisk klastra. Wybierz **ról**. Nazwa grupy dostępności, używanego jest rola w klastrze. Tej grupy dostępności nie ma adresu IP dla połączeń klienckich, ponieważ nie może skonfigurować odbiornik. Skonfiguruj odbiornik po utworzeniu usługi równoważenia obciążenia Azure.

   ![W Menedżerze klastra trybu Failover grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Nie należy próbować awaryjnie grupy dostępności z Menedżera klastra pracy awaryjnej. Należy wykonać wszystkie operacje trybu failover z poziomu **pulpitu nawigacyjnego AlwaysOn** w programie SSMS. Aby uzyskać więcej informacji, zobacz [ograniczenia na przy użyciu Menedżera klastra trybu Failover z grupami dostępności](https://msdn.microsoft.com/library/ff929171.aspx).
    >

W tym momencie masz grupę dostępności z replik na dwa wystąpienia programu SQL Server. Grupa dostępności można przenosić między wystąpieniami. Nie można połączyć do grupy dostępności jeszcze, ponieważ nie masz odbiornik. W maszynach wirtualnych platformy Azure odbiornika wymaga usługi równoważenia obciążenia. Następnym krokiem jest tworzenie usługi równoważenia obciążenia na platformie Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Tworzenie modułu równoważenia obciążenia na platformie Azure

Na maszynach wirtualnych Azure grupy dostępności programu SQL Server wymaga usługi równoważenia obciążenia. Moduł równoważenia obciążenia zawiera adres IP dla odbiornika grupy dostępności. Ta sekcja zawiera podsumowanie sposobu tworzenia modułu równoważenia obciążenia w portalu Azure.

1. W portalu Azure, przejdź do grupy zasobów, w których są serwery SQL i kliknij przycisk **+ Dodaj**.
2. Wyszukaj **modułu równoważenia obciążenia**. Wybierz opublikowane przez firmę Microsoft modułu równoważenia obciążenia.

   ![W Menedżerze klastra trybu Failover grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1.  Kliknij przycisk **Utwórz**.
3. Skonfiguruj następujące parametry dla usługi równoważenia obciążenia.

   | Ustawienie | Pole |
   | --- | --- |
   | **Nazwa** |Użyj nazwy tekstu dla usługi równoważenia obciążenia, na przykład **sqlLB**. |
   | **Typ** |Wewnętrzny |
   | **Sieć wirtualna** |Użyj nazwy sieci wirtualnej platformy Azure. |
   | **Podsieć** |Użyj nazwy podsieci, która znajduje się w maszynie wirtualnej.  |
   | **Przypisywanie adresów IP** |Statyczny |
   | **Adres IP** |Użyj dostępny adres podsieci. Należy pamiętać, że ta różni się od adresu IP klastra |
   | **Subskrypcja** |Użyj tej samej subskrypcji co maszyna wirtualna. |
   | **Lokalizacja** |Użyj tej samej lokalizacji co maszyna wirtualna. |

   Blok portalu Azure powinna wyglądać następująco:

   ![Tworzenie usługi równoważenia obciążenia](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Kliknij przycisk **Utwórz**w celu utworzenia usługi równoważenia obciążenia.

Aby skonfigurować usługę równoważenia obciążenia, musisz utworzyć puli wewnętrznej bazy danych, sondowania i ustawić reguły równoważenia obciążenia. Czy w portalu Azure.

### <a name="add-backend-pool"></a>Dodawanie puli wewnętrznej bazy danych

1. W portalu Azure przejdź do tej grupy dostępności. Może być konieczne odświeżenie widoku, aby zobaczyć usługi równoważenia obciążenia nowo utworzony.

   ![Znajdź moduł równoważenia obciążenia w grupie zasobów](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Kliknij usługę równoważenia obciążenia, kliknij przycisk **pul zaplecza**i kliknij przycisk **+ Dodaj**. 

1. Skojarz puli wewnętrznej bazy danych z zestawu dostępności, która zawiera maszyn wirtualnych.

1. W obszarze **Target konfiguracje adresów IP sieci**, sprawdź **maszyny WIRTUALNEJ** i obie maszyny wirtualne, które będą obsługiwać repliki grupy dostępności. Nie dołączaj serwera monitora udziału plików.

   >[!NOTE]
   >Jeśli obie maszyny wirtualne nie są określone, połączenia powiedzie się tylko do repliki podstawowej.

1. Kliknij przycisk **OK** do utworzenia puli wewnętrznej bazy danych.

### <a name="set-the-probe"></a>Ustaw sondy

1. Kliknij usługę równoważenia obciążenia, kliknij przycisk **sondy kondycji**i kliknij przycisk **+ Dodaj**.

1. Ustaw sondy kondycji w następujący sposób:

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Tekst | SQLAlwaysOnEndPointProbe |
   | **Protokół** | Wybierz protokół TCP | TCP |
   | **Port** | Wszelkie nieużywany port. | 59999 |
   | **Interwał**  | Ilość czasu między próbami sondy w sekundach |5 |
   | **Próg złej kondycji** | Liczba kolejnych niepowodzeń sondy musi odbywać się dla maszyny wirtualnej być określana jako Zła  | 2 |

1. Kliknij przycisk **OK** można ustawić sondy kondycji.

### <a name="set-the-load-balancing-rules"></a>Ustaw reguły równoważenia obciążenia

1. Kliknij usługę równoważenia obciążenia, kliknij przycisk **reguły równoważenia obciążenia**i kliknij przycisk **+ Dodaj**.

1. Ustaw następujące zasady funkcji równoważenia obciążenia.
   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Tekst | SQLAlwaysOnEndPointListener |
   | **Adres IP frontonu** | Wybierz adres |Użyj adresu, który został utworzony podczas tworzenia modułu równoważenia obciążenia. |
   | **Protokół** | Wybierz protokół TCP |TCP |
   | **Port** | Użyj portu dla wystąpienia programu SQL Server | 1433 |
   | **Port zaplecza** | To pole nie jest używany, gdy pływający adres IP jest wartość dla serwera bezpośredniego zwrotu | 1433 |
   | **Sondy** |Nazwa określona dla sondy | SQLAlwaysOnEndPointProbe |
   | **Trwałość sesji** | Listy rozwijanej | **Brak** |
   | **Limit czasu bezczynności** | Minut, aby utrzymać otwarte połączenie TCP | 4 |
   | **Zmienny adres IP (bezpośredni zwrot serwera)** | |Enabled (Włączony) |

   > [!WARNING]
   > Bezpośredni zwrot serwera jest ustawiana podczas tworzenia. Nie można zmienić tej nazwy.

1. Kliknij przycisk **OK** można ustawić reguły równoważenia obciążenia.

## <a name="configure-listener"></a> Konfigurowanie odbiornika

Następnym etapem jest skonfigurować odbiornik grupy dostępności w klastrze pracy awaryjnej.

> [!NOTE]
> W tym samouczku przedstawiono sposób tworzenia jednego odbiornika — z jednym adresem ILB IP. Aby utworzyć co najmniej jednego odbiornika za pomocą jednego lub więcej adresów IP, zobacz [odbiornika Create Availability Group, a moduł równoważenia obciążenia | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Port odbiornika zestawu

W programu SQL Server Management Studio Ustaw numer portu odbiornika.

1. Uruchom program SQL Server Management Studio i połącz się repliką podstawową.

1. Przejdź do **AlwaysOn wysokiej dostępności** | **grup dostępności** | **odbiorniki grupy dostępności**.

1. Powinna zostać wyświetlona nazwa odbiornika utworzonego w Menedżerze klastra trybu Failover. Kliknij prawym przyciskiem myszy nazwę odbiornika, a następnie kliknij przycisk **właściwości**.

1. W **portu** polu Określ numer portu dla odbiornika grupy dostępności, używając $EndpointPort używany w starszych (1433 jest wartość domyślna), następnie kliknij przycisk **OK**.

Masz teraz grupy dostępności programu SQL Server na maszynach wirtualnych Azure działających w trybie Menedżera zasobów.

## <a name="test-connection-to-listener"></a>Przetestuj połączenie odbiornika

Aby przetestować połączenie:

1. RDP do programu SQL Server, który znajduje się w tej samej sieci wirtualnej, ale nie jest właścicielem repliki. Można użyć programu SQL Server w klastrze.

1. Użyj **sqlcmd** narzędzia do testowania połączenia. Na przykład poniższy skrypt ustanawia **sqlcmd** połączenie z repliką podstawową za pośrednictwem odbiornika z uwierzytelnianiem systemu Windows:

    ```
    sqlcmd -S <listenerName> -E
    ```

    Jeśli odbiornik używa portu innego niż domyślny port (1433), określ numer portu w parametrach połączenia. Na przykład następujące polecenie narzędzia sqlcmd łączy odbiornik portu 1435:

    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

Połączenia narzędzia SQLCMD automatycznie łączy się z innego wystąpienia programu SQL Server obsługuje replikę podstawową.

> [!TIP]
> Upewnij się, że port, który określisz jest otwarty na zaporze oba serwery SQL. Oba serwery wymagają regułę ruchu przychodzącego dla portu TCP, którego używasz. Aby uzyskać więcej informacji, zobacz [Dodawanie lub edytowanie reguły zapory](http://technet.microsoft.com/library/cc753558.aspx).
>
>



<!--**Notes**: *Notes provide just-in-time info: A Note is “by the way” info, an Important is info users need to complete a task, Tip is for shortcuts. Don’t overdo*.-->


<!--**Procedures**: *This is the second “step." They often include substeps. Again, use a short title that tells users what they’ll do*. *("Configure a new web project.")*-->

<!--**UI**: *Note the format for documenting the UI: bold for UI elements and arrow keys for sequence. (Ex. Click **File > New > Project**.)*-->

<!--**Screenshot**: *Screenshots really help users. But don’t include too many since they’re difficult to maintain. Highlight areas you are referring to in red.*-->

<!--**No. of steps**: *Make sure the number of steps within a procedure is 10 or fewer. Seven steps is ideal. Break up long procedure logically.*-->


<!--**Next steps**: *Reiterate what users have done, and give them interesting and useful next steps so they want to go on.*-->

## <a name="next-steps"></a>Kolejne kroki

- [Dodaj adres IP do modułu równoważenia obciążenia dla drugiej grupy dostępności](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
