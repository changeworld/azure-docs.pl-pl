---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 57f238a8f91df1271e91894b88a7f02118b1f123
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183175"
---
### <a name="determine-the-dns-name-of-the-virtual-machine"></a>Określanie nazwy DNS maszyny wirtualnej
Aby połączyć się z aparatem baz danych programu SQL Server z innego komputera, musisz znać nazwę systemu nazw domen (DNS) maszyny wirtualnej. (Jest to nazwa używana przez Internet do identyfikowania maszyny wirtualnej. Można użyć adresu IP, ale adres IP może ulec zmianie, gdy platforma Azure przenosi zasoby w celu nadmiarowości lub konserwacji. Nazwa DNS będzie stabilna, ponieważ można ją przekierować na nowy adres IP).  

1. W witrynie Azure Portal (lub z poprzedniego kroku) wybierz pozycję **Maszyny wirtualne (klasyczne).**
2. Wybierz maszynę wirtualną SQL.
3. W bloku **maszyny wirtualnej** skopiuj **nazwę DNS** maszyny wirtualnej.
   
    ![Nazwa DNS](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Nawiązywanie połączenia z aparatem bazy danych z innego komputera
1. Na komputerze podłączonym do Internetu otwórz program SQL Server Management Studio.
2. W oknie dialogowym **Łączenie się z serwerem** lub **Łączenie z aparatem baz danych** w polu Nazwa **serwera** wprowadź nazwę DNS maszyny wirtualnej (określoną w poprzednim zadaniu) i numer portu publicznego punktu końcowego w formacie *DNSName,portnumber,* taki jak **mysqlvm.cloudapp.net,57500**.
   
    ![Łączenie za pomocą usługi SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)
   
    Jeśli nie pamiętasz numer portu publicznego punktu końcowego, który został wcześniej utworzony, można go znaleźć w obszarze **Punkty końcowe** bloku **maszyny wirtualnej.**
   
    ![Port publiczny](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. W polu **Authentication** (Uwierzytelnianie) wybierz opcję **SQL Server Authentication** (Uwierzytelnianie programu SQL Server).
4. W polu **Logowanie** wpisz nazwę logowania utworzonego we wcześniejszym zadaniu.
5. W polu **Hasło** wpisz hasło logowania utworzonego we wcześniejszym zadaniu.
6. Kliknij pozycję **Połącz**.

