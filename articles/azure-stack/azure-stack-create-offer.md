---
title: Tworzenie oferty w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Jako administrator chmury Dowiedz się, jak utworzyć ofertę dla użytkowników w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 42336205726823dd04e0412f29c3e7a23d134d39
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763997"
---
# <a name="create-an-offer-in-azure-stack"></a>Tworzenie oferty w usłudze Azure Stack

[Oferuje](azure-stack-key-features.md) grup co najmniej jeden plan, które dostawcy przedstawiają użytkowników, których Ci użytkownicy mogą kupić lub subskrybować. W tym artykule opisano, jak utworzyć ofertę obejmującą [planu, który został utworzony](azure-stack-create-plan.md). Ta oferta zapewnia subskrybentom możliwość konfigurowania maszyn wirtualnych (VM).

## <a name="create-an-offer-1902-and-later"></a>Utwórz ofertę (1902 lub nowszy)

1. Zaloguj się do [portalu administratora usługi Azure Stack](https://adminportal.local.azurestack.external) i wybierz **+ Utwórz zasób**, następnie **oferty i plany**, a następnie **oferują**.

   ![Tworzenie oferty](media/azure-stack-create-offer/offers.png)

2. Interfejs użytkownika z kartami wyświetlany jest pozwala zdefiniować nazwę oferty i Dodaj istniejącą lub Utwórz nowe plany podstawowe i planów dodatków. Co najważniejsze możesz przejrzeć szczegóły oferty, której tworzysz, przed podjęciem decyzji o jej utworzenia.

   W **podstawy** , w obszarze **nowa oferta**, wprowadź **nazwę wyświetlaną** i **Nazwa zasobu**, a następnie w obszarze **zasobów Grupa**, wybierz opcję **Utwórz nową** lub **Użyj istniejącej**. Nazwa wyświetlana jest przyjazną nazwę dla oferty. Ta przyjazna nazwa jest tylko informacje dotyczące oferty, którą użytkownicy zobaczą podczas subskrybowania oferty w portalu użytkowników. Użyj nazwy intuicyjne, która pomaga użytkownikom zrozumieć, co jest dostarczane z tej oferty. Tylko administrator może zobaczyć nazwy zasobu. Jest to nazwa używana przez administratorów do pracy z ofertą jako zasobem usługi Azure Resource Manager. Na tej karcie można również można upublicznić tej oferty lub zachować te dane prywatne, co jest ustawieniem domyślnym. Możesz [zmiany stanu publicznych lub prywatnych oferty](#change-the-state-of-an-offer) później, jak również.

   ![Nowa oferta](media/azure-stack-create-offer/new-offer.png)
  
3. Wybierz **plany bazowe** kartę. Wybierz plany, które chcesz uwzględnić w ofercie.

   ![Wybierz plan](media/azure-stack-create-offer/select-plan.png)

4. W tym momencie można utworzyć planu dodatku, aby zmodyfikować plan podstawowy, ale jest to opcjonalne. W następnym artykule, utworzymy planu dodatku [planów dodatkowych w usłudze Azure Stack](create-add-on-plan.md).

5. Wybierz **przeglądu + Utwórz** kartę. Przejrzyj podsumowanie oferty, aby upewnić się, że wszystkie wartości są poprawne. Interfejs umożliwia rozszerzenie przydziały w wybranych planach, w czasie, aby wyświetlić szczegóły każdy przydział w planie i wróć do wprowadź niezbędne zmiany.

6. Wybierz **Utwórz** Aby utworzyć ofertę.

   ![Przejrzyj i utwórz](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>Zmień stan oferty

Po utworzeniu oferty, możesz zmienić jego stan. Oferty muszą być wykonane **publicznych** dla użytkowników uzyskać pełny widok podczas subskrybowania. Może być oferty:

   - **Publiczne**: Widoczne dla użytkowników.
   - **Prywatne**: Jest to widoczne tylko dla administratorów w chmurze. To ustawienie jest przydatne podczas opracowywania planu lub oferty, lub jeśli chce się z administratorem chmury [tworzenia każdej subskrypcji dla użytkowników](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Zlikwidowana**: Zamknięte dla nowych subskrybentów. Administrator chmury można zlikwidować oferty, aby uniemożliwić przyszłe subskrypcje, ale pozostawić bieżących subskrybentów bez zmian.

   > [!TIP]  
   > Zmiany w ramach oferty nie są bezpośrednio widoczne dla użytkownika. Aby zobaczyć zmiany, użytkownicy mogą mieć wylogowania się i zaloguj się ponownie do portalu użytkowników będzie nowej oferty.

Istnieją dwa sposoby, aby zmienić stan oferty:

1. W **wszystkie zasoby**, wybierz nazwę oferty. Na **Przegląd** ekranu dla tej oferty, wybierz opcję **zmiany stanu**. Wybierz stan, w której chcesz użyć (na przykład **publicznych**).

   ![Wybierz stan](media/azure-stack-create-offer/change-state.png)

2. W **wszystkie zasoby**, wybierz nazwę oferty. Następnie wybierz pozycję **ustawienia oferty**. Wybierz stan, w której chcesz użyć (na przykład **publicznych**), a następnie wybierz **Zapisz**.

   ![Wybierz stan ułatwień dostępu](media/azure-stack-create-offer/offer-settings.png)

## <a name="create-an-offer-1901-and-earlier"></a>Utwórz ofertę (1901 i starszych)

1. Zaloguj się do [portalu administratora usługi Azure Stack](https://adminportal.local.azurestack.external) i wybierz **+ Utwórz zasób**, następnie **dzierżawy oferty i plany**, a następnie **oferują**.

   ![Tworzenie oferty](media/azure-stack-create-offer/image01.png)
  
2. W obszarze **nowa oferta**, wprowadź **nazwę wyświetlaną** i **Nazwa zasobu**, a następnie w obszarze **grupy zasobów**, wybierz opcję **Create nowe** lub **Użyj istniejącej**. Nazwa wyświetlana jest przyjazną nazwę dla oferty. Ta przyjazna nazwa jest tylko informacje dotyczące oferty, którą widzą użytkownicy Po subskrybowaniu oferty. Użyj nazwy intuicyjne, która pomaga użytkownikom zrozumieć, co jest dostarczane z tej oferty. Tylko administrator może zobaczyć nazwy zasobu. Jest to nazwa używana przez administratorów do pracy z ofertą jako zasobem usługi Azure Resource Manager.

   ![Nowa oferta](media/azure-stack-create-offer/image01a.png)
  
3. Wybierz **plany bazowe** otworzyć **Planowanie**. Wybierz plany, aby uwzględnić w ofercie, a następnie wybierz **wybierz**. Aby utworzyć wybierz ofertę **Utwórz**.

   ![Wybierz plan](media/azure-stack-create-offer/image02.png)
  
4. Po utworzeniu oferty, możesz zmienić jego stan. Oferty muszą być wykonane **publicznych** dla użytkowników uzyskać pełny widok podczas subskrybowania. Może być oferty:

   - **Publiczne**: Widoczne dla użytkowników.
   - **Prywatne**: Jest to widoczne tylko dla administratorów w chmurze. To ustawienie jest przydatne podczas opracowywania planu lub oferty, lub jeśli chce się z administratorem chmury [tworzenia każdej subskrypcji dla użytkowników](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Zlikwidowana**: Zamknięte dla nowych subskrybentów. Administrator chmury można zlikwidować oferty, aby uniemożliwić przyszłe subskrypcje, ale pozostawić bieżących subskrybentów bez zmian.

   > [!TIP]  
   > Zmiany w ramach oferty nie są bezpośrednio widoczne dla użytkownika. Aby zobaczyć zmiany, użytkownicy mogą mieć wylogowania się i zaloguj się ponownie do portalu użytkowników będzie nowej oferty.

   Na ekranie Przegląd dla oferty, wybierz **stan ułatwień dostępu**. Wybierz stan, w której chcesz użyć (na przykład **publicznych**), a następnie wybierz pozycję **Zapisz**.

     ![Wybierz stan](media/azure-stack-create-offer/change-stage-1807.png)

     Jako alternatywę, wybierz **zmiany stanu** , a następnie wybierz stan.

    ![Wybierz stan ułatwień dostępu](media/azure-stack-create-offer/change-stage-select-1807.png)

> [!NOTE]
> Umożliwia także środowiska PowerShell do utworzenia domyślnego oferty, przydziały i planów. Aby uzyskać więcej informacji, zobacz [modułu programu PowerShell usługi Azure Stack 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie subskrypcji](azure-stack-subscribe-plan-provision-vm.md)
- [Inicjowanie obsługi administracyjnej maszyny wirtualnej](azure-stack-provision-vm.md)
