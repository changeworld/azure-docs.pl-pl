---
title: Konfiguracja magazynu dla maszyn wirtualnych SQL Server | Microsoft Docs
description: W tym temacie opisano, jak platforma Azure konfiguruje magazyn dla SQL Server maszyn wirtualnych podczas aprowizacji (Menedżer zasobów model wdrażania). Wyjaśniono również, jak można skonfigurować magazyn dla istniejących maszyn wirtualnych SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: mathoma
ms.openlocfilehash: e28478d31a674d742870344b33eac6b84c3ae584
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123837"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Konfiguracja magazynu dla maszyn wirtualnych SQL Server

W przypadku konfigurowania SQL Server obrazu maszyny wirtualnej na platformie Azure portal pomaga zautomatyzować konfigurację magazynu. Obejmuje to dołączenie magazynu do maszyny wirtualnej, dzięki czemu można uzyskać dostęp do tego magazynu SQL Server i skonfigurować go do optymalizacji pod kątem konkretnych wymagań dotyczących wydajności.

W tym temacie wyjaśniono, jak platforma Azure konfiguruje magazyn dla maszyn wirtualnych SQL Server zarówno podczas aprowizacji, jak i dla istniejących maszyn wirtualnych. Ta konfiguracja jest oparta na [najlepszych rozwiązaniach dotyczących wydajności](virtual-machines-windows-sql-performance.md) dla maszyn wirtualnych platformy Azure z systemem SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było korzystać z ustawień konfiguracji automatycznego magazynu, maszyna wirtualna wymaga następujących cech:

* Udostępnione za pomocą [obrazu galerii SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Używa [modelu wdrażania Menedżer zasobów](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Używa warstwy [Premium dysków SSD](../disks-types.md).

## <a name="new-vms"></a>Nowe maszyny wirtualne

W poniższych sekcjach opisano sposób konfigurowania magazynu dla nowych maszyn wirtualnych SQL Server.

### <a name="azure-portal"></a>Azure Portal

Podczas aprowizacji maszyny wirtualnej platformy Azure przy użyciu obrazu galerii SQL Server możesz wybrać opcję automatycznego konfigurowania magazynu dla nowej maszyny wirtualnej. Należy określić rozmiar magazynu, limity wydajności i typ obciążenia. Poniższy zrzut ekranu przedstawia blok konfiguracji magazynu używany podczas aprowizacji maszyny wirtualnej SQL.

![SQL Server konfigurację magazynu maszyny wirtualnej podczas aprowizacji](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Na podstawie wybranych opcji platforma Azure wykonuje następujące zadania konfiguracji magazynu po utworzeniu maszyny wirtualnej:

* Tworzy i dołącza dysków SSD Premium do maszyny wirtualnej.
* Konfiguruje dyski danych, które mają być dostępne dla SQL Server.
* Konfiguruje dyski danych w puli magazynów na podstawie określonych wymagań dotyczących rozmiaru i wydajności (IOPS i przepływności).
* Kojarzy pulę magazynów z nowym dyskiem na maszynie wirtualnej.
* Optymalizuje ten nowy dysk w oparciu o określony typ obciążenia (magazynowanie danych, przetwarzanie transakcyjne lub ogólne).

Aby uzyskać więcej informacji na temat konfigurowania ustawień magazynu przez platformę Azure, zobacz [sekcję Konfiguracja magazynu](#storage-configuration). Aby zapoznać się z pełnym przewodnikiem dotyczącym tworzenia SQL Server maszyny wirtualnej w Azure Portal, zobacz [samouczek aprowizacji](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Zarządzanie szablonami zasobów

W przypadku używania następujących szablonów Menedżer zasobów są domyślnie dołączone dwa dyski z danymi Premium bez konfiguracji puli magazynu. Można jednak dostosować te szablony, aby zmienić liczbę dysków danych w warstwie Premium dołączonych do maszyny wirtualnej.

* [Tworzenie maszyny wirtualnej przy użyciu zautomatyzowanej kopii zapasowej](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Tworzenie maszyny wirtualnej z automatyczną poprawką](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Tworzenie maszyny wirtualnej z integracją AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Istniejące maszyny wirtualne

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

W przypadku istniejących maszyn wirtualnych SQL Server można zmodyfikować niektóre ustawienia magazynu w Azure Portal. Otwórz [zasób usługi SQL Virtual Machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)i wybierz pozycję **Przegląd**. Na stronie Przegląd SQL Server jest wyświetlane bieżące użycie magazynu dla maszyny wirtualnej. Na tym wykresie są wyświetlane wszystkie dyski, które znajdują się na maszynie wirtualnej. Dla każdego dysku jest wyświetlana w czterech sekcjach:

* Dane SQL
* Dziennik SQL
* Inne (magazyn inny niż SQL)
* Dostępne

Aby zmodyfikować ustawienia magazynu, wybierz pozycję **Konfiguruj** w obszarze **Ustawienia**. 

![Konfigurowanie magazynu dla istniejącej maszyny wirtualnej SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Opcje konfiguracji, które można zobaczyć, różnią się w zależności od tego, czy ta funkcja została wcześniej użyta. W przypadku korzystania z programu po raz pierwszy można określić wymagania dotyczące magazynu dla nowego dysku. Jeśli ta funkcja została wcześniej użyta do utworzenia dysku, można wybrać opcję przechowania magazynu tego dysku.

### <a name="use-for-the-first-time"></a>Użyj po raz pierwszy

Jeśli korzystasz z tej funkcji po raz pierwszy, możesz określić rozmiar magazynu i limity wydajności dla nowego dysku. To środowisko jest podobne do tego, co zobaczysz w czasie aprowizacji. Główną różnicą jest to, że nie można określić typu obciążenia. To ograniczenie uniemożliwia zakłócenie istniejących konfiguracji SQL Server na maszynie wirtualnej.

Platforma Azure tworzy nowy dysk na podstawie specyfikacji. W tym scenariuszu platforma Azure wykonuje następujące zadania konfiguracji magazynu:

* Tworzy i dołącza dyski danych magazynu Premium Storage do maszyny wirtualnej.
* Konfiguruje dyski danych, które mają być dostępne dla SQL Server.
* Konfiguruje dyski danych w puli magazynów na podstawie określonych wymagań dotyczących rozmiaru i wydajności (IOPS i przepływności).
* Kojarzy pulę magazynów z nowym dyskiem na maszynie wirtualnej.

Aby uzyskać więcej informacji na temat konfigurowania ustawień magazynu przez platformę Azure, zobacz [sekcję Konfiguracja magazynu](#storage-configuration).

### <a name="add-a-new-drive"></a>Dodaj nowy dysk

Jeśli już skonfigurowano magazyn na maszynie wirtualnej SQL Server, rozbudowany magazyn oferuje dwie nowe opcje. Pierwsza opcja polega na dodaniu nowego dysku, co może zwiększyć poziom wydajności maszyny wirtualnej.

Jednak po dodaniu dysku należy wykonać dodatkową konfigurację ręczną, aby osiągnąć wzrost wydajności.

### <a name="extend-the-drive"></a>Zwiększ dysk

Drugą opcją rozwinięcia magazynu jest rozszerzenie istniejącego dysku. Ta opcja zwiększa ilość dostępnego miejsca na dysku, ale nie zwiększa wydajności. W przypadku pul magazynów nie można zmienić liczby kolumn po utworzeniu puli magazynów. Liczba kolumn określa liczbę równoległych zapisów, które mogą być rozłożone na dyskach danych. W związku z tym wszystkie dodane dyski danych nie mogą zwiększyć wydajności. Mogą one zapewnić więcej miejsca do magazynowania dla zapisywanych danych. To ograniczenie oznacza również, że w przypadku rozszerzenia dysku liczba kolumn określa minimalną liczbę dysków z danymi, które można dodać. Dlatego w przypadku utworzenia puli magazynów z czterema dyskami danych liczba kolumn jest również cztery. Za każdym razem, gdy rozszerzy magazyn, należy dodać co najmniej cztery dyski z danymi.

![Zwiększanie dysku dla maszyny wirtualnej SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Konfiguracja usługi Storage

Ta sekcja zawiera informacje o zmianach konfiguracji magazynu, które usługa Azure automatycznie wykonuje podczas aprowizacji lub konfiguracji maszyny wirtualnej SQL w Azure Portal.

* Jeśli wybrano mniej niż dwa usługi TBs magazynu dla maszyny wirtualnej, platforma Azure nie utworzy puli magazynów.
* Jeśli wybrano co najmniej dwa usługi TBs magazynu dla maszyny wirtualnej, platforma Azure skonfiguruje pulę magazynów. W następnej sekcji tego tematu znajdują się szczegółowe informacje o konfiguracji puli magazynu.
* Funkcja automatycznej konfiguracji magazynu zawsze używa dysków danych w [warstwie Premium dysków SSD](../disks-types.md) P30. W związku z tym istnieje 1:1 mapowanie między wybraną liczbą terabajtów a liczbą dysków danych podłączonych do maszyny wirtualnej.

Aby uzyskać informacje o cenach, zobacz stronę z cennikiem usługi [Storage](https://azure.microsoft.com/pricing/details/storage) na karcie **Disk Storage** .

### <a name="creation-of-the-storage-pool"></a>Tworzenie puli magazynów

Platforma Azure używa następujących ustawień do utworzenia puli magazynów na SQL Server maszynach wirtualnych.

| Ustawienie | Value |
| --- | --- |
| Rozmiar paska |256 KB (magazynowanie danych); 64 KB (transakcyjna) |
| Rozmiary dysków |1 TB każdego |
| Pamięć podręczna |Odczyt |
| Rozmiar alokacji |rozmiar jednostki alokacji systemu plików NTFS 64 KB |
| Natychmiastowe inicjowanie pliku |Włączono |
| Zablokuj strony w pamięci |Włączono |
| Odzyskiwanie |Odzyskiwanie proste (bez odporności) |
| Liczba kolumn |Liczba dysków z danymi<sup>1</sup> |
| Lokalizacja bazy danych TempDB |Przechowywane na dyskach danych<sup>2</sup> |

<sup>1</sup> po utworzeniu puli magazynów nie można zmienić liczby kolumn w puli magazynów.

<sup>2</sup> to ustawienie dotyczy tylko pierwszego dysku tworzonego przy użyciu funkcji konfiguracji magazynu.

## <a name="workload-optimization-settings"></a>Ustawienia optymalizacji obciążenia

W poniższej tabeli opisano trzy dostępne opcje typu obciążenia oraz ich odpowiednie optymalizacje:

| Typ obciążenia | Opis | Optymalizacje |
| --- | --- | --- |
| **Ogólne** |Ustawienie domyślne obsługujące większość obciążeń |Brak |
| **Przetwarzanie transakcyjne** |Optymalizuje magazyn dla tradycyjnych obciążeń OLTP bazy danych |Flaga śledzenia 1117<br/>Flaga śledzenia 1118 |
| **Magazynowania danych** |Optymalizuje magazyn do obciążeń analitycznych i sprawozdawczych |Flaga śledzenia 610<br/>Flaga śledzenia 1117 |

> [!NOTE]
> Typ obciążenia można określić tylko podczas inicjowania obsługi administracyjnej maszyny wirtualnej SQL, wybierając ją w kroku konfiguracji magazynu.

## <a name="next-steps"></a>Następne kroki

Aby poznać inne tematy związane z uruchamianiem SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na platformie azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).
