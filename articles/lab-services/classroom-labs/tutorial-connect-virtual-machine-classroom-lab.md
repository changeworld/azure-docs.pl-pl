---
title: Dostęp do laboratorium w usłudze Azure Lab Services | Microsoft Docs
description: W tym samouczku maszyny wirtualne są dostępne w laboratorium skonfigurowanym przez nauczyciela.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 2e34aef9a6f184c60fbb23023f798941a1fc11da
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894432"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Samouczek: uzyskiwanie dostępu do laboratorium w usłudze Azure Lab Services
W tym samouczku Ty, jako osoba ucząca się, nawiążesz połączenie z maszyną wirtualną w laboratorium. 

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Użycie linku rejestracji 
> * Nawiązywanie połączenia z maszyną wirtualną

## <a name="use-the-registration-link"></a>Użycie linku rejestracji

1. Przejdź do **adresu URL rejestracji** otrzymanego od nauczyciela. Po ukończeniu rejestracji nie musisz używać adresu URL rejestracji. Zamiast tego użyj adresu URL: [https://labs.azure.com](https://labs.azure.com). 
1. Zaloguj się do usługi przy użyciu konta służbowego, aby ukończyć rejestrację. 
2. Po zarejestrowaniu upewnij się, że widzisz maszyny wirtualne dla laboratorium, do którego masz dostęp. 
3. Zaczekaj, aż maszyna wirtualna będzie gotowa, a następnie **uruchom** ją. Ten proces zajmuje trochę czasu.  

    ![Uruchamianie maszyny wirtualnej](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

1. Wybierz polecenie **Połącz** na kafelku odpowiadającym maszynie wirtualnej laboratorium, do której chcesz uzyskać dostęp. 

    ![Łączenie z maszyną wirtualną](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Zapisz plik RDP na dysku twardym, a następnie otwórz go (pod warunkiem, że jest to maszyna wirtualna z systemem Windows).
3. Użyj **nazwy użytkownika** i **hasła** uzyskanych od nauczyciela, aby zalogować się do maszyny. 

## <a name="next-steps"></a>Następne kroki
W tym samouczku użyto linku rejestracji otrzymanego od nauczyciela w celu uzyskania dostępu do laboratorium.

Jako właściciel laboratorium możesz sprawdzać, kto zarejestrował się w Twoim laboratorium, i śledzić użycie maszyn wirtualnych. Przejdź do następnego samouczka, aby dowiedzieć się, jak śledzić użycie laboratorium:

> [!div class="nextstepaction"]
> [Śledzenie użycia laboratorium](tutorial-track-usage.md) 