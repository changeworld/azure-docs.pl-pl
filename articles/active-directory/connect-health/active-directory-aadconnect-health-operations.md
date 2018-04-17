---
title: Operacje platformy Azure Active Directory Connect Health
description: W tym artykule opisano dodatkowe operacje, które mogą być wykonywane po wdrożeniu usługi Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: karavar
manager: mtillman
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: c9920857f7d1910921b4bb3771e770fb4c3a42c3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-active-directory-connect-health-operations"></a>Operacje platformy Azure Active Directory Connect Health
W tym temacie opisano różne operacje, które można wykonać za pomocą usługi Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Włącz powiadomienia e-mail
Można skonfigurować usługę Azure AD Connect Health do wysyłania powiadomień e-mail, gdy alerty wskazują na to, że infrastruktury tożsamości nie jest w dobrej kondycji. Dzieje się tak, gdy alert jest generowany, a po rozwiązaniu.

![Ustawienia powiadomień pocztą e-mail programu zrzut ekranu Azure AD Connect Health](./media/active-directory-aadconnect-health/email_noti_discover.png)

> [!NOTE]
> Powiadomienia e-mail są domyślnie włączone.
>
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Aby włączyć powiadomienia e-mail Azure AD Connect Health
1. Otwórz **alerty** bloku dla usługi, dla którego chcesz otrzymywać powiadomienia e-mail.
2. Na pasku akcji kliknij **ustawienia powiadomień**.
3. Przełącznik powiadomienia e-mail, wybierz **ON**.
4. Zaznacz pole wyboru, jeśli chcesz, aby wszystkim administratorom globalnym, aby otrzymywać powiadomienia pocztą e-mail.
5. Jeśli chcesz otrzymywać powiadomienia pocztą e-mail na inne adresy e-mail, określ je w **dodatkowych adresatów wiadomości E-mail** pole. Aby usunąć adres e-mail z tej listy, kliknij prawym przyciskiem myszy wpis, a następnie wybierz **usunąć**.
6. Aby zakończyć zmiany, kliknij przycisk **zapisać**. Zmiany zaczynają obowiązywać dopiero po zapisaniu.

## <a name="delete-a-server-or-service-instance"></a>Usuń wystąpienia usługi lub serwera

>[!NOTE] 
> Azure AD premium licencji jest wymagana dla procedury usuwania.

W niektórych przypadkach możesz usunąć serwer z monitorowane. Oto, co należy wiedzieć, aby usunąć serwer z usługi Azure AD Connect Health.

W przypadku usuwania serwera, należy pamiętać o następujących czynności:

* Ta akcja powoduje zatrzymanie zbierania danych z tego serwera. Ten serwer jest usuwany z usługi monitorowania. Po wykonaniu tej akcji nie jest możliwe do wyświetlania nowych alertów, monitorowania lub dane analityczne użycia dla tego serwera.
* Ta akcja nie powoduje odinstalowania agenta programu Health z serwera. Jeśli nie odinstalowano agenta programu Health przed wykonaniem tej czynności, mogą występować błędy związane z agentem programu Health na serwerze.
* Ta akcja nie powoduje usunięcia danych już zebrane z tego serwera. Że dane zostaną usunięte zgodnie z zasadami przechowywania danych platformy Azure.
* Po wykonaniu tej akcji, jeśli chcesz rozpocząć monitorowanie tego samego serwera, należy odinstalować i ponownie zainstalować agenta programu Health na tym serwerze.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Usuwanie serwera z usługi Azure AD Connect Health

>[!NOTE] 
> Azure AD premium licencji jest wymagana dla procedury usuwania.

Azure AD Connect Health dla usług federacyjnych Active Directory (AD FS) i Azure AD Connect (synchronizacja):

1. Otwórz **serwera** bloku z **listy serwerów** bloku, wybierając nazwę serwera, który ma zostać usunięty.
2. Na **serwera** bloku na pasku akcji kliknij **usunąć**.
3. Upewnij się, wpisując nazwę serwera w polu potwierdzenia.
4. Kliknij polecenie **Usuń**.

Azure AD Connect Health dla usług domenowych w usłudze Azure Active Directory:

