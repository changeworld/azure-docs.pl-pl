---
title: Wyświetl zmiany zawartości plików za pomocą usługi Azure Automation
description: Aby wyświetlić zawartość pliku, które uległy zmianie, należy użyć funkcji zmiana zawartości pliku śledzenia zmian.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 07/03/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dae0ea3e564a3dfafcd7f072f9b0d8854df17ed7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61304344"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Wyświetl zawartość pliku, który jest śledzone za pomocą śledzenia zmian

Śledzenie zawartości pliku umożliwia wyświetlenie zawartości pliku, przed i po zmianie, która jest śledzona z śledzenie zmian. Aby to zrobić, zapisuje zawartość pliku na koncie magazynu po każdej zmianie.

## <a name="requirements"></a>Wymagania

* Standardowe konto magazynu przy użyciu modelu wdrażania usługi Resource Manager jest wymagany do przechowywania zawartości pliku. Premium i kont magazynu w modelu wdrożenia klasycznego nie powinny być używane. Aby uzyskać więcej informacji dotyczących kont magazynu, zobacz [kontach magazynu Azure — informacje](../storage/common/storage-create-storage-account.md)

* Konto magazynu używane na może mieć tylko 1 konta usługi Automation połączone.

* [Śledzenie zmian](automation-change-tracking.md) jest włączona na koncie usługi Automation.

## <a name="enable-file-content-tracking"></a>Włącz śledzenie zawartości pliku

1. W witrynie Azure portal Otwórz konto usługi Automation, a następnie wybierz **śledzenie zmian**.
2. W górnym menu wybierz **edytowanie ustawień**.
3. Wybierz **zawartość pliku** i kliknij przycisk **łącze**. Spowoduje to otwarcie **Dodaj lokalizację zawartości na potrzeby śledzenia zmian** okienka.

   ![Włącz](./media/change-tracking-file-contents/enable.png)

4. Wybierz subskrypcję i konto magazynu służące do przechowywania zawartości pliku do. Jeśli chcesz włączyć śledzenie zawartości pliku dla wszystkich istniejących plików śledzone, wybierz **na** dla **przekazywać zawartość pliku dla wszystkich ustawień**. Możesz zmienić to dla każdej ścieżki pliku później.

   ![Ustaw konto magazynu](./media/change-tracking-file-contents/storage-account.png)

5. Po włączeniu na koncie magazynu i identyfikatorów URI sygnatury dostępu Współdzielonego są wyświetlane. Identyfikatory URI sygnatury dostępu Współdzielonego wygasają po upływie 365 dni i może być odtworzona, klikając **ponownie wygenerować** przycisku.

   ![Wyświetl listę kluczy konta](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Dodaj plik

W poniższych krokach objaśniono poprzez włączenie śledzenia zmian dla pliku:

1. Na **edytowanie ustawień** strony **Change Tracking**, wybierz opcję **pliki Windows** lub **pliki systemu Linux** kartę, a następnie kliknij przycisk  **Dodaj**

1. Wypełnij informacje dla ścieżki pliku, a następnie wybierz **True** w obszarze **przekazywać zawartość pliku dla wszystkich ustawień**. To ustawienie włącza zawartość pliku śledzenia dla tej ścieżki pliku.

   ![Dodawanie pliku systemu linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Wyświetlanie zawartości pliku śledzonych

1. Gdy wykryto zmiany dla pliku lub pliku w ścieżce pokazuje w portalu. Wybierz zmianę pliku z listy zmian. **Zmiany szczegółów** zostanie wyświetlone okienko.

   ![Lista zmian](./media/change-tracking-file-contents/change-list.png)

1. Na **zmiany szczegółów** stronie zostanie wyświetlony standardowy przed i po nim plików informacji, w lewym górnym rogu, kliknij **Wyświetl zmiany zawartości plików** Aby wyświetlić zawartość pliku.

   ![Szczegóły zmiany](./media/change-tracking-file-contents/change-details.png)

1. Nowa strona zawiera zawartość pliku, w widoku side-by-side. Możesz również wybrać **wbudowane** Aby wyświetlić widok wbudowane zmian.

   ![Przeglądanie zmian w plikach](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Kolejne kroki

Odwiedź samouczka na śledzenie zmian, aby dowiedzieć się więcej o korzystaniu z rozwiązania:

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów dotyczących zmian w Twoim środowisku](automation-tutorial-troubleshoot-changes.md)

* Użyj [przeszukiwania dzienników w dzienniki usługi Azure Monitor](../log-analytics/log-analytics-log-searches.md) Aby wyświetlić szczegółowe dane rozwiązania change tracking.

