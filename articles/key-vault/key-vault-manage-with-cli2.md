---
title: Zarządzanie usługi Azure Key Vault przy użyciu interfejsu wiersza polecenia — usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: Skorzystaj z tego artykułu w celu automatyzacji typowych zadań w usłudze Key Vault przy użyciu wiersza polecenia platformy Azure
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: barclayn
ms.openlocfilehash: d7d76458601b2afecafc1313e334215bf08b6545
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64713826"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Zarządzanie Key Vault przy użyciu wiersza polecenia platformy Azure 

W tym artykule opisano, jak rozpocząć pracę z usługą Azure Key Vault, przy użyciu wiersza polecenia platformy Azure.  Informacje są widoczne na:

- Jak utworzyć kontener ze wzmocnionymi zabezpieczeniami (magazyn) na platformie Azure
- Dodawanie klucza, hasło lub certyfikat do magazynu kluczy
- Rejestrowanie aplikacji w usłudze Azure Active Directory
- Autoryzowanie aplikacji na używanie klucza lub klucza tajnego
- Ustawienia magazynu kluczy, zaawansowane zasady dostępu
- Praca z sprzętowych modułów zabezpieczeń (HSM)
- Usuwanie magazynu kluczy oraz skojarzonych kluczy i wpisów tajnych
- Polecenia różne interfejsu wiersza polecenia platformy Azure dla wielu Platform


Usługa Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Ten artykuł nie zawiera instrukcji dotyczących pisania aplikacji platformy Azure, że jeden z kroków zawiera, który pokazuje, jak Autoryzuj aplikację do używania klucza lub klucza tajnego w magazynie kluczy.
>

Omówienie usługi Azure Key Vault, zobacz [co to jest usługa Azure Key Vault?](key-vault-whatis.md)
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć polecenia wiersza polecenia platformy Azure, w tym artykule, musi mieć następujące elementy:

* Subskrypcja platformy Microsoft Azure. Jeśli go nie masz, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial).
* Interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby zainstalować najnowszą wersję, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Aplikacja, która zostanie skonfigurowana do używania klucza lub hasła utworzonego w tym artykule. Przykładowa aplikacja jest dostępna w [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=45343). Aby uzyskać instrukcje zobacz dołączony plik Readme.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Uzyskiwanie pomocy przy użyciu interfejsu wiersza polecenia platformy Azure dla wielu Platform

W tym artykule założono, że jesteś zaznajomiony z interfejsu wiersza polecenia (powłoki Bash, Terminal, wiersza polecenia).

--H lub help parametru należy używać, aby wyświetlić Pomoc dla określonego polecenia. Alternatywnie Azure help [polecenie], że format [opcje] może również służyć za. W razie wątpliwości dotyczące parametrów wymaganych za pomocą polecenia, zobacz Pomoc. Na przykład poniższe polecenia, wszystkie zwracają te same informacje:

```azurecli
az account set --help
az account set -h
```

Można również przeczytać następujące artykuły, aby zapoznać się z usługą Azure Resource Manager w użyciu interfejsu wiersza polecenia dla wielu Platform Azure:

