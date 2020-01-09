---
title: Rejestrowanie aplikacji jednostronicowych — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację jednostronicową (Rejestracja aplikacji)
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
ms.openlocfilehash: f964d4b4c7032599cf8f74b285f819581fae907b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423654"
---
# <a name="single-page-application-app-registration"></a>Aplikacja jednostronicowa: Rejestracja aplikacji

Ta strona zawiera wyjaśnienia dotyczące rejestracji aplikacji dla aplikacji jednostronicowej (SPA).

Postępuj zgodnie z instrukcjami, aby [zarejestrować nową aplikację na platformie tożsamości firmy Microsoft](quickstart-register-app.md), a następnie wybierz obsługiwane konta aplikacji. Scenariusz SPA może obsługiwać uwierzytelnianie z kontami w organizacji lub dowolną organizację i osobiste konta Microsoft.

Następnie zapoznaj się z określonymi aspektami rejestracji aplikacji, które mają zastosowanie do aplikacji jednostronicowych.

## <a name="register-a-redirect-uri"></a>Rejestrowanie identyfikatora URI przekierowania

Przepływ niejawny wysyła tokeny w przekierowaniu do aplikacji jednostronicowej działającej w przeglądarce sieci Web. Ważne jest, aby zarejestrować identyfikator URI przekierowania, w którym aplikacja może odbierać tokeny. Upewnij się, że identyfikator URI przekierowania dokładnie odpowiada identyfikatorowi URI aplikacji.

W [Azure Portal](https://go.microsoft.com/fwlink/?linkid=2083908)przejdź do swojej zarejestrowanej aplikacji. Na stronie **uwierzytelnianie** w aplikacji wybierz platformę **sieci Web** . Wprowadź wartość identyfikatora URI przekierowania dla aplikacji w polu **Identyfikator URI przekierowania** .

## <a name="enable-the-implicit-flow"></a>Włącz przepływ niejawny

Na tej samej stronie **uwierzytelniania** w obszarze **Ustawienia zaawansowane**należy również włączyć **niejawny**przydział. Jeśli aplikacja loguje się tylko do użytkowników i otrzymuje tokeny identyfikatora, wystarczy zaznaczyć pole wyboru **tokeny identyfikatorów** .

Jeśli aplikacja wymaga również uzyskania tokenów dostępu do wywoływania interfejsów API, pamiętaj o zaznaczeniu pola wyboru **tokeny dostępu** . Aby uzyskać więcej informacji, zobacz [identyfikatory tokenów](./id-tokens.md) i [tokeny dostępu](./access-tokens.md).

## <a name="api-permissions"></a>Uprawnienia do interfejsu API

Aplikacje jednostronicowe mogą wywoływać interfejsy API w imieniu zalogowanego użytkownika. Muszą oni zażądać uprawnień delegowanych. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-spa-app-configuration.md)
