---
title: Tworzenie maszyny Wirtualnej za pomocą serwera raportów w trybie macierzystym przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: 'W tym temacie opisano i przedstawiono wdrażanie i konfigurowanie serwera raportów SQL Server Reporting Services w trybie macierzystym w maszynie wirtualnej platformy Azure. '
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
ms.openlocfilehash: 6339b49d0bc9c635457f305dad7b1a075327a1dd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609816"
---
# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Korzystanie z programu PowerShell do tworzenia maszyny wirtualnej platformy Azure z serwerem raportów pracującym w trybie macierzystym
> [!IMPORTANT] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługi Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

W tym temacie opisano i przedstawiono wdrażanie i konfigurowanie serwera raportów SQL Server Reporting Services w trybie macierzystym w maszynie wirtualnej platformy Azure. Kroki opisane w tym dokumencie użyć kombinacji wymagane ręczne wykonanie czynności, aby utworzyć maszynę wirtualną i skrypt programu Windows PowerShell, aby skonfigurować usługi Reporting Services na maszynie Wirtualnej. Skrypt konfiguracji zawiera otwierania portu zapory dla protokołu HTTP lub HTTPs.

> [!NOTE]
> Jeśli nie potrzebujesz **HTTPS** na serwerze raportów **pominąć krok 2**.
> 
> Po utworzeniu maszyny Wirtualnej w kroku 1, przejdź do sekcji Użyj skryptu, aby skonfigurować serwer raportów i HTTP. Po uruchomieniu skryptu serwer raportów jest gotowa do użycia.

