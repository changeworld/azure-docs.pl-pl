---
title: Eksportowanie lub usuwanie ustawień witryny Azure portal
description: Dowiedz się, jak wyeksportować lub usunąć ustawienia użytkownika, prywatne pulpity nawigacyjne i ustawienia niestandardowe w witrynie Azure portal.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900761"
---
# <a name="export-or-delete-user-settings"></a>Eksportowanie i usuwanie ustawień użytkownika

Ustawienia i funkcje w witrynie Azure portal można użyć do utworzenia środowiska niestandardowego. Informacje o ustawieniach niestandardowych są przechowywane na platformie Azure. Można wyeksportować lub usunąć następujące dane użytkownika:

* Prywatne pulpity nawigacyjne w witrynie Azure portal
* Ustawienia użytkownika, takie jak ulubione subskrypcje lub katalogi, i ostatnio zalogowany katalog
* Motywy i inne niestandardowe ustawienia portalu

Przed ich usunięciem warto wyeksportować i przejrzeć ustawienia. Przebudowa pulpitów nawigacyjnych lub ponowne ponawianie ustawień niestandardowych może być czasochłonne.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Eksportowanie lub usuwanie ustawień portalu

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. W nagłówku portalu wybierz ![ikonę](media/azure-portal-export-delete-settings/settings-icon.png) **ustawień Ustawienia**.

1. Wybierz **pozycję Eksportuj wszystkie ustawienia** lub Usuń wszystkie ustawienia i prywatne **pulpity nawigacyjne**.

    ![Ustawienia i opcje ustawień witryny Azure portal](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      W poniższej tabeli opisano te akcje.

      | Akcja | Opis |
      | --- | --- |
      | **Eksportuj wszystkie ustawienia** | Tworzy plik *.json* zawierający ustawienia użytkownika, takie jak motyw kolorów, ulubione i prywatne pulpity nawigacyjne.|
      | **Usuwanie wszystkich ustawień i prywatnych pulpitów nawigacyjnych** | Usuwa wszystkie łącza do prywatnych pulpitów nawigacyjnych i innych ustawień niestandardowych wprowadzonych w portalu. |

> [!NOTE]
> Ze względu na dynamiczny charakter ustawień użytkownika i ryzyko uszkodzenia danych nie można importować ustawień z pliku *.json.*
>
>

## <a name="next-steps"></a>Następne kroki

* [Udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu kontroli dostępu opartej na rolach](azure-portal-dashboard-share-access.md)
* [Dodawanie, usuwanie i zmienianie rozmieszczenia ulubionych](azure-portal-add-remove-sort-favorites.md)
