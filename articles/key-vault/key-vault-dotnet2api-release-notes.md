---
title: Informacje o wersji interfejsu API 2.x .NET magazynu kluczy | Dokumentacja firmy Microsoft
description: Deweloperom platformy .NET będzie używać tego interfejsu API do kodu usługi Azure Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
editor: bryanla
ms.service: key-vault
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: f9dd8a48da08f00cea1219f72940dd84dd3a97ac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64725510"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Usługa Azure Key Vault .NET w wersji 2.0 — przewodnik migracji i informacje o wersji
Poniższe informacje ułatwia Migrowanie do biblioteki usługi Azure Key Vault w wersji 2.0 dla języka C# i .NET.  Aplikacje napisane w przypadku starszych wersji konieczne zaktualizowany do obsługi najnowszej wersji.  Te zmiany są potrzebne do zapewnienia pełnej obsługi nowych i ulepszonych funkcjach, takich jak **certyfikaty usługi Key Vault**.

## <a name="key-vault-certificates"></a>Certyfikaty usługi Key Vault

Certyfikaty usługi Key Vault Zarządzanie x509 certyfikaty i obsługuje następujące zachowania:  

* Tworzenie certyfikatów proces tworzenia usługi Key Vault lub importowania istniejącego certyfikatu. Dotyczy to zarówno z podpisem własnym, a urząd certyfikacji (CA) generowanych certyfikaty.
* Bezpieczne przechowywanie i zarządzanie x509 certyfikatu magazynu bez interakcji z przy użyciu materiał klucza prywatnego.  
* Definiowanie zasad, które kierują usługi Key Vault do zarządzania cyklem życia certyfikatu.  
* Podaj informacje kontaktowe dla zdarzenia cyklu życia, takie jak ostrzeżenia dotyczące wygaśnięcia i powiadomienia o odnowieniu.  
* Automatyczne odnawianie certyfikatów z wystawców wybranych (dostawców certyfikatów partnera X509 usługi Key Vault i urzędy certyfikacji). * certyfikatu pomocy technicznej z alternatywny (przez partnerów) zapewnia i urzędy certyfikacji (nie obsługuje automatycznego odnawiania).  

## <a name="net-support"></a>Obsługa platformy .NET

* **Program .NET 4.0** nie jest obsługiwany przez bibliotekę Azure Key Vault dla środowiska .NET w wersji 2.0
* **.NET framework 4.5.2** jest obsługiwany przez bibliotekę Azure Key Vault dla środowiska .NET w wersji 2.0
* **.NET standard 1.4** jest obsługiwany przez bibliotekę Azure Key Vault dla środowiska .NET w wersji 2.0

## <a name="namespaces"></a>Przestrzenie nazw

* Przestrzeń nazw dla **modeli** zostało zmienione z **Microsoft.Azure.KeyVault** do **Microsoft.Azure.KeyVault.Models**.
* **Microsoft.Azure.KeyVault.Internal** przestrzeni nazw jest porzucany.
* Masz następujące przestrzenie nazw zależności zestawu Azure SDK 

    - **Hyak.Common** jest teraz **Microsoft.Rest**.
    - **Hyak.Common.Internals** jest teraz **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Zmiana typu

* *Klucz tajny* zmieniony na *SecretBundle*
* *Słownik* zmieniony na *IDictionary*
* *Lista<T>, string []* zmieniony na *IList<T>*
* *NextList* zmieniony na *NextPageLink*

## <a name="return-types"></a>Typy zwracane

* **KeyList** i **SecretList** teraz zwraca *IPage<T>*  zamiast *ListKeysResponseMessage*
* Wygenerowany **BackupKeyAsync** teraz zwraca *BackupKeyResult*, który zawiera *wartość* (wykonaj kopię zapasową obiektów blob). Wcześniej metoda została opakowana i zwracany wyłącznie wartość.

## <a name="exceptions"></a>Wyjątki

* *KeyVaultClientException* jest zmieniana na *KeyVaultErrorException*
* Błąd usługi zmieniła się z *wyjątku. Błąd* do *wyjątku. Body.Error.Message*.
* Usunięte dodatkowe informacje z komunikat o błędzie dla **[JsonExtensionData]** .

## <a name="constructors"></a>Konstruktory

* Zamiast akceptowanie *HttpClient* jako argument konstruktora akceptuje tylko Konstruktor *HttpClientHandler* lub *DelegatingHandler []* .

## <a name="downloaded-packages"></a>Pakietów do pobrania

W przypadku klienta przetwarza zależności usługi Key Vault, są pobierane następujące pakiety:

### <a name="previous-package-list"></a>Poprzednie listy pakietów

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Bieżąca lista pakietów

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Klasa zmiany

* **UnixEpoch** klasa została usunięta.
* **Base64UrlConverter** klasa została zmieniona na **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Inne zmiany

* Obsługa konfiguracji zasady ponawiania operacji KV na przejściowe awarie, dodano do tej wersji interfejsu API.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Dla operacji, które zwróciły *magazynu*, zwracany typ został klasę, która zawiera **magazynu** właściwości. Typ zwracany jest teraz *magazynu*.
* *PermissionsToKeys* i *PermissionsToSecrets* są teraz *Permissions.Keys* i *Permissions.Secrets*
* Niektóre typy zwracane występujące zmiany dotyczą płaszczyźnie kontroli również.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Pakiet został przerwany maksymalnie **Microsoft.Azure.KeyVault.Extensions** i **Microsoft.Azure.KeyVault.Cryptography** dla operacji kryptograficznych.

