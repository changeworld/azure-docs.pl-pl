---
title: Przejście do publicznych certyfikatów urzędu certyfikacji dla bram P2S | Azure VPN Gateway | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci pomyślnie przejść do nowych publicznych certyfikatów urzędu certyfikacji P2S bram.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: cherylmc
ms.openlocfilehash: 1d45e1a5e4053ead4330967c5e250c0797c19fe7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827470"
---
# <a name="transition-to-a-public-ca-gateway-certificate-for-p2s"></a>Przejście do certyfikatu bramy publicznego urzędu certyfikacji dla połączenia punkt-lokacja

Usługa Azure VPN Gateway nie jest już wystawia certyfikaty z podpisem własnym poziomie platformy Azure do jego bram dla połączeń P2S. Wystawione certyfikaty są teraz podpisany przez publiczny urząd certyfikacji (CA). Jednak niektóre starsze bramy mogą nadal korzystać z certyfikatów z podpisem własnym. Te certyfikaty z podpisem własnym są prawie daty ważności i musi przejść do publicznych certyfikatów urzędu certyfikacji.

>[!NOTE]
> * Certyfikaty z podpisem własnym używany do uwierzytelniania klientów P2S nie ma wpływu ta zmiana certyfikatu poziomie platformy Azure. Można nadal wystawiania i używać certyfikatów z podpisem własnym jako normalny.
>

Certyfikaty, w tym kontekście są dodatkowy certyfikat poziomie platformy Azure. Nie są one łańcuchów certyfikatów, używane podczas generowania własnych certyfikatów głównych z podpisem własnym i certyfikatów klienta do uwierzytelniania. Te certyfikaty pozostaną niezmienione i wygaśnie w dniu dat wygenerowany ich nie skonfigurują.

Wcześniej certyfikat z podpisem własnym dla bramy (wystawiony przez platformę Azure w tle) nie trzeba aktualizować co 18 miesięcy. Pliki konfiguracji klienta sieci VPN trzeba było wygenerowanie konfiguracji węzłów i został przeniesiony na wszystkich klientach P2S. Przenoszenie do publicznych certyfikatów urzędu certyfikacji, który eliminuje to ograniczenie. Oprócz przejścia dla certyfikatów zmiana ta udostępnia również ulepszenia, lepsze metryki i ulepszona stabilność.

Ta zmiana dotyczy tylko starsze bramy. Jeśli certyfikatu bramy musi miesięcznego okresu nastąpi przejście, otrzymasz komunikat lub wyskakującego powiadomienia w witrynie Azure portal. Można sprawdzić, brama ma wpływ wykonując kroki opisane w tym artykule.

> [!IMPORTANT]
> Przejście do publicznych certyfikatów urzędu certyfikacji została zakończona w dniu 12 maja 2019 r. **W tym dokumencie zostaną usunięte w dniu 31 maja 2019 r.**

## <a name="1-verify-your-certificate"></a>1. Zweryfikuj swój certyfikat

### <a name="resource-manager"></a>Resource Manager

1. Sprawdź, jeśli jest narażony na tę aktualizację. Pobieranie bieżącej konfiguracji klienta sieci VPN przy użyciu kroków w [w tym artykule](point-to-site-vpn-client-configuration-azure-cert.md).

2. Otwórz lub Wyodrębnij plik zip i przejdź do folderu "Generic". W folderze ogólnym zobaczysz dwa pliki, z których jedna jest *VPNSettings.xml*.
3. Otwórz *VPNSettings.xml* w dowolnym Podgląd i edytorem xml. W pliku xml Wyszukaj następujące pola:

   * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
   * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. Jeśli *ServerCertRotCn* i *ServerCertIssuerCn* są "DigiCert globalnego głównego urzędu certyfikacji", użytkownik nie ma wpływu tej aktualizacji i nie ma potrzeby wykonaj kroki opisane w tym artykule. Jeśli jednak pokazują coś innego certyfikatu bramy jest częścią aktualizacji i zostanie przeniesiona.

### <a name="classic"></a>Wdrożenie klasyczne

1. Na komputerze klienckim przejdź do ścieżki `%appdata%/Microsoft/Network/Connections/Cm/<gatewayID>`. W folderze identyfikator bramy można wyświetlić certyfikat.
2. Na karcie Ogólne dla certyfikatu sprawdź, czy urząd wystawiający certyfikaty jest "DigiCert globalnego głównego urzędu certyfikacji". Jeśli masz coś innego niż ten urząd wystawiający certyfikat bramy jest częścią aktualizacji i zostanie przeniesiona.

## <a name="2-check-certificate-transition-schedule"></a>2. Certyfikat przejścia harmonogram sprawdzania

Jeśli Twój certyfikat wkrótce część tej aktualizacji, zostaną przeniesione z certyfikatem bramy. Zapoznaj się **ważne** Uwaga do czasu przejścia. Po aktualizacji klientów P2S nie będzie można połączyć się przy użyciu ich stary profil. Należy wygenerować nowe profile klienta sieci VPN i zainstalować je na klientach.

## <a name="3-generate-vpn-client-configuration-profile"></a>3. Generowanie profilu konfiguracji klienta sieci VPN

Po przejściu certyfikatu należy pobrać nowy profil sieci VPN (plików konfiguracji klienta sieci VPN) w witrynie Azure portal. Aby uzyskać instrukcje, zobacz [tworzenie i instalowanie plików konfiguracji klienta sieci VPN](point-to-site-vpn-client-configuration-azure-cert.md). Nie trzeba generować nowe certyfikaty klienta.

## <a name="4-deploy-vpn-client-profile"></a>4. Wdróż profil klienta VPN

Wdróż nowy profil do wszystkich klientów sieci VPN typu punkt lokacja. Klienci sieci VPN spowoduje utratę połączenia, dopóki nowy profil sieci VPN dla połączeń punkt lokacja nie zostanie pobrana i wdrażane na urządzeniach klienckich. Certyfikaty klienta, które są już zainstalowane na komputerach klienckich sieci VPN nie trzeba go ponownie wydać.

## <a name="5-connect-the-vpn-client"></a>5. Łączenie klienta sieci VPN

Łączenie z platformą Azure z klienta sieci VPN w zwykły sposób.
