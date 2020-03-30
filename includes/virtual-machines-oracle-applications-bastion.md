---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361546"
---
### <a name="bastion-tier"></a>Warstwa bastionu

Host bastionu jest opcjonalnym składnikiem, którego można użyć jako serwera szybkiego dostępu do aplikacji i wystąpień bazy danych. Maszyna wirtualna hosta bastionu może mieć przypisany publiczny adres IP, chociaż zaleca się skonfigurowanie połączenia usługi ExpressRoute lub sieci VPN typu lokacja-lokacja w sieci lokalnej w celu zapewnienia bezpiecznego dostępu. Ponadto tylko SSH (port 22, Linux) lub RDP (port 3389, Windows Server) powinny być otwarte dla ruchu przychodzącego. Aby uzyskać wysoką dostępność, należy wdrożyć host bastionu w dwóch strefach dostępności lub w jednym zestawie dostępności.

Można również włączyć przekazywanie agenta SSH na maszynach wirtualnych, co umożliwia dostęp do innych maszyn wirtualnych w sieci wirtualnej przez przekazywanie poświadczeń z hosta bastionu. Można też użyć tunelowania SSH, aby uzyskać dostęp do innych wystąpień.

Oto przykład przekierowania agenta:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

To polecenie łączy się z bastionem, a następnie natychmiast uruchamia się `ssh` ponownie, dzięki czemu pojawi się terminal w wystąpieniu docelowym. Może być konieczne określenie użytkownika innego niż root w wystąpieniu docelowym, jeśli klaster jest skonfigurowany inaczej. Argument `-A` przekazuje połączenie agenta, dzięki czemu klucz prywatny na komputerze lokalnym jest używany automatycznie. Należy zauważyć, że przekazywanie agenta `ssh` jest `-A` łańcuchem, więc drugie polecenie zawiera również tak, że wszystkie kolejne połączenia SSH zainicjowane z wystąpienia docelowego również używać lokalnego klucza prywatnego.