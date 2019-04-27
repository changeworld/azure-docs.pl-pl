---
title: Program SQL Server na maszynach wirtualnych platformy Azure z systemem Linux — często zadawane pytania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania na temat uruchamiania programu SQL Server na maszynach wirtualnych Azure z systemem Linux.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: fb1cafcf9405576749ea91aeea033c6ee783a026
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60739271"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Często zadawane pytania dotyczące programu SQL Server na maszynach wirtualnych Azure Linux

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania na temat uruchamiania [programu SQL Server na maszynach wirtualnych Azure Linux](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Ten artykuł koncentruje się na kwestie związane z programu SQL Server na maszynach wirtualnych z systemem Linux. Jeśli używasz programu SQL Server na maszynach wirtualnych Windows, zobacz [Windows — często zadawane pytania](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Obrazy

1. **Jakie obrazów w galerii maszyn wirtualnych programu SQL Server są dostępne?**

   Azure obsługuje obrazy maszyn wirtualnych dla wszystkich obsługiwanych wersji głównych programu SQL Server we wszystkich wersjach dla systemów Linux i Windows. Aby uzyskać więcej informacji, zobacz pełną listę [obrazów maszyn wirtualnych z systemem Linux](sql-server-linux-virtual-machines-overview.md#create) i [obrazów maszyn wirtualnych Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Obrazy galerii w istniejącej maszyny wirtualnej programu SQL Server są aktualizowane?**

   Co dwa miesiące obrazów programu SQL Server w galerii maszyn wirtualnych są aktualizowane przy użyciu najnowszych systemu Linux i Windows aktualizacji. Dla obrazów systemu Linux w tym najnowsze aktualizacje systemu. W przypadku obrazów Windows w tym wszelkie aktualizacje, które są oznaczone jako ważne w aktualizacji Windows, w tym ważne aktualizacje zabezpieczeń programu SQL Server i dodatków service pack. Aktualizacje zbiorcze programu SQL Server są obsługiwane inaczej dla systemów Linux i Windows. Dla systemu Linux aktualizacje zbiorcze programu SQL Server znajdują się również w odświeżania. Ale w tej chwili Windows maszyny wirtualne nie zostaną zaktualizowane o aktualizacje zbiorcze programu SQL Server lub Windows Server.

1. **Co związane z instalowane są również pakiety programu SQL Server?**

   Aby wyświetlić pakiety programu SQL Server, które są instalowane domyślnie na maszynach wirtualnych systemu Linux serwera SQL, zobacz [zainstalowanych pakietów](sql-server-linux-virtual-machines-overview.md#packages).

1. **Można usuwane z galerii obrazów maszyn wirtualnych programu SQL Server?**

   Tak. Platforma Azure utrzymuje tylko jeden obraz na głównej wersji i wydania. Na przykład po udostępnieniu nowego dodatku service pack programu SQL Server, platforma Azure dodaje nowy obraz w galerii dla tego dodatku service pack. Obraz programu SQL Server dla poprzedniego dodatku service pack jest usuwane natychmiast w witrynie Azure portal. Jednak jest wciąż dostępna na potrzeby aprowizacji za pomocą programu PowerShell dla następnych trzech miesięcy. Po upływie trzech miesięcy na poprzedniej ilustracji pakiet usługi nie jest już dostępna. Te zasady usuwania czy mają zastosowanie również w wersji programu SQL Server staje się nieobsługiwany po osiągnięciu końca swojego cyklu życia.

## <a name="creation"></a>Tworzenie

1. **Jak utworzyć maszynę wirtualną systemu Linux platformy Azure z programem SQL Server?**

   Jest to najłatwiejsze rozwiązanie do utworzenia maszyny wirtualnej systemu Linux, który zawiera program SQL Server. Samouczek dotyczący rejestracji na platformie Azure i tworzenie maszyny Wirtualnej SQL z portalu, zobacz [Aprowizować maszynę wirtualną programu SQL Server w systemie Linux w witrynie Azure portal](provision-sql-server-linux-virtual-machine.md). Istnieje również opcja ręcznego instalowania programu SQL Server na maszynie Wirtualnej z oboma swobodnie licencjonowaną (Developer i Express) lub dzięki ponownemu wykorzystaniu licencji w środowisku lokalnym. W przypadku przeniesienia własnej licencji, konieczne jest posiadanie [przenośności licencji za pośrednictwem programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Dlaczego nie można aprowizować systemu RHEL lub SLES maszyny Wirtualnej z serwera SQL z subskrypcją platformy Azure, która jest objęta limitem wydatków?**

   Maszyn wirtualnych systemu RHEL lub SLES wymaga subskrypcji przy użyciu bez limitu wydatków oraz zweryfikowanej zapłaty (zazwyczaj karty kredytowej) skojarzonego z subskrypcją. Zainicjowanie obsługi systemu RHEL lub SLES maszyny Wirtualnej bez usunięcia limitu wydatków, subskrypcja zostanie wyłączone i zatrzymanie wszystkich maszyn wirtualnych/usług. Jeśli napotkasz ten stan, aby ponownie włączyć subskrypcję [Usuń limit wydatków](https://account.windowsazure.com/subscriptions). Pozostałe środki zostaną przywrócone dla bieżącego cyklu rozliczeniowego, ale przeciążenia obrazu systemu RHEL lub SLES maszyny Wirtualnej zaczną się karta kredytowa, jeśli chcesz ponownie uruchomić i nadal z niego korzystać.

## <a name="licensing"></a>Licencjonowanie

1. **Jak mogę zainstalować moją licencjonowaną kopię programu SQL Server na maszynie wirtualnej platformy Azure?**

   Najpierw należy utworzyć maszyny wirtualnej tylko do systemu operacyjnego Linux. Następnie uruchom [kroki instalacji programu SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) dla Twojej dystrybucji systemu Linux. Jeśli nie instalujesz jeden swobodnie licencjonowanej wersji programu SQL Server, również musi mieć licencję programu SQL Server i [przenośności licencji za pośrednictwem programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Dostępne są obrazy maszyny wirtualnej systemu Linux Bring-Your-Own-License (BYOL) dla programu SQL Server?**

   W tej chwili nie istnieją żadne obrazów maszyn wirtualnych systemu Linux w ramach opcji BYOL dla programu SQL Server. Jednak można ręcznie zainstalować program SQL Server na maszynie Wirtualnej systemu Linux — tylko zgodnie z opisem w odpowiedzi na powyższe pytania.

1. **Czy mogę zmienić maszynę wirtualną tak, aby używać mojej licencji programu SQL Server, jeśli została ona utworzona z jednego z obrazów z galerii w modelu płatności zgodnie z rzeczywistym użyciem?**

   Nie. Nie można przełączyć się z płatności na sekundę licencji do korzystania z własnych licencji. Należy utworzyć nową maszynę Wirtualną systemu Linux, instalacji programu SQL Server i migrację danych. Zobacz pytanie Wstecz, aby uzyskać więcej informacji o użycie własnej licencji.

## <a name="administration"></a>Administracja

1. **Czy mogę zarządzać maszyną wirtualną programu SQL Server w systemie Linux przy użyciu programu SQL Server Management Studio (SSMS)**

   Tak, ale program SSMS jest obecnie narzędzie tylko do Windows. Należy połączyć zdalnie z komputera Windows używać narzędzia SSMS przy użyciu maszyn wirtualnych systemu Linux SQL Server. Lokalnie w systemie Linux nowe [mssql conf.](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) narzędzia można wykonywać wiele zadań administracyjnych. Aby uzyskać narzędzie do zarządzania bazą danych dla wielu platform, zobacz [usługi Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is).

1. **Czy mogę usunąć całkowicie program SQL Server z maszyny wirtualnej SQL?**

   Tak, ale będą nadal naliczane za maszynę Wirtualną SQL, zgodnie z opisem w [ceny wskazówki dotyczące maszyn wirtualnych programu SQL Server Azure](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Jeśli program SQL Server nie jest już potrzebny, można wdrożyć nową maszynę wirtualną, a następnie migrować dane i aplikacje do nowej maszyny wirtualnej. Następnie można usunąć maszynę wirtualną z programem SQL Server.

## <a name="updating-and-patching"></a>Stosowanie poprawek i aktualizacji

1. **Jak uaktualnić do nowej wersji/wydania programu SQL Server w Maszynie wirtualnej platformy Azure?**

   obecnie nie są dostępne żadne uaktualnienia w miejscu dla programu SQL Server uruchomionego na maszynie wirtualnej platformy Azure. Utwórz nową maszynę wirtualną platformy Azure z odpowiednią wersją programu SQL Server, a następnie migruj bazy danych na nowy serwer przy użyciu [standardowych technik migracji danych](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Ogólne

1. **Czy rozwiązania wysokiej dostępności programu SQL Server są obsługiwane na maszynach wirtualnych platformy Azure?**

   Nie w tej chwili. Zawsze włączone grupy dostępności i klaster trybu Failover wymagają rozwiązania klastrowania w systemie Linux, takich jak program Pacemaker. Obsługiwane dystrybucje systemu Linux dla programu SQL Server nie obsługują ich dodatki wysokiej dostępności w chmurze.

## <a name="resources"></a>Zasoby

**Maszyny wirtualne z systemem Linux**:

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Linux](sql-server-linux-virtual-machines-overview.md)
* [Aprowizowanie maszyny Wirtualnej systemu Linux z programu SQL Server](provision-sql-server-linux-virtual-machine.md)
* [Dokumentacja programu SQL Server w systemie Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Maszyny wirtualne z systemem Windows**:

* [Omówienie programu SQL Server na maszynie Wirtualnej Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Aprowizowanie Windows serwer SQL maszyny Wirtualnej](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Często zadawane pytania (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)