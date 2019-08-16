---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "67183724"
---
Domyślnie interfejsy API w Mobile Apps zaplecza mogą być wywoływane anonimowo. Następnie musisz ograniczyć dostęp tylko do uwierzytelnionych klientów.  

* Zaplecze środowiska **Node. js (za pośrednictwem Azure Portal)** :  

    W ustawieniach Mobile Apps kliknij pozycję **łatwe tabele** i wybierz tabelę. Kliknij pozycję **Zmień uprawnienia**, wybierz pozycję **dostęp uwierzytelniony tylko** dla wszystkich uprawnień, a następnie kliknij przycisk **Zapisz**.
* **Zaplecze .NET (C#)** :  

    W projekcie serwera przejdź do **kontrolera** > **TodoItemController.cs**. Dodaj atrybut do klasy TodoItemController w następujący sposób. `[Authorize]` Aby ograniczyć dostęp tylko do określonych metod, można również zastosować ten atrybut tylko do tych metod, a nie klasy. Opublikuj ponownie projekt serwera.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* Zaplecze środowiska **Node. js (za pośrednictwem kodu Node. js)** :  

    Aby wymagać uwierzytelniania w celu uzyskania dostępu do tabeli, Dodaj następujący wiersz do skryptu serwera Node. js:

        table.access = 'authenticated';

    Aby uzyskać więcej informacji, [zobacz How to: Wymagaj uwierzytelniania w celu uzyskania dostępu](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth)do tabel. Aby dowiedzieć się, jak pobrać projekt kodu szybkiego startu z witryny [, zobacz How to: Pobierz projekt kodu zaplecze środowiska Node. js przy](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)użyciu narzędzia Git.
