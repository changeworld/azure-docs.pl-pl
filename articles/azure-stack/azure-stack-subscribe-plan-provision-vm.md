---
title: Subskrybowanie oferty w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Tworzenie subskrypcji dla ofert w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: efemmano
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 6f91d011e9f15b80c2efd6ad1c6bbcd549786882
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2019
ms.locfileid: "55976847"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Tworzenie subskrypcji ofert w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Po zakończeniu [Utwórz ofertę](azure-stack-create-offer.md), użytkownicy muszą subskrypcji dla tej oferty, zanim będzie można go używać. Istnieją dwa sposoby, Uzyskaj subskrypcję użytkowników oferty:

- Operator chmury można utworzyć subskrypcji dla użytkownika z portalu administratora. Subskrypcje, które tworzysz może być w przypadku ofert prywatnych i publicznych.
- Jako użytkownik dzierżawy możesz subskrybować oferty publicznej korzystając z portalu użytkowników.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Utwórz subskrypcję jako operator chmury

Operatorzy chmury mogą korzystać z portalu administratora, aby utworzyć subskrypcję z ofertą użytkownika. Można tworzyć subskrypcje dla członków dzierżawie katalogu. Gdy [wielodostępu](azure-stack-enable-multitenancy.md) jest włączone, można również utworzyć subskrypcji dla użytkowników w dzierżawach dodatkowego katalogu.

Jeśli nie chcesz, aby dzierżawcom możliwości tworzenia własnych subskrypcji, Oznacz jako prywatne ofert, a następnie Utwórz subskrypcje dla dzierżawców. To podejście jest typowa, gdy integracji usługi Azure Stack z zewnętrznego rozliczeń lub systemy wykazu usług.

Po utworzeniu subskrypcji dla użytkownika, można zalogować się do portalu użytkowników i zobacz, że mają subskrypcję oferty.  

### <a name="to-create-a-subscription-for-a-user"></a>Aby utworzyć subskrypcję dla użytkownika

1. W portalu administracyjnym, przejść do **subskrypcji użytkownika.**
2. Wybierz pozycję **Dodaj**. W obszarze **Nowa subskrypcja użytkownika**, wprowadź następujące informacje:  

   - **Nazwa wyświetlana** — przyjazna nazwa do identyfikacji subskrypcję, która jest wyświetlana jako *Nazwa subskrypcji użytkownika*.
   - **Użytkownik** — określ użytkownika z dzierżawy katalogu dostępna dla tej subskrypcji. Nazwa użytkownika jest wyświetlana jako *właściciela*.  Format nazwy użytkownika jest zależna od danym rozwiązaniu tożsamości. Na przykład:

     - **Usługa Azure AD:** `<user1>@<contoso.onmicrosoft.com>`

     - **USŁUG AD FS:** `<user1>@<azurestack.local>`

   - **Dzierżawy katalogu** — wybierz dzierżawę katalogu, w której należy konto użytkownika. Jeśli nie została włączona wielodostępność, dostępna jest dzierżawie katalogu lokalnego.

3. Wybierz **oferują**. W obszarze **oferuje**, wybierz **oferują** dla tej subskrypcji. Ponieważ tworzysz subskrypcji dla użytkownika, wybierz **prywatnej** jako stan ułatwień dostępu.

4. Wybierz **Utwórz** do utworzenia subskrypcji. Nowa subskrypcja jest wyświetlana w obszarze **subskrypcja użytkownika**. Gdy użytkownik loguje się do portalu użytkowników, mogą zobaczyć szczegółowe informacje dotyczące subskrypcji.

### <a name="to-make-an-add-on-plan-available"></a>Aby udostępnić planu dodatku

Operator chmury można dodać planu do utworzonej wcześniej subskrypcję w dowolnym momencie:

1. W portalu administracyjnym wybierz **wszystkich usług** a następnie w obszarze **zasoby administracyjne** kategorii, wybierz opcję **subskrypcji użytkownika**. Wybierz subskrypcję, którą chcesz zmienić.

2. Wybierz **dodatki** , a następnie wybierz **+ Dodaj**.  

3. W obszarze **Dodaj plan**, wybieranie odpowiedniego planu jako dodatek.

## <a name="create-a-subscription-as-a-user"></a>Utwórz subskrypcję jako użytkownik

Jako użytkownik należy zalogować się do portalu użytkowników, aby zlokalizować i subskrybować oferty publicznej i planów dodatkowych dla Twojej dzierżawy katalogu (organizacja).

>[!NOTE]
>Jeśli Twoje środowisko usługi Azure Stack obsługuje [wielodostępu](azure-stack-enable-multitenancy.md), można również subskrybować oferty w dzierżawie katalogu zdalnego.

### <a name="to-subscribe-to-an-offer"></a>Subskrybowanie oferty

1. [Zaloguj się w](azure-stack-connect-azure-stack.md) do [portal użytkowników usługi Azure Stack](https://portal.local.azurestack.external) i wybierz **Uzyskaj subskrypcję**.

   ![Uzyskaj subskrypcję](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. W obszarze **Uzyskaj subskrypcję**, wprowadź przyjazną nazwę subskrypcji w **nazwę wyświetlaną**. Wybierz **oferują** i w obszarze **wybierz ofertę**, wybierz ofertę. Wybierz **Utwórz** do utworzenia subskrypcji.

   ![Tworzenie oferty](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Po subskrybowaniu oferty, Odśwież portal aby zobaczyć, jakie usługi są częścią nowej subskrypcji.

4. Aby wyświetlić subskrypcji, którą utworzono, wybierz **wszystkich usług** a następnie w obszarze **ogólne** wybierz kategorię **subskrypcje**. Wybierz subskrypcję, aby wyświetlić szczegóły subskrypcji.  

### <a name="to-subscribe-to-an-add-on-plan"></a>Aby subskrybować planu dodatku

Jeśli oferta ma planu dodatku, można dodać ten plan do subskrypcji w dowolnym momencie.  

1. W aplikacji portal użytkowników, wybierz **wszystkich usług**. Następnie w obszarze **ogólne** kategorii, wybierz opcję **subskrypcje**, a następnie wybierz subskrypcję, zmiany. Jeśli są dostępne, żadnych planów dodatków **+ Dodaj plan** jest aktywny i Kafelek, aby **planów dodatków**. 

   Jeśli **+ Dodaj plan** nie jest aktywne, a następnie nie ma żadnych planów dodatków dla oferty skojarzonego z posiadaną subskrypcją.

1. Wybierz **+ Dodaj plan** lub **planów dodatków** kafelka. W obszarze **planów dodatków**, wybierz plan, którego chcesz dodać.

## <a name="next-steps"></a>Kolejne kroki

- [Inicjowanie obsługi administracyjnej maszyny wirtualnej](azure-stack-provision-vm.md)
