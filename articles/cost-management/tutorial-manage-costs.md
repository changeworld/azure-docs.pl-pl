---
title: Samouczek — zarządzanie kosztami przy użyciu usługi Azure Cost Management | Microsoft Docs
description: Ten samouczek przedstawia zarządzanie kosztami metodą przydzielania kosztów oraz przy użyciu raportów przewidywanych kosztów i obciążeń zwrotnych.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: 16f86eace9b5848f263e0d0772db441a123f21ae
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-manage-costs-by-using-azure-cost-management"></a>Samouczek: zarządzanie kosztami przy użyciu usługi Azure Cost Management

Usługa Azure Cost Management umożliwia zarządzanie kosztami i tworzenie raportów przewidywanych kosztów przez przydzielanie kosztów na podstawie tagów. W procesie przydzielania kosztów do wykorzystywanych zasobów w chmurze przypisywane są koszty. Koszty są w pełni przydzielone, gdy wszystkie zasoby są skategoryzowane przy użyciu tagów. Po przydzieleniu kosztów można udostępnić użytkownikom analizę przewidywanych kosztów i obciążeń zwrotnych za pomocą pulpitów nawigacyjnych i raportów. Jednak na początku korzystania z usługi Cost Management wiele zasobów może nie mieć przypisanych tagów lub przypisanie im tagów może być niemożliwe.

Na przykład może być konieczne uzyskanie zwrotu kosztów inżynieryjnych. Potrzebna jest możliwość przedstawienia zespołowi inżynierów tego, że wymagane jest określona kwota — na podstawie kosztów zasobów. Można pokazać im raport dotyczący wszystkich wykorzystanych zasobów z tagiem *inżynieria*.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przydzielanie kosztów za pomocą tagów niestandardowych.
> * Tworzenie raportów przewidywanych kosztów i obciążeń zwrotnych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć konto platformy Azure.
- Musisz mieć rejestrację próbną lub płatną subskrypcję usługi Azure Cost Management.

## <a name="use-custom-tags-to-allocate-costs"></a>Przydzielanie kosztów za pomocą tagów niestandardowych

Podczas przydzielania kosztów w pierwszej kolejności należy zdefiniować zakres przy użyciu modelu kosztów. Model kosztów nie zmienia kosztów, lecz je dystrybuuje. Tworzenie modelu kosztów polega na segmentacji danych według jednostek kosztów, kont lub subskrypcji, a także według wielu tagów. Przykłady typowych tagów to kod rozliczeń, centrum kosztu lub nazwa grupy. Tagi ułatwiają też analizę przewidywanych kosztów i obciążeń zwrotnych do innych części organizacji.

Aby utworzyć niestandardowy model przydzielania kosztów, wybierz pozycję **Cost** (Koszty) &gt; **Cost Management** (Zarządzanie kosztami) &gt; **Cost Allocation 360°** (Alokacja kosztu 360°) w menu raportu.

![Wybieranie pozycji Cost Allocation 360 (Alokacja kosztu 360)](./media/tutorial-manage-costs/cost-allocation-360.png)

Na stronie **Cost Allocation 360** (Alokacja kosztu 360) wybierz pozycję **Add** (Dodaj), a następnie wprowadź nazwę i opis tworzonego modelu kosztów. Wybierz albo wszystkie konta, albo poszczególne konta. Jeśli chcesz użyć poszczególnych kont, możesz wybrać wiele kont od wielu dostawców usług w chmurze. Następnie kliknij pozycję **Categorization** (Kategoryzacja), aby wybrać odnalezione tagi kategoryzujące dane kosztów. Wybierz tagi (kategorie), które chcesz dołączyć do modelu. W poniższym przykładzie wybrany jest tag **Unit** (Jednostka).

![Przykład kategoryzacji modelu kosztów](./media/tutorial-manage-costs/cost-model01.png)



W przykładzie pokazano, że kwota 14 444 USD nie jest skategoryzowana (nie ma tagów).

