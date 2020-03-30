---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183724"
---
Domyślnie interfejsy API w zapleczu aplikacji mobilnych mogą być wywoływane anonimowo. Następnie należy ograniczyć dostęp tylko do uwierzytelnionych klientów.  

* **Zaplecze node.js (za pośrednictwem portalu Azure)** :  

    W ustawieniach aplikacji mobilnych kliknij **pozycję Łatwe tabele** i wybierz tabelę. Kliknij **pozycję Zmień uprawnienia**, wybierz pozycję **Uwierzytelniony dostęp tylko** dla wszystkich uprawnień, a następnie kliknij przycisk **Zapisz**.
* **.NET zaplecza (C#)**:  

    W projekcie serwera przejdź do **pozycji Kontrolery** > **TodoItemController.cs**. Dodaj `[Authorize]` atrybut do **Klasy TodoItemController,** w następujący sposób. Aby ograniczyć dostęp tylko do określonych metod, można również zastosować ten atrybut tylko do tych metod zamiast klasy. Ponownie opublikuj projekt serwera.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Wewnętrznej bazy danych Node.js (za pomocą kodu Node.js):**  

    Aby wymagać uwierzytelnienia w celu uzyskania dostępu do tabeli, dodaj następujący wiersz do skryptu serwera Node.js:

        table.access = 'authenticated';

    Aby uzyskać więcej informacji, zobacz [Jak: Wymaganie uwierzytelniania w celu uzyskania dostępu do tabel](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Aby dowiedzieć się, jak pobrać projekt kodu szybkiego startu z witryny, zobacz [Jak: Pobieranie projektu kodu szybkiego startu w pliku Node.js przy użyciu git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).
