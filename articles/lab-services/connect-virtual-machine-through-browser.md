---
title: Łączenie się z maszynami wirtualnymi za pośrednictwem przeglądarki — Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć się z maszynami wirtualnymi za pośrednictwem przeglądarki.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: 4549ee0f50c06040b70307c96b5ca96cd4162214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974301"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Łączenie się z maszynami wirtualnymi za pośrednictwem przeglądarki 

DevTest Labs integruje się z [usługą Azure Bastion](https://docs.microsoft.com/azure/bastion/), która umożliwia łączenie się z maszynami wirtualnymi za pośrednictwem przeglądarki. Aby uzyskać informacje na temat włączania tej funkcji w laboratoriach DevTest, zobacz [Włączanie połączenia przeglądarki na laboratoryjnych maszynach wirtualnych](enable-browser-connection-lab-virtual-machines.md).

Po *włączeniu połączenia z przeglądarką* użytkownicy laboratorium mogą uzyskiwać dostęp do maszyn wirtualnych za pośrednictwem przeglądarki.  

## <a name="create-a-lab-virtual-machine"></a>Tworzenie maszyny wirtualnej laboratorium

Najpierw należy utworzyć maszynę wirtualną laboratorium w sieci wirtualnej, która ma bastion skonfigurowany na nim. Wybierz drugą **podsieć** utworzoną, a nie AzureBastionSubnet. Sieć wirtualną można wybrać podczas tworzenia maszyny wirtualnej, przechodząc na kartę **Ustawienia zaawansowane.**

![Tworzenie maszyny wirtualnej](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Uruchamianie maszyny wirtualnej w przeglądarce

Po utworzeniu maszyny wirtualnej można ją uruchomić w przeglądarce, klikając przycisk *Połącz przeglądarkę* i wprowadzając nazwę użytkownika i hasło do urządzenia.  

![Uruchamianie w przeglądarce](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Następne kroki

[Dodawanie maszyny wirtualnej do laboratorium w laboratorium usługi Azure DevTest Labs](devtest-lab-add-vm.md)
