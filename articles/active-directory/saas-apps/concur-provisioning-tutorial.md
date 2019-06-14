---
title: 'Samouczek: Konfigurowanie Concur dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Concur.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441aa9805f2a453e22f207238315125d2a281838
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60280402"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Concur dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazanie czynności, które należy wykonać w Concur i Azure AD w celu automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do Concur.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

*   Dzierżawy usługi Azure Active directory.
*   Concur logowanie jednokrotne włączone subskrypcji.
*   Konto użytkownika w Concur z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-concur"></a>Przypisywanie użytkowników do Concur

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście Inicjowanie obsługi administracyjnej konta użytkowników są synchronizowane tylko użytkowników i grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i włącz usługę aprowizacji, musisz zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Concur. Po decyzję, możesz przypisać użytkowników do aplikacji Concur, zgodnie z instrukcjami w tym miejscu:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Concur

*   Zalecane jest, że jeden użytkownik usługi Azure AD można przypisać do Concur do testowania konfiguracji aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania użytkowników do Concur, należy wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-user-provisioning"></a>Włącz aprowizację użytkowników

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do konta użytkownika firmy Concur aprowizujący interfejs API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji, a następnie wyłącz konta użytkowników przypisane w Concur na podstawie przypisania użytkowników i grup w usłudze Azure AD.

> [!Tip] 
> Można też włączyć opartej na SAML logowania jednokrotnego dla Concur, wykonując instrukcje podane w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-user-account-provisioning"></a>Aby skonfigurować, inicjowanie obsługi administracyjnej konta użytkownika:

Jest celem tej sekcji opisano sposób włączyć aprowizację kont użytkowników usługi Active Directory do Concur.

Aby włączyć aplikacje w usłudze wydatków ma musi być prawidłową konfigurację i użycie profilu administratora usługi sieci Web. Nie dodawaj rolę administratora WS do istniejącego profilu administratora używanego dla T & E funkcje administracyjne.

Concur konsultantów lub administrator klienta należy utworzyć odrębne profil administratora usługi sieci Web i administrator klienta, należy użyć tego profilu funkcji administratora usługi sieci Web (na przykład, dzięki czemu aplikacje). Te profile muszą być oddzielone od administratora klienta codzienne T & admin atny (T & atny administratora nie powinny mieć przypisaną rolę WSAdmin).

Podczas tworzenia profilu służący do włączania aplikacji, wprowadź nazwę administratora klienta w polach profilu użytkownika. Powoduje prawa własności do tego profilu. Po utworzeniu co najmniej jeden profil klienta, należy zalogować się do tego profilu, kliknij pozycję "*Włącz*" przycisk aplikacji partnera w ramach menu usługi sieci Web.

Z następujących powodów ta akcja nie powinna być podejmowana z profil, który ich za pomocą której administrujesz normalne T & E.

* Klient musi mieć ten, który kliknie przycisk "*tak*" w oknie dialogu, które jest wyświetlane, gdy aplikacja zostanie włączona. Tego kliknij potwierdza, że klient jest gotowa dla aplikacji partnera dostęp do swoich danych, dzięki czemu możesz lub partnera nie kliknij ten przycisk Tak.

* Jeśli administrator klienta, która włączyła aplikację przy użyciu administratora T & E profilu opuści firmę (co w profilu są zdezaktywowano), wszystkie aplikacje, włączone za pomocą profilu nie działać do czasu włączenia aplikacji z innym profilem administratora WS active. Jest to, dlaczego są powinien utworzyć odrębne administratora WS profile.

* Jeśli administrator opuści firmę, nazwy skojarzonej z profilem administrator WS można zmienić administratorowi zastąpienia, w razie potrzeby bez wywierania wpływu na, że włączono aplikacji, ponieważ ten profil nie jest konieczne zdezaktywowano.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Concur** dzierżawy.

2. Z **administracji** menu, wybierz opcję **usług sieci Web**.
   
    ![Dzierżawy Concur](./media/concur-provisioning-tutorial/IC721729.png "Concur dzierżawy")

3. Po lewej stronie z **usług sieci Web** okienku wybierz **Włączanie aplikacji partnera**.
   
    ![Włącz aplikację partnera](./media/concur-provisioning-tutorial/ic721730.png "partnera aplikacji włączyć")

4. Z **Włączanie aplikacji** listy wybierz **usługi Azure Active Directory**, a następnie kliknij przycisk **Włącz**.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Kliknij przycisk **tak** zamknąć **Potwierdź akcję** okna dialogowego.
   
    ![Potwierdź akcję](./media/concur-provisioning-tutorial/ic721732.png "Potwierdź akcję")

6. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

7. Jeśli już skonfigurowano Concur dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi Concur przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Concur** w galerii aplikacji. Wybierz Concur z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

8. Wybierz wystąpienie Concur, a następnie wybierz **aprowizacji** kartę.

9. Ustaw **tryb obsługi administracyjnej** do **automatyczne**. 
 
    ![Inicjowanie obsługi administracyjnej](./media/concur-provisioning-tutorial/provisioning.png)

10. W obszarze **poświadczeń administratora** sekcji, wprowadź **nazwa_użytkownika** i **hasło** administratora Concur.

11. W witrynie Azure portal kliknij pozycję **Testuj połączenie** aby upewnij się, Azure AD można połączyć się z aplikacji Concur. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Concur ma uprawnienia administratora zespołu.

12. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru.

13. Kliknij przycisk **Zapisz.**

14. W sekcji mapowania, wybierz **synchronizacji Azure użytkownicy usługi Active Directory do Concur.**

15. W **mapowania atrybutów** Przejrzyj atrybutów użytkowników, które są synchronizowane z usługi Azure AD Concur. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Concur operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

16. Aby włączyć usługi Azure AD, usługi dla Concur inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji

17. Kliknij przycisk **Zapisz.**

Teraz można utworzyć konta testowego. Poczekaj do 20 minut, aby sprawdzić, czy konta zostały zsynchronizowane Concur.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](concur-tutorial.md)

