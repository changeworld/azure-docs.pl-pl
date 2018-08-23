---
title: Ustawienie kontroli dostępu w usłudze Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować kontrolę dostępu w usłudze Azure Cosmos DB.
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2018
ms.author: rafats
ms.openlocfilehash: 0d4595bcf8a9f009dce928d3f3cbc442328ec39b
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "42060204"
---
# <a name="access-control-in-azure-cosmos-db"></a>Kontrola dostępu w usłudze Azure Cosmos DB

Aby dodać dostęp czytelnika konta usługi Azure Cosmos DB z Twoim kontem użytkownika, należy mieć właścicielem subskrypcji, wykonaj następujące kroki w witrynie Azure portal.

1. Otwórz witrynę Azure portal, a następnie wybierz swoje konto usługi Azure Cosmos DB.
2. Kliknij przycisk **kontrola dostępu (IAM)** kartę, a następnie kliknij przycisk **+ Dodaj**.
3. W **Dodaj uprawnienia** okienku w obszarze **roli** wybierz opcję **Rola czytelnika konta bazy danych Cosmos**.
4. W **przypisywanie dostępu do usługi box**, wybierz opcję **użytkownika usługi Azure AD, grupa lub aplikacja**.
5. Wybierz użytkownika, grupę lub aplikację w katalogu, do którego chcesz udzielić dostępu.  Możesz przeszukiwać katalog według nazwy wyświetlanej, adresu e-mail lub identyfikatorów obiektów.
    Wybranego użytkownika, grupy lub aplikacji zostanie wyświetlony na liście wybranych członków.
6. Kliknij pozycję **Zapisz**.

Jednostka może teraz odczytywać zasoby usługi Azure Cosmos DB.
