---
title: Łączenie maszyny Wirtualnej klienta — wystąpienie zarządzane
description: Połącz się z wystąpieniem zarządzanym bazy danych SQL azure przy użyciu programu SQL Server Management Studio z maszyny wirtualnej platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: 7273c7b1dbf5eb6c855b95a8661f38bd4bd14af7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839047"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Szybki start: konfigurowanie maszyny Wirtualnej platformy Azure do łączenia się z wystąpieniem zarządzanym bazy danych SQL platformy Azure

Ten przewodnik Szybki start pokazuje, jak skonfigurować maszynę wirtualną platformy Azure do łączenia się z wystąpieniem zarządzanym bazy danych SQL azure przy użyciu programu SQL Server Management Studio (SSMS). Aby uzyskać przewodnik Szybki start pokazujący, jak łączyć się z lokalnego komputera klienckiego przy użyciu połączenia typu punkt-lokacja, zobacz [Konfigurowanie połączenia typu punkt-lokacja](sql-database-managed-instance-configure-p2s.md)

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start używa zasobów utworzonych w [create a managed instance](sql-database-managed-instance-get-started.md) jako punkt wyjścia.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Tworzenie nowej podsieci w sieci wirtualnej wystąpienia zarządzanego

Poniższe kroki utworzyć nową podsieć w sieci wirtualnej wystąpienia zarządzanego, dzięki czemu maszyna wirtualna platformy Azure może połączyć się z wystąpieniem zarządzanym. Podsieć wystąpienia zarządzanego jest dedykowana wystąpieniom zarządzanym. Nie można utworzyć żadnych innych zasobów, takich jak maszyny wirtualne platformy Azure, w tej podsieci.

1. Otwórz grupę zasobów dla wystąpienia zarządzanego utworzonego w przewodniku Szybki start [Tworzenia wystąpienia zarządzanego.](sql-database-managed-instance-get-started.md) Wybierz sieć wirtualną dla wystąpienia zarządzanego.

   ![Zasoby wystąpienia zarządzanego](./media/sql-database-managed-instance-configure-vm/resources.png)

