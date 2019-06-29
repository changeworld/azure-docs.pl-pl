---
title: Szczegóły dokumentu — niestandardowe w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Na stronie listy dokumentów zawiera 10 pierwszych dokumentu w obszarze roboczym. Dla każdego z dokumentów wyświetla nazwę, parowania, typu, języka, sygnaturę czasową przekazywania i adres e-mail użytkownika, który przekazywane dokumentu.
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 267d60c2e9941c66ab128f68bd7aaf1cc54fedd9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442504"
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
