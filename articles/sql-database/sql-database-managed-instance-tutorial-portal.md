---
title: 'Witryna Azure Portal: tworzenie wystąpienia zarządzanego usługi Azure SQL Database | Microsoft Docs'
description: Utwórz wystąpienie zarządzane usługi Azure SQL Database w sieci wirtualnej i użyj narzędzia SSMS, aby przywrócić kopię zapasową bazy danych Wide World Importers.
keywords: samouczek usługi sql database, tworzenie wystąpienia zarządzanego usługi sql database
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 03/14/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: 774a761465cfd886b85378a35dd43ac656a7ee48
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2018
ms.locfileid: "31004481"
---
# <a name="create-an-azure-sql-database-managed-instance-in-the-azure-portal"></a>Tworzenie wystąpienia zarządzanego usługi Azure SQL Database w witrynie Azure Portal

Ten samouczek przedstawia sposób tworzenia wystąpienia zarządzanego usługi Azure SQL Database (wersja zapoznawcza) przy użyciu witryny Azure Portal w dedykowanej podsieci sieci wirtualnej. Następnie przedstawia sposób nawiązywania połączenia z wystąpieniem zarządzanym przy użyciu programu SQL Server Management Studio na maszynie wirtualnej w tej samej podsieci oraz przywrócenia kopii zapasowej bazy danych przechowywanej w magazynie obiektów platformy Azure do wystąpienia zarządzanego.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

> [!IMPORTANT]
> Listę regionów, w których jest obecnie dostępne wystąpienie zarządzane, można znaleźć w temacie [Migrate your databases to a fully managed service with Azure SQL Database Managed Instance (Migrowanie baz danych do w pełni zarządzanej usługi za pomocą wystąpienia zarządzanego usługi Azure SQL Database)](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/).
 
## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="whitelist-your-subscription"></a>Wpisanie subskrypcji na listę dozwolonych

Wystąpienie zarządzane jest wydawane wstępnie jako warunkowa publiczna wersja zapoznawcza, która wymaga dodania subskrypcji do listy dozwolonych. Jeśli subskrypcja nie jest jeszcze dodana do listy dozwolonych, użyj następujących kroków, aby otrzymać i zaakceptować warunki wersji zapoznawczej oraz wysłać żądanie dodania do listy dozwolonych.

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
2. Zlokalizuj **Wystąpienie zarządzane**, a następnie wybierz pozycję **Wystąpienie zarządzane usługi Azure SQL Database (wersja zapoznawcza)**.
3. Kliknij przycisk **Utwórz**.

   ![tworzenie wystąpienia zarządzanego](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. Wybierz subskrypcję, kliknij pozycję **Warunki dotyczące wersji zapoznawczej**, a następnie podaj żądane informacje.

   ![warunki dotyczące wersji zapoznawczej wystąpienia zarządzanego](./media/sql-database-managed-instance-tutorial/preview-terms.png)

5. Kliknij przycisk **OK** po zakończeniu.

   ![warunki dotyczące wersji zapoznawczej wystąpienia zarządzanego](./media/sql-database-managed-instance-tutorial/preview-approval-pending.png)

   > [!NOTE]
   > Podczas oczekiwania na zatwierdzenie wersji zapoznawczej możesz kontynuować pracę i zakończyć kilka następnych sekcji tego samouczka.

## <a name="configure-a-virtual-network-vnet"></a>Konfigurowanie sieci wirtualnej

W poniższych krokach przedstawiono sposób tworzenia nowej sieci wirtualnej usługi [Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) do użycia dla wystąpienia zarządzanego. Aby uzyskać więcej informacji o konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-vnet-configuration.md).

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
2. Zlokalizuj i kliknij opcję **Sieć wirtualna**, upewnij się, że wybrano opcję **Resource Manager** jako tryb wdrożenia, a następnie kliknij opcję **Utwórz**.

   ![tworzenie sieci wirtualnej](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Wypełnij formularz sieci wirtualnej, podając wymagane informacje. Skorzystaj z informacji w następującej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   |**Nazwa**|Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Przestrzeń adresowa**|Dowolny prawidłowy zakres adresów, np. 10.14.0.0/24|Nazwa adresu sieci wirtualnej w notacji CIDR.|
   |**Subskrypcja**|Twoja subskrypcja|Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions).|
   |**Grupa zasobów**|Dowolna prawidłowa grupa zasobów (nowa lub istniejąca)|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Lokalizacja**|Dowolna prawidłowa lokalizacja| Aby uzyskać informacje na temat regionów, zobacz temat [Regiony systemu Azure](https://azure.microsoft.com/regions/).|
   |**Nazwa podsieci**|Dowolna prawidłowa nazwa podsieci, np. mi_subnet|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Zakres adresów podsieci**|Dowolny prawidłowy adres podsieci, np. 10.14.0.0/28. Użyj przestrzeni adresów podsieci mniejszej niż przestrzeń adresów, aby zapewnić miejsce na tworzenie innych podsieci w ramach tej samej sieci wirtualnej, np. podsieci na potrzeby testowania hostingu / aplikacji klienckich lub podsieci bram na potrzeby nawiązywania połączenia z rozwiązań lokalnych lub innych sieci wirtualnych.|Zakres adresów podsieci w notacji CIDR. Musi zawierać się w przestrzeni adresów sieci wirtualnej|
   |**Punkty końcowe usługi**|Disabled (Wyłączony)|Włączanie co najmniej jednego punktu końcowego usługi dla tej podsieci|
   ||||

   ![formularz tworzenia sieci wirtualnej](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Kliknij przycisk **Utwórz**.

## <a name="create-new-route-table-and-a-route"></a>Tworzenie nowej tabeli tras i trasy

Poniższe kroki przedstawiają sposób utworzenia trasy internetowej następnego przeskoku 0.0.0.0/0.

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
2. Zlokalizuj i kliknij opcję **Tabela tras**, a następnie kliknij opcję **Utwórz** na stronie tabeli tras. 

   ![tworzenie tabeli tras](./media/sql-database-managed-instance-tutorial/route-table-create.png)

3. Wypełnij formularz tabeli tras, podając wymagane informacje. Skorzystaj z informacji w następującej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   |**Nazwa**|Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Subskrypcja**|Twoja subskrypcja|Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions).|
   |**Grupa zasobów**|Wybieranie grupy zasobów, która została utworzona w poprzedniej procedurze|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Lokalizacja**|Wybieranie lokalizacji, która została określona w poprzedniej procedurze| Aby uzyskać informacje na temat regionów, zobacz temat [Regiony systemu Azure](https://azure.microsoft.com/regions/).|
   |**Wyłączanie propagowania tras BCP**|Disabled (Wyłączony)||
   ||||

   ![formularz tworzenia tabeli tras](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

4. Kliknij przycisk **Utwórz**.
5. Po utworzeniu tabeli tras otwórz nowo utworzoną tabelę tras.

   ![tabela tras](./media/sql-database-managed-instance-tutorial/route-table.png)

6. Kliknij pozycję **Trasy**, a następnie kliknij pozycję **Dodaj**.

   ![dodawanie tabeli tras](./media/sql-database-managed-instance-tutorial/route-table-add.png)

7.  Dodaj **trasę następnego przeskoku 0.0.0.0/0** jako **jedyną** trasę, korzystając z informacji zawartych w poniższej tabeli:

    | Ustawienie| Sugerowana wartość | Opis |
    | ------ | --------------- | ----------- |
    |**Nazwa trasy**|Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
    |**Prefiks adresu**|0.0.0.0/0|Docelowy adres IP w notacji CIDR, dla którego trasa ma zastosowanie.|
    |**Typ następnego skoku**|Internet|Następny skok obsługuje pasujące pakiety dla tej trasy|
    |||

    ![trasa](./media/sql-database-managed-instance-tutorial/route.png)

8. Kliknij przycisk **OK**.

## <a name="to-apply-the-route-table-to-the-managed-instance-subnet"></a>Zastosowanie tabeli tras do podsieci wystąpienia zarządzanego

Poniższe kroki pokazują, jak ustawić nową tabelę tras w podsieci wystąpienia zarządzanego.

1. Aby ustawić tabelę tras w podsieci wystąpienia zarządzanego, otwórz sieć wirtualną, która została utworzona wcześniej.
2. Kliknij opcję **Podsieci**, a następnie kliknij podsieć wystąpienia zarządzanego (**mi_subnet** na poniższym zrzucie ekranu).

    ![podsieć](./media/sql-database-managed-instance-tutorial/subnet.png)

11. Kliknij opcję **Tabela tras**, a następnie wybierz pozycję **myMI_route_table**.

    ![ustawianie tabeli tras](./media/sql-database-managed-instance-tutorial/set-route-table.png)

12. Kliknij pozycję **Zapisz**

    ![zapisywanie ustawionej tabeli tras](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)

## <a name="create-a-managed-instance"></a>Tworzenie wystąpienia zarządzanego

Poniższe kroki pokazują, jak utworzyć wystąpienie zarządzane po zatwierdzeniu wersji zapoznawczej.

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
2. Zlokalizuj **Wystąpienie zarządzane**, a następnie wybierz pozycję **Wystąpienie zarządzane usługi Azure SQL Database (wersja zapoznawcza)**.
3. Kliknij przycisk **Utwórz**.

   ![tworzenie wystąpienia zarządzanego](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. Wybierz subskrypcję i upewnij się, że warunki dotyczące wersji zapoznawczej są oznaczone jako **Zaakceptowane**.

   ![zaakceptowana wersja zapoznawcza wystąpienia zarządzanego](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

4. Wypełnij formularz wystąpienia zarządzanego, podając wymagane informacje. Skorzystaj z informacji w następującej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   |**Nazwa wystąpienia zarządzanego**|Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Identyfikator logowania administratora wystąpienia zarządzanego**|Dowolna prawidłowa nazwa użytkownika|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).| 
   |**Hasło**|Dowolne prawidłowe hasło|Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Grupa zasobów**|Grupa zasobów, która została utworzona wcześniej||
   |**Lokalizacja**|Lokalizacja, która została wybrana wcześniej|Aby uzyskać informacje na temat regionów, zobacz temat [Regiony systemu Azure](https://azure.microsoft.com/regions/).|
   |**Sieć wirtualna**|Sieć wirtualna, która została utworzona wcześniej|

   ![formularz tworzenia wystąpienia zarządzanego](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

5. Kliknij pozycję **Warstwa cenowa**, aby ustalić rozmiar zasobów obliczeniowych i magazynu, a także sprawdzić opcje warstw cenowych. Domyślnie wystąpienie zyskuje bezpłatnie 32 GB miejsca do magazynowania, co może nie być wystarczające dla Twoich aplikacji.
6. Użyj suwaków lub pól tekstowych, aby określić ilość pamięci i liczbę rdzeni wirtualnych. 
   ![formularz tworzenia wystąpienia zarządzanego](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

7. Po zakończeniu kliknij pozycję **Zastosuj**, aby zapisać wybór.  
8. Kliknij pozycję **Utwórz**, aby wdrożyć wystąpienie zarządzane.
9. Kliknij ikonę **Powiadomienia**, aby wyświetlić stan wdrożenia.
 
   ![postęp wdrażania](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

9. Kliknij pozycję **Wdrażanie jest w toku**, aby otworzyć okno wystąpienia zarządzanego i dokładniej monitorować postęp wdrażania.
 
   ![postęp wdrażania 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

Podczas wdrażania możesz przejść do następnej procedury.

> [!IMPORTANT]
> W przypadku pierwszego wystąpienia w podsieci czas wdrożenia jest zazwyczaj znacznie dłuższy niż w przypadku kolejnych wystąpień — czasami zakończenie procesu zajmuje ponad 24 godziny. Nie anuluj operacji wdrażania, ponieważ proces trwa dłużej niż oczekiwano. Ten długi czas wdrożenia pierwszego wystąpienia jest sytuacją tymczasową. Wkrótce po rozpoczęciu publicznej wersji zapoznawczej należy oczekiwać znacznej redukcji czasu wdrożenia.

## <a name="create-a-new-subnet-in-the-vnet-for-a-virtual-machine"></a>Tworzenie nowej podsieci w sieci wirtualnej dla maszyny wirtualnej

Poniższe kroki pokazują sposób utworzenia drugiej podsieci w sieci wirtualnej dla maszyny wirtualnej, na której zostanie zainstalowany program SQL Server Management Studio i która będzie używana do nawiązywania połączenia z wystąpieniem zarządzanym.

1. Otwórz zasób sieci wirtualnej.
 
   ![VNet](./media/sql-database-managed-instance-tutorial/vnet.png)

2. Kliknij opcję **Podsieci**, a następnie kliknij opcję **+Podsieć**.
 
   ![dodawanie podsieci](./media/sql-database-managed-instance-tutorial/add-subnet.png)

3. Wypełnij formularz podsieci, podając wymagane informacje. Skorzystaj z informacji w następującej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   |**Nazwa**|Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Zakres adresów (blok CIDR)**|Dowolny prawidłowy zakres adresów w sieci wirtualnej (użyj wartości domyślnej)||
   |**Sieciowa grupa zabezpieczeń**|None||
   |**Tabela tras**|None||
   |**Punkty końcowe usługi**|Brak||

   ![szczegóły podsieci maszyny wirtualnej](./media/sql-database-managed-instance-tutorial/vm-subnet-details.png)

4. Kliknij przycisk **OK**.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Tworzenie maszyny wirtualnej w nowej podsieci w sieci wirtualnej

Poniższe kroki pokazują sposób utworzenia maszyny wirtualnej w tej samej sieci wirtualnej, w której utworzono wystąpienie zarządzane. 

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter** lub **Windows 10**. W tej części samouczka użyto systemu Windows Server. Konfigurowanie systemu Windows 10 jest bardzo podobne. 

   ![obliczanie](./media/sql-database-managed-instance-tutorial/compute.png)

3. Wypełnij formularz maszyny wirtualnej, podając wymagane informacje. Skorzystaj z informacji w następującej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   |**Nazwa**|Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Typ dysku maszyny wirtualnej**|SSD|Dyski SSD zapewniają najlepszą równowagę między ceną i wydajnością.|   
   |**Nazwa użytkownika**|Dowolna prawidłowa nazwa użytkownika|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).| 
   |**Hasło**|Dowolne prawidłowe hasło|Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).| 
   |**Subskrypcja**|Twoja subskrypcja|Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions).|
   |**Grupa zasobów**|Grupa zasobów, która została utworzona wcześniej||
   |**Lokalizacja**|Lokalizacja, która została wybrana wcześniej||
   |**Masz już licencję systemu Windows?**|Nie|Jeśli masz licencje na system Windows z aktywnym pakietem Software Assurance (SA), użyj korzyści użycia hybrydowego platformy Azure, aby obniżyć koszty związane z zasobami obliczeniowymi|
   ||||

   ![formularz tworzenia maszyny wirtualnej](./media/sql-database-managed-instance-tutorial/virtual-machine-create-form.png)

3. Kliknij przycisk **OK**.
4. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. Na potrzeby tego samouczka wystarczy jedynie mała maszyna wirtualna.

    ![Rozmiary maszyn wirtualnych](./media/sql-database-managed-instance-tutorial/virtual-machine-size.png)  

5. Kliknij pozycję **Wybierz**.
6. W formularzu **Ustawienia** kliknij opcję **Podsieć** i wybierz pozycję **vm_subnet**. Nie wybieraj podsieci, w której aprowizowano wystąpienie zarządzane, ale inną podsieć w tej samej sieci wirtualnej.

    ![Ustawienia maszyny wirtualnej](./media/sql-database-managed-instance-tutorial/virtual-machine-settings.png)  

7. Kliknij przycisk **OK**.
8. Na stronie z podsumowaniem sprawdź szczegóły oferty i kliknij przycisk **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej.
 
## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Poniższe kroki pokazują, jak nawiązać połączenie z nowo utworzoną maszyną wirtualną przy użyciu połączenia pulpitu zdalnego.

1. Po zakończeniu wdrażania przejdź do zasobu maszyny wirtualnej.

    ![VM](./media/sql-database-managed-instance-tutorial/vm.png)  

2. Kliknij przycisk **Połącz** we właściwościach maszyny wirtualnej. Zostanie utworzony i pobrany plik Remote Desktop Protocol (rdp).
3. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP. 
4. Kiedy zostanie wyświetlony monit, kliknij przycisk **Połącz**. Na komputerze Mac należy skorzystać z klienta RDP, takiego jak ten [klient pulpitu zdalnego](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) ze sklepu Mac App Store.

5. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej, a następnie kliknij przycisk **OK**.

6. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk **Tak** lub **Kontynuuj**, aby kontynuować nawiązywanie połączenia.

Nawiązano połączenie z maszyną wirtualną na pulpicie nawigacyjnym Menedżera serwera.

> [!IMPORTANT]
> Nie należy kontynuować, dopóki wystąpienie zarządzanie nie zostanie pomyślnie aprowizowane. Po zakończeniu aprowizacji pobierz nazwę hosta wystąpienia w polu **Wystąpienie zarządzane** na karcie **Omówienie** wystąpienia zarządzanego. Nazwa będzie podobna do następującej: **drfadfadsfd.tr23.westus1-a.worker.database.windows.net**.

## <a name="install-ssms-and-connect-to-the-managed-instance"></a>Instalacja narzędzia SSMS oraz nawiązywanie połączenia z wystąpieniem zarządzanym

Poniższe kroki pokazują, jak pobrać i zainstalować narzędzie SSMS, a następnie nawiązać połączenie z wystąpieniem zarządzanym.

1. W Menedżerze serwera kliknij pozycję **Serwer lokalny** w okienku po lewej stronie.

    ![właściwości menedżera serwera](./media/sql-database-managed-instance-tutorial/server-manager-properties.png)  

2. W okienku **Właściwości** kliknij przycisk **Wł.**, aby zmodyfikować konfigurację zwiększonych zabezpieczeń programu Internet Explorer.
3. W oknie dialogowym **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** kliknij przycisk **Wył.** w sekcji administratorów okna dialogowego, a następnie kliknij przycisk **OK**.

    ![konfiguracja zwiększonych zabezpieczeń programu Internet Explorer](./media/sql-database-managed-instance-tutorial/internet-explorer-security-configuration.png)  
4. Otwórz program **Internet Explorer** z paska zadań.
5. Wybierz pozycję **Użyj zalecanych ustawień zabezpieczeń i zgodności**, a następnie kliknij przycisk **OK**, aby zakończyć konfigurację programu Internet Explorer 11.
6. Wprowadź ciąg https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms w polu adresu URL i naciśnij klawisz **Enter**. 
7. Pobierz najnowszą wersję programu SQL Server Management Studio i kliknij pozycję **Uruchom** po wyświetleniu monitu.
8. Po wyświetleniu monitu kliknij przycisk **Zainstaluj**, aby rozpocząć.
9. Po zakończeniu instalacji kliknij przycisk **Zamknij**.
10. Otwórz program SSMS.
11. W oknie dialogowym **Połącz z serwerem** wprowadź **nazwę hosta* wystąpienia zarządzanego w polu **Nazwa serwera**, wybierz opcję **Uwierzytelnianie programu SQL Server**, podaj login i hasło, a następnie kliknij opcję **Połącz**.

    ![nawiązywanie połączenia w programie ssms](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

Po nawiązaniu połączenia możesz wyświetlić systemowe bazy danych oraz bazy danych użytkownika w węźle Bazy danych, a także różne obiekty w węzłach Zabezpieczenia, Obiekty serwera, Replikacja, Zarządzanie, Agent programu SQL Server oraz Profiler systemu XEvent.

## <a name="download-the-wide-world-importers---standard-backup-file"></a>Pobieranie pliku kopii zapasowej Wide World Importers — Standard

Wykonaj następujące kroki, aby pobrać plik kopii zapasowej Wide World Importers — Standard.

W programie Internet Explorer wprowadź ciąg https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak w polu adresu URL, a następnie po wyświetleniu monitu kliknij pozycję **Zapisz**, aby zapisać ten plik w folderze **Pobrane**.

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Tworzenie konta magazynu platformy Azure oraz przekazywanie pliku kopii zapasowej

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
2. Zlokalizuj **Magazyn** i kliknij opcję **Konto magazynu**, aby otworzyć formularz konta magazynu.

   ![konto magazynu](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. Wypełnij formularz konta magazynu, podając wymagane informacje. Skorzystaj z informacji w następującej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   |**Nazwa**|Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Model wdrażania**|Model zasobów||
   |**Rodzaj konta**|Blob Storage||
   |**Wydajność**|Standardowa lub Premium|Dyski magnetyczne lub dyski SSD|
   |**Replikacja**|Magazyn lokalnie nadmiarowy||
   |**Warstwa dostępu (domyślna)|Chłodna lub gorąca||
   |**Wymagany bezpieczny transfer**|Disabled (Wyłączony)||
   |**Subskrypcja**|Twoja subskrypcja|Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions).|
   |**Grupa zasobów**|Grupa zasobów, która została utworzona wcześniej|| 
   |**Lokalizacja**|Lokalizacja, która została wybrana wcześniej||
   |**Sieci wirtualne**|Disabled (Wyłączony)||

4. Kliknij przycisk **Utwórz**.

   ![szczegóły konta magazynu](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. Po pomyślnym wdrożeniu konta magazynu otwórz nowe konto magazynu.
6. W obszarze **Ustawienia** kliknij opcję **Sygnatura dostępu współdzielonego**, aby otworzyć formularz sygnatury dostępu współdzielonego.

   ![formularz sygnatury dostępu współdzielonego](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. W formularzu sygnatury dostępu współdzielonego zmodyfikuj wartości domyślne wedle potrzeb. Należy pamiętać, że data/godzina wygaśnięcia wynosi domyślnie zaledwie 8 godzin.
8. Kliknij opcję **Generuj sygnaturę dostępu współdzielonego**.

   ![wypełniony formularz sygnatury dostępu współdzielonego](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. Skopiuj i zapisz **token sygnatury dostępu współdzielonego** oraz **adres URL sygnatury dostępu współdzielonego serwera obiektów blob**.
10. W obszarze **Ustawienia** kliknij przycisk **Kontenery**.

    ![containers](./media/sql-database-managed-instance-tutorial/containers.png)

11. Kliknij przycisk **+ Kontener**, aby utworzyć kontener do przechowywania pliku kopii zapasowej.
12. Wypełnij formularz kontenera, podając wymagane informacje. Skorzystaj z informacji w następującej tabeli:

    | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   |**Nazwa**|Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Poziom dostępu publicznego**|Prywatny (bez dostępu anonimowego)||

    ![szczegóły kontenera](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. Kliknij przycisk **OK**.
14. Po utworzeniu kontenera otwórz go.

    ![kontener](./media/sql-database-managed-instance-tutorial/container.png)

15. Kliknij opcję **Właściwości kontenera**, a następnie skopiuj adres URL do kontenera.

    ![adres URL kontenera](./media/sql-database-managed-instance-tutorial/container-url.png)

16. Kliknij pozycję **Przekaż**, aby otworzyć formularz **Przekazywanie obiektu blob**.

    ![przekazywanie](./media/sql-database-managed-instance-tutorial/upload.png)

17. Przejdź do folderu pobierania i wybierz plik **AdventureWorks2016.bak**.

    ![przekazywanie](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. Kliknij pozycję **Przekaż**.
19. Nie kontynuuj pracy, dopóki przekazywanie nie zostanie zakończone.

    ![przekazywanie zakończone](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Przywracanie bazy danych Wide World Importers z pliku kopii zapasowej

W programie SSMS użyj następujących kroków, aby przywrócić bazę danych Wide World Importers do wystąpienia zarządzanego z pliku kopii zapasowej.

1. W programie SSMS otwórz nowe okno zapytania.
2. Użyj następującego skryptu, aby utworzyć poświadczenia sygnatury dostępu współdzielonego — podając adres URL kontenera konta magazynu oraz klucz sygnatury dostępu współdzielonego zgodnie z poleceniem.

   ```
CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
, SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![poświadczenia](./media/sql-database-managed-instance-tutorial/credential.png)

3. Użyj następującego skryptu, aby sprawdzić poprawność poświadczenia sygnatury dostępu współdzielonego i kopii zapasowej — podając adres URL kontenera z plikiem kopii zapasowej:

   ```
   RESTORE FILELISTONLY FROM URL = 
   'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![lista plików](./media/sql-database-managed-instance-tutorial/file-list.png)

4. Użyj następującego skryptu, aby przywrócić bazę danych Adventure Works 2012 z pliku kopii zapasowej — podając adres URL kontenera z plikiem kopii zapasowej:

   ```
   RESTORE DATABASE [Wide World Importers] FROM URL =
  'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![wykonywanie przywrócenia](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. Aby śledzić stan operacji przywracania, uruchom następujące zapytanie w nowej sesji zapytania:

   ```
SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
, dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
FROM sys.dm_exec_requests r 
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![procent wykonania przywracania](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. Po zakończeniu operacji przywracania wyświetl ją w Eksploratorze obiektów. 

    ![ukończenie przywracania](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o wystąpieniu zarządzanym, zobacz [Czym jest wystąpienie zarządzane?](sql-database-managed-instance.md)
- Aby uzyskać więcej informacji o konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-vnet-configuration.md).
- Aby uzyskać więcej informacji o nawiązywaniu połączenia z aplikacjami, zobacz [Nawiązywanie połączenia z aplikacjami](sql-database-managed-instance-connect-app.md).
- Aby skorzystać z samouczka w zakresie używania usługi Azure Database Migration Service (DMS) do celów migracji, zobacz [Migracja wystąpień zarządzanych przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md).