2. Wybierz **podsieci,** a następnie wybierz + **Podsieć,** aby utworzyć nową podsieć.

   ![Podsieci wystąpienia zarządzanego](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. Wypełnij formularz, korzystając z informacji zawartych w tej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ---------------- | ----------------- | ----------- |
   | **Nazwa** | Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](/azure/architecture/best-practices/resource-naming).|
   | **Zakres adresów (blok CIDR)** | Prawidłowy zakres | Wartość domyślna jest dobra dla tego szybkiego startu.|
   | **Grupa zabezpieczeń sieci** | Brak | Wartość domyślna jest dobra dla tego szybkiego startu.|
   | **Tabela tras** | Brak | Wartość domyślna jest dobra dla tego szybkiego startu.|
   | **Punkty końcowe usługi** | Wybrano 0 | Wartość domyślna jest dobra dla tego szybkiego startu.|
   | **Delegowanie podsieci** | Brak | Wartość domyślna jest dobra dla tego szybkiego startu.|

   ![Nowa podsieć wystąpienia zarządzanego dla maszyny Wirtualnej klienta](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. Wybierz **przycisk OK,** aby utworzyć tę dodatkową podsieć w sieci wirtualnej wystąpienia zarządzanego.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Tworzenie maszyny wirtualnej w nowej podsieci w sieci wirtualnej

Poniższe kroki pokazują, jak utworzyć maszynę wirtualną w nowej podsieci, aby połączyć się z wystąpieniem zarządzanym.

## <a name="prepare-the-azure-virtual-machine"></a>Przygotowywanie maszyny wirtualnej platformy Azure

Ponieważ wystąpienie zarządzane sql jest umieszczane w prywatnej sieci wirtualnej, należy utworzyć maszynę wirtualną platformy Azure z zainstalowanym narzędziem klienta SQL, takim jak SQL Server Management Studio lub Azure Data Studio. To narzędzie umożliwia łączenie się z wystąpieniem zarządzanym i wykonywanie zapytań. W tym przewodniku Szybki start używany jest program SQL Server Management Studio.

Najprostszym sposobem utworzenia maszyny wirtualnej klienta ze wszystkimi niezbędnymi narzędziami jest użycie szablonów usługi Azure Resource Manager.

1. Upewnij się, że zalogowano się do witryny Azure portal w innej karcie przeglądarki. Następnie wybierz następujący przycisk, aby utworzyć maszynę wirtualną klienta i zainstalować program SQL Server Management Studio:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. Wypełnij formularz, korzystając z informacji z poniższej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ---------------- | ----------------- | ----------- |
   | **Subskrypcja** | Prawidłowa subskrypcja | Musi być subskrypcja, w której masz uprawnienia do tworzenia nowych zasobów. |
   | **Grupa zasobów** |Grupa zasobów określona w przewodniku Szybki start [Tworzenie wystąpienia zarządzanego.](sql-database-managed-instance-get-started.md)|Ta grupa zasobów musi być tą, w której istnieje sieci wirtualnej.|
   | **Lokalizacja** | Lokalizacja grupy zasobów | Ta wartość jest wypełniana na podstawie wybranej grupy zasobów. |
   | **Nazwa maszyny wirtualnej**  | Dowolna prawidłowa nazwa | Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](/azure/architecture/best-practices/resource-naming).|
   |**Nazwa użytkownika administratora**|Każda prawidłowa nazwa użytkownika|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](/azure/architecture/best-practices/resource-naming). Nie używaj nazwy „serveradmin”, gdyż jest ona zarezerwowana dla roli poziomu serwera.<br>Tej nazwy użytkownika należy używać za każdym razem, gdy [łączysz się z maszyną wirtualną](#connect-to-virtual-machine).|
   |**Hasło**|Dowolne prawidłowe hasło|Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Tego hasła należy używać za każdym razem, gdy [łączysz się z maszyną wirtualną](#connect-to-virtual-machine).|
   | **Rozmiar maszyny wirtualnej** | Dowolny prawidłowy rozmiar | Domyślny w tym szablonie **Standard_B2s** jest wystarczający do tego szybkiego startu. |
   | **Lokalizacja**|[resourceGroup().location].| Nie zmieniaj tej wartości. |
   | **Nazwa sieci wirtualnej**|Sieć wirtualna, w której utworzono wystąpienie zarządzane.|
   | **Nazwa podsieci**|Nazwa podsieci utworzonej w poprzedniej procedurze| Nie wybieraj podsieci, w której utworzono wystąpienie zarządzane.|
   | **lokalizacje artefaktów** | [deployment().properties.templateLink.uri] | Nie zmieniaj tej wartości. |
   | **artefakty Token Sasa lokalizacji** | pozostaw puste | Nie zmieniaj tej wartości. |

   ![tworzenie maszyny wirtualnej klienta](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   Jeśli podczas tworzenia wystąpienia zarządzanego użyto sugerowanej nazwy sieci wirtualnej i domyślnej [podsieci,](sql-database-managed-instance-get-started.md)nie trzeba zmieniać dwóch ostatnich parametrów. W przeciwnym razie należy zmienić te wartości na wartości wprowadzone podczas konfigurowania środowiska sieciowego.

3. Zaznacz pole **wyboru Zgadzam się z warunkami podanymi powyżej.**
4. Wybierz **opcję Zakup,** aby wdrożyć maszynę wirtualną platformy Azure w sieci.
5. Wybierz ikonę **Powiadomienia**, aby wyświetlić stan wdrożenia.

> [!IMPORTANT]
> Nie należy kontynuować do około 15 minut po utworzeniu maszyny wirtualnej, aby dać czas na skrypty po utworzeniu zainstalować SQL Server Management Studio.

## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Poniższe kroki pokazują, jak nawiązać połączenie z nowo utworzoną maszyną wirtualną przy użyciu połączenia pulpitu zdalnego.

1. Po zakończeniu wdrażania przejdź do zasobu maszyny wirtualnej.

    ![VM](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. Wybierz przycisk **Połącz**.

   Zostanie wyświetlony formularz pliku protokołu usługi pulpitu zdalnego (plik rdp) z publicznym adresem IP i numerem portu maszyny wirtualnej.

   ![Formularz RDP](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. Wybierz opcję **Pobierz plik RDP**.

   > [!NOTE]
   > Można również użyć SSH, aby połączyć się z maszyną wirtualną.

4. Zamknij formularz **Połącz z maszyną wirtualną.**
5. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP.
6. Po wyświetleniu monitu wybierz pozycję **Połącz**. Na komputerze Mac należy skorzystać z klienta RDP, takiego jak ten [klient pulpitu zdalnego](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) ze sklepu Mac App Store.

7. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej, a następnie wybierz **przycisk OK**.

8. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz **pozycję Tak** lub **Kontynuuj,** aby kontynuować połączenie.

Połączenie z maszyną wirtualną jest połączone na pulpicie nawigacyjnym Menedżera serwera.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Łączenie się z wystąpieniem zarządzanym za pomocą usługi SSMS

1. Na maszynie wirtualnej otwórz program SQL Server Management Studio (SSMS).

   To trwa kilka chwil, aby otworzyć, ponieważ musi zakończyć swoją konfigurację, ponieważ jest to pierwszy raz, gdy SSMS został uruchomiony.
2. W oknie dialogowym **Łączenie z serwerem** wprowadź w pełni kwalifikowaną **nazwę hosta** wystąpienia zarządzanego w polu **Nazwa serwera.** Wybierz **pozycję Uwierzytelnianie programu SQL Server**, podaj nazwę użytkownika i hasło, a następnie wybierz pozycję **Połącz**.

    ![nawiązywanie połączenia w programie ssms](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Po nawiązaniu połączenia możesz wyświetlić systemowe bazy danych oraz bazy danych użytkownika w węźle Bazy danych, a także różne obiekty w węzłach Zabezpieczenia, Obiekty serwera, Replikacja, Zarządzanie, Agent programu SQL Server oraz Profiler systemu XEvent.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać przewodnik Szybki start pokazujący, jak łączyć się z lokalnego komputera klienckiego przy użyciu połączenia typu punkt-lokacja, zobacz [Konfigurowanie połączenia typu punkt-lokacja](sql-database-managed-instance-configure-p2s.md).
- Aby uzyskać omówienie opcji połączenia dla aplikacji, zobacz artykuł [Łączenie aplikacji z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md).
- Aby przywrócić istniejącą bazę danych programu SQL Server z lokalnego wystąpienia do wystąpienia zarządzanego, można użyć [usługi migracji bazy danych Azure Database (DMS) do migracji](../dms/tutorial-sql-server-to-managed-instance.md) lub polecenia [T-SQL RESTORE,](sql-database-managed-instance-get-started-restore.md) aby przywrócić z pliku kopii zapasowej bazy danych.
