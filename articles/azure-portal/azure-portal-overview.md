---
title: Omówienie witryny Azure Portal | Microsoft Docs
description: Dowiedz się, jak nawigować w Azure Portal i używać jej do zarządzania usługami
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 05/24/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 6e176a8b16129cd35fc011e14fcb36038f7c0144
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000341"
---
# <a name="azure-portal-overview"></a>Azure Portal — omówienie

W tym artykule wprowadzono Azure Portal, identyfikują elementy strony portalu i ułatwiają zapoznanie się z możliwościami zarządzania Azure Portal.

## <a name="what-is-the-azure-portal"></a>Co to jest Azure Portal?

Azure Portal jest ujednoliconą, opartą na sieci Web konsolą, która zapewnia alternatywę dla narzędzi wiersza polecenia. Za pomocą Azure Portal możesz zarządzać subskrypcją platformy Azure przy użyciu graficznego interfejsu użytkownika. Możesz tworzyć, zarządzać i monitorować wszystko z prostych aplikacji sieci Web w złożonych wdrożeniach w chmurze, tworzyć niestandardowe pulpity nawigacyjne na potrzeby uporządkowanego widoku zasobów i konfigurować opcje ułatwień dostępu w celu uzyskania najlepszego środowiska.

Azure Portal jest zaprojektowana pod kątem odporności i ciągłej dostępności. Jest ona dostępna w każdym centrum danych platformy Azure, dzięki czemu jest to odporne na awarie poszczególnych centrów danych, a także pozwala uniknąć spowolnienia pracy w sieci w pobliżu użytkowników. Azure Portal są stale aktualizowane i nie wymagają przestojów w przypadku działań konserwacyjnych.

## <a name="azure-home"></a>Strona główna platformy Azure

Jako nowy subskrybent usług platformy Azure pierwszy element widoczny po [zalogowaniu się do portalu](https://portal.azure.com) to **Strona główna platformy Azure**. Ta strona kompiluje zasoby, które pomogą w pełni wykorzystać subskrypcję platformy Azure. Dodaliśmy linki do bezpłatnych kursów online, dokumentacji, usług podstawowych i przydatnych witryn, które umożliwiają bieżące i zarządzanie zmianami w organizacji. Aby szybko i łatwo uzyskać dostęp do pracy w toku, pokazujemy również listę ostatnio odwiedzonych zasobów. Nie możesz dostosować tej strony, ale możesz wybrać, czy ma być wyświetlany **pulpit nawigacyjny** **platformy Azure** , czy Azure jako widok domyślny. Gdy logujesz się po raz pierwszy, w górnej części strony pojawi się monit, na którym możesz zapisać preferencję.

![Zrzut ekranu przedstawiający selektor widoku domyślnego](./media/azure-portal-overview/azure-portal-default-view.png)

## <a name="azure-dashboard"></a>Pulpit nawigacyjny platformy Azure

Pulpity nawigacyjne zapewniają skoncentrowany widok zasobów w ramach subskrypcji, które najlepiej odpowiadają. Udostępniono domyślny pulpit nawigacyjny, aby rozpocząć pracę. Możesz dostosować ten pulpit nawigacyjny, aby wykorzystać najczęściej używane zasoby w jednym widoku. Wszelkie zmiany wprowadzone w widoku domyślnym mają wpływ tylko na Twoje doświadczenie. Można jednak utworzyć dodatkowe pulpity nawigacyjne do własnych potrzeb lub opublikować dostosowane pulpity nawigacyjne i udostępnić je innym użytkownikom w organizacji. Aby uzyskać więcej informacji, zobacz [Tworzenie i udostępnianie pulpitów nawigacyjnych w Azure Portal](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Więcej informacji na temat portalu

Pomocne jest zrozumienie podstawowego układu portalu i sposobu korzystania z niego. W tym miejscu wprowadzimy składniki interfejsu użytkownika i część terminologii, której używamy w celu uzyskania instrukcji. Aby zapoznać się z bardziej szczegółowym przewodnikiem dotyczącym portalu, zobacz Lekcja kursu [nawigowanie po portalu](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal).

Azure Portal pasek boczny i nagłówek strony to globalne elementy, które zawsze są obecne. Te funkcje trwałe są "powłoką" dla interfejsu użytkownika skojarzonego z każdą pojedynczą usługą lub funkcją, a nagłówek zapewnia dostęp do kontrolek globalnych. Strona konfiguracji (czasami określana jako "blok") dla zasobu może być również w lewym okienku ułatwiającym przechodzenie między funkcjami.

Na rysunku poniżej przedstawiono podstawowe elementy Azure Portal, z których każdy jest opisany w poniższej tabeli.

![Zrzut ekranu przedstawiający widok portalu pełnoekranowego i klucz do elementów interfejsu użytkownika](./media/azure-portal-overview/azure-portal-fullscreen-map.png)

|Klucz|Opis
|:---:|---|
|1|Nagłówek strony. Pojawia się u góry każdej strony portalu i zawiera elementy globalne.|
|2| Wyszukiwanie globalne. Pasek wyszukiwania umożliwia szybkie znalezienie określonego zasobu, usługi lub dokumentacji.|
|3|Kontrolki globalne. Podobnie jak w przypadku wszystkich elementów globalnych, te funkcje są zachowywane w portalu i obejmują: Cloud Shell, filtr subskrypcji, powiadomienia, ustawienia portalu, pomoc i obsługa techniczna oraz Wyślij nam opinię.|
|4|Twoje konto. Wyświetl informacje o Twoim koncie, przełącz katalogi, Wyloguj się lub Zaloguj się przy użyciu innego konta.|
|5|—. Pasek boczny jest elementem globalnym, który ułatwia nawigowanie między usługami. Pasek boczny może być zwinięty, aby zwiększyć fokus okienka roboczego.|
|6|Formant główny służący do tworzenia nowego zasobu w bieżącej subskrypcji. Wyszukaj lub Przeglądaj witrynę Azure Marketplace dla typu zasobu, który chcesz utworzyć.|
|7|Lista ulubionych. Dodaj lub Usuń Ulubione ze strony **wszystkie usługi** .|
|8|Okienko po lewej stronie. Wiele usług obejmuje menu po lewej stronie, które ułatwia zarządzanie usługą.|
|9|Pasek poleceń. Formanty na pasku poleceń są kontekstowe dla bieżącego fokusu.|
|10|Stron. Linki do stron nadrzędnych umożliwiają przeniesienie poziomu w przepływie pracy.|
|11|Okienko robocze.  Wyświetla szczegóły zasobu, który jest aktualnie fokusem.|

## <a name="get-started-with-services"></a>Wprowadzenie do usług

Jeśli jesteś nowym subskrybentem, musisz utworzyć zasób, aby móc zarządzać nimi. Wybierz pozycję **+ Utwórz zasób** , aby wyświetlić usługi dostępne w portalu Azure Marketplace. W tym miejscu znajdziesz aplikacje i usługi z setek dostawców, a wszystkie certyfikowane do uruchamiania na platformie Azure.

Twoje ulubione na pasku bocznym są wstępnie wypełnione za pomocą linków do często używanych usług.  Aby wyświetlić wszystkie dostępne usługi, wybierz pozycję **wszystkie usługi** na pasku bocznym.

> [!TIP]
> Najszybszym sposobem znalezienia zasobu, usługi lub dokumentacji jest użycie *wyszukiwania* w nagłówku globalnym. Użyj linków do stron nadrzędnych, aby wrócić do poprzedniej strony.
>
Obejrzyj ten film wideo, aby zapoznać się z pokazem korzystania z wyszukiwania globalnego w Azure Portal.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Jak używać wyszukiwania globalnego w Azure Portal](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o tym, gdzie uruchamiać Azure Portal w [obsługiwanych przeglądarkach i urządzeniach](../azure-portal/azure-portal-supported-browsers-devices.md)

* Pozostawanie połączone z [aplikacja mobilna platformy Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
