---
title: Eksportowanie lub Usuń ustawienia portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyeksportować lub usunąć swoje ustawienia użytkownika, prywatne pulpity nawigacyjne i niestandardowe ustawienia w witrynie Azure portal.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: kfollis
ms.openlocfilehash: fde7ffbaa3ef4d47eea48302a99948932aeb4f00
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361959"
---
# <a name="export-or-delete-user-settings"></a>Eksportowanie i usuwanie ustawień użytkownika

Można użyć ustawień i funkcji w witrynie Azure portal do tworzenia niestandardowego środowiska. Informacje o ustawieniach niestandardowych są przechowywane na platformie Azure. Można wyeksportować lub usunąć następujące dane użytkownika:

* Prywatne pulpity nawigacyjne w witrynie Azure portal
* Ustawienia użytkownika, takie jak subskrypcje ulubionych lub katalogów i katalog ostatniego logowania
* Motywy i inne niestandardowe ustawienia portalu

To dobry pomysł, aby wyeksportować i przejrzeć ustawienia przed ich usunięciem. Ponowne tworzenie pulpitów nawigacyjnych lub ponawianie ustawień niestandardowych, może być czasochłonne.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Eksportowanie lub Usuń ustawienia portalu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W nagłówku portalu wybierz **ustawienia**.

    ![Zrzut ekranu pokazujący koło zębate ustawień portalu](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. Wybierz **wyeksportować wszystkie ustawienia** lub **Usuń wszystkie ustawienia i prywatne pulpity nawigacyjne**.

    ![Zrzut ekranu przedstawiający portal eksportowania i usuwania ustawień](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      W poniższej tabeli opisano te akcje.

      | Akcja | Opis |
      | --- | --- |
      | **Eksportuj wszystkie ustawienia** | Tworzy plik JSON, który zawiera ustawień użytkownika, takie jak motyw kolorów, ulubionych i prywatne pulpity nawigacyjne.|
      | **Usuń wszystkie ustawienia i prywatne pulpity nawigacyjne** | Usuwa wszystkie łącza do prywatne pulpity nawigacyjne i inne ustawienia niestandardowe, wprowadzone do portalu. |

> [!NOTE]
> Ze względu na dynamiczny charakter ustawienia użytkownika i ryzyko uszkodzenia danych nie można zaimportować ustawienia z pliku JSON.
>
>


## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie i udostępnianie pulpitów nawigacyjnych platformy Azure](azure-portal-dashboard-share-access.md)
* [Dodawanie, usuwanie i sortowanie ulubionych](azure-portal-add-remove-sort-favorites.md)
