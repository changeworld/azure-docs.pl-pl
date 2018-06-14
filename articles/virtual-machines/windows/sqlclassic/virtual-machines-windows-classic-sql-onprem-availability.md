---
title: Rozszerzanie lokalnymi zawsze włączone grupy dostępności na platformie Azure | Dokumentacja firmy Microsoft
description: W tym samouczku są używane zasoby utworzone za pomocą klasycznym modelu wdrażania i informacje dotyczące używania kreatora Dodawanie repliki w programu SQL Server Management Studio (SSMS) można dodać repliki zawsze włączonej grupy dostępności na platformie Azure.
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
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29400481"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Rozszerzanie lokalnymi zawsze włączone grupy dostępności na platformie Azure
Zawsze włączone grupy dostępności zapewnia wysoką dostępność dla grup bazy danych, dodając replikach pomocniczych. Zezwalaj na tych replik przechodzenie w tryb failover baz danych w przypadku awarii. Ponadto one może służyć do odciążenia odczytu obciążeń lub zadania tworzenia kopii zapasowych.

Możesz rozszerzyć lokalnych grup dostępności do systemu Microsoft Azure przez inicjowania obsługi administracyjnej przynajmniej jednej maszyny wirtualnej Azure z programem SQL Server, a następnie dodanie ich jako repliki do lokalnych grup dostępności.

W tym samouczku przyjęto założenie, że należy dysponować następującymi elementami:

