---
title: Tworzenie i udostępnianie pulpitów nawigacyjnych w Azure Portal
description: W tym artykule opisano sposób tworzenia, dostosowywania, publikowania i udostępniania pulpitów nawigacyjnych w Azure Portal.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: fee3d9f0ef4f2c622d42373f4ed8c895f2c76adf
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901047"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Tworzenie i udostępnianie pulpitów nawigacyjnych w Azure Portal

Pulpity nawigacyjne to skoncentrowany i zorganizowany widok zasobów w chmurze w Azure Portal. Używaj pulpitów nawigacyjnych jako obszaru roboczego, w którym można szybko uruchamiać zadania na potrzeby codziennych operacji i monitorować zasoby. Można na przykład tworzyć niestandardowe pulpity nawigacyjne na podstawie projektów, zadań lub ról użytkownika.

Azure Portal udostępnia domyślny pulpit nawigacyjny jako punkt początkowy. Można edytować domyślnego pulpitu nawigacyjnego. Tworzenie i dostosowywanie dodatkowych pulpitów nawigacyjnych oraz publikowanie i udostępnianie pulpitów nawigacyjnych w celu udostępnienia ich innym użytkownikom. W tym artykule opisano sposób tworzenia nowego pulpitu nawigacyjnego, dostosowywania interfejsu i publikowania i udostępniania pulpitów nawigacyjnych.

## <a name="create-a-new-dashboard"></a>Tworzenie nowego pulpitu nawigacyjnego

