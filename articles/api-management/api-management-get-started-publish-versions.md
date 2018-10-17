---
title: Publikowanie wersji interfejsu API przy użyciu usługi Azure API Management | Microsoft Docs
description: Wykonaj kroki tego samouczka, aby dowiedzieć się, jak publikować wiele wersji w usłudze API Management.
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
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: a7e5051248a579b0943fa69620215b060bd1e235
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092697"
---
# <a name="publish-multiple-versions-of-your-api"></a>Publikowanie wielu wersji interfejsu API 

Istnieją sytuacje, w których używanie tej samej wersji przez wszystkich wywołujących interfejs API jest niepraktyczne. Kiedy wywołujący chcą przeprowadzić uaktualnienie do nowszej wersji, chcą mieć możliwość wykonania tej operacji w łatwy do zrozumienia sposób. Jest to możliwe do zrobienia przy użyciu **wersji** w usłudze Azure API Management. Aby uzyskać więcej informacji, zobacz [Wersje i poprawki](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie nowej wersji do istniejącego interfejsu API
> * Wybieranie schematu wersji
> * Dodawanie wersji do produktu
> * Przeglądanie portalu dla deweloperów w celu wyświetlenia wersji

![Wersja przedstawiona w portalu dla deweloperów](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
* Ponadto wykonaj zadania z następującego samouczka: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

## <a name="add-a-new-version"></a>Dodawanie nowej wersji

![Menu kontekstowe interfejsu API — dodawanie wersji](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. Z listy interfejsów API wybierz pozycję **Wersja demonstracyjna interfejsu API Conference**.
2. Wybierz menu kontekstowe (**...**) obok niego.
3. Wybierz opcję **+ Dodaj wersję**.

    > [!TIP]
    > Wersje można również włączyć podczas tworzenia nowego interfejsu API — wybierz opcję **Czy chcesz utworzyć wersję tego interfejsu API?** na ekranie **Dodawanie interfejsu API**.

## <a name="choose-a-versioning-scheme"></a>Wybieranie schematu przechowywania wersji

Usługa Azure API Management umożliwia wybranie sposobu określania przez wywołujących pożądanej wersji interfejsu API. Wersję interfejsu API do użycia określasz poprzez wybranie **schematu przechowywania wersji**. Ten schemat może być **ścieżką, nagłówkiem lub ciągiem zapytania**. W poniższym przykładzie użyto ścieżki do wybrania schematu przechowywania wersji.

![Ekran dodawania wersji](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. Zostaw **ścieżkę** wybraną jako **schemat przechowywania wersji**.
2. Dodaj **v1** jako **nazwę** i **identyfikator wersji**.

    > [!TIP]
    > Jeśli wybierzesz **nagłówek** lub **ciąg zapytania** jako schemat przechowywania wersji, musisz podać dodatkową wartość — nazwę nagłówka lub parametr ciągu zapytania.

3. Wybierz opcję **Utwórz**, aby skonfigurować nową wersję.
4. W obszarze **Wersja demonstracyjna interfejsu API konferencji** na liście interfejsów API będą teraz widoczne dwa różne interfejsy API — **Oryginalny** i **v1**.

    ![Wersje wymienione na liście w sekcji interfejsów API w witrynie Azure Portal](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Jeśli dodasz wersję do niewersjonowanego interfejsu API, automatycznie zostanie utworzony **Oryginalny** interfejs API — odpowiadający pod domyślnym adresem URL. Dzięki temu zapewnia się, że nie wystąpią przerwy dla istniejących wywołujących spowodowane dodawaniem wersji. Jeśli tworzysz nowy interfejs API z wersjami włączonymi od początku, oryginalny interfejs API nie zostanie utworzony.

5. Teraz możesz edytować i konfigurować interfejs API **v1** oddzielnie od interfejsu **Oryginalny**. Zmiany wprowadzane w jednej wersji nie wpływają na drugą.

## <a name="add-the-version-to-a-product"></a>Dodawanie wersji do produktu

Aby wywołujący mogli zobaczyć nową wersję, musi zostać ona dodana do **produktu**.

1. Wybierz pozycję **Produkty** na stronie klasycznego modelu wdrażania.

    ![Produkty przeznaczone do usługi API Management](media/api-management-getstarted-publish-versions/Products.png)

2. Wybierz pozycję **Bez ograniczeń**.
3. Wybierz pozycję **Interfejsy API**.
4. Wybierz pozycję **Dodaj**.
5. Wybierz pozycję **Interfejs API Pokaz konferencji, wersja v1**.
6. Przejdź do strony zarządzania usługami i wybierz pozycję **Interfejsy API**.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Przeglądanie portalu dla deweloperów w celu wyświetlenia wersji

1. Wybierz opcję **Portal dla deweloperów** z górnego menu.
2. Wybierz opcję **Interfejsy API**. Zauważ, że pozycja **Interfejs API Pokaz konferencji** ma wersje **Oryginalny** i **v1**.
3. Wybierz opcję **v1**.
4. Zwróć uwagę na **Adres URL żądania** pierwszej operacji na liście. Pozycja pokazuje, że ścieżka adresu URL interfejsu API uwzględnia ciąg **v1**.

    ![Menu kontekstowe interfejsu API — dodawanie wersji](media/api-management-getstarted-publish-versions/developer_portal.png)

## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie nowej wersji do istniejącego interfejsu API
> * Wybieranie schematu wersji 
> * Dodawanie wersji do produktu
> * Przeglądanie portalu dla deweloperów w celu wyświetlenia wersji

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Dostosowywanie stylu stron portalu dla deweloperów](api-management-customize-styles.md)