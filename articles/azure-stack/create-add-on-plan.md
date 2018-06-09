---
title: W tym artykule opisano sposób aktualizacji oferty Azure stosu i planów | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób wyświetlania i modyfikowania istniejących oferty Azure stosu i planów.
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
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a84148a3ac31d51ff30cebffab00e5fec8fdaa87
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238508"
---
# <a name="azure-stack-add-on-plans"></a>Azure planów dodatek stosu
Jako operator stosu Azure możesz utworzyć plany, które zawierają żądane usług i odpowiednie limity przydziału dla użytkowników, aby subskrybować. Te [ *podstawowa planów* ](azure-stack-create-plan.md) zawiera podstawowe usługi przeznaczonej dla użytkowników i może mieć tylko jeden plan bazowy na ofertę. Jeśli trzeba zmodyfikować ofertę, możesz użyć *planów dodatek* umożliwiają modyfikowanie planu rozszerzenie komputera, magazynu, albo sieci przydziały początkowo oferowany z planu podstawowego. 

Mimo że łączenie wszystko w ramach jednego planu mogą być optymalne w niektórych przypadkach, możesz mieć podstawy plan i oferują dodatkowe usługi przy użyciu planów dodatek. Na przykład można zdecydować, że oferty usług IaaS jako część planu podstawowego, ze wszystkimi usługami PaaS traktowane jako dodatek do planów. Plany można również sterować zużycie zasobów w środowisku Azure stosu. Na przykład jeśli chcesz, aby użytkownicy w trosce o ich użycia zasobów, może mieć stosunkowo mały planu podstawowego (w zależności od usług wymaganych) i jak użytkownicy dostępu pojemności, będzie otrzymywać alerty już zajmowany alokacji zasobów na podstawie ich przypisanej planu. Z tego miejsca użytkownicy mogą wybierz plan dostępne dodatkowe dodatkowych zasobów. 

> [!NOTE]
> Gdy użytkownik dodaje plan dodatek do istniejącej subskrypcji oferty, dodatkowych zasobów może potrwać do godziny pojawią się. 

## <a name="create-an-add-on-plan"></a>Tworzenie planu dodatku
Dodatek planów są tworzone przez zmodyfikowanie istniejącej oferty:

1. Zaloguj się do portalu administratora stosu Azure jako administrator chmury.
2. Wykonaj te same kroki umożliwiają [Utwórz nowy plan bazowy](azure-stack-create-plan.md) do tworzenia nowego planu oferty usług, które nie zostały wcześniej oferowane. W tym przykładzie usługi Key Vault (Microsoft.KeyVault) będą uwzględniane w nowy plan.
3. W portalu administratora, kliknij przycisk **oferuje** , a następnie wybierz ofertę zaktualizowania z planem dodatek.

   ![](media/create-add-on-plan/1.PNG)

4.  Przewiń w dół właściwości oferty i wybierz **planów dodatek**. Kliknij pozycję **Add** (Dodaj).
   
    ![](media/create-add-on-plan/2.PNG)

5. Wybierz plan do dodania. W tym przykładzie nosi nazwę planu **klucza magazynu planu**, a następnie kliknij przycisk **wybierz** dodać planu do oferty. Otrzymasz powiadomienie, że plan zostało pomyślnie dodane do oferty.
   
    ![](media/create-add-on-plan/3.PNG)

6. Zapoznaj się z listą dodatek, który na liście planów dołączone do oferty, aby sprawdzić, czy dodatek nowy plan.
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie oferty](azure-stack-create-offer.md)