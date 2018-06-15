---
title: Dostosowanie stylu strony w portalu deweloperów usługi Azure API Management | Microsoft Docs
description: Wykonaj kroki tego przewodnika Szybki start, aby dostosować styl elementów w portalu deweloperów usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: d1f638c9825ea5eedf6eaee0e0ca2ccfd5a491bc
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "33933712"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Dostosowywanie stylu stron portalu deweloperów

Istnieją trzy najpopularniejsze sposoby dostosowywania portalu deweloperów usługi Azure API Management:
 
* [Edycja zawartości stron statycznych i elementów układu strony](api-management-modify-content-layout.md)
* Aktualizacja stylów używanych dla elementów strony w portalu deweloperów (opisana w tym przewodniku)
* [Modyfikowanie szablonów używanych dla stron wygenerowanych przez portal](api-management-developer-portal-templates.md) (na przykład dokumentacji interfejsu API, produktów czy uwierzytelniania użytkowników)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dostosowywanie stylu elementów na stronach portalu **deweloperów**
> * Wyświetlanie zmiany

![Dostosowywanie stylu](./media/modify-developer-portal-style/developer_portal.png)

## <a name="prerequisites"></a>Wymagania wstępne

+ Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Ponadto wykonaj zadania z następującego samouczka: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

## <a name="customize-the-developer-portal"></a>Dostosowywanie portalu deweloperów

1. Wybierz pozycję **Przegląd**.
2. Kliknij przycisk **Portal deweloperów** u góry okna **Przegląd**. Możesz też kliknąć link **Adres URL portalu deweloperów**.
3. W lewej górnej części ekranu będzie widoczna ikona składająca się z dwóch pędzli. Zatrzymaj wskaźnik myszy nad tą ikoną, aby otworzyć menu dostosowywania portalu.

    ![Dostosowywanie stylu](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Wybierz pozycję **Style** z menu, aby otworzyć okienko dostosowywania stylu.

    Na stronie zostaną wyświetlone wszystkie elementy, które można dostosować za pomocą **stylów**
5. Wprowadź wartość „headings-color” w polu **Change variable values to customize developer portal appearance:** (Zmień wartości zmiennych w celu dostosowania wyglądu portalu dla deweloperów:).

    Na stronie zostanie wyświetlony element **@headings-color**. Ta zmienna steruje kolorem tekstu.

    ![Dostosowywanie stylu](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Kliknij pole dla zmiennej **@headings-color**. 
    
    Zostanie otwarta lista rozwijana selektora kolorów.
7. Wybierz nowy kolor z listy rozwijanej selektora kolorów.

    > [!TIP]
    > Dla wszystkich zmian wyświetlany jest podgląd w czasie rzeczywistym. W górnej części okienka dostosowywania widoczny jest wskaźnik postępu. Po kilku sekundach tekst nagłówka zmieni kolor na nowo wybrany.

8. Wybierz pozycję **Publikuj** w lewym dolnym rogu menu okienka dostosowywania.
9. Wybierz pozycję **Publikuj dostosowania**, aby udostępnić zmiany publicznie.

## <a name="view-your-change"></a>Wyświetlanie zmiany

1. Przejdź do portalu deweloperów.
2. Wprowadzone zmiany będą widoczne.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dostosowywanie stylu elementów na stronach portalu **deweloperów**
> * Wyświetlanie zmiany

> [!div class="nextstepaction"]
> [Dostosowywanie portalu deweloperów usługi Azure API Management przy użyciu szablonów](api-management-developer-portal-templates.md)