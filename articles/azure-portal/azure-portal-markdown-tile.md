---
title: Używanie niestandardowego kafelka znaczników na pulpitach nawigacyjnych platformy Azure
description: Dowiedz się, jak dodać kafelek znaczników do pulpitu nawigacyjnego platformy Azure w celu wyświetlania zawartości statycznej
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/08/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 74102423461a56bb6fc19c2eb9874f96a76e34e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310717"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Wyświetlanie zawartości niestandardowej za pomocą kafelka znaczników na pulpitach nawigacyjnych platformy Azure

Możesz dodać kafelek znaczników do pulpitów nawigacyjnych platformy Azure, aby wyświetlić niestandardową, statyczną zawartość. Na przykład można wyświetlić podstawowe instrukcje, obraz lub zestaw hiperłączy na kafelku znaczników.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Dodawanie kafelka znaczników do pulpitu nawigacyjnego

1. Wybierz **pulpit nawigacyjny** z paska bocznego portalu Azure.

   ![Zrzut ekranu przedstawiający pasek boczny portalu](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Jeśli utworzono niestandardowe pulpity nawigacyjne, w widoku pulpitu nawigacyjnego użyj listy rozwijanej, aby wybrać pulpit nawigacyjny, na którym ma być wyświetlany niestandardowy kafelek znaczników. Wybierz ikonę edycji, aby otworzyć **Galerię kafelków**.

   ![Zrzut ekranu przedstawiający widok edycji pulpitu nawigacyjnego](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. W **Galerii kafelków**znajdź kafelek o nazwie **Markdown** i wybierz pozycję **Dodaj**. Kafelek zostanie dodany do pulpitu nawigacyjnego i zostanie otwarte okienko **Edytuj znaczniki.**

1. Wprowadź wartości **tytułów** i **napisów,** które są wyświetlane na kafelku po przejściu do innego pola.

   ![Zrzut ekranu przedstawiający wyniki wprowadzania tytułu i napisów](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Wybierz jedną z opcji dołączania zawartości znaczników: **Edycja w linii** lub **Wstaw zawartość przy użyciu adresu URL**.

   - Wybierz **edycję w linii,** jeśli chcesz wprowadzić markdown bezpośrednio.

      ![Zrzut ekranu przedstawiający wprowadzanie zawartości wbudowanej](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Wybierz **pozycję Wstaw zawartość przy użyciu adresu URL,** jeśli chcesz użyć istniejącej zawartości znaczników, która jest hostowana w trybie online.

      ![Zrzut ekranu przedstawiający wprowadzanie adresu URL](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Aby zwiększyć bezpieczeństwo, można utworzyć plik znaczników i przechowywać go w [obiekcie blob konta magazynu platformy Azure, gdzie szyfrowanie jest włączone,](../storage/common/storage-service-encryption.md)a następnie wskazać plik przy użyciu opcji adresu URL. Zawartość znaczników jest szyfrowana za pomocą opcji szyfrowania konta magazynu. Tylko użytkownicy z uprawnieniami do pliku mogą zobaczyć zawartość znaczników na pulpicie nawigacyjnym.

1. Wybierz **pozycję Gotowe,** aby odrzucić okienko **Edytuj markdown.** Zawartość pojawi się na kafelku Markdown, którego rozmiar można zmienić, przeciągając uchwyt w prawym dolnym rogu.

   ![Zrzut ekranu przedstawiający kafelek niestandardowego oznaczania w dół](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Możliwości i ograniczenia zawartości znaczników

Na kafelku znaczników można użyć dowolnej kombinacji zwykłego tekstu, składni Znaczników i zawartości HTML. Portal Azure używa biblioteki typu open source o nazwie _oznaczone_ do przekształcenia zawartości w kod HTML, który jest wyświetlany na kafelku. Kod HTML produkowany przez _oznaczone_ jest wstępnie przetwarzane przez portal przed jego renderowania. Ten krok pomaga upewnić się, że dostosowanie nie wpłynie na bezpieczeństwo lub układ portalu. Podczas tego przetwarzania wstępnego usuwana jest dowolna część kodu HTML, która stanowi potencjalne zagrożenie. Portal nie zezwala na następujące typy zawartości:

* JavaScript `<script>` – tagi i wbudowane oceny JavaScript zostaną usunięte.
* iframe - `<iframe>` tagi zostaną usunięte.
* Styl `<style>` - tagi zostaną usunięte. Atrybuty stylu wbudowanego w elementach HTML nie są oficjalnie obsługiwane. Może się okazać, że niektóre elementy stylu wbudowanego działają dla Ciebie, ale jeśli kolidują z układem portalu, mogą przestać działać w dowolnym momencie. Kafelek Markdown jest przeznaczony dla podstawowej, statycznej zawartości, która używa domyślnych stylów portalu.

## <a name="next-steps"></a>Następne kroki

* Aby utworzyć niestandardowy pulpit nawigacyjny, zobacz [Tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure Portal](../azure-portal/azure-portal-dashboards.md)
