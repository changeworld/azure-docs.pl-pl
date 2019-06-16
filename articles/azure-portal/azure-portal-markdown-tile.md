---
title: Użyj kafelka niestandardowymi elementami języka markdown na pulpitach nawigacyjnych platformy Azure
description: Dowiedz się, jak dodać tabliczkę języka znaczników markdown do pulpitu nawigacyjnego platformy Azure, aby wyświetlić zawartość statyczna
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ec8cbddda4137656a53fd4968c451cd413959274
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60551586"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Użyj kafelka markdown na pulpitach nawigacyjnych platformy Azure, aby pokazać zawartości niestandardowej

Możesz dodać kafelka markdown do pulpitów nawigacyjnych platformy Azure do wyświetlania zawartości statycznej, niestandardowe. Na przykład można wyświetlić podstawowe instrukcje, obrazu lub zestaw hiperłączy z kafelkiem markdown.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Dodawanie kafelka markdown do pulpitu nawigacyjnego

1. Wybierz **pulpit nawigacyjny** z paska bocznego portalu Azure. Po utworzeniu żadnych niestandardowych pulpitów nawigacyjnych w widoku pulpitu nawigacyjnego, należy z listy rozwijanej wybierz pulpit nawigacyjny, gdzie powinna zostać wyświetlona na kafelku niestandardowymi elementami języka markdown. Wybierz ikonę edycji, aby otworzyć **Galeria kafelków**.

   ![Zrzut ekranu przedstawiający pulpit nawigacyjny Edytuj widok](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. W **Galeria kafelków**, Znajdź Kafelek o nazwie **Markdown** i kliknij przycisk **Dodaj**. Kafelek zostanie dodany do pulpitu nawigacyjnego i **edytowania kodu Markdown** zostanie otwarte okienko.

1. Edytuj **tytuł**, **podtytuł**, i **zawartości** pola, aby dostosować kafelka. W przykładzie poniżej pokazano kafelka markdown został zmodyfikowany do wyświetlania informacji działu pomocy niestandardowej.

   ![Zrzut ekranu przedstawiający widok edycji kafelka markdown](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. Wybierz **gotowe** odrzucać **edytowania kodu Markdown** okienka. Zawartość będą wyświetlane na kafelku języka znaczników Markdown, które można zmienić, przeciągając uchwyt w prawym dolnym rogu.

   ![Zrzut ekranu przedstawiający niestandardowymi elementami języka markdown kafelka](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Możliwości zawartości markdown i ograniczenia

Na kafelku języka znaczników markdown, można użyć dowolnej kombinacji zwykłego tekstu, składnia języka Markdown i zawartość HTML. Azure portal korzysta z biblioteki typu open source o nazwie _oznaczone_ do przekształcania zawartości HTML, która jest wyświetlana na kafelku. Utworzona przez testowany kod HTML _oznaczone_ wstępnie jest przetwarzany przez portal, zanim zostanie renderowana. Ten krok umożliwia, upewnij się, że zabezpieczenia lub układ portalu nie wpłyną na dostosowanie. Podczas wstępnego przetworzenia, dowolną część HTML, który stanowi potencjalne zagrożenie, zostanie usunięta. Następujące rodzaje zawartości nie są dozwolone w portalu:

* JavaScript — `<script>` tagów i oceny kodu JavaScript wbudowane zostaną usunięte.
* elementy IFRAME - `<iframe>` tagi zostaną usunięte.
* Style - `<style>` tagi zostaną usunięte. Wbudowane style atrybutów elementów HTML oficjalnie nie są obsługiwane. Może się okazać, że niektóre elementy style wbudowane będą dla Ciebie, ale jeśli zakłócają układ portalu one może przestać działać w dowolnym momencie. Sąsiadująco Markdown jest przeznaczony dla podstawowych, statycznej zawartości, która używa domyślne style portalu.

## <a name="next-steps"></a>Kolejne kroki

* Aby utworzyć niestandardowy pulpit nawigacyjny, zobacz [tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure portal](../azure-portal/azure-portal-dashboards.md)
