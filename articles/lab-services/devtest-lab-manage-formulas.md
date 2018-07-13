---
title: Zarządzanie formułami w usłudze Azure DevTest Labs, aby tworzyć maszyny wirtualne | Dokumentacja firmy Microsoft
description: Dowiedz się, jak aktualizacja i usuwanie formuł w usłudze Azure DevTest Labs
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
ms.openlocfilehash: b7a68f545f60829e5da83f0734c57a4d210cb843
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001485"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Zarządzanie formułami Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

W tym artykule pokazano, jak utworzyć formułę z podstawowej (niestandardowych obrazów, obraz z witryny Marketplace lub innymi formułami) lub istniejącej maszyny Wirtualnej. W tym artykule również przeprowadzi Cię przez zarządzanie istniejące formuły.

## <a name="create-a-formula"></a>Tworzenie formuły
Każda osoba mająca DevTest Labs *użytkowników* uprawnień jest w stanie Tworzenie maszyn wirtualnych przy użyciu formuły jako podstawy. Istnieją dwa sposoby tworzenia formuły: 

* Od podstawy — należy użyć do zdefiniowania parametrów formuły.
* Z istniejącego laboratorium maszyny Wirtualnej — Użyj utworzyć formułę, zgodnie z ustawieniami istniejącej maszyny Wirtualnej.

