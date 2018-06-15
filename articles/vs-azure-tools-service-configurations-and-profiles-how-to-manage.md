---
title: Jak zarządzać konfiguracje usług i profile | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pracować z plikami konfiguracji usługi konfiguracji i profile | które ustawienia w środowiskach wdrożenia są przechowywane i ustawienia publikowania dla usługi w chmurze.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 7da8c551-fb06-4057-b5c7-c77f4b39d803
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 8/11/2017
ms.author: ghogen
ms.openlocfilehash: 4f2dce7cdbf81718f9ccf0c1b7d52c6678cc847f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799060"
---
# <a name="how-to-manage-service-configurations-and-profiles"></a>Jak zarządzać konfiguracje usług i profili
## <a name="overview"></a>Przegląd
Po opublikowaniu usługi w chmurze programu Visual Studio przechowuje informacje o konfiguracji w dwa rodzaje pliki konfiguracji: usługa konfiguracji i profilów. Konfiguracja usługi (.cscfg pliki) przechowywania ustawień dla środowisk wdrażania dla usługi w chmurze Azure. Azure korzysta z tych plików konfiguracji podczas zarządzania usługi w chmurze. Z drugiej strony profile (pliki .azurePubxml) magazynu ustawienia publikowania dla usługi w chmurze. Te ustawienia są rekord wybierz po Kreator publikowania i są używane lokalnie przez program Visual Studio. W tym temacie wyjaśniono, jak pracować z obu typów plików konfiguracyjnych.

## <a name="service-configurations"></a>Konfiguracja usługi
Możesz utworzyć wiele konfiguracji usługi do użycia dla poszczególnych środowisk wdrażania. Może na przykład utworzyć konfigurację usługi środowisku lokalnym, który umożliwia uruchamianie i testowanie aplikacji Azure i inną konfigurację usługi dla środowiska produkcyjnego.

Można dodać, usunąć, Zmień nazwę i zmodyfikuj te konfiguracje usługi, w zależności od wymagań. Te konfiguracje usługi można zarządzać w programie Visual Studio, jak pokazano na poniższej ilustracji.

