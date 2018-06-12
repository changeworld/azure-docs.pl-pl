---
title: Serwer Proxy aplikacji usługi Azure Active Directory i Tableau | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać serwera Proxy aplikacji usługi Azure Active Directory (Azure AD) do zapewniania dostępu zdalnego dla danego wdrożenia Tableau.  .
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 832d37e6c168a7b25adecee967b6523f6cea5554
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303611"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Serwer Proxy aplikacji usługi Azure Active Directory i Tableau 

Azure Active Directory serwera Proxy aplikacji i Tableau ma współpracę aby mieć pewność, że łatwo można używać serwera Proxy aplikacji do zapewniania dostępu zdalnego dla danego wdrożenia Tableau. W tym artykule opisano sposób konfigurowania tego scenariusza.  

## <a name="prerequisites"></a>Wymagania wstępne 

Scenariusz, w tym artykule przyjęto założenie, że masz:

- [TABLEAU](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) skonfigurowany. 

- [Łącznika serwera Proxy aplikacji](application-proxy-enable.md) zainstalowane. 

 

## <a name="enabling-application-proxy-for-tableau"></a>Włączanie serwera Proxy aplikacji dla Tableau 

Jeśli chcesz używać serwera Proxy aplikacji dla Tableau, musisz wysłać wiadomość e-mail na adres [ aadapfeedback@microsoft.com ](mailto:aadapfeedback@microsoft.com) można uzyskać w tym scenariuszu włączone.
W wiadomości e-mail:

-   Włącz serwer Proxy aplikacji dla Tableau używa jako podmiotu
-   Obejmują Identyfikatora dzierżawy w treści    

Możesz uzyskać potwierdzenie, gdy wszystko będzie gotowe do korzystania z aplikacji. Możesz ukończyć konfiguracji podczas oczekiwania na potwierdzenie.


 

## <a name="publish-your-applications-in-azure"></a>Publikowanie własnych aplikacji na platformie Azure 

Aby opublikować Tableau, należy opublikować aplikację w portalu Azure.

W przypadku:

- Szczegółowe instrukcje kroki 1 – 8, zobacz [publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-publish-azure-portal.md). 
- Informacje o sposobach znajdowania Tableau wartości w polach serwera Proxy aplikacji można znaleźć w dokumentacji Tableau.  

**Aby opublikować aplikację**: 


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) jako administrator globalny. 

2. Wybierz **usługi Azure Active Directory > aplikacje dla przedsiębiorstw**. 

3. Wybierz **Dodaj** w górnej części bloku. 

4. Wybierz **lokalnej aplikacji**. 

5. Wypełnij wymagane pola informacje o nowej aplikacji. Użyj poniższych wskazówek dla ustawienia: 

    - **Wewnętrzny adres URL**: Ta aplikacja ma wewnętrzny adres URL jest adresem URL Tableau, sama. Na przykład `https://adventure-works.tableau.com`. 

    - **Metoda wstępnego uwierzytelnienia**: Azure Active Directory (zalecane, ale nie wymagane). 

6. Wybierz **Dodaj** w górnej części bloku. Aplikacja zostanie dodany, a następnie otwiera menu szybki start. 

7. Wybierz z menu szybki start **przypisać użytkownika do testowania**, i Dodaj przynajmniej jednego użytkownika do aplikacji. Upewnij się, że to konto testu ma dostęp do aplikacji lokalnych. 

8. Wybierz **przypisać** można zapisać przypisanie użytkownika testu. 

9. (Opcjonalnie) Na stronie Zarządzanie aplikacjami, wybierz **logowanie jednokrotne**. Wybierz **zintegrowane uwierzytelnianie systemu Windows** z listy rozwijanej, a następnie wypełnij wymagane pola, zgodnie z konfiguracją Tableau. Wybierz pozycję **Zapisz**. 

 

## <a name="testing"></a>Testowanie 

Aplikacja jest teraz gotowy do testowania. Dostęp do zewnętrznego adresu URL używany do publikowania Tableau, i zaloguj się jako użytkownik, któremu przypisano do obydwu aplikacji.



## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji dotyczących serwera Proxy aplikacji usługi Azure AD, zobacz [jak zapewnić bezpieczny zdalny dostęp do aplikacji lokalnych](application-proxy.md).

