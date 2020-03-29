---
title: Samoobsługowe resetowanie hasła w usłudze Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Pokazuje, jak skonfigurować samoobsługowe resetowanie hasła dla klientów w usłudze Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d6dad52c8a3e63c64bb8e0e0030e8c50b5bab42c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183112"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Konfigurowanie samoobsługowego resetowania hasła dla klientów

Dzięki funkcji samoobsługowego resetowania hasła klienci, którzy zarejestrowali się na kontach lokalnych, mogą samodzielnie resetować swoje hasła. Znacznie zmniejsza to obciążenie personelu pomocy technicznej, zwłaszcza jeśli aplikacja ma miliony klientów korzystających z niej regularnie. Obecnie przy użyciu zweryfikowanego adresu e-mail jest jedyną obsługiwaną metodą odzyskiwania.

> [!NOTE]
> Ten artykuł dotyczy samoobsługowego resetowania hasła używanego w kontekście przepływu użytkownika **logowania w** wersji 1, który używa logowania **konta lokalnego** jako dostawcy tożsamości. Jeśli potrzebujesz w pełni konfigurowalnych przepływów użytkownika resetowania hasła wywoływanych z aplikacji, zobacz [ten artykuł](user-flow-overview.md).
>
>

Domyślnie katalog nie ma włączonego samoobsługowego resetowania hasła. Aby ją włączyć, należy wykonać następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator subskrypcji. Jest to to samo konto służbowe lub to samo konto Microsoft, które zostało użyte do utworzenia katalogu.
2. Otwórz **usługę Azure Active Directory** (na pasku nawigacyjnym po lewej stronie).
3. Przewiń w dół na bloku opcji i wybierz **opcję Resetowanie hasła**.
4. Ustaw **samoobsługowe resetowanie hasła włączone** na **Wszystkie**.
5. Kliknij przycisk **Zapisz** w górnej części strony. Gotowe!

Aby przetestować, użyj funkcji "Uruchom teraz" w dowolnym przepływie użytkownika logowania, który ma konta lokalne jako dostawca tożsamości. Na stronie logowania do konta lokalnego (gdzie wprowadzasz adres e-mail i hasło lub nazwę użytkownika i hasło) kliknij pozycję **Nie możesz uzyskać dostępu do konta?**

> [!NOTE]
> Strony samoobsługowego resetowania hasła można dostosować za pomocą [funkcji znakowania firmy.](../active-directory/fundamentals/customize-branding.md)
>
>

