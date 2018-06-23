---
title: Rejestrowanie aplikacji - kognitywnych usług Azure | Dokumentacja firmy Microsoft
description: Przewodnik krok po kroku jak zarejestrować nową aplikację z usługi Azure Service decyzji niestandardowe
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo;alekh;marossi
ms.openlocfilehash: 2aa8fbe77c11df4434eefa4c92d8529d5ca1d885
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348961"
---
# <a name="register-your-application"></a>Rejestrowanie aplikacji

Aby użyć niestandardowej decyzji usługi dla aplikacji, zarejestruj go w portalu. W tym artykule opisano sposób.

1. Przejdź do [FrontPage](https://ds.microsoft.com/) niestandardowe usługi decyzji. Na wstążce kliknij **portalu Moje**, są tak wyróżnione w obrazie:

    ![Moje portalu](./media/portal.png)

    Jeśli dany system nie są już podpisany, portalu wyświetli monit o Zaloguj się przy użyciu programu [konta Microsoft](https://account.microsoft.com/account). Po zalogowaniu się portalu Wyświetla konta Microsoft w prawym górnym rogu strony.

2. Aby zarejestrować aplikację, kliknij przycisk **nową aplikację** przycisku.

3. W oknie dialogowym Wybierz identyfikator aplikacji dla aplikacji. Niestandardowe usługi decyzji wymaga unikatowego Identyfikatora dla każdej aplikacji. Jeśli ktoś miała już ten identyfikator, zostanie wyświetlona prośba systemu i wybierz inną subskrypcję.

4. Określ zbiór akcji interfejsu API. To ustawienie jest RSS lub źródło Atom, który komunikuje się dostępnej zawartości aplikacji do usługi decyzji niestandardowe. Wprowadź nazwę dla źródła danych, a następnie wprowadź adres URL, z którego jest ono obsłużone. Aby wykonać ten krok później, kliknij przycisk **źródła** przycisk, a następnie kliknij przycisk **nowe źródło** przycisku. Przykład tworzenia źródła danych RSS jest opisany później.

5. Aby zarejestrować aplikację, wybierz **aplikacji niestandardowej** pole wyboru w lewym dolnym rogu. Wprowadź [ciąg połączenia](../../storage/common/storage-configure-connection-string.md) dla konta magazynu Azure, gdzie jest rejestrowane dane aplikacji. Aby uzyskać więcej informacji na temat sposobu tworzenia konta magazynu, zobacz [sposobu tworzenia, zarządzania i usuwania konta magazynu](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Kolejne kroki

* Wprowadzenie do optymalizacji [strony sieci Web](custom-decision-service-get-started-browser.md) lub [aplikacja na smartfona](custom-decision-service-get-started-app.md).
* Pracy za pośrednictwem [samouczek](custom-decision-service-tutorial-news.md) na pełniejsze przykład.
* Zapoznaj się [dokumentacja interfejsu API](custom-decision-service-api-reference.md) Aby dowiedzieć się więcej o funkcjach podana.