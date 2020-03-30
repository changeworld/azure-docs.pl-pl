---
title: 'Samouczek: Konfigurowanie velpic do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i wyrównywalizacji kont użytkowników do programu Velpic.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b7a6c2c9b7ecb0b160f7481d95f7682f3f7a109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064125"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie velpic do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest pokazanie kroki, które należy wykonać w velpic i usługi Azure AD, aby automatycznie aprowizować i wyrównywać konta użytkowników z usługi Azure AD do Velpic.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Najemca Velpic z [planem Enterprise](https://www.velpic.com/pricing.html) lub lepiej włączony
* Konto użytkownika w velpic z uprawnieniami administratora

## <a name="assigning-users-to-velpic"></a>Przypisywanie użytkowników do velpic

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD zostaną zsynchronizowane. 

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Velpic. Po podjęciu decyzji, można przypisać tych użytkowników do aplikacji Velpic, postępując zgodnie z instrukcjami tutaj:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Velpic

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do velpic do testowania konfiguracji inicjowania obsługi administracyjnej. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do velpic, należy wybrać rolę **użytkownika** lub inną prawidłową rolę specyficzne dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Należy zauważyć, że rola **dostępu domyślnego** nie działa w przypadku inicjowania obsługi administracyjnej, a ci użytkownicy zostaną pominięci.

## <a name="configuring-user-provisioning-to-velpic"></a>Konfigurowanie inicjowania obsługi administracyjnej użytkownika w ucho.

W tej sekcji przewodnik po połączeniu usługi Azure AD z interfejsem API inicjowania obsługi administracyjnej konta użytkownika w programie Velpic i skonfigurowaniu usługi inicjowania obsługi administracyjnej w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w programie Velpic na podstawie przypisania użytkownika i grupy w usłudze Azure AD.

> [!TIP]
> Można również włączyć samoobsesję jednoobjętą opartą na SAML dla velpic, postępując zgodnie z instrukcjami podanymi w [witrynie Azure portal.](https://portal.azure.com) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej konta użytkownika do velpic w usłudze Azure AD:

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > Enterprise Apps > wszystkie aplikacje.**

2. Jeśli velpic został już skonfigurowany do logowania jednokrotnego, wyszukaj wystąpienie Velpic za pomocą pola wyszukiwania. W przeciwnym razie wybierz **pozycję Dodaj** i wyszukaj **Velpic** w galerii aplikacji. Wybierz Velpic z wyników wyszukiwania i dodaj go do listy aplikacji.

3. Wybierz wystąpienie Velpic, a następnie wybierz kartę **Inicjowanie obsługi administracyjnej.**

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Inicjowanie obsługi administracyjnej velpic](./media/velpic-provisioning-tutorial/Velpic1.png)

5. W sekcji **Poświadczenia administratora** wprowadź **adres URL dzierżawy&tajny token** Velpic. (Można znaleźć te wartości na koncie Velpic: **Zarządzanie** > **Integracja** > **Plugin** > **SCIM**)

    ![Wartości autoryzacji](./media/velpic-provisioning-tutorial/Velpic2.png)

6. W witrynie Azure portal kliknij pozycję **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z aplikacją Velpic. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Velpic ma uprawnienia administratora i spróbuj ponownie wykonać krok 5.

7. Wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej w polu **Wiadomości e-mail z powiadomieniem,** i zaznacz pole wyboru poniżej.

8. Kliknij przycisk **Zapisz**.

9. W sekcji Mapowania wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z velpic**.

10. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika, które będą synchronizowane z usługi Azure AD do Velpic. Należy zauważyć, że atrybuty wybrane jako **pasujące** właściwości będą używane do dopasowania kont użytkowników w Velpic dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić wszelkie zmiany.

11. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla programu Velpic, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia**

12. Kliknij przycisk **Zapisz**.

Rozpocznie to początkową synchronizację wszystkich użytkowników i/lub grup przypisanych do Velpic w sekcji Użytkownicy i grupy. Należy zauważyć, że synchronizacja początkowa potrwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i obserwować łącza do raportów działań inicjowania obsługi administracyjnej, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)