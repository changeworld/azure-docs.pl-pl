---
title: Konfigurację magazynu dla maszyn wirtualnych programu SQL Server | Dokumentacja firmy Microsoft
description: W tym temacie opisano, jak Azure konfiguruje magazynu dla maszyn wirtualnych programu SQL Server podczas inicjowania obsługi administracyjnej (modelu wdrażania usługi Resource Manager). Wyjaśniono również, jak skonfigurować magazyn dla istniejących maszyn wirtualnych serwera SQL.
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
ms.openlocfilehash: 21c8b955d48da03559097db93b2cb66029a203ec
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399087"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Konfigurację magazynu dla maszyn wirtualnych programu SQL Server
Po skonfigurowaniu obraz maszyny wirtualnej programu SQL Server na platformie Azure, portalu pomaga zautomatyzować konfigurację magazynu. W tym podłączania do maszyny Wirtualnej, udostępnienie magazynu do programu SQL Server i skonfigurowania go do Optymalizuj pod kątem wymagań dotyczących wydajności dotyczące magazynu.

W tym temacie wyjaśniono, jak Azure konfiguruje magazynu dla maszyn wirtualnych programu SQL Server podczas inicjowania obsługi administracyjnej oraz dla istniejących maszyn wirtualnych. Ta konfiguracja jest oparta na [najlepsze rozwiązania w zakresie wydajności](virtual-machines-windows-sql-performance.md) dla maszyn wirtualnych platformy Azure, programem SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Aby korzystać z automatycznego przechowywania ustawień konfiguracji, maszyny wirtualnej wymaga następującej charakterystyce:

* Z udostępnionym [obraz w galerii programu SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Używa [modelu wdrażania usługi Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Używa [magazyn w warstwie Premium](../premium-storage.md).

## <a name="new-vms"></a>Nowe maszyny wirtualne
W poniższych sekcjach opisano sposób konfigurowania magazynu dla maszyn wirtualnych programu SQL Server.

### <a name="azure-portal"></a>Azure Portal
Podczas inicjowania obsługi administracyjnej maszyny Wirtualnej platformy Azure przy użyciu obrazu galerii programu SQL Server, można automatycznie skonfigurować magazyn dla nowej maszyny Wirtualnej. Należy określić rozmiar magazynu ograniczeń wydajności, a typ obciążenia. Poniższy zrzut ekranu przedstawia blok konfiguracji magazynu używane podczas maszyny Wirtualnej SQL inicjowania obsługi administracyjnej.

![Konfiguracja magazynu maszyny Wirtualnej programu SQL Server podczas inicjowania obsługi](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

W oparciu o wybrane opcje, Azure wykonuje następujące zadania konfiguracji magazynu po utworzeniu maszyny Wirtualnej:

* Tworzy i dołącza dysków w warstwie premium magazynu danych do maszyny wirtualnej.
* Konfiguruje dyski danych, aby umożliwić dostęp do programu SQL Server.
* Skonfigurowanie dysków danych w puli magazynu, na podstawie określonego rozmiaru i wydajności (IOPS i przepływność) wymagań.
* Kojarzy puli magazynu o nowy dysk na maszynie wirtualnej.
* Optymalizuje ten nowy dysk, na podstawie Twojej typu określonego obciążenia (magazynowanie danych, przetwarzanie transakcji, lub ogólne).

Aby uzyskać więcej szczegółowych informacji dotyczących sposobu Azure umożliwia skonfigurowanie ustawień magazynu, zobacz [sekcji konfiguracji magazynu](#storage-configuration). Aby uzyskać pełny Przewodnik tworzenia maszyny Wirtualnej programu SQL Server w portalu Azure, zobacz [samouczku dotyczącym aprowizacji](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Szablony Zarządzaj zasobów
Jeśli korzystasz z następujących szablonów usługi Resource Manager dwóch dysków danych w warstwie premium są dołączone domyślnie z nie konfiguracji puli magazynu. Można jednak dostosować te szablony, aby zmienić liczbę dysków danych w warstwie premium dołączonych do maszyny wirtualnej.

* [Tworzenie maszyny Wirtualnej z automatycznego tworzenia kopii zapasowej](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Tworzenie maszyny Wirtualnej z automatyczne stosowanie poprawek](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Tworzenie maszyny Wirtualnej z Integracja](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Istniejące maszyny wirtualne
Dla istniejących maszyn wirtualnych serwera SQL można zmodyfikować niektórych ustawień magazynu w portalu Azure. Wybierz maszyny Wirtualnej, przejdź do obszaru Ustawienia, a następnie wybierz konfigurację serwera SQL. Blok konfiguracji serwera SQL zawiera bieżące użycie magazynu maszyny Wirtualnej. Wszystkie dyski, które istnieją na maszynie Wirtualnej są wyświetlane na tym wykresie. Dla każdego dysku miejsca do magazynowania jest wyświetlany w cztery sekcje:

* Danych SQL
* Dziennika SQL
* Inne (z systemem innym niż SQL magazynu)
* Dostępna

![Konfigurowanie magazynu dla istniejącego programu SQL Server maszyny Wirtualnej](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Aby skonfigurować Magazyn, aby dodać nowy dysk lub rozszerzyć istniejący dysk, kliknij łącze edycji powyżej wykresu.

Opcje konfiguracji, które widać, może być różna w zależności od tego, czy używasz tej funkcji przed. Korzystając z po raz pierwszy, można określić wymagania dotyczące magazynowania dla nowego dysku. Jeśli poprzednio korzystano tej funkcji do utworzenia dysku, można rozszerzyć magazynu tego dysku.

### <a name="use-for-the-first-time"></a>Użyj po raz pierwszy
Jeśli jest używany po raz pierwszy za pomocą tej funkcji, można określić limity rozmiaru i wydajności magazynu dla nowego dysku. To środowisko jest podobny do zobaczysz na inicjowanie obsługi administracyjnej czasu. Główna różnica polega na tym, że nie masz uprawnień do określania typu obciążenia. Tego ograniczenia zapobiega zakłócania wszystkie istniejące konfiguracje programu SQL Server na maszynie wirtualnej.

![Konfigurowanie programu SQL Server magazynu suwaki](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Platforma Azure tworzy nowy dysk, na podstawie specyfikacji użytkownika. W tym scenariuszu Azure wykonuje następujące zadania magazynu w konfiguracji:

* Tworzy i dołącza dysków w warstwie premium magazynu danych do maszyny wirtualnej.
* Konfiguruje dyski danych, aby umożliwić dostęp do programu SQL Server.
* Skonfigurowanie dysków danych w puli magazynu, na podstawie określonego rozmiaru i wydajności (IOPS i przepływność) wymagań.
* Kojarzy puli magazynu o nowy dysk na maszynie wirtualnej.

Aby uzyskać więcej szczegółowych informacji dotyczących sposobu Azure umożliwia skonfigurowanie ustawień magazynu, zobacz [sekcji konfiguracji magazynu](#storage-configuration).

### <a name="add-a-new-drive"></a>Dodaj nowy dysk
Jeśli został już skonfigurowany magazyn na maszyną Wirtualną programu SQL Server, rozszerzanie magazynu powoduje wyświetlenie dwóch nowych opcji. Pierwsza opcja jest aby dodać nowy dysk, co może zwiększyć poziom wydajności maszyny Wirtualnej.

![Dodaj nowy dysk do maszyny Wirtualnej SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

Jednak po dodaniu dysku, należy wykonać pewne dodatkowe czynności konfiguracyjne, które umożliwia zwiększenie wydajności.

### <a name="extend-the-drive"></a>Rozszerz dysk
Druga opcja dotyczące rozszerzania magazynu to rozszerzenie istniejącego dysku. Ta opcja zwiększa dostępny magazyn na dysku, ale nie zwiększa wydajność. Używając puli magazynu nie można zmienić liczbę kolumn, po utworzeniu puli magazynu. Liczba kolumn określa liczbę równoległych zapisy, które mogą być rozkładane na dyskach danych. W związku z tym wszystkie dyski dodane danych nie może zwiększyć wydajność. Użytkownik tylko podać więcej pamięci masowej dla zapisywanych danych. To ograniczenie również oznacza, że, podczas rozszerzania dysku, liczbę kolumn określa minimalną liczbę dysków z danymi, które można dodać. Dlatego po utworzeniu puli magazynów z dysków z danymi cztery liczba kolumn jest również cztery. Zawsze, gdy rozszerzenie magazynu, należy dodać dyski danych co najmniej cztery.

![Rozszerz dysk maszyny wirtualnej SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Konfiguracja usługi Storage
Ta sekcja zawiera odwołanie do zmiany konfiguracji magazynu, które Azure automatycznie wykonuje podczas obsługi maszyny Wirtualnej SQL lub konfiguracją w portalu Azure.

* Jeśli wybrano mniej niż dwóch tabel magazynu dla maszyny Wirtualnej, Azure nie powoduje utworzenia puli magazynu.
* Jeśli wybrano co najmniej dwóch tabel magazynu dla maszyny Wirtualnej, Azure konfiguruje pulę magazynu. W następnej sekcji tego tematu zawiera szczegółowe informacje o konfiguracji puli magazynu.
* Automatyczne magazynu konfiguracji zawsze używa [magazyn w warstwie premium](../premium-storage.md) P30 dysków z danymi. W rezultacie jest 1:1 mapowanie między wybranej liczby terabajty i liczba dysków danych dołączonych do maszyny Wirtualnej.

Aby uzyskać informacje o cenach, zobacz [cennik Storage](https://azure.microsoft.com/pricing/details/storage) strony na **Magazyn dyskowy** kartę.

### <a name="creation-of-the-storage-pool"></a>Tworzenie puli magazynu
Platforma Azure korzysta następujące ustawienia, można utworzyć puli magazynu na maszynach wirtualnych serwera SQL.

| Ustawienie | Wartość |
| --- | --- |
| Rozmiar usługi STRIPE |256 KB (magazynowanie danych); 64 KB (transakcyjnej) |
| Rozmiary dysków |1 TB |
| Pamięć podręczna |Odczyt |
| Rozmiar alokacji |Rozmiar jednostki alokacji systemu plików NTFS 64 KB |
| Inicjowanie błyskawicznych plików |Enabled (Włączony) |
| Blokowanie stron w pamięci |Enabled (Włączony) |
| Odzyskiwanie |Odzyskiwania prostego (Brak odporności) |
| Liczba kolumn |Liczba dysków danych<sup>1</sup> |
| Lokalizacja bazy danych TempDB |Przechowywane na dyskach danych<sup>2</sup> |

<sup>1</sup> po utworzeniu puli magazynu nie można zmienić liczbę kolumn w puli magazynu.

<sup>2</sup> to ustawienie dotyczy tylko pierwszego dysku utworzonych za pomocą funkcji konfiguracji magazynu.

## <a name="workload-optimization-settings"></a>Ustawienia optymalizacji obciążenia
W poniższej tabeli opisano dostępne opcje typu trzy obciążenia i ich odpowiednich optymalizacje:

| Typ obciążenia | Opis | Optymalizacje |
| --- | --- | --- |
| Ogólne |Ustawienie domyślne obsługujące większość obciążeń |None |
| **Przetwarzania transakcyjnego** |Optymalizuje magazyn dla obciążeń OLTP tradycyjne bazy danych |Flaga śledzenia 1117<br/>Flaga śledzenia 1118 |
| **Magazynowania danych** |Optymalizuje magazyn dla analizą i raportami obciążeń |Flaga śledzenia 610<br/>Flaga śledzenia 1117 |

> [!NOTE]
> Typ obciążenia można określić tylko w przypadku, gdy aprowizowanie maszyny wirtualnej programu SQL, wybierz je w kroku konfiguracji magazynu.
>
>

## <a name="next-steps"></a>Kolejne kroki
Do innych tematów związanych z programem SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych Azure](virtual-machines-windows-sql-server-iaas-overview.md).
