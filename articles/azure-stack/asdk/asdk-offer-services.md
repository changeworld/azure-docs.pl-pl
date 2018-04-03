---
title: W tym samouczku utworzysz stosu Azure oferuje | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć ofertę Azure stosu, włącznie z planami i przydziały.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 8bcc2f3077e79ff83ac2e90db0bb0fa53ae83adc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="tutorial-offer-azure-stack-iaas-services"></a>Samouczek: oferty usług Azure IaaS stosu
Jako administrator chmury Azure stosu można utworzyć oferty, które mogą subskrybować użytkowników (nazywane czasami dzierżawcy). Za pomocą swoich subskrypcji, użytkownicy będą mogły używać usługi Azure stosu.

Ten samouczek pokazuje, jak utworzyć ofertę, aby umożliwić użytkownikom tworzenie maszyn wirtualnych na podstawie obrazu systemu Windows Server 2016 Datacenter stosu Azure marketplace, przekazywać w [samouczek poprzedniej](asdk-marketplace-item.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie oferty
> * Tworzenie planu
> * Ustawianie przydziałów
> * Oferta zestawu na publiczne

W stosie Azure usługi są dostarczane do użytkowników przy użyciu subskrypcji, ofertami i planów. Użytkownicy mogą subskrybować wielu ofert. Oferty może mieć co najmniej jeden plan i planów może mieć co najmniej jedną usługę, jak pokazano na poniższym diagramie:

![Subskrypcje, ofertami i planów](media/asdk-offer-services/sop.png)

Jako operator stosu Azure oferty usługi monit najpierw Utwórz ofertę, a następnie planu, a na końcu przydziałów. Po utworzeniu oferty Azure stosu użytkowników może subskrybować oferty w portalu użytkownika.

## <a name="create-an-offer"></a>Tworzenie oferty
**Oferuje** są grupami co najmniej jeden plan udostępniające operatory stosu Azure do użytkowników w celu zakupu lub subskrybować.

1. Zaloguj się do [portal Azure stosu](https://adminportal.local.azurestack.external) jako administrator chmury.

2. Kliknij przycisk **nowe** > **oferuje + plany** > **oferują**.

   ![Nowa oferta](media/asdk-offer-services/new-offer.png)

2. W **oferują nowe** sekcji, wypełnij **Nazwa wyświetlana** i **Nazwa zasobu**, a następnie wybierz nowy lub istniejący **grupy zasobów**. Nazwa wyświetlana jest oferta publicznego przyjazną nazwę, którą użytkownicy będą widzieć. Operator chmury widzą nazwę zasobu, który jest używany przez administratorów do pracy z ofertą jako zasób usługi Azure Resource Manager.

   ![Nazwa wyświetlana](media/asdk-offer-services/offer-display-name.png)


## <a name="create-a-plan"></a>Tworzenie planu
Po wprowadzeniu informacji oferta podstawowe, należy dodać co najmniej jeden plan bazowy do oferty. 

**Plany** zezwolić operatorom stosu Azure do grupy usług i ich skojarzonych przydziały będą udostępniane użytkownikom.

1. Kliknij przycisk **podstawowa planów**i w **planu** kliknij **Dodaj** można dodać nowego planu do oferty.

   ![Dodaj plan](media/asdk-offer-services/new-plan.png)

2. W **nowy Plan** sekcji, wypełnij **Nazwa wyświetlana** i **Nazwa zasobu**. Nazwa wyświetlana jest przyjazna nazwa planu, dostępna dla użytkowników. Operator chmury wyświetlana nazwa zasobu używany przez operatorom chmury do pracy z planem jako zasób usługi Azure Resource Manager.

   ![Nazwa wyświetlana planu](media/asdk-offer-services/plan-display-name.png)

3. Następnie wybierz usługi, które mają zostać uwzględnione w planie. Aby oferują usługi IaaS, kliknij przycisk **usług**, wybierz pozycję **Microsoft.Compute**, **Microsoft.Network**, i **Microsoft.Storage**, a następnie Kliknij przycisk **wybierz**.

   ![Planowanie usług](media/asdk-offer-services/select-services.png)


## <a name="set-quotas"></a>Ustawianie przydziałów
Oferta uzyskuje plan, który zawiera usługi, należy ustawić limity przydziału dla każdego z nich. 

**Przydziały** określić ilość zasobów, które użytkownik może wykorzystać dla każdej usługi uwzględnione w planie oferowany.

1. Kliknij przycisk **przydziały**, a następnie wybierz usługę, dla której chcesz utworzyć przydział. 

   Aby rozpocząć pracę, należy najpierw utworzyć przydział dla usługi obliczeniowej. Na liście przestrzeni nazw, wybierz **Microsoft.Compute** przestrzeni nazw, a następnie kliknij przycisk **Utwórz nowy przydział**.
   
   ![Tworzenie nowego przydziału](media/asdk-offer-services/create-quota.png)

2. Na **Tworzenie przydziału** sekcji, wpisz nazwę limit przydziału, ustaw odpowiednie parametry dla limitu przydziału i kliknij przycisk **OK**.

   ![Nazwa przydziału](media/asdk-offer-services/quota-properties.png)

3. Wybierz limit przydziału, który został utworzony dla **Microsoft.Compute** usługi.

   ![Wybierz limit przydziału](media/asdk-offer-services/set-quota.png)

4. Powtórz kroki od 1 do 3, ustawiać przydziały dla usług sieci i magazynu, a następnie kliknij przycisk OK w sekcji przydziałów. Następnie kliknij przycisk **OK** na **nowy plan** sekcji, aby ukończyć instalację na plan. 

   ![Ustaw wszystkie przydziały](media/asdk-offer-services/all-quotas-set.png)

5. Zakończ tworzenie oferty, klikając **Utwórz** w sekcji planu. Po pomyślnym utworzeniu oferty i zostaną wyświetlone jako dostępne oferty, zostanie wyświetlone powiadomienie.

   ![Utworzone oferty](media/asdk-offer-services/offer-complete.png)

## <a name="set-offer-to-public"></a>Oferta zestawu na publiczne
Oferty muszą być wprowadzane publiczne dla użytkowników je wyświetlić, wybierając ofertę do subskrybowania. 

Oferty można:
- **Publiczny**: widoczne dla wszystkich użytkowników.
- **Prywatne**: widoczne tylko dla administratorów chmury. Przydatne podczas opracowywania planu lub oferty, lub jeśli administrator chmury chce utworzyć każdej subskrypcji dla użytkowników.
- **Zlikwidowane**: zamknięte dla nowych subskrybentów. Administrator chmury można użyć wycofany z eksploatacji uniknąć przyszłych subskrypcji, pozostawiając jednak bieżący subskrybentów bez zmian.

> [!TIP]
> Zmiany do oferty, nie są bezpośrednio widoczne dla użytkowników. Aby wyświetlić zmiany, użytkownicy mogą mieć wylogować i zalogować się ponownie do [portal użytkowników](https://portal.local.azurestack.external) zobaczysz tę ofertę nowe.

Aby ustawić nowe oferty na publiczne: 
   - Wersja 1803 lub nowszy: 
     1. W menu nawigacyjnym kliknij **oferuje** a następnie kliknij przycisk Oferta, dla której został utworzony.

     2. Kliknij przycisk **stan dostępności**, a następnie kliknij przycisk **publicznego**.

        ![Zmień stan](media/asdk-offer-services/change-state.png)

     3. Oferta, dla której będą teraz dostępne w portalu Azure stosu użytkownika.


   - Przed wersją 1803:  
     1. W menu nawigacyjnym kliknij **oferuje** a następnie kliknij przycisk Oferta, dla której został utworzony.

     2. Kliknij pozycję **Zmień stan**, a następnie kliknij pozycję **Publiczne**.

        ![Stan publiczny](media/asdk-offer-services/set-public.png)

     3. Oferta, dla której będą teraz dostępne w portalu Azure stosu użytkownika.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie oferty
> * Tworzenie planu
> * Ustawianie przydziałów
> * Oferta zestawu na publiczne

Przejście do dalej samouczkiem, aby dowiedzieć się, jak subskrybować oferty, utworzony jako użytkownik usługi Azure stosu.

> [!div class="nextstepaction"]
> [Subskrybowanie oferty](asdk-subscribe-services.md)