---
title: Udostępnić użytkownikom stosu Azure maszyny wirtualnej | Dokumentacja firmy Microsoft
description: Samouczek, aby udostępnić maszyn wirtualnych na stosie Azure
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
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: af97f32736959f8ebf8f3c4fbca400d6b0c41f3e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="make-virtual-machines-available-to-your-azure-stack-users"></a>Udostępnić użytkownikom stosu Azure maszyny wirtualne
Jako administrator chmury Azure stosu można utworzyć oferty, które mogą subskrybować użytkowników (nazywane czasami dzierżawcy). Za pomocą swoich subskrypcji, użytkownicy będą mogły używać usługi Azure stosu.

W tym artykule przedstawiono sposób tworzenia oferty, a następnie sprawdź go. Dla testu będzie zalogować się do portalu jako użytkownik, subskrybować oferty, a następnie utwórz maszynę wirtualną przy użyciu subskrypcji.

Co dowiesz się:

> [!div class="checklist"]
> * Tworzenie oferty
> * Dodawanie obrazu
> * Testowanie oferty


W stosie Azure usługi są dostarczane do użytkowników przy użyciu subskrypcji, ofertami i planów. Użytkownicy mogą subskrybować wielu ofert. Oferty może mieć co najmniej jeden plan i planów może mieć co najmniej jedna usługa.

![Subskrypcje, ofertami i planów](media/azure-stack-key-features/image4.png)

