---
title: 'Azure Active Directory Domain Services: Zasady haseł | Dokumentacja firmy Microsoft'
description: Omówienie zasad haseł w domenach zarządzanych
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
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
ms.author: mstephen
ms.openlocfilehash: 71511fd83f9c00f768f5f7bedb3516fef8599e70
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246842"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Zasady blokowania haseł i kont, w domenach zarządzanych
W tym artykule opisano domyślne zasady haseł w domenie zarządzanej. Obejmuje ona również, jak można skonfigurować te zasady.

## <a name="fine-grained-password-policies-fgpp"></a>Dobrym rozwiązaniem szczegółowe zasady haseł (FGPP)
Użyj szczegółowych zasad haseł, aby określić wiele zasad haseł w pojedynczej domenie. Szczegółowych zasad HASEŁ umożliwia zastosowania różnych ograniczeń dla zasad blokowania haseł i kont do różnych zestawów użytkowników w domenie. Na przykład można zastosować ustawień strict hasła do kont uprzywilejowanych.

Można skonfigurować następujące ustawienia hasła, przy użyciu szczegółowych zasad HASEŁ:
* Minimalna długość hasła
* Historię haseł
* Hasło musi spełniać wymagania co do złożoności
* Minimalny okres ważności hasła
* Maksymalny okres ważności hasła
* Zasady blokady konta
    * Czas trwania blokady konta
    * Liczba nieudanych prób logowania dozwolona
    * Resetowanie nieudanych prób logowania licznik po


## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>Domyślne ustawienia zasady szczegółowe hasło w domenie zarządzanej
Poniższy zrzut ekranu przedstawia domyślne poprawnie zasady szczegółowe hasło skonfigurowane w domenie zarządzanej usług domenowych Azure AD.

![Domyślnie szczegółowe zasady haseł](./media/how-to/default-fgpp.png)

> [!NOTE]
> Nie można zmodyfikować lub usunąć domyślny wbudowanych szczegółowe zasady haseł. Członkowie grupy "Administratorzy usługi AAD DC" można tworzyć niestandardowe szczegółowych zasad HASEŁ i skonfiguruj ją, aby zastąpić (pierwszeństwo) domyślnie wbudowana szczegółowych zasad HASEŁ.
>
>

## <a name="password-policy-settings"></a>Ustawienia zasad haseł
W domenie zarządzanej domyślnie są konfigurowane następujące zasady haseł:
* Minimalna długość hasła (znaki): 7
* Maksymalny okres ważności hasła (podsumowanie): 90 dni
* Hasło musi spełniać wymagania co do złożoności

### <a name="account-lockout-settings"></a>Ustawienia blokady konta
W domenie zarządzanej domyślnie są konfigurowane następujące zasady blokady konta:
* Czas trwania blokady konta: 30
* Liczba nieudanych prób logowania dozwolona: 5
* Resetowanie nieudanych prób logowania licznik po: 30 minut

Skutecznie konta użytkowników są zablokowane przez 30 minut Jeśli pięć podania hasła są używane w ciągu 2 minut. Konta są automatycznie odblokowane po 30 minutach.


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>Tworzenie niestandardowych szczegółowe zasady haseł (FGPP) w domenie zarządzanej
Można tworzyć niestandardowe szczegółowych zasad HASEŁ i zastosować je do określonych grup w domenie zarządzanej. Ta konfiguracja skutecznie zastępuje to domyślne, szczegółowych zasad HASEŁ skonfigurowane dla domeny zarządzanej.

> [!TIP]
> Tylko członkowie **"Administratorzy usługi AAD DC"** grupa ma uprawnienia do tworzenia niestandardowych zasady szczegółowe hasło.
>
>

Ponadto również utworzyć zasady niestandardowe, dobrym rozwiązaniem, szczegółowe hasło i je zastosować do wszystkich niestandardowych jednostkach organizacyjnych, Utwórz w domenie zarządzanej.

