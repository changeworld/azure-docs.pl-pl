---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183177"
---
W tym kroku utworzysz regułę zapory, aby otworzyć port sondy dla punktu końcowego z równoważenia obciążenia (59999, jak określono wcześniej) i inną regułę, aby otworzyć port odbiornika grupy dostępności. Ponieważ utworzono punkt końcowy z równoważenia obciążenia na maszynach wirtualnych, które zawierają repliki grup dostępności, należy otworzyć port sondy i port odbiornika na odpowiednich maszynach wirtualnych.

1. Na maszynach wirtualnych, które hostują repliki, uruchom **Zaporę systemu Windows z zabezpieczeniami zaawansowanymi**.

2. Kliknij prawym przyciskiem myszy **reguły ruchu przychodzącego**, a następnie kliknij przycisk **nową regułę**.

3. Na stronie **Typ reguły** wybierz pozycję **Port**, a następnie kliknij przycisk **Dalej**.

4. Na stronie **Protokół i porty** wybierz pozycję **TCP**, wpisz **59999** w polu **Określone porty lokalne,** a następnie kliknij przycisk **Dalej**.

5. Na stronie **Akcja** **zachowaj pozycję Zezwalaj na połączenie,** a następnie kliknij przycisk **Dalej**.

6. Na stronie **Profil** zaakceptuj ustawienia domyślne, a następnie kliknij przycisk **Dalej**.

7. Na stronie **Nazwa** w polu tekstowym **Nazwa** określ nazwę reguły, taką jak **Always On Listener Probe Port**, a następnie kliknij przycisk **Zakończ**.

8. Powtórz poprzednie kroki dla portu odbiornika grupy dostępności (jak określono wcześniej w $EndpointPort parametru skryptu), a następnie określ odpowiednią nazwę reguły, taką jak **Always On Listener Port**.

