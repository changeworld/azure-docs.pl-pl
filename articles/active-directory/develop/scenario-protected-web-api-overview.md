---
title: Chroniony internetowy interfejs API — Przegląd | Azure
description: Dowiedz się, jak utworzyć chroniony internetowy interfejs API (omówienie).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5bc0075e6604bd1f94531040f2e4a0628e70667
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074893"
---
# <a name="scenario-protected-web-api"></a>Scenariusz: Chroniony internetowy interfejs API

W tym scenariuszu pokazano, jak udostępnić internetowy interfejs API i jak można go chronić tak, aby tylko uwierzytelnieni użytkownicy mogą uzyskiwać dostęp do interfejsu API. Należy włączyć uwierzytelnionych użytkowników z zarówno pracy i konta służbowe lub osobiste konto Microsoft do użycia interfejsu API sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Szczegółowe informacje

Poniżej przedstawiono niektóre szczegółowe informacje, musisz wiedzieć, aby chronić interfejsy API sieci web:

- Rejestracja aplikacji należy udostępnić co najmniej jeden zakres. Token wersji zaakceptowane przez internetowy interfejs API zależy od logowania w grupie odbiorców.
- Konfiguracja kodu dla internetowego interfejsu API musi Zweryfikuj token, który jest używany podczas wywoływania interfejsu API sieci web.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-protected-web-api-app-registration.md)
