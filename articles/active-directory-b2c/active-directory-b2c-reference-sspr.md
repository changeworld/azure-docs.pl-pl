---
title: Samoobsługowe resetowanie haseł w Azure Active Directory B2C | Microsoft Docs
description: Pokazuje, jak skonfigurować Samoobsługowe resetowanie haseł dla klientów w Azure Active Directory B2C
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7903ec669772c3a3858a9c1d514ab3e6de6a2bd4
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936834"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Konfigurowanie samoobsługowego resetowania haseł dla klientów

Dzięki funkcji samoobsługowego resetowania hasła Klienci, którzy zarejestrowali się do kont lokalnych, mogą samodzielnie resetować swoje hasła. Znacznie zmniejsza to obciążenie personelu pomocy technicznej, zwłaszcza jeśli aplikacja ma miliony klientów regularnie korzystających z nich. Obecnie przy użyciu zweryfikowanego adresu e-mail jest jedyną obsługiwaną metodą odzyskiwania.

> [!NOTE]
> Ten artykuł ma zastosowanie do funkcji samoobsługowego resetowania hasła używanej w kontekście usługi przepływu użytkowników **logowania** w wersji 1, która używa **konta lokalnego logowania** jako dostawcy tożsamości. Jeśli chcesz w pełni dostosowywalne przepływy użytkownika resetowania haseł wywoływane z poziomu aplikacji, zobacz [ten artykuł](active-directory-b2c-reference-policies.md).
> 
> 

Domyślnie katalog nie ma włączonej funkcji samoobsługowego resetowania hasła. Wykonaj następujące kroki, aby je włączyć:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako administrator subskrypcji. To samo konto służbowe lub szkolne konto Microsoft, które zostało użyte do utworzenia katalogu.
2. Otwórz **Azure Active Directory** (na pasku nawigacyjnym po lewej stronie).
3. Przewiń w dół w bloku opcje i wybierz pozycję **Resetowanie hasła**.
4. Ustaw funkcję samoobsługowego **resetowania hasła** na **wszystkie**. 
5. Kliknij przycisk **Zapisz** w górnej części strony. Wszystko gotowe!

Aby przetestować, użyj funkcji "Uruchom teraz" w dowolnym przepływie użytkownika logowania, który ma konta lokalne jako dostawcę tożsamości. Na stronie logowania do konta lokalnego (w przypadku wprowadzenia adresu e-mail i hasła lub nazwy użytkownika i hasła) kliknij pozycję **nie można uzyskać dostępu do konta?** , aby zweryfikować środowisko klienta.

> [!NOTE]
> Strony samoobsługowego resetowania hasła można dostosować przy użyciu [funkcji znakowania firmowego](../active-directory/fundamentals/customize-branding.md).
> 
> 