1. Otwórz **kontrolerów domeny** pulpitu nawigacyjnego.
2. Wybierz kontroler domeny, który ma zostać usunięty.
3. Na pasku akcji kliknij **usunąć wybrane**.
4. Potwierdź tę akcję można usunąć serwera.
5. Kliknij polecenie **Usuń**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Usuń wystąpienia usługi z usługi Azure AD Connect Health
W niektórych przypadkach można usunąć wystąpienia usługi. Oto, co należy wiedzieć, aby usunąć wystąpienie usługi z usługi Azure AD Connect Health.

Gdy usuwane wystąpienie usługi, należy pamiętać o następujących czynności:

* Ta akcja powoduje usunięcie bieżącego wystąpienia usługi z usługi monitorowania.
* Ta akcja nie odinstalować lub usunąć agenta programu Health z żadnym z serwerów monitorowane w ramach tego wystąpienia usługi. Jeśli nie odinstalowano agenta programu Health przed wykonaniem tej czynności, mogą występować błędy związane z agentem programu Health na serwerach.
* Wszystkie dane z tego wystąpienia usługi zostaną usunięte zgodnie z zasadami przechowywania danych platformy Azure.
* Po wykonaniu tej akcji, jeśli chcesz rozpocząć monitorowanie usługi, odinstaluj i ponownie zainstalować agenta programu Health na wszystkich serwerach. Po wykonaniu tej akcji, jeśli chcesz rozpocząć monitorowanie tego samego serwera ponownie odinstalować, ponownie zainstaluj i zarejestruj agenta programu Health na tym serwerze.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Aby usunąć wystąpienie usługi z usługi Azure AD Connect Health
1. Otwórz **usługi** bloku z **listę usług** bloku, wybierając z identyfikatorem usługi (nazwa farmy), który chcesz usunąć.
2. Na **serwera** bloku na pasku akcji kliknij **usunąć**.
3. Upewnij się, wpisując nazwę usługi w polu potwierdzenia (na przykład: sts.contoso.com).
4. Kliknij polecenie **Usuń**.
   <br><br>

