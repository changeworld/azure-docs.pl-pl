---
title: Rozpoczęcie pracy z programem PowerShell dla usługi Azure Batch | Microsoft Docs
description: Krótkie wprowadzenie do poleceń cmdlet programu Azure PowerShell, których można użyć do zarządzania zasobami usługi Batch.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: f9ad62c5-27bf-4e6b-a5bf-c5f5914e6199
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d263dbb89b10876c3a1deb330f969c598addd6a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Zarządzanie zasobami usługi Batch za pomocą poleceń cmdlet programu PowerShell

Za pomocą poleceń cmdlet PowerShell usługi Azure Batch można wykonywać oraz tworzyć skrypty dla wielu tych samych zadań, które wykonuje się za pomocą interfejsów API usługi Batch, witryny Azure Portal oraz interfejsu wiersza polecenia Azure (CLI). Oto krótkie wprowadzenie do poleceń cmdlet, których można używać do zarządzania kontami usługi Batch oraz pracy z zasobami usługi Batch, np. pulami i zadaniami.

Pełna lista poleceń cmdlet w usłudze Batch oraz szczegółowa składnia poleceń cmdlet znajdują się w [dokumentacji dotyczącej poleceń cmdlet w usłudze Azure Batch](/powershell/module/azurerm.batch/#batch).

Informacje w tym artykule dotyczą poleceń cmdlet programu Azure PowerShell w wersji 3.0.0. Zaleca się częstą aktualizację programu Azure PowerShell, aby mieć możliwość korzystania z aktualizacji i rozszerzeń usługi.

## <a name="prerequisites"></a>Wymagania wstępne
Wykonaj poniższe operacje, aby używać programu Azure PowerShell do zarządzania zasobami usługi Batch.

* [Zainstaluj i skonfiguruj program Azure PowerShell](/powershell/azure/overview)
* Uruchom polecenie cmdlet **Connect-AzureRmAccount**, aby połączyć się z subskrypcją (polecenia cmdlet usługi Azure Batch są dostarczane w module usługi Azure Resource Manager):
  
    `Connect-AzureRmAccount`
* **Zarejestruj się w przestrzeni nazw dostawcy usługi Batch**. Tę operację należy wykonać tylko **raz w całym okresie obowiązywania subskrypcji**.
  
    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Zarządzanie kontami i kluczami usługi Batch
### <a name="create-a-batch-account"></a>Tworzenie konta usługi Batch
Polecenie **New-AzureRmBatchAccount** umożliwia utworzenie konta usługi Batch w określonej grupie zasobów. Jeśli nie masz jeszcze grupy zasobów, utwórz ją, uruchamiając polecenie cmdlet [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). W parametrze **Location** określ jeden z regionów świadczenia usługi Azure, na przykład „Środkowe stany USA”. Na przykład:

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

Następnie utwórz konto usługi Batch w grupie zasobów, określając nazwę konta w parametrze <*account_name*> i lokalizację oraz nazwę grupy zasobów. Tworzenie konta usługi Batch może zająć nieco czasu. Na przykład:

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [!NOTE]
> Nazwa konta usługi Batch musi być unikatowa dla regionu Azure dla grupy zasobów, zawierać od 3 do 24 znaków i tylko małe litery i cyfry.
> 
> 

### <a name="get-account-access-keys"></a>Pobieranie kluczy dostępu do konta
Polecenie **Get-AzureRmBatchAccountKeys** umożliwia wyświetlenie kluczy dostępu powiązanych z kontem usługi Azure Batch. Na przykład uruchom następujące polecenia, aby pobrać klucz podstawowy i klucz pomocniczy do utworzonego konta.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Generowanie nowego klucza dostępu
Polecenie **New-AzureRmBatchAccountKey** umożliwia generowanie nowego klucza podstawowego lub klucza pomocniczego do konta usługi Azure Batch. Na przykład, aby wygenerować nowy klucz podstawowy do konta usługi Batch, wpisz:

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [!NOTE]
> Aby wygenerować nowy klucz pomocniczy, wpisz „Secondary” dla parametru **KeyType**. Ponowne generowanie klucza podstawowego i klucza pomocniczego należy wykonywać oddzielnie.
> 
> 

### <a name="delete-a-batch-account"></a>Usuwanie konta usługi Batch
Polecenie **Remove-AzureRmBatchAccount** umożliwia usunięcie konta usługi Batch. Na przykład:

    Remove-AzureRmBatchAccount -AccountName <account_name>

Po wyświetleniu monitu potwierdź, że chcesz usunąć konto. Usunięcie konta może potrwać trochę czasu.

## <a name="create-a-batchaccountcontext-object"></a>Tworzenie obiektu BatchAccountContext
Aby uwierzytelniać się przy użyciu poleceń cmdlet programu PowerShell w usłudze Batch podczas tworzenia pul, zadań, podzadań oraz innych zasobów w usłudze Batch oraz zarządzania nimi, najpierw utwórz obiekt BatchAccountContext, aby przechować nazwę konta i klucze do konta:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Obiekt BatchAccountContext zostaje przeniesiony do poleceń cmdlet, które używają parametru **BatchContext**.

> [!NOTE]
> Domyślnie do uwierzytelniania używany jest klucz podstawowy do konta, ale można jawnie wybrać klucz do użycia przez zmianę właściwości obiektu BatchAccountContext **KeyInUse**: `$context.KeyInUse = "Secondary"`.
> 
> 

## <a name="create-and-modify-batch-resources"></a>Tworzenie i modyfikowanie zasobów usługi Batch
Do tworzenia zasobów w ramach konta usługi Batch służą takie polecenia cmdlet jak **New-AzureBatchPool**, **New-AzureBatchJob** oraz **New-AzureBatchTask**. Istnieją odpowiednie polecenia cmdlet **Get-** i **Set-** do aktualizacji właściwości istniejących zasobów oraz polecenia cmdlet **Remove-** do usuwania zasobów w ramach konta usługi Batch.

Podczas korzystania z wielu tych poleceń cmdlet oprócz przekazywania obiektu BatchContext należy utworzyć lub przekazać obiekty, które zawierają szczegółowe ustawienia zasobów, jak pokazano w poniższym przykładzie. Dodatkowe przykłady zamieszczono w szczegółowych plikach pomocy każdego polecenia cmdlet.

### <a name="create-a-batch-pool"></a>Tworzenie puli usługi Batch
Podczas tworzenia lub aktualizowania puli usługi Batch należy wybrać konfigurację usługi w chmurze lub konfigurację maszyny wirtualnej dla systemu operacyjnego węzłów obliczeniowych — zobacz artykuł [Batch feature overview (Omówienie funkcji usługi Batch)](batch-api-basics.md#pool). Jeśli wybierzesz konfigurację usługi w chmurze, węzły obliczeniowe będą obrazami z jednej z [wersji systemu operacyjnego gościa platformy Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases). Jeśli wybierzesz konfigurację maszyny wirtualnej, możesz określić jeden z obsługiwanych obrazów maszyn wirtualnych z systemem Linux lub Windows wymienionych w witrynie [Azure Virtual Machines Marketplace][vm_marketplace] lub udostępnić samodzielnie przygotowany obraz niestandardowy.

Po uruchomieniu polecenia **New-AzureBatchPool** należy przekazać ustawienia systemu operacyjnego w obiekcie PSCloudServiceConfiguration lub PSVirtualMachineConfiguration. Na przykład poniższe polecenie cmdlet tworzy nową pulę usługi Batch z małymi węzłami obliczeniowymi w konfiguracji usługi w chmurze i obrazami najnowszej wersji systemu operacyjnego z rodziny 3 (Windows Server 2012). W tym miejscu parametr **CloudServiceConfiguration** określa zmienną *$configuration* jako obiekt PSCloudServiceConfiguration. Parametr **BatchContext** określa uprzednio zdefiniowaną zmienną *$context* jako obiekt BatchAccountContext.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

Docelowa liczba węzłów obliczeniowych w nowej puli jest określana przez formułę skalowania automatycznego. W takim przypadku formuła wygląda po prostu w taki sposób — **$TargetDedicated=4**, co oznacza, że liczba węzłów obliczeniowych w puli nie przekracza 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Zapytania dotyczące puli, zadań, podzadań oraz innych szczegółów
Takie polecenia cmdlet jak **Get-AzureBatchPool**, **Get-AzureBatchJob** oraz **Get-AzureBatchTask** służą do przesyłania zapytań dotyczących jednostek utworzonych w ramach konta usługi Batch.

### <a name="query-for-data"></a>Zapytania dotyczące danych
Przykładowo polecenie **Get-AzureBatchPools** służy do znajdowania pul. Domyślnie umożliwia to przesłanie zapytań dotyczących wszystkich pul w ramach konta, zakładając, że obiekt BatchAccountContext został już zapisany w zmiennej *$context*:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Korzystanie z filtru OData
Można skonfigurować filtr OData przy użyciu parametru **Filtr** w taki sposób, by znajdowane były tylko obiekty, które interesują użytkownika. Np. można znaleźć wszystkie pule z identyfikatorami zaczynającymi się od „myPool”:

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Ta metoda nie jest tak elastyczna jak w przypadku korzystania z parametru „Where-Object” w lokalnym potoku. Jednak zapytanie zostaje przesłane bezpośrednio do usługi Batch, więc całe filtrowanie odbywa się po stronie serwera, co pozwala na oszczędność przepustowości internetowej.

### <a name="use-the-id-parameter"></a>Korzystanie z parametru Id
Alternatywą dla filtru OData jest użycie parametru **Id**. Aby przesłać zapytanie dotyczące określonej puli o identyfikatorze „myPool”:

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

Parametr **Id** obsługuje tylko wyszukiwanie pełnych identyfikatorów, a nie symboli wieloznacznych czy filtrów typu OData.

### <a name="use-the-maxcount-parameter"></a>Korzystanie z parametru MaxCount
Domyślnie każde polecenie cmdlet zwraca maksymalnie 1000 obiektów. W przypadku osiągnięcia tego limitu zmień ustawienia filtru w taki sposób, aby zwracał mniej obiektów, lub jawnie ustaw wartość maksymalną przy użyciu parametru **MaxCount** (Maksymalna liczba). Na przykład:

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Aby usunąć górną granicę, ustaw parametr **MaxCount** na wartość 0 lub mniejszą.

### <a name="use-the-powershell-pipeline"></a>Korzystanie z potoku programu PowerShell
Polecenia cmdlet usługi Batch mogą użyć potoku programu PowerShell do przesyłania danych między poleceniami cmdlet. Powoduje to taki sam skutek co określenie parametru, ale sprawia, że praca z wieloma jednostkami jest łatwiejsza.

Na przykład znalezienie i wyświetlenie wszystkich zadań na Twoim koncie:

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Ponowne uruchomienie (ponowny rozruch) każdego węzła obliczeniowego w puli:

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Zarządzanie pakietem aplikacji
Pakiety aplikacji zapewniają uproszczony sposób na wdrażanie aplikacji do węzłów obliczeniowych w pulach. Przy użyciu poleceń cmdlet programu PowerShell usługi Batch możesz przekazywać pakiety aplikacji na swoim koncie usługi Batch i zarządzać nimi oraz wdrażać wersje pakietów do węzłów obliczeniowych.

**Tworzenie** aplikacji:

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Dodawanie** pakietu aplikacji:

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Ustaw **wersję domyślną** aplikacji:

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Wyświetlanie listy** pakietów aplikacji

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Usuwanie** pakietu aplikacji

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Usuwanie** aplikacji

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

> [!NOTE]
> Przed usunięciem aplikacji musisz usunąć wszystkie wersje pakietu aplikacji. Jeśli spróbujesz usunąć aplikację, która zawiera obecnie pakiety aplikacji, zostanie wyświetlony komunikat o błędzie „Konflikt”.
> 
> 

### <a name="deploy-an-application-package"></a>Wdrażanie pakietu aplikacji
Podczas tworzenia puli możesz określić co najmniej jeden pakiet aplikacji dla wdrożenia. Jeśli określisz pakiet w czasie tworzenia puli, zostanie wdrożony w każdym węźle w przypadku dołączenia węzła do puli. Pakiety są też wdrażane, gdy węzeł zostaje uruchomiony ponownie lub odtworzony z obrazu.

Określ opcję `-ApplicationPackageReference` podczas tworzenia puli, aby wdrożyć pakiet aplikacji do węzłów dołączanych do puli. Najpierw utwórz obiekt **PSApplicationPackageReference** i skonfiguruj go, używając identyfikatora aplikacji i wersji pakietu, który chcesz wdrożyć do węzłów obliczeniowych puli:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Teraz utwórz pulę i określ obiekt odwołania do pakietu jako argument opcji `ApplicationPackageReferences`:

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Więcej informacji dotyczących pakietów aplikacji można znaleźć w temacie [Deploy applications to compute nodes with Batch application packages (Wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji usługi Batch)](batch-application-packages.md).

> [!IMPORTANT]
> Najpierw [połącz konto usługi Azure Storage](#linked-storage-account-autostorage) z kontem usługi Batch, aby użyć pakietów aplikacji.
> 
> 

### <a name="update-a-pools-application-packages"></a>Aktualizowanie pakietów aplikacji puli
Aby zaktualizować aplikacje przypisane do istniejącej puli, najpierw utwórz obiekt PSApplicationPackageReference z żądanymi właściwościami (identyfikatorem aplikacji oraz wersją pakietu):

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

Następnie pobierz pulę z usługi Batch, wyczyść wszystkie istniejące pakiety, dodaj nasze nowe odwołanie do pakietu i zaktualizuj usługę Batch przy użyciu nowych ustawień puli:

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

Właściwości puli w usłudze Batch zostały zaktualizowane. Jednak aby rzeczywiście wdrożyć nowy pakiet aplikacji do węzłów obliczeniowych w puli, musisz uruchomić ponownie te węzły lub odtworzyć je z obrazu. Każdy węzeł w puli możesz uruchomić ponownie za pomocą tego polecenia:

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

> [!TIP]
> Do węzłów obliczeniowych w puli możesz wdrożyć wiele pakietów aplikacji. Jeśli chcesz *dodać* pakiet aplikacji zamiast zastępowania aktualnie wdrożonych pakietów, pomiń wiersz `$pool.ApplicationPackageReferences.Clear()` powyżej.
> 
> 

## <a name="next-steps"></a>Następne kroki
* Szczegóły składni poleceń cmdlet oraz przykłady znajdują się w [dokumentacji dotyczącej poleceń cmdlet w usłudze Azure Batch](/powershell/module/azurerm.batch/#batch).
* Aby uzyskać więcej informacji dotyczących aplikacji i pakietów aplikacji w usłudze Batch, zobacz temat [Deploy applications to compute nodes with Batch application packages (Wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji usługi Batch)](batch-application-packages.md).

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