Następnie wybierz pozycję **Uncategorized Resources** (Zasoby nieskategoryzowane) i wybierz usługi, które zawierają nieprzydzielone koszty. Potem zdefiniuj reguły przydzielania kosztów.

Na przykład koszty magazynu platformy Azure możesz dystrybuować równomiernie między maszynami wirtualnymi platformy Azure. Aby to zrobić, wybierz usługę **Azure/Storage**, pozycję **Proportional to Categorized** (Proporcjonalnie do skategoryzowanych), a następnie pozycję **Azure/VM**. Następnie wybierz przycisk **Create** (Utwórz).

![Przykładowa reguła przydzielania w modelu kosztów zapewniająca równomierną dystrybucję](./media/tutorial-manage-costs/cost-model02.png)



Inny przykład: możesz chcieć przydzielić wszystkie koszty sieci platformy Azure do określonej jednostki biznesowej w organizacji. Aby to zrobić, wybierz pozycję **Azure/Network**, a następnie pozycję **Explicit Distribution** (Dystrybucja jawna). Następnie ustaw dla procentu dystrybucji wartość 100 i wybierz jednostkę biznesową — na poniższej ilustracji jest to jednostka **G&amp;A**:

![Przykładowa reguła przydzielania do określonej jednostki biznesowej w modelu kosztów](./media/tutorial-manage-costs/cost-model03.png)



Dla wszystkich pozostałych nieskategoryzowanych zasobów utwórz dodatkowe reguły przydzielania.

Jeśli masz nieprzydzielone wystąpienia zarezerwowane usługi Amazon Web Services (AWS), możesz przypisać je do otagowanych kategorii za pomocą pozycji **Reserved Instances** (Wystąpienia zarezerwowane).

Aby wyświetlić informacje o wybranych opcjach przydzielania kosztów, wybierz pozycję **Summary** (Podsumowanie). Aby zapisać informacje i kontynuować pracę nad dodatkowymi regułami później, wybierz pozycję **Save As Draft** (Zapisz jako wersję roboczą). Ewentualnie aby zapisać informacje i rozpocząć przetwarzanie przez usługę Cloudyn modelu przydzielania kosztów, wybierz pozycję **Save and Activate** (Zapisz i aktywuj).

Na liście modeli kosztów nowy model kosztów będzie wyświetlany ze stanem **Processing** (Przetwarzanie). Może upłynąć pewien czas, zanim baza danych Cloudyn zostanie aktualizowana o ten model kosztów. Po zakończeniu przetwarzania stan zostanie zaktualizowany do **Completed** (Ukończono). Dane z modelu kosztów będzie wtedy można wyświetlić w raporcie analizy kosztów w obszarze **Extended Filters** (Rozszerzone filtry) &gt; **Cost Model** (Modelu kosztów).

### <a name="category-manager"></a>Narzędzie Category Manager

Category Manager to narzędzie do czyszczenia danych, które pomaga scalić wartości wielu kategorii (tagi), aby utworzyć nowe. Jest to proste narzędzie oparte na regułach, które umożliwia wybranie kategorii i utworzenie reguł w celu scalenia istniejących wartości. Mogą na przykład istnieć kategorie **R&amp;D** oraz **dev** — obie reprezentujące grupy deweloperskie.

W portalu Cloudyn kliknij symbol koła zębatego w prawym górnym rogu i wybierz pozycję **Category Manager** (Menedżer kategorii). Aby utworzyć nową kategorię, wybierz znak plus (**+**). Wprowadź nazwę dla kategorii, a następnie w obszarze **Keys** (Klucze) wprowadź klucze kategorii, które mają zostać uwzględnione w nowej kategorii.

Podczas definiowania reguły można dodać wiele wartości z warunkiem LUB. Można również wykonywać pewne podstawowe operacje na ciągach. Obie opcje wymagają kliknięcia symbolu wielokropka (**...**) z prawej strony pozycji **Rule** (Reguła).

Aby zdefiniować nową regułę, w obszarze **Rules** (Reguły) utwórz nową regułę. Na przykład wprowadź tekst **dev** w obszarze **Rules** (Reguły), a następnie tekst **R&amp;D** w obszarze **Actions** (Akcje). Gdy wszystko będzie gotowe, zapisz nową kategorię.

