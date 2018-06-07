---
title: Ustawienia kontroli dostępu w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ustawić kontroli dostępu w usłudze Azure DB rozwiązania Cosmos.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2018
ms.author: sngun
ms.openlocfilehash: 2dae2b6291aa7ce18cc6f612b25cd5bdcc1ba753
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611147"
---
# <a name="access-control-in-azure-cosmos-db"></a>Kontrola dostępu w usłudze Azure DB rozwiązania Cosmos

Aby dodać bazy danych Azure rozwiązania Cosmos konta czytnika dostępu do konta użytkownika, należy mieć właściciela subskrypcji, wykonaj następujące kroki w portalu Azure.

1. Otwórz Azure portal, a następnie wybierz konto bazy danych Azure rozwiązania Cosmos.
2. Kliknij przycisk **(IAM) kontroli dostępu** , a następnie kliknij pozycję **+ Dodaj**.
3. W **dodać uprawnienia** okienko w **roli** wybierz opcję **rozwiązania Cosmos bazy danych konta czytnik roli**.
4. W **przypisywanie dostępu do pola**, wybierz pozycję **użytkownika usługi Azure AD, grupy lub aplikacji**.
5. Wybierz użytkownika, grupę lub aplikację w katalogu, do którego chcesz udzielić dostępu.  Można wyszukiwać według nazwy wyświetlanej, adresu e-mail lub identyfikatorów obiektów katalogu.
    Wybranego użytkownika, grupy lub aplikacji zostanie wyświetlony na liście wybranych członków.
6. Kliknij pozycję **Zapisz**.

Jednostka mogą teraz odczytywać zasobów bazy danych Azure rozwiązania Cosmos.
