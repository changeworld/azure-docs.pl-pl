---
title: Jak utworzyć projekt - Custom Translator
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak utworzyć projekt i zarządzać nim w usłudze Azure Cognitive Services Custom Translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 5b3a6f2e903d8178554b7a076ae1277633569353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836560"
---
# <a name="create-a-project"></a>Tworzenie projektu

Projekt jest kontenerem dla modeli, dokumentów i testów. Każdy projekt automatycznie zawiera wszystkie dokumenty, które są przekazywane do tego obszaru roboczego, które mają poprawną parę języków.

Tworzenie projektu jest pierwszym krokiem w kierunku tworzenia modelu.

## <a name="create-a-project"></a>Tworzenie projektu:

1.  W portalu [Translator niestandardowy](https://portal.customtranslator.azure.ai) kliknij pozycję Utwórz projekt.

    ![Tworzenie projektu](media/how-to/how-to-create-project.png)

2.  W oknie dialogowym wprowadź następujące szczegóły dotyczące projektu:

    a.  Nazwa projektu (wymagana): nadaj projektowi unikatową, znaczącą nazwę. Nie trzeba wymieniać języków w tytule.

    b.  Opis: krótkie podsumowanie dotyczące projektu. Ten opis nie ma wpływu na zachowanie translatora niestandardowego lub wynikowego systemu niestandardowego, ale może pomóc w rozróżnieniu między różnymi projektami.

    d.  Para języków (wymagana): wybierz język, z którego tłumaczysz i do którego się wybierasz.

    d.  Kategoria (wymagana): wybierz kategorię, która jest najbardziej odpowiednia dla twojego projektu. Kategoria opisuje terminologię i styl dokumentów, które zamierzasz przetłumaczyć.

    e.  Opis kategorii: użyj tego pola, aby lepiej opisać daną dziedzinę lub branżę, w której pracujesz. Na przykład, jeśli twoja kategoria jest medycyna, można dodać konkretny dokument, taki zabieg, lub pediatrii. Opis nie ma wpływu na zachowanie translatora niestandardowego lub wynikowego systemu niestandardowego.

    f.  Etykieta [projektu: Etykieta projektu](workspace-and-project.md#project-labels) rozróżnia projekty z tą samą parą językową i kategorią. Najlepszym rozwiązaniem jest używanie etykiety *tylko* wtedy, gdy planujesz utworzyć wiele projektów dla tej samej pary języków i tej samej kategorii i chcesz uzyskać dostęp do tych projektów z innym identyfikatorem kategorii. Nie używaj tego pola, jeśli budujesz systemy tylko dla jednej kategorii. Etykieta projektu nie jest wymagana i nie jest pomocna w rozróżnianie par języków. Tej samej etykiety można użyć dla wielu projektów.

    ![Okno dialogowe Tworzenie projektu](media/how-to/how-to-create-project-dialog.png)

3.  Kliknięcie pozycji Utwórz

## <a name="view-project-details"></a>Wyświetlanie szczegółów projektu

Strona docelowa Translatora niestandardowego pokazuje pierwsze 10 projektów w obszarze roboczym. Wyświetla nazwę projektu, parę języków, kategorię, stan i wynik BLEU.

Po wybraniu projektu na stronie projektu pojawią się następujące elementy:

- Identyfikator kategorii: Identyfikator kategorii jest tworzony przez łączenie identyfikatora roboczego, etykiety projektu i kodu kategorii. CategoryID z interfejsem API translatora tekstu służy do uzyskania tłumaczeń niestandardowych.

- Przycisk Trenuj: Użyj tego przycisku, aby rozpocząć [szkolenie modelu](how-to-train-model.md).

- Przycisk Dodaj dokumenty: użyj tego przycisku, aby [przesłać dokumenty](how-to-upload-document.md).

- Przycisk Filtruj dokumenty: użyj tego przycisku, aby filtrować i wyszukiwać określone dokumenty.

    ![Wyświetlanie szczegółów projektu](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz [się, jak wyszukiwać, edytować i usuwać projekt](how-to-search-edit-delete-projects.md).
- Dowiedz [się, jak przekazać dokument](how-to-upload-document.md) do tworzenia modeli tłumaczeń.
