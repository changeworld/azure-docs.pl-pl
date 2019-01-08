---
title: Jak rozwiązywać typowe problemy występujące podczas tworzenia dysku VHD | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące rozwiązywania problemów i problemy występujące podczas tworzenia dysku VHD.
services: Azure Marketplace
documentationcenter: ''
author: v-miclar
manager: ''
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: hascipio; v-divte
ROBOTS: NOINDEX
ms.openlocfilehash: e58a5f8901b2ab53d47095e1d5fd232783e3b1ba
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54079140"
---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>Jak rozwiązywać problemy z typowych problemów napotykanych podczas tworzenia wirtualnego dysku twardego
Ten artykuł zapewnia pomoc w portalu Azure Marketplace, wydawcy i/lub współadministrator, które może napotkać problem lub mają często zadawane pytania podczas publikowania lub zarządzania ich rozwiązań maszyny wirtualnej.

1. Jak zmienić nazwę hosta?
   
    Po utworzeniu maszyny Wirtualnej, użytkownicy nie można zaktualizować nazwy hosta.
2. Jak zresetować swoje hasło logowania lub usługi pulpitu zdalnego?
   
   * [Dokumentacja dotycząca maszyn wirtualnych Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Dokumentacja dotycząca maszyn wirtualnych systemu Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. Jak można wygenerować nowy ssh certyfikatów?
   
   Skorzystaj z łącza: [https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. Jak skonfigurować Otwórz certyfikatu sieci VPN?
   
   Skorzystaj z łącza: [https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/)
5. Co to są zasady pomocy technicznej, do uruchamiania oprogramowania serwerowego firmy Microsoft w środowisku maszyny wirtualnej Microsoft Azure (infrastruktury as-a-service)?
   
   Skorzystaj z łącza: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
6. Maszyny wirtualne mają wszystkie Unikatowy identyfikator?
   
   Azure koduje unikatowego Identyfikatora maszyny Wirtualnej platformy Azure w każdej maszynie Wirtualnej. Zobacz szczegółowe informacje w tym blogu i dokumentację.
7. W przypadku maszyny Wirtualnej w jaki sposób zarządzać niestandardowego rozszerzenia skryptu w zadaniu uruchamiania?
   
   Skorzystaj z łącza: [https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. Jak utworzyć Maszynę wirtualną w witrynie Azure portal przy użyciu wirtualnego dysku twardego, który zostanie przekazany do usługi premium storage?
   
   Firma Microsoft nie obsługuje tej funkcji jeszcze.
9. Aplikacji 32-bitowych jest obsługiwane w witrynie Azure Marketplace?
   
   Skorzystaj z linku, aby uzyskać szczegółowe informacje na temat zasady pomocy technicznej: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
10. Za każdym razem, gdy próbuję Tworzenie obrazu na podstawie moich wirtualne dyski twarde, jest zgłaszany błąd ". Wirtualny dysk twardy jest już zarejestrowany przy użyciu repozytorium obrazów jako zasób"w programie PowerShell. Nie utworzył obrazów przed ani I znalazł dowolnego obrazu o tej nazwie na platformie Azure. Jak rozwiązać ten problem?
    
    Ten problem zazwyczaj występuje użytkownika zainicjowano obsługę administracyjną maszyny Wirtualnej z ten wirtualny dysk twardy, jeśli ma blokady na tym dysku VHD. Sprawdź, że nie ma żadnych maszyn wirtualnych, przydzielany z ten wirtualny dysk twardy. Jeśli błąd będzie występować nadal, następnie Zgłoś bilet pomocy technicznej za pomocą tego łącza lub publikowania portal (szczegóły podano w odpowiedzi na pytania 11).