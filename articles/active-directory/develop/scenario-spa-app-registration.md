---
title: Aplikacja jednostronicowa (rejestracji aplikacji) — Platforma tożsamości firmy Microsoft
description: Informacje o sposobie tworzenia aplikacji jednostronicowych (rejestracji aplikacji)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1faf4669dca2aaaf3f873e66f859473ccd99f10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074833"
---
# <a name="single-page-application---app-registration"></a>Aplikacja jednostronicowa — rejestrowanie aplikacji

Tej stronie wyjaśniamy specyfiki rejestracji aplikacji dla aplikacji jednostronicowej (SPA).

Postępuj zgodnie z instrukcjami, aby [zarejestrowania nowej aplikacji z platformą tożsamości Microsoft](quickstart-register-app.md)i wybierz obsługiwanych kont dla swojej aplikacji. Scenariusz SPA może obsługiwać uwierzytelnianie przy użyciu kont w organizacji lub dowolnej organizacji i osobistych kont Microsoft.

Dowiedz się, konkretnych aspektach rejestracji aplikacji, które są stosowane do jednej strony aplikacji.

## <a name="register-a-redirect-uri"></a>Zarejestruj identyfikator URI przekierowania

Niejawny przepływ wysyła tokeny w przekierowanie do aplikacji jednostronicowej działającej w przeglądarce sieci web. Dlatego jest ważne wymagania, można zarejestrować identyfikatora URI, których aplikacja może odbierać tokeny przekierowania. Upewnij się, że przekierowania, który pasuje do identyfikatora URI z identyfikatora URI dla aplikacji.

W [witryny Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908), przejdź do już aplikację zarejestrowaną w **uwierzytelniania** strony aplikacji, wybierz pozycję **Web** platformy i wprowadź wartość Identyfikator URI przekierowania aplikacji na platformie **identyfikator URI przekierowania** pola.

## <a name="enable-the-implicit-flow"></a>Włącz niejawny przepływ

W tym samym **uwierzytelniania** w obszarze **Zaawansowane ustawienia**, należy również włączyć **przyznawanie niejawne**. Jeśli aplikacja działa tylko logowania użytkowników i uzyskiwania tokenów Identyfikatora, jest wystarczające, aby umożliwić **tokeny Identyfikatora** pola wyboru.

Jeśli aplikacja wymaga również uzyskiwanie tokenów dostępu w celu wywoływania interfejsów API, upewnij się umożliwić **tokeny dostępu** również pole wyboru. Aby uzyskać więcej informacji, zobacz [tokeny Identyfikatora](./id-tokens.md) i [tokeny dostępu](./access-tokens.md).

## <a name="api-permissions"></a>Uprawnienia do interfejsu API

Aplikacje jednej strony można wywołać interfejsy API w imieniu zalogowanego użytkownika. Muszą zażądać uprawnień delegowanych. Aby uzyskać więcej informacji, zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-spa-app-configuration.md)
