---
title: Wdrożenie SAP IDES EHP7 SP3 dla SAP ERP 6.0 na platformie Azure | Dokumentacja firmy Microsoft
description: Wdrożenie SAP IDES EHP7 SP3 dla SAP ERP 6.0 na platformie Azure
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
ms.openlocfilehash: b1895fb1910c5f30cbcff1c16ca66057d31a580b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656486"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Wdrożenie SAP IDES EHP7 SP3 dla SAP ERP 6.0 na platformie Azure
W tym artykule opisano sposób wdrażania systemu SAP IDES uruchomiony z programu SQL Server i systemu operacyjnego Windows na platformie Azure za pomocą biblioteki urządzenia chmury SAP (SAP CAL) 3.0. Zrzuty ekranu pokazują krok po kroku procesu. Aby wdrożyć inne rozwiązanie, wykonaj te same czynności.

Aby rozpocząć SAP CAL, przejdź do [biblioteki urządzenia chmury SAP](https://cal.sap.com/) witryny sieci Web. SAP ma również blog o nowej [SAP chmury urządzenia biblioteki 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
Począwszy od 29 maja 2017 modelu wdrażania usługi Azure Resource Manager oprócz preferowane mniej klasycznym modelu wdrażania służy do wdrażania SAP CAL. Firma Microsoft zaleca używanie nowego modelu wdrażania usługi Resource Manager i pominąć klasycznym modelu wdrażania.

Jeśli utworzono już konta SAP CAL, które używają modelu klasycznego, *należy utworzyć nowe konto SAP CAL*. To konto należy wdrażać wyłącznie na platformie Azure przy użyciu modelu Resource Manager.

Po zalogowaniu się do SAP CAL, pierwsza strona zwykle prowadzi do **rozwiązań** strony. Rozwiązania oferowane na SAP CAL stopniowo coraz, więc konieczne może być dość nieco przewiń do rozwiązania, które mają. Wyróżnione rozwiązania IDES SAP opartych na systemie Windows, które jest dostępne wyłącznie w systemie Azure przedstawiono proces wdrażania:

![Rozwiązania CAL SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Tworzenie konta w SAP CAL
1. Zaloguj się do SAP CAL po raz pierwszy, należy użyć programu SAP S-użytkownika lub inny użytkownik zarejestrowany w usłudze SAP. Następnie zdefiniuj SAP CAL konta, które jest używane przez SAP CAL do wdrażania urządzeń na platformie Azure. W definicji konta musisz:

    a. Wybierz model wdrażania na platformie Azure (Resource Manager lub classic).

    b. Wprowadź subskrypcji platformy Azure. Konto SAP CAL można przypisać do tylko jednej subskrypcji. Jeśli potrzebujesz więcej niż jedną subskrypcję, musisz utworzyć nowe konto SAP CAL.
    
    c. Nadaj uprawnienia SAP CAL do wdrożenia w ramach subskrypcji platformy Azure.

    > [!NOTE]
    W następnych krokach przedstawiono sposób tworzenia konta SAP CAL dla wdrożenia usługi Resource Manager. Jeśli masz już konto SAP CAL połączonego z klasycznym modelu wdrożenia, możesz *muszą* wykonać następujące kroki, aby utworzyć nowe konto SAP CAL. Nowe konto SAP CAL musi wdrożyć w modelu usługi Resource Manager.

2. Aby utworzyć nowe konto SAP CAL, **kont** strony zawiera dwa wybory dla platformy Azure: 

    a. **Microsoft Azure (klasyczne)** jest klasycznym modelu wdrażania i nie jest preferowany.

    b. **Microsoft Azure** jest nowy model wdrażania Menedżera zasobów.

    ![Konta CAL SAP](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Aby wdrożyć w modelu usługi Resource Manager, wybierz **Microsoft Azure**.

    ![Konta CAL SAP](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

3. Wprowadź Azure **identyfikator subskrypcji** znajdującymi się w portalu Azure. 

    ![Identyfikator subskrypcji CAL SAP](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

4. Aby autoryzować CAL SAP do wdrożenia w subskrypcji platformy Azure, zdefiniowaną, kliknij przycisk **autoryzacji**. Na karcie przeglądarki jest wyświetlana następująca strona:

    ![Logowania usług w chmurze programu Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

5. Jeśli jest wymieniona więcej niż jednego użytkownika, wybierz konto Microsoft, która jest połączona coadministrator subskrypcji Azure, które wybrano. Na karcie przeglądarki jest wyświetlana następująca strona:

    ![Potwierdzenie usług w chmurze programu Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

6. Kliknij przycisk **zaakceptować**. Jeśli autoryzacja zakończy się pomyślnie, SAP CAL definicji konta zostaną wyświetlone ponownie. Po pewnym czasie wyświetli się komunikat potwierdzający, pomyślnego procesu autoryzacji.

7. Aby przypisać nowo utworzonych kont SAP CAL do użytkownika, wprowadź użytkownika **identyfikator użytkownika** w polu tekstowym w prawo, a następnie kliknij **Dodaj**. 

    ![Konta do skojarzenia użytkownika](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

8. Aby skojarzyć konto użytkownika, którego używasz do logowania się na SAP CAL, kliknij przycisk **przeglądu**. 

9. Aby utworzyć skojarzenie użytkownika z nowo utworzonego konta SAP CAL, kliknij przycisk **Utwórz**.

    ![Użytkownikowi na skojarzenie konta](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Pomyślnie utworzono konto SAP CAL, które jest w stanie:

- Użyj modelu wdrażania usługi Resource Manager.
- Wdrażanie systemów SAP do Twojej subskrypcji platformy Azure.

> [!NOTE]
Przed wdrożeniem rozwiązania SAP IDES, w oparciu o system Windows i program SQL Server może być konieczne w celu uzyskania subskrypcji SAP CAL. W przeciwnym razie rozwiązania może być wyświetlany jako **zablokowany** na stronie przeglądu.

### <a name="deploy-a-solution"></a>Wdrażanie rozwiązania
1. Po skonfigurowaniu konta SAP CAL, wybierz **rozwiązania IDES SAP w systemach Windows i program SQL Server** rozwiązania. Kliknij przycisk **Utwórz wystąpienie**i Potwierdź warunki użytkowania i warunki. 

2. Na **podstawowe tryb: Utwórz wystąpienie** strony, musisz:

    a. Wprowadź wystąpienia **nazwa**.

    b. Wybierz platformy Azure **Region**. Może być niezbędna jest subskrypcja SAP CAL, można uzyskać w wielu regionach platformy Azure oferowane.

    c.  Wprowadź wzorzec **hasło** dla rozwiązania, jak pokazano:

    ![Tryb CAL Basic SAP: Utworzenie wystąpienia](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

3. Kliknij przycisk **Utwórz**. Po pewnym czasie w zależności od rozmiaru i złożoności rozwiązania (SAP CAL zapewnia szacunkową), zostanie wyświetlony stan jako aktywne i gotowe do użycia: 

    ![Wystąpienia CAL SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

4. Aby znaleźć grupy zasobów i wszystkie jego obiekty, które zostały utworzone przez SAP CAL, przejdź do portalu Azure. Począwszy od tej samej nazwy wystąpienia, które podano w SAP CAL można znaleźć maszyny wirtualnej.

    ![Obiekty grupy zasobów](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

5. W portalu SAP CAL, przejdź do wdrożonej wystąpienia, a następnie kliknij przycisk **Connect**. Zostanie wyświetlone następujące okno podręczne: 

    ![Połącz się z wystąpieniem](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

6. Przed użyciem jedną z opcji łączenia się z systemami wdrożone, kliknij przycisk **Getting Started Guide**. Dokumentację nazwy użytkowników, dla każdej z metod łączności. Hasła dla tych użytkowników ustawiono hasło główne, zdefiniowanych na początku procesu wdrażania. W dokumentacji innym użytkownikom bardziej funkcjonalny są wyświetlane z haseł, których można używać do logowania się na wdrożonym systemie.

    ![SAP dokumentacji-Zapraszamy!](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

W ciągu kilku godzin dobrej kondycji systemu SAP IDES jest wdrażana na platformie Azure.

Jeśli zakupiono subskrypcję SAP CAL SAP w pełni obsługuje wdrożeniami SAP CAL na platformie Azure. Kolejka pomocy technicznej jest BC-VCM-licencji dostępu klienta.

