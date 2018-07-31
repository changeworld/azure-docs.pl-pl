---
title: W tym artykule dowiesz się, jak zaktualizować usługę Azure Stack oferty i plany | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wyświetlać i modyfikować istniejące usługi Azure Stack oferty i plany.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 07/30/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a35ba993e6fd1162fa4a18bc0d6bc9351fe7dfa2
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358285"
---
# <a name="azure-stack-add-on-plans"></a>Plany dodatkowe usługi Azure Stack

Jako operatorów usługi Azure Stack, tworzenie planów dodatków do modyfikowania [ *plan podstawowy* ](azure-stack-create-plan.md) gdy zachodzi potrzeba oferują dodatkowych usług lub rozszerzyć pewne *komputera*, *magazynu* , lub *sieci* limity przydziału poza wstępną ofertę planów podstawowych. Plany dodatkowe zmodyfikować plan podstawowy i są opcjonalne rozszerzenia, które użytkownicy mogą wybrać do subskrybowania. 

Istnieją terminy, gdy łączenie wszystko w jednym planie jest optymalna. Czasami warto mieć podstawowy plan, a następnie oferują dodatkowe usługi przy użyciu planów dodatków. Na przykład można zdecydować, że oferty usługi IaaS w ramach planu podstawowego w przypadku wszystkich usług PaaS traktowane jako planów dodatków.

Kolejny powód, aby użyć planów dodatków ma pomóc użytkownikom w trosce o ich użycia zasobów. Aby to zrobić, możesz rozpocząć podstawowy plan, który obejmuje przydziały stosunkowo mały (w zależności od usługi, wymagane). Następnie tylko użytkownicy osiągnąć wydajność, ich będą otrzymywać alerty, które są już używane alokacji zasobów na podstawie ich przypisanych planu. W tym miejscu użytkownicy mogą następnie wybierz planu dodatku, który zawiera dodatkowe zasoby.

> [!NOTE]
> Jeśli nie chcesz rozszerzyć limit przydziału przy użyciu planu w dodatku, istnieje również możliwość [Edytuj oryginalną konfigurację limitu przydziału](azure-stack-quota-types.md#to-edit-a-quota). 

Gdy użytkownik doda planu dodatku do istniejącej subskrypcji oferty, dodatkowych zasobów może potrwać do godziny do są wyświetlane. 

## <a name="create-an-add-on-plan"></a>Tworzenie planu dodatku
Plany dodatkowe są tworzone przez zmodyfikowanie istniejącej oferty:

1. Zaloguj się do portalu administratora usługi Azure Stack jako administrator w chmurze.
2. Wykonaj te same czynności, które są używane do [Utwórz nowy plan bazowy](azure-stack-create-plan.md) do tworzenia nowego planu, oferty usług, które nie zostały wcześniej dostępna. W tym przykładzie usługi Key Vault (Microsoft.KeyVault) zostaną uwzględnione w nowym planie.
3. W portalu administratora kliknij **oferuje** , a następnie wybierz ofertę można zaktualizować przy użyciu planu dodatku.

   ![](media/create-add-on-plan/1.PNG)

4.  Przewiń w dół właściwości oferty, a następnie wybierz pozycję **planów dodatków**. Kliknij pozycję **Add** (Dodaj).
   
    ![](media/create-add-on-plan/2.PNG)

5. Wybierz plan do dodania. W tym przykładzie jest wywoływana plan **Key vault plan**. Po wybraniu planu kliknij pozycję **wybierz** można dodać planu do oferty. Otrzymasz powiadomienie, że plan został pomyślnie dodany do tej oferty.
   
    ![](media/create-add-on-plan/3.PNG)

6. Przejrzyj listę dodatek, który wliczonej w ofertę, aby sprawdzić, czy plan dodatkowy nowe plany na liście.
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie oferty](azure-stack-create-offer.md)