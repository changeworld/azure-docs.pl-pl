---
title: Jak utworzyć zasady konfiguracji gościa
description: Dowiedz się, jak utworzyć Azure Policy zasady konfiguracji gościa dla maszyn wirtualnych z systemem Windows lub Linux.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/20/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: fcb65e75de730178901742dc36c72776e39b044b
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71977969"
---
# <a name="how-to-create-guest-configuration-policies"></a>Jak utworzyć zasady konfiguracji gościa

Konfiguracja gościa używa modułu zasobów [Konfiguracja żądanego stanu](/powershell/dsc) (DSC) do tworzenia konfiguracji inspekcji maszyn platformy Azure. Konfiguracja DSC definiuje warunek, w którym maszyna powinna znajdować się w programie. Jeśli Ocena konfiguracji nie powiedzie się, zostanie wyzwolony efekt zasad **auditIfNotExists** i maszyna zostanie uznana za **niezgodną**.

[Azure Policy konfiguracja gościa](/azure/governance/policy/concepts/guest-configuration) może być używana tylko do inspekcji ustawień wewnątrz maszyn. Korygowanie ustawień wewnątrz maszyn nie jest jeszcze dostępne.

Aby utworzyć własną konfigurację weryfikacji stanu maszyny platformy Azure, należy wykonać następujące czynności.

> [!IMPORTANT]
> Zasady niestandardowe z konfiguracją gościa są funkcją w wersji zapoznawczej.

## <a name="add-the-guestconfiguration-resource-module"></a>Dodawanie modułu zasobów GuestConfiguration

