---
title: SQL Server na Windows Virtual Machines na platformie Azure — często zadawane pytania | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące uruchamiania SQL Server na maszynach wirtualnych platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-shysun
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 4b50b4acf6ea655c40821e7c49824af11aeeb9ab
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816305"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Często zadawane pytania dotyczące SQL Server uruchomionych na maszynach wirtualnych z systemem Windows na platformie Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Ten artykuł zawiera odpowiedzi na niektóre z najczęstszych pytań dotyczących uruchamiania [SQL Server na Windows Virtual Machines na platformie Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> W tym artykule omówiono problemy dotyczące SQL Server na maszynach wirtualnych z systemem Windows. Jeśli używasz maszyn wirtualnych SQL Server on Linux, zobacz [często zadawane pytania dotyczące systemu Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>Rastrow

1. **Jakie są dostępne obrazy z galerii maszyn wirtualnych SQL Server?** 

   Platforma Azure przechowuje obrazy maszyn wirtualnych dla wszystkich obsługiwanych głównych wersji SQL Server we wszystkich wersjach systemu Windows i Linux. Aby uzyskać więcej informacji, zobacz kompletna lista [obrazów maszyn wirtualnych z systemem Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) i [obrazów maszyn wirtualnych](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create)z systemem Linux.

1. **Czy istnieją zaktualizowane obrazy galerii maszyn wirtualnych SQL Server?**

   Co dwa miesiące SQL Server obrazy w galerii maszyn wirtualnych są aktualizowane przy użyciu najnowszych aktualizacji systemu Windows i Linux. W przypadku obrazów systemu Windows obejmuje to wszystkie aktualizacje oznaczone jako ważne w Windows Update, w tym ważne SQL Server aktualizacje zabezpieczeń i dodatki Service Pack. W przypadku obrazów systemu Linux obejmuje to najnowsze aktualizacje systemowe. SQL Server aktualizacje zbiorcze są obsługiwane inaczej dla systemów Linux i Windows. W przypadku systemu Linux w odświeżeniu są również uwzględniane SQL Server aktualizacje zbiorcze. Jednak w tej chwili maszyny wirtualne z systemem Windows nie są aktualizowane przy użyciu aktualizacji zbiorczych SQL Server lub systemu Windows Server.

1. **Czy SQL Server obrazy maszyn wirtualnych zostaną usunięte z galerii?**

   Tak. System Azure obsługuje tylko jeden obraz na wersję główną i wydanie. Na przykład po wydaniu nowego dodatku Service Pack SQL Server platforma Azure dodaje nowy obraz do galerii dla tego dodatku Service Pack. Obraz SQL Server dla poprzedniego dodatku Service Pack zostanie natychmiast usunięty z Azure Portal. Jest on jednak nadal dostępny do aprowizacji z programu PowerShell w ciągu następnych trzech miesięcy. Po trzech miesiącach Poprzedni obraz dodatku Service Pack nie jest już dostępny. Te zasady usuwania również będą stosowane, jeśli wersja SQL Server nie będzie obsługiwana, gdy osiągnie koniec cyklu życia.


