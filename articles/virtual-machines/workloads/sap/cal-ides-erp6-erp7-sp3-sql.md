---
title: Wdrażanie dodatku SAP IDES EHP7 z dodatku SP3 dla systemu SAP ERP 6.0 na platformie Azure | Dokumenty firmy Microsoft
description: Wdrażanie dodatku SAP IDES EHP7 z dodatku SP3 dla systemu SAP ERP 6.0 na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 3efd92226b7c69590f3960458ffec49b63b8364f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616704"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Wdrażanie dodatku SAP IDES EHP7 z dodatku SP3 dla systemu SAP ERP 6.0 na platformie Azure
W tym artykule opisano sposób wdrażania systemu SAP IDES działającego z programem SQL Server i systemu operacyjnego Windows na platformie Azure za pośrednictwem biblioteki SAP Cloud Appliance Library (SAP CAL) 3.0. Zrzuty ekranu pokazują proces krok po kroku. Aby wdrożyć inne rozwiązanie, wykonaj te same kroki.

Aby rozpocząć od biblioteki CAL SAP, przejdź do [witryny SAP Cloud Appliance Library.](https://cal.sap.com/) SAP ma również bloga o nowej [bibliotece SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
> Od 29 maja 2017 r. można użyć modelu wdrażania usługi Azure Resource Manager oprócz mniej preferowanego modelu wdrażania klasycznego w celu wdrożenia narzędzia SAP CAL. Zaleca się użycie nowego modelu wdrażania Menedżera zasobów i pominięcie klasycznego modelu wdrażania.

Jeśli utworzono już konto SAP CAL, które korzysta z modelu klasycznego, *należy utworzyć inne konto SAP CAL*. To konto musi być wdrażane wyłącznie na platformie Azure przy użyciu modelu Usługi Resource Manager.

Po zalogowaniu się do środowiska SAP CAL pierwsza strona zwykle prowadzi do strony **Rozwiązania.** Rozwiązania oferowane w sap CAL stale rosną, więc może być konieczne przewinięcie sporo, aby znaleźć rozwiązanie, które chcesz. Wyróżnione rozwiązanie SAP IDES oparte na systemie Windows, które jest dostępne wyłącznie na platformie Azure, pokazuje proces wdrażania:

![Rozwiązania SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Tworzenie konta w liczeczce SAP
1. Aby zalogować się do środowiska SAP CAL po raz pierwszy, użyj użytkownika SAP S lub innego użytkownika zarejestrowanego w sap. Następnie zdefiniuj konto SAP CAL, które jest używane przez środowisko SAP CAL do wdrażania urządzeń na platformie Azure. W definicji konta musisz:

    a. Wybierz model wdrażania na platformie Azure (Menedżer zasobów lub klasyczny).

    b. Wprowadź subskrypcję platformy Azure. Konto SAP CAL można przypisać tylko do jednej subskrypcji. Jeśli potrzebujesz więcej niż jednej subskrypcji, musisz utworzyć inne konto SAP CAL.
    
    d. Nadaj uprawnieniaemu sap CAL do wdrożenia w ramach subskrypcji platformy Azure.

   > [!NOTE]
   >  W następnych krokach pokazano, jak utworzyć konto SAP CAL dla wdrożeń Menedżera zasobów. Jeśli masz już konto SAP CAL, które jest połączone z klasycznym modelem wdrażania, *musisz* wykonać następujące kroki, aby utworzyć nowe konto SAP CAL. Nowe konto SAP CAL musi być wdrożone w modelu Menedżera zasobów.

1. Aby utworzyć nowe konto SAP CAL, na stronie **Konta** przedstawiono dwie opcje dla platformy Azure: 

    a. **Microsoft Azure (klasyczny)** jest klasycznym modelem wdrażania i nie jest już preferowany.

    b. **Microsoft Azure** to nowy model wdrażania usługi Resource Manager.

    ![Konta SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Aby wdrożyć w modelu Menedżera zasobów, wybierz pozycję **Microsoft Azure**.

    ![Konta SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Wprowadź **identyfikator subskrypcji** platformy Azure, który można znaleźć w witrynie Azure portal. 

    ![Identyfikator subskrypcji SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Aby autoryzować zdefiniowaną subskrypcję SAP CAL w celu wdrożenia w zdefiniowanej subskrypcji platformy Azure, kliknij przycisk **Autoryzuj**. Na karcie przeglądarki pojawi się następująca strona:

    ![Logowanie się do usług w chmurze programu Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Jeśli na liście znajduje się więcej niż jeden użytkownik, wybierz konto Microsoft, które jest połączone jako współadministrator wybranej subskrypcji platformy Azure. Na karcie przeglądarki pojawi się następująca strona:

    ![Potwierdzenie usług w chmurze programu Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Kliknij przycisk **Zaakceptuj**. Jeśli autoryzacja zakończy się pomyślnie, definicja konta SAP CAL zostanie wyświetlona ponownie. Po krótkim czasie komunikat potwierdza, że proces autoryzacji zakończył się pomyślnie.

1. Aby przypisać nowo utworzone konto SAP CAL do użytkownika, wprowadź jego **identyfikator użytkownika** w polu tekstowym po prawej stronie i kliknij przycisk **Dodaj**. 

    ![Powiązanie konta z użytkownikiem](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Aby skojarzyć swoje konto z użytkownikiem używanym do logowania się do środowiska SAP CAL, kliknij przycisk **Przejrzyj**. 

1. Aby utworzyć skojarzenie między użytkownikiem a nowo utworzonym kontem SAP CAL, kliknij przycisk **Utwórz**.

    ![Skojarzenie między użytkownikiem a kontem](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Pomyślnie utworzono konto SAP CAL, które może:

- Użyj modelu wdrażania usługi Resource Manager.
- Wdrażanie systemów SAP w ramach subskrypcji platformy Azure.

> [!NOTE]
> Przed wdrożeniem rozwiązania SAP IDES opartego na systemach Windows i SQL Server może być konieczne utworzenie subskrypcji licencji SAP CAL. W przeciwnym razie rozwiązanie może pojawić się jako **zablokowane** na stronie przeglądu.

### <a name="deploy-a-solution"></a>Wdrażanie rozwiązania
1. Po skonfigurowaniu konta LICENCJI SAP wybierz **rozwiązanie SAP IDES w systemie Windows i SQL Server.** Kliknij **pozycję Utwórz wystąpienie**i potwierdź warunki użytkowania i warunków. 

1. Na stronie **Tryb podstawowy: Tworzenie wystąpienia** należy:

    a. Wprowadź **nazwę**wystąpienia .

    b. Wybierz **region**platformy Azure . Może być potrzebna subskrypcja SAP CAL, aby uzyskać wiele regionów platformy Azure oferowanych.

    d.  Wprowadź **hasło** główne rozwiązania, jak pokazano na rysunku:

    ![Tryb podstawowy SAP CAL: Utwórz wystąpienie](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Kliknij przycisk **Utwórz**. Po pewnym czasie, w zależności od rozmiaru i złożoności rozwiązania (środowisko CAL SAP zapewnia oszacowanie), stan jest wyświetlany jako aktywny i gotowy do użycia: 

    ![Wystąpienia środowiska SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Aby znaleźć grupę zasobów i wszystkie jej obiekty, które zostały utworzone przez narzędzie CAL SAP, przejdź do witryny Azure portal. Maszynę wirtualną można znaleźć, zaczynając od tej samej nazwy wystąpienia, która została podana w pamięci CAL SAP.

    ![Obiekty grupy zasobów](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. W portalu SAP CAL przejdź do wdrożonych wystąpień i kliknij przycisk **Połącz**. Zostanie wyświetlenie następującego okna podręcznego: 

    ![Łączenie się z wystąpieniem](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Aby można było użyć jednej z opcji do łączenia się z wdrożonymi systemami, kliknij przycisk **Wprowadzenie**. Dokumentacja nazywa użytkowników dla każdej z metod łączności. Hasła dla tych użytkowników są ustawione na hasło główne zdefiniowane na początku procesu wdrażania. W dokumentacji inni użytkownicy bardziej funkcjonalni są wymienieni z hasłami, których można użyć do zalogowania się do wdrożonego systemu.

    ![Dokumentacja powitalna SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

W ciągu kilku godzin na platformie Azure zostanie wdrożony zdrowy system SAP IDES.

Jeśli zakupiono subskrypcję sap cal, SAP w pełni obsługuje wdrożenia za pośrednictwem narzędzia SAP CAL na platformie Azure. Kolejka pomocy technicznej to BC-VCM-CAL.

