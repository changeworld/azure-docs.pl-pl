---
title: Zarządzanie wersjami w aplikacji usługi LUIS na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać wersjami w aplikacjach Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/29/2017
ms.author: diberry
ms.openlocfilehash: 4941cf533f1b860ead07a416d5af6f62a1978305
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226598"
---
# <a name="manage-versions"></a>Zarządzanie wersjami

Każdorazowo, działają na podstawie modelu, tworzenie innego [wersji](luis-concept-version.md) aplikacji. 

## <a name="set-active-version"></a>Ustaw wersję active
Aby pracować z wersjami, Otwórz aplikację, wybierając jego nazwę na **Moje aplikacje** strony, a następnie wybierz pozycję **ustawienia** na górnym pasku.

![Strona wersji](./media/luis-how-to-manage-versions/settings.png)

**Ustawienia** strona umożliwia konfigurowanie ustawień dla całej aplikacji, w tym wersje i współpracowników. 

## <a name="clone-a-version"></a>Klonowanie wersji
1. Na **ustawienia** stronie po sekcji ustawień aplikacji i współpracowników Znajdź wiersz mający wersję, aby sklonować. Wybierz przycisk wielokropka (***...*** ) przycisk na prawej. 

    ![Właściwości wiersza wersji](./media/luis-how-to-manage-versions/version-section.png)

2. Wybierz **klonowania** z listy.

    ![Wybór właściwości wiersza wersji](./media/luis-how-to-manage-versions/version-three-dots-modal.png)

3. W **wersji klonowania** okno dialogowe, wpisz nazwę dla nowej wersji, takich jak "0,2".

   ![Okno dialogowe wersji klonowania](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
 > [!NOTE]
 > Wersja identyfikator może zawierać tylko znaki, cyfry lub "." i nie może być dłuższa niż 10 znaków.
 
 Nową wersję przy użyciu określonej nazwy jest utworzona i ustawiona jako aktywnej wersji.
 
  ![Wersja zostanie utworzony i dodany do listy](./media/luis-how-to-manage-versions/new-version.png)

 > [!NOTE]
 > Pokazany na wcześniejszej ilustracji, opublikowanej wersji jest skojarzony z kolorowym znacznikiem, wskazujący typ miejsca, gdzie został opublikowany: produkcyjnych (zielony), przemieszczania (czerwony) i obie (czarny). Szkolenia i publikowania daty są wyświetlane dla każdej opublikowanej wersji.

## <a name="set-active-version"></a>Ustaw wersję active
1. Na **ustawienia** stronie **wersji** listy, wybierz przycisk wielokropka (***...*** ) przycisk po prawej stronie.

2. Wybierz z listy wyskakującego **Ustaw jako aktywne**.

    ![Ustaw wersję active](./media/luis-how-to-manage-versions/set-active-version.png)

    Wersja aktywna jest wyróżniona jasny kolor niebieski, jak pokazano na poniższym zrzucie ekranu:

    ![Wersja aktywna](./media/luis-how-to-manage-versions/set-active-version-done.png) 


## <a name="import-version"></a>Wersja importu
Wersja można zaimportować z pliku JSON. Po zaimportowaniu wersji nowej wersji staje się aktywny wersji.

**Aby zaimportować wersję:**

1. Na **ustawienia** wybierz opcję **importowania nowej wersji** przycisku.

    ![Przycisk Importuj](./media/luis-how-to-manage-versions/import-version.png)

2. Wybierz **Przeglądaj** i wybierz plik JSON.

    ![Okno dialogowe importowania wersji](./media/luis-how-to-manage-versions/import-version-dialog.png)

Należy ustawić identyfikator wersji, jeśli w pliku JSON w wersji już istnieje w aplikacji.

## <a name="export-version"></a>Eksportuj wersję
Wersja można wyeksportować do pliku JSON.

**Aby wyeksportować wersji:**

1. Na **ustawienia** stronie **wersji** listy, wybierz przycisk wielokropka (***...*** ) przycisk po prawej stronie.

2. Wybierz **wyeksportować** wyskakującego liście akcji i wybierz, w którym chcesz zapisać plik.

## <a name="delete-a-version"></a>Usuwanie wersji
Można usuwać wersje, ale musisz zachować co najmniej jedną wersję aplikacji. Możesz usunąć wszystkie wersje, z wyjątkiem aktywnej wersji. 

1. Na **ustawienia** stronie **wersji** listy, wybierz przycisk wielokropka (***...*** ) przycisk po prawej stronie.

2. Wybierz **Usuń** wyskakującego liście akcji i wybierz, w którym chcesz zapisać plik.

    ![Wersja potwierdzenie usunięcia](./media/luis-how-to-manage-versions/delete-menu.png) 


## <a name="rename-a-version"></a>Zmień nazwę wersji
Można zmienić wersji, tak długo, jak nazwa wersji nie jest już używany.  

1. Na **ustawienia** stronie **wersji** listy, wybierz przycisk wielokropka (***...*** ) przycisk po prawej stronie.

2. Wybierz **Zmień nazwę** wyskakującego liście akcji.

3. Wprowadź nazwę nowej wersji, a następnie wybierz pozycję **gotowe**.

    ![Zmień nazwę wersji potwierdzenia](./media/luis-how-to-manage-versions/rename-popup.png) 
