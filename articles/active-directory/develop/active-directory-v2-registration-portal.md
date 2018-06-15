---
title: Tematy pomocy portalu rejestracji aplikacji | Dokumentacja firmy Microsoft
description: Opis różnych funkcji w portalu rejestracji aplikacji firmy Microsoft.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.openlocfilehash: 9d38f6e6d6b9fa47b1cd1497820f7ff887954ad5
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156191"
---
# <a name="app-registration-reference"></a>Informacje dotyczące rejestracji aplikacji
Ten dokument zawiera kontekstu i opisy różnych funkcji w portalu rejestracji aplikacji Microsoft [ https://apps.dev.microsoft.com ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/).

## <a name="my-applications"></a>Moje aplikacje
Ta lista zawiera wszystkie aplikacje zarejestrowany do użytkowania z punktem końcowym v2.0 usługi Azure AD. Te aplikacje mają możliwość do logowania użytkowników z osobistego konta Microsoft i kont pracy/służbowych z usługi Azure Active Directory. Aby dowiedzieć się więcej na temat punktu końcowego v2.0 usługi Azure AD, zobacz [omówienie v2.0](active-directory-appmodel-v2-overview.md). Te aplikacje można również zintegrować z punktu końcowego uwierzytelniania konta Microsoft, `https://login.live.com`.

## <a name="live-sdk-applications"></a>Na żywo aplikacji zestawu SDK
Ta lista zawiera wszystkie aplikacje zarejestrowany do użytkowania wyłącznie z kontem Microsoft. Nie są włączone do użytku w usłudze Azure Active Directory. Jest to, gdzie znaleźć wszystkie aplikacje, które zostały wcześniej zarejestrowane portalu dla deweloperów MSA w `https://account.live.com/developers/applications`. Wszystkie funkcje, które należy wcześniej wykonać `https://account.live.com/developers/applications` można teraz wykonać w tym nowego portalu `https://apps.dev.microsoft.com`. Jeśli masz dodatkowe pytania dotyczące aplikacji konta Microsoft, skontaktuj się z nami.

## <a name="application-secrets"></a>Wpisy tajne aplikacji
Hasła aplikacji są poświadczenia, które umożliwią niezawodnych aplikacji do wykonywania [uwierzytelnianie klienta](http://tools.ietf.org/html/rfc6749#section-2.3) z usługą Azure AD. OAuth i OpenID Connect, klucz tajny aplikacji jest często określana jako `client_secret`. W protokole v2.0, każda aplikacja, która odbiera token zabezpieczający w lokalizacji adresowanego sieci web (przy użyciu `https` schemat) należy użyć klucz tajny aplikacji do identyfikacji do usługi Azure AD po realizacji tego tokenu zabezpieczającego. Ponadto natywnego klienta, który odbiera tokenów na urządzeniu będzie zabronione użycie klucz tajny aplikacji w celu uwierzytelniania klienta. To odradza magazynu kluczy tajnych w środowiskach niezabezpieczonych.

Każda aplikacja może zawierać dwa klucze tajne prawidłową aplikację w danym momencie. Dzięki utrzymywaniu dwa klucze tajne, masz możliwość wykonywania okresowe Przerzucanie klucza w środowisku całej aplikacji. Po migracji całości aplikacji do nowego klucza tajnego, możesz usunąć stary klucz tajny i zainicjowania obsługi kolejnego.

W tej chwili tylko dwa typy aplikacji kluczy tajnych są dozwolone w portalu rejestracji aplikacji. Wybieranie **wygenerować nowe hasło** generuje i przechowuje wspólny klucz tajny w magazynie odpowiednich danych, którego można użyć w aplikacji. Wybieranie **wygenerować nową parę klucz** tworzy nowe pary kluczy publicznych i prywatnych, które mogą być pobierane i używany do uwierzytelniania klienta do usługi Azure AD. Wybieranie **przekazać klucza publicznego** umożliwia użycie własnego pary kluczy publiczny/prywatny.
Należy przekazać certyfikat zawierający klucz publiczny.

## <a name="profile"></a>Profil
Profil części portalu rejestracji aplikacji można dostosować stronę logowania w aplikacji. W tej chwili można zmienić logowania strony aplikacji logo, warunków adres URL usługi i adres URL prywatności. Logo musi być przezroczystym obrazem o wymiarach 48 x 48 lub 50 x 50 pikseli w formacie pliku GIF, PNG lub JPEG o maksymalnym rozmiarze 15 KB. Spróbuj zmiana wartości i wyświetlanie wynikowego strony logowania!

## <a name="live-sdk-support"></a>Obsługa w zestawie SDK na żywo
Po włączeniu "Live SDK Obsługa" kluczy tajnych żadnych aplikacji, należy utworzyć będą udostępniane w obu programu Azure AD i magazyny danych Account firmy Microsoft. Pozwala to aplikacjom łączyć bezpośrednio z usługą Microsoft Account (login.live.com). Jeśli chcesz skompilować aplikację przy użyciu Account Microsoft bezpośrednio (a nie przy użyciu punktu końcowego v2.0 usługi Azure AD), należy upewnić się, czy włączona jest obsługa Live SDK.

Wyłączenie obsługi zestaw Live SDK gwarantuje, że klucz tajny aplikacji tylko są zapisywane na dane usługi Azure AD przechowywania. Dane usługi Azure AD magazynu zawiera przepisy dotyczące korporacyjnej Zezwalaj, aby spełnić pewne standardy, takie jak FISMA zgodności. Jeśli zostanie włączona obsługa zestaw Live SDK, aplikacja może nie zapewnienia zgodności z niektóre z tymi standardami.

Jeśli planujesz wyłącznie stale używać punktu końcowego v2.0 usługi Azure AD, można wyłączyć bezpiecznie zestaw Live SDK pomocy technicznej.

