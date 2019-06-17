---
title: 'Generowanie i eksportowanie certyfikatów for Point-to-Site: MakeCert : Azure | Microsoft Docs'
description: Utworzyć certyfikat główny z podpisem własnym, a następnie wyeksportować klucz publiczny i generowania certyfikatów klienta, za pomocą narzędzia MakeCert.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/05/2018
ms.author: cherylmc
ms.openlocfilehash: 973c0aa3bd187e963f15adbe34955d6bc9fa612d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60768110"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Generowanie i eksportowanie certyfikatów dla połączeń punkt-lokacja za pomocą narzędzia MakeCert

Połączenia punkt-lokacja używają certyfikatów do uwierzytelniania. W tym artykule pokazano, jak utworzyć certyfikat główny z podpisem własnym i generowania certyfikatów klienta, za pomocą narzędzia MakeCert. Jeśli potrzebujesz innego certyfikatu instrukcje, zobacz [certyfikatów — PowerShell](vpn-gateway-certificates-point-to-site.md) lub [certyfikaty — Linux](vpn-gateway-certificates-point-to-site-linux.md).

Gdy firma Microsoft zaleca używanie [kroki systemu Windows 10 PowerShell](vpn-gateway-certificates-point-to-site.md) do tworzenia certyfikatów, firma Microsoft zapewnia następujące instrukcje MakeCert jako opcjonalny metody. Certyfikaty, które są generowane za pomocą jednej z metod, które można zainstalować na [dowolnym systemie operacyjnym klienta obsługiwanych](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). Jednak użycie narzędzia MakeCert ma następujące ograniczenia:

* Narzędzie MakeCert jest przestarzałe. Oznacza to, że to narzędzie można usunąć w dowolnym momencie. Wszystkie certyfikaty, które już wygenerowany za pomocą narzędzia MakeCert nie będą modyfikowane, gdy narzędzie MakeCert nie jest już dostępna. Narzędzie MakeCert jest używana tylko w celu wygenerowania certyfikatów, nie jako mechanizmu sprawdzania poprawności.

## <a name="rootcert"></a>Tworzenie certyfikatu głównego z podpisem własnym

Poniższe kroki pokazują, jak utworzyć certyfikat z podpisem własnym za pomocą narzędzia MakeCert. Te kroki nie są określonego modelu wdrażania. Są one prawidłowe dla usługi Resource Manager i model klasyczny.

1. Pobierz i zainstaluj [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx).
2. Po zakończeniu instalacji można zwykle znaleźć narzędzie makecert.exe dla danej ścieżki: 'C:\Program Files (x86)\Windows Kits\10\bin\<arch>'. Chociaż istnieje możliwość, że został zainstalowany w innej lokalizacji. Otwórz wiersz polecenia jako administrator i przejdź do lokalizacji narzędzia MakeCert. Można skorzystaj z następującego przykładu, dostosowując do właściwego położenia:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Utwórz i zainstaluj certyfikat w osobistym magazynie certyfikatów na komputerze. Poniższy przykład tworzy odpowiedni *cer* pliku, który możesz przekazać na platformę Azure podczas konfigurowania P2S. Zastąp nazwę, która ma być użyty dla certyfikatu "P2SRootCert" i nazwę "P2SRootCert.cer". Certyfikat znajduje się w sieci "Certyfikaty - bieżący użytkownik\osobisty\certyfikat".

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="cer"></a>Wyeksportuj klucz publiczny (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Plik exported.cer należy przekazać na platformę Azure. Aby uzyskać instrukcje, zobacz [Konfigurowanie połączenia typu punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Aby dodać dodatkowe zaufanego certyfikatu głównego, zobacz [w tej sekcji](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) tego artykułu.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Eksportowanie certyfikatu z podpisem własnym i klucz prywatny, zapisz go (opcjonalnie)

Możesz chcieć wyeksportować certyfikat główny z podpisem własnym i zapisz je bezpiecznie. Jeśli muszą być później zainstalować ją na innym komputerze i generowania certyfikatów klienta lub wyeksportować inny plik cer. Aby wyeksportować certyfikat główny z podpisem własnym, jako plik PFX, wybierz certyfikat główny i tych samych kroków, zgodnie z opisem w [wyeksportować certyfikat klienta](#clientexport).

## <a name="create-and-install-client-certificates"></a>Tworzenie i instalowanie certyfikatów klienta

Nie instaluj certyfikatu z podpisem własnym bezpośrednio na komputerze klienckim. Należy wygenerować certyfikat klienta z certyfikatu z podpisem własnym. Następnie eksportowanie i instalowanie certyfikatu klienta na komputerze klienckim. Poniższe kroki nie są określonego modelu wdrażania. Są one prawidłowe dla usługi Resource Manager i model klasyczny.

### <a name="clientcert"></a>Generuj certyfikat klienta

Na każdym komputerze klienckim nawiązującym połączenie z siecią wirtualną za pomocą połączenia typu punkt-lokacja musi być zainstalowany certyfikat klienta w celu uwierzytelniania. Generowanie certyfikatu klienta z certyfikatu głównego z podpisem własnym i wyeksportować i instalowania certyfikatu klienta. Jeśli certyfikat klienta nie jest zainstalowany, uwierzytelnianie nie powiedzie się. 

W poniższych krokach objaśniono poprzez generowanie certyfikatu klienta na podstawie certyfikatu głównego z podpisem własnym. Możesz wygenerować wiele certyfikatów klienta z tym samym certyfikatem głównym. Podczas generowania certyfikatów klienta wykonując poniższe kroki, certyfikat klienta jest automatycznie instalowany na komputerze, którego użyto do generowania certyfikatu. Jeśli chcesz zainstalować certyfikat klienta na innym komputerze klienckim, możesz wyeksportować certyfikat.
 
1. Na tym samym komputerze, który został użyty do utworzenia certyfikatu z podpisem własnym Otwórz wiersz polecenia jako administrator.
2. Zmodyfikuj i uruchomić przykład, aby wygenerować certyfikat klienta.
   * Zmiana *"P2SRootCert"* nazwę głównego z podpisem własnym, który jest generowany certyfikat klienta z. Upewnij się, że używasz nazwy certyfikatu głównego, który jest niezależnie od "CN =" wartość została określona podczas tworzenia głównego z podpisem własnym.
   * Zmiana *P2SChildCert* nazwę chcesz wygenerować certyfikat klienta to.

   Jeśli uruchomisz poniższy przykład bez modyfikowania go, wynik jest certyfikat klienta o nazwie P2SChildcert w magazynie certyfikatów osobistych, który został wygenerowany z certyfikatu głównego P2SRootCert.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="clientexport"></a>Eksportowanie certyfikatu klienta

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>Zainstaluj certyfikat wyeksportowany klienta

Aby zainstalować certyfikat klienta, zobacz [Instalowanie certyfikatu klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Kolejne kroki

Kontynuuj konfigurację typu punkt-lokacja. 

* Dla **usługi Resource Manager** kroków modelu wdrażania, zobacz [P2S skonfigurować przy użyciu uwierzytelniania certyfikatu platformy Azure natywnych](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Dla **klasycznego** kroków modelu wdrażania, zobacz [Konfigurowanie połączenia sieci VPN typu punkt-lokacja sieci wirtualnej (klasycznej)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Aby uzyskać informacje dotyczące rozwiązywania problemów z połączeniem typu punkt-lokacja, zobacz [Troubleshooting Azure point-to-site connections (Rozwiązywanie problemów z połączeniami typu punkt-lokacja na platformie Azure)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).