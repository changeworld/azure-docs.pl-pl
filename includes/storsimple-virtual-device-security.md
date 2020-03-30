---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183275"
---
<!--v-sharos 10/13/2105 virtual device security-->

Podczas korzystania z urządzenia wirtualnego StorSimple należy pamiętać o następujących zagadnieniach dotyczących zabezpieczeń:

* Urządzenie wirtualne jest zabezpieczone za pośrednictwem subskrypcji platformy Microsoft Azure. Oznacza to, że jeśli używasz urządzenia wirtualnego, a subskrypcja platformy Azure zostanie naruszona, dane przechowywane na urządzeniu wirtualnym są również podatne.
* Klucz publiczny certyfikatu używanego do szyfrowania danych przechowywanych w usłudze Azure StorSimple jest bezpiecznie udostępniany w klasycznej witrynie azure portal, a klucz prywatny jest zachowywany za pomocą urządzenia StorSimple. Na urządzeniu wirtualnym StorSimple klucze publiczne i prywatne są przechowywane na platformie Azure.
* Urządzenie wirtualne jest hostowane w centrum danych platformy Microsoft Azure.

