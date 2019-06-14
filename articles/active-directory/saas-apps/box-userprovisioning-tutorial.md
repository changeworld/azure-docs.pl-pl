---
title: 'Samouczek: Konfigurowanie pola dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i pola.
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
ms.openlocfilehash: cd7826455624ca4a84d668455f522cbde411ac8b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60431760"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie pola dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest ukazują kroki, które należy wykonać w polu i usługi Azure AD, aby automatycznie aprowizować i anulować obsługę kont użytkowników z usługi Azure AD do usługi Box.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Box, potrzebne są następujące elementy:

- Dzierżawa usługi Azure AD
- Plan firma pole lub lepszy

> [!NOTE]
> Podczas testowania kroki opisane w tym samouczku, firma Microsoft zaleca wykonanie *nie* za pomocą środowiska produkcyjnego.

Aby przetestować kroki w tym samouczku, musisz mieć dostęp do następujących elementów:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Przypisywanie użytkowników do usługi Box 

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście Inicjowanie obsługi administracyjnej konta użytkowników są synchronizowane tylko użytkowników i grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i włącz usługę aprowizacji, musisz zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Box. Po decyzję, możesz przypisać użytkowników do aplikacji Box, wykonując instrukcje podane w tym miejscu:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Przypisywanie użytkowników i grup
**Pole > Użytkownicy i grupy** kartę w witrynie Azure portal umożliwia określenie, którzy użytkownicy i grupy może być przyznany dostęp do pola. Przypisywanie użytkowników lub grupy powoduje, że wystąpienia następujących czynności:

* Usługa Azure AD umożliwia przypisany użytkownik (albo przez przypisania bezpośredniego lub członkostwa w grupie) do uwierzytelniania w usłudze Box. Jeśli użytkownik nie jest przypisany, usługi Azure AD nie zezwala na go o zalogowanie się do pola i zwraca błąd, na stronie logowania w usłudze Azure AD.
* Do użytkownika zostanie dodany Kafelek aplikacji dla pola [uruchamiania aplikacji](../manage-apps/end-user-experiences.md).
* Po włączeniu automatycznej aprowizacji następnie przypisanych użytkowników i/lub grup są dodawane do inicjowania obsługi administracyjnej kolejki, aby być automatycznie udostępnione.
  
  * Jeśli tylko obiekty użytkownika zostały skonfigurowane do zainicjowania obsługi administracyjnej, następnie wszystkich bezpośrednio przypisanych użytkowników są umieszczane w kolejce inicjowania obsługi administracyjnej i wszystkich użytkowników, którzy są członkami żadnych przypisanych grup są umieszczane w kolejce inicjowania obsługi administracyjnej. 
  * Jeśli obiekty grupy zostały skonfigurowane do zainicjowania obsługi administracyjnej, wszystkie obiekty w przypisanej grupie są aprowizowane do pola, a także wszystkich użytkowników, którzy należą do tych grup. Grupy i użytkownika w grupach są zachowywane po zapisywanych do pola.

Możesz użyć **atrybuty > logowanie jednokrotne** kartę, aby skonfigurować, które atrybutów użytkownika (lub oświadczenia) są dostarczane do usługi Box przy uwierzytelnianie oparte na protokole SAML i **atrybuty > Aprowizowanie** kartę Skonfiguruj, jak atrybuty użytkowników i grup przepływu z usługi Azure AD do pola podczas inicjowania obsługi operacji.

### <a name="important-tips-for-assigning-users-to-box"></a>Ważne wskazówki dotyczące przypisywania użytkowników do usługi Box 

*   Zalecane jest, że jeden użytkownik usługi Azure AD przypisane do pola, aby przetestować konfigurację aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania użytkowników do pola, należy wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-automated-user-provisioning"></a>Włącz automatyczną Aprowizację użytkowników

