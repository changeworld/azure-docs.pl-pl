---
title: Tworzenie maszyny wirtualnej z programem SQL Server 2017 i systemem Linux na platformie Azure | Microsoft Docs
description: W tym samouczku pokazano sposób tworzenia maszyny wirtualnej z programem SQL Server 2017 i systemem Linux na platformie Azure.
services: virtual-machines-linux
author: rothja
ms.author: jroth
manager: jhubbard
ms.date: 05/11/2018
ms.topic: hero-article
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.technology: database-engine
ms.openlocfilehash: b86dd47c112c38bc65c045158787d19b470899a0
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071725"
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Aprowizowanie maszyny wirtualnej z programem SQL Server w witrynie Azure Portal

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

W tym samouczku szybkiego startu użyjesz witryny Azure Portal do utworzenia maszyny wirtualnej z systemem Linux z zainstalowanym programem SQL Server 2017.

W tym samouczku zostaną wykonane następujące czynności:

* [Tworzenie maszyny wirtualnej z systemem Linux i programem SQL przy użyciu galerii](#create)
* [Łączenie z nową maszyną wirtualną przy użyciu protokołu SSH](#connect)
* [Zmienianie hasła administratora systemu](#password)
* [Konfigurowanie pod kątem połączeń zdalnych](#remote)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a id="create"></a> Tworzenie maszyny wirtualnej z systemem Linux i zainstalowanym programem SQL Server

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. W okienku po lewej stronie kliknij pozycję **Utwórz zasób**.

1. W okienku **Tworzenie zasobu** kliknij pozycję **Obliczenia**.

1. Kliknij przycisk **Zobacz wszystko** obok nagłówka **Polecane**.

   ![Wyświetlanie wszystkich obrazów maszyn wirtualnych](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. W polu wyszukiwania wpisz **SQL Server 2017** i naciśnij klawisz **Enter**, aby rozpocząć wyszukiwanie.

1. Kliknij ikonę **Filtr**, ogranicz wyszukiwanie do obrazów **firmy Microsoft** **opartych na systemie Linux**, a następnie kliknij przycisk **Gotowe**.

    ![Filtr wyszukiwania dla obrazów maszyn wirtualnych z programem SQL Server 2017](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Wybierz obraz systemu Linux z programem SQL Server 2017 w wynikach wyszukiwania. W tym samouczku jest używany obraz **Free SQL Server License: SQL Server 2017 Developer on Red Hat Enterprise Linux 7.4 (Bezpłatna licencja programu SQL Server: SQL Server 2017 Developer w systemie Red Hat Enterprise Linux 7.4)**.

   > [!TIP]
   > Wersja Developer umożliwia testowanie lub programowanie przy użyciu funkcji wersji Enterprise, ale bez kosztów licencjonowania programu SQL Server. Ponosisz tylko koszty obsługi maszyny wirtualnej z systemem Linux.

1. Kliknij przycisk **Utwórz**.

1. W oknie **Podstawowe informacje** wypełnij szczegóły dla swojej maszyny wirtualnej z systemem Linux. 

    ![Okno Podstawowe informacje](./media/provision-sql-server-linux-virtual-machine/basics.png)

    > [!Note]
    > Do uwierzytelniania możesz używać klucza publicznego SSH lub hasła. Protokół SSH jest bezpieczniejszy. Aby uzyskać instrukcje na temat sposobu generowania klucza SSH, zobacz [Create SSH keys on Linux and Mac for Linux VMs in Azure (Tworzenie kluczy SSH w systemie Linux i na komputerach Mac dla maszyn wirtualnych z systemem Linux na platformie Azure)](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

1. Kliknij przycisk **OK**.

1. W oknie **Rozmiar** wybierz rozmiar maszyny. Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych, zobacz [Linux VM sizes (Rozmiary maszyn wirtualnych z systemem Linux)](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes).

    ![Wybieranie rozmiaru maszyny wirtualnej](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Do programowania i testowania funkcjonalnego zaleca się rozmiar maszyny wirtualnej **DS2** lub większy. Do testowania wydajnościowego należy użyć rozmiaru **DS13** lub większego.

1. Kliknij pozycję **Wybierz**.

1. W oknie **Ustawienia** wybierz port **SSH (22)** z listy **Select public inbound ports** (Wybierz publiczne porty wejściowe). Ten krok w niniejszym przewodniku Szybki start jest konieczny do połączenia z programem SQL Server i ukończenia jego konfiguracji. Jeśli chcesz łączyć się zdalnie z programem SQL Server, wybierz również pozycję **MS SQL (1433)**, aby otworzyć port 1433 na połączenia przez Internet.

   ![Porty wejściowe](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. Możesz wprowadzić zmiany w innych ustawieniach lub zachować ustawienia domyślne. Następnie kliknij przycisk **OK**.

1. Na stronie **Podsumowanie** kliknij przycisk **Kup**, aby utworzyć maszynę wirtualną.

## <a id="connect"></a> Łączenie z maszyną wirtualną z systemem Linux

Jeśli już używasz powłoki BASH, połącz się z maszyną wirtualną platformy Azure przy użyciu polecenia **ssh**. W poniższym poleceniu zastąp nazwę użytkownika i adres IP maszyny wirtualnej, aby nawiązać połączenie z maszyną wirtualną z systemem Linux.

```bash
ssh azureadmin@40.55.55.555
```

Adres IP swojej maszyny wirtualnej możesz znaleźć w witrynie Azure Portal.

![Adres IP w witrynie Azure Portal](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Jeśli używasz systemu Windows i nie masz powłoki BASH, możesz zainstalować klienta SSH, takiego jak PuTTY.

1. [Pobierz i zainstaluj program PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Uruchom program PuTTY.

1. Na ekranie konfiguracji programu PuTTY wprowadź publiczny adres IP maszyny wirtualnej.

1. Kliknij przycisk Open (Otwórz), a następnie, w odpowiedzi na monity, wprowadź nazwę użytkownika i hasło.

Aby uzyskać więcej informacji na temat nawiązywania połączenia z maszynami wirtualnymi z systemem Linux, zobacz [Create a Linux VM on Azure using the Portal (Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu portalu)](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal#ssh-to-the-vm).

## <a id="password"></a> Zmienianie hasła administratora systemu

Nowa maszyna wirtualna instaluje program SQL Server z losowym hasłem administratora systemu. Należy zresetować to hasło, zanim będzie można połączyć się z programem SQL Server za pomocą danych logowania administratora systemu.

1. Po połączeniu ze swoją maszyną wirtualną z systemem Linux otwórz nowy terminal poleceń.

1. Zmień hasło administratora systemu za pomocą następujących poleceń:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Wprowadź nowe hasło administratora systemu i potwierdź hasło po wyświetleniu monitu.

1. Uruchom ponownie usługę SQL Server.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Dodawanie narzędzi do swojej ścieżki (opcjonalne)

Kilka [pakietów](sql-server-linux-virtual-machines-overview.md#packages) programu SQL Server zostanie domyślnie zainstalowanych, w tym pakiet narzędzi wiersza polecenia programu SQL Server. Pakiet narzędzi zawiera narzędzia **sqlcmd** i **bcp**. Dla wygody możesz opcjonalnie dodać ścieżkę narzędzi, `/opt/mssql-tools/bin/`, do swojej zmiennej środowiskowej **PATH**.

1. Uruchom następujące polecenia, aby zmodyfikować zmienną **PATH** zarówno dla sesji logowania, jak i sesji interaktywnych/bez logowania:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a id="remote"></a> Konfigurowanie pod kątem połączeń zdalnych

Jeśli potrzebujesz zdalnie połączyć się z programem SQL Server na maszynie wirtualnej platformy Azure, musisz skonfigurować regułę ruchu przychodzącego w sieciowej grupie zabezpieczeń. Reguła zezwala na ruch przez port, na którym nasłuchuje program SQL Server (domyślnie 1433). Poniższe kroki pokazują, jak korzystać z witryny Azure Portal w tym kroku.

> [!TIP]
> Jeśli podczas inicjowania obsługi w oknie Ustawienia wybrano port wejściowy **MS SQL (1433)**, te zmiany zostały wprowadzone automatycznie. Możesz przejść do kolejnej sekcji, która dotyczy konfigurowania zapory.

1. W portalu wybierz pozycję **Maszyny wirtualne**, a następnie wybierz swoją maszynę wirtualną z programem SQL Server.

1. Na liście właściwości wybierz pozycję **Sieć**.

1. W oknie **Sieć** kliknij przycisk **Dodaj** w obszarze **Reguły portów wejściowych**.

   ![Reguły portów wejściowych](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. Na liście **Usługa** wybierz pozycję **MS SQL**.

    ![Reguła grupy zabezpieczeń MS SQL](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. Kliknij przycisk **OK**, aby zapisać regułę dla swojej maszyny wirtualnej.

### <a name="open-the-firewall-on-rhel"></a>Otwieranie zapory w systemie RHEL

Ten samouczek pomógł utworzyć maszynę wirtualną z systemem Red Hat Enterprise Linux (RHEL). Jeśli chcesz łączyć się zdalnie z maszynami wirtualnymi z systemem RHEL, musisz też otworzyć port 1433 w zaporze systemu Linux.

1. [Połącz się](#connect) ze swoją maszyną wirtualną z systemem RHEL.

1. W powłoce BASH uruchom następujące polecenia:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz maszynę wirtualną z programem SQL Server 2017 na platformie Azure, możesz się połączyć lokalnie z narzędziem **sqlcmd**, aby uruchamiać zapytania Transact-SQL.

Jeśli maszynę wirtualną platformy Azure skonfigurowano pod kątem połączeń zdalnych programu SQL Server, powinno być możliwe zdalne łączenie się z tą maszyną. Aby zapoznać się z przykładem, jak połączyć się zdalnie z programem SQL Server w systemie Linux z systemu Windows, zobacz [Use SSMS on Windows to connect to SQL Server on Linux (Używanie aplikacji SSMS w systemie Windows, aby połączyć się z programem SQL Server w systemie Linux)](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms). Aby połączyć się z programem Visual Studio Code, zobacz [Use Visual Studio Code to create and run Transact-SQL scripts for SQL Server (Używanie programu Visual Studio Code do tworzenia i uruchamiania skryptów języka Transact-SQL dla programu SQL Server)](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode)

Aby uzyskać więcej ogólnych informacji na temat programu SQL Server w systemie Linux, zobacz [Overview of SQL Server 2017 on Linux (Omówienie programu SQL Server 2017 w systemie Linux)](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Aby uzyskać więcej informacji o używaniu maszyn wirtualnych z programem SQL Server 2017 i systemem Linux, zobacz [Overview of SQL Server 2017 virtual machines on Azure (Omówienie maszyn wirtualnych z programem SQL Server 2017 na platformie Azure)](sql-server-linux-virtual-machines-overview.md).