W tym przykładzie utworzymy nowy, prywatny pulpit nawigacyjny i przypiszesz nazwę. Wykonaj następujące kroki, aby rozpocząć proces:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Z menu Azure Portal wybierz pozycję **pulpit nawigacyjny**. Domyślny widok może już być ustawiony na pulpit nawigacyjny.

    ![Otwieranie pulpitu nawigacyjnego](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Wybierz pozycję **nowy pulpit nawigacyjny**.

    ![Zrzut ekranu przedstawiający domyślny pulpit nawigacyjny](./media/azure-portal-dashboards/create-new-dashboard.png)

    Ta akcja powoduje otwarcie **galerii kafelków**, z której będą wybierane kafelki, i pustej siatki, w której będą rozmieszczane kafelki.

    ![Zrzut ekranu przedstawiający galerię kafelków i pustą siatkę](./media/azure-portal-dashboards/dashboard-name.png)

1. Wybierz **mój tekst mojego pulpitu nawigacyjnego** w etykiecie pulpitu nawigacyjnego i wprowadź nazwę, która ułatwi Ci łatwą identyfikację niestandardowego pulpitu nawigacyjnego.

1. Wybierz pozycję **gotowe Dostosowywanie** w nagłówku strony, aby wyjść z trybu edycji.

W widoku pulpitu nawigacyjnego zostanie wyświetlony pusty pulpit nawigacyjny. Wybierz strzałkę obok nazwy pulpitu nawigacyjnego, aby wyświetlić dostępne pulpity nawigacyjne. Lista może zawierać pulpity nawigacyjne, które zostały utworzone i udostępnione przez innych użytkowników.

## <a name="edit-a-dashboard"></a>Edytowanie pulpitu nawigacyjnego

Teraz Zmodyfikujmy pulpit nawigacyjny, aby dodawać, zmieniać rozmiar i rozmieszczać kafelki, które reprezentują zasoby platformy Azure.

### <a name="add-tiles"></a>Dodaj kafelki

Aby dodać kafelki do pulpitu nawigacyjnego, wykonaj następujące kroki:

1. Wybierz ![Edytuj ikonę](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Edytuj** z nagłówka strony.

    ![Zrzut ekranu przedstawiający edytowanie na pulpicie nawigacyjnym](./media/azure-portal-dashboards/dashboard-edit.png)

1. Przeglądaj **galerię kafelków** lub użyj pola wyszukiwania, aby znaleźć żądany kafelek.

1. Wybierz pozycję **Dodaj** , aby dodać kafelek do pulpitu nawigacyjnego przy użyciu domyślnego rozmiaru i lokalizacji. Możesz też przeciągnąć kafelek do siatki i umieścić go w dowolnym miejscu.

Wiele stron zasobów zawiera ikonę pinezki na pasku poleceń. Jeśli wybierzesz ikonę, kafelek reprezentujący stronę źródłową zostanie przypięty do pulpitu nawigacyjnego, który jest obecnie aktywny. Ta metoda jest alternatywnym sposobem dodawania kafelków do pulpitu nawigacyjnego.

![Zrzut ekranu przedstawiający pasek poleceń strony z ikoną pinezki](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> Jeśli pracujesz z więcej niż jedną organizacją, Dodaj kafelek **tożsamość organizacji** do pulpitu nawigacyjnego, aby wyraźnie pokazać, do której organizacji należą zasoby.
>
>

### <a name="resize-or-rearrange-tiles"></a>Zmiana rozmiaru lub zmiany układu kafelków

Aby zmienić rozmiar kafelka lub ponownie rozmieścić kafelki na pulpicie nawigacyjnym, wykonaj następujące kroki:

1. Wybierz ![Edytuj ikonę](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Edytuj** z nagłówka strony.

1. Wybierz menu kontekstowe w prawym górnym rogu kafelka. Następnie wybierz rozmiar kafelka. Kafelki obsługujące dowolny rozmiar obejmują również "uchwyt" w prawym dolnym rogu, które umożliwiają przeciągnięcie kafelka do żądanego rozmiaru.

    ![Zrzut ekranu pulpitu nawigacyjnego z otwartym menu Rozmiar kafelka](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Wybierz kafelek i przeciągnij go do nowej lokalizacji w siatce, aby rozmieścić pulpit nawigacyjny.

### <a name="additional-tile-configuration"></a>Dodatkowa konfiguracja kafelka

Niektóre kafelki mogą wymagać więcej konfiguracji w celu wyświetlenia potrzebnych informacji. Na przykład kafelek **wykresu metryk** musi być skonfigurowany do wyświetlania metryki z **Azure monitor**. Możesz również dostosować dane kafelków, aby zastąpić domyślne ustawienia czasu pulpitu nawigacyjnego.

Wszystkie kafelki, które muszą zostać skonfigurowane, są wyświetlane na transparencie **kafelka** , dopóki nie zostanie on dostosowany. Wybierz ten transparent, a następnie wykonaj wymagane czynności konfiguracyjne.

![Zrzut ekranu przedstawiający kafelek, który wymaga konfiguracji](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Kafelek promocji pozwala na Wyświetlanie niestandardowej, statycznej zawartości na pulpicie nawigacyjnym. Mogą to być podstawowe instrukcje, obraz, zestaw hiperłączy, a nawet informacje kontaktowe. Aby uzyskać więcej informacji na temat korzystania z kafelka z promocji, zobacz temat [Używanie kafelka promocji na pulpitach nawigacyjnych platformy Azure do wyświetlania zawartości niestandardowej](azure-portal-markdown-tile.md).
>
>
### <a name="customize-tile-data"></a>Dostosuj dane kafelka

Dane na pulpicie nawigacyjnym automatycznie pokazują aktywność dla ostatnich 24 godzin. Aby wyświetlić inny przedział czasu dla tylko tego kafelka, wykonaj następujące kroki:

1. Wybierz opcję **Dostosuj dane kafelków** z menu kontekstowego lub ikony filtru ![,](./media/azure-portal-dashboards/dashboard-filter.png) filtr w lewym górnym rogu kafelka.

    ![Zrzut ekranu przedstawiający menu kontekstowe kafelka](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Zaznacz pole wyboru, aby **zastąpić ustawienia czasu pulpitu nawigacyjnego na poziomie kafelka**.

    ![Zrzut ekranu okna dialogowego, aby skonfigurować ustawienia czasu kafelka](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Wybierz przedział czasu, który ma być wyświetlany dla tego kafelka. Możesz wybrać od ostatnich 30 minut do ostatnich 30 dni lub zdefiniować zakres niestandardowy.

1. Wybierz stopień szczegółowości czasu do wyświetlenia. Możesz pokazać dowolne miejsce od 1 minuty do jednego miesiąca.

1. Wybierz przycisk **Zastosuj**.

## <a name="delete-a-tile"></a>Usuwanie kafelka

Aby usunąć kafelek z pulpitu nawigacyjnego, wykonaj następujące kroki:

* Wybierz menu kontekstowe w prawym górnym rogu kafelka, a następnie wybierz pozycję **Usuń z pulpitu nawigacyjnego**. Lub:

* Wybierz pozycję ![Edytuj ikonę](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Edytuj** , aby wprowadzić tryb dostosowywania. Umieść kursor w prawym górnym rogu kafelka, a następnie wybierz ikonę usuwania ![](./media/azure-portal-dashboards/dashboard-delete-icon.png) ikona Usuń, aby usunąć kafelek z pulpitu nawigacyjnego.

   ![Zrzut ekranu przedstawiający sposób usuwania kafelka z pulpitu nawigacyjnego](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Klonowanie pulpitu nawigacyjnego

Aby użyć istniejącego pulpitu nawigacyjnego jako szablonu dla nowego pulpitu nawigacyjnego, wykonaj następujące kroki:

1. Upewnij się, że widok pulpitu nawigacyjnego pokazuje pulpit nawigacyjny, który chcesz skopiować.

1. W nagłówku strony wybierz pozycję ![ikona klonowania](./media/azure-portal-dashboards/dashboard-clone.png) **klon**.

1. Kopia pulpitu nawigacyjnego o nazwie **klon** *nazwy pulpitu nawigacyjnego* otwiera się w trybie edycji. Aby zmienić nazwę i dostosować pulpit nawigacyjny, należy wykonać kroki opisane w tym artykule.

## <a name="publish-and-share-a-dashboard"></a>Publikowanie i udostępnianie pulpitu nawigacyjnego

Gdy tworzysz pulpit nawigacyjny, jest on domyślnie prywatny, co oznacza, że jesteś jedyną osobą, która ją widzi. Aby udostępnić pulpity nawigacyjne innym użytkownikom, możesz udostępnić je innym osobom. Najpierw musisz opublikować pulpit nawigacyjny jako zasób platformy Azure. Aby opublikować i udostępnić niestandardowy pulpit nawigacyjny, wykonaj następujące kroki:

1. Wybierz ikonę udostępniania ![](./media/azure-portal-dashboards/dashboard-share-icon.png) **Udostępnij** z nagłówka strony. **Udostępnianie i kontrola dostępu** otwiera się.

1. Sprawdź, czy podano poprawną nazwę pulpitu nawigacyjnego.

1. Wybierz **nazwę subskrypcji**. Użytkownicy z dostępem do subskrypcji mogą korzystać z udostępnionego pulpitu nawigacyjnego. Dostęp do zasobów reprezentowanych przez poszczególne kafelki jest określany przez kontrolę dostępu opartą na rolach na platformie Azure.

1. Zaznacz pole wyboru, aby opublikować ten pulpit nawigacyjny w grupie zasobów "pulpity nawigacyjne" dla wybranej subskrypcji. Lub wyczyść pole wyboru i wybierz opcję opublikowania w istniejącej grupie zasobów.

1. Wybierz lokalizację dla zasobu pulpitu nawigacyjnego. Zalecamy znalezienie pulpitu nawigacyjnego z innymi zasobami. W przypadku wybrania z istniejących grup zasobów pulpit nawigacyjny zostanie automatycznie umieszczony w tej grupie zasobów.

1. Wybierz pozycję **Publikuj**.

    ![Zrzut ekranu okna dialogowego publikowania pulpitu nawigacyjnego](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>Ustawianie kontroli dostępu na udostępnionym pulpicie nawigacyjnym

Po opublikowaniu pulpitu nawigacyjnego Zarządzaj tym, kto ma dostęp do pulpitu nawigacyjnego, wykonując następujące czynności:

1. W okienku **udostępnianie i kontrola dostępu** wybierz pozycję **Zarządzaj użytkownikami**.

    ![Zrzut ekranu okna dialogowego udostępnianie pulpitu nawigacyjnego i kontrola dostępu](./media/azure-portal-dashboards/dashboard-share-access-control.png)

1. Zostanie otwarta strona **Access Control** . Możesz sprawdzić poziom dostępu dla kogoś lub dodać nowe przypisanie roli. W tym miejscu można dodać uprawnienia do pulpitu nawigacyjnego.

> [!NOTE]
> Kafelki są reprezentatywnymi widokami zasobów w organizacji. Dostęp do zasobów jest zarządzany za pośrednictwem przypisywania kontroli dostępu opartej na rolach, a uprawnienia są dziedziczone z subskrypcji do zasobu. Przyznanie dostępu do pulpitu nawigacyjnego nie powoduje automatycznego przypisywania uprawnień do zasobów pokazywanych na pulpicie nawigacyjnym. Aby uzyskać więcej informacji o uprawnieniach do udostępnionych pulpitów nawigacyjnych i kontroli dostępu opartej na rolach dla zasobów, zobacz [udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu Access Control opartych na rolach](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Otwórz udostępniony pulpit nawigacyjny

Aby znaleźć i otworzyć udostępniony pulpit nawigacyjny, wykonaj następujące kroki:

1. Wybierz strzałkę obok nazwy pulpitu nawigacyjnego.

1. Wybierz z wyświetlonej listy pulpitów nawigacyjnych lub **Przeglądaj wszystkie pulpity** nawigacyjne, jeśli pulpit nawigacyjny, który chcesz otworzyć, nie znajduje się na liście.

    ![Zrzut ekranu przedstawiający menu wyboru pulpitu nawigacyjnego](./media/azure-portal-dashboards/dashboard-browse.png)

1. W polu **Typ** wybierz pozycję **udostępnione pulpity nawigacyjne**.

1. Wybierz co najmniej jedną subskrypcję. Możesz również wprowadzić tekst, aby filtrować pulpity nawigacyjne według nazwy.

1. Wybierz pulpit nawigacyjny z listy udostępnionych pulpitów nawigacyjnych.

## <a name="delete-a-dashboard"></a>Usuwanie pulpitu nawigacyjnego

Aby trwale usunąć prywatny lub udostępniony pulpit nawigacyjny, wykonaj następujące kroki:

1. Wybierz pulpit nawigacyjny, który chcesz usunąć z listy obok nazwy pulpitu nawigacyjnego.

1. Wybierz ![Usuń ikonę](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Usuń** z nagłówka strony.

1. W przypadku prywatnego pulpitu nawigacyjnego wybierz pozycję **OK** w oknie dialogowym potwierdzenia, aby usunąć pulpit nawigacyjny. W przypadku udostępnionego pulpitu nawigacyjnego w oknie dialogowym potwierdzenia zaznacz pole wyboru, aby potwierdzić, że opublikowany pulpit nawigacyjny nie będzie już widoczny dla innych użytkowników. Następnie wybierz przycisk **OK**.

    ![Zrzut ekranu przedstawiający potwierdzenie usunięcia](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Następne kroki

* [Udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu Access Control opartej na rolach](azure-portal-dashboard-share-access.md)
* [Programowe tworzenie pulpitów nawigacyjnych platformy Azure](azure-portal-dashboards-create-programmatically.md)
