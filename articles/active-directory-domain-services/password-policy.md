---
title: Tworzenie i używanie zasad haseł w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak i dlaczego używać zasad haseł ziarnistych do zabezpieczania i kontrolowania haseł kont w domenie zarządzanej usług Azure AD DS.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: c4402c1ce2f051c8d1911e7c0332d4cac787ce1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613194"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Zasady blokady haseł i kont w domenach zarządzanych

Aby zarządzać zabezpieczeniami użytkowników w usługach domenowych Active Directory platformy Azure (Usługi Azure AD DS), można zdefiniować szczegółowe zasady haseł, które kontrolują ustawienia blokady konta lub minimalną długość i złożoność hasła. Domyślna zasada haseł drobnoziarnistych jest tworzona i stosowana do wszystkich użytkowników w domenie zarządzanej usług Azure AD DS. Aby zapewnić szczegółową kontrolę i spełnić określone potrzeby biznesowe lub związane z przepisami, można tworzyć i stosować dodatkowe zasady do określonych grup użytkowników.

W tym artykule pokazano, jak utworzyć i skonfigurować szczegółowe zasady haseł w usługach Azure AD DS przy użyciu Centrum administracyjnego usługi Active Directory.

> [!NOTE]
> Zasady haseł są dostępne tylko dla domen zarządzanych usług Azure AD DS utworzonych przy użyciu modelu wdrażania Menedżera zasobów. W przypadku starszych domen zarządzanych utworzonych przy użyciu [klasycznego modelu sieci wirtualnej należy przeprowadzić migrację z klasycznego modelu sieci wirtualnej do Menedżera zasobów][migrate-from-classic].

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
  * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
  * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
  * W razie potrzeby wykonaj samouczek, aby [utworzyć i skonfigurować wystąpienie usług domenowych Usługi domenowe Active Directory][create-azure-ad-ds-instance]platformy Azure .
  * Wystąpienie usługi Azure AD DS musi zostać utworzone przy użyciu modelu wdrażania Usługi Resource Manager. W razie potrzeby [należy przeprowadzić migrację z klasycznego modelu sieci wirtualnej do Menedżera zasobów][migrate-from-classic].
