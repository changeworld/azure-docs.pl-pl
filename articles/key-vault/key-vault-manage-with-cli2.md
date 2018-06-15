---
title: Zarządzanie usługą Azure Key Vault przy użyciu interfejsu wiersza polecenia | Dokumentacja firmy Microsoft
description: Użyj tego samouczka do automatyzacji typowych zadań w Key Vault za pomocą interfejsu wiersza polecenia 2.0
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
ms.date: 04/19/2018
ms.author: barclayn
ms.openlocfilehash: 95e35ed1f26a861ab934570fae613dda95fcb537
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796136"
---
# <a name="manage-key-vault-using-cli-20"></a>Zarządzanie za pomocą interfejsu wiersza polecenia 2.0 magazyn kluczy

W tym artykule omówiono, jak rozpocząć pracę z usługą Azure Key Vault za pomocą 2.0 interfejsu wiersza polecenia platformy Azure. Można wyświetlić informacje na:
- Jak utworzyć wzmocnionego kontenera (magazynu) na platformie Azure
- Sposób przechowywania i zarządzania nimi kluczy kryptograficznych i kluczy tajnych na platformie Azure. 
- Proces tworzenia magazynu, który zawiera klucz lub hasło, które następnie można za pomocą aplikacji Azure przy użyciu interfejsu wiersza polecenia platformy Azure i Platform. 
- Jak aplikacja może następnie użyć tego klucza lub hasła.

Usługa Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

**Szacowany czas trwania:** 20 minut

> [!NOTE]
> Ten samouczek nie zawiera instrukcji dotyczących sposobu pisania aplikacji Azure, że jeden z kroków zawiera, który pokazuje, jak zezwolić aplikacji na używanie klucza lub klucza tajnego w magazynie kluczy.
>

