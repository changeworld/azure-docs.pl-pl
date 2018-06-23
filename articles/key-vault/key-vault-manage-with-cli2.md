---
title: Zarządzanie usługą Azure Key Vault przy użyciu interfejsu wiersza polecenia | Dokumentacja firmy Microsoft
description: Niniejszy artykuł służy automatyzacji typowych zadań w Key Vault za pomocą interfejsu wiersza polecenia 2.0
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: ''
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: barclayn
ms.openlocfilehash: 8c2501b5e89e81709de074c0b0c93b317ecebd7b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316598"
---
# <a name="manage-key-vault-using-cli-20"></a>Zarządzanie za pomocą interfejsu wiersza polecenia 2.0 magazyn kluczy

W tym artykule omówiono, jak rozpocząć pracę z usługą Azure Key Vault za pomocą 2.0 interfejsu wiersza polecenia platformy Azure. Można wyświetlić informacje na:
- Jak utworzyć wzmocnionego kontenera (magazynu) na platformie Azure
- Sposób przechowywania i zarządzania nimi kluczy kryptograficznych i kluczy tajnych na platformie Azure. 
- Aby utworzyć magazyn przy użyciu wiersza polecenia platformy Azure.
- Tworzenie klucza lub hasła, które następnie można za pomocą aplikacji Azure. 
- Jak aplikacja może użyć utworzony klucz lub hasło.

Usługa Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).


> [!NOTE]
> Ten artykuł zawiera instrukcje dotyczące sposobu pisania aplikacji Azure, że jeden z kroków zawiera, który pokazuje, jak zezwolić aplikacji na używanie klucza lub klucza tajnego w magazynie kluczy.
>

