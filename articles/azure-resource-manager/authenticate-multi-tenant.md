---
title: Uwierzytelnianie między dzierżawcami — Azure Resource Manager
description: Opisuje, w jaki sposób Azure Resource Manager obsługuje żądania uwierzytelniania między dzierżawcami.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: tomfitz
ms.openlocfilehash: 625a17156eaf199af0d51151c6fd37769b8f7b4a
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848765"
---
# <a name="authenticate-requests-across-tenants"></a>Uwierzytelnianie żądań między dzierżawcami

Podczas tworzenia aplikacji z wieloma dzierżawcami może być konieczne obsłużenie żądań uwierzytelniania zasobów należących do różnych dzierżawców. Typowy scenariusz polega na tym, że maszyna wirtualna w jednej dzierżawie musi przyłączyć się do sieci wirtualnej w innej dzierżawie. Azure Resource Manager udostępnia wartość nagłówka do przechowywania tokenów pomocniczych w celu uwierzytelniania żądań do innych dzierżawców.

## <a name="header-values-for-authentication"></a>Wartości nagłówka uwierzytelniania

Żądanie ma następujące wartości nagłówka uwierzytelniania:

| Nazwa nagłówka | Opis | Przykładowa wartość |
| ----------- | ----------- | ------------ |
| Authorization | Token podstawowy | &lt;Podstawowy token okaziciela&gt; |
| x-MS-Authorization-pomocniczy | Tokeny pomocnicze | &lt;&gt; &lt;Pomocniczy pomocnik — token1&gt;, EncryptedBearer pomocniczy, token2 pomocniczy — token3 &lt;&gt; |

Nagłówek pomocniczy może zawierać maksymalnie trzy tokeny pomocnicze. 

W kodzie aplikacji z wieloma dzierżawcami Pobierz token uwierzytelniania dla innych dzierżawców i Zapisz je w nagłówkach pomocniczych. Wszystkie tokeny muszą należeć do tego samego użytkownika lub aplikacji. Użytkownik lub aplikacja musiała zostać zaproszeni jako gość do innych dzierżawców.

## <a name="processing-the-request"></a>Przetwarzanie żądania

Gdy aplikacja wysyła żądanie do Menedżer zasobów, żądanie jest uruchamiane w ramach tożsamości z podstawowego tokenu. Token podstawowy musi być prawidłowy i niewygasły. Token musi być z dzierżawy, który może zarządzać subskrypcją.

Gdy żądanie odwołuje się do zasobu z innej dzierżawy, Menedżer zasobów sprawdza tokeny pomocnicze, aby określić, czy można przetworzyć żądanie. Wszystkie tokeny pomocnicze w nagłówku muszą być prawidłowe i niewygasłe. W przypadku wygaśnięcia dowolnego tokenu Menedżer zasobów zwraca kod odpowiedzi 401. Odpowiedź zawiera identyfikator klienta i identyfikator dzierżawy z tokenu, który jest nieprawidłowy. Jeśli nagłówek pomocniczy zawiera prawidłowy token dla dzierżawcy, żądanie Cross dzierżawca jest przetwarzane.

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o wysyłaniu żądań uwierzytelniania przy użyciu interfejsów API Azure Resource Manager, zobacz [Używanie interfejsu API uwierzytelniania Menedżer zasobów do uzyskiwania dostępu do subskrypcji](resource-manager-api-authentication.md).
* Aby uzyskać więcej informacji o tokenach, zobacz [Azure Active Directory tokeny dostępu](/azure/active-directory/develop/access-tokens).
