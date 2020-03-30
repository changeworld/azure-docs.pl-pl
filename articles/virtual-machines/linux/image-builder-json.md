---
title: Tworzenie szablonu konstruktora obrazów platformy Azure (wersja zapoznawcza)
description: Dowiedz się, jak utworzyć szablon do użycia w usłudze Azure Image Builder.
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: e1f1bc09406c34836c13deb805fa399ab4751d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246793"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Wersja zapoznawcza: tworzenie szablonu konstruktora obrazów platformy Azure 

Usługa Azure Image Builder używa pliku json do przekazywania informacji do usługi Konstruktora obrazów. W tym artykule przejdziemy do sekcji pliku json, dzięki czemu można zbudować własne. Aby zobaczyć przykłady pełnych plików .json, zobacz [GitHub programu Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Jest to podstawowy format szablonu:

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
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "name": "<vnetName>",
                "subnetName": "<subnetName>",
                "resourceGroupName": "<vnetRgName>"
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Typ i wersja interfejsu API

Jest `type` to typ zasobu, `"Microsoft.VirtualMachineImages/imageTemplates"`który musi być . Będzie `apiVersion` się zmieniać wraz z czasem zmiany `"2019-05-01-preview"` interfejsu API, ale powinny być w wersji zapoznawczej.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Lokalizacja

Lokalizacja to region, w którym zostanie utworzony obraz niestandardowy. W przypadku podglądu Konstruktora obrazów obsługiwane są następujące regiony:

- Wschodnie stany USA
- Wschodnie stany USA 2
- Zachodnio-środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2
- Europa Północna
- Europa Zachodnia


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
Domyślnie Image Builder użyje "Standard_D1_v2" kompilacji maszyny Wirtualnej, można zastąpić to, na przykład, jeśli chcesz dostosować obraz dla maszyny Wirtualnej GPU, potrzebujesz rozmiaru maszyny Wirtualnej GPU. Jest to opcjonalne.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Domyślnie Konstruktor obrazów nie zmieni rozmiaru obrazu, użyje rozmiaru z obrazu źródłowego. Można zwiększyć rozmiar dysku systemu operacyjnego (Win i Linux), jest to opcjonalne, a wartość 0 oznacza pozostawić ten sam rozmiar jako obraz źródłowy. 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Jeśli nie określisz żadnych właściwości sieci wirtualnej, a następnie Image Builder utworzy własną sieć wirtualną, publiczny adres IP i nsg. Publiczny adres IP jest używany do komunikacji z maszyną wirtualną kompilacji, jednak jeśli nie chcesz publicznego adresu IP lub chcesz, aby Kreator obrazów miał dostęp do istniejących zasobów sieci wirtualnej, takich jak serwery konfiguracji (DSC, Chef, Puppet, Ansible), udziały plików itp. , a następnie można określić sieci wirtualnej. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją sieci ,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder)jest to opcjonalne.

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Tagi

Są to pary klucz/wartość, które można określić dla generowanego obrazu.

## <a name="depends-on-optional"></a>Zależy od (opcjonalnie)

Ta opcjonalna sekcja może służyć do zapewnienia, że zależności są wykonywane przed kontynuowaniem. 

```json
    "dependsOn": [],
```

Aby uzyskać więcej informacji, zobacz [Definiowanie zależności zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Tożsamość
Domyślnie Program Image Builder obsługuje używanie skryptów lub kopiowanie plików z wielu lokalizacji, takich jak GitHub i Azure Storage. Aby z nich korzystać, muszą być publicznie dostępne.

Można również użyć tożsamości zarządzanej przypisanej przez użytkownika platformy Azure, zdefiniowanej przez użytkownika, aby zezwolić konstruktorowi obrazów na dostęp do usługi Azure Storage, o ile tożsamość została przyznana jako minimum "Czytnik danych obiektów blob magazynu" na koncie magazynu platformy Azure. Oznacza to, że nie trzeba udostępniać obiektów blob magazynu zewnętrznie lub skonfigurować tokeny Sygnatury dostępu Współdzielonego.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Aby uzyskać pełny przykład, zobacz [Uzyskiwanie dostępu do plików w usłudze Azure Storage za pomocą tożsamości zarządzanej przypisanej przez użytkownika platformy Azure.](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)

Obsługa konstruktora obrazów dla tożsamości przypisanej przez użytkownika: • Obsługuje tylko jedną tożsamość • Nie obsługuje niestandardowych nazw domen

Aby dowiedzieć się więcej, zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Aby uzyskać więcej informacji na temat wdrażania tej funkcji, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)

