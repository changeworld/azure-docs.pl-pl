---
title: Program SQL Server na maszynach wirtualnych systemu Linux platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące programem SQL Server na maszynach wirtualnych systemu Linux platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: jroth
ms.openlocfilehash: 56a0629249cdb5f0f098d2b7b6d36b3fbb215009
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30190875"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Często zadawane pytania dotyczące programu SQL Server na maszynach wirtualnych Azure systemu Linux

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące uruchamiania [programu SQL Server na maszynach wirtualnych Azure Linux](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Ten artykuł skupia się na kwestie związane z programem SQL Server na maszynach wirtualnych systemu Linux. Jeśli używasz programu SQL Server na maszynach wirtualnych systemu Windows, temacie [systemu Windows — często zadawane pytania](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Obrazy

1. **Jakie galerii obrazów maszyny wirtualnej programu SQL Server są dostępne?**

   Azure obsługuje obrazy maszyny wirtualnej dla wszystkich obsługiwanych wersjach głównych programu SQL Server we wszystkich wersjach systemu dla systemów Linux i Windows. Aby uzyskać więcej informacji, zobacz pełną listę [obrazów maszyny Wirtualnej systemu Linux](sql-server-linux-virtual-machines-overview.md#create) i [obrazów maszyn wirtualnych systemu Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Istniejących obrazów Galeria maszyny wirtualnej programu SQL Server są aktualizowane?**

   Co dwa miesiące obrazów programu SQL Server w galerii maszyn wirtualnych są aktualizowane przy użyciu najnowszych systemu Linux i aktualizacji systemu Windows. W przypadku obrazów systemu Linux w tym najnowsze aktualizacje systemu. W przypadku obrazów systemu Windows w tym wszelkie aktualizacje, które są oznaczone jako ważne w usłudze Windows Update, włączając ważne aktualizacje zabezpieczeń programu SQL Server i dodatków service pack. Aktualizacje zbiorcze programu SQL Server są obsługiwane inaczej dla systemów Linux i Windows. Dla systemu Linux aktualizacji zbiorczych programu SQL Server znajdują się również w odświeżania. Ale w tej chwili maszyn wirtualnych systemu Windows nie są aktualizowane z programu SQL Server lub Windows Server aktualizacjami zbiorczymi.

1. **Co powiązanych pakietów programu SQL Server są również instalowane?**

   Aby wyświetlić pakiety programu SQL Server, które są instalowane domyślnie na maszynach wirtualnych systemu Linux serwera SQL, zobacz [zainstalowane pakiety](sql-server-linux-virtual-machines-overview.md#packages).

1. **Obrazy maszyny wirtualnej programu SQL Server uzyskać wyjąć z galerii?**

   Tak. Azure obsługuje tylko jeden obraz na głównych wersji i wydania. Na przykład po wydaniu nowego dodatku service pack programu SQL Server Azure dodaje nowy obraz w galerii tego dodatku service pack. Obraz programu SQL Server dla poprzedniego dodatku service pack jest od razu usunięte z portalu Azure. Jednak jest wciąż dostępna na potrzeby inicjowania obsługi administracyjnej z programu PowerShell dla trzy kolejne miesiące. Po upływie trzech miesięcy poprzednie obrazu dodatku service pack nie jest już dostępny. Te zasady usuwania również będą miały zastosowania, jeśli po osiągnie koniec cyklu jego życia, jest nieobsługiwana wersja programu SQL Server.

## <a name="creation"></a>Tworzenie

1. **Jak utworzyć maszynę wirtualną systemu Linux platformy Azure z programem SQL Server?**

   Najlepszym rozwiązaniem jest tworzenie maszyny wirtualnej systemu Linux, który zawiera program SQL Server. Samouczek dotyczący rejestracji na platformie Azure i tworzenia maszyny Wirtualnej SQL z portalu, zobacz [Aprowizowanie maszyny wirtualnej systemu Linux programu SQL Server, w portalu Azure](provision-sql-server-linux-virtual-machine.md). Istnieje również opcja ręcznego instalowania programu SQL Server na maszynie Wirtualnej w trybie za darmo licencjonowanej (deweloperem lub Express) lub przez ponowne użycie licencji usługi lokalnej. Jeśli użycie własnej licencji, musisz mieć [przenośność licencji za pośrednictwem programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Dlaczego nie można udostępnić RHEL lub maszyna wirtualna SLES SQL Server subskrypcji platformy Azure, która jest objęta limitem wydatków?**

   Maszyny wirtualne RHEL i SLES wymagają subskrypcji z limitu wydatków i skojarzone z subskrypcją formę płatności zweryfikowano (zazwyczaj karty kredytowej). Jeśli dostarczasz RHEL lub SLES maszyny Wirtualnej bez usunięcia limitu wydatków, subskrypcja zostanie wyłączone i zatrzymanie wszystkich maszyn wirtualnych/usług. Jeśli zostanie uruchomione w tym stanie, aby ponownie włączyć subskrypcji [usunąć limit wydatków](https://account.windowsazure.com/subscriptions). Zostaną przywrócone z pozostałych środków dla bieżącego cyklu rozliczeniowego, ale RHEL lub wirtualna SLES przeciążenia obrazu zostanie umieszczona przed karty kredytowej Jeśli zdecydujesz się na ponowne uruchomienie i kontynuować jego uruchomieniem.

## <a name="licensing"></a>Licencjonowanie

1. **Jak zainstalować mój licencjonowanej kopii programu SQL Server na maszynie Wirtualnej platformy Azure?**

   Najpierw należy utworzyć maszyny wirtualnej tylko do systemu operacyjnego Linux. Następnie uruchom [kroki instalacji programu SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) dla dystrybucji systemu Linux. Jeśli nie instalujesz jedną z pełni licencjonowanej wersji programu SQL Server, również musi mieć licencję programu SQL Server i [przenośność licencji za pośrednictwem programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Dla programu SQL Server są obrazy maszyny wirtualnej systemu Linux Bring-Your-właścicielem-License (BYOL)?**

   W tej chwili brak nie BYOL Linux obrazów maszyny wirtualnej dla programu SQL Server. Jednak można ręcznie zainstalować program SQL Server na maszynie Wirtualnej systemu Linux — tylko do zgodnie z opisem w pytania Wstecz.

1. **Czy można zmienić maszyny Wirtualnej, aby użyć własnej licencji programu SQL Server, jeśli została utworzona z jednego z obrazów z galerii?**

   Nie. Nie można zmienić na przy użyciu własnej licencji Licencjonowanie płatności na sekundę. Należy utworzyć nową maszynę Wirtualną systemu Linux, instalowania programu SQL Server i migrację danych. Zobacz na powyższe pytanie, aby uzyskać więcej informacji na temat przełączania własnej licencji.

## <a name="administration"></a>Administracja

1. **Czy mogę zarządzać maszyny wirtualnej systemu Linux programu SQL Server z programu SQL Server Management Studio (SSMS)**

   Tak, ale SSMS jest obecnie narzędzia systemu Windows. Należy połączyć zdalnie z komputera z systemem Windows do użycia narzędzia SSMS z maszyn wirtualnych systemu Linux programu SQL Server. Lokalnie w systemie Linux nowe [mssql conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) narzędzie można wykonywać wielu zadań administracyjnych. Aby wyświetlić podgląd narzędzia do zarządzania i platform bazy danych, zobacz [Studio operacji serwera SQL (wersja zapoznawcza)](https://docs.microsoft.com/sql/sql-operations-studio/what-is).

1. **Można usunąć serwera SQL całkowicie z maszyny Wirtualnej SQL?**

   Tak, ale będą nadal naliczane za maszyną Wirtualną programu SQL, zgodnie z opisem w [cennik wskazówki dotyczące maszyn wirtualnych programu SQL Server Azure](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Jeśli program SQL Server nie są już potrzebne, można wdrożyć nową maszynę wirtualną i przeprowadzić migrację danych i aplikacji do nowej maszyny wirtualnej. Następnie można usunąć maszyny wirtualnej programu SQL Server.

## <a name="updating-and-patching"></a>Aktualizowanie i stosowanie poprawek

1. **Jak uaktualnić do nowej wersji/wydania programu SQL Server w maszynie Wirtualnej platformy Azure?**

   Obecnie nie są bez uaktualnienia w miejscu programu SQL Server w maszynie Wirtualnej platformy Azure. Utworzenie nowej maszyny wirtualnej platformy Azure z żądanej wersji programu SQL Server/wydania, a następnie przeprowadzić migrację bazy danych do nowego serwera przy użyciu [standardowych danych migracji techniki](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Ogólne

1. **Rozwiązania wysokiej dostępności programu SQL Server są obsługiwane na maszynach wirtualnych Azure?**

   Nie w tej chwili. Zawsze włączone grupy dostępności i klaster pracy awaryjnej wymagają klastrowania rozwiązania w systemie Linux, takich jak rozrusznik. Obsługiwane dystrybucje systemu Linux dla programu SQL Server nie obsługują ich dodatki wysokiej dostępności w chmurze.

## <a name="resources"></a>Zasoby

**Maszyny wirtualne systemu Linux**:

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Linux](sql-server-linux-virtual-machines-overview.md)
* [Udostępnić Maszynę wirtualną systemu Linux serwera SQL](provision-sql-server-linux-virtual-machine.md)
* [Serwer SQL w dokumentacji systemu Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Maszyny wirtualne systemu Windows**:

* [Omówienie programu SQL Server w systemie Windows maszyny Wirtualnej](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Udostępnianie maszyn wirtualnych systemu Windows serwera SQL](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Często zadawane pytania (system Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)