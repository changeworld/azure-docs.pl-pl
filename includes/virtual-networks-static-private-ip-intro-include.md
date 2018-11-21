---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 5c1caf87f32ddd827b85263ee634d3e15d821124
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2018
ms.locfileid: "52271658"
---
Maszyny wirtualne IaaS (VM) i wystąpień roli PaaS w sieci wirtualnej automatycznie otrzymywać prywatny adres IP z zakresu, który określisz, na podstawie podsieci, które są połączone. Ten adres jest zachowywany przez maszyny wirtualne i wystąpienia roli, do momentu ich likwidacji. Zlikwidowaniu maszyny Wirtualnej lub wystąpienie roli, zatrzymując ją, z programu PowerShell, interfejsu wiersza polecenia platformy Azure lub witryny Azure portal. W przypadkach gdy wystąpienie roli lub maszyna wirtualna jest uruchamiana ponownie, będzie ona otrzymywać dostępny adres IP z infrastruktury platformy Azure nie może być taka sama, jaką miała wcześniej. Jeśli wystąpienie maszyny Wirtualnej lub roli systemu operacyjnego gościa zostanie zamknięta, zachowuje adres IP, jaką miała.  

W niektórych przypadkach chcesz maszyny Wirtualnej lub wystąpienie roli ma statyczny adres IP, na przykład, czy maszyna wirtualna zostanie uruchomione DNS jest kontrolerem domeny. Można to zrobić, ustawiając statyczny prywatny adres IP.

