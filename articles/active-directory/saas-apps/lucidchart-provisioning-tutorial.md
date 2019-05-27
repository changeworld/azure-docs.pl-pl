---
title: 'Samouczek: Konfigurowanie LucidChart dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do LucidChart.
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
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9540cf882af6b11f0e8624e477ad336f6d5d9ad3
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963681"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie LucidChart dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazanie czynności, które należy wykonać w LucidChart i Azure AD w celu automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do LucidChart. 

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

* Dzierżawy usługi Azure Active directory
* Dzierżawcy LucidChart z [Enterprise plan](https://www.lucidchart.com/user/117598685#/subscriptionLevel) lub lepiej nie są włączone
* Konto użytkownika LucidChart z uprawnieniami administratora

## <a name="assigning-users-to-lucidchart"></a>Przypisywanie użytkowników do LucidChart

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście Inicjowanie obsługi administracyjnej konta użytkowników są synchronizowane tylko użytkowników i grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i włącz usługę aprowizacji, musisz zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji LucidChart. Po decyzję, możesz przypisać użytkowników do aplikacji LucidChart, zgodnie z instrukcjami w tym miejscu:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Ważne wskazówki dotyczące przypisywania użytkowników do LucidChart

* Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do LucidChart do testowania konfiguracji aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do LucidChart, należy wybrać **użytkownika** roli, lub inną prawidłową specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. **Domyślnego dostępu** roli nie działa w przypadku inicjowania obsługi administracyjnej i Ci użytkownicy są pomijane.

## <a name="configuring-user-provisioning-to-lucidchart"></a>Konfigurowaniem aprowizowania użytkowników w LucidChart

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do konta użytkownika w LucidChart aprowizujący interfejs API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji, a następnie wyłącz konta użytkowników przypisane w LucidChart na podstawie przypisania użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla LucidChart, wykonując instrukcje podane w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Skonfiguruj automatyczne aprowizowaniem kont użytkowników do LucidChart w usłudze Azure AD

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

2. Jeśli już skonfigurowano LucidChart dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi LucidChart przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **LucidChart** w galerii aplikacji. Wybierz LucidChart z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

3. Wybierz wystąpienie LucidChart, a następnie wybierz **aprowizacji** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![LucidChart inicjowania obsługi administracyjnej.](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **klucz tajny tokenu** generowane przez konto usługi LucidChart (tokenu można znaleźć w ramach konta usługi: **Zespół** > **integracji aplikacji** > **Standard SCIM**).

    ![LucidChart inicjowania obsługi administracyjnej.](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. W witrynie Azure portal kliknij pozycję **Testuj połączenie** zapewniające usługi Azure AD connect można LucidChart aplikacji. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto LucidChart ma uprawnienia administratora i spróbuj ponownie krok 5.

7. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi błąd."

8. Kliknij pozycję **Zapisz**.

9. W sekcji mapowania, wybierz **synchronizacji Azure użytkownicy usługi Active Directory do LucidChart**.

10. W **mapowania atrybutów** Przejrzyj atrybutów użytkowników, które są synchronizowane z usługi Azure AD LucidChart. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w LucidChart operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

11. Aby włączyć usługi Azure AD, usługi dla LucidChart inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji

12. Kliknij pozycję **Zapisz**.

Ta operacja uruchamia wstępnej synchronizacji użytkowników i/lub grupy przypisane do LucidChart w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności, które opisują każdą akcję wykonaną przez usługę aprowizacji.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)
