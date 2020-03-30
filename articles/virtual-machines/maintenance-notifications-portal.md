---
title: Użyj portalu do powiadomień o konserwacji
description: Wyświetl powiadomienia o konserwacji dla maszyn wirtualnych uruchomionych na platformie Azure i rozpocznij samoobsługową konserwację przy użyciu portalu.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 46fcc825ac49f0181ac74e9c3e2deaea577f3329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115743"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Obsługa powiadomień o planowanej konserwacji za pomocą portalu

**Ten artykuł dotyczy maszyn wirtualnych z systemem Linux i Windows.**

Po zaplanowaniu [planowanej fali konserwacji](maintenance-notifications.md) można sprawdzić listę maszyn wirtualnych, których dotyczy problem. 

Możesz użyć witryny Azure portal i poszukać maszyn wirtualnych zaplanowanych do konserwacji.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. W lewej nawigacji kliknij pozycję **Maszyny wirtualne**.

3. W okienku Maszyny wirtualne wybierz przycisk **Edytuj kolumny,** aby otworzyć listę dostępnych kolumn.

4. Zaznacz i dodaj następujące kolumny:

   **Stan konserwacji:** Pokazuje stan konserwacji maszyny Wirtualnej. Poniżej przedstawiono wartości potencjalne:
      
      | Wartość | Opis |
      |-------|-------------|
      | Rozpocznij teraz | Maszyna wirtualna znajduje się w oknie samoobsługowej konserwacji, które umożliwia samodzielne inicjowanie konserwacji. Zobacz poniżej, jak rozpocząć konserwację maszyny Wirtualnej. | 
      | Zaplanowana | Maszyna wirtualna jest zaplanowana do konserwacji bez opcji umożliwiającej samodzielne zainicjowanie konserwacji. O oknie konserwacji można dowiedzieć się, wybierając okno Konserwacja — zaplanowane w tym widoku lub klikając maszynę wirtualną. | 
      | Już zaktualizowany | Maszyna wirtualna jest już zaktualizowana i w tej chwili nie są wymagane żadne dalsze działania. | 
      | Ponów próbę później | Rozpoczęto konserwację bez powodzenia. Będziesz mógł skorzystać z opcji samoobsługowej konserwacji w późniejszym czasie. | 
      | Ponów próbę teraz | Można ponowić próbę wcześniej nieudanej samodzielnej konserwacji. | 
      | - | Maszyna wirtualna nie jest częścią planowanej fali konserwacji. |
      

   **Konserwacja — okno samoobsługowe**: Pokazuje przedział czasu, kiedy można samodzielnie rozpocząć konserwację maszyn wirtualnych.
   
   **Konserwacja — zaplanowane okno:** Pokazuje przedział czasu, kiedy platforma Azure będzie utrzymywać maszynę wirtualną w celu zakończenia konserwacji. 



## <a name="notification-and-alerts-in-the-portal"></a>Powiadomienia i alerty w portalu

Platforma Azure komunikuje harmonogram planowanej konserwacji, wysyłając wiadomość e-mail do właściciela subskrypcji i grupy współwłaścicieli. Można dodać dodatkowych adresatów i kanałów do tej komunikacji, tworząc alerty dziennika aktywności platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie alertów dziennika aktywności w powiadomieniach o usługach](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

Upewnij się, że **typ zdarzenia** został ustawiony jako **Planowana konserwacja**i **Usługi** jako **zestawy skalowania maszyny wirtualnej** i/lub **maszyny wirtualne**.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Rozpocznij konserwację maszyny Wirtualnej z portalu

Podczas patrzenia na szczegóły maszyny Wirtualnej, będzie można zobaczyć więcej szczegółów związanych z konserwacją.  
W górnej części widoku szczegółów maszyny Wirtualnej zostanie dodana nowa wstążka powiadomień, jeśli maszyna wirtualna jest uwzględniona w planowanej fali konserwacji. Ponadto w miarę możliwości dodawana jest nowa opcja, aby rozpocząć konserwację. 


Kliknij powiadomienie o konserwacji, aby wyświetlić stronę konserwacji z bardziej szczegółowych informacji na temat planowanej konserwacji. Stamtąd będzie można rozpocząć **konserwację** na maszynie Wirtualnej.

Po uruchomieniu konserwacji maszyna wirtualna zostanie zachowana, a stan konserwacji zostanie zaktualizowany, aby odzwierciedlić wynik w ciągu kilku minut.

Jeśli pominięto okno samoobsługi, nadal będzie można zobaczyć okno, gdy maszyna wirtualna będzie obsługiwana przez platformę Azure. 


## <a name="next-steps"></a>Następne kroki

Można również obsługiwać planowaną konserwację przy użyciu [interfejsu wiersza polecenia platformy Azure](maintenance-notifications-cli.md) lub programu [PowerShell](maintenance-notifications-powershell.md).