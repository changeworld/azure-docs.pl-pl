---
title: Konfigurowanie certyfikatu szyfrowania w klastrach systemu Windows
description: Dowiedz się, jak skonfigurować certyfikat szyfrowania i zaszyfrować wpisy tajne w klastrach systemu Windows.
author: vturecek
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: d9413a37be221adc375836719dc1f467a5571fa0
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610186"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Konfigurowanie certyfikatu szyfrowania i szyfrowanie wpisów tajnych w klastrach systemu Windows
W tym artykule przedstawiono sposób konfigurowania certyfikatu szyfrowania i używania go do szyfrowania wpisów tajnych w klastrach systemu Windows. W przypadku klastrów systemu Linux zobacz [Konfigurowanie certyfikatu szyfrowania i szyfrowanie wpisów tajnych w klastrach systemu Linux.][secret-management-linux-specific-link]

[Azure Key Vault][key-vault-get-started] jest używany w tym miejscu jako bezpieczna lokalizacja magazynu dla certyfikatów i jako sposób uzyskiwania certyfikatów zainstalowanych w klastrach Service Fabric na platformie Azure. Jeśli nie planujesz wdrożenia na platformie Azure, nie musisz używać Key Vault do zarządzania wpisami tajnymi w aplikacjach Service Fabric. Jednak *Używanie* wpisów tajnych w aplikacji to Cloud Platform-niezależny od, aby umożliwić wdrażanie aplikacji w klastrze hostowanym w dowolnym miejscu. 

## <a name="obtain-a-data-encipherment-certificate"></a>Uzyskiwanie certyfikatu szyfrowania danych
Certyfikat szyfrowanie danych jest używany wyłącznie do szyfrowania i odszyfrowywania [parametrów][parameters-link] w ustawieniach usługi. XML i [zmiennych środowiskowych][environment-variables-link] w pliku servicemanifest. XML usługi. Nie jest on używany do uwierzytelniania ani podpisywania tekstu szyfrowania. Certyfikat musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny.
* Należy utworzyć certyfikat do wymiany kluczy, który można wyeksportować do pliku wymiany informacji osobistych (pfx).
* Użycie klucza certyfikatu musi obejmować szyfrowanie danych (10) i nie powinno obejmować uwierzytelniania serwera ani uwierzytelniania klientów. 
  
  Na przykład podczas tworzenia certyfikatu z podpisem własnym przy użyciu programu PowerShell flaga `KeyUsage` musi być ustawiona na `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalowanie certyfikatu w klastrze
Ten certyfikat musi być zainstalowany w każdym węźle klastra. Aby uzyskać instrukcje dotyczące instalacji [, zobacz jak utworzyć klaster przy użyciu Azure Resource Manager][service-fabric-cluster-creation-via-arm] . 

## <a name="encrypt-application-secrets"></a>Szyfruj wpisy tajne aplikacji
Następujące polecenie programu PowerShell służy do szyfrowania klucza tajnego. To polecenie szyfruje tylko wartość; **nie podpisuje** tekstu szyfru. Musisz użyć tego samego certyfikatu szyfrowania, który jest zainstalowany w klastrze w celu wygenerowania tekstu szyfrowanego dla wartości tajnych:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Ciąg zakodowany Base-64 zawiera zarówno klucz tajny, jak i informacje o certyfikacie użytym do jego zaszyfrowania.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [określić zaszyfrowane klucze tajne w aplikacji.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
