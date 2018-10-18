---
title: Tworzenie konta magazynu usługi Azure Data Lake Storage 2. generacji w wersji zapoznawczej | Microsoft Docs
description: Szybko naucz się, jak utworzyć nowe konto magazynu z dostępem do usługi Data Lake Storage 2. generacji w wersji zapoznawczej przy użyciu witryny Azure Portal, programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 06/27/2018
ms.author: jamesbak
ms.openlocfilehash: 4c3354786d7320caee0dfe2f0eca44909d77f2fe
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386855"
---
# <a name="quickstart-create-an-azure-data-lake-storage-gen2-preview-storage-account"></a>Szybki start: tworzenie konta magazynu usługi Azure Data Lake Storage 2. generacji w wersji zapoznawczej

Konta usługi Azure Data Lake Storage 2. generacji w wersji zapoznawczej [obsługują usługę hierarchicznych przestrzeni nazw](introduction.md), która udostępnia natywny system plików oparty na katalogach przystosowany do pracy z rozproszonym systemem plików usługi Hadoop (HDFS, Hadoop Distributed File System). Dostęp do danych usługi Data Lake Storage 2. generacji z systemu plików HDFS można uzyskiwać za pośrednictwem [sterownika ABFS](abfs-driver.md).

Aby włączyć możliwości usługi Data Lake Storage 2. generacji na koncie magazynu, [wypełnij ankietę dotyczącą wersji zapoznawczej, aby zażądać dostępu](https://aka.ms/adlsgen2signup). Po zatwierdzeniu będziesz mieć możliwość utworzenia nowego konta usługi Data Lake Storage 2. generacji. W tym przewodniku Szybki start przedstawiono sposób tworzenia konta przy użyciu [witryny Azure Portal](https://portal.azure.com/) lub [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) albo za pośrednictwem [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

> [!NOTE]
> Interfejs użytkownika tworzenia konta w witrynie Azure Portal jest aktualizowany po zatwierdzeniu możliwości tworzenia konta usługi Data Lake Storage 2. generacji przez użytkownika. W ten sam sposób argumenty programu PowerShell i interfejsu wiersza polecenia powiązane z usługą Data Lake Storage 2. generacji działają tylko po zatwierdzeniu użytkownika do korzystania z wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

|           | Wymagania wstępne |
|-----------|--------------|
|Portal     | Brak         |
|PowerShell | Dla tego przewodnika Szybki start jest wymagany moduł Azure PowerShell w wersji **5.0.4-preview** lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby określić bieżącą wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). |
|Interfejs wiersza polecenia        | Możesz zalogować się do platformy Azure i uruchomić polecenia interfejsu wiersza polecenia platformy Azure w jeden z dwóch sposobów: <ul><li>Polecenia interfejsu wiersza polecenia platformy Azure możesz uruchamiać z poziomu witryny Azure Portal w usłudze Azure Cloud Shell </li><li>Możesz zainstalować interfejs wiersza polecenia i uruchamiać jego polecenia lokalnie</li></ul>|

Podczas pracy w wierszu polecenia masz opcję uruchamiania powłoki chmury platformy Azure lub instalowania interfejsu wiersza polecenia lokalnie.

### <a name="use-azure-cloud-shell"></a>Używanie usługi Azure Cloud Shell

Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Kliknij przycisk **Cloud Shell** w menu w prawym górnym rogu witryny Azure Portal:

[![Cloud Shell](./media/quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Ten przycisk służy do uruchamiania interaktywnej powłoki, której możesz używać do wykonywania kroków opisanych w tym przewodniku Szybki start:

[![Zrzut ekranu przedstawiający okno usługi Cloud Shell w portalu](./media/quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalowanie interfejsu wiersza polecenia lokalnie

Interfejs wiersza polecenia platformy Azure możesz również zainstalować i używać go lokalnie. Ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.38 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="overview-of-creating-an-azure-data-lake-storage-gen2-account"></a>Omówienie tworzenia konta usługi Azure Data Lake Storage 2. generacji

Przed utworzeniem konta musisz najpierw utworzyć grupę zasobów, która działa jako kontener logiczny tworzonych kont magazynu lub innych zasobów platformy Azure. Jeśli chcesz oczyścić zasoby utworzone w tym przewodniku Szybki start, możesz po prostu usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego konta magazynu i wszystkich innych zasobów skojarzonych z tą grupą zasobów. Aby uzyskać więcej informacji dotyczących grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> Aby korzystać z zalet funkcji usługi Data Lake Storage 2. generacji, należy tworzyć nowe konta magazynu jako typ **StorageV2 (ogólnego przeznaczenia w wersji 2)**.  

Aby uzyskać więcej informacji dotyczących kont magazynu, zobacz temat [Azure Storage account overview](../common/storage-account-overview.md) (Omówienie konta usługi Azure Storage).

Podczas określania nazwy konta magazynu należy pamiętać o następujących regułach:

- Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery.
- Nazwa konta magazynu musi być unikatowa w obrębie platformy Azure. Każde konto magazynu musi mieć inną nazwę.

## <a name="create-an-account-using-the-azure-portal"></a>Tworzenie konta przy użyciu witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby utworzyć grupę zasobów w witrynie Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję **Grupy zasobów**.
2. Kliknij przycisk **Dodaj**, aby dodać nową grupę zasobów.
3. Wprowadź nazwę nowej grupy zasobów.
4. Wybierz subskrypcję, w ramach której chcesz utworzyć nową grupę zasobów.
5. Wybierz lokalizację grupy zasobów.
6. Kliknij przycisk **Utwórz**.  

![Zrzut ekranu przedstawiający proces tworzenia grupy zasobów w witrynie Azure Portal](./media/quickstart-create-account/create-resource-group.png)

### <a name="create-a-general-purpose-v2-storage-account"></a>Tworzenie konta magazynu ogólnego przeznaczenia w wersji 2

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 w witrynie Azure Portal, wykonaj następujące kroki:

> [!NOTE]
> Hierarchiczna przestrzeń nazw jest włączona tylko we Wschodnich stanach USA, Wschodnich stanach USA 2, Zachodnich stanach USA, Zachodnich stanach USA 2, Zachodnio-środkowych stanach USA, Europie Północnej, Europie Zachodniej, Azji Południowo-Wschodniej i Australii Wschodniej. Pamiętaj, aby podczas tworzenia konta magazynu wybrać jedną z tych lokalizacji.

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję **Wszystkie usługi**. Następnie przewiń w dół do pozycji **Storage** i wybierz pozycję **Konta magazynu**. W oknie **Konta magazynu**, które zostanie wyświetlone, wybierz pozycję **Dodaj**.
2. Wprowadź nazwę konta magazynu.
3. Pozostaw pole **Model wdrożenia** ustawione na wartość domyślną.
4. Ustaw pole **Rodzaj konta** na wartość **StorageV2 (ogólnego przeznaczenia, wersja 2)**.
5. Ustaw pole **Lokalizacja** na **Zachodnie stany USA 2**.
6. Pozostaw pole **Replikacja** ustawione na wartość **Magazyn lokalnie nadmiarowy (LRS)**.
7. Pozostaw te pola ustawione na wartości domyślne: **Replikacja**, **Wydajność**, **Warstwa dostępu**.
8. Wybierz subskrypcję, w ramach której chcesz utworzyć konto magazynu.
9. Z listy rozwijanej **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz grupę zasobów utworzoną w poprzedniej sekcji.
10. Zachowaj wartość domyślną pola **Sieci wirtualne**.
11. W sekcji **Data Lake Storage 2. generacji (wersja zapoznawcza)** ustaw pole na **Hierarchiczna przestrzeń nazw** na **Włączono**.
12. Kliknij pozycję **Utwórz**, aby utworzyć konto magazynu.

![Zrzut ekranu przedstawiający proces tworzenia konta magazynu w witrynie Azure Portal](./media/quickstart-create-account/azure-data-lake-storage-account-create.png)

Konto magazynu zostało utworzone za pośrednictwem portalu.

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć grupę zasobów za pomocą witryny Azure Portal:

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję **Grupy zasobów**, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk **Więcej** (**...** ) po prawej stronie listy.
3. Wybierz pozycję **Usuń grupę zasobów** i potwierdź.

## <a name="create-an-account-using-powershell"></a>Tworzenie konta przy użyciu programu PowerShell

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Login-AzureRmAccount`, a następnie postępuj zgodnie z instrukcjami wyświetlanymi na ekranie w celu uwierzytelnienia.

```powershell
Login-AzureRmAccount
```

### <a name="upgrade-your-powershell-module"></a>Uaktualnianie modułu PowerShell

Aby zapewnić interakcję z usługą Data Lake Storage 2. generacji za pośrednictwem programu PowerShell, musisz uaktualnić moduł do wersji zapoznawczej.

Aby to zrobić, otwórz program PowerShell z podwyższonym poziomem uprawnień i wprowadź następujące polecenie: `Install-Module AzureRM.Storage –Repository PSGallery -RequiredVersion 5.0.4-preview –AllowPrerelease –AllowClobber –Force `

Następnie ponownie uruchom powłokę.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby utworzyć nową grupę zasobów za pomocą programu PowerShell, użyj polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup): 

> [!NOTE]
> Hierarchiczna przestrzeń nazw jest włączona tylko we Wschodnich stanach USA, Wschodnich stanach USA 2, Zachodnich stanach USA, Zachodnich stanach USA 2, Zachodnio-środkowych stanach USA, Europie Północnej, Europie Zachodniej, Azji Południowo-Wschodniej i Australii Wschodniej. Pamiętaj, aby podczas tworzenia konta magazynu wybrać jedną z tych lokalizacji.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>Tworzenie konta magazynu ogólnego przeznaczenia w wersji 2

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 z poziomu programu PowerShell przy użyciu magazynu lokalnie nadmiarowego (LRS), użyj polecenia [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount):

```powershell
Get-AzureRmLocation | select Location 
$location = "westus2"

New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć grupę zasobów i skojarzone z nią zasoby, w tym nowe konto magazynu, użyj polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup): 

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Tworzenie konta przy użyciu interfejsu wiersza polecenia platformy Azure

Aby uruchomić usługę Azure Cloud Shell, zaloguj się do witryny [Azure Portal](https://portal.azure.com).

Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia, uruchom polecenie logowania:

```cli
az login
```

### <a name="upgrade-your-cli-module"></a>Uaktualnianie modułu interfejsu wiersza polecenia

Aby zapewnić interakcję z usługą Data Lake Storage 2. generacji za pośrednictwem interfejsu wiersza polecenia, musisz dodać rozszerzenie do powłoki.

Aby to zrobić: wprowadź następujące polecenie przy użyciu usługi Cloud Shell lub powłoki lokalnej: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby utworzyć nową grupę zasobów za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenia [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create \
    --name storage-quickstart-resource-group \
    --location westus2
```

> [!NOTE]
> Hierarchiczna przestrzeń nazw jest włączona tylko we Wschodnich stanach USA, Wschodnich stanach USA 2, Zachodnich stanach USA, Zachodnich stanach USA 2, Zachodnio-środkowych stanach USA, Europie Północnej, Europie Zachodniej, Azji Południowo-Wschodniej i Australii Wschodniej. Pamiętaj, aby podczas tworzenia konta magazynu wybrać jedną z tych lokalizacji.

### <a name="create-a-general-purpose-v2-storage-account"></a>Tworzenie konta magazynu ogólnego przeznaczenia w wersji 2

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 z poziomu interfejsu wiersza polecenia platformy Azure przy użyciu magazynu lokalnie nadmiarowego, użyj polecenia [az storage account create](/cli/azure/storage/account#az_storage_account_create).

```azurecli-interactive
az storage account create \
    --name storagequickstart \
    --resource-group storage-quickstart-resource-group \
    --location westus2 \
    --sku Standard_LRS \
    --kind StorageV2 \
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć grupę zasobów i skojarzone z nią zasoby, w tym nowe konto magazynu, użyj polecenia [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zostało utworzone konto magazynu usługi Data Lake Storage 2. generacji. Aby dowiedzieć się, jak przekazywać i pobierać obiekty blob z konta magazynu i do niego, przejdź do przewodnika Szybki start dotyczącego magazynu obiektów blob.

* [Przenoszenie danych do oraz z usługi Azure Blob Storage za pomocą usługi AzCopy](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azcopy)