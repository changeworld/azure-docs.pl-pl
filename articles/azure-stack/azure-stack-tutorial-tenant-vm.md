---
title: Udostępnić użytkownikom stosu Azure maszyny wirtualnej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak udostępnić maszyn wirtualnych na stosie Azure
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
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 9329cb0dbfa24cf239b820573ef7f642cdca9103
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248163"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Samouczek: udostępnia maszyny wirtualne Azure stosu użytkowników

Jako administrator chmury Azure stosu można utworzyć oferty, które mogą subskrybować użytkowników (nazywane czasami dzierżawcy). Subskrybuj oferty, użytkownicy mogą używać usług Azure stosu, które zapewnia oferty.

W tym samouczku pokazano, jak utworzyć ofertę dla maszyny wirtualnej, a następnie zaloguj się jako użytkownik do testowania oferty.

Co dowiesz się:

> [!div class="checklist"]
> * Tworzenie oferty
> * Dodawanie obrazu
> * Testowanie oferty

W stosie Azure usługi są dostarczane do użytkowników przy użyciu subskrypcji, ofertami i planów. Użytkownicy mogą subskrybować wielu ofert. Oferta może mieć co najmniej jeden plan i planu może mieć co najmniej jedną usługę.

![Subskrypcje, ofertami i planów](media/azure-stack-key-features/image4.png)

Aby dowiedzieć się więcej, zobacz [główne funkcje i pojęcia dotyczące stosu Azure](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Tworzenie oferty

Oferty to grupy co najmniej jeden plan udostępniające dostawców do użytkowników w celu zakupu lub subskrybować. Proces tworzenia oferty ma kilka kroków. Po pierwsze zostanie wyświetlony monit o Utwórz ofertę, a następnie planu, a na końcu przydziałów.

1. [Zaloguj się](azure-stack-connect-azure-stack.md) do portalu jako administrator chmury, a następnie wybierz **nowy** > **oferuje + plany** > **oferują**.

   ![Nowa oferta](media/azure-stack-tutorial-tenant-vm/image01.png)

2. W **oferują nowe**, wprowadź **Nazwa wyświetlana** i **Nazwa zasobu**, a następnie wybierz nowy lub istniejący **grupy zasobów**. Nazwa wyświetlana jest przyjazną nazwą oferty. Operator chmury wyświetlana nazwa zasobu. Jest to nazwa używana przez administratorów do pracy z ofertą jako zasobem usługi Azure Resource Manager.

   ![Nazwa wyświetlana](media/azure-stack-tutorial-tenant-vm/image02.png)

3. Wybierz **podstawowa planów**i w **planu** zaznacz **Dodaj** można dodać nowego planu do oferty.

   ![Dodaj plan](media/azure-stack-tutorial-tenant-vm/image03.png)

4. W **nowy Plan** sekcji, wypełnij **Nazwa wyświetlana** i **Nazwa zasobu**. Nazwa wyświetlana jest przyjazna nazwa planu, dostępna dla użytkowników. Operator chmury wyświetlana nazwa zasobu. Jest to nazwa, Operatorzy chmury używanego do pracy z planem jako zasób usługi Azure Resource Manager.

   ![Nazwa wyświetlana planu](media/azure-stack-tutorial-tenant-vm/image04.png)

5. Wybierz **usług**. Na liście usług wybierz **Microsoft.Compute**, **Microsoft.Network**, i **Microsoft.Storage**. Wybierz **wybierz** do dodania tych usług do planu.

   ![Planowanie usług](media/azure-stack-tutorial-tenant-vm/image05.png)

6. Wybierz **przydziały**, a następnie wybierz pierwszej usługi, który chcesz utworzyć przydział dla. Dla limitu przydziału IaaS skorzystaj z następującego przykładu jako przewodnik konfigurowania przydziały na potrzeby usług obliczeniowych, sieci i magazynu.

   - Najpierw należy utworzyć przydział dla usługi obliczeniowej. Na liście przestrzeni nazw, wybierz **Microsoft.Compute** , a następnie wybierz **Utwórz nowy przydział**.

     ![Tworzenie nowego przydziału](media/azure-stack-tutorial-tenant-vm/image06.png)

   - W **Tworzenie przydziału**, wprowadź nazwę dla limitu przydziału. Można zmienić lub akceptuje dowolne wartości limitu przydziału, które są wyświetlane dla tworzonego limit przydziału. W tym przykładzie możemy zaakceptować ustawienia domyślne i wybierz **OK**.

     ![Nazwa przydziału](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Wybierz **Microsoft.Compute** w liście przestrzeni nazw, a następnie wybierz limit przydziału, który został utworzony. Limit przydziału to łączy do usługi obliczeniowej.

     ![Wybierz limit przydziału](media/azure-stack-tutorial-tenant-vm/image08.png)

      Powtórz te kroki dla usług sieci i magazynu. Gdy skończysz, wybierz **OK** w **przydziały** można zapisać wszystkich przydziałów.

7. W **nowy plan**, wybierz pozycję **OK**.

8. W obszarze **planu**, wybierz nowego planu, a następnie **wybierz**.

9. W **nowej oferty**, wybierz pozycję **Utwórz**. Po utworzeniu oferty, zostanie wyświetlone powiadomienie.

10. W menu nawigacyjnym wybierz **oferuje** , a następnie wybierz oferty, został utworzony.

11. Wybierz **zmiany stanu**, a następnie wybrano **publicznego**.

    ![Stan publiczny](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Dodawanie obrazu

Przed udostępnieniem można maszyn wirtualnych, należy dodać obraz do stosu Azure marketplace. Możesz dodać obraz wyboru, w tym obrazów systemu Linux, z poziomu portalu Azure Marketplace.

Jeśli pracujesz w scenariuszu połączonych i jeśli wystąpienia stosu Azure zostały zarejestrowane przy użyciu platformy Azure, następnie można pobrać obrazu maszyny Wirtualnej systemu Windows Server 2016 z portalu Azure Marketplace przy użyciu procedury opisanej w [pobierania witryny marketplace elementy z platformy Azure do stosu Azure](azure-stack-download-azure-marketplace-item.md) tematu.

Aby uzyskać informacji o dodawaniu różne elementy do witryny marketplace, zobacz [Azure Marketplace stosu](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testowanie oferty

Teraz, po utworzeniu oferty, można sprawdzić. Będzie Zaloguj się jako użytkownik, subskrybować oferty, a następnie dodaj maszynę wirtualną.

1. **Subskrybowanie oferty**

   a. Zaloguj się do portalu użytkowników przy użyciu konta użytkownika, a następnie wybierz **uzyskania subskrypcji** kafelka.
   - Zintegrowany system, zmienia się na podstawie od regionu i nazwy domen zewnętrznych z operatorem adres URL, a musi być w formacie https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
   - Jeśli używasz usługi Azure stosu Development Kit adres portalu jest https://portal.local.azurestack.external.

   ![Uzyskaj subskrypcję](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. W **uzyskania subskrypcji**, wprowadź nazwę dla Twojej subskrypcji w **Nazwa wyświetlana** pola. Wybierz **oferują**, a następnie wybierz jedną z oferty w **wybierz ofertę** listy. Wybierz pozycję **Utwórz**.

   ![Tworzenie oferty](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Aby wyświetlić subskrypcji, wybierz **więcej usług**, a następnie wybierz **subskrypcje**. Wybierz nowej subskrypcji, aby wyświetlić usługi, które są częścią subskrypcji.

   >[!NOTE]
   >Po zasubskrybowaniu oferta może być konieczne Odśwież portalu, aby wyświetlić usługi, które są częścią nowej subskrypcji.

2. **Inicjowanie obsługi administracyjnej maszyny wirtualnej**

   Za pomocą portalu użytkownika można udostępnić maszynę wirtualną przy użyciu nowej subskrypcji.

   a. Zaloguj się do portalu użytkowników przy użyciu konta użytkownika.
      - Zintegrowany system, zmienia się na podstawie od regionu i nazwy domen zewnętrznych z operatorem adres URL, a musi być w formacie https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
   - Jeśli używasz usługi Azure stosu Development Kit adres portalu jest https://portal.local.azurestack.external.

   b.  Na pulpicie nawigacyjnym i wybierz **nowy** > **obliczeniowe** > **Eval centrum danych systemu Windows Server 2016**, a następnie wybierz **Utwórz**.

   c. W **podstawy**, podaj następujące informacje:
      - Wprowadź **nazwy**
      - Wprowadź **nazwy użytkownika**
      - Wprowadź **hasła**
      - Wybierz **subskrypcji**
      - Utwórz **grupy zasobów** (lub wybierz istniejący.) 
      - Wybierz **OK** Aby zapisać te informacje.

   d. W **wybierz rozmiar**, wybierz pozycję **A1 standardowe**, a następnie **wybierz**.  

   e. W **ustawienia**, wybierz pozycję **sieci wirtualnej**.

   f. W **sieci wirtualnej wybierz**, wybierz pozycję **Utwórz nowy**.

   g. W **Utwórz sieć wirtualną**, Zaakceptuj wszystkie ustawienia domyślne, a wybierz **OK**.

   h. Wybierz **OK** w **ustawienia** można zapisać konfiguracji sieci.

   ![Tworzenie sieci wirtualnej](media/azure-stack-provision-vm/image04.png)

   i. W **Podsumowanie**, wybierz pozycję **OK** można utworzyć maszyny wirtualnej.  

   j. Aby wyświetlić nową maszynę wirtualną, wybierz **wszystkie zasoby**. Wyszukaj maszyny wirtualnej, a zaznacz jego nazwę w wynikach wyszukiwania.

   ![Wszystkie zasoby](media/azure-stack-provision-vm/image06.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie oferty
> * Dodawanie obrazu
> * Testowanie oferty

ADVANCE do samouczka dalej, aby dowiedzieć się, jak:
> [!div class="nextstepaction"]
> [Udostępnić użytkownikom Azure stosu bazy danych SQL](azure-stack-tutorial-sql-server.md)