Aby uzyskać więcej informacji na temat dodawania użytkowników i uprawnień, zobacz [Dodawanie właścicieli i użytkowników w usłudze Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Tworzenie formuły na podstawie podstawowej
Poniższe kroki ułatwiają tworzenie formuły z niestandardowego obrazu, obrazu z witryny Marketplace lub innymi formułami.

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.

3. Z listy labs wybierz żądane laboratorium.  

4. W bloku laboratorium wybierz **formuły (zasady wielokrotnego użytku)**.
   
    ![Menu formuły](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Na **formuły** bloku wybierz **+ Dodaj**.
   
    ![Dodawanie formuły](./media/devtest-lab-create-formulas/add-formula.png)

6. Na **wybierz podstawowej** bloku wybierz pozycję podstawowy (niestandardowego obrazu, obrazu z witryny Marketplace lub formuły), z którego chcesz utworzyć formułę.
   
    ![Podstawowa lista](./media/devtest-lab-create-formulas/base-list.png)

7. Na **tworzona jest formuła** bloku określ następujące wartości:
   
    * **Nazwa formuły** — wprowadź nazwę swojej formule. Ta wartość jest wyświetlana na liście obrazy podstawowe, podczas tworzenia maszyny Wirtualnej. Nazwa jest weryfikowana, wpisz ją, a jeśli to nie jest prawidłowa, komunikat wskazuje, wymagania dotyczące prawidłową nazwę.
    * **Opis** — Podaj znaczący opis formuły. Ta wartość jest dostępne z menu kontekstowego w formule podczas tworzenia maszyny Wirtualnej.
    * **Nazwa użytkownika** — wprowadź nazwę użytkownika, któremu udzielono uprawnień administratora.
    * **Hasło** — wprowadź — lub wybierz z listy rozwijanej - wartość, która jest skojarzona z klucz tajny (hasło), którego chcesz użyć dla określonego użytkownika. Aby dowiedzieć się więcej na temat zapisywania wpisów tajnych w magazynie kluczy i korzystanie z nich podczas tworzenia zasobów laboratorium, zobacz [Store wpisów tajnych w usłudze Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).
    * **Typ dysku maszyny wirtualnej** — określ albo HDD (dysk twardy) lub dysk SSD (SSD), aby wskazać typ dysku magazynu, które jest dozwolone w przypadku maszyn wirtualnych aprowizowane za pomocą tego obrazu podstawowego.
    * ** Maszyna wirtualna rozmiar ** — wybierz jedną z wstępnie zdefiniowane elementy, które Określ rdzeni procesora, pamięci RAM i rozmiar dysku twardego maszyny wirtualnej, aby utworzyć. 
    * **Artefakty** — wybierz, aby otworzyć **Dodaj artefakty** bloku, w którym można wybrać i skonfigurować artefaktów, które chcesz dodać do obrazu podstawowego. Aby uzyskać więcej informacji na temat artefaktów, zobacz [Tworzenie niestandardowych artefaktów dla maszyny wirtualnej usługi Azure DevTest Labs](devtest-lab-artifact-author.md).
    * **Zaawansowane ustawienia** — wybierz, aby otworzyć **zaawansowane** bloku, w którym można skonfigurować następujące ustawienia:
        * **Sieć wirtualna** -Określ żądany sieci wirtualnej.
        * **Podsieci** -określ odpowiednią podsieć.    
        * **Konfiguracja adresu IP** — Określ, czy adresy prywatnej, publicznej lub udostępnione adresów IP. Aby uzyskać więcej informacji o współużytkowanych adresach IP, zobacz [omówienie udostępnionych adresów IP w usłudze Azure DevTest Labs](./devtest-lab-shared-ip.md).
        * **Przydziel tej maszynie przejęcia** -wprowadzania maszynę "przejęcia" oznacza, że go nie można przypisać własności w czasie tworzenia. Zamiast tego laboratorium użytkownicy będą mogli przejęcie na własność ("oświadczenia") maszyny w laboratorium bloku.     
    * **Obraz** -wyświetlana nazwa obrazu podstawowego wybranej w poprzednim bloku. 
     
       ![Tworzenie formuły](./media/devtest-lab-create-formulas/create-formula.png)

8. Wybierz **Utwórz** utworzyć formułę.

9. Formuła został utworzony, jest wyświetlana na liście na **formuły** bloku.

### <a name="create-a-formula-from-a-vm"></a>Tworzenie formuły z maszyny Wirtualnej
Poniższe kroki ułatwiają tworzenie formuły na podstawie istniejącej maszyny wirtualnej. 

> [!NOTE]
> Aby utworzyć formułę z maszyny Wirtualnej, maszyny Wirtualnej musi być utworzony po 30 marca 2016 r. 
> 
> 

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
3. Z listy labs wybierz żądane laboratorium.  
4. W laboratorium **Przegląd** bloku wybierz maszynę Wirtualną, z którego chcesz utworzyć formułę.
   
    ![Maszyny wirtualne laboratoria](./media/devtest-lab-create-formulas/my-vms.png)
5. W bloku maszyny Wirtualnej wybierz **tworzona jest formuła (base wielokrotnego użytku)**.
   
    ![Tworzenie formuły](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Na **tworzona jest formuła** bloku wprowadź **nazwa** i **opis** dla nowej formuły.
   
    ![Tworzenie formuły bloku](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Wybierz **OK** utworzyć formułę.

## <a name="modify-a-formula"></a>Modyfikowanie formuły
Aby zmodyfikować formułę, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
3. Z listy labs wybierz żądane laboratorium.  
4. W bloku laboratorium wybierz **formuły (zasady wielokrotnego użytku)**.
   
    ![Menu formuły](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na **formuły laboratorium** bloku wybierz formułę, którą chcesz zmodyfikować.
6. Na **Aktualizovat vzorec** bloku, wprowadź żądane zmiany, a następnie wybierz pozycję **aktualizacji**.

## <a name="delete-a-formula"></a>Usuwanie formuły
Aby usunąć formułę, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
3. Z listy labs wybierz żądane laboratorium.  
4. W środowisku laboratoryjnym **ustawienia** bloku wybierz **formuły**.
   
    ![Menu formuły](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na **formuły laboratorium** bloku, wybierz przycisk wielokropka z prawej strony formułę, którą chcesz usunąć.
   
    ![Menu formuły](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. W menu kontekstowym tej formuły, wybierz **Usuń**.
   
    ![Menu kontekstowe formuły](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Wybierz **tak** w oknie dialogowym potwierdzenia usunięcia.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Wpisy w blogu pokrewne
* [Obrazy niestandardowe lub formuł?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu formuły do użycia podczas tworzenia maszyny Wirtualnej, następnym krokiem jest [Dodaj Maszynę wirtualną do środowiska laboratoryjnego](devtest-lab-add-vm.md).

