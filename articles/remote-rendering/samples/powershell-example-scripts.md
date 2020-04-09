---
title: Przykładowe skrypty programu PowerShell
description: Przykłady, które pokazują, jak korzystać z frontu za pomocą skryptów programu PowerShell
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: c45d2fc34ccbab6d813f12563678d036f9f35753
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891496"
---
# <a name="example-powershell-scripts"></a>Przykładowe skrypty programu PowerShell

Renderowanie zdalne platformy Azure udostępnia następujące dwa interfejsy API REST:

- [Interfejs API REST konwersji](../how-tos/conversion/conversion-rest-api.md)
- [Interfejs API REST sesji](../how-tos/session-rest-api.md)

[Repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering) zawiera przykładowe skrypty w folderze *Skrypty* do interakcji z interfejsami API REST usługi. W tym artykule opisano ich użycie.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać przykładowe skrypty, potrzebujesz funkcjonalnej konfiguracji [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

1. Zainstaluj moduł Azure PowerShell:
    1. Otwieranie programu PowerShell z prawami administratora
    1. Uruchomić:`Install-Module -Name Az -AllowClobber`

1. Jeśli pojawia się błąd dotyczący uruchamiania skryptów, upewnij się, że [zasady wykonywania](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) są odpowiednio ustawione:
    1. Otwieranie programu PowerShell z prawami administratora
    1. Uruchomić:`Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [Przygotowywanie konta usługi Azure Storage](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Zaloguj się do subskrypcji zawierającej konto zdalnego renderowania platformy Azure:
    1. Otwieranie programu PowerShell
    1. Uruchom: `Connect-AzAccount` i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie.

> [!NOTE]
> W przypadku, gdy organizacja ma więcej niż jedną subskrypcję, może być konieczne określenie argumentów Identyfikator subskrypcji i Dzierżawa. Szczegółowe informacje można znaleźć w [dokumentacji Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

1. Pobierz folder *Skrypty* z [repozytorium GithHub renderowania zdalnego azure](https://github.com/Azure/azure-remote-rendering).

## <a name="configuration-file"></a>Plik konfiguracji

Obok `.ps1` plików znajduje `arrconfig.json` się, który należy wypełnić:

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> Upewnij się, że prawidłowo wyciął ukośniki w ścieżce LocalAssetDirectoryPath przy użyciu podwójnych ukośników odwrotnych: "\\\\" i użyć ukośników "/" we wszystkich innych ścieżkach, takich jak inputFolderPath i inputAssetPath.

### <a name="accountsettings"></a>kontaSettings

Dla `arrAccountId` `arrAccountKey`i , zobacz [Tworzenie konta zdalnego renderowania platformy Azure](../how-tos/create-an-account.md).
Aby `region` zapoznać się [z listą dostępnych regionów](../reference/regions.md).

### <a name="renderingsessionsettings"></a>renderingSessionSettings

Ta struktura musi być wypełniona, jeśli chcesz uruchomić **RenderingSession.ps1**.

- **vmSize:** Wybiera rozmiar maszyny wirtualnej. Wybierz *standard* lub *premię*. Zamknij sesje renderowania, gdy nie są już potrzebne.
- **maxLeaseTime:** Czas trwania, dla którego chcesz dzierżawić maszynę wirtualną. Zostanie on zamknięty po wygaśnięciu dzierżawy. Czas dzierżawy można przedłużyć później (patrz poniżej).

### <a name="assetconversionsettings"></a>aktywaKonwersjeStytucje

Ta struktura musi być wypełniona, jeśli chcesz uruchomić **Conversion.ps1**.

Aby uzyskać szczegółowe informacje, zobacz [Przygotowywanie konta usługi Azure Storage](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts).

## <a name="script-renderingsessionps1"></a>Skrypt: RenderingSession.ps1

Ten skrypt jest używany do tworzenia, wykonywania zapytań i zatrzymywania sesji renderowania.

> [!IMPORTANT]
> Upewnij się, że wypełniłeś *sekcje kontSettings* i *renderingSessionSettings* w pliku arrconfig.json.

### <a name="create-a-rendering-session"></a>Tworzenie sesji renderowania

Normalne użycie z w pełni wypełnionym arrconfig.json:

```PowerShell
.\RenderingSession.ps1
```

Skrypt wywoła [interfejs API REST zarządzania sesjami,](../how-tos/session-rest-api.md) aby rozkręcić renderowanie maszyny Wirtualnej z określonymi ustawieniami. Po sukcesie zostanie pobrana *sessionId*. Następnie sonduje właściwości sesji, dopóki sesja nie będzie gotowa lub wystąpił błąd.

Aby użyć alternatywnego pliku **konfiguracyjnego:**

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Poszczególne **ustawienia** można zastąpić z pliku konfiguracyjnego:

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Aby rozpocząć sesję tylko **bez sondowania,** można użyć:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

Identyfikator *sessionId,* który pobiera skrypt, musi być przekazany do większości innych poleceń sesji.

### <a name="retrieve-session-properties"></a>Pobieranie właściwości sesji

Aby uzyskać właściwości sesji, uruchom:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

Służy `-Poll` do oczekiwania, aż sesja jest *gotowa* lub wystąpił błąd.

### <a name="list-active-sessions"></a>Lista aktywnych sesji

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>Zatrzymywać sesję

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>Zmienianie właściwości sesji

W tej chwili obsługujemy tylko zmianę maxLeaseTime sesji.

> [!NOTE]
> Czas dzierżawy jest zawsze liczony od momentu utworzenia maszyny wirtualnej sesji. Tak więc, aby przedłużyć dzierżawę sesji o kolejną godzinę, zwiększ *maxLeaseTime* o jedną godzinę.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Skrypt: Conversion.ps1

Ten skrypt służy do konwertowania modeli wejściowych do formatu określonego środowiska uruchomieniowego renderowania zdalnego platformy Azure.

> [!IMPORTANT]
> Upewnij się, że wypełniłeś *sekcje kontSettings* i *assetConversionSettings* w pliku arrconfig.json.

Skrypt pokazuje dwie opcje używania kont magazynu w usłudze:

- Konto Magazynu połączone z kontem zdalnego renderowania platformy Azure
- Zapewnienie dostępu do pamięci masowej za pośrednictwem sygnatur dostępu współdzielonego (SAS)

### <a name="linked-storage-account"></a>Połączone konto magazynu

Po pełnym wypełnieniu arrconfig.json i połączeniu konta magazynu można użyć następującego polecenia. Łączenie konta magazynu opisano w witrynie [Utwórz konto](../how-tos/create-an-account.md#link-storage-accounts).

Korzystanie z połączonego konta magazynu jest preferowanym sposobem korzystania z usługi konwersji, ponieważ nie ma potrzeby generowania podpisów dostępu współdzielonego.

```PowerShell
.\Conversion.ps1
```

1. Prześlij wszystkie `assetConversionSettings.modelLocation` pliki zawarte w kontenerze wejściowego obiektu blob pod danym`inputFolderPath`
1. Wywołanie [interfejsu API REST konwersji modelu](../how-tos/conversion/conversion-rest-api.md) w celu rozpoczęcia [konwersji modelu](../how-tos/conversion/model-conversion.md)
1. Sonduj stan konwersji, aż konwersja zakończy się pomyślnie lub nie powiodła się
1. Szczegóły danych wyjściowych przekonwertowanego pliku (konto magazynu, kontener wyjściowy, ścieżka pliku w kontenerze)

### <a name="access-to-storage-via-shared-access-signatures"></a>Dostęp do magazynu za pośrednictwem podpisów dostępu współdzielonego

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Będzie to:

1. Przekazywanie pliku lokalnego `assetConversionSettings.localAssetDirectoryPath` z kontenera wejściowego obiektu blob
1. Generowanie identyfikatora URI sygnatury dostępu Współdzielonego dla kontenera wejściowego
1. Generowanie identyfikatora URI sygnatury dostępu Współdzielonego dla kontenera wyjściowego
1. Wywołanie [interfejsu API REST konwersji modelu](../how-tos/conversion/conversion-rest-api.md) w celu rozpoczęcia [konwersji modelu](../how-tos/conversion/model-conversion.md)
1. Sonduj stan konwersji, aż konwersja zakończy się pomyślnie lub nie powiodła się
1. Szczegóły danych wyjściowych przekonwertowanego pliku (konto magazynu, kontener wyjściowy, ścieżka pliku w kontenerze)
1. Wyprowadzanie identyfikatora URI sygnatury dostępu Współdzielonego do przekonwertowanego modelu w wyjściowym kontenerze obiektów blob

### <a name="additional-command-line-options"></a>Dodatkowe opcje wiersza polecenia

Aby użyć alternatywnego pliku **konfiguracyjnego:**

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Aby **rozpocząć konwersję modelu tylko bez sondowania,** możesz użyć:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

Poszczególne **ustawienia** można zastąpić z pliku konfiguracyjnego za pomocą następujących przełączników wiersza polecenia:

* **Identyfikator:** ConversionId używany z GetConversionStatus
* **ArrAccountId:** arrAccountId of accountSettings ArrAccountId: arrAccountId of accountSettings ArrAccountId
* **ArrAccountKey:** zastępowanie arrAccountKey kontSettings
* **Region:** zastępowanie regionu kontZalięki
* **ResourceGroup:** zastępowanie zasobówGrupa zasobówKonwersje
* **StorageAccountName:** zastępowanie magazynuName zasobówConversionSettings
* **Nazwa pliku BlobInputContainerName:** zastępowanie dla obiektu blobInputContainer zasobówConversionSettings
* **LocalAssetDirectoryPath:** zastępowanie dla localAssetDirectoryPath zasobówConversionSettings
* **InputAssetPath:** zastępowanie dla inputAssetPath zasobówConversionSettings
* **Nazwa:** zastępowanie dla obiektu blobOutputContainerName of assetConversionSettings
* **OutputFolderPath:** zastępowanie danych wyjściowychFolderPath assetConversionSettings
* **OutputAssetFileName:** zastępowanie danych wyjściowychAssetFileName zasobówConversionSettings

Na przykład można połączyć kilka podanych opcji, takich jak:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>Uruchamianie poszczególnych etapów konwersji

Jeśli chcesz uruchomić poszczególne etapy procesu, możesz użyć:

Przekazywanie tylko danych z podanej ścieżki localassetdirectorypath

```PowerShell
.\Conversion.ps1 -Upload
```

Rozpocznij proces konwersji modelu już przesłanego do magazynu obiektów blob (nie uruchamiaj przesyłania, nie sonduj stanu konwersji) Skrypt zwróci *identyfikator konwersji*.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

Możesz też odzyskać stan konwersji tej konwersji za pomocą:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

Należy `-Poll` zaczekać, aż konwersja zostanie wykonana lub wystąpił błąd.

## <a name="next-steps"></a>Następne kroki

- [Szybki start: renderowanie modelu za pomocą unity](../quickstarts/render-model.md)
- [Szybki start: konwertowanie modelu do renderowania](../quickstarts/convert-model.md)
- [Konwersja modelu](../how-tos/conversion/model-conversion.md)
