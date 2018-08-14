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
ms.date: 07/30/2018
ms.author: spelluru
ms.openlocfilehash: dadc90e6a39b9e9689bab0249e6496fdea6f6205
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450219"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Samouczek: dostęp do laboratorium w usłudze Azure Lab Services
W tym samouczku Ty, jako osoba ucząca się, nawiążesz połączenie z maszyną wirtualną w laboratorium. 

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Użycie linku rejestracji 
> * Nawiązywanie połączenia z maszyną wirtualną

## <a name="use-the-registration-link"></a>Użycie linku rejestracji

1. Przejdź do **adresu URL rejestracji** otrzymanego od nauczyciela. 
2. Zaloguj się do usługi przy użyciu konta służbowego, aby ukończyć rejestrację. 
3. Po zarejestrowaniu upewnij się, że widzisz maszyny wirtualne dla laboratorium, do którego masz dostęp. 
2. Zaczekaj, aż maszyna wirtualna będzie gotowa, a następnie **uruchom** ją.

    ![Uruchamianie maszyny wirtualnej](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

1. Wybierz polecenie **Połącz** na kafelku reprezentującym maszynę wirtualną laboratorium, do której chcesz uzyskać dostęp. 

    ![Łączenie z maszyną wirtualną](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Zapisz plik RDP na dysku twardym, a następnie otwórz go. 
3. Użyj **nazwy użytkownika** i **hasła** uzyskanych od nauczyciela, aby zalogować się do maszyny. 

## <a name="next-steps"></a>Następne kroki
W tym samouczku użyto linku rejestracji otrzymanego od nauczyciela w celu uzyskania dostępu do laboratorium.

Jako właściciel laboratorium możesz sprawdzać, kto zarejestrował się w Twoim laboratorium, i śledzić użycie maszyn wirtualnych. Aby dowiedzieć się, jak to zrobić, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Śledzenie użycia laboratorium](tutorial-track-usage.md) 