Aby dowiedzieć się więcej, zobacz [główne funkcje i pojęcia dotyczące stosu Azure](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Tworzenie oferty

Teraz możesz uzyskać rzeczy gotowy dla użytkowników. Podczas uruchamiania procesu monit najpierw Utwórz ofertę, a następnie planu, a na końcu przydziałów.

3. **Tworzenie oferty**

   Oferty to grupy co najmniej jeden plan udostępniające dostawców do użytkowników w celu zakupu lub subskrybować.

   a. [Zaloguj się](azure-stack-connect-azure-stack.md) do portalu jako administrator chmury, a następnie kliknij przycisk **nowy** > **oferuje + plany** > **oferują**.
   ![Nowe oferty](media/azure-stack-tutorial-tenant-vm/image01.png)

   b. W **oferują nowe** sekcji, wypełnij **Nazwa wyświetlana** i **Nazwa zasobu**, a następnie wybierz nowy lub istniejący **grupy zasobów**. Nazwa wyświetlana jest przyjazną nazwą oferty. Operator chmury wyświetlana nazwa zasobu. Jest to nazwa używana przez administratorów do pracy z ofertą jako zasobem usługi Azure Resource Manager.

   ![Nazwa wyświetlana](media/azure-stack-tutorial-tenant-vm/image02.png)

   c. Kliknij przycisk **podstawowa planów**i w **planu** kliknij **Dodaj** można dodać nowego planu do oferty.

   ![Dodaj plan](media/azure-stack-tutorial-tenant-vm/image03.png)

   d. W **nowy Plan** sekcji, wypełnij **Nazwa wyświetlana** i **Nazwa zasobu**. Nazwa wyświetlana jest przyjazna nazwa planu, dostępna dla użytkowników. Operator chmury wyświetlana nazwa zasobu. Jest to nazwa, Operatorzy chmury używanego do pracy z planem jako zasób usługi Azure Resource Manager.

   ![Nazwa wyświetlana planu](media/azure-stack-tutorial-tenant-vm/image04.png)

   e. Kliknij przycisk **usług**, wybierz pozycję **Microsoft.Compute**, **Microsoft.Network**, i **Microsoft.Storage**, a następnie kliknij przycisk **Wybierz**.

   ![Planowanie usług](media/azure-stack-tutorial-tenant-vm/image05.png)

   f. Kliknij przycisk **przydziały**, a następnie wybierz pierwszej usługi, dla której chcesz utworzyć przydział. Dla limitu przydziału IaaS wykonaj następujące kroki dla usług obliczeniowych, sieci i magazynu.

   W tym przykładzie mamy najpierw utworzyć przydział dla usługi obliczeniowej. Na liście przestrzeni nazw, wybierz **Microsoft.Compute** przestrzeni nazw, a następnie kliknij przycisk **Utwórz nowy przydział**.
   
   ![Tworzenie nowego przydziału](media/azure-stack-tutorial-tenant-vm/image06.png)

   g. Na **Tworzenie przydziału** sekcji, wpisz nazwę przydziału i ustaw odpowiednie parametry limitu przydziału i kliknij **OK**.

   ![Nazwa przydziału](media/azure-stack-tutorial-tenant-vm/image07.png)

   h. Teraz, aby uzyskać **Microsoft.Compute**, wybierz limit przydziału, który został utworzony.

   ![Wybierz limit przydziału](media/azure-stack-tutorial-tenant-vm/image08.png)

   Powtórz te kroki dla usług sieci i magazynu, a następnie kliknij przycisk **OK** na **przydziały** sekcji.

   i. Kliknij przycisk **OK** na **nowy plan** sekcji.

   j. Na **planu** , wybierz nowy plan i kliknij pozycję **wybierz**.

   k. Na **nowej oferty** kliknij **Utwórz**. Zostanie wyświetlone powiadomienie po utworzeniu oferty.

   l. W menu nawigacyjnym kliknij **oferuje** a następnie kliknij przycisk Oferta, dla której został utworzony.

   m. Kliknij pozycję **Zmień stan**, a następnie kliknij pozycję **Publiczne**.

   ![Stan publiczny](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Dodawanie obrazu

Przed udostępnieniem można maszyn wirtualnych, należy dodać obraz do stosu Azure marketplace. Możesz dodać obraz wyboru, w tym obrazów systemu Linux, z poziomu portalu Azure Marketplace.

Jeśli pracujesz w scenariuszu połączonych i jeśli wystąpienia stosu Azure zostały zarejestrowane przy użyciu platformy Azure, następnie można pobrać obrazu maszyny Wirtualnej systemu Windows Server 2016 z portalu Azure Marketplace przy użyciu procedury opisanej w [pobierania witryny marketplace elementy z platformy Azure do stosu Azure](azure-stack-download-azure-marketplace-item.md) tematu.

Aby uzyskać informacji o dodawaniu różne elementy do witryny marketplace, zobacz [Azure Marketplace stosu](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testowanie oferty

Teraz, po utworzeniu oferty, można sprawdzić. Zaloguj się jako użytkownik i subskrybować oferty, a następnie dodać maszyny wirtualnej.

1. **Subskrybowanie oferty**

   Teraz możesz zalogować się do portalu użytkownika do subskrybowania oferty.

   a. Zaloguj się do portalu użytkowników jako użytkowników i kliknij przycisk **uzyskania subskrypcji**.
   - Zintegrowany system, zmienia się na podstawie od regionu i nazwy domen zewnętrznych z operatorem adres URL, a musi być w formacie https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
   - Jeśli używasz usługi Azure stosu Development Kit adres portalu jest https://portal.local.azurestack.external.

   ![Uzyskaj subskrypcję](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. W **Nazwa wyświetlana** pola, wpisz nazwę dla Twojej subskrypcji, kliknij przycisk **oferują**, kliknij jeden z oferty w **wybierz ofertę** sekcji, a następnie kliknij przycisk  **Utwórz**.

   ![Tworzenie oferty](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Aby wyświetlić utworzony subskrypcji, kliknij przycisk **więcej usług**, kliknij przycisk **subskrypcje**, następnie kliknij przycisk nowej subskrypcji.  

   Po zasubskrybowaniu ofertę odświeżyć portalu, aby wyświetlić usługi, które są częścią nowej subskrypcji.

2. **Inicjowanie obsługi administracyjnej maszyny wirtualnej**

   Teraz możesz zalogować się do portalu użytkownika do udostępnienia maszyny wirtualnej przy użyciu subskrypcji. 

   a. Zaloguj się do portalu użytkowników, jako użytkownik.
      - Zintegrowany system, zmienia się na podstawie od regionu i nazwy domen zewnętrznych z operatorem adres URL, a musi być w formacie https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
   - Jeśli używasz usługi Azure stosu Development Kit adres portalu jest https://portal.local.azurestack.external.

   b.  Na pulpicie nawigacyjnym kliknij **nowy** > **obliczeniowe** > **Eval centrum danych systemu Windows Server 2016**, a następnie kliknij przycisk **Utwórz**.

   c. W **podstawy** wpisz **nazwa**, **nazwy użytkownika**, i **hasło**, wybierz **subskrypcji**, Utwórz **grupy zasobów** (lub wybrać istniejącą), a następnie kliknij przycisk **OK**.

   d. W **wybierz rozmiar** kliknij **A1 standardowe**, a następnie kliknij przycisk **wybierz**.  

   e. W **ustawienia** kliknij **sieci wirtualnej**. W **sieci wirtualnej wybierz** kliknij **Utwórz nowy**. W **Utwórz sieć wirtualną** sekcji, Zaakceptuj wszystkie ustawienia domyślne, a następnie kliknij przycisk **OK**. W **ustawienia** kliknij **OK**.

   ![Tworzenie sieci wirtualnej](media/azure-stack-provision-vm/image04.png)

   f. W **Podsumowanie** kliknij **OK** można utworzyć maszyny wirtualnej.  

   g. Aby wyświetlić nową maszynę wirtualną, kliknij **wszystkie zasoby**, następnie wyszukaj maszyny wirtualnej i kliknij jego nazwę.

    ![Wszystkie zasoby](media/azure-stack-provision-vm/image06.png)

Przedstawiono w tym samouczku:

> [!div class="checklist"]
> * Tworzenie oferty
> * Dodawanie obrazu
> * Testowanie oferty

> [!div class="nextstepaction"]
> [Udostępnić aplikacje interfejsu API, mobilne i sieci web użytkownikom Azure stosu](azure-stack-tutorial-app-service.md)