---
title: 'Brama sieci VPN platformy Azure: generowanie certyfikatów eksportu & dla P2S: MakeCert'
description: Utwórz certyfikat główny z podpisem własnym, wyeksportuj klucz publiczny i wygeneruj certyfikaty klientów przy użyciu funkcji MakeCert.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/05/2018
ms.author: cherylmc
ms.openlocfilehash: ad2ab31e6771efc54238d5747863fa2a9bb2f356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75833974"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Generowanie i eksportowanie certyfikatów dla połączeń typu punkt-lokacja przy użyciu funkcji MakeCert

Połączenia typu "punkt-lokacja" używają certyfikatów do uwierzytelniania. W tym artykule pokazano, jak utworzyć certyfikat główny z podpisem własnym i wygenerować certyfikaty klienta przy użyciu funkcji MakeCert. Jeśli szukasz różnych instrukcji certyfikatów, zobacz [Certyfikaty - PowerShell](vpn-gateway-certificates-point-to-site.md) lub [Certyfikaty - Linux](vpn-gateway-certificates-point-to-site-linux.md).

Chociaż zaleca się użycie [kroków programu Windows 10 PowerShell](vpn-gateway-certificates-point-to-site.md) do tworzenia certyfikatów, udostępniamy te instrukcje MakeCert jako opcjonalną metodę. Certyfikaty generowane przy użyciu dowolnej metody można zainstalować w [dowolnym obsługiwanym systemie operacyjnym klienta](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). Jednak MakeCert ma następujące ograniczenia:

* MakeCert jest przestarzały. Oznacza to, że to narzędzie może zostać usunięte w dowolnym momencie. Wszystkie certyfikaty, które zostały już wygenerowane przy użyciu funkcji MakeCert, nie będą miały wpływu, gdy MakeCert nie będzie już dostępny. MakeCert jest używany tylko do generowania certyfikatów, a nie jako mechanizm sprawdzania poprawności.

## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Tworzenie certyfikatu głównego z podpisem własnym

W poniższych krokach pokazano, jak utworzyć certyfikat z podpisem własnym przy użyciu funkcji MakeCert. Te kroki nie są specyficzne dla modelu wdrożenia. Są one ważne zarówno dla Menedżera zasobów, jak i klasyczne.

1. Pobierz i zainstaluj [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx).
2. Po instalacji narzędzie makecert.exe można zazwyczaj znaleźć pod tą ścieżką: "C:\Program Files (x86)\Windows Kits\10\bin\<arch>". Chociaż możliwe, że został zainstalowany w innej lokalizacji. Otwórz wiersz polecenia jako administrator i przejdź do lokalizacji narzędzia MakeCert. Można użyć następującego przykładu, dostosowując się do właściwej lokalizacji:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Utwórz i zainstaluj certyfikat w magazynie certyfikatów osobistych na komputerze. Poniższy przykład tworzy odpowiedni plik *cer,* który można przekazać na platformę Azure podczas konfigurowania P2S. Zastąp "P2SRootCert" i "P2SRootCert.cer" nazwą, której chcesz użyć dla certyfikatu. Certyfikat znajduje się w folderze "Certyfikaty — bieżący użytkownik\Osobisty\Certyfikaty".

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="export-the-public-key-cer"></a><a name="cer"></a>Eksportowanie klucza publicznego (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Plik exported.cer musi zostać przekazany na platformę Azure. Aby uzyskać instrukcje, zobacz [Konfigurowanie połączenia typu punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Aby dodać dodatkowy zaufany certyfikat główny, zobacz [tę sekcję](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) artykułu.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Eksportowanie certyfikatu z podpisem własnym i klucza prywatnego w celu jego przechowywania (opcjonalnie)

Można wyeksportować certyfikat główny z podpisem własnym i bezpiecznie go przechowywać. W razie potrzeby można go później zainstalować na innym komputerze i wygenerować więcej certyfikatów klienta lub wyeksportować inny plik cer. Aby wyeksportować certyfikat główny z podpisem własnym jako .pfx, wybierz certyfikat główny i użyj tych samych kroków, jak opisano w obszarze [Eksportowanie certyfikatu klienta](#clientexport).

## <a name="create-and-install-client-certificates"></a>Tworzenie i instalowanie certyfikatów klientów

Certyfikat z podpisem własnym nie jest instalowyny bezpośrednio na komputerze klienckim. Należy wygenerować certyfikat klienta z certyfikatu z podpisem własnym. Następnie należy wyeksportować i zainstalować certyfikat klienta na komputerze klienckim. Poniższe kroki nie są specyficzne dla modelu wdrożenia. Są one ważne zarówno dla Menedżera zasobów, jak i klasyczne.

### <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Generowanie certyfikatu klienta

Na każdym komputerze klienckim nawiązującym połączenie z siecią wirtualną za pomocą połączenia typu punkt-lokacja musi być zainstalowany certyfikat klienta w celu uwierzytelniania. Certyfikat klienta jest generowany z certyfikatu głównego z podpisem własnym, a następnie eksportuje i instaluje certyfikat klienta. Jeśli certyfikat klienta nie jest zainstalowany, uwierzytelnianie kończy się niepowodzeniem. 

Poniższe kroki przebiegają przez generowanie certyfikatu klienta z certyfikatu głównego z podpisem własnym. Można wygenerować wiele certyfikatów klienta z tego samego certyfikatu głównego. Podczas generowania certyfikatów klientów przy użyciu poniższych kroków certyfikat klienta jest automatycznie instalowany na komputerze używanym do generowania certyfikatu. Jeśli chcesz zainstalować certyfikat klienta na innym komputerze klienckim, możesz go wyeksportować.
 
1. Na tym samym komputerze, który został użyty do utworzenia certyfikatu z podpisem własnym, otwórz wiersz polecenia jako administrator.
2. Zmodyfikuj i uruchom próbkę, aby wygenerować certyfikat klienta.
   * Zmień *"P2SRootCert"* na nazwę katalogu głównego z podpisem własnym, z którego generujesz certyfikat klienta. Upewnij się, że używasz nazwy certyfikatu głównego, która jest niezależnie od wartości "CN =", która została określona podczas tworzenia katalogu głównego z podpisem własnym.
   * Zmień *P2SChildCert* na nazwę, którą chcesz wygenerować certyfikat klienta.

   Jeśli uruchomisz poniższy przykład bez modyfikowania go, wynikiem jest certyfikat klienta o nazwie P2SChildcert w magazynie certyfikatów osobistych, który został wygenerowany z certyfikatu głównego P2SRootCert.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="export-a-client-certificate"></a><a name="clientexport"></a>Eksportowanie certyfikatu klienta

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install-an-exported-client-certificate"></a><a name="install"></a>Instalowanie wyeksportowanego certyfikatu klienta

Aby zainstalować certyfikat klienta, zobacz [Instalowanie certyfikatu klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Następne kroki

Kontynuuj konfigurację punktu do lokacji. 

* Aby **zapoznać się z** instrukcjami procesu wdrażania usługi Resource Manager, zobacz [Konfigurowanie systemu P2S przy użyciu natywnego uwierzytelniania certyfikatów platformy Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Aby zapoznać się z **klasycznymi** krokami modelu wdrażania, zobacz [Konfigurowanie połączenia sieci VPN typu punkt-lokacja do sieci wirtualnej (klasycznej).](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Aby uzyskać informacje dotyczące rozwiązywania problemów z połączeniem typu punkt-lokacja, zobacz [Troubleshooting Azure point-to-site connections (Rozwiązywanie problemów z połączeniami typu punkt-lokacja na platformie Azure)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).