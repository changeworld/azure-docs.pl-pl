---
title: Tworzenie i Używanie zasad haseł w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak i dlaczego należy używać szczegółowych zasad haseł do zabezpieczania i kontrolowania haseł kont w domenie zarządzanej AD DS platformy Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 08/08/2019
ms.author: iainfou
ms.openlocfilehash: 19a618bd576687fcb0d92f8e35613e4cdc749e70
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71320444"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Zasady blokowania haseł i kont w domenach zarządzanych

Aby zarządzać zabezpieczeniami kont w Azure Active Directory Domain Services (Azure AD DS), można zdefiniować szczegółowe zasady haseł, które kontrolują ustawienia, takie jak minimalna długość hasła, czas wygaśnięcia hasła lub złożoność hasła. Domyślne zasady haseł są stosowane do wszystkich użytkowników w domenie zarządzanej AD DS platformy Azure. Aby zapewnić szczegółową kontrolę i spełnić określone potrzeby biznesowe lub dotyczące zgodności, można utworzyć dodatkowe zasady i zastosować je do określonych grup użytkowników.

W tym artykule opisano sposób tworzenia i konfigurowania szczegółowych zasad haseł przy użyciu Centrum administracyjne usługi Active Directory.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
  * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
  * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
  * W razie potrzeby Uzupełnij samouczek, aby [utworzyć i skonfigurować wystąpienie Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Maszyna wirtualna zarządzania systemem Windows Server, która jest dołączona do domeny zarządzanej AD DS platformy Azure.
  * W razie potrzeby Ukończ samouczek, aby [utworzyć maszynę wirtualną zarządzania][tutorial-create-management-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów DC usługi Azure AD* w dzierżawie usługi Azure AD.

## <a name="fine-grained-password-policies-fgpp-overview"></a>Szczegółowe zasady haseł (szczegółowych zasad haseł) — Omówienie

Szczegółowe zasady haseł (FGPPs) pozwalają stosować określone ograniczenia dotyczące zasad blokowania haseł i kont dla różnych użytkowników w domenie. Na przykład, aby zabezpieczyć konta uprzywilejowane, można zastosować bardziej rygorystyczne ustawienia hasła niż regularne konta bez uprawnień. Można utworzyć wiele FGPPs, aby określić zasady haseł w ramach domeny zarządzanej AD DS platformy Azure.

Następujące ustawienia hasła można skonfigurować za pomocą szczegółowych zasad haseł:

* Minimalna długość hasła
* Historia haseł
* Hasła muszą spełniać wymagania dotyczące złożoności
* Minimalny wiek hasła
* Maksymalny okres ważności hasła
* Zasady blokady konta
  * Czas trwania blokady konta
  * Liczba dozwolonych nieudanych prób logowania
  * Resetowanie liczby nieudanych prób logowania po

SZCZEGÓŁOWYCH zasad haseł ma wpływ tylko na użytkowników utworzonych w usłudze Azure AD DS. Zasady haseł nie mają wpływu na użytkowników w chmurze i użytkowników domeny, które zostały zsynchronizowane z domeną zarządzaną AD DS platformy Azure z usługi Azure AD.

Zasady są dystrybuowane za pomocą skojarzenia grupy w domenie zarządzanej platformy Azure AD DS i wszelkie wprowadzone zmiany są stosowane podczas następnego logowania użytkownika. Zmiana zasad nie powoduje odblokowania konta użytkownika, które jest już zablokowane.

## <a name="default-fine-grained-password-policy-settings"></a>Domyślne szczegółowe ustawienia zasad haseł

W domenie zarządzanej AD DS platformy Azure następujące zasady haseł są domyślnie konfigurowane i stosowane dla wszystkich użytkowników:

* **Minimalna długość hasła (w znakach):** 7
* **Maksymalny wiek hasła (okres istnienia):** 90 dni
* **Hasła muszą spełniać wymagania dotyczące złożoności**

Następujące zasady blokady konta są konfigurowane domyślnie:

* **Czas trwania blokady konta:** 30
* **Liczba dozwolonych nieudanych prób logowania:** 5
* **Resetowanie nieudanych prób logowania po:** 30 minut

W przypadku tych domyślnych ustawień konta użytkowników są blokowane przez 30 minut, jeśli pięć nieprawidłowych haseł zostało użytych w ciągu 2 minut. Konta są automatycznie odblokowywane po 30 minutach.

Nie można modyfikować ani usuwać domyślnych zasad szczegółowych haseł. Zamiast tego członkowie grupy *Administratorzy domeny usługi AAD* mogą tworzyć niestandardowe szczegółowych zasad haseł i konfigurować je do przesłonięcia (mają pierwszeństwo przed) domyślną wbudowaną szczegółowych zasad haseł, jak pokazano w następnej sekcji.

## <a name="create-a-custom-fine-grained-password-policy"></a>Tworzenie niestandardowych zasad haseł szczegółowych

Podczas kompilowania i tworzenia aplikacji na platformie Azure możesz chcieć skonfigurować niestandardowe szczegółowych zasad haseł. Niektóre przykłady potrzeby tworzenia niestandardowych szczegółowych zasad haseł obejmują ustawianie różnych zasad blokowania kont lub Konfigurowanie domyślnego okresu istnienia hasła dla domeny zarządzanej.

Można utworzyć niestandardowe szczegółowych zasad haseł i zastosować je do określonych grup w domenie zarządzanej platformy Azure AD DS. Ta konfiguracja skutecznie zastępuje domyślny szczegółowych zasad haseł. Można także utworzyć niestandardowe zasady haseł i zastosować je do wszystkich niestandardowych jednostek organizacyjnych tworzonych w domenie zarządzanej AD DS platformy Azure.

Aby utworzyć szczegółowe zasady haseł, użyj Active Directory narzędzi administracyjnych z maszyny wirtualnej przyłączonej do domeny. Centrum administracyjne usługi Active Directory umożliwia wyświetlanie, edytowanie i tworzenie zasobów w domenie zarządzanej AD DS platformy Azure, w tym jednostek organizacyjnych.

> [!NOTE]
> Aby utworzyć szczegółowe zasady haseł w domenie zarządzanej usługi Azure AD DS, użytkownik musi być zalogowany na koncie użytkownika, który jest członkiem grupy *administratorów DC* .

1. Na ekranie startowym wybierz pozycję **Narzędzia administracyjne**. Zostanie wyświetlona lista dostępnych narzędzi do zarządzania, które zostały zainstalowane w samouczku, aby [utworzyć maszynę wirtualną zarządzania][tutorial-create-management-vm].
1. Aby utworzyć jednostki organizacyjne i zarządzać nimi, wybierz **Centrum administracyjne usługi Active Directory** z listy narzędzi administracyjnych.
1. W lewym okienku wybierz domenę zarządzaną platformy Azure AD DS, na przykład *contoso.com*.
1. Otwórz kontener **system** , a następnie pozycję kontener **ustawień haseł** .

    Zostanie wyświetlona wbudowana szczegółowych zasad haseł dla domeny zarządzanej platformy Azure AD DS. Nie można zmodyfikować tego wbudowanego szczegółowych zasad haseł. Zamiast tego Utwórz nowy niestandardowy szczegółowych zasad haseł, aby zastąpić domyślny szczegółowych zasad haseł.
1. W panelu **zadania** po prawej stronie wybierz pozycję **nowe ustawienia hasła >** .
1. W oknie dialogowym **Tworzenie ustawień hasła** wprowadź nazwę zasad, na przykład *MyCustomFGPP*. Ustaw odpowiedni priorytet, aby zastąpić domyślny szczegółowych zasad haseł ( *200*), na przykład *1*.

    Edytuj inne ustawienia zasad haseł zgodnie z potrzebami, na przykład **Wymuszaj historię haseł** , aby wymagać od użytkownika utworzenia hasła, które różni się od poprzednich *24* haseł.

    ![Tworzenie niestandardowych zasad haseł szczegółowych](./media/how-to/custom-fgpp.png)

1. Usuń zaznaczenie pola **Chroń przed przypadkowym usunięciem**. W przypadku wybrania tej opcji nie można zapisać szczegółowych zasad haseł.
1. W sekcji **bezpośrednio stosuje się do** wybierz przycisk **Dodaj** . W oknie dialogowym **Wybieranie użytkowników lub grup** kliknij przycisk **lokalizacje** .

    ![Wybierz użytkowników i grupy, do których mają zostać zastosowane zasady haseł](./media/how-to/fgpp-applies-to.png)

1. Szczegółowe zasady haseł mogą być stosowane tylko do grup. W oknie dialogowym **lokalizacje** rozwiń nazwę domeny, na przykład *contoso.com*, a następnie wybierz jednostkę organizacyjną, taką jak **AADDC users**. Jeśli masz niestandardową jednostkę organizacyjną zawierającą grupę użytkowników, których chcesz użyć, wybierz tę jednostkę organizacyjną.

    ![Wybierz jednostkę organizacyjną, do której należy Grupa](./media/how-to/fgpp-container.png)

1. Wpisz nazwę grupy, do której chcesz zastosować zasady, a następnie wybierz pozycję **Sprawdź nazwy** , aby sprawdzić, czy grupa istnieje.

    ![Wyszukaj i wybierz grupę, która ma zostać zastosowana szczegółowych zasad haseł](./media/how-to/fgpp-apply-group.png)

1. Po wybraniu nazwy grupy, która została **zastosowana bezpośrednio, ma zastosowanie do** sekcji, wybierz **przycisk OK** , aby zapisać niestandardowe zasady haseł.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat szczegółowych zasad haseł i korzystania z centrum administracyjnego Active Directory, zobacz następujące artykuły:

* [Więcej informacji na temat szczegółowych zasad haseł](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurowanie szczegółowych zasad haseł przy użyciu Centrum administracyjne usługi AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
