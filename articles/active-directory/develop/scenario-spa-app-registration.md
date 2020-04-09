---
title: Rejestrowanie aplikacji jednostronicowych — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację jednostronicową (rejestracja aplikacji)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 6f690a8b3436a45d434ccad2bbaa7d2a1b0b76aa
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882152"
---
# <a name="single-page-application-app-registration"></a>Aplikacja jednostronicowa: rejestracja aplikacji

Na tej stronie opisano szczegóły rejestracji aplikacji dla aplikacji jednostronicowej (SPA).

Wykonaj kroki, aby [zarejestrować nową aplikację na platformie tożsamości firmy Microsoft](quickstart-register-app.md)i wybierz obsługiwane konta dla aplikacji. Scenariusz SPA może obsługiwać uwierzytelnianie przy użyciu kont w organizacji lub dowolnej organizacji i osobistych kont Microsoft.

Następnie zapoznaj się z konkretnymi aspektami rejestracji aplikacji, które mają zastosowanie do aplikacji jednostronicowych.

## <a name="register-a-redirect-uri"></a>Rejestrowanie identyfikatora URI przekierowania

Przepływ niejawny wysyła tokeny w przekierowaniu do aplikacji jednostronicowej uruchomionej w przeglądarce sieci web. Dlatego ważne jest, aby zarejestrować identyfikator URI przekierowania, gdzie aplikacja może odbierać tokeny. Upewnij się, że identyfikator URI przekierowania dokładnie pasuje do identyfikatora URI dla aplikacji.

W [witrynie Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908)przejdź do zarejestrowanej aplikacji. Na stronie **Uwierzytelnianie** aplikacji wybierz platformę **sieci Web.** Wprowadź wartość identyfikatora URI przekierowania dla aplikacji w polu **Przekierowanie identyfikatora URI.**

## <a name="enable-the-implicit-flow"></a>Włączanie przepływu niejawnego

Na tej samej stronie **uwierzytelniania** w obszarze **Ustawienia zaawansowane**należy również włączyć **niejawne przyznanie**. Jeśli aplikacja loguje się tylko do użytkowników i otrzymuje tokeny identyfikatorów, wystarczy zaznaczyć pole wyboru **tokeny identyfikatorów.**

Jeśli aplikacja musi również uzyskać tokeny dostępu do wywoływania interfejsów API, upewnij się, aby **zaznaczyć tokeny dostępu** pole wyboru, jak również. Aby uzyskać więcej informacji, zobacz [tokeny identyfikatorów](./id-tokens.md) i [tokeny dostępu](./access-tokens.md).

## <a name="api-permissions"></a>Uprawnienia aplikacji

Aplikacje jednostronicowe mogą wywoływać interfejsy API w imieniu zalogowanego użytkownika. Muszą zażądać delegowanych uprawnień. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie uprawnień dostępu do internetowych interfejsów API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-spa-app-configuration.md)
