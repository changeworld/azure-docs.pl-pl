---
title: 'Azure Active Directory Domain Services: Zasady dotyczące haseł | Microsoft Docs'
description: Zrozumienie zasad haseł w domenach zarządzanych
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 30f4558339bbfddd2296cd1cb918c6ef8999b67e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879191"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Zasady blokowania haseł i kont w domenach zarządzanych
W tym artykule opisano domyślne zasady dotyczące haseł w domenie zarządzanej. Opisano w nim również, jak można skonfigurować te zasady.

## <a name="fine-grained-password-policies-fgpp"></a>Szczegółowe zasady haseł (szczegółowych zasad haseł)
Aby określić wiele zasad haseł w ramach jednej domeny, należy użyć szczegółowych zasad haseł. SZCZEGÓŁOWYCH zasad haseł pozwala zastosować różne ograniczenia dotyczące zasad blokowania haseł i kont dla różnych zestawów użytkowników w domenie. Można na przykład zastosować rygorystyczne ustawienia haseł do kont uprzywilejowanych.

Następujące ustawienia hasła można skonfigurować przy użyciu szczegółowych zasad haseł:
* Minimalna długość hasła
* Historia haseł
* Hasła muszą spełniać wymagania dotyczące złożoności
* Minimalny wiek hasła
* Maksymalny wiek hasła
* Zasady blokady konta
    * Czas trwania blokady konta
    * Liczba dozwolonych nieudanych prób logowania
    * Resetowanie liczby nieudanych prób logowania po
    
SZCZEGÓŁOWYCH zasad haseł ma wpływ tylko na użytkowników utworzonych bezpośrednio w usłudze Azure AD DS. Ustawienia złożoności haseł mają wpływ na użytkowników chmury i użytkowników domeny zsynchronizowanych z domeną zarządzaną AD DS platformy Azure z usługi Azure AD. SZCZEGÓŁOWYCH zasad haseł jest dystrybuowany za pomocą skojarzenia grup w domenie zarządzanej platformy Azure AD DS i wszelkie wprowadzone zmiany są stosowane podczas następnego logowania użytkownika. Zmiana zasad nie powoduje odblokowania konta użytkownika, które jest już zablokowane.

## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>Domyślne szczegółowe ustawienia zasad haseł w domenie zarządzanej
Poniższy zrzut ekranu ilustruje domyślne szczegółowe zasady haseł skonfigurowane w Azure AD Domain Services domenie zarządzanej.

![Domyślne szczegółowe zasady haseł](./media/how-to/default-fgpp.png)

> [!NOTE]
> Nie można modyfikować ani usuwać domyślnych zasad szczegółowych haseł. Członkowie grupy "Administratorzy usługi AAD DC" mogą tworzyć niestandardowe szczegółowych zasad haseł i konfigurować ją w celu przesłonięcia (mają pierwszeństwo przed) domyślną wbudowaną szczegółowych zasad haseł.
>
>

## <a name="password-policy-settings"></a>Ustawienia zasad haseł
W domenie zarządzanej domyślnie są konfigurowane następujące zasady dotyczące haseł:
* Minimalna długość hasła (w znakach): 7
* Maksymalny wiek hasła (okres istnienia): 90 dni
* Hasła muszą spełniać wymagania dotyczące złożoności

### <a name="account-lockout-settings"></a>Ustawienia blokady konta
W domenie zarządzanej następujące zasady blokady konta są konfigurowane domyślnie:
* Czas trwania blokady konta: 30
* Liczba dozwolonych nieudanych prób logowania: 5
* Resetowanie nieudanych prób logowania po: 30 minut

W praktyce konta użytkowników są blokowane przez 30 minut, jeśli pięć nieprawidłowych haseł zostanie użytych w ciągu 2 minut. Konta są automatycznie odblokowywane po 30 minutach.


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>Tworzenie niestandardowych zasad haseł szczegółowych (szczegółowych zasad haseł) w domenie zarządzanej
Można utworzyć niestandardowe szczegółowych zasad haseł i zastosować je do określonych grup w domenie zarządzanej. Ta konfiguracja skutecznie zastępuje domyślny szczegółowych zasad haseł skonfigurowany dla domeny zarządzanej.

> [!TIP]
> Tylko członkowie grupy **"Administratorzy usługi AAD DC"** mają uprawnienia do tworzenia niestandardowych zasad haseł.
>
>

