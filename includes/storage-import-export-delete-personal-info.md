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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183444"
---
## <a name="deleting-personal-information"></a>Usuwanie danych osobowych

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Dane osobowe są istotne dla usługi importu/eksportu (za pośrednictwem portalu i INTERFEJSU API) podczas operacji importu i eksportu. Dane wykorzystywane podczas tych procesów obejmują:

- Nazwisko osoby kontaktowej
- Numer telefonu
- Adres e-mail
- Adres
- Miasto
- Kod pocztowy
- Stan
- Kraj/Województwo/Region
- Identyfikator dysku
- Numer konta operatora
- Numer śledzenia dostawy

Podczas tworzenia zadania importu/eksportu użytkownicy podają informacje kontaktowe i adres wysyłki. Dane osobowe są przechowywane w maksymalnie dwóch różnych lokalizacjach: w zadaniu i opcjonalnie w ustawieniach portalu. Dane osobowe są przechowywane w ustawieniach portalu tylko wtedy, gdy zaznaczysz pole wyboru oznaczone etykietą **Zapisz przewoźnika i adres zwrotny jako domyślny** podczas sekcji *Informacje o wysyłce zwrotu* procesu eksportowania.

Osobiste dane kontaktowe mogą zostać usunięte w następujący sposób:

- Dane zapisane za pomocą zadania zostaną usunięte z zadaniem. Użytkownicy mogą usuwać zadania ręcznie, a ukończone zadania są automatycznie usuwane po 90 dniach. Zadania można ręcznie usunąć za pośrednictwem interfejsu API REST lub witryny Azure portal. Aby usunąć zadanie w witrynie Azure portal, przejdź do zadania importu/eksportu i kliknij przycisk *Usuń* na pasku poleceń. Szczegółowe informacje na temat usuwania zadania importowania/eksportowania za pośrednictwem interfejsu API REST można znaleźć w części [Usuwanie zadania importu/eksportu](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- Informacje kontaktowe zapisane w ustawieniach portalu można usunąć, usuwając ustawienia portalu. Ustawienia portalu można usunąć, wykonując następujące czynności:
  - Zaloguj się do [Portalu Azure](https://portal.azure.com).
  - Kliknij ikonę ![ *Ustawienia* ustawienia platformy Azure Ikona](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Kliknij *pozycję Eksportuj wszystkie ustawienia* (aby zapisać bieżące ustawienia w `.json` pliku).
  - Kliknij *pozycję Usuń wszystkie ustawienia i prywatne pulpity nawigacyjne,* aby usunąć wszystkie ustawienia, w tym zapisane informacje kontaktowe.

Aby uzyskać więcej informacji, zapoznaj się z zasadami zachowania poufności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter)