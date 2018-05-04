---
title: Serwer Proxy aplikacji usługi Azure Active Directory i Tableau | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać serwera Proxy aplikacji usługi Azure Active Directory (Azure AD) do zapewniania dostępu zdalnego dla danego wdrożenia Tableau.  .
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 480e1cd72bcb42d7f39a92fc49e7c3d66ecf05f8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Serwer Proxy aplikacji usługi Azure Active Directory i Tableau 

Azure Active Directory serwera Proxy aplikacji i Tableau ma współpracę aby mieć pewność, że łatwo można używać serwera Proxy aplikacji do zapewniania dostępu zdalnego dla danego wdrożenia Tableau. W tym artykule opisano sposób konfigurowania tego scenariusza.  

## <a name="prerequisites"></a>Wymagania wstępne 

Scenariusz, w tym artykule przyjęto założenie, że masz:

- [TABLEAU](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#reverse-proxy-server) skonfigurowany. 

- [Łącznika serwera Proxy aplikacji](active-directory-application-proxy-enable.md) zainstalowane. 

 

## <a name="enabling-application-proxy-for-tableau"></a>Włączanie serwera Proxy aplikacji dla Tableau 

Jeśli chcesz używać serwera Proxy aplikacji dla Tableau, musisz wysłać wiadomość e-mail na adres [ aadapfeedback@microsoft.com ](mailto:aadapfeedback@microsoft.com) można uzyskać w tym scenariuszu włączone.
W wiadomości e-mail:

-   Włącz serwer Proxy aplikacji dla Tableau używa jako podmiotu
-   Obejmują Identyfikatora dzierżawy w treści    

Możesz uzyskać potwierdzenie, gdy wszystko będzie gotowe do korzystania z aplikacji. Zazwyczaj trwa to około tygodnia. Jednak możesz ukończyć konfiguracji podczas oczekiwania na potwierdzenie.


 

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

Aby uzyskać więcej informacji dotyczących serwera Proxy aplikacji usługi Azure AD, zobacz [jak zapewnić bezpieczny zdalny dostęp do aplikacji lokalnych](active-directory-application-proxy-get-started.md).

