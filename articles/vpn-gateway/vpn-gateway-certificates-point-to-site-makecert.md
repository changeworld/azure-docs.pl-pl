---
title: 'Generowanie i eksportowania certyfikatów dla lokacji punktu: MakeCert: Azure | Dokumentacja firmy Microsoft'
description: Utwórz certyfikat z podpisem własnym głównego, wyeksportować klucz publiczny i generowania certyfikatów klientów za pomocą narzędzia MakeCert.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: b2f31761e4560cf4b9b9a5b92f5de9982a663a75
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
ms.locfileid: "29345706"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Generowanie i eksportowania certyfikatów połączeń punkt-lokacja za pomocą narzędzia MakeCert

Połączenia punkt-lokacja używa certyfikatów w celu uwierzytelniania. W tym artykule pokazano, jak utworzyć certyfikat z podpisem własnym głównego i generowania certyfikatów klientów za pomocą narzędzia MakeCert. Jeśli szukasz punkt-lokacja kroków konfiguracji, takich jak sposób przekazywania certyfikatów głównych, wybierz jedną z artykułów "Konfiguruj punkt-lokacja" z następującej listy:

> [!div class="op_single_selector"]
> * [Tworzenie certyfikatów z podpisem własnym — PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Tworzenie certyfikatów z podpisem własnym — MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Skonfiguruj punkt-lokacja - Resource Manager - portalu Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Konfigurowanie programu PowerShell punkt lokacja - Resource Manager —](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Skonfiguruj punkt-lokacja — Classic - portalu Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Gdy firma Microsoft zaleca używanie [kroki Windows 10 PowerShell](vpn-gateway-certificates-point-to-site.md) do tworzenia certyfikatów, udostępniamy instrukcje te MakeCert jako opcjonalny metody. Certyfikaty, które można wygenerować za pomocą jednej z metod można zainstalować na [dowolnego systemu operacyjnego klienta obsługiwanych](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). Jednak MakeCert ma następujące ograniczenia:

* MakeCert jest przestarzały. Oznacza to, że to narzędzie można usunąć w dowolnym momencie. Wszystkie certyfikaty, które już wygenerowane za pomocą narzędzia MakeCert nie będzie mieć wpływ na podczas MakeCert nie jest już dostępny. MakeCert jest używana tylko w celu wygenerowania certyfikatów, nie jako mechanizmu sprawdzania poprawności.

## <a name="rootcert"></a>Utwórz certyfikat z podpisem własnym głównego

Poniższe kroki pokazują, jak utworzyć certyfikat z podpisem własnym za pomocą narzędzia MakeCert. Te kroki nie są właściwe modelu wdrażania. Są one ważne dla usługi Resource Manager i model klasyczny.

1. Pobierz i zainstaluj [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx).
2. Po zakończeniu instalacji, zwykle można znaleźć narzędzie makecert.exe w tej ścieżce: "C:\Program Files (x86) \Windows Kits\10\bin\<arch >". Chociaż jest to możliwe, że został zainstalowany w innej lokalizacji. Otwórz wiersz polecenia jako administrator, a następnie przejdź do lokalizacji narzędzia MakeCert. Poniższy przykład, można użyć dostosowania do właściwego położenia:

  ```cmd
  cd C:\Program Files (x86)\Windows Kits\10\bin\x64
  ```
3. Utwórz i zainstaluj certyfikat w magazynie certyfikatów osobistych na tym komputerze. Poniższy przykład tworzy odpowiadającego *.cer* przekazywanego do platformy Azure, podczas konfigurowania P2S pliku. Zamień "P2SRootCert" i 'P2SRootCert.cer' Nazwa, która ma być używany dla certyfikatu. Certyfikat znajduje się w "Certyfikaty — bieżący User\Personal\Certificates".

  ```cmd
  makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
  ```

## <a name="cer"></a>Wyeksportuj klucz publiczny (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Plik exported.cer, należy przekazać do platformy Azure. Aby uzyskać instrukcje, zobacz [skonfigurować połączenie punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Aby dodać dodatkowe zaufany certyfikat główny, zobacz [w tej sekcji](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) artykułu.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Eksportowanie certyfikatu z podpisem własnym oraz klucza prywatnego, zapisz go (opcjonalnie)

Można wyeksportować certyfikat główny z podpisem własnym i zapisze go w bezpieczne. Jeśli musisz być później zainstalować ją na innym komputerze i generować więcej certyfikaty klienta lub wyeksportować innego pliku .cer. Aby wyeksportować certyfikat główny z podpisem własnym jako pliku PFX, wybierz certyfikat główny i Zastosuj te same kroki, zgodnie z opisem w [wyeksportować certyfikat klienta](#clientexport).

## <a name="create-and-install-client-certificates"></a>Tworzenie i instalowania certyfikatów klienta

Certyfikat z podpisem własnym nie instaluj bezpośrednio na komputerze klienckim. Należy wygenerować certyfikat klienta na podstawie certyfikatu z podpisem własnym. Następnie wyeksportować i instalowania certyfikatu klienta na komputerze klienckim. Poniższe kroki nie są właściwe modelu wdrażania. Są one ważne dla usługi Resource Manager i model klasyczny.

### <a name="clientcert"></a>Generuj certyfikat klienta

Na każdym komputerze klienckim nawiązującym połączenie z siecią wirtualną za pomocą połączenia typu punkt-lokacja musi być zainstalowany certyfikat klienta w celu uwierzytelniania. Wygeneruj certyfikat klienta z certyfikatu głównego z podpisem własnym i wyeksportować i instalowania certyfikatu klienta. Jeśli certyfikat klienta nie jest zainstalowany, uwierzytelnianie nie powiedzie się. 

W poniższych krokach objaśniono za pośrednictwem generowania certyfikatu klienta z podpisem własnym głównego certyfikatu. Wiele certyfikatów klienta może generować z tym samym certyfikatem głównym. Podczas generowania certyfikatów klienta, wykonaj kroki opisane poniżej certyfikat klienta jest automatycznie zainstalowane na komputerze, który został użyty do wygenerowania certyfikatu. Jeśli chcesz zainstalować certyfikat klienta na inny komputer kliencki, można wyeksportować certyfikat.
 
1. Na tym samym komputerze, który został użyty do utworzenia certyfikatu z podpisem własnym Otwórz wiersz polecenia jako administrator.
2. Zmodyfikuj i uruchom przykład, aby wygenerować certyfikat klienta.
  * Zmień *"P2SRootCert"* nazwy generowania certyfikatu klienta z podpisem własnym katalogu głównego. Upewnij się, że używasz nazwę certyfikatu głównego, który jest niezależnie od "CN =' wartość została określona podczas tworzenia katalogu głównego podpisem.
  * Zmień *P2SChildCert* chcesz wygenerować certyfikat klienta jako nazwy.

  Po uruchomieniu poniższy przykład bez modyfikowania jej wynikiem jest certyfikat klienta o nazwie P2SChildcert w magazynie certyfikatów osobistych, który został wygenerowany na podstawie certyfikatu głównego P2SRootCert.

  ```cmd
  makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
  ```

### <a name="clientexport"></a>Eksportowanie certyfikatu klienta

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>Zainstaluj certyfikat wyeksportowany klienta

Aby zainstalować certyfikat klienta, zobacz [zainstalować certyfikat klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Kolejne kroki

Kontynuuj konfigurację punkt-lokacja. 

* Dla **Resource Manager** kroków modelu wdrażania, zobacz [P2S skonfigurować przy użyciu uwierzytelniania certyfikatu Azure natywnego](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Dla **klasycznego** kroków modelu wdrażania, zobacz [skonfigurować połączenie sieci VPN typu punkt-lokacja sieci wirtualnej (klasyczne)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Aby uzyskać informacje dotyczące rozwiązywania problemów z połączeniem typu punkt-lokacja, zobacz [Troubleshooting Azure point-to-site connections (Rozwiązywanie problemów z połączeniami typu punkt-lokacja na platformie Azure)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).