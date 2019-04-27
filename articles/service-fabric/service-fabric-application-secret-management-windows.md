---
title: Konfigurowanie certyfikatu szyfrowania i szyfrowanie kluczy tajnych w klastrach usługi Azure Service Fabric Windows | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować certyfikat szyfrowania do szyfrowania kluczy tajnych w klastrach Windows.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 3d324c54d10433520a73f2bd836c26bd79f1b3bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615269"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Konfigurowanie certyfikatu szyfrowania i szyfrowanie kluczy tajnych w klastrach Windows
W tym artykule pokazano, jak skonfigurować certyfikat szyfrowania i użyć go do szyfrowania kluczy tajnych w klastrach Windows. W przypadku klastrów systemu Linux, zobacz [ustawić certyfikat szyfrowania w górę i szyfrowanie kluczy tajnych w klastrach systemu Linux.][secret-management-linux-specific-link]

[Usługa Azure Key Vault] [ key-vault-get-started] jest używany tutaj jako lokalizację bezpieczne przechowywanie certyfikatów i sposobem uzyskania certyfikatów zainstalowanych w klastrach usługi Service Fabric na platformie Azure. Jeśli nie są wdrażane na platformie Azure, nie należy używać usługi Key Vault do zarządzania wpisami tajnymi w aplikacji usługi Service Fabric. Jednak *przy użyciu* wpisów tajnych aplikacji w chmurze jest niezależne od platformy do umożliwienia aplikacji można wdrożyć w klastrze, hostowanych w dowolnym miejscu. 

## <a name="obtain-a-data-encipherment-certificate"></a>Uzyskaj certyfikat Szyfrowanie danych
Certyfikat Szyfrowanie danych jest używane wyłącznie do szyfrowania i odszyfrowywania [parametry] [ parameters-link] w Settings.xml usługi i [zmienne środowiskowe] [ environment-variables-link] w ServiceManifest.xml usługi. Nie jest używany do uwierzytelniania lub podpisywania z zaszyfrowanego tekstu. Certyfikat musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny.
* Certyfikat musi zostać utworzona na potrzeby wymiany kluczy, który można eksportować do pliku wymiany informacji osobistych (pfx).
* Użycia klucza certyfikatu musi zawierać szyfrowanie danych (10), a nie może zawierać uwierzytelnianie serwera i uwierzytelnianie klienta. 
  
  Na przykład podczas tworzenia certyfikatu z podpisem własnym za pomocą programu PowerShell `KeyUsage` musi zostać ustawiona flaga `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Zainstaluj certyfikat w klastrze
Ten certyfikat należy zainstalować na każdym węźle w klastrze. Zobacz [sposób tworzenia klastra przy użyciu usługi Azure Resource Manager] [ service-fabric-cluster-creation-via-arm] instrukcje instalacji. 

## <a name="encrypt-application-secrets"></a>Szyfrowanie kluczy tajnych aplikacji
Następujące polecenie programu PowerShell jest używany do szyfrowania klucza tajnego. To polecenie szyfruje wartości; robi **nie** zarejestrować zaszyfrowanego tekstu. Musisz użyć tego samego certyfikatu szyfrowanie, który jest zainstalowany w klastrze, aby wygenerować tekstu szyfrowanego wpisu tajnego wartości:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Wynikowy ciąg zakodowany base-64 zawiera zarówno tekstu szyfrowanego wpisu tajnego oraz informacje dotyczące certyfikatu, który został użyty do zaszyfrowania.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [Określ zaszyfrowane klucze tajne w aplikacji.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
