---
title: Łączenie maszyny Wirtualnej klienta - wystąpienia zarządzanego Azure SQL Database | Dokumentacja firmy Microsoft
description: Nawiązać połączenia bazy danych wystąpienia zarządzanego Azure SQL przy użyciu programu SQL Server Management Studio, z maszyną wirtualną platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, srbozovi, bonova
manager: craigg
ms.date: 09/13/2018
ms.openlocfilehash: 08b8e37493ea7bc549a2352aaa6714ef7c65bfdb
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159520"
---
# <a name="configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Konfigurowanie maszyny Wirtualnej platformy Azure, nawiązać połączenia z bazą danych wystąpienia zarządzanego Azure SQL

Ta przewodników Szybki Start pokazano, jak skonfigurować maszynę wirtualną platformy Azure, nawiązać połączenia z bazą danych wystąpienia zarządzanego Azure SQL przy użyciu programu SQL Server Management Studio (SSMS). Aby uzyskać szybki start przedstawiający sposób nawiązywania połączeń z poziomu komputera klienckiego w środowisku lokalnym za pomocą połączenia punkt lokacja, zobacz [Konfigurowanie połączenia typu punkt lokacja](sql-database-managed-instance-configure-p2s.md) 

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start używa jako punktu początkowego zasobów utworzonych w tym przewodniku Szybki Start: [utworzysz wystąpienie zarządzane](sql-database-managed-instance-get-started.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Utwórz nową podsieć w sieci wirtualnej wystąpienia zarządzanego

Poniższe kroki umożliwiają utworzenie nowej podsieci w zarządzane wystąpienia sieci wirtualnej na maszynie wirtualnej platformy Azure, aby nawiązać połączenie z wystąpieniem zarządzanym. Podsieci wystąpienia zarządzanego jest dedykowany do wystąpienia zarządzanego i inne zasoby (na przykład maszyny wirtualne platformy Azure) nie można utworzyć w tej podsieci. 

1. Otwórz grupę zasobów dla wystąpienia zarządzanego, który został utworzony w [utworzysz wystąpienie zarządzane](sql-database-managed-instance-get-started.md) Szybki Start i kliknij sieć wirtualną dla wystąpienia zarządzanego, a następnie kliknij przycisk **podsieci**.

   ![Zarządzane zasoby wystąpienia](./media/sql-database-managed-instance-configure-vm/resources.png)

2. Kliknij przycisk **+** dalej, aby zarejestrować **podsieci** do utworzenia nowej podsieci.

   ![Zarządzane wystąpienia podsieci](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. Wypełnij formularz, podając wymagane informacje, skorzystaj z informacji w poniższej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ---------------- | ----------------- | ----------- | 
   | **Nazwa** | Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Zakres adresów (blok CIDR)** | Prawidłowy zakres | Wartość domyślna to rozwiązanie dobre dla tego przewodnika Szybki Start.|
   | **Sieciowa grupa zabezpieczeń** | Brak | Wartość domyślna to rozwiązanie dobre dla tego przewodnika Szybki Start.|
   | **Tabela tras** | Brak | Wartość domyślna to rozwiązanie dobre dla tego przewodnika Szybki Start.|
   | ** Punkty końcowe usługi ** | Wybrano 0 | Wartość domyślna to rozwiązanie dobre dla tego przewodnika Szybki Start.|
   | **Delegowanie podsieci** | Brak | Wartość domyślna to rozwiązanie dobre dla tego przewodnika Szybki Start.|
 
   ![Nowa podsieć wystąpienia zarządzanego dla maszyny Wirtualnej klienta](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. Kliknij przycisk **OK** utworzyć ta dodatkowa podsieć w sieci wirtualnej wystąpienia zarządzanego.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Tworzenie maszyny wirtualnej w nowej podsieci w sieci wirtualnej

Poniższe kroki pokazują, jak utworzyć maszynę wirtualną w nowej podsieci, aby nawiązać połączenie z wystąpieniem zarządzanym. 

## <a name="prepare-the-azure-virtual-machine"></a>Przygotowywanie maszyny wirtualnej platformy Azure

Ponieważ wystąpienia zarządzanego SQL znajduje się w prywatnej sieci wirtualnej, musisz utworzyć Maszynę wirtualną platformy Azure przy użyciu niektóre zainstalowane narzędzia klienta SQL, takiego jak SQL Server Management Studio lub Studio danych platformy Azure, aby podłączyć się do wystąpienia zarządzanego i wykonywania zapytań. Ten przewodnik Szybki Start używa programu SQL Server Management Studio.

Najprostszym sposobem utworzenia maszyny wirtualnej klienta za pomocą wszystkie niezbędne narzędzia jest za pomocą szablonów usługi Azure Resource Manager.

1. Kliknij poniższy przycisk, aby utworzyć maszynę wirtualną klienta i zainstalować SQL Server Management Studio (Upewnij się, że możesz jest zalogowany do witryny Azure portal w innej karcie przeglądarki):

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. Wypełnij formularz, podając wymagane informacje, skorzystaj z informacji w poniższej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ---------------- | ----------------- | ----------- |
   | **Subskrypcja** | Ważną subskrypcją | Musi to być subskrypcja, w którym masz uprawnienia do tworzenia nowych zasobów |
   | **Grupa zasobów** |Grupa zasobów, który określiłeś w [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md) Szybki Start.|Musi to być grupa zasobów, w którym istnieje sieć wirtualna.|
   | **Lokalizacja** | Lokalizacja grupy zasobów | Ta wartość jest wypełniana na podstawie grupy zasobów, wybrana | 
   | **Nazwa maszyny wirtualnej**  | Dowolna prawidłowa nazwa | Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Nazwa użytkownika administratora**|Dowolna prawidłowa nazwa użytkownika|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Nie używaj nazwy „serveradmin”, gdyż jest ona zarezerwowana dla roli poziomu serwera.| 
   |**Hasło**|Dowolne prawidłowe hasło|Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   | **Rozmiar maszyny wirtualnej** | Wszelkie prawidłowy rozmiar | Wartość domyślna, w tym szablonie o ** Standard_B2s jest wystarczająca na potrzeby tego przewodnika Szybki Start. |
   | **Lokalizacja**|[resourceGroup () .location].| Nie zmieniaj tej wartości |
   | **Nazwa sieci wirtualnej**|Lokalizacja, która została wybrana wcześniej|Aby uzyskać informacje na temat regionów, zobacz temat [Regiony platformy Azure](https://azure.microsoft.com/regions/).|
   | **Nazwa podsieci**|Nazwa podsieci, który został utworzony w poprzedniej procedurze| Nie należy wybierać podsieci, w której utworzono wystąpienie zarządzane|
   | **Artefakty lokalizacji** | [.properties.templateLink.uri wdrożenia ()]  Nie zmieniaj tej wartości |
   | **token sygnatury dostępu współdzielonego lokalizacji artefaktów** | Pozostaw to pole puste | Nie zmieniaj tej wartości |

   ![tworzenie maszyny wirtualnej klienta](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   Jeśli używasz sugerowana nazwa sieci wirtualnej i podsieci domyślne w [tworzenia wystąpienia zarządzanego](sql-database-managed-instance-get-started.md), nie trzeba zmienić ostatnie dwa parametry. W przeciwnym razie należy zmieniać te wartości do wartości, które wprowadzony podczas konfigurowania środowiska sieciowego.

3. Wybierz **zgodę na warunki i postanowienia, o których wspomniano** pola wyboru.
4. Kliknij przycisk **zakupu** do wdrażania maszyn wirtualnych platformy Azure w sieci.
5. Kliknij ikonę **Powiadomienia**, aby wyświetlić stan wdrożenia.
   
   Nie należy kontynuować dopiero po utworzeniu maszyny wirtualnej platformy Azure. 

## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Poniższe kroki pokazują, jak nawiązać połączenie z nowo utworzoną maszyną wirtualną przy użyciu połączenia pulpitu zdalnego.

1. Po zakończeniu wdrażania przejdź do zasobu maszyny wirtualnej.

    ![VM](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. Kliknij przycisk **Połącz**. 
   
   Zostanie wyświetlony formularz protokołu Remote Desktop Protocol pliku (RDP), za pomocą publicznego adresu IP adres i numer portu dla maszyny wirtualnej. 

   ![Formularz protokołu RDP](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. Kliknij przycisk **Pobierz plik RDP**.
 
   > [!NOTE]
   > Można również używanie protokołu SSH, aby nawiązać połączenie z maszyną Wirtualną.

4. Zamknij **Połącz z maszyną wirtualną** formularza.
5. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP. 
6. Kiedy zostanie wyświetlony monit, kliknij przycisk **Połącz**. Na komputerze Mac należy skorzystać z klienta RDP, takiego jak ten [klient pulpitu zdalnego](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) ze sklepu Mac App Store.

6. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej, a następnie kliknij przycisk **OK**.

7. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk **Tak** lub **Kontynuuj**, aby kontynuować nawiązywanie połączenia.

Nawiązano połączenie z maszyną wirtualną na pulpicie nawigacyjnym Menedżera serwera.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Nawiązywanie połączenia z wystąpieniem zarządzanym za pomocą programu SSMS

1. Na maszynie wirtualnej Otwórz program SQL Server Management Studio (SSMS).
 
   Potrwa kilka chwil, Otwórz, ile potrzebuje ukończyć jej konfigurowanie, ponieważ podczas pierwszego uruchomienia programu SSMS.
2. W **Połącz z serwerem** okna dialogowego wprowadź w pełni kwalifikowaną **nazwy hosta** wystąpienia zarządzanego w **nazwy serwera** wybierz opcję **programu SQL Server Uwierzytelnianie**login i hasło, a następnie kliknij przycisk **Connect**.

    ![nawiązywanie połączenia w programie ssms](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Po nawiązaniu połączenia możesz wyświetlić systemowe bazy danych oraz bazy danych użytkownika w węźle Bazy danych, a także różne obiekty w węzłach Zabezpieczenia, Obiekty serwera, Replikacja, Zarządzanie, Agent programu SQL Server oraz Profiler systemu XEvent.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szybki start przedstawiający sposób nawiązywania połączeń z poziomu komputera klienckiego w środowisku lokalnym za pomocą połączenia punkt lokacja, zobacz [Konfigurowanie połączenia typu punkt lokacja](sql-database-managed-instance-configure-p2s.md).
- Aby uzyskać omówienie opcji połączenia dla aplikacji, zobacz [łączenie aplikacji z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md).
- Aby przywrócić istniejącą bazę danych programu SQL Server ze środowiska lokalnego do wystąpienia zarządzanego, można użyć [usługi Azure Database Migration Service (DMS) do migracji](../dms/tutorial-sql-server-to-managed-instance.md) do przywrócenia z pliku kopii zapasowej bazy danych lub [T-SQL RESTORE polecenie](sql-database-managed-instance-get-started-restore.md) do przywrócenia z pliku kopii zapasowej bazy danych.
