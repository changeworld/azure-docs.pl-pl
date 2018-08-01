---
title: Serwer Proxy aplikacji usługi Azure Active Directory i Tableau | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć serwera Proxy aplikacji usługi Azure Active Directory (Azure AD), aby zapewnić dostęp zdalny dla danego wdrożenia Tableau.  .
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 7cace1af527c1c7c80bf0e23f7a88aa9ac9f9d03
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365026"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Serwer Proxy aplikacji usługi Azure Active Directory i Tableau 

Usługa Azure Active Directory serwera Proxy aplikacji i Tableau wspólnie opracowały upewnij się, że możesz łatwo udostępnić dostępu zdalnego we wdrożeniu Tableau za pomocą serwera Proxy aplikacji. W tym artykule opisano sposób konfigurowania tego scenariusza.  

## <a name="prerequisites"></a>Wymagania wstępne 

Scenariusz, w tym artykule założono, że masz:

- [TABLEAU](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) skonfigurowane. 

- [Łącznik serwera Proxy aplikacji](application-proxy-enable.md) zainstalowane. 

 

## <a name="enabling-application-proxy-for-tableau"></a>Włączanie serwera Proxy aplikacji dla firmy Tableau 

Jeśli chcesz użyć serwera Proxy aplikacji dla firmy Tableau, musisz wysłać wiadomość e-mail na adres [ aadapfeedback@microsoft.com ](mailto:aadapfeedback@microsoft.com) można pobrać w tym scenariuszu włączone.
W wiadomości e-mail:

-   Na użytek Włączanie serwera Proxy aplikacji Tableau jako podmiot
-   Uwzględnić swój identyfikator dzierżawy w treści    

Otrzymasz potwierdzenie, gdy jesteś gotowy do korzystania z aplikacji. Możesz ukończyć konfiguracji podczas oczekiwania na potwierdzenie.


 

## <a name="publish-your-applications-in-azure"></a>Publikuj swoje aplikacje na platformie Azure 

Aby opublikować Tableau, musisz opublikować aplikację w witrynie Azure Portal.

W przypadku:

- Szczegółowe instrukcje z kroków 1 – 8, zobacz [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-publish-azure-portal.md). 
- Informacje o sposobach znajdowania Tableau wartości dla pól serwera Proxy aplikacji można znaleźć w dokumentacji Tableau.  

**Aby opublikować aplikację**: 


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) jako administrator globalny. 

2. Wybierz **usługi Azure Active Directory > aplikacje dla przedsiębiorstw**. 

3. Wybierz **Dodaj** w górnej części bloku. 

4. Wybierz **aplikacje lokalne**. 

5. Wypełnij wymagane pola informacji o nowej aplikacji. Użyj poniższych wskazówek, aby ustawienia: 

    - **Wewnętrzny adres URL**: Ta aplikacja powinna mieć wewnętrzny adres URL, który jest adresem URL Tableau, sam. Na przykład `https://adventure-works.tableau.com`. 

    - **Metoda wstępnego uwierzytelnienia**: usługi Azure Active Directory (zalecane, ale nie wymagane). 

6. Wybierz **Dodaj** w górnej części bloku. Aplikacja zostanie dodany, a zostanie otwarte menu skrócone. 

7. Wybierz z menu skrócone **przypisać użytkownika do testowania**, i Dodaj co najmniej jednego użytkownika do aplikacji. Upewnij się, że to konto testu ma dostęp do aplikacji w środowisku lokalnym. 

8. Wybierz **przypisać** można zapisać przypisania użytkownika testowego. 

9. (Opcjonalnie) Na stronie zarządzania aplikacji wybierz **logowanie jednokrotne**. Wybierz **zintegrowane uwierzytelnianie Windows** z menu rozwijanego, a następnie wypełnij wymagane pola, zgodnie z konfiguracją Tableau. Wybierz pozycję **Zapisz**. 

 

## <a name="testing"></a>Testowanie 

Twoja aplikacja jest teraz gotowe do testu. Dostęp zewnętrzny adres URL używany do publikowania Tableau, i zaloguj się jako użytkownik przypisany do obydwu aplikacji.



## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o serwerze Proxy aplikacji usługi Azure AD, zobacz [jak zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych](application-proxy.md).

