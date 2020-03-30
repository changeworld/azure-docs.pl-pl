---
title: 'Samouczek: Konfigurowanie grupy dostępności'
description: W tym samouczku pokazano, jak utworzyć grupę dostępności programu SQL Server Always On Availability Group na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 426ba4c0ac84799b4d0e6bf9330508f928437fd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060191"
---
# <a name="tutorial-configure-availability-group-on-azure-sql-server-vm-manually"></a>Samouczek: Ręczne konfigurowanie grupy dostępności na maszynie Wirtualnej programu Azure SQL Server

W tym samouczku pokazano, jak utworzyć grupę dostępności programu SQL Server Always On Availability Group na maszynach wirtualnych platformy Azure. Kompletny samouczek tworzy grupę dostępności z repliką bazy danych na dwóch serwerach SQL.

**Szacowanie czasu:** Trwa około 30 minut, aby zakończyć po spełnieniu wymagań wstępnych.

Diagram ilustruje, co można utworzyć w samouczku.

![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Wymagania wstępne

W samouczku przyjęto założenie, że masz podstawową wiedzę na temat grup dostępności programu SQL Server Always On Availability. Jeśli potrzebujesz więcej informacji, zobacz [Omówienie grup dostępności zawsze włączonych (SQL Server).](https://msdn.microsoft.com/library/ff877884.aspx)

W poniższej tabeli wymieniono wymagania wstępne, które należy wykonać przed rozpoczęciem tego samouczka:

|  |Wymaganie |Opis |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Dwa serwery SQL | - W zestawie dostępności platformy Azure <br/> - W jednej domenie <br/> - Z zainstalowaną funkcją klastrowania trybu failover |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Udział plików dla monitora klastra |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Konto usługi programu SQL Server | Konto domeny |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Konto usługi agenta programu SQL Server | Konto domeny |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Otwarte porty zapory | - SQL Server: **1433** dla domyślnego wystąpienia <br/> - Dublowanie bazy danych punkt końcowy: **5022** lub dowolny dostępny port <br/> - Sonda kondycji adresu IP modułu dyspozycyjności grupy dostępności: **59999** lub dowolny dostępny port <br/> - Sonda kondycji adresu IP modułu ładowania rdzenia klastra: **58888** lub dowolny dostępny port |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Dodawanie funkcji klastrowania trybu failover | Oba serwery SQL wymagają tej funkcji |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Konto domeny instalacji | - Lokalny administrator na każdym serwerze SQL <br/> - Członek programu SQL Server sysadmin stałej roli serwera dla każdego wystąpienia programu SQL Server  |


Przed rozpoczęciem samouczka należy [wykonać wymagania wstępne dotyczące tworzenia grup dostępności zawsze włączone w maszynach wirtualnych platformy Azure.](virtual-machines-windows-portal-sql-availability-group-prereq.md) Jeśli te wymagania wstępne zostały już ukończone, można przejść do [tworzenia klastra](#CreateCluster).

  >[!NOTE]
  > Wiele kroków podanych w tym samouczku można teraz zautomatyzować za pomocą [interfejsu wiersza polecenia platformy Azure SQL VM i](virtual-machines-windows-sql-availability-group-cli.md) [szablonów szybki start platformy Azure.](virtual-machines-windows-sql-availability-group-quickstart-template.md)


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>Tworzenie klastra

Po zakończeniu wymagań wstępnych pierwszym krokiem jest utworzenie klastra trybu failover systemu Windows Server, który zawiera dwa programy SQL Severs i serwer świadka.

1. RDP do pierwszego programu SQL Server przy użyciu konta domeny, który jest administratorem zarówno na serwerach SQL i serwerze monitora.

   >[!TIP]
   >Jeśli po [dokumencie wymagań wstępnych](virtual-machines-windows-portal-sql-availability-group-prereq.md)utworzono konto o nazwie **CORP\Install**. Użyj tego konta.

2. Na pulpicie nawigacyjnym **Menedżera serwera** wybierz pozycję **Narzędzia**, a następnie kliknij pozycję **Menedżer klastrów trybu failover**.
3. W lewym okienku kliknij prawym przyciskiem myszy pozycję **Menedżer klastrów trybu failover,** a następnie kliknij polecenie **Utwórz klaster**.
   ![Tworzenie klastra](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. W Kreatorze tworzenia klastra utwórz klaster z jednym węzłem, przechodząc przez strony z ustawieniami w poniższej tabeli:

   | Strona | Ustawienia |
   | --- | --- |
   | Zanim rozpoczniesz |Użyj ustawień domyślnych |
   | Wybierz serwery |Wpisz pierwszą nazwę programu SQL Server w **aplikacji Enter nazwa serwera** i kliknij przycisk **Dodaj**. |
   | Ostrzeżenie dotyczące sprawdzania poprawności |Wybierz **pozycję Nie. Nie wymaga pomocy technicznej firmy Microsoft dla tego klastra i dlatego nie chcesz uruchamiać testów sprawdzania poprawności. Po kliknięciu przycisku Dalej kontynuuj tworzenie klastra**. |
   | Punkt dostępu do administrowania klastrem |Wpisz nazwę klastra, na przykład **SQLAGCluster1** w **programie Cluster Name**.|
   | Potwierdzenie |Użyj wartości domyślnych, chyba że używasz miejsca do magazynowania. Zobacz notatkę po tej tabeli. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Ustawianie adresu IP klastra trybu failover systemu Windows Server

  > [!NOTE]
  > W systemie Windows Server 2019 klaster tworzy **nazwę serwera rozproszonego** zamiast **nazwy sieciowej klastra**. Jeśli używasz systemu Windows Server 2019, pomiń wszelkie kroki, które odnoszą się do nazwy rdzenia klastra w tym samouczku. Nazwę sieci klastra można utworzyć za pomocą programu [PowerShell](virtual-machines-windows-portal-sql-create-failover-cluster.md#windows-server-2019). Więcej informacji można znaleźć w blogu [Klaster trybu failover: Obiekt sieci klastra.](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) 

1. W **Menedżerze klastrów trybu failover**przewiń w dół do **podstawowego zasobu klastra** i rozwiń szczegóły klastra. Powinny być widoczne zarówno **name** i **adres IP** zasobów w stanie **Niepowodzenie.** Nie można przewieźć zasobu adresu IP do trybu online, ponieważ klaster jest przypisany tym samym adresem IP co sam komputer, dlatego jest to zduplikowany adres.

2. Kliknij prawym przyciskiem myszy nieudany **zasób** **adresu IP,** a następnie kliknij polecenie Właściwości .

   ![Właściwości klastra](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Wybierz **statyczny adres IP** i określ dostępny adres z tej samej podsieci co maszyny wirtualne.

4. W sekcji **Zasoby podstawowe klastra** kliknij prawym przyciskiem myszy nazwę klastra i kliknij polecenie **Przetwórdź w trybie online**. Następnie poczekaj, aż oba zasoby będą w trybie online. Gdy zasób nazwy klastra zostanie udostępniony w trybie online, aktualizuje serwer kontrolera domeny o nowe konto komputera usługi AD. Użyj tego konta usługi AD, aby później uruchomić usługę klastrową Grupy dostępności.

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>Dodawanie innego programu SQL Server do klastra

Dodaj inny program SQL Server do klastra.

1. W drzewie przeglądarki kliknij prawym przyciskiem myszy klaster i kliknij polecenie **Dodaj węzeł**.

    ![Dodawanie węzła do klastra](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. W **Kreatorze dodawania węzłów**kliknij przycisk **Dalej**. Na stronie **Wybierz serwery** dodaj drugi program SQL Server. Wpisz nazwę serwera w **pliku Enter nazwa serwera,** a następnie kliknij przycisk **Dodaj**. Po zakończeniu kliknij przycisk **Dalej**.

1. Na stronie **Ostrzeżenie sprawdzania poprawności** kliknij przycisk **Nie** (w scenariuszu produkcyjnym należy wykonać testy sprawdzania poprawności). Następnie kliknij przycisk **Dalej**.

8. Na stronie **Potwierdzenie,** jeśli używasz miejsca do magazynowania, wyczyść pole wyboru **Dodaj wszystkie kwalifikujące się miejsca do klastra.**

   ![Dodawanie potwierdzenia węzła](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Jeśli używasz miejsca do magazynowania i nie wyeznawaj **zaznaczyć Dodaj wszystkie kwalifikujące się miejsca do klastra,** system Windows odłącza dyski wirtualne podczas procesu klastrowania. W rezultacie nie są one wyświetlane w Menedżerze dysków lub Eksploratorze, dopóki miejsca do magazynowania nie zostaną usunięte z klastra i ponownie dołączone przy użyciu programu PowerShell. Miejsca do magazynowania grupuje wiele dysków w pulach magazynu. Aby uzyskać więcej informacji, zobacz [Miejsca do magazynowania](https://technet.microsoft.com/library/hh831739).

1. Kliknij przycisk **alej**.

1. Kliknij przycisk **Zakończ**.

   Menedżer klastrów trybu failover pokazuje, że klaster ma nowy węzeł i wyświetla go w kontenerze **Węzły.**

10. Wyloguj się z sesji pulpitu zdalnego.

### <a name="add-a-cluster-quorum-file-share"></a>Dodawanie udziału plików kworum klastra

W tym przykładzie klaster systemu Windows używa udziału plików do utworzenia kworum klastra. W tym samouczku użyto kworum większościowego udziału węzła i pliku. Aby uzyskać więcej informacji, zobacz [Opis konfiguracji kworum w klastrze trybu failover](https://technet.microsoft.com/library/cc731739.aspx).

1. Połącz się z serwerem członkowskim monitora udziału plików z sesją pulpitu zdalnego.

1. W **Menedżerze serwera**kliknij pozycję **Narzędzia**. Otwórz **zarządzanie komputerem**.

1. Kliknij pozycję **Foldery udostępnione**.

1. Kliknij prawym przyciskiem myszy **pozycję Udostępnij**i kliknij polecenie **Nowy udział...**.

   ![Nowa akcja](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Tworzenie udziału **za pomocą Kreatora tworzenia folderów udostępnionych.**

1. W **ścieżce folderu**kliknij pozycję **Przeglądaj** i znajdź lub utwórz ścieżkę dla folderu udostępnionego. Kliknij przycisk **alej**.

1. W **polu Nazwa, opis i ustawienia** sprawdź nazwę i ścieżkę udziału. Kliknij przycisk **alej**.

1. W **obszarze Uprawnienia folderów udostępnionych** ustaw **uprawnienia Dostosuj**. Kliknij **pozycję Niestandardowe...**.

1. W **obszarze Dostosowywanie uprawnień**kliknij pozycję **Dodaj...**.

1. Upewnij się, że konto użyte do utworzenia klastra ma pełną kontrolę.

   ![Nowa akcja](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Kliknij przycisk **OK**.

1. W **obszarze Uprawnienia folderów udostępnionych**kliknij pozycję **Zakończ**. Kliknij **ponownie pozycję Zakończ.**  

1. Wyloguj się z serwera

### <a name="configure-cluster-quorum"></a>Konfigurowanie kworum klastra

Następnie ustaw kworum klastra.

1. Połącz się z pierwszym węzłem klastra za pomocą pulpitu zdalnego.

1. W **Menedżerze klastrów trybu failover**kliknij prawym przyciskiem myszy klaster, wskaż **polecenie Więcej akcji**i kliknij polecenie **Konfiguruj ustawienia kworum klastra...**.

   ![Nowa akcja](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. W **kreatorze Konfigurowanie kworum klastra**kliknij przycisk **Dalej**.

1. W **obszarze Wybierz opcję konfiguracji kworum**wybierz pozycję Wybierz monitor **kworum**i kliknij przycisk **Dalej**.

1. W **obszarze Wybierz monitor kworum**kliknij pozycję **Konfiguruj monitor udziału plików**.

   >[!TIP]
   >System Windows Server 2016 obsługuje monitor w chmurze. Jeśli wybierzesz tego typu świadka, nie potrzebujesz monitora udziału plików. Aby uzyskać więcej informacji, zobacz [Wdrażanie monitora w chmurze dla klastra trybu failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). W tym samouczku użyto monitora udziału plików, który jest obsługiwany przez poprzednie systemy operacyjne.

1. W **programie Configure File Share Witness**wpisz ścieżkę utworzonego udziału. Kliknij przycisk **alej**.

1. Sprawdź ustawienia w **stronie Potwierdzenie**. Kliknij przycisk **alej**.

1. Kliknij przycisk **Zakończ**.

Podstawowe zasoby klastra są konfigurowane za pomocą monitora udziału plików.

## <a name="enable-availability-groups"></a>Włączanie grup dostępności

Następnie włącz funkcję **AlwaysOn Availability** Groups. Wykonaj te kroki na obu serwerach SQL.

1. Na ekranie **startowym** uruchom program **SQL Server Configuration Manager**.
2. W drzewie przeglądarki kliknij polecenie **SQL Server Services**, a następnie kliknij prawym przyciskiem myszy usługę SQL Server **(MSSQLSERVER)** i kliknij polecenie **Właściwości**.
3. Kliknij kartę **AlwaysOn High Availability (Zawsze na wysokim poziomie dostępności),** a następnie wybierz pozycję **Włącz grupy dostępności AlwaysOn**w następujący sposób:

    ![Włącz grupy dostępności AlwaysOn](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Kliknij przycisk **Zastosuj**. Kliknij **przycisk OK** w wyskakującym oknie dialogowym.

5. Uruchom ponownie usługę SQL Server.

Powtórz te kroki na innym programie SQL Server.

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

## <a name="create-a-database-on-the-first-sql-server"></a>Tworzenie bazy danych na pierwszym programie SQL Server

1. Uruchom plik RDP do pierwszego programu SQL Server z kontem domeny, który jest członkiem roli serwera stałego sysadmin.
1. Otwórz program SQL Server Management Studio i połącz się z pierwszym programem SQL Server.
7. W **Eksploratorze obiektów**kliknij prawym przyciskiem myszy **pozycję Bazy danych** i kliknij polecenie Nowa **baza danych**.
8. W **pliku Nazwa bazy danych**wpisz **myDB1**, a następnie kliknij przycisk **OK**.

### <a name="create-a-backup-share"></a><a name="backupshare"></a>Tworzenie udziału kopii zapasowej

1. W pierwszym programie SQL Server w **Menedżerze serwera**kliknij pozycję **Narzędzia**. Otwórz **zarządzanie komputerem**.

1. Kliknij pozycję **Foldery udostępnione**.

1. Kliknij prawym przyciskiem myszy **pozycję Udostępnij**i kliknij polecenie **Nowy udział...**.

   ![Nowa akcja](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Tworzenie udziału **za pomocą Kreatora tworzenia folderów udostępnionych.**

1. W **ścieżce folderów**kliknij pozycję **Przeglądaj** i znajdź lub utwórz ścieżkę dla folderu udostępnionego kopii zapasowej bazy danych. Kliknij przycisk **alej**.

1. W **polu Nazwa, opis i ustawienia** sprawdź nazwę i ścieżkę udziału. Kliknij przycisk **alej**.

1. W **obszarze Uprawnienia folderów udostępnionych** ustaw **uprawnienia Dostosuj**. Kliknij **pozycję Niestandardowe...**.

1. W **obszarze Dostosowywanie uprawnień**kliknij pozycję **Dodaj...**.

1. Upewnij się, że konta usługi SQL Server i SQL Server Agent dla obu serwerów mają pełną kontrolę.

   ![Nowa akcja](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Kliknij przycisk **OK**.

1. W **obszarze Uprawnienia folderów udostępnionych**kliknij pozycję **Zakończ**. Kliknij **ponownie pozycję Zakończ.**  

### <a name="take-a-full-backup-of-the-database"></a>Wykonaj pełną kopię zapasową bazy danych

Należy wykonać wysuń nową bazę danych, aby zainicjować łańcuch dziennika. Jeśli nie wykonasz kopii zapasowej nowej bazy danych, nie można jej uwzględnić w grupie dostępności.

1. W **Eksploratorze obiektów**kliknij prawym przyciskiem myszy bazę danych, wskaż **polecenie Zadania...** kliknij przycisk **Zapasowy**.

1. Kliknij **przycisk OK,** aby wykonać pełną kopię zapasową do domyślnej lokalizacji kopii zapasowej.

## <a name="create-the-availability-group"></a>Tworzenie grupy dostępności
Teraz można skonfigurować grupę dostępności, wykonując następujące czynności:

* Utwórz bazę danych na pierwszym programie SQL Server.
* Wykonaj zarówno pełną kopię zapasową, jak i kopię zapasową dziennika transakcji bazy danych
* Przywracanie pełnych i rejestrowanych kopii zapasowych do drugiego programu SQL Server za pomocą opcji **NORECOVERY**
* Tworzenie grupy dostępności **(AG1)** z synchroniczalnym zatwierdzaniem, automatyczną przegrupowaniem awaryjnym i czytelnymi replikami pomocniczymi

### <a name="create-the-availability-group"></a>Utwórz grupę dostępności:

1. Na sesji pulpitu zdalnego do pierwszego programu SQL Server. W **Eksploratorze obiektów** w programie SSMS kliknij prawym przyciskiem myszy **pozycję Zawszezawyższa dostępność** i kliknij polecenie Kreator nowych grup **dostępności**.

    ![Uruchom Kreatora nowej grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. Na stronie **Wprowadzenie** kliknij przycisk **Dalej**. Na stronie **Określanie nazwy grupy dostępności** wpisz nazwę grupy dostępności, na przykład **AG1**, w **polu Nazwa grupy Dostępność**. Kliknij przycisk **alej**.

    ![Nowy kreator AG, określ nazwę AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. Na stronie **Wybierz bazy danych** wybierz bazę danych i kliknij przycisk **Dalej**.

   >[!NOTE]
   >Baza danych spełnia wymagania wstępne dla grupy dostępności, ponieważ została podjęta co najmniej jedna pełna kopia zapasowa w zamierzonej repliki podstawowej.

   ![Nowy kreator AG, wybierz bazy danych](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. Na stronie **Określanie replik** kliknij pozycję **Dodaj replikę**.

   ![Nowy kreator AG, określ repliki](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. Zostanie wyświetlone okno dialogowe **Połącz z serwerem.** Wpisz nazwę drugiego serwera w **nazwie serwera**. Kliknij pozycję **Połącz**.

   Na stronie **Określ repliki** powinien być teraz wyświetlany drugi serwer wymieniony w polu **Repliki dostępności**. Skonfiguruj repliki w następujący sposób.

   ![Nowy kreator AG, określ repliki (ukończone)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Kliknij **pozycję Punkty końcowe,** aby wyświetlić dublowanie bazy danych punktu końcowego dla tej grupy dostępności. Użyj tego samego portu, który był używany podczas ustawiania [reguły zapory dla punktów końcowych dublowania bazy danych](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Nowy Kreator AG, wybierz początkową synchronizację danych](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. Na stronie **Wybierz początkową synchronizację danych** wybierz pozycję **Pełna** i określ udostępnioną lokalizację sieciową. W przypadku lokalizacji użyj [utworzonego udziału kopii zapasowej](#backupshare). W przykładzie ** \\ \\ \<było, Pierwszy\>program\\SQL Server \Backup**. Kliknij przycisk **alej**.

   >[!NOTE]
   >Pełna synchronizacja wykonuje pełną kopię zapasową bazy danych w pierwszym wystąpieniu programu SQL Server i przywraca ją do drugiego wystąpienia. W przypadku dużych baz danych pełna synchronizacja nie jest zalecana, ponieważ może to zająć dużo czasu. Możesz skrócić ten czas, ręcznie wykonać kopię zapasową `NO RECOVERY`bazy danych i przywrócić ją za pomocą programu . Jeśli baza danych została `NO RECOVERY` już przywrócona na drugim serwerze SQL Przed skonfigurowaniem grupy dostępności, wybierz opcję **Dołącz tylko**. Jeśli po skonfigurowaniu grupy dostępności chcesz wykonać kopię zapasową, wybierz polecenie **Pomiń początkową synchronizację danych**.

    ![Nowy Kreator AG, wybierz początkową synchronizację danych](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Na stronie **Sprawdzanie poprawności** kliknij przycisk **Dalej**. Ta strona powinna wyglądać podobnie do następującego obrazu:

    ![Nowy kreator AG, walidacja](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Istnieje ostrzeżenie dotyczące konfiguracji odbiornika, ponieważ nie skonfigurowano odbiornika grupy dostępności. Można zignorować to ostrzeżenie, ponieważ na maszynach wirtualnych platformy Azure można utworzyć odbiornika po utworzeniu modułu równoważenia obciążenia platformy Azure.

10. Na stronie **Podsumowanie** kliknij pozycję **Zakończ**, a następnie poczekaj, aż kreator skonfiguruje nową grupę dostępności. Na stronie **Postęp** można kliknąć więcej **szczegółów,** aby wyświetlić szczegółowy postęp. Po zakończeniu pracy kreatora sprawdź stronę **Wyniki,** aby sprawdzić, czy grupa dostępności została pomyślnie utworzona.

     ![Nowy kreator AG, Wyniki](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Kliknij przycisk **Zamknij** , aby zakończyć działanie kreatora.

### <a name="check-the-availability-group"></a>Sprawdź grupę dostępności

1. W **Eksploratorze obiektów**rozwiń węzeł **AlwaysOn High Availability**( Rozwiń węzeł **Grupy dostępności**). W tym kontenerze powinna zostać wyświetlena nowa grupa dostępności. Kliknij prawym przyciskiem myszy grupę dostępności i kliknij polecenie **Pokaż pulpit nawigacyjny**.

   ![Pokaż pulpit nawigacyjny AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   Twój **pulpit nawigacyjny AlwaysOn** powinien wyglądać podobnie do tego.

   ![Pulpit nawigacyjny AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Można zobaczyć repliki, tryb pracy awaryjnej każdej repliki i stan synchronizacji.

2. W **Menedżerze klastrów trybu failover**kliknij klaster. Wybierz **pozycję Role**. Nazwa grupy dostępności, która została użyta, jest rolą w klastrze. Ta grupa dostępności nie ma adresu IP dla połączeń klientów, ponieważ odbiornik nie został skonfigurowany. Odbiornik zostanie skonfigurowany po utworzeniu modułu równoważenia obciążenia platformy Azure.

   ![AG w Menedżerze klastrów trybu failover](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Nie próbuj przechonąć grupy dostępności w trybie failover z Menedżera klastra trybu failover. Wszystkie operacje pracy awaryjnej powinny być wykonywane z poziomu **pulpitu nawigacyjnego AlwaysOn** w programie SSMS. Aby uzyskać więcej informacji, zobacz [Ograniczenia dotyczące korzystania z Menedżera klastra trybu failover z grupami dostępności](https://msdn.microsoft.com/library/ff929171.aspx).
    >

W tym momencie masz grupę dostępności z replikami na dwóch wystąpieniach programu SQL Server. Grupę dostępności można przenosić między wystąpieniami. Nie można jeszcze połączyć się z grupą dostępności, ponieważ nie masz odbiornika. Na maszynach wirtualnych platformy Azure odbiornik wymaga modułu równoważenia obciążenia. Następnym krokiem jest utworzenie modułu równoważenia obciążenia na platformie Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Tworzenie modułu równoważenia obciążenia na platformie Azure

Na maszynach wirtualnych platformy Azure grupa dostępności programu SQL Server wymaga modułu równoważenia obciążenia. Moduł równoważenia obciążenia przechowuje adresy IP odbiorników grupy dostępności i klastra trybu failover systemu Windows Server. W tej sekcji podsumowano sposób tworzenia modułu równoważenia obciążenia w witrynie Azure portal.

Moduł równoważenia obciążenia platformy Azure może być modułem równoważenia obciążenia standardowego lub podstawowym modułem równoważenia obciążenia. Standardowy moduł równoważenia obciążenia ma więcej funkcji niż podstawowy moduł równoważenia obciążenia. W przypadku grupy dostępności standardowy moduł równoważenia obciążenia jest wymagany, jeśli używasz strefy dostępności (zamiast zestawu dostępności). Aby uzyskać szczegółowe informacje na temat różnicy między typami modułu równoważenia obciążenia, zobacz [Porównanie jednostki SKU modułu równoważenia obciążenia](../../../load-balancer/concepts-limitations.md#skus).

1. W witrynie Azure portal przejdź do grupy zasobów, w której znajdują się serwery SQL i kliknij **przycisk + Dodaj**.
1. Wyszukaj **moduł równoważenia obciążenia**. Wybierz moduł równoważenia obciążenia opublikowany przez firmę Microsoft.

   ![AG w Menedżerze klastrów trybu failover](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Kliknij przycisk **Utwórz**.
1. Skonfiguruj następujące parametry modułu równoważenia obciążenia.

   | Ustawienie | Pole |
   | --- | --- |
   | **Nazwa** |Użyj nazwy tekstowej modułu równoważenia obciążenia, na przykład **sqlLB**. |
   | **Typ** |Wewnętrzny |
   | **Sieć wirtualna** |Użyj nazwy sieci wirtualnej platformy Azure. |
   | **Podsieci** |Użyj nazwy podsieci, w której znajduje się maszyna wirtualna.  |
   | **Przypisanie adresu IP** |Statyczny |
   | **Adres IP** |Użyj dostępnego adresu z podsieci. Użyj tego adresu dla odbiornika grupy dostępności. Należy zauważyć, że różni się to od adresu IP klastra.  |
   | **Subskrypcja** |Użyj tej samej subskrypcji co maszyna wirtualna. |
   | **Lokalizacja** |Użyj tej samej lokalizacji co maszyna wirtualna. |

   Blok portalu Azure powinien wyglądać następująco:

   ![Tworzenie modułu równoważenia obciążenia](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Kliknij **przycisk Utwórz**, aby utworzyć moduł równoważenia obciążenia.

Aby skonfigurować moduł równoważenia obciążenia, należy utworzyć pulę wewnętrznej bazy danych, sondę i ustawić reguły równoważenia obciążenia. Zrób to w witrynie Azure portal.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Dodawanie puli wewnętrznej bazy danych dla detektora grupy dostępności

1. W witrynie Azure portal przejdź do swojej grupy dostępności. Może być konieczne odświeżenie widoku, aby wyświetlić nowo utworzony moduł równoważenia obciążenia.

   ![Znajdowanie modułu równoważenia obciążenia w grupie zasobów](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Kliknij moduł równoważenia obciążenia, kliknij pozycję **Pule wewnętrznej bazy,** a następnie kliknij przycisk **+Dodaj**.

1. Wpisz nazwę puli wewnętrznej bazy danych.

1. Skojarz pulę wewnętrznej bazy danych z zestawem dostępności zawierającym maszyny wirtualne.

1. W obszarze **Konfiguracje adresów IP sieci docelowej**sprawdź **maszynę wirtualną** i wybierz obie maszyny wirtualne, które będą hostować repliki grup dostępności. Nie należy dołączać serwera monitora udziału plików.

   >[!NOTE]
   >Jeśli obie maszyny wirtualne nie są określone, połączenia będą tylko powiodą się z repliką podstawową.

1. Kliknij **przycisk OK,** aby utworzyć pulę wewnętrznej bazy danych.

### <a name="set-the-probe"></a>Ustawianie sondy

1. Kliknij moduł równoważenia obciążenia, kliknij pozycję **Sondy kondycji**i kliknij przycisk **+Dodaj**.

1. Ustaw sondę kondycji odbiornika w następujący sposób:

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Tekst | SqlAlwaysOnEndPointProbe (Narzędzie SQLAlwaysOnEndPointProbe) |
   | **Protokół** | Wybierz pozycję TCP | TCP |
   | **Portu** | Każdy nieużyny port | 59999 |
   | **Interwał**  | Czas między próbami sondy w sekundach |5 |
   | **Niezdrowy próg** | Liczba kolejnych błędów sondy, które muszą wystąpić, aby maszyna wirtualna została uznana za złą w złej kondycji  | 2 |

1. Kliknij **przycisk OK,** aby ustawić sondę kondycji.

### <a name="set-the-load-balancing-rules"></a>Ustawianie reguł równoważenia obciążenia

1. Kliknij moduł równoważenia obciążenia, kliknij pozycję **Reguły równoważenia obciążenia**i kliknij przycisk **+Dodaj**.

1. Ustaw reguły równoważenia obciążenia odbiornika w następujący sposób.

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Tekst | SQLAlwaysOnEndPointListener |
   | **Adres IP frontu** | Wybieranie adresu |Użyj adresu utworzonego podczas tworzenia modułu równoważenia obciążenia. |
   | **Protokół** | Wybierz pozycję TCP |TCP |
   | **Portu** | Użyj portu dla detektora grupy dostępności | 1433 |
   | **Port zaplecza** | To pole nie jest używane, gdy zmienny adres IP jest ustawiony na bezpośredni powrót serwera | 1433 |
   | **Sondy** |Nazwa określona dla sondy | SqlAlwaysOnEndPointProbe (Narzędzie SQLAlwaysOnEndPointProbe) |
   | **Trwałość sesji** | Lista rozwijana | **Brak** |
   | **Limit czasu bezczynności** | Minuty na otwarcie połączenia TCP | 4 |
   | **Przestawny adres IP (bezpośredni powrót serwera)** | |Enabled (Włączony) |

   > [!WARNING]
   > Bezpośredni powrót serwera jest ustawiany podczas tworzenia. Nie można zmienić tej nazwy.

1. Kliknij **przycisk OK,** aby ustawić reguły równoważenia obciążenia odbiornika.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Dodawanie podstawowego adresu IP klastra dla klastra trybu failover systemu Windows Server (WSFC)

Adres IP WSFC również musi znajdować się na modułu równoważenia obciążenia.

1. W portalu na tym samym modułze równoważenia obciążenia platformy Azure kliknij pozycję **Konfiguracja ip zaplecza** i kliknij przycisk **+Dodaj**. Użyj adresu IP skonfigurowany dla WSFC w podstawowych zasobów klastra. Ustaw adres IP jako statyczny.

1. Na modułu równoważenia obciążenia kliknij pozycję **Sondy kondycji**i kliknij przycisk **+Dodaj**.

1. Ustaw sondę kondycji podstawowego adresu IP klastra WSFC w następujący sposób:

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Tekst | WSFCEndPointProbe (WSFCEndPointProbe) |
   | **Protokół** | Wybierz pozycję TCP | TCP |
   | **Portu** | Każdy nieużyny port | 58888 |
   | **Interwał**  | Czas między próbami sondy w sekundach |5 |
   | **Niezdrowy próg** | Liczba kolejnych błędów sondy, które muszą wystąpić, aby maszyna wirtualna została uznana za złą w złej kondycji  | 2 |

1. Kliknij **przycisk OK,** aby ustawić sondę kondycji.

1. Ustaw reguły równoważenia obciążenia. Kliknij **pozycję Wczytywanie reguł równoważenia**i kliknij pozycję **+Dodaj**.

1. Ustaw podstawowe reguły równoważenia obciążenia adresów IP klastra w następujący sposób.

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Tekst | Punkt WSFCEnd |
   | **Adres IP frontu** | Wybieranie adresu |Użyj adresu utworzonego podczas konfigurowania adresu IP WSFC. Różni się to od adresu IP odbiornika |
   | **Protokół** | Wybierz pozycję TCP |TCP |
   | **Portu** | Użyj portu dla adresu IP klastra. Jest to dostępny port, który nie jest używany dla portu sondy odbiornika. | 58888 |
   | **Port zaplecza** | To pole nie jest używane, gdy zmienny adres IP jest ustawiony na bezpośredni powrót serwera | 58888 |
   | **Sondy** |Nazwa określona dla sondy | WSFCEndPointProbe (WSFCEndPointProbe) |
   | **Trwałość sesji** | Lista rozwijana | **Brak** |
   | **Limit czasu bezczynności** | Minuty na otwarcie połączenia TCP | 4 |
   | **Przestawny adres IP (bezpośredni powrót serwera)** | |Enabled (Włączony) |

   > [!WARNING]
   > Bezpośredni powrót serwera jest ustawiany podczas tworzenia. Nie można zmienić tej nazwy.

1. Kliknij **przycisk OK,** aby ustawić reguły równoważenia obciążenia.

## <a name="configure-the-listener"></a><a name="configure-listener"></a>Konfigurowanie odbiornika

Następną rzeczą do zrobienia jest skonfigurowanie odbiornika grupy dostępności w klastrze trybu failover.

> [!NOTE]
> W tym samouczku pokazano, jak utworzyć pojedynczy odbiornik — przy jednym adresie IP równoważenia obciążenia. Aby utworzyć jeden lub więcej odbiorników przy użyciu jednego lub więcej adresów IP, zobacz [Tworzenie odbiornika grupy dostępności i modułu równoważenia obciążenia | Platforma Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Ustawianie portu odbiornika

W programie SQL Server Management Studio ustaw port odbiornika.

1. Uruchom program SQL Server Management Studio i połącz się z repliką podstawową.

1. Przejdź do **pozycji Stalenajedniejszych** | **Availability Groups** | **odbiorników**grup dostępności o wysokiej dostępności .

1. Nazwa odbiornika utworzona w Menedżerze klastrów trybu failover powinna być teraz widoczna. Kliknij prawym przyciskiem myszy nazwę odbiornika i kliknij polecenie **Właściwości**.

1. W polu **Port** określ numer portu dla odbiornika grupy dostępności. 1433 jest wartością domyślną, a następnie kliknij przycisk **OK**.

Masz teraz grupę dostępności programu SQL Server w maszynach wirtualnych platformy Azure uruchomionych w trybie Menedżera zasobów.

## <a name="test-connection-to-listener"></a>Testowanie połączenia ze słuchaczem

Aby przetestować połączenie:

1. RDP do programu SQL Server, który znajduje się w tej samej sieci wirtualnej, ale nie jest właścicielem repliki. Można użyć innego programu SQL Server w klastrze.

1. Użyj narzędzia **sqlcmd,** aby przetestować połączenie. Na przykład następujący skrypt ustanawia połączenie **sqlcmd** z repliką podstawową za pośrednictwem odbiornika za pomocą uwierzytelniania systemu Windows:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Jeśli odbiornik używa portu innego niż port domyślny (1433), określ port w ciągu połączenia. Na przykład następujące polecenie sqlcmd łączy się z odbiornikiem na porcie 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

Połączenie SQLCMD automatycznie łączy się z którymkolwiek wystąpieniem programu SQL Server hostuje replikę podstawową.

> [!TIP]
> Upewnij się, że określony port jest otwarty na zaporze obu serwerów SQL. Oba serwery wymagają reguły przychodzącej dla używanego portu TCP. Aby uzyskać więcej informacji, zobacz [Dodawanie lub edytowanie reguły zapory](https://technet.microsoft.com/library/cc753558.aspx).

## <a name="next-steps"></a>Następne kroki

- [Dodaj adres IP do modułu równoważenia obciążenia dla drugiej grupy dostępności](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
