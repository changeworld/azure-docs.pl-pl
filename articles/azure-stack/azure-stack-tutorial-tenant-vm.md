---
title: Udostępnij maszyny wirtualne dla użytkowników usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak udostępnić maszyn wirtualnych w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 7c4f8fd3d7a4885893a126c63776a5e241dc0f91
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246538"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Samouczek: udostępnić maszyn wirtualnych dla użytkowników usługi Azure Stack

Jako administrator chmury Azure Stack można utworzyć oferty, które może być subskrybowana przez użytkowników (czasami określane jako dzierżaw). Subskrybuj ofertę, użytkownicy mogą korzystać z usługi Azure Stack, które udostępnia ofertę.

W tym samouczku pokazano, jak utworzyć ofertę dla maszyny wirtualnej, a następnie zaloguj się jako użytkownik do testowania oferty.

Co dowiesz się:

> [!div class="checklist"]
> * Tworzenie oferty
> * Dodawanie obrazu
> * Testowanie oferty

W usłudze Azure Stack usług są dostarczane do użytkowników przy użyciu plany, oferty i subskrypcje. Użytkownicy mogą subskrybować wiele ofert. Oferta może mieć co najmniej jeden plan, a plan może mieć co najmniej jednej usługi.

![Plany, oferty i subskrypcje](media/azure-stack-key-features/image4.png)