![Zarządzanie konfiguracjami usługi](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

Można również otworzyć **Zarządzanie konfiguracjami** dialogowe strony właściwości tej roli. Aby otworzyć właściwości rolę w projekcie platformy Azure, otwórz menu skrótów dla tej roli, a następnie wybierz **właściwości**. Na **ustawienia** karcie, rozwiń **konfiguracji usługi** listy, a następnie wybierz **Zarządzaj** otworzyć **Zarządzanie konfiguracjami** okno dialogowe.

### <a name="to-add-a-service-configuration"></a>Aby dodać konfigurację usługi
1. W Eksploratorze rozwiązań Otwórz menu skrótów projektu platformy Azure, a następnie wybierz **Zarządzanie konfiguracjami**.
   
    **Konfiguracji usługi zarządzania** zostanie wyświetlone okno dialogowe.
2. Aby dodać konfiguracji usługi, należy utworzyć kopię istniejącej konfiguracji. Aby to zrobić, należy wybrać konfigurację, którą chcesz skopiować z listy nazw, a następnie wybierz **utworzyć kopię**.
3. (Opcjonalnie) Aby podać inną nazwę konfiguracji usługi, wybierz z listy Nazwa nowej konfiguracji usługi, a następnie wybierz **zmienić**. W **nazwa** tekstu wpisz nazwę, która ma być używana dla tej konfiguracji usługi, a następnie wybierz **OK**.
   
    Nowy plik konfiguracji usługi o nazwie element ServiceConfiguration. [Nazwa nowego] .cscfg zostanie dodany do projektu platformy Azure w Eksploratorze rozwiązań.

### <a name="to-delete-a-service-configuration"></a>Aby usunąć konfigurację usługi
1. W Eksploratorze rozwiązań Otwórz menu skrótów projektu platformy Azure, a następnie wybierz **Zarządzanie konfiguracjami**.
   
    **Konfiguracji usługi zarządzania** zostanie wyświetlone okno dialogowe.
2. Aby usunąć konfigurację usługi, wybierz pozycję konfiguracji, który chcesz usunąć z **nazwa** listy, a następnie wybierz **Usuń**. Aby sprawdzić, czy chcesz usunąć tę konfigurację, pojawi się okno dialogowe.
3. Wybierz pozycję **Usuń**.
   
     Plik konfiguracji usługi zostanie usunięty z projektu platformy Azure w Eksploratorze rozwiązań.

### <a name="to-rename-a-service-configuration"></a>Aby zmienić konfigurację usługi
1. W Eksploratorze rozwiązań Otwórz menu skrótów projektu platformy Azure, a następnie wybierz **Zarządzanie konfiguracjami**.
   
    **Konfiguracji usługi zarządzania** zostanie wyświetlone okno dialogowe.
2. Aby zmienić nazwę konfiguracji usługi, wybierz nową konfigurację usługi z **nazwa** listy, a następnie wybierz **zmienić**. W **nazwa** tekstu wpisz nazwę, która ma być używana dla tej konfiguracji usługi, a następnie wybierz **OK**.
   
    Nazwa pliku konfiguracji usługi została zmieniona w projekcie platformy Azure w Eksploratorze rozwiązań.

### <a name="to-change-a-service-configuration"></a>Aby zmienić konfigurację usługi
* Jeśli chcesz zmienić konfigurację usługi, otwórz menu skrótów dla konkretnej roli, które chcesz zmienić w projekcie platformy Azure, a następnie wybierz **właściwości**. Zobacz [porady: Konfigurowanie ról dla usługi w chmurze platformy Azure z programem Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) Aby uzyskać więcej informacji.

## <a name="make-different-setting-combinations-by-using-profiles"></a>Tworzenie ustawienie różnych kombinacji przy użyciu profili
Przy użyciu profilu, można automatycznie wypełnić **Kreator publikowania** z różną kombinacją możliwości ustawienia do różnych celów. Na przykład można mieć jeden profil debugowania i drugi dla wersji kompilacji. W takim przypadku Twojej **debugowania** byłyby profilu **IntelliTrace** włączone i **debugowania** zaznaczone, Konfiguracja i **wersji** byłyby profilu **IntelliTrace** wyłączone i **wersji** Konfiguracja wybrana. Różne profile można także użyć do wdrażania usługi przy użyciu innego konta magazynu.

Po uruchomieniu kreatora po raz pierwszy, tworzony jest profil domyślny. Visual Studio zapisuje profil w pliku z rozszerzeniem .azurePubXml, które jest dodawane do projektu platformy Azure w obszarze **profile** folderu. Jeśli po uruchomieniu kreatora później, ręcznie określić różne opcje, plik jest automatycznie aktualizacji. Przed uruchomieniem Poniższa procedura powinna już opublikowany usługi w chmurze co najmniej raz.

### <a name="to-add-a-profile"></a>Aby dodać profil
1. Otwórz menu skrótów projektu platformy Azure, a następnie wybierz **publikowania**.
2. Obok pozycji **docelowego profilu** listy, wybierz **Zapisz profil** przycisku, jak przedstawiono na poniższej ilustracji. Spowoduje to utworzenie profilu dla Ciebie.
   
    ![Utwórz nowy profil](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)
3. Po utworzeniu profilu wybierz **< Zarządzaj >** w **docelowego profilu** listy.
   
    **Zarządzania profilami** zostanie wyświetlone okno dialogowe, jak przedstawiono na poniższej ilustracji.
   
    ![Okno dialogowe Profile Zarządzanie](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)
4. W **nazwa** listy, wybierz profil, a następnie wybierz **Utwórz kopię**.
5. Wybierz **Zamknij** przycisku.
   
    Nowy profil zostanie wyświetlony na liście profilu.
6. W **docelowego profilu** listy, wybierz profil, który został utworzony. Ustawienia kreatora Publikowanie są wypełniane opcji z wybranego profilu.
7. Wybierz **Wstecz** i **dalej** przycisków, aby wyświetlić każdej strony Kreatora publikacji, a następnie dostosować ustawienia dla tego profilu. Zobacz [Kreator publikowania aplikacji Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) informacji.
8. Po zakończeniu Dostosowywanie ustawień, wybierz **dalej** aby powrócić do strony ustawień. Profil zostanie zapisany po opublikowaniu usługi przy użyciu tych ustawień lub w przypadku wybrania **zapisać** obok listy profilów.

### <a name="to-rename-or-delete-a-profile"></a>Aby zmienić lub usunąć profil
1. Otwórz menu skrótów projektu platformy Azure, a następnie wybierz **publikowania**.
2. W **docelowego profilu** listy, wybierz **Zarządzaj**.
3. W **zarządzania profilami** okno dialogowe, wybierz profil, który chcesz usunąć, a następnie wybierz **Usuń**.
4. W oknie dialogowym potwierdzenia wybierz **OK**.
5. Wybierz **Zamknij**.

### <a name="to-change-a-profile"></a>Aby zmienić profil
1. Otwórz menu skrótów projektu platformy Azure, a następnie wybierz **publikowania**.
2. W **docelowego profilu** listy, wybierz profil, który chcesz zmienić.
3. Wybierz **Wstecz** i **dalej** przycisków, aby wyświetlić każdej strony Kreatora publikacji, a następnie zmień odpowiednie ustawienia. Zobacz [Kreator publikowania aplikacji Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) informacji.
4. Po zakończeniu zmiany ustawień, wybierz **dalej** aby powrócić do **ustawienia** strony.
5. (Opcjonalnie) zaznacz **publikowania** publikowania usługi w chmurze przy użyciu nowych ustawień. Jeśli nie chcesz publikować w tej chwili usługi w chmurze, a następnie zamknij Kreatora publikacji, Visual Studio zapyta, czy chcesz zapisać zmiany w profilu.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o konfigurowaniu innych części projektu platformy Azure w programie Visual Studio, zobacz [Konfigurowanie projektu platformy Azure](http://go.microsoft.com/fwlink/p/?LinkID=623075)

