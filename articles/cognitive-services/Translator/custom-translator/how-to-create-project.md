---
title: Jak utworzyć projekt? — Niestandardowy w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Jak utworzyć projekt w usłudze translator niestandardowym?
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a62cc6133fe01bf7478166c526e32b3215ceebf1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595753"
---
# <a name="create-a-project"></a>Tworzenie projektu

Projekt jest kontenerem dla modeli, dokumentów i testów. Każdy projekt automatycznie zawiera wszystkie dokumenty, które są przekazywane do tego obszaru roboczego, który ma poprawną parę językową.

Tworzenie projektu to pierwszy krok w kierunku tworzenia modelu.

## <a name="create-a-project"></a>Utwórz projekt:

1.  W portalu [translatora niestandardowego](https://portal.customtranslator.azure.ai) kliknij pozycję Utwórz projekt.

    ![Tworzenie projektu](media/how-to/how-to-create-project.png)

2.  W oknie dialogowym wprowadź następujące szczegółowe informacje o projekcie:

    a.  Nazwa projektu (wymagana): Nadaj projektowi unikatową, zrozumiałą nazwę. Nie trzeba wymieniać języków w tytule.

    b.  Opis: Krótkie podsumowanie dotyczące projektu. Ten opis nie ma wpływu na zachowanie translatora niestandardowego ani systemowego systemu, ale może ułatwić rozróżnienie między różnymi projektami.

    c.  Para języków (wymagana): Wybierz język, z którego jest przeprowadzana translacja.

    d.  Kategoria (wymagana): Wybierz kategorię, która jest najbardziej odpowiednia dla Twojego projektu. Kategoria zawiera opis terminologii i stylu dokumentów, które zamierzasz przetłumaczyć.

    e.  Opis kategorii: Użyj tego pola, aby lepiej opisać konkretne pole lub branżę, w której pracujesz. Na przykład jeśli Twoja kategoria to medycyna, możesz dodać określony dokument, taki chirurg lub Pediatrics. Opis nie ma wpływu na zachowanie niestandardowego translatora ani systemu niestandardowego.

    f.  Etykieta projektu: [Etykieta projektu](workspace-and-project.md#project-labels) rozróżnia projekty z tą samą parą językową i kategorią. Najlepszym rozwiązaniem jest użycie etykiety *tylko* wtedy, gdy planujesz kompilację wielu projektów dla tej samej pary językowej i tej samej kategorii i chcesz uzyskać dostęp do tych projektów z innym IDKategorii. Nie używaj tego pola, jeśli tworzysz systemy tylko dla jednej kategorii. Etykieta projektu nie jest wymagana i nie ułatwia rozróżniania par językowych. Możesz użyć tej samej etykiety dla wielu projektów.

    ![Okno dialogowe Tworzenie projektu](media/how-to/how-to-create-project-dialog.png)

3.  Kliknięcie pozycji Utwórz

## <a name="view-project-details"></a>Wyświetl szczegóły projektu

Strona docelowa usługi tłumaczenia niestandardowego pokazuje pierwsze 10 projektów w obszarze roboczym. Wyświetla nazwę projektu, parę języków, kategorię, stan i ocenę BLEU.

Po wybraniu projektu na stronie projektu zostaną wyświetlone następujące elementy:

- Wartooć IDKategorii jest tworzony przez połączenie identyfikator obszaru roboczego, etykiety projektu i kodu kategorii. Używasz IDKategorii z interfejsem API usługi Text translator, aby uzyskać niestandardowe tłumaczenia.

- Przycisk uczenia: Użyj tego przycisku, aby rozpocząć [uczenie modelu](how-to-train-model.md).

- Przycisk Dodaj dokumenty: Ten przycisk służy do [przekazywania dokumentów](how-to-upload-document.md).

- Przycisk filtrowania dokumentów: Ten przycisk służy do filtrowania i wyszukiwania określonych dokumentów.

    ![Wyświetl szczegóły projektu](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wyszukiwać, edytować i usuwać projekt](how-to-search-edit-delete-projects.md).
- Dowiedz się, [jak przekazywać dokumenty](how-to-upload-document.md) do modeli tłumaczeń.
