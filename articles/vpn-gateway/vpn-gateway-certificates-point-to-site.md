---
title: 'Generowanie i eksportowanie certyfikatów for Point-to-Site: Program PowerShell: Azure | Microsoft Docs'
description: Utworzyć certyfikat główny z podpisem własnym, a następnie wyeksportować klucz publiczny i wygenerować certyfikaty klienta przy użyciu programu PowerShell w systemie Windows 10 lub Windows Server 2016.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: 74639dee6fb548e1c9067cae6fc22f6e3cc872c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764020"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Generowanie i eksportowanie certyfikatów dla punkt-lokacja przy użyciu programu PowerShell

Połączenia punkt-lokacja używają certyfikatów do uwierzytelniania. W tym artykule pokazano, jak utworzyć certyfikat główny z podpisem własnym i generowania certyfikatów klienta przy użyciu programu PowerShell w systemie Windows 10 lub Windows Server 2016. Jeśli potrzebujesz innego certyfikatu instrukcje, zobacz [certyfikaty — Linux](vpn-gateway-certificates-point-to-site-linux.md) lub [certyfikaty — narzędzie MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Należy wykonać kroki opisane w tym artykule, na komputerze z systemem Windows 10 lub Windows Server 2016. Polecenia cmdlet programu PowerShell, których używasz do generowania certyfikatów są częścią systemu operacyjnego i nie działają w innych wersjach systemu Windows. Na komputerze systemu Windows 10 lub Windows Server 2016 jest wymagana tylko w celu wygenerowania certyfikatów. Gdy certyfikaty są generowane, możesz przekazać je lub je zainstalować w dowolnym systemie operacyjnym klienta obsługiwane. 

Jeśli nie masz dostępu do komputera z systemem Windows 10 lub Windows Server 2016, możesz użyć [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) do generowania certyfikatów. Certyfikaty, które są generowane za pomocą jednej z metod, można instalować na żadnym [obsługiwane](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) kliencki system operacyjny.

## <a name="rootcert"></a>1. Tworzenie certyfikatu głównego z podpisem własnym

Użyj polecenia cmdlet New-SelfSignedCertificate, aby utworzyć certyfikat główny z podpisem własnym. Aby uzyskać informacje o dodatkowych parametrach, zobacz [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Z komputera z systemem Windows 10 lub Windows Server 2016 Otwórz konsolę programu Windows PowerShell z podwyższonym poziomem uprawnień. Te przykłady nie będą działać w usłudze Azure Cloud Shell, "Wypróbuj". Te przykłady należy uruchomić lokalnie.
2. Skorzystaj z następującego przykładu, aby utworzyć certyfikat główny z podpisem własnym. Poniższy przykład tworzy certyfikat główny z podpisem własnym o nazwie "P2SRootCert", który jest automatycznie instalowany w "Certyfikaty-bieżący użytkownik\osobisty\certyfikat". Certyfikat można wyświetlić, otwierając *certmgr.msc*, lub *Zarządzaj certyfikatami użytkowników*.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

## <a name="clientcert"></a>2. Generowanie certyfikatu klienta

Na każdym komputerze klienckim nawiązującym połączenie z siecią wirtualną za pomocą połączenia typu punkt-lokacja musi być zainstalowany certyfikat klienta w celu uwierzytelniania. Generowanie certyfikatu klienta z certyfikatu głównego z podpisem własnym i wyeksportować i instalowania certyfikatu klienta. Jeśli certyfikat klienta nie jest zainstalowany, uwierzytelnianie nie powiedzie się. 

W poniższych krokach objaśniono poprzez generowanie certyfikatu klienta na podstawie certyfikatu głównego z podpisem własnym. Możesz wygenerować wiele certyfikatów klienta z tym samym certyfikatem głównym. Podczas generowania certyfikatów klienta wykonując poniższe kroki, certyfikat klienta jest automatycznie instalowany na komputerze, którego użyto do generowania certyfikatu. Jeśli chcesz zainstalować certyfikat klienta na innym komputerze klienckim, możesz wyeksportować certyfikat.

W przykładach użyto polecenia cmdlet New-SelfSignedCertificate, aby wygenerować certyfikat klienta, która wygasa po roku. Aby uzyskać informacje dodatkowych parametrów, na przykład ustawienie wartości różnych wygaśnięcia certyfikatu klienta, zobacz [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Przykład 1

W tym przykładzie należy użyć, jeśli konsola programu PowerShell nie został zamknięty po utworzeniu certyfikatu głównego z podpisem własnym. W tym przykładzie jest kontynuowane od poprzedniej sekcji i używa zadeklarowanej zmiennej "$cert". Jeśli zamknąć konsolę programu PowerShell po utworzeniu certyfikatu głównego z podpisem własnym lub tworzenia dodatkowych klientów certyfikatów w nowej sesji konsoli programu PowerShell, użyj kroków w [przykład 2](#ex2).

Zmodyfikuj i uruchomić przykład, aby wygenerować certyfikat klienta. Jeśli uruchomisz poniższy przykład bez modyfikowania go, wynik jest certyfikat klienta o nazwie "P2SChildCert".  Jeśli chcesz nazwać certyfikatu podrzędnego, coś innego, zmodyfikuj wartości nazwy Pospolitej. Nie zmieniaj TextExtension podczas uruchamiania tego przykładu. Możesz wygenerować certyfikat klienta jest automatycznie instalowany w katalogu "Certyfikaty - bieżący użytkownik\osobisty\certyfikat" na komputerze.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Przykład 2

Jeśli tworzysz dodatkowych certyfikatów klienta lub nie korzystają z tej samej sesji programu PowerShell, który został użyty do utworzenia certyfikatu głównego z podpisem własnym, wykonaj następujące kroki:

1. Zidentyfikuj certyfikat główny z podpisem własnym, który jest zainstalowany na komputerze. To polecenie cmdlet zwraca listę certyfikatów, które są zainstalowane na komputerze.

   ```powershell
   Get-ChildItem -Path “Cert:\CurrentUser\My”
   ```
2. Znajdź nazwę podmiotu na liście zwrócony, a następnie skopiuj odcisk palca, który znajduje się obok niego w pliku tekstowym. W poniższym przykładzie istnieją dwa certyfikaty. Nazwa CN jest nazwa certyfikatu głównego z podpisem własnym, z którego chcesz wygenerować certyfikat podrzędnego. W tym przypadku "P2SRootCert".

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Zadeklaruj zmienną dla certyfikatu głównego, używając odcisku palca z poprzedniego kroku. Zastąp odcisk PALCA odcisk palca certyfikatu głównego, z którego chcesz wygenerować certyfikat podrzędnego.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Na przykład w poprzednim kroku, używając odcisku palca dla P2SRootCert, zmienna wygląda następująco:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Zmodyfikuj i uruchomić przykład, aby wygenerować certyfikat klienta. Jeśli uruchomisz poniższy przykład bez modyfikowania go, wynik jest certyfikat klienta o nazwie "P2SChildCert". Jeśli chcesz nazwać certyfikatu podrzędnego, coś innego, zmodyfikuj wartości nazwy Pospolitej. Nie zmieniaj TextExtension podczas uruchamiania tego przykładu. Możesz wygenerować certyfikat klienta jest automatycznie instalowany w katalogu "Certyfikaty - bieżący użytkownik\osobisty\certyfikat" na komputerze.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="cer"></a>3. Wyeksportować klucz publiczny certyfikat główny (cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]


### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Eksportowanie certyfikatu głównego z podpisem własnym i klucz prywatny, zapisz go (opcjonalnie)

Możesz chcieć wyeksportować certyfikat główny z podpisem własnym i zapisz je bezpiecznie jako kopii zapasowej. Jeśli taka potrzeba, można później zainstalować ją na innym komputerze i generowania certyfikatów klienta. Aby wyeksportować certyfikat główny z podpisem własnym, jako plik PFX, wybierz certyfikat główny i tych samych kroków, zgodnie z opisem w [wyeksportować certyfikat klienta](#clientexport).

## <a name="clientexport"></a>4. Eksportowanie certyfikatu klienta

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]


## <a name="install"></a>5. Instalowanie wyeksportowanego certyfikatu klienta

Każdy klient, który nawiązuje połączenie z siecią wirtualną za pośrednictwem połączenia P2S wymaga certyfikatu klienta do zainstalowania lokalnie.

Aby zainstalować certyfikat klienta, zobacz [zainstalować certyfikat klienta dla połączeń punkt-lokacja](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="install"></a>6. Wykonaj kroki konfiguracji P2S

Kontynuuj konfigurację typu punkt-lokacja.

* Dla **usługi Resource Manager** kroków modelu wdrażania, zobacz [P2S skonfigurować przy użyciu uwierzytelniania certyfikatu platformy Azure natywnych](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Dla **klasycznego** kroków modelu wdrażania, zobacz [Konfigurowanie połączenia sieci VPN typu punkt-lokacja sieci wirtualnej (klasycznej)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).
* Aby uzyskać P2S Rozwiązywanie problemów z informacji, zobacz [połączeń punkt lokacja Rozwiązywanie problemów z Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
