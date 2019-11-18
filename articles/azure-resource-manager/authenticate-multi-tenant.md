---
title: Uwierzytelnianie w wielu dzierżawach
description: Opisuje, w jaki sposób Azure Resource Manager obsługuje żądania uwierzytelniania między dzierżawcami.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 1dccfc522179cd88e69bc5a58307b343aaef8f9e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149979"
---
# <a name="authenticate-requests-across-tenants"></a>Uwierzytelnianie żądań między dzierżawcami

Podczas tworzenia aplikacji z wieloma dzierżawcami może być konieczne obsłużenie żądań uwierzytelniania zasobów należących do różnych dzierżawców. Typowy scenariusz polega na tym, że maszyna wirtualna w jednej dzierżawie musi przyłączyć się do sieci wirtualnej w innej dzierżawie. Azure Resource Manager udostępnia wartość nagłówka do przechowywania tokenów pomocniczych w celu uwierzytelniania żądań do innych dzierżawców.

## <a name="header-values-for-authentication"></a>Wartości nagłówka uwierzytelniania

Żądanie ma następujące wartości nagłówka uwierzytelniania:

| Nazwa nagłówka | Opis | Przykładowa wartość |
| ----------- | ----------- | ------------ |
| Autoryzacja | Token podstawowy | Okaziciela &lt;token podstawowy&gt; |
| x-MS-Authorization-pomocniczy | Tokeny pomocnicze | Posiadacz &lt;pomocniczy token1&gt;, EncryptedBearer &lt;pomocnicze token2&gt;, Okazicielaer &lt;pomocnicze token3&gt; |

Nagłówek pomocniczy może zawierać maksymalnie trzy tokeny pomocnicze. 

W kodzie aplikacji z wieloma dzierżawcami Pobierz token uwierzytelniania dla innych dzierżawców i Zapisz je w nagłówkach pomocniczych. Wszystkie tokeny muszą należeć do tego samego użytkownika lub aplikacji. Użytkownik lub aplikacja musiała zostać zaproszeni jako gość do innych dzierżawców.

## <a name="processing-the-request"></a>Przetwarzanie żądania

Gdy aplikacja wysyła żądanie do Menedżer zasobów, żądanie jest uruchamiane w ramach tożsamości z podstawowego tokenu. Token podstawowy musi być prawidłowy i niewygasły. Token musi być z dzierżawy, który może zarządzać subskrypcją.

Gdy żądanie odwołuje się do zasobu z innej dzierżawy, Menedżer zasobów sprawdza tokeny pomocnicze, aby określić, czy można przetworzyć żądanie. Wszystkie tokeny pomocnicze w nagłówku muszą być prawidłowe i niewygasłe. W przypadku wygaśnięcia dowolnego tokenu Menedżer zasobów zwraca kod odpowiedzi 401. Odpowiedź zawiera identyfikator klienta i identyfikator dzierżawy z tokenu, który jest nieprawidłowy. Jeśli nagłówek pomocniczy zawiera prawidłowy token dla dzierżawcy, żądanie Cross dzierżawca jest przetwarzane.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o żądaniach uwierzytelniania, zobacz [przepływy uwierzytelniania i scenariusze aplikacji](../active-directory/develop/authentication-flows-app-scenarios.md).
* Aby uzyskać więcej informacji o tokenach, zobacz [Azure Active Directory tokeny dostępu](../active-directory/develop/access-tokens.md).
