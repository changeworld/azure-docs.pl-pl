---
title: Włącz ustawienia współpracy zewnętrznej B2B — usługa Azure AD
description: Dowiedz się, jak włączyć współpracę zewnętrzną usługi Active Directory B2B i zarządzać tym, kto może zapraszać użytkowników-gości. Za pomocą roli Zaproszony gość można delegować zaproszenia.
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
ms.openlocfilehash: 7fb10863334392b207c7cfd2172dc9260cf15e2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272904"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Włącz współpracę zewnętrzną B2B i zarządzaj, kto może zapraszać gości

W tym artykule opisano sposób włączania współpracy B2B usługi Azure Active Directory (Azure AD) i określania, kto może zapraszać gości. Domyślnie wszyscy użytkownicy i goście w katalogu mogą zapraszać gości, nawet jeśli nie są przypisani do roli administratora. Ustawienia współpracy zewnętrznej umożliwiają włączanie lub wyłączanie zaproszeń gości dla różnych typów użytkowników w organizacji. Zaproszenia można również delegować poszczególnym użytkownikom, przypisując role, które umożliwiają im zapraszanie gości.

## <a name="configure-b2b-external-collaboration-settings"></a>Konfigurowanie ustawień współpracy zewnętrznej B2B

Dzięki współpracy usługi Azure AD B2B administrator dzierżawy może ustawić następujące zasady zaproszeń:

- Wyłączanie zaproszeń
- Tylko administratorzy i użytkownicy w roli Zaproszony mogą zapraszać
- Administratorzy, rola Zaproszony gość i członkowie mogą zapraszać
- Wszyscy użytkownicy, w tym goście, mogą

Domyślnie wszyscy użytkownicy, w tym goście, mogą zapraszać użytkowników-gości.

### <a name="to-configure-external-collaboration-settings"></a>Aby skonfigurować ustawienia współpracy zewnętrznej:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator dzierżawy.
2. Wybierz**ustawienia użytkownika****użytkowników** >  **usługi Azure Active Directory** > .
3. W obszarze **Użytkownicy zewnętrzni**wybierz pozycję **Zarządzaj ustawieniami współpracy zewnętrznej**.
   > [!NOTE]
   > **Ustawienia współpracy zewnętrznej** są również dostępne na stronie Relacje **organizacyjne.** W usłudze Azure Active Directory w obszarze **Zarządzanie**przejdź do**ustawień** **relacji** > organizacji .
4. Na stronie **Ustawienia współpracy zewnętrznej** wybierz zasady, które chcesz włączyć.

   ![Ustawienia zewnętrznej współpracy](./media/delegate-invitations/control-who-to-invite.png)

  - **Uprawnienia użytkowników-gości są ograniczone:** Ta zasada określa uprawnienia gości w katalogu. Wybierz **pozycję Tak,** aby zablokować gościom określone zadania katalogu, takie jak wyliczanie użytkowników, grup lub innych zasobów katalogu. Wybierz **opcję Nie,** aby dać gościom taki sam dostęp do danych katalogu, jak zwykli użytkownicy w katalogu.
   - **Administratorzy i użytkownicy w roli osoby zapraszają gościa mogą zapraszać:** Aby umożliwić administratorom i użytkownikom w roli "Zaproszony gość" zapraszanie gości, ustaw tę zasadę na **Tak**.
   - **Członkowie mogą zapraszać:** Aby zezwolić członkom katalogu niebędącym administratorami na zapraszanie gości, ustaw tę zasadę na **Tak**.
   - **Goście mogą zaprosić**: Aby umożliwić gościom zaproszenie innych Gości, ustaw tę zasadę na **Tak.**
   - **Włącz jednorazowy kod dostępu dla gości (Wersja zapoznawcza) :** Aby uzyskać więcej informacji na temat funkcji jednorazowego kodu, zobacz [Uwierzytelnianie jednorazowym kodem dostępu (wersja zapoznawcza)](one-time-passcode.md)
   - **Ograniczenia współpracy:** Aby uzyskać więcej informacji na temat zezwalania lub blokowania zaproszeń do określonych domen, zobacz [Zezwalanie lub blokowanie zaproszeń do użytkowników B2B z określonych organizacji](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Przypisywanie roli zapraszania gościa do użytkownika

Dzięki roli Zaproszony gość możesz dać poszczególnym użytkownikom możliwość zapraszania gości bez przypisywania im administratora globalnego lub innej roli administratora. Przypisz rolę osoby zaproszone przez gościa. Następnie upewnij się, że **ustawisz Administratorzy i użytkownicy w roli osoby zapraszają gościa mogą zapraszać** do **Tak**.

Oto przykład, który pokazuje, jak dodać użytkownika do roli osoby zapraszanej za pomocą programu PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły dotyczące współpracy usługi Azure AD B2B:

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Dodawanie użytkowników-gości współpracujących b2b bez zaproszenia](add-user-without-invite.md)
- [Dodawanie użytkownika współpracy B2B do roli](add-guest-to-role.md)


