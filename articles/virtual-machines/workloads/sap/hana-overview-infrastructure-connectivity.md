---
title: Infrastruktura i łączność do platformy SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Konfigurowanie infrastruktury wymaga połączenia do używania oprogramowania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c61dffc6fd9d0c65f5e925daebdf2a02cfb5d6ba
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161360"
---
# <a name="sap-hana-large-instances-deployment"></a>Wdrożenie oprogramowania SAP HANA (duże wystąpienia) 

Po zakupie platformy SAP HANA na platformie Azure (duże wystąpienia) jest aktualnie finalizowana między Tobą i zespół kont Microsoft enterprise, następujących informacji jest wymagane przez firmę Microsoft do wdrażania dużych jednostek wystąpienie oprogramowania HANA:

- Nazwa klienta
- Informacje kontaktowe firmy (w tym adres e-mail i numer telefonu)
- Skontaktuj się z pomocą informacje techniczne (w tym wiadomości e-mail adres i numer telefonu)
- Technicznych skontaktuj się z pomocą informacje o sieci (w tym wiadomości e-mail adres i numer telefonu)
- Region wdrożenia platformy Azure (zachodnie stany USA, wschodnie stany USA, Australia Wschodnia, Australia Południowo-Wschodnia, Europa Zachodnia i Europa Północna, począwszy od lipca 2017 r.)
- Upewnij się, SAP HANA na platformy Azure (duże wystąpienia) jednostek SKU (Konfiguracja)
- Jak już szczegółowo opisane w dokumencie omówienie i architektura dla dużych wystąpień HANA dla każdego regionu platformy Azure, wdrażane na:
    - Wartość/29 zakresu adresów IP dla połączeń ER P2P, która łączenie sieci wirtualnych platformy Azure do dużych wystąpień HANA
    - Prefiksie/24 blok CIDR używany dla platformy HANA dużych wystąpień serwera puli adresów IP
- Wartości zakresu adresów IP używanych w atrybucie przestrzeni adresowej sieci wirtualnej w każdej sieci wirtualnej platformy Azure, który nawiązuje połączenie z dużych wystąpień HANA
- Dane dla każdego systemu dużych wystąpień HANA:
  - Żądany hostname - najlepiej z w pełni kwalifikowaną nazwę domeny.
  - Żądany adres IP dla jednostki dużych wystąpień HANA poza zakres adresów puli adresów IP serwerów — należy pamiętać o tym, że pierwsze 30 adresów IP w zakresie adresów puli adresów IP serwerów są zarezerwowane do wewnętrznego użycia w ramach dużych wystąpień HANA
  - SAP HANA identyfikatorów SID / nazw dla tego wystąpienia platformy SAP HANA (wymagane do tworzenia woluminów potrzeby związane z platformą SAP HANA dysku). Identyfikator SID HANA jest wymagana podczas tworzenia uprawnienia sidadm na woluminach systemu plików NFS, wprowadzenie dołączone do jednostki dużych wystąpień HANA. Również służy jako jeden ze składników nazwy woluminów dysków, które zostać zainstalowany. Jeśli chcesz uruchomić więcej niż jedno wystąpienie oprogramowania HANA w jednostce, musisz listy wiele identyfikatorów SID platformy HANA. Każdy z nich pobiera oddzielny zestaw woluminów przypisane.
  - Identyfikator grupy, którą ma użytkownik sidadm w systemie operacyjnym Linux jest wymagana do utworzenia woluminy związane z platformą SAP HANA dostępnego na dysku. Instalacja SAP HANA zazwyczaj tworzy grupy sapsys identyfikatorem grupy 1001. Użytkownik sidadm jest częścią tej grupy
  - Identyfikator użytkownika, który użytkownik sidadm ma w systemie operacyjnym Linux jest wymagany do tworzenia woluminów konieczne dysku związane z platformą SAP HANA. Jeśli używasz wielu wystąpień HANA w jednostce, należy wyświetlić listę wszystkich <sid>użytkowników usługi adm 
- Identyfikator subskrypcji platformy Azure dla subskrypcji platformy Azure, do których platformy SAP HANA na platformie Azure HANA — duże wystąpienia będzie podłączone bezpośrednio. Ten identyfikator subskrypcji odwołuje się do subskrypcji platformy Azure, który będzie naliczona jedn dużych wystąpień HANA.

Po podaniu informacji firma Microsoft udostępnia oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) i zwróci informacje niezbędne do łączenia sieci wirtualnych platformy Azure do dużych wystąpień HANA i dostęp do jednostki dużych wystąpień HANA.

Przed przeczytaniem tego artykułu, zapoznaj się z [dużych wystąpień HANA typowe terminy](hana-know-terms.md) i [jednostek SKU platformy HANA dużych wystąpień](hana-available-skus.md).

Połączyć się z dużych wystąpień HANA po jej wdrożeniu przez firmę Microsoft, można użyć następującej sekwencji:

1. [Łączenie maszyn wirtualnych platformy Azure do dużych wystąpień HANA](hana-connect-azure-vm-large-instances.md)
2. [Łączenie sieci wirtualnej usługi expressroute dużych wystąpień HANA](hana-connect-vnet-express-route.md)
3. [Wymagania dodatkowe sieci (opcjonalnie)](hana-additional-network-requirements.md)

**Następne kroki**

- Zapoznaj się [łączenie maszyn wirtualnych platformy Azure do dużych wystąpień HANA](hana-connect-azure-vm-large-instances.md).
- Zapoznaj się [procesu łączenia sieci z usługą ExpressRoute dużych wystąpień HANA](hana-connect-vnet-express-route.md).