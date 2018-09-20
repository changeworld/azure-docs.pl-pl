---
title: Zarejestruj swoją aplikację — Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Instrukcje krok po kroku przewodnik dotyczący sposobu rejestrowania nowej aplikacji przy użyciu usługi Azure Custom Decision Service.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo
ms.openlocfilehash: 598300597856d858095ff7c2e2cf9e9264190a9d
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365404"
---
# <a name="register-your-application"></a>Rejestrowanie aplikacji

Aby użyć usługi Custom Decision Service dla aplikacji, należy zarejestrować go w portalu. W tym artykule opisano sposób.

1. Przejdź do [strony](https://ds.microsoft.com/) z usługi Custom Decision Service. Na wstążce kliknij **portalu Moje**, jak podkreślono na ilustracji:

    ![Moje portalu](./media/portal.png)

    Jeśli użytkownik są nie już zalogowany, portalu monit logowania usługi [konta Microsoft](https://account.microsoft.com/account). Po zarejestrowaniu portalu jest wyświetlany swojego konta Microsoft, w prawym górnym rogu strony.

2. Aby zarejestrować swoją aplikację, kliknij przycisk **nową aplikację** przycisku.

3. W oknie dialogowym Wybierz identyfikator aplikacji dla aplikacji. Usługa Custom Decision Service wymaga unikatowego Identyfikatora dla każdej aplikacji. Jeśli inna osoba miała już ten identyfikator, system pyta, aby wybrać inny.

4. Wybierz zestaw akcji interfejsu API. To ustawienie jest źródło danych RSS lub źródło danych Atom komunikujący się dostępnej zawartości dla aplikacji do usługi Custom Decision Service. Wprowadź nazwę dla źródła danych, a następnie wprowadź adres URL, z którego jest obsługiwany. Aby ten krok należy wykonać później, kliknij przycisk **źródła** przycisk, a następnie kliknij przycisk **nowe źródło danych** przycisku. Przykład tworzenia kanału informacyjnego RSS jest opisany w dalszej części.

5. Aby zarejestrować swoją aplikację, wybierz pozycję **aplikacji niestandardowych** pole wyboru w lewym dolnym rogu. Wprowadź [parametry połączenia](../../storage/common/storage-configure-connection-string.md) dla konta magazynu platformy Azure, w którym dane aplikacji są rejestrowane. Aby uzyskać więcej informacji na temat tworzenia konta magazynu, zobacz [sposoby tworzenia, zarządzania i usuwania konta magazynu](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Kolejne kroki

* Wprowadzenie do optymalizacji [strony sieci Web](custom-decision-service-get-started-browser.md) lub [aplikacja na smartfona](custom-decision-service-get-started-app.md).
* Trzymać się kolejności [samouczek](custom-decision-service-tutorial-news.md) bardziej szczegółowy przykład.
* Zapoznaj się z [dokumentacja interfejsu API](custom-decision-service-api-reference.md) Aby dowiedzieć się więcej o podanej funkcji.