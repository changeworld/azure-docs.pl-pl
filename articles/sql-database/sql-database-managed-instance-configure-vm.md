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
ms.reviewer: sstein, carlrab, srbozovi, bonova
manager: craigg
ms.date: 02/18/2019
ms.openlocfilehash: 59088ad53e923f1303c0e800df9c25f70e63812f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60701929"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Szybki start: Konfigurowanie maszyny Wirtualnej platformy Azure, nawiązać połączenia z bazą danych wystąpienia zarządzanego Azure SQL

Ten przewodnik Szybki Start dowiesz się, jak skonfigurować maszynę wirtualną platformy Azure, nawiązać połączenia z bazą danych wystąpienia zarządzanego Azure SQL przy użyciu programu SQL Server Management Studio (SSMS). Aby uzyskać szybki start przedstawiający sposób nawiązywania połączeń z poziomu komputera klienckiego w środowisku lokalnym za pomocą połączenia punkt lokacja, zobacz [Konfigurowanie połączenia typu punkt lokacja](sql-database-managed-instance-configure-p2s.md)

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start używa zasobów utworzonych w [utworzysz wystąpienie zarządzane](sql-database-managed-instance-get-started.md) jako punktu początkowego.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Utwórz nową podsieć w sieci wirtualnej wystąpienia zarządzanego

Poniższe kroki umożliwiają utworzenie nowej podsieci w sieci wirtualnej wystąpienia zarządzanego maszynie wirtualnej platformy Azure do połączenia się do wystąpienia zarządzanego. Podsieci wystąpienia zarządzanego jest dedykowany do wystąpienia zarządzanego. Inne zasoby, takie jak maszyny wirtualne platformy Azure, nie można utworzyć w tej podsieci.

1. Otwórz grupę zasobów dla wystąpienia zarządzanego, który został utworzony w [utworzysz wystąpienie zarządzane](sql-database-managed-instance-get-started.md) Szybki Start. Wybierz sieć wirtualną dla wystąpienia zarządzanego.

   ![Zasoby wystąpienia zarządzanego](./media/sql-database-managed-instance-configure-vm/resources.png)