Aby utworzyć zasady konfiguracji gościa, należy dodać moduł zasobów. Tego modułu zasobów można używać z zainstalowanym lokalnie programem PowerShell z programem [Azure Cloud Shell](https://shell.azure.com)lub z [obrazem platformy Docker Azure PowerShell](https://hub.docker.com/rsdk-powershell/).

### <a name="base-requirements"></a>Wymagania podstawowe

Moduł zasobów konfiguracji gościa wymaga następującego oprogramowania:

- Narzędzia. Jeśli nie jest jeszcze zainstalowana, wykonaj [te instrukcje](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 lub wyższy. Jeśli nie jest jeszcze zainstalowana, wykonaj [te instrukcje](/powershell/azure/install-az-ps).

### <a name="install-the-module"></a>Instalowanie modułu

Konfiguracja gościa używa modułu zasobów **GuestConfiguration** do tworzenia konfiguracji DSC i publikowania ich w celu Azure Policy:

1. W wierszu polecenia programu PowerShell uruchom następujące polecenie:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Sprawdź, czy moduł został zaimportowany:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="create-custom-guest-configuration-configuration-and-resources"></a>Utwórz niestandardową konfigurację i zasoby konfiguracji gościa

Pierwszym krokiem tworzenia zasad niestandardowych dla konfiguracji gościa jest utworzenie konfiguracji DSC. Omówienie pojęć i terminologii DSC można znaleźć w temacie [Omówienie DSC programu PowerShell](/powershell/dsc/overview/overview).

Jeśli konfiguracja wymaga tylko zasobów, które są wbudowane z instalacją agenta konfiguracji gościa, wystarczy tylko utworzyć plik MOF konfiguracji. Jeśli musisz uruchomić dodatkowy skrypt, musisz utworzyć niestandardowy moduł zasobów.

### <a name="requirements-for-guest-configuration-custom-resources"></a>Wymagania dotyczące zasobów niestandardowych konfiguracji gościa

Gdy konfiguracja gościa przeprowadza inspekcję maszyny, najpierw uruchamia `Test-TargetResource`, aby określić, czy jest w poprawnym stanie. Wartość logiczna zwrócona przez funkcję określa, czy stan Azure Resource Manager dla przypisania gościa powinien być zgodny/niezgodny. Jeśli wartość logiczna jest `$false` dla dowolnego zasobu w konfiguracji, dostawca zostanie uruchomiony `Get-TargetResource`. Jeśli wartość logiczna jest `$true`, wówczas `Get-TargetResource` nie zostanie wywołana.

Funkcja `Get-TargetResource` ma specjalne wymagania dotyczące konfiguracji gościa, która nie jest wymagana w przypadku konfiguracji żądanego stanu systemu Windows.

- Zwracana tablica skrótów musi zawierać właściwość o nazwie **powody**.
- Właściwość przyczyn musi być tablicą.
- Każdy element w tablicy powinien być tablicą skrótów z kluczami o nazwie **Code** i **phrase**.

Właściwość powody jest używana przez usługę do standaryzacji sposobu prezentowania informacji, gdy maszyna nie jest zgodna. Każdy element może być uważany za "powód", że zasób nie jest zgodny. Właściwość jest tablicą, ponieważ zasób może być niezgodny z więcej niż jedną przyczyną.

**Kod** właściwości i **frazy** są oczekiwane przez usługę. Podczas tworzenia zasobu niestandardowego Ustaw tekst (zazwyczaj stdout), który ma być pokazywany jako powód, w którym zasób nie jest zgodny jako wartość **frazy**. **Kod** ma określone wymagania dotyczące formatowania, dlatego raportowanie może jasno wyświetlić informacje o zasobie, który został użyty do przeprowadzenia inspekcji. To rozwiązanie sprawia, że konfiguracja gościa jest rozszerzalna. Każde polecenie można uruchomić w celu inspekcji maszyny, o ile dane wyjściowe mogą być przechwytywane i zwracane jako wartość ciągu dla właściwości **phrase** .

- **Kod** (ciąg): nazwa zasobu, powtórzona i krótka nazwa bez spacji jako identyfikator przyczyny. Te trzy wartości powinny być rozdzielane średnikami bez spacji.
  - Przykładem może być `registry:registry:keynotpresent`
- **Phrase** (ciąg): czytelny dla człowieka tekst objaśniający, dlaczego ustawienie nie jest zgodne.
  - Przykładem może być `The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

#### <a name="scaffolding-a-guest-configuration-project"></a>Tworzenie szkieletu projektu konfiguracji gościa

Dla deweloperów, którzy chcą skrócić proces rozpoczynania pracy i korzystania z przykładowego kodu, projekt społecznościowy o nazwie " **Projekt konfiguracji gościa** " istnieje jako szablon modułu [gips](https://github.com/powershell/plaster) PowerShell. To narzędzie może służyć do tworzenia szkieletu projektu, w tym konfiguracji roboczej i przykładowego zasobu, oraz zestawu testów [szkodników](https://github.com/pester/pester) do sprawdzania poprawności projektu. Szablon zawiera również moduły uruchamiające zadania dla Visual Studio Code do automatyzacji kompilowania i weryfikowania pakietu konfiguracji gościa. Aby uzyskać więcej informacji, zobacz [Projekt konfiguracji gościa](https://github.com/microsoft/guestconfigurationproject)projektu GitHub.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Konfiguracja niestandardowej konfiguracji gościa w systemie Linux

Konfiguracja DSC dla konfiguracji gościa w systemie Linux używa zasobu `ChefInSpecResource`, aby zapewnić aparatowi nazwę definicji [specyfikacji Chef](https://www.chef.io/inspec/) . **Nazwa** jest jedyną wymaganą właściwością zasobu.

Poniższy przykład tworzy konfigurację o nazwie **linia bazowa**, importuje moduł zasobów **GuestConfiguration** i używa zasobu `ChefInSpecResource` jako nazwy definicji specyfikacji w systemie **Linux-patch-Baseline**:

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration baseline
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    ChefInSpecResource 'Audit Linux patch baseline'
    {
        Name = 'linux-patch-baseline'
    }
}

# Compile the configuration to create the MOF files
baseline
```

Aby uzyskać więcej informacji, zobacz [Zapisywanie, kompilowanie i stosowanie konfiguracji](/powershell/dsc/configurations/write-compile-apply-configuration).

### <a name="custom-guest-configuration-configuration-on-windows"></a>Konfiguracja niestandardowej konfiguracji gościa w systemie Windows

Konfiguracja DSC Azure Policy konfiguracji gościa jest używana tylko przez agenta konfiguracji gościa, nie powoduje konfliktu z konfiguracją żądanego stanu programu Windows PowerShell.

Poniższy przykład tworzy konfigurację o nazwie **AuditBitLocker**, importuje moduł zasobów **GuestConfiguration** i używa zasobu `Service` do inspekcji dla uruchomionej usługi:

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Service 'Ensure BitLocker service is present and running'
    {
        Name = 'BDESVC'
        Ensure = 'Present'
        State = 'Running'
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

Aby uzyskać więcej informacji, zobacz [Zapisywanie, kompilowanie i stosowanie konfiguracji](/powershell/dsc/configurations/write-compile-apply-configuration).

## <a name="create-guest-configuration-custom-policy-package"></a>Utwórz niestandardowy pakiet zasad konfiguracji gościa

Po skompilowaniu pliku MOF pliki pomocnicze muszą być spakowane razem. Ukończony pakiet jest używany przez konfigurację gościa do tworzenia definicji Azure Policy. Pakiet składa się z:

- Skompilowana Konfiguracja DSC jako plik MOF
- Folder modułów
  - Moduł GuestConfiguration
  - Moduł DscNativeResources
  - System Folder z definicją Chef niespecyfikacją i dodatkową zawartością
  - Systemy Moduły zasobów DSC, które nie są wbudowane

Polecenie cmdlet `New-GuestConfigurationPackage` tworzy pakiet. Następujący format służy do tworzenia pakietu niestandardowego:

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

Parametry polecenia cmdlet `New-GuestConfigurationPackage`:

- **Nazwa**: Nazwa pakietu konfiguracji gościa.
- **Konfiguracja**: pełna ścieżka do skompilowanego dokumentu konfiguracji DSC.
- **Ścieżka**: ścieżka folderu wyjściowego. Ten parametr jest opcjonalny. Jeśli nie zostanie określony, pakiet zostanie utworzony w bieżącym katalogu.
- **ChefProfilePath**: pełna ścieżka do profilu INSPEC. Ten parametr jest obsługiwany tylko podczas tworzenia zawartości do inspekcji systemu Linux.

Ukończony pakiet musi być przechowywany w lokalizacji dostępnej dla zarządzanych maszyn wirtualnych. Przykłady obejmują repozytoria GitHub, repozytorium platformy Azure lub usługę Azure Storage. Jeśli wolisz nie udostępniać pakietu publicznie, możesz dołączyć [token sygnatury dostępu współdzielonego](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) w adresie URL. Można również zaimplementować [punkt końcowy usługi](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) dla maszyn w sieci prywatnej, chociaż ta konfiguracja ma zastosowanie tylko do uzyskiwania dostępu do pakietu i nie komunikuje się z usługą.

### <a name="working-with-secrets-in-guest-configuration-packages"></a>Praca z wpisami tajnymi w pakietach konfiguracji gościa

W Azure Policy konfiguracji gościa najlepszym sposobem zarządzania kluczami tajnymi używanymi w czasie wykonywania jest przechowywanie ich w Azure Key Vault. Ten projekt jest implementowany w ramach niestandardowych zasobów DSC.

1. Najpierw utwórz tożsamość zarządzaną przypisaną przez użytkownika na platformie Azure.

   Tożsamość jest używana przez maszyny do uzyskiwania dostępu do wpisów tajnych przechowywanych w Key Vault. Aby uzyskać szczegółowe instrukcje, zobacz [Tworzenie, wyświetlanie listy lub usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure PowerShell](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

1. Utwórz wystąpienie Key Vault.

   Aby uzyskać szczegółowe instrukcje, zobacz [Ustawianie i pobieranie wpisu tajnego — PowerShell](../../../key-vault/quick-create-powershell.md).
   Przypisz uprawnienia do wystąpienia, aby przyznać tożsamości przypisanej użytkownikowi dostęp do wpisów tajnych przechowywanych w Key Vault. Aby uzyskać szczegółowe instrukcje, zobacz [Ustawianie i pobieranie wpisu tajnego platformy .NET](../../../key-vault/quick-create-net.md#give-the-service-principal-access-to-your-key-vault).

1. Przypisz do komputera tożsamość przypisaną przez użytkownika.

   Aby uzyskać szczegółowe instrukcje, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md#user-assigned-managed-identity).
   Na dużą skalę Przypisz tę tożsamość przy użyciu Azure Resource Manager za pośrednictwem Azure Policy. Aby uzyskać szczegółowe instrukcje, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu szablonu](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#assign-a-user-assigned-managed-identity-to-an-azure-vm).

1. Na koniec w ramach zasobu niestandardowego Użyj identyfikatora klienta wygenerowanego powyżej, aby uzyskać dostęp do Key Vault przy użyciu tokenu dostępnego na komputerze.

   @No__t-0 i adres URL do wystąpienia Key Vault można przesłać do zasobu jako [Właściwości](/powershell/dsc/resources/authoringresourcemof#creating-the-mof-schema) , więc nie trzeba aktualizować zasobu dla wielu środowisk lub należy zmienić wartości.

Następujący przykładowy kod może być używany w zasobie niestandardowym w celu pobierania wpisów tajnych z Key Vault przy użyciu tożsamości przypisanej do użytkownika. Wartość zwrócona przez żądanie do Key Vault jest zwykłym tekstem. Najlepszym rozwiązaniem jest zapisanie go w ramach obiektu Credential.

```azurepowershell-interactive
# the following values should be input as properties
$client_id = 'e3a78c9b-4dd2-46e1-8bfa-88c0574697ce'
$keyvault_url = 'https://keyvaultname.vault.azure.net/secrets/mysecret'

$access_token = ((Invoke-WebRequest -Uri "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=$client_id&resource=https%3A%2F%2Fvault.azure.net" -Method GET -Headers @{Metadata='true'}).Content | ConvertFrom-Json).access_token

$value = ((Invoke-WebRequest -Uri $($keyvault_url+'?api-version=2016-10-01') -Method GET -Headers @{Authorization="Bearer $access_token"}).content | convertfrom-json).value |  ConvertTo-SecureString -asplaintext -force

$credential = New-Object System.Management.Automation.PSCredential('secret',$value)
```

## <a name="test-a-guest-configuration-package"></a>Testowanie pakietu konfiguracji gościa

Po utworzeniu pakietu konfiguracyjnego, ale przed opublikowaniem go na platformie Azure, można przetestować funkcjonalność pakietu ze stacji roboczej lub środowiska CI/CD. Moduł GuestConfiguration zawiera polecenie cmdlet `Test-GuestConfigurationPackage` ładujące tego samego agenta w środowisku deweloperskim, które jest używane wewnątrz maszyn platformy Azure. Korzystając z tego rozwiązania, można przeprowadzić testowanie integracji lokalnie przed wydaniem do rozliczanego testu/pytań i odpowiedzi/środowisk produkcyjnych.

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

Parametry polecenia cmdlet `Test-GuestConfigurationPackage`:

- **Nazwa**: Nazwa zasad konfiguracji gościa.
- **Parameter**: parametry zasad podane w formacie Hashtable.
- **Ścieżka**: pełna ścieżka pakietu konfiguracji gościa.

Polecenie cmdlet obsługuje również dane wejściowe z potoku programu PowerShell. Potok danych wyjściowych polecenia cmdlet `New-GuestConfigurationPackage` można wykonać przy użyciu polecenia cmdlet `Test-GuestConfigurationPackage`.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

Więcej informacji o testowaniu z parametrami znajduje się w sekcji poniżej [przy użyciu parametrów w niestandardowych zasadach konfiguracji gościa](/azure/governance/policy/how-to/guest-configuration-create#using-parameters-in-custom-guest-configuration-policies).

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>Tworzenie plików wdrożenia definicji Azure Policy i inicjatywy

Gdy niestandardowy pakiet zasad konfiguracji gościa został utworzony i przekazany do lokalizacji dostępnej dla maszyn, Utwórz definicję zasad konfiguracji gościa dla Azure Policy. Polecenie cmdlet `New-GuestConfigurationPolicy` przyjmuje publicznie dostępny pakiet zasad konfiguracji gościa i tworzy definicję zasad **auditIfNotExists** i **deployIfNotExists** . Zostanie również utworzona definicja inicjatywy zasad, która obejmuje zarówno definicje zasad.

Poniższy przykład tworzy definicje zasad i inicjatyw w określonej ścieżce z pakietu zasad niestandardowych konfiguracji gościa dla systemu Windows i zawiera nazwę, opis i wersję:

```azurepowershell-interactive
New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

Parametry polecenia cmdlet `New-GuestConfigurationPolicy`:

- **ContentUri**: publiczny identyfikator URI http (s) dla pakietu zawartości konfiguracji gościa.
- **DisplayName**: Nazwa wyświetlana zasad.
- **Opis**: Opis zasad.
- **Parameter**: parametry zasad podane w formacie Hashtable.
- **Wersja**: wersja zasad.
- **Ścieżka**: ścieżka docelowa, w której są tworzone definicje zasad.
- **Platforma**: platforma docelowa (Windows/Linux) dla zasad konfiguracji gościa i pakietu zawartości.

Następujące pliki są tworzone przez `New-GuestConfigurationPolicy`:

- **auditIfNotExists. JSON**
- **deployIfNotExists. JSON**
- **Initiative. JSON**

Dane wyjściowe polecenia cmdlet zwracają obiekt zawierający nazwę wyświetlaną inicjatywy i ścieżkę plików zasad.

Jeśli chcesz użyć tego polecenia do tworzenia szkieletu niestandardowego projektu zasad, możesz wprowadzić zmiany w tych plikach. Przykładem może być modyfikacja sekcji "If", aby sprawdzić, czy określony tag jest obecny dla maszyn. Aby uzyskać szczegółowe informacje na temat tworzenia zasad, zobacz programowe [Tworzenie zasad](./programmatically-create.md).

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Używanie parametrów w niestandardowych zasadach konfiguracji gościa

Konfiguracja gościa obsługuje Zastępowanie właściwości konfiguracji w czasie wykonywania. Ta funkcja oznacza, że wartości w pliku MOF w pakiecie nie muszą być uznawane za statyczne. Wartości przesłonięć są udostępniane za pomocą Azure Policy i nie mają wpływu na sposób tworzenia lub kompilowania konfiguracji.

Polecenia cmdlet `New-GuestConfigurationPolicy` i `Test-GuestConfigurationPolicyPackage` zawierają parametr o nazwie **Parameters**. Ten parametr przyjmuje definicję obiektu Hashtable obejmującą wszystkie szczegóły każdego z parametrów i automatycznie tworzy wszystkie wymagane sekcje plików użytych do utworzenia każdej definicji Azure Policy.

Poniższy przykład tworzy Azure Policy do inspekcji usługi, gdzie użytkownik wybiera z listy usług w momencie przypisywania zasad.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

W przypadku zasad systemu Linux Uwzględnij Właściwość **AttributesYmlContent** w konfiguracji i Zastąp odpowiednie wartości. Agent konfiguracji gościa automatycznie tworzy plik YaML używany przez specyfikację do przechowywania atrybutów. Zobacz Poniższy przykład.

```azurepowershell-interactive
Configuration FirewalldEnabled {

    Import-DscResource -ModuleName 'GuestConfiguration'

    Node FirewalldEnabled {

        ChefInSpecResource FirewalldEnabled {
            Name = 'FirewalldEnabled'
            AttributesYmlContent = "DefaultFirewalldProfile: [public]"
        }
    }
}
```

Dla każdego dodatkowego parametru Dodaj tablicę skrótów do tablicy. W plikach zasad zostaną wyświetlone właściwości dodawane do konfiguracji gościa ConfigurationName, które identyfikują typ zasobu, nazwę, właściwość i wartość.

```json
{
    "apiVersion": "2018-11-20",
    "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
    "name": "[concat(parameters('vmName'), '/Microsoft.GuestConfiguration/', parameters('configurationName'))]",
    "location": "[parameters('location')]",
    "properties": {
        "guestConfiguration": {
            "name": "[parameters('configurationName')]",
            "version": "1.*",
            "configurationParameter": [{
                "name": "[Service]windowsService;Name",
                "value": "[parameters('ServiceName')]"
            }]
        }
    }
}
```

## <a name="publish-to-azure-policy"></a>Publikuj w Azure Policy

Moduł zasobów **GuestConfiguration** oferuje sposób tworzenia definicji zasad i definicji inicjatywy na platformie Azure z jednym krokiem za pomocą polecenia cmdlet `Publish-GuestConfigurationPolicy`.
Polecenie cmdlet ma tylko parametr **Path** wskazujący lokalizację trzech plików JSON utworzonych przez `New-GuestConfigurationPolicy`.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

Polecenie cmdlet `Publish-GuestConfigurationPolicy` akceptuje ścieżkę z potoku programu PowerShell. Ta funkcja oznacza, że można tworzyć pliki zasad i publikować je w pojedynczym zestawie poleceń potokowych.

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

W przypadku definicji zasad i inicjatyw utworzonych na platformie Azure ostatni krok to przypisanie inicjatywy. Zobacz, jak przypisać inicjatywę przy użyciu [portalu](../assign-policy-portal.md), [interfejsu wiersza polecenia platformy Azure](../assign-policy-azurecli.md)i [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Zasady konfiguracji gościa muszą być **zawsze** przypisywane przy użyciu inicjatywy, która łączy zasady _AuditIfNotExists_ i _DeployIfNotExists_ . Jeśli tylko zasady _AuditIfNotExists_ są przypisane, wymagania wstępne nie są wdrażane, a zasady zawsze pokazują, że serwery "0" są zgodne.

## <a name="policy-lifecycle"></a>Cykl życia zasad

Po opublikowaniu niestandardowego Azure Policy przy użyciu niestandardowego pakietu zawartości istnieją dwa pola, które należy zaktualizować, jeśli chcesz opublikować nową wersję.

- **Wersja**: po uruchomieniu polecenia cmdlet `New-GuestConfigurationPolicy` należy określić numer wersji większy niż aktualnie opublikowany. Właściwość aktualizuje wersję przypisania konfiguracji gościa w nowym pliku zasad, więc rozszerzenie rozpozna, że pakiet został zaktualizowany.
- **contentHash**: Ta właściwość jest automatycznie aktualizowana przez polecenie cmdlet `New-GuestConfigurationPolicy`. Jest to wartość skrótu pakietu utworzonego przez `New-GuestConfigurationPackage`. Właściwość musi być poprawna dla publikowanego pliku `.zip`. Jeśli zostanie zaktualizowana tylko właściwość **contentUri** , na przykład w przypadku, gdy ktoś mógłby wprowadzić ręczną zmianę definicji zasad z portalu, rozszerzenie nie zaakceptuje pakietu zawartości.

Najprostszym sposobem zwolnienia zaktualizowanego pakietu jest powtórzenie procesu opisanego w tym artykule i udostępnienie zaktualizowanego numeru wersji. Ten proces gwarantuje, że wszystkie właściwości zostały prawidłowo zaktualizowane.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Konwertowanie zawartości systemu Windows zasady grupy na konfigurację Azure Policy gościa

Konfiguracja gościa podczas inspekcji maszyn z systemem Windows jest implementacją składni konfiguracji żądanego stanu programu PowerShell. Społeczność DSC opublikowała narzędzia do konwertowania wyeksportowanych szablonów zasady grupy w formacie DSC. Korzystając z tego narzędzia wraz z poleceniami cmdlet konfiguracji gościa opisanymi powyżej, można skonwertować zawartość systemu Windows zasady grupy i pakiet/opublikować ją Azure Policy do inspekcji. Aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia, zobacz artykuł [Szybki Start: konwertowanie zasady grupy na DSC](/powershell/dsc/quickstarts/gpo-quickstart).
Po przeprowadzeniu konwersji zawartości należy wykonać kroki opisane powyżej, aby utworzyć pakiet i opublikować go jako Azure Policy będzie taka sama jak w przypadku dowolnej zawartości DSC.

## <a name="optional-signing-guest-configuration-packages"></a>OPCJONALNE: podpisywanie pakietów konfiguracji gościa

Zasady niestandardowe konfiguracji gościa domyślnie używają skrótu SHA256, aby sprawdzić, czy pakiet zasad nie został zmieniony z momentu, gdy został on opublikowany przez serwer, który jest poddany inspekcji.
Opcjonalnie klienci mogą również używać certyfikatu do podpisywania pakietów i wymuszania rozszerzenia konfiguracji gościa, aby zezwalać tylko na podpisaną zawartość.

Aby włączyć ten scenariusz, należy wykonać dwa kroki. Uruchom polecenie cmdlet, aby podpisać pakiet zawartości, i Dołącz tag do maszyn, które powinny wymagać podpisania kodu.

Aby skorzystać z funkcji walidacji podpisu, uruchom polecenie cmdlet `Protect-GuestConfigurationPackage`, aby podpisać pakiet przed jego opublikowaniem. To polecenie cmdlet wymaga certyfikatu "podpisywanie kodu".

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parametry polecenia cmdlet `Protect-GuestConfigurationPackage`:

- **Ścieżka**: pełna ścieżka pakietu konfiguracji gościa.
- **Certyfikat**: certyfikat podpisywania kodu w celu podpisania pakietu. Ten parametr jest obsługiwany tylko podczas podpisywania zawartości dla systemu Windows.
- **PrivateGpgKeyPath**: Private GPG Key Path. Ten parametr jest obsługiwany tylko w przypadku podpisywania zawartości dla systemu Linux.
- **PublicGpgKeyPath**: Public GPG Key Path. Ten parametr jest obsługiwany tylko w przypadku podpisywania zawartości dla systemu Linux.

Agent GuestConfiguration oczekuje obecności klucza publicznego certyfikatu w "zaufanych urzędach głównych certyfikatów" na maszynach z systemem Windows i w ścieżce `/usr/local/share/ca-certificates/extra` na maszynach z systemem Linux. Aby węzeł mógł zweryfikować podpisaną zawartość, przed zastosowaniem zasad niestandardowych Zainstaluj klucz publiczny certyfikatu na komputerze. Ten proces można wykonać przy użyciu dowolnej techniki wewnątrz maszyny wirtualnej lub za pomocą Azure Policy. Przykładowy szablon jest [dostępny tutaj](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Zasady dostępu Key Vault muszą zezwalać dostawcy zasobów obliczeniowych na dostęp do certyfikatów podczas wdrożeń. Aby uzyskać szczegółowe instrukcje, zobacz [konfigurowanie Key Vault dla maszyn wirtualnych w programie Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Poniżej znajduje się przykład eksportowania klucza publicznego z certyfikatu podpisywania w celu zaimportowania go do maszyny.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Odpowiednie informacje na temat tworzenia kluczy GPG do użycia z maszynami z systemem Linux są dostępne w artykule w witrynie GitHub, [generując nowy klucz GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

Po opublikowaniu zawartości Dodaj tag o nazwie `GuestConfigPolicyCertificateValidation` i Value `enabled` do wszystkich maszyn wirtualnych, na których należy podać podpisywanie kodu. Ten tag można dostarczyć na dużą skalę przy użyciu Azure Policy. Zapoznaj się z przykładem [Zastosuj tag i jego wartość domyślną](../samples/apply-tag-default-value.md) . Po zastosowaniu tego tagu definicja zasad wygenerowana przy użyciu polecenia cmdlet `New-GuestConfigurationPolicy` włącza wymaganie za pośrednictwem rozszerzenia konfiguracji gościa.

## <a name="preview-troubleshooting-guest-configuration-policy-assignments"></a>PRZEGLĄDANIA Rozwiązywanie problemów z przypisaniami zasad konfiguracji gościa

Narzędzie jest dostępne w wersji zapoznawczej, aby pomóc w rozwiązywaniu problemów z przypisaniami konfiguracji gościa Azure Policy. Narzędzie jest w wersji zapoznawczej i zostało opublikowane w Galeria programu PowerShell jako nazwa modułu [Narzędzia do rozwiązywania problemów z konfiguracją gościa](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Aby uzyskać więcej informacji na temat poleceń cmdlet w tym narzędziu, użyj polecenia Get-Help w programie PowerShell, aby wyświetlić wbudowane wskazówki. Ponieważ narzędzie pobiera częste aktualizacje, to najlepszy sposób uzyskiwania najnowszych informacji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat inspekcji maszyn wirtualnych z [konfiguracją gościa](../concepts/guest-configuration.md).
- Dowiedz się, jak [programowo utworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](getting-compliance-data.md).