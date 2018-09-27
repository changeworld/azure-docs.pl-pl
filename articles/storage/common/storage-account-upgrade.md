---
title: Uaktualnianie do konta magazynu ogólnego przeznaczenia v2 — usługi Azure Storage | Dokumentacja firmy Microsoft
description: Uaktualnij do kont magazynu ogólnego przeznaczenia w wersji 2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: tamram
ms.openlocfilehash: 6e77c4836531a7efd0b52b9a411ac40ff6a613fa
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224498"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Uaktualnienie do konta magazynu ogólnego przeznaczenia w wersji 2

Konta magazynu ogólnego przeznaczenia v2 obsługi najnowszych funkcji usługi Azure Storage i uwzględnić je wszystkie funkcje ogólnego przeznaczenia w wersji 1 i kont usługi Blob storage. Konta ogólnego przeznaczenia v2 są zalecane w przypadku większości scenariuszy magazynu. Konta ogólnego przeznaczenia w wersji 2 dostarczanie najniższy gigabajt ceny pojemności dla usługi Azure Storage oraz konkurencyjne w branży ceny transakcji.

Uaktualnienie do konta magazynu ogólnego przeznaczenia v2 z Twojej ogólnego przeznaczenia w wersji 1 lub kont usługi Blob storage jest proste. Można uaktualnić za pomocą witryny Azure portal, programu PowerShell lub wiersza polecenia platformy Azure. Trwa uaktualnianie konta nie można cofnąć i może spowodować naliczenie opłaty.

## <a name="upgrade-using-the-azure-portal"></a>Uaktualnianie za pomocą witryny Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do swojego konta magazynu.
3. W **ustawienia** kliknij **konfiguracji**.
4. W obszarze **Rodzaj konta** kliknij pozycję **Uaktualnij**.
5. W obszarze **Potwierdź uaktualnianie** wpisz nazwę konta. 
6. Kliknij przycisk **uaktualnienia** w dolnej części bloku.

## <a name="upgrade-with-powershell"></a>Podnoszenie poziomu przy użyciu programu PowerShell

Aby podnieść poziom konta ogólnego przeznaczenia w wersji 1 do konta ogólnego przeznaczenia w wersji 2 przy użyciu programu PowerShell, należy najpierw zaktualizować program PowerShell w celu używania najnowszej wersji programu **AzureRm.Storage** modułu. Aby uzyskać więcej informacji na temat instalowania programu PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). 

Następnie wywołaj następujące polecenie, aby uaktualnić konto, zastępując nazwę grupy zasobów i konto magazynu:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Podnoszenie poziomu za pomocą interfejsu wiersza polecenia platformy Azure