Omówienie usługi Azure Key Vault, zobacz [co to jest usługa Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Wymagania wstępne
Użycie poleceń interfejsu wiersza polecenia Azure, w tym artykule, musi mieć następujące elementy:

* Subskrypcja platformy Microsoft Azure. Jeśli go nie masz, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial).
* Interfejs wiersza polecenia w wersji 2.0 lub nowszej. Aby zainstalować najnowszą wersję, zobacz [Instalowanie i konfigurowanie Azure 2.0 interfejsu wiersza polecenia i Platform](/cli/azure/install-azure-cli).
* Aplikacja zostanie skonfigurowana w celu używania klucza lub hasła utworzonego w tym artykule. Przykładowa aplikacja jest dostępna w [Centrum pobierania Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Aby uzyskać instrukcje zobacz dołączony plik Readme.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Uzyskiwanie pomocy z interfejsu wiersza polecenia platformy Azure i Platform
W tym artykule przyjęto założenie, że znasz interfejsu wiersza polecenia (Bash, terminali, wiersza polecenia).

Parametru pomocy lub -h można wyświetlić Pomoc dla określonego polecenia. Alternatywnie Azure help [polecenie] się, że format [opcje] można również zbyt. W razie wątpliwości o parametry wymagane przez polecenie, zajrzyj do pomocy. Na przykład poniższe polecenia wszystkie zwracają te same informacje:

```azurecli-interactive
az account set --help
az account set -h
```

Możesz przeczytać następujące artykuły, aby zapoznać się z usługą Azure Resource Manager w interfejsu wiersza polecenia platformy Azure i Platform:

* [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
* [Wprowadzenie do usługi Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Nawiązywanie połączenia z subskrypcjami

Do logowania interakcyjnego, użyj następującego polecenia:

```azurecli
az login
```
Aby zalogować się przy użyciu konta organizacji, można przekazać nazwę użytkownika i hasło.

```azurecli
az login -u username@domain.com -p password
```

Jeśli masz więcej niż jedną subskrypcję i konieczne jest określenie, który ma zostać użyty, wpisz następujące polecenie, aby zobaczyć subskrypcje dla swojego konta:

```azurecli
az account list
```

Określ subskrypcję za pomocą parametru subskrypcji.

```azurecli
az account set --subscription <subscription name or ID>
```

Aby uzyskać więcej informacji o konfigurowaniu Azure Międzyplatformowego interfejsu wiersza polecenia, zobacz [instalowanie interfejsu wiersza polecenia Azure](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Utworzenie nowej grupy zasobów
Podczas korzystania z usługi Azure Resource Manager, wszystkie powiązane zasoby są tworzone wewnątrz grupy zasobów. Można utworzyć magazynu kluczy w istniejącej grupie zasobów. Jeśli chcesz użyć nową grupę zasobów, można utworzyć nowy.

```azurecli
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

Pierwszym parametrem jest nazwa grupy zasobów, a drugi parametr jest lokalizacji. Aby uzyskać listę wszystkich możliwych wpisz lokalizacje:

```azurecli
az account list-locations
``` 

## <a name="register-the-key-vault-resource-provider"></a>Rejestrowanie dostawcy zasobów magazynu kluczy
 Może zostać wyświetlony błąd "subskrypcji nie jest zarejestrowany do korzystania z przestrzeni nazw"Microsoft.KeyVault"" podczas próby utworzenia nowego magazynu kluczy. Jeśli zostanie wyświetlony ten komunikat, upewnij się, że tego dostawcy zasobów usługi Key Vault jest zarejestrowany w ramach subskrypcji. Jest to jednorazowa operacja dla każdej subskrypcji.

```azurecli
az provider register -n Microsoft.KeyVault
```


## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Użyj `az keyvault create` polecenie, aby utworzyć magazyn kluczy. Ten skrypt ma trzy obowiązkowe parametry: Nazwa grupy zasobów, nazwa magazynu kluczy i lokalizacji geograficznej.

Aby utworzyć nowy magazyn o nazwie **ContosoKeyVault**, w grupie zasobów **ContosoResourceGroup**, przechowywanych w **Azja Wschodnia** lokalizacji, wpisz: 

```azurecli
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

Dane wyjściowe tego polecenia są wyświetlane właściwości magazynu kluczy, które zostały utworzone. Dwie najważniejsze właściwości to:

* **Nazwa**: W tym przykładzie nazwa jest ContosoKeyVault. Będziesz używać tej nazwy dla innych poleceń usługi Key Vault.
* **vaultUri**: W tym przykładzie identyfikator URI jest https://contosokeyvault.vault.azure.net. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure ma teraz uprawnienia do wykonywania dowolnych operacji na tym magazynie kluczy. Jeszcze nikt inny nie ma uprawnień.

## <a name="add-a-key-secret-or-certificate-to-the-key-vault"></a>Dodaj klucz, hasło lub certyfikat do magazynu kluczy

Usługa Azure Key Vault utworzenie klucza chronionego przez oprogramowanie do, należy użyć `az key create` polecenia.

```azurecli
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```

Jeśli masz istniejący klucz w pliku PEM, można przekazać jej do usługi Azure Key Vault. Istnieje możliwość ochrony klucza z modułu HSM lub oprogramowania. Aby zaimportować klucz z pliku PEM, a następnie włącz ochronę z oprogramowaniem, skorzystaj z następujących:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'Pa$$w0rd' --protection software
```

Teraz możesz odwoływać się klucz, który został utworzony lub przekazany do usługi Azure Key Vault, za pomocą jego identyfikatora URI. Użyj **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** aby zawsze uzyskać bieżącą wersję. Użyj https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] Aby uzyskać tę konkretną wersję. Na przykład **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**. 

Dodawanie klucza tajnego w magazynie, który jest hasłem o nazwie SQLPassword i ma wartość z Pa$ $w0rd do magazynów kluczy Azure. 

```azurecli
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```

Odwoływać się do hasła za pomocą jego identyfikatora URI. Użyj **https://ContosoVault.vault.azure.net/secrets/SQLPassword** zawsze uzyskać bieżącą wersję, a https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] Aby uzyskać tę konkretną wersję. Na przykład **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Zaimportuj certyfikat do magazynu przy użyciu PEM lub pfx.

```azurecli
az keyvault certificate import --vault-name 'ContosoKeyVault' --file 'c:\cert\cert.pfx' --name 'ContosoCert' --password 'Pa$$w0rd'
```

Teraz wyświetlić klucz, klucz tajny lub utworzony certyfikat:

* Aby wyświetlić klucze, wpisz: 

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

* Aby wyświetlić tajnych, wpisz: 

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

* Aby wyświetlić certyfikaty, wpisz: 

```azurecli
az keyvault certificate list --vault-name 'ContosoKeyVault'
```

## <a name="register-an-application-with-azure-active-directory"></a>Rejestrowanie aplikacji w usłudze Azure Active Directory
Ten krok będzie zazwyczaj wykonywany przez programistę na innym komputerze. Nie jest specyficzne dla usługi Azure Key Vault, ale został tu zawarty, dla świadomości. Do ukończenia rejestracji aplikacji, Twoje konto, Magazyn i aplikacji muszą być w tym samym katalogu platformy Azure.

Aplikacje używające magazynu kluczy muszą zostać uwierzytelnione przy użyciu tokenu z usługi Azure Active Directory.  Właściciel aplikacji musi być zarejestrowany w usłudze Azure Active Directory najpierw. Na koniec rejestracji właściciel aplikacji otrzymuje następujące wartości:

- **Identyfikator aplikacji** (znany także jako identyfikator klienta usługi AAD lub appID)
- **Klucz uwierzytelniania** (nazywany też wspólnym wpisem tajnym). 

Aby uzyskać token, aplikacja musi przedstawić obie wartości usłudze Azure Active Directory. Jak aplikacja jest skonfigurowana w celu pobrania tokenu zależy od aplikacji. W przypadku [przykładowej aplikacji usługi Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) właściciel aplikacji ustawia te wartości w pliku app.config.

Aby uzyskać szczegółowe instrukcje dotyczące rejestrowania aplikacji w usłudze Azure Active Directory należy przejrzeć artykuły zatytułowany [Integrowanie aplikacji z usługą Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md), [Użyj portalu, aby utworzyć w usłudze Azure Active Katalog aplikacji i nazwy głównej usługi, który ma dostęp do zasobów](../azure-resource-manager/resource-group-create-service-principal-portal.md), i [zapisać nazwy głównej usługi platformy Azure z Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli).

Aby zarejestrować aplikację w usłudze Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password 'Pa$$w0rd' --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Zezwalanie aplikacji na używanie klucza lub klucza tajnego

Aby zezwolić aplikacji na dostęp do klucza lub klucza tajnego w magazynie, użyj `az keyvault set-policy` polecenia.

Na przykład jeśli nazwa Twojego magazynu to ContosoKeyVault, aplikacja ma appID 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, i chcesz zezwolić aplikacji na odszyfrowywanie oraz logowanie z kluczy w magazynie, użyj następującego polecenia:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Aby zezwolić tej samej aplikacji na odczyt kluczy tajnych w magazynie, wpisz następujące polecenie:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a> Magazyn kluczy zestaw zaawansowanych zasad dostępu 
Użyj [aktualizacji keyvault az](/cli/azure/keyvault#az-keyvault-update) do włączenia zaawansowanych zasad dla magazynu kluczy. 

 Włączanie usługi Key Vault dla wdrożenia: umożliwia pobranie przechowywane jako klucze tajne z magazynu certyfikatów maszyn wirtualnych.
 ```azurecli
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-deployment 'true'
 ``` 

Włącz magazyn kluczy do szyfrowania dysku: wymagane w przypadku używania magazynu dla szyfrowania dysków Azure.

 ```azurecli
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-disk-encryption 'true'
 ```  

Włączanie usługi Key Vault dla wdrożenia szablonu: umożliwia Resource Manager można pobrać z magazynu kluczy tajnych.
 ```azurecli 
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-template-deployment 'true'
 ```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Użycie sprzętowego modułu zabezpieczeń (HSM, hardware security module)

Dla dodatkowego bezpieczeństwa możesz zaimportować lub wygenerować klucze w sprzętowych modułów zabezpieczeń (HSM), które nigdy nie opuszczają granicy modułów HSM. Moduły HSM są zweryfikowane w trybie FIPS 140-2 poziom 2. Jeżeli te wymagania nie odnoszą się do Ciebie, pomiń tę sekcję i przejdź do sekcji [Usuwanie magazynu kluczy oraz skojarzonych kluczy i kluczy tajnych](#delete-the-key-vault-and-associated-keys-and-secrets).

Aby utworzyć te klucze chronione przez moduł HSM, musi mieć subskrypcję magazynu obsługującą klucze chronione przez moduł HSM.

Podczas tworzenia keyvault, Dodaj parametr "sku":

```azurecli
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```

Do tego magazynu możesz dodać klucze chronione oprogramowaniem (jak pokazano wcześniej) oraz klucze chronione modułem HSM. Aby utworzyć klucz chroniony przez moduł HSM, ustaw dla parametru docelowego "HSM":

```azurecli
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

Następujące polecenie służy do importowania klucza z pliku PEM, na tym komputerze. To polecenie importuje klucz do modułu HSM w usłudze Key Vault:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```

Następne polecenie importuje pakiet „Wprowadź własny klucz” (BYOK, bring your own key). Umożliwia to wygenerowanie własnego klucza w lokalnym module HSM i przeniesienie go do modułów HSM w usłudze Key Vault bez opuszczania przez klucz granic modułu HSM:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```

Aby uzyskać szczegółowe instrukcje na temat generowania pakietu BYOK, zobacz [sposobu korzystania z usługi Azure Key Vault HSM-Protected klucze](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Usuwanie magazynu kluczy oraz skojarzonych kluczy i kluczy tajnych

Jeśli potrzebujesz już magazyn kluczy i jego kluczy lub kluczy tajnych, można usunąć magazynu kluczy za pomocą `az keyvault delete` polecenia:

```azurecli
az keyvault delete --name 'ContosoKeyVault'
```

Możesz także usunąć całą grupę zasobów platformy Azure zawierającą magazyn kluczy oraz inne zasoby, które zostały dodane do tej grupy:

```azurecli
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Inne polecenia interfejsu wiersza polecenia platformy Azure i Platform

Inne polecenia, które mogą być przydatne do zarządzania usługą Azure Key Vault.

To polecenie wyświetla tabelaryczny widok wszystkich kluczy i wybranych właściwości:

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

To polecenie wyświetla pełną listę właściwości dla określonego klucza:

```azurecli
az keyvault key show --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

To polecenie wyświetla tabelaryczny widok wszystkich nazw kluczy tajnych i wybranych właściwości:

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

Poniżej przedstawiono przykładowy sposób usunięcia określonego klucza:

```azurecli
az keyvault key delete --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Poniżej przedstawiono przykładowy sposób usunięcia określonego klucza tajnego:

```azurecli
az keyvault secret delete --vault-name 'ContosoKeyVault' --name 'SQLPassword'
```

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać pełną dokumentację interfejsu wiersza polecenia Azure dla magazynu kluczy poleceń, zobacz [odwołanie klucza magazynu interfejsu wiersza polecenia](/cli/azure/keyvault).

- Odwołania dotyczące programowania znajdują się w [przewodniku dewelopera usługi Azure Key Vault](key-vault-developers-guide.md).

- Aby uzyskać informacje dotyczące usługi Azure Key Vault i sprzętowych modułów zabezpieczeń, zobacz [sposobu korzystania z usługi Azure Key Vault HSM-Protected klucze](key-vault-hsm-protected-keys.md).