## <a name="properties-source"></a>Właściwości: źródło

Sekcja `source` zawiera informacje o obrazie źródłowym, który będzie używany przez Konstruktora obrazów.

Interfejs API wymaga "SourceType", który definiuje źródło kompilacji obrazu, obecnie istnieją trzy typy:
- PlatformImage - wskazano, że obraz źródłowy jest obraz marketplace.
- ManagedImage — użyj tego, gdy zaczyna się od zwykłego obrazu zarządzanego.
- SharedImageVersion — jest to używane podczas korzystania z wersji obrazu w galerii obrazów udostępnionych jako źródła.

### <a name="iso-source"></a>Źródło ISO
Jesteśmy przestarzałe tej funkcji z kreatora obrazów, jak są teraz [RHEL Bring Your Own Subscription obrazy,](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos)proszę przejrzeć osi czasu poniżej:
    * 31 marca 2020 r. — szablony obrazów ze źródłami ISO RHEL będą teraz dłużej akceptowane przez dostawcę zasobów.
    * 30 kwietnia 2020 r. — szablony obrazów zawierające źródła ISO RHEL nie będą już przetwarzane.

### <a name="platformimage-source"></a>Źródło PlatformyImage 
Usługa Azure Image Builder obsługuje obrazy systemu Windows Server i klienta oraz usługi Linux Azure Marketplace, zobacz [pełną](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) listę tutaj. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


Właściwości w tym miejscu są takie same, które są używane do tworzenia maszyn wirtualnych, przy użyciu interfejsu wiersza polecenia AZ, uruchom poniżej, aby uzyskać właściwości: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

Można użyć "najnowsze" w wersji, wersja jest oceniana, gdy kompilacja obrazu ma miejsce, a nie po przesłaniu szablonu. Jeśli ta funkcja jest używana z miejscem docelowym galerii obrazów udostępnionych, można uniknąć ponownego ponownego przesłania szablonu i ponownie uruchomić kompilację obrazu w odstępach czasu, aby obrazy były odtworzone z najnowszych obrazów.

### <a name="managedimage-source"></a>Źródło ManagedImage

Ustawia obraz źródłowy jako istniejący obraz zarządzany uogólnionej dysku VHD lub maszyny Wirtualnej. Źródłowy obraz zarządzany musi być obsługiwanym systemu operacyjnego i znajdować się w tym samym regionie co szablon usługi Azure Image Builder. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

Powinien `imageId` być ResourceId obrazu zarządzanego. Służy `az image list` do wystawiania dostępnych obrazów.


### <a name="sharedimageversion-source"></a>Źródło SharedImageVersion
Ustawia obraz źródłowy istniejącą wersję obrazu w Galerii obrazów udostępnionych. Wersja obrazu musi być obsługiwanego systemu operacyjnego, a obraz musi być replikowany do tego samego regionu, co szablon usługi Azure Image Builder. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

