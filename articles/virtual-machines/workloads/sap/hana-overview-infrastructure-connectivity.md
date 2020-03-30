---
title: Infrastruktura i łączność z SAP HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Skonfiguruj wymaganą infrastrukturę łączności do korzystania z sap HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cea89087742f1987f693b8bfb627bd71038a0c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616923"
---
# <a name="sap-hana-large-instances-deployment"></a>Wdrożenie SAP HANA (duże wystąpienia) 

W tym artykule przyjęto założenie, że zakup usługi SAP HANA na platformie Azure (duże wystąpienia) od firmy Microsoft został ukończony. Przed przeczytaniem tego artykułu, aby uzyskać ogólne tło, zobacz [HANA dużych wystąpień wspólnych terminów](hana-know-terms.md) i [HANA dużych wystąpień jednostek SKU](hana-available-skus.md).


Firma Microsoft wymaga następujących informacji do wdrożenia jednostek dużych wystąpień HANA:

- Nazwa klienta.
- Biznesowe informacje kontaktowe (w tym adres e-mail i numer telefonu).
- Techniczne dane kontaktowe (w tym adres e-mail i numer telefonu).
- Techniczne informacje kontaktowe sieci (w tym adres e-mail i numer telefonu).
- Region wdrażania platformy Azure (na przykład zachodnie stany USA, Australia Wschodnia lub Europa Północna).
- SAP HANA na platformie Azure (duże wystąpienia) SKU (konfiguracja).
- Dla każdego regionu wdrażania platformy Azure:
    - Zakres adresów IP /29 dla połączeń ER-P2P łączących sieci wirtualne platformy Azure z dużymi wystąpieniami HANA.
    - Blok /24 CIDR używany dla puli adresów IP serwera dużych wystąpień HANA.
    - Opcjonalnie w przypadku korzystania z [usługi ExpressRoute Global Reach,](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) aby włączyć routing bezpośredni z lokalnych do jednostek dużych wystąpień HANA lub routingu między jednostkami dużych wystąpień HANA w różnych regionach platformy Azure, należy zarezerwować inny /29 zakres adresów IP. Ten konkretny zakres nie może pokrywać się z żadnym z innych zakresów adresów IP zdefiniowanych wcześniej.
- Wartości zakresu adresów IP używane w atrybucie przestrzeni adresowej sieci wirtualnej każdej sieci wirtualnej platformy Azure, która łączy się z wystąpieniami dużymi hana.
- Dane dla każdego systemu dużych wystąpień HANA:
  - Żądana nazwa hosta, najlepiej z w pełni kwalifikowaną nazwą domeny.
  - Żądany adres IP dla jednostki wystąpienia dużego HANA poza zakresem adresów puli adresów IP serwera. (Pierwsze 30 adresów IP w zakresie adresów puli adresów IP serwera są zarezerwowane do użytku wewnętrznego w dużych wystąpieniach HANA).
  - Nazwa identyfikatora SID SAP HANA dla wystąpienia SAP HANA (wymagana do utworzenia niezbędnych woluminów dyskowych związanych z SAP HANA). Firma Microsoft potrzebuje identyfikatora SID HANA do tworzenia uprawnień do sidadm na woluminach NFS. Woluminy te dołączyć do jednostki wystąpienia dużych HANA. Identyfikator SID HANA jest również używany jako jeden ze składników nazw woluminów dyskowych, które są montowane. Jeśli chcesz uruchomić więcej niż jedno wystąpienie HANA na urządzeniu, należy wyświetlić listę wielu identyfikatorów HANA. Każdy z nich otrzymuje oddzielny zestaw przypisanych woluminów.
  - W systemie operacyjnym Linux użytkownik sidadm ma identyfikator grupy. Ten identyfikator jest wymagany do utworzenia niezbędnych woluminów dyskowych związanych z SAP HANA. Instalacja SAP HANA zwykle tworzy grupę sapsys o identyfikatorze grupy 1001. Użytkownik sidadm jest częścią tej grupy.
  - W systemie operacyjnym Linux użytkownik sidadm ma identyfikator użytkownika. Ten identyfikator jest wymagany do utworzenia niezbędnych woluminów dyskowych związanych z SAP HANA. Jeśli używasz kilku wystąpień HANA na urządzeniu, wyświetl listę wszystkich użytkowników sidadm. 
- Identyfikator subskrypcji platformy Azure dla subskrypcji platformy Azure, z którym sap HANA na platformie Azure HANA dużych wystąpień będą bezpośrednio połączone. Ten identyfikator subskrypcji odwołuje się do subskrypcji platformy Azure, która będzie obciążana jednostką lub jednostkami wystąpienia dużego hana.

Po podasz powyższe informacje, microsoft przepisów SAP HANA na platformie Azure (duże wystąpienia). Firma Microsoft wysyła informacje w celu połączenia sieci wirtualnych platformy Azure z dużymi wystąpieniami HANA. Można również uzyskać dostęp do jednostek dużych wystąpień HANA.

Użyj następującej sekwencji, aby połączyć się z dużymi wystąpieniami HANA po wdrożeniu go przez firmę Microsoft:

1. [Łączenie maszyn wirtualnych platformy Azure z dużymi wystąpieniami hana](hana-connect-azure-vm-large-instances.md)
2. [Podłączanie sieci wirtualnej do dużych wystąpień hana usługi ExpressRoute](hana-connect-vnet-express-route.md)
3. [Dodatkowe wymagania sieciowe (opcjonalnie)](hana-additional-network-requirements.md)

