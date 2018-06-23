---
title: Zarządzanie wersjami w aplikacjach LUIS na platformie Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie zarządzania wersjami w aplikacjach opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/29/2017
ms.author: v-geberr
ms.openlocfilehash: 672f7991be0fc236e39daf7d1ce1d6080b31815b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347085"
---
# <a name="manage-versions"></a>Zarządzanie wersjami

Za każdym razem, pracy z modelem, tworzy inną [wersji](luis-concept-version.md) aplikacji. 

## <a name="set-active-version"></a>Ustaw wersję active
Aby pracować z wersjami, Otwórz aplikację wybierając jego nazwę na **Moje aplikacje** , a następnie wybierz **ustawienia** w górnym pasku.

![Wersje strony](./media/luis-how-to-manage-versions/settings.png)

**Ustawienia** strona umożliwia konfigurowanie ustawień dla całej aplikacji, w tym wersje i współpracowników. 

## <a name="clone-a-version"></a>Klonowanie wersji
1. Na **ustawienia** strony, po sekcji ustawień aplikacji i współpracownicy Znajdź wiersz mający wersję chcesz sklonować. Wybierz wielokropek (...) po prawej. 

    ![Wersja wiersza właściwości](./media/luis-how-to-manage-versions/version-section.png)

2. Wybierz **klonowania** z listy.

    ![Wybór właściwości wiersza wersji](./media/luis-how-to-manage-versions/version-three-dots-modal.png)

3. W **wersji w klonowania** okno dialogowe, wpisz nazwę nowej wersji, takie jak "0,2".

   ![Okno dialogowe wersji w klonowania](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
 > [!NOTE]
 > Wersja identyfikator może zawierać tylko znaki, cyfry lub '.' i nie może być dłuższa niż 10 znaków.
 
 Nowej wersji przy użyciu określonej nazwy jest utworzona i ustawiona jako aktywna wersja.
 
  ![Wersja jest tworzony i dodawany do listy](./media/luis-how-to-manage-versions/new-version.png)

 > [!NOTE]
 > Jak pokazano w poprzednim obrazu, opublikowanej wersji jest skojarzony z znacznik kolorowe wskazujący typ miejsca, w którym zostały opublikowane: produkcji (zielony), przemieszczania (czerwony) i obie (czarny). Szkolenie i publikowania daty są wyświetlane dla każdej wersji opublikowanej.

## <a name="set-active-version"></a>Ustaw wersję active
1. Na **ustawienia** strony w **wersji** listy, wybierz wielokropek (...) po prawej.

2. Wybierz z listy wyskakujących **ustawione jako aktywne**.

    ![Ustaw wersję active](./media/luis-how-to-manage-versions/set-active-version.png)

    Wersja aktywna jest wyróżniony światła niebieski, jak pokazano na poniższym zrzucie ekranu:

    ![Wersja aktywna](./media/luis-how-to-manage-versions/set-active-version-done.png) 


## <a name="import-version"></a>Wersja importu
Wersja można zaimportować z pliku JSON. Po zaimportowaniu wersji nowej wersji staje się aktywny wersji.

**Aby zaimportować wersję:**

1. Na **ustawienia** wybierz pozycję **importu nowej wersji** przycisku.

    ![Przycisk Importuj](./media/luis-how-to-manage-versions/import-version.png)

2. Wybierz **Przeglądaj** i wybierz plik formatu JSON.

    ![Okno dialogowe wersji importu](./media/luis-how-to-manage-versions/import-version-dialog.png)

Należy ustawić identyfikator wersji, jeśli istnieje już wersja w pliku JSON w aplikacji.

## <a name="export-version"></a>Wersja eksportu
Wersja można wyeksportować do pliku JSON.

**Aby wyeksportować wersji:**

1. Na **ustawienia** strony w **wersji** listy, wybierz wielokropek (...) po prawej.

2. Wybierz **wyeksportować** na liście wyskakującego działań i wybierz, w którym chcesz zapisać plik.

## <a name="delete-a-version"></a>Usuwanie wersji
Można usuwać wersje, ale musisz zachować co najmniej jedną wersję aplikacji. Możesz usunąć wszystkich wersji z wyjątkiem aktywnej wersji. 

1. Na **ustawienia** strony w **wersji** listy, wybierz wielokropek (...) po prawej.

2. Wybierz **usunąć** na liście wyskakującego działań i wybierz, w którym chcesz zapisać plik.

    ![Wersja potwierdzenie usunięcia](./media/luis-how-to-manage-versions/delete-menu.png) 


## <a name="rename-a-version"></a>Zmień nazwę wersji
Można zmienić wersji tak długo, jak nazwa wersji nie jest już używany.  

1. Na **ustawienia** strony w **wersji** listy, wybierz wielokropek (...) po prawej.

2. Wybierz **zmienić** wyskakujących liście akcji.

3. Wprowadź nazwę nowej wersji, a następnie wybierz **gotowe**.

    ![Zmień nazwę wersji potwierdzenia](./media/luis-how-to-manage-versions/rename-popup.png) 
