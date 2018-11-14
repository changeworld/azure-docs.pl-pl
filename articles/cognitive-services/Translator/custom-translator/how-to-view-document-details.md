---
title: Szczegóły dokumentu — niestandardowe w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Na stronie listy dokumentów zawiera 10 pierwszych dokumentu w obszarze roboczym. Dla każdego z dokumentów wyświetla nazwę, parowania, typu, języka, sygnaturę czasową przekazywania i adres e-mail użytkownika, który przekazywane dokumentu.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: how to edit a model
ms.openlocfilehash: fe108831f173eb31af79a2f8e5f7faf57015c38a
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627630"
---
# <a name="view-document-details"></a>Wyświetl szczegóły dokumentu

Na stronie listy dokumentów zawiera 10 pierwszych dokumentu w obszarze roboczym. Dla każdego z dokumentów wyświetla nazwę, parowania, typu, języka, sygnaturę czasową przekazywania i adres e-mail użytkownika, który przekazywane dokumentu.

Kliknij pojedynczy dokument, aby wyświetlić stronę szczegółów dokumentu. Na stronie szczegółów dokumentów umożliwia wyświetlenie listy zdań wyodrębnione z dokumentu.

- "Źródło" język jest zaznaczone domyślnie w polu listy rozwijanej, ale można przełączać się do wyświetlania zdań w języku docelowym. 
- 20 zdania są domyślnie wyświetlane na stronie. Formant podziału na strony do przechodzenia między stronami.

![szczegóły dokumentu](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Usuwanie dokumentu

Użytkownik musi być właściciela obszaru roboczego, można usunąć dokumentu, można usunąć dokumentu. Ponadto jeśli dokument jest używany przez model, który znajduje się w dowolnej części procesu uczenia lub dowolną część procesu wdrażania, nie można usunąć dokumentu.

1. Przejdź do strony z dokumentu
2.  Umieść kursor na dowolny rekord dokumentu, a następnie kliknij ikonę Kosza na śmieci.

    ![Usuwanie dokumentu](media/how-to/how-to-delete-document-1.png)

3.  Potwierdzenie usunięcia.

    ![Potwierdzenie usunięcia](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, [sposób trenowania modelu](how-to-train-model.md).
