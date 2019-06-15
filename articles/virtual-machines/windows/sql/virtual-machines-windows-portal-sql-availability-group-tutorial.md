---
title: Grupy dostępności serwera SQL — usługa Azure Virtual Machines — samouczek | Dokumentacja firmy Microsoft
description: W tym samouczku przedstawiono sposób tworzenia programu SQL Server zawsze włączonej grupy dostępności na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
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
ms.date: 08/30/2018
ms.author: mikeray
ms.openlocfilehash: d86538fca907f7181bf58ff236bba8de186641fb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60593378"
---
# <a name="tutorial-configure-always-on-availability-group-in-azure-vm-manually"></a>Samouczek: Konfigurowanie zawsze włączonej grupy dostępności na maszynie Wirtualnej platformy Azure ręcznie

W tym samouczku przedstawiono sposób tworzenia programu SQL Server zawsze włączonej grupy dostępności na maszynach wirtualnych platformy Azure. Kompletny samouczek tworzy grupy dostępności z repliki bazy danych na dwóch serwerach SQL.

**Szacowany czas**: Trwa około 30 minut po spełnieniu wymagań wstępnych.

Na diagramie przedstawiono kompilacji w tym samouczku.

![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Wymagania wstępne

W samouczku przyjęto założenie, że masz podstawową wiedzę na temat programu SQL Server zawsze włączonych grup dostępności. Aby uzyskać więcej informacji, zobacz [przegląd z zawsze włączonych grup dostępności (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

Poniższa tabela zawiera listę wymagań wstępnych, które należy wykonać przed rozpoczęciem tego samouczka:

|  |Wymaganie |Opis |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Dwa serwery SQL | -W zestawie dostępności platformy Azure <br/> — W jednej domenie <br/> -Z zainstalowaną funkcją klastra trybu Failover |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Udziału plików monitora klastra |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Konto usługi programu SQL Server | Konto domeny |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Konto usługi programu SQL Server Agent | Konto domeny |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Otwórz porty zapory | — Program SQL Server: **1433** dla domyślnego wystąpienia <br/> -Końcowego dublowania bazy danych: **5022** lub dowolny dostępny port <br/> — Grupa dostępności obciążenia sonda kondycji adres IP modułu równoważenia: **59999** lub dowolny dostępny port <br/> -Klastra sondy kondycji adres IP modułu równoważenia obciążenia core: **58888** lub dowolny dostępny port |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Dodawanie obsługi klastrów pracy awaryjnej | Oba serwery SQL wymagają tej funkcji |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Konto domeny instalacji | -Lokalnego administratora na każdego programu SQL Server <br/> -Członkiem programu SQL Server stałej roli serwera sysadmin dla każdego wystąpienia programu SQL Server  |


Przed rozpoczęciem tego samouczka musisz [spełnić wymagania wstępne dotyczące tworzenia zawsze włączonych grup dostępności w usłudze Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md). Jeśli te wymagania wstępne są już wypełnione, możesz przejść do tematu [tworzenia klastrów](#CreateCluster).

  >[!NOTE]
  > Wiele z krokami opisanymi w tym samouczku teraz można zautomatyzować za pomocą [wiersza polecenia platformy Azure SQL VM](virtual-machines-windows-sql-availability-group-cli.md) i [szablony szybkiego startu platformy Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>Tworzenie klastra

Po ukończeniu wymagania wstępne, pierwszym krokiem jest do utworzenia klastra trybu Failover serwera systemu Windows, która zawiera dwa serwery SQL i serwer monitora.

1. Protokół RDP do pierwszego programu SQL Server przy użyciu konta domeny, które jest kontem administratora na serwerach SQL i serwer monitora.

   >[!TIP]
   >Jeśli wykonano [dokumentu z wymaganiami wstępnymi](virtual-machines-windows-portal-sql-availability-group-prereq.md), zostało utworzone konto o nazwie **CORP\Install**. Użyj tego konta.

2. W **Menedżera serwera** pulpitu nawigacyjnego, wybierz opcję **narzędzia**, a następnie kliknij przycisk **Menedżera klastra trybu Failover**.
3. W lewym okienku kliknij prawym przyciskiem myszy **Menedżera klastra trybu Failover**, a następnie kliknij przycisk **Utwórz klaster**.
   ![Tworzenie klastra](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. W obszarze Kreator tworzenia klastra należy utworzyć klastra z jednym węzłem, przechodzenie krok po kroku na stronach z ustawieniami podanymi w poniższej tabeli:

   | Strona | Ustawienia |
   | --- | --- |
   | Przed rozpoczęciem |Użyj wartości domyślnych |
   | Wybierz serwery |Wpisz nazwę pierwszego programu SQL Server w **wprowadź nazwę serwera** i kliknij przycisk **Dodaj**. |
   | Ostrzeżenie dotyczące sprawdzania poprawności |Wybierz **nie. nie jest wymagana obsługa firmy Microsoft dotyczącej tego klastra, a w związku z tym nie chcesz uruchomić testy weryfikacyjne. Po kliknięciu przycisku Dalej kontynuacja procesu tworzenia klastra**. |
   | Punkt dostępu do administrowania klastrem |Wpisz nazwę klastra, na przykład **SQLAGCluster1** w **nazwy klastra**.|
   | Potwierdzenie |Użyj ustawień domyślnych, chyba że korzysta z funkcji miejsca do magazynowania. Zobacz uwagi pod tą tabelą. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Ustaw serwer Windows adres IP klastra trybu failover

1. W **Menedżera klastra trybu Failover**, przewiń w dół do **zasoby podstawowe klastra** i rozwiń szczegóły klastra. Powinny pojawić się zarówno **nazwa** i **adres IP** **zasobów** w stanu. Zasób adresu IP nie można przełączyć online, ponieważ klaster jest przypisany ten sam adres IP jako samą maszynę, dlatego jest zduplikowany adres.

2. Kliknij prawym przyciskiem myszy nieudane **adresu IP** zasobów, a następnie kliknij **właściwości**.

   ![Właściwości klastra](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Wybierz **statyczny adres IP** i określając dostępny adres do tej samej podsieci maszyn wirtualnych.

4. W **zasoby podstawowe klastra** sekcji, kliknij prawym przyciskiem myszy nazwę klastra i kliknij przycisk **przejdź do trybu Online**. Następnie poczekaj na oba zasoby są w trybie online. Gdy zasobu nazwy klastra do trybu online, aktualizuje serwera kontrolera domeny przy użyciu nowego konta komputera usługi AD. Do uruchamiania usługi grupy dostępności w klastrze później, należy użyć tego konta usługi AD.

### <a name="addNode"></a>Dodawanie programu SQL Server do klastra

SQL Server można dodać do klastra.

1. W drzewie przeglądarki kliknij prawym przyciskiem myszy klaster, a następnie kliknij przycisk **Dodaj węzeł**.

    ![Dodaj węzeł do klastra](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. W **Kreatora dodawania węzłów**, kliknij przycisk **dalej**. W **wybierz serwery** strony i dodać drugi serwer SQL. Wpisz nazwę serwera w **wprowadź nazwę serwera** a następnie kliknij przycisk **Dodaj**. Gdy wszystko będzie gotowe, kliknij przycisk **dalej**.

1. W **ostrzeżenie dotyczące sprawdzania poprawności** kliknij **nie** (w środowisku produkcyjnym należy wykonać testy weryfikacyjne). Następnie kliknij przycisk **Dalej**.

8. W **potwierdzenie** strony, jeśli używasz funkcji miejsca do magazynowania, należy usunąć zaznaczenie pola wyboru **Dodaj wszystkie odpowiednie magazyny do klastra.**

   ![Dodaj potwierdzenie węzła](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Jeśli używasz funkcji miejsca do magazynowania, a nie Usuń zaznaczenie pola wyboru **Dodaj wszystkie odpowiednie magazyny do klastra**, Windows powoduje odłączenie dysków wirtualnych podczas procesu klastrowania. W rezultacie nie są wyświetlane w Menedżerze dysków lub Eksploratorze aż miejsca do magazynowania są usuwane z klastra i ponownie dołączyć przy użyciu programu PowerShell. Miejsca do magazynowania grupuje wielu dysków w pule magazynu. Aby uzyskać więcej informacji, zobacz [miejsca do magazynowania](https://technet.microsoft.com/library/hh831739).

1. Kliknij przycisk **Dalej**.

1. Kliknij przycisk **Zakończ**.

   Menedżer klastra trybu failover pokazuje, że klaster ma nowy węzeł i wyświetla go w **węzłów** kontenera.

10. Wyloguj się z sesji usług pulpitu zdalnego.

### <a name="add-a-cluster-quorum-file-share"></a>Dodawanie udziału plików kworum klastra

W tym przykładzie klaster Windows używa udziału plików w celu utworzenia kworum klastra. Ten samouczek używa kworum Większość węzłów i plików udziału. Aby uzyskać więcej informacji, zobacz [opis konfiguracji kworum w klastrze trybu Failover](https://technet.microsoft.com/library/cc731739.aspx).

1. Nawiązać serwerze elementu członkowskiego monitora udostępniania plików przy użyciu sesji pulpitu zdalnego.

1. Na **Menedżera serwera**, kliknij przycisk **narzędzia**. Otwórz **Zarządzanie komputerem**.

1. Kliknij przycisk **foldery udostępnione**.

1. Kliknij prawym przyciskiem myszy **udziałów**i kliknij przycisk **nowy udział...** .

   ![Nowy udział](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Użyj **udostępnione Kreator tworzenia folderu** utworzyć udział.

1. Na **ścieżka folderu**, kliknij przycisk **Przeglądaj** oraz znajdowanie lub tworzenie ścieżki dla folderu udostępnionego. Kliknij przycisk **Dalej**.

1. W **nazwę, opis i ustawienia** Sprawdź nazwę udziału i ścieżki. Kliknij przycisk **Dalej**.

1. Na **uprawnienia folderu udostępnionego** ustaw **dostosowywać uprawnienia**. Kliknij przycisk **niestandardowe...** .

1. Na **dostosowywanie uprawnień**, kliknij przycisk **Dodaj...** .

1. Upewnij się, że konto używane do utworzenia klastra ma pełną kontrolę.

   ![Nowy udział](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Kliknij przycisk **OK**.

1. W **uprawnienia folderu udostępnionego**, kliknij przycisk **Zakończ**. Kliknij przycisk **Zakończ** ponownie.  

1. Wyloguj się z serwerem

### <a name="configure-cluster-quorum"></a>Konfigurowanie kworum klastra

Następnym etapem jest skonfigurowanie kworum klastra.

1. Połącz w pierwszym węźle klastra, przy użyciu pulpitu zdalnego.

1. W **Menedżera klastra trybu Failover**, kliknij prawym przyciskiem myszy klaster, wskaż **więcej akcji**i kliknij przycisk **Konfiguruj ustawienia kworum klastra...** .

   ![Nowy udział](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. W **Kreatora konfiguracji kworum klastra**, kliknij przycisk **dalej**.

1. W **Wybieranie opcji konfiguracji kworum**, wybierz **Wybieranie monitora kworum**i kliknij przycisk **dalej**.

1. Na **Wybieranie monitora kworum**, kliknij przycisk **Konfigurowanie monitora udziału plików**.

   >[!TIP]
   >Windows Server 2016 obsługuje monitora w chmurze. Jeśli ten typ monitora, nie potrzebujesz pliku monitor udostępniania. Aby uzyskać więcej informacji, zobacz [wdrażanie monitora chmury dla klastra trybu Failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Ten samouczek używa monitora udziału plików, który jest obsługiwany przez starszych systemów operacyjnych.

1. Na **Konfigurowanie monitora udostępniania plików**, wpisz ścieżkę do udziału, został utworzony. Kliknij przycisk **Dalej**.

1. Sprawdź ustawienia na **potwierdzenie**. Kliknij przycisk **Dalej**.

1. Kliknij przycisk **Zakończ**.

Zasoby podstawowe klastra są skonfigurowane przy użyciu monitora udziału plików.

## <a name="enable-availability-groups"></a>Włącz grupy dostępności

Następnie Włącz **zawsze włączonych grup dostępności** funkcji. Wykonaj następujące kroki na obu serwerach SQL.

1. Z **Start** ekranu, uruchom **Menedżera konfiguracji SQL Server**.
2. W drzewie przeglądarki kliknij **usług SQL Server**, kliknij prawym przyciskiem myszy **programu SQL Server (MSSQLSERVER)** usługi, a następnie kliknij przycisk **właściwości**.
3. Kliknij przycisk **wysokiej dostępności (AlwaysOn)** kartę, a następnie wybierz **Włącz zawsze włączone grupy dostępności**, wykonując następujące czynności:

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

## <a name="create-a-database-on-the-first-sql-server"></a>Tworzenie bazy danych na pierwszym serwerze SQL

1. Uruchom plik protokołu RDP do pierwszego programu SQL Server przy użyciu konta domeny, który jest członkiem stałej roli serwera sysadmin.
1. Otwórz program SQL Server Management Studio i Połącz z pierwszego serwera SQL.
7. W **Eksplorator obiektów**, kliknij prawym przyciskiem myszy **baz danych** i kliknij przycisk **nową bazę danych**.
8. W **Nazwa bazy danych**, typ **MyDB1**, następnie kliknij przycisk **OK**.

### <a name="backupshare"></a> Tworzenie kopii zapasowych udziału

1. Na pierwszym serwerze SQL w **Menedżera serwera**, kliknij przycisk **narzędzia**. Otwórz **Zarządzanie komputerem**.

1. Kliknij przycisk **foldery udostępnione**.

1. Kliknij prawym przyciskiem myszy **udziałów**i kliknij przycisk **nowy udział...** .

   ![Nowy udział](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Użyj **udostępnione Kreator tworzenia folderu** utworzyć udział.

1. Na **ścieżka folderu**, kliknij przycisk **Przeglądaj** oraz znajdowanie lub tworzenie ścieżki dla udostępnionego folderu kopii zapasowej bazy danych. Kliknij przycisk **Dalej**.

1. W **nazwę, opis i ustawienia** Sprawdź nazwę udziału i ścieżki. Kliknij przycisk **Dalej**.

1. Na **uprawnienia folderu udostępnionego** ustaw **dostosowywać uprawnienia**. Kliknij przycisk **niestandardowe...** .

1. Na **dostosowywanie uprawnień**, kliknij przycisk **Dodaj...** .

1. Upewnij się, że konta usług SQL Server i SQL Server Agent oba serwery mają pełną kontrolę.

   ![Nowy udział](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Kliknij przycisk **OK**.

1. W **uprawnienia folderu udostępnionego**, kliknij przycisk **Zakończ**. Kliknij przycisk **Zakończ** ponownie.  

### <a name="take-a-full-backup-of-the-database"></a>Wykonać pełną kopię zapasową bazy danych

Należy utworzyć kopię zapasową nową bazę danych, aby zainicjować łańcuch dziennika. Jeśli nie wykonasz kopii zapasowej nową bazę danych, nie można uwzględnić w grupie dostępności.

1. W **Eksplorator obiektów**, kliknij prawym przyciskiem myszy bazę danych, wskaż **zadania...** , kliknij przycisk **Utwórz kopię zapasową**.

1. Kliknij przycisk **OK** do wykonania pełnej kopii zapasowej do domyślnej lokalizacji kopii zapasowej.

## <a name="create-the-availability-group"></a>Tworzenie grupy dostępności
Teraz można przystąpić do konfigurowania grupy dostępności wykonując następujące czynności:

* Tworzenie bazy danych na pierwszym serwerze SQL.
* Wykonaj pełną kopię zapasową i kopii zapasowej dziennika transakcji bazy danych
* Przywracanie pełnej i kopie zapasowe dzienników do drugiego programu SQL Server za pomocą **NORECOVERY** opcji
* Tworzenie grupy dostępności (**AG1**) za pomocą zatwierdzania synchronicznego, automatycznej pracy awaryjnej i do odczytu replikach pomocniczych

### <a name="create-the-availability-group"></a>Tworzenie grupy dostępności:

1. W sesji usług pulpitu zdalnego do pierwszego serwera SQL. W **Eksplorator obiektów** w programie SSMS kliknij prawym przyciskiem myszy **wysokiej dostępności (AlwaysOn)** i kliknij przycisk **Kreatora nowej grupy dostępności**.

    ![Uruchom Kreatora nowej grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. W **wprowadzenie** kliknij **dalej**. W **Określ nazwę grupy dostępności** strony, na przykład wpisz nazwę grupy dostępności **AG1**w **Nazwa grupy dostępności**. Kliknij przycisk **Dalej**.

    ![Kreatora nowej grupy dostępności, podaj nazwę grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. W **wybierz bazy danych** stronie, wybierz bazę danych i kliknij przycisk **dalej**.

   >[!NOTE]
   >Baza danych spełnia wymagania wstępne grupy dostępności, ponieważ co najmniej jedną pełną kopię zapasową wykonano w zamierzonej replice podstawowej.

   ![Kreatora nowej grupy dostępności, wybierz bazę danych](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. W **Określanie replik** kliknij **dodać repliki**.

   ![Kreatora nowej grupy dostępności, określ repliki](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. **Połącz z serwerem** pojawi się okno dialogowe. Wpisz nazwę drugiego serwera w **nazwy serwera**. Kliknij przycisk **Połącz**.

   Ponownie **Określanie replik** stronie powinien zostać wyświetlony drugi serwer, na liście **replik dostępności**. Skonfiguruj replik w następujący sposób.

   ![Kreatora nowej grupy dostępności, określ repliki (zaawansowane)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Kliknij przycisk **punktów końcowych** się końcowego dublowania bazy danych dla tej grupy dostępności. Użyj tego samego portu, którego użyto podczas ustawiania [reguły zapory dla punktów końcowych dublowania bazy danych](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Kreatora nowej grupy dostępności, wybierz początkową synchronizację danych](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. W **Wybierz początkową synchronizację danych** wybierz **pełne** i określ udostępnionej lokalizacji sieciowej. W przypadku lokalizacji, użyj [udziału kopii zapasowej, który został utworzony](#backupshare). W przykładzie, mógł on  **\\ \\ \<pierwszego serwera SQL\>\Backup\\** . Kliknij przycisk **Dalej**.

   >[!NOTE]
   >Pełna synchronizacja ma pełną kopię zapasową bazy danych w pierwszym wystąpieniu programu SQL Server i przywrócenie go do drugiego wystąpienia. Pełna synchronizacja w przypadku dużych baz danych nie jest zalecane, ponieważ może potrwać dłuższy czas. Teraz można zmniejszyć ręcznie wykonywanie kopii zapasowej bazy danych i przywracanie jej przy użyciu `NO RECOVERY`. Jeśli baza danych jest już przywrócona z `NO RECOVERY` na drugim serwerze SQL przed rozpoczęciem konfigurowania grupy dostępności, wybierz **tylko Dołącz**. Jeśli chcesz wykonać kopię zapasową po skonfigurowaniu grupy dostępności, wybierz polecenie **Pomiń początkową synchronizację danych**.

    ![Kreatora nowej grupy dostępności, wybierz początkową synchronizację danych](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. W **weryfikacji** kliknij **dalej**. Ta strona powinna wyglądać podobnie do poniższej ilustracji:

    ![Kreatora nowej grupy dostępności, sprawdzanie poprawności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Istnieje ostrzeżenie dla konfiguracji odbiornika, ponieważ nie skonfigurowano odbiornika grupy dostępności. Można zignorować to ostrzeżenie, ponieważ na maszynach wirtualnych platformy Azure Utwórz odbiornik po utworzeniu modułu równoważenia obciążenia platformy Azure.

10. W **Podsumowanie** kliknij **Zakończ**, zaczekaj, aż Kreator skonfiguruje nowe grupy dostępności. W **postępu** strony, możesz kliknąć **szczegółowe** Aby wyświetlić szczegółowe informacje o postępie. Po zakończeniu pracy kreatora sprawdź **wyniki** stronę, aby sprawdzić, czy grupy dostępności został pomyślnie utworzony.

     ![Wyniki kreatora nowej grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Kliknij przycisk **Zamknij** aby zakończyć pracę kreatora.

### <a name="check-the-availability-group"></a>Sprawdź grupy dostępności

1. W **Eksplorator obiektów**, rozwiń węzeł **wysokiej dostępności (AlwaysOn)** , następnie rozwiń **grup dostępności**. Powinien zostać wyświetlony nowej grupy dostępności, w tym kontenerze. Kliknij prawym przyciskiem myszy grupę dostępności, a następnie kliknij przycisk **wyświetlić pulpit nawigacyjny**.

   ![Pokaż pulpit nawigacyjny grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   Twoje **pulpitu nawigacyjnego z funkcji AlwaysOn** powinien wyglądać podobnie do poniższego.

   ![Pulpit nawigacyjny grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Możesz zobaczyć replik, tryb pracy awaryjnej każdej repliki i stan synchronizacji.

2. W **Menedżera klastra trybu Failover**, kliknij klaster. Wybierz **role**. Nazwa grupy dostępności, którego użyłeś jest rolą w klastrze. Tej grupy dostępności nie ma adresu IP dla połączeń klienta, ponieważ nie skonfigurowała odbiornik. Skonfiguruj odbiornik po utworzeniu usługi Azure load balancer.

   ![W Menedżerze klastra trybu Failover grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Nie należy do grupy dostępności w Menedżerze klastra trybu Failover w tryb failover. Wszystkie operacje trybu failover, należy wykonać z poziomu **pulpitu nawigacyjnego z funkcji AlwaysOn** w programie SSMS. Aby uzyskać więcej informacji, zobacz [ograniczenia na przy użyciu Menedżera klastra trybu Failover z użyciem grup dostępności](https://msdn.microsoft.com/library/ff929171.aspx).
    >

W tym momencie masz grupy dostępności przy użyciu repliki na dwa wystąpienia programu SQL Server. Grupa dostępności można przenosić między wystąpieniami. Nie można połączyć do grupy dostępności jeszcze, ponieważ nie masz odbiornik. W usłudze Azure virtual machines odbiornik wymaga modułu równoważenia obciążenia. Następnym krokiem jest do utworzenia modułu równoważenia obciążenia na platformie Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Tworzenie modułu równoważenia obciążenia na platformie Azure

Na maszynach wirtualnych platformy Azure grupy dostępności programu SQL Server wymaga modułu równoważenia obciążenia. Moduł równoważenia obciążenia zawiera adresy IP do odbiorników grup dostępności i klastra trybu Failover systemu Windows Server. Ta sekcja zawiera podsumowanie sposobu tworzenia modułu równoważenia obciążenia w witrynie Azure portal.

Usługi Azure Load Balancer może być standardowego modułu równoważenia obciążenia lub podstawowego modułu równoważenia obciążenia. Standardowy moduł równoważenia obciążenia ma więcej funkcji niż podstawowy moduł równoważenia obciążenia. Dla grupy dostępności standardowego modułu równoważenia obciążenia jest wymagany, jeśli używasz strefie dostępności (zamiast zestawu dostępności). Aby uzyskać więcej informacji dotyczących różnicy między typami modułu równoważenia obciążenia, zobacz [porównania SKU modułu równoważenia obciążenia](../../../load-balancer/load-balancer-overview.md#skus).

1. W witrynie Azure portal przejdź do grupy zasobów, w których są serwery SQL i kliknij przycisk **+ Dodaj**.
1. Wyszukaj **moduł równoważenia obciążenia**. Wybierz moduł równoważenia obciążenia, opublikowane przez firmę Microsoft.

   ![W Menedżerze klastra trybu Failover grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Kliknij pozycję **Utwórz**.
1. Skonfiguruj następujące parametry dla modułu równoważenia obciążenia.

   | Ustawienie | Pole |
   | --- | --- |
   | **Nazwa** |Na przykład użyj nazwy tekstu dla modułu równoważenia obciążenia **sqlLB**. |
   | **Typ** |Wewnętrzne |
   | **Sieć wirtualna** |Użyj nazwy sieci wirtualnej platformy Azure. |
   | **Podsieć** |Użyj nazwy podsieci, należącym do maszyny wirtualnej.  |
   | **Przypisywanie adresów IP** |Static |
   | **Adres IP** |Użyj adresu dostępne z podsieci. Ten adres na użytek z odbiornikiem grupy dostępności. Należy pamiętać, że to różni się od adresu IP klastra.  |
   | **Subskrypcja** |Użyj tej samej subskrypcji co maszyna wirtualna. |
   | **Lokalizacja** |Użyj tej samej lokalizacji co maszyna wirtualna. |

   Blok Azure portal powinien wyglądać następująco:

   ![Tworzenie modułu równoważenia obciążenia](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Kliknij przycisk **Utwórz**, aby utworzyć moduł równoważenia obciążenia.

Aby skonfigurować moduł równoważenia obciążenia, musisz utworzyć pulę zaplecza, sondy i ustawić reguły równoważenia obciążenia. Wykonaj w witrynie Azure portal.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Dodawanie puli zaplecza dla odbiornika grupy dostępności

1. W witrynie Azure portal przejdź do grupy dostępności. Może być konieczne odświeżenie widoku, aby zobaczyć nowo utworzony moduł równoważenia obciążenia.

   ![Znajdź moduł równoważenia obciążenia w grupie zasobów](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Kliknij moduł równoważenia obciążenia, kliknij przycisk **pule zaplecza**i kliknij przycisk **+ Dodaj**.

1. Wpisz nazwę dla puli zaplecza.

1. Skojarz puli wewnętrznej bazy danych z zestawu dostępności, która zawiera maszyny wirtualne.

1. W obszarze **docelowe konfiguracje protokołu IP sieci**, sprawdź **maszyny WIRTUALNEJ** i wybrać obu maszyn wirtualnych, które będą obsługiwać repliki grupy dostępności. Nie dołączaj serwera monitora udziału plików.

   >[!NOTE]
   >Jeśli obie maszyny wirtualne nie są określone, połączenia powiedzie się tylko do repliki podstawowej.

1. Kliknij przycisk **OK** do utworzenia puli zaplecza.

### <a name="set-the-probe"></a>Ustaw sondy

1. Kliknij moduł równoważenia obciążenia, kliknij przycisk **sondy kondycji**i kliknij przycisk **+ Dodaj**.

1. Ustaw sondy kondycji odbiornika w następujący sposób:

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Text | SQLAlwaysOnEndPointProbe |
   | **Protokół** | Wybierz protokół TCP | TCP |
   | **Port** | Wszelkie nieużywanego portu | 59999 |
   | **Interval**  | Czas między próbami sondy w sekundach |5 |
   | **Próg złej kondycji** | Liczba kolejnych niepowodzeń sondy musi wystąpić na maszynie wirtualnej zostały uznane za złej kondycji  | 2 |

1. Kliknij przycisk **OK** można ustawić sondy kondycji.

### <a name="set-the-load-balancing-rules"></a>Ustaw reguły równoważenia obciążenia

1. Kliknij moduł równoważenia obciążenia, kliknij przycisk **reguły równoważenia obciążenia**i kliknij przycisk **+ Dodaj**.

1. Ustaw odbiornika równoważenia zasady w następujący sposób.

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Text | SQLAlwaysOnEndPointListener |
   | **Adres IP frontonu** | Wybierz adres |Użyj adresu, który został utworzony podczas tworzenia modułu równoważenia obciążenia. |
   | **Protokół** | Wybierz protokół TCP |TCP |
   | **Port** | Na użytek port odbiornika grupy dostępności | 1433 |
   | **Port zaplecza** | To pole nie jest używane, gdy pływającego adresu IP ma wartość bezpośrednie serwera return | 1433 |
   | **Probe** |Nazwa określona dla sondy | SQLAlwaysOnEndPointProbe |
   | **Trwałość sesji** | Lista rozwijana | **Brak** |
   | **Limit czasu bezczynności** | Nie zamykaj połączenie TCP minut | 4 |
   | **Pływający adres IP (bezpośredni zwrot serwera)** | |Enabled (Włączony) |

   > [!WARNING]
   > Bezpośredni zwrot serwera jest ustawiany podczas tworzenia. Nie można zmienić tej nazwy.

1. Kliknij przycisk **OK** można ustawić reguły równoważenia obciążenia odbiornika.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Dodaj adres IP klastra core dla systemu Windows Server Failover klastra (WSFC)

Adres IP usługi WSFC musi znajdować się na moduł równoważenia obciążenia.

1. W portalu, tego samego modułu równoważenia obciążenia platformy Azure kliknij **konfiguracja adresu IP frontonu** i kliknij przycisk **+ Dodaj**. Użyj adresu IP skonfigurowany dla usługi WSFC w zasoby podstawowe klastra. Ustaw adres IP jako statyczny.

1. W module równoważenia obciążenia, kliknij przycisk **sondy kondycji**i kliknij przycisk **+ Dodaj**.

1. Ustaw sondy kondycji adres IP podstawowej usługi WSFC klastra w następujący sposób:

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Text | WSFCEndPointProbe |
   | **Protokół** | Wybierz protokół TCP | TCP |
   | **Port** | Wszelkie nieużywanego portu | 58888 |
   | **Interval**  | Czas między próbami sondy w sekundach |5 |
   | **Próg złej kondycji** | Liczba kolejnych niepowodzeń sondy musi wystąpić na maszynie wirtualnej zostały uznane za złej kondycji  | 2 |

1. Kliknij przycisk **OK** można ustawić sondy kondycji.

1. Ustaw reguły równoważenia obciążenia. Kliknij przycisk **reguły równoważenia obciążenia**i kliknij przycisk **+ Dodaj**.

1. Ustaw klaster core IP adres reguły równoważenia obciążenia w następujący sposób.

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Text | WSFCEndPoint |
   | **Adres IP frontonu** | Wybierz adres |Użyj adresu, który został utworzony podczas konfigurowania adresu IP usługi WSFC. To różni się od adresu IP odbiornika |
   | **Protokół** | Wybierz protokół TCP |TCP |
   | **Port** | Użyj portu dla adresu IP klastra. Jest dostępny port, który nie jest używany przez port sondy odbiornika. | 58888 |
   | **Port zaplecza** | To pole nie jest używane, gdy pływającego adresu IP ma wartość bezpośrednie serwera return | 58888 |
   | **Probe** |Nazwa określona dla sondy | WSFCEndPointProbe |
   | **Trwałość sesji** | Lista rozwijana | **Brak** |
   | **Limit czasu bezczynności** | Nie zamykaj połączenie TCP minut | 4 |
   | **Pływający adres IP (bezpośredni zwrot serwera)** | |Enabled (Włączony) |

   > [!WARNING]
   > Bezpośredni zwrot serwera jest ustawiany podczas tworzenia. Nie można zmienić tej nazwy.

1. Kliknij przycisk **OK** można ustawić reguły równoważenia obciążenia.

## <a name="configure-listener"></a> Konfigurowanie odbiornika

W następnej kolejności wykonaj jest Konfigurowanie odbiornika grupy dostępności w klastrze trybu failover.

> [!NOTE]
> W tym samouczku przedstawiono sposób tworzenia z pojedynczego odbiornika — jeden adres IP wewnętrznego modułu równoważenia obciążenia. Aby utworzyć co najmniej jednego odbiornika przy użyciu co najmniej jeden adres IP, zobacz [Create Availability Group listener i load balancer | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Ustaw port odbiornika

W SQL Server Management Studio należy ustawić na port odbiornika.

1. Uruchom program SQL Server Management Studio i Połącz z repliką podstawową.

1. Przejdź do **AlwaysOn wysokiej dostępności** | **grup dostępności** | **odbiorników grup dostępności**.

1. Powinna zostać wyświetlona nazwa odbiornika, który został utworzony w Menedżerze klastra trybu Failover. Kliknij prawym przyciskiem myszy odbiornik o nazwie, a następnie kliknij przycisk **właściwości**.

1. W **portu** polu Określ numer portu odbiornika grupy dostępności. 1433 jest domyślnym, kliknij przycisk **OK**.

Masz teraz grupy dostępności programu SQL Server w maszynach wirtualnych platformy Azure działa w trybie usługi Resource Manager.

## <a name="test-connection-to-listener"></a>Testuj połączenie z odbiornikiem

Aby przetestować połączenie:

1. Protokół RDP do programu SQL Server, która znajduje się w tej samej sieci wirtualnej, ale nie jest właścicielem repliki. Można użyć programu SQL Server w klastrze.

1. Użyj **sqlcmd** narzędzie, aby przetestować połączenie. Na przykład, poniższy skrypt ustanawia **sqlcmd** połączenie z repliką podstawową, za pośrednictwem odbiornika z uwierzytelnianiem Windows:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Jeśli odbiornik używa portu innego niż domyślny portu (1433), określ numer portu w parametrach połączenia. Na przykład następujące polecenie sqlcmd łączy odbiornika na porcie 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

SQLCMD automatycznie nawiązania połączenia z dowolnego wystąpienia programu SQL Server obsługuje replikę podstawową.

> [!TIP]
> Upewnij się, że port, który określisz jest otwarty na zaporze oba serwery SQL. Oba serwery wymagają regułę ruchu przychodzącego dla portu TCP, którego używasz. Aby uzyskać więcej informacji, zobacz [apletu Dodaj lub Edytuj regułę zapory](https://technet.microsoft.com/library/cc753558.aspx).

## <a name="next-steps"></a>Kolejne kroki

- [Dodaj adres IP do modułu równoważenia obciążenia dla drugiej grupy dostępności](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
