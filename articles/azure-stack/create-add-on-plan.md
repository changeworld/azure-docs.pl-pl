---
title: W tym artykule dowiesz się, jak zaktualizować usługę Azure Stack oferty i plany | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wyświetlać i modyfikować istniejące usługi Azure Stack oferty i plany.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 0a7cf008dd2d2656153d7f1d70f71b9832be5d81
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157806"
---
# <a name="azure-stack-add-on-plans"></a>Plany dodatkowe usługi Azure Stack

Jako operatorów usługi Azure Stack, tworzenie planów dodatków do modyfikowania [plan podstawowy](azure-stack-create-plan.md) gdy zachodzi potrzeba oferują dodatkowych usług lub rozszerzyć pewne *komputera*, *magazynu*, lub *sieci* limity przydziału poza wstępną ofertę plan podstawowy. Plany dodatkowe zmodyfikować plan podstawowy i są opcjonalne rozszerzenia, które użytkownicy mogą wybrać do subskrybowania.

Istnieją terminy, gdy łączenie wszystko w jednym planie jest optymalna. Czasami warto mieć podstawowy plan, a następnie oferują dodatkowe usługi przy użyciu planów dodatków. Na przykład można zdecydować, że oferty usługi IaaS w ramach planu podstawowego w przypadku wszystkich usług PaaS traktowane jako planów dodatków.

Kolejny powód, aby użyć planów dodatków ma pomóc użytkownikom w trosce o ich użycia zasobów. Aby to zrobić, możesz rozpocząć podstawowy plan, który obejmuje przydziały stosunkowo mały (w zależności od usługi, wymagane). Następnie tylko użytkownicy osiągnąć wydajność, ich będą otrzymywać alerty, które są już używane alokacji zasobów na podstawie ich przypisanych planu. W tym miejscu użytkownicy będą mogli wybrać planu dodatku, który zawiera dodatkowe zasoby.

> [!NOTE]
> Gdy chcesz rozszerzyć limit przydziału przy użyciu planu w dodatku, istnieje również możliwość [Edytuj oryginalną konfigurację limitu przydziału](azure-stack-quota-types.md#edit-a-quota).

Gdy użytkownik doda planu dodatku do istniejącej subskrypcji oferty, dodatkowych zasobów może potrwać do godziny się pojawić.

## <a name="create-an-add-on-plan"></a>Tworzenie planu dodatku

Plany dodatkowe są tworzone przez zmodyfikowanie istniejącej oferty:

1. Zaloguj się do portalu administratora usługi Azure Stack jako administrator w chmurze.
2. Wykonaj te same czynności, które są używane do [Utwórz nowy plan bazowy](azure-stack-create-plan.md) do tworzenia nowego planu, oferty usług, które nie zostały wcześniej dostępna. W tym przykładzie usługa Key Vault (**Microsoft.KeyVault**) usług, które zostaną uwzględnione w nowym planie.
3. W portalu administratora kliknij **oferuje** , a następnie wybierz ofertę można zaktualizować przy użyciu planu dodatku.

   ![Tworzenie planu dodatku](media/create-add-on-plan/1.PNG)

4. Przewiń w dół właściwości oferty, a następnie wybierz pozycję **planów dodatków**. Kliknij pozycję **Add** (Dodaj).

    ![Tworzenie planu dodatku](media/create-add-on-plan/2.PNG)

5. Wybierz plan do dodania. W tym przykładzie jest wywoływana plan **Key vault plan**. Po wybraniu planu kliknij pozycję **wybierz** można dodać planu do oferty. Otrzymasz powiadomienie, że plan został pomyślnie dodany do tej oferty.

    ![Tworzenie planu dodatku](media/create-add-on-plan/3.PNG)

6. Przejrzyj listę planów dodatków wliczonej w ofertę, aby sprawdzić, czy znajduje się nowy plan dodatku.

    ![Tworzenie planu dodatku](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie oferty](azure-stack-create-offer.md)