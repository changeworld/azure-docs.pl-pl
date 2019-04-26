---
title: 'Samouczek: Skonfiguruj obszar roboczy w usłudze Facebook dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c2e23b0d60ca242549ebf2c058ea8f44f2b1c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60520160"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Samouczek: Skonfiguruj obszar roboczy w usłudze Facebook dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest Wam czynności, które należy wykonać w miejscu pracy, Facebook i usługi Azure AD, aby automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do miejsca pracy w serwisie Facebook.

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Workplace by Facebook potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Pracy przez logowania jednokrotnego usługi Facebook włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>Przypisywanie użytkowników do miejsca pracy w serwisie Facebook

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście Inicjowanie obsługi administracyjnej konta użytkowników są synchronizowane tylko użytkowników i grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i włącz usługę aprowizacji, musisz zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy muszą mieć dostęp do miejsca pracy przez aplikację usługi Facebook. Po decyzję, możesz przypisać użytkowników do miejsca pracy przez aplikację usługi Facebook zgodnie z instrukcjami w tym miejscu:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Ważne wskazówki dotyczące przypisywania użytkowników do miejsca pracy w serwisie Facebook

*   Zalecane jest, pojedynczego użytkownika usługi Azure AD jest przypisywany do miejsca pracy przez Facebook, aby przetestować konfigurację aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania użytkownika w serwisie Facebook w miejscu pracy, należy wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-user-provisioning"></a>Włącz Aprowizację użytkowników

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do miejsca pracy przez konto użytkownika w serwisie Facebook aprowizujący interfejs API i konfigurowanie usługi aprowizacji, aby utworzyć, zaktualizować, a następnie wyłącz konta użytkowników przypisane w miejscu pracy w oparciu o użytkowników i grup w usłudze Facebook przypisania w usłudze Azure AD.

>[!Tip]
>Można też włączyć opartej na SAML logowania jednokrotnego dla miejsca pracy, Facebook, postępując zgodnie z instrukcjami dostarczone w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Aby skonfigurować aprowizacją konta użytkownika w serwisie Facebook w miejscu pracy w usłudze Azure AD:

Jest celem tej sekcji opisano sposób włączyć aprowizację kont użytkowników usługi Active Directory, Facebook w miejscu pracy.

Usługi Azure AD umożliwia automatyczną synchronizację szczegóły konta użytkowników przypisane do miejsca pracy w serwisie Facebook. To automatyczną synchronizację umożliwia miejsca pracy, Facebook, aby pobrać dane, jakich potrzebuje do autoryzowania użytkowników na potrzeby dostępu przed ich spróbujesz się zalogować się po raz pierwszy. Także cofnąć aprowizuje użytkowników z miejsca pracy, Facebook, gdy został odwołany dostęp w usłudze Azure AD.

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** sekcji.

2. Jeśli już skonfigurowano miejsca pracy w serwisie Facebook dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia w miejscu pracy, korzystając z pola wyszukiwania w serwisie Facebook. W przeciwnym razie wybierz **Dodaj** i wyszukaj **miejsca pracy w serwisie Facebook** w galerii aplikacji. Wybierz obszar roboczy w serwisie Facebook w wynikach wyszukiwania, a następnie dodaj go do listy aplikacji.

3. Wybierz wystąpienie usługi w miejscu pracy, Facebook, a następnie wybierz **aprowizacji** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**. 

    ![aprowizowanie](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. W obszarze **poświadczeń administratora** sekcji, wprowadź Token dostępu z miejsca pracy przez administratora usługi Facebook i ustaw wartość adres URL dzierżawy `https://www.facebook.com/scim/v1/` . Zobacz te [instrukcje](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) na tworzenie tokenu dostępu dla miejsca pracy. 

6. W witrynie Azure portal kliknij pozycję **Testuj połączenie** zapewniające usługi Azure AD można połączyć z miejsca pracy przy aplikacji usługi Facebook. Jeśli połączenie nie powiedzie się, upewnij się, że z miejsca pracy przez konta w serwisie Facebook, ma uprawnienia administratora zespołu.

7. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru.

8. Kliknij przycisk **Zapisz.**

9. W sekcji mapowania, wybierz **synchronizacji Azure użytkownicy usługi Active Directory, Facebook w miejscu pracy.**

10. W **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do miejsca pracy przez usługi Facebook. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w miejscu pracy usługi Facebook do operacje aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

11. Aby włączyć usługi Azure AD, inicjowania obsługi usługi dla miejsca pracy, Facebook, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji

12. Kliknij przycisk **Zapisz.**

Aby uzyskać więcej informacji na temat konfigurowania automatycznej aprowizacji zobacz [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Teraz można utworzyć konta testowego. Poczekaj do 20 minut, aby sprawdzić, czy konto zostało zsynchronizowane w miejscu pracy w serwisie Facebook.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](workplacebyfacebook-tutorial.md)