* Maszyna wirtualna z zarządzaniem systemem Windows Server, która jest przyłączona do domeny zarządzanej usług Azure AD DS.
  * W razie potrzeby wykonaj samouczek, aby [utworzyć maszynę wirtualną do zarządzania][tutorial-create-management-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów usługi Azure AD DC* w dzierżawie usługi Azure AD.

## <a name="default-password-policy-settings"></a>Domyślne ustawienia zasad haseł

Szczegółowe zasady haseł (FGPPs) umożliwiają stosowanie określonych ograniczeń dla zasad blokowania haseł i kont do różnych użytkowników w domenie. Na przykład w celu zabezpieczenia kont uprzywilejowanych można zastosować bardziej rygorystyczne ustawienia blokady konta niż zwykłe konta nieuprzywilejowane. Można utworzyć wiele kontrolerów FGP w domenie zarządzanej usług Azure AD DS i określić kolejność priorytetów, aby zastosować je do użytkowników.

Zasady są dystrybuowane za pośrednictwem skojarzenia grupy w domenie zarządzanej usługi Azure AD DS, a wszelkie wprowadzone zmiany są stosowane przy następnym logowanie użytkownika. Zmiana zasad nie odblokowuje konta użytkownika, które jest już zablokowane.

Zasady haseł zachowują się nieco inaczej w zależności od sposobu utworzenia konta użytkownika, do które zostały zastosowane. Istnieją dwa sposoby tworzenia konta użytkownika w usługach Azure AD DS:

* Konto użytkownika można zsynchronizować z usługi Azure AD. Obejmuje to konta użytkowników tylko w chmurze utworzone bezpośrednio na platformie Azure i konta użytkowników hybrydowych zsynchronizowane z lokalnego środowiska usług AD DS przy użyciu usługi Azure AD Connect.
    * Większość kont użytkowników w usługach Azure AD DS są tworzone za pomocą procesu synchronizacji z usługi Azure AD.
* Konto użytkownika można ręcznie utworzyć w domenie zarządzanej usług Azure AD DS i nie istnieje w usłudze Azure AD.

Wszyscy użytkownicy, niezależnie od sposobu ich tworzenia, mają następujące zasady blokady konta stosowane przez domyślne zasady haseł w usługach Azure AD DS:

* **Czas trwania blokady konta:** 30
* **Dozwolona liczba nieudanych prób logowania:** 5
* **Resetowanie nieudanych prób logowania liczy się po:** 30 minutach
* **Maksymalny wiek hasła (okres istnienia):** 90 dni

W przypadku tych ustawień domyślnych konta użytkowników są blokowane na 30 minut, jeśli w ciągu 2 minut zostanie użytych pięć nieprawidłowych haseł. Konta są automatycznie odblokowywane po 30 minutach.

Blokady kont występują tylko w domenie zarządzanej. Konta użytkowników są zablokowane tylko w usługach Azure AD DS i tylko z powodu nieudanych prób logowania względem domeny zarządzanej. Konta użytkowników, które zostały zsynchronizowane z usługi Azure AD lub lokalnie nie są zablokowane w ich katalogach źródłowych, tylko w usługach Azure AD DS.

Jeśli masz zasady haseł usługi Azure AD, która określa maksymalny wiek haseł większy niż 90 dni, ten wiek hasła jest stosowany do zasad domyślnych w usługach Azure AD DS. Można skonfigurować niestandardowe zasady haseł, aby zdefiniować inny maksymalny wiek haseł w usługach Azure AD DS. Należy uważać, jeśli masz krótszy maksymalny wiek haseł skonfigurowany w zasadach haseł usług AD DS usługi Azure niż w usłudze Azure AD lub lokalnym środowisku usług AD DS. W tym scenariuszu hasło użytkownika może wygasnąć w usługach Azure AD DS, zanim zostanie wyświetlony monit o zmianę w usłudze Azure AD lub lokalnym środowisku usług AD DS.

W przypadku kont użytkowników utworzonych ręcznie w domenie zarządzanej usług Azure AD DS z zasad domyślnych są również stosowane następujące dodatkowe ustawienia hasła. Te ustawienia nie mają zastosowania do kont użytkowników zsynchronizowanych z usługi Azure AD, ponieważ użytkownik nie może zaktualizować hasła bezpośrednio w usługach Azure AD DS.

* **Minimalna długość hasła (znaki):** 7
* **Hasła muszą spełniać wymagania dotyczące złożoności**

Nie można zmodyfikować ustawień blokady konta ani hasła w domyślnych zasadach haseł. Zamiast tego członkowie grupy *Administratorzy kontrolera domeny usługi AAD* mogą tworzyć niestandardowe zasady haseł i konfigurować je w taki sposób, aby zastępować domyślne wbudowane zasady (mają pierwszeństwo), jak pokazano w następnej sekcji.

## <a name="create-a-custom-password-policy"></a>Tworzenie niestandardowych zasad haseł

Podczas tworzenia i uruchamiania aplikacji na platformie Azure można skonfigurować niestandardowe zasady haseł. Na przykład można utworzyć zasadę, aby ustawić różne ustawienia zasad blokady konta.

Niestandardowe zasady haseł są stosowane do grup w domenie zarządzanej usług Azure AD DS. Ta konfiguracja skutecznie zastępuje zasady domyślne.

Aby utworzyć niestandardowe zasady haseł, należy użyć narzędzi administracyjnych usługi Active Directory z maszyny wirtualnej przyłączone do domeny. Centrum administracyjne usługi Active Directory umożliwia wyświetlanie, edytowanie i tworzenie zasobów w domenie zarządzanej usług Azure AD DS, w tym w jednostkach organizacyjnych.

> [!NOTE]
> Aby utworzyć niestandardowe zasady haseł w domenie zarządzanej usług Azure AD DS, musisz zalogować się do konta użytkownika, które jest członkiem grupy *Administratorzy kontrolera domeny usługi AAD.*

1. Na ekranie startowym wybierz pozycję **Narzędzia administracyjne**. Zostanie wyświetlona lista dostępnych narzędzi do zarządzania, które zostały zainstalowane w samouczku w celu [utworzenia maszyny wirtualnej do zarządzania][tutorial-create-management-vm].
1. Aby utworzyć procesory organizacyjne i zarządzać nimi, wybierz pozycję **Centrum administracyjne usługi Active Directory** z listy narzędzi administracyjnych.
1. W lewym okienku wybierz domenę zarządzana usługą Azure AD DS, taką jak *aaddscontoso.com*.
1. Otwórz kontener **systemowy,** a następnie **kontener ustawień haseł**.

    Zostanie wyświetlona wbudowana zasada haseł dla domeny zarządzanej usług Azure AD DS. Nie można zmodyfikować tej wbudowanej zasady. Zamiast tego należy utworzyć niestandardowe zasady haseł, aby zastąpić domyślną zasadę.

    ![Tworzenie zasad haseł w Centrum administracyjnym usługi Active Directory](./media/password-policy/create-password-policy-adac.png)

1. W panelu **Zadania** po prawej stronie wybierz pozycję **Nowe > Ustawienia hasła**.
1. W oknie dialogowym **Tworzenie ustawień hasła** wprowadź nazwę zasad, na przykład *MyCustomFGPP*.
1. Jeśli istnieje wiele zasad haseł, zasady o najwyższym priorytecie lub priorytecie są stosowane do użytkownika. Im mniejsza liczba, tym wyższy priorytet. Domyślna zasada haseł ma priorytet *200*.

    Ustaw pierwszeństwo dla niestandardowych zasad haseł, aby zastąpić domyślne, takie jak *1*.

1. Edytuj inne ustawienia zasad haseł zgodnie z potrzebami. Zapamiętaj następujące kluczowe punkty:

    * Ustawienia, takie jak złożoność hasła, wiek lub czas wygaśnięcia tylko dla użytkowników ręcznie utworzonych w domenie zarządzanej usług Azure AD DS.
    * Ustawienia blokady konta dotyczą wszystkich użytkowników, ale są obowiązywać tylko w domenie zarządzanej, a nie w samej usłudze Azure AD.

    ![Tworzenie niestandardowych zasad haseł precyzyjnych](./media/how-to/custom-fgpp.png)

1. Odznacz pola wyboru **Chroń przed przypadkowym usunięciem**. Jeśli ta opcja jest zaznaczona, nie można zapisać FGPP.
1. W sekcji **Bezpośrednio dotyczy** przycisku **Dodaj.** W oknie dialogowym **Wybieranie użytkowników lub grup** wybierz przycisk **Lokalizacje.**

    ![Wybierz użytkowników i grupy, aby zastosować zasady haseł do](./media/how-to/fgpp-applies-to.png)

1. Zasady haseł można stosować tylko do grup. W oknie dialogowym **Lokalizacje** rozwiń nazwę domeny, taką jak *aaddscontoso.com*, a następnie wybierz o.o., na przykład **Użytkownicy usługi AADDC**. Jeśli masz niestandardową instalację organizacyjną, która zawiera grupę użytkowników, których chcesz zastosować, wybierz tę ojówę.

    ![Wybierz ou, do którego należy grupa](./media/how-to/fgpp-container.png)

1. Wpisz nazwę grupy, do której chcesz zastosować zasady, a następnie wybierz pozycję **Sprawdź nazwy,** aby sprawdzić, czy grupa istnieje.

    ![Wyszukaj i wybierz grupę, aby zastosować FGPP](./media/how-to/fgpp-apply-group.png)

1. Po nazwie wybranej grupy wyświetlanej teraz w sekcji **Bezpośrednie dotyczy,** wybierz przycisk **OK,** aby zapisać niestandardowe zasady haseł.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zasad haseł i korzystania z Centrum administracyjnego usługi Active Directory, zobacz następujące artykuły:

* [Dowiedz się więcej o zasadach dotyczących haseł precyzyjnych](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurowanie zasad haseł ziarnistych przy użyciu Centrum administracyjnego usługi AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
