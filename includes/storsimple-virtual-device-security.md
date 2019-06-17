---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159183"
---
<!--v-sharos 10/13/2105 virtual device security-->

Korzystając z urządzenia wirtualnego StorSimple, należy pamiętać o następujących kwestiach dotyczących zabezpieczeń:

* Urządzenie wirtualne jest zabezpieczona za pośrednictwem danej subskrypcji Microsoft Azure. Oznacza, że jeśli używasz urządzenia wirtualnego i subskrypcji platformy Azure zostanie naruszone, dane przechowywane na urządzeniu wirtualnym również narażone.
* Klucz publiczny certyfikatu używanego do szyfrowania danych przechowywanych w usłudze Azure StorSimple jest bezpiecznie udostępniany do klasycznego portalu Azure, a klucz prywatny jest zachowywany z urządzeniem StorSimple. Na urządzeniu wirtualnym StorSimple klucze publiczne i prywatne są przechowywane na platformie Azure.
* Urządzenie wirtualne znajduje się w centrum danych Microsoft Azure.

