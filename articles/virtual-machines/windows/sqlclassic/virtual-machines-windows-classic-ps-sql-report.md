---
title: Tworzenie maszyny Wirtualnej z serwerem raportów w trybie macierzystym przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: 'W tym temacie opisano i przedstawiono wdrożenia i konfiguracji serwera raportów usług SQL Server Reporting Services w trybie macierzystym w maszynie wirtualnej platformy Azure. '
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 553af55b-d02e-4e32-904c-682bfa20fa0f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: edfae3a56bc13e4c41a1676bfc0f4e8cf4cd9d30
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Korzystanie z programu PowerShell do tworzenia maszyny wirtualnej platformy Azure z serwerem raportów pracującym w trybie macierzystym
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

W tym temacie opisano i przedstawiono wdrożenia i konfiguracji serwera raportów usług SQL Server Reporting Services w trybie macierzystym w maszynie wirtualnej platformy Azure. Kroki opisane w tym dokumencie Użyj kombinację wymagane ręczne wykonanie czynności, aby utworzyć maszynę wirtualną i skrypt programu Windows PowerShell w celu skonfigurowania usług Reporting Services na maszynie Wirtualnej. Skrypt konfiguracji zawiera otwierania portu zapory dla protokołu HTTP lub HTTPs.

> [!NOTE]
> Jeśli nie wymagają **HTTPS** na serwerze raportów **pominąć krok 2**.
> 
> Po utworzeniu maszyny Wirtualnej w kroku 1, przejdź do sekcji Użyj skryptu do konfigurowania serwera raportów i HTTP. Po uruchomieniu skryptu serwera raportów jest gotowa do użycia.

