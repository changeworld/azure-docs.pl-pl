---
title: Subskrybowanie oferty Azure stosu | Dokumentacja firmy Microsoft
description: Utwórz subskrypcje w przypadku ofert w stosie Azure
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/11/2018
ms.author: brenduns
ms.openlocfilehash: 9153649774a67533649fb62da83a3f50abd592da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295214"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Utwórz subskrypcje, aby oferty Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Po [utworzyć ofertę](azure-stack-create-offer.md), użytkownicy muszą subskrypcji do tej oferty, zanim będzie można go używać. Istnieją dwa sposoby, aby uzyskać subskrypcję użytkowników do oferty:

- Jako operatora chmury można utworzyć subskrypcji dla użytkownika w portalu administratora. Subskrypcje, które możesz utworzyć można w przypadku ofert publicznymi i prywatnymi.
- Jako użytkownik dzierżawy będzie możliwe subskrybowanie ofertę publiczną przy użyciu portalu użytkownika.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Utwórz subskrypcję jako operatorowi chmury

Operatorom chmury można korzystać z portalu administratora, aby utworzyć subskrypcję do oferty dla użytkownika.  Można utworzyć subskrypcji dla członków dzierżawy katalogu.  Gdy [wielodostępność](azure-stack-enable-multitenancy.md) jest włączone, można również tworzyć subskrypcje dla użytkowników w katalogu dodatkowych dzierżaw.

Jeśli nie chcesz dzierżawcom tworzyć własne subskrypcje, Przekształć w prywatny Twojej oferty, a następnie utworzyć subskrypcji dla dzierżawców. Ta metoda jest wspólnych składników podczas integrowania z zewnętrznego rozliczeń lub systemami katalogu usługi Azure stosu.

Po utworzeniu subskrypcji dla użytkownika mogą zalogować się do portalu użytkowników i zobacz, czy jest subskrybuje oferty.  

### <a name="to-create-a-subscription-for-a-user"></a>Aby utworzyć subskrypcję dla użytkownika

1. W portalu administracyjnym, przejdź do **subskrypcji użytkownika.**
2. Wybierz pozycję **Dodaj**. W obszarze **nową subskrypcję użytkownika**, wprowadź następujące informacje:  

   - **Nazwa wyświetlana** — przyjazną nazwę dla identyfikacji subskrypcji, która jest wyświetlana jako *Nazwa subskrypcji użytkownika*.
   - **Użytkownik** — Określ użytkownik dzierżawy dostępnych dla tej subskrypcji. Nazwa użytkownika jest wyświetlana jako *właściciela*.  Format nazwy użytkownika zależy od rozwiązania tożsamości. Na przykład:

     - **Azure AD:***&lt;Użytkownik1 > @&lt;contoso.onmicrosoft.com >* 

     - **Usługi AD FS:***&lt;Użytkownik1 > @&lt;azurestack.local >* 

   - **Katalog dzierżawy** — wybierz dzierżawy katalogu, w których należy konto użytkownika. Jeśli nie włączono obsługi wielu dzierżawców, dzierżawy katalogu lokalnego jest dostępna.

3. Wybierz **oferują**. W obszarze **oferuje**, wybierz **oferują** dla tej subskrypcji. Ponieważ w przypadku tworzenia subskrypcji dla użytkownika, wybierz **prywatnej** jako stan dostępności.

4. Wybierz **Utwórz** do utworzenia subskrypcji. Zobaczysz nową subskrypcję w obszarze **subskrypcji użytkownika**. Gdy użytkownik loguje się do portalu użytkowników zostanie wyświetlona Szczegóły subskrypcji.

### <a name="to-make-an-add-on-plan-available"></a>Aby udostępnić plan dodatku

Operatorowi chmury można dodać plan dodatek do utworzonej wcześniej subskrypcję w dowolnym momencie:

1. W portalu administracyjnym wybierz **więcej usług** > **subskrypcji użytkownika**. Wybierz subskrypcję, którą chcesz zmienić.

2. Wybierz **dodatki** , a następnie wybierz **+ Dodaj**.  

3. W obszarze **planu Dodaj**, wybierz plan mają jako dodatek.

## <a name="create-a-subscription-as-a-user"></a>Utwórz subskrypcję jako użytkownik

Użytkownik może Zaloguj się do portalu użytkowników do lokalizowania i subskrybować publiczne oferty i planów dodatku dla dzierżawy katalogu (organizacja).

>[!NOTE]
>Jeśli środowisko stosu Azure obsługuje [wielodostępność](azure-stack-enable-multitenancy.md) można również subskrybować ofert od dzierżawcy katalog zdalny.

### <a name="to-subscribe-to-an-offer"></a>Aby subskrybować oferty

1. [Zaloguj się](azure-stack-connect-azure-stack.md) do aplikacji portal użytkowników usługi Azure stosu (https://portal.local.azurestack.external) i wybierz **uzyskania subskrypcji**.

   ![Uzyskaj subskrypcję](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. W obszarze **uzyskania subskrypcji**, wprowadź przyjazną nazwę subskrypcji w **Nazwa wyświetlana**. Wybierz **oferują** i w obszarze **wybierz ofertę**, wybierz ofertę. Wybierz **Utwórz** do utworzenia subskrypcji.

   ![Tworzenie oferty](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Po zasubskrybowaniu ofertę odświeżyć portalu, aby wyświetlić usługi, które są częścią nowej subskrypcji.
4. Aby wyświetlić utworzony subskrypcji, wybierz **więcej usług** , a następnie wybierz **subskrypcje**. Wybierz subskrypcję, aby wyświetlić szczegóły subskrypcji.  

### <a name="to-subscribe-to-an-add-on-plan"></a>Aby subskrybować plan dodatku

Jeśli oferta plan dodatek, subskrypcję w dowolnym momencie można dodać tego planu.  

1. W portalu użytkowników, wybierz **więcej usług** > **subskrypcje**, a następnie wybierz subskrypcję, zmiany. Jeśli są dostępne, wszystkie plany dodatek **+ Dodaj planu** jest aktywne i ma kafelka w celu **planów dodatek**.

   >[!NOTE]
   >Jeśli **+ Dodaj planu** nie jest aktywne, a następnie nie ma żadnych planów dodatek do oferty skojarzone z jego subskrypcją.

1. Wybierz **+ Dodaj planu** lub **planów dodatek** kafelka. W obszarze **planów dodatek**, wybierz plan, którego chcesz dodać.

## <a name="next-steps"></a>Kolejne kroki

[Inicjowanie obsługi administracyjnej maszyny wirtualnej](azure-stack-provision-vm.md)
