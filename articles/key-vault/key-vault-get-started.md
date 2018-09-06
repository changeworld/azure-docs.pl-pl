---
title: Rozpoczęcie pracy z usługą Azure Key Vault | Microsoft Docs
description: Ten samouczek ułatwi Ci rozpoczęcie pracy usługą Azure Key Vault w celu utworzenia wzmocnionego kontenera na platformie Azure do przechowywania kluczy kryptograficznych i kluczy tajnych na platformie Azure oraz zarządzania nimi.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 36721e1d-38b8-4a15-ba6f-14ed5be4de79
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2018
ms.author: barclayn
ms.openlocfilehash: 3f3adb1230d6ca6b3a7e616a0beed15d66895124
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43283001"
---
# <a name="get-started-with-azure-key-vault"></a>Rozpoczynanie pracy z usługą Azure Key Vault
Ten artykuł ułatwia rozpoczęcie pracy z usługą Azure Key Vault przy użyciu programu PowerShell i zawiera omówienie następujących działań:
- Dowiedz się, jak utworzyć kontener ze wzmocnionymi zabezpieczeniami (magazyn) na platformie Azure.
- Dowiedz się, jak przechowywać klucze kryptograficzne i wpisy tajne oraz zarządzać nimi na platformie Azure za pomocą usługi KeyVault.
- Dowiedz się, jak aplikacja może użyć tego klucza lub hasła.

Usługa Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

Instrukcje dotyczące wieloplatformowego interfejsu wiersza polecenia znajdują się w [tym równoważnym samouczku](key-vault-manage-with-cli2.md).

## <a name="requirements"></a>Wymagania
Przed przejściem do dalszej części artykułu upewnij się, że masz następujące elementy:

