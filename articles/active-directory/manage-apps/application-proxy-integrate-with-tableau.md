---
title: Serwer proxy i tableau aplikacji usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać serwera proxy aplikacji usługi Azure Active Directory (Azure AD) w celu zapewnienia dostępu zdalnego dla wdrożenia Tableau.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1aa99e7e71ad78a62c1a9da303b2ecc8347ebeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65783850"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Serwer proxy i tableau aplikacji usługi Azure Active Directory 

Usługi Azure Active Directory Application Proxy i Tableau nawiązały współpracę, aby upewnić się, że można łatwo używać serwera proxy aplikacji, aby zapewnić dostęp zdalny dla wdrożenia Tableau. W tym artykule wyjaśniono, jak skonfigurować ten scenariusz.  

## <a name="prerequisites"></a>Wymagania wstępne 

Scenariusz w tym artykule zakłada, że masz:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) skonfigurowany. 

- Zainstalowano [złącze serwera proxy aplikacji.](application-proxy-add-on-premises-application.md) 

 
## <a name="enabling-application-proxy-for-tableau"></a>Włączanie serwera proxy aplikacji dla tableau 

Serwer proxy aplikacji obsługuje przepływ grantów OAuth 2.0, który jest wymagany do poprawnego działania Tableau. Oznacza to, że nie ma już żadnych specjalnych kroków wymaganych do włączenia tej aplikacji, inne niż konfigurowanie go, wykonując kroki publikowania poniżej.


## <a name="publish-your-applications-in-azure"></a>Publikowanie aplikacji na platformie Azure 

Aby opublikować Tableau, należy opublikować aplikację w witrynie Azure Portal.

For:

- Szczegółowe instrukcje kroków 1-8, zobacz [Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md). 
- Informacje o znajdowaniu wartości Tableau dla pól Serwera proxy aplikacji można znaleźć w dokumentacji Tableau.  

**Aby opublikować aplikację:** 


1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator aplikacji. 

2. Wybierz **pozycję Azure Active Directory > aplikacje enterprise**. 

3. Wybierz **pozycję Dodaj** w górnej części bloku. 

4. Wybierz **opcję Aplikacja lokalna**. 

5. Wypełnij wymagane pola informacjami o nowej aplikacji. Użyj następujących wskazówek dotyczących ustawień: 

    - **Wewnętrzny adres URL:** Ta aplikacja powinna mieć wewnętrzny adres URL, który jest sam adres URL Tableau. Na przykład `https://adventure-works.tableau.com`. 

    - **Metoda uwierzytelniania wstępnego:** usługa Azure Active Directory (zalecana, ale nie jest wymagana). 

6. Wybierz **pozycję Dodaj** w górnej części bloku. Aplikacja zostanie dodana i otworzy się menu szybkiego startu. 

7. W menu szybki start wybierz pozycję **Przypisz użytkownika do testowania**i dodaj do aplikacji co najmniej jednego użytkownika. Upewnij się, że to konto testowe ma dostęp do aplikacji lokalnej. 

8. Wybierz **pozycję Przypisz,** aby zapisać przypisanie użytkownika testowego. 

9. (Opcjonalnie) Na stronie zarządzania aplikacjami wybierz pozycję **Logowanie jednokrotne**. Z menu rozwijanego wybierz **polecenie Zintegrowane uwierzytelnianie systemu Windows** i wypełnij wymagane pola na podstawie konfiguracji Tableau. Wybierz **pozycję Zapisz**. 

 

## <a name="testing"></a>Testowanie 

Aplikacja jest teraz gotowa do testowania. Dostęp do zewnętrznego adresu URL używanego do publikowania Tableau i logowanie jako użytkownik przypisany do obu aplikacji.



## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat serwera proxy aplikacji usługi Azure AD, zobacz [Jak zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych.](application-proxy.md)