Ponadto można utworzyć niestandardowe, szczegółowe zasady haseł i zastosować je do wszystkich niestandardowych jednostek organizacyjnych tworzonych w domenie zarządzanej.

Niestandardową szczegółowych zasad haseł można skonfigurować z następujących powodów:
* Aby ustawić inne zasady blokady konta.
* Aby skonfigurować domyślne ustawienie okresu istnienia hasła dla domeny zarządzanej.

Aby utworzyć niestandardową szczegółowych zasad haseł w domenie zarządzanej:
1. Zaloguj się do maszyny wirtualnej z systemem Windows, która jest używana do administrowania domeną zarządzaną (musi być co najmniej Windows Server 2012 R2). Jeśli go nie masz, postępuj zgodnie z instrukcjami, aby [zarządzać domeną Azure AD Domain Services](manage-domain.md).
2. Uruchom **Centrum administracyjne usługi Active Directory** na maszynie wirtualnej.
3. Kliknij nazwę domeny (na przykład "contoso100.com").
4. Kliknij dwukrotnie pozycję **system** , aby otworzyć kontener system.
5. Kliknij dwukrotnie **Kontener ustawień haseł**.
6. Zobaczysz domyślną wbudowaną szczegółowych zasad haseł dla domeny zarządzanej o nazwie **AADDSSTFPSO**. Nie można zmodyfikować tego wbudowanego szczegółowych zasad haseł. Można jednak utworzyć nowe niestandardowe szczegółowych zasad haseł Zastąp domyślną szczegółowych zasad haseł.
7. W panelu **zadania** po prawej stronie kliknij pozycję **Nowy** , a następnie kliknij pozycję **ustawienia hasła**.
8. W oknie dialogowym **Tworzenie ustawień haseł** określ niestandardowe ustawienia hasła, które mają zostać zastosowane jako część niestandardowego szczegółowych zasad haseł. Pamiętaj, aby ustawić pierwszeństwo odpowiednio do przesłania domyślnego szczegółowych zasad haseł.

   ![Utwórz niestandardowy szczegółowych zasad haseł](./media/how-to/custom-fgpp.png)

   > [!TIP]
   > **Pamiętaj, aby usunąć zaznaczenie opcji Chroń przed przypadkowym usunięciem.** W przypadku wybrania tej opcji nie można zapisać szczegółowych zasad haseł.
   >
   >

9. W programie **bezpośrednio stosuje się do**programu, kliknij przycisk **Dodaj** . W oknie dialogowym **Wybieranie użytkowników lub grup** kliknij przycisk **lokalizacje** .

   ![Wybieranie użytkowników i grup](./media/how-to/fgpp-applies-to.png)

10. W oknie dialogowym **lokalizacje** rozwiń nazwę domeny, a następnie kliknij pozycję **AADDC Users (Użytkownicy**). Teraz możesz wybrać grupę z wbudowanej jednostki organizacyjnej użytkownicy, do której ma zostać zastosowany szczegółowych zasad haseł.

    ![Wybierz jednostkę organizacyjną, do której należy Grupa](./media/how-to/fgpp-container.png)

11. Wpisz nazwę grupy, a następnie kliknij przycisk **Sprawdź nazwy** , aby sprawdzić, czy grupa istnieje.

    ![Wybierz grupę do zastosowania szczegółowych zasad haseł](./media/how-to/fgpp-apply-group.png)

12. Nazwa grupy jest wyświetlana **bezpośrednio** w sekcji. Kliknij przycisk **OK** , aby zapisać te zmiany.

    ![SZCZEGÓŁOWYCH zasad haseł zastosowany](./media/how-to/fgpp-applied.png)

> [!TIP]
> **Aby zastosować niestandardowe zasady haseł dla kont użytkowników w niestandardowej jednostce organizacyjnej:** Szczegółowe zasady haseł można stosować tylko do grup. Aby skonfigurować niestandardowe zasady haseł tylko dla użytkowników z niestandardowej jednostki organizacyjnej, Utwórz grupę zawierającą użytkowników w tej jednostce organizacyjnej.
>
>

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o Active Directory szczegółowych zasadach haseł](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurowanie szczegółowych zasad haseł przy użyciu Centrum administracyjnego usługi AD](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