Powinien `imageVersionId` być ResourceId wersji obrazu. Użyj [listy az sig image-version,](/cli/azure/sig/image-version#az-sig-image-version-list) aby wyświetlić listę wersji obrazów.

## <a name="properties-buildtimeoutinminutes"></a>Właściwości: buildTimeoutInMinutes

Domyślnie Konstruktor obrazów będzie działał przez 240 minut. Po tym, będzie limit czasu i zatrzymać, czy kompilacja obrazu jest zakończona. Jeśli limit czasu zostanie trafiony, zostanie wyświetlony błąd podobny do tego:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Jeśli nie określisz buildTimeoutInMinutes wartość lub ustawić go na 0, użyje wartości domyślnej. Można zwiększyć lub zmniejszyć wartość, maksymalnie 960 minut (16 godzin). W przypadku systemu Windows nie zaleca się ustawiania tego poniżej 60 minut. Jeśli okaże się, że widzisz limit czasu, przejrzyj [dzienniki,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)aby sprawdzić, czy krok dostosowywania oczekuje na coś takiego jak dane wejściowe użytkownika. 

Jeśli okaże się, że potrzebujesz więcej czasu na dostosowanie do wykonania, ustaw to na to, co uważasz, że potrzebujesz, z niewielkim obciążeniem. Ale nie należy ustawiać go zbyt wysoko, ponieważ może być konieczne czekać na jego limit czasu przed widząc błąd. 


## <a name="properties-customize"></a>Właściwości: dostosuj

Image Builder obsługuje wiele "konfiguratorów". Konfiguratory to funkcje, które są używane do dostosowywania obrazu, takie jak uruchamianie skryptów lub ponowne uruchamianie serwerów. 

Podczas `customize`korzystania z : 
- Można użyć wielu konfiguratorów, ale `name`muszą one mieć unikatowy .
- Konfiguratory są wykonywane w kolejności określonej w szablonie.
- Jeśli jeden konfigurator ulegnie awarii, składnik całego dostosowywania zakończy się niepowodzeniem i zgłosi błąd.
- Zdecydowanie zaleca się dokładne przetestowanie skryptu przed użyciem go w szablonie. Debugowanie skryptu na własnej maszynie wirtualnej będzie łatwiejsze.
- Nie należy umieszczać poufnych danych w skryptach. 
- Lokalizacje skryptów muszą być publicznie dostępne, chyba że używasz [msi](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
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

 
Sekcja dostosowywania jest tablicą. Usługa Azure Image Builder będzie działać za pośrednictwem konfiguratorów w kolejności. Każdy błąd w dowolnym konfiguratorze zakończy się niepowodzeniem procesu kompilacji. 
 
 
### <a name="shell-customizer"></a>Konfigurator powłoki

Konfigurator powłoki obsługuje uruchamianie skryptów powłoki, muszą one być publicznie dostępne dla IB, aby uzyskać do nich dostęp.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
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
 
Dostosuj właściwości:

- **typ** – Powłoka 
- **nazwa** - nazwa śledzenia dostosowywania 
- **scriptUri** - identyfikator URI do lokalizacji pliku 
- **inline** - tablica poleceń powłoki, oddzielona przecinkami.
- **sha256Checksum** — wartość sumy kontrolnej sha256 pliku, wygenerujesz to lokalnie, a następnie Konstruktor obrazów będzie sumować sumę kontrolną i sprawdzać poprawność.
    * Aby wygenerować sha256Checksum, przy użyciu terminala na Mac / Linux uruchomić:`sha256sum <fileName>`


Aby polecenia działały z uprawnieniami superużytów, `sudo`muszą być poprzedzone .

> [!NOTE]
> Podczas uruchamiania konfiguratora powłoki ze źródłem ISO RHEL, należy upewnić się, że pierwsza powłoka dostosowywania obsługuje rejestrowanie się na serwerze uprawnień Red Hat przed wystąpieniem jakichkolwiek dostosowywania. Po zakończeniu dostosowywania skrypt powinien zostać wyrejestrowany na serwerze uprawnień.

### <a name="windows-restart-customizer"></a>Konfigurator ponownego uruchamiania systemu Windows 
Konfigurator Restart umożliwia ponowne uruchomienie maszyny Wirtualnej systemu Windows i oczekiwanie na jej powrót do trybu online, co pozwala na zainstalowanie oprogramowania, które wymaga ponownego uruchomienia komputera.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

Obsługa systemu operacyjnego: Windows
 
Dostosuj właściwości:
- **Typ**: WindowsRestart
- **restartCommand** — polecenie wykonania ponownego uruchomienia (opcjonalnie). Wartość domyślna to `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** — polecenie, aby sprawdzić, czy ponowne uruchomienie powiodło się (opcjonalnie). 
- **restartTimeout** — limit czasu ponownego uruchomienia określony jako ciąg wielkości i jednostki. Na przykład `5m` (5 minut) lub `2h` (2 godziny). Wartość domyślna to: '5m'

### <a name="linux-restart"></a>Ponowne uruchomienie systemu Linux  
Nie ma konfiguratora restartu systemu Linux, jednak jeśli instalujesz sterowniki lub składniki, które wymagają ponownego uruchomienia, można je zainstalować i wywołać ponowne uruchomienie przy użyciu konfiguratora Powłoki, istnieje 20min SSH limit czasu do maszyny Wirtualnej kompilacji.

### <a name="powershell-customizer"></a>Konfigurator programu PowerShell 
Konfigurator powłoki obsługuje uruchamianie skryptów programu PowerShell i polecenia wbudowanego, skrypty muszą być publicznie dostępne dla IB, aby uzyskać do nich dostęp.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

Obsługa systemu operacyjnego: Windows i Linux

Dostosuj właściwości:

- **typ** – PowerShell.
- **scriptUri** — identyfikator URI do lokalizacji pliku skryptu programu PowerShell. 
- **inline** — polecenia wbudowane do uruchomienia, oddzielone przecinkami.
- **validExitCodes** — opcjonalne, prawidłowe kody, które mogą być zwracane z polecenia skrypt/wbudowany, pozwoli to uniknąć zgłoszonego niepowodzenia polecenia skrypt/wbudowany.
- **runElevated** — opcjonalne, logiczne, obsługa uruchamiania poleceń i skryptów z podwyższonymi uprawnieniami.
- **sha256Checksum** — wartość sumy kontrolnej sha256 pliku, wygenerujesz to lokalnie, a następnie Konstruktor obrazów będzie sumować sumę kontrolną i sprawdzać poprawność.
    * Aby wygenerować sha256Checksum, przy użyciu programu PowerShell w systemie Windows [Get-Hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Konfigurator plików

Konfigurator plików umożliwia konstruktorowi obrazów pobieranie pliku z usługi GitHub lub magazynu platformy Azure. Jeśli masz potok kompilacji obrazu, który opiera się na artefakty kompilacji, można następnie ustawić konfigurator plików do pobrania z udziału kompilacji i przenieść artefakty do obrazu.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

Obsługa systemu operacyjnego: Linux i Windows 

Właściwości konfiguratora plików:

- **sourceUri** — punkt końcowy magazynu dostępne, może to być GitHub lub azure storage. Można pobrać tylko jeden plik, a nie cały katalog. Jeśli chcesz pobrać katalog, użyj skompresowanego pliku, a następnie zdekompresuj go za pomocą konfiguratorów Powłoki lub Programu PowerShell. 
- **miejsce docelowe** – jest to pełna ścieżka docelowa i nazwa pliku. Wszystkie ścieżki i podkatalogi, do których istnieją odwołania, muszą istnieć, użyj konfiguratorów Powłoki lub Programu PowerShell, aby wcześniej je skonfigurować. Można użyć konfiguratorów skryptów, aby utworzyć ścieżkę. 

Jest to obsługiwane przez katalogi systemu Windows i ścieżki systemu Linux, ale istnieją pewne różnice: 
- Linux OS - jedyną ścieżką, do którą może pisać konstruktor obrazów, jest /tmp.
- Windows — nie ma ograniczenia ścieżki, ale ścieżka musi istnieć.
 
 
Jeśli wystąpił błąd podczas próby pobrania pliku lub umieszczenia go w określonym katalogu, krok dostosowywania zakończy się niepowodzeniem, a to będzie w pliku customization.log.

> [!NOTE]
> Konfigurator plików nadaje się tylko do pobierania małych plików, < 20MB. W przypadku większych pobrań plików użyj skryptu lub polecenia wbudowanego, `curl`użyj `Invoke-WebRequest`kodu do pobierania plików, takich jak Linux `wget` lub , Windows, .

Pliki w konfiguratorze plików można pobrać z usługi Azure Storage przy użyciu [programu MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="windows-update-customizer"></a>Konfigurator usługi Windows Update
Ten konfigurator jest zbudowany na [społeczności Windows Update Provisioner](https://packer.io/docs/provisioners/community-supported.html) dla packer, który jest projekt open source obsługiwane przez społeczność Packer. Firma Microsoft testuje i sprawdza poprawność aprowizacji za pomocą usługi Image Builder i będzie obsługiwać badanie problemów z nim i pracować nad rozwiązaniem problemów, jednak projekt open source nie jest oficjalnie obsługiwany przez firmę Microsoft. Aby uzyskać szczegółową dokumentację dotyczącą aprowizacji usługi Windows Update i pomocy, zobacz repozytorium projektu.
 
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
Obsługa systemu operacyjnego: Windows

Dostosuj właściwości:
- **typ** — WindowsUpdate.
- **searchCriteria** — opcjonalnie, określa, jaki typ aktualizacji są zainstalowane (Zalecane, Ważne itp.), BrowseOnly=0 i IsInstalled=0 (Zalecane) jest ustawieniem domyślnym.
- **filtry** — opcjonalnie, pozwala określić filtr, aby uwzględnić lub wykluczyć aktualizacje.
- **updateLimit** — opcjonalnie, określa, ile aktualizacji można zainstalować, domyślnie 1000.
 
 

### <a name="generalize"></a>Generalize 
Domyślnie usługa Azure Image Builder będzie również uruchamiać kod "deprovision" na końcu każdej fazy dostosowywania obrazu, aby "uogólnić" obraz. Generalizacja jest procesem, w którym obraz jest skonfigurowany, dzięki czemu można go ponownie użyć do utworzenia wielu maszyn wirtualnych. W przypadku maszyn wirtualnych z systemem Windows usługa Azure Image Builder używa narzędzia Sysprep. W systemie Linux w usłudze Azure Image Builder uruchamia program "waagent -deprovision". 

Polecenia użytkowników Konstruktora obrazów do uogólniania może nie być odpowiednie dla każdej sytuacji, więc usługa Azure Image Builder pozwoli dostosować to polecenie, jeśli to konieczne. 

W przypadku migracji istniejących dostosowywań i używania różnych poleceń Sysprep/waagent można użyć ogólnych poleceń Konstruktora obrazów, a jeśli tworzenie maszyny Wirtualnej nie powiedzie się, użyj własnych poleceń Sysprep lub waagent.

Jeśli kreator obrazów platformy Azure pomyślnie utworzy obraz niestandardowy systemu Windows i utworzysz z niego maszynę wirtualną, a następnie okaże się, że tworzenie maszyny Wirtualnej zakończy się niepowodzeniem lub nie zakończy się pomyślnie, należy przejrzeć dokumentację narzędzia Sysprep systemu Windows Server lub zgłosić żądanie pomocy technicznej za pomocą Zespół pomocy technicznej systemu Windows Server Sysprep, który może rozwiązywać problemy i doradzać w zakresie prawidłowego użycia narzędzia Sysprep.


#### <a name="default-sysprep-command"></a>Domyślne polecenie Sysprep
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Domyślne polecenie anulowania obsługi administracyjnej systemu Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Zastępowanie poleceń
Aby zastąpić polecenia, użyj inicjatorów skryptów programu PowerShell lub Shell, aby utworzyć pliki poleceń o dokładnej nazwie pliku i umieścić je w odpowiednich katalogach:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder odczyta te polecenia, są one zapisywane w dziennikach AIB, "customization.log". Zobacz [rozwiązywanie problemów](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) dotyczących sposobu zbierania dzienników.
 
## <a name="properties-distribute"></a>Właściwości: dystrybucja

Usługa Azure Image Builder obsługuje trzy obiekty docelowe dystrybucji: 

- **managedImage** - obraz zarządzany.
- **sharedImage** — Galeria zdjęć udostępnionych.
- **VHD** - VHD na koncie pamięci masowej.

Obraz można rozpowszechniać do obu typów docelowych w tej samej konfiguracji, zobacz [przykłady](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Ponieważ można mieć więcej niż jeden cel do dystrybucji, Image Builder utrzymuje stan dla każdego celu `runOutputName`dystrybucji, który można uzyskać, korzystając z kwerendy . .  Jest `runOutputName` obiektem, który można zbadać dystrybucji księgi informacji o tej dystrybucji. Na przykład można zbadać lokalizację dysku VHD lub regionów, w których wersja obrazu została zreplikowana lub utworzono wersję SIG Image. Jest to właściwość każdego celu dystrybucji. Musi `runOutputName` być unikatowy dla każdego celu dystrybucji. Oto przykład, to jest zapytanie dystrybucji Shared Image Gallery:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
```

Dane wyjściowe:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Dystrybucja: managedImage

Dane wyjściowe obrazu będą zasobem obrazu zarządzanego.

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
 
Rozmieszczaj właściwości:
- **typ** – zarządzanyImage 
- **imageId** — identyfikator zasobu obrazu docelowego, oczekiwany\<format: /subscriptions/ subscriptionId\<>/resourceGroups/destinationResourceGroupName>/providers/Microsoft.Compute/images/imageName\<>
- **lokalizacja** - lokalizacja zarządzanego obrazu.  
- **runOutputName** — unikatowa nazwa do identyfikacji dystrybucji.  
- **artifactTags** — opcjonalne tagi pary wartości klucza określone przez użytkownika.
 
 
> [!NOTE]
> Docelowa grupa zasobów musi istnieć.
> Jeśli obraz ma być dystrybuowany do innego regionu, wydłuży czas wdrożenia . 

### <a name="distribute-sharedimage"></a>Dystrybucja: sharedImage 
Galeria obrazów udostępnionych platformy Azure to nowa usługa zarządzania obrazami, która umożliwia zarządzanie replikacją, przechowywaniem wersji i udostępnianiem obrazów niestandardowych. Usługa Azure Image Builder obsługuje dystrybucję za pomocą tej usługi, dzięki czemu można rozpowszechniać obrazy do regionów obsługiwanych przez galerie obrazów udostępnionych. 
 
Galeria obrazów udostępnionych składa się z: 
 
- Galeria — kontener dla wielu udostępnionych obrazów. Galeria jest wdrażana w jednym regionie.
- Definicje obrazów - grupowanie koncepcyjne obrazów. 
- Wersje obrazów — jest to typ obrazu używany do wdrażania zestawu maszyn wirtualnych lub skalowania. Wersje obrazów mogą być replikowane do innych regionów, w których należy wdrożyć maszyny wirtualne.
 
Aby można było rozpowszechniać w Galerii obrazów, należy utworzyć galerię i definicję obrazu, zobacz [Obrazy udostępnione](shared-images.md). 

```json
{
    "type": "sharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Rozpowszechniaj właściwości udostępnionych galerii obrazów:

- **typ** - sharedImage  
- **galleryImageId** – identyfikator udostępnionej galerii obrazów. Format jest:\</subscriptions/ subscriptionId>/resourceGroups/\<resourceGroupName>/providers/Microsoft.Compute/galleries/\<sharedImageGalleryName>/images/image/\<imageGalleryName>.
- **runOutputName** — unikatowa nazwa do identyfikacji dystrybucji.  
- **artifactTags** — opcjonalne tagi pary wartości klucza określone przez użytkownika.
- **replicationRegions** — tablica regionów replikacji. Jednym z regionów musi być region, w którym jest wdrażana galeria.
 
> [!NOTE]
> Konstruktora obrazów platformy Azure można użyć w innym regionie do galerii, ale usługa Azure Image Builder będzie musiał przenieść obraz między centrami danych, a to potrwa dłużej. Konstruktor obrazów automatycznie wersjonuje obraz na podstawie monotonicznej liczby całkowitej, nie można go obecnie określić. 

### <a name="distribute-vhd"></a>Dystrybucja: VHD  
Można wyprowadzić do dysku VHD. Następnie można skopiować dysk VHD i użyć go do opublikowania w usłudze Azure MarketPlace lub użycia z usługą Azure Stack.  

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

Rozłóż parametry VHD:

- **typ** - VHD.
- **runOutputName** — unikatowa nazwa do identyfikacji dystrybucji.  
- **tagi** — opcjonalne znaczniki pary wartości klucza określone przez użytkownika.
 
Usługa Azure Image Builder nie zezwala użytkownikowi na określenie lokalizacji konta magazynu, ale można zbadać `runOutputs` stan, aby uzyskać lokalizację.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Po utworzeniu dysku VHD skopiuj go w innej lokalizacji, tak szybko, jak to możliwe. Dysk VHD jest przechowywany na koncie magazynu w tymczasowej grupie zasobów utworzonej podczas przesyłania szablonu obrazu do usługi Azure Image Builder. Jeśli usuniesz szablon obrazu, utracisz dysk VHD. 
 
## <a name="next-steps"></a>Następne kroki

Istnieją przykładowe pliki .json dla różnych scenariuszy w [usłudze Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder).
 
