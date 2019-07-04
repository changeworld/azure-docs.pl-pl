---
title: Tworzenie i udostępnianie pulpitów nawigacyjnych portalu platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia, dostosowywania, publikowanie i udostępnianie pulpitów nawigacyjnych w witrynie Azure portal.
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
ms.date: 07/01/2019
ms.author: kfollis
ms.openlocfilehash: 8dd1349ca9ab62484eb6693291e3b869ff079dc1
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537205"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure portal

Pulpity nawigacyjne umożliwiają tworzenie widoku ukierunkowanie i zorganizowane w witrynie Azure portal zasobów w chmurze. Używaj pulpitów nawigacyjnych jako obszar roboczy, w którym można szybko uruchomić zadań związanych z typowymi operacjami i monitorowanie zasobów.  Twórz niestandardowe pulpity nawigacyjne w oparciu o projekty, zadania lub rolę użytkownika, na przykład.  Azure portal udostępnia domyślny pulpit nawigacyjny jako punktu wyjścia. Można edytować domyślny pulpit nawigacyjny, tworzyć i dostosowywać dodatkowe pulpity nawigacyjne i publikowanie i udostępnianie pulpitów nawigacyjnych, aby udostępnić je innym użytkownikom. W tym artykule opisano, jak utworzyć nowy pulpit nawigacyjny, dostosowywania interfejsu, publikowanie i udostępnianie pulpitów nawigacyjnych.

## <a name="create-a-new-dashboard"></a>Tworzenie nowego pulpitu nawigacyjnego

W tym przykładzie utworzymy nowego, prywatnego pulpitu nawigacyjnego i przypiszemy nazwę. Wykonaj następujące kroki, aby rozpocząć pracę:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz **pulpit nawigacyjny** z górnej sekcji lewym pasku bocznym. Widok domyślny może być już ustawione do pulpitu nawigacyjnego.
1. Wybierz **+ nowy pulpit nawigacyjny**.

    ![Zrzut ekranu przedstawiający domyślny pulpit nawigacyjny](./media/azure-portal-dashboards/dashboard-new.png)

4. Ta akcja powoduje otwarcie **Galeria kafelków**, w którym należy wybrać kafelków i pustą siatką, gdzie będzie rozmieszczać Kafelki.

    ![Zrzut ekranu przedstawiający Galeria kafelków i pustej siatki](./media/azure-portal-dashboards/dashboard-name.png)

5. Wybierz **Mój pulpit nawigacyjny** tekst na pulpicie nawigacyjnym etykiety, a następnie wprowadź nazwę, która pomoże Ci łatwo zidentyfikować niestandardowego pulpitu nawigacyjnego.
1. Wybierz **Zakończono dostosowywanie** w nagłówku strony, aby zamknąć tryb edycji.

Wyświetl pulpit nawigacyjny zawiera obecnie pustego pulpitu nawigacyjnego. Wybierz listę rozwijaną obok nazwy pulpitu nawigacyjnego, aby wyświetlić pulpity nawigacyjne dostępne dla Ciebie — na liście mogą znajdować się pulpity nawigacyjne, które utworzone i udostępnione innym użytkownikom.

## <a name="edit-a-dashboard"></a>Edytuj pulpit nawigacyjny

Teraz możemy edytować pulpit nawigacyjny, aby dodać, zmienianie rozmiaru i położenia Kafelki, które reprezentują zasoby platformy Azure.

### <a name="add-tiles"></a>Dodaj Kafelki

