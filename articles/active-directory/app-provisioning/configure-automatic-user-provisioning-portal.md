---
title: Zarządzanie inicjowania obsługi administracyjnej dla aplikacji dla przedsiębiorstw w usłudze Azure AD
description: Dowiedz się, jak zarządzać inicjowania obsługi administracyjnej kont użytkowników dla aplikacji przedsiębiorstwa przy użyciu usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f9bd1afeebedf4cbf37d75d9c57f3d8be0f288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264130"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Zarządzanie aprowewaniem kont użytkowników dla aplikacji korporacyjnych w witrynie Azure portal

W tym artykule opisano ogólne kroki zarządzania automatyczną inicjowania obsługi administracyjnej konta użytkownika i cofania obsługi administracyjnej dla aplikacji, które go obsługują. *Inicjowanie obsługi administracyjnej konta użytkownika* jest czynnością tworzenia, aktualizowania i/lub wyłączania rekordów kont użytkowników w lokalnym magazynie profilu użytkownika aplikacji. Większość aplikacji w chmurze i SaaS przechowuje rolę i uprawnienia użytkowników we własnym magazynie profilu użytkownika lokalnego, a obecność takiego rekordu użytkownika w magazynie lokalnym użytkownika jest *wymagana* do logowania jednokrotnego i dostępu do pracy. Aby dowiedzieć się więcej na temat automatycznego inicjowania obsługi administracyjnej konta użytkownika, zobacz [Automatyzacja inicjowania obsługi administracyjnej i anulowania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](user-provisioning.md).

> [!IMPORTANT]
> Usługa Azure Active Directory (Azure AD) ma galerię zawierającą tysiące wstępnie zintegrowanych aplikacji, które są włączone do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD. Należy rozpocząć od znalezienia samouczka konfiguracji inicjowania obsługi administracyjnej specyficznego dla aplikacji [na liście samouczków dotyczących integracji aplikacji SaaS z usługą Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). Prawdopodobnie znajdziesz wskazówki krok po kroku dotyczące konfigurowania aplikacji i usługi Azure AD w celu utworzenia połączenia inicjowania obsługi administracyjnej.

## <a name="finding-your-apps-in-the-portal"></a>Znajdowanie aplikacji w portalu

Użyj portalu usługi Azure Active Directory, aby wyświetlać wszystkie aplikacje skonfigurowane do logowania jednokrotnego w katalogu i zarządzać nimi. Aplikacje dla przedsiębiorstw to aplikacje wdrażane i używane w organizacji. Wykonaj następujące kroki, aby wyświetlić aplikacje dla przedsiębiorstw i zarządzać nimi:

1. Otwórz [portal usługi Azure Active Directory](https://aad.portal.azure.com).
1. Wybierz **aplikacje enterprise** z lewego okienka. Zostanie wyświetlona lista wszystkich skonfigurowanych aplikacji, w tym aplikacji dodanych z galerii.
1. Wybierz dowolną aplikację, aby załadować okienko zasobów, w którym możesz wyświetlać raporty i zarządzać ustawieniami aplikacji.
1. Wybierz **pozycję Inicjowanie obsługi administracyjnej,** aby zarządzać ustawieniami inicjowania obsługi administracyjnej konta użytkownika dla wybranej aplikacji.

   ![Ekran inicjowania obsługi administracyjnej w celu zarządzania ustawieniami inicjowania obsługi administracyjnej konta użytkownika](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Tryby inicjowania obsługi administracyjnej

Okienko **inicjowania obsługi administracyjnej** rozpoczyna się od menu **Tryb,** które pokazuje tryby inicjowania obsługi administracyjnej obsługiwane dla aplikacji przedsiębiorstwa i umożliwia ich skonfigurowanie. Dostępne opcje obejmują:

* **Automatyczne** — ta opcja jest wyświetlana, jeśli usługa Azure AD obsługuje automatyczne inicjowanie obsługi administracyjnej oparte na interfejsie API lub usuwanie obsługi administracyjnej kont użytkowników do tej aplikacji. Wybierz ten tryb, aby wyświetlić interfejs, który pomaga administratorom:

  * Konfigurowanie usługi Azure AD do łączenia się z interfejsem API zarządzania użytkownikami aplikacji
  * Tworzenie mapowań kont i przepływów pracy definiujących przepływ danych konta użytkownika między usługą Azure AD a aplikacją
  * Zarządzanie usługą inicjowania obsługi administracyjnej usługi Azure AD

* **Ręcznie** — ta opcja jest wyświetlana, jeśli usługa Azure AD nie obsługuje automatycznego inicjowania obsługi administracyjnej kont użytkowników do tej aplikacji. W takim przypadku rekordy konta użytkownika przechowywane w aplikacji muszą być zarządzane przy użyciu procesu zewnętrznego, na podstawie funkcji zarządzania użytkownikami i inicjowania obsługi administracyjnej dostarczonych przez tę aplikację (które mogą obejmować samol just-in-time in-time in-time).

## <a name="configuring-automatic-user-account-provisioning"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika

Wybierz opcję **Automatycznie,** aby określić ustawienia poświadczeń administratora, mapowania, uruchamiania i zatrzymywania oraz synchronizacji.

### <a name="admin-credentials"></a>Poświadczenia administratora

Rozwiń **poświadczenia administratora,** aby wprowadzić poświadczenia wymagane dla usługi Azure AD, aby połączyć się z interfejsem API zarządzania użytkownikami aplikacji. Wymagane dane wejściowe różnią się w zależności od aplikacji. Aby dowiedzieć się więcej o typach poświadczeń i wymaganiach dla określonych aplikacji, zobacz [samouczek konfiguracji dla tej konkretnej aplikacji](user-provisioning.md).

Wybierz **opcję Testuj połączenie,** aby przetestować poświadczenia, próbując połączyć się z aplikacją inicjowania obsługi administracyjnej aplikacji przy użyciu podanych poświadczeń.

### <a name="mappings"></a>Mapowania

Rozwiń **mapowania,** aby wyświetlić i edytować atrybuty użytkownika, które przepływają między usługą Azure AD i aplikacji docelowej, gdy konta użytkowników są aprowizgowne lub aktualizowane.

Istnieje wstępnie skonfigurowany zestaw mapowań między obiektami użytkowników usługi Azure AD a obiektami użytkownika każdej aplikacji SaaS. Niektóre aplikacje zarządzają innymi typami obiektów, takimi jak Grupy lub Kontakty. Wybierz mapowanie w tabeli, aby otworzyć edytor mapowania po prawej stronie, gdzie można je wyświetlać i dostosowywać.

![Ekran Mapowanie atrybutów](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Obsługiwane dostosowania obejmują:

* Włączanie i wyłączanie mapowań dla określonych obiektów, takich jak obiekt użytkownika usługi Azure AD do obiektu użytkownika aplikacji SaaS.
* Edytowanie atrybutów, które przepływają z obiektu użytkownika usługi Azure AD do obiektu użytkownika aplikacji. Aby uzyskać więcej informacji na temat mapowania atrybutów, zobacz [Opis typów mapowania atrybutów](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrowanie akcji inicjowania obsługi administracyjnej, które usługa Azure AD jest uruchamiana w aplikacji docelowej. Zamiast w pełni synchronizować obiekty usługi Azure AD, można ograniczyć uruchamianie akcji.

  Na przykład tylko wybierz **update** i usługi Azure AD aktualizuje tylko istniejące konta użytkowników w aplikacji, ale nie tworzy nowych. Wybierz tylko **pozycję Utwórz,** a platforma Azure tworzy tylko nowe konta użytkowników, ale nie aktualizuje istniejących. Ta funkcja umożliwia administratorom tworzenie różnych mapowań dla przepływów pracy tworzenia i aktualizowania kont.

* Dodawanie nowego mapowania atrybutów. Wybierz **pozycję Dodaj nowe mapowanie** u dołu okienka **Mapowanie atrybutów.** Wypełnij formularz **Edytuj atrybut** i **wybierz** ok, aby dodać nowe mapowanie do listy.

### <a name="settings"></a>Ustawienia

Usługę inicjowania obsługi administracyjnej usługi Azure AD dla wybranej aplikacji można uruchomić i zatrzymać w obszarze **Ustawienia** na ekranie **Inicjowanie obsługi administracyjnej.** Można również wyczyścić pamięć podręczną inicjowania obsługi administracyjnej i ponownie uruchomić usługę.

Jeśli inicjowanie obsługi administracyjnej jest włączone po raz pierwszy dla aplikacji, włącz usługę, zmieniając **stan inicjowania obsługi administracyjnej** **na Włączone**. Ta zmiana powoduje, że usługa inicjowania obsługi administracyjnej usługi Azure AD uruchamia początkowy cykl. Odczytuje użytkowników przypisanych w sekcji **Użytkownicy i grupy,** wysyła zapytanie do aplikacji docelowej dla nich, a następnie uruchamia akcje inicjowania obsługi administracyjnej zdefiniowane w sekcji **Mapowania** usługi Azure AD. Podczas tego procesu usługa inicjowania obsługi administracyjnej przechowuje buforowane dane dotyczące zarządzanych kont użytkowników, więc niekomrządzone konta wewnątrz aplikacji docelowych, które nigdy nie były w zakresie przydziału, nie mają wpływu na operacje de-inicjowania obsługi administracyjnej. Po cyklu początkowym usługa inicjowania obsługi administracyjnej automatycznie synchronizuje obiekty użytkowników i grup w odstępie czterdziestominutowym.

Zmień **stan inicjowania obsługi administracyjnej** na **Wyłączone,** aby wstrzymać usługę inicjowania obsługi administracyjnej. W tym stanie platforma Azure nie tworzy, nie aktualizuje ani nie usuwa żadnych obiektów użytkownika lub grupy w aplikacji. Zmień stan z powrotem **na Włączone,** a usługa odbierze miejsce, w którym zostało przerwane.

**Wyczyść bieżący stan i ponowną synchronizację** wyzwala początkowy cykl. Usługa będzie następnie ponownie ocenić wszystkich użytkowników w systemie źródłowym i określić, czy są one w zakresie inicjowania obsługi administracyjnej. Może to być przydatne, gdy aplikacja jest obecnie w kwarantannie lub trzeba wprowadzić zmiany do mapowania atrybutów. Należy zauważyć, że początkowy cykl trwa dłużej niż typowy cykl przyrostowy ze względu na liczbę obiektów, które muszą zostać ocenione. Możesz dowiedzieć się więcej o wydajności cykli początkowych i przyrostowych [tutaj](application-provisioning-when-will-provisioning-finish-specific-user.md). 
