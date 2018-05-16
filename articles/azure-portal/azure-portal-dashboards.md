---
title: Tworzyć i udostępniać pulpity nawigacyjne portalu Azure | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak tworzyć i edytować pulpitów nawigacyjnych w portalu Azure.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: doubeby
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/06/2016
ms.author: sewatson
ms.openlocfilehash: 3bdeb3255c8dc97d2867eca0b9cdec8223d31b21
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Tworzenie i udostępnianie pulpitów nawigacyjnych w portalu Azure
Można tworzyć wiele pulpitów nawigacyjnych i udostępniać je innym użytkownikom, którzy mają dostęp do Twojej subskrypcji platformy Azure.  W tym artykule przechodzi przez podstawy tworzenia, edytowania, publikowanie i zarządzanie dostępem do pulpitów nawigacyjnych.

## <a name="create-a-dashboard"></a>Utwórz pulpit nawigacyjny
Aby utworzyć pulpit nawigacyjny, wybierz przycisk **Nowy pulpit nawigacyjny** obok nazwy bieżącego pulpitu nawigacyjnego.  

![Utwórz pulpit nawigacyjny](./media/azure-portal-dashboards/new-dashboard.png)

Ta akcja tworzy nowy, pusty, prywatny pulpit nawigacyjny i przenosi użytkownika do trybu dostosowania, w którym można nazwać pulpit nawigacyjny oraz dodać kafelki i zmienić ich ułożenie.  W tym trybie galerii zwijanej kafelka przejmuje menu nawigacji po lewej stronie.  Galeria kafelka pozwala znaleźć kafelków dla zasobów platformy Azure na różne sposoby: można przeglądać [grupy zasobów](../azure-resource-manager/resource-group-overview.md#resource-groups), przez przez typ zasobu [tag](../azure-resource-manager/resource-group-using-tags.md), lub przez wyszukiwanie według nazwy zasobu.  

![Dostosowanie pulpitu nawigacyjnego](./media/azure-portal-dashboards/customize-dashboard.png)

Dodaj Kafelki przez przeciąganie i upuszczanie je na powierzchnię pulpitu nawigacyjnego wszędzie tam, gdzie ma.

Brak nową kategorię o nazwie **ogólne** dla kafelków, które nie są skojarzone z określonego zasobu.  W tym przykładzie mamy przypinanie kafelka Markdown.  Ten Kafelek umożliwia dodawanie niestandardowej zawartości do pulpitu nawigacyjnego.  Kafelek obsługuje zwykłego tekstu, [składni języka Markdown](https://daringfireball.net/projects/markdown/syntax)oraz ograniczony zestaw HTML.  (Dla bezpieczeństwa, nie może wykonać czynności, takie jak wstrzyknąć `<script>` znaczniki lub używać niektórych elementu Style CSS, która może kolidować z portalu.) 

![Dodawanie znaczników markdown](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>Edytuj pulpit nawigacyjny
Po utworzeniu pulpitu nawigacyjnego, można przypiąć Kafelki z galerii kafelka lub reprezentacja kafelka bloków. Załóżmy przypiąć reprezentację naszej grupy zasobów. Można albo numeru pin podczas przeglądania elementu, lub z bloku grupy zasobów. W obu przypadkach efekt spowodować przypinanie kafelka reprezentację grupy zasobów.

![Przypnij do pulpitu nawigacyjnego](./media/azure-portal-dashboards/pin-to-dashboard.png)

Po przypięciu elementu, wygląda na to, na pulpicie nawigacyjnym.

![widok pulpitu nawigacyjnego](./media/azure-portal-dashboards/view-dashboard.png)

Mamy kafelków Markdown i grupy zasobów przypięty do pulpitu nawigacyjnego, firma Microsoft można zmieniać rozmiar i zmienić Kafelki na odpowiedni układ.

Kursora myszy i wybierając "..." lub klikając Kafelek można wyświetlić wszystkie polecenia kontekstowych dla tego kafelka. Domyślnie istnieją dwie pozycje:

1. **Odepnij z pulpitu nawigacyjnego** — usuwa kafelka pulpitu nawigacyjnego
2. **Dostosowywanie** — wprowadza trybu dostosowania

![Dostosowywanie kafelka](./media/azure-portal-dashboards/customize-tile.png)

Wybierając dostosować, można zmieniać rozmiar i zmienić kolejność kafelków. Aby zmienić rozmiar kafelka, wybierz nowy rozmiar z menu kontekstowego, jak pokazano na poniższej ilustracji.

![Zmień rozmiar kafelka](./media/azure-portal-dashboards/resize-tile.png)

Lub, jeśli Kafelek obsługuje dowolnej wielkości, Zmień rozmiar można przeciągnąć prawym dolnym rogu.

![Zmień rozmiar kafelka](./media/azure-portal-dashboards/resize-corner.png)

Po zmianie rozmiaru kafelków, wyświetlić pulpit nawigacyjny.

![Widok kafelków](./media/azure-portal-dashboards/view-tile.png)

Po zakończeniu dostosowanie pulpitu nawigacyjnego po prostu wybierz **gotowe dostosowywanie** aby zakończyć działanie trybu dostosowania, lub kliknij prawym przyciskiem myszy i wybierz polecenie **gotowe dostosowywanie** z menu kontekstowego.

## <a name="publish-a-dashboard-and-manage-access-control"></a>Publikowanie pulpitu nawigacyjnego i zarządzanie kontrolą dostępu
Podczas tworzenia pulpitu nawigacyjnego jest on domyślnie prywatny, co oznacza, że użytkownik jest jedyną osobą, która może go zobaczyć.  Aby uwidocznić go dla innych osób, użyj przycisku **Udostępnij**, który jest wyświetlany wraz z innymi poleceniami pulpitu nawigacyjnego.

![Udostępnij pulpit nawigacyjny](./media/azure-portal-dashboards/share-dashboard.png)

Zostanie wyświetlona prośba o wybór subskrypcji i grupy zasobów, dla których zostanie opublikowany pulpit nawigacyjny. Aby bezproblemowo zintegrować pulpitów nawigacyjnych w ekosystemie, wdrożyliśmy udostępnionych pulpitów nawigacyjnych jako zasobów platformy Azure (tak, aby nie można udostępnić, wpisując adres e-mail).  Dostęp do informacji wyświetlanych przez większość Kafelki w portalu są regulowane przez [kontroli dostępu opartej na roli Azure](../role-based-access-control/role-assignments-portal.md). Z punktu widzenia kontroli dostępu do udostępnionych pulpitów nawigacyjnych nie różnią się od maszyny wirtualnej lub konto magazynu.  

Załóżmy, że masz subskrypcję platformy Azure i członkowie zespołu są przypisane role **właściciela**, **współautora**, lub **czytnika** subskrypcji.  Użytkownicy, którzy są właściciele i współautorzy mogą się na liście, wyświetlanie, tworzenie, modyfikowanie lub usuwanie pulpitów nawigacyjnych w ramach danej subskrypcji.  Użytkownicy, którzy są czytników są w stanie listy i widok pulpity nawigacyjne, ale nie można je modyfikować lub usuwać.  Użytkownicy z dostępem czytnika można wprowadzać zmian lokalnych do udostępnionego pulpitu nawigacyjnego, ale nie będą mogły publikować tych zmian na serwerze.  Jednak mogą one ułatwić prywatnej kopii pulpitu nawigacyjnego na własny użytek.  Jak zawsze poszczególnych Kafelki na pulpicie nawigacyjnym wymuszanie własnych regułami kontroli dostępu na podstawie zasobów, które są zgodne.  

Dla wygody środowisko publikowania w portalu skieruje Cię do wzorca, w którym umieszcza się pulpity nawigacyjne w grupie zasobów o nazwie **pulpity nawigacyjne**.  

![Publikowanie pulpitu nawigacyjnego](./media/azure-portal-dashboards/publish-dashboard.png)

Można również opublikować pulpit nawigacyjny do określonej grupy zasobów.  Kontrola dostępu do tego pulpitu nawigacyjnego odpowiada kontroli dostępu dla grupy zasobów.  Użytkownicy, którzy mogą zarządzać zasobami w danej grupie zasobów mają także dostęp do pulpitów nawigacyjnych.

![Publikowanie pulpitu nawigacyjnego do grupy zasobów](./media/azure-portal-dashboards/publish-to-resource-group.png)

Po opublikowaniu pulpitu nawigacyjnego **udostępniania + dostępu** Panelu sterowania zostanie odświeżania i wyświetlić informacje o pulpicie nawigacyjnym opublikowane, łącznie z łączem do zarządzania dostępem użytkowników do pulpitu nawigacyjnego.  To łącze powoduje uruchomienie standardowego bloku kontroli dostępu opartej na rolach, używany do zarządzania dostępem dla wszystkich zasobów platformy Azure.  Możesz zawsze wrócić do tego widoku, wybierając **udziału**.

![Zarządzanie kontrolą dostępu](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>Kolejne kroki
* Aby zarządzać zasobami, zobacz [zasobów Azure zarządzania za pośrednictwem portalu](../azure-resource-manager/resource-group-portal.md).
* Aby wdrożyć zasobów, zobacz [wdrożenie zasobów z szablonami usługi Resource Manager i portalu Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

