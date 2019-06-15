---
title: Inicjowanie obsługi administracyjnej zarządzania dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory użytkownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzanie aprowizacją konta użytkownika dla aplikacji korporacyjnych przy użyciu usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/01/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bf265f51f2fea16f90dd0bcf2891bd9bed5cef8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963575"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Zarządzanie kontami użytkowników, inicjowanie obsługi administracyjnej dla aplikacji dla przedsiębiorstw w witrynie Azure portal

W tym artykule opisano sposób używania [witryny Azure portal](https://portal.azure.com) Zarządzanie aprowizacją konta użytkownika automatyczne i cofanie aprowizacji dla aplikacji, które go obsługują. Aby dowiedzieć się więcej na temat inicjowania obsługi administracyjnej konta użytkowników i sposób jej działania, zobacz [Automatyzowanie aprowizacji użytkowników oraz anulowania zastrzeżenia do aplikacji SaaS w usłudze Azure Active Directory](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Znajdowanie aplikacji w portalu

Użyj portalu usługi Azure Active Directory do przeglądania i zarządzania wszystkich aplikacji, które są skonfigurowane do logowania jednokrotnego w katalogu. Aplikacje dla przedsiębiorstw to aplikacje, które są wdrażane i używane w organizacji. Wykonaj następujące kroki, aby wyświetlić aplikacje i zarządzaj nimi przedsiębiorstwa:

1. Otwórz [portalu Azure Active Directory](https://aad.portal.azure.com).

1. Wybierz **aplikacje dla przedsiębiorstw** z okienka po lewej stronie. Jest wyświetlana lista wszystkich skonfigurowanych aplikacji, w tym aplikacje, które zostały dodane z galerii.

1. Wybierz dowolną aplikację, aby załadować jej okienko zasobów, gdzie można wyświetlać raporty i Zarządzaj ustawieniami aplikacji.

1. Wybierz **aprowizacji** Zarządzanie kontem użytkownika inicjowania obsługi ustawienia dla wybranej aplikacji.

   ![Okienko zasobów aplikacji](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Tryby udostępniania

**Aprowizacji** okienko zaczyna się od **tryb** menu, które pokazuje tryby udostępniania, obsługiwane w przypadku aplikacji dla przedsiębiorstw i można je skonfigurować. Dostępne opcje to:

* **Automatyczne** — ta opcja jest wyświetlana, jeśli usługa Azure AD obsługuje oparte na interfejsie API automatyczne aprowizowanie lub cofanie aprowizacji kont użytkowników do tej aplikacji. Wybierz ten tryb, aby wyświetlić interfejs, który ułatwia administratorom:

  * Konfigurowanie usługi Azure AD, aby nawiązać połączenie z interfejsem API zarządzania użytkownikami aplikacji
  * Tworzenie mapowania konta i przepływów pracy, które definiują przepływ danych kont użytkowników między usługą Azure AD i aplikacji
  * Zarządzanie usługą Azure AD, usługa aprowizacji

* **Ręczne** — ta opcja jest wyświetlana, jeśli usługa Azure AD nie obsługuje automatyczną aprowizację kont użytkowników do tej aplikacji. W tym przypadku konta użytkownika, które rekordy są przechowywane w aplikacji muszą być zarządzane przy użyciu procesu zewnętrznego, oparta na funkcjach zarządzania i inicjowania obsługi użytkowników udostępniane przez tę aplikację (wraz z ewentualnym aprowizacji SAML just in Time).

## <a name="configuring-automatic-user-account-provisioning"></a>Konfigurowanie automatycznej konta aprowizacji użytkowników

Wybierz **automatyczne** opcję, aby określić ustawienia poświadczeń administratora, mapowań, uruchamianie i zatrzymywanie i synchronizacji.

### <a name="admin-credentials"></a>Poświadczenia administratora

Rozwiń **poświadczeń administratora** o podanie poświadczeń wymaganych przez usługi Azure AD nawiązać połączenie z interfejsem API zarządzania użytkownikami aplikacji. Wymagane dane wejściowe różni się w zależności od aplikacji. Aby dowiedzieć się więcej o typach poświadczeń i wymagania dotyczące określonych aplikacji, zobacz [samouczek konfigurowania aplikacji dla tej konkretnej aplikacji](user-provisioning.md).

Wybierz **Testuj połączenie** do testowania poświadczeń dzięki usłudze Azure AD próba nawiązania połączenia w aplikacji przez Inicjowanie obsługi administracyjnej aplikacji przy użyciu podanych poświadczeń.

### <a name="mappings"></a>Mapowania

Rozwiń **mapowania** możesz wyświetlać i edytować atrybuty użytkownika, które przepływ między usługą Azure AD a aplikacją docelową, gdy konta użytkowników są aprowizowane lub aktualizowane.

Istnieje zestaw wstępnie skonfigurowanego mapowania między obiektami użytkownika usługi Azure AD i obiektów użytkowników każdej aplikacji SaaS. Niektóre aplikacje, zarządzać innych typów obiektów, takich jak grup ani kontaktów. Wybierz mapowanie w tabeli, aby otworzyć Edytor mapowania do prawej, w którym można wyświetlać i odpowiednio je Dostosuj.

![Okienko zasobów aplikacji](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Obsługiwane dostosowania obejmują:

* Włączanie i wyłączanie mapowania dla określonych obiektów, takich jak obiekt użytkownika usługi Azure AD do obiektu użytkownika dla aplikacji SaaS.
* Edycja atrybutów, które będą działać z obiektu użytkownika w usłudze Azure AD do obiektu użytkownika w aplikacji. Aby uzyskać więcej informacji na temat Mapowanie atrybutów, zobacz [opis atrybutu mapowania typów](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrowanie akcji aprowizacji, które usługi Azure AD, który jest uruchamiany w aplikacji docelowej. Zamiast pełnej synchronizacji obiektów z usługi Azure AD, można ograniczyć uruchamianie akcji. 

  Na przykład wybrać tylko **aktualizacji** i usługi Azure AD tylko aktualizacje istniejącego użytkownika konta w aplikacji, ale nie tworzy nowe. Wybierz tylko **Utwórz** i platformy Azure tylko powoduje utworzenie nowych kont użytkowników, ale nie aktualizuje już istniejące. Ta funkcja umożliwia administratorom tworzenie różnych mapowań dla tworzenia kont i aktualizowanie przepływów pracy.

* Dodano nowe mapowanie atrybutu. Wybierz **Dodaj nowe mapowanie** w dolnej części **mapowanie atrybutu** okienka. Wypełnij **Edytuj atrybut** formularza, a następnie wybierz pozycję **Ok** można dodać nowe mapowanie do listy. 

### <a name="settings"></a>Ustawienia

Można uruchomić i zatrzymać usługi Azure AD, usługi dla wybranej aplikacji w inicjowania obsługi administracyjnej **ustawienia** obszaru **aprowizacji** ekranu. Można także wyczyścić pamięć podręczną inicjowania obsługi administracyjnej i uruchom ponownie usługę.

Jeśli Inicjowanie obsługi administracyjnej jest włączane po raz pierwszy dla aplikacji, należy włączyć usługę, zmieniając **stanie aprowizacji** do **na**. Ta zmiana powoduje, że usługi Azure AD usługi inicjowania obsługi administracyjnej do uruchamiania synchronizacji początkowej. Odczytuje użytkowników przypisanych w **użytkowników i grup** sekcji aplikacji docelowej dla nich zapytania, a następnie wykonuje akcji aprowizacji, zdefiniowane w usłudze Azure AD **mapowania** sekcji. W trakcie tego procesu usługi aprowizacji są przechowywane dane buforowane dotyczące konta użytkownika, które zarządza, niezarządzanego konta w aplikacjach docelowych, które nigdy nie były zakres przypisania nie ma wpływu na anulowanie obsługi operacji. Po wykonaniu początkowej synchronizacji usługi aprowizacji automatycznie synchronizuje obiekty użytkowników i grup na dziesięć minut.

Zmiana **stanie aprowizacji** do **poza** wstrzymanie usługi aprowizacji. W tym stanie platformy Azure nie utworzyć, zaktualizować lub usunąć wszystkich użytkowników lub grupy obiektów w aplikacji. Zmień stan z powrotem do **na** i przejmuje tam, gdzie ją przerwaliśmy usługi.

Wybierz **wyczyść bieżący stan i ponownie Rozpocznij synchronizację** pole wyboru i wybrać **Zapisz** do:

* Zatrzymaj usługę aprowizacji
* Zrzutu pamięci podręcznej dane dotyczące konta, które jest zarządzany przez usługę Azure AD
* Uruchom ponownie usługi i uruchom ponownie synchronizacji początkowej

Ta opcja umożliwia administratorom IT rozpocząć proces wdrażania inicjowania obsługi administracyjnej za pośrednictwem ponownie.

### <a name="synchronization-details"></a>Szczegóły synchronizacji

Ta sekcja zawiera szczegółowe informacje dotyczące działania usługi aprowizacji, w tym imię i nazwisko przypadków, gdy usługa aprowizowania przeprowadzony na aplikacji, a ilu użytkowników i grupy obiektów, którymi zarządza.

Zostanie podane łącze aby **aprowizacji raport aktywności**, które zapewnia dziennikiem wszystkich użytkowników i grup utworzonych, zaktualizowano i usunięto między usługi Azure AD a aplikacją docelową. Udostępniane są również link do **raport o błędach aprowizacji**, który zawiera bardziej szczegółowe komunikaty o błędach dla użytkowników i grupy obiektów, które nie udało się odczytać, utworzony, zaktualizowany lub usunięty.
