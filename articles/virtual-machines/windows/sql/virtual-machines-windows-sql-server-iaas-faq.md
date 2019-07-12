---
title: SQL Server na maszynach wirtualnych Windows Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure.
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
ms.date: 07/12/2018
ms.author: v-shysun
ms.openlocfilehash: 95ad2ba4798d41f2e5e49ca33735b997859af23f
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658140"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Często zadawane pytania dotyczące programu SQL Server uruchomionego na maszynach wirtualnych Windows Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania na temat uruchamiania [programu SQL Server na maszynach wirtualnych Windows na platformie Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Ten artykuł koncentruje się na kwestie związane z programu SQL Server na maszynach wirtualnych Windows. Jeśli używasz programu SQL Server na maszynach wirtualnych z systemem Linux, zobacz [Linux — często zadawane pytania](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Obrazy

1. **Jakie obrazów w galerii maszyn wirtualnych programu SQL Server są dostępne?** 

   Azure obsługuje obrazy maszyn wirtualnych dla wszystkich obsługiwanych wersji głównych programu SQL Server we wszystkich wersjach dla systemów Windows i Linux. Aby uzyskać więcej informacji, zobacz pełną listę [obrazów maszyn wirtualnych Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) i [obrazów maszyn wirtualnych z systemem Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Obrazy galerii w istniejącej maszyny wirtualnej programu SQL Server są aktualizowane?**

   Co dwa miesiące obrazów programu SQL Server w galerii maszyn wirtualnych są aktualizowane przy użyciu najnowszych Windows i Linux aktualizacji. W przypadku obrazów Windows w tym wszelkie aktualizacje, które są oznaczone jako ważne w aktualizacji Windows, w tym ważne aktualizacje zabezpieczeń programu SQL Server i dodatków service pack. Dla obrazów systemu Linux w tym najnowsze aktualizacje systemu. Aktualizacje zbiorcze programu SQL Server są obsługiwane inaczej dla systemów Linux i Windows. Dla systemu Linux aktualizacje zbiorcze programu SQL Server znajdują się również w odświeżania. Ale w tej chwili Windows maszyny wirtualne nie zostaną zaktualizowane o aktualizacje zbiorcze programu SQL Server lub Windows Server.

1. **Można usuwane z galerii obrazów maszyn wirtualnych programu SQL Server?**

   Tak. Platforma Azure utrzymuje tylko jeden obraz na głównej wersji i wydania. Na przykład po udostępnieniu nowego dodatku service pack programu SQL Server, platforma Azure dodaje nowy obraz w galerii dla tego dodatku service pack. Obraz programu SQL Server dla poprzedniego dodatku service pack jest usuwane natychmiast w witrynie Azure portal. Jednak jest wciąż dostępna na potrzeby aprowizacji za pomocą programu PowerShell dla następnych trzech miesięcy. Po upływie trzech miesięcy na poprzedniej ilustracji pakiet usługi nie jest już dostępna. Te zasady usuwania czy mają zastosowanie również w wersji programu SQL Server staje się nieobsługiwany po osiągnięciu końca swojego cyklu życia.


1. **Czy jest możliwe do wdrożenia obrazu starsze programu SQL Server, który nie jest widoczny w witrynie Azure portal?**

   Tak, przy użyciu programu PowerShell. Aby uzyskać więcej informacji o wdrażaniu maszyny wirtualne SQL Server przy użyciu programu PowerShell, zobacz [jak inicjować obsługę maszyn wirtualnych programu SQL Server przy użyciu programu Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Czy mogę utworzyć obraz wirtualnego dysku twardego z maszyny Wirtualnej programu SQL Server?**

   Tak, ale jest kilka istotnych kwestii. Jeśli wdrożono ten wirtualny dysk twardy do nowej maszyny Wirtualnej na platformie Azure, nie są w sekcji Konfiguracja programu SQL Server w portalu. Następnie można zarządzać opcje konfiguracji programu SQL Server za pomocą programu PowerShell. Ponadto możesz opłata zgodnie ze stawką maszyn wirtualnych SQL, obraz został pierwotnie oparty na. Ta zasada obowiązuje, nawet w przypadku usunięcia programu SQL Server z dysku VHD, przed wdrożeniem. 

1. **Czy jest możliwe do skonfigurowania konfiguracje nie są wyświetlane w galerii maszyn wirtualnych (na przykład Windows 2008 R2 i SQL Server 2012)?**

   Nie. Dla obrazów galerii maszyn wirtualnych, które obejmują program SQL Server, należy wybrać jedną z podanych obrazy w witrynie Azure portal lub za pośrednictwem [PowerShell](virtual-machines-windows-ps-sql-create.md). 


## <a name="creation"></a>Tworzenie

1. **Jak utworzyć maszynę wirtualną platformy Azure z programem SQL Server?**

   Najprostszym rozwiązaniem jest do utworzenia maszyny wirtualnej, który zawiera program SQL Server. Samouczek dotyczący rejestracji na platformie Azure i tworzenie maszyny Wirtualnej SQL z portalu, zobacz [Aprowizowanie maszyny wirtualnej programu SQL Server w witrynie Azure portal](virtual-machines-windows-portal-sql-server-provision.md). Można wybrać obraz maszyny wirtualnej, który używa licencjonowania programu SQL Server płatności na sekundę lub użyć obrazu, który pozwala na model dostarczania własnej licencji programu SQL Server. Istnieje również opcja ręcznego instalowania programu SQL Server na maszynie Wirtualnej z oboma swobodnie licencjonowaną (Developer i Express) lub dzięki ponownemu wykorzystaniu licencji w środowisku lokalnym. W przypadku przeniesienia własnej licencji, konieczne jest posiadanie [przenośności licencji za pośrednictwem programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/). Aby uzyskać więcej informacji, zobacz [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).

1. **Jak migrować mojej lokalnej bazy danych programu SQL Server do chmury**

   Najpierw należy utworzyć maszynę wirtualną platformy Azure przy użyciu wystąpienia programu SQL Server. Następnie należy przeprowadzić migrację lokalnych baz danych do tego wystąpienia. Aby Strategie migracji danych, zobacz [migracji bazy danych programu SQL Server do programu SQL Server na Maszynie wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licencjonowanie

1. **Jak mogę zainstalować moją licencjonowaną kopię programu SQL Server na maszynie wirtualnej platformy Azure?**

   Istnieją dwa sposoby, aby to zrobić. Możesz aprowizować jeden z [obrazów maszyn wirtualnych, który obsługuje licencje](virtual-machines-windows-sql-server-iaas-overview.md#BYOL). Ta metoda nosi nazwę bring-your-own-license (BYOL). Innym rozwiązaniem jest skopiowanie nośników instalacyjnych programu SQL Server na maszynę wirtualną z systemem Windows Server, a następnie zainstalowanie programu SQL Server na maszynie wirtualnej. Jednak w przypadku ręcznego zainstalowania programu SQL Server nie ma integracji z portalem, a rozszerzenie agenta IaaS programu SQL Server nie jest obsługiwane, dlatego w tym scenariuszu nie będą działać takie funkcje jak automatyczne tworzenie kopii zapasowych i automatyczne stosowanie poprawek. Z tego względu zalecamy użycie jednego z obrazów z galerii BYOL. Aby użyć BYOL i multimediów programu SQL Server na Maszynie wirtualnej platformy Azure, konieczne jest posiadanie [przenośności licencji za pośrednictwem programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/). Aby uzyskać więcej informacji, zobacz [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).

1. **Czy muszę płacić za licencję programu SQL Server na maszynie wirtualnej platformy Azure, jeśli używam jej tylko w trybie wstrzymania/trybie failover?**

   Jeśli masz pakiet Software Assurance i używają opcji przenoszenia licencji zgodnie z opisem w [często zadawane pytania dotyczące licencjonowania maszyny wirtualnej](https://azure.microsoft.com/pricing/licensing-faq/), nie trzeba płacić za licencję na jeden udział jako replika pomocnicza pasywnym we wdrożeniu o wysokiej dostępności programu SQL Server. W przeciwnym razie musisz zapłacić za licencję.

1. **Czy mogę zmienić maszynę wirtualną tak, aby używać mojej licencji programu SQL Server, jeśli została ona utworzona z jednego z obrazów z galerii w modelu płatności zgodnie z rzeczywistym użyciem?**

   Tak. Można łatwo przenosić przenoszenia między dwoma modelami licencji, jeśli pierwotnie uruchomiona z obrazem galerii zgodnie z rzeczywistym użyciem. Jednak jeśli na początku został użyty obraz BYOL, nie możesz przełączyć licencji na model płatności zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji, zobacz [jak zmienić modelu licencjonowania dla maszyny Wirtualnej programu SQL Server](virtual-machines-windows-sql-ahb.md).

   > [!Note]
   > Obecnie tej funkcji jest dostępna tylko dla klientów w chmurze publicznej.

1. **Czy w celu utworzenia nowej maszyny wirtualnej SQL muszę użyć obrazu BYOL, czy punktu odzyskiwania maszyny wirtualnej SQL?**

   Obrazy bring-your-own-license (BYOL) są dostępne tylko dla klientów z umową EA. Inni klienci posiadający pakiet Software Assurance Użyj dostawcy zasobów maszyny Wirtualnej SQL do tworzenia maszyny Wirtualnej SQL przy użyciu [korzyść użycia hybrydowego platformy Azure (AHB)](https://azure.microsoft.com/pricing/licensing-faq/). 

1. **Czy przełączanie modeli licencjonowania wymaga jakiegokolwiek przestoju w pracy programu SQL Server?**

   Nie. [Zmiana modelu licencjonowania](virtual-machines-windows-sql-ahb.md) nie wymaga żadnych przestojów dla programu SQL Server, ponieważ zmiana zacznie obowiązywać natychmiast i nie wymaga ponownego uruchomienia maszyny wirtualnej. Jednak, aby zarejestrować maszyny Wirtualnej programu SQL Server za pomocą dostawcy zasobów maszyny Wirtualnej SQL [rozszerzenie SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) jest wymaganiem wstępnym i instalowanie rozszerzenie SQL IaaS powoduje ponowne uruchomienie usługi programu SQL Server. W związku z tym jeśli rozszerzenie SQL IaaS wymaga zainstalowania, należy to zrobić podczas okna konserwacji. 

1. **Subskrypcji CSP aktywować korzyść użycia hybrydowego platformy Azure?**

   Tak, korzyść użycia hybrydowego platformy Azure jest dostępna dla subskrypcji programu CSP. Dostawcy usług Kryptograficznych powinni najpierw wdrożyć obraz zgodnie z rzeczywistym użyciem, a następnie [zmienić modelu licencjonowania](virtual-machines-windows-sql-ahb.md) do bring-your-own-license.  

1. **Czy zarejestrowanie mojej maszyny wirtualnej u nowego dostawcy zasobów maszyny wirtualnej SQL spowoduje powstanie dodatkowych kosztów?**

   Nie. Dostawca zasobów maszyny wirtualnej SQL udostępnia po prostu dodatkowe funkcje zarządzania dla programu SQL Server na maszynie wirtualnej platformy Azure bez żadnych dodatkowych opłat. 

1. **Czy dostawca zasobów maszyny wirtualnej SQL jest dostępny dla wszystkich klientów?**
 
   Tak. Wszyscy klienci mogą zarejestrować się u nowego dostawcy zasobów maszyny wirtualnej SQL. Jednak tylko klienci z korzyści programu Software Assurance mogą aktywować [korzyść użycia hybrydowego platformy Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (lub BYOL) na maszynę Wirtualną programu SQL Server. 

1. **Co się dzieje z _Microsoft.SqlVirtualMachine_ zasobu, jeśli zasób maszyny Wirtualnej jest przeniesiony lub usunięty?** 

   Gdy zasobów Microsoft.Compute/VirtualMachine jest porzucone lub przeniesiona, a następnie skojarzonego zasobu Microsoft.SqlVirtualMachine powiadomić o asynchronicznym replikowaniu operacji.

1. **Co się dzieje z maszyny Wirtualnej, jeśli _Microsoft.SqlVirtualMachine_ zasobów jest porzucany?**

    Zasób Microsoft.Compute/VirtualMachine nie ulega zmianie po upuszczeniu zasobów Microsoft.SqlVirtualMachine. Jednak zmiany licencjonowania domyślnie powrócą do oryginalnego źródła obrazu. 

1. **Czy istnieje możliwość zarejestrowania samodzielnie wdrożonych maszyn wirtualnych z programem SQL Server u dostawcy zasobów maszyny wirtualnej SQL?**

    Tak. Jeśli wdrożono programu SQL Server z własnych nośnika oraz zainstalowane rozszerzenie SQL IaaS można zarejestrować maszyny Wirtualnej programu SQL Server za pomocą dostawcy zasobów, aby uzyskać korzyści możliwości udostępniane przez rozszerzenie SQL IaaS. Jednak nie możesz przekonwertować samodzielnie wdrożonej maszyny wirtualnej SQL na model płatności zgodnie z rzeczywistym użyciem.

## <a name="administration"></a>Administracja

1. **Czy mogę zainstalować drugie wystąpienie programu SQL Server na tej samej maszynie wirtualnej? Czy mogę zmienić zainstalowane funkcje wystąpienia domyślnego?**

   Tak. Nośnik instalacyjny programu SQL Server znajduje się w folderze na **C** dysku. Uruchom **Setup.exe** z lokalizacji, aby dodać nowe wystąpienia programu SQL Server lub do zmiany innych zainstalowane funkcje programu SQL Server na maszynie. Należy pamiętać, że niektóre funkcje, takie jak automatyczne kopie zapasowe, automatyczne stosowanie poprawek i integracji magazynu kluczy Azure, działać tylko względem wystąpienia domyślnego lub nazwane wystąpienie, które zostało poprawnie skonfigurowane (zobacz pytanie 3). 

1. **Czy mogę odinstalować domyślne wystąpienie programu SQL Server?**

   Tak, ale należy wziąć pod uwagę pewne kwestie. Jak wspomniano w poprzedniej odpowiedzi na pytanie, są funkcje, które zależą od [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md).  Po odinstalowaniu domyślnego wystąpienia bez usuwania rozszerzenia IaaS również rozszerzenie jej szukać w dalszym ciągu i może generować błędy dziennika zdarzeń. Te błędy pochodzą z dwóch następujących źródeł: **Zarządzanie poświadczeniami serwera Microsoft SQL** i **agenta IaaS programu Microsoft SQL Server**. Jeden z błędów może być podobny do następującego:

      Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny.

   Jeśli zdecydujesz się odinstalować domyślnego wystąpienia, również odinstalować [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md) także.

1. **Do czego służy nazwane wystąpienie programu SQL Server z rozszerzeniem IaaS**?
   
   Tak, to nazwane wystąpienie jest tylko wystąpienie programu SQL Server, a został oryginalne wystąpienie domyślne [poprawnie odinstalować](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md#installation). Jeśli żadne wystąpienie domyślne, a w pojedynczej maszyny Wirtualnej programu SQL Server znajdują się wiele wystąpień nazwanych, rozszerzenie IaaS zakończy się niepowodzeniem do zainstalowania. 

1. **Czy mogę usunąć całkowicie program SQL Server z maszyny wirtualnej SQL?**

   Tak, ale będą nadal naliczane za maszynę Wirtualną SQL, zgodnie z opisem w [ceny wskazówki dotyczące maszyn wirtualnych programu SQL Server Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Jeśli program SQL Server nie jest już potrzebny, można wdrożyć nową maszynę wirtualną, a następnie migrować dane i aplikacje do nowej maszyny wirtualnej. Następnie można usunąć maszynę wirtualną z programem SQL Server.
   
## <a name="updating-and-patching"></a>Stosowanie poprawek i aktualizacji

1. **Jak zmienić do różnych wersji/wydania programu SQL Server w Maszynie wirtualnej platformy Azure?**

   Klienci mogą zmieniać ich wersją programu SQL Server przy użyciu nośnika instalacyjnego, który zawiera ich żądanej wersji lub wersji programu SQL Server. Po zmianie wersji użyj witryny Azure portal, aby zmodyfikować właściwości wersji maszyny wirtualnej, aby dokładnie odzwierciedlać rozliczeń dla maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Zmień wersję maszyny Wirtualnej programu SQL Server](virtual-machines-windows-sql-change-edition.md). 


1. **Sposób aktualizacji i dodatków service pack stosowania na maszynę Wirtualną programu SQL Server?**

   maszyny wirtualne zapewniają kontrolę nad maszyną hosta, w tym nad czasem i sposobem stosowania aktualizacji. Dla systemu operacyjnego możesz ręcznie zastosować aktualizacje systemu windows, można również włączyć usługę harmonogramu o nazwie [automatyczne stosowanie poprawek](virtual-machines-windows-sql-automated-patching.md). Usługa Automatyczne stosowanie poprawek instaluje wszelkie aktualizacje, które są oznaczone jako ważne, w tym aktualizacje programu SQL Server w tej kategorii. Inne, opcjonalne aktualizacje dla programu SQL Server należy zainstalować ręcznie.

## <a name="general"></a>Ogólne

1. **Czy wystąpienia klastra trybu Failover (FCI) programu SQL Server są obsługiwane na maszynach wirtualnych platformy Azure?**

   Tak. Możesz [utworzyć klaster pracy awaryjnej Windows w systemie Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) i korzystania z bezpośrednimi miejscami do magazynowania (S2D) jako magazynu klastra. Alternatywnie, można użyć klastrowania lub magazynu rozwiązania innych firm zgodnie z opisem w [wysokiej dostępności i odzyskiwania po awarii dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > W tej chwili [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md) nie jest obsługiwana dla infrastruktury klasyfikacji plików programu SQL Server na platformie Azure. Firma Microsoft zaleca, odinstaluj rozszerzenie z maszyny wirtualne, które uczestniczą w trybu. To rozszerzenie obsługuje funkcje, takie jak automatyczne kopie zapasowe i stosowanie poprawek i niektórych funkcji portalu do języka SQL. Te funkcje nie będą działać dla maszyn wirtualnych SQL, po odinstalowaniu agenta.

1. **Jaka jest różnica między maszynami wirtualnymi SQL i usługi SQL Database?**

   Koncepcyjnie programem SQL Server na maszynie wirtualnej platformy Azure jest to, że nie różni się od uruchamiania programu SQL Server w zdalnym centrum danych. Z kolei [bazy danych SQL](../../../sql-database/sql-database-technical-overview.md) oferuje bazy danych jako usługę. Usługa SQL Database nie masz dostępu do maszyn, na których są hostowane bazy danych. Dla pełnego porównania, zobacz [Wybieranie opcji programu SQL Server w chmurze: Usługa Azure SQL Database (PaaS) lub SQL Server na maszynach wirtualnych platformy Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Jak zainstalować narzędzia danych serwera SQL na maszynie Wirtualnej platformy Azure?**

    Pobieranie i instalowanie narzędzi danych programu SQL z [programu Microsoft SQL Server Data Tools — Business Intelligence programu Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Czy transakcji rozproszonych za pomocą usługi MSDTC jest obsługiwana na maszynach wirtualnych programu SQL Server?**
   
    Tak. Lokalna Usługa DTC jest obsługiwana dla programu SQL Server 2016 z dodatkiem SP2 lub nowszym. Jednak aplikacje muszą zostać przetestowane, gdy przy użyciu zawsze włączonych grup dostępności jako transakcje, śledząc podczas przejścia w tryb failover zakończy się niepowodzeniem i musi zostać ponowiona. Klastra usługi DTC jest dostępna, począwszy od systemu Windows Server w 2019 r. 

## <a name="resources"></a>Zasoby

**Maszyny wirtualne z systemem Windows**:

* [Omówienie programu SQL Server na maszynie Wirtualnej Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Aprowizowanie Windows serwer SQL maszyny Wirtualnej](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md)
* [Wysoka dostępność i odzyskiwanie po awarii programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Wzorce aplikacji i strategie programowania dla programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Maszyny wirtualne z systemem Linux**:

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Aprowizowanie maszyny Wirtualnej systemu Linux z programu SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Często zadawane pytania (system Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentacja programu SQL Server w systemie Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
