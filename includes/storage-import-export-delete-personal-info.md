---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-policy
author: craigshoemaker
ms.service: azure-policy
ms.topic: include
ms.date: 05/18/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: e6a0ded137162328fd446b65ddb4a15fa6f1db88
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114973"
---
## <a name="deleting-personal-information"></a>Usuwanie danych osobowych

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Dane osobowe jest odpowiednia dla importu/eksportu usługi (za pośrednictwem portalu i interfejsów API) podczas importowania i eksportowania operacji. Dane używane podczas procesy te obejmują:

- Nazwa kontaktu
- Numer telefonu
- Email
- Adres
- Miasto
- Kod pocztowy
- Stan
- Kraj/Województwo/Region
- Identyfikator dysku
- Numer konta operatora
- Numer śledzenia dostawy

Po utworzeniu zadania importu/eksportu użytkowników Podaj informacje kontaktowe i adresu do wysyłki. Dane osobowe są przechowywane w maksymalnie dwóch różnych lokalizacjach: w ramach zadania i, opcjonalnie, w ustawieniach portalu. Informacje osobiste są przechowywane wyłącznie w ustawieniach portalu, jeśli zaznaczysz pole wyboru, **Zapisz operatora i zwrócenia adres jako domyślne** podczas *zwracają informacje o wysyłce* części procesu eksportu.

Osobiste informacje kontaktowe mogą zostać usunięte w następujący sposób:

- Dane zapisane z zadaniem jest usuwany przez zadanie. Użytkownicy mogą usuwać zadania ręcznie i zakończonych zadań są automatycznie usuwane po 90 dniach. Można ręcznie usunąć zadania za pomocą interfejsu API REST lub witryny Azure portal. Aby usunąć zadanie w witrynie Azure portal, przejdź do zadania importu/eksportu, a następnie kliknij przycisk *Usuń* na pasku poleceń. Aby uzyskać szczegółowe informacje dotyczące sposobu usuwania zadania importu/eksportu za pośrednictwem interfejsu API REST, zobacz [usuwanie zadania importu/eksportu](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- Informacje kontaktowe, zapisane w ustawieniach portalu może zostać usunięty przez usunięcie ustawienia portalu. Aby usunąć ustawienia portalu, wykonaj następujące czynności:
  - Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
  - Kliknij pozycję *ustawienia* ikonę ![ikonę ustawień platformy Azure](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Kliknij przycisk *wyeksportować wszystkie ustawienia* (do zapisania swoich bieżących ustawień do `.json` pliku).
  - Kliknij przycisk *Usuń wszystkie ustawienia i prywatne pulpity nawigacyjne* można usunąć wszystkie ustawienia w tym zapisane informacje kontaktowe.

Aby uzyskać więcej informacji, przejrzyj zasady Privacy firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter)