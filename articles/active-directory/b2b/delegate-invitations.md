---
title: Włącz ustawienia zewnętrznej współpracy B2B — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć zewnętrznej współpracy B2B usługi Active Directory i zarządzanie nimi, kto może zapraszać użytkowników-gości. Rola osoba zapraszająca gości umożliwia delegowanie zaproszeń.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11dda7fc3760f468c094fb4cf4484a27895f83b9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812667"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Włącz zewnętrznego współpracy B2B i Zarządzaj, kto może zapraszać gości

W tym artykule opisano sposób włączania współpracy B2B usługi Azure Active Directory (Azure AD) i określić, kto może zapraszać gości. Domyślnie wszyscy użytkownicy i Goście w Twoim katalogu mogą zapraszać gości, nawet jeśli nie masz przypisaną rolę administratora. Ustawienia zewnętrznej współpracy umożliwiają włączenie zaproszenia gościa lub wyłączyć dla różnych typów użytkowników w Twojej organizacji. Możesz również delegować zaproszenia do poszczególnych użytkowników przez przypisywanie ról, które zezwalają na zaproszenie gości.

## <a name="configure-b2b-external-collaboration-settings"></a>Skonfiguruj ustawienia zewnętrznej współpracy B2B

Przy użyciu funkcji współpracy B2B usługi Azure AD administrator dzierżawy można ustawić następujące zasady zaproszenia:

- Wyłącz zaproszenia
- Tylko administratorzy i użytkownicy o roli zapraszającego gości mogą zapraszać
- Administratorzy, rola osoba zapraszająca gości i członkowie mogą zapraszać
- Wszyscy użytkownicy, w tym gości, mogą zapraszać

Domyślnie wszyscy użytkownicy, w tym gości, można zaprosić użytkowników-gości.

### <a name="to-configure-external-collaboration-settings"></a>Aby skonfigurować ustawienia zewnętrznej współpracy:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator dzierżawy.
2. Wybierz **usługi Azure Active Directory** > **użytkowników** > **ustawienia użytkownika**.
3. W obszarze **użytkowników zewnętrznych**, wybierz opcję **Zarządzaj ustawieniami współpracy zewnętrznej**.
   > [!NOTE]
   > **Ustawieniami współpracy zewnętrznej** są także dostępne z **relacje w organizacji** strony. W usłudze Azure Active Directory w obszarze **Zarządzaj**, przejdź do **relacje w organizacji** > **ustawienia**.
4. Na **ustawieniami współpracy zewnętrznej** wybierz zasady, aby włączyć.

   ![Ustawienia zewnętrznej współpracy](./media/delegate-invitations/control-who-to-invite.png)

  - **Uprawnienia użytkowników-gości są ograniczone**: Ta zasada określa uprawnienia dla gości w Twoim katalogu. Wybierz **tak** goście blok z określonych zadań w katalogu, takich jak wyliczanie użytkowników, grup lub innych zasobów katalogu. Wybierz **nie** zapewnienie gości takie same prawa dostępu do danych katalogu jako zwykli użytkownicy w katalogu.
   - **Administratorzy i użytkownicy o roli zapraszającego gości mogą zapraszać**: Aby umożliwić Administratorzy i użytkownicy w roli "Osoba zapraszająca gości", aby zapraszać gości, należy ustawić te zasady na **tak**.
   - **Członkowie mogą zapraszać**: Aby umożliwić członkom bez uprawnień administratora katalogu zapraszać gości, należy ustawić te zasady na **tak**.
   - **Goście mogą zapraszać gości**: Aby umożliwić gości zapraszać innych gości, należy ustawić te zasady na **tak**.
   - **Włącz kod dostępu jednorazowe poczty E-mail dla gości (wersja zapoznawcza)** : Aby uzyskać więcej informacji na temat funkcji jednorazowy kod dostępu, zobacz [uwierzytelniania jednorazowy kod dostępu poczty E-mail (wersja zapoznawcza)](one-time-passcode.md).
   - **Ograniczenia współpracy**: Aby uzyskać więcej informacji na temat zezwalanie lub blokowanie zaproszeń do określonych domen, zobacz [dozwolonych lub zablokowanych zaproszeń do użytkowników B2B z określonym organizacjom](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Przypisywanie użytkownikowi roli osoba zapraszająca gości

Rola osoba zapraszająca gościa można udostępnić użytkownikom indywidualne możliwość zapraszać gości bez przypisywania do nich administratora globalnego ani innych ról administratora. Przypisywanie roli zapraszającego gości do osób. Następnie upewnij się, możesz ustawić **Administratorzy i użytkownicy o roli zapraszającego gości mogą zapraszać** do **tak**.

Oto przykład, w którym pokazano, jak dodać użytkownika do roli osoby zapraszającej gości za pomocą programu PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące artykuły:

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Dodawanie użytkowników-gości współpracy B2B bez zaproszenia](add-user-without-invite.md)
- [Dodawanie użytkownika współpracy B2B do roli](add-guest-to-role.md)


