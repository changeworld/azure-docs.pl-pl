---
title: Konfiguracja usługi Storage dla maszyn wirtualnych programu SQL Server | Dokumentacja firmy Microsoft
description: W tym temacie opisano, jak platforma Azure konfiguruje magazynu dla maszyn wirtualnych programu SQL Server podczas inicjowania obsługi administracyjnej (model wdrażania usługi Resource Manager). Wyjaśniono również, jak konfigurować magazynu dla istniejących maszyn wirtualnych serwera SQL.
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: ninarn
ms.openlocfilehash: 360ffb3d2c682d6bd2344cb3ae95447ff3df278d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076869"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Konfiguracja usługi Storage dla maszyn wirtualnych programu SQL Server

Po skonfigurowaniu obrazu maszyny wirtualnej programu SQL Server na platformie Azure Portal pozwala zautomatyzować konfigurację magazynu. W tym podłączania magazynu z maszyną wirtualną, ułatwianie dostępu do programu SQL Server do tego magazynu, a jego konfigurowania optymalizacji wydajności specyficznych wymagań.

W tym temacie wyjaśniono, jak platforma Azure konfiguruje magazynu dla maszyn wirtualnych programu SQL Server zarówno podczas inicjowania obsługi, jak i dla istniejących maszyn wirtualnych. Ta konfiguracja jest oparta na [najlepsze rozwiązania w zakresie wydajności](virtual-machines-windows-sql-performance.md) dla maszyn wirtualnych Azure z programem SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z automatycznego przechowywania ustawień konfiguracji, maszyna wirtualna wymaga następujących właściwości:

* Aprowizowane przy użyciu [obrazu z galerii programu SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Używa [modelu wdrażania usługi Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Używa [premium SSD](../disks-types.md).

## <a name="new-vms"></a>Nowe maszyny wirtualne

Poniższe sekcje zawierają opis konfigurowania magazynu dla nowych maszyn wirtualnych programu SQL Server.

### <a name="azure-portal"></a>Azure Portal

Podczas aprowizowania maszyny Wirtualnej platformy Azure przy użyciu obrazu z galerii programu SQL Server, można automatycznie konfigurowania magazynu dla nowej maszyny Wirtualnej. Należy określić rozmiar magazynu, limity wydajności i obciążenia typu. Poniższy zrzut ekranu przedstawiono bloku konfiguracji magazynu, które są używane podczas maszyny Wirtualnej SQL inicjowania obsługi administracyjnej.

![Konfiguracja magazynu maszyny Wirtualnej programu SQL Server podczas inicjowania obsługi](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Oparte na wybrane opcje, platforma Azure przeprowadza następujących zadań konfiguracji magazynu po utworzeniu maszyny Wirtualnej:

* Tworzy i dołącza dyski SSD w warstwie premium do maszyny wirtualnej.
* Konfiguruje dyski danych, aby umożliwić dostęp do programu SQL Server.
* Konfiguruje dyski danych w puli magazynu, na podstawie określonego rozmiaru i wydajności (operacje We/Wy i przepływność) wymagań.
* Kojarzy puli magazynu z nowego dysku na maszynie wirtualnej.
* Optymalizuje tego nowego dysku na podstawie Twojego typu określonego obciążenia (magazynowanie danych, przetwarzanie transakcji, lub ogólne).

Aby uzyskać więcej szczegółowych informacji dotyczących sposobu Azure konfiguruje ustawienia magazynu, zobacz [sekcji konfiguracji magazynu](#storage-configuration). Pełne instrukcje dotyczące sposobu tworzenia maszyny Wirtualnej programu SQL Server w witrynie Azure portal, zobacz [samouczku dotyczącym aprowizacji](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Szablony zarządzania zasobów

Korzystając z poniższych szablonów usługi Resource Manager, domyślnie, bez konieczności konfiguracji puli magazynu są dołączane dwa dyski danych premium. Można jednak dostosować te szablony, aby zmienić liczbę dysków danych w warstwie premium, które są dołączone do maszyny wirtualnej.

* [Tworzenie maszyny Wirtualnej, dzięki zautomatyzowanej kopii zapasowej](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Tworzenie maszyny Wirtualnej z automatyczne stosowanie poprawek](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Tworzenie maszyny Wirtualnej z Integracja](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Istniejące maszyny wirtualne

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Dla istniejących maszyn wirtualnych programu SQL Server można zmodyfikować niektóre ustawienia magazynu w witrynie Azure portal. Otwórz swoje [zasobów maszyn wirtualnych SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource)i wybierz **Przegląd**. Na stronie Omówienie programu SQL Server znajdują się bieżące użycie magazynu maszyny wirtualnej. Wszystkie dyski, które istnieją na maszynie Wirtualnej są wyświetlane na tym wykresie. Dla każdego dysku miejsca do magazynowania jest wyświetlany w cztery sekcje:

* Dane SQL
* Dziennik SQL
* Inne (magazyn-SQL)
* Dostępne

Aby zmodyfikować ustawienia magazynu, wybierz **Konfiguruj** w obszarze **ustawienia**. 

![Konfigurowanie magazynu dla istniejącej maszyny Wirtualnej serwera SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Opcje konfiguracji, które widzisz różni się w zależności od tego, czy używasz tej funkcji przed. Korzystając z po raz pierwszy, można określić wymagań dotyczących magazynu dla nowego dysku. Jeśli wcześniej używano tej funkcji w celu utworzenia dysku, można rozszerzyć magazynu tego dysku.

### <a name="use-for-the-first-time"></a>Użyj po raz pierwszy

Jeśli po raz pierwszy przy użyciu tej funkcji, można określić limity rozmiaru i wydajności magazynu dla nowego dysku. To środowisko jest podobny do zobaczysz na czas obsługi administracyjnej. Główną różnicą jest to, że nie masz uprawnień do określania typu obciążenia. To ograniczenie zapobiega zakłóca żadnych istniejących konfiguracji programu SQL Server na maszynie wirtualnej.

Platforma Azure tworzy nowy dysk, na podstawie Twojej specyfikacji. W tym scenariuszu platforma Azure przeprowadza następujących zadań konfiguracji magazynu:

* Tworzy i dołącza dysków magazynu premium storage danych do maszyny wirtualnej.
* Konfiguruje dyski danych, aby umożliwić dostęp do programu SQL Server.
* Konfiguruje dyski danych w puli magazynu, na podstawie określonego rozmiaru i wydajności (operacje We/Wy i przepływność) wymagań.
* Kojarzy puli magazynu z nowego dysku na maszynie wirtualnej.

Aby uzyskać więcej szczegółowych informacji dotyczących sposobu Azure konfiguruje ustawienia magazynu, zobacz [sekcji konfiguracji magazynu](#storage-configuration).

### <a name="add-a-new-drive"></a>Dodaj nowy dysk

Jeśli już skonfigurowano magazynu na maszynie Wirtualnej programu SQL Server, rozszerzając magazynu powoduje wyświetlenie dwóch nowych opcji. Pierwsza opcja jest Dodaj nowy dysk, co może zwiększyć poziom wydajności maszyny wirtualnej.

Jednak po dodaniu dysku, należy wykonać pewne dodatkowe czynności konfiguracyjne do osiągnięcia wzrost wydajności.

### <a name="extend-the-drive"></a>Rozszerz dysk

Inną opcją dotyczące rozszerzania magazynu jest rozszerzenie istniejącego dysku. Spowoduje to zwiększenie dostępnego magazynu na dysku, ale nie zwiększa wydajność. Pule magazynu nie można zmienić liczbę kolumn, po utworzeniu puli magazynu. Liczba kolumn określa liczbę równoległych zapisy, które mogą być rozłożone na dyski z danymi. W związku z tym wszystkie dyski dodane dane nie może zwiększyć wydajność. Więcej pamięci masowej mogą podać tylko dla zapisywanych danych. To ograniczenie oznacza również, że podczas rozszerzania dysku, liczbę kolumn określa minimalną liczbę dysków z danymi, które można dodać. Dlatego po utworzeniu puli magazynu z cztery dyski danych, liczba kolumn jest również cztery. Za każdym razem, gdy możesz rozszerzyć magazynu, należy dodać co najmniej cztery dyski danych.

![Rozszerzanie dysku dla maszyny Wirtualnej SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Konfiguracja usługi Storage

Ta sekcja zawiera odwołanie do zmiany konfiguracji magazynu, które platforma Azure automatycznie przeprowadza podczas aprowizowania maszyn wirtualnych SQL lub konfiguracji w witrynie Azure portal.

* Mniej niż 2 TB magazynu zaznaczenie dla maszyny Wirtualnej platformy Azure nie powoduje utworzenia puli magazynów.
* Jeśli wybrano co najmniej dwóch TB magazynu dla maszyny Wirtualnej, platforma Azure konfiguruje pulę magazynu. Następna sekcji tego tematu zawiera szczegółowe informacje o konfiguracji puli magazynu.
* Konfiguracja automatycznego przechowywania zawsze używa [premium SSD](../disks-types.md) dysków z danymi P30. W związku z tym istnieje mapowanie 1:1 między wybranej liczby terabajtów, a liczba dysków danych podłączonych do maszyny Wirtualnej.

Aby uzyskać informacje o cenach, zobacz [ceny za magazyn](https://azure.microsoft.com/pricing/details/storage) stronie **Magazyn dyskowy** kartę.

### <a name="creation-of-the-storage-pool"></a>Tworzenie puli magazynu

Platforma Azure stosuje następujące ustawienia, aby utworzyć pulę magazynu na maszynach wirtualnych programu SQL Server.

| Ustawienie | Wartość |
| --- | --- |
| Rozmiar woluminu rozłożonego |256 KB (magazynowania danych); 64 KB (transakcyjnych) |
| Rozmiary dysków |1 TB |
| Pamięć podręczna |Odczyt |
| Rozmiar alokacji |Rozmiar jednostki alokacji systemu plików NTFS 64 KB |
| Inicjowanie błyskawiczne plików |Enabled (Włączony) |
| Blokowanie stron w pamięci |Enabled (Włączony) |
| Odzyskiwanie |Odzyskiwanie proste (Brak odporności) |
| Liczba kolumn |Liczba dysków danych<sup>1</sup> |
| Lokalizacja bazy danych TempDB |Przechowywane na dyskach danych<sup>2</sup> |

<sup>1</sup> po utworzeniu puli magazynów nie można zmienić liczbę kolumn w puli magazynów.

<sup>2</sup> to ustawienie dotyczy tylko pierwszego dysku, możesz utworzyć przy użyciu funkcji konfiguracji magazynu.

## <a name="workload-optimization-settings"></a>Ustawienia optymalizacji obciążenia

W poniższej tabeli opisano dostępne opcje typu trzy obciążenia i ich odpowiednich optymalizacje:

| Typ obciążeń | Opis | Optymalizacje |
| --- | --- | --- |
| **Ogólne** |Ustawienie domyślne obsługujące większość obciążeń |Brak |
| **Przetwarzanie transakcyjne** |Optymalizuje magazyn dla tradycyjnych baz danych obciążeń OLTP |Flaga śledzenia 1117<br/>Flaga śledzenia 1118 |
| **Magazynowania danych** |Optymalizuje magazyn dla obciążeń analizy i raportowania |Flaga śledzenia 610<br/>Flaga śledzenia 1117 |

> [!NOTE]
> Typ obciążenia można określić tylko podczas aprowizowania maszyny wirtualnej programu SQL, wybierając ją w kroku konfiguracji magazynu.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać inne tematy związane z programem SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych Azure](virtual-machines-windows-sql-server-iaas-overview.md).