[//]: # (Początek sekcji RBAC)
## <a name="manage-access-with-role-based-access-control"></a>Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach
[Kontrola dostępu oparta na rolach (RBAC)](../../role-based-access-control/role-assignments-portal.md) dla usługi Azure AD Connect Health zapewnia dostęp do użytkowników i grup innych niż administratorzy globalni. RBAC przypisuje role dla uprawnionych użytkowników i grup oraz zapewnia mechanizm, aby ograniczyć liczbę administratorów globalnych w katalogu.

### <a name="roles"></a>Role
Azure AD Connect Health obsługuje następujące wbudowane role:

| Rola | Uprawnienia |
| --- | --- |
| Właściciel |Właściciele mogą *zarządzanie dostępem do* (na przykład przypisać rolę do użytkownika lub grupy), *wyświetlać wszystkie informacje* (na przykład wyświetlanie alertów) z portalu i *zmienić ustawienia* (dla przykład, powiadomienia e-mail) w ramach usługi Azure AD Connect Health. <br>Domyślnie administratorzy globalni usługi Azure AD są przypisani do tej roli, a nie można zmienić. |
| Współautor |Współautorzy mogą *wyświetlać wszystkie informacje* (na przykład wyświetlanie alertów) z portalu i *zmienić ustawienia* (na przykład wiadomości e-mail z powiadomieniami) w ramach usługi Azure AD Connect Health. |
| Czytelnik |Czytelnicy mogą *wyświetlać wszystkie informacje* (na przykład wyświetlanie alertów) z portalu w ramach usługi Azure AD Connect Health. |

Wszystkie role (na przykład Administratorzy dostępu użytkownika lub DevTest Labs użytkowników) nie mają wpływu do dostępu w ramach usługi Azure AD Connect Health, nawet jeśli role są dostępne w portalu obsługi.

### <a name="access-scope"></a>Zakres dostępu
Azure AD Connect Health obsługuje zarządzanie dostępem na dwóch poziomach:

* **Wszystkie wystąpienia usługi**: jest to ścieżka zalecana w większości przypadków. Określa dostęp do wszystkich wystąpień usługi (na przykład farmy usług AD FS), wszystkich typów roli, które są monitorowane przez program Azure AD Connect Health.
* **Wystąpienie usługi**: W niektórych przypadkach może być konieczne segregowanie dostępu na podstawie typów roli lub przez wystąpienie usługi. W takim przypadku można zarządzać dostęp na poziomie wystąpienia usługi.  

Uprawnienie zostanie udzielone, jeśli użytkownik ma dostęp w katalogu lub usługi wystąpienie poziomu.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Zezwalaj użytkownikom lub grupom dostęp do usługi Azure AD Connect Health
Poniższe kroki pokazują, jak zezwolić na dostęp.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Krok 1: Wybierz zakres dostępu
Aby zezwolić użytkownikowi dostępu w *wszystkich wystąpień usługi* poziomu w ramach usługi Azure AD Connect Health, otwórz głównego bloku w Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Krok 2: Dodawanie użytkowników i grup i przypisz role
1. Z **Konfiguruj** kliknij **użytkowników**.<br>
   ![Zrzut ekranu Azure AD Connect Health RBAC głównego bloku użytkownikom wyróżnione](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Wybierz pozycję **Dodaj**.
3. W **wybierz rolę** okienku, wybierz rolę (na przykład **właściciela**).<br>
   ![Zrzut ekranu Azure AD Connect Health RBAC użytkowników okna](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Wpisz nazwę lub identyfikator wybrany użytkownik lub grupa. Jednocześnie można wybrać jeden lub więcej użytkowników lub grup. Kliknij pozycję **Wybierz**.
   ![Zrzut ekranu Azure AD Connect Health RBAC użytkowników okna](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Kliknij przycisk **OK**.<br>
6. Po zakończeniu przypisania roli użytkowników i grup są wyświetlane na liście.<br>
   ![Zrzut ekranu Azure AD Connect Health RBAC użytkowników okno, przy użyciu nowych użytkowników wyróżnione](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Teraz wyświetlani użytkownicy i grupy mają dostęp, zgodnie z ich przypisane role.

> [!NOTE]
> * Administratorzy globalni zawsze mają pełny dostęp do wszystkich operacji, ale nie są obecne w powyższej listy kont administratora globalnego.
> * Funkcja zaprosić użytkowników nie jest obsługiwana w ramach usługi Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Krok 3: Udostępnianie lokalizacji bloku użytkowników lub grup
1. Po przypisaniu uprawnienia, użytkownik może uzyskać dostęp Azure AD Connect Health, przechodząc [tutaj](https://aka.ms/aadconnecthealth).
2. W bloku użytkownika można przypiąć blok lub składniki, do pulpitu nawigacyjnego. Po prostu kliknij **Przypnij do pulpitu nawigacyjnego** ikony.<br>
   ![Zrzut ekranu Azure AD Connect Health RBAC Przypnij blok z ikonę pinezki podświetlone](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)

> [!NOTE]
> Użytkownik mający rolę czytelnika przypisane nie będzie mógł uzyskać rozszerzenie Azure AD Connect Health z portalu Azure Marketplace. Użytkownik nie może wykonać niezbędnych operacji w tym celu "Utwórz". Użytkownik może nadal otrzymywać do bloku, przechodząc do poprzedniego łącza. Kolejne użycia przez użytkownika można przypiąć blok do pulpitu nawigacyjnego.
>
>

### <a name="remove-users-or-groups"></a>Usuwanie użytkowników lub grup
Można usunąć użytkownika lub grupy dodane do usługi Azure AD Connect RBAC kondycji. Po prostu kliknij prawym przyciskiem myszy użytkownika lub grupy i wybierz **Usuń**.<br>
![Zrzut ekranu Azure AD Connect Health RBAC użytkowników okna, z Usuń wyróżniony](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (Koniec sekcji RBAC)

## <a name="next-steps"></a>Kolejne kroki
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalowanie agenta programu Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Używanie programu Azure AD Connect Health z usługami AD FS](active-directory-aadconnect-health-adfs.md)
* [Używanie programu Azure AD Connect Health w celu synchronizacji](active-directory-aadconnect-health-sync.md)
* [Używanie programu Azure AD Connect Health z usługami AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health — często zadawane pytania](active-directory-aadconnect-health-faq.md)
* [Historia wersji programu Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
