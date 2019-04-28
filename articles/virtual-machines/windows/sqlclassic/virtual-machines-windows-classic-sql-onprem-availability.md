---
title: Rozszerzanie lokalnych zawsze włączone grupy dostępności na platformie Azure | Dokumentacja firmy Microsoft
description: Ten samouczek używa zasobów utworzonych za pomocą klasycznego modelu wdrażania i opisuje sposób Użyj Kreatora dodawania repliki w SQL Server Management Studio (SSMS), aby dodać replikę zawsze włączonych grup dostępności na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: d3e56f1741a9cfd3f2d9f786c2ce22eb6a946ef2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481624"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Rozszerzanie lokalnych zawsze włączone grupy dostępności na platformie Azure
Zawsze włączone grupy dostępności zapewniają wysoką dostępność dla grupy bazy danych, dodając replik pomocniczych. Zezwalaj na tych replik przechodzenia w tryb failover bazy danych w razie awarii. Ponadto użyciem odciążania obciążeniami odczytu lub zadania tworzenia kopii zapasowych.

Inicjowanie obsługi administracyjnej przynajmniej jednej maszyny wirtualnej platformy Azure z programem SQL Server, a następnie dodanie ich jako repliki do grupy dostępności w środowisku lokalnym, można rozszerzyć lokalnych grup dostępności w systemie Microsoft Azure.

W tym samouczku przyjęto założenie, że dysponujesz następującymi elementami:

* Aktywna subskrypcja platformy Azure. Możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Istniejące zawsze włączonej grupy dostępności w środowisku lokalnym. Aby uzyskać więcej informacji dotyczących grup dostępności, zobacz [zawsze włączonych grup dostępności](https://msdn.microsoft.com/library/hh510230.aspx).
* Połączenie między siecią lokalną a siecią wirtualną platformy Azure. Aby uzyskać więcej informacji na temat tworzenia tej sieci wirtualnej, zobacz [utworzyć połączenie lokacja-lokacja przy użyciu witryny Azure portal (klasyczny)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługi Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

## <a name="add-azure-replica-wizard"></a>Dodaj kreatora repliki platformy Azure
W tej sekcji dowiesz się, jak używać **Kreatora dodawania repliki platformy Azure** rozszerzyć rozwiązanie zawsze włączonej grupy dostępności, aby uwzględnić repliki platformy Azure.

> [!IMPORTANT]
> **Kreatora dodawania repliki platformy Azure** obsługuje tylko maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania. Nowych wdrożeń maszyn wirtualnych należy używać nowy model usługi Resource Manager. Jeśli używasz maszyn wirtualnych przy użyciu usługi Resource Manager, należy ręcznie dodać repliki pomocniczej platformy Azure przy użyciu języka Transact-SQL commmands (nie pokazane tutaj). Ten kreator nie będzie działać w tym scenariuszu usługi Resource Manager.

1. Z poziomu programu SQL Server Management Studio, rozwiń węzeł **zawsze włączona wysoka dostępność** > **grup dostępności** > **[Nazwa grupy dostępności]**.
2. Kliknij prawym przyciskiem myszy **replik dostępności**, następnie kliknij przycisk **dodać repliki**.
3. Domyślnie **dodać repliki z Kreatora grup dostępności** jest wyświetlana. Kliknij przycisk **Dalej**.  Jeśli wybrano **nie pokazuj tej strony ponownie** opcję w dolnej części strony podczas poprzedniego uruchomienia tego kreatora, a ten ekran nie będą wyświetlane.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Trzeba będzie połączyć się z wszystkich istniejących replik pomocniczych. Możesz kliknąć **Connect...** obok każdej repliki, lub kliknąć **łączenie wszystkich...** w dolnej części ekranu. Po uwierzytelnieniu kliknij **dalej** aby przejść do następnego ekranu.
5. Na **Określanie replik** stronie wielu kart są wyświetlane w górnej części: **Repliki**, **punktów końcowych**, **kopii zapasowej preferencje**, i **odbiornika**. Z **replik** kliknij pozycję **dodawania repliki platformy Azure...** Aby uruchomić Kreatora dodawania repliki platformy Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Wybierz istniejący certyfikat zarządzania platformy Azure z lokalnego magazynu certyfikatów Windows, jeśli zainstalowano jeden przed. Wybierz lub wprowadź identyfikator subskrypcji platformy Azure, jeśli używano przed. Możesz kliknąć Pobierz, aby pobrać i zainstalować certyfikat zarządzania platformy Azure i Pobierz listę subskrypcji przy użyciu konta platformy Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Zostaną wyświetlone każdego pola na stronie z wartościami, które będą używane do utworzenia maszyny wirtualnej (maszyny Wirtualnej Azure), który będzie obsługiwał replikę.
   
   | Ustawienie | Opis |
   | --- | --- |
   | **Obraz** |Wybierz odpowiednią kombinację systemu operacyjnego i programu SQL Server |
   | **Rozmiar maszyny Wirtualnej** |Wybierz rozmiar maszyny Wirtualnej, która najlepiej pasuje do potrzeb biznesowych |
   | **Nazwa maszyny Wirtualnej** |Określ unikatową nazwę dla nowej maszyny Wirtualnej. Nazwa musi składać się z od 3 do 15 znaków, może zawierać tylko litery, cyfry i łączniki, musi rozpoczynać się literą i kończyć literą lub cyfrą. |
   | **Nazwa użytkownika maszyny Wirtualnej** |Określ nazwę użytkownika, który ma zostać konta administratora na maszynie Wirtualnej |
   | **Hasło administratora maszyny Wirtualnej** |Określ hasło dla nowego konta |
   | **Potwierdź hasło** |Potwierdź hasło dla nowego konta |
   | **Virtual Network** |Określ sieć wirtualna platformy Azure, który ma być używany w nowej maszyny Wirtualnej. Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz [Omówienie usługi Virtual Network](../../../virtual-network/virtual-networks-overview.md). |
   | **Virtual Network Subnet** |Określ podsieci sieci wirtualnej, który ma być używany w nowej maszyny Wirtualnej |
   | **Domeny** |Upewnij się, że wartość wstępnie wypełnione dla domeny jest poprawna |
   | **Domena nazwa użytkownika** |Określ konto które znajduje się w grupie administratorów lokalnych na węzłach klastra lokalnego |
   | **Hasło** |Określ hasło dla nazwy użytkownika domeny |
8. Kliknij przycisk **OK** można zweryfikować ustawień wdrażania.
9. Postanowienia prawne są wyświetlane obok. Przeczytaj i kliknij przycisk **OK** Jeśli akceptujesz te postanowienia.
10. **Określanie replik** ponownie zostanie wyświetlona strona. Sprawdź ustawienia nowej repliki platformy Azure na **replik**, **punktów końcowych**, i **preferencji kopii zapasowej** karty. Zmodyfikuj ustawienia, aby spełnić wymagania biznesowe.  Aby uzyskać więcej informacji na temat parametrów znajdujących się na tych kartach, zobacz [Określanie replik strony (Kreatora nowej grupy dostępności Kreator/Dodaj repliki)](https://msdn.microsoft.com/library/hh213088.aspx). Należy pamiętać, że odbiorniki nie można utworzyć za pomocą karty odbiornika dla grupy dostępności, zawierające repliki platformy Azure. Ponadto jeśli odbiornik został już utworzony przed uruchomieniem kreatora, otrzymasz komunikat wskazujący, że nie jest obsługiwana na platformie Azure. Przedstawiony zostanie sposób tworzenia słuchaczy w **Utwórz odbiornik grupy dostępności** sekcji.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Kliknij przycisk **Dalej**.
12. Wybierz metodę synchronizacji danych, którego chcesz użyć na **Wybierz początkową synchronizację danych** strony, a następnie kliknij przycisk **dalej**. W większości przypadków zaznacz **pełną synchronizację danych**. Aby uzyskać więcej informacji o metodach synchronizowania danych, zobacz [zaznaczyć dane synchronizacji stronę początkową (zawsze na dostępność grupy kreatorzy)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Przejrzyj wyniki na **weryfikacji** strony. Popraw zagadnienia i ponownie uruchom sprawdzenie poprawności, jeśli to konieczne. Kliknij przycisk **Dalej**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Sprawdź ustawienia na **Podsumowanie** stronie, a następnie kliknij przycisk **Zakończ**.
15. Rozpocznie się proces inicjowania obsługi administracyjnej. Po pomyślnym zakończeniu działania kreatora, kliknij przycisk **Zamknij** aby wyjść z kreatora.

> [!NOTE]
> Kreatora dodawania repliki platformy Azure tworzy plik dziennika w Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Ten plik dziennika umożliwia rozwiązywanie problemów z wdrożeniami repliki platformy Azure nie powiodło się. W przypadku wykonywania żadnych czynności w kreatorze nie powiedzie się, wszystkich poprzednich operacji są wycofywane, łącznie z usunięciem aprowizowanej maszyny Wirtualnej.
> 
> 

## <a name="create-an-availability-group-listener"></a>Utwórz odbiornik grupy dostępności
Po utworzeniu grupy dostępności, należy utworzyć odbiornik, aby klienci mogli nawiązać połączenie z repliki. Odbiorniki bezpośrednie połączenia przychodzące do podstawowej lub pomocniczej replice tylko do odczytu. Aby uzyskać więcej informacji na temat odbiorników, zobacz [Konfigurowanie odbiornika ILB dla zawsze włączonych grup dostępności na platformie Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Kolejne kroki
Oprócz używania **Kreatora dodawania repliki platformy Azure** można rozszerzyć swoje zawsze włączonej grupy dostępności na platformie Azure, możesz również przenieść niektórych obciążeń programu SQL Server całkowicie na platformie Azure. Aby rozpocząć pracę, zobacz [inicjowania obsługi administracyjnej maszyny wirtualnej programu SQL Server na platformie Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Aby uzyskać inne tematy związane z programem SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

