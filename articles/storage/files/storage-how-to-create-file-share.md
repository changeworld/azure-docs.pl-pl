---
title: Tworzenie udziału plików platformy Azure
titleSuffix: Azure Files
description: Jak utworzyć udział plików platformy Azure przy użyciu witryny Azure portal, powershell lub interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ed6abbac7c5953eaec4fa4584248d0d98b49ba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596920"
---
# <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
Aby utworzyć udział plików platformy Azure, musisz odpowiedzieć na trzy pytania dotyczące sposobu jego użycia:

- **Jakie są wymagania dotyczące wydajności udziału plików platformy Azure?**  
    Usługa Azure Files oferuje standardowe udziały plików, które są hostowane na sprzęcie opartym na dysku twardym (hdd) i udziałach plików premium, które są hostowane na sprzęcie opartym na dysku PÓŁPRZEWODNIKOWYM (SSD).

- **Jakiego rozmiaru pliku potrzebujesz?**  
    Standardowe udziały plików mogą obejmować do 100 TiB, jednak ta funkcja nie jest domyślnie włączona; Jeśli potrzebujesz udziału plików, który jest większy niż 5 TiB, musisz włączyć funkcję dużego udziału plików dla swojego konta magazynu. Udziały plików Premium mogą obejmować do 100 TiB bez specjalnego ustawienia, jednak udziały plików premium są aprowizowane, zamiast płacić jak standardowe udziały plików. Oznacza to, że inicjowanie obsługi administracyjnej udziału plików znacznie większe niż to, czego potrzebujesz, zwiększy całkowity koszt magazynu.

