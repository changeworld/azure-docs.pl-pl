---
title: Konfigurowanie kont laboratorium w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować konto laboratorium po jego utworzeniu.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 1a1b1e662a2e9adedfc68f1818f868c0a5318652
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428987"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Konfigurowanie kont laboratorium w Azure Lab Services 
W Azure Lab Services konto laboratorium jest kontenerem dla zarządzanych typów laboratorium, takich jak pracownie. Administrator konfiguruje konto laboratorium przy użyciu Azure Lab Services i zapewnia dostęp do właścicieli laboratorium, którzy mogą tworzyć laboratoria na koncie. W tym artykule opisano sposób tworzenia konta laboratorium, wyświetlania wszystkich kont laboratorium lub usuwania konta laboratorium.

## <a name="connect-with-a-peer-virtual-network"></a>Nawiązywanie połączenia z równorzędną siecią wirtualną
Aby połączyć sieć wirtualną jako sieć równorzędną z siecią wirtualną laboratorium, wykonaj następujące kroki:

1. Na stronie **konto laboratorium** wybierz pozycję **Konfiguracja laboratoriów** w menu po lewej stronie.

    ![Strona konfiguracji laboratoriów](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. W przypadku **równorzędnej sieci wirtualnej**wybierz opcję **włączone** lub **wyłączone**. Wartość domyślna jest **wyłączona**. Aby włączyć równorzędną sieć wirtualną, wykonaj następujące czynności: 
    1. Wybierz pozycję **Włączone**.
    2. Wybierz **sieć wirtualną** z listy rozwijanej. 
3. Wybierz pozycję **Zapisz** na pasku narzędzi. 

Laboratoria utworzone na tym koncie są połączone z wybraną siecią wirtualną. Mogą oni uzyskać dostęp do zasobów w wybranej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [łączenie sieci laboratorium z równorzędną siecią wirtualną w Azure Lab Services](how-to-connect-peer-virtual-network.md).

W przypadku wybrania sieci wirtualnej dla pola **równorzędna Sieć wirtualna** opcja **Zezwól na wybór laboratorium do wyboru lokalizacji laboratorium** jest wyłączona. Wynika to z faktu, że laboratorium na koncie laboratorium musi znajdować się w tym samym regionie co konto laboratorium, aby można było połączyć się z zasobami w równorzędnej sieci wirtualnej. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Zezwól twórcy laboratorium na wybranie lokalizacji dla laboratorium
Można zezwolić na tworzenie laboratoriów przez twórcę w innej lokalizacji niż lokalizacja konta laboratorium, wykonując następujące czynności: 

1. Na stronie **konto laboratorium** wybierz pozycję **Konfiguracja laboratoriów** w menu po lewej stronie.
2. W polu Zezwalaj na wybór laboratorium w celu wybrania **lokalizacji laboratorium**wybierz opcję **włączone** , jeśli chcesz, aby twórca laboratorium mógł wybrać lokalizację dla laboratorium. W przypadku wyłączenia tej funkcji laboratoria są tworzone automatycznie w tej samej lokalizacji, w której znajduje się konto laboratorium. 
    
    To pole jest wyłączone w przypadku wybrania sieci wirtualnej dla pola **równorzędnej sieci wirtualnej** . Jest to spowodowane tym, że laboratorium na koncie laboratorium musi znajdować się w tym samym regionie co konto laboratorium, aby uzyskać dostęp do zasobów w równorzędnej sieci wirtualnej. 
1. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Skonfiguruj ustawienie lokalizacji laboratorium](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Określ zakres adresów dla maszyn wirtualnych w laboratorium
Poniższa procedura zawiera kroki umożliwiające określenie zakresu adresów dla maszyn wirtualnych w laboratorium. W przypadku aktualizacji zakresu, który został wcześniej określony, zmodyfikowany zakres adresów ma zastosowanie tylko do maszyn wirtualnych, które zostały utworzone po wprowadzeniu zmiany. 

Poniżej przedstawiono niektóre ograniczenia dotyczące określania zakresu adresów, który należy zachować na uwadze. 

- Prefiks musi być mniejszy lub równy 23. 
- Jeśli sieć wirtualna jest połączona z kontem laboratorium, podany zakres adresów nie może pokrywać się z zakresem adresów z równorzędnej sieci wirtualnej.

1. Na stronie **konto laboratorium** wybierz pozycję **Konfiguracja laboratoriów** w menu po lewej stronie.
2. Dla pola **zakres adresów** Określ zakres adresów dla maszyn wirtualnych, które zostaną utworzone w laboratorium. Zakres adresów powinien znajdować się w notacji Classless Inter-Domain Routing (CIDR) (przykład: 10.20.0.0/23). Maszyny wirtualne w laboratorium zostaną utworzone w tym zakresie adresów.
3. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Konfiguruj zakres adresów](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Dodawanie użytkownika do roli twórcy laboratorium
Aby skonfigurować laboratorium na potrzeby zajęć w ramach konta laboratorium, użytkownik musi być członkiem roli **Twórca laboratorium** na koncie laboratorium. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do tej roli. Jeśli planujesz użyć tego samego konta użytkownika w celu utworzenia laboratorium na potrzeby zajęć, możesz pominąć ten krok. Aby użyć innego konta użytkownika do utworzenia laboratorium na potrzeby zajęć, wykonaj następujące czynności: 

Aby przyznać nauczycielom uprawnienie do tworzenia laboratoriów na potrzeby zajęć, dodaj ich do roli **Twórca laboratorium**:

1. Na stronie **Konto laboratorium** wybierz pozycję **Kontrola dostępu (Zarządzanie dostępem i tożsamościami)** i kliknij pozycję **+ Dodaj przypisanie roli** na pasku narzędzi. 

    ![Kontrola dostępu -> przycisk Dodaj przypisanie roli](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na stronie **Dodawanie przypisania roli** wybierz pozycję **Twórca laboratorium** w obszarze **Rola**, wybierz użytkownika do dodania do roli Twórcy laboratorium, a następnie wybierz pozycję **Zapisz**. 

    ![Dodawanie twórcy laboratorium](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Jeśli dodajesz użytkownika innego niż konto Microsoft jako twórcę laboratorium, zobacz sekcję [Dodawanie użytkownika niekonto Microsoftowego jako kreatora laboratorium](#add-a-non-microsoft-account-user-as-a-lab-creator) . 

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Określanie obrazów w portalu Marketplace dostępnych dla twórców laboratorium
Jako właściciel konta laboratorium możesz określić obrazy witryny Marketplace, których twórcy laboratorium mogą używać do tworzenia laboratoriów na koncie laboratorium. 

1. W menu po lewej stronie wybierz pozycję **Obrazy w portalu Marketplace**. Domyślnie zobaczysz pełną listę obrazów (włączonych i wyłączonych). Tę listę można filtrować, aby wyświetlić tylko obrazy włączone/wyłączone, wybierając z listy rozwijanej u góry opcję **Tylko włączone**/**Tylko wyłączone**. 
    
    ![Strona Obrazy w portalu Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    Obrazy witryny Marketplace wyświetlane na liście to tylko obrazy spełniające następujące warunki:
        
    - Tworzą jedną maszynę wirtualną
    - Używają usługi Azure Resource Manager do aprowizowania maszyn wirtualnych
    - Nie wymagają zakupu dodatkowego planu licencjonowania
2. Aby w portalu Marketplace **wyłączyć** obraz, który zostały włączony, wykonaj jedną z następujących czynności: 
    1. Wybierz pozycję **... (wielokropek)** w ostatniej kolumnie, a następnie polecenie **Wyłącz obraz**. 

        ![Wyłączanie jednego obrazu](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Wybierz jeden lub większą liczbę obrazów z listy przez zaznaczenie pól wyboru przed nazwami obrazów, a następnie wybierz pozycję **Wyłącz wybrane obrazy**. 

        ![Wyłączanie wielu obrazów](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. Podobnie, aby w portalu Marketplace **włączyć** obraz, wykonaj jedną z następujących czynności: 
    1. Wybierz pozycję **... (wielokropek)** w ostatniej kolumnie, a następnie polecenie **Włącz obraz**. 
    2. Wybierz jeden lub większą liczbę obrazów z listy przez zaznaczenie pól wyboru przed nazwami obrazów, a następnie wybierz pozycję **Włącz wybrane obrazy**. 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Dodawanie użytkownika innego niż konto Microsoft jako twórca laboratorium
Aby dodać użytkownika jako twórcę laboratorium, należy użyć swoich kont e-mail. Mogą być używane następujące typy kont e-mail:

- Konto e-mail, które jest dostarczane przez Azure Active Directory Office 365 (AAD). 
- Konto e-mail firmy Microsoft, takie jak `@outlook.com`, `@hotmail.com`, `@msn.com`lub `@live.com`.
- Konto e-mail inne niż firmy Microsoft, na przykład udostępniane przez firmę Yahoo lub Google. Jednak te typy kont muszą być połączone z konto Microsoft.
- Konto usługi GitHub. To konto musi być połączone z konto Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Korzystanie z konta e-mail innego niż Microsoft
Twórcy laboratorium/instruktorzy mogą korzystać z kont e-mail innych niż firmy Microsoft w celu zarejestrowania się w laboratorium zajęć i zalogowania się do niego.  Jednak zalogowanie się w portalu usług Lab Services wymaga, aby instruktorzy utworzyli konto Microsoft, które są połączone ze swoim adresem e-mail firmy innej niż Microsoft.

Wiele instruktorów może już mieć konto Microsoft połączony ze swoimi adresami e-mail spoza firmy Microsoft. Na przykład Instruktorzy mają już konto Microsoft, jeśli użyły swojego adresu e-mail od innych produktów lub usług firmy Microsoft, takich jak Office, Skype, OneDrive lub Windows.  

Gdy instruktorzy logują się do portalu usług Lab Services, są monitowani o podanie adresu e-mail i hasła. Jeśli instruktor podejmie próbę zalogowania się przy użyciu niekonto Microsoft, który nie ma powiązanego konto Microsoft, zostanie wyświetlony następujący komunikat o błędzie: 

![Komunikat o błędzie](../media/how-to-configure-student-usage/cant-find-account.png)

Aby zarejestrować się w celu konto Microsoft, Instruktorzy powinni przejść do [http://signup.live.com](http://signup.live.com).  


### <a name="using-a-github-account"></a>Korzystanie z konta usługi GitHub
Instruktorzy mogą także użyć istniejącego konta usługi GitHub do zarejestrowania się i zalogować się do laboratorium z klasą. Jeśli instruktor ma już konto Microsoft połączony z kontem usługi GitHub, może się zalogować i podać hasło, jak pokazano w poprzedniej sekcji. Jeśli konto usługi GitHub nie zostało jeszcze połączone z konto Microsoft, należy wybrać **Opcje logowania**:

![Link opcji logowania](../media/how-to-configure-student-usage/signin-options.png)

Na stronie **Opcje logowania** wybierz pozycję **Zaloguj się za pomocą usługi GitHub**.

![Zaloguj się przy użyciu linku GitHub](../media/how-to-configure-student-usage/signin-github.png)

Na koniec są wyświetlane monity o utworzenie konto Microsoft połączonego z kontem usługi GitHub. Odbywa się to automatycznie po wybraniu **przycisku Dalej**przez instruktora.  Instruktor jest następnie natychmiast zalogowany i połączony z laboratorium klasy.

## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Automatyczne zamykanie maszyn wirtualnych przy rozłączaniu
Można włączyć lub wyłączyć automatyczne zamykanie maszyn wirtualnych laboratorium systemu Windows (szablonu lub ucznia) po odłączeniu połączenia z pulpitem zdalnym. Możesz również określić, jak długo maszyny wirtualne powinny czekać, aby użytkownik mógł ponownie nawiązać połączenie przed automatycznym zamknięciem.

![Ustawienie automatycznego zamykania na koncie laboratorium](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

To ustawienie ma zastosowanie do wszystkich laboratoriów utworzonych w ramach konta laboratorium. Właściciel laboratorium może zastąpić to ustawienie na poziomie laboratorium. Zmiana tego ustawienia na koncie laboratorium będzie miała wpływ tylko na laboratoria, które są tworzone po wprowadzeniu zmiany.

Aby dowiedzieć się, jak właściciel laboratorium może skonfigurować to ustawienie na poziomie laboratorium, zobacz [ten artykuł](how-to-enable-shutdown-disconnect.md) .

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium Uzyskuj dostęp do laboratoriów zajęć](how-to-use-classroom-lab.md)
