---
title: Niestandardowa serializacja pamięci podręcznej tokenów (MSAL Python) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak serializować pamięć podręczną tokenów dla usługi MSAL dla języka Python
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 2593cc856afb98cf5186c4e33032c5e9151614f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76704395"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Niestandardowa serializacja pamięci podręcznej tokenów w języku MSAL dla języka Python

W języku MSAL Python, pamięć podręczna tokenów w pamięci, która utrzymuje się przez cały czas trwania sesji aplikacji, jest domyślnie dostarczana podczas tworzenia wystąpienia [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication).

Serializacja pamięci podręcznej tokenu, tak aby różne sesje aplikacji mogą uzyskać do niej dostęp, nie jest dostępna "po wyjęciu z pudełka". To dlatego, że MSAL Python może być używany w typach aplikacji, które nie mają dostępu do systemu plików — takich jak aplikacje sieci Web. Aby mieć trwałą pamięć podręczną tokenu w aplikacji MSAL Python, należy podać niestandardową serializację pamięci podręcznej tokenów.

Strategie serializacji pamięci podręcznej tokenu różnią się w zależności od tego, czy piszesz publiczną aplikację kliencką (pulpit), czy poufną aplikację kliencką (aplikacja sieci Web, interfejs API sieci Web lub aplikacja Demon).

## <a name="token-cache-for-a-public-client-application"></a>Pamięć podręczna tokenu dla publicznej aplikacji klienckiej

Publiczne aplikacje klienckie są uruchamiane na urządzeniu użytkownika i zarządzają tokenami dla pojedynczego użytkownika. W takim przypadku można serializować całą pamięć podręczną do pliku. Pamiętaj, aby zapewnić blokowanie plików, jeśli aplikacja lub inna aplikacja może jednocześnie uzyskać dostęp do pamięci podręcznej. Prosty przykład serializacji pamięci podręcznej tokenu do pliku bez blokowania można znaleźć w przykładzie w dokumentacji referencyjnej klasy [SerializableTokenCache.](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache)

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Pamięć podręczna tokenu dla aplikacji sieci Web (poufna aplikacja kliencka)

W przypadku aplikacji sieci Web lub interfejsów API sieci Web można użyć sesji, pamięci podręcznej Redis lub bazy danych do przechowywania pamięci podręcznej tokenów. Powinna istnieć jedna pamięć podręczna tokenu na użytkownika (na konto), więc upewnij się, że serializujesz pamięć podręczną tokenu na konto.

## <a name="next-steps"></a>Następne kroki

Zobacz [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) na przykład, jak używać pamięci podręcznej tokenów dla aplikacji sieci Web systemu Windows lub Linux lub interfejsu API sieci Web. Przykładem jest aplikacja sieci web, która wywołuje interfejs API programu Microsoft Graph.