W tej sekcji prowadzi przez połączenie usługi Azure AD do konta użytkownika pola aprowizujący interfejs API i konfigurowanie usługi aprowizacji, aby utworzyć, zaktualizować, a następnie wyłącz konta użytkowników przypisane w oparciu o przypisania użytkowników i grup w usłudze Azure AD.

Po włączeniu automatycznej aprowizacji następnie przypisanych użytkowników i/lub grup są dodawane do inicjowania obsługi administracyjnej kolejki, aby być automatycznie udostępnione.
    
 * Jeśli tylko obiekty użytkownika są skonfigurowane do udostępnienia, a następnie bezpośrednio przypisanych użytkowników są umieszczane w kolejce inicjowania obsługi administracyjnej i wszystkich użytkowników, którzy są członkami żadnych przypisanych grup są umieszczane w kolejce inicjowania obsługi administracyjnej. 
    
 * Jeśli obiekty grupy zostały skonfigurowane do zainicjowania obsługi administracyjnej, wszystkie obiekty w przypisanej grupie są aprowizowane do pola, a także wszystkich użytkowników, którzy należą do tych grup. Grupy i użytkownika w grupach są zachowywane po zapisywanych do pola.

> [!TIP] 
> Można też włączyć opartej na SAML logowania jednokrotnego dla pola, wykonując instrukcje podane w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-account-provisioning"></a>Aby skonfigurować, aprowizacja kont użytkowników:

Jest celem tej sekcji opisano sposób włączyć aprowizację kont użytkowników usługi Active Directory do usługi Box.

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

2. Jeśli już skonfigurowano pola dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi Box przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **pole** w galerii aplikacji. Zaznacz pole w wynikach wyszukiwania, a następnie dodaj go do listy aplikacji.

3. Wybierz wystąpienie, pola, a następnie wybierz **aprowizacji** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**. 

    ![Inicjowanie obsługi administracyjnej](./media/box-userprovisioning-tutorial/provisioning.png)

5. W obszarze **poświadczeń administratora** kliknij **Autoryzuj** aby otworzyć okno dialogowe logowania w nowym oknie przeglądarki.

6. Na **logowania, aby udzielić dostępu do usługi Box** strony, podaj poświadczenia wymagane, a następnie kliknij przycisk **Autoryzuj**. 
   
    ![Włączanie automatycznej aprowizacji użytkowników](./media/box-userprovisioning-tutorial/IC769546.png "Włączanie automatycznej aprowizacji użytkowników")

7. Kliknij przycisk **udzielić dostępu do usługi Box** Aby autoryzować tę operację i powrócić do witryny Azure portal. 
   
    ![Włączanie automatycznej aprowizacji użytkowników](./media/box-userprovisioning-tutorial/IC769549.png "Włączanie automatycznej aprowizacji użytkowników")

8. W witrynie Azure portal kliknij pozycję **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z aplikacji Box. Jeśli połączenie nie powiedzie się, upewnij się, koncie usługi Box ma uprawnienia administratora zespołu i spróbuj **"Autoryzuj"** krok ponownie.

9. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru.

10. Kliknij przycisk **Zapisz.**

11. W sekcji mapowania, wybierz **synchronizacji Azure użytkownicy usługi Active Directory do usługi Box.**

12. W **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do usługi Box. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w usłudze Box dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

13. Aby włączyć usługi Azure AD, inicjowania obsługi usługi dla pola, zmień **stanie aprowizacji** do **na** w sekcji Ustawienia

14. Kliknij przycisk **Zapisz.**

Którego początkowe wstępnej synchronizacji użytkowników i/lub grupy przypisane do pola w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności, które opisują każdą akcję wykonaną przez usługę aprowizacji w aplikacji Box.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

W Twojej dzierżawie pole zsynchronizowani użytkownicy są wyświetlane w obszarze **zarządzanych użytkowników** w **konsoli administracyjnej**.

![Stan integracji](./media/box-userprovisioning-tutorial/IC769556.png "stan integracji")


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](box-tutorial.md)
