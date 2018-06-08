---
title: Zarządzanie formuły w usłudze Azure DevTest Labs do tworzenia maszyn wirtualnych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak aktualizacja i usuwanie formuły Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 40c30958c5e0e24f2b35475848c1828df4a40d0c
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "33787612"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Zarządzanie formuły Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

W tym artykule przedstawiono procedurę tworzenia formuły z podstawowej (obrazu niestandardowego obrazu z witryny Marketplace i formułą) lub istniejącej maszyny Wirtualnej. W tym artykule również przeprowadzi Cię przez zarządzanie istniejące formuły.

## <a name="create-a-formula"></a>Utwórz formułę
Każda osoba mająca DevTest Labs *użytkowników* uprawnień jest możliwość tworzenia maszyn wirtualnych przy użyciu formuły jako podstawy. Istnieją dwa sposoby tworzenia formuły: 

* Z podstawowej — należy użyć do zdefiniowania wszystkich parametrów formuły.
* Z istniejącego laboratorium maszyny Wirtualnej — używany, gdy chcesz utworzyć formuły na podstawie ustawień istniejącej maszyny Wirtualnej.

Aby uzyskać więcej informacji na temat dodawania użytkowników i uprawnień, zobacz [Dodaj właścicieli i użytkowników w usłudze Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Utwórz formułę z podstawowej
Poniższe kroki ułatwiają tworzenie formuły z niestandardowego obrazu, obrazu z witryny Marketplace lub formułą.

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.

3. Z listy labs wybierz żądany laboratorium.  

4. W bloku laboratorium, wybierz **formuły (podstaw wielokrotnego użytku)**.
   
    ![Formuły menu](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Na **formuły** bloku, wybierz opcję **+ Dodaj**.
   
    ![Dodawanie formuły](./media/devtest-lab-create-formulas/add-formula.png)

6. Na **wybierz podstawowej** bloku, wybierz base (obrazu niestandardowego obrazu z witryny Marketplace i formuły), z którego chcesz utworzyć formułę.
   
    ![Lista podstawowa](./media/devtest-lab-create-formulas/base-list.png)

7. Na **utworzyć formuły** bloku, określ następujące wartości:
   
    * **Nazwa formuły** — wprowadź nazwę dla formuły. Ta wartość jest wyświetlana na liście podstawowej obrazów, podczas tworzenia maszyny Wirtualnej. Nazwa została sprawdzona, wpisz ją, a jeśli jest on nieprawidłowy komunikat wskazuje wymagania dotyczące prawidłową nazwę.
    * **Opis elementu** — wpisz zrozumiały opis dla formuły. Ta wartość jest dostępna z menu kontekstowego formuły, podczas tworzenia maszyny Wirtualnej.
    * **Nazwa użytkownika** — wprowadź nazwę użytkownika, któremu udzielono uprawnień administratora.
    * **Hasło** — wprowadź - lub wybierz z listy rozwijanej - wartość, która jest skojarzona z hasło, którego chcesz używać dla określonego użytkownika. Aby uzyskać więcej informacji na temat kluczy tajnych, zobacz [Azure DevTest Labs: tajne magazynie osobistym](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store/).
    * **Typ dysku maszyny wirtualnej** — określ albo dysk twardy (dysk twardy) lub dysków SSD (SSD), aby wskazać, który typ dysku magazynu jest dozwolony dla maszyn wirtualnych udostępniane przy użyciu tego obrazu podstawowego.
    * ** Maszyna wirtualna rozmiar ** — wybierz jedną z wstępnie zdefiniowane elementy, które określić rdzeni procesora, rozmiar pamięci RAM i rozmiar dysku twardego maszyny Wirtualnej, aby utworzyć. 
    * **Artefakty** — wybierz tę opcję, aby otworzyć **dodać artefakty** bloku, w którym można wybrać i skonfigurować artefaktów, które chcesz dodać do obrazu podstawowego. Aby uzyskać więcej informacji na temat artefaktów, zobacz [Tworzenie niestandardowych artefaktów dla maszyny wirtualnej Azure DevTest Labs](devtest-lab-artifact-author.md).
    * **Zaawansowane ustawienia** — wybierz tę opcję, aby otworzyć **zaawansowane** bloku, w którym można skonfigurować następujące ustawienia:
        * **Sieć wirtualna** — określ odpowiednią sieć wirtualną.
        * **Podsieci** — Określ żądany podsieci.    
        * **Adres IP** — Określ, czy adresy Public, Private lub udostępnione IP. Aby uzyskać więcej informacji na temat udostępnionych adresów IP, zobacz [omówienie udostępnionych adresów IP w usłudze Azure DevTest Labs](./devtest-lab-shared-ip.md).
        * **Przydziel tej maszynie claimable** — co maszyna "claimable" oznacza, że go nie zostaną przypisane prawa własności w momencie tworzenia obiektu. Zamiast tego laboratorium użytkownicy będą mogli do przejęcia na własność ("roszczenie") komputera w laboratorium należy utworzyć bloku.     
    * **Obraz** -wyświetlana nazwa obrazu podstawowego wybrane w poprzednim bloku. 
     
       ![Utwórz formułę](./media/devtest-lab-create-formulas/create-formula.png)

8. Wybierz **Utwórz** można utworzyć formuły.

9. Po utworzeniu formuła wyświetla na liście na **formuły** bloku.

### <a name="create-a-formula-from-a-vm"></a>Utwórz formułę z maszyny Wirtualnej
Poniższe kroki ułatwiają tworzenie formułę opartą na istniejącej maszyny Wirtualnej. 

> [!NOTE]
> Aby utworzyć formuły z maszyny Wirtualnej, maszyna wirtualna musi być utworzony po 30 marca 2016 r. 
> 
> 

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.
3. Z listy labs wybierz żądany laboratorium.  
4. W laboratorium **omówienie** bloku, wybierz maszynę Wirtualną, z którego chcesz utworzyć formuły.
   
    ![Maszyny wirtualne laboratoria](./media/devtest-lab-create-formulas/my-vms.png)
5. W bloku maszyny Wirtualnej, wybierz **utworzyć formuły (base wielokrotnego użytku)**.
   
    ![Utwórz formułę](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Na **utworzyć formuły** bloku, wprowadź **nazwa** i **opis** dla nowej formuły.
   
    ![Tworzenie formuły bloku](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Wybierz **OK** można utworzyć formuły.

## <a name="modify-a-formula"></a>Zmodyfikuj formułę
Aby zmodyfikować formuły, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.
3. Z listy labs wybierz żądany laboratorium.  
4. W bloku laboratorium, wybierz **formuły (podstaw wielokrotnego użytku)**.
   
    ![Formuły menu](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na **formuły laboratorium** bloku, wybierz ją, który chcesz zmodyfikować.
6. Na **zaktualizować formułę** bloku, wprowadź żądane zmiany, a następnie wybierz **aktualizacji**.

## <a name="delete-a-formula"></a>Usuwanie formuły
Aby usunąć formuły, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.
3. Z listy labs wybierz żądany laboratorium.  
4. W środowisku laboratoryjnym **ustawienia** bloku, wybierz opcję **formuły**.
   
    ![Formuły menu](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na **formuły laboratorium** bloku, wybierz przycisk wielokropka z prawej strony formułę do usunięcia.
   
    ![Formuły menu](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. W menu kontekstowym formuły, wybierz **usunąć**.
   
    ![Menu kontekstowe formuły](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Wybierz **tak** do okna dialogowego potwierdzenia usunięcia.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Wpisy na blogu pokrewne
* [Niestandardowe obrazy lub formuł?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu formułę do użycia podczas tworzenia maszyny Wirtualnej, następnym krokiem jest [Dodaj Maszynę wirtualną do laboratorium](devtest-lab-add-vm.md).

