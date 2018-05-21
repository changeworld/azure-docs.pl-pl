---
title: Program SQL Server na maszynach wirtualnych platformy Azure z systemem Windows — często zadawane pytania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące programem SQL Server na maszynach wirtualnych Azure.
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
ms.date: 03/20/2018
ms.author: v-shysun
ms.openlocfilehash: e0254cd16c27597c3d52aed19b4c4ece49bac765
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-azure-virtual-machines"></a>Często zadawane pytania dotyczące programu SQL Server uruchomionego na maszynach wirtualnych Windows Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące uruchamiania [programu SQL Server na maszynach wirtualnych Windows Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Ten artykuł skupia się na kwestie związane z programem SQL Server na maszynach wirtualnych systemu Windows. Jeśli używasz programu SQL Server na maszynach wirtualnych systemu Linux, zobacz [Linux — często zadawane pytania](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Obrazy

1. **Jakie galerii obrazów maszyny wirtualnej programu SQL Server są dostępne?**

   Azure obsługuje obrazy maszyny wirtualnej dla wszystkich obsługiwanych wersjach głównych programu SQL Server we wszystkich wersjach systemu dla systemów Windows i Linux. Aby uzyskać więcej informacji, zobacz pełną listę [obrazów maszyn wirtualnych systemu Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) i [obrazów maszyny Wirtualnej systemu Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Istniejących obrazów Galeria maszyny wirtualnej programu SQL Server są aktualizowane?**

   Co dwa miesiące obrazów programu SQL Server w galerii maszyn wirtualnych są aktualizowane przy użyciu najnowszych systemu Windows i Linux aktualizacji. W przypadku obrazów systemu Windows w tym wszelkie aktualizacje, które są oznaczone jako ważne w usłudze Windows Update, włączając ważne aktualizacje zabezpieczeń programu SQL Server i dodatków service pack. W przypadku obrazów systemu Linux w tym najnowsze aktualizacje systemu. Aktualizacje zbiorcze programu SQL Server są obsługiwane inaczej dla systemów Linux i Windows. Dla systemu Linux aktualizacji zbiorczych programu SQL Server znajdują się również w odświeżania. Ale w tej chwili maszyn wirtualnych systemu Windows nie są aktualizowane z programu SQL Server lub Windows Server aktualizacjami zbiorczymi.

1. **Obrazy maszyny wirtualnej programu SQL Server uzyskać wyjąć z galerii?**

   Tak. Azure obsługuje tylko jeden obraz na głównych wersji i wydania. Na przykład po wydaniu nowego dodatku service pack programu SQL Server Azure dodaje nowy obraz w galerii tego dodatku service pack. Obraz programu SQL Server dla poprzedniego dodatku service pack jest od razu usunięte z portalu Azure. Jednak jest wciąż dostępna na potrzeby inicjowania obsługi administracyjnej z programu PowerShell dla trzy kolejne miesiące. Po upływie trzech miesięcy poprzednie obrazu dodatku service pack nie jest już dostępny. Te zasady usuwania również będą miały zastosowania, jeśli po osiągnie koniec cyklu jego życia, jest nieobsługiwana wersja programu SQL Server.

1. **Obraz wirtualnego dysku twardego można utworzyć maszyny wirtualnej programu SQL Server?**

   Tak, ale są kilka zagadnienia. Wdrożenie tego wirtualnego dysku twardego do nowej maszyny Wirtualnej na platformie Azure, możesz zrobić nie ge sekcji konfiguracji serwera SQL w portalu. Następnie należy zarządzać opcje konfiguracji programu SQL Server za pomocą programu PowerShell. Ponadto możesz zostanie naliczona opłata za szybkością obraz został pierwotnie na podstawie maszyny wirtualnej SQL. Dotyczy to nawet po usunięciu programu SQL Server z dysku VHD przed wdrożeniem. 

1. **Czy jest możliwe do skonfigurowania konfiguracje nie są wyświetlane w galerii maszyn wirtualnych (na przykład Windows 2008 R2 i SQL Server 2012)**

   Nie. Dla maszyny wirtualnej galerii obrazów, które obejmują program SQL Server należy wybrać jeden z dostarczonego obrazów.

## <a name="creation"></a>Tworzenie

1. **Jak utworzyć maszynę wirtualną platformy Azure z programem SQL Server?**

   Najlepszym rozwiązaniem jest, aby utworzyć maszynę wirtualną, która obejmuje program SQL Server. Samouczek dotyczący rejestracji na platformie Azure i tworzenia maszyny Wirtualnej SQL z portalu, zobacz [Aprowizowanie maszyny wirtualnej programu SQL Server w portalu Azure](virtual-machines-windows-portal-sql-server-provision.md). Można wybrać obraz maszyny wirtualnej, który używa licencjonowania programu SQL Server o płatności na sekundę, lub można użyć obrazu, który pozwala na użycie własnej licencji programu SQL Server. Istnieje również opcja ręcznego instalowania programu SQL Server na maszynie Wirtualnej w trybie za darmo licencjonowanej (deweloperem lub Express) lub przez ponowne użycie licencji usługi lokalnej. Jeśli użycie własnej licencji, musisz mieć [przenośność licencji za pośrednictwem programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/). Aby uzyskać więcej informacji, zobacz [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).

1. **Jak migrować lokalnej bazy danych programu SQL Server do chmury**

   Najpierw należy utworzyć maszyny wirtualnej platformy Azure z wystąpieniem programu SQL Server. Następnie należy przeprowadzić migrację lokalnych baz danych do tego wystąpienia. Aby strategii migracji danych, zobacz [migrację bazy danych programu SQL Server do programu SQL Server w maszynie Wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licencjonowanie

1. **Jak zainstalować mój licencjonowanej kopii programu SQL Server na maszynie Wirtualnej platformy Azure?**

   Istnieją dwa sposoby, w tym celu. Można obsługiwać jeden z [obrazy maszyny wirtualnej, które obsługuje licencji](virtual-machines-windows-sql-server-iaas-overview.md#BYOL). Innym rozwiązaniem jest skopiować nośnik instalacyjny programu SQL Server do maszyny Wirtualnej systemu Windows Server, a następnie zainstalować program SQL Server na maszynie Wirtualnej. Licencjonowania powodów, musisz mieć [przenośność licencji za pośrednictwem programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/). Aby uzyskać więcej informacji, zobacz [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).

1. **Czy można zmienić maszyny Wirtualnej, aby użyć własnej licencji programu SQL Server, jeśli została utworzona z jednego z obrazów z galerii?**

   Nie. Nie można zmienić na przy użyciu własnej licencji Licencjonowanie płatności na sekundę. Tworzenie nowej maszyny wirtualnej platformy Azure przy użyciu jednej z [obrazy BYOL](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), a następnie przeprowadzić migrację bazy danych do nowego serwera przy użyciu standardu [techniki migracji danych](virtual-machines-windows-migrate-sql.md).

1. **Czy muszę płacić za licencji programu SQL Server na maszynie Wirtualnej platformy Azure, jeśli jest on używany tylko dla stanu wstrzymania i pracy awaryjnej?**

   Jeśli masz Software Assurance i używać przenośność licencji, zgodnie z opisem w [często zadawane pytania dotyczące licencjonowania maszyny wirtualnej](http://azure.microsoft.com/pricing/licensing-faq/) nie trzeba płacić za licencji SQL Server uczestniczących jako replika pomocnicza pasywnym we wdrożeniu wysokiej dostępności. W przeciwnym razie trzeba płacić za jej licencji.


## <a name="administration"></a>Administracja

1. **Drugie wystąpienie programu SQL Server można zainstalować na tej samej maszyny Wirtualnej? Czy można zmieniać zainstalowane funkcje domyślnego wystąpienia?**

   Tak. Nośnik instalacyjny programu SQL Server znajduje się w folderze na **C** dysku. Uruchom **Setup.exe** z tej lokalizacji, aby dodać nowe wystąpienia programu SQL Server lub zmień inne zainstalowane funkcje programu SQL Server na komputerze. Należy pamiętać, że niektóre funkcje, takie jak automatyczna usługa Backup, automatyczne stosowanie poprawek i integracji magazynu kluczy Azure wykonywać operacje tylko na domyślnym wystąpieniu.

1. **Czy można odinstalować domyślnego wystąpienia programu SQL Server**

   Tak, ale są pewne zagadnienia. Jak już wspomniano w poprzedniej odpowiedzi, funkcje, które polegać na [rozszerzenia agenta programu SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) wykonywać operacje tylko na domyślnym wystąpieniu. Po odinstalowaniu wystąpienie domyślne rozszerzenie wyszukaj go w dalszym ciągu i może generować błędy dziennika zdarzeń. Te błędy są z następujących dwóch źródeł: **zarządzania poświadczeń programu Microsoft SQL Server** i **programu Microsoft SQL Server IaaS Agent**. Błędy mogą być podobne do następujących:

      Wystąpił błąd związany z siecią lub wystąpieniem podczas ustanawiania połączenia z programem SQL Server. Serwer nie został znaleziony lub był niedostępny.

   Jeśli zdecydujesz się wystąpienia domyślnego, również odinstalować [rozszerzenia agenta programu SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) również.

1. **Można usunąć serwera SQL całkowicie z maszyny Wirtualnej SQL?**

   Tak, ale będą nadal naliczane za maszyną Wirtualną programu SQL, zgodnie z opisem w [cennik wskazówki dotyczące maszyn wirtualnych programu SQL Server Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Jeśli program SQL Server nie są już potrzebne, można wdrożyć nową maszynę wirtualną i przeprowadzić migrację danych i aplikacji do nowej maszyny wirtualnej. Następnie można usunąć maszyny wirtualnej programu SQL Server.
   
## <a name="updating-and-patching"></a>Aktualizowanie i stosowanie poprawek

1. **Jak uaktualnić do nowej wersji/wydania programu SQL Server w maszynie Wirtualnej platformy Azure?**

   Obecnie nie są bez uaktualnienia w miejscu programu SQL Server w maszynie Wirtualnej platformy Azure. Tworzenie nowej maszyny wirtualnej platformy Azure z wersji/wydania żądanego programu SQL Server, a następnie przeprowadzić migrację bazy danych do nowego serwera przy użyciu standardu [techniki migracji danych](virtual-machines-windows-migrate-sql.md).

1. **Jak są aktualizacje i dodatków service pack stosowane na maszynie Wirtualnej serwera SQL?**

   Maszyn wirtualnych zapewnia kontrolę nad komputer hosta, w tym, kiedy i jak należy zastosować aktualizacje. Dla systemu operacyjnego, możesz ręcznie zastosować aktualizacje systemu windows lub można włączyć usługi harmonogramu o nazwie [automatyczne stosowanie poprawek](virtual-machines-windows-sql-automated-patching.md). Usługa Automatyczne stosowanie poprawek instaluje wszelkie aktualizacje, które są oznaczone jako ważne, w tym aktualizacje programu SQL Server w tej kategorii. Inne, opcjonalne aktualizacje dla programu SQL Server należy zainstalować ręcznie.

## <a name="general"></a>Ogólne

1. **Wystąpienia klastra trybu Failover (FCI) programu SQL Server są obsługiwane na maszynach wirtualnych Azure?**

   Tak. Możesz [utworzyć klaster trybu Failover systemu Windows w systemie Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) i używać bezpośrednie miejsca do magazynowania (S2D) do magazynu klastra. Można również użyć rozwiązań innych firm klastra lub magazyn zgodnie z opisem w [wysokiej dostępności i odzyskiwania po awarii dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

1. **Jaka jest różnica między SQL maszyny wirtualne i usługi SQL Database?**

   Koncepcyjnie programem SQL Server na maszynie wirtualnej platformy Azure nie, która różni się z programem SQL Server w zdalnym centrum danych. Z kolei [bazy danych SQL](../../../sql-database/sql-database-technical-overview.md) oferuje bazy danych jako usługa. Z bazy danych SQL nie masz dostępu do komputerów, które obsługi baz danych. Porównanie pełnej, zobacz [wybierz chmurę programu SQL Server option: Azure SQL Database (PaaS) lub SQL Server na maszynach wirtualnych Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Jak zainstalować narzędzia danych serwera SQL na maszynie Wirtualnej platformy Azure?**

    Pobierz i zainstaluj narzędzia danych SQL z [Microsoft SQL Server Data Tools - Business Intelligence dla programu Visual Studio 2013](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Zasoby

**Maszyny wirtualne systemu Windows**:

* [Omówienie programu SQL Server w systemie Windows wirtualna](virtual-machines-windows-sql-server-iaas-overview.md).
* [Udostępnianie maszyn wirtualnych systemu Windows serwera SQL](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md)
* [Wysoka dostępność i odzyskiwanie po awarii dla programu SQL Server na maszynach wirtualnych Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Wzorce aplikacji i strategii rozwoju dla programu SQL Server na maszynach wirtualnych Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Maszyny wirtualne systemu Linux**:

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Udostępnić Maszynę wirtualną systemu Linux serwera SQL](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Często zadawane pytania (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentacja programu SQL Server w systemie Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
