---
title: Ustawianie preferencji witryny Azure portal | Dokumenty firmy Microsoft
description: Ustawienia domyślne witryny Azure Portal można zmienić, aby spełnić własne preferencje. Ustawienia obejmują nieaktywny limit czasu sesji, widok domyślny, tryb menu, kontrast, motyw, powiadomienia oraz formaty językowe i regionalne
services: azure-portal
keywords: ustawienia, limit czasu, język, regionalny
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7bcfdeec832b14eb53c0dab6cb2f53970d85c804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310683"
---
# <a name="set-your-azure-portal-preferences"></a>Ustawianie preferencji witryny Azure Portal

Można zmienić ustawienia domyślne witryny Azure portal, aby spełnić własne preferencje. Można zmienić każde z poniższych ustawień:

* [Nieaktywny limit czasu sesji](#change-the-idle-duration-for-inactive-sign-out)
* [Widok domyślny](#choose-your-default-view)
* [Tryb menu portalu](#choose-a-portal-menu-mode)
* [Motyw kolorów i wysokiego kontrastu](#choose-a-theme)
* [Wyskakujące powiadomienia](#enable-or-disable-pop-up-notifications)
* [Język i format regionalny](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Zmienianie ogólnych ustawień portalu

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz **pozycję Ustawienia** z globalnego nagłówka strony.

    ![Zrzut ekranu przedstawiający globalne ikony nagłówka strony z wyróżnionymi ustawieniami](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>Zmienianie czasu bezczynnego dla nieaktywnego wylogowania

Ustawienie limitu czasu braku aktywności pomaga chronić zasoby przed nieautoryzowanym dostępem, jeśli zapomnisz zabezpieczyć stację roboczą. Po przejściu na chwilę bezczynności automatycznie wylogujesz się z sesji witryny Azure portal.

Wybierz pozycję Rozwijana w obszarze **Wyloguj mnie, gdy jest nieaktywna**. Wybierz czas trwania, po którym sesja portalu Azure jest wylogowywane, jeśli jesteś bezczynny.

   ![Zrzut ekranu przedstawiający ustawienia portalu z wyróżnionymi nieaktywnymi ustawieniami limitu czasu](./media/set-preferences/inactive-signout-user.png)

Zmiana zostanie zapisana automatycznie. Jeśli jesteś bezczynny, twoja sesja portalu Azure wyloguje się po ustawionym czasie trwania.

To ustawienie może być również wprowadzone przez administratora na poziomie katalogu, aby wymusić maksymalny czas bezczynności. Jeśli administrator dokonał ustawienia limitu czasu na poziomie katalogu, nadal można ustawić własny nieaktywny czas wylogowania. Wybierz ustawienie czasu, które jest mniejsze niż ustawione na poziomie katalogu.

Jeśli administrator włączył zasady limitu czasu braku aktywności, zaznacz pole wyboru **Zastąpozjanie zasady limitu czasu braku aktywności katalogu.** Ustaw przedział czasu, który jest mniejszy niż ustawienie zasad.

   ![Wyróżniony zrzut ekranu przedstawiający ustawienia portalu z wyróżnionym ustawieniem zasad limitu czasu braku aktywności katalogu](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Jeśli jesteś administratorem i chcesz wymusić nieaktywne ustawienie limitu czasu dla wszystkich użytkowników portalu Azure, zobacz [Ustawianie limitu czasu braku aktywności na poziomie katalogu dla użytkowników witryny Azure Portal](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>Wybieranie widoku domyślnego 

Można zmienić stronę, która otwiera się domyślnie, gdy logowanie do witryny Azure portal.

   ![Zrzut ekranu przedstawiający ustawienia portalu Azure z wyróżnionym widokiem domyślnym](./media/set-preferences/default-view.png)

Ustawienie widoku domyślnego określa, który widok portalu Azure jest wyświetlany po zalogowaniu się. Domyślnie można otworzyć usługę Azure Home lub widok pulpitu nawigacyjnego.

* **Nie** można dostosować domu.  Wyświetla skróty do popularnych usług platformy Azure i wyświetla listę zasobów, które zostały ostatnio użyte. Zapewniamy również przydatne łącza do zasobów, takich jak Microsoft Learn i plan działania platformy Azure.
* Pulpity nawigacyjne można dostosować, aby utworzyć obszar roboczy zaprojektowany specjalnie dla Ciebie. Na przykład można utworzyć pulpit nawigacyjny, który koncentruje się na projekcie, zadaniu lub roli. Jeśli wybierzesz **pulpit nawigacyjny,** widok domyślny przejdzie do ostatnio używanego pulpitu nawigacyjnego.

### <a name="choose-a-portal-menu-mode"></a>Wybieranie trybu menu portalu

Domyślny tryb menu portalu określa ilość miejsca, które menu portalu zajmuje na stronie.

* Gdy menu portalu jest w trybie **wysuwu,** jest ukryte, dopóki nie będzie potrzebne. Wybierz ikonę menu, aby otworzyć lub zamknąć menu.
* Jeśli **wybierzesz tryb zadokowany** dla menu portalu, jest on zawsze widoczny. Można zwinąć menu, aby zapewnić więcej miejsca pracy. 

### <a name="choose-a-theme"></a>Wybierz motyw

Wybranego motywu wpływa na kolory tła i czcionki, które pojawiają się w witrynie Azure portal. Można wybrać jeden z czterech wstępnie ustawionych motywów kolorystykowych. Wybierz każdą miniaturę, aby znaleźć motyw, który najbardziej Ci odpowiada.

   ![Zrzut ekranu przedstawiający ustawienia witryny Azure portal z wyróżnionymi motywami](./media/set-preferences/theme.png)

Zamiast tego można wybrać jeden z motywów o wysokim kontraście. Ustawienia wysokiego kontrastu ułatwiają czytanie witryny Azure portal dla użytkowników niedowidzących i zastępują wszystkie inne opcje motywu. Aby uzyskać więcej informacji, zobacz [Włączanie motywu o wysokim kontraście lub zmienianie motywu](azure-portal-change-theme-high-contrast.md).

### <a name="enable-or-disable-pop-up-notifications"></a>Włączanie lub wyłączanie wyskakujących powiadomień

Powiadomienia są komunikatami systemowymi związanymi z bieżącą sesją. Dostarczają one informacji, takich jak bieżące saldo kredytu, gdy zasoby, które właśnie utworzyłeś, stają się dostępne, lub na przykład potwierdzają ostatnią akcję. Gdy wyskakujące powiadomienia są włączone, wiadomości są wyświetlane krótko w górnym rogu ekranu. 

Aby włączyć lub wyłączyć wyskakujące powiadomienia, zaznacz lub odznacz pole wyboru **Włącz wyskakujące powiadomienia.**

   ![Zrzut ekranu przedstawiający ustawienia portalu Azure z wyróżnionymi powiadomieniami podręcznymi](./media/set-preferences/popup-notifications.png)

Aby odczytać wszystkie powiadomienia otrzymane podczas bieżącej sesji, wybierz **powiadomienia** z nagłówka globalnego.

   ![Zrzut ekranu przedstawiający globalny nagłówek witryny Azure portal z wyróżnionymi powiadomieniami](./media/set-preferences/read-notifications.png)

Jeśli chcesz odczytać powiadomienia z poprzednich sesji, poszukaj zdarzeń w dzienniku aktywności. Aby dowiedzieć się więcej, przeczytaj [artykuł Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log-view).

### <a name="settings-under-useful-links"></a>Ustawienia pod przydatnymi linkami

Jeśli wprowadzono zmiany w ustawieniach portalu Azure i chcesz je odrzucić, wybierz pozycję **Przywróć ustawienia domyślne**. Wszelkie zmiany wprowadzone w ustawieniach portalu zostaną utracone. Ta opcja nie ma wpływu na dostosowania pulpitu nawigacyjnego.

Aby uzyskać więcej informacji o **eksportowanie wszystkich ustawień** lub **Usuwanie wszystkich ustawień i prywatnych pulpitów nawigacyjnych,** zobacz [Eksportowanie lub usuwanie ustawień użytkownika](azure-portal-export-delete-settings.md).

## <a name="change-language-and-regional-settings"></a>Zmienianie ustawień językowych i regionalnych

Istnieją dwa ustawienia, które kontrolują sposób wyświetlanie tekstu w witrynie Azure Portal. Ustawienie **Język** steruje językiem, który jest widoczny dla tekstu w witrynie Azure Portal. **Format regionalny** steruje sposobem, w jaki są wyświetlane daty, godziny, liczby i waluty.

Aby zmienić język, który jest używany w witrynie Azure portal, użyj listy rozwijanej, aby wybrać z listy dostępnych języków.

Wybór formatu regionalnego zmienia się, aby wyświetlić opcje regionalne tylko dla wybranego języka. Aby zmienić ten wybór automatyczny, użyj listy rozwijanej, aby wybrać odpowiedni format regionalny.

Jeśli na przykład jako język wybierzesz angielski, a następnie wybierzesz Stany Zjednoczone jako format regionalny, waluta będzie wyświetlana w dolarach amerykańskich. Jeśli wybierzesz angielski jako język, a następnie wybierzesz Europę jako format regionalny, waluta jest wyświetlana w euro.

Wybierz **pozycję Zastosuj,** aby zaktualizować ustawienia języka i formatu regionalnego.

   ![Zrzut ekranu przedstawiający ustawienia języka i formatu regionalnego](./media/set-preferences/language.png)

>[!NOTE]
>Te ustawienia języka i regionalne mają wpływ tylko na witrynę Azure portal. Łącza dokumentacji otwierane w nowej karcie lub oknie będą używać ustawień języka przeglądarki do określenia języka do wyświetlenia.
>

## <a name="next-steps"></a>Następne kroki

* [Tworzenie i udostępnianie niestandardowych pulpitów nawigacyjnych](azure-portal-dashboards.md)
* [Seria instruktażowych wideo dla witryny Azure Portal](azure-portal-video-series.md)
