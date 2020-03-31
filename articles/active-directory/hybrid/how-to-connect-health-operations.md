---
title: Operacje kondycji połączenia usługi Azure Active Directory
description: W tym artykule opisano dodatkowe operacje, które można wykonać po wdrożeniu usługi Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef908429d359020282920d73480a472bfde0aa60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261517"
---
# <a name="azure-active-directory-connect-health-operations"></a>Operacje kondycji połączenia usługi Azure Active Directory
W tym temacie opisano różne operacje, które można wykonać przy użyciu usługi Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Włączanie powiadomień e-mail
Usługę Azure AD Connect Health można skonfigurować do wysyłania powiadomień e-mail, gdy alerty wskazują, że infrastruktura tożsamości nie jest w dobrej kondycji. Dzieje się tak, gdy alert jest generowany i gdy zostanie rozwiązany.

![Zrzut ekranu przedstawiający ustawienia powiadomień e-mail usługi Azure AD Connect Health](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> Powiadomienia e-mail są domyślnie włączone.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Aby włączyć powiadomienia e-mail usługi Azure AD Connect Health
1. Otwórz **alerty** bloku dla usługi, dla której chcesz otrzymywać powiadomienia e-mail.
2. Na pasku akcji kliknij pozycję **Ustawienia powiadomień**.
3. Na przełączniku powiadomień e-mail wybierz opcję **Wł.**
4. Zaznacz to pole wyboru, jeśli chcesz, aby wszyscy administratorzy globalni otrzymywali powiadomienia e-mail.
5. Jeśli chcesz otrzymywać powiadomienia e-mail na inne adresy e-mail, określ je w polu **Dodatkowe adresaci wiadomości e-mail.** Aby usunąć adres e-mail z tej listy, kliknij prawym przyciskiem myszy wpis i wybierz polecenie **Usuń**.
6. Aby sfinalizować zmiany, kliknij przycisk **Zapisz**. Zmiany zajmą się dopiero po zapisaniu.

>[!NOTE] 
> W przypadku problemów z przetwarzaniem żądań synchronizacji w naszej usłudze wewnętrznej bazy danych ta usługa wysyła wiadomość e-mail z powiadomieniem ze szczegółami błędu na adres e-mail(-ów) kontaktu administracyjnego dzierżawcy. Usłyszeliśmy opinie klientów, że w niektórych przypadkach ilość tych wiadomości jest zbyt duża, więc zmieniamy sposób wysyłania tych wiadomości. 
>
> Zamiast wysyłać wiadomość dla każdego błędu synchronizacji za każdym razem, gdy występuje, wyślemy codzienne podsumowanie wszystkich błędów, które zwróciła usługa wewnętrznej bazy danych. Dzięki temu klienci mogą przetwarzać te błędy w bardziej efektywny sposób i zmniejsza liczbę zduplikowanych komunikatów o błędach.
>
> Planujemy, że ta zmiana zostanie wdrożona 15 stycznia 2020 roku.

## <a name="delete-a-server-or-service-instance"></a>Usuwanie wystąpienia serwera lub usługi

>[!NOTE] 
> Licencja premium usługi Azure AD jest wymagana do wykonania kroków usuwania.

W niektórych przypadkach można usunąć serwer z monitorowanego. Oto, co musisz wiedzieć, aby usunąć serwer z usługi Azure AD Connect Health.

Podczas usuwania serwera należy pamiętać o następujących czynnościach:

* Ta akcja zatrzymuje zbieranie dalszych danych z tego serwera. Ten serwer jest usuwany z usługi monitorowania. Po tej akcji nie można wyświetlić nowych alertów, monitorowania lub danych analizy użycia dla tego serwera.
* Ta akcja nie powoduje odinstalowania agenta kondycji z serwera. Jeśli agent kondycji nie został odinstalowany przed wykonaniem tego kroku, mogą pojawić się błędy związane z agentem kondycji na serwerze.
* Ta akcja nie powoduje usunięcia danych już zebranych z tego serwera. Te dane są usuwane zgodnie z zasadami przechowywania danych platformy Azure.
* Po wykonaniu tej akcji, jeśli chcesz ponownie rozpocząć monitorowanie tego samego serwera, należy odinstalować i ponownie zainstalować agenta kondycji na tym serwerze.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Usuwanie serwera z usługi Azure AD Connect Health

>[!NOTE] 
> Licencja premium usługi Azure AD jest wymagana do wykonania kroków usuwania.

Kondycja usługi Azure AD Connect dla usług federacyjnych Active Directory (AD FS) i usługi Azure AD Connect (Sync):

1. Otwórz blok **serwera** z bloku **Lista serwerów,** wybierając nazwę serwera do usunięcia.
2. Na bloku **Serwer** na pasku akcji kliknij pozycję **Usuń**.
![Zrzut ekranu przedstawiający serwer usuwania usługi Azure AD Connect Health](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Potwierdź, wpisując nazwę serwera w polu potwierdzenia.
4. Kliknij **pozycję Usuń**.

Usługa Azure AD Connect Health dla usług domenowych Usługi domenowe Active Directory platformy Azure:

1. Otwórz pulpit nawigacyjny **kontrolerów domeny.**
2. Wybierz kontroler domeny do usunięcia.
3. Na pasku akcji kliknij pozycję **Usuń zaznaczone**.
4. Potwierdź akcję usunięcia serwera.
5. Kliknij **pozycję Usuń**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Usuwanie wystąpienia usługi z usługi Azure AD Connect Health
W niektórych przypadkach można usunąć wystąpienie usługi. Oto, co musisz wiedzieć, aby usunąć wystąpienie usługi z usługi Azure AD Connect Health.

Podczas usuwania wystąpienia usługi należy pamiętać o następujących czynnościach:

* Ta akcja usuwa bieżące wystąpienie usługi z usługi monitorowania.
* Ta akcja nie odinstalowuje ani nie usuwa agenta kondycji z żadnego z serwerów, które były monitorowane w ramach tego wystąpienia usługi. Jeśli agent kondycji nie został odinstalowany przed wykonaniem tego kroku, na serwerach mogą pojawić się błędy związane z agentem kondycji.
* Wszystkie dane z tego wystąpienia usługi są usuwane zgodnie z zasadami przechowywania danych platformy Azure.
* Po wykonaniu tej akcji, jeśli chcesz rozpocząć monitorowanie usługi, odinstaluj i ponownie zainstaluj agenta kondycji na wszystkich serwerach. Po wykonaniu tej akcji, jeśli chcesz ponownie rozpocząć monitorowanie tego samego serwera, odinstaluj, zainstaluj ponownie i zarejestruj agenta kondycji na tym serwerze.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Aby usunąć wystąpienie usługi z usługi Azure AD Connect Health
1. Otwórz **bloku usługi** z **listy usług** bloku, wybierając identyfikator usługi (nazwa farmy), który chcesz usunąć. 
2. Na bloku **Usługa** na pasku akcji kliknij pozycję **Usuń**. 
![Zrzut ekranu przedstawiający usługę Azure AD Connect Health delete service](./media/how-to-connect-health-operations/DeleteServer.png)
3. Potwierdź, wpisując nazwę usługi w polu potwierdzenia (na przykład: sts.contoso.com).
4. Kliknij **pozycję Usuń**.
   <br><br>

[//]: # (Początek sekcji RBAC)
## <a name="manage-access-with-role-based-access-control"></a>Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach
[Kontrola dostępu oparta na rolach (RBAC)](../../role-based-access-control/role-assignments-portal.md) dla usługi Azure AD Connect Health zapewnia dostęp do użytkowników i grup innych niż administratorzy globalni. RBAC przypisuje role do zamierzonych użytkowników i grup i zapewnia mechanizm ograniczania administratorów globalnych w katalogu.

### <a name="roles"></a>Role
Usługa Azure AD Connect Health obsługuje następujące wbudowane role:

| Rola | Uprawnienia |
| --- | --- |
| Właściciel |Właściciele mogą *zarządzać dostępem* (na przykład przypisywać rolę do użytkownika lub grupy), *wyświetlać wszystkie informacje* (na przykład wyświetlać alerty) z portalu i zmieniać *ustawienia* (na przykład powiadomienia e-mail) w usłudze Azure AD Connect Health. <br>Domyślnie administratorzy globalni usługi Azure AD są przypisywani tej roli i nie można tego zmienić. |
| Współautor |Współautorzy mogą *wyświetlać wszystkie informacje* (na przykład wyświetlać alerty) z portalu i *zmieniać ustawienia* (na przykład powiadomienia e-mail) w usłudze Azure AD Connect Health. |
| Czytelnik |Czytelnicy mogą *wyświetlać wszystkie informacje* (na przykład wyświetlać alerty) z portalu w usłudze Azure AD Connect Health. |

Wszystkie inne role (takie jak Administratorzy dostępu użytkowników lub Użytkownicy laboratoriów DevTest) nie mają wpływu na dostęp w ramach usługi Azure AD Connect Health, nawet jeśli role są dostępne w środowisku portalu.

### <a name="access-scope"></a>Zakres dostępu
Usługa Azure AD Connect Health obsługuje zarządzanie dostępem na dwóch poziomach:

* **Wszystkie wystąpienia usługi:** Jest to zalecana ścieżka w większości przypadków. Kontroluje dostęp dla wszystkich wystąpień usługi (na przykład farmy usług AD FS) we wszystkich typach ról, które są monitorowane przez usługę Azure AD Connect Health.
* **Wystąpienie usługi:** W niektórych przypadkach może być konieczne segregować dostęp na podstawie typów ról lub wystąpienia usługi. W takim przypadku można zarządzać dostępem na poziomie wystąpienia usługi.  

Uprawnienie jest przyznawane, jeśli użytkownik końcowy ma dostęp na poziomie wystąpienia katalogu lub usługi.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Zezwalaj użytkownikom lub grupom na dostęp do usługi Azure AD Connect Health
Poniższe kroki pokazują, jak zezwolić na dostęp.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Krok 1: Wybierz odpowiedni zakres dostępu
Aby zezwolić użytkownikowi na dostęp na poziomie *wszystkich wystąpień usługi* w usłudze Azure AD Connect Health, otwórz główny obiekt w usłudze Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Krok 2: Dodawanie użytkowników i grup oraz przypisywanie ról
1. W sekcji **Konfigurowanie** kliknij pozycję **Użytkownicy**.<br>
   ![Zrzut ekranu przedstawiający pasek boczny zasobów usługi Azure AD Connect](./media/how-to-connect-health-operations/startRBAC.png)
2. Wybierz pozycję **Dodaj**.
3. W **okienku Wybierz rolę** wybierz rolę (na przykład **Właściciel**).<br>
   ![Zrzut ekranu przedstawiający okno Użytkownicy RBAC usługi Azure AD Connect](./media/how-to-connect-health-operations/RBAC_add.png)
4. Wpisz nazwę lub identyfikator docelowego użytkownika lub grupy. Można wybrać jednego lub więcej użytkowników lub grup w tym samym czasie. Kliknij **pozycję Wybierz**.
   ![Zrzut ekranu przedstawiający okno Użytkownicy RBAC usługi Azure AD Connect](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Kliknij przycisk **OK**.<br>
6. Po zakończeniu przypisania roli użytkownicy i grupy pojawią się na liście.<br>
   ![Zrzut ekranu przedstawiający okno Użytkownicy RBAC usługi Azure AD Connect Health z wyróżnionymi nowymi użytkownikami](./media/how-to-connect-health-operations/RBAC_user_list.png)

Teraz wymienieni użytkownicy i grupy mają dostęp, zgodnie z przypisanymi im rolami.

> [!NOTE]
> * Administratorzy globalni zawsze mają pełny dostęp do wszystkich operacji, ale konta administratora globalnego nie znajdują się na poprzedniej liście.
> * Funkcja Zaproś użytkowników nie jest obsługiwana w usłudze Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Krok 3: Udostępnianie lokalizacji bloku użytkownikom lub grupom
1. Po przypisaniu uprawnień użytkownik może uzyskać dostęp do usługi Azure AD Connect Health, przechodząc [tutaj](https://aka.ms/aadconnecthealth).
2. Na bloku użytkownik może przypiąć ostrze lub różne jego części do pulpitu nawigacyjnego. Wystarczy kliknąć ikonę **Przypinaj do pulpitu nawigacyjnego.**<br>
   ![Zrzut ekranu przedstawiający ostrzkę rbac usługi Azure AD Connect Health z wyróżnioną ikoną pinezki](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Użytkownik z przypisaną rolą czytnika nie może uzyskać rozszerzenia usługi Azure AD Connect Health z portalu Azure Marketplace. Użytkownik nie może wykonać niezbędnej operacji "create", aby to zrobić. Użytkownik nadal może uzyskać dostęp do bloku, przechodząc do poprzedniego łącza. W celu późniejszego użycia użytkownik może przypiąć blok do pulpitu nawigacyjnego.
>
>

### <a name="remove-users-or-groups"></a>Usuwanie użytkowników lub grup
Można usunąć użytkownika lub grupę dodaną do usługi Azure AD Connect Health RBAC. Wystarczy kliknąć prawym przyciskiem myszy użytkownika lub grupę, a następnie wybrać polecenie **Usuń**.<br>
![Zrzut ekranu przedstawiający okno Użytkownicy RBAC usługi Azure AD Connect Health z wyróżnioną połącz](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Koniec sekcji RBAC)

## <a name="next-steps"></a>Następne kroki
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalacja agenta kondycji usługi Azure AD Connect](how-to-connect-health-agent-install.md)
* [Używanie programu Azure AD Connect Health z usługami AD FS](how-to-connect-health-adfs.md)
* [Używanie usługi Azure AD Connect Health do synchronizacji](how-to-connect-health-sync.md)
* [Używanie programu Azure AD Connect Health z usługami AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health — często zadawane pytania](reference-connect-health-faq.md)
* [Historia wersji programu Azure AD Connect Health](reference-connect-health-version-history.md)
