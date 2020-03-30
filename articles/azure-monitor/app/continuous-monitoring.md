---
title: Ciągłe monitorowanie potoku wydania devops za pomocą potoków platformy Azure i usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Zawiera instrukcje szybkiego konfigurowania ciągłego monitorowania za pomocą usługi Application Insights
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: e565101218b975ef2bd29b8a32a4aa1bf4300b6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655399"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Dodawanie ciągłego monitorowania do potoku wydania

Usługa Azure Pipelines integruje się z usługą Azure Application Insights, aby umożliwić ciągłe monitorowanie potoku wydania DevOps w całym cyklu życia tworzenia oprogramowania. 

Dzięki ciągłemu monitorowaniu potoki wersji mogą zawierać dane monitorowania z usługi Application Insights i innych zasobów platformy Azure. Gdy potok wydania wykryje alert usługi Application Insights, potok może bramy lub wycofać wdrożenia, dopóki alert zostanie rozwiązany. Jeśli wszystkie testy zdają, wdrożenia mogą automatycznie przechodzić od testu aż do produkcji, bez konieczności ręcznej interwencji. 

## <a name="configure-continuous-monitoring"></a>Konfigurowanie ciągłego monitorowania

1. W [usłudze Azure DevOps](https://dev.azure.com)wybierz organizację i projekt.
   
1. W menu po lewej stronie projektu wybierz pozycję **Pipelines** > **Releases**. 
   
1. Rozwijana strzałka obok **pozycji Nowy** i wybierz pozycję Nowy **potok wydania**. Lub, jeśli nie masz jeszcze potoku, wybierz **nowy potok** na stronie, która się pojawi.
   
1. W **okienku Wybierz szablon** wyszukaj i wybierz **wdrożenie usługi Azure App Service z ciągłym monitorowaniem,** a następnie wybierz pozycję **Zastosuj**. 

   ![Nowy potok potoku potoków platformy Azure](media/continuous-monitoring/001.png)

1. W polu **Etap 1** zaznacz hiperłącze do **wyświetlania zadań stołu montażowego.**

   ![Wyświetlanie zadań stołu montażego](media/continuous-monitoring/002.png)

1. W okienku konfiguracji **etap 1** uzupełnij następujące pola: 

    | Parametr        | Wartość |
   | ------------- |:-----|
   | **Nazwa etapu**      | Podaj nazwę etapu lub pozostaw ją na **etapie 1**. |
   | **Subskrypcja platformy Azure** | Rozwijana i wybierz połączona subskrypcja platformy Azure, której chcesz użyć.|
   | **Typ aplikacji** | Rozwijane i wybieranie typu aplikacji. |
   | **Nazwa usługi aplikacji** | Wprowadź nazwę usługi Azure App Service. |
   | **Nazwa grupy zasobów dla usługi Application Insights**    | Rozwijana i wybierz grupę zasobów, której chcesz użyć. |
   | **Nazwa zasobu usługi Application Insights** | Rozwijana i wybierz zasób Usługi Application Insights dla wybranej grupy zasobów.

1. Aby zapisać potok z domyślnymi ustawieniami reguł alertów, wybierz pozycję **Zapisz** w prawym górnym rogu w oknie Azure DevOps. Wprowadź opisowy komentarz, a następnie wybierz przycisk **OK**.

## <a name="modify-alert-rules"></a>Modyfikowanie reguł alertów

Wdrożenie **usługi Azure App Service z szablonem monitorowania ciągłego** ma cztery **reguły**alertów: Dostępność , **Żądania nie powiodło się,** **Czas odpowiedzi serwera**i **wyjątki serwera**. Można dodać więcej reguł lub zmienić ustawienia reguły, aby spełnić wymagania poziomu usług. 

Aby zmodyfikować ustawienia reguł alertów:

1. W lewym okienku strony potoku wydania wybierz pozycję **Konfiguruj alerty wglądu w aplikacje**.

1. W okienku **Alerty monitora platformy Azure** wybierz wielokropek **...** obok **reguł alertów**.
   
1. W oknie dialogowym **Reguły alertu** wybierz symbol listy rozwijanej obok reguły alertu, takiej jak **Dostępność**. 
   
1. Zmodyfikuj **próg** i inne ustawienia, aby spełnić wymagania.
   
   ![Modyfikowanie alertu](media/continuous-monitoring/003.png)
   
1. Wybierz **przycisk OK**, a następnie wybierz pozycję **Zapisz** w prawym górnym rogu w oknie Azure DevOps. Wprowadź opisowy komentarz, a następnie wybierz przycisk **OK**.

## <a name="add-deployment-conditions"></a>Dodawanie warunków wdrażania

Po dodaniu bramek wdrożenia do potoku wydania alert, który przekracza ustawione progi, zapobiega niechcianej promocji wydania. Po rozwiązaniu alertu wdrożenie można kontynuować automatycznie.

Aby dodać bramy wdrażania:

1. Na głównej stronie potoku w obszarze **Etapy**wybierz symbol **Warunki przed wdrożeniem** lub **Warunki po wdrożeniu,** w zależności od tego, który etap wymaga ciągłej bramy monitorowania.
   
   ![Warunki przed wydrożeniem](media/continuous-monitoring/004.png)
   
1. W okienku konfiguracji **Warunki przedwydrożeniowe** ustaw **bramę** na **Włączone**.
   
1. Obok **pozycji Bramy wdrażania**wybierz pozycję **Dodaj**.
   
1. Wybierz **alerty usługi Query Azure Monitor** z menu rozwijanego. Ta opcja umożliwia dostęp do alertów usługi Azure Monitor i Application Insights.
   
   ![Kwerenda usługi Azure Monitor alertów](media/continuous-monitoring/005.png)
   
1. W **obszarze Opcje oceny**wprowadź odpowiednie wartości dla ustawień, takich jak Czas między **ponowną oceną bramek** i **Limit czasu, po którym bramy nie powiodą się.** 

## <a name="view-release-logs"></a>Wyświetlanie dzienników wersji

Zachowanie bramy wdrażania i inne kroki wydania można zobaczyć w dziennikach wydania. Aby otworzyć dzienniki:

1. Wybierz **pozycję Zwalnia** z lewego menu strony potoku. 
   
1. Wybierz dowolną wersję. 
   
1. W obszarze **Etapy**wybierz dowolny etap, aby wyświetlić podsumowanie wydania. 
   
1. Aby wyświetlić dzienniki, wybierz **pozycję Wyświetl dzienniki** w podsumowaniu wydania, wybierz hiperłącze **Powiodło się** lub **Niepowodzenie** na dowolnym stole montażowym lub umieść wskaźnik myszy na dowolnym etapie i wybierz pozycję **Dzienniki**. 
   
   ![Wyświetlanie dzienników wersji](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat potoków platformy Azure, zobacz [dokumentację usługi Azure Pipelines.](https://docs.microsoft.com/azure/devops/pipelines)