- **Jakie są wymagania dotyczące nadmiarowości dla udziału plików platformy Azure?**  
    Standardowe udziały plików oferują magazynowanie lokalnie nadmiarowe (LRS), nadmiarowe strefy (ZRS), geobekbowane (GRS) lub magazynowanie geograficznie nadmiarowe (GZRS), jednak funkcja dużego udziału plików jest obsługiwana tylko w lokalnie nadmiarowych i nadmiarowych udziałach plików. Udziały plików premium nie obsługują żadnej formy nadmiarowości geograficznej.

    Udziały plików premium są dostępne z nadmiarowością lokalną w większości regionów, które oferują konta magazynu i nadmiarowością stref w mniejszym podzbiorze regionów. Aby dowiedzieć się, czy udziały plików w usłudze Premium są obecnie dostępne w Twoim regionie, zobacz produkty dostępne na stronie [regionu](https://azure.microsoft.com/global-infrastructure/services/?products=storage) na platformie Azure. Aby uzyskać informacje o regionach obsługujących usługę ZRS, zobacz [Nadmiarowość usługi Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Aby uzyskać więcej informacji na temat tych trzech opcji, zobacz [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md).

## <a name="prerequisites"></a>Wymagania wstępne
- W tym artykule przyjęto założenie, że subskrypcja platformy Azure została już utworzona. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jeśli zamierzasz korzystać z programu Azure PowerShell, [zainstaluj najnowszą wersję](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Jeśli zamierzasz korzystać z interfejsu wiersza polecenia platformy Azure, [zainstaluj najnowszą wersję](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Udziały plików platformy Azure są wdrażane na *kontach magazynu,* które są obiektami najwyższego poziomu reprezentującymi współużytkową pulę magazynu. Ta pula magazynu może służyć do wdrażania wielu udziałów plików. 

Platforma Azure obsługuje wiele typów kont magazynu dla różnych scenariuszy magazynu, które klienci mogą mieć, ale istnieją dwa główne typy kont magazynu dla usług Azure Files. Typ konta magazynu, który należy utworzyć, zależy od tego, czy chcesz utworzyć standardowy udział plików, czy udział plików w premium: 

- **Konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2):** konta magazynu GPv2 umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na standardzie/dysku twardym (opartym na dyskach twardych). Oprócz przechowywania udziałów plików platformy Azure konta magazynu GPv2 mogą przechowywać inne zasoby magazynu, takie jak kontenery obiektów blob, kolejki lub tabele. 

- **Konta magazynu FileStorage:** Konta magazynu FileStorage umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dysku premium/półprzewodnikowym (SSD). Konta FileStorage mogą być używane tylko do przechowywania udziałów plików platformy Azure; żadne inne zasoby magazynu (kontenery obiektów blob, kolejki, tabele itp.) nie mogą być wdrażane na koncie FileStorage.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby utworzyć konto magazynu za pośrednictwem witryny Azure portal, wybierz **+ Utwórz zasób** z pulpitu nawigacyjnego. W wynikowym oknie wyszukiwania w portalu Azure Marketplace wyszukaj **konto magazynu** i wybierz wynikowy wynik wyszukiwania. Doprowadzi to do strony przeglądu dla kont magazynu; wybierz **pozycję Utwórz,** aby przejść do kreatora tworzenia konta magazynu.

![Zrzut ekranu przedstawiający opcję szybkiego tworzenia konta magazynu w przeglądarce](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>Sekcja Podstawy
Pierwsza sekcja do ukończenia w celu utworzenia konta magazynu jest oznaczona jako **Podstawy**. Zawiera wszystkie pola wymagane do utworzenia konta magazynu. Aby utworzyć konto pamięci masowej GPv2, upewnij się, że przycisk opcji **Wydajność** jest ustawiony na *Standardowy,* a lista rozwijana **Rodzaj konta** jest wybrana na *StorageV2 (ogólnego przeznaczenia w wersji 2).*

![Zrzut ekranu przedstawiający przycisk opcji Wydajność z wybraną wybraną funkcją Standard i rodzaj konta z wybraną funkcją StorageV2](media/storage-how-to-create-file-share/create-storage-account-1.png)

Aby utworzyć konto magazynu FileStorage, upewnij się, że przycisk opcji **Wydajność** jest ustawiony na *Premium,* a lista rozwijana **Rodzaj konta** jest wybrana na *FileStorage*.

![Zrzut ekranu przedstawiający przycisk opcji Wydajność z wybraną funkcją Premium i rodzaj konta z wybraną funkcją FileStorage](media/storage-how-to-create-file-share/create-storage-account-2.png)

Inne pola podstawy są niezależne od wyboru konta magazynu:
- **Subskrypcja:** subskrypcja dla konta magazynu, które ma zostać wdrożone. 
- **Grupa zasobów:** Grupa zasobów dla konta magazynu, w które ma zostać wdrożone. Można utworzyć nową grupę zasobów lub użyć istniejącej grupy zasobów. Grupa zasobów to logiczny kontener przeznaczony do grupowania usług platformy Azure. Podczas tworzenia konta magazynu masz możliwość utworzenia nowej grupy zasobów lub użycia istniejącej grupy zasobów.
- **Nazwa konta magazynu**: Nazwa zasobu konta magazynu, który ma zostać utworzony. Ta nazwa musi być unikatowa globalnie, ale w przeciwnym razie może dowolną nazwę chcesz. Nazwa konta magazynu będzie używana jako nazwa serwera podczas instalowania udziału plików platformy Azure za pośrednictwem protokołu SMB.
- **Lokalizacja:** Region dla konta magazynu, które ma zostać wdrożone. Może to być region skojarzony z grupą zasobów lub dowolnym innym dostępnym regionem.
- **Replikacja:** Chociaż jest to replikacja oznaczona etykietą, to pole faktycznie oznacza **nadmiarowość;** jest to pożądany poziom nadmiarowości: nadmiarowość lokalna (LRS), nadmiarowość stref (ZRS), nadmiarowość geograficzna (GRS) i nadmiarowość strefy geograficznej. Ta lista rozwijana zawiera również nadmiarowość geograficzną dostępu do odczytu (RA-GRS) i nadmiarowość stref geograficznych dostępu do odczytu (RA-GZRS), które nie mają zastosowania do udziałów plików platformy Azure; każdy udział plików utworzony na koncie magazynu z tymi wybranymi będzie w rzeczywistości odpowiednio geograficznie nadmiarowy lub geo-strefowy. W zależności od regionu lub wybranego typu konta magazynu niektóre opcje nadmiarowości mogą być niedozwolone.
- **Warstwa dostępu:** to pole nie ma zastosowania do usługi Azure Files, więc można wybrać jeden z przycisków radiowych.

#### <a name="the-networking-blade"></a>Ostrze sieciowe
Sekcja sieciowa umożliwia skonfigurowanie opcji sieciowych. Te ustawienia są opcjonalne do tworzenia konta magazynu i mogą być konfigurowane później w razie potrzeby. Aby uzyskać więcej informacji na temat tych opcji, zobacz [Zagadnienia dotyczące sieci usług Azure Files](storage-files-networking-overview.md).

#### <a name="the-advanced-blade"></a>Zaawansowane ostrze
Sekcja zaawansowana zawiera kilka ważnych ustawień dla udziałów plików platformy Azure:

- **Wymagany bezpieczny transfer:** To pole wskazuje, czy konto magazynu wymaga szyfrowania podczas przesyłania w celu komunikacji z kontem magazynu. Zaleca się, że jest to włączone, jednak jeśli potrzebujesz obsługi SMB 2.1, należy to wyłączyć. Zalecamy wyłączenie szyfrowania, które ogranicza dostęp konta magazynu do sieci wirtualnej z punktami końcowymi usługi i/lub prywatnymi punktami końcowymi.
- **Duże udziały plików:** To pole umożliwia konto magazynu dla udziałów plików obejmujących do 100 TiB. Włączenie tej funkcji ograniczy konto magazynu tylko do opcji magazynu nadmiarowego lokalnie i nadmiarowego strefy. Po włączeniu konta magazynu GPv2 dla dużych udziałów plików nie można wyłączyć możliwości dużego udziału plików. Konta magazynu FileStorage (konta magazynu dla udziałów plików premium) nie mają tej opcji, ponieważ wszystkie udziały plików premium mogą być skalowane do 100 TiB. 

![Zrzut ekranu przedstawiający ważne ustawienia zaawansowane dotyczące usług Azure Files](media/storage-how-to-create-file-share/create-storage-account-3.png)

Inne ustawienia, które są dostępne na karcie zaawansowane (usunięcie programowe obiektu blob, hierarchiczny obszar nazw dla usługi Azure Data Lake storage gen 2 i NFSv3 dla magazynu obiektów blob) nie mają zastosowania do usługi Azure Files.

#### <a name="tags"></a>Tagi
Tagi to pary nazw i wartości, które umożliwiają kategoryzowanie zasobów i wyświetlanie skonsolidowanych rozliczeń przez zastosowanie tego samego tagu do wielu zasobów i grup zasobów. Są one opcjonalne i mogą być stosowane po utworzeniu konta magazynu.

#### <a name="review--create"></a>Recenzja + tworzenie
Ostatnim krokiem do utworzenia konta magazynu jest wybranie przycisku **Utwórz** na karcie **Recenzja + utwórz.** Ten przycisk nie będzie dostępny, jeśli wszystkie wymagane pola dla konta magazynu nie zostaną wypełnione.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Aby utworzyć konto magazynu przy użyciu programu `New-AzStorageAccount` PowerShell, użyjemy polecenia cmdlet. To polecenie cmdlet ma wiele opcji; wyświetlane są tylko wymagane opcje. Aby dowiedzieć się więcej o zaawansowanych opcjach, zobacz [ `New-AzStorageAccount` dokumentację polecenia cmdlet](/powershell/module/az.storage/new-azstorageaccount).

Aby uprościć tworzenie konta magazynu i kolejny udział plików, będziemy przechowywać kilka parametrów w zmiennych. Zawartość zmiennej można zastąpić dowolną wartością, jednak należy pamiętać, że nazwa konta magazynu musi być unikatowa globalnie.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Aby utworzyć konto magazynu zdolne do przechowywania standardowych udziałów plików platformy Azure, użyjemy następującego polecenia. Parametr `-SkuName` odnosi się do rodzaju redundancji pożądane; Jeśli chcesz geo-nadmiarowe lub geo-zone-nadmiarowe konto magazynu, należy również usunąć `-EnableLargeFileShare` parametr.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Aby utworzyć konto magazynu zdolne do przechowywania udziałów plików platformy Azure w wersji premium, użyjemy następującego polecenia. Należy zauważyć, że `-SkuName` parametr `Premium` został zmieniony tak, aby uwzględnić`LRS`zarówno żądany poziom nadmiarowości lokalnie nadmiarowy ( ). Parametr `-Kind` jest `FileStorage` zamiast `StorageV2` dlatego, że udziały plików w premii muszą być tworzone na koncie magazynu FileStorage zamiast konta magazynu GPv2.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby utworzyć konto magazynu przy użyciu interfejsu wiersza polecenia platformy Azure, użyjemy polecenia tworzenia konta magazynu az. To polecenie ma wiele opcji; wyświetlane są tylko wymagane opcje. Aby dowiedzieć się więcej o zaawansowanych opcjach, zapoznaj się z [ `az storage account create` dokumentacją polecenia](/cli/azure/storage/account).

Aby uprościć tworzenie konta magazynu i kolejny udział plików, będziemy przechowywać kilka parametrów w zmiennych. Zawartość zmiennej można zastąpić dowolną wartością, jednak należy pamiętać, że nazwa konta magazynu musi być unikatowa globalnie.

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Aby utworzyć konto magazynu zdolne do przechowywania standardowych udziałów plików platformy Azure, użyjemy następującego polecenia. Parametr `--sku` odnosi się do rodzaju redundancji pożądane; Jeśli chcesz geo-nadmiarowe lub geo-zone-nadmiarowe konto magazynu, należy również usunąć `--enable-large-file-share` parametr.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Aby utworzyć konto magazynu zdolne do przechowywania udziałów plików platformy Azure w wersji premium, użyjemy następującego polecenia. Należy zauważyć, że `--sku` parametr `Premium` został zmieniony tak, aby uwzględnić`LRS`zarówno żądany poziom nadmiarowości lokalnie nadmiarowy ( ). Parametr `--kind` jest `FileStorage` zamiast `StorageV2` dlatego, że udziały plików w premii muszą być tworzone na koncie magazynu FileStorage zamiast konta magazynu GPv2.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Tworzenie udziału plików
Po utworzeniu konta magazynu pozostaje tylko utworzyć udział plików. Ten proces jest w większości taki sam, niezależnie od tego, czy używasz udziału plików premium, czy standardowego udziału plików. Podstawową różnicą jest **przydział** i to, co reprezentuje.

W przypadku standardowych udziałów plików jest to górna granica udziału plików platformy Azure, po przekroju którego użytkownicy końcowi nie mogą przejść. Głównym celem kontyngentu dla standardowego udziału plików jest budżet: "Nie chcę, aby ten udział plików rósł poza ten punkt". Jeśli przydział nie jest określony, standardowy udział plików może obejmować maksymalnie 100 TiB (lub 5 TiB, jeśli właściwość dużych udziałów plików nie jest ustawiona dla konta magazynu).

W przypadku udziałów plików premium przydział jest przeciążony, aby oznaczać **aprowizowany rozmiar**. Aprowizowany rozmiar to kwota, za którą zostanie naliczona kwota, niezależnie od rzeczywistego użycia. Podczas aprowizowania udziału plików w usłudze premium należy wziąć pod uwagę dwa czynniki: 1) przyszły wzrost udziału z perspektywy wykorzystania miejsca i 2) usługi We/Wy wymagane dla obciążenia. Każdy aprowizowany GiB uprawnia cię do dodatkowych zastrzeżonych i pęknięć we/wy. Aby uzyskać więcej informacji na temat planowania udziału plików premium, zobacz [inicjowanie obsługi administracyjnej udziałów plików premium](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Jeśli właśnie utworzono konto magazynu, można przejść do niego z ekranu wdrażania, wybierając **pozycję Przejdź do zasobu**. Jeśli konto magazynu zostało wcześniej utworzone, można przejść do niego za pośrednictwem zawierającej go grupy zasobów. Po wejściu na konto magazynu wybierz kafelek o nazwie **Udziały plików** (możesz również przejść do **pozycji Udziały plików** za pośrednictwem spisu treści dla konta magazynu).

![Zrzut ekranu przedstawiający kafelek Udziały plików](media/storage-how-to-create-file-share/create-file-share-1.png)

Na liście udziału plików powinny być widoczne wszystkie udziały plików utworzone wcześniej na tym koncie magazynu; pustą tabelę, jeśli nie utworzono jeszcze żadnych udziałów plików. Wybierz **+ Udział plików,** aby utworzyć nowy udział plików.

Na ekranie powinien pojawić się nowy blok udziału plików. Wypełnij pola w nowym bloku udziału plików, aby utworzyć udział plików:

- **Nazwa**: nazwa udziału plików, który ma zostać utworzony.
- **Przydział**: Przydział udziału plików dla standardowych udziałów plików; aprowizowanego rozmiaru udziału plików dla udziałów plików premium.

Wybierz **pozycję Utwórz,** aby zakończyć tworzenie nowego udziału. Należy zauważyć, że jeśli konto magazynu znajduje się w sieci wirtualnej, nie będzie można pomyślnie utworzyć udziału plików platformy Azure, chyba że klient jest również w sieci wirtualnej. Można również obejść to ograniczenie punktu w czasie przy `New-AzRmStorageShare` użyciu polecenia cmdlet programu Azure PowerShell.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Można utworzyć udział pliku platformy [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) Azure za pomocą polecenia cmdlet. Następujące polecenia programu PowerShell zakładają, `$resourceGroupName` że `$storageAccountName` ustawiono zmienne i zgodnie z powyższym zdefiniowane w tworzeniu konta magazynu z sekcją Programu Azure PowerShell. 

> [!Important]  
> W przypadku udziałów `-QuotaGiB` plików w usłudze Premium parametr odnosi się do aprowizowanego rozmiaru udziału plików. Aprowizowany rozmiar udziału plików jest kwotą, za którą zostanie naliczona kwota, niezależnie od użycia. Standardowe udziały plików są rozliczane na podstawie użycia, a nie aprowizowanego rozmiaru.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> Nazwa udziału plików musi się składać z samych małych liter. Aby uzyskać szczegółowe informacje na temat nazewnictwa udziałów i plików plików, zobacz [Nazywanie i odwoływanie się do udziałów, katalogów, plików i metadanych](https://msdn.microsoft.com/library/azure/dn167011.aspx).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Zanim będziemy mogli utworzyć udział plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure, należy uzyskać klucz konta magazynu, aby autoryzować operację tworzenia udziału plików. Można to zrobić [`az storage account keys list`](/cli/azure/storage/account/keys) za pomocą polecenia:

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

Po uzyskaniu klucza konta magazynu można utworzyć udział [`az storage share create`](/cli/azure/storage/share) plików platformy Azure za pomocą polecenia. 

> [!Important]  
> W przypadku udziałów `--quota` plików w usłudze Premium parametr odnosi się do aprowizowanego rozmiaru udziału plików. Aprowizowany rozmiar udziału plików jest kwotą, za którą zostanie naliczona kwota, niezależnie od użycia. Standardowe udziały plików są rozliczane na podstawie użycia, a nie aprowizowanego rozmiaru.

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

To polecenie zakończy się niepowodzeniem, jeśli konto magazynu znajduje się w sieci wirtualnej, a komputer, z którego wywołuje to polecenie, nie jest częścią sieci wirtualnej. Można obejść to ograniczenie punktu w czasie przy użyciu `New-AzRmStorageShare` polecenia cmdlet programu Azure PowerShell, jak opisano powyżej lub wykonując interfejs wiersza polecenia platformy Azure z komputera, który jest częścią sieci wirtualnej, w tym za pośrednictwem połączenia sieci VPN.

---

> [!Note]  
> Nazwa udziału plików musi się składać z samych małych liter. Aby uzyskać szczegółowe informacje na temat nazewnictwa udziałów i plików plików, zobacz [Nazywanie i odwoływanie się do udziałów, katalogów, plików i metadanych](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="next-steps"></a>Następne kroki
- [Zaplanuj wdrożenie plików platformy Azure](storage-files-planning.md) lub planu wdrożenia usługi Azure File [Sync](storage-sync-files-planning.md). 
- [Omówienie sieci](storage-files-networking-overview.md).
- Połącz i zamontuj udział plików w systemach [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)i [Linux](storage-how-to-use-files-linux.md).