Omówienie usługi Azure Key Vault, zobacz [co to jest usługa Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Microsoft Azure. Jeśli nie masz, możesz zarejestrować się w celu [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial).
* Interfejs wiersza polecenia w wersji 2.0 lub nowszej. Aby zainstalować najnowszą wersję i nawiązać połączenia z subskrypcją platformy Azure, zobacz [Instalowanie i konfigurowanie Azure 2.0 interfejsu wiersza polecenia i Platform](/cli/azure/install-azure-cli).
* Aplikacja, która zostanie skonfigurowana w celu używania klucza lub hasła utworzonego w tym samouczku. Przykładowa aplikacja jest dostępna w [Centrum pobierania Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Aby uzyskać instrukcje, zobacz załączony plik Readme.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Uzyskiwanie pomocy z interfejsu wiersza polecenia platformy Azure i Platform
Ten samouczek zakłada, że znasz interfejsu wiersza polecenia (Bash, terminali, wiersza polecenia)

Parametru pomocy lub -h można wyświetlić Pomoc dla określonego polecenia. Alternatywnie azure help [polecenie] [opcje] format również może zostać użyty do zwrócenia tych samych informacji. Na przykład poniższe polecenia wszystkie zwracają te same informacje:

```azurecli-interactive
az account set --help
az account set -h
```

W razie wątpliwości o parametry wymagane przez polecenie, zajrzyj do pomocy, przy użyciu — pomoc, -h lub az help [polecenie].

Możesz przeczytać następujące samouczki, aby zapoznać się z usługą Azure Resource Manager w interfejsu wiersza polecenia platformy Azure i Platform:

* [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
* [Wprowadzenie do usługi Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Nawiązywanie połączenia z subskrypcjami
Aby zalogować się przy użyciu konta organizacji, użyj następującego polecenia:

```azurecli
az login -u username@domain.com -p password
```

lub jeśli chcesz się zalogować, wpisując interakcyjnego

```azurecli
az login
```

Jeśli masz wiele subskrypcji i chcesz określić, która ma być używana przez usługę Azure Key Vault, wpisz następujące polecenie, aby zobaczyć subskrypcje przypisane do konta:

```azurecli
az account list
```

Aby określić subskrypcję, która ma być używana, wpisz polecenie:

```azurecli
az account set --subscription <subscription name or ID>
```

Aby uzyskać więcej informacji o konfigurowaniu Azure Międzyplatformowego interfejsu wiersza polecenia, zobacz [instalowanie interfejsu wiersza polecenia Azure](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Utworzenie nowej grupy zasobów
Podczas korzystania z usługi Azure Resource Manager, wszystkie powiązane zasoby są tworzone wewnątrz grupy zasobów. W tym samouczku utworzymy nową grupę zasobów "ContosoResourceGroup".

```azurecli
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

Pierwszym parametrem jest nazwa grupy zasobów, a drugi parametr jest lokalizacji. Aby uzyskać listę wszystkich możliwych wpisz lokalizacje:

```azurecli
az account list-locations
``` 

Aby uzyskać więcej informacji, wpisz: 

```azurecli
az account list-locations -h
```

## <a name="register-the-key-vault-resource-provider"></a>Rejestrowanie dostawcy zasobów magazynu kluczy
Podczas próby utworzenia nowego magazynu kluczy może zostać wyświetlony błąd "subskrypcji nie jest zarejestrowany do korzystania z przestrzeni nazw"Microsoft.KeyVault"". Jeśli ten komunikat zostanie wyświetlony, upewnij się, że dostawca zasobów usługi Key Vault jest zarejestrowany w ramach Twojej subskrypcji:

```azurecli
az provider register -n Microsoft.KeyVault
```

>[!NOTE]
To tylko należy jednak wykonać jeden raz dla subskrypcji.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Użyj `az keyvault create` polecenie, aby utworzyć magazyn kluczy. Ten skrypt ma trzy obowiązkowe parametry: Nazwa grupy zasobów, nazwa magazynu kluczy i lokalizacji geograficznej.

Na przykład:

- Jeśli używasz nazwy magazynu **ContosoKeyVault**
- Nazwa grupy zasobów z **ContosoResourceGroup**
- Lokalizacja **Azja Wschodnia**

należy wpisać:

```azurecli
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

Dane wyjściowe tego polecenia są wyświetlane właściwości magazynu kluczy, które zostały utworzone. Dwie najważniejsze właściwości to:

* **Nazwa**: W tym przykładzie jest to ContosoKeyVault. Użyje tej nazwy dla innych poleceń usługi Key Vault.
* **vaultUri**: W tym przykładzie jest to https://contosokeyvault.vault.azure.net. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure ma teraz uprawnienia do wykonywania dowolnych operacji na tym magazynie kluczy. Nikt inny nie jest do tego upoważniony.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Dodawanie klucza lub klucza tajnego do magazynu kluczy

Usługa Azure Key Vault utworzenie klucza chronionego przez oprogramowanie do, należy użyć `az key create` polecenia i wpisz następujące polecenie:

```azurecli
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```

Jednak jeśli masz istniejący klucz w pliku PEM, zapisany jako plik lokalny w pliku o nazwie softkey.pem, który chcesz przekazać do usługi Azure Key Vault, wpisz następujące polecenie, aby zaimportować klucz z. Plik PEM, który chroni klucz przez oprogramowanie w usłudze Key Vault:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'PaSSWORD' --protection software
```

Teraz możesz odwoływać się klucz, który został utworzony lub przekazany do usługi Azure Key Vault, za pomocą jego identyfikatora URI. Użyj **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** aby zawsze uzyskać bieżącą wersję oraz **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** uzyskać tę konkretną wersję.

Aby dodać klucza tajnego w magazynie, który jest hasłem o nazwie SQLPassword i ma wartość z Pa$ $w0rd do usługi Azure Key Vault, wpisz następujące polecenie:

```azurecli
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```

Teraz możesz odwoływać się do hasła dodanego do usługi Azure Key Vault za pomocą jego identyfikatora URI. Użyj **https://ContosoVault.vault.azure.net/secrets/SQLPassword** aby zawsze uzyskać bieżącą wersję oraz **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** uzyskać tę konkretną wersję.

Teraz wyświetlić klucz lub klucz tajny, który został właśnie utworzony:

* Aby wyświetlić klucz, wpisz polecenie: 

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

* Aby wyświetlić klucz tajny, wpisz polecenie: 

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

## <a name="register-an-application-with-azure-active-directory"></a>Rejestrowanie aplikacji w usłudze Azure Active Directory
Ten krok będzie zazwyczaj wykonywany przez programistę na innym komputerze. Nie jest specyficzne dla usługi Azure Key Vault, ale został tu zawarty, dla świadomości.

> [!IMPORTANT]
> Aby ukończyć samouczek, Twoje konto, magazyn i aplikacja, która zostanie zarejestrowana w tym kroku, muszą należeć do tego samego katalogu Azure.
>
>

Aplikacje używające magazynu kluczy muszą zostać uwierzytelnione przy użyciu tokenu z usługi Azure Active Directory. Aby to zrobić, właściciel aplikacji musi najpierw zarejestrować ją w usłudze Azure Active Directory. Na koniec rejestracji właściciel aplikacji otrzymuje następujące wartości:

- **Identyfikator aplikacji** 
- **Klucz uwierzytelniania** (nazywany też wspólnym wpisem tajnym). 

Aby uzyskać token, aplikacja musi przedstawić obie wartości usłudze Azure Active Directory. Sposób konfiguracji aplikacji w tym celu zależy od aplikacji. W przypadku [przykładowej aplikacji usługi Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) właściciel aplikacji ustawia te wartości w pliku app.config.

Aby uzyskać szczegółowe instrukcje dotyczące rejestrowania aplikacji w usłudze Azure Active Directory należy przeczytać artykuł zatytułowany [Integrowanie aplikacji z usługą Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) lub [Użyj portalu, aby utworzyć w usłudze Azure Active Katalog aplikacji i nazwy głównej usługi, który ma dostęp do zasobów](../azure-resource-manager/resource-group-create-service-principal-portal.md) zarejestrować aplikację w usłudze Azure Active Directory:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie kliknij pozycję **Rejestracje aplikacji**. Jeśli nie widzisz rejestracji aplikacji, kliknij pozycję **Więcej usług** i poszukaj ich.  
[!NOTE]
Musisz wybrać ten sam katalog, który zawiera subskrypcję platformy Azure użytą do utworzenia magazynu kluczy. 
3. Kliknij pozycję **Rejestrowanie nowej aplikacji**.
4. W bloku **Tworzenie** podaj nazwę swojej aplikacji, a następnie wybierz pozycję **APLIKACJA SIECI WEB I/LUB INTERFEJS API SIECI WEB** (ustawienie domyślne) i określ **ADRES URL LOGOWANIA** dla aplikacji internetowej. Jeśli nie masz tych informacji w tym momencie można tworzyć dla tego kroku (na przykład można określić http://test1.contoso.com ). Nie ma znaczenia, czy takie witryny istnieją. 

    ![Rejestrowanie nowej aplikacji](./media/key-vault-manage-with-cli2/new-application-registration.png)
    >[!WARNING]
    Upewnij się, że wybrano pozycję **APLIKACJA SIECI WEB I/LUB INTERFEJS API SIECI WEB** — w przeciwnym razie w ustawieniach nie będzie widoczna opcja **klucze**.

5. Kliknij przycisk **Utwórz**.
6. Po ukończeniu rejestracji aplikacji możesz wyświetlić listę zarejestrowanych aplikacji. Znajdź właśnie zarejestrowaną aplikację i kliknij ją.
7. Kliknij blok **Zarejestrowana aplikacja** i skopiuj **identyfikator aplikacji**
8. Kliknij pozycję **Wszystkie ustawienia**
9. W bloku **Ustawienia** kliknij pozycję **klucze**
9. Wpisz opis w polu **Opis klucza** i wybierz czas trwania, a następnie kliknij pozycję **ZAPISZ**. Strona zostanie odświeżona i pojawi się na niej wartość klucza. 
10. Informacje dotyczące **identyfikatora aplikacji** i **klucza** zostaną użyte w następnym kroku w celu ustawienia uprawnień dotyczących magazynu.


## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Zezwalanie aplikacji na używanie klucza lub klucza tajnego

Aby zezwolić aplikacji na dostęp do klucza lub klucza tajnego w magazynie, użyj `az keyvault set-policy` polecenia.

Na przykład jeśli nazwa Twojego magazynu to ContosoKeyVault i aplikacji, które chcesz autoryzować ma identyfikator klienta 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed i chcesz zezwolić aplikacji na odszyfrowywanie oraz logowanie z kluczy w magazynie, następnie uruchom następujące polecenie:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Jeśli chcesz zezwolić tej samej aplikacji na odczyt kluczy tajnych w magazynie, uruchom następujące polecenie:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Użycie sprzętowego modułu zabezpieczeń (HSM, hardware security module)

W celu zapewnienia dodatkowego bezpieczeństwa możesz zaimportować lub wygenerować klucze w sprzętowych modułach zabezpieczeń (HSM), które nigdy nie opuszczają granicy modułów HSM. Moduły HSM są zweryfikowane w trybie FIPS 140-2 poziom 2. Jeżeli te wymagania nie odnoszą się do Ciebie, pomiń tę sekcję i przejdź do sekcji [Usuwanie magazynu kluczy oraz skojarzonych kluczy i kluczy tajnych](#delete-the-key-vault-and-associated-keys-and-secrets).

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

Jeśli nie są już potrzebne magazynu kluczy oraz klucz lub klucz tajny, który zawiera magazyn kluczy można usunąć za pomocą `az keyvault delete` polecenia:

```azurecli
az keyvault delete --name 'ContosoKeyVault'
```

Możesz także usunąć całą grupę zasobów platformy Azure zawierającą magazyn kluczy oraz inne zasoby, które zostały dodane do tej grupy:

```azurecli
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Inne polecenia interfejsu wiersza polecenia platformy Azure i Platform

Inne polecenia użytkownik może przydatne do zarządzania usługą Azure Key Vault.

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