## <a name="prerequisites-and-assumptions"></a>Wymagania wstępne i założenia
* **Subskrypcja platformy Azure**: Sprawdź liczba rdzeni dostępne w Twojej subskrypcji platformy Azure. Jeśli utworzysz zalecany rozmiar maszyny Wirtualnej **A3**, należy **4** dostępne rdzenie. Jeśli używasz rozmiar maszyny Wirtualnej **A2**, należy **2** dostępne rdzenie.
  
  * Aby sprawdzić limit rdzeni w ramach subskrypcji, w portalu Azure kliknij ustawienia w okienku po lewej stronie, a następnie kliknij przycisk użycia w menu u góry.
  * Aby zwiększyć limit przydziału rdzeni, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/). Uzyskać rozmiaru maszyny Wirtualnej, zobacz [rozmiarów maszyn wirtualnych na platformie Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* **Windows PowerShell do obsługi skryptów**: temacie założono, że masz podstawową wiedzę na temat pracy programu Windows PowerShell. Aby uzyskać więcej informacji o korzystaniu z programu Windows PowerShell zobacz następujące tematy:
  
  * [Uruchamianie środowiska Windows PowerShell w systemie Windows Server](https://technet.microsoft.com/library/hh847814.aspx)
  * [Wprowadzenie do korzystania z programu Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Krok 1: Udostępnić maszynie wirtualnej platformy Azure
1. Przejdź do portalu Azure.
2. Kliknij przycisk **maszyn wirtualnych** w okienku po lewej stronie.
   
    ![maszyny wirtualne Microsoft azure](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)
3. Kliknij przycisk **Nowy**.
   
    ![Przycisk Nowy](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)
4. Kliknij przycisk **z galerii**.
   
    ![Nowa maszyna wirtualna z galerii](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)
5. Kliknij przycisk **SQL Server 2014 RTM Standard — Windows Server 2012 R2** , a następnie kliknij strzałkę, aby kontynuować.
   
    ![Dalej](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
   
    Jeśli potrzebujesz danych usług Reporting Services na funkcji subskrypcji, wybierz **programu SQL Server 2014 RTM Enterprise — Windows Server 2012 R2**. Aby uzyskać więcej informacji o wersjach programu SQL Server i obsługę funkcji, zobacz [funkcje obsługiwane przez wersje programu SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).
6. Na **konfiguracji maszyny wirtualnej** Edytuj następujące pola:
   
   * Jeśli istnieje więcej niż jeden **Data wydania wersji**, wybierz najnowszą wersję.
   * **Nazwa maszyny wirtualnej**: Nazwa komputera jest używany również na następnej stronie konfiguracji jako domyślną nazwę DNS usługi w chmurze. Nazwa DNS musi być unikatowa w usługi Azure. Rozważ skonfigurowanie maszyny Wirtualnej z opisujący maszyny Wirtualnej do czego służy nazwą komputera. Na przykład ssrsnativecloud.
   * **Warstwa**: standardowy
   * **Rozmiar: A3** jest zalecany rozmiar maszyny Wirtualnej dla obciążeń programu SQL Server. Jeśli maszyna wirtualna jest używana tylko jako serwer raportów maszyny Wirtualnej o rozmiarze A2 jest wystarczająca, chyba że serwer raportów napotka duże obciążenie. Dla maszyny Wirtualnej, informacje o cenach, zobacz [cennik maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/).
   * **Nową nazwę użytkownika**: podanej nazwie zostanie utworzona jako administrator na maszynie Wirtualnej.
   * **Nowe hasło** i **potwierdzić**. To hasło służy do nowego konta administratora i zaleca się, że używasz silne hasło.
   * Kliknij przycisk **Dalej**. ![next](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
7. Na następnej stronie można edytować następujących pól:
   
   * **Usługi w chmurze**: Wybierz **Utwórz nową usługę w chmurze**.
   * **Nazwa DNS usługi w chmurze**: jest to publiczna nazwa DNS usługa w chmurze, która jest skojarzona z maszyną Wirtualną. Domyślna nazwa to nazwa została wpisana nazwa maszyny Wirtualnej. Jeśli w kolejnych krokach tego tematu, Utwórz zaufany certyfikat SSL, a następnie nazwa DNS jest używana dla wartości "**wystawiony dla**" certyfikatu.
   * **Region/koligacji grupy/wirtualnej sieci**: Wybierz region najbliższy użytkownikom końcowym.
   * **Konto magazynu**: Użyj konta magazynu wygenerowanej automatycznie.
   * **Zestaw dostępności**: Brak.
   * **Punkty końcowe** zachować **pulpitu zdalnego** i **PowerShell** punktów końcowych, a następnie dodaj końcowy HTTP lub HTTPS, w zależności od środowiska.
     
     * **HTTP**: domyślne porty publiczne i prywatne są **80**. Należy pamiętać, że jeśli korzystanie z prywatnych portu innego niż 80, zmodyfikuj **$HTTPport = 80** w skrypcie http.
     * **HTTPS**: domyślne porty publiczne i prywatne są **443**. Ze względów bezpieczeństwa jest zmienić port prywatny i skonfigurować zapory i serwera raportów używać port prywatny. Aby uzyskać więcej informacji dotyczących punktów końcowych, zobacz [jak się komunikacja między z maszyną wirtualną](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Należy pamiętać, że jeśli użyjesz portu innego niż 443, Zmień parametr **$HTTPsport = 443** w skrypcie HTTPS.
   * Kliknij przycisk Dalej. ![Dalej](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
8. Na ostatniej stronie kreatora, zachowaj ustawienie domyślne **Zainstaluj agenta maszyny Wirtualnej** wybrane. Kroki opisane w tym temacie, nie będą korzystać agenta maszyny Wirtualnej, ale jeśli chcesz zachować tę maszynę Wirtualną, agent maszyny Wirtualnej i rozszerzenia programu umożliwi zwiększenia on CM.  Aby uzyskać więcej informacji dotyczących agenta maszyny Wirtualnej, zobacz [agenta maszyny Wirtualnej i rozszerzenia — część 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Jednym z ad zainstalowanych rozszerzeń domyślne uruchomiona jest rozszerzenie "BGINFO", który wyświetla na pulpicie maszyny Wirtualnej, informacje o systemie, takie jak wewnętrznym adresem IP i wolnego miejsca na dysku.
9. Kliknij polecenie ukończone. ![ok](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)
10. **Stan** maszyny wirtualnej będzie wyświetlany jako **uruchamianie (inicjowania obsługi administracyjnej)** podczas procesu udostępniania i następnie wyświetlana **systemem** gdy maszyna wirtualna jest inicjowana i są gotowe do użycia.

## <a name="step-2-create-a-server-certificate"></a>Krok 2: Utworzenie certyfikatu serwera
> [!NOTE]
> Jeśli nie wymagają protokołu HTTPS na serwerze raportów, możesz **pominąć krok 2** i przejdź do sekcji **użyć skryptu, aby skonfigurować serwer raportowania i HTTP**. Użyć skryptu HTTP, aby szybko skonfigurować serwer raportowania i serwer raportów nie będzie gotowe do użycia.

Aby można było używać protokołu HTTPS na maszynie Wirtualnej, należy zaufany certyfikat SSL. W zależności od scenariusza można użyć jednej z następujących dwóch metod:

* Prawidłowy certyfikat SSL wystawiony przez urząd certyfikacji (CA) i zaufany przez firmę Microsoft. Certyfikaty głównego urzędu certyfikacji są wymagane za pośrednictwem programu certyfikatów głównych firmy Microsoft. Aby uzyskać więcej informacji na temat tego programu, zobacz [systemu Windows i Windows Phone 8 SSL programu certyfikatów głównych (elementu członkowskiego CAs)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) i [wprowadzenie do programu certyfikatów głównych firmy Microsoft](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).
* Certyfikatu z podpisem własnym. Certyfikaty z podpisem własnym nie jest zalecana dla środowisk produkcyjnych.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Do korzystania z certyfikatu utworzony przez zaufany urząd certyfikacji
1. **Żądanie certyfikatu serwera dla witryny sieci Web od urzędu certyfikacji**. 
   
    Generowanie pliku żądania certyfikatu (Certreq.txt), który możesz wysłać do urzędu certyfikacji, lub wygenerować żądanie do urzędu certyfikacji online, można użyć Kreatora certyfikatu serwera sieci Web. Na przykład usług certyfikatów firmy Microsoft w systemie Windows Server 2012. W zależności od poziomu wiarygodności identyfikacji oferowanego przez dany certyfikat serwera jest kilka dni do kilku miesięcy dla urzędu certyfikacji zatwierdzić Twoje żądanie i wysłanie pliku certyfikatu. 
   
    Aby uzyskać więcej informacji na temat żądania certyfikatów serwera zobacz następujące tematy: 
   
   * Użyj [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
   * Narzędzia zabezpieczeń do administrowania systemem Windows Server 2012.
     
     [Narzędzia zabezpieczeń do administrowania systemem Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)
     
     > [!NOTE]
     > **Wystawiony dla** pola zaufany certyfikat SSL powinna być taka sama jak **nazwa DNS usługi w chmurze** używane dla nowej maszyny Wirtualnej.

2. **Zainstaluj certyfikat serwera na serwerze sieci Web**. Serwer sieci Web jest w tym przypadku maszyny Wirtualnej, który jest hostem serwera raportów, a witryna internetowa jest tworzona w kolejnych krokach podczas konfigurowania usług Reporting Services. Aby uzyskać więcej informacji na temat instalowania certyfikatu serwera na serwerze sieci Web za pomocą przystawki MMC certyfikatów, zobacz [zainstalować certyfikat serwera](https://technet.microsoft.com/library/cc740068).
   
    Jeśli chcesz użyć skryptu uwzględnionych w tym temacie, aby konfiguracji serwera raportów, wartość certyfikaty **odcisk palca** są wymagane jako parametr skryptu. Zobacz następną sekcję szczegółowe informacje na temat sposobu uzyskania odcisku palca certyfikatu.
3. Przypisz certyfikat serwera na serwerze raportów. Przypisanie jest ukończone w następnej sekcji, podczas konfigurowania serwera raportów.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Aby użyć certyfikatu z podpisem własnym maszyny wirtualne
Certyfikatu z podpisem własnym został utworzony na maszynie Wirtualnej podczas przydzielania został maszyny Wirtualnej. Certyfikat ma taką samą nazwę jak nazwa DNS maszyny Wirtualnej. W celu uniknięcia błędów certyfikatów, wymagane jest, że certyfikat jest zaufany na samej maszyny Wirtualnej, a także przez wszystkich użytkowników witryny.

1. Aby pochodzi z zaufanego głównego urzędu certyfikacji certyfikatu na lokalnej maszynie Wirtualnej, należy dodać certyfikat do **zaufane główne urzędy certyfikacji**. Poniżej znajduje się podsumowanie kroków wymaganych. Aby uzyskać szczegółowe instrukcje dotyczące sposobu ufać urzędowi certyfikacji, zobacz [zainstalować certyfikat serwera](https://technet.microsoft.com/library/cc740068).
   
   1. W portalu Azure wybierz maszynę Wirtualną, a następnie kliknij przycisk Połącz. W zależności od konfiguracji przeglądarki może być monit o zapisanie pliku RDP do połączenia z maszyną wirtualną.
      
       ![Połącz z maszyną wirtualną azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Użyj nazwy maszyny Wirtualnej użytkownika, nazwę użytkownika i hasło, które zostało skonfigurowane podczas tworzenia maszyny Wirtualnej. 
      
       Na przykład na poniższej ilustracji, Nazwa maszyny Wirtualnej jest **ssrsnativecloud** i nazwa użytkownika jest **testuser**.
      
       ![Nazwa maszyny wirtualnej zawiera logowania](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
   2. Uruchom mmc.exe. Aby uzyskać więcej informacji, zobacz [porady: wyświetlanie certyfikatów w przystawce MMC](https://msdn.microsoft.com/library/ms788967.aspx).
   3. W aplikacji konsoli **pliku** menu Dodaj **certyfikaty** przystawki, wybierz pozycję **konto komputera** po wyświetleniu monitu, a następnie kliknij przycisk **dalej**.
   4. Wybierz **komputera lokalnego** zarządzać, a następnie kliknij przycisk **Zakończ**.
   5. Kliknij przycisk **Ok** , a następnie rozwiń węzeł **certyfikaty - osobiste** węzłów, a następnie kliknij przycisk **certyfikaty**. Certyfikat jest nosi nazwę DNS maszyny wirtualnej i kończy **cloudapp.net**. Kliknij prawym przyciskiem myszy nazwę certyfikatu, a następnie kliknij przycisk **kopiowania**.
   6. Rozwiń węzeł **zaufane główne urzędy certyfikacji** węzła, a następnie kliknij prawym przyciskiem myszy **certyfikaty** , a następnie kliknij przycisk **Wklej**.
   7. Aby zweryfikować, kliknij dwukrotnie nazwę certyfikatu w obszarze **zaufane główne urzędy certyfikacji** i sprawdź, czy nie ma żadnych błędów i wyświetlić certyfikat. Jeśli chcesz użyć skryptu HTTPS uwzględnionych w tym temacie, aby konfiguracji serwera raportów, wartość certyfikaty **odcisk palca** są wymagane jako parametr skryptu. **Aby uzyskać wartość odcisku palca**, wykonaj następujące czynności. Istnieje również próbkę programu PowerShell do pobrania odcisk palca w sekcji [użyć skryptu, aby skonfigurować serwer raportowania i HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
      
      1. Kliknij dwukrotnie nazwę certyfikatu, na przykład ssrsnativecloud.cloudapp.net.
      2. Kliknij przycisk **szczegóły** kartę.
      3. Kliknij przycisk **odcisk palca**. Wartość odcisku palca jest wyświetlana w polu szczegółowe informacje, na przykład a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c fb 2f.
      4. Skopiuj odcisk palca i Zapisz wartość do użycia później lub teraz go edytować.
      5. (*) Przed uruchomieniem skryptu Usuń spacje Between par wartości. Na przykład odcisk palca zauważyć przed będzie teraz a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
      6. Przypisz certyfikat serwera na serwerze raportów. Przypisanie jest ukończone w następnej sekcji, podczas konfigurowania serwera raportów.

Jeśli używasz certyfikatu SSL z podpisem własnym, nazwa certyfikatu już zgodna z nazwą hosta maszyny wirtualnej. W związku z tym DNS na komputerze jest już zarejestrowany globalnie i jest możliwy za pomocą dowolnego klienta.

## <a name="step-3-configure-the-report-server"></a>Krok 3: Konfigurowanie serwera raportów
Ta sekcja przeprowadzi Cię przez konfigurację maszyny Wirtualnej jako serwera raportów usług Reporting Services w trybie macierzystym. Jedną z następujących metod umożliwiają skonfigurowanie serwera raportów:

* Użyj skryptu do konfiguracji serwera raportów
* Umożliwia skonfigurowanie serwera raportów programu Configuration Manager.

Aby uzyskać bardziej szczegółowe kroki, zobacz sekcję [podłączyć się do maszyny wirtualnej, a następnie uruchom Menedżera konfiguracji usług Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Uwierzytelnianie Uwaga:** zalecaną metodą uwierzytelniania jest uwierzytelnianie systemu Windows i jest domyślne uwierzytelnianie usług Reporting Services. Tylko użytkownicy, które są skonfigurowane na maszynie Wirtualnej mogą uzyskiwać dostęp do usług Reporting Services i przypisane do ról usług Reporting Services.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Użyj skryptu do konfigurowania serwera raportów i HTTP
Aby użyć skryptu programu Windows PowerShell do konfigurowania serwera raportów, wykonaj następujące kroki. Konfiguracja obejmuje protokołu HTTP, a nie HTTPS:

1. W portalu Azure wybierz maszynę Wirtualną, a następnie kliknij przycisk Połącz. W zależności od konfiguracji przeglądarki może być monit o zapisanie pliku RDP do połączenia z maszyną wirtualną.
   
    ![Połącz z maszyną wirtualną azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Użyj nazwy maszyny Wirtualnej użytkownika, nazwę użytkownika i hasło, które zostało skonfigurowane podczas tworzenia maszyny Wirtualnej. 
   
    Na przykład na poniższej ilustracji, Nazwa maszyny Wirtualnej jest **ssrsnativecloud** i nazwa użytkownika jest **testuser**.
   
    ![Nazwa maszyny wirtualnej zawiera logowania](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Na Maszynie wirtualnej, należy otworzyć **programu Windows PowerShell ISE** z uprawnieniami administracyjnymi. PowerShell ISE jest instalowany domyślnie w systemie Windows server 2012. Zaleca się, że używasz zamiast standardowego okna programu Windows PowerShell ISE, aby można wkleić skrypt ISE, zmodyfikuj skrypt, a następnie uruchom skrypt.
3. W środowisku Windows PowerShell ISE, kliknij przycisk **widoku** menu, a następnie kliknij przycisk **Pokaż okienko skryptu**.
4. Skopiuj poniższy skrypt, a następnie wklej skryptu w okienku skryptów programu Windows PowerShell ISE.
   
        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
   
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
   
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Report Server Configuration Steps
   
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
   
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
   
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
5. Jeśli utworzono maszynę Wirtualną z portem HTTP innych niż 80, zmodyfikuj parametr $HTTPport = 80.
6. Skrypt jest obecnie skonfigurowany dla usług Reporting Services. Jeśli chcesz uruchomić skrypt dla usług Reporting Services, zmodyfikuj wersję część ścieżki do przestrzeni nazw do "v11" w instrukcji Get-WmiObject.
7. Uruchom skrypt.

**Sprawdzanie poprawności**: Aby sprawdzić, czy działa raportu podstawowe funkcje serwera, zobacz [Sprawdź konfigurację](#verify-the-configuration) później w tym temacie.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Użyj skryptu do konfigurowania serwera raportów i HTTPS
Aby użyć środowiska Windows PowerShell do konfigurowania serwera raportów, wykonaj następujące kroki. Konfiguracja obejmuje protokołu HTTPS, a nie HTTP.

1. W portalu Azure wybierz maszynę Wirtualną, a następnie kliknij przycisk Połącz. W zależności od konfiguracji przeglądarki może być monit o zapisanie pliku RDP do połączenia z maszyną wirtualną.
   
    ![Połącz z maszyną wirtualną azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Użyj nazwy maszyny Wirtualnej użytkownika, nazwę użytkownika i hasło, które zostało skonfigurowane podczas tworzenia maszyny Wirtualnej. 
   
    Na przykład na poniższej ilustracji, Nazwa maszyny Wirtualnej jest **ssrsnativecloud** i nazwa użytkownika jest **testuser**.
   
    ![Nazwa maszyny wirtualnej zawiera logowania](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Na Maszynie wirtualnej, należy otworzyć **programu Windows PowerShell ISE** z uprawnieniami administracyjnymi. PowerShell ISE jest instalowany domyślnie w systemie Windows server 2012. Zaleca się, że używasz zamiast standardowego okna programu Windows PowerShell ISE, aby można wkleić skrypt ISE, zmodyfikuj skrypt, a następnie uruchom skrypt.
3. Aby włączyć uruchamianie skryptów, uruchom następujące polecenie programu Windows PowerShell:
   
        Set-ExecutionPolicy RemoteSigned
   
    Następnie możesz uruchomić następujące polecenie, aby zweryfikować zasady:
   
        Get-ExecutionPolicy
4. W **programu Windows PowerShell ISE**, kliknij przycisk **widoku** menu, a następnie kliknij przycisk **Pokaż okienko skryptu**.
5. Skopiuj poniższy skrypt i wklej go w okienku skryptów programu Windows PowerShell ISE.
   
        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
   
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
   
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Reporting Services Report Server Configuration Steps
   
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
   
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
   
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## 3. Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
   
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
   
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
6. Modyfikowanie **$certificatehash** parametru w skrypcie:
   
   * Jest to **wymagane** parametru. Jeśli nie została zapisana wartość certyfikatu z poprzednich kroków, użyj jednej z następujących metod skopiować wartość skrótu certyfikatu z odciskiem palca certyfikatów.:
     
       Na Maszynie wirtualnej Otwórz program Windows PowerShell ISE, a następnie uruchom następujące polecenie:
     
           dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
     
       Dane wyjściowe będą podobne do następującego. Jeśli skrypt zwraca pusty wiersz, maszyna wirtualna nie ma certyfikatu skonfigurowanego na przykład, zobacz sekcję [do używania certyfikatu z podpisem własnym maszyn wirtualnych](#to-use-the-virtual-machines-self-signed-certificate).
     
     LUB
   * Uruchom mmc.exe na Maszynie wirtualnej, a następnie dodaj **certyfikaty** przystawki.
   * W obszarze **zaufane główne urzędy certyfikacji** węzła kliknij dwukrotnie nazwę certyfikatu. Jeśli używasz certyfikatu z podpisem własnym maszyny wirtualnej, certyfikat jest nosi nazwę DNS maszyny wirtualnej i kończy **cloudapp.net**.
   * Kliknij przycisk **szczegóły** kartę.
   * Kliknij przycisk **odcisk palca**. Wartość odcisku palca jest wyświetlana w polu szczegółowe informacje, na przykład af 11 60 b6 4b 28 8 d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48
   * **Przed uruchomieniem skryptu**, Usuń spacje Between par wartości. Na przykład af1160b64b288d890a8212ff6ba9c3664f319048
7. Modyfikowanie **$httpsport** parametru: 
   
   * Jeśli użyto portu 443 dla protokołu HTTPS punktu końcowego nie konieczne zaktualizowanie tego parametru w skrypcie. W przeciwnym razie użyj wartości port wybrany podczas konfigurowania punktu końcowego prywatnej HTTPS na maszynie Wirtualnej.
8. Modyfikowanie **$DNSName** parametru: 
   
   * Skrypt jest skonfigurowany certyfikat wieloznaczny $DNSName = "+". Jeśli to zrobisz, nie ma konfiguracji dla symbolu wieloznacznego powiązanie certyfikatu, komentarz $DNSName ="+"i Włącz następujący wiersz, odwołanie $DNSNAme pełne, ## $DNSName="$server.cloudapp.net".
     
       Zmień wartość $DNSName, jeśli nie chcesz użyć nazwy DNS maszyny wirtualnej dla usług Reporting Services. Jeśli parametr jest używany, certyfikat musi także użyć tej nazwy i zarejestrować nazwę globalnie na serwerze DNS.
9. Skrypt jest obecnie skonfigurowany dla usług Reporting Services. Jeśli chcesz uruchomić skrypt dla usług Reporting Services, zmodyfikuj wersję część ścieżki do przestrzeni nazw do "v11" w instrukcji Get-WmiObject.
10. Uruchom skrypt.

**Sprawdzanie poprawności**: Aby sprawdzić, czy działa raportu podstawowe funkcje serwera, zobacz [Sprawdź konfigurację](#verify-the-connection) później w tym temacie. Aby zweryfikować certyfikat powiązanie Otwórz wiersz polecenia z uprawnieniami administracyjnymi, a następnie uruchom następujące polecenie:

    netsh http show sslcert

Wynik będzie zawierać następujące czynności:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Umożliwia skonfigurowanie serwera raportów programu Configuration Manager
Jeśli nie chcesz uruchomić skrypt programu PowerShell do konfigurowania serwera raportów, wykonaj kroki opisane w tej sekcji, aby użyć Menedżera konfiguracji usług Reporting Services w trybie macierzystym do konfiguracji serwera raportów.

1. W portalu Azure wybierz maszynę Wirtualną, a następnie kliknij przycisk Połącz. Użyj nazwy użytkownika i hasła skonfigurowanego podczas tworzenia maszyny Wirtualnej.
   
    ![Połącz z maszyną wirtualną azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)
2. Uruchom usługę Windows update i instalować aktualizacje do maszyny Wirtualnej. Jeśli wymagane jest ponowne uruchomienie maszyny wirtualnej, uruchom ponownie maszynę Wirtualną i ponownie połączyć się z maszyną Wirtualną z portalu Azure.
3. W menu Start na maszynie Wirtualnej, wpisz **usług Reporting Services** , a następnie otwórz **Reporting Services Configuration Manager**.
4. Pozostaw wartości domyślne dla **nazwy serwera** i **wystąpienie serwera raportów**. Kliknij przycisk **Połącz**.
5. W okienku po lewej stronie kliknij **adres URL usługi sieci Web**.
6. Domyślnie RS jest skonfigurowany dla protokołu HTTP portu 80 z adresem IP "Wszystkie przypisane". Aby dodać HTTPS:
   
   1. W **certyfikat SSL**: Wybierz certyfikat, którego chcesz użyć, na przykład [Nazwa maszyny Wirtualnej]. cloudapp.net. Jeśli żadne certyfikaty nie są wyświetlane, zobacz sekcję **krok 2: Utworzenie certyfikatu serwera** informacji na temat instalowania i ufać certyfikatowi na maszynie Wirtualnej.
   2. W obszarze **SSL Port**: Wybierz 443. Jeśli prywatnej punkt końcowy HTTPS jest skonfigurowany w maszynie Wirtualnej z inną port prywatny, w tym miejscu użyć tej wartości.
   3. Kliknij przycisk **Zastosuj** i poczekaj na ukończenie tej operacji.
7. W okienku po lewej stronie kliknij **bazy danych**.
   
   1. Kliknij przycisk **zmienić baza danych**e.
   2. Kliknij przycisk **Utwórz nową bazę danych serwera raportów** , a następnie kliknij przycisk **dalej**.
   3. Pozostaw wartość domyślną **nazwy serwera**: co maszyna wirtualna nazwy i pozostaw wartość domyślną **typ uwierzytelniania** jako **bieżącego użytkownika** — **zintegrowane zabezpieczenia**. Kliknij przycisk **Dalej**.
   4. Pozostaw wartość domyślną **Nazwa bazy danych** jako **ReportServer** i kliknij przycisk **dalej**.
   5. Pozostaw wartość domyślną **typ uwierzytelniania** jako **poświadczenia usługi** i kliknij przycisk **dalej**.
   6. Kliknij przycisk **dalej** na **Podsumowanie** strony.
   7. Po zakończeniu konfiguracji kliknij przycisk **Zakończ**.
8. W okienku po lewej stronie kliknij **adres URL Menedżera raportów**. Pozostaw wartość domyślną **katalogu wirtualnego** jako **raporty** i kliknij przycisk **Zastosuj**.
9. Kliknij przycisk **zakończenia** zamknąć Menedżera konfiguracji usług Reporting Services.

## <a name="step-4-open-windows-firewall-port"></a>Krok 4: Port zapory systemu Windows otwórz
> [!NOTE]
> Jeśli używasz skrypty do konfiguracji serwera raportów można pominąć tę sekcję. Skrypt uwzględnione krok, aby otworzyć port zapory. Wartość domyślna jest port 80 dla protokołu HTTP i 443 dla protokołu HTTPS.
> 
> 

Aby nawiązać połączenie zdalne Menedżera raportów lub serwer raportów na maszynie wirtualnej, punkt końcowy protokołu TCP jest wymagany na maszynie Wirtualnej. Jest wymagany do otwierania tego samego portu w zaporze maszyny Wirtualnej. Punkt końcowy został utworzony podczas zainicjowano obsługę administracyjną maszyny Wirtualnej.

Ta sekcja zawiera podstawowe informacje dotyczące sposobu otwierania portu zapory. Aby uzyskać więcej informacji, zobacz [konfigurowania zapory dla dostępu do serwera raportów](https://technet.microsoft.com/library/bb934283.aspx)

> [!NOTE]
> Jeśli skrypt jest używany do konfigurowania serwera raportów, możesz pominąć tę sekcję. Skrypt uwzględnione krok, aby otworzyć port zapory.
> 
> 

Jeśli port prywatny jest skonfigurowany do obsługi protokołu HTTPS innego niż 443, zmodyfikuj odpowiednio następujący skrypt. Aby otworzyć port **443** zaporę systemu Windows, należy wykonać następujące czynności:

1. Otwórz okno programu Windows PowerShell z uprawnieniami administracyjnymi.
2. Jeśli użyto portu innego niż 443, gdy punkt końcowy HTTPS jest skonfigurowane na maszynie Wirtualnej, Aktualizuj port w następujące polecenie, a następnie uruchom polecenie:
   
        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443
3. Po zakończeniu wykonywania polecenia **Ok** jest wyświetlany w wierszu polecenia.

Aby sprawdzić, czy port jest otwarty, Otwórz okno programu Windows PowerShell i uruchom następujące polecenie:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Sprawdź konfigurację
Aby sprawdzić, czy raport podstawowe funkcje serwera działa, otwórz przeglądarkę z uprawnieniami administracyjnymi, a następnie przejdź do następujących raportów ad raportu Menedżera serwera adresów URL:

* Na Maszynie wirtualnej przejdź do adresu URL serwera raportów:
  
        http://localhost/reportserver
* Na Maszynie wirtualnej przejdź do adresu URL Menedżera raportów:
  
        http://localhost/Reports
* Z komputera lokalnego, przejdź do **zdalnego** raport Manager na maszynie Wirtualnej. Aktualizacja nazwy DNS w poniższym przykładzie zależnie od potrzeb. Po wyświetleniu monitu o podanie hasła, należy użyć poświadczeń administratora utworzony zainicjowano obsługę administracyjną maszyny Wirtualnej. Nazwa użytkownika jest [domena]\[nazwa użytkownika] format, w którym domena jest nazwą komputera maszyny Wirtualnej, na przykład ssrsnativecloud\testuser. Jeśli nie używasz HTTP**S**, Usuń **s** w adresie URL. W następnej sekcji informacji na temat tworzenia dodatkowych użytkowników na maszynie Wirtualnej.
  
        https://ssrsnativecloud.cloudapp.net/Reports
* Z komputera lokalnego przejdź do adresu URL serwera raportów zdalnego. Aktualizacja nazwy DNS w poniższym przykładzie zależnie od potrzeb. Jeśli nie używasz protokołu HTTPS, należy usunąć s w adresie URL.
  
        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Tworzenie użytkowników i przypisywania ról
Po konfigurowania i sprawdzania poprawności serwera raportów, typowych zadań administracyjnych jest utworzyć jeden lub więcej użytkowników i przypisywania użytkowników do ról usług Reporting Services. Aby uzyskać więcej informacji zobacz następujące tematy:

* [Utwórz konto użytkownika lokalnego](https://technet.microsoft.com/library/cc770642.aspx)
* [Przyznaj użytkownikowi dostęp do serwera raportów (Menedżer raportów)](https://msdn.microsoft.com/library/ms156034.aspx))
* [Tworzenie i zarządzanie nimi przypisań ról](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Aby utworzyć i opublikować raporty do maszyny wirtualnej platformy Azure
W poniższej tabeli przedstawiono niektóre opcje, aby opublikować istniejących raportów z komputera lokalnego na serwerze raportów hostowanych na maszynie wirtualnej Microsoft Azure:

* **Skrypt RS.exe**: RS.exe Użyj skryptu, aby skopiować elementy raportu z i istniejącego serwera raportów do maszyny wirtualnej programu Microsoft Azure. Aby uzyskać więcej informacji, zobacz sekcję "Tryb macierzysty tryb macierzysty — maszyny wirtualnej platformy Microsoft Azure" w [rs.exe usług Reporting Services przykładowy skrypt w celu migracji zawartości między serwerami raportu](https://msdn.microsoft.com/library/dn531017.aspx).
* **Report Builder**: maszyny wirtualnej zawiera kliknięcie — raz wersji programu Microsoft SQL Server Report Builder. Uruchomienie raportu konstruktora pierwszy na maszynie wirtualnej:
  
  1. Uruchom przeglądarkę z uprawnieniami administracyjnymi.
  2. Przejdź do Menedżera raportów na maszynie wirtualnej, a następnie kliknij przycisk **Report Builder** na Wstążce.
     
     Aby uzyskać więcej informacji, zobacz [instalowanie, odinstalowywanie i obsługa programu Report Builder](https://technet.microsoft.com/library/dd207038.aspx).
* **Programu SQL Server Data Tools: Maszyna wirtualna**: Jeśli utworzono maszynę Wirtualną z programu SQL Server 2012, SQL Server Data Tools jest zainstalowany na maszynie wirtualnej i może służyć do tworzenia **projektów serwera raportów** i raporty na maszynie wirtualnej. SQL Server Data Tools można opublikować raporty do serwera raportów na maszynie wirtualnej.
  
    Jeśli utworzono maszynę Wirtualną z programem SQL server 2014, należy zainstalować Biznesowej programu SQL Server Data Tools — dla programu visual Studio. Aby uzyskać więcej informacji zobacz następujące tematy:
  
  * [Microsoft SQL Server Data Tools - Business Intelligence dla programu Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)
  * [Microsoft SQL Server Data Tools - Business Intelligence dla programu Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)
  * [SQL Server Data Tools i SQL Server Business Intelligence (SSDT BI)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)
* **Program SQL Server Data Tools: Zdalny**: na komputerze lokalnym, Utwórz projekt usług Reporting Services w programie SQL Server Data Tools, który zawiera raporty usług Reporting Services. Skonfiguruj projekt w celu nawiązania połączenia adres URL usługi sieci web.
  
    ![Właściwości projektu narzędzia SSDT dla projektu usług SSRS](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)
* **Użyj skryptu**: użycie skryptu w celu skopiowania zawartości serwera raportów. Aby uzyskać więcej informacji, zobacz [rs.exe usług Reporting Services przykładowy skrypt w celu migracji zawartości między serwerami raportu](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Zminimalizować koszty, jeśli nie używasz maszyny Wirtualnej
> [!NOTE]
> Aby zminimalizować koszty dla maszyn wirtualnych platformy Azure nieużywane, zamknij maszynę Wirtualną z portalu Azure. Jeśli używasz systemu Windows Opcje zasilania wewnątrz maszyny Wirtualnej można zamknąć maszyny Wirtualnej, są nadal naliczane samo dla maszyny Wirtualnej. Aby zmniejszyć koszty, musisz zamykania maszyny Wirtualnej w portalu Azure. Jeśli maszyna wirtualna nie jest już potrzebny, pamiętaj, aby usunąć maszyny Wirtualnej oraz pliki VHD skojarzonego w celu uniknięcia opłat za magazyn. Aby uzyskać więcej informacji, zobacz sekcję — Często zadawane pytania na [maszyny wirtualne — cennik](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>Więcej informacji
### <a name="resources"></a>Zasoby
* Podobne zawartości powiązane z wdrożenia pojedynczego serwera SQL Server Business Intelligence i SharePoint 2013, zobacz [Użyj środowiska Windows PowerShell do tworzenia Azure maszyny Wirtualnej z Biznesowej programu SQL Server i SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx).
* Podobne zawartości powiązane do wdrożenia wielu serwerów programu SQL Server Business Intelligence i programu SharePoint 2013, zobacz [wdrożenia programu SQL Server Business Intelligence w maszynach wirtualnych platformy Azure](https://msdn.microsoft.com/library/dn321998.aspx).
* Aby uzyskać informacje ogólne dotyczące wdrożenia programu SQL Server Business Intelligence w maszynach wirtualnych platformy Azure, zobacz [analizy biznesowej programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-classic-ps-sql-bi.md).
* Aby uzyskać więcej informacji o koszt opłat obliczeń platformy Azure, zobacz kartę maszyn wirtualnych [Azure Kalkulator cen](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Zawartość społeczności
* Aby uzyskać instrukcje krok po kroku dotyczące sposobu tworzenia natywnych usług raportowania serwera raportów tryb bez użycia skryptu, zobacz [Hosting usług SQL Reporting Services na maszynie wirtualnej platformy Azure](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Linki do innych zasobów dla programu SQL Server na maszynach wirtualnych Azure
[Program SQL Server na maszynach wirtualnych platformy Azure — omówienie](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

