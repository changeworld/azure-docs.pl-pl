---
title: 'Samouczek: Konfigurowanie usługi Dropbox do automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Dropbox dla Firm.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f3a42e4-6897-4234-af84-b47c148ec3e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d53d10b036a37489be0b7aae6208880044b766a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60280027"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi Dropbox dla firm dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazanie czynności, które należy wykonać w usłudze Dropbox dla firm i Azure AD, do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do usługi Dropbox dla firm.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

*   Dzierżawy usługi Azure Active directory.
*   Dropbox dla firm logowania jednokrotnego włączonych subskrypcji.
*   Konto użytkownika w usłudze Dropbox dla firm z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-dropbox-for-business"></a>Przypisywanie użytkowników do usługi Dropbox dla firm

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście Inicjowanie obsługi administracyjnej konta użytkowników są synchronizowane tylko użytkowników i grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i włącz usługę aprowizacji, musisz zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do usługi Dropbox dla firm. Po decyzję, możesz przypisać użytkowników do usługi Dropbox dla firm, zgodnie z instrukcjami w tym miejscu:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Ważne wskazówki dotyczące przypisywania użytkowników do usługi Dropbox dla firm

*   Zalecane jest, że jeden użytkownik usługi Azure AD jest przypisany do usługi Dropbox dla firm, aby przetestować konfigurację aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania użytkowników do usługi Dropbox dla firm, należy wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi...

## <a name="enable-automated-user-provisioning"></a>Włącz automatyczną Aprowizację użytkowników

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do usługi Dropbox dla konta użytkownika firmy aprowizujący interfejs API i konfigurowanie usługi aprowizacji, aby utworzyć, zaktualizować, a następnie wyłącz konta użytkowników przypisane w usłudze Dropbox dla firm, w oparciu o użytkowników i grup przypisania w usłudze Azure AD.

>[!Tip]
>Można też włączyć opartej na SAML logowania jednokrotnego do usługi Dropbox dla firm, wykonując instrukcje podane w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-account-provisioning"></a>Aby skonfigurować, aprowizacja kont użytkowników:

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

2. Jeśli została już skonfigurowana usługa Dropbox dla firm dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi Dropbox dla firm przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **usługa Dropbox dla firm** w galerii aplikacji. Wybierz usługa Dropbox dla firm z listy wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

3. Wybierz wystąpienie usługi Dropbox dla firm, a następnie wybierz **aprowizacji** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**. 

    ![aprowizowanie](./media/dropboxforbusiness-provisioning-tutorial/provisioning.png)

5. W obszarze **poświadczeń administratora** kliknij **Autoryzuj**. Dropbox dla okna dialogowego logowania firmy zostanie otwarty w nowym oknie przeglądarki.

6. Na **logowania do usługi Dropbox, aby połączyć się z usługą Azure AD** okno dialogowe, zaloguj się do usługi Dropbox dla firm dzierżawy.

     ![Inicjowanie obsługi użytkowników](./media/dropboxforbusiness-provisioning-tutorial/ic769518.png "aprowizacji użytkowników")

7. Upewnij się, że chcesz przyznać uprawnienia usługi Azure Active Directory, aby wprowadzić zmiany w usłudze Dropbox dla firm dzierżawy. Kliknij przycisk **Zezwalaj**.
    
      ![Inicjowanie obsługi użytkowników](./media/dropboxforbusiness-provisioning-tutorial/ic769519.png "aprowizacji użytkowników")

8. W witrynie Azure portal kliknij pozycję **Testuj połączenie** aby upewnij się, Azure AD można połączyć się z usługi Dropbox dla firm. Jeśli połączenie nie powiedzie się, upewnij się, usłudze Dropbox dla firm konto ma uprawnienia administratora zespołu i spróbuj **"Autoryzuj"** krok ponownie.

9. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru.

10. Kliknij przycisk **Zapisz.**

11. W sekcji mapowania, wybierz **synchronizacji Azure użytkownicy usługi Active Directory do usługi Dropbox dla firm.**

12. W **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do usługi Dropbox dla firm. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w usłudze Dropbox dla firm dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

13. Aby włączyć usługi Azure AD, inicjowania obsługi usługi Dropbox dla firm, zmień **stanie aprowizacji** do **na** w sekcji Ustawienia

14. Kliknij przycisk **Zapisz.**

Rozpoczyna się wstępna synchronizacja użytkowników i/lub grupy przypisane do usługi Dropbox dla firm w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności, które opisują każdą akcję wykonaną przez usługę aprowizacji w usłudze Dropbox dla firm.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](dropboxforbusiness-tutorial.md)
