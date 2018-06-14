---
title: Jak rozwiązywać typowe problemy podczas tworzenia dysku VHD | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące rozwiązywania problemów i problemy podczas tworzenia dysku VHD.
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: ''
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: mbaldwin
ms.openlocfilehash: 65361ad5bd7c3311c428b64b8476ec8f2ea2d17b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29941606"
---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>Sposoby rozwiązywania typowych problemów napotykanych podczas tworzenia dysku VHD
W tym artykule zapewnia pomoc w portalu Azure Marketplace wydawcy i/lub administratora współpracującego, który może zgłaszać problemy lub ma często zadawane pytania podczas publikowania lub zarządzania ich solution(s) maszyny wirtualnej.

1. Jak zmienić nazwę hosta?
   
    Po utworzeniu maszyny Wirtualnej, użytkownicy nie można zaktualizować nazwy hosta.
2. Jak można zresetować usług pulpitu zdalnego lub jego hasło logowania?
   
   * [Informacje dotyczące systemu Windows maszyny Wirtualnej](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Informacje dotyczące maszyny Wirtualnej systemu Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. Sposób generowania nowych ssh certyfikatów?
   
   Skorzystaj z łącza: [https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. Jak skonfigurować Otwórz certyfikatu sieci VPN?
   
   Skorzystaj z łącza: [https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/)
5. Co to jest zasady udzielania pomocy technicznej do uruchamiania w środowisku maszyny wirtualnej Microsoft Azure (infrastruktury jako — usługa) oprogramowanie serwera firmy Microsoft?
   
   Skorzystaj z łącza: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
6. Czy maszyny wirtualne mają żadnych Unikatowy identyfikator?
   
   Azure koduje Azure VM Unikatowy identyfikator w każdej maszynie Wirtualnej. Zobacz szczegółowe informacje w tym blogu i dokumentacji.
7. Na maszynie wirtualnej w jaki sposób zarządzać rozszerzenie skryptu niestandardowego w zadanie uruchamiania?
   
   Skorzystaj z łącza: [https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. Jak utworzyć Maszynę wirtualną z portalu Azure za pomocą wirtualny dysk twardy zostanie przekazany do magazynu premium?
   
   Firma Microsoft nie obsługują tej funkcji jeszcze.
9. Aplikacja 32-bitowych jest obsługiwane w portalu Azure Marketplace?
   
   Zapoznaj się łącze, aby uzyskać więcej informacji na temat zasad pomocy technicznej: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
10. Za każdym razem próbuję Tworzenie obrazu na podstawie moich wirtualne dyski twarde, zgłaszany jest błąd ". Dysk VHD jest już zarejestrowany z repozytorium obrazów jako zasób"w programie PowerShell. Nie utworzono żadnego obrazu, przed nie może znaleźć żadnego obrazu o tej nazwie na platformie Azure. Jak rozwiązać ten problem?
    
    Zazwyczaj dzieje się tak, jeśli użytkownik zainicjowaniu obsługi maszyny Wirtualnej z tym dyskiem VHD i istnieje blokada na tym dysku VHD. Sprawdź, czy przydzielony z tym dyskiem VHD maszyna wirtualna nie istnieje. Jeśli błąd nadal będzie nadal występować, następnie należy podnieść biletu pomocy technicznej za pomocą tego łącza lub z publikowania portalu dotyczącej to (szczegółowe informacje są podane w odpowiedzi na pytania 11).