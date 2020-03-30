---
title: Konfigurowanie ustawień serwera proxy usługi mobilności dla platformy Azure w celu odzyskiwania po awarii platformy Azure | Dokumenty firmy Microsoft
description: Zawiera szczegółowe informacje na temat konfigurowania usługi mobilności, gdy klienci korzystają z serwera proxy w środowisku źródłowym.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 3d33b5a89a718a41e5c547551f6e7eb4f7033a63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503129"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Konfigurowanie ustawień serwera proxy usługi mobilności dla platformy Azure w celu odzyskiwania po awarii platformy Azure

Ten artykuł zawiera wskazówki dotyczące dostosowywania konfiguracji sieci na docelowej maszynie wirtualnej platformy Azure podczas replikowania i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do drugiego przy użyciu [usługi Azure Site Recovery.](site-recovery-overview.md)

Celem tego dokumentu jest zapewnienie kroków w celu skonfigurowania ustawień serwera proxy dla usługi Azure Site Recovery Mobility Service w scenariuszu odzyskiwania po awarii platformy Azure na platformie Azure. 

Serwery proxy to bramy sieciowe, które zezwalają na/nie zezwalają na połączenia sieciowe z punktami końcowymi. Zazwyczaj serwer proxy jest komputerem poza komputerem klienckim, który próbuje uzyskać dostęp do punktów końcowych sieci. Lista pomijania umożliwia klientowi nawiązywania połączeń bezpośrednio do punktów końcowych bez przechodzenia przez serwer proxy. Nazwa użytkownika i hasło mogą być opcjonalnie ustawione dla serwera proxy przez administratorów sieci, tak aby tylko uwierzytelnini klienci mogli używać serwera proxy. 

## <a name="before-you-start"></a>Przed rozpoczęciem

Dowiedz się, jak odzyskiwanie witryny zapewnia odzyskiwanie po awarii w [tym scenariuszu.](azure-to-azure-architecture.md)
Zapoznaj się ze [wskazówkami dotyczącymi sieci](azure-to-azure-about-networking.md) podczas replikowania i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do drugiego przy użyciu [usługi Azure Site Recovery.](site-recovery-overview.md)
Upewnij się, że serwer proxy jest skonfigurowany odpowiednio w zależności od potrzeb organizacji.

## <a name="configure-the-mobility-service"></a>Konfigurowanie usługi mobilności

Usługa mobilności obsługuje tylko nieuwierzyte serwery proxy. Zapewnia dwa sposoby wprowadzania szczegółów serwera proxy do komunikacji z punktami końcowymi odzyskiwania lokacji. 

### <a name="method-1-auto-detection"></a>Metoda 1: Automatyczne wykrywanie

Usługa mobilności automatycznie wykrywa ustawienia serwera proxy na podstawie ustawień środowiska lub ustawień IE (tylko w systemie Windows) podczas włączania replikacji. 

- System operacyjny Windows: Podczas włączania replikacji usługa mobilności wykrywa ustawienia serwera proxy skonfigurowane w programie Internet Explorer dla użytkownika systemu lokalnego. Aby skonfigurować serwer proxy dla konta systemu lokalnego, administrator może użyć psexec do uruchomienia wiersza polecenia, a następnie programu Internet Explorer. 
- System operacyjny Windows: Ustawienia serwera proxy są skonfigurowane jako zmienne środowiskowe http_proxy i no_proxy. 
- Linux OS: Ustawienia serwera proxy są skonfigurowane w /etc/profile lub /etc/environment jako zmienne środowiskowe http_proxy, no_proxy. 
- Automatycznie wykryte ustawienia serwera proxy są zapisywane w pliku konfiguracyjnym serwera proxy usługi Mobilności ProxyInfo.conf 
- Domyślna lokalizacja pliku ProxyInfo.conf 
    - Windows: C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux: /usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>Metoda 2: Zapewnienie niestandardowych ustawień serwera proxy aplikacji

W takim przypadku klient udostępnia niestandardowe ustawienia serwera proxy aplikacji w pliku konfiguracyjnym usługi Mobilności ProxyInfo.conf. Ta metoda umożliwia klientom dostarczanie serwera proxy tylko dla usługi mobilności lub innego serwera proxy usługi Azure Site Recovery Mobility Service niż serwer proxy (lub brak serwera proxy) dla pozostałych aplikacji na komputerze.

## <a name="proxy-template"></a>Szablon serwera proxy
Plik ProxyInfo.conf zawiera następujący szablonhttp://1.2.3.4 [proxy] Adres= Port=5678 BypassList=hypervrecoverymanager.windowsazure.com,login.microsoftonline.com,blob.core.windows.net. BypassList nie obsługuje symboli wieloznacznych, takich jak "*.windows.net", ale dając windows.net jest wystarczająco dobry, aby ominąć. 

## <a name="next-steps"></a>Następne kroki:
- Przeczytaj [wskazówki dotyczące sieci do](site-recovery-azure-to-azure-networking-guidance.md) replikowania maszyn wirtualnych platformy Azure.
- Wdrażanie odzyskiwania po awarii przez [replikowanie maszyn wirtualnych platformy Azure](site-recovery-azure-to-azure.md).