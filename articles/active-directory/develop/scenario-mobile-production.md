---
title: Przygotowywanie interfejsów API sieci web do wywoływania aplikacji mobilnych do produkcji | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje internetowe interfejsy API. (Przygotowanie aplikacji do produkcji).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 28ace84f9a80b71209d7963d02b66317292b151b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882543"
---
# <a name="prepare-mobile-apps-for-production"></a>Przygotowywanie aplikacji mobilnych do produkcji

Ten artykuł zawiera szczegółowe informacje o tym, jak poprawić jakość i niezawodność aplikacji mobilnej przed przeniesieniem jej do produkcji.

## <a name="handle-errors"></a>Obsługa błędów

Podczas przygotowywania aplikacji mobilnej do produkcji może wystąpić kilka warunków błędu. Główne sprawy, które będą obsługiwane są ciche błędy i rezerwy do interakcji. Inne warunki, które należy wziąć pod uwagę obejmują sytuacje bez sieci, awarie usługi, wymagania dotyczące zgody administratora i inne przypadki specyficzne dla scenariusza.

Dla każdego typu biblioteki uwierzytelniania firmy Microsoft (MSAL) można znaleźć przykładowy kod i zawartość wiki, która opisuje sposób obsługi warunków błędów:

- [ESAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [E-czajka msal z systemem iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Łagodzenie i badanie problemów

Aby lepiej zdiagnozować problemy w aplikacji, zbieraj dane. Aby uzyskać informacje o rodzajach danych, które można zbierać, zobacz [Rejestrowanie w aplikacjach MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-logging).

Oto kilka sugestii dotyczących gromadzenia danych:

- Użytkownicy mogą poprosić o pomoc, gdy mają problemy. Najlepszym rozwiązaniem jest przechwytywanie i tymczasowe przechowywanie dzienników. Podaj lokalizację, w której użytkownicy mogą przekazywać dzienniki. Msal udostępnia rozszerzenia rejestrowania, aby przechwycić szczegółowe informacje o uwierzytelnianiu.

- Jeśli dane telemetryczne są dostępne, włącz ją za pośrednictwem usługi MSAL, aby zebrać dane dotyczące sposobu logowania się użytkowników do aplikacji.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

Aby wypróbować dodatkowe przykłady, zobacz [Publiczne aplikacje klienckie dla komputerów i urządzeń przenośnych](sample-v2-code.md#desktop-and-mobile-public-client-apps).
