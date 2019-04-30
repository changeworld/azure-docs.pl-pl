---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 57f238a8f91df1271e91894b88a7f02118b1f123
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108433"
---
### <a name="determine-the-dns-name-of-the-virtual-machine"></a>Określ nazwę DNS maszyny wirtualnej
Aby połączyć się z aparatem bazy danych programu SQL Server z innego komputera, musisz znać nazwę systemu nazw domen (DNS, Domain Name System) maszyny wirtualnej. (Jest to nazwa, używanych przez internet do identyfikowania maszyny wirtualnej. Możesz użyć adresu IP, ale adresu IP mogą ulec zmianie, gdy Azure przenosi zasoby dla zapewnienia nadmiarowości lub konserwacji. Nazwa DNS będzie stabilny, ponieważ mogą zostać przekierowane do nowego adresu IP).  

1. W witrynie Azure Portal (lub w poprzednim kroku), wybierz **maszyny wirtualne (klasyczne)**.
2. Wybierz maszynę Wirtualną SQL.
3. Na **maszyny wirtualnej** bloku, kopia **nazwy DNS** dla maszyny wirtualnej.
   
    ![Nazwa DNS](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Nawiązywanie połączenia z aparatem bazy danych z innego komputera
1. Na komputerze podłączonym do Internetu Otwórz program SQL Server Management Studio.
2. W **Połącz z serwerem** lub **nawiązywanie połączenia z aparatem bazy danych** dialogowym **nazwy serwera** wprowadź nazwę DNS maszyny wirtualnej (określoną w poprzednim zadaniu) i numeru portu publicznego punktu końcowego w formacie *DNSName, numer_portu* takich jak **mysqlvm.cloudapp.net,57500**.
   
    ![Nawiązywanie połączenia przy użyciu programu SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)
   
    Jeśli nie pamiętasz numeru portu publicznego punktu końcowego została wcześniej utworzona, można znaleźć w **punktów końcowych** obszaru **maszyny wirtualnej** bloku.
   
    ![Port publiczny](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. W polu **Authentication** (Uwierzytelnianie) wybierz opcję **SQL Server Authentication** (Uwierzytelnianie programu SQL Server).
4. W **logowania** wpisz nazwę logowania, który został utworzony w starszej zadania.
5. W **hasło** wpisz hasło logowania, utworzonego do wcześniejszych zadań.
6. Kliknij przycisk **Połącz**.

