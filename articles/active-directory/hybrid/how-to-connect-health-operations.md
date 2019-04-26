---
title: Operacje usługi Azure Active Directory Connect Health
description: W tym artykule opisano dodatkowe operacje, które mogą być wykonywane po wdrożeniu usługi Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 090a066afb24c4776f9844b8850264ffad842c59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60350156"
---
# <a name="azure-active-directory-connect-health-operations"></a>Operacje usługi Azure Active Directory Connect Health
W tym temacie opisano różne operacje, które można wykonać przy użyciu usługi Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Włącz powiadomienia e-mail
Można skonfigurować usługę Azure AD Connect Health do wysyłania powiadomień e-mail, gdy alerty wskazują, że swoją infrastrukturę tożsamości nie jest w dobrej kondycji. Dzieje się tak, gdy alert jest generowany, a po rozwiązaniu.

![Ustawienia powiadomień e-mail programu zrzut ekranu programu Azure AD Connect Health](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> Powiadomienia e-mail są domyślnie włączone.
>
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Aby włączyć powiadomienia e-mail w usłudze Azure AD Connect Health
1. Otwórz **alerty** bloku dla usługi, dla którego chcesz otrzymywać powiadomienia e-mail.
2. Na pasku akcji kliknij **ustawień powiadomień**.
3. O przełącznikiem powiadomień e-mail wybierz **ON**.
4. Zaznacz pole wyboru, jeśli chcesz, aby wszyscy administratorzy globalni, aby otrzymywać powiadomienia e-mail.
5. Jeśli chcesz otrzymywać powiadomień pocztą e-mail na inne adresy e-mail, określ je w **dodatkowych adresatów wiadomości E-mail** pole. Aby usunąć adres e-mail z tej listy, kliknij prawym przyciskiem myszy wpis, a następnie wybierz **Usuń**.
6. Aby zakończyć zmiany, kliknij przycisk **Zapisz**. Zmiany wprowadzone dopiero po zapisaniu.

## <a name="delete-a-server-or-service-instance"></a>Usuń wystąpienie serwera lub usługi

>[!NOTE] 
> Licencja na usługę Azure AD — wersja premium jest wymagana na potrzeby procedury usuwania.

W niektórych przypadkach możesz chcieć usunąć serwer z monitorowanych. Oto, co musisz wiedzieć, aby usunąć serwer z usługi Azure AD Connect Health.

Gdy usuwane serwera, należy pamiętać o następujących czynności:

* Ta akcja zatrzymania zbierania wszelkich dalszych danych z tego serwera. Ten serwer jest usuwany z usługi monitorowania. Po wykonaniu tej akcji nie jest możliwe do wyświetlania nowych alertów, monitorowania lub dane analizy użycia dla tego serwera.
* Ta akcja nie powoduje odinstalowania agenta programu Health z serwera. Jeśli nie odinstalowano agenta programu Health przed wykonaniem tej czynności, można napotkać błędy związane z agentem programu Health na serwerze.
* Ta akcja nie powoduje usunięcia danych już zebranych z tego serwera. Że dane zostaną usunięte zgodnie z zasadami przechowywania danych platformy Azure.
* Po wykonaniu tej akcji, jeśli chcesz rozpocząć monitorowanie tego samego serwera ponownie, należy odinstalować i ponownie zainstalować agenta programu Health na tym serwerze.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Usuwanie serwera z usługi Azure AD Connect Health

>[!NOTE] 
> Licencja na usługę Azure AD — wersja premium jest wymagana na potrzeby procedury usuwania.

Azure AD Connect Health dla usługi Active Directory Federation Services (AD FS) i usługi Azure AD Connect (synchronizacja):

1. Otwórz **serwera** bloku z **listy serwerów** bloku, wybierając nazwę serwera, który ma zostać usunięty.
2. Na **serwera** bloku na pasku akcji kliknij **Usuń**.
![Zrzut ekranu programu Azure AD Connect Health usuwanie serwera](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Potwierdź, wpisując nazwę serwera w okno dialogowe z potwierdzeniem.
4. Kliknij polecenie **Usuń**.

Azure AD Connect Health dla usług Azure Active Directory Domain Services:

1. Otwórz **kontrolery domeny** pulpitu nawigacyjnego.
2. Wybierz kontroler domeny, który ma zostać usunięty.
3. Na pasku akcji kliknij **Usuń wybrane**.
4. Potwierdź tę akcję można usunąć serwera.
5. Kliknij polecenie **Usuń**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Usuń wystąpienie usługi z usługi Azure AD Connect Health
W niektórych przypadkach można usunąć wystąpienia usługi. Oto, co musisz wiedzieć, aby usunąć wystąpienie usługi z usługi Azure AD Connect Health.

Gdy usuwane wystąpienia usługi, należy pamiętać o następujących czynności:

* Ta akcja spowoduje usunięcie bieżącego wystąpienia usługi z usługi monitorowania.
* Ta akcja nie odinstalować lub usunąć agenta programu Health z dowolnego z serwerów, które były monitorowane w ramach tego wystąpienia usługi. Jeśli nie odinstalowano agenta programu Health przed wykonaniem tej czynności, można napotkać błędy związane z agentem programu Health na serwerach.
* Wszystkie dane tego wystąpienia usługi zostaną usunięte zgodnie z zasadami przechowywania danych platformy Azure.
* Po wykonaniu tej akcji, jeśli chcesz rozpocząć monitorowanie usługi, odinstaluj i ponownie zainstalować agenta programu Health na wszystkich serwerach. Po wykonaniu tej akcji, jeśli chcesz rozpocząć monitorowanie tego samego serwera ponownie, odinstaluj, zainstaluj ponownie i zarejestrować agenta programu Health na tym serwerze.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Aby usunąć wystąpienie usługi z usługi Azure AD Connect Health
1. Otwórz **usługi** bloku z **listę usług** bloku, wybierając identyfikator usług (nazwa farmy), który chcesz usunąć. 
2. Na **usługi** bloku na pasku akcji kliknij **Usuń**. 
![Zrzut ekranu programu Azure AD Connect Health Usuń usługę](./media/how-to-connect-health-operations/DeleteServer.png)
3. Potwierdź, wpisując nazwę usługi w polu potwierdzenia (na przykład: sts.contoso.com).
4. Kliknij polecenie **Usuń**.
   <br><br>

[//]: # (Początek sekcji RBAC)
## <a name="manage-access-with-role-based-access-control"></a>Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach
[Kontrola dostępu oparta na rolach (RBAC)](../../role-based-access-control/role-assignments-portal.md) dla usługi Azure AD Connect Health udostępnia użytkownikom i grupom inne niż administratorzy globalni. RBAC przypisuje role dla uprawnionych użytkowników i grup oraz udostępnia mechanizm do ograniczania Administratorzy globalni w katalogu.

### <a name="roles"></a>Role
Program Azure AD Connect Health obsługuje następujące wbudowane role:

| Rola | Uprawnienia |
| --- | --- |
| Właściciel |Właściciele mogą *zarządzanie dostępem do* (na przykład przypisać rolę użytkownikowi lub grupie), *wyświetlać wszystkie informacje dotyczące* (na przykład wyświetlić alerty) z portalu i *zmiany ustawień* (dla przykład powiadomienia e-mail) w ramach usługi Azure AD Connect Health. <br>Domyślnie administratorzy globalni usługi Azure AD są przypisani do tej roli, a nie można jej zmienić. |
| Współautor |Współautorzy mogą *wyświetlać wszystkie informacje dotyczące* (na przykład wyświetlić alerty) z portalu i *zmiany ustawień* (na przykład wiadomości e-mail z powiadomieniami) w ramach usługi Azure AD Connect Health. |
| Czytelnik |Czytelnicy mogą *wyświetlać wszystkie informacje dotyczące* (na przykład wyświetlić alerty) z portalu w ramach usługi Azure AD Connect Health. |

Wszystkie role (na przykład Administratorzy dostępu użytkownika lub usługa DevTest Labs użytkowników) nie mają wpływu dostęp w usłudze Azure AD Connect Health, nawet jeśli role są dostępne w interfejsie portalu.

### <a name="access-scope"></a>Zakres dostępu
Usługa Azure AD Connect Health obsługuje zarządzanie dostępem na dwóch poziomach:

* **Wszystkie wystąpienia usługi**: Jest to zalecane ścieżka, w większości przypadków. Kontroluje dostęp do wszystkich wystąpień usługi (na przykład farmę usług AD FS) w przypadku wszystkich typów ról, które są monitorowane przez program Azure AD Connect Health.
* **Wystąpienie usługi**: W niektórych przypadkach może być konieczne oddzielenie czynności związanych z dostępem opartym na typy ról lub przez wystąpienie usługi. W takim przypadku możesz zarządzać dostępem na poziomie wystąpienia usługi.  

Uprawnienie jest przydzielane, jeśli użytkownik końcowy ma dostęp w katalogu lub usługa wystąpienia w poziomie.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Zezwalaj użytkownikom lub grupom dostęp do usługi Azure AD Connect Health
Poniższe kroki pokazują jak zezwolić na dostęp.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Krok 1: Wybierz zakres dostępu
Aby zezwolić na dostęp użytkowników, u *wszystkich wystąpień usługi* poziomu w ramach usługi Azure AD Connect Health, otwórz głównego bloku w usłudze Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Krok 2: Dodawanie użytkowników i grup i przypisywania ról
1. Z **Konfiguruj** kliknij **użytkowników**.<br>
   ![Zrzut ekranu programu Azure AD Connect Health zasobów z paska bocznego](./media/how-to-connect-health-operations/startRBAC.png)
2. Wybierz pozycję **Dodaj**.
3. W **wybierz rolę** okienku, wybierz rolę (na przykład **właściciela**).<br>
   ![Zrzut ekranu programu Azure AD Connect Health RBAC użytkowników okna](./media/how-to-connect-health-operations/RBAC_add.png)
4. Wpisz nazwę lub identyfikator użytkownika docelowego lub grupy. W tym samym czasie, możesz wybrać jeden lub więcej użytkowników lub grup. Kliknij pozycję **Wybierz**.
   ![Zrzut ekranu programu Azure AD Connect Health RBAC użytkowników okna](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Kliknij przycisk **OK**.<br>
6. Po zakończeniu przypisanie roli użytkowników i grup są wyświetlane na liście.<br>
   ![Zrzut ekranu programu Azure AD Connect Health RBAC użytkowników okna z nowym użytkownikom wyróżniony](./media/how-to-connect-health-operations/RBAC_user_list.png)

Teraz wyświetlani użytkownicy i grupy mają dostęp, zgodnie z ich przypisane role.

> [!NOTE]
> * Administratorzy globalni zawsze mają pełny dostęp do wszystkich operacji, ale konta administratora globalnego nie są obecne na powyższej liście.
> * Funkcja zaprosić użytkowników nie jest obsługiwana w ramach usługi Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Krok 3: Udostępnianie lokalizacji bloku użytkownicy lub grupy
1. Przypisanie uprawnień, użytkownik może uzyskać dostęp usługi Azure AD Connect Health, przechodząc [tutaj](https://aka.ms/aadconnecthealth).
2. W bloku użytkownika można przypiąć bloku lub różnych części go do pulpitu nawigacyjnego. Po prostu kliknij **Przypnij do pulpitu nawigacyjnego** ikony.<br>
   ![Zrzut ekranu programu Azure AD Connect Health RBAC Przypnij blok z wyróżnioną ikoną przypinania](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Użytkownik mający rolę Czytelnik przypisane nie jest w stanie pobrać rozszerzenia usługi Azure AD Connect Health w witrynie Azure Marketplace. Użytkownik nie może wykonać niezbędne działania, aby to zrobić "Utwórz". Użytkownik nadal korzystać do bloku, przechodząc do pod powyższym linkiem. Kolejne użycie użytkownika można przypiąć blok do pulpitu nawigacyjnego.
>
>

### <a name="remove-users-or-groups"></a>Usuń użytkowników lub grupy
Użytkownik lub grupa dodana do usługi Azure AD Connect Health RBAC można usunąć. Po prostu kliknij prawym przyciskiem myszy użytkownika lub grupy, a wybierz **Usuń**.<br>
![Zrzut ekranu programu Azure AD Connect Health RBAC użytkowników okna z Usuń wyróżniony](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Koniec sekcji RBAC)

## <a name="next-steps"></a>Kolejne kroki
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalowanie agenta programu Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Używanie programu Azure AD Connect Health z usługami AD FS](how-to-connect-health-adfs.md)
* [Używanie programu Azure AD Connect Health w celu synchronizacji](how-to-connect-health-sync.md)
* [Używanie programu Azure AD Connect Health z usługami AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health — często zadawane pytania](reference-connect-health-faq.md)
* [Historia wersji programu Azure AD Connect Health](reference-connect-health-version-history.md)
