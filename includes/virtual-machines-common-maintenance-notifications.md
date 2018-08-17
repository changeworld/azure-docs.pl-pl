---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 07/02/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: efedb2f48748264fb936fe82a1dbb3cf4403cc5e
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "40166458"
---
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>View VMs zaplanowana do konserwacji w portalu

Gdy planowanej konserwacji jest zaplanowane, można zaobserwować, listę maszyn wirtualnych, które mają wpływ wave zbliżającej się konserwacji. 

Można użyć witryny Azure portal i wyszukaj zaplanowana konserwacja maszyn wirtualnych.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Na lewym pasku nawigacyjnym kliknij **maszyn wirtualnych**.

3. W okienku maszyn wirtualnych kliknij **kolumn** przycisk, aby otworzyć listę dostępnych kolumn.

4. Wybierz i dodaj następujące kolumny:

   **Konserwacja**: Wyświetla stan konserwacji maszyny wirtualnej. Poniżej przedstawiono potencjalne wartości:
      
      | Wartość | Opis |
      |-------|-------------|
      | Rozpocznij teraz | Maszyna wirtualna jest w oknie konserwacji samoobsługowej, które pozwala na zainicjowanie konserwacji, samodzielnie. Poniżej przedstawiono dotyczących sposobu rozpoczęcia konserwacji na maszynie Wirtualnej. | 
      | Zaplanowane | Maszyna wirtualna jest zaplanowana do konserwacji bez opcji umożliwiającej samodzielne zainicjowanie konserwacji. Okna obsługi można znaleźć, wybierając konserwacja — okno zaplanowane, w tym widoku lub klikając maszynę Wirtualną. | 
      | Już zaktualizowane | Maszyna wirtualna została już zaktualizowana, a w tym momencie są wymagane żadne dalsze działania. | 
      | Spróbuj ponownie później | Zainicjowano konserwacji bez powodzenia. Można korzystać z opcji konserwacji samoobsługowej w późniejszym czasie. | 
      | Spróbuj ponownie teraz | Możesz ponowić próbę się wcześniej niepowodzeniem konserwacji własnym zainicjowane. | 
      | - | Maszyna wirtualna nie jest częścią planowanej konserwacji. |
      

   **Konserwacja — okno samoobsługi**: wyświetlany przedział czasu, kiedy własnym możesz uruchomić konserwację na maszynach wirtualnych.
   
   **Konserwacja — okno zaplanowanej**: wyświetlany przedział czasu, kiedy Azure zachowają maszyny Wirtualnej w celu obsługi. 



## <a name="notification-and-alerts-in-the-portal"></a>Powiadomienia i alerty w portalu

Azure komunikuje się harmonogram planowanej konserwacji, wysyłając wiadomość e-mail do grupy właściciel i współwłaściciele subskrypcji. Możesz dodać dodatkowych adresatów oraz kanały do komunikacji przez tworzenie alertów dziennika aktywności platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowanie aktywności subskrypcji z dziennika aktywności platformy Azure] (.. / articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz **Monitor**. 
3. W **monitorowanie — alerty (klasyczne)** okienku kliknij **+ Dodaj alert dziennika aktywności**.
5. Uzupełnij informacje w **Dodaj alert dziennika aktywności** strony i upewnij się, ustaw następujące opcje **kryteria**:
   - **Kategoria zdarzenia**: kondycja usługi
   - **Usługi**: zestawy skalowania maszyn wirtualnych i maszyn wirtualnych
   - **Typ**: Planowana konserwacja 
    
Aby dowiedzieć się więcej na temat konfigurowania alertów dziennika aktywności, zobacz [Tworzenie alertów dziennika aktywności](../articles/monitoring-and-diagnostics/monitoring-activity-log-alerts.md).
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Uruchom konserwację na maszynie Wirtualnej z poziomu portalu

Podczas przeglądania szczegółów maszyny Wirtualnej, można zobaczyć więcej szczegółów dotyczących konserwacji.  
W górnej części widoku szczegółów maszyny Wirtualnej nowe wstążki powiadomień zostaną dodane, jeśli maszyna wirtualna znajduje się w planowanej konserwacji. Ponadto nowa opcja jest dodawany do uruchomienia konserwacji, jeśli jest to możliwe. 


Kliknij powiadomienie o konserwacji, aby wyświetlić stronę konserwacji z bardziej szczegółowymi informacjami o planowanej konserwacji. W tym miejscu, będą mieć możliwość **Uruchom konserwację** na maszynie Wirtualnej.

Po rozpoczęciu konserwacji maszyny wirtualnej będzie przechowywany i stanu konserwacji zostanie zaktualizowana w celu odzwierciedlenia wynik w ciągu kilku minut.

Jeśli zdarzyło Ci się przeoczyć okna samoobsługi, nadal będzie można wyświetlić okno, gdy maszyna wirtualna będzie obsługiwany przez platformę Azure. 