Na poniższej ilustracji przedstawiono przykład reguł utworzonych dla nowej kategorii o nazwie **Work-Load**:

![Przykład kategorii](./media/tutorial-manage-costs/category01.png)

### <a name="tag-sources-and-reports"></a>Tagowanie źródeł i raportów

Dane tagów widoczne w raportach usługi Cloudyn pochodzą z trzech miejsc:

- Interfejsy API zasobów dostawców chmury
- Interfejsy API rozliczeń dostawców chmury
- Tagi utworzone ręcznie z następujących źródeł:
    - Tagi jednostek Cloudyn — metadane zdefiniowane przez użytkownika zastosowane do jednostek Cloudyn
    - Category Manager — narzędzie do czyszczenia danych, które tworzy nowe tagi na podstawie reguł zastosowanych do istniejących tagów

Aby wyświetlać w raportach kosztów Cloudyn tagi dostawców chmury, należy utworzyć niestandardowy model przydzielania kosztów przy użyciu funkcji Cost Allocation 360. Aby to zrobić, przejdź do pozycji **Cost** (Koszty) > **Cost Management** (Zarządzanie kosztami) > **Cost Allocation 360°** (Alokacja kosztu 360°), wybierz odpowiednie tagi, a następnie zdefiniuj reguły obsługi nieotagowanych kosztów. Następnie utwórz nowy model kosztów. Następnie możesz przeglądać raporty w analizie przydzielania kosztów (Cost Allocation Analysis) w celu wyświetlania, filtrowania i sortowania tagów zasobów platformy Azure.

Tagi zasobów platformy Azure są wyświetlane tylko w raportach **Cost Allocation Analysis** (Analiza przydzielania kosztów).

Tagi rozliczeń dostawców chmury są widoczne we wszystkich raportach kosztów.

Tagi jednostek Cloudyn i tagi utworzone ręcznie są widoczne we wszystkich raportach kosztów.


## <a name="create-showback-and-chargeback-reports"></a>Tworzenie raportów przewidywanych kosztów i obciążeń zwrotnych

Stosowane przez organizacje metody tworzenia analizy przewidywanych kosztów i obciążeń zwrotnych są bardzo zróżnicowane. Jednak do dowolnego z tych celów można użyć pulpitów nawigacyjnych i raportów w portalu Cloudyn. Dowolnym osobom w organizacji można zapewnić dostęp użytkownika, tak aby mogły wyświetlać pulpity nawigacyjne i raporty na żądanie. Wszystkie raporty analizy kosztów obsługują analizę przewidywanych kosztów, ponieważ użytkownicy dowiadują się z nich, jakie zasoby wykorzystali. Pozwalają też użytkownikom przejść do szczegółów w danych kosztów lub użycia specyficznych dla ich grupy w organizacji.

Aby wyświetlić wyniki przydzielania kosztów, otwórz raport analizy kosztów i wybierz utworzony przez siebie model kosztów. Następnie dodaj zgrupowanie według jednego lub większej liczby tagów wybranych w modelu kosztów.

![Raport analizy kosztów](./media/tutorial-manage-costs/cost-analysis.png)

Łatwo można tworzyć i zapisywać raporty skupione na określonych usługach używanych przez określone grupy. Na przykład: w pewnym dziale intensywnie korzysta się z maszyn wirtualnych platformy Azure. Można utworzyć raport filtrowany według maszyn wirtualnych platformy Azure, aby pokazać wykorzystanie i koszty.

Jeśli trzeba przekazać dane migawki innym zespołom, można wyeksportować dowolny raport w formacie PDF lub CSV.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przydzielanie kosztów za pomocą tagów niestandardowych.
> * Tworzenie raportów przewidywanych kosztów i obciążeń zwrotnych.



Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat kontrolowania dostępu do danych.

> [!div class="nextstepaction"]
> [Kontrola dostępu do danych](tutorial-user-access.md)
