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
ms.openlocfilehash: d8f2ae64e2f8e694de5a7cf5aa9049e63998dca0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60562596"
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

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.

3. Z listy labs wybierz żądane laboratorium.  

4. Na stronie laboratorium wybierz **formuły (zasady wielokrotnego użytku)** .
   
    ![Menu formuły](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Na **formuły** wybierz opcję **+ Dodaj**.
   
    ![Dodawanie formuły](./media/devtest-lab-create-formulas/add-formula.png)

6. Na **wybierz podstawowej** Wybierz podstawowy (niestandardowego obrazu, obrazu z witryny Marketplace lub formuły), z którego chcesz utworzyć formułę.
   
    ![Podstawowa lista](./media/devtest-lab-create-formulas/base-list.png)

7. Na **podstawowych ustawień** karcie **tworzona jest formuła** określ następujące wartości:
   
    * **Nazwa formuły** — wprowadź nazwę swojej formule. Ta wartość jest wyświetlana na liście obrazy podstawowe, podczas tworzenia maszyny Wirtualnej. Nazwa jest weryfikowana, wpisz ją, a jeśli to nie jest prawidłowa, komunikat wskazuje, wymagania dotyczące prawidłową nazwę.
    * **Nazwa użytkownika** — wprowadź nazwę użytkownika, któremu udzielono uprawnień administratora.
    * **Hasło** — wprowadź — lub wybierz z listy rozwijanej - wartość, która jest skojarzona z klucz tajny (hasło), którego chcesz użyć dla określonego użytkownika. Aby dowiedzieć się więcej na temat zapisywania wpisów tajnych w magazynie kluczy i korzystanie z nich podczas tworzenia zasobów laboratorium, zobacz [Store wpisów tajnych w usłudze Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).
    * **Rozmiar maszyny Wirtualnej** — wybierz tę opcję **zmienić rozmiar** można zmienić rozmiar maszyny Wirtualnej. 
    * **Artefakty** — wybierz tę opcję **Dodawanie lub usuwanie artefaktów** strony, w którym można wybrać i skonfigurować artefaktów, które chcesz dodać do obrazu podstawowego. Aby uzyskać więcej informacji na temat artefaktów, zobacz [Tworzenie niestandardowych artefaktów dla maszyny wirtualnej usługi Azure DevTest Labs](devtest-lab-artifact-author.md).
8. Przełącz się do **Zaawansowane ustawienia** kartę, a następnie określ następujące wartości:
    - **Sieć wirtualna** — Aby zmienić sieć wirtualną, wybrać **zmienić sieci wirtualnej**. 
    - **Podsieci** — Aby zmienić podsieć, wybierz **zmienić podsieć**. 
    - **Konfiguracja adresu IP** — Określ, czy adresy prywatnej, publicznej lub udostępnione adresów IP. Aby uzyskać więcej informacji o współużytkowanych adresach IP, zobacz [omówienie udostępnionych adresów IP w usłudze Azure DevTest Labs](./devtest-lab-shared-ip.md).
    - **Data i godzina wygaśnięcia** — Podaj datę wygaśnięcia i godzina dla maszyny Wirtualnej tak, aby maszyna wirtualna zostanie automatycznie usunięty. 
    - **Przydziel tej maszynie przejęcia** -wprowadzania maszynę "przejęcia" oznacza, że go nie można przypisać własności w czasie tworzenia. Zamiast tego użytkownicy laboratorium będzie przejęcie na własność ("oświadczenia") maszyny w laboratorium strony.     
    - **Liczba wystąpień przejęcia** — Określ, ile wystąpień przejęcia, którą chcesz utworzyć. 
8. Wybierz **przesyłania** utworzyć formułę.

9. Formuła został utworzony, jest wyświetlana na liście na **formuły** strony.

### <a name="create-a-formula-from-a-vm"></a>Tworzenie formuły z maszyny Wirtualnej
Poniższe kroki ułatwiają tworzenie formuły na podstawie istniejącej maszyny wirtualnej. 

> [!NOTE]
> Aby utworzyć formułę z maszyny Wirtualnej, maszyny Wirtualnej musi być utworzony po 30 marca 2016 r. 
> 
> 

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
3. Z listy labs wybierz żądane laboratorium.  
4. W laboratorium **Przegląd** wybierz maszynę Wirtualną, z którego chcesz utworzyć formułę.
   
    ![Maszyny wirtualne laboratoria](./media/devtest-lab-create-formulas/my-vms.png)
5. Na stronie maszyny wybierz **tworzona jest formuła (base wielokrotnego użytku)** .
   
    ![Tworzenie formuły](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Na **tworzona jest formuła** wpisz **nazwa** i **opis** dla nowej formuły.
   
    ![Tworzenie formuły strony](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Wybierz **OK** utworzyć formułę.

## <a name="modify-a-formula"></a>Modyfikowanie formuły
Aby zmodyfikować formułę, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
3. Z listy labs wybierz żądane laboratorium.  
4. Na stronie laboratorium wybierz **formuły (zasady wielokrotnego użytku)** .
   
    ![Menu formuły](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na **formuły laboratorium** wybierz formułę, którą chcesz zmodyfikować.
6. Na **Aktualizovat vzorec** strony, wprowadź żądane zmiany, a następnie wybierz **aktualizacji**.

## <a name="delete-a-formula"></a>Usuwanie formuły
Aby usunąć formułę, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
3. Z listy labs wybierz żądane laboratorium.  
4. W środowisku laboratoryjnym **ustawienia** wybierz opcję **formuły**.
   
    ![Menu formuły](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na **formuły laboratorium** stronie, wybierz przycisk wielokropka z prawej strony formułę, którą chcesz usunąć.
   
    ![Menu formuły](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. W menu kontekstowym tej formuły, wybierz **Usuń**.
   
    ![Menu kontekstowe formuły](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Wybierz **tak** w oknie dialogowym potwierdzenia usunięcia.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Wpisy w blogu pokrewne
* [Obrazy niestandardowe lub formuł?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu formuły do użycia podczas tworzenia maszyny Wirtualnej, następnym krokiem jest [Dodaj Maszynę wirtualną do środowiska laboratoryjnego](devtest-lab-add-vm.md).