Aby dodać Kafelki do pulpitu nawigacyjnego, wykonaj następujące kroki:
1. Wybierz ![ikona edycji](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Edytuj** w nagłówku strony.

    ![Zrzut ekranu przedstawiający pulpit nawigacyjny, wyróżnianie edycji](./media/azure-portal-dashboards/dashboard-edit.png)

2. Przeglądaj **Galeria kafelków** lub użyj pola wyszukiwania, aby znaleźć kafelka.
1. Wybierz **Dodaj** do automatycznego dodawania kafelka do pulpitu nawigacyjnego z domyślnym rozmiarem i lokalizacją. Lub przeciągnij Kafelek do siatki i umieść go w miejscu.

Wiele stron zasobów (znany także jako "bloków") obejmują ikonę pinezki na pasku poleceń. Wybranie ikony kafelka reprezentujący źródłowej strony jest przypięta do pulpitu nawigacyjnego, który jest obecnie aktywny. Ta metoda jest alternatywny sposób, aby dodać Kafelki do pulpitu nawigacyjnego.

![Zrzut ekranu przedstawiający stronę paska poleceń z ikonę pinezki](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> Jeśli pracujesz z więcej niż jednej z organizacji, należy dodać **tożsamości organizacji** kafelka do pulpitu nawigacyjnego wyraźnego wyświetlania której organizacji zasoby należą do.
>
>
### <a name="resize-or-rearrange-tiles"></a>Zmień rozmiar oraz zmianę układu kafelków
Aby zmienić rozmiar kafelka lub zmienić układ kafelków na pulpicie nawigacyjnym, wykonaj następujące kroki:

1. Wybierz ![ikona edycji](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Edytuj** w nagłówku strony.
1. W prawym górnym rogu kafelka, wybierz z menu kontekstowego. Następnie wybierz rozmiar kafelka. Kafelki, które obsługiwać dowolne rozmiary także "uchwytu" w prawym dolnym rogu, umożliwiająca przeciągnij Kafelek do żądanego rozmiaru.

   ![Zrzut ekranu przedstawiający pulpit nawigacyjny z menu Rozmiar kafelka, otwórz](./media/azure-portal-dashboards/dashboard-tile-resize.png)

3. Wybierz Kafelek i przeciągnij go do nowej lokalizacji w siatce, aby zorganizować pulpitu nawigacyjnego.

### <a name="additional-tile-configuration"></a>Kafelek dodatkowych konfiguracji

Niektóre Kafelki może wymagać większej liczby czynności konfiguracyjnych pokazanie żądane informacje. Na przykład **wykres metryk** Kafelek ma być skonfigurowane tak, aby wyświetlić metryki z **usługi Azure Monitor**. Można również dostosować dane kafelka, aby zastąpić ustawienia czasu domyślnego pulpitu nawigacyjnego.

Dowolny Kafelek, który musi być skonfigurowane Wyświetla **Kafelek Konfigurowanie** Baner, aż dostosujesz kafelka. Wybierz ten transparent, a następnie wykonaj wymagane ustawienia.

![Zrzut ekranu przedstawiający Kafelek, który wymaga konfiguracji](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Fragment kodu markdown umożliwia wyświetlanie zawartości statycznej, niestandardowe na pulpicie nawigacyjnym. To podstawowe instrukcje, obraz, zestaw hiperłączy lub nawet informacje kontaktowe. Aby uzyskać więcej informacji o korzystaniu z kafelkiem markdown, zobacz [Użyj kafelka niestandardowymi elementami języka markdown](azure-portal-markdown-tile.md).
>
>
### <a name="customize-tile-data"></a>Dostosuj dane kafelka

Dane na pulpicie nawigacyjnym automatycznie aktywnością w ciągu ostatnich 24 godzin. Aby wyświetlić inny okres dla właśnie tego kafelka, wykonaj następujące kroki:

1. Wybierz ![ikonę filtru](./media/azure-portal-dashboards/dashboard-filter.png) ikonę filtru w lewym górnym rogu kafelka i wybierz **Dostosuj dane kafelka** z menu kontekstowego.

   ![Zrzut ekranu przedstawiający menu kontekstowe kafelka](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

2. Zaznacz pole wyboru, aby **Zastąp ustawienia czasu pulpitu nawigacyjnego na poziomie kafelka**.

   ![Zrzut ekranu okna dialogowego, aby skonfigurować ustawienia czasu kafelka](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

3. Wybierz przedział czasu do wyświetlenia dla tego kafelka. Można wybrać z ostatnich 30 minut do ostatnich 30 dni lub zdefiniować niestandardowy zakres.
1. Wybierz stopień szczegółowości czasu do wyświetlenia. Możesz wyświetlić dowolne miejsce w przyrostach minutowych jeden do jednego miesiąca.
1. Wybierz przycisk **Zastosuj**.

## <a name="delete-a-tile"></a>Usuwanie kafelka

Aby usunąć Kafelek z pulpitu nawigacyjnego, wykonaj następujące kroki:

* W prawym górnym rogu kafelka, wybierz menu kontekstowe, a następnie wybierz **Usuń z pulpitu nawigacyjnego**. Ewentualnie
* Wybierz ![ikona edycji](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Edytuj** aby przejść do trybu dostosowania. Umieść kursor w prawym górnym rogu kafelka, a następnie wybierz ![ikona usuwania](./media/azure-portal-dashboards/dashboard-delete-icon.png) Usuń ikonę, aby usunąć Kafelek na pulpicie nawigacyjnym.

   ![Zrzut ekranu przedstawiający sposób Kafelek z pulpitu nawigacyjnego](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Klonowanie pulpitu nawigacyjnego

Aby używać istniejącego pulpitu nawigacyjnego jako szablon dla nowego pulpitu nawigacyjnego, wykonaj następujące kroki:

1. Upewnij się, że widok pulpitu nawigacyjnego jest wyświetlany pulpit nawigacyjny, który chcesz skopiować.
1. W nagłówku strony wybierz ![ikona Klonuj](./media/azure-portal-dashboards/dashboard-clone.png) **klonowania**.
1. Kopię pulpitu nawigacyjnego o nazwie "klon *swoją nazwę pulpitu nawigacyjnego*" zostanie otwarta w trybie edycji. Użyj powyższych kroków w tym artykule, zmiana nazwy i dostosowywać pulpit nawigacyjny.

## <a name="publish-and-share-a-dashboard"></a>Publikowanie i udostępnianie pulpitu nawigacyjnego

Podczas tworzenia pulpitu nawigacyjnego jest domyślnie prywatny, co oznacza, że jesteś jedyną osobą, która mogli je zobaczyć. Aby udostępnić pulpity nawigacyjne do innych osób, można udostępnisz je innym użytkownikom. Najpierw musisz opublikować pulpitu nawigacyjnego jako zasób platformy Azure. Publikowanie i udostępnianie niestandardowego pulpitu nawigacyjnego, wykonaj następujące kroki:

1. Wybierz ![ikona udostępniania](./media/azure-portal-dashboards/dashboard-share-icon.png) **udostępnianie** w nagłówku strony. **Udostępniania i kontroli dostępu** zostanie wyświetlony formularz.
1. Sprawdź, czy nazwa poprawne pulpitu nawigacyjnego jest widoczne.
1. Wybierz **Nazwa subskrypcji**. Użytkownicy z dostępem do subskrypcji mogą używać udostępnionego pulpitu nawigacyjnego. Dostęp do zasobów, reprezentowane przez poszczególnych kafelków jest określany za pomocą kontroli dostępu opartej na rolach na platformie Azure.
1. Zaznacz pole wyboru, aby opublikować ten pulpit nawigacyjny w grupie zasobów "pulpity nawigacyjne" dla wybranej subskrypcji. Lub, wyczyść pole wyboru, a następnie opublikować do istniejącej grupy zasobów zamiast tego.
1. Wybierz lokalizację dla zasobów pulpitu nawigacyjnego. Zaleca się, że Znajdź pulpit nawigacyjny z innymi zasobami. Uwaga: Jeśli z istniejącej grupy zasobów, pulpit nawigacyjny znajduje się automatycznie z tą grupą zasobów.
1. Wybierz **publikowania**.

   ![Zrzut ekranu przedstawiający okno dialogowe publikowanie pulpitu nawigacyjnego](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>Ustawianie kontroli dostępu na udostępniony pulpit nawigacyjny

Pulpit nawigacyjny zostanie opublikowany, aby zarządzać, kto ma dostęp do pulpitu nawigacyjnego, wykonując następujące czynności:

1. W **udostępniania i kontroli dostępu** okienku wybierz **Zarządzanie użytkownikami**.

   ![Zrzut ekranu przedstawiający udostępnianie pulpitu nawigacyjnego i dostęp kontrolować okna dialogowego](./media/azure-portal-dashboards/dashboard-share-access-control.png)

2. **Kontroli dostępu** zostanie otwarta strona. Na tej stronie można przejrzeć poziom dostępu dla osób, lub Dodaj nowe przypisanie roli. Po dodaniu przypisania roli w tym miejscu chcesz udzielić uprawnień do pulpitu nawigacyjnego.

> [!NOTE]
> Kafelki są reprezentatywne widoki zasobów w Twojej organizacji. Dostęp do zasobów odbywa się za pośrednictwem przypisanie kontroli dostępu opartej na rolach i uprawnienia są dziedziczone z subskrypcji w dół do zasobu. Przyznawanie dostępu do pulpitu nawigacyjnego nie automatycznie przypisać uprawnienia do zasobów wyświetlane na pulpicie nawigacyjnym. Aby uzyskać więcej informacji na temat uprawnień do udostępnionych pulpitów nawigacyjnych i kontrola dostępu oparta na rolach do zasobów, zobacz [udostępnianie pulpitów nawigacyjnych przy użyciu kontroli dostępu opartej na rolach](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Otwórz udostępniony pulpit nawigacyjny

Aby znaleźć i otworzyć udostępnionego pulpitu nawigacyjnego, wykonaj następujące kroki:

1. Wybierz listę rozwijaną obok nazwy pulpitu nawigacyjnego.
1. Wybierz jedną z wyświetlonej listy pulpitów nawigacyjnych lub **Przeglądaj wszystkie pulpity nawigacyjne** Jeśli pulpit nawigacyjny, który chcesz otworzyć nie ma na liście.

   ![Zrzut ekranu przedstawiający menu wyboru pulpitu nawigacyjnego](./media/azure-portal-dashboards/dashboard-browse.png)

3. W **typu** pól, zaznacz **udostępnione pulpity nawigacyjne**.
1. Wybierz co najmniej jedną subskrypcję. Można również wprowadzić tekst do odfiltrowania pulpity nawigacyjne według nazwy.
1. Wybierz pulpit nawigacyjny z listy udostępnionych pulpitów nawigacyjnych.

## <a name="delete-a-dashboard"></a>Usuwanie pulpitu nawigacyjnego

Aby trwale usunąć prywatnych lub udostępnionego pulpitu nawigacyjnego, wykonaj następujące kroki:

1. Wybierz pulpit nawigacyjny, który chcesz usunąć z listy rozwijanej obok nazwy pulpitu nawigacyjnego.
1. Wybierz ![ikona usuwania](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Usuń** w nagłówku strony.
1. Prywatnego pulpitu nawigacyjnego, wybierz **OK** w oknie dialogowym potwierdzenia, aby usunąć pulpitu nawigacyjnego. Dla udostępnionego pulpitu nawigacyjnego, w oknie dialogowym potwierdzenia wybierz pole wyboru, aby upewnić się, że opublikowany pulpit nawigacyjny przestanie być widoczny dla innych. Następnie wybierz przycisk **OK**.

   ![Zrzut ekranu przedstawiający potwierdzenie usunięcia](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Kolejne kroki

* [Udostępnianie pulpitów nawigacyjnych przy użyciu kontroli dostępu opartej na rolach](azure-portal-dashboard-share-access.md)
* [Programowe tworzenie pulpitów nawigacyjnych platformy Azure](azure-portal-dashboards-create-programmatically.md)
