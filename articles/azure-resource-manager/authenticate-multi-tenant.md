---
title: Uwierzytelnianie dla dzierżaw — usługi Azure Resource Manager
description: W tym artykule opisano sposób obsługi żądań uwierzytelniania usługi Azure Resource Manager dla dzierżaw.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: tomfitz
ms.openlocfilehash: 5370b9b6d6a8bee82f8feca6dbcbcd78a4c12193
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205609"
---
# <a name="authenticate-requests-across-tenants"></a>Uwierzytelniaj żądania dla dzierżaw

Podczas tworzenia aplikacji z wieloma dzierżawami, może być konieczne do obsługi żądań uwierzytelniania dla zasobów, które znajdują się w różnych dzierżawach. Typowym scenariuszem jest, gdy maszynę wirtualną w jednej dzierżawy musi dołączenia do sieci wirtualnej w innej dzierżawie. Usługa Azure Resource Manager udostępnia wartość nagłówka do przechowywania tokenów pomocniczych do uwierzytelniania żądań do różnych dzierżawach.

## <a name="header-values-for-authentication"></a>Wartości nagłówka uwierzytelniania

Żądanie zawiera następujące wartości nagłówka uwierzytelniania:

| Nazwa nagłówka | Opis | Przykładowa wartość |
| ----------- | ----------- | ------------ |
| Autoryzacja | Token podstawowy | Elementu nośnego &lt;token podstawowy&gt; |
| x-ms-authorization-auxiliary | Tokeny pomocnicze | Bearer &lt;auxiliary-token1&gt;; EncryptedBearer &lt;auxiliary-token2&gt;; Bearer &lt;auxiliary-token3&gt; |

Pomocnicze nagłówek może zawierać maksymalnie trzy tokeny pomocnicze. 

W kodzie aplikacji wielodostępnych uzyskiwanie uwierzytelnianie tokenu w przypadku innych dzierżaw i przechowywać je w nagłówkach pomocnicze. Wszystkie tokeny muszą pochodzić z tego samego użytkownika lub aplikacji. Użytkownik lub aplikacja musi zostali zaproszeni jako gościa do innych dzierżaw.

## <a name="processing-the-request"></a>Przetwarzanie żądania

Gdy aplikacja wysyła żądanie do usługi Resource Manager, żądanie jest uruchamiane z tożsamością z podstawowego tokenu. Token podstawowy musi być prawidłowy i niewygasłe. Ten token musi pochodzić z dzierżawy, która może zarządzać subskrypcją.

Gdy żądanie odwołuje się do zasobu z innej dzierżawy, Menedżer zasobów sprawdza tokeny pomocnicze, aby określić, jeśli można przetworzyć żądania. Wszystkie tokeny pomocnicze w nagłówku musi być prawidłowy i niewygasłe. Jeśli wszystkie token wygasł, usługi Resource Manager zwraca kod odpowiedzi 401. Odpowiedź zawiera klienta identyfikator i identyfikator dzierżawy z tokenu, który jest nieprawidłowy. Jeśli pomocniczy nagłówek zawiera prawidłowy token dla dzierżawy, przetwarzanie żądania między dzierżawy.

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać informacje dotyczące wysyłania żądań uwierzytelniania za pomocą interfejsów API usługi Azure Resource Manager, zobacz [interfejs API uwierzytelniania Użyj usługi Resource Manager do dostępu do subskrypcji](resource-manager-api-authentication.md).
* Aby uzyskać więcej informacji na temat tokenów, zobacz [tokenów dostępu usługi Azure Active Directory](/azure/active-directory/develop/access-tokens).
