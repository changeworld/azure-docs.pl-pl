---
title: Rozszerzanie lokalnych grup dostępności zawsze dostępnych na platformę Azure | Dokumenty firmy Microsoft
description: W tym samouczku użyto zasobów utworzonych za pomocą klasycznego modelu wdrażania i opisano sposób używania kreatora Dodawanie repliki w programie SQL Server Management Studio (SSMS) do dodawania repliki grupy zawsze włączonej dostępności na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 4521c2c112c93e83144cfc84d600208817b2ccac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978046"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Rozszerzanie lokalnych, zawsze dostępnych grup dostępności na platformę Azure
Zawsze włączone grupy dostępności zapewniają wysoką dostępność dla grup bazy danych przez dodanie replik pomocniczych. Te repliki umożliwiają wywiązywki w błąd baz danych w przypadku awarii. Ponadto mogą one służyć do odciążania obciążeń odczytu lub zadań tworzenia kopii zapasowych.

Lokalne grupy dostępności można rozszerzyć na platformę Microsoft Azure, inicjując inicjowanie obsługi administracyjnej co najmniej jednej maszyny wirtualnej platformy Azure za pomocą programu SQL Server, a następnie dodając je jako repliki do lokalnych grup dostępności.

W tym samouczku założono, że masz następujące elementy:

* Aktywna subskrypcja platformy Azure. Możesz [zapisać się na bezpłatną wersję próbną](https://azure.microsoft.com/pricing/free-trial/).
* Istniejąca grupa zawsze na dostępność lokalnie. Aby uzyskać więcej informacji na temat grup dostępności, zobacz [Zawsze włączone grupy dostępności](https://msdn.microsoft.com/library/hh510230.aspx).
* Łączność między siecią lokalną a siecią wirtualną platformy Azure. Aby uzyskać więcej informacji na temat tworzenia tej sieci [wirtualnej, zobacz Tworzenie połączenia lokacja lokacja przy użyciu portalu Azure (klasyczny)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Menedżer zasobów i Klasyczny](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano przy użyciu modelu wdrażania klasycznego. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

## <a name="add-azure-replica-wizard"></a>Kreator dodawania replik platformy Azure
W tej sekcji pokazano, jak za pomocą **Kreatora dodawania replik platformy Azure,** aby rozszerzyć rozwiązanie grupy zawsze włączone do dostępności, aby uwzględnić repliki platformy Azure.

> [!IMPORTANT]
> **Kreator dodawania repliki platformy Azure** obsługuje tylko maszyny wirtualne utworzone za pomocą klasycznego modelu wdrażania. Nowe wdrożenia maszyn wirtualnych powinny używać nowszego modelu Menedżera zasobów. Jeśli używasz maszyn wirtualnych z Menedżerem zasobów, należy ręcznie dodać pomocniczą replikę platformy Azure przy użyciu poleceń Transact-SQL (nie pokazano tutaj). Ten kreator nie będzie działać w scenariuszu Menedżera zasobów.

1. Z poziomu programu SQL Server Management Studio rozwiń rozwiń grupy**zawsze** >  **przy wysokiej dostępności** > **[Nazwa grupy dostępności]**.
2. Kliknij prawym przyciskiem myszy **pozycję Repliki dostępności**, a następnie kliknij polecenie **Dodaj replikę**.
3. Domyślnie wyświetlany jest **Kreator grupy Dodaj replikę do dostępności.** Kliknij przycisk **alej**.  Jeśli podczas poprzedniego uruchomienia tego kreatora wybrano opcję **Nie pokazuj ponownie tej strony** u dołu strony, ten ekran nie będzie wyświetlany.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Konieczne będzie połączenie ze wszystkimi istniejącymi replikami pomocniczymi. Możesz kliknąć **na Połącz...** obok każdej repliki lub można kliknąć **Połącz wszystko...** w dolnej części ekranu. Po uwierzytelnieniu kliknij przycisk **Dalej,** aby przejść do następnego ekranu.
5. Na stronie **Określanie replik** u góry znajduje się wiele kart: **Repliki,** **Punkty końcowe,** **Preferencje kopii zapasowej**i **Odbiornik**. Na karcie **Repliki** kliknij pozycję **Dodaj replikę platformy Azure...** , aby uruchomić Kreatora dodawania replik platformy Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Wybierz istniejący certyfikat usługi Azure Management z lokalnego magazynu certyfikatów systemu Windows, jeśli został zainstalowany wcześniej. Wybierz lub wprowadź identyfikator subskrypcji platformy Azure, jeśli był używany wcześniej. Możesz kliknąć przycisk Pobierz, aby pobrać i zainstalować certyfikat zarządzania platformy Azure i pobrać listę subskrypcji przy użyciu konta platformy Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Wypełnisz każde pole na stronie wartościami, które będą używane do tworzenia maszyny wirtualnej platformy Azure (VM), która będzie obsługiwać replikę.
   
   | Ustawienie | Opis |
   | --- | --- |
   | **Obrazu** |Wybierz żądaną kombinację systemu operacyjnego i programu SQL Server |
   | **Rozmiar maszyny Wirtualnej** |Wybierz rozmiar maszyny Wirtualnej, który najlepiej odpowiada Twoim potrzebom biznesowym |
   | **Nazwa maszyny Wirtualnej** |Określ unikatową nazwę dla nowej maszyny Wirtualnej. Nazwa musi zawierać od 3 do 15 znaków, może zawierać tylko litery, cyfry i łączniki i musi zaczynać się od litery i kończyć literą lub cyfrą. |
   | **Nazwa użytkownika maszyny Wirtualnej** |Określ nazwę użytkownika, która stanie się kontem administratora na maszynie wirtualnej |
   | **Hasło administratora maszyny Wirtualnej** |Określanie hasła dla nowego konta |
   | **Potwierdź hasło** |Potwierdź hasło nowego konta |
   | **Sieć wirtualna** |Określ sieć wirtualną platformy Azure, której powinna używać nowa maszyna wirtualna. Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz [Omówienie sieci wirtualnej](../../../virtual-network/virtual-networks-overview.md). |
   | **Podsieć sieci wirtualnej** |Określ podsieć sieci wirtualnej, której powinna używać nowa maszyna wirtualna |
   | **Domain** |Upewnij się, że wstępnie wypełniona wartość domeny jest poprawna |
   | **Nazwa użytkownika domeny** |Określanie konta w grupie administratorów lokalnych w lokalnych węzłach klastra |
   | **Hasło** |Określanie hasła do nazwy użytkownika domeny |
8. Kliknij **przycisk OK,** aby sprawdzić poprawność ustawień wdrażania.
9. Terminy prawne są wyświetlane w następnej kolejności. Przeczytaj i kliknij **przycisk OK,** jeśli zgadzasz się na te warunki.
10. Strona **Określ repliki** zostanie wyświetlona ponownie. Sprawdź ustawienia nowej repliki platformy Azure na **kartach Repliki,** **Punkty końcowe**i **Preferencje kopii zapasowej.** Modyfikuj ustawienia, aby spełnić wymagania biznesowe.  Aby uzyskać więcej informacji na temat parametrów zawartych na tych kartach, zobacz [Określanie strony replik (Kreator nowej grupy dostępności/Kreator dodawania replik).](https://msdn.microsoft.com/library/hh213088.aspx) Należy zauważyć, że odbiorników nie można utworzyć przy użyciu karty Odbiornik dla grup dostępności, które zawierają repliki platformy Azure. Ponadto jeśli odbiornik został już utworzony przed uruchomieniem Kreatora, zostanie wyświetlony komunikat informujący, że nie jest obsługiwany na platformie Azure. Przyjrzymy się, jak utworzyć detektory w sekcji **Tworzenie odbiornika grupy dostępności.**
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Kliknij przycisk **alej**.
12. Wybierz metodę synchronizacji danych, której chcesz użyć na stronie **Wybieranie początkowej synchronizacji danych,** a następnie kliknij przycisk **Dalej**. W przypadku większości scenariuszy wybierz opcję **Pełna synchronizacja danych**. Aby uzyskać więcej informacji na temat metod synchronizacji danych, zobacz [Wybieranie strony początkowej synchronizacji danych (Kreatory grup zawsze przy dostępności).](https://msdn.microsoft.com/library/hh231021.aspx)
13. Przejrzyj wyniki na stronie **Sprawdzanie poprawności.** Rozwiąć nierozstrzygnięte problemy i w razie potrzeby ponownie uruchomić sprawdzanie poprawności. Kliknij przycisk **alej**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Przejrzyj ustawienia na stronie **Podsumowanie,** a następnie kliknij przycisk **Zakończ**.
15. Rozpoczyna się proces inicjowania obsługi administracyjnej. Po pomyślnym zakończeniu pracy kreatora kliknij przycisk **Zamknij,** aby wyjść z kreatora.

> [!NOTE]
> Kreator dodawania repliki platformy Azure tworzy plik dziennika w pliku Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Ten plik dziennika może służyć do rozwiązywania problemów z nieudanymi wdrożeniami replik platformy Azure. Jeśli Kreator nie wykonuje żadnej akcji, wszystkie poprzednie operacje są przywracane, w tym usuwanie aprowizowaną maszynę wirtualną.
> 
> 

## <a name="create-an-availability-group-listener"></a>Tworzenie odbiornika grupy dostępności
Po utworzeniu grupy dostępności należy utworzyć odbiornik dla klientów, aby połączyć się z replikami. Odbiorniki kierować połączenia przychodzące do repliki pomocniczej podstawowej lub tylko do odczytu. Aby uzyskać więcej informacji na temat odbiorników, zobacz [Konfigurowanie odbiornika równoważenia obciążenia sieciowego dla grup dostępności zawsze włączone na platformie Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Następne kroki
Oprócz korzystania z **Kreatora dodawania replik platformy Azure** w celu rozszerzenia grupy zawsze na dostępność na platformę Azure, można również przenieść niektóre obciążenia programu SQL Server całkowicie na platformę Azure. Aby rozpocząć, zobacz [Inicjowanie obsługi administracyjnej maszyny wirtualnej programu SQL Server na platformie Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Aby zapoznać się z innymi tematami związanymi z uruchamianiem programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na maszynach wirtualnych platformy Azure.](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

