---
title: Jak usunąć i wyeksportować dane osobowe z usługi Azure DevTest Labs
description: Dowiedz się, jak usuwać i eksportować dane osobowe z usługi Azure DevLast Labs w celu obsługi zobowiązań wynikających z ogólnego rozporządzenia o ochronie danych (RODO).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: c87e2fb534480bbf9bbe625d67782e5a11eda18c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169698"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Eksportowanie lub usuwanie danych osobowych z usługi Azure DevTest Labs
Ten artykuł zawiera kroki usuwania i eksportowania danych osobowych z usługi Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Jakie dane osobowe zbiera DevTest Labs?
DevTest Labs zbiera dwa główne dane osobowe od użytkownika. Są to: adres e-mail użytkownika i identyfikator obiektu użytkownika. Te informacje mają kluczowe znaczenie dla usługi, aby zapewnić funkcje w usłudze administratorom laboratorium i użytkownikom laboratorium.

### <a name="user-email-address"></a>Adres e-mail użytkownika
DevTest Labs używa adresu e-mail użytkownika do wysyłania powiadomień e-mail automatycznego zamykania do użytkowników laboratorium. Wiadomość e-mail powiadamia użytkowników o wyłączeniu komputera. Użytkownicy mogą odroczyć lub pominąć zamknięcie systemu, jeśli chcą to zrobić. Skonfiguruj adres e-mail na poziomie laboratorium lub na poziomie maszyny Wirtualnej.

**Ustawianie poczty e-mail w laboratorium:**

![Ustawianie poczty e-mail na poziomie laboratorium](./media/personal-data-delete-export/lab-user-email.png)

**Ustawianie wiadomości e-mail na maszynie Wirtualnej:**

![Ustawianie poczty e-mail na poziomie maszyny Wirtualnej](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>Identyfikator obiektu użytkownika
DevTest Labs używa identyfikatora obiektu użytkownika, aby pokazać trendy kosztów miesiąc do miesiąca i informacje o kosztach według zasobów administratorom laboratorium. Pozwala im śledzić koszty i zarządzać progami dla swojego laboratorium. 

**Szacowany trend kosztów dla bieżącego miesiąca kalendarzowego:**
![szacowany trend kosztów dla bieżącego miesiąca kalendarzowego](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Szacowany koszt od początku miesiąca według zasobu:**
![szacowany koszt od początku miesiąca według zasobu](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Dlaczego potrzebujemy tych danych osobowych?
Usługa DevTest Labs wykorzystuje dane osobowe do celów operacyjnych. Te dane mają kluczowe znaczenie dla dostarczania przez usługę kluczowych funkcji. Jeśli ustawisz zasady przechowywania na adres e-mail użytkownika, użytkownicy laboratorium nie otrzymają terminowych powiadomień e-mail o automatycznym zamykaniu po usunięciu ich adresu e-mail z naszego systemu. Podobnie administrator laboratorium nie może wyświetlać trendów kosztów miesiąc do miesiąca i kosztów według zasobów dla komputerów w swoich laboratoriach, jeśli identyfikatory obiektów użytkownika są usuwane na podstawie zasad przechowywania. W związku z tym te dane muszą być przechowywane tak długo, jak zasób użytkownika jest aktywny w laboratorium.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Jak mogę mieć system, aby zapomnieć o moich danych osobowych?
Jako użytkownik laboratorium, jeśli chcesz usunąć te dane osobowe, możesz to zrobić, usuwając odpowiedni zasób w laboratorium. Usługa DevTest Labs anonimizuje usunięte dane osobowe 30 dni po ich usunięciu przez użytkownika.

Na przykład Jeśli usuniesz maszynę wirtualną lub usuniesz swój adres e-mail, usługa DevTest Labs anonimizuje te dane 30 dni po usunięciu zasobu. 30-dniowe zasady przechowywania po usunięciu jest upewnienie się, że zapewniamy dokładne miesiąc do miesiąca prognozy kosztów do administratora laboratorium.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Jak mogę poprosić o eksport moich danych osobowych?
Dane użycia osobistego i laboratorium można wyeksportować przy użyciu witryny Azure portal lub programu PowerShell. Dane są eksportowane jako dwa różne pliki CSV:

- **disks.csv** - zawiera informacje o dyskach używanych przez różne maszyny wirtualne
- **virtualmachines.csv** - zawiera informacje o maszynach wirtualnych w laboratorium.

### <a name="azure-portal"></a>Portal Azure
Jako użytkownik laboratorium możesz zażądać eksportu danych osobowych, które przechowuje usługa DevTest Labs. Aby poprosić o eksport, przejdź do opcji **Dane osobowe** na stronie **Przegląd** laboratorium. Wybierz przycisk **Poproś o eksport** rozpoczyna tworzenie pliku excel do pobrania na koncie magazynu administratora laboratorium. Następnie możesz skontaktować się z administratorem laboratorium, aby wyświetlić te dane.

1. Wybierz **pozycję Dane osobowe** w menu po lewej stronie. 

    ![Strona danych osobowych](./media/personal-data-delete-export/personal-data-page.png)
2. Wybierz **grupę zasobów** zawierającą laboratorium.

    ![Wybieranie grupy zasobów](./media/personal-data-delete-export/select-resource-group.png)
3. Wybierz **konto magazynu** w grupie zasobów.
4. Na stronie **Konto magazynu** wybierz pozycję **Obiekty blob**.

    ![Wybieranie kafelka obiektów blob](./media/personal-data-delete-export/select-blobs-tile.png)
5. Wybierz kontener o nazwie **labresourceusage** na liście kontenerów.

    ![Wybierz kontener obiektów blob](./media/personal-data-delete-export/select-blob-container.png)
6. Wybierz **folder** nazwany po laboratorium. Pliki **csv** dla **dysków** i maszyn **wirtualnych** można znaleźć w laboratorium w tym folderze. Możesz pobrać te pliki csv, filtrować zawartość dla użytkownika laboratorium żądającego dostępu i udostępniać je im.

    ![Pobierz plik CSV](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

Kluczowymi składnikami powyższej próbki są:

- Polecenie Invoke-AzureRmResourceAction.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Dwa parametry akcji
    - **blobStorageAbsoluteSasUri** — identyfikator URI konta magazynu z tokenem sygnatury dostępu współdzielonego (SAS). W skrypcie programu PowerShell ta wartość może być przekazywana zamiast klucza magazynu.
    - **usageStartDate** — data początkowa ściągania danych, przy czym datą zakończenia jest bieżąca data wykonania akcji. Ziarnistość jest na poziomie dnia, więc nawet jeśli dodasz informacje o czasie, zostanie on zignorowany.

### <a name="exported-data---a-closer-look"></a>Eksportowane dane - bliższe spojrzenie
Teraz przyjrzyjmy się bliżej wyeksportowanym danym. Jak wspomniano wcześniej, po pomyślnym wyeksportowanymi danych pojawią się dwa pliki CSV. 

**Virtualmachines.csv** zawiera następujące kolumny danych:

| Nazwa kolumny | Opis |
| ----------- | ----------- | 
| SubscriptionId | Identyfikator subskrypcji, w których istnieje laboratorium. |
| LabUId ( LabUId ) | Unikatowy identyfikator GUID dla laboratorium. |
| Nazwa laboratorium | Nazwa laboratorium. |
| LabResourceId (LabResourceId) | W pełni kwalifikowany identyfikator zasobu laboratorium. |
| ResourceGroupName | Nazwa grupy zasobów zawierającej maszynę wirtualną | 
| ResourceId | W pełni kwalifikowany identyfikator zasobu dla maszyny Wirtualnej. |
| Identyfikator zasobu | Identyfikator GUID dla maszyny Wirtualnej |
| Nazwa | Nazwa maszyny wirtualnej. |
| CreatedTime | Data i godzina utworzenia maszyny Wirtualnej. |
| Data usunięta | Data i godzina, w której maszyna wirtualna została usunięta. Jeśli jest pusta, usunięcie nie nastąpiło, jeszcze. |
| Właściciel zasobów | Właściciel maszyny Wirtualnej. Jeśli wartość jest pusta, jest to maszyna wirtualna z roszczeniem lub utworzona przez jednostkę usługi. |
| CennikTier | Warstwa cenowa maszyny Wirtualnej |
| Stan zasobów | Stan dostępności maszyny Wirtualnej. Aktywne, jeśli nadal istnieje lub nieaktywne, jeśli maszyna wirtualna została usunięta. |
| ComputeResourceId (łańz obliczeń) | W pełni kwalifikowany identyfikator zasobów obliczeniowych maszyny wirtualnej. |
| Podlegające z roszczeniem | Ustawiona na wartość true, jeśli maszyna wirtualna jest maszyną wirtualną, która może być niona | 
| EnvironmentId (ida środowiska) | Identyfikator zasobu środowiska, w którym maszyna wirtualna została utworzona w. Jest pusta, gdy maszyna wirtualna nie została utworzona jako część zasobu środowiska. |
| Data ważności | Data wygaśnięcia maszyny Wirtualnej. Jest ustawiona na pusta, jeśli data wygaśnięcia nie została ustawiona.
| GaleriaImageReferenceVersion |  Wersja obrazu podstawowego maszyny Wirtualnej. |
| GaleriaImageReferenceOffer | Oferta obrazu podstawowego maszyny Wirtualnej. |
| GaleriaImageReferencePublisher | Wydawca obrazu podstawowego maszyny Wirtualnej. |
| GaleriaImageReferenceSku | Sku obrazu podstawowego maszyny Wirtualnej |
| Typ GalleryImageReferenceOsType | Typ systemu operacyjnego obrazu podstawowego maszyny Wirtualnej |
| Niestandardowy imijid | W pełni kwalifikowany identyfikator niestandardowego obrazu podstawowego maszyny Wirtualnej. |

Kolumny danych zawarte w **pliku disks.csv** są wymienione poniżej:

| Nazwa kolumny | Opis | 
| ----------- | ----------- | 
| SubscriptionId | Identyfikator subskrypcji zawierającej laboratorium |
| LabUId ( LabUId ) | Identyfikator GUID dla laboratorium |
| Nazwa laboratorium | Nazwa laboratorium | 
| LabResourceId (LabResourceId) | W pełni kwalifikowany identyfikator zasobu dla laboratorium | 
| ResourceGroupName | Nazwa grupy zasobów zawierającej laboratorium | 
| ResourceId | W pełni kwalifikowany identyfikator zasobu dla maszyny Wirtualnej. |
| Identyfikator zasobu | Identyfikator GUID dla maszyny Wirtualnej |
 |Nazwa | Nazwa dołączonego dysku |
| CreatedTime |Data i godzina utworzenia dysku danych. |
| Data usunięta | Data i godzina usunięcia dysku danych. |
| Stan zasobów | Stan zasobu. Aktywny, jeśli zasób istnieje. Nieaktywny po usunięciu. |
| Nazwa dysku | Nazwa obiektu blob dysku danych. |
| DysksizeGB | Rozmiar dysku danych. |
| DiskType | Typ dysku danych. 0 dla standardu, 1 dla premium. |
| Identyfikator LeasedByVmId | Identyfikator zasobu maszyny Wirtualnej, do której został dołączony dysk danych. |


> [!NOTE]
> Jeśli masz do czynienia z wieloma laboratoriami i chcesz uzyskać ogólne informacje, dwie kolumny kluczy są **LabUID** i **ResourceUId**, które są unikatowe identyfikatory w ramach subskrypcji.

Eksportowane dane mogą być manipulowane i wizualizowane za pomocą narzędzi, takich jak SQL Server, Power BI itp. Ta funkcja jest szczególnie przydatna, gdy chcesz zgłosić użycie laboratorium do zespołu zarządzania, który może nie używać tej samej subskrypcji platformy Azure, jak to zrobić.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 

- [Ustawianie zasad dla laboratorium](devtest-lab-get-started-with-lab-policies.md)
- [Często zadawane pytania](devtest-lab-faq.md)
