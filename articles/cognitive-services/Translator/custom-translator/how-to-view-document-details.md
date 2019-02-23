---
title: Szczegóły dokumentu — niestandardowe w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Na stronie listy dokumentów zawiera 10 pierwszych dokumentu w obszarze roboczym. Dla każdego z dokumentów wyświetla nazwę, parowania, typu, języka, sygnaturę czasową przekazywania i adres e-mail użytkownika, który przekazywane dokumentu.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: ed872a00cd35fcef74ab0b578a86cdb6cd2ec8c7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727574"
---
# <a name="view-document-details"></a>Wyświetlanie szczegółów dokumentu

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