1. **Czy jest możliwe wdrożenie starszego obrazu SQL Server, który nie jest widoczny w Azure Portal?**

   Tak, przy użyciu programu PowerShell. Aby uzyskać więcej informacji na temat wdrażania maszyn wirtualnych SQL Server przy użyciu programu PowerShell, zobacz [jak zainicjować obsługę administracyjną SQL Server maszyny wirtualne w Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Czy mogę utworzyć obraz VHD na podstawie SQL Server maszyny wirtualnej?**

   Tak, ale istnieją pewne zagadnienia. Jeśli ten wirtualny dysk twardy zostanie wdrożony na nowej maszynie wirtualnej na platformie Azure, nie uzyskasz sekcji Konfiguracja SQL Server w portalu. Następnie należy zarządzać SQL Server opcjami konfiguracji za pomocą programu PowerShell. Ponadto opłata zostanie naliczona za według stawki za maszynę wirtualną SQL, na której pierwotnie oparto obraz. Jest to prawdziwe nawet w przypadku usunięcia SQL Server z dysku VHD przed wdrożeniem. 

1. **Czy można skonfigurować konfiguracje, które nie są wyświetlane w galerii maszyn wirtualnych (na przykład Windows 2008 R2 + SQL Server 2012)?**

   Nie. W przypadku obrazów galerii maszyn wirtualnych, które zawierają SQL Server, należy wybrać jeden z podanych obrazów za pośrednictwem Azure Portal lub za pośrednictwem [programu PowerShell](virtual-machines-windows-ps-sql-create.md). 


## <a name="creation"></a>Tworzenie

1. **Jak mogę utworzyć maszynę wirtualną platformy Azure z SQL Server?**

   Najłatwiej jest utworzyć maszynę wirtualną, która zawiera SQL Server. Aby zapoznać się z samouczkiem dotyczącym rejestracji w usłudze Azure i tworzenia maszyny wirtualnej SQL z poziomu portalu, zobacz temat [Inicjowanie obsługi maszyny wirtualnej SQL Server w Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Możesz wybrać obraz maszyny wirtualnej, który korzysta z licencjonowania SQL Server z opcją płatność za sekundę, lub możesz użyć obrazu, który pozwala na korzystanie z własnej licencji SQL Server. Istnieje również możliwość ręcznej instalacji SQL Server na maszynie wirtualnej z bezpłatną licencjonowaną wersją (Developer lub Express) lub przez ponowne użycie licencji lokalnej. W przypadku posiadania własnej licencji musisz mieć [Przenośność licencji w ramach programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/). Aby uzyskać więcej informacji, zobacz [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).

1. **Jak można migrować lokalną bazę danych SQL Server do chmury?**

   Najpierw utwórz maszynę wirtualną platformy Azure z wystąpieniem SQL Server. Następnie Przeprowadź migrację lokalnych baz danych do tego wystąpienia. Aby uzyskać strategie migracji danych, zobacz [Migrowanie bazy danych SQL Server do SQL Server na maszynie wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licencjonowanie

1. **Jak mogę zainstalować moją licencjonowaną kopię programu SQL Server na maszynie wirtualnej platformy Azure?**

   Istnieją dwa sposoby, aby to zrobić. Możesz aprowizować jeden z [obrazów maszyn wirtualnych, który obsługuje licencje](virtual-machines-windows-sql-server-iaas-overview.md#BYOL). Ta metoda nosi nazwę bring-your-own-license (BYOL). Innym rozwiązaniem jest skopiowanie nośników instalacyjnych programu SQL Server na maszynę wirtualną z systemem Windows Server, a następnie zainstalowanie programu SQL Server na maszynie wirtualnej. Jednak w przypadku ręcznego zainstalowania programu SQL Server nie ma integracji z portalem, a rozszerzenie agenta IaaS programu SQL Server nie jest obsługiwane, dlatego w tym scenariuszu nie będą działać takie funkcje jak automatyczne tworzenie kopii zapasowych i automatyczne stosowanie poprawek. Z tego względu zalecamy użycie jednego z obrazów z galerii BYOL. Aby używać BYOL lub własnych nośników SQL Server na maszynie wirtualnej platformy Azure, musisz mieć [Przenośność licencji w ramach programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/). Aby uzyskać więcej informacji, zobacz [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).

1. **Czy muszę płacić za licencję programu SQL Server na maszynie wirtualnej platformy Azure, jeśli używam jej tylko w trybie wstrzymania/trybie failover?**

   Jeśli masz program Software Assurance i używasz przenośność licencji zgodnie z opisem w temacie [Licencjonowanie maszyn wirtualnych — często zadawane pytania](https://azure.microsoft.com/pricing/licensing-faq/), nie musisz uiszczać licencji jednej SQL Server uczestniczy jako pasywna replika pomocnicza w ramach wdrożenia ha. W przeciwnym razie musisz zapłacić za licencję.

1. **Czy mogę zmienić maszynę wirtualną tak, aby używać mojej licencji programu SQL Server, jeśli została ona utworzona z jednego z obrazów z galerii w modelu płatności zgodnie z rzeczywistym użyciem?**

   Tak. Możesz łatwo przenieść się między dwoma modelami licencjonowania, jeśli pierwotnie uruchomiono obraz z galerii płatność zgodnie z rzeczywistym użyciem. Jednak jeśli na początku został użyty obraz BYOL, nie możesz przełączyć licencji na model płatności zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji, zobacz [How to Change the Licensing model for a SQL Server VM](virtual-machines-windows-sql-ahb.md).

   > [!Note]
   > Obecnie ta funkcja jest dostępna tylko dla klientów chmury publicznej.

1. **Czy w celu utworzenia nowej maszyny wirtualnej SQL muszę użyć obrazu BYOL, czy punktu odzyskiwania maszyny wirtualnej SQL?**

   Obrazy bring-your-own-license (BYOL) są dostępne tylko dla klientów z umową EA. Inni Klienci, którzy mają program Software Assurance, powinni używać dostawcy zasobów maszyny wirtualnej SQL do tworzenia maszyny wirtualnej SQL przy użyciu [korzyść użycia hybrydowego platformy Azure (AHB)](https://azure.microsoft.com/pricing/licensing-faq/). 

1. **Czy przełączanie modeli licencjonowania wymaga jakiegokolwiek przestoju w pracy programu SQL Server?**

   Nie. [Zmiana modelu licencjonowania](virtual-machines-windows-sql-ahb.md) nie wymaga żadnych przestojów dla SQL Server, ponieważ zmiana zacznie obowiązywać natychmiast i nie wymaga ponownego uruchomienia maszyny wirtualnej. Jednak, aby zarejestrować maszynę wirtualną SQL Server przy użyciu dostawcy zasobów maszyny wirtualnej SQL, [rozszerzenie SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) jest wymaganiem wstępnym i zainstalowanie rozszerzenia IaaS SQL powoduje ponowne uruchomienie usługi SQL Server. W związku z tym jeśli rozszerzenie SQL IaaS wymaga zainstalowania, należy to zrobić podczas okna konserwacji. 

1. **Czy subskrypcje CSP mogą aktywować Korzyść użycia hybrydowego platformy Azure?**

   Tak, Korzyść użycia hybrydowego platformy Azure jest dostępny dla subskrypcji dostawcy CSP. Klienci programu CSP powinni najpierw wdrożyć obraz z opcją płatność zgodnie z rzeczywistym użyciem, a następnie [zmienić model licencjonowania](virtual-machines-windows-sql-ahb.md) na własny.  

1. **Czy zarejestrowanie mojej maszyny wirtualnej u nowego dostawcy zasobów maszyny wirtualnej SQL spowoduje powstanie dodatkowych kosztów?**

   Nie. Dostawca zasobów maszyny wirtualnej SQL udostępnia po prostu dodatkowe funkcje zarządzania dla programu SQL Server na maszynie wirtualnej platformy Azure bez żadnych dodatkowych opłat. 

1. **Czy dostawca zasobów maszyny wirtualnej SQL jest dostępny dla wszystkich klientów?**
 
   Tak. Wszyscy klienci mogą zarejestrować się u nowego dostawcy zasobów maszyny wirtualnej SQL. Jednak tylko klienci z korzyścią z programu Software Assurance mogą aktywować [korzyść użycia hybrydowego platformy Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (lub BYOL) na SQL Server maszynie wirtualnej. 

1. **Co się stanie z zasobem _Microsoft. SqlVirtualMachine_ , jeśli zasób maszyny wirtualnej zostanie przeniesiony lub porzucony?** 

   Gdy zasób Microsoft. COMPUTE/VirtualMachine zostanie usunięty lub przeniesiony, skojarzony z nim zasób Microsoft. SqlVirtualMachine zostanie powiadomiony o asynchronicznym replikacji operacji.

1. **Co się stanie z maszyną wirtualną, jeśli zasób _Microsoft. SqlVirtualMachine_ został porzucony?**

    Nie ma to wpływu na zasób Microsoft. COMPUTE/VirtualMachine, gdy zostanie usunięty zasób Microsoft. SqlVirtualMachine. Zmiany licencjonowania zostaną jednak domyślnie przywrócone do oryginalnego źródła obrazu. 

1. **Czy istnieje możliwość zarejestrowania samodzielnie wdrożonych maszyn wirtualnych z programem SQL Server u dostawcy zasobów maszyny wirtualnej SQL?**

    Tak. Jeśli wdrożono SQL Server z własnego nośnika i zainstalowano rozszerzenie SQL IaaS, można zarejestrować SQL Serverą maszynę wirtualną przy użyciu dostawcy zasobów, aby uzyskać korzyści z zarządzania udostępniane przez rozszerzenie SQL IaaS. Jednak nie możesz przekonwertować samodzielnie wdrożonej maszyny wirtualnej SQL na model płatności zgodnie z rzeczywistym użyciem.

1. **Czy jest możliwe przełączenie modelu licencjonowania na SQL Server maszyny wirtualnej wdrożonej przy użyciu modelu klasycznego?**

   Nie. Zmiana modelu licencjonowania nie jest obsługiwana na klasycznej maszynie wirtualnej. Możesz migrować maszynę wirtualną do modelu Resource Manager (ARM) i zarejestrować się u dostawcy zasobów maszyny wirtualnej SQL. Po zarejestrowaniu maszyny wirtualnej przy użyciu dostawcy zasobów maszyny wirtualnej SQL zmiany modelu licencjonowania będą dostępne na maszynie wirtualnej. 
   

## <a name="administration"></a>Administracja

1. **Czy mogę zainstalować drugie wystąpienie programu SQL Server na tej samej maszynie wirtualnej? Czy mogę zmienić zainstalowane funkcje wystąpienia domyślnego?**

   Tak. Nośnik instalacyjny SQL Server znajduje się w folderze na dysku **C** . Uruchom **plik Setup. exe** z tej lokalizacji, aby dodać nowe wystąpienia SQL Server lub zmienić inne zainstalowane funkcje SQL Server na komputerze. Należy zauważyć, że niektóre funkcje, takie jak automatyczne tworzenie kopii zapasowych, automatyczne stosowanie poprawek i integracja Azure Key Vault, działają tylko względem wystąpienia domyślnego lub nazwanego wystąpienia, które zostało prawidłowo skonfigurowane (zobacz pytanie 3). 

1. **Czy mogę odinstalować domyślne wystąpienie programu SQL Server?**

   Tak, ale należy wziąć pod uwagę pewne kwestie. Zgodnie z opisem w poprzedniej odpowiedzi istnieją funkcje, które polegają na [rozszerzeniu SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md).  Po odinstalowaniu wystąpienia domyślnego bez usuwania rozszerzenia IaaS, rozszerzenie będzie nadal wyszukiwane i może generować błędy dziennika zdarzeń. Te błędy pochodzą z dwóch następujących źródeł: **Microsoft SQL Server Zarządzanie poświadczeniami** i **Microsoft SQL Server agenta IaaS**. Jeden z błędów może być podobny do następującego:

      Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny.

   W przypadku podjęcia decyzji o odinstalowaniu wystąpienia domyślnego należy również odinstalować także [rozszerzenie Agent SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) .

1. **Czy można użyć nazwanego wystąpienia SQL Server z rozszerzeniem IaaS**?
   
   Tak, jeśli nazwane wystąpienie jest jedynym wystąpieniem w SQL Server i jeśli oryginalne wystąpienie domyślne zostało [odinstalowane prawidłowo](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md#installation). Jeśli nie ma wystąpienia domyślnego i istnieje wiele wystąpień nazwanych na jednej maszynie wirtualnej SQL Server, instalacja rozszerzenia IaaS zakończy się niepowodzeniem. 

1. **Czy mogę usunąć całkowicie program SQL Server z maszyny wirtualnej SQL?**

   Tak, ale nadal będzie naliczana opłata za maszynę wirtualną SQL, zgodnie z opisem w temacie [wskazówki dotyczące cen dla SQL Server maszyn wirtualnych platformy Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Jeśli program SQL Server nie jest już potrzebny, można wdrożyć nową maszynę wirtualną, a następnie migrować dane i aplikacje do nowej maszyny wirtualnej. Następnie można usunąć maszynę wirtualną z programem SQL Server.
   
## <a name="updating-and-patching"></a>Aktualizowanie i stosowanie poprawek

1. **Jak zmienić wersję/edycję programu SQL Server na inną na maszynie wirtualnej platformy Azure?**

   Klienci mogą zmienić wersję/edycję programu SQL Server przy użyciu nośnika instalacyjnego zawierającego odpowiednią wersję/edycję programu SQL Server. Po zmianie edycji za pomocą witryny Azure Portal zmodyfikuj właściwość edycji maszyny wirtualnej w celu dokładnego odzwierciedlenia rozliczeń za tę maszynę wirtualną. Aby uzyskać więcej informacji, zobacz [Zmiana wersji maszyny wirtualnej SQL Server](virtual-machines-windows-sql-change-edition.md). 


1. **Jak są stosowane aktualizacje i dodatki Service Pack na maszynie wirtualnej SQL Server?**

   maszyny wirtualne zapewniają kontrolę nad maszyną hosta, w tym nad czasem i sposobem stosowania aktualizacji. W przypadku systemu operacyjnego można ręcznie zastosować aktualizacje systemu Windows lub włączyć usługę planowania o nazwie [zautomatyzowane stosowanie poprawek](virtual-machines-windows-sql-automated-patching.md). Usługa Automatyczne stosowanie poprawek instaluje wszelkie aktualizacje, które są oznaczone jako ważne, w tym aktualizacje programu SQL Server w tej kategorii. Inne, opcjonalne aktualizacje dla programu SQL Server należy zainstalować ręcznie.

## <a name="general"></a>Ogólne

1. **Czy SQL Server wystąpienia klastra trybu failover (FCI) są obsługiwane na maszynach wirtualnych platformy Azure?**

   Tak. Można [utworzyć klaster trybu failover systemu Windows w systemie Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) i użyć bezpośrednie miejsca do magazynowania (S2D) dla magazynu klastra. Alternatywnie możesz użyć rozwiązań do obsługi klastrów lub magazynu innych firm zgodnie z opisem w temacie [wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > W tej chwili [rozszerzenie Agent SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) nie jest obsługiwane dla SQL Server FCI na platformie Azure. Zalecamy odinstalowanie rozszerzenia z maszyn wirtualnych, które uczestniczą w FCI. To rozszerzenie obsługuje funkcje, takie jak automatyczne tworzenie kopii zapasowych i stosowanie poprawek oraz niektóre funkcje portalu dla języka SQL. Te funkcje nie będą działały w przypadku maszyn wirtualnych SQL po odinstalowaniu agenta.

1. **Jaka jest różnica między maszynami wirtualnymi SQL i usługą SQL Database?**

   Ze względu na to, że Uruchamianie SQL Server na maszynie wirtualnej platformy Azure nie różni się od uruchamiania SQL Server w zdalnym centrum danych. W przeciwieństwie [SQL Database](../../../sql-database/sql-database-technical-overview.md) oferuje bazę danych jako usługę. W przypadku SQL Database nie masz dostępu do maszyn, które obsługują bazy danych. Aby zapoznać się z pełnym porównaniem, zobacz [Wybieranie opcji SQL Server w chmurze: Baza danych Azure SQL (PaaS) lub SQL Server na maszynach wirtualnych platformy](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)Azure (IaaS).

1. **Jak mogę zainstalować narzędzia SQL Data Tools na mojej maszynie wirtualnej platformy Azure?**

    Pobierz i zainstaluj narzędzia SQL Data Tools z [Microsoft SQL Server Data Tools — analiza biznesowa dla Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Czy transakcje rozproszone z usługą MSDTC są obsługiwane na maszynach wirtualnych SQL Server?**
   
    Tak. Lokalna usługa DTC jest obsługiwana w przypadku SQL Server 2016 z dodatkiem SP2 i nowszych. Jednak aplikacje muszą być testowane w przypadku używania zawsze włączonych grup dostępności, ponieważ transakcje w trakcie pracy w trybie failover będą kończyć się niepowodzeniem i muszą zostać ponowione. Klastrowane usługi DTC są dostępne począwszy od systemu Windows Server 2019. 

## <a name="resources"></a>Zasoby

**Maszyny wirtualne z systemem Windows**:

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Inicjowanie obsługi administracyjnej maszyny wirtualnej z systemem SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrowanie bazy danych do SQL Server na maszynie wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md)
* [Wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Wzorce aplikacji i strategie programowania dla SQL Server na platformie Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Maszyny wirtualne z systemem Linux**:

* [Omówienie SQL Server na maszynie wirtualnej z systemem Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Inicjowanie obsługi administracyjnej maszyny wirtualnej z systemem SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Często zadawane pytania (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentacja programu SQL Server w systemie Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
