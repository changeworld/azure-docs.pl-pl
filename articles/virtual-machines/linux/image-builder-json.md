---
title: Tworzenie szablonu Kreatora obrazów platformy Azure (wersja zapoznawcza)
description: Dowiedz się, jak utworzyć szablon za pomocą Kreatora obrazów platformy Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/10/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: cf8264cbad3c5c88c58cff3b95cb5c68adf0686c
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65538294"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Wersja zapoznawcza: Tworzenie szablonu Kreatora obrazów platformy Azure 

Kreator obrazów platformy Azure korzysta z pliku JSON do przekazywania informacji do usługi kreatora obrazów. W tym artykule firma Microsoft będzie przechodzi przez części pliku json, dzięki czemu można tworzyć własne. Aby wyświetlić przykłady plików pełnej JSON, zobacz [Azure obrazu konstruktora w witrynie GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Ten format jest używany podstawowy szablon:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Wersja typu i interfejsu API

`type` Jest typem zasobu, który musi być `"Microsoft.VirtualMachineImages/imageTemplates"`. `apiVersion` Zmieni się wraz z upływem czasu zgodnie ze zmianami interfejsu API, ale powinien być `"2019-05-01-preview"` w wersji zapoznawczej.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Lokalizacja

Lokalizacja jest region, w której zostanie utworzony obraz niestandardowy. Kreator obrazów (wersja zapoznawcza) obsługiwane są następujące regiony:

- Wschodnie stany USA
- Wschodnie stany USA 2
- Zachodnio-środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2


```json
    "location": "<region>",
```
    
## <a name="depends-on-optional"></a>Zależy od (opcjonalnie)

Ta opcjonalna sekcja może służyć do zapewnienia, że zależności odbywa się przed kontynuowaniem. 

```json
    "dependsOn": [],
```

Aby uzyskać więcej informacji, zobacz [Definiowanie zależności zasobu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Tożsamość
Domyślnie obsługuje Kreatora obrazów za pomocą skryptów lub kopiowania plików z wielu lokalizacji, takich jak GitHub i Azure storage. Można ich używać, muszą być dostępny publicznie.

Umożliwia także tożsamości zarządzanej Azure User-Assigned, zdefiniowane przez użytkownika, aby zezwolić na dostęp Kreator obrazów usługi Azure Storage, tak długo, jak tożsamość udzielono co najmniej "Czytnik danych obiektu Blob magazynu" na koncie usługi Azure storage. Oznacza to, że nie trzeba dostępną z zewnątrz obiektów blob storage lub Instalator tokeny sygnatur dostępu Współdzielonego.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Aby uzyskać kompletny przykład, zobacz [ korzystać z tożsamości zarządzanej Azure User-Assigned uzyskiwania dostępu do plików w usłudze Azure Storage](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Obraz konstruktora, obsługę tożsamości User-Assigned: • obsługuje jednej tożsamości, tylko • nie obsługuje niestandardowych nazw domen

Aby dowiedzieć się więcej, zobacz [co to jest zarządzanych tożsamości dla zasobów platformy Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Aby uzyskać więcej informacji na temat wdrażania tej funkcji, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Właściwości: źródła

`source` Sekcja zawiera informacje dotyczące obrazu źródłowego, który będzie używany przez kreatora obrazów.

Interfejs API wymaga "SourceType" definiujące źródło budowania obrazu, obecnie dostępne są trzy typy:
- ISO — Użyj tego, gdy źródłem jest ISO systemu RHEL.
- PlatformImage - wykazały, że obraz źródłowy jest obraz z witryny Marketplace.
- ManagedImage — Użyj tego ustawienia podczas od zwykłego obrazu zarządzanego.
- SharedImageVersion — to jest używany, gdy używana jest wersja obrazu w galerii obrazów udostępnione jako źródło.

### <a name="iso-source"></a>Źródło ISO

Kreator obrazów platformy Azure obsługuje tylko przy użyciu opublikowanych Red Hat Enterprise Linux 7.x binarne DVD obrazy ISO, w wersji zapoznawczej. Kreator obrazów obsługuje:
- RHEL 7.3 
- RHEL 7.4 
- RHEL W WERSJI 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

Aby uzyskać `sourceURI` i `sha256Checksum` wartości, przejdź do `https://access.redhat.com/downloads` następnie wybierz produkt **Red Hat Enterprise Linux**oraz obsługiwaną wersją. 

Na liście **instalatorów i obrazów Red Hat Enterprise Linux Server**, należy skopiować link DVD binarne programu Red Hat Enterprise Linux 7.x i sumy kontrolnej.

> [!NOTE]
> Tokeny dostępu łącza są odświeżane w krótkich odstępach czasu, więc za każdym razem, gdy użytkownik chce przesłać szablon, musisz sprawdzić, Jeśli połączysz RH adres uległ zmianie.
 
### <a name="platformimage-source"></a>Źródło PlatformImage 
Kreator obrazów platformy Azure obsługuje następujące obrazów portalu Azure Marketplace:
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7.6
* CentOS 7.6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


Właściwości, w tym miejscu są takie same, służą do tworzenia maszyny Wirtualnej, przy użyciu interfejsu wiersza polecenia AZ Uruchom poniżej, aby pobrać właściwości: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> Wersja nie może być "najnowszy", należy użyć polecenia powyżej sposób uzyskać numer wersji. 

### <a name="managedimage-source"></a>Źródło ManagedImage

Ustawia obraz źródłowy jako istniejącego zarządzanego obrazu uogólnionego wirtualnego dysku twardego lub maszyny Wirtualnej. Zarządzanego obrazu źródłowego musi być obsługiwane systemy operacyjne i musi być w tym samym regionie, co Twój szablon kreatora obrazów platformy Azure. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` Powinny być ResourceId zarządzanego obrazu. Użyj `az image list` Aby wyświetlić listę dostępnych obrazów.


### <a name="sharedimageversion-source"></a>Źródło SharedImageVersion
Ustawia obraz źródłowy istniejącą wersję obrazu w galerii obrazów udostępnione. Wersja obrazu muszą być obsługiwanego systemu operacyjnego i obraz, który musi zostać zreplikowana do tego samego regionu co szablon kreatora obrazów platformy Azure. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId` Powinny być ResourceId wersji obrazu. Użyj [az sig wersję obrazu listy](/cli/azure/sig/image-version#az-sig-image-version-list) do listy obrazu wersji.

## <a name="properties-customize"></a>Właściwości: dostosowywanie


Kreator obrazów obsługuje wiele "twórcom dostosowań". Twórcom dostosowań są funkcje, które są używane do dostosowywania obrazu, takie jak uruchamianie skryptów lub ponowne uruchamianie serwerów. 

Korzystając z `customize`: 
- Można użyć wielu twórcom dostosowań, ale musi mieć unikatową `name`.
- Twórcom dostosowań wykonywane w kolejności określonej w szablonie.
- W przypadku awarii jednego konfiguratora składnik całego dostosowania zostaną zakończyć się niepowodzeniem i ponownie zgłosić błąd.
- Należy rozważyć, jak długo kompilację obrazu wymagają i Dostosuj właściwości "buildTimeoutInMinutes", umożliwia kreatora obrazów wystarczająco dużo czasu na ukończenie.
- Zdecydowanie zalecane jest, że należy dokładnie przetestować skrypt przed jego użyciem w szablonie. Debugowanie skryptu na własnej maszynie Wirtualnej będzie łatwiejsza.
- Nie należy umieszczać w skryptach poufnych danych. 
- Lokalizacje skryptu muszą być dostępny publicznie, chyba że używasz [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
W sekcji Dostosuj jest tablicą. Kreator obrazów platformy Azure zostanie uruchomiony za pośrednictwem twórcom dostosowań w kolejności sekwencyjnej. Jakiekolwiek niepowodzenie w dowolnym konfiguratora zakończy się niepowodzeniem procesu kompilacji. 
 
 
### <a name="shell-customizer"></a>Konfigurator powłoki

Obsługuje konfiguratora powłoki uruchamianie skryptów powłoki, muszą to być publicznie dostępne dla IB do nich dostęp.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>"        
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Obsługa systemu operacyjnego: Linux 
 
Dostosowywanie właściwości:

- **Typ** — skorupach 
- **Nazwa** — Nazwa śledzenia dostosowania 
- **scriptUri** — identyfikator URI do lokalizacji pliku 
- **wbudowane** -tablicę poleceń powłoki, oddzielonych przecinkami.
 
> [!NOTE]
> Podczas uruchamiania konfiguratora powłoki ze źródłem ISO systemu RHEL, należy upewnić się pierwszy uchwytów w powłoki dostosowywania rejestrowanie przy użyciu serwera uprawnienie firmy Red Hat, zanim nastąpi wszelkie dostosowania. Po zakończeniu dostosowywania skryptu należy wyrejestrować z serwerem uprawnienie.

### <a name="windows-restart-customizer"></a>Windows ponownie uruchomić konfiguratora 
Konfigurator ponownego uruchomienia pozwala na ponowne uruchomienie maszyny Wirtualnej z systemem Windows i poczekaj na jego powrocie do trybu online, pozwala na instalowanie oprogramowania, wymagającego ponownego uruchomienia systemu.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

Obsługa systemu operacyjnego: Windows
 
Dostosowywanie właściwości:
- **Typ**: WindowsRestart
- **restartCommand** — polecenie do wykonania ponownego uruchomienia (opcjonalnie). Wartość domyślna to `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** — polecenie, aby sprawdzić, jeśli ponowne uruchomienie powiodło się (opcjonalnie). 
- **restartTimeout** -Uruchom ponownie limit czasu określony w postaci ciągu, wielkości i jednostki. Na przykład `5m` (5 minut) lub `2h` (2 godziny). Wartość domyślna to: '5m'


### <a name="powershell-customizer"></a>Konfigurator programu PowerShell 
Obsługuje konfiguratora powłoki, uruchamianie skryptów programu PowerShell i wierszu polecenia, skrypty muszą być dostępne publicznie IB do nich dostęp.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

Obsługa systemu operacyjnego: System Windows i Linux

Dostosowywanie właściwości:

- **Typ** — program PowerShell.
- **scriptUri** — identyfikator URI do lokalizacji pliku skryptu programu PowerShell. 
- **wbudowane** — wbudowanego polecenia, aby uruchomić, oddzielonych przecinkami.
- **valid_exit_codes** — jest to opcjonalne, prawidłowe kody, które mogą być zwracane przez skrypt/wbudowanego polecenia, pozwoli to uniknąć zgłoszonego błędu polecenia skryptu/wbudowanego.

### <a name="file-customizer"></a>Konfigurator pliku

Konfigurator pliku umożliwia kreatora obrazów, Pobierz plik z usługi GitHub lub usługi Azure storage. Jeśli potok kompilacji obrazu, która korzysta z artefaktów kompilacji, można następnie ustawić konfiguratora plików do pobrania z udziału kompilacji i Przenieś artefakty do obrazu.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
         }
     ]
```

Obsługa systemu operacyjnego: Linux i Windows 

Właściwości konfiguratora pliku:

- **sourceuri i** — punkt końcowy dostępny magazyn, może to być GitHub lub usługi Azure storage. Można go pobrać tylko jeden plik, a nie cały katalog. Jeśli trzeba pobrać katalogu, użyj skompresowany plik, a następnie zdekompresować go za pomocą programu PowerShell lub powłoki twórcom dostosowań. 
- **miejsce docelowe** — jest to docelowy pełną ścieżkę i nazwę pliku. Wszystkie odwołania ścieżkę i podkatalogi musi istnieje, skonfigurować tych wcześniej za pomocą programu PowerShell lub powłoki twórcom dostosowań. Utwórz ścieżkę umożliwia twórcom dostosowań skryptu. 

Jest to obsługiwane przez katalogi Windows i Linux ścieżki, ale istnieją pewne różnice: 
- Linux systemu operacyjnego — jedyna ścieżka obrazu może zapisywać konstruktora jest tmp.
- Windows — Brak ograniczeń ścieżki, ale ścieżki musi istnieć.
 
 
Jeśli wystąpi błąd próbują pobrać plik i umieść ją w określonym katalogu krok Dostosuj zakończy się niepowodzeniem i będzie on miał customization.log.

Pliki w konfiguratora plików, które można pobrać z usługi Azure Storage przy użyciu [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="generalize"></a>Generalize 
Domyślnie Kreator obrazów usługi Azure będzie również uruchomić "deprovision" kod na końcu każdej fazy dostosowywania obrazu do uogólnienia obrazu. Uogólnianie jest procesem, gdzie obraz, który jest skonfigurowany tak, może zostać użyte do utworzenia wielu maszyn wirtualnych. W przypadku maszyn wirtualnych Windows Azure kreatora obrazów używa programu Sysprep. W przypadku systemu Linux jest uruchamiany Kreator obrazów platformy Azure "waagent-deprovision". 

Polecenia kreatora obrazów użytkowników do uogólnienia może nie być odpowiednie dla każdej sytuacji, więc kreator obrazów platformy Azure będzie umożliwiają dostosowanie tego polecenia, jeśli to konieczne. 

Jeśli migrujesz istniejące dostosowywania, a używasz różne polecenia Sysprep/waagent, można za pomocą poleceń ogólnych kreatora obrazów, a jeśli proces tworzenia maszyny Wirtualnej nie powiedzie się, należy użyć własnego polecenia Sysprep lub ich waagent.

Jeśli Kreator obrazów platformy Azure tworzy niestandardowy obraz Windows pomyślnie, a następnie utwórz Maszynę wirtualną z go, a następnie okaże się, że proces tworzenia maszyny Wirtualnej nie powiedzie się lub nie zostanie ukończone pomyślnie, należy zapoznać się z dokumentacją programu Sysprep systemu Windows Server lub Prześlij żądanie pomocy technicznej za pomocą Zespołem pomocy technicznej systemu Windows Server Sysprep klienta usług, kto może rozwiązać i wykonać funkcji advise dla poprawne użycie narzędzia Sysprep.


#### <a name="default-sysprep-command"></a>Domyślnie polecenia Sysprep
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Domyślnie polecenia anulowania aprowizacji systemu Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Zastępowanie poleceń
Aby zastąpić polecenia, użyj provisioners skrypt programu PowerShell lub powłoki, aby utworzyć pliki poleceń przy użyciu dokładnej nazwy pliku i umieść je w katalogu poprawny katalogów:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Kreator obrazów odczyta te polecenia, te są zapisywane w dziennikach AIB "customization.log". Zobacz [Rozwiązywanie problemów z](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) na temat sposobu zbierania dzienników.
 
## <a name="properties-distribute"></a>Właściwości: Dystrybucja

Kreator obrazów platformy Azure obsługuje trzy dystrybucji elementów docelowych: 

- **managedImage** — zarządzanego obrazu.
- **sharedImage** — Galeria obrazów udostępnionych.
- **Wirtualny dysk twardy** — dysk VHD na koncie magazynu.

Można dokonać dystrybucji obrazu do obu typów docelowego z taką samą konfigurację, zapoznaj się z artykułem [przykłady](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Ponieważ może mieć więcej niż jeden element docelowy i przekazać go, Kreator obrazów zachowuje stan każdego obiektu docelowego dystrybucji, który jest możliwy, badając `runOutputName`.  `runOutputName` Jest obiekt można tworzyć zapytania wpis dystrybucji dla informacji o dystrybucji. Na przykład można badać lokalizacja wirtualnego dysku twardego lub regiony, w których wersja obrazu zostały zreplikowane w usłudze. Jest to właściwość każdej dystrybucji elementu docelowego. `runOutputName` Musi być unikatowa dla każdego obiektu docelowego dystrybucji.
 
### <a name="distribute-managedimage"></a>Dystrybucja: managedImage

Dane wyjściowe obrazu będzie zasób obrazu zarządzanego.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Dystrybucja właściwości:
- **Typ** — managedImage 
- **imageId** — identyfikator zasobu obrazu docelowego, oczekiwany format: /subscriptions/<subscriptionId>/resourceGroups/<destinationResourceGroupName>/providers/Microsoft.Compute/images/<imageName>
- **Lokalizacja** — lokalizacja zarządzanego obrazu.  
- **runOutputName** — unikatowa nazwa do identyfikacji dystrybucji.  
- **artifactTags** — tagi pary wartości klucza określone opcjonalne użytkownika.
 
 
> [!NOTE]
> Docelowa grupa zasobów musi istnieć.
> Jeśli chcesz, aby obraz przekazany do innego regionu, zwiększy czas wdrażania. 

### <a name="distribute-sharedimage"></a>Dystrybucja: sharedImage 
Galeria obrazów platformy Azure Shared jest nowa usługa zarządzania obrazami, która umożliwia zarządzanie replikacji region obrazu, przechowywanie wersji i udostępniania obrazów niestandardowych. Kreator obrazów platformy Azure obsługuje dystrybucję z tą usługą, dzięki czemu można dokonać dystrybucji obrazów regiony obsługiwane przez udostępnione, galerie obrazów. 
 
Galeria obrazów współdzielona składają się: 
 
- Galeria — kontener dla wielu obrazów udostępnionych. Galeria zostanie wdrożona w jednym regionie.
- Obraz definicje - koncepcyjny grupowania obrazów. 
- Wersje obrazów — jest to typ obrazu, który jest używany do wdrażania maszyn wirtualnych lub skalowania zestawu. Wersje obrazów mogą być replikowane do innych regionów, w której maszyny wirtualne muszą zostać wdrożone.
 
Aby można było dystrybuować do galerii obrazów, musi utworzyć galerii i definicję obrazu, zobacz [udostępnionych obrazów](shared-images.md). 

```json
{
     "type": "sharedImage",
     "galleryImageId": “<resource ID>”,
     "runOutputName": "<name>",
     "artifactTags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
     "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]}
``` 

Dystrybucja właściwości galerii udostępnionego obrazu:

- **Typ** -sharedImage  
- **galleryImageId** — identyfikator galerii obrazów udostępnionych. Format to: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>.
- **runOutputName** — unikatowa nazwa do identyfikacji dystrybucji.  
- **artifactTags** — tagi pary wartości klucza określone opcjonalne użytkownika.
- **replicationRegions** — tablica regionach w celu replikacji. Jeden z regionów musi być regionu, w których jest wdrażany galerii.
 
> [!NOTE]
> Można użyć Kreatora obrazów platformy Azure w innym regionie, w galerii, ale usługa kreatora obrazów platformy Azure musi transfer obrazu między centrami danych i to będzie trwać dłużej. Kreator obrazów zostaną automatycznie wersję obrazu, w oparciu o całkowitą monotoniczny, nie można określić jej obecnie. 

### <a name="distribute-vhd"></a>Dystrybucji: VHD  
Do dysku VHD może zapewniać dane wyjściowe. Można następnie skopiuj wirtualny dysk twardy i użyć go do publikowania w portalu Azure MarketPlace lub za pomocą usługi Azure Stack.  

```json
 { 
     "type": "VHD",
     "runOutputName": "<VHD name>",
     "tags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
 }
```
 
Obsługa systemu operacyjnego: Windows i Linux

Dystrybucja parametry wirtualnego dysku twardego:

- **Typ** -wirtualnego dysku twardego.
- **runOutputName** — unikatowa nazwa do identyfikacji dystrybucji.  
- **tagi** — tagi pary wartości klucza określone opcjonalne użytkownika.
 
Kreator obrazów platformy Azure nie zezwala użytkownikowi na określenie Lokalizacja konta magazynu, ale możesz zbadać stan `runOutputs` lokalizacji.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Po utworzeniu wirtualnego dysku twardego, skopiuj go do innej lokalizacji, tak szybko, jak to możliwe. Wirtualny dysk twardy jest przechowywany na koncie magazynu w grupie zasobów tymczasowe, utworzone po przesłaniu obrazu szablonu usługi kreatora obrazów platformy Azure. Jeśli usuniesz szablon obrazu, spowoduje utratę wirtualnego dysku twardego. 
 
## <a name="next-steps"></a>Kolejne kroki

Przykładowe pliki JSON dla różnych scenariuszy, w [Azure obrazu konstruktora w witrynie GitHub](https://github.com/danielsollondon/azvmimagebuilder).
 
 
 
 
 
 
 
 
 
 