- **Subskrypcja platformy Azure**. Jeśli jej nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/en-us/free/).
- Program **Azure PowerShell**, **minimalna wersja 1.1.0**. Aby zainstalować program Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [Sposób instalowania i konfigurowania programu Azure PowerShell](/powershell/azure/overview). Jeśli masz już zainstalowany program Azure PowerShell, ale nie wiesz, z jakiej wersji korzystasz, w konsoli programu Azure PowerShell wpisz polecenie `(Get-Module azure -ListAvailable).Version`. Jeśli masz zainstalowany program Azure PowerShell w wersji od 0.9.1 do 0.9.8, nadal możesz korzystać tego samouczka z niewielkimi zmianami. Na przykład musisz użyć polecenia `Switch-AzureMode AzureResourceManager`. Do tego niektóre polecenia usługi Azure Key Vault uległy zmianie. Aby uzyskać listę poleceń cmdlet usługi Key Vault dla wersji od 0.9.1 do 0.9.8, zobacz [Polecenia cmdlet usługi Azure Key Vault](/powershell/module/azurerm.keyvault/#key_vault).
- **Aplikacja, którą można skonfigurować do używania usługi Key Vault**. Przykładowa aplikacja jest dostępna w [Centrum pobierania Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Aby uzyskać instrukcje, zobacz załączony plik **Readme**.

>[!NOTE]
W tym artykule przyjęto założenie, że masz podstawową wiedzą dotyczącą programu PowerShell i platformy Azure. Aby uzyskać więcej informacji na temat programu PowerShell, zobacz [Wprowadzenie do programu Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Aby uzyskać szczegółowe informacje na temat każdego polecenia cmdlet, które pojawi się w tym samouczku, użyj polecenia cmdlet **Get-Help**.

```powershell-interactive
Get-Help <cmdlet-name> -Detailed
```
    
Na przykład aby uzyskać pomoc dotyczącą polecenia cmdlet **Connect-AzureRmAccount**, wpisz polecenie:

```PowerShell
Get-Help Connect-AzureRmAccount -Detailed
```

Aby zapoznać się z modelem wdrażania usługi Azure Resource Manager w programie Azure PowerShell, możesz też przeczytać następujące artykuły:

* [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview)
* [Używanie programu Azure PowerShell z usługą Resource Manager](../powershell-azure-resource-manager.md)

## <a id="connect"></a>Nawiązywanie połączenia z subskrypcjami
Uruchom sesję programu PowerShell Azure i zaloguj się na konto platformy Azure przy użyciu następującego polecenia:  

```PowerShell
Connect-AzureRmAccount
```

>[!NOTE]
 Jeśli używasz konkretnego wystąpienia platformy Azure, użyj parametru -Environment. Na przykład: 
 ```powershell
 Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
 ```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Program Azure PowerShell pobierze wszystkie subskrypcje, które są skojarzone z tym kontem, i domyślnie użyje pierwszej.

Jeśli masz wiele subskrypcji i chcesz określić, która ma być używana przez usługę Azure Key Vault, wpisz następujące polecenie, aby zobaczyć subskrypcje przypisane do konta:

```powershell
Get-AzureRmSubscription
```

Aby określić subskrypcję, która ma być używana, wpisz polecenie:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

Aby uzyskać więcej informacji na temat konfigurowania programu Azure PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

## <a id="resource"></a>Tworzenie nowej grupy zasobów
Podczas korzystania z usługi Azure Resource Manager wszystkie powiązane zasoby są tworzone wewnątrz grupy zasobów. W tym samouczku utworzysz nową grupę zasobów o nazwie **ContosoResourceGroup**:

```powershell
New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East US'
```

## <a id="vault"></a>Tworzenie magazynu kluczy
Użyj polecenia cmdlet [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault), aby utworzyć magazyn kluczy. To polecenie cmdlet ma trzy obowiązkowe parametry: **nazwa grupy zasobów**, **nazwa magazynu kluczy** oraz **lokalizacja geograficzna**.

Używane przykładowe wartości:
- Nazwa magazynu: **ContosoKeyVault**.
- Nazwa grupy zasobów: **ContosoResourceGroup**.
- Lokalizacja: **Wschodnie stany USA**.

Wartość do wpisania:

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```
![Dane wyjściowe po ukończeniu wykonywania polecenia tworzenia magazynu kluczy](./media/key-vault-get-started/output-after-creating-keyvault.png)

Dane wyjściowe polecenia cmdlet pokazują właściwości utworzonego magazynu kluczy. Dwie najważniejsze właściwości to:

* **Nazwa magazynu**: w tym przykładzie jest to **ContosoKeyVault**. Ta nazwa będzie używana do innych poleceń cmdlet usługi Key Vault.
* **Identyfikator URI magazynu**: w tym przykładzie jest to https://contosokeyvault.vault.azure.net/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure ma teraz uprawnienia do wykonywania dowolnych operacji na tym magazynie kluczy. Nikt inny nie jest do tego upoważniony.

> [!NOTE]
> Podczas próby utworzenia nowego magazynu kluczy może zostać wyświetlony błąd **Subskrypcja nie jest zarejestrowana w celu używania przestrzeni nazw „Microsoft.KeyVault”**. W takiej sytuacji uruchom polecenie `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"`. Po pomyślnym ukończeniu rejestracji możesz ponownie uruchomić polecenie New-AzureRmKeyVault. Aby uzyskać więcej informacji, zobacz temat [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider).
>
>

## <a id="add"></a>Dodawanie klucza lub klucza tajnego do magazynu kluczy
Istnieje kilka różnych sposobów interakcji z magazynem kluczy i kluczami lub wpisami tajnymi.

### <a name="azure-key-vault-generates-a-software-protected-key"></a>Usługa Azure Key Vault generuje klucz chroniony oprogramowaniem

Jeśli chcesz, aby usługa Azure Key Vault utworzyła klucz chroniony oprogramowaniem, użyj polecenia cmdlet [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) i wpisz następujące polecenie:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'
```
Aby wyświetlić identyfikator URI dla tego klucza, wpisz polecenie:
```powershell
$key.id
```

Możesz odwoływać się do klucza, który został utworzony lub przekazany do usługi Azure Key Vault, za pomocą jego identyfikatora URI. Aby uzyskać bieżącą wersję, możesz użyć identyfikatora **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey**, a aby uzyskać tę konkretną wersję, możesz użyć identyfikatora **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**.  

### <a name="importing-an-existing-pfx-file-into-azure-key-vault"></a>Importowanie istniejącego pliku PFX do usługi Azure Key Vault

Procedura różni się w przypadku istniejących kluczy przechowywanych w pliku pfx do przekazania do usługi Azure Key Vault. Na przykład:
- Jeśli masz istniejący klucz chroniony oprogramowaniem w pliku PFX
- Plik pfx ma nazwę softkey.pfx 
- Plik jest przechowywany na dysku C.

Możesz wpisać następujące polecenie:

```powershell
$securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force  // This stores the password 123 in the variable $securepfxpwd
```

Następnie wpisz następujące polecenie, aby zaimportować klucz z pliku PFX, który chroni klucz w usłudze Key Vault przy użyciu oprogramowania:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoImportedPFX' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd
```

Aby wyświetlić identyfikator URI dla tego klucza, wpisz polecenie:

```powershell
$Key.id
```
Aby wyświetlić klucz, wpisz polecenie: 

```powershell
Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'
```
Właściwości pliku PFX w portalu są wyświetlane w formie podobnej do przedstawionej na poniższym obrazie.

![Jak wygląda certyfikat w portalu](./media/key-vault-get-started/imported-pfx.png)
### <a name="to-add-a-secret-to-azure-key-vault"></a>Aby dodać wpis tajny do usługi Azure Key Vault

Aby dodać do magazynu wpis tajny, który jest hasłem o nazwie SQLPassword i ma wartość Pa$$w0rd w usłudze Azure Key Vault, najpierw przekonwertuj wartość Pa$$w0rd na bezpieczny ciąg, wpisując następujące polecenie:

```powershell    
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

Następnie wpisz następujące polecenie:

```powershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue
```


Teraz możesz odwoływać się do hasła dodanego do usługi Azure Key Vault za pomocą jego identyfikatora URI. Użyj identyfikatora **https://ContosoVault.vault.azure.net/secrets/SQLPassword**, aby zawsze pobierać bieżącą wersję, a identyfikatora **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**, aby pobierać tę konkretną wersję.

Aby wyświetlić identyfikator URI dla tego klucza tajnego, wpisz polecenie:

```powershell
$secret.Id
```
Aby wyświetlić wpis tajny, wpisz polecenie `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`. Możesz też wyświetlić wpis tajny w portalu.

![wpis tajny](./media/key-vault-get-started/secret-value.png)

Aby wyświetlić wartość zawartą we wpisie tajnym jako zwykły tekst:
```powershell
(get-azurekeyvaultsecret -vaultName "Contosokeyvault" -name "SQLPassword").SecretValueText
```
Teraz magazyn kluczy oraz klucz lub klucz tajny jest gotowy do użycia przez aplikacje. Musisz zezwolić aplikacjom na korzystanie z nich.  

## <a id="register"></a>Rejestrowanie aplikacji w usłudze Azure Active Directory
Ten krok będzie zazwyczaj wykonywany przez programistę na innym komputerze. Nie dotyczy on tylko usługi Azure Key Vault. Aby uzyskać szczegółowe instrukcje dotyczące rejestrowania aplikacji w usłudze Azure Active Directory, przeczytaj artykuł [Integrating applications with Azure Active Directory (Integrowanie aplikacji za pomocą usługi Azure Active Directory)](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) lub [Use portal to create an Azure Active Directory application and service principal that can access resources (Tworzenie aplikacji usługi Azure Active Directory aplikacji i jednostki usługi z możliwością dostępu do zasobów)](../azure-resource-manager/resource-group-create-service-principal-portal.md)

> [!IMPORTANT]
> Aby ukończyć samouczek, Twoje konto, magazyn i aplikacja, która zostanie zarejestrowana w tym kroku, muszą należeć do tego samego katalogu Azure.


Aplikacje używające magazynu kluczy muszą zostać uwierzytelnione przy użyciu tokenu z usługi Azure Active Directory. Aby to zrobić, właściciel aplikacji musi najpierw zarejestrować ją w usłudze Azure Active Directory. Na koniec rejestracji właściciel aplikacji otrzymuje następujące wartości:

- **Identyfikator aplikacji** 
- **Klucz uwierzytelniania** (nazywany też wspólnym wpisem tajnym). 

Aby uzyskać token, aplikacja musi przedstawić obie wartości usłudze Azure Active Directory. Sposób konfiguracji aplikacji w tym celu zależy od aplikacji. W przypadku [przykładowej aplikacji usługi Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) właściciel aplikacji ustawia te wartości w pliku app.config.


Aby zarejestrować aplikację w usłudze Azure Active Directory:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie kliknij pozycję **Rejestracje aplikacji**. Jeśli nie widzisz rejestracji aplikacji, kliknij pozycję **Więcej usług** i poszukaj ich.  
>[!NOTE]
Musisz wybrać ten sam katalog, który zawiera subskrypcję platformy Azure użytą do utworzenia magazynu kluczy. 
3. Kliknij pozycję **Rejestrowanie nowej aplikacji**.
4. W bloku **Tworzenie** podaj nazwę swojej aplikacji, a następnie wybierz pozycję **APLIKACJA SIECI WEB I/LUB INTERFEJS API SIECI WEB** (ustawienie domyślne) i określ **ADRES URL LOGOWANIA** dla aplikacji internetowej. Jeśli nie masz teraz tych informacji, możesz użyć fikcyjnych danych na potrzeby tego kroku (na przykład możesz określić adres http://test1.contoso.com). Nie ma znaczenia, czy takie witryny istnieją. 

    ![Rejestrowanie nowej aplikacji](./media/key-vault-get-started/new-application-registration.png)
    >[!WARNING]
    Upewnij się, że wybrano pozycję **APLIKACJA INTENETOWA I/LUB INTERNETOWY INTERFEJS API** — w przeciwnym razie w ustawieniach nie będzie widoczna opcja **klucze**.

5. Kliknij przycisk **Utwórz**.
6. Po ukończeniu rejestracji aplikacji możesz wyświetlić listę zarejestrowanych aplikacji. Znajdź właśnie zarejestrowaną aplikację i kliknij ją.
7. Kliknij blok **Zarejestrowana aplikacja** i skopiuj **identyfikator aplikacji**
8. Kliknij pozycję **Wszystkie ustawienia**
9. W bloku **Ustawienia** kliknij pozycję **klucze**
9. Wpisz opis w polu **Opis klucza** i wybierz czas trwania, a następnie kliknij pozycję **ZAPISZ**. Strona zostanie odświeżona i pojawi się na niej wartość klucza. 
10. Informacje dotyczące **identyfikatora aplikacji** i **klucza** zostaną użyte w następnym kroku w celu ustawienia uprawnień dotyczących magazynu.

## <a id="authorize"></a>Zezwalanie aplikacji na używanie klucza lub klucza tajnego
Istnieją dwa sposoby autoryzowania aplikacji na potrzeby uzyskiwania dostępu do klucza lub wpisu tajnego w magazynie.

### <a name="using-powershell"></a>Korzystanie z programu PowerShell
Aby użyć programu PowerShell, wpisz polecenie cmdlet [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy).

Na przykład jeśli nazwa Twojego magazynu to **ContosoKeyVault**, a identyfikator klienta aplikacji, której chcesz przyznać dostęp, to 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed i chcesz zezwolić aplikacji na odszyfrowywanie oraz logowanie przy użyciu kluczy w magazynie, uruchom następujące polecenie:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Jeśli chcesz zezwolić tej samej aplikacji na odczyt kluczy tajnych w magazynie, uruchom następujące polecenie:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get
```
### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Aby zmienić autoryzację aplikacji do używania kluczy lub wpisów tajnych:
1. Wybierz opcję **Zasady dostępu** z bloku zasobów usługi Key Vault
2. Kliknij przycisk [+ Dodaj nowy] u góry bloku
3. Kliknij pozycję **Wybierz podmiot zabezpieczeń**, aby wybrać wcześniej utworzoną aplikację
4. Z listy rozwijanej **Uprawnienia klucza** wybierz opcję „Odszyfruj” i „Podpisz”, aby autoryzować aplikację do odszyfrowywania i podpisywania przy użyciu kluczy z magazynu
5. Z listy rozwijanej **Uprawnienia wpisu tajnego** wybierz opcję „Pobierz”, aby zezwolić aplikacji na odczytywanie wpisów tajnych w magazynie

## <a id="HSM"></a>Praca ze sprzętowym modułem zabezpieczeń (HSM, hardware security module)
W celu zapewnienia dodatkowego bezpieczeństwa możesz zaimportować lub wygenerować klucze w sprzętowych modułach zabezpieczeń (HSM), które nigdy nie opuszczają granicy modułów HSM. Moduły HSM są zweryfikowane w trybie FIPS 140-2 poziom 2. Jeżeli te wymagania nie odnoszą się do Ciebie, pomiń tę sekcję i przejdź do sekcji [Usuwanie magazynu kluczy oraz skojarzonych kluczy i kluczy tajnych](#delete).

Aby utworzyć te klucze chronione modułem HSM, należy użyć [usługi Azure Key Vault w warstwie Premium do obsługi kluczy chronionych modułem HSM](https://azure.microsoft.com/pricing/details/key-vault/). Ponadto warto zauważyć, że funkcja ta nie jest dostępna dla chińskiej wersji platformy Azure.

Po utworzeniu magazynu kluczy dodaj parametr **- SKU**:

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US' -SKU 'Premium'
```


Do tego magazynu kluczy możesz dodać klucze chronione oprogramowaniem (jak pokazano wcześniej) oraz klucze chronione modułem HSM. Aby utworzyć klucz chroniony modułem HSM, ustaw parametr **-Destination** w opcji „HSM”:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'
```

Następujące polecenie służy do importowania klucza z pliku PFX znajdującego się na Twoim komputerze. To polecenie importuje klucz do modułu HSM w usłudze Key Vault:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'
```

Następne polecenie importuje pakiet „Wprowadź własny klucz” (BYOK, bring your own key). Ten scenariusz umożliwia wygenerowanie własnego klucza w lokalnym module HSM i przeniesienie go do modułów HSM w usłudze Key Vault bez opuszczania przez klucz granic modułu HSM:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'
```

Aby uzyskać szczegółowe instrukcje na temat generowania pakietu BYOK, zobacz artykuł [Generowanie i przenoszenie kluczy chronionych modułem HSM dla usługi Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Usuwanie magazynu kluczy oraz skojarzonych kluczy i kluczy tajnych
Jeśli magazyn kluczy oraz zawarte w nim klucze i klucze tajne nie są już potrzebne, możesz je usunąć przy pomocy polecenia cmdlet [Remove-AzureRmKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault):

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'
```

Możesz także usunąć całą grupę zasobów platformy Azure zawierającą magazyn kluczy oraz inne zasoby, które zostały dodane do tej grupy:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'
```

## <a id="other"></a>Inne polecenia cmdlet programu Azure PowerShell
Inne polecenia, które mogą być przydatne do zarządzania usługą Azure Key Vault:

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: To polecenie wyświetla tabelaryczny widok wszystkich kluczy i wybranych właściwości.
- `$Keys[0]`: To polecenie wyświetla pełną listę właściwości dla określonego klucza
- `Get-AzureKeyVaultSecret`: To polecenie wyświetla tabelaryczny widok wszystkich nazw kluczy tajnych i wybranych właściwości.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: Przykładowy sposób usunięcia określonego klucza.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: Przykładowy sposób usunięcia określonego klucza tajnego.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać ogólne informacje na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](key-vault-whatis.md)
- Aby zobaczyć sposób użycia Twojego magazynu kluczy, zobacz artykuł [Rejestrowanie usługi Azure Key Vault](key-vault-logging.md).
- Aby zapoznać się z kolejnym samouczkiem, w którym jest używana usługa Azure Key Vault w aplikacji internetowej, zobacz artykuł [Użycie usługi Azure Key Vault z aplikacji internetowej](key-vault-use-from-web-application.md).
- Odwołania dotyczące programowania znajdują się w [przewodniku dewelopera usługi Azure Key Vault](key-vault-developers-guide.md).
- Aby zapoznać się z listą najnowszych poleceń cmdlet programu Azure PowerShell dla usługi Azure Key Vault, zobacz artykuł [Polecenia cmdlet w usłudze Azure Key Vault](/powershell/module/azurerm.keyvault/#key_vault).
