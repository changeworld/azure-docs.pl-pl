---
title: Eksportowanie lub usuwanie ustawień Azure Portal | Microsoft Docs
description: Dowiedz się, jak eksportować lub usuwać ustawienia użytkownika, prywatne pulpity nawigacyjne i ustawienia niestandardowe w Azure Portal.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: f033af37985077f4d8df9d541b55764df0c75eda
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640180"
---
# <a name="export-or-delete-user-settings"></a>Eksportowanie i usuwanie ustawień użytkownika

Aby utworzyć niestandardowe środowisko, można użyć ustawień i funkcji w Azure Portal. Informacje o ustawieniach niestandardowych są przechowywane na platformie Azure. Możesz wyeksportować lub usunąć następujące dane użytkownika:

* Prywatne pulpity nawigacyjne w Azure Portal
* Ustawienia użytkownika, takie jak Ulubione subskrypcje lub katalogi, i ostatni zalogowany katalog
* Motywy i inne ustawienia portalu niestandardowego

Dobrym pomysłem jest wyeksportowanie i przejrzenie ustawień przed ich usunięciem. Ponowne kompilowanie pulpitów nawigacyjnych lub przeprowadzenie niestandardowych ustawień może być czasochłonne.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Eksportowanie lub usuwanie ustawień portalu

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. W nagłówku portalu wybierz pozycję **Ustawienia**.

    ![Zrzut ekranu pokazujący koła zębate ustawień portalu](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. Wybierz opcję **Eksportuj wszystkie ustawienia** lub **Usuń wszystkie ustawienia i prywatne pulpity nawigacyjne**.

    ![Zrzut ekranu pokazujący ustawienia eksportowania i usuwania portalu](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      W poniższej tabeli opisano te akcje.

      | Działanie | Opis |
      | --- | --- |
      | **Eksportuj wszystkie ustawienia** | Tworzy plik JSON, który zawiera ustawienia użytkownika, takie jak motyw kolorów, Ulubione i prywatne pulpity nawigacyjne.|
      | **Usuń wszystkie ustawienia i prywatne pulpity nawigacyjne** | Usuwa wszystkie linki do prywatnych pulpitów nawigacyjnych i innych ustawień niestandardowych, które zostały wprowadzone do portalu. |

> [!NOTE]
> Ze względu na dynamiczny charakter ustawień użytkownika i ryzyko uszkodzenia danych nie można importować ustawień z pliku JSON.
>
>


## <a name="next-steps"></a>Następne kroki

* [Tworzenie i udostępnianie pulpitów nawigacyjnych platformy Azure](azure-portal-dashboard-share-access.md)
* [Dodawanie, usuwanie i sortowanie ulubionych](azure-portal-add-remove-sort-favorites.md)