Aby dowiedzieć się więcej, zobacz [kluczowe funkcje i pojęcia w usłudze Azure Stack](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Tworzenie oferty

Oferty są grupami co najmniej jeden plan, które dostawcy przedstawiają użytkownikom, aby zakupić lub subskrybować. Proces tworzenia oferty składa się z kilku kroków. Po pierwsze zostanie wyświetlony monit tworzenia tej oferty, a następnie planu, a na końcu przydziałów.

1. [Zaloguj się w](azure-stack-connect-azure-stack.md) do portalu jako administrator chmury, a następnie wybierz pozycję **+ Utwórz zasób** > **oferty i plany** > **oferują**.

   ![Nowa oferta](media/azure-stack-tutorial-tenant-vm/image01.png)

1. W **nowa oferta**, wprowadź **nazwę wyświetlaną** i **Nazwa zasobu**, a następnie wybierz nową lub istniejącą **grupy zasobów**. Nazwa wyświetlana jest przyjazną nazwą oferty. Tylko operatorowi chmury wyświetlana nazwa zasobu, jest to nazwa używana przez administratorów do pracy z ofertą jako zasobem usługi Azure Resource Manager.

   ![Nazwa wyświetlana](media/azure-stack-tutorial-tenant-vm/image02.png)

1. Wybierz **plany bazowe**, a następnie w **Plan** zaznacz **Dodaj** Aby dodać nowy plan do oferty.

   ![Dodaj plan](media/azure-stack-tutorial-tenant-vm/image03.png)

1. W **nowy plan** sekcji, wypełnij **nazwę wyświetlaną** i **Nazwa zasobu**. Nazwa wyświetlana jest przyjazną nazwą planu, którą użytkownicy widzą. Operator tylko chmura zobaczyć nazwę zasobu, która jest nazwą, która operatorom chmury używana do pracy z planem jako zasobem usługi Azure Resource Manager.

   ![Nazwa wyświetlana planu](media/azure-stack-tutorial-tenant-vm/image04.png)

1. Wybierz **usług**. Z listy usług wybierz **Microsoft.Compute**, **Microsoft.Network**, i **Microsoft.Storage**. Wybierz **wybierz** do dodania tych usług, zgodnie z planem.

   ![Planowanie usług](media/azure-stack-tutorial-tenant-vm/image05.png)

1. Wybierz **przydziały**, a następnie wybierz pozycję pierwszej usługi, który chcesz utworzyć przydział dla. Dla limitu przydziału IaaS skorzystaj z poniższego przykładu jako wskazówki dotyczące konfigurowania przydziały dla usług obliczeniowych, sieci i magazynu.

   - Najpierw należy utworzyć przydział usługi Compute. Na liście przestrzeni nazw wybierz **Microsoft.Compute** , a następnie wybierz **Tworzenie nowego limitu przydziału**.

     ![Tworzenie nowego limitu przydziału](media/azure-stack-tutorial-tenant-vm/image06.png)

   - W **Utwórz przydział**, wprowadź nazwę dla limitu przydziału. Można zmienić lub akceptować wartości limitu przydziału, które są wyświetlane dla limitu przydziału, który tworzysz. W tym przykładzie firma Microsoft Zaakceptuj ustawienia domyślne i wybierz **OK**.

     ![Nazwa limitu przydziału](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Wybierz **Microsoft.Compute** w liście przestrzeni nazw, a następnie wybierz limit przydziału, który został utworzony. W ten sposób przydział usługi Compute.

     ![Wybierz limit przydziału](media/azure-stack-tutorial-tenant-vm/image08.png)

      Powtórz te czynności dla usług sieciowych i magazynowych. Gdy skończysz, wybierz pozycję **OK** w **przydziały** można zapisać cały limit przydziału.

1. W **nowy plan**, wybierz opcję **OK**.

1. W obszarze **Plan**, wybierz nowy plan, a następnie **wybierz**.

1. W **nowa oferta**, wybierz opcję **Utwórz**. Po utworzeniu oferty, zostanie wyświetlone powiadomienie.

1. W menu pulpitu nawigacyjnego wybierz **oferuje** a następnie wybierz utworzoną ofertę.

1. Wybierz **zmiany stanu**, a następnie wybierz **publicznych**.

    ![Stan publiczny](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Dodawanie obrazu

Zanim można aprowizować maszyny wirtualne, należy dodać obraz do witryny marketplace usługi Azure Stack. Możesz dodać obraz wyboru, w tym obrazów systemu Linux, w portalu Azure Marketplace.

Jeśli pracujesz w przypadku połączonych i jeśli wystąpienie usługi Azure Stack zostały zarejestrowane przy użyciu platformy Azure, następnie można pobrać obrazu maszyny Wirtualnej systemu Windows Server 2016 w portalu Azure Marketplace, korzystając z procedury opisanej w [pobierania w portalu marketplace elementy z platformy Azure do usługi Azure Stack](azure-stack-download-azure-marketplace-item.md) tematu.

Aby uzyskać informacje dotyczące dodawania różne elementy w portalu Marketplace, zobacz [stosu portalu Azure Marketplace](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testowanie oferty

Teraz, po utworzeniu oferty, można ją przetestować. Należy logować się jako użytkownik subskrybowaniu oferty, a następnie dodaj maszynę wirtualną.

1. **Subskrybowanie oferty**

   a. Zaloguj się do portalu użytkowników przy użyciu konta użytkownika, a następnie wybierz pozycję **Uzyskaj subskrypcję** kafelka.
   - Zintegrowany system, aby uzyskać adres URL zależności na region i nazwy domeny zewnętrznej przez operatora i będzie w formacie https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
   - Jeśli używasz usługi Azure Stack Development Kit portalu adres jest https://portal.local.azurestack.external.

   ![Uzyskaj subskrypcję](media/azure-stack-tutorial-tenant-vm/image10.png)

   b. W **Uzyskaj subskrypcję**, wprowadź nazwę dla Twojej subskrypcji w **nazwę wyświetlaną** pola. Wybierz **oferują**, a następnie wybierz jedno z ofert w **wybierz ofertę** listy. Wybierz pozycję **Utwórz**.

   ![Tworzenie oferty](media/azure-stack-tutorial-tenant-vm/image11.png)

   c. Do wyświetlania subskrypcji, wybierz **wszystkich usług**, a następnie w obszarze **ogólne** wybierz kategorię **subskrypcje**. Wybierz swojej nowej subskrypcji, aby wyświetlić usługi, które są częścią subskrypcji.

   >[!NOTE]
   >Po subskrybowaniu oferty, trzeba będzie odświeżyć portal aby zobaczyć, jakie usługi są częścią nowej subskrypcji.

1. **Inicjowanie obsługi administracyjnej maszyny wirtualnej**

   Za pomocą portalu użytkownika można aprowizować maszynę wirtualną, korzystając z nowej subskrypcji.

   a. Zaloguj się do portalu użytkowników przy użyciu konta użytkownika.
      - Zintegrowany system, aby uzyskać adres URL zależności na region i nazwy domeny zewnętrznej przez operatora i będzie w formacie https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
   - Jeśli używasz usługi Azure Stack Development Kit portalu adres jest https://portal.local.azurestack.external.

   b.  Na pulpicie nawigacyjnym wybierz **+ Utwórz zasób** > **obliczenia** > **systemu Windows Server 2016 Datacenter — wersja próbna**, a następnie wybierz pozycję **Tworzenie**.

   c. W **podstawy**, podaj następujące informacje:
      - Wprowadź **nazwy**
      - Wprowadź **nazwy użytkownika**
      - Wprowadź **hasła**
      - Wybierz **subskrypcji**
      - Tworzenie **grupy zasobów** (lub wybierz istniejącą). 
      - Wybierz **OK** można zapisać tych informacji.

   d. W **wybierz rozmiar**, wybierz opcję **standardowa A1**, a następnie **wybierz**.  

   e. W **ustawienia**, wybierz opcję **sieć wirtualna**.

   f. W **wybierz sieć wirtualną**, wybierz opcję **Utwórz nową**.

   g. W **Utwórz sieć wirtualną**, Zaakceptuj wszystkie ustawienia domyślne i wybierz **OK**.

   h. Wybierz **OK** w **ustawienia** można zapisać konfiguracji sieci.

      i. W **Podsumowanie**, wybierz opcję **OK** do utworzenia maszyny wirtualnej.  

   j. Aby wyświetlić nową maszynę wirtualną, wybierz **wszystkie zasoby**. Wyszukaj maszynę wirtualną, a następnie wybierz jego nazwę w wynikach wyszukiwania.

   
## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie oferty
> * Dodawanie obrazu
> * Testowanie oferty

Przejdź do następnego samouczka, aby dowiedzieć się, jak:
> [!div class="nextstepaction"]
> [Udostępnij baz danych SQL dla użytkowników usługi Azure Stack](azure-stack-tutorial-sql-server.md)