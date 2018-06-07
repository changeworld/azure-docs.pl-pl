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
ms.openlocfilehash: 3d85208096b1300904e1cc1f59c45dd4dd5713a3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34684425"
---
## <a name="deleting-personal-information"></a>Usuwanie informacji osobistych

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Informacje osobiste ma zastosowanie w przypadku importowania/eksportowania usługi (za pośrednictwem portalu i interfejsu API) podczas importowania i eksportowania operacji. Używane podczas procesy te dane obejmują:

- Nazwa kontaktu
- Numer telefonu
- Email
- Adres
- Miasto
- Kod pocztowy
- Stan
- Region/Kraj/Region
- Identyfikator dysku
- Operator numer konta
- Numer identyfikacyjny wysyłki

Po utworzeniu zadania importu/eksportu, użytkowników, podaj informacje kontaktowe i adres wysyłkowy. Informacje osobiste są przechowywane w maksymalnie dwóch różnych lokalizacjach: w zadaniu i opcjonalnie w ustawieniach portalu. Osobiste informacje są przechowywane wyłącznie w ustawieniach portalu po zaznaczeniu pola wyboru z etykietą, **Zapisz adres operatora i przywracać jako domyślny** podczas *zwraca informacje o wysyłki* części procesu eksportowania.

Osobiste informacje kontaktowe mogą zostać usunięte w następujący sposób:

- Dane zapisane za pomocą zadania jest usuwane z zadaniem. Użytkownicy mogą ręcznie usuwać zadania i zakończonych zadań są automatycznie usuwane po 90 dniach. Można ręcznie usunąć zadania za pośrednictwem interfejsu API REST lub w portalu Azure. Aby usunąć zadania w portalu Azure, przejdź do zadania importu/eksportu, a następnie kliknij przycisk *usunąć* z paska poleceń. Aby uzyskać więcej informacji na temat sposobu usunięcia zadania importu/eksportu za pomocą interfejsu API REST, zapoznaj się [usunąć zadania importu/eksportu](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- Informacje kontaktowe zapisane w ustawieniach portalu mogą zostać usunięte przez usunięcie ustawienia portalu. Aby usunąć ustawienia portalu, wykonaj następujące czynności:
  - Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
  - Polecenie *ustawienia* ikona ![ikonę ustawień platformy Azure](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Kliknij przycisk *wyeksportować wszystkie ustawienia* (do zapisania swoich bieżących ustawień do `.json` pliku).
  - Kliknij przycisk *usunięcie wszystkich ustawień i prywatne pulpity nawigacyjne* można usunąć wszystkie ustawienia w tym zapisane informacje kontaktowe.

Aby uzyskać więcej informacji, przejrzyj zasady Privacy firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter)