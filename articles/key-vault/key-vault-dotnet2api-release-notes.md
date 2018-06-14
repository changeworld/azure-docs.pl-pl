---
title: Klucz magazynu .NET 2.x API informacje o wersji | Dokumentacja firmy Microsoft
description: .NET umożliwia deweloperom ten interfejs API do kodu dla usługi Azure Key Vault
services: key-vault
author: lleonard-msft
manager: mbaldwin
editor: alleonar
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: alleonar
ms.openlocfilehash: a7735f8c1c4332bf2472bc83c0c37baf49019004
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "27909758"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Usługi Azure Key Vault .NET 2.0 — przewodnik migracji i informacje o wersji
Następujące informacje pomogą w migracji do wersji 2.0 biblioteki usługi Azure Key Vault języka C# i platformy .NET.  Aplikacji napisanych dla wcześniejszych wersji trzeba zaktualizować do obsługi najnowszej wersji.  Te zmiany są potrzebne do zapewnienia pełnej obsługi nowych i ulepszonych funkcji, takich jak **certyfikaty usługi Key Vault**.

## <a name="key-vault-certificates"></a>Magazyn kluczy certyfikatów

Magazyn kluczy certyfikatów zarządzania x509 certyfikaty i obsługuje następujące zachowania:  

* Tworzenie certyfikatów za pośrednictwem procesu tworzenia usługi Key Vault lub importowania istniejącego certyfikatu. Dotyczy to zarówno z podpisem własnym i certyfikatów wygenerowany certyfikat urzędu certyfikacji.
* Bezpieczne przechowywanie i zarządzanie x509 certyfikatu magazynu bez interakcji przy użyciu materiału klucza prywatnego.  
* Definiowanie zasad, które bezpośrednie Key Vault w celu zarządzania cyklem życia certyfikatów.  
* Podaj informacje kontaktowe dla zdarzenia cyklu życia, takie jak ostrzeżenia dotyczące wygaśnięcia i odnawiania powiadomienia.  
* Automatycznego odnawiania certyfikatów z wystawców wybranych (partnera X509 dostawców magazynu kluczy i urzędy certyfikacji). * certyfikat pomocy technicznej z alternatywnym (z systemem innym niż partnerów) zapewnia i urzędy certyfikacji (nie obsługuje automatycznego odnawiania).  

## <a name="net-support"></a>Obsługa .NET

* **.NET 4.0** nie jest obsługiwana przez wersję biblioteki Azure Key Vault .NET 2.0
* **.NET framework 4.5.2** jest obsługiwana przez wersję biblioteki Azure Key Vault .NET 2.0
* **1.4 standardowe .NET** jest obsługiwana przez wersję biblioteki Azure Key Vault .NET 2.0

## <a name="namespaces"></a>Przestrzenie nazw

* W obszarze nazw **modele** została zmieniona z **Microsoft.Azure.KeyVault** do **Microsoft.Azure.KeyVault.Models**.
* **Microsoft.Azure.KeyVault.Internal** przestrzeni nazw jest porzucany.
* Ma następujące obszary nazw zależności zestawu Azure SDK 

    - **Hyak.Common** jest teraz **Microsoft.Rest**.
    - **Hyak.Common.Internals** jest teraz **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Zmiana typu

* *Klucz tajny* zmieniony na *SecretBundle*
* *Słownik* zmieniony na *IDictionary*
* *Lista<T>, string []* zmieniony na *IList<T>*
* *NextList* zmieniony na *NextPageLink*

## <a name="return-types"></a>Zwracane typy

* **KeyList** i **SecretList** teraz zwraca *IPage<T>*  zamiast *ListKeysResponseMessage*
* Wygenerowany **BackupKeyAsync** teraz zwraca *BackupKeyResult*, który zawiera *wartość* (kopie zapasowe obiektów blob). Wcześniej metoda została opakowana i zwracane tylko wartości.

## <a name="exceptions"></a>Wyjątki

* *KeyVaultClientException* jest zmieniana na *KeyVaultErrorException*
* Błąd usługi zmieniła się z *wyjątku. Błąd* do *wyjątku. Body.Error.Message*.
* Usunięte informacje dodatkowe z komunikat o błędzie dla **[JsonExtensionData]**.

## <a name="constructors"></a>Konstruktory

* Zamiast akceptowanie *HttpClient* jako argument konstruktora akceptuje tylko konstruktora *HttpClientHandler* lub *DelegatingHandler []*.

## <a name="downloaded-packages"></a>Pobranych pakietów

Gdy klient przetwarza zależności usługi Key Vault, są pobierane następujące pakiety:

### <a name="previous-package-list"></a>Poprzedniej liście pakietu

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

* **UnixEpoch** klasy został usunięty.
* **Base64UrlConverter** klasy jest zmieniana na **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Inne zmiany

* Dodano obsługę dla konfiguracji zasady ponawiania operacji KV na przejściowych błędów do tej wersji interfejsu API.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Dla operacji, które zwrócił *magazynu*, zwracany typ został klasę, która zawiera **magazynu** właściwości. Typ zwracany jest teraz *magazynu*.
* *PermissionsToKeys* i *PermissionsToSecrets* są teraz *Permissions.Keys* i *Permissions.Secrets*
* Niektóre typy zwracane zmiany dotyczą płaszczyzny sterowania również.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Pakiet został przerwany do **Microsoft.Azure.KeyVault.Extensions** i **Microsoft.Azure.KeyVault.Cryptography** dla operacji kryptograficznych.