* [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
* [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Jak utworzyć kontener ze wzmocnionymi zabezpieczeniami (magazyn) na platformie Azure

Magazyny zabezpieczonych kontenerów, wspierane przez sprzętowe moduły zabezpieczeń. Magazyny zmniejszają prawdopodobieństwo przypadkowej utraty danych zabezpieczeń, stanowiąc centrum przechowywania wpisów tajnych aplikacji. Magazyny usługi Key Vault umożliwiają także kontrolowanie i rejestrowanie dostępu do wszelkich elementów, które są w nich przechowywane. Usługa Azure Key Vault obsługuje żądania i odnawianie certyfikatów protokołu TLS (Transport Layer Security), udostępniając funkcje wymagane przez niezawodne rozwiązania do zarządzania cyklem życia certyfikatu. W następnych krokach utworzysz magazyn.

### <a name="connect-to-your-subscriptions"></a>Nawiązywanie połączenia z subskrypcjami

Logujesz się interaktywnie, użyj następującego polecenia:

```azurecli
az login
```
Aby zalogować się przy użyciu konta organizacji, możesz przekazać nazwę użytkownika i hasło.

```azurecli
az login -u username@domain.com -p password
```

Jeśli masz więcej niż jedną subskrypcję i w związku z tym należy określić, której mają zostać użyte, wpisz następujące polecenie, aby zobaczyć subskrypcje dla konta:

```azurecli
az account list
```

Określ subskrypcję za pomocą parametru subskrypcji.

```azurecli
az account set --subscription <subscription name or ID>
```

Aby uzyskać więcej informacji na temat konfigurowania interfejsu wiersza polecenia dla wielu Platform, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Utworzenie nowej grupy zasobów

Korzystając z usługi Azure Resource Manager, wszystkie powiązane zasoby są tworzone wewnątrz grupy zasobów. Można utworzyć magazyn kluczy w istniejącej grupie zasobów. Jeśli chcesz użyć nowej grupy zasobów, możesz utworzyć nowy.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

Pierwszy parametr jest nazwa grupy zasobów, a drugi parametr jest lokalizacji. Aby uzyskać listę wszystkich możliwych wpisz lokalizacje:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Zarejestruj dostawcę zasobów usługi Key Vault

 Może zostać wyświetlony błąd "subskrypcja nie jest zarejestrowana do korzystania z przestrzeni nazw"Microsoft.KeyVault"" podczas próby utworzenia nowego magazynu kluczy. Jeśli pojawi się ten komunikat, upewnij się, że dostawca zasobów tej usługi Key Vault jest zarejestrowany w ramach subskrypcji. Jest to jednorazowa operacja dla każdej subskrypcji.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Użyj `az keyvault create` polecenie, aby utworzyć magazyn kluczy. Ten skrypt ma trzy obowiązkowe parametry: Nazwa grupy zasobów, nazwę magazynu kluczy i lokalizacji geograficznej.

Aby utworzyć nowy magazyn o nazwie **ContosoKeyVault**, w grupie zasobów **ContosoResourceGroup**, przechowywanych w **Azja Wschodnia** lokalizacji, wpisz: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

Dane wyjściowe tego polecenia pokazują właściwości magazynu kluczy, który został utworzony. Dwie najważniejsze właściwości to:

* **name**: W tym przykładzie nazwa to ContosoKeyVault. Użyjesz tej nazwy dla innych poleceń usługi Key Vault.
* **vaultUri**: W tym przykładzie identyfikator URI jest https://contosokeyvault.vault.azure.net. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure ma teraz uprawnienia do wykonywania dowolnych operacji na tym magazynie kluczy. Począwszy od jeszcze nikt inny nie ma autoryzacji.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Dodawanie klucza, hasło lub certyfikat do magazynu kluczy

Jeśli chcesz, aby usługa Azure Key Vault utworzyła klucz chroniony przez oprogramowanie, użyj `az key create` polecenia.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Jeśli masz istniejący klucz w pliku PEM, można przesłać go do usługi Azure Key Vault. Istnieje możliwość ochrony klucza przy użyciu oprogramowania lub sprzętowego modułu zabezpieczeń. W tym przykładzie importowania klucza z pliku PEM i chronić go za pomocą oprogramowania przy użyciu hasła "hVFkk965BuUv":

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Teraz możesz odwoływać się klucz, który został utworzony lub przekazany do usługi Azure Key Vault za pomocą jego identyfikatora URI. Użyj **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** można zawsze uzyskać bieżącą wersję. Użyj https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] Aby uzyskać tę konkretną wersję. Na przykład **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** . 

Dodawanie wpisu tajnego w magazynie, który jest hasłem o nazwie SQLPassword i ma wartość "hVFkk965BuUv" do usługi Azure Key Vault. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Odwoływać się do hasła za pomocą jego identyfikatora URI. Użyj **https://ContosoVault.vault.azure.net/secrets/SQLPassword** zawsze uzyskać bieżącą wersję oraz https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] Aby uzyskać tę konkretną wersję. Na przykład **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** .

Zaimportuj certyfikat do magazynu przy użyciu PEM lub pfx.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Teraz można wyświetlić klucz, hasło lub certyfikat, który został utworzony:

* Aby wyświetlić klucze, należy wpisać: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Aby wyświetlić klucze tajne, wpisz: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Aby wyświetlić certyfikaty, należy wpisać: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Rejestrowanie aplikacji w usłudze Azure Active Directory

Ten krok będzie zazwyczaj wykonywany przez programistę na innym komputerze. Nie jest specyficzne dla usługi Azure Key Vault, ale został tu zawarty, aby funkcja rozpoznawania poprawnie. Aby ukończyć rejestrację aplikacji, Twoje konto, Magazyn i aplikacja muszą być w tym samym katalogu platformy Azure.

Aplikacje używające magazynu kluczy muszą zostać uwierzytelnione przy użyciu tokenu z usługi Azure Active Directory.  Właściciel aplikacji musi być zarejestrowany w usłudze Azure Active Directory najpierw. Na koniec rejestracji właściciel aplikacji otrzymuje następujące wartości:

- **Identyfikator aplikacji** (znany także jako identyfikator klienta usługi AAD lub identyfikator aplikacji)
- **Klucz uwierzytelniania** (nazywany też wspólnym wpisem tajnym). 

Aby uzyskać token, aplikacja musi przedstawić obie wartości usłudze Azure Active Directory. Jak aplikacja jest skonfigurowana w celu pobrania tokenu będzie zależeć od aplikacji. W przypadku [przykładowej aplikacji usługi Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) właściciel aplikacji ustawia te wartości w pliku app.config.

Aby uzyskać szczegółowe instrukcje dotyczące rejestrowania aplikacji w usłudze Azure Active Directory należy zapoznać się z artykułami pod tytułem [Integrowanie aplikacji z usługą Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md), [w obsłudze portalu, aby utworzyć Azure Active Directory Katalog aplikacji i jednostki usługi, które mogą uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md), i [Tworzenie jednostki usługi platformy Azure przy użyciu wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

Aby zarejestrować aplikację w usłudze Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Autoryzowanie aplikacji na używanie klucza lub klucza tajnego

Aby przyznać aplikacji dostęp do klucza lub klucza tajnego w magazynie, użyj `az keyvault set-policy` polecenia.

Na przykład jeśli nazwa Twojego magazynu to ContosoKeyVault, aplikacja ma appID 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed i chcesz zezwolić aplikacji na odszyfrowywanie oraz logowanie za pomocą kluczy w magazynie, użyj następującego polecenia:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Aby zezwolić tej samej aplikacji na odczytywanie wpisów tajnych w magazynie, wpisz następujące polecenie:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a> Ustawienia magazynu kluczy, zaawansowane zasady dostępu

Użyj [az keyvault update](/cli/azure/keyvault#az-keyvault-update) można włączyć zaawansowane zasady dla usługi key vault.

 Włącz usługi Key Vault dla wdrożenia: Umożliwia maszyn wirtualnych, aby pobrać certyfikaty zapisane jako wpisy tajne z magazynu.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Włącz magazyn kluczy do szyfrowania dysku: Wymagane w przypadku używania magazynu dla usługi Azure Disk encryption.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Włącz usługi Key Vault dla wdrożenia szablonu: Pozwala pobrać Wpisy tajne z magazynu usługi Resource Manager.

```azurecli 
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
 ```

## <a name="working-with-hardware-security-modules-hsms"></a>Praca z sprzętowych modułów zabezpieczeń (HSM)

Dodano bezpieczeństwa można importować lub generować klucze w sprzętowych modułach zabezpieczeń (HSM), które nigdy nie opuszczają modułu HSM. Moduły HSM są zweryfikowane w trybie FIPS 140-2 poziom 2. Jeśli te wymagania nie odnoszą się do Ciebie, Pomiń tę sekcję i przejdź do usuwania magazynu kluczy oraz skojarzonych kluczy i wpisów tajnych.

Aby utworzyć te klucze chronione modułem HSM, musi mieć subskrypcję magazynu obsługującą klucze chronione przez moduł HSM.

Podczas tworzenia magazynu kluczy, Dodaj parametr "sku":

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Do tego magazynu możesz dodać klucze chronione oprogramowaniem (jak pokazano wcześniej) oraz klucze chronione modułem HSM. Aby utworzyć klucz chroniony przez moduł HSM, ustaw parametr Destination "HSM":

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

Aby zaimportować klucz z pliku PEM, na komputerze, można użyć następującego polecenia. To polecenie importuje klucz do modułu HSM w usłudze Key Vault:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

Następne polecenie importuje pakiet „Wprowadź własny klucz” (BYOK, bring your own key). Umożliwia to wygenerowanie własnego klucza w lokalnym module HSM i przeniesienie go do modułów HSM w usłudze Key Vault bez opuszczania przez klucz granic modułu HSM:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Aby uzyskać szczegółowe instrukcje dotyczące sposobu generowania pakietu BYOK, zobacz [sposobu użycia kluczy HSM-Protected przy użyciu usługi Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Usuwanie magazynu kluczy oraz skojarzonych kluczy i wpisów tajnych

Jeśli nie potrzebujesz już magazyn kluczy i jego klucze i wpisy tajne, można usunąć usługi key vault za pomocą `az keyvault delete` polecenia:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Możesz także usunąć całą grupę zasobów platformy Azure zawierającą magazyn kluczy oraz inne zasoby, które zostały dodane do tej grupy:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Polecenia różne interfejsu wiersza polecenia platformy Azure dla wielu Platform

Innych poleceń, które mogą być przydatne do zarządzania usługi Azure Key Vault.

To polecenie wyświetla tabelaryczny widok wszystkich kluczy i wybranych właściwości:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

To polecenie wyświetla pełną listę właściwości dla określonego klucza:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

To polecenie wyświetla tabelaryczny widok wszystkich nazw kluczy tajnych i wybranych właściwości:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Poniżej przedstawiono przykładowy sposób usunięcia określonego klucza:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Poniżej przedstawiono przykładowy sposób usunięcia określonego klucza tajnego:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać pełną dokumentację interfejsu wiersza polecenia platformy Azure dla poleceń usługi key vault, zobacz [dokumentacja interfejsu wiersza polecenia klucz magazynu](/cli/azure/keyvault).

- Odwołania dotyczące programowania, zobacz [przewodnik dewelopera usługi Azure Key Vault](key-vault-developers-guide.md)

- Aby uzyskać informacje na temat usługi Azure Key Vault i sprzętowych modułów zabezpieczeń, zobacz [sposobu użycia kluczy HSM-Protected przy użyciu usługi Azure Key Vault](key-vault-hsm-protected-keys.md).
