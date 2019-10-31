---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/10/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 89fa06dda418f328b3bc07aada49aa347e35220a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182258"
---
## <a name="rootcert"></a>Tworzenie certyfikatu głównego z podpisem własnym

Użyj polecenia cmdlet New-SelfSignedCertificate, aby utworzyć certyfikat główny z podpisem własnym. Aby uzyskać dodatkowe informacje o parametrach, zobacz polecenie [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Na komputerze z systemem Windows 10 lub Windows Server 2016 Otwórz konsolę programu Windows PowerShell z podwyższonym poziomem uprawnień. Te przykłady nie działają w Azure Cloud Shell "try". Te przykłady należy uruchomić lokalnie.
2. Użyj poniższego przykładu, aby utworzyć certyfikat główny z podpisem własnym. Poniższy przykład tworzy certyfikat główny z podpisem własnym o nazwie "P2SRootCert", który jest automatycznie instalowany w "Certificates-Current User\Personal\Certificates". Certyfikat można wyświetlić, otwierając *certmgr. msc*lub *Zarządzaj certyfikatami użytkowników*.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

## <a name="clientcert"></a>Generowanie certyfikatu klienta

Na każdym komputerze klienckim nawiązującym połączenie z siecią wirtualną za pomocą połączenia typu punkt-lokacja musi być zainstalowany certyfikat klienta w celu uwierzytelniania. Certyfikat klienta jest generowany na podstawie certyfikatu głównego z podpisem własnym, a następnie eksportowany i instalowany jest certyfikat klienta. Jeśli certyfikat klienta nie jest zainstalowany, uwierzytelnianie kończy się niepowodzeniem. 

Poniższe kroki przeprowadzą Cię przez proces generowania certyfikatu klienta z certyfikatu głównego z podpisem własnym. Można generować wiele certyfikatów klienta z tego samego certyfikatu głównego. Po wygenerowaniu certyfikatów klienta przy użyciu poniższych kroków certyfikat klienta jest automatycznie instalowany na komputerze, który został użyty do wygenerowania certyfikatu. Jeśli chcesz zainstalować certyfikat klienta na innym komputerze klienckim, możesz wyeksportować certyfikat.

W przykładach użyto polecenia cmdlet New-SelfSignedCertificate w celu wygenerowania certyfikatu klienta, który wygaśnie w ciągu roku. Dodatkowe informacje o parametrach, takie jak ustawienie innej wartości wygaśnięcia dla certyfikatu klienta, można znaleźć w temacie [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Przykład 1

Użyj tego przykładu, jeśli konsola programu PowerShell nie została zamknięta po utworzeniu certyfikatu głównego z podpisem własnym. Ten przykład jest kontynuowany z poprzedniej sekcji i używa zadeklarowanej zmiennej "$cert". Jeśli konsola programu PowerShell została zamknięta po utworzeniu certyfikatu głównego z podpisem własnym lub tworzenia dodatkowych certyfikatów klienta w nowej sesji konsoli programu PowerShell, wykonaj kroki opisane w [przykładzie 2](#ex2).

Zmodyfikuj i uruchom przykład, aby wygenerować certyfikat klienta. W przypadku uruchomienia poniższego przykładu bez modyfikowania, wynik jest certyfikat klienta o nazwie "P2SChildCert".  Jeśli chcesz nazwać certyfikat podrzędny coś innego, zmodyfikuj wartość nazwy POSPOLITej. Nie zmieniaj rozszerzenia textextension podczas uruchamiania tego przykładu. Wygenerowany certyfikat klienta jest automatycznie instalowany w "Certificates-Current User\Personal\Certificates" na komputerze.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Przykład 2

Jeśli tworzysz dodatkowe certyfikaty klienta lub nie korzystasz z tej samej sesji programu PowerShell, która została użyta do utworzenia certyfikatu głównego z podpisem własnym, wykonaj następujące czynności:

1. Zidentyfikuj certyfikat główny z podpisem własnym, który jest zainstalowany na komputerze. To polecenie cmdlet zwraca listę certyfikatów zainstalowanych na komputerze.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```
2. Znajdź nazwę podmiotu z listy zwracanej, a następnie skopiuj odcisk palca znajdujący się obok niego do pliku tekstowego. W poniższym przykładzie istnieją dwa certyfikaty. Nazwa POSPOLITa to nazwa certyfikatu głównego z podpisem własnym, z którego ma zostać wygenerowany certyfikat podrzędny. W tym przypadku "P2SRootCert".

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Zadeklaruj zmienną dla certyfikatu głównego przy użyciu odcisku palca z poprzedniego kroku. Zamień odcisk PALCa na odcisk palca certyfikatu głównego, z którego chcesz wygenerować certyfikat podrzędny.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Na przykład, przy użyciu odcisku palca dla P2SRootCert w poprzednim kroku, zmienna wygląda następująco:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Zmodyfikuj i uruchom przykład, aby wygenerować certyfikat klienta. W przypadku uruchomienia poniższego przykładu bez modyfikowania, wynik jest certyfikat klienta o nazwie "P2SChildCert". Jeśli chcesz nazwać certyfikat podrzędny coś innego, zmodyfikuj wartość nazwy POSPOLITej. Nie zmieniaj rozszerzenia textextension podczas uruchamiania tego przykładu. Wygenerowany certyfikat klienta jest automatycznie instalowany w "Certificates-Current User\Personal\Certificates" na komputerze.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="cer"></a>Eksportowanie klucza publicznego certyfikatu głównego (CER)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Eksportowanie certyfikatu głównego z podpisem własnym i klucza prywatnego do magazynu (opcjonalnie)

Możesz chcieć wyeksportować certyfikat główny z podpisem własnym i bezpiecznie zapisać go jako kopię zapasową. W razie potrzeby można zainstalować je później na innym komputerze i generować więcej certyfikatów klienta. Aby wyeksportować certyfikat główny z podpisem własnym jako plik PFX, wybierz certyfikat główny i wykonaj te same kroki, zgodnie z opisem w sekcji [Eksportowanie certyfikatu klienta](#clientexport).

## <a name="clientexport"></a>Eksportowanie certyfikatu klienta

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]
