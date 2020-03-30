---
title: 'Samouczek: Inicjowanie obsługi administracyjnej dla usługi LinkedIn Elevate — usługa Azure AD'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej kont użytkowników w usłudze LinkedIn Elevate.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa0a26eaeac431ed2c78c5bd938bbbe7dff14e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057417"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi LinkedIn Elevate w celu automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest pokazanie kroki, które należy wykonać w linkedin elevate i usługi Azure AD, aby automatycznie aprowizować i de-provision kont użytkowników z usługi Azure AD do LinkedIn Elevate.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Dzierżawa LinkedIn Elevate
* Konto administratora w uniesieniu poziomu LinkedIn z dostępem do Centrum kont LinkedIn

> [!NOTE]
> Usługa Azure Active Directory integruje się z usługą LinkedIn Elevate przy użyciu protokołu [SCIM.](http://www.simplecloud.info/)

## <a name="assigning-users-to-linkedin-elevate"></a>Przypisywanie użytkowników do usługi LinkedIn Elevate

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD zostaną zsynchronizowane.

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do usługi LinkedIn Elevate. Po podjęciu decyzji, można przypisać tych użytkowników do LinkedIn Elevate, postępując zgodnie z instrukcjami tutaj:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Ważne wskazówki dotyczące przypisywania użytkowników do LinkedIn Elevate

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do usługi LinkedIn Elevate w celu przetestowania konfiguracji inicjowania obsługi administracyjnej. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do usługi LinkedIn Elevate należy wybrać rolę **użytkownika** w oknie dialogowym przypisania. Rola "Dostęp domyślny" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Konfigurowanie inicjowania obsługi administracyjnej użytkownika w uniesieniu poziomu linkedin

W tej sekcji przewodnik po połączeniu usługi Azure AD z interfejsem API obsługi administracyjnej konta scim firmy LinkedIn elevate i skonfigurowaniu usługi inicjowania obsługi administracyjnej w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w usłudze LinkedIn Elevate na podstawie przypisania użytkownika i grupy w usłudze Azure AD.

**Wskazówka:** Można również włączyć samooceny samol oparte na logowanie jednokrotne dla LinkedIn Elevate, postępując zgodnie z instrukcjami podanymi w [witrynie Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Aby skonfigurować automatyczne udostępnianie obsługi administracyjnej konta użytkownika do usługi LinkedIn Elevate w usłudze Azure AD:

Pierwszym krokiem jest pobranie tokenu dostępu linkedin. Jeśli jesteś administratorem przedsiębiorstwa, możesz samodzielnie aprowizować token dostępu. W centrum kont przejdź do **ustawień globalnych ustawień &gt; ** i otwórz panel Ustawienia **SCIM.**

> [!NOTE]
> Jeśli uzyskujesz dostęp do centrum kont bezpośrednio, a nie za pośrednictwem łącza, możesz do niego dotrzeć, wykonując następujące kroki.

1. Zaloguj się do Centrum kont.

2. Wybierz ** &gt; pozycję Ustawienia administratora** .

3. Kliknij pozycję **Zaawansowane integracje** na lewym pasku bocznym. Jesteś kierowany do centrum kont.

4. Kliknij **+ Dodaj nową konfigurację SCIM** i postępuj zgodnie z procedurą, wypełniając każde pole.

    > [!NOTE]
    > Jeśli automatyczne przypisywanie licencji nie jest włączone, oznacza to, że synchronizowane są tylko dane użytkownika.

    ![Inicjowanie obsługi administracyjnej usługi LinkedIn](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > Gdy przypisanie autolicencji jest włączone, należy zanotować wystąpienie aplikacji i typ licencji. Licencje są przypisywane na zasadzie "kto pierwszy, ten lepszy", dopóki nie zostaną podjęte wszystkie licencje.

    ![Inicjowanie obsługi administracyjnej usługi LinkedIn](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. Kliknij **pozycję Generuj token**. Powinien zostać wyświetlony token dostępu wyświetlany w polu **token dostępu.**

6. Zapisz token dostępu w schowku lub komputerze przed opuszczeniem strony.

7. Następnie zaloguj się do [witryny Azure Portal](https://portal.azure.com)i przejdź do sekcji **Azure Active Directory > Enterprise Apps > wszystkie aplikacje.**

8. Jeśli linkedin elevate został już skonfigurowany do logowania jednokrotnego, wyszukaj wystąpienie Usługi LinkedIn Elevate za pomocą pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i wyszukaj **pozycję Podnieś pozycję LinkedIn** w galerii aplikacji. Wybierz pozycję LinkedIn Elevate z wyników wyszukiwania i dodaj go do listy aplikacji.

9. Wybierz wystąpienie LinkedIn Elevate, a następnie wybierz kartę **Inicjowanie obsługi administracyjnej.**

10. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Inicjowanie obsługi administracyjnej usługi LinkedIn](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. Wypełnij następujące pola w obszarze **Poświadczenia administratora:**

    * W polu Adres URL `https://api.linkedin.com` **dzierżawy** wprowadź .

    * W polu **Tajny token** wprowadź token dostępu wygenerowany w kroku 1 i kliknij przycisk **Testuj połączenie** .

    * Powiadomienie o sukcesie powinno być widoczne po prawej stronie portalu.

12. Wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej w polu **Wiadomości e-mail z powiadomieniem,** i zaznacz pole wyboru poniżej.

13. Kliknij przycisk **Zapisz**.

14. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika i grupy, które będą synchronizowane z usługi Azure AD do usługi LinkedIn Elevate. Należy zauważyć, że atrybuty wybrane jako **pasujące** właściwości będą używane do dopasowania kont użytkowników i grup w LinkedIn Elevate dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić wszelkie zmiany.

    ![Inicjowanie obsługi administracyjnej usługi LinkedIn](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla usługi LinkedIn Elevate, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia**

16. Kliknij przycisk **Zapisz**.

Spowoduje to rozpoczęcie początkowej synchronizacji wszystkich użytkowników i/lub grup przypisanych do usługi LinkedIn Elevate w sekcji Użytkownicy i grupy. Należy zauważyć, że synchronizacja początkowa potrwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i obserwować łącza do dzienników działań inicjowania obsługi administracyjnej, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji LinkedIn Elevate.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