Można skonfigurować niestandardowe zasady FGPP z następujących powodów:
* Aby ustawić zasady blokady innego konta.
* Aby skonfigurować domyślne ustawienie okresu istnienia hasła dla domeny zarządzanej.

Aby utworzyć niestandardowe szczegółowych zasad HASEŁ w domenie zarządzanej:
1. Zaloguj się do maszyny Wirtualnej Windows służy do administrowania domeny zarządzanej. Jeśli nie masz, postępuj zgodnie z instrukcjami, aby [Zarządzanie domeny usług domenowych Azure AD](manage-domain.md).
2. Uruchom **Centrum administracyjne usługi Active Directory** na maszynie Wirtualnej.
3. Kliknij nazwę domeny (na przykład "contoso100.com").
4. Kliknij dwukrotnie **systemu** otworzyć kontenera systemu.
5. Kliknij dwukrotnie **kontener ustawień haseł**.
6. Zostanie wyświetlona domyślna o nazwie wbudowanych szczegółowych zasad HASEŁ dla domeny zarządzanej **AADDSSTFPSO**. Nie można zmodyfikować tej wbudowanej szczegółowych zasad HASEŁ. Można jednak utworzyć nowe zastąpienie niestandardowe zasady FGPP domyślne szczegółowych zasad HASEŁ.
7. Na **zadania** panelu kliknij prawym przyciskiem myszy, **New** i kliknij przycisk **ustawienia hasła**.
8. W **Tworzenie ustawień haseł** okno dialogowe, określ ustawienia niestandardowe hasło, które mają być stosowane jako część niestandardowego szczegółowych zasad HASEŁ. Pamiętaj, aby odpowiednio ustawić priorytet, aby zastąpić domyślne szczegółowych zasad HASEŁ.

   ![Tworzenie niestandardowych zasad FGPP](./media/how-to/custom-fgpp.png)

   > [!TIP]
   > **Pamiętaj, aby usunąć zaznaczenie Chroń przed przypadkowym usunięciem opcji.** Jeśli ta opcja jest wybrana, nie można zapisać szczegółowych zasad HASEŁ.
   >
   >

9. W **bezpośrednio dotyczy**, kliknij przycisk **Dodaj** przycisku. W **Wybieranie użytkowników lub grup** okno dialogowe, kliknij przycisk **lokalizacje** przycisku.

   ![Wybierz użytkowników i grupy](./media/how-to/fgpp-applies-to.png)

10. W **lokalizacje** okna dialogowego, rozwiń węzeł z nazwą domeny, a następnie kliknij przycisk **użytkownicy usługi AAD DC**. Można teraz wybierz grupę użytkowników wbudowanej jednostki Organizacyjnej, do którego należy zastosować szczegółowych zasad HASEŁ.

    ![Wybierz jednostkę Organizacyjną tej grupy, należy do](./media/how-to/fgpp-container.png)

11. Wpisz nazwę grupy, a następnie kliknij przycisk **Sprawdź nazwy** istnieje przycisk, aby zweryfikować grupy.

    ![Wybierz grupy do zastosowania szczegółowych zasad HASEŁ](./media/how-to/fgpp-apply-group.png)

12. Nazwa grupy jest wyświetlana w **bezpośrednio dotyczy** sekcji. Kliknij przycisk **OK** przycisk, aby zapisać te zmiany.

    ![Zastosowane zasady FGPP](./media/how-to/fgpp-applied.png)

> [!TIP]
> **Aby zastosować zasady niestandardowe haseł dla kont użytkowników w jednostce Organizacyjnej, niestandardowe:** Zasady szczegółowe hasło w dobrym stanie można stosować tylko do grup. Aby skonfigurować zasady niestandardowe hasło tylko dla użytkowników z niestandardowej jednostki Organizacyjnej, utworzyć grupę, która zawiera użytkowników w tej jednostce Organizacyjnej.
>
>

## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się więcej o zasady szczegółowe hasło w dobrym stanie usługi Active Directory](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Skonfiguruj zasady szczegółowe hasło, za pomocą Centrum administracyjnego usługi AD](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
