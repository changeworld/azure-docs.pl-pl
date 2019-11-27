---
title: Włączanie i tworzenie dużych udziałów plików — Azure Files
description: W tym artykule dowiesz się, jak włączyć i utworzyć duże udziały plików.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 34b8af56a8f2f108b96ca07fa73f90bb9eb5bf13
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422732"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>Jak włączyć i utworzyć duże udziały plików

Początkowo standardowe udziały plików mogą skalować maksymalnie do 5 TiB, teraz z dużymi udziałami plików, można skalować do 100 TiB. Domyślnie można skalować do 100 TiB. 

Aby można było skalować do 100 TiB przy użyciu standardowych udziałów plików, należy włączyć konto magazynu do używania dużych udziałów plików. Możesz włączyć istniejące konto lub utworzyć nowe konto, aby korzystać z dużych udziałów plików.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- Jeśli zamierzasz korzystać z interfejsu wiersza polecenia platformy Azure, upewnij się, że [zainstalowano najnowszą wersję](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Jeśli zamierzasz używać Azure PowerShell, upewnij się, że [zainstalowano najnowszą wersję](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Ograniczenia

Konta obsługujące duże udziały plików obsługują funkcję LRS lub ZRS. Na razie duże udziały plików obsługują konta, które nie obsługują GZRS, GRS lub RA-GRS. Włączenie dużych udziałów plików na koncie jest procesem nieodwracalnym, po włączeniu którego nie można przekonwertować konta na GZRS, GRS lub RA-GRS.

## <a name="create-a-new-storage-account"></a>Tworzenie nowego konta magazynu

### <a name="portal"></a>Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wpisz wartość **Konta magazynu**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Konta usługi Storage**.
1. W oknie **Konta magazynu**, które zostanie wyświetlone, wybierz pozycję **Dodaj**.
1. Wybierz subskrypcję, w ramach której chcesz utworzyć konto magazynu.
1. W polu **Grupa zasobów** wybierz pozycję **Utwórz nową**. Wprowadź nazwę nowej grupy zasobów, jak pokazano na poniższej ilustracji.

    ![Zrzut ekranu przedstawiający sposób tworzenia grupy zasobów w portalu](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Następnie wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie całej platformy Azure. Ponadto nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.
1. Wybierz lokalizację dla konta magazynu, upewnij się, że jest to [jeden z regionów obsługiwanych przez LFS](storage-files-planning.md#regional-availability).
1. Ustaw replikację na magazyn **lokalnie nadmiarowy** lub do **magazynu Strefowo nadmiarowego**.
1. Pozostaw poniższe pola ustawione na wartości domyślne:

   |Pole  |Wartość  |
   |---------|---------|
   |Model wdrażania     |Resource Manager         |
   |Wydajność     |Standardowa (Standard)         |
   |Rodzaj konta     |StorageV2 (ogólnego przeznaczenia wersja 2)         |
   |Warstwa dostępu     |Gorąca         |

1. Wybierz pozycję **Zaawansowane** i wybierz opcję **włączone** dla **dużych udziałów plików**.
1. Wybierz pozycję **Przejrzyj i utwórz**, aby przejrzeć ustawienia konta magazynu i utworzyć konto.

    ![Large-file-shares-Advanced-Enable. png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Wybierz pozycję **Utwórz**.


### <a name="cli"></a>Interfejs wiersza polecenia

Najpierw upewnij się, że [zainstalowano najnowszą wersję](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), aby można było włączyć tę funkcję.

Aby utworzyć konto magazynu z włączonymi dużymi udziałami plików, Zastąp `<yourStorageAccountName>`, `<yourResourceGroup>`i `<yourDesiredRegion>` wartościami, a następnie użyj następującego polecenia:

```azurecli-interactive
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
az storage account create –name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> –sku Standard_LRS --kind StorageV2 –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Najpierw upewnij się, że [zainstalowano najnowszą wersję](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0), aby można było włączyć tę funkcję.

Aby utworzyć konto magazynu z włączonymi dużymi udziałami plików, Zastąp `<yourStorageAccountName>`, `<yourResourceGroup>`i `<yourDesiredRegion>` wartościami, a następnie użyj następującego polecenia:

```PowerShell
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-on-existing-account"></a>Włącz na istniejącym koncie

### <a name="portal"></a>Portal

Możesz również włączyć duże udziały plików na istniejących kontach. W takim przypadku konto nie będzie już mogło być konwertowane na GZRS, GRS lub RA-GRS. Ten wybór jest nieodwracalny dla tego konta.

1. Otwórz [Azure Portal](https://portal.azure.com) i przejdź do konta magazynu, na którym chcesz włączyć duże udziały plików.
1. Otwórz konto magazynu i wybierz pozycję **Konfiguracja**.
1. Wybierz pozycję **włączone** w przypadku **dużych udziałów plików**, a następnie wybierz pozycję Zapisz.
1. Wybierz pozycję **Przegląd** , a następnie wybierz pozycję **Odśwież**.

![Enable-Large-file-shares-on-Existing. png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Na koncie magazynu są teraz włączone duże udziały plików.

Jeśli zostanie wyświetlony następujący błąd: "duże udziały plików nie są jeszcze dostępne dla konta". Możesz poczekać jakiś czas, ponieważ Twój region jest prawdopodobnie w trakcie kończenia jego wdrożenia lub, jeśli masz pilne potrzeby, skontaktuj się z pomocą techniczną.

### <a name="cli"></a>Interfejs wiersza polecenia

Na istniejących kontach można włączyć duże udziały plików. W takim przypadku konto nie będzie już mogło być konwertowane na GZRS, GRS lub RA-GRS. Ten wybór jest nieodwracalny dla tego konta.

Zastąp `<yourStorageAccountName>` i `<yourResourceGroup>` w poniższym poleceniu, a następnie użyj go, aby włączyć duże udziały plików na istniejącym koncie:

```azurecli-interactive
az storage account update –name <yourStorageAccountName> -g <yourResourceGroup> –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Na istniejących kontach można włączyć duże udziały plików. W takim przypadku konto nie będzie już mogło być konwertowane na GZRS, GRS lub RA-GRS. Ten wybór jest nieodwracalny dla tego konta.

Zastąp `<yourStorageAccountName>` i `<yourResourceGroup>` w poniższym poleceniu, a następnie użyj go, aby włączyć duże udziały plików na istniejącym koncie:

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Tworzenie dużego udziału plików

### <a name="portal"></a>Portal

Tworzenie dużego udziału plików jest niemal identyczne z tworzeniem standardowego udziału plików. Główną różnicą jest to, że można ustawić limit przydziału do 100 TiB.

1. Na koncie magazynu wybierz pozycję **udziały plików**.
1. Wybierz pozycję **+ Udział plików**.
1. Wprowadź nazwę udziału plików i (opcjonalnie) żądany rozmiar przydziału, do 100 TiB, a następnie wybierz pozycję **Utwórz**. 

![Large-file-shares-Create-Share. png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>Interfejs wiersza polecenia

Po włączeniu dużych udziałów plików na koncie magazynu można utworzyć udziały plików na tym koncie z wyższymi przydziałami. W poniższym poleceniu Zastąp wartości `<yourStorageAccountName>`, `<yourStorageAccountKey>`i `<yourFileShareName>`, a następnie możesz użyć jej do utworzenia dużego udziału plików:

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Po włączeniu dużych udziałów plików na koncie magazynu można utworzyć udziały plików na tym koncie z wyższymi przydziałami. W poniższym poleceniu Zastąp wartości `<YourStorageAccountName>`, `<YourStorageAccountKey>`i `<YourStorageAccountFileShareName>`, a następnie możesz użyć jej do utworzenia dużego udziału plików:

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Rozwiń istniejące udziały plików

### <a name="portal"></a>Portal

Po włączeniu dużych udziałów plików na koncie magazynu można rozszerzyć istniejące udziały do wyższego przydziału.

1. Na koncie magazynu wybierz pozycję **udziały plików**.
1. Kliknij prawym przyciskiem myszy udział plików, a następnie wybierz pozycję **przydział**.
1. Wprowadź żądany rozmiar, a następnie wybierz przycisk **OK**.

![Update-Large-File-Share-quota. png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>Interfejs wiersza polecenia

Po włączeniu dużych udziałów plików na koncie magazynu można rozwinąć istniejące udziały plików na tym koncie do wyższych przydziałów. W poniższym poleceniu Zastąp wartości `<yourStorageAccountName>`, `<yourStorageAccountKey>`i `<yourFileShareName>`, a następnie możesz użyć jej do ustawienia maksymalnego limitu przydziału:

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Po włączeniu dużych udziałów plików na koncie magazynu można rozwinąć istniejące udziały plików na tym koncie do wyższych przydziałów. W poniższym poleceniu Zastąp wartości `<YourStorageAccountName>`, `<YourStorageAccountKey>`i `<YourStorageAccountFileShareName>`, a następnie możesz użyć jej do ustawienia maksymalnego limitu przydziału:

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Następne kroki

* [Łączenie i instalowanie udziału plików — system Windows](storage-how-to-use-files-windows.md)
* [Łączenie i instalowanie udziału plików — system Linux](../storage-how-to-use-files-linux.md)
* [Łączenie i instalowanie udziału plików — system macOS](storage-how-to-use-files-mac.md)