* Aktywna subskrypcja platformy Azure. Możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Istniejące zawsze włączonej grupy dostępności lokalnego. Aby uzyskać więcej informacji dotyczących grup dostępności, zobacz [zawsze włączonych grup dostępności](https://msdn.microsoft.com/library/hh510230.aspx).
* Połączenie między siecią lokalną i sieci wirtualnej platformy Azure. Aby uzyskać więcej informacji o tworzeniu tej sieci wirtualnej, zobacz [utworzyć połączenie lokacja-lokacja przy użyciu portalu Azure (klasyczne)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

## <a name="add-azure-replica-wizard"></a>Dodaj kreatora Azure repliki
W tej sekcji przedstawia sposób użycia **Kreatora dodawania repliki Azure** do rozszerzenia rozwiązania zawsze włączonej grupy dostępności, aby uwzględnić Azure replik.

> [!IMPORTANT]
> **Kreatora dodawania repliki Azure** obsługuje tylko maszyny wirtualne utworzone za pomocą klasycznego modelu wdrożenia. Nowe wdrożenia maszyny Wirtualnej należy używać nowszej modelu Resource Manager. Jeśli korzystasz z maszyn wirtualnych za pomocą Menedżera zasobów, należy ręcznie dodać repliki pomocniczej Azure przy użyciu języka Transact-SQL commmands (nie jest tutaj widoczny). Ten kreator nie będzie działać w scenariuszu Menedżera zasobów.

1. Od w ramach programu SQL Server Management Studio rozwiń **zawsze na wysoką dostępność** > **grup dostępności** > **[Nazwa grupy dostępności]**.
2. Kliknij prawym przyciskiem myszy **replik dostępności**, następnie kliknij przycisk **dodać repliki**.
3. Domyślnie **dodać repliki dostępności grupy kreatora** jest wyświetlany. Kliknij przycisk **Dalej**.  Jeśli wybrano **nie pokazuj tej strony ponownie** opcji w dolnej części strony podczas poprzedniego uruchomienia tego kreatora, ten ekran nie będą wyświetlane.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Trzeba będzie do nawiązania połączenia wszystkich istniejących replik pomocniczych. Możesz kliknąć **połączenia...** obok każdej replice lub kliknąć **wszystkie połączenia...** w dolnej części ekranu. Po uwierzytelnieniu kliknij **dalej** aby przejść do następnego ekranu.
5. Na **Określanie replik** strony, wiele kart są wyświetlane u góry: **replik**, **punkty końcowe**, **preferencji tworzenia kopii zapasowej**, i **odbiornika**. Z **replik** , kliknij pozycję **dodać repliki Azure...** Aby uruchomić Kreatora dodawania repliki Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Wybierz istniejący certyfikat zarządzania platformy Azure z lokalnego magazynu certyfikatów systemu Windows, jeśli została zainstalowana przed. Wybierz lub wprowadź identyfikator subskrypcji platformy Azure, jeśli użyto jednego przed. Możesz kliknąć pobierania, aby pobrać i zainstalować certyfikat zarządzania platformy Azure i pobieranie listy subskrypcji przy użyciu konta platformy Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Wypełnisz każdego pola na stronie z wartościami, które będą używane do tworzenia maszyny wirtualnej (maszyny Wirtualnej Azure), który będzie obsługiwał replikę.
   
   | Ustawienie | Opis |
   | --- | --- |
   | **Obraz** |Wybierz żądaną kombinację systemu operacyjnego i programu SQL Server |
   | Rozmiar maszyny Wirtualnej |Wybierz rozmiar maszyny Wirtualnej, który najlepiej odpowiada Twoim potrzebom biznesowa |
   | **Nazwa maszyny Wirtualnej** |Określ unikatową nazwę dla nowej maszyny Wirtualnej. Nazwa musi zawierać od 3 do 15 znaków, może zawierać tylko litery, cyfry i łączniki, musi zaczynać się literą i kończyć literą lub cyfrą. |
   | **Nazwa użytkownika maszyny Wirtualnej** |Określ nazwę użytkownika, który ma zostać konta administratora na maszynie Wirtualnej |
   | **Hasło administratora maszyny Wirtualnej** |Określ hasło dla nowego konta |
   | **Potwierdź hasło** |Potwierdź hasło dla nowego konta |
   | **Virtual Network** |Określ sieć wirtualna platformy Azure, która powinna być używana w nowej maszyny Wirtualnej. Aby uzyskać więcej informacji o sieciach wirtualnych, zobacz [omówienie sieci wirtualnych](../../../virtual-network/virtual-networks-overview.md). |
   | **Podsieć sieci wirtualnej** |Określ podsieć sieci wirtualnej, która powinna być używana w nowej maszyny Wirtualnej |
   | **Domeny** |Potwierdź poprawność wstępnie wypełnione wartości dla domeny |
   | **Nazwa użytkownika domeny** |Określ konto do grupy administratorów lokalnych na węzłach klastra lokalnego |
   | **Hasło** |Podaj hasło dla nazwy użytkownika domeny |
8. Kliknij przycisk **OK** można sprawdzić poprawności ustawień wdrażania.
9. Postanowienia prawne są wyświetlane obok. Przeczytaj i kliknij przycisk **OK** Jeśli zgadzasz się na te warunki.
10. **Określanie replik** ponownie zostanie wyświetlona strona. Sprawdź ustawienia nowej repliki Azure na **replik**, **punkty końcowe**, i **preferencji tworzenia kopii zapasowej** karty. Zmodyfikuj ustawienia w celu spełnienia wymagań biznesowych.  Aby uzyskać więcej informacji o parametrach zawartych w tych kartach, zobacz [określanie strony repliki (Kreatora nowej grupy dostępności Kreator/Dodaj repliki)](https://msdn.microsoft.com/library/hh213088.aspx). Należy pamiętać, że odbiorników nie można utworzyć przy użyciu karty odbiornika dla grupy dostępności, które zawierają Azure replik. Ponadto jeśli odbiornik został już utworzony przed uruchomieniem kreatora, zostanie wyświetlony komunikat informujący, że nie jest obsługiwana na platformie Azure. Przedstawiono sposób tworzenia odbiorników w **tworzenia odbiornika grupy dostępności** sekcji.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Kliknij przycisk **Dalej**.
12. Wybierz metodę synchronizacji danych, którego chcesz użyć na **Wybierz początkową synchronizację danych** i kliknij przycisk **dalej**. W przypadku większości scenariuszy wybrać **pełną synchronizację danych**. Aby uzyskać więcej informacji o metodach synchronizowania danych, zobacz [wybierz danych synchronizacji stronę początkową (zawsze na dostępność grupy kreatorów)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Przejrzyj wyniki na **weryfikacji** strony. Popraw nierozwiązane problemy i ponownie uruchom sprawdzenie poprawności, jeśli to konieczne. Kliknij przycisk **Dalej**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Sprawdź ustawienia na **Podsumowanie** strony, a następnie kliknij przycisk **Zakończ**.
15. Rozpocznie się proces inicjowania obsługi administracyjnej. Po pomyślnym zakończeniu działania kreatora, kliknij przycisk **Zamknij** aby wyjść z kreatora.

> [!NOTE]
> Kreator dodawania repliki Azure tworzy plik dziennika w Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Ten plik dziennika umożliwia rozwiązywanie problemów z wdrożeniami repliki Azure nie powiodło się. W przypadku niepowodzenia wykonywania żadnych czynności w Kreatorze wszystkich poprzednich operacji są wycofywane, włącznie z usunięciem zainicjowana VM.
> 
> 

## <a name="create-an-availability-group-listener"></a>Utwórz odbiornik grupy dostępności
Po utworzeniu grupy dostępności, należy utworzyć odbiornika dla klientom na łączenie się z replikami. Odbiorniki bezpośrednie połączenia przychodzące do podstawowej lub pomocniczej replice tylko do odczytu. Aby uzyskać więcej informacji na odbiorników, zobacz [skonfigurować odbiornik ILB dla zawsze włączonych grup dostępności na platformie Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Kolejne kroki
Oprócz używania **Kreatora dodawania repliki Azure** do rozszerzenia Twojej zawsze włączonej grupy dostępności na platformie Azure, to może również przenieść niektórych obciążeń programu SQL Server całkowicie na platformie Azure. Aby rozpocząć, zobacz [inicjowania obsługi maszyny wirtualnej programu SQL Server na platformie Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Do innych tematów związanych z programem SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

