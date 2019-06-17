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
ms.openlocfilehash: 1d8bbe2fc218004116177c4c9d95777d9ec57503
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60477301"
---
# <a name="sap-hana-large-instances-deployment"></a>Wdrożenie oprogramowania SAP HANA (duże wystąpienia) 

W tym artykule przyjęto założenie, ukończono zakupu oprogramowania SAP Hana na platformie Azure (duże wystąpienia) firmy Microsoft. Przed przeczytaniem tego artykułu, aby uzyskać ogólne, zobacz [HANA, duże wystąpienia typowe terminy](hana-know-terms.md) i [HANA, duże wystąpienia jednostki SKU](hana-available-skus.md).


Firma Microsoft wymaga następujące informacje, aby wdrożyć jednostki dużych wystąpień HANA:

- Nazwa klienta.
- Informacje kontaktowe firmy (takie jak wiadomości e-mail adres i numer telefonu).
- Informacje kontaktowe techniczne (takie jak wiadomości e-mail adres i numer telefonu).
- Techniczne sieć informacje kontaktowe (w tym wiadomości e-mail adres i numer telefonu).
- Region wdrożenia platformy Azure (na przykład zachodnie stany USA, Australia Wschodnia lub Europa Północna).
- Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) jednostek SKU (Konfiguracja).
- Dla każdego regionu wdrożenia platformy Azure:
    - Wartość/29 zakres adresów IP dla połączeń ER P2P, łączących się dużych wystąpień HANA z sieciami wirtualnymi platformy Azure.
    - Prefiksie/24 blok CIDR używany dla puli adresów IP serwera dużych wystąpień HANA.
- Wartości zakresu adresów IP używanych w atrybucie przestrzeni adresów sieci wirtualnej co usługa Azure virtual network, który nawiązuje połączenie z dużych wystąpień HANA.
- Dane dla każdego systemu dużych wystąpień HANA:
  - Żądaną nazwą hosta, najlepiej z w pełni kwalifikowanej nazwy domeny.
  - Żądany adres IP dla jednostki dużych wystąpień HANA poza zakres adresów puli adresów IP z serwera. (Pierwsze 30 adresów IP w zakresie adresów puli adresów IP serwera są zarezerwowane do użytku wewnętrznego w dużych wystąpień HANA).
  - SAP HANA identyfikatorów SID / nazw dla tego wystąpienia platformy SAP HANA (wymagane do tworzenia woluminów potrzeby związane z platformą SAP HANA dysku). Identyfikator SID HANA jest potrzebny w firmie Microsoft do tworzenia uprawnienia sidadm na woluminach systemu plików NFS. Dołącz tych woluminów do jednostki dużych wystąpień HANA. Identyfikator SID HANA służy również jako jedna z części nazwy woluminów dysków, które zostać zainstalowany. Jeśli chcesz uruchomić więcej niż jedno wystąpienie oprogramowania HANA w jednostce, powinien zawierać wiele identyfikatorów SID platformy HANA. Każdy z nich pobiera oddzielny zestaw woluminów przypisane.
  - W systemie operacyjnym Linux użytkownik sidadm ma identyfikator grupy. Ten identyfikator jest wymagany do tworzenia woluminów konieczne dysku związane z platformą SAP HANA. Instalacja SAP HANA zazwyczaj tworzy grupy sapsys identyfikatorem grupy 1001. Użytkownik sidadm jest częścią tej grupy.
  - W systemie operacyjnym Linux użytkownik sidadm ma identyfikator użytkownika. Ten identyfikator jest wymagany do tworzenia woluminów konieczne dysku związane z platformą SAP HANA. Jeśli korzystasz z kilku wystąpieniach oprogramowania HANA w jednostce, Utwórz listę wszystkich użytkowników sidadm. 
- Identyfikator subskrypcji platformy Azure dla subskrypcji platformy Azure, do których platformy SAP HANA na platformie Azure HANA — duże wystąpienia będzie podłączone bezpośrednio. Ten identyfikator subskrypcji odwołuje się do subskrypcji platformy Azure, który będzie naliczona zespołu dużych wystąpień HANA lub jednostki.

Po podaniu powyższych informacji firmy Microsoft obsługuje platformy SAP HANA na platformie Azure (duże wystąpienia). Firma Microsoft wysyła informacje do łączenia sieci wirtualnych platformy Azure do dużych wystąpień HANA. Można również uzyskać dostęp do jednostki dużych wystąpień HANA.

Po wdrożeniu firmy Microsoft, jej połączyć się z dużych wystąpień HANA, należy użyć następującej procedury:

1. [Łączenie maszyn wirtualnych platformy Azure do dużych wystąpień HANA](hana-connect-azure-vm-large-instances.md)
2. [Łączenie sieci wirtualnej do dużych wystąpień HANA usługi ExpressRoute](hana-connect-vnet-express-route.md)
3. [Wymagania dodatkowe sieci (opcjonalnie)](hana-additional-network-requirements.md)