Aby podnieść poziom konta ogólnego przeznaczenia w wersji 1 do konta ogólnego przeznaczenia w wersji 2 przy użyciu wiersza polecenia platformy Azure, należy najpierw zainstalować najnowszą wersję interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia, zobacz [Instalacja interfejsu wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

Następnie wywołaj następujące polecenie, aby uaktualnić konto, zastępując nazwę grupy zasobów i konto magazynu:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
``` 

## <a name="specify-an-access-tier-for-blob-data"></a>Określenie warstwy dostępu do danych obiektów blob

Konta ogólnego przeznaczenia v2 obsługują wszystkie usługi Azure storage i obiekty danych, ale warstwy dostępu są dostępne tylko dla blokowych obiektów blob w magazynie obiektów Blob. Podczas uaktualniania do konta magazynu ogólnego przeznaczenia w wersji 2 można określić warstwy dostępu do danych obiektów blob. 

Warstwy dostępu umożliwiają wybranie najbardziej ekonomiczny magazyn na podstawie wzorców Twojej oczekiwanego użycia. Blokowe obiekty BLOB mogą być przechowywane w warstwie gorąca, chłodna lub archiwum warstwy. Aby uzyskać więcej informacji na temat warstw dostępu, zobacz [usługi Azure Blob storage: gorąca, chłodna i archiwalna magazynu](../blobs/storage-blob-storage-tiers.md).

Domyślnie nowe konto magazynu jest tworzony w ramach warstwy dostępu gorąca, a konto magazynu ogólnego przeznaczenia w wersji 1 jest uaktualniany do warstwy dostępu gorąca. Jeśli analizujesz które warstwy dostępu na potrzeby usługi danych po uaktualnieniu, należy wziąć pod uwagę danego scenariusza. Istnieją dwa scenariusze typowy użytkownik migracji do konta ogólnego przeznaczenia v2:

* Masz już istniejące konto magazynu ogólnego przeznaczenia w wersji 1 i chcesz ocenić jego konto magazynu ogólnego przeznaczenia w wersji 2 w ramach dobraną warstwą magazynowania danych obiektów blob.
* Zamierzasz użyć konta magazynu ogólnego przeznaczenia w wersji 2 lub już mieć jedną i chcesz ocenić, czy warstwy magazynowania gorąca czy chłodna należy używać do danych obiektów blob.

W obu przypadkach priorytetem jest oszacować koszt przechowywania, uzyskiwanie dostępu i wykonywanie operacji na danych przechowywanych na koncie magazynu ogólnego przeznaczenia w wersji 2 i porównać z obecnie ponoszonymi kosztami.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Oszacuj koszty swoje bieżące wzorce użycia

Aby oszacować koszty magazynowania i uzyskiwania dostępu do danych obiektów blob w ramach konta magazynu ogólnego przeznaczenia w wersji 2 w określonej warstwie, oceny istniejącego wzorca użycia lub określenie w przybliżeniu oczekiwanego wzorca użycia. Ogólnie potrzebne są odpowiedzi na następujące pytania:

* Obiekt Blob zużycie miejsca w magazynie, w gigabajtach, w tym:
    - Ile danych jest przechowywanych na koncie magazynu?
    - Jak zmienia się ilość danych w miesiącu? Czy nowe dane nieustannie zastępują stare dane?
* Wzorzec dostępu dane magazynu obiektów Blob, w tym:
    - Jak dużo danych jest odczytywanych i zapisywanych do konta magazynu? 
    - Jak wiele operacji odczytu i zapisu operacje są wykonywane na danych w ramach konta magazynu?

Aby wybrać najważniejsze dla Twoich potrzeb warstwę dostępu, może być przydatne, aby określić, ile pojemności z danymi obiektów blob jest obecnie używany i sposobu korzystania z tych danych. 

Aby zebrać dane użycia dla konta magazynu, przed migracją, można monitorować przy użyciu konta magazynu [usługi Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Usługa Azure Monitor umożliwia rejestrowanie i dostarcza danych metryk dotyczących usług platformy Azure, w tym usługi Azure Storage. 

Aby monitorować użycie danych obiektów blob na koncie magazynu, należy włączyć metryki pojemności w usłudze Azure Monitor. Metryki pojemności rejestrować dane dotyczące wymagań dotyczących magazynu obiektów blob w ramach Twojego konta jest używany codziennie. Metryki wydajności może służyć do szacowania kosztów przechowywania danych na koncie magazynu. Aby dowiedzieć się, jaka pojemność magazynu obiektów Blob jest cena dla każdego typu konta, zobacz [cennik usługi Block blob](https://azure.microsoft.com/pricing/details/storage/blobs/).

Aby monitorować wzorce dostępu do danych dla magazynu obiektów Blob, należy włączyć metryki transakcji w usłudze Azure Monitor. Można filtrować według różnych operacji usługi Azure Storage, aby oszacować częstotliwość każdej nosi nazwę. Aby dowiedzieć się, jak różnego rodzaju transakcji są naliczane dla blokowych i uzupełnialnych obiektów blob dla każdego typu konta, zobacz [cennik usługi Block blob](https://azure.microsoft.com/pricing/details/storage/blobs/).  

Aby uzyskać więcej informacji na temat zbierania metryk z usługi Azure Monitor, zobacz [metryk usługi Azure Storage w usłudze Azure Monitor](storage-metrics-in-azure-monitor.md).

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie konta magazynu](storage-quickstart-create-account.md)
- [Zarządzanie kontami usługi Azure storage](storage-account-manage.md)