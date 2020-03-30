---
title: Omówienie witryny Azure Portal | Microsoft Docs
description: Portal Azure to graficzny interfejs użytkownika, którego można używać do zarządzania usługami platformy Azure. Dowiedz się, jak nawigować i znajdować zasoby w witrynie Azure portal.
services: azure-portal
keywords: portal
author: mgblythe
ms.author: mblythe
ms.date: 12/20/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5e02e791185db3713c67b8ff97b8f7eebe9da77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310734"
---
# <a name="azure-portal-overview"></a>Azure Portal — omówienie

W tym artykule przedstawiono witrynę Azure portal, identyfikuje elementy strony portalu i ułatwia zapoznanie się z środowiska zarządzania witryną Azure portal.

## <a name="what-is-the-azure-portal"></a>Co to jest witryna Azure Portal?

Witryna Azure Portal to ujednolicona, oparta na sieci Web konsola, która stanowi alternatywę dla narzędzi wiersza polecenia. Za pomocą witryny Azure Portal możesz zarządzać subskrypcją platformy Azure przy użyciu graficznego interfejsu użytkownika. Możesz tworzyć, zarządzać i monitorować wszystko, od prostych aplikacji internetowych po złożone wdrożenia w chmurze. Tworzenie niestandardowych pulpitów nawigacyjnych dla zorganizowanego widoku zasobów. Skonfiguruj opcje ułatwień dostępu, aby zapewnić optymalne działanie.

Portal Azure został zaprojektowany z myślą o odporności i ciągłej dostępności. Ma obecność w każdym centrum danych platformy Azure. Ta konfiguracja sprawia, że portal Azure jest odporny na błędy poszczególnych centrów danych i pozwala uniknąć spowolnienia sieci, będąc blisko użytkowników. Portal Azure jest aktualizowany w sposób ciągły i nie wymaga żadnych przestojów w działaniach konserwacyjnych.

## <a name="azure-portal-menu"></a>Menu portalu platformy Azure

Można wybrać tryb domyślny dla menu portalu. Może być zadokowany lub może działać jako panel wysuwu.

Gdy menu portalu jest w trybie wysuwu, jest ukryte, dopóki nie będzie potrzebne. Wybierz ikonę menu, aby otworzyć lub zamknąć menu.

![Menu portalu platformy Azure w trybie wysuwu](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

Jeśli wybierzesz tryb zadokowany dla menu portalu, będzie on zawsze widoczny. Można zwinąć menu, aby zapewnić więcej miejsca pracy.

![Menu portalu platformy Azure w trybie zadokowanym](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Strona główna platformy Azure

Jako nowy subskrybent usług platformy Azure pierwszą rzeczą, którą widzisz po [zalogowaniu się do portalu,](https://portal.azure.com) jest **usługa Azure Home.** Ta strona kompiluje zasoby, które pomogą Ci w pełni wykorzystać subskrypcję platformy Azure. Zamieściliśmy linki do bezpłatnych kursów online, dokumentacji, podstawowych usług i przydatnych witryn, aby zachować aktualną i zarządzać zmianami w Organizacji. Aby uzyskać szybki i łatwy dostęp do pracy w toku, pokazujemy również listę ostatnio odwiedzonych zasobów. Nie można dostosować tej strony, ale możesz wybrać, czy widok domyślny ma być widoczny dla **platformy Azure Home** czy Pulpit nawigacyjny platformy **Azure.** Przy pierwszym logowaniu w górnej części strony znajduje się monit, w którym możesz zapisać swoje preferencje.

![Zrzut ekranu przedstawiający selektor widoku domyślnego](./media/azure-portal-overview/azure-portal-default-view.png)

Zarówno menu portalu platformy Azure, jak i domyślny widok platformy Azure można zmienić w **ustawieniach portalu**. Jeśli zmienisz wybór, zmiana zostanie natychmiast zastosowana.

![Zrzut ekranu przedstawiający selektor widoku domyślnego](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Pulpit nawigacyjny platformy Azure

Pulpity nawigacyjne zapewniają skoncentrowany widok zasobów w ramach subskrypcji, które są dla Ciebie najważniejsze. Na początku nadajemy Ci domyślny pulpit nawigacyjny. Ten pulpit nawigacyjny można dostosować, aby zasoby, których często używasz, były często używane w jednym widoku. Wszelkie zmiany wprowadzone w widoku domyślnym mają wpływ tylko na środowisko. Można jednak tworzyć dodatkowe pulpity nawigacyjne do własnego użytku lub publikować dostosowane pulpity nawigacyjne i udostępniać je innym użytkownikom w organizacji. Aby uzyskać więcej informacji, zobacz [Tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure portal](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Poruszanie się po portalu

Warto zrozumieć podstawowy układ portalu i sposób interakcji z nim. W tym miejscu przedstawimy składniki interfejsu użytkownika i niektóre terminologii, której używamy do udzielania instrukcji. Aby uzyskać bardziej szczegółowe informacje na temat portalu, zobacz lekcję kursu [Poruszanie się po portalu](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal).

Menu portalu Azure i nagłówek strony są elementami globalnymi, które są zawsze obecne. Te trwałe funkcje są "powłoki" dla interfejsu użytkownika skojarzonego z poszczególnych usług lub funkcji i nagłówek zapewnia dostęp do kontroli globalnych. Strona konfiguracji (czasami określane jako "blok") dla zasobu może również mieć menu zasobów, które ułatwia przechodzenie między funkcjami.

Poniższy rysunek etykietuje podstawowe elementy witryny Azure portal, z których każdy jest opisany w poniższej tabeli.

![Zrzut ekranu przedstawiający pełnoekranowy widok portalu i klucz do elementów interfejsu użytkownika](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![Zrzut ekranu przedstawiający rozwinięte menu portalu](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|Klucz|Opis
|:---:|---|
|1|Nagłówek strony. Pojawia się u góry każdej strony portalu i zawiera elementy globalne.|
|2| Wyszukiwanie globalne. Użyj paska wyszukiwania, aby szybko znaleźć określony zasób, usługę lub dokumentację.|
|3|Globalne kontrole. Podobnie jak wszystkie elementy globalne, te funkcje utrzymują się w portalu i obejmują: Cloud Shell, filtr subskrypcji, powiadomienia, ustawienia portalu, pomoc i pomoc techniczną oraz wyślij nam opinię.|
|4|Twoje konto. Wyświetl informacje o swoim koncie, przełącz katalogi, wyloguj się lub zaloguj się przy innym koncie.|
|5|Menu portalu. Menu portalu jest elementem globalnym, który ułatwia przechodzenie między usługami. Czasami określane jako pasek boczny, tryb menu portalu można zmienić w **ustawieniach portalu**.|
|6|Menu zasobów. Wiele usług zawiera menu zasobów ułatwiające zarządzanie usługą. Może pojawić się ten element, o którym mowa w lewym okienku.|
|7|Pasek poleceń. Formanty na pasku poleceń są kontekstowe do bieżącego fokusu.|
|8|Okienko robocze.  Wyświetla szczegółowe informacje o zasobie, który jest aktualnie w centrum uwagi.|
|9|Breadcrumb. Za pomocą łączy strony napełnienia można przenieść poziom w przepływie pracy.|
|10|Kontrola wzorca, aby utworzyć nowy zasób w bieżącej subskrypcji. Rozwiń lub otwórz menu portalu, aby znaleźć **+ Utwórz zasób**. Wyszukaj lub przejrzyj witrynę Azure Marketplace pod kątem typu zasobu, który chcesz utworzyć.|
|11|Twoja lista ulubionych. Zobacz [Dodawanie, usuwanie i sortowanie ulubionych,](../azure-portal/azure-portal-add-remove-sort-favorites.md) aby dowiedzieć się, jak dostosować listę.|

## <a name="get-started-with-services"></a>Wprowadzenie do usług

Jeśli jesteś nowym subskrybentem, musisz utworzyć zasób, zanim będzie cokolwiek zarządzać. Wybierz **+ Utwórz zasób,** aby wyświetlić usługi dostępne w portalu Azure Marketplace. Aplikacje i usługi od setek dostawców znajdziesz tutaj, wszystkie certyfikowane do uruchamiania na platformie Azure.

Wstępnie zapełniliśmy Twoje ulubione na pasku bocznym łączami do powszechnie używanych usług.  Aby wyświetlić wszystkie dostępne usługi, wybierz **pozycję Wszystkie usługi** z paska bocznego.

> [!TIP]
> Najszybszym sposobem znalezienia zasobu, usługi lub dokumentacji jest użycie *wyszukiwania* w nagłówku globalnym. Użyj linków do stron nasadowych, aby wrócić do poprzednich stron.
>
Obejrzyj ten klip wideo, aby zobaczyć prezentację na temat używania wyszukiwania globalnego w witrynie Azure portal.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Jak korzystać z wyszukiwania globalnego w witrynie Azure portal](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o tym, gdzie uruchomić witrynę Azure portal w [obsługiwanych przeglądarkach i urządzeniach](../azure-portal/azure-portal-supported-browsers-devices.md)
* Pozostawanie w kontakcie w podróży dzięki [aplikacji mobilnej Platformy Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* Wbudowane i skonfiguruj środowisko chmurowe za pomocą Centrum szybki startu [platformy Azure](../azure-portal/azure-portal-quickstart-center.md)
