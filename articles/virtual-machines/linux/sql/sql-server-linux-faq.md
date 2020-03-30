---
title: SQL Server na maszynach wirtualnych platformy Linux — często zadawane pytania | Dokumenty firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure z systemem Linux.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1e729c608a2cad28c810f8d5236360c909a496b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70082028"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Często zadawane pytania dotyczące programu SQL Server na maszynach wirtualnych platformy Linux Azure

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Ten artykuł zawiera odpowiedzi na niektóre z najczęstszych pytań dotyczących uruchamiania [programu SQL Server na maszynach wirtualnych platformy Linux Azure.](sql-server-linux-virtual-machines-overview.md)

> [!NOTE]
> W tym artykule skupiono się na problemach specyficznych dla programu SQL Server na maszynach wirtualnych z systemem Linux. Jeśli używasz programu SQL Server na maszynach wirtualnych systemu Windows, zobacz często zadawane pytania dotyczące [systemu Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Obrazów

1. **Jakie obrazy galerii maszyn wirtualnych programu SQL Server są dostępne?**

   Platforma Azure przechowuje obrazy maszyn wirtualnych dla wszystkich obsługiwanych głównych wersji programu SQL Server we wszystkich wersjach systemu Linux i Windows. Aby uzyskać więcej informacji, zobacz pełną listę [obrazów maszyn wirtualnych](sql-server-linux-virtual-machines-overview.md#create) z systemem Linux i [obrazów maszyn wirtualnych systemu Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Czy istniejące obrazy galerii maszyn wirtualnych programu SQL Server są aktualizowane?**

   Co dwa miesiące obrazy programu SQL Server w galerii maszyn wirtualnych są aktualizowane o najnowsze aktualizacje systemu Linux i Windows. W przypadku obrazów systemu Linux obejmuje to najnowsze aktualizacje systemu. W przypadku obrazów systemu Windows obejmuje to wszystkie aktualizacje, które są oznaczone jako ważne w usłudze Windows Update, w tym ważne aktualizacje zabezpieczeń programu SQL Server i dodatki Service Pack. Aktualizacje zbiorcze programu SQL Server są obsługiwane inaczej dla systemów Linux i Windows. W systemie Linux aktualizacje zbiorcze programu SQL Server są również uwzględniane w odświeżaniu. Ale w tej chwili maszyny wirtualne systemu Windows nie są aktualizowane za pomocą aktualizacji zbiorczych programu SQL Server lub Windows Server.

1. **Jakie powiązane pakiety programu SQL Server są również zainstalowane?**

   Aby wyświetlić pakiety programu SQL Server, które są instalowane domyślnie na maszynach wirtualnych z systemem SQL Server z systemem Linux, zobacz [Zainstalowane pakiety](sql-server-linux-virtual-machines-overview.md#packages).

1. **Czy obrazy maszyn wirtualnych programu SQL Server mogą zostać usunięte z galerii?**

   Tak. Platforma Azure przechowuje tylko jeden obraz na wersję główną i wersję. Na przykład po wydaniu nowego dodatku Service Pack programu SQL Server platforma Azure dodaje nowy obraz do galerii dla tego dodatku Service Pack. Obraz programu SQL Server dla poprzedniego dodatku Service Pack jest natychmiast usuwany z witryny Azure portal. Jednak nadal jest dostępna do inicjowania obsługi administracyjnej z programu PowerShell przez następne trzy miesiące. Po trzech miesiącach poprzedni obraz dodatku Service Pack nie jest już dostępny. Ta zasada usuwania będzie również stosowana, jeśli wersja programu SQL Server staje się nieobsługiwała po zakończeniu cyklu życia.

## <a name="creation"></a>Tworzenie

1. **Jak utworzyć maszynę wirtualną platformy Linux Azure za pomocą programu SQL Server?**

   Najprostszym rozwiązaniem jest utworzenie maszyny wirtualnej systemu Linux, która zawiera program SQL Server. Aby zapoznać się z samouczkiem na temat rejestrowania się na platformie Azure i tworzenia maszyny wirtualnej SQL z portalu, zobacz [Aprowizowanie maszyny wirtualnej programu Linux SQL Server w witrynie Azure portal](provision-sql-server-linux-virtual-machine.md). Istnieje również możliwość ręcznego instalowania programu SQL Server na maszynie Wirtualnej z swobodnie licencjonowaną wersję (Developer lub Express) lub ponownego użycia licencji lokalnej. Jeśli masz przy sobie własną licencję, musisz mieć [licencję Mobilności za pośrednictwem pakietu Software Assurance na platformie Azure.](https://azure.microsoft.com/pricing/license-mobility)

1. **Dlaczego nie mogę aprowizować maszyny Wirtualnej RHEL lub SLES SQL Server za pomocą subskrypcji platformy Azure, która ma limit wydatków?**

   Maszyny wirtualne RHEL i SLES wymagają subskrypcji bez limitu wydatków i zweryfikowanej metody płatności (zwykle karty kredytowej) skojarzonej z subskrypcją. Jeśli aprowizujesz maszynę wirtualną RHEL lub SLES bez usuwania limitu wydatków, subskrypcja zostanie wyłączona, a wszystkie maszyny wirtualne/usługi zatrzymane. Jeśli napotkasz ten stan, aby ponownie włączyć [subskrypcję, usuń limit wydatków](https://account.windowsazure.com/subscriptions). Pozostałe środki zostaną przywrócone w bieżącym cyklu rozliczeniowym, ale dopłata do obrazu RHEL lub SLES VM będzie sprzeczna z twoją kartą kredytową, jeśli zdecydujesz się ponownie uruchomić i kontynuować jej uruchamianie.

## <a name="licensing"></a>Licencjonowanie

1. **Jak mogę zainstalować moją licencjonowaną kopię programu SQL Server na maszynie wirtualnej platformy Azure?**

   Najpierw utwórz maszynę wirtualną tylko do systemu operacyjnego Linux. Następnie uruchom [kroki instalacji programu SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) dla dystrybucji systemu Linux. Jeśli nie instalujesz jednej z swobodnie licencjonowanych wersji programu SQL Server, musisz mieć również licencję programu SQL Server i [mobilność licencji za pośrednictwem pakietu Software Assurance na platformie Azure.](https://azure.microsoft.com/pricing/license-mobility/)

1. **Czy istnieją obrazy maszyn wirtualnych Systemu Linux typu Bring-Your-Own-License (BYOL) dla programu SQL Server?**

   W tej chwili nie ma obrazów maszyn wirtualnych BYOL Linux dla programu SQL Server. Można jednak ręcznie zainstalować program SQL Server na maszynie wirtualnej tylko dla systemu Linux, jak omówiono w poprzednich pytaniach.

1. **Czy mogę zmienić maszynę wirtualną tak, aby używać mojej licencji programu SQL Server, jeśli została ona utworzona z jednego z obrazów z galerii w modelu płatności zgodnie z rzeczywistym użyciem?**

   Nie. Nie można przełączyć się z licencjonowania płatności na sekundę na korzystanie z własnej licencji. Należy utworzyć nową maszynę wirtualną systemu Linux, zainstalować program SQL Server i przeprowadzić migrację danych. Zobacz poprzednie pytanie, aby uzyskać więcej informacji na temat przynoszenia własnej licencji.

## <a name="administration"></a>Administracja

1. **Czy mogę zarządzać maszyną wirtualną programu Linux SQL Server za pomocą programu SQL Server Management Studio (SSMS)?**

   Tak, ale SSMS jest obecnie narzędziem tylko dla systemu Windows. Aby używać systemu SSMS z maszynami wirtualnymi programu Linux SQL Server, należy połączyć się zdalnie z komputera z systemem Windows. Lokalnie w systemie Linux nowe narzędzie [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) może wykonywać wiele zadań administracyjnych. Aby uzyskać wieloplatformowe narzędzie do zarządzania bazami danych, zobacz [Usługa Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is).

1. **Czy mogę usunąć całkowicie program SQL Server z maszyny wirtualnej SQL?**

   Tak, ale nadal będzie naliczana opłata za maszynę wirtualną SQL zgodnie z opisem w [wskazówki dotyczące cen dla maszyn wirtualnych platformy Azure programu SQL Server.](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json) Jeśli program SQL Server nie jest już potrzebny, można wdrożyć nową maszynę wirtualną, a następnie migrować dane i aplikacje do nowej maszyny wirtualnej. Następnie można usunąć maszynę wirtualną z programem SQL Server.

## <a name="updating-and-patching"></a>Aktualizowanie i łatanie

1. **Jak uaktualnić do nowej wersji/wersji programu SQL Server na maszynie Wirtualnej platformy Azure?**

   obecnie nie są dostępne żadne uaktualnienia w miejscu dla programu SQL Server uruchomionego na maszynie wirtualnej platformy Azure. Utwórz nową maszynę wirtualną platformy Azure z odpowiednią wersją programu SQL Server, a następnie migruj bazy danych na nowy serwer przy użyciu [standardowych technik migracji danych](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Ogólne

1. **Czy rozwiązania SQL Server o wysokiej dostępności są obsługiwane na maszynach wirtualnych platformy Azure?**

   Nie w tej chwili. Zawsze włączone grupy dostępności i klastrowanie trybu failover wymagają rozwiązania klastrowania w systemie Linux, takiego jak rozrusznik serca. Obsługiwane dystrybucje systemu Linux dla programu SQL Server nie obsługują ich wysokiej dostępności dodatków w chmurze.

## <a name="resources"></a>Resources

**Maszyny wirtualne z systemem Linux**:

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Linux](sql-server-linux-virtual-machines-overview.md)
* [Aprowizuj maszynę wirtualną systemu SQL Server z systemem Linux](provision-sql-server-linux-virtual-machine.md)
* [Dokumentacja programu SQL Server on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Maszyny wirtualne z systemem Windows**:

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Aprowizuj maszynę wirtualną systemu Windows z systemem SQL Server](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Często zadawane pytania (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)