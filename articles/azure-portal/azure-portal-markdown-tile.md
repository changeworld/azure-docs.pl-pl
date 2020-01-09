---
title: Korzystanie z niestandardowego kafelka promocji na pulpitach nawigacyjnych platformy Azure
description: Dowiedz się, jak dodać kafelek z promocji do pulpitu nawigacyjnego platformy Azure w celu wyświetlenia zawartości statycznej
services: azure-portal
keywords: ''
author: mblythe
ms.author: mblythe
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3d2e6d2d0bde76a35a18373fabf64ce36c6c320e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640146"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Użyj kafelka promocji na pulpitach nawigacyjnych platformy Azure, aby pokazać zawartość niestandardową

Możesz dodać kafelek promocji do pulpitów nawigacyjnych platformy Azure, aby wyświetlić niestandardową zawartość statyczną. Na przykład możesz wyświetlić podstawowe instrukcje, obraz lub zestaw hiperlinków z kafelkiem promocji.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Dodawanie kafelka promocji do pulpitu nawigacyjnego

1. Wybierz pozycję **pulpit nawigacyjny** na pasku bocznym Azure Portal. Jeśli utworzono niestandardowe pulpity nawigacyjne, w widoku pulpitu nawigacyjnego, Użyj listy rozwijanej, aby wybrać pulpit nawigacyjny, w którym ma zostać wyświetlona niestandardowa Tabliczka promocji. Wybierz ikonę Edytuj, aby otworzyć **galerię kafelków**.

   ![Zrzut ekranu przedstawiający widok edycji pulpitu nawigacyjnego](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. W **galerii kafelków**Znajdź kafelek o nazwie **promocji** i kliknij przycisk **Dodaj**. Kafelek zostanie dodany do pulpitu nawigacyjnego i zostanie otwarte okienko **Edycja promocji** .

1. Edytuj **tytuł**, **podtytuł**i pola **zawartości** , aby dostosować kafelek. W przykładzie pokazanym poniżej został zmodyfikowany kafelek promocji w celu wyświetlenia niestandardowych informacji pomocy technicznej.

   ![Zrzut ekranu przedstawiający widok edycji kafelka promocji](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. Wybierz pozycję **gotowe** , aby zamknąć okienko **Edycja promocji** . Zawartość będzie wyświetlana na kafelku promocji, którego rozmiar można zmienić, przeciągając uchwyt w prawym dolnym rogu.

   ![Zrzut ekranu przedstawiający niestandardowy kafelek promocji](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Możliwości i ograniczenia dotyczące promocji

Możesz użyć dowolnej kombinacji zwykłego tekstu, składni promocji i zawartości HTML na kafelku promocji. Azure Portal używa biblioteki Open Source o nazwie _oznaczonej_ do przekształcenia zawartości na HTML, która jest wyświetlana na kafelku. KOD HTML utworzony przez _program jest wstępnie przetworzony przez portal_ przed jego renderowaniem. Ten krok pomaga upewnić się, że dostosowanie nie będzie miało wpływu na zabezpieczenia ani układ portalu. Podczas tego wstępnego przetwarzania jakakolwiek część kodu HTML, która stanowi potencjalne zagrożenie, jest usuwana. Portal nie zezwala na następujące typy zawartości:

* Język JavaScript — Tagi `<script>` i oceny wbudowanych języka JavaScript zostaną usunięte.
* iframes — Tagi `<iframe>` zostaną usunięte.
* Style — `<style>` Tagi zostaną usunięte. Atrybuty stylu wbudowanego dla elementów HTML nie są oficjalnie obsługiwane. Może się okazać, że niektóre elementy stylu wbudowanego działają dla Ciebie, ale jeśli zakłócają układ portalu, mogą przestać działać w dowolnym momencie. Kafelek promocji jest przeznaczony dla podstawowej, statycznej zawartości, która używa domyślnych stylów portalu.

## <a name="next-steps"></a>Następne kroki

* Aby utworzyć niestandardowy pulpit nawigacyjny, zobacz [Tworzenie i udostępnianie pulpitów nawigacyjnych w Azure Portal](../azure-portal/azure-portal-dashboards.md)
