---
title: Jak wyszukiwać, edytować i usuwać projekt — Tłumacz niestandardowy
titleSuffix: Azure Cognitive Services
description: Usługa Custom Translator oferuje różne sposoby efektywnego zarządzania projektami. Możesz tworzyć wiele projektów, wyszukiwać na podstawie kryteriów, edytować projekty. Usuwanie projektu jest również możliwe w translatorze niestandardowym.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 9432ac390b7677b3dea5aebc84cf23ef6e45db92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595716"
---
# <a name="search-edit-and-delete-projects"></a>Wyszukiwanie, edytowanie i usuwanie projektów

Usługa Custom Translator oferuje wiele sposobów efektywnego zarządzania projektami. Możesz tworzyć wiele projektów, wyszukiwać na podstawie kryteriów i edytować projekty. Usuwanie projektu jest również możliwe w translatorze niestandardowym.  

## <a name="search-and-filter-projects"></a>Wyszukiwanie i filtrowanie projektów

Narzędzie filtrujące umożliwia wyszukiwanie projektów według różnych warunków filtrowania. Filtruje, takie jak nazwa projektu, stan, język źródłowy i docelowy oraz kategoria projektu.

1.  Kliknij przycisk filtru.

    ![Szukaj projektu](media/how-to/how-to-search-project.png)

1.  Możesz filtrować według dowolnego (lub wszystkich) następujących pól: nazwa projektu, stan, język źródłowy, język docelowy i kategoria.

2.  Kliknij przycisk Zastosuj.

    ![Opcje filtru projektu wyszukiwania](media/how-to/how-to-search-project-filters.png)

3.  Wyczyść filtr, aby wyświetlić wszystkie projekty, naciskając "Wyczyść".


## <a name="edit-a-project"></a>Edytowanie projektu

Translator niestandardowy umożliwia edytowanie nazwy i opisu projektu. Inne metadane projektu, takie jak kategoria, język źródłowy i język docelowy, nie są dostępne do edycji. W poniższych krokach opisano sposób edytowania projektu.

1.  Kliknij ikonę ołówka, która pojawi się po najechaniu kursorem na projekt.

    ![Edytuj projekt](media/how-to/how-to-edit-project.png)

2.  W oknie dialogowym można zmodyfikować nazwę projektu lub opis projektu, ale nie można zmodyfikować etykiety projektu, kategorii ani pary języków.

    ![Okno dialogowe Edytowanie projektu](media/how-to/how-to-edit-project-dialog.png)

3.  Kliknij przycisk filtru.

## <a name="delete-a-project"></a>Usuwanie projektu

Możesz usunąć projekt, gdy nie jest już potrzebny. Poniższe kroki opisują sposób usuwania projektu.

1. Najedź kursorem na dowolny rekord projektu i kliknij ikonę kosza.

   ![Usuń projekt](media/how-to/how-to-delete-project.png)

2. Potwierdź usunięcie. Usunięcie projektu spowoduje usunięcie wszystkich modeli, które zostały utworzone w ramach tego projektu. Usunięcie projektu nie wpłynie na dokumenty.

   ![Okno dialogowe Usuwanie potwierdzenia](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>Następne kroki

- [Prześlij dokumenty,](how-to-upload-document.md) aby rozpocząć tworzenie niestandardowego modelu tłumaczenia.
