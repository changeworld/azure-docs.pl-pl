---
title: Omówienie witryny Azure Portal | Microsoft Docs
description: Dowiedz się, jak przejść do witryny Azure portal i użyć go do zarządzania usługami
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 05/24/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: de04b461d8e2d2dce9a88052a86fbe5d163fafec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244199"
---
# <a name="azure-portal-overview"></a>Azure Portal — omówienie

Ten artykuł wprowadza witryny Azure portal, identyfikuje elementy strony portalu i pomoże Ci zapoznać się ze środowiskiem Azure portal management.

## <a name="what-is-the-azure-portal"></a>Co to jest witryna Azure portal?

Azure portal to oparte na sieci web, ujednoliconej konsoli, która stanowi alternatywę dla narzędzia wiersza polecenia. W witrynie Azure portal można zarządzać subskrypcją platformy Azure przy użyciu graficznego interfejsu użytkownika. Można tworzyć, zarządzać i monitorować wszystko — od prostych aplikacji sieci web do wdrożeń w chmurze złożonych, tworzyć niestandardowe pulpity nawigacyjne dla zorganizowany widok zasobów i skonfigurować opcje ułatwień dostępu, aby uzyskać najlepsze wyniki.

Witryna Azure portal jest przeznaczona dla odporności i stałej dostępności. Ma będzie ona widoczna w każdym centrum danych platformy Azure, umożliwiając odporna na awarie pojedynczych centrum danych i unika również slow-downs sieci dzięki blisko użytkowników. Witryna Azure portal stale aktualizuje i nie wymaga przestojów konserwacyjne.

## <a name="azure-home"></a>Azure Home

Jako nowych subskrybentów do usług platformy Azure, najpierw zostanie wyświetlony po [Zaloguj się do portalu](https://portal.azure.com) jest **Azure — strona główna**. Kompiluje tej strony, zasoby, które pomogą w pełni wykorzystać subskrypcję platformy Azure. Wprowadzono linki do bezpłatnych kursów online, dokumentacja, podstawowych usług i przydatne witryn zostajesz bieżące i zarządzanie Zmień dla Twojej organizacji. Aby uzyskać szybki i łatwy dostęp do pracy w toku pokazujemy, również listy ostatnio odwiedzonych zasobów. Nie można dostosować tę stronę, ale można wybrać, czy wyświetlane są **Azure — strona główna** lub **pulpit nawigacyjny platformy Azure** jako domyślny. Podczas pierwszego logowania, Brak wiersza w górnej części strony, w którym można zapisać swoje preferencje.

![Zrzut ekranu przedstawiający domyślny widok selektora](./media/azure-portal-overview/azure-portal-default-view.png)

## <a name="azure-dashboard"></a>Pulpit nawigacyjny platformy Azure

Pulpity nawigacyjne zapewniają wąsko zdefiniowany widok zasobów w ramach subskrypcji, które mają znaczenie najbardziej dla Ciebie. Firma Microsoft udzielono domyślny pulpit nawigacyjny, aby rozpocząć pracę. Możesz dostosować pulpit nawigacyjny, aby przenieść zasoby, których używasz najczęściej w jednym widoku. Wszelkie zmiany wprowadzone do widoku domyślnego wpływa na środowisko tylko. Można jednak tworzyć dodatkowe pulpity nawigacyjne na własny użytek lub opublikować niestandardowe pulpity nawigacyjne i udostępniać je innym użytkownikom w organizacji. Aby uzyskać więcej informacji, zobacz [tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure portal](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Poruszanie się w portalu

Jest to pomocne w zrozumieniu podstawowy układ portalu oraz jak korzystać z niego. W tym miejscu wprowadzimy, składniki interfejsu użytkownika i niektórych terminów, których używamy wskazówki. Aby uzyskać bardziej szczegółowy przewodnik dotyczący portalu, zobacz lekcji kurs [przejdź do portalu](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal).

Azure portal paska bocznego i nagłówek strony są elementy globalne, które są zawsze obecne. Te funkcje trwałe to "powłoka" dla interfejsu użytkownika skojarzone z każdym poszczególnych usług lub funkcji i nagłówek zapewnia dostęp do globalnych kontrolek. Na stronie konfiguracji (czasami określane jako "bloku") zasobu może być konieczne w okienku po lewej stronie, które ułatwiają przejście między funkcjami.

Na poniższej ilustracji etykiet podstawowych elementów w witrynie Azure Portal, każdy z nich są opisane w poniższej tabeli.

![Zrzut ekranu przedstawiający portal widokiem pełnoekranowym i klucz do elementów interfejsu użytkownika](./media/azure-portal-overview/azure-portal-fullscreen-map.png)

|Klucz|Opis
|:---:|---|
|1|Nagłówek strony. Pojawi się u góry każdej strony portalu i przechowuje elementy globalne.|
|2| Wyszukiwanie globalne. Aby szybko znaleźć określonego zasobu, usługi lub dokumentacja za pomocą paska wyszukiwania.|
|3|Globalnych kontrolek. Podobnie jak wszystkie elementy globalne te funkcje ulegają zmianie portalu i obejmują: Cloud Shell, filtr subskrypcji, powiadomienia, ustawienia portalu, Pomoc i obsługa techniczna, a następnie Prześlij nam swoją opinię.|
|4|Twoje konto. Wyświetl informacje o Twoim koncie, Przełącz katalogi, wyloguj się lub zaloguj się przy użyciu innego konta.|
|5|Pasku bocznym. Pasek boczny jest element globalny, który ułatwia nawigowanie między usługami. Aby uaktywnić więcej pracy w okienku można zwinąć pasku bocznym.|
|6|Formant główny do utworzenia nowego zasobu w bieżącej subskrypcji. Szukaj lub Przeglądaj w portalu Azure Marketplace dla typu zasobu, który chcesz utworzyć.|
|7|Listy Ulubione. Dodawanie lub usuwanie ulubionych zawartość z **wszystkich usług** strony.|
|8|W okienku po lewej stronie. Wiele usług obejmują menu po lewej, ułatwiają zarządzanie usługą.|
|9|Pasek poleceń. Formanty paska poleceń są kontekstowych na bieżącym skupiać się.|
|10|Łącza do stron nadrzędnych. Łącza nawigacji można użyć w celu przeniesienia na poziom z powrotem w przepływie pracy.|
|11|Okienko pracy.  Wyświetla szczegółowe informacje o zasobie, który jest obecnie w trybie koncentracji uwagi.|

## <a name="get-started-with-services"></a>Rozpocznij pracę z usługami

W przypadku nowych subskrybentów, musisz utworzyć zasób, zanim coś do zarządzania. Wybierz **+ Utwórz zasób** do wyświetlenia wszystkich usług, które są dostępne w witrynie Azure Marketplace. Znajdziesz, aplikacji i usług z setek dostawców, w tym miejscu wszystkie z certyfikatem działające na platformie Azure.

Wstępnie wypełniona Ulubione na pasku bocznym, wraz z łączami do często używanych usług.  Aby wyświetlić wszystkie dostępne usługi, wybierz **wszystkich usług** z paska bocznego.

> [!TIP]
> Najszybszym sposobem znalezienia zasobów, usługi lub dokumentacja jest użycie *wyszukiwania* w nagłówku globalnego. Użyj linków nawigacji wrócić do poprzedniej strony.
>

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o tym, gdzie można uruchamiać z witryny Azure portal w [obsługiwanych przeglądarek i urządzeń](../azure-portal/azure-portal-supported-browsers-devices.md)

* Pozostań w kontakcie dzięki [aplikacja mobilna platformy Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
