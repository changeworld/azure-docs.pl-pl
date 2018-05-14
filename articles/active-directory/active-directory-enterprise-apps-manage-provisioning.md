---
title: Inicjowanie obsługi zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory użytkownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa przy użyciu usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: asmalser
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: ebc4de2b3a01138a8b48cc637dcab5fc67a68841
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Zarządzanie kontami użytkowników, inicjowanie obsługi administracyjnej dla aplikacji przedsiębiorstwa w portalu Azure
W tym artykule opisano sposób użycia [portalu Azure](https://portal.azure.com) zarządzać aprowizację konta użytkowników i anulowanie obsługi aplikacji obsługujących go, zwłaszcza tych, które zostały dodane z "dostępne" kategorii [ Galerii aplikacji usługi Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Aby dowiedzieć się więcej na temat Inicjowanie obsługi konta użytkowników i jak działa, zobacz [zautomatyzować Inicjowanie obsługi użytkowników i anulowania zastrzeżenia do aplikacji SaaS w usłudze Azure Active Directory](active-directory-saas-app-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Znajdowanie aplikacji w portalu
Wszystkie aplikacje, które są skonfigurowane dla rejestracji jednokrotnej w katalogu, administrator katalogu przy użyciu [galerii aplikacji usługi Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery), można wyświetlać i zarządzane w [portalu Azure](https://portal.azure.com). Aplikacje można znaleźć w **wszystkie usługi** &gt; **aplikacje dla przedsiębiorstw** części portalu. Enterprise aplikacje to aplikacje, które są wdrożone i używane w organizacji.

![Okienko aplikacje dla przedsiębiorstw](./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-pane.png)

Wybieranie **wszystkie aplikacje** link po lewej stronie zawiera listę wszystkich aplikacji, które zostały skonfigurowane, w tym aplikacji, które dodano w galerii. Wybieranie aplikacji ładuje okienku zasobów dla danej aplikacji, w którym raporty można wyświetlać dla danej aplikacji i różne ustawienia mogą być zarządzane.

Inicjowanie obsługi administracyjnej ustawienia konta użytkownika mogą być zarządzane przez wybranie **inicjowania obsługi administracyjnej** po lewej stronie.

![Okienko zasobów aplikacji](./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Tryby inicjowania obsługi administracyjnej
**Inicjowania obsługi administracyjnej** okienko rozpoczyna się od **tryb** menu, które pokazuje, jakie tryby inicjowania obsługi administracyjnej są obsługiwane w przypadku aplikacji dla przedsiębiorstw i można je skonfigurować. Dostępne opcje to:

* **Automatyczne** — ta opcja jest dostępna w przypadku usługi Azure AD obsługuje automatyczne udostępnianie oparty na interfejsach API i/lub anulowanie obsługi kont użytkowników w tej aplikacji. Wybranie tego trybu powoduje wyświetlenie interfejs, który prowadzi administratorów za pośrednictwem Konfigurowanie usługi Azure AD do nawiązania połączenia interfejsu API zarządzania użytkownika aplikacji, tworząc mapowania konta i przepływów pracy, które określają, jak dane konto użytkownika należy przepływ między usługą Azure AD i Aplikacja i zarządzania usługą Azure AD, inicjowania obsługi usługi.
* **Ręczne** — ta opcja jest wyświetlana, jeśli usługi Azure AD nie obsługuje automatycznego inicjowania obsługi administracyjnej kont użytkowników w tej aplikacji. Ta opcja oznacza, że rekordy kont użytkowników przechowywanych w aplikacji musi być zarządzane przy użyciu procesu zewnętrznego, oparte na możliwości zarządzania i inicjowania obsługi użytkowników udostępniane przez tę aplikację (co może obejmować udostępniania SAML just in Time).

## <a name="configuring-automatic-user-account-provisioning"></a>Konfigurowanie konta użytkownika automatycznego inicjowania obsługi administracyjnej
Wybieranie **automatyczne** opcja powoduje wyświetlenie ekranu, który jest podzielony na cztery sekcje:

### <a name="admin-credentials"></a>Poświadczenia administratora
Ta sekcja ma, gdzie wymagane poświadczenia dla usługi Azure AD do nawiązania połączenia Zarządzanie użytkownikami aplikacji interfejsu API zostały wprowadzone. Dane wejściowe wymagane różni się w zależności od aplikacji. Aby dowiedzieć się więcej na temat poświadczeń i wymagań dotyczących konkretnych aplikacji, zobacz [samouczek konfiguracji dla tej konkretnej aplikacji](active-directory-saas-app-provisioning.md).

Wybieranie **Testuj połączenie** przycisk można testować poświadczenia dzięki użyciu usługi Azure AD próba łączenia się z aplikacją do inicjowania obsługi administracyjnej aplikacji przy użyciu podanych poświadczeń.

### <a name="mappings"></a>Mapowania
W tej sekcji jest, gdzie Administratorzy można wyświetlać i edytować jakie przepływu atrybutów użytkownika między usługą Azure AD i aplikacji docelowej, gdy konta użytkowników są lub aktualizacji.

Brak zestawu wstępnie skonfigurowanych mapowania między obiektami użytkownika usługi Azure AD i każda aplikacja SaaS użytkownika. Niektóre aplikacje zarządzania innych typów obiektów, takich jak grup ani kontaktów. Wybierając jedną z te mapowania w tabeli pokazuje Edytor mapowania z prawej strony, gdzie można je wyświetlać i dostosować.

![Okienko zasobów aplikacji](./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.png)

Obsługiwane dostosowania obejmują:

* Włączanie i wyłączanie mapowań określonych obiektów, takich jak obiekt użytkownika usługi Azure AD do obiektu użytkownika aplikacji SaaS.
* Edycja atrybutów, które wpływają od obiektu użytkownika usługi Azure AD do obiektu użytkownika aplikacji. Aby uzyskać więcej informacji na mapowanie atrybutu, zobacz [opis atrybutu mapowania typów](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).
* Filtrowanie inicjowania obsługi administracyjnej akcji wykonywanych przez usługę Azure AD w aplikacji docelowej. Zamiast usługi Azure AD, w pełni synchronizowania obiektów, można ograniczyć akcje wykonywane. Na przykład, wybierając tylko **aktualizacji**, usługi Azure AD tylko aktualizacji istniejącego użytkownika konta w aplikacji i nie tworzyć nowe. Tylko wybierając **Utwórz**, Azure tylko tworzy nowe konta użytkowników, ale nie aktualizuje istniejące. Ta funkcja umożliwia administratorom tworzenie różnych mapowań dla tworzenia kont i aktualizowanie przepływów pracy.

### <a name="settings"></a>Ustawienia
Ta sekcja umożliwia administratorom uruchomić i zatrzymać usługi Azure AD, świadczenie usługi dla wybranej aplikacji, a także opcjonalnie wyczyścić pamięć podręczną inicjowania obsługi administracyjnej i uruchom ponownie usługę.

Jeśli Inicjowanie obsługi administracyjnej jest włączana na potrzeby aplikacji po raz pierwszy, należy włączyć usługę, zmieniając **stan inicjowania obsługi administracyjnej** do **na**. Ta zmiana powoduje programu Azure AD świadczenie usługi Przeprowadź początkową synchronizację, w przypadku tekstu użytkownicy przypisani w **użytkowników i grup** sekcji, wysyła zapytanie do aplikacji docelowej dla nich, a następnie wykonuje akcje inicjowania obsługi administracyjnej zdefiniowane w usłudze Azure AD **mapowania** sekcji. W trakcie tego procesu inicjowania obsługi usługi przechowuje dane buforowane dotyczące konta użytkownika zarządza, co niezarządzanych kont w aplikacjach docelowych, które były nigdy nie w zakresie przypisania nie dotyczą anulowanie obsługi operacji. Po początkowej synchronizacji zastrzegania usługi automatycznie synchronizuje obiektów użytkowników i grup na 10 minutowych interwałach.

Zmiana **stan inicjowania obsługi administracyjnej** do **poza** po prostu wstrzymuje usługę inicjowania obsługi administracyjnej. W tym stanie Azure nie utworzyć, zaktualizować lub usunąć wszystkich użytkowników lub grupy obiektów w aplikacji. Zmiana stanu się na powoduje, że usługa do pobrania miejsca, w którym.

Wybieranie **wyczyścić bieżący stan i ponownie uruchomić synchronizację** wyboru i zapisywania zatrzymuje usługę inicjowania obsługi administracyjnej zrzuty pamięci podręcznej dane dotyczące konta usługi Azure AD jest ponowne uruchomienie usług i zarządzanie wykonuje wstępnego synchronizację ponownie. Ta opcja umożliwia administratorom rozpoczęcia procesu wdrażania inicjowania obsługi administracyjnej za pośrednictwem ponownie.

### <a name="synchronization-details"></a>Szczegóły synchronizacji
Ta sekcja zawiera dodanie szczegółów dotyczących operacji inicjowania obsługi usługi, w tym godziny imię i nazwisko, które usługa dostarczania przeprowadzony na aplikacji i są zarządzane jak wiele obiektów użytkowników i grupy.

Podano linki **inicjowania obsługi administracyjnej raport aktywności** zapewnia dziennik wszystkich użytkowników i grup utworzonych, zaktualizowane i usunięty między usługą Azure AD i aplikacji docelowej i **inicjowania obsługi administracyjnejraportuobłędach** zapewnia bardziej szczegółowe komunikaty o błędach dla obiektów użytkowników i grup, które nie można odczytać, tworzyć, zaktualizowane lub usunięte. 

## <a name="feedback"></a>Opinia

Pamiętaj o opinie przesyłanych! Opinie i pomysły dotyczące poprawy **portalu administracyjnego** sekcji naszych [forum opinii](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Zespołu inżynieryjnego jest podekscytowani, informacje o kompilowaniu chłodnych nowości codziennie i użyj wskazówek z kształtem i zdefiniować, co należy utworzyć w następnej kolejności.

