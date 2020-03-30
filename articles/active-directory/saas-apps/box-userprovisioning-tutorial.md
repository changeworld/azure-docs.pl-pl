---
title: 'Samouczek: Konfigurowanie pola do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a box .
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1397b4189a9c2c15e3878687ea8c67c1da7567f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058573"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie pola do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest pokazanie kroków, które należy wykonać w polu i usłudze Azure AD, aby automatycznie aprowizować i usuwać z nich konta użytkowników z usługi Azure AD na Box.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Box, potrzebne są następujące elementy:

- Dzierżawa usługi Azure AD
- Box Business plan lub lepiej

> [!NOTE]
> Podczas testowania kroków w tym samouczku, zaleca się, aby *nie* używać środowiska produkcyjnego.

Aby przetestować kroki w tym samouczku, musisz mieć dostęp do następujących elementów:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska usługi Azure AD w wersji próbnej, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Przypisywanie użytkowników do boxu 

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD jest synchronizowana.

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Box. Po podjęciu decyzji możesz przypisać tych użytkowników do aplikacji Box, postępując zgodnie z instrukcjami tutaj:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Przypisywanie użytkowników i grup
Pole **> użytkownicy i grupy** kartę w witrynie Azure portal umożliwia określenie, którzy użytkownicy i grupy powinny mieć dostęp do Box. Przypisanie użytkownika lub grupy powoduje wystąpienie następujących czynności:

* Usługa Azure AD zezwala przypisanemu użytkownikowi (przez bezpośrednie przypisanie lub członkostwo w grupie) na uwierzytelnienie do boxu. Jeśli użytkownik nie jest przypisany, a następnie usługi Azure AD nie zezwala mu na zalogowanie się do Box i zwraca błąd na stronie logowania usługi Azure AD.
* Kafelek aplikacji dla box jest dodawany do [uruchamiania aplikacji](../manage-apps/end-user-experiences.md)użytkownika .
* Jeśli automatyczne inicjowanie obsługi administracyjnej jest włączone, przypisani użytkownicy i/lub grupy są dodawane do kolejki inicjowania obsługi administracyjnej, aby automatycznie aprowizacji.
  
  * Jeśli tylko obiekty użytkownika zostały skonfigurowane do obsługi administracyjnej, a następnie wszyscy bezpośrednio przypisani użytkownicy są umieszczane w kolejce inicjowania obsługi administracyjnej i wszystkich użytkowników, którzy są członkami wszystkich przypisanych grup są umieszczane w kolejce inicjowania obsługi administracyjnej. 
  * Jeśli obiekty grupy zostały skonfigurowane do obsługi administracyjnej, wszystkie przypisane obiekty grupy są aprowizowane do Box i wszystkich użytkowników, którzy są członkami tych grup. Członkostwo w grupie i użytkowniku jest zachowywane po zapisaniu w polu.

Za pomocą **atrybutów > logowania jednokrotnego** kartę, aby skonfigurować atrybuty użytkownika (lub oświadczenia) są prezentowane do pola podczas uwierzytelniania opartego na SAML i **atrybuty > inicjowania obsługi administracyjnej** kartę, aby skonfigurować przepływ atrybutów użytkowników i grup z usługi Azure AD do Box podczas operacji inicjowania obsługi administracyjnej.

### <a name="important-tips-for-assigning-users-to-box"></a>Ważne wskazówki dotyczące przypisywania użytkowników do boxu 

*   Zaleca się, aby jeden użytkownik usługi Azure AD przypisany do box do testowania konfiguracji inicjowania obsługi administracyjnej. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

*   Podczas przypisywania użytkownika do pola należy wybrać prawidłową rolę użytkownika. Rola "Dostęp domyślny" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-automated-user-provisioning"></a>Włącz automatyczne inicjowanie obsługi administracyjnej użytkowników

W tej sekcji przewodników poprzez łączenie usługi Azure AD do konta użytkownika interfejsu API usługi Box i konfigurowanie usługi inicjowania obsługi administracyjnej do tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w polu na podstawie przypisania użytkownika i grupy w usłudze Azure AD.

Jeśli automatyczne inicjowanie obsługi administracyjnej jest włączone, przypisani użytkownicy i/lub grupy są dodawane do kolejki inicjowania obsługi administracyjnej, aby automatycznie aprowizacji.
    
 * Jeśli tylko obiekty użytkownika są skonfigurowane do obsługi administracyjnej, a następnie bezpośrednio przypisane użytkownicy są umieszczane w kolejce inicjowania obsługi administracyjnej i wszystkich użytkowników, którzy są członkami wszystkich przypisanych grup są umieszczane w kolejce inicjowania obsługi administracyjnej. 
    
 * Jeśli obiekty grupy zostały skonfigurowane do obsługi administracyjnej, wszystkie przypisane obiekty grupy są aprowizowane do Box i wszystkich użytkowników, którzy są członkami tych grup. Członkostwo w grupie i użytkowniku jest zachowywane po zapisaniu w polu.

> [!TIP] 
> Można również włączyć samooczyszczenie jednokrotne dla skrzynki oparte na saml, postępując zgodnie z instrukcjami podanymi w [witrynie Azure portal.](https://portal.azure.com) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-account-provisioning"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej konta użytkownika:

Celem tej sekcji jest określenie sposobu włączania obsługi administracyjnej kont użytkowników usługi Active Directory do box.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > Enterprise Apps > wszystkie aplikacje.**

2. Jeśli pole zostało już skonfigurowane do logowania jednokrotnego, wyszukaj wystąpienie Pole Box za pomocą pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i wyszukaj **pole** w galerii aplikacji. Wybierz pole z wyników wyszukiwania i dodaj go do listy aplikacji.

3. Wybierz wystąpienie Pole, a następnie wybierz kartę **Inicjowanie obsługi administracyjnej.**

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**. 

    ![Inicjowania obsługi](./media/box-userprovisioning-tutorial/provisioning.png)

5. W sekcji **Poświadczenia administratora** kliknij pozycję **Autoryzuj,** aby otworzyć okno dialogowe logowania w oknie wyboru w nowym oknie przeglądarki.

6. Na stronie **Logowanie, aby udzielić dostępu do boxu,** podaj wymagane poświadczenia, a następnie kliknij przycisk **Autoryzuj**. 
   
    ![Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników](./media/box-userprovisioning-tutorial/IC769546.png "Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników")

7. Kliknij **przycisk Udziel dostępu do box,** aby autoryzować tę operację i powrócić do witryny Azure portal. 
   
    ![Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników](./media/box-userprovisioning-tutorial/IC769549.png "Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników")

8. W witrynie Azure portal kliknij pozycję **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z aplikacją Box. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Box ma uprawnienia administratora zespołu i spróbuj ponownie wykonać krok **"Autoryzuj".**

9. Wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej w polu **Wiadomości e-mail powiadomień,** i zaznacz pole wyboru.

10. Kliknij **pozycję Zapisz.**

11. W sekcji Mapowania wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z polem.**

12. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Box. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w polu do operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić wszelkie zmiany.

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla boxu, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji Ustawienia

14. Kliknij **pozycję Zapisz.**

To rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup przypisanych do Box w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do dzienników aktywności inicjowania obsługi administracyjnej, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji Box.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

W dzierżawie box zsynchronizowanych użytkowników są wyświetlane w obszarze **Użytkownicy zarządzane** w **Konsoli administracyjnej**.

![Stan integracji](./media/box-userprovisioning-tutorial/IC769556.png "Stan integracji")


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](box-tutorial.md)
