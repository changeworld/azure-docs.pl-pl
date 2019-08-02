---
title: Zarejestruj aplikację — Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Przewodnik krok po kroku dotyczący rejestrowania nowej aplikacji za pomocą usługi Azure Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo
ROBOTS: NOINDEX
ms.openlocfilehash: 4a8e4e91e768e3ad28ea9ff88c18515db4a40323
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704565"
---
# <a name="register-your-application"></a>Zarejestruj swoją aplikację

Aby użyć Custom Decision Service aplikacji, zarejestruj ją w portalu. W tym artykule wyjaśniono, jak.

1. Przejdź na [stronę frontonu](https://portal.ds.microsoft.com/) Custom Decision Service. Na wstążce kliknij pozycję **mój Portal**, co zostało wyróżnione na obrazie:

    ![Mój Portal](./media/portal.png)

    Jeśli użytkownik nie jest jeszcze zalogowany, w portalu zostanie wyświetlony monit o zalogowanie się przy użyciu [konto Microsoft](https://account.microsoft.com/account). Po zalogowaniu się w portalu zostanie wyświetlony konto Microsoft w prawym górnym rogu strony.

2. Aby zarejestrować aplikację, kliknij przycisk **Nowa aplikacja** .

3. W oknie dialogowym Wybierz identyfikator aplikacji dla swojej aplikacji. Custom Decision Service wymaga unikatowego identyfikatora dla każdej aplikacji. Jeśli ktoś inny już pobrał ten identyfikator, system wyświetli monit o wybranie innego.

4. Określ interfejs API zestawu akcji. To ustawienie jest kanałem informacyjnym RSS lub Atom, który komunikuje dostępną zawartość aplikacji do Custom Decision Service. Wprowadź nazwę źródła danych, a następnie wprowadź adres URL, z którego jest obsługiwany. Aby wykonać ten krok później, kliknij przycisk **źródła danych** , a następnie kliknij przycisk **nowe źródło** . Przykład służący do tworzenia kanału informacyjnego RSS jest opisany w dalszej części.

5. Aby zarejestrować aplikację, zaznacz pole wyboru **aplikacja** niestandardowa w lewym dolnym rogu. Wprowadź [Parametry połączenia](../../storage/common/storage-configure-connection-string.md) dla konta usługi Azure Storage, na którym są rejestrowane dane aplikacji. Aby uzyskać więcej informacji na temat tworzenia konta magazynu, zobacz [jak utworzyć, zarządzać lub usunąć konto magazynu](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Kolejne kroki

* Zoptymalizuj [stronę internetową](custom-decision-service-get-started-browser.md) lub [aplikację na smartfona](custom-decision-service-get-started-app.md).
* Przechodzenie przez [samouczek](custom-decision-service-tutorial-news.md) , aby zapoznać się z bardziej szczegółowym przykładem.
* Zapoznaj się z [dokumentacją interfejsu API](custom-decision-service-api-reference.md), aby dowiedzieć się więcej o funkcjach usługi.
