---
title: Inicjowanie obsługi administracyjnej zarządzania dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory użytkownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzanie aprowizacją konta użytkownika dla aplikacji korporacyjnych przy użyciu usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2018
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 4bdec7c5aa4e6cd29dec870fd7d28d48a40bae57
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462984"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Zarządzanie kontami użytkowników, inicjowanie obsługi administracyjnej dla aplikacji dla przedsiębiorstw w witrynie Azure portal
W tym artykule opisano sposób używania [witryny Azure portal](https://portal.azure.com) Zarządzanie aprowizacją konta użytkownika automatyczne i cofanie aprowizacji dla aplikacji, które go obsługują. Aby dowiedzieć się więcej na temat inicjowania obsługi administracyjnej konta użytkowników i sposób jej działania, zobacz [Automatyzowanie aprowizacji użytkowników oraz anulowania zastrzeżenia do aplikacji SaaS w usłudze Azure Active Directory](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Znajdowanie aplikacji w portalu
Wszystkie aplikacje, które są skonfigurowane do obsługi logowania jednokrotnego w katalogu można wyświetlać i zarządzać w [witryny Azure portal](https://portal.azure.com). Aplikacje można znaleźć w **wszystkich usług** &gt; **aplikacje dla przedsiębiorstw** sekcji w portalu. Aplikacje dla przedsiębiorstw to aplikacje, które są wdrażane i używane w organizacji.

![Okienko aplikacje dla przedsiębiorstw](./media/configure-automatic-user-provisioning-portal/enterprise-apps-pane.png)

Wybieranie **wszystkie aplikacje** link po lewej stronie zawiera listę wszystkich aplikacji, które zostały skonfigurowane, w tym aplikacje, które dodano w galerii. Wybieranie aplikacji ładuje okienka zasobów dla tej aplikacji, w którym raporty mogą być wyświetlane dla tej aplikacji i różne ustawienia, które mogą być zarządzane.

Konto użytkownika, inicjowania obsługi ustawienia mogą być zarządzane przez wybranie **aprowizacji** po lewej stronie.

![Okienko zasobów aplikacji](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Tryby udostępniania
**Aprowizacji** okienko zaczyna się od **tryb** menu, które pokazuje, jakie metody inicjowania obsługi administracyjnej są obsługiwane w przypadku aplikacji dla przedsiębiorstw i można je skonfigurować. Dostępne opcje to:

* **Automatyczne** — ta opcja jest wyświetlana, jeśli usługa Azure AD obsługuje automatyczną aprowizację oparte na interfejsie API i/lub anulowania obsługi administracyjnej kont użytkowników do tej aplikacji. Wybranie tego trybu wyświetla interfejs, który zawiera informacje na temat administratorów za pomocą Konfigurowanie usługi Azure AD, aby nawiązać połączenie z interfejsem API zarządzania użytkownikami aplikacji, tworząc mapowania konta i przepływów pracy, które definiują przepływ danych kont użytkowników między usługą Azure AD i Aplikacja i zarządzania usługą Azure AD usługi aprowizacji.
* **Ręczne** — ta opcja jest wyświetlana, jeśli usługi Azure AD nie obsługuje automatyczną aprowizację kont użytkowników do tej aplikacji. Ta opcja oznacza, że rekordów kont użytkowników przechowywanych w aplikacji muszą być zarządzane przy użyciu procesu zewnętrznego, oparta na funkcjach zarządzania i inicjowania obsługi użytkowników udostępniane przez tę aplikację (wraz z ewentualnym aprowizacji SAML just in Time).

## <a name="configuring-automatic-user-account-provisioning"></a>Konfigurowanie automatycznej konta aprowizacji użytkowników
Wybieranie **automatyczne** opcji powoduje wyświetlenie ekranu, który zawiera cztery sekcje:

### <a name="admin-credentials"></a>Poświadczenia administratora
Ta sekcja dotyczy, gdzie wymagane poświadczenia dla usługi Azure AD connect do zarządzania użytkownikami aplikacji interfejsu API zostały wprowadzone. Wymagane dane wejściowe różni się w zależności od aplikacji. Aby dowiedzieć się więcej o typach poświadczeń i wymagania dotyczące określonych aplikacji, zobacz [samouczek konfigurowania aplikacji dla tej konkretnej aplikacji](user-provisioning.md).

Wybieranie **Testuj połączenie** przycisk umożliwia przetestowanie poświadczeń dzięki usłudze Azure AD próba nawiązania połączenia do aplikacji przez Inicjowanie obsługi administracyjnej aplikacji przy użyciu podanych poświadczeń.

### <a name="mappings"></a>Mapowania
Ta sekcja dotyczy, gdzie administratorzy mogą wyświetlać i edytować jakie przepływu atrybutów użytkownika między usługą Azure AD a aplikacją docelową, gdy konta użytkowników są aprowizowane lub aktualizowane.

Istnieje zestaw wstępnie skonfigurowanego mapowania między obiektami użytkownika usługi Azure AD i obiektów użytkowników każdej aplikacji SaaS. Niektóre aplikacje, zarządzać innych typów obiektów, takich jak grup ani kontaktów. Wybierając jedną z tych mapowań w tabeli przedstawiono Edytor mapowania z prawej strony, gdzie można je wyświetlać i dostosowane.

![Okienko zasobów aplikacji](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Obsługiwane dostosowania obejmują:

* Włączanie i wyłączanie mapowania dla określonych obiektów, takich jak obiekt użytkownika usługi Azure AD do obiektu użytkownika dla aplikacji SaaS.
* Edycja atrybutów, które będą działać z obiektu użytkownika w usłudze Azure AD do obiektu użytkownika w aplikacji. Aby uzyskać więcej informacji na temat Mapowanie atrybutów, zobacz [opis atrybutu mapowania typów](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtruj akcji aprowizacji, wykonywanych przez usługę Azure AD w aplikacji docelowej. Zamiast pełnej synchronizacji obiektów z usługi Azure AD, można ograniczyć akcje wykonywane. Na przykład, wybierając tylko **aktualizacji**, tylko aktualizacji usługi Azure AD istniejącego użytkownika konta w aplikacji, a nie tworzyć nowe. Tylko wybierając **Utwórz**, platforma Azure tylko powoduje utworzenie nowych kont użytkowników, ale nie aktualizuje już istniejące. Ta funkcja umożliwia administratorom tworzenie różnych mapowań dla tworzenia kont i aktualizowanie przepływów pracy.

### <a name="settings"></a>Ustawienia
Ta sekcja umożliwia administratorom uruchomić i zatrzymać usługi dla wybranej aplikacji aprowizacji usługi Azure AD, a także Opcjonalnie wyczyść inicjowania obsługi administracyjnej pamięci podręcznej i uruchom ponownie usługę.

Jeśli Inicjowanie obsługi administracyjnej jest włączane po raz pierwszy dla aplikacji, należy włączyć usługę, zmieniając **stanie aprowizacji** do **na**. Ta zmiana powoduje, że usługi Azure AD usługi inicjowania obsługi administracyjnej do wykonania początkowej synchronizacji, w przypadku tekstu użytkowników przypisanych w **użytkowników i grup** sekcji aplikacji docelowej dla nich zapytania, a następnie wykonuje akcji aprowizacji zdefiniowane w usłudze Azure AD **mapowania** sekcji. W trakcie tego procesu usługi aprowizacji są przechowywane dane buforowane dotyczące konta użytkownika, które zarządza, niezarządzanego konta w aplikacjach docelowych, które nigdy nie były zakres przypisania nie ma wpływu na anulowanie obsługi operacji. Po wykonaniu początkowej synchronizacji usługi aprowizacji automatycznie synchronizuje obiekty użytkowników i grup na dziesięć minut.

Zmiana **stanie aprowizacji** do **poza** po prostu wstrzymuje usługę aprowizacji. W tym stanie platformy Azure nie tworzenie, aktualizowanie lub usuwanie wszyscy użytkownicy lub grupy obiektów w aplikacji. Zmiana stanu się na powoduje, że usługa przejmą tam, gdzie ją przerwaliśmy.

Wybieranie **wyczyść bieżący stan i ponownie Rozpocznij synchronizację** pole wyboru i zapisywanie zatrzymuje usługę aprowizacji, zrzuty pamięci podręcznej dane dotyczące konta usługi Azure AD jest zarządzany, uruchamia usługi i wykonuje wstępne synchronizację ponownie. Ta opcja pozwala administratorom na początku procesu wdrażania inicjowania obsługi administracyjnej ponownie.

### <a name="synchronization-details"></a>Szczegóły synchronizacji
Ta sekcja zawiera dodatkowe szczegóły dotyczące działania usługi aprowizacji, w tym imię i nazwisko przypadków, gdy usługa aprowizowania przeprowadzony na aplikacji, a ilu użytkowników i grupy obiektów są zarządzane.

Podano linki **aprowizacji raport aktywności** zapewniający dziennik wszystkich użytkowników i grup utworzonych, zaktualizowano i usunięto między usługi Azure AD a aplikacją docelową i **raportobłędachaprowizacji** zawierający bardziej szczegółowe komunikaty o błędach dla obiektów użytkowników i grup, których nie udało się odczytać, utworzone, zaktualizowane lub usunięte. 



