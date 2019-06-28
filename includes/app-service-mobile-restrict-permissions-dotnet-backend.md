---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183724"
---
Domyślnie interfejsy API w zaplecze funkcji Mobile Apps może być wywoływany anonimowo. Następnie należy ograniczyć dostęp do tylko uwierzytelnionym klientom.  

* **Środowisko node.js z powrotem zakończenia (za pośrednictwem witryny Azure portal)** :  

    W ustawieniach aplikacji mobilnej kliknij **łatwych tabel** i Wybieranie tabeli. Kliknij przycisk **zmienić uprawnienia**, wybierz opcję **tylko dostęp uwierzytelniony** wszystkich uprawnień, a następnie kliknij przycisk **Zapisz**.
* **Zaplecze platformy .NET (C#)** :  

    W programie project server, przejdź do **kontrolerów** > **TodoItemController.cs**. Dodaj `[Authorize]` atrybutu **TodoItemController** klasy w następujący sposób. Aby ograniczyć dostęp tylko do określonych metod, można także zastosować ten atrybut tylko do jednej z tych metod zamiast klasy. Ponownie opublikować projekt serwera.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Zaplecze środowiska node.js (przy użyciu kodu w języku Node.js)** :  

    Aby wymagać uwierzytelniania w celu dostępu do tabel, Dodaj następujący wiersz do skryptu serwera Node.js:

        table.access = 'authenticated';

    Aby uzyskać więcej informacji, zobacz [jak: Wymaganie uwierzytelniania na potrzeby dostępu do tabel](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Aby dowiedzieć się, jak pobrać projekt kodu szybkiego startu z lokacji, zobacz [jak: Pobierz projekt kodu szybkiego startu zaplecza Node.js przy użyciu narzędzia Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).
