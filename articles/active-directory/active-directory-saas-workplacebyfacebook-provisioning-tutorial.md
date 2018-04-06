---
title: 'Samouczek: Skonfiguruj miejsca pracy przez Facebook dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i miejsca pracy przez usługi Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: d42a6b63833bffc3cce1830a728ec21083b25dc9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Samouczek: Skonfiguruj miejsca pracy przez usługi Facebook dla użytkownika automatycznego inicjowania obsługi administracyjnej.

Celem tego samouczka jest opisano czynności, które należy wykonać w miejscu pracy przez Facebook i Azure AD, aby automatycznie zapewnianie i usuwanie kont użytkowników z usługi Azure AD do miejsca pracy przez usługi Facebook.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z miejsca pracy przez usługi Facebook, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Pracy przez Facebook jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>Przypisywanie użytkowników do miejsca pracy przez usługi Facebook

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD jest zsynchronizowany.

Przed Skonfiguruj i włącz usługę inicjowania obsługi administracyjnej, należy zdecydować, jakie użytkownicy i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do miejsca pracy przez aplikację usługi Facebook. Po decyzję, można przypisać użytkowników do miejsca pracy przez aplikację usługi Facebook postępując zgodnie z instrukcjami w tym miejscu:

[Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Ważne porady dotyczące przypisywania użytkowników do miejsca pracy przez usługi Facebook

*   Zalecane jest jeden użytkownik usługi Azure AD jest przypisany do miejsca pracy przez Facebook do testowania konfiguracji inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Przypisanie użytkownika do miejsca pracy przez usługi Facebook, musisz wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-user-provisioning"></a>Włącz inicjowanie obsługi użytkowników

Ta sekcja przeprowadzi Cię przez łączenie usługi Azure AD do miejsca pracy przez konto użytkownika usługi Facebook na inicjowanie obsługi interfejsu API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączania konta użytkowników przypisane w pracy przez usługi Facebook na podstawie przypisań użytkowników i grup w usłudze Azure AD.

>[!Tip]
>Można też włączyć na języku SAML logowania jednokrotnego dla miejsca pracy przez usługi Facebook, wykonując instrukcje podane w [portalu Azure](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje uzupełniania siebie nawzajem.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Aby skonfigurować użytkownika Inicjowanie obsługi konta w serwisie Facebook w miejscu pracy w usłudze Azure AD:

Celem tej sekcji jest przedstawiają sposób włączania obsługi kont użytkowników usługi Active Directory do miejsca pracy przez usługi Facebook.

Usługi Azure AD umożliwia automatyczną synchronizację szczegóły konta użytkowników przypisane do miejsca pracy przez usługi Facebook. To automatyczną synchronizację umożliwia dołączanie przez Facebook, aby pobrać dane, należy go autoryzacji użytkowników dostępu przed ich próbował zarejestrować się po raz pierwszy. Również cofnąć udostępnia użytkowników z miejsca pracy przez usługi Facebook, gdy został odwołany dostępu w usłudze Azure AD.

1. W [portalu Azure](https://portal.azure.com), przejdź do **usługi Azure Active Directory** > **aplikacje przedsiębiorstwa** > **wszystkie aplikacje** sekcji.

2. Obszar roboczy w serwisie Facebook został już skonfigurowany dla logowania jednokrotnego, wyszukaj wystąpienia miejsca pracy przez usługi Facebook, korzystając z pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **miejsca pracy przez Facebook** w galerii aplikacji. Wybierz obszar roboczy w serwisie Facebook w wynikach wyszukiwania, a następnie dodaj go do listy aplikacji.

3. Wybierz wystąpienia programu miejsca pracy przez usługi Facebook, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**. 

    ![aprowizowanie](./media/active-directory-saas-workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. W obszarze **poświadczeń administratora** sekcji, wprowadź Token dostępu z miejsca pracy przez administratora usługi Facebook i ustaw wartość adres URL dzierżawy `https://www.facebook.com/scim/v1/` . Zobacz te [instrukcje](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) dotyczące tworzenia tokenu dostępu do miejsca pracy. 

6. W portalu Azure kliknij **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z miejsca pracy przez aplikację usługi Facebook. Jeśli połączenie nie powiedzie się, upewnij się, że miejsca pracy przez Facebook konto ma uprawnienia administratora zespołu.

7. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru.

8. Kliknij przycisk **zapisać.**

9. W sekcji mapowania wybierz **synchronizacji Azure użytkownicy usługi Active Directory do miejsca pracy przez usługi Facebook.**

10. W **mapowań atrybutów** Przejrzyj atrybutów użytkowników, które są synchronizowane z usługi Azure AD do miejsca pracy przez usługi Facebook. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w miejscu pracy przez Facebook dla operacji update. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

11. Aby włączyć usługi Azure AD, świadczenie usługi dla miejsca pracy przez usługi Facebook, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia** sekcji

12. Kliknij przycisk **zapisać.**

Aby uzyskać więcej informacji na temat konfigurowania automatycznego inicjowania obsługi administracyjnej zobacz [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Można teraz utworzyć konta testowego. Poczekaj maksymalnie 20 minut, aby sprawdzić, czy konto zostało zsynchronizowane w miejscu pracy przez usługi Facebook.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurowanie rejestracji jednokrotnej](active-directory-saas-workplacebyfacebook-tutorial.md)
