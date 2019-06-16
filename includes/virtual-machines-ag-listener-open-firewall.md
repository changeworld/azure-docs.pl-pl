---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66165475"
---
W tym kroku należy utworzyć regułę zapory, aby otworzyć port sondy do endpoint ze zrównoważonym obciążeniem (jak określono wcześniej 59999), a innej reguły, aby otworzyć port odbiornika grupy dostępności. Ponieważ utworzono punkt końcowy z równoważeniem obciążenia na maszynach wirtualnych, które zawierają repliki grupy dostępności, musisz otworzyć port sondy i port odbiornika na odpowiednich maszyn wirtualnych.

1. Na maszynach wirtualnych, które hostują repliki, należy uruchomić **Zapora Windows z zabezpieczeniami zaawansowanymi**.

2. Kliknij prawym przyciskiem myszy **reguły ruchu przychodzącego**, a następnie kliknij przycisk **nową regułę**.

3. Na **typ reguły** wybierz opcję **portu**, a następnie kliknij przycisk **dalej**.

4. Na **protokół i porty** wybierz opcję **TCP**, typ **59999** w **określone porty lokalne** , a następnie kliknij przycisk  **Następny**.

5. Na **akcji** Zachowaj **Zezwalaj na połączenie** zaznaczone, a następnie kliknij przycisk **dalej**.

6. Na **profilu** strony, zaakceptuj ustawienia domyślne, a następnie kliknij przycisk **dalej**.

7. Na **nazwa** strony w **nazwa** tekstu określ nazwę reguły, takie jak **zawsze na sondowania Port odbiornika**, a następnie kliknij przycisk **Zakończ**.

8. Powtórz te czynności na port odbiornika grupy dostępności (jak określono wcześniej w parametrze $EndpointPort skryptu), a następnie określ nazwę odpowiednią regułę, takich jak **zawsze na Port odbiornika**.

