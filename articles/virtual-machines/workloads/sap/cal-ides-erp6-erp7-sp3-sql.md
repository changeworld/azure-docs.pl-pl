---
title: Wdrażanie oprogramowania SAP IDES EHP7 SP3 dla oprogramowania SAP ERP 6.0 na platformie Azure | Dokumentacja firmy Microsoft
description: Wdrażanie oprogramowania SAP IDES EHP7 SP3 dla oprogramowania SAP ERP 6.0 na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 413e449f005d288f66da0257f5b800733e58eb94
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60836589"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Wdrażanie oprogramowania SAP IDES EHP7 SP3 dla oprogramowania SAP ERP 6.0 na platformie Azure
W tym artykule opisano, jak wdrożyć system SAP IDES z programem SQL Server i systemu operacyjnego Windows na platformie Azure przy użyciu biblioteki SAP Cloud Appliance Library (SAP CAL) 3.0. Zrzuty ekranu pokazują krok po kroku procesu. Aby wdrożyć inne rozwiązanie, wykonaj te same czynności.

Aby rozpocząć SAP CAL, przejdź do [SAP Cloud Appliance Library](https://cal.sap.com/) witryny sieci Web. Firma SAP ma również blog o nowym [SAP Cloud Appliance biblioteki 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
> Od 29 maja 2017 r. modelu wdrażania usługi Azure Resource Manager, oprócz preferowane mniej klasycznego modelu wdrażania można użyć do wdrożenia SAP CAL. Firma Microsoft zaleca użycie nowego modelu wdrażania usługi Resource Manager, a następnie pominąć klasycznego modelu wdrażania.

Jeśli nie zostało utworzone konto SAP CAL, która korzysta z modelu klasycznego, *musisz utworzyć nowe konto SAP CAL*. To konto musi wyłącznie wdrażanie na platformie Azure przy użyciu modelu usługi Resource Manager.

Po zalogowaniu się do SAP CAL, pierwsza strona zwykle prowadzi do **rozwiązania** strony. Rozwiązań oferowanych w bibliotece SAP CAL stale rosną, więc może być konieczne przewinięcie widoku w chwilę dość mogła znaleźć rozwiązania, które mają. Wyróżnione rozwiązanie oparte na Windows SAP IDES, która jest dostępna wyłącznie na platformie Azure przedstawia proces wdrażania:

![Rozwiązania programu SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Tworzenie konta usługi w SAP CAL
1. Aby zalogować się do SAP CAL po raz pierwszy, użyj SAP S-użytkownika lub inny użytkownik zarejestrowany z oprogramowaniem SAP. Następnie zdefiniuj SAP CAL konta, które jest używane przez SAP CAL do wdrażania urządzeń na platformie Azure. W definicji konta należy:

    a. Wybierz model wdrażania na platformie Azure (Resource Manager lub model klasyczny).

    b. Wprowadź swoją subskrypcję platformy Azure. Konto SAP CAL można przypisać do tylko jednej subskrypcji. Jeśli potrzebujesz więcej niż jedną subskrypcję, musisz utworzyć nowe konto SAP CAL.
    
    c. Nadaj uprawnienia SAP CAL do wdrożenia w ramach subskrypcji platformy Azure.

   > [!NOTE]
   >  Następne kroki pokazują jak utworzyć konto SAP CAL dla wdrożeń usługi Resource Manager. Jeśli masz już konto SAP CAL, który jest połączony z klasycznego modelu wdrażania, możesz *muszą* wykonaj następujące kroki, aby utworzyć nowe konto SAP CAL. Nowe konto SAP CAL musi wdrożyć w modelu usługi Resource Manager.

1. Aby utworzyć nowe konto SAP CAL **kont** strona zawiera jedną z dwóch opcji dla platformy Azure: 

    a. **Microsoft Azure (wersja klasyczna)** jest klasycznego modelu wdrażania i preferowana.

    b. **Microsoft Azure** jest nowy model wdrażania usługi Resource Manager.

    ![SAP CAL, kont](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Aby wdrożyć w modelu usługi Resource Manager, wybierz **Microsoft Azure**.

    ![SAP CAL, kont](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Wprowadź Azure **identyfikator subskrypcji** znajdującymi się w witrynie Azure portal. 

    ![Identyfikator subskrypcji systemu SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Aby autoryzować SAP CAL do wdrożenia w subskrypcji platformy Azure zdefiniowany, kliknij przycisk **Autoryzuj**. Na karcie przeglądarki zostanie wyświetlona następująca strona:

    ![Usługi w chmurze programu Internet Explorer Zaloguj się](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Jeśli więcej niż jeden użytkownik ma na liście, wybierz konto Microsoft, które jest połączone współadministrator subskrypcji platformy Azure, które wybrano. Na karcie przeglądarki zostanie wyświetlona następująca strona:

    ![Potwierdzenie usług w chmurze programu Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Kliknij przycisk **zaakceptować**. Autoryzacja zakończy się pomyślnie, definicja konta SAP CAL zostanie ponownie wyświetla. Po pewnym czasie wyświetli się komunikat potwierdzający, że proces autoryzacji zakończyło się pomyślnie.

1. Aby przypisać nowo utworzonego konta SAP CAL na użytkownika, wprowadź swoje **identyfikator użytkownika** w polu tekstowym z prawej strony i kliknij przycisk **Dodaj**. 

    ![Konta do skojarzenia użytkownika](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Aby skojarzyć swoje konto użytkownika, którego używasz do logowania się na SAP CAL, kliknij przycisk **przeglądu**. 

1. Aby utworzyć skojarzenie między użytkownika a nowo utworzonego konta SAP CAL, kliknij przycisk **Utwórz**.

    ![Użytkownikowi na skojarzenie konta](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Pomyślnie utworzono konto SAP CAL, który jest w stanie:

- Przy użyciu modelu wdrażania usługi Resource Manager.
- Wdrażanie systemów SAP w ramach subskrypcji platformy Azure.

> [!NOTE]
> Przed wdrożeniem rozwiązania SAP IDES na podstawie Windows i programu SQL Server, może być konieczne w celu uzyskania subskrypcji usługi SAP CAL. W przeciwnym razie rozwiązania może być wyświetlany jako **zablokowany** na stronie Przegląd.

### <a name="deploy-a-solution"></a>Wdrażanie rozwiązania
1. Po skonfigurowaniu konta SAP CAL, wybierz **rozwiązanie SAP IDES na Windows i program SQL Server** rozwiązania. Kliknij przycisk **Utwórz wystąpienie**i upewnij się, warunki użytkowania i warunki. 

1. Na **trybie podstawowym: Tworzenie wystąpienia** strony, należy:

    a. Wprowadź wystąpienie **nazwa**.

    b. Wybierz platformę Azure **Region**. Może być niezbędna jest subskrypcja SAP CAL, aby uzyskać wiele regionów systemu Azure, oferowane.

    c.  Wprowadź wzorzec **hasło** dla rozwiązania, jak pokazano:

    ![SAP CAL Basic tryb: Tworzenie wystąpienia](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Kliknij pozycję **Utwórz**. Po pewnym czasie w zależności od rozmiaru i złożoność rozwiązania (SAP CAL zapewnia oszacowanie), zostanie wyświetlony stan jako aktywne i gotowe do użycia: 

    ![Wystąpienia SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Aby znaleźć grupy zasobów i wszystkie jego obiekty, które zostały utworzone przez SAP CAL, przejdź do witryny Azure portal. Począwszy od tej samej nazwy wystąpienia, czy podano w SAP CAL można znaleźć maszyny wirtualnej.

    ![Obiekty grupy zasobów](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. W portalu SAP CAL, przejdź do wdrożonego wystąpienia, a następnie kliknij przycisk **Connect**. Zostanie wyświetlone następujące okno podręczne: 

    ![Połącz z wystąpieniem programu](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Zanim użyjesz jedną z opcji, aby nawiązać połączenie wdrożonych systemów, kliknij przycisk **Getting Started Guide**. Dokumentacja nazwy użytkowników dla wszystkich metod łączności. Hasło główne, zdefiniowanych na początku procesu wdrażania można ustawić hasła dla tych użytkowników. W dokumentacji innym użytkownikom bardziej funkcjonalnego są wyświetlane z haseł, których można użyć do logowania się na wdrożonym systemie.

    ![Dokumentacja usługi SAP-Zapraszamy!](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

W ciągu kilku godzin dobrej kondycji systemu SAP IDES jest wdrażana na platformie Azure.

Jeśli zakupiono subskrypcję SAP CAL, SAP w pełni obsługuje wdrożenia za pośrednictwem SAP CAL na platformie Azure. Kolejka pomocy technicznej jest BC-VCM-licencji CAL.

