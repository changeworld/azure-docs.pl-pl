---
title: 'Azure Active Directory Domain Services: Dołącz maszynę Wirtualną z systemem Windows Server do domeny zarządzanej | Dokumentacja firmy Microsoft'
description: Dołączanie maszyny wirtualnej systemu Windows Server do usługi Azure AD DS
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: mstephen
ms.openlocfilehash: 1564fff8a749cc2d4db9e8e820981ba5dc1da570
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245972"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Przyłączanie maszyny wirtualnej z systemem Windows Server do domeny zarządzanej
W tym artykule pokazano, jak wdrożyć maszynę wirtualną systemu Windows Server przy użyciu witryny Azure portal. Go następnie pokazano, jak dołączyć maszyny wirtualnej do domeny zarządzanej usługi Azure Active Directory Domain Services (Azure AD DS).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="step-1-create-a-windows-server-virtual-machine"></a>Krok 1: Tworzenie maszyny wirtualnej z systemem Windows Server
Aby utworzyć maszynę wirtualną Windows, który jest dołączony do sieci wirtualnej, w której włączono usługi Azure AD DS, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W górnej części okienka po lewej stronie wybierz **New**.
3. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**.

    ![Łącze systemu Windows Server 2016 Datacenter](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. W **podstawy** okienka kreatora, skonfiguruj ustawienia podstawowe dla maszyny wirtualnej.

    ![W okienku podstawy](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > Nazwa użytkownika i hasło wprowadzone w tym miejscu są konta administratora lokalnego, która jest używana do logowania się do maszyny wirtualnej. Wybierz silne hasło, aby chronić tę maszynę wirtualną przed atakami siłowymi hasła. Nie należy wprowadzać poświadczeń konta użytkownika domeny w tym miejscu.
    >

5. Wybierz **rozmiar** dla maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz **Wyświetl wszystkie** lub zmienić **obsługiwany typ dysku** filtru.

    ![W okienku "Wybierz rozmiar"](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. W **ustawienia** okienku wybierz sieć wirtualną, w której jest wdrażany domeny usługi Azure AD DS, zarządzane. Wybierz niż Twoja domena zarządzana jest wdrażana w innej podsieci. Dla innych ustawień pozostaw wartości domyślne, a następnie wybierz **OK**.

    ![Ustawienia sieci wirtualnej dla maszyny wirtualnej](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Wybierz właściwą sieć i podsieci.**
    >
    > Wybierz albo sieć wirtualną, w której jest wdrażany z domeną zarządzaną lub sieci wirtualnej, która jest z nią połączona za pomocą wirtualnej sieci równorzędnej. Jeśli wybierzesz niepołączoną wirtualną, nie można dołączyć do maszyny wirtualnej do domeny zarządzanej.
    >
    > Firma Microsoft zaleca wdrażanie Twojej domeny zarządzanej w dedykowanej podsieci. Nie w związku z tym, wybierz podsieć, w którym włączono Twojej domeny zarządzanej.

7. Dla innych ustawień pozostaw wartości domyślne, a następnie wybierz **OK**.
8. Na **zakupu** strony, przejrzyj ustawienia, a następnie wybierz pozycję **OK** wdrożyć maszynę wirtualną.
9. Wdrażanie maszyny Wirtualnej jest przypięta do pulpitu nawigacyjnego portalu platformy Azure.

    ![Gotowe](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
10. Po zakończeniu wdrożenia można wyświetlić informacje o maszynie Wirtualnej na **Przegląd** strony.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-by-using-the-local-administrator-account"></a>Krok 2: Łączenie z maszyną wirtualną systemu Windows Server przy użyciu konta administratora lokalnego
Następnie należy połączyć się z nowo utworzoną maszyną wirtualną systemu Windows Server, aby przyłączyć do domeny. Użyj poświadczeń administratora lokalnego, określony podczas tworzenia maszyny wirtualnej.

Aby połączyć z maszyną wirtualną, wykonaj następujące czynności:

1. W **Przegląd** okienku wybierz **Connect**.  
    Plik Remote Desktop Protocol (RDP), zostanie utworzony i pobrany.

    ![Łączenie z maszyną wirtualną Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP. Po wyświetleniu monitu wybierz pozycję **Połącz**.
3. Wprowadź swoje **poświadczenia administratora lokalnego**, które określono podczas tworzenia maszyny wirtualnej (na przykład *localhost\mahesh*).
4. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie w procesie logowania, wybierz opcję **tak** lub **Kontynuuj** połączyć.

W tym momencie użytkownik powinien być zalogowany do nowo utworzonej maszyny wirtualnej Windows przy użyciu poświadczeń administratora lokalnego. Następnym krokiem jest można dołączyć maszyny wirtualnej do domeny.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-azure-ad-ds-managed-domain"></a>Krok 3: Dołączanie maszyny wirtualnej systemu Windows Server do usługi Azure AD domeny zarządzanej usług DS
Można dołączyć maszyny wirtualnej systemu Windows Server do usługi Azure AD domeny zarządzanej usługi Katalogowej, wykonaj następujące czynności:

1. Podłącz do maszyny Wirtualnej systemu Windows Server, jak pokazano w "Krok 2." Na **Start** otwartym ekranem **Menedżera serwera**.
2. W okienku po lewej stronie **Menedżera serwera** wybierz **lokalnego serwera**.

    ![Okno Menedżera serwera na maszynie wirtualnej](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. W obszarze **właściwości**, wybierz opcję **grupy roboczej**.
4. W **właściwości systemu** wybierz **zmiany** do przyłączania do domeny.

    ![W oknie Właściwości systemu](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

5. W **domeny** pola, określ nazwę domeny usługi Azure AD DS, zarządzanych, a następnie wybierz **OK**.

    ![Określ domenę, która ma zostać umieszczony](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

6. Pojawi się prośba o podanie poświadczeń do przyłączania do domeny. Użyj poświadczeń dla *użytkownika, który należy do grupy Administratorzy kontrolera domeny usługi AD Azure*. Tylko członkowie tej grupy mają uprawnienia do przyłączania maszyn do domeny zarządzanej.

    ![W oknie zabezpieczenia Windows określania poświadczeń](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

7. Należy określić poświadczenia w jednym z następujących sposobów:

   * **Format nazwy UPN**: (Zalecane) Określ sufiks głównej nazwy (UPN) użytkownika dla konta użytkownika, zgodnie z konfiguracją w usłudze Azure AD. W tym przykładzie sufiks nazwy UPN użytkownika *bob* jest *bob\@domainservicespreview.onmicrosoft.com*.

   * **Formacie SAMAccountName**: Można określić nazwę konta w formacie SAMAccountName. W tym przykładzie użytkownik *bob* będzie konieczne wprowadzenie *CONTOSO100\bob*.

     > [!TIP]
     > **Zalecamy używanie w formacie UPN, aby określić poświadczenia.**
     >
     > Jeśli prefiks nazwy UPN użytkownika jest zbyt długa (na przykład *joehasareallylongname*), SAMAccountName mogą być generowane automatycznie. Jeśli wielu użytkowników mają ten sam prefiks nazwy UPN (na przykład *bob*) w swojej dzierżawie usługi Azure AD, ich formacie SAMAccountName może być automatycznie generowane przez usługę. W takich przypadkach w formacie UPN można wiarygodnie zalogować się do domeny.
     >

8. Po zostały pomyślnie przyłączone do domeny, następujący komunikat o przyjęciu do domeny.

    ![Domeny — Zapraszamy!](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

9. Aby wykonać dołączenie do domeny, uruchom ponownie maszynę wirtualną.

## <a name="troubleshoot-joining-a-domain"></a>Rozwiązywanie problemów z przyłączania do domeny
### <a name="connectivity-issues"></a>Problemy z łącznością
Jeśli maszyna wirtualna nie może znaleźć domeny, spróbuj wykonać następujące kroki:

* Sprawdź, czy maszyna wirtualna jest podłączona do usługi Azure AD DS jest włączone w tej samej sieci wirtualnej. W przeciwnym razie maszyny wirtualnej nie może nawiązać połączenie, lub przyłączenia do domeny.

* Sprawdź, czy maszyna wirtualna znajduje się w sieci wirtualnej, która z kolei jest połączony z siecią wirtualną usługi Azure AD DS jest włączone w.

* Spróbuj wysłać polecenie ping nazwa domeny DNS z domeny zarządzanej (na przykład *ping contoso100.com*). Jeśli nie możesz to zrobić, spróbuj wysłać polecenie ping adresów IP dla domeny, który jest wyświetlany na stronie którym włączono usługi Azure AD DS (na przykład *ping 10.0.0.4*). Możesz wysłać polecenie ping do adresu IP, ale nie domeny, DNS może być niepoprawnie skonfigurowana. Sprawdź, czy adresy IP domeny są skonfigurowane jako serwery DNS dla sieci wirtualnej.

* Spróbuj opróżniania pamięci podręcznej programu rozpoznawania nazw DNS na maszynie wirtualnej (*ipconfig/flushdns*).

Jeśli okno jest wyświetlane z prośbą o podanie poświadczeń w celu przyłączenia do domeny, nie masz problemy z łącznością.

### <a name="credentials-related-issues"></a>Problemy związane z poświadczeniami
Jeśli występują problemy z poświadczeniami i nie można przyłączyć do domeny, spróbuj wykonać następujące kroki:

* Spróbuj użyć w formacie UPN, aby określić poświadczenia. W przypadku wielu użytkowników korzystających z tym samym prefiksem nazwy UPN w swojej dzierżawie lub prefiksu nazwy UPN jest zbyt długi, SAMAccountName konta może być generowane automatycznie. W takich przypadkach w formacie SAMAccountName, konta mogą różnić się od co oczekiwane lub użyj w domenie lokalnej.

* Spróbuj przy użyciu poświadczeń konta użytkownika, który należy do *Administratorzy usługi AAD DC* grupy.

* Upewnij się, że masz [włączoną synchronizację haseł](active-directory-ds-getting-started-password-sync.md) do domeny zarządzanej.

* Sprawdź, że znasz nazwę UPN użytkownika zgodnie z konfiguracją w usłudze Azure AD (na przykład *bob\@domainservicespreview.onmicrosoft.com*) do logowania.

* Poczekaj wystarczająco długi, synchronizacja haseł zakończona, jak określono w przewodniku z wprowadzeniem.

## <a name="related-content"></a>Powiązana zawartość
* [Usługa Azure AD DS Wprowadzenie — przewodnik](create-instance.md)
* [Zarządzaj domeną usługi Azure AD Domain Services](manage-domain.md)
