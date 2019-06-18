---
title: 'Samouczek: Konfigurowanie Velpic do automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do Velpic.
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
ms.openlocfilehash: 16c302fbe151d6cd8c2198240bc31a2bd69dbd7b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337684"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Velpic do automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazanie czynności, które należy wykonać w Velpic i Azure AD w celu automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do Velpic.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Dzierżawcy Velpic z [Enterprise plan](https://www.velpic.com/pricing.html) lub lepiej nie są włączone
* Konto użytkownika Velpic z uprawnieniami administratora

## <a name="assigning-users-to-velpic"></a>Przypisywanie użytkowników do Velpic

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście konta użytkownika automatycznego inicjowania obsługi administracyjnej tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD będą synchronizowane. 

Przed Skonfiguruj i włącz usługę aprowizacji, należy zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Velpic. Po decyzję, możesz przypisać użytkowników do aplikacji Velpic, zgodnie z instrukcjami w tym miejscu:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Velpic

* Zalecane jest, że jeden użytkownik usługi Azure AD można przypisać do Velpic do testowania konfiguracji aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do Velpic, należy wybrać **użytkownika** roli, lub inną prawidłową specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Należy pamiętać, że **domyślnego dostępu** roli nie działa w przypadku inicjowania obsługi administracyjnej i Ci użytkownicy zostaną pominięte.

## <a name="configuring-user-provisioning-to-velpic"></a>Konfigurowaniem aprowizowania użytkowników w Velpic

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do konta użytkownika w Velpic aprowizujący interfejs API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączyć przypisane do kont użytkowników w Velpic na podstawie użytkownika i przypisanie do grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla Velpic, wykonując instrukcje podane w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Aby skonfigurować automatyczne aprowizowaniem kont użytkowników do Velpic w usłudze Azure AD:

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

2. Jeśli już skonfigurowano Velpic dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi Velpic przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Velpic** w galerii aplikacji. Wybierz Velpic z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

3. Wybierz wystąpienie Velpic, a następnie wybierz **aprowizacji** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Velpic inicjowania obsługi administracyjnej.](./media/velpic-provisioning-tutorial/Velpic1.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **adres URL dzierżawy i klucz tajny tokenu** z Velpic. () Te wartości można znaleźć w ramach konta usługi Velpic: **Zarządzanie** > **integracji** > **wtyczki** > **Standard SCIM**)

    ![Wartości autoryzacji](./media/velpic-provisioning-tutorial/Velpic2.png)

6. W witrynie Azure portal kliknij pozycję **Testuj połączenie** zapewniające usługi Azure AD connect można Velpic aplikacji. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Velpic ma uprawnienia administratora i spróbuj ponownie krok 5.

7. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz poniższe pole wyboru.

8. Kliknij pozycję **Zapisz**.

9. W sekcji mapowania, wybierz **synchronizacji Azure użytkownicy usługi Active Directory do Velpic**.

10. W **mapowania atrybutów** Przejrzyj atrybutów użytkowników, które będą synchronizowane z usługi Azure AD z usługą Velpic. Należy zauważyć, że atrybuty wybrany jako **zgodne** właściwości, które będą używane do dopasowania kont użytkowników w Velpic operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

11. Aby włączyć usługi Azure AD, usługi dla Velpic inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji

12. Kliknij pozycję **Zapisz**.

Spowoduje to uruchomienie synchronizacji wstępnej użytkowników i/lub grupy przypisane do Velpic w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacja początkowa może potrwać dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do udostępniania raportów działań, które opisują każdą akcję wykonaną przez usługę aprowizacji.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)