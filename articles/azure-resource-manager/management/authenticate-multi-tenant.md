---
title: Uwierzytelnianie w wielu dzierżawach
description: W tym artykule opisano, jak usługa Azure Resource Manager obsługuje żądania uwierzytelniania między dzierżawami.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478827"
---
# <a name="authenticate-requests-across-tenants"></a>Uwierzytelnij żądania między dzierżawcami

Podczas tworzenia aplikacji wielodostępnych może być konieczne obsługa żądań uwierzytelniania dla zasobów, które znajdują się w różnych dzierżaw. Typowym scenariuszem jest, gdy maszyna wirtualna w jednej dzierżawie musi dołączyć do sieci wirtualnej w innej dzierżawie. Usługa Azure Resource Manager udostępnia wartość nagłówka do przechowywania tokenów pomocniczych w celu uwierzytelnienia żądań do różnych dzierżaw.

## <a name="header-values-for-authentication"></a>Wartości nagłówka uwierzytelniania

Żądanie ma następujące wartości nagłówka uwierzytelniania:

| Nazwa nagłówka | Opis | Przykładowa wartość |
| ----------- | ----------- | ------------ |
| Autoryzacja | Token podstawowy | Token &lt;podstawowy okaziciela&gt; |
| x-ms-autoryzacja pomocnicza | Tokeny pomocnicze | Token &lt;pomocniczy na&gt;okaziciela1 &lt;, EncryptedBearer&gt; &lt;auxiliary-token2 , Nosownik pomocniczy-token3&gt; |

Nagłówek pomocniczy może pomieścić do trzech tokenów pomocniczych. 

W kodzie aplikacji z wieloma dzierżawami pobierz token uwierzytelniania dla innych dzierżawców i przechowuj je w nagłówkach pomocniczych. Wszystkie tokeny muszą pochodzić od tego samego użytkownika lub aplikacji. Użytkownik lub aplikacja musi zostać zaproszona jako gość do innych dzierżawców.

## <a name="processing-the-request"></a>Przetwarzanie żądania

Gdy aplikacja wysyła żądanie do Menedżera zasobów, żądanie jest uruchamiane w ramach tożsamości z tokenu podstawowego. Token podstawowy musi być prawidłowy i niewygasły. Ten token musi pochodzić od dzierżawy, która może zarządzać subskrypcją.

Gdy żądanie odwołuje się do zasobu z innej dzierżawy, Menedżer zasobów sprawdza tokeny pomocnicze, aby ustalić, czy żądanie może być przetwarzane. Wszystkie tokeny pomocnicze w nagłówku muszą być prawidłowe i niewygasłe. Jeśli dowolny token wygasł, Menedżer zasobów zwraca kod odpowiedzi 401. Odpowiedź zawiera identyfikator klienta i identyfikator dzierżawy z tokenu, który nie jest prawidłowy. Jeśli nagłówek pomocniczy zawiera prawidłowy token dla dzierżawcy, przetwarzane jest żądanie między dzierżawą.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o żądaniach uwierzytelniania, zobacz [Przepływy uwierzytelniania i scenariusze aplikacji](../../active-directory/develop/authentication-flows-app-scenarios.md).
* Aby uzyskać więcej informacji na temat tokenów, zobacz [Tokeny dostępu usługi Azure Active Directory](../../active-directory/develop/access-tokens.md).