2. Wybierz **podsieci** , a następnie wybierz **+ podsieć** do utworzenia nowej podsieci.

   ![Zarządzane wystąpienia podsieci](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. Wypełnij formularz, korzystając z informacji w tej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ---------------- | ----------------- | ----------- |
   | **Nazwa** | Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Zakres adresów (blok CIDR)** | Prawidłowy zakres | Wartość domyślna to rozwiązanie dobre dla tego przewodnika Szybki Start.|
   | **Sieciowa grupa zabezpieczeń** | Brak | Wartość domyślna to rozwiązanie dobre dla tego przewodnika Szybki Start.|
   | **Tabela tras** | Brak | Wartość domyślna to rozwiązanie dobre dla tego przewodnika Szybki Start.|
   | **Punkty końcowe usługi** | Wybrano 0 | Wartość domyślna to rozwiązanie dobre dla tego przewodnika Szybki Start.|
   | **Delegowanie podsieci** | Brak | Wartość domyślna to rozwiązanie dobre dla tego przewodnika Szybki Start.|

   ![Nowa podsieć wystąpienia zarządzanego dla maszyny Wirtualnej klienta](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. Wybierz **OK** utworzyć ta dodatkowa podsieć w sieci wirtualnej wystąpienia zarządzanego.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Tworzenie maszyny wirtualnej w nowej podsieci w sieci wirtualnej

Poniższe kroki pokazują, jak utworzyć maszynę wirtualną w nowej podsieci, aby nawiązać połączenie z wystąpieniem zarządzanym.

## <a name="prepare-the-azure-virtual-machine"></a>Przygotowywanie maszyny wirtualnej platformy Azure

Ponieważ wystąpienia zarządzanego SQL znajduje się w prywatnej sieci wirtualnej, musisz utworzyć Maszynę wirtualną platformy Azure za pomocą narzędzia zainstalowanego klienta SQL, takich jak SQL Server Management Studio lub Studio danych platformy Azure. To narzędzie pozwala nawiązać połączenie z wystąpieniem zarządzanym i wykonywania zapytań. Ten przewodnik Szybki Start używa programu SQL Server Management Studio.

Najprostszym sposobem utworzenia maszyny wirtualnej klienta za pomocą wszystkie niezbędne narzędzia jest za pomocą szablonów usługi Azure Resource Manager.

1. Upewnij się, że użytkownik zalogowany do witryny Azure portal w innej karcie przeglądarki. Wybierz poniższy przycisk, aby utworzyć maszynę wirtualną klienta i zainstalować SQL Server Management Studio:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. Wypełnij formularz, korzystając z informacji w poniższej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ---------------- | ----------------- | ----------- |
   | **Subskrypcja** | Ważną subskrypcją | Musi być subskrypcją, w którym masz uprawnienia do tworzenia nowych zasobów. |
   | **Grupa zasobów** |Grupa zasobów, który określiłeś w [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md) Szybki Start.|Ta grupa zasobów musi być jedną, w którym istnieje sieć wirtualna.|
   | **Lokalizacja** | Lokalizacja grupy zasobów | Ta wartość jest wypełniana na podstawie grupy zasobów, wybrana. |
   | **Nazwa maszyny wirtualnej**  | Dowolna prawidłowa nazwa | Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Nazwa użytkownika administratora**|Dowolną prawidłową nazwę użytkownika|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Nie używaj nazwy „serveradmin”, gdyż jest ona zarezerwowana dla roli poziomu serwera.<br>Użyj tej nazwy użytkownika wtedy, gdy [nawiązać połączenie z maszyną Wirtualną](#connect-to-virtual-machine).|
   |**Hasło**|Dowolne prawidłowe hasło|Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>To hasło jest używane wtedy, gdy [nawiązać połączenie z maszyną Wirtualną](#connect-to-virtual-machine).|
   | **Rozmiar maszyny wirtualnej** | Wszelkie prawidłowy rozmiar | Wartość domyślna, w tym szablonie o **Standard_B2s** jest wystarczająca na potrzeby tego przewodnika Szybki Start. |
   | **Lokalizacja**|[resourceGroup () .location].| Nie zmieniaj tej wartości. |
   | **Nazwa sieci wirtualnej**|Sieć wirtualna, w którym utworzono wystąpienie zarządzane.|
   | **Nazwa podsieci**|Nazwa podsieci, który został utworzony w poprzedniej procedurze| Nie wybieraj podsieci, w której utworzono wystąpienie zarządzane.|
   | **Artefakty lokalizacji** | [.properties.templateLink.uri wdrożenia ()] | Nie zmieniaj tej wartości. |
   | **token sygnatury dostępu współdzielonego lokalizacji artefaktów** | Pozostaw to pole puste | Nie zmieniaj tej wartości. |

   ![tworzenie maszyny wirtualnej klienta](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   Jeśli używasz sugerowana nazwa sieci wirtualnej i podsieci domyślne w [tworzenia wystąpienia zarządzanego](sql-database-managed-instance-get-started.md), nie trzeba zmienić ostatnie dwa parametry. W przeciwnym razie należy zmieniać te wartości do wartości, które wprowadzony podczas konfigurowania środowiska sieciowego.

3. Wybierz **zgodę na warunki i postanowienia, o których wspomniano** pola wyboru.
4. Wybierz **zakupu** do wdrażania maszyn wirtualnych platformy Azure w sieci.
5. Wybierz ikonę **Powiadomienia**, aby wyświetlić stan wdrożenia.

> [!IMPORTANT]
> Nie należy przechodzić do około 15 minut po utworzeniu maszyny wirtualnej, aby zapewnić czas na skrypty po utworzeniu zainstalować program SQL Server Management Studio.

## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Poniższe kroki pokazują, jak nawiązać połączenie z nowo utworzoną maszyną wirtualną przy użyciu połączenia pulpitu zdalnego.

1. Po zakończeniu wdrażania przejdź do zasobu maszyny wirtualnej.

    ![VM](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. Wybierz przycisk **Połącz**.

   Zostanie wyświetlony formularz protokołu Remote Desktop Protocol pliku (RDP), za pomocą publicznego adresu IP adres i numer portu dla maszyny wirtualnej.

   ![Formularz protokołu RDP](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. Wybierz **Pobierz plik RDP**.

   > [!NOTE]
   > Można również używanie protokołu SSH, aby nawiązać połączenie z maszyną Wirtualną.

4. Zamknij **Połącz z maszyną wirtualną** formularza.
5. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP.
6. Po wyświetleniu monitu wybierz **Connect**. Na komputerze Mac należy skorzystać z klienta RDP, takiego jak ten [klient pulpitu zdalnego](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) ze sklepu Mac App Store.

7. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej, a następnie wybierz **OK**.

8. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz **tak** lub **Kontynuuj** Aby nawiązać połączenie.

Masz połączenia z maszyną wirtualną na pulpicie nawigacyjnym Menedżera serwera.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Nawiązywanie połączenia z wystąpieniem zarządzanym za pomocą programu SSMS

1. Na maszynie wirtualnej Otwórz program SQL Server Management Studio (SSMS).

   Może potrwać kilka chwil, Otwórz, ile potrzebuje ukończyć jej konfigurowanie, ponieważ jest to przy pierwszym uruchomieniu programu SSMS.
2. W **Połącz z serwerem** okna dialogowego wprowadź w pełni kwalifikowaną **nazwy hosta** wystąpienia zarządzanego w **nazwy serwera** pole. Wybierz **uwierzytelniania programu SQL Server**, podaj nazwę użytkownika i hasło, a następnie wybierz **Connect**.

    ![nawiązywanie połączenia w programie ssms](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Po nawiązaniu połączenia możesz wyświetlić systemowe bazy danych oraz bazy danych użytkownika w węźle Bazy danych, a także różne obiekty w węzłach Zabezpieczenia, Obiekty serwera, Replikacja, Zarządzanie, Agent programu SQL Server oraz Profiler systemu XEvent.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szybki start przedstawiający sposób nawiązywania połączeń z poziomu komputera klienckiego w środowisku lokalnym za pomocą połączenia punkt lokacja, zobacz [Konfigurowanie połączenia typu punkt lokacja](sql-database-managed-instance-configure-p2s.md).
- Aby uzyskać omówienie opcji połączenia dla aplikacji, zobacz artykuł [Connect your applications to Managed Instance](sql-database-managed-instance-connect-app.md) (Łączenie aplikacji z wystąpieniem zarządzanym).
- Aby przywrócić istniejącą bazę danych programu SQL Server ze środowiska lokalnego do wystąpienia zarządzanego, można użyć [usługi Azure Database Migration Service (DMS) do migracji](../dms/tutorial-sql-server-to-managed-instance.md) lub [polecenia języka T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) przywracania z Plik kopii zapasowej bazy danych.