## <a name="prerequisites-and-assumptions"></a>Wymagania wstępne i założenia
* **Subskrypcja platformy Azure**: Sprawdź, liczba rdzeni dostępnych w ramach subskrypcji platformy Azure. Jeśli tworzysz zalecany rozmiar maszyny Wirtualnej **A3**, potrzebujesz **4** dostępnych rdzeni. Jeśli używasz rozmiar maszyny Wirtualnej **A2**, potrzebujesz **2** dostępnych rdzeni.
  
  * Aby sprawdzić limit liczby rdzeni subskrypcji w witrynie Azure portal w menu u góry kliknij przycisk Ustawienia w okienku po lewej stronie, a następnie kliknij opcję użycia.
  * Aby zwiększyć limit przydziału rdzeni, skontaktuj się z pomocą [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/). Aby uzyskać informacje o rozmiarze maszyny Wirtualnej, zobacz [rozmiarów maszyn wirtualnych na platformie Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* **Windows PowerShell do obsługi skryptów**: Temat zakłada, że masz podstawową wiedzę na temat pracy programu Windows PowerShell. Aby uzyskać więcej informacji o używaniu programu Windows PowerShell zobacz następujące tematy:
  
  * [Uruchamianie programu Windows PowerShell w systemie Windows Server](https://docs.microsoft.com/powershell/scripting/setup/starting-windows-powershell)
  * [Wprowadzenie do programu Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Krok 1: Aprowizuj maszynę wirtualną platformy Azure
1. Przejdź do witryny Azure portal.
2. Kliknij przycisk **maszyn wirtualnych** w okienku po lewej stronie.
   
    ![microsoft azure virtual machines](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)
3. Kliknij przycisk **Nowy**.
   
    ![przycisk Nowy](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)
4. Kliknij przycisk **z galerii**.
   
    ![nową maszynę wirtualną z galerii](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)
5. Kliknij przycisk **programu SQL Server 2014 RTM Standard — Windows Server 2012 R2** a następnie kliknij strzałkę, aby kontynuować.
   
    ![Dalej](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
   
    Jeśli potrzebujesz danych usług Reporting Services, oparte na funkcji subskrypcji, wybierz opcję **programu SQL Server 2014 RTM Enterprise — Windows Server 2012 R2**. Aby uzyskać więcej informacji o wersjach programu SQL Server i obsługi różnych funkcji, zobacz [funkcje obsługiwane przez wersje programu SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).
6. Na **konfiguracji maszyny wirtualnej** strony, edycji następujących pól:
   
   * Jeśli istnieje więcej niż jedna **daty wydania wersji**, wybierz najnowszą wersję.
   * **Nazwa maszyny wirtualnej**: Nazwa maszyny jest także używana na następnej stronie konfiguracji jako domyślną nazwę DNS usługi w chmurze. Nazwa DNS musi być unikatowa w ramach usługi Azure. Zaleca się skonfigurowanie maszyny Wirtualnej przy użyciu nazwy komputera, która opisuje przeznaczenie maszyny Wirtualnej. Na przykład ssrsnativecloud.
   * **Warstwa**: Standardowa (Standard)
   * **Rozmiar: A3** jest zalecany rozmiar maszyny Wirtualnej dla obciążeń programu SQL Server. Jeśli maszyna wirtualna jest używana tylko jako serwer raportów, rozmiar maszyny Wirtualnej A2 jest wystarczająca, chyba że serwer raportów napotka dużych obciążeń. Aby uzyskać informacje o cenach maszyn wirtualnych, zobacz [cennik usługi Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/).
   * **Nową nazwę użytkownika**: podawanej nazwy jest tworzony z uprawnieniami administratora na maszynie Wirtualnej.
   * **Nowe hasło** i **potwierdzić**. To hasło jest używane dla nowego konta administratora i zalecane jest, że używasz silne hasło.
   * Kliknij przycisk **Dalej**. ![next](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
7. Na następnej stronie można edytować następujących pól:
   
   * **Usługa w chmurze**: Wybierz **Utwórz nową usługę w chmurze**.
   * **Nazwa DNS usługi w chmurze**: Jest to publiczna nazwa DNS usługi w chmurze, który jest skojarzony z maszyną Wirtualną. Domyślną nazwą jest nazwa wpisana dla nazwy maszyny Wirtualnej. Jeśli w kolejnych krokach tego tematu, Utwórz zaufany certyfikat SSL, a następnie nazwa DNS jest używana dla wartości "**wystawiony dla**" certyfikatu.
   * **Region/grupa koligacji/sieci wirtualnej**: Wybierz region najbliżej Twoich użytkowników końcowych.
   * **Konto magazynu**: Użyj konta usługi storage automatycznie wygenerowany.
   * **Zestaw dostępności**: Brak.
   * **Punkty końcowe** zachować **pulpitu zdalnego** i **PowerShell** punktów końcowych, a następnie dodaj HTTP lub HTTPS punktu końcowego, w zależności od środowiska.
     
     * **HTTP**: Domyślne porty publiczne i prywatne są **80**. Należy zauważyć, że jeśli korzystasz z prywatnej portu innego niż 80, zmodyfikuj **$HTTPport = 80** w skrypcie http.
     * **HTTPS**: Domyślne porty publiczne i prywatne są **443**. Ze względów bezpieczeństwa jest zmiana portu prywatnego i skonfigurować zapory i serwera raportów, aby używał portu prywatnego. Aby uzyskać więcej informacji na temat punktów końcowych, zobacz [jak się komunikacja między z maszyną wirtualną](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Należy zauważyć, że w przypadku użycia portu innego niż 443, Zmień parametr **$HTTPsport = 443** w skrypcie protokołu HTTPS.
   * Kliknij przycisk Dalej. ![Dalej](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
8. Na ostatniej stronie kreatora, zachowaj ustawienie domyślne **Zainstaluj agenta maszyny Wirtualnej** wybrane. Kroki opisane w tym temacie nie korzystanie z agenta maszyny Wirtualnej, ale jeśli planowane jest zapewnienie tej maszyny Wirtualnej, agent maszyny Wirtualnej i rozszerzenia pozwala zwiększyć on zarządzania certyfikatami w usłudze.  Aby uzyskać więcej informacji na temat agenta maszyny Wirtualnej, zobacz [agenta maszyny Wirtualnej i rozszerzenia — część 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Jednym z ad zainstalowanych rozszerzeń domyślne działającą jest rozszerzenie "BGINFO", która wyświetla na pulpicie maszyny Wirtualnej, informacje o systemie, takie jak wewnętrzny adres IP i wolnego miejsca na dysku.
9. Kliknij ukończone. ![ok](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)
10. **Stan** maszyny wirtualnej są wyświetlane jako **początkową (aprowizacji)** podczas procesu aprowizacji i następnie wyświetlana **systemem** po maszyna wirtualna jest aprowizowana i gotowe do użycia.

## <a name="step-2-create-a-server-certificate"></a>Krok 2: Utworzenie certyfikatu serwera
> [!NOTE]
> Jeśli nie potrzebujesz HTTPS na serwerze raportów, możesz to zrobić **pominąć krok 2** i przejdź do sekcji **użyć skryptu, aby skonfigurować serwer raportów i HTTP**. Użyj skryptu HTTP, aby szybko skonfigurować serwer raportów i serwera raportów będzie gotowa do użycia.

Aby używać protokołu HTTPS na maszynie Wirtualnej, konieczne będzie zaufany certyfikat SSL. W zależności od scenariusza można użyć jednej z następujących dwóch metod:

* Ważnego certyfikatu SSL wystawionego przez urząd certyfikacji (CA) i zaufany przez firmę Microsoft. Certyfikaty głównego urzędu certyfikacji są wymagane za pośrednictwem programu certyfikatów głównych firmy Microsoft. Aby uzyskać więcej informacji na temat tego programu, zobacz [Windows i Windows Phone 8 SSL programu certyfikatów głównych (członek CAs)](https://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) i [wprowadzenie do programu certyfikatów głównych firmy Microsoft](https://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).
* Certyfikat z podpisem własnym. Certyfikaty z podpisem własnym nie są zalecane w środowiskach produkcyjnych.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Do korzystania z certyfikatu, utworzony przez zaufany urząd certyfikacji (CA)
1. **Żądanie certyfikatu serwera dla witryny sieci Web od urzędu certyfikacji**. 
   
    Można użyć Kreatora certyfikatu serwera sieci Web, aby wygenerować plik żądania certyfikatu (Certreq.txt), który możesz wysłać do urzędu certyfikacji lub wygenerować żądanie do urzędu certyfikacji w trybie online. Na przykład usług certyfikatów firmy Microsoft w systemie Windows Server 2012. W zależności od poziomu wiarygodności identyfikacji oferowanego przez Twój certyfikat serwera jest kilku dni do kilku miesięcy dla urzędu certyfikacji zatwierdzić Twoje zgłoszenie i wysłanie pliku certyfikatu. 
   
    Aby uzyskać więcej informacji na temat żądania certyfikatów serwera zobacz następujące tematy: 
   
   * Użyj [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
   * Narzędzia zabezpieczeń do administrowania systemem Windows Server 2012.
     
     [Narzędzia zabezpieczeń do administrowania systemem Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)
     
     > [!NOTE]
     > **Wystawiony dla** pola zaufany certyfikat SSL powinna być taka sama jak **nazwa DNS usługi w chmurze** została użyta dla nowej maszyny Wirtualnej.

2. **Zainstaluj certyfikat serwera na serwerze sieci Web**. Serwer sieci Web jest w tym przypadku maszyny Wirtualnej, który jest hostem serwera raportów, a witryna internetowa jest tworzona w kolejnych krokach, podczas konfigurowania usług Reporting Services. Aby uzyskać więcej informacji na temat instalowania certyfikatu serwera na serwerze sieci Web za pomocą przystawki MMC certyfikatów, zobacz [zainstalować certyfikat serwera](https://technet.microsoft.com/library/cc740068).
   
    Jeśli chcesz użyć skryptu dołączone do tego tematu, aby skonfigurować serwer raportów, wartość certyfikaty **odcisk palca** są wymagane jako parametru skryptu. Zobacz następną sekcję, aby uzyskać szczegółowe informacje, na temat sposobu uzyskania odcisk palca certyfikatu.
3. Przypisz certyfikat serwera na serwerze raportów. Przypisanie jest wykonywane w następnej sekcji, podczas konfigurowania serwera raportów.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Aby użyć certyfikatu z podpisem własnym maszyn wirtualnych
Certyfikat z podpisem własnym został utworzony na maszynie Wirtualnej, gdy maszyna wirtualna została aprowizowana. Certyfikat ma taką samą nazwę jak nazwę DNS maszyny Wirtualnej. Aby uniknąć błędów certyfikatu, wymagane jest, certyfikat jest zaufany na samą maszynę Wirtualną, a także dla wszystkich użytkowników witryny.

1. Aby zaufany główny urząd certyfikacji certyfikatu na lokalną maszynę Wirtualną, należy dodać certyfikat do **zaufane główne urzędy certyfikacji**. Poniżej znajduje się podsumowanie kroków wymaganych. Aby uzyskać szczegółowe instrukcje dotyczące sposobu ufać urzędowi certyfikacji, zobacz [zainstalować certyfikat serwera](https://technet.microsoft.com/library/cc740068).
   
   1. W witrynie Azure portal wybierz maszynę Wirtualną, a następnie kliknij przycisk Połącz. W zależności od konfiguracji przeglądarki może być monit o zapisanie pliku RDP do połączenia z maszyną wirtualną.
      
       ![Łączenie z maszyną wirtualną platformy azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Użyj nazwy maszyny Wirtualnej użytkownika, nazwę użytkownika i hasła skonfigurowanego podczas tworzenia maszyny Wirtualnej. 
      
       Na przykład na poniższej ilustracji Nazwa maszyny Wirtualnej jest **ssrsnativecloud** i nazwa użytkownika jest **testuser**.
      
       ![Zaloguj się obejmuje nazwę maszyny wirtualnej](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
   2. Uruchom mmc.exe. Aby uzyskać więcej informacji, zobacz [jak: Wyświetlanie certyfikatów za pomocą przystawki programu MMC](https://msdn.microsoft.com/library/ms788967.aspx).
   3. W aplikacji konsolowej **pliku** menu Dodaj **certyfikaty** przystawki, wybierz opcję **konto komputera** po wyświetleniu monitu, a następnie kliknij przycisk **dalej**.
   4. Wybierz **komputera lokalnego** zarządzać, a następnie kliknij przycisk **Zakończ**.
   5. Kliknij przycisk **Ok** a następnie rozwiń węzeł **certyfikaty - osobistych** węzłów, a następnie kliknij przycisk **certyfikaty**. Certyfikat jest nazwana na nazwę DNS maszyny Wirtualnej i kończy się **cloudapp.net**. Kliknij prawym przyciskiem myszy nazwę certyfikatu, a następnie kliknij przycisk **kopiowania**.
   6. Rozwiń **zaufane główne urzędy certyfikacji** węzła i klikając prawym przyciskiem myszy **certyfikaty** a następnie kliknij przycisk **Wklej**.
   7. Aby sprawdzić, kliknij dwukrotnie nazwę certyfikatu w obszarze **zaufane główne urzędy certyfikacji** i sprawdź, czy nie ma żadnych błędów i zobacz swój certyfikat. Jeśli chcesz użyć skryptu HTTPS dołączone do tego tematu, aby skonfigurować serwer raportów, a wartość certyfikaty **odcisk palca** są wymagane jako parametru skryptu. **Aby uzyskać wartość odcisku palca**, wykonaj następujące czynności. Istnieje również przykład programu PowerShell można pobrać odcisku palca w sekcji [użyć skryptu, aby skonfigurować serwer raportów i HTTPS](#use-script-to-configure-the-report-server-and-https).
      
      1. Kliknij dwukrotnie nazwę certyfikatu, na przykład ssrsnativecloud.cloudapp.net.
      2. Kliknij kartę **Szczegóły** .
      3. Kliknij przycisk **odcisk palca**. Wartość odcisku palca jest wyświetlana w polu szczegółowe informacje, na przykład w ramach wystąpienia a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c platformy fb 2f.
      4. Skopiuj odcisk palca i Zapisz wartości do późniejszego lub przeprowadź edycję skryptu teraz.
      5. (*) Przed uruchomieniem skryptu Usuń spacje między par wartości. Na przykład odcisk palca zauważyć przed będzie teraz a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
      6. Przypisz certyfikat serwera na serwerze raportów. Przypisanie jest wykonywane w następnej sekcji, podczas konfigurowania serwera raportów.

Jeśli używasz certyfikatu SSL z podpisem własnym umieszczonej w certyfikacie już pasuje do nazwy hosta maszyny wirtualnej. Dlatego DNS komputera jest już zarejestrowany globalnie i są dostępne z dowolnego klienta.

## <a name="step-3-configure-the-report-server"></a>Krok 3: Konfigurowanie serwera raportów
Ta sekcja przeprowadzi Cię przez skonfigurowanie maszyny Wirtualnej jako serwera raportów usług Reporting Services w trybie macierzystym. Do konfigurowania serwera raportów, można użyć jednej z następujących metod:

* Użyj skryptu, aby skonfigurować serwer raportów
* Użyj programu Configuration Manager, aby skonfigurować serwer raportów.

Aby uzyskać szczegółowe kroki, zobacz sekcję [łączenie z maszyną wirtualną i uruchomić Menedżera konfiguracji usług Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Uwaga uwierzytelniania:** Zalecaną metodą uwierzytelniania jest uwierzytelnianie Windows i jest domyślne uwierzytelnianie usług Reporting Services. Tylko użytkownicy, którzy są skonfigurowane na maszynie Wirtualnej można uzyskać dostęp do usług raportowania i przypisane do ról w usługach Reporting Services.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Użyj skryptu, aby skonfigurować serwer raportów i HTTP
Aby użyć skryptu programu Windows PowerShell do konfigurowania serwera raportów, wykonaj następujące czynności. Ta konfiguracja obejmuje protokołu HTTP, a nie HTTPS:

1. W witrynie Azure portal wybierz maszynę Wirtualną, a następnie kliknij przycisk Połącz. W zależności od konfiguracji przeglądarki może być monit o zapisanie pliku RDP do połączenia z maszyną wirtualną.
   
    ![Łączenie z maszyną wirtualną platformy azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Użyj nazwy maszyny Wirtualnej użytkownika, nazwę użytkownika i hasła skonfigurowanego podczas tworzenia maszyny Wirtualnej. 
   
    Na przykład na poniższej ilustracji Nazwa maszyny Wirtualnej jest **ssrsnativecloud** i nazwa użytkownika jest **testuser**.
   
    ![Zaloguj się obejmuje nazwę maszyny wirtualnej](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Na maszynie Wirtualnej, należy otworzyć **środowiska Windows PowerShell ISE** z uprawnieniami administracyjnymi. W środowisku ISE programu PowerShell jest instalowany domyślnie w systemie Windows server 2012. Zalecane jest, że używasz zamiast standardowego okna programu Windows PowerShell ISE tak, aby użytkownik Wklej skrypt do środowiska ISE, zmodyfikuj skrypt, a następnie uruchom skrypt.
3. W środowisku Windows PowerShell ISE kliknij **widoku** menu, a następnie kliknij przycisk **Pokaż okienko skryptu**.
4. Skopiuj poniższy skrypt, a następnie wklej skrypt w okienku skryptów środowiska Windows PowerShell ISE.
   
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
5. Jeśli maszyna wirtualna została utworzona przy użyciu portu HTTP, innego niż 80, zmodyfikuj parametr $HTTPport = 80.
6. Skrypt jest obecnie skonfigurowana dla usług Reporting Services. Jeśli chcesz uruchomić skrypt dla usług Reporting Services, należy zmodyfikować wersji część ścieżki do przestrzeni nazw do "v11" w instrukcji Get-WmiObject.
7. Uruchom skrypt.

**Sprawdzanie poprawności**: Aby zweryfikować funkcjonalność serwera podstawowego raportu, zobacz [Sprawdź konfigurację](#verify-the-configuration) w dalszej części tego tematu.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Użyj skryptu, aby skonfigurować serwer raportów i protokołu HTTPS
Aby skonfigurować serwer raportów przy użyciu programu Windows PowerShell, wykonaj następujące czynności. Ta konfiguracja obejmuje protokołu HTTPS, a nie HTTP.

1. W witrynie Azure portal wybierz maszynę Wirtualną, a następnie kliknij przycisk Połącz. W zależności od konfiguracji przeglądarki może być monit o zapisanie pliku RDP do połączenia z maszyną wirtualną.
   
    ![Łączenie z maszyną wirtualną platformy azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Użyj nazwy maszyny Wirtualnej użytkownika, nazwę użytkownika i hasła skonfigurowanego podczas tworzenia maszyny Wirtualnej. 
   
    Na przykład na poniższej ilustracji Nazwa maszyny Wirtualnej jest **ssrsnativecloud** i nazwa użytkownika jest **testuser**.
   
    ![Zaloguj się obejmuje nazwę maszyny wirtualnej](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Na maszynie Wirtualnej, należy otworzyć **środowiska Windows PowerShell ISE** z uprawnieniami administracyjnymi. W środowisku ISE programu PowerShell jest instalowany domyślnie w systemie Windows server 2012. Zalecane jest, że używasz zamiast standardowego okna programu Windows PowerShell ISE tak, aby użytkownik Wklej skrypt do środowiska ISE, zmodyfikuj skrypt, a następnie uruchom skrypt.
3. Aby włączyć uruchamianie skryptów, uruchom następujące polecenie programu Windows PowerShell:
   
        Set-ExecutionPolicy RemoteSigned
   
    Następnie możesz uruchomić następujące polecenie, aby sprawdzić zasady:
   
        Get-ExecutionPolicy
4. W **środowiska Windows PowerShell ISE**, kliknij przycisk **widoku** menu, a następnie kliknij przycisk **Pokaż okienko skryptu**.
5. Skopiuj poniższy skrypt, a następnie wklej go w okienku skryptów środowiska Windows PowerShell ISE.
   
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
   
   * Jest to **wymagane** parametru. Jeśli nie zapisał wartość certyfikatu z poprzednich kroków, użyj jednej z następujących metod można skopiować wartość skrótu certyfikatu z odciskiem palca certyfikatów.:
     
       Na maszynie Wirtualnej Otwórz program Windows PowerShell ISE, a następnie uruchom następujące polecenie:
     
           dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
     
       Dane wyjściowe będą podobne do następujących. Jeśli skrypt zwraca pusty wiersz, maszyna wirtualna nie ma certyfikatu skonfigurowanego na przykład, zobacz sekcję [do użycia certyfikatu z podpisem własnym maszyn wirtualnych](#to-use-the-virtual-machines-self-signed-certificate).
     
     LUB
   * Na maszynie Wirtualnej Uruchom mmc.exe, a następnie dodaj **certyfikaty** przystawki.
   * W obszarze **zaufane główne urzędy certyfikacji** węzła, kliknij dwukrotnie nazwę certyfikatu. Jeśli używasz certyfikatu z podpisem własnym maszyny wirtualnej certyfikat jest nazwana na nazwę DNS maszyny Wirtualnej i kończy się **cloudapp.net**.
   * Kliknij kartę **Szczegóły** .
   * Kliknij przycisk **odcisk palca**. Wartość odcisku palca jest wyświetlana w polu szczegółowe informacje, na przykład af 11 60 b6 4b 28 8 d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48
   * **Przed uruchomieniem skryptu**, Usuń spacje między par wartości. Na przykład af1160b64b288d890a8212ff6ba9c3664f319048
7. Modyfikowanie **$httpsport** parametru: 
   
   * Jeśli użyto portu 443 dla punktu końcowego protokołu HTTPS, nie trzeba zaktualizować ten parametr w skrypcie. W przeciwnym razie użyj wartości portu, wybranej podczas konfigurowania prywatnych punktów końcowych HTTPS na maszynie Wirtualnej.
8. Modyfikowanie **$DNSName** parametru: 
   
   * Skrypt jest skonfigurowana dla certyfikatu wieloznacznego $DNSName = "+". Jeśli to zrobisz, nie ma konfiguracji dla powiązania certyfikatu symboli wieloznacznych, komentarz $DNSName ="+"i włączyć następujący wiersz, odwołanie do pełnego $DNSNAme, ## $DNSName="$server.cloudapp.net".
     
       Zmień wartość $DNSName, jeśli nie chcesz używać nazwy DNS maszyny wirtualnej dla usług Reporting Services. Jeśli parametr jest używany, certyfikat należy również użyć tej nazwy, a następnie zarejestrować nazwę globalnie na serwerze DNS.
9. Skrypt jest obecnie skonfigurowana dla usług Reporting Services. Jeśli chcesz uruchomić skrypt dla usług Reporting Services, należy zmodyfikować wersji część ścieżki do przestrzeni nazw do "v11" w instrukcji Get-WmiObject.
10. Uruchom skrypt.

**Sprawdzanie poprawności**: Aby zweryfikować funkcjonalność serwera podstawowego raportu, zobacz Sprawdź sekcji konfiguracji w dalszej części tego tematu. Aby zweryfikować certyfikat powiązania, otwórz wiersz polecenia z uprawnieniami administracyjnymi, a następnie uruchom następujące polecenie:

    netsh http show sslcert

Wynik będzie zawierać następujące czynności:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Konfigurowanie serwera raportów przy użyciu Menedżera konfiguracji
Jeśli nie chcesz uruchomić skrypt programu PowerShell, aby skonfigurować serwer raportów, wykonaj kroki opisane w tej sekcji konfigurowania serwera raportów przy użyciu Menedżera konfiguracji usług Reporting Services w trybie macierzystym.

1. W witrynie Azure portal wybierz maszynę Wirtualną, a następnie kliknij przycisk Połącz. Użyj nazwy użytkownika i hasła, które zostały skonfigurowane podczas tworzenia maszyny Wirtualnej.
   
    ![Łączenie z maszyną wirtualną platformy azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)
2. Uruchom usługę Windows update i zainstalować aktualizacje do maszyny Wirtualnej. Jeśli wymagane jest ponowne uruchomienie maszyny wirtualnej, uruchom ponownie maszynę Wirtualną i ponownie nawiązać połączenia z maszyną wirtualną z witryny Azure portal.
3. W menu Start na maszynie Wirtualnej, wpisz polecenie **usług Reporting Services** , a następnie otwórz **Reporting Services Configuration Manager**.
4. Pozostaw wartości domyślne dla **nazwy serwera** i **wystąpienie serwera raportów**. Kliknij przycisk **Połącz**.
5. W okienku po lewej stronie kliknij **adres URL usługi sieci Web**.
6. Domyślnie RS jest skonfigurowany dla portu HTTP 80 z adresem IP "Wszystkie przypisane". Aby dodać HTTPS:
   
   1. W **certyfikat SSL**: Wybierz certyfikat, którego chcesz użyć, na przykład [VM name]. cloudapp.net. Jeśli żadne certyfikaty nie są wyświetlane, zobacz sekcję **krok 2: Utworzenie certyfikatu serwera** instrukcje dotyczące sposobu instalowania i traktować jako zaufany certyfikat na maszynie Wirtualnej.
   2. W obszarze **SSL Port**: Wybierz 443. Jeśli skonfigurowano prywatnych punktów końcowych HTTPS na maszynie Wirtualnej, używając innego portu prywatnego wartości należy używać w tym miejscu.
   3. Kliknij przycisk **Zastosuj** i poczekaj na zakończenie operacji.
7. W okienku po lewej stronie kliknij **bazy danych**.
   
   1. Kliknij przycisk **zmienić daN**e.
   2. Kliknij przycisk **Tworzenie nowej bazy danych serwera raportów** a następnie kliknij przycisk **dalej**.
   3. Pozostaw wartość domyślną **nazwy serwera**: co maszyna wirtualna nazwy i pozostaw wartość domyślną **typ uwierzytelniania** jako **bieżącego użytkownika** — **zintegrowane zabezpieczenia**. Kliknij przycisk **Dalej**.
   4. Pozostaw wartość domyślną **Nazwa bazy danych** jako **ReportServer** i kliknij przycisk **dalej**.
   5. Pozostaw wartość domyślną **typ uwierzytelniania** jako **poświadczenia usługi** i kliknij przycisk **dalej**.
   6. Kliknij przycisk **dalej** na **Podsumowanie** strony.
   7. Po zakończeniu konfiguracji kliknij przycisk **Zakończ**.
8. W okienku po lewej stronie kliknij **adres URL Menedżera raportów**. Pozostaw wartość domyślną **katalog wirtualny** jako **raporty** i kliknij przycisk **Zastosuj**.
9. Kliknij przycisk **zakończenia** zamknąć Menedżera konfiguracji usług Reporting Services.

## <a name="step-4-open-windows-firewall-port"></a>Krok 4: Otwórz Port zapory Windows
> [!NOTE]
> Jeśli używasz skrypty do konfigurowania serwera raportów można pominąć tę sekcję. Skrypt uwzględnione krok, aby otworzyć port zapory. Wartość domyślna jest port 80 dla protokołu HTTP i portu 443 dla protokołu HTTPS.
> 
> 

Aby połączyć się zdalnie do Menedżera raportów lub serwera raportów na maszynie wirtualnej, punkt końcowy protokołu TCP jest wymagany na maszynie Wirtualnej. Jest wymagany do otwierania tego samego portu w zaporze maszyny Wirtualnej. Punkt końcowy został utworzony, gdy maszyna wirtualna została aprowizowana.

Ta sekcja zawiera podstawowe informacje na temat otwierania portu zapory. Aby uzyskać więcej informacji, zobacz [konfigurowania zapory dla dostępu do serwera raportów](https://technet.microsoft.com/library/bb934283.aspx)

> [!NOTE]
> Jeśli skrypt jest używany do konfigurowania serwera raportów, możesz pominąć tę sekcję. Skrypt uwzględnione krok, aby otworzyć port zapory.
> 
> 

Jeśli port prywatny jest skonfigurowany do obsługi protokołu HTTPS innego niż 443, zmodyfikuj odpowiednio następujący skrypt. Aby otworzyć port **443** zapory Windows, wykonaj następujące czynności:

1. Otwórz okno programu Windows PowerShell z uprawnieniami administracyjnymi.
2. Jeśli użyto portu innego niż 443 w przypadku skonfigurowania punktu końcowego HTTPS na maszynie Wirtualnej, zaktualizuj port w następującym poleceniu, a następnie uruchom polecenie:
   
        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443
3. Po zakończeniu wykonywania polecenia **Ok** jest wyświetlany w wierszu polecenia.

Aby sprawdzić, czy port jest otwarty, Otwórz okno programu Windows PowerShell i uruchom następujące polecenie:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Sprawdzanie konfiguracji
Aby sprawdzić, czy raport podstawowe funkcje serwera działa, otwórz przeglądarkę z uprawnieniami administracyjnymi, a następnie przejdź do następujących Menedżera serwera raportowania ad raport adresów URL:

* Na maszynie Wirtualnej przejdź do adresu URL serwera raportów:
  
        http://localhost/reportserver
* Na maszynie Wirtualnej przejdź do adresu URL Menedżera raportów:
  
        http://localhost/Reports
* Z komputera lokalnego, przejdź do **zdalnego** raportować Menedżera maszyny Wirtualnej. Zaktualizuj nazwę DNS, w poniższym przykładzie zgodnie z potrzebami. Po wyświetleniu monitu o podanie hasła, Użyj poświadczeń administratora, który został utworzony, gdy maszyna wirtualna została aprowizowana. Nazwa użytkownika jest [domena]\[nazwa_użytkownika] format, gdzie domena jest nazwą komputera maszyny Wirtualnej, na przykład ssrsnativecloud\testuser. Jeśli nie używasz protokołu HTTP**S**, Usuń **s** w adresie URL. Zobacz następną sekcję informacji na temat tworzenia dodatkowych użytkowników na maszynie Wirtualnej.
  
        https://ssrsnativecloud.cloudapp.net/Reports
* Z komputera lokalnego przejdź do adresu URL serwera raportów zdalnego. Zaktualizuj nazwę DNS, w poniższym przykładzie zgodnie z potrzebami. Jeśli nie używasz protokołu HTTPS, należy usunąć s w adresie URL.
  
        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Tworzenie użytkowników i przypisywać role
Po konfigurowania i sprawdzania poprawności na serwerze raportów, typowych zadań administracyjnych jest utworzyć jeden lub więcej użytkowników i przypisywania użytkowników do ról w usługach Reporting Services. Aby uzyskać więcej informacji zobacz następujące tematy:

* [Utwórz konto użytkownika lokalnego](https://technet.microsoft.com/library/cc770642.aspx)
* [Przyznaj użytkownikowi dostęp do serwera raportów (Report Manager)](https://msdn.microsoft.com/library/ms156034.aspx))
* [Tworzenie i Zarządzanie przypisaniami ról](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Można tworzyć i publikować raporty do maszyny wirtualnej platformy Azure
Poniższa tabela zawiera podsumowanie opcji, aby opublikować istniejące raporty z komputera lokalnego na serwerze raportów hostowany na maszynie wirtualnej platformy Microsoft:

* **Skrypt RS.exe**: Użyj skryptu RS.exe, aby skopiować elementy raportu z i istniejącego serwera raportów do maszyny wirtualnej usługi Microsoft Azure. Aby uzyskać więcej informacji, zobacz sekcję "Tryb macierzysty na Tryb natywny — Microsoft Azure, maszyny wirtualnej" w [rs.exe usług Reporting Services przykładowy skrypt w celu migracji zawartości między serwerami raportów w usłudze](https://msdn.microsoft.com/library/dn531017.aspx).
* **Report Builder**: Maszyna wirtualna zawiera kliknięcie — raz wersji programu Microsoft SQL Server Report Builder. Uruchomienie raportu konstruktora pierwszej maszynie wirtualnej:
  
  1. Uruchom przeglądarkę z uprawnieniami administracyjnymi.
  2. Przejdź do Menedżera raportów na maszynie wirtualnej, a następnie kliknij przycisk **programu Report Builder** na Wstążce.
     
     Aby uzyskać więcej informacji, zobacz [instalowanie, odinstalowywanie i obsługa programu Report Builder](https://technet.microsoft.com/library/dd207038.aspx).
* **SQL Server Data Tools: VM**:  Jeśli maszyna wirtualna została utworzona przy użyciu programu SQL Server 2012, a następnie SQL Server Data Tools jest zainstalowany na maszynie wirtualnej i może służyć do tworzenia **projekty z serwera raportów** i raporty na maszynie wirtualnej. SQL Server Data Tools mogą publikować raporty serwera raportów na maszynie wirtualnej.
  
    Jeśli utworzono maszynę Wirtualną z programem SQL server 2014, można zainstalować danych narzędzia - Biznesowej programu SQL Server dla programu visual Studio. Aby uzyskać więcej informacji zobacz następujące tematy:
  
  * [Microsoft SQL Server Data Tools — Business Intelligence programu Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)
  * [Microsoft SQL Server Data Tools — Business Intelligence programu Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)
  * [SQL Server Data Tools i SQL Server Business Intelligence (SSDT BI)](https://docs.microsoft.com/sql/ssdt/previous-releases-of-sql-server-data-tools-ssdt-and-ssdt-bi)
* **SQL Server Data Tools: Zdalne**:  Na komputerze lokalnym Utwórz projekt usług Reporting Services w programie SQL Server Data Tools, która zawiera raporty usług Reporting Services. Skonfiguruj projekt, aby nawiązać połączenie z adresu URL usługi internetowej.
  
    ![Program SSDT właściwości projektu dla projektu usług SSRS](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)
* **Użyj skryptu**: Użyj skryptu do kopiowania zawartości serwera raportów. Aby uzyskać więcej informacji, zobacz [rs.exe usług Reporting Services przykładowy skrypt w celu migracji zawartości między serwerami raportów w usłudze](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Zminimalizowanie kosztów, jeśli nie używasz maszyny Wirtualnej
> [!NOTE]
> Aby zminimalizować koszty dla maszyn wirtualnych platformy Azure nieużywane, zamknij maszynę Wirtualną w witrynie Azure portal. Jeśli używasz opcji zasilania Windows wewnątrz maszyny Wirtualnej do zamykania maszyny Wirtualnej, zostanie naliczona tyle samo dla maszyny Wirtualnej. Aby zmniejszyć koszty, musisz zamykanie maszyny Wirtualnej w witrynie Azure portal. Jeśli maszyna wirtualna nie są już potrzebne, pamiętaj, aby usunąć maszyny Wirtualnej i plików VHD skojarzone, aby uniknąć naliczania opłat za magazyn. Aby uzyskać więcej informacji, zobacz sekcję Często zadawane pytania na [Virtual Machines — szczegóły cennika](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>Więcej informacji
### <a name="resources"></a>Zasoby
* Podobne zawartości związane z wdrożeniem pojedynczego serwera, analiza biznesowa programu SQL Server i SharePoint 2013, zobacz [programie Windows PowerShell Utwórz Azure maszyny Wirtualnej za pomocą Biznesowej programu SQL Server i SharePoint 2013](https://blogs.technet.microsoft.com/ptsblog/2013/10/24/use-powershell-to-create-a-windows-azure-vm-with-sql-server-bi-and-sharepoint-2013/).
* Aby uzyskać informacje ogólne dotyczące wdrożeń analiza biznesowa programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [analiza biznesowa programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-classic-ps-sql-bi.md).
* Aby uzyskać więcej informacji o koszcie opłaty obliczeniowe platformy Azure, zobacz kartę maszyny wirtualne [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Zawartość społeczności
* Aby uzyskać instrukcje krok po kroku dotyczące sposobu tworzenia natywnych usług raportowania serwera raportów z trybem bez użycia skryptu, zobacz [hostingu SQL Reporting Service na maszynie wirtualnej platformy Azure](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Linki do innych zasobów programu SQL Server na maszynach wirtualnych platformy Azure
[Program SQL Server na maszynach wirtualnych platformy Azure — omówienie](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

