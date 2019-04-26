---
title: Tworzenie i udostępnianie pulpitów nawigacyjnych portalu platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak tworzyć i edytować pulpitów nawigacyjnych w witrynie Azure portal.
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
ms.author: kfollis
ms.openlocfilehash: 693e973fb988a57c15b4ea2fae47f16b4ff39011
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552711"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure portal
Można utworzyć wiele pulpitów nawigacyjnych i udostępnianie ich innym osobom, które mają dostęp do subskrypcji platformy Azure.  W tym artykule omówiono podstawowe informacje dotyczące tworzenia, edytowania, publikowanie i zarządzanie dostępem do pulpitów nawigacyjnych.

## <a name="create-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego
Aby utworzyć pulpit nawigacyjny, wybierz przycisk **Nowy pulpit nawigacyjny** obok nazwy bieżącego pulpitu nawigacyjnego.  

![Tworzenie pulpitu nawigacyjnego](./media/azure-portal-dashboards/new-dashboard.png)

Ta akcja tworzy nowy, pusty, prywatny pulpit nawigacyjny i przenosi użytkownika do trybu dostosowania, w którym można nazwać pulpit nawigacyjny oraz dodać kafelki i zmienić ich ułożenie.  W tym trybie Galeria kafelków zwijany przejmuje menu nawigacji po lewej stronie.  Galeria kafelków pozwala wyszukiwać kafelków dla zasobów platformy Azure na różne sposoby: można przeglądać, [grupy zasobów](../azure-resource-manager/resource-group-overview.md#resource-groups), przez typ zasobu [tag](../azure-resource-manager/resource-group-using-tags.md), lub przez wyszukiwanie według nazwy zasobu.  

![Dostosowywanie pulpitu nawigacyjnego](./media/azure-portal-dashboards/customize-dashboard.png)

Dodaj Kafelki, przeciągając i upuszczając je na powierzchnię pulpitu nawigacyjnego, wszędzie tam, gdzie chcesz.

Brak nową kategorię o nazwie **ogólne** dla kafelków, które nie są skojarzone z określonego zasobu.  W tym przykładzie przypięciu kafelka Markdown.  Ten Kafelek umożliwia dodawanie zawartości niestandardowe do pulpitu nawigacyjnego.  Kafelek obsługuje zwykły tekst [składnię języka znaczników Markdown](https://daringfireball.net/projects/markdown/syntax)oraz ograniczony zestaw elementów HTML.  (Pod kątem bezpieczeństwa, nie można wykonać czynności, takie jak wstrzyknięcia `<script>` tagów lub używać niektórych element Style CSS, która może kolidować z portalem.) 

![Dodawanie znaczników markdown](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>Edytuj pulpit nawigacyjny
Po utworzeniu pulpitu nawigacyjnego, możesz przypiąć Kafelki z galerii kafelków lub kafelka reprezentacja bloków. Teraz można przypiąć reprezentacja naszą grupę zasobów. Można albo numeru pin podczas przeglądania elementu, jak i w bloku grupy zasobów. Oba podejścia spowodować przypinanie kafelka reprezentacja grupy zasobów.

![Przypnij do pulpitu nawigacyjnego](./media/azure-portal-dashboards/pin-to-dashboard.png)

Po przypięciu elementu, jest wyświetlana na pulpicie nawigacyjnym.

![Wyświetl pulpit nawigacyjny](./media/azure-portal-dashboards/view-dashboard.png)

Skoro mamy kafelka Markdown i grupę zasobów przypięte do pulpitu nawigacyjnego, firma Microsoft Zmień rozmiar i zmienić Kafelki do odpowiedniego układu.

Przez umieszczenie nad nim kursora i wybranie w "menu..." lub kliknij prawym przyciskiem myszy na kafelku widać wszystkie polecenia kontekstowych dla danego kafelka. Domyślnie istnieją dwa elementy:

1. **Odepnij od pulpitu nawigacyjnego** — usuwa kafelka na pulpicie nawigacyjnym
2. **Dostosowywanie** — wprowadza trybu dostosowania

![Dostosowywanie kafelka](./media/azure-portal-dashboards/customize-tile.png)

Wybierając dostosować, można zmienić rozmiar i kolejność kafelków. Aby zmienić rozmiar kafelka, wybierz nowy rozmiar z menu kontekstowego, jak pokazano na poniższej ilustracji.

![Zmiana rozmiaru kafelka](./media/azure-portal-dashboards/resize-tile.png)

Lub, jeśli Kafelek obsługuje dowolnej wielkości, Zmień rozmiar, można przeciągnąć prawym dolnym rogu.

![Zmiana rozmiaru kafelka](./media/azure-portal-dashboards/resize-corner.png)

Po zmianie rozmiaru kafelków, wyświetlić pulpit nawigacyjny.

![— Kafelek widok](./media/azure-portal-dashboards/view-tile.png)

Po zakończeniu dostosowywania pulpitu nawigacyjnego po prostu wybierz **Zakończono dostosowywanie** aby zakończyć działanie trybu dostosowania, lub kliknij prawym przyciskiem myszy i wybierz polecenie **Zakończono dostosowywanie** z menu kontekstowego.

## <a name="publish-a-dashboard-and-manage-access-control"></a>Publikowanie pulpitu nawigacyjnego i zarządzanie kontrolą dostępu
Podczas tworzenia pulpitu nawigacyjnego jest on domyślnie prywatny, co oznacza, że użytkownik jest jedyną osobą, która może go zobaczyć.  Aby uwidocznić go dla innych osób, użyj przycisku **Udostępnij**, który jest wyświetlany wraz z innymi poleceniami pulpitu nawigacyjnego.

![udostępnianie pulpitu nawigacyjnego](./media/azure-portal-dashboards/share-dashboard.png)

Zostanie wyświetlona prośba o wybór subskrypcji i grupy zasobów, dla których zostanie opublikowany pulpit nawigacyjny. Bezproblemowo zintegrować pulpitów nawigacyjnych w ekosystemie, wprowadziliśmy udostępnionych pulpitów nawigacyjnych jako zasoby platformy Azure (tak, aby nie można udostępniać, wpisując adres e-mail).  Dostęp do informacji wyświetlanych przez większość kafelków w portalu są regulowane przez [kontroli dostępu opartej na roli Azure](../role-based-access-control/role-assignments-portal.md). Z punktu widzenia kontroli dostępu do udostępnionych pulpitów nawigacyjnych nie różnią się od maszyny wirtualnej lub na koncie magazynu.  

Załóżmy, że masz subskrypcję platformy Azure i członkowie zespołu są przypisane role **właściciela**, **Współautor**, lub **czytnika** subskrypcji.  Użytkownicy, którzy są właściciele lub współautorzy mogą się do listy, wyświetlanie, tworzenie, modyfikowanie lub usuwanie pulpity nawigacyjne w tej subskrypcji.  Użytkownicy, którzy są czytelnicy będą mogli listy i widok pulpitów nawigacyjnych, ale nie je modyfikować lub usuwać.  Użytkownicy z dostępem do czytnika mogą wprowadzać zmiany lokalne do udostępnionego pulpitu nawigacyjnego, ale nie będą mogli publikować te zmiany na serwerze.  Jednakże mogą robić więjsze prywatną kopię pulpitu nawigacyjnego na własny użytek.  Jak zawsze poszczególnych kafelków na pulpicie nawigacyjnym wymuszanie własnych reguł kontroli dostępu na podstawie zasobów, które są zgodne.  

Dla wygody środowisko publikowania w portalu skieruje Cię do wzorca, w którym umieszcza się pulpity nawigacyjne w grupie zasobów o nazwie **pulpity nawigacyjne**.  

![Publikowanie pulpitu nawigacyjnego](./media/azure-portal-dashboards/publish-dashboard.png)

Możesz również opublikować pulpitu nawigacyjnego do określonej grupy zasobów.  Kontrola dostępu do tego pulpitu nawigacyjnego pasuje do kontroli dostępu dla grupy zasobów.  Użytkownicy, którzy mogą zarządzać zasobami w danej grupie zasobów również mieć dostęp do pulpitów nawigacyjnych.

![Publikowanie pulpitu nawigacyjnego do grupy zasobów](./media/azure-portal-dashboards/publish-to-resource-group.png)

Po opublikowaniu pulpitu nawigacyjnego **dostępu + udostępniania** okienko sterowania spowoduje odświeżenie i wyświetlenie informacji o opublikowany pulpit nawigacyjny, w tym link do zarządzania dostępem użytkowników do pulpitu nawigacyjnego.  To łącze powoduje uruchomienie standardowego bloku kontroli dostępu opartej na rolach, używane do zarządzania dostępem dla dowolnych zasobów platformy Azure.  Możesz zawsze wrócić do tego widoku, wybierając **udziału**.

![Zarządzanie kontrolą dostępu](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>Kolejne kroki
* Aby zarządzać zasobami, zobacz [zarządzanie zasobami platformy Azure przy użyciu witryny Azure portal](../azure-resource-manager/manage-resources-portal.md).
* Aby wdrożyć zasoby, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).

