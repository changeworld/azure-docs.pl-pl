---
title: Szczegóły dokumentu — tłumacz niestandardowy
titleSuffix: Azure Cognitive Services
description: Strona listy dokumentów zawiera pierwszy dokument 10 w obszarze roboczym. Dla każdego z dokumentów wyświetla nazwę, parowanie, typ, język, sygnaturę czasową przesyłania i adres e-mail użytkownika, który przekazał dokument.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf0d96414c40784210723e315da5d885d61198c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595576"
---
# <a name="view-document-details"></a>Wyświetlanie szczegółów dokumentu

Strona listy dokumentów zawiera pierwszy dokument 10 w obszarze roboczym. Dla każdego z dokumentów wyświetla nazwę, parowanie, typ, język, sygnaturę czasową przesyłania i adres e-mail użytkownika, który przekazał dokument.

Kliknij pojedynczy dokument, aby wyświetlić stronę szczegółów dokumentu. Na stronie szczegółów dokumentu jest wyświetlana lista wyodrębnionych zdań z dokumentu.

- Domyślnie w polu rozwijanym jest zaznaczony język "źródłowy", ale można przełączać się, aby wyświetlić zdania w języku docelowym.
- Domyślnie na stronę wyświetlanych jest 20 zdań. Formant podziału na strony można używać do przeglądania między stronami.

![szczegóły dokumentu](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Usuwanie dokumentu

Użytkownik musi być właścicielem obszaru roboczego, aby usunąć dokument, aby usunąć dokument. Ponadto jeśli dokument jest używany przez model, który znajduje się w dowolnej części procesu szkoleniowego lub dowolnej części procesu wdrażania, dokumentu nie można usunąć.

1. Przejdź do strony dokumentu
2.  Najedź kursorem na dowolny rekord dokumentu i kliknij ikonę kosza.

    ![Usuwanie dokumentu](media/how-to/how-to-delete-document-1.png)

3.  Potwierdź usunięcie.

    ![Usuń potwierdzenie](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz [się, jak szkolić model](how-to-train-model.md).
