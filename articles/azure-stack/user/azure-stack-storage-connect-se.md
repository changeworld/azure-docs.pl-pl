---
title: Łączenie Eksploratora usługi storage do subskrypcji usługi Azure Stack lub kontem magazynu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć programu storage explorer z subskrypcją usługi Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 314304e75ce0f2586f41b71a889fa0185501b845
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622012"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Łączenie Eksploratora usługi storage do subskrypcji usługi Azure Stack lub na koncie magazynu

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

W tym artykule dowiesz się, jak połączyć się do subskrypcji usługi Azure Stack i kontami magazynu przy użyciu Eksploratora usługi storage. Eksplorator usługi Azure storage jest aplikacją autonomiczną, która umożliwia łatwą obsługę danych w usłudze Azure Stack storage w Windows, macOS i Linux.

> [!NOTE]  
> Istnieje kilka dostępne narzędzia do przenoszenia danych do i z magazynu usługi Azure Stack. Aby uzyskać więcej informacji, zobacz [transferu danych, narzędzia do magazynu usługi Azure Stack](azure-stack-storage-transfer.md).

Jeśli nie zainstalowano jeszcze programu storage explorer [Pobierz bezpłatnie Eksplorator magazynu](https://www.storageexplorer.com/) i zainstaluj go.

Po połączeniu z subskrypcją usługi Azure Stack lub na koncie magazynu, można użyć [usługi Azure storage explorer artykuły](../../vs-azure-tools-storage-manage-with-storage-explorer.md) do pracy z danymi usługi Azure Stack. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Przygotowanie do nawiązywania połączenia z usługi Azure Stack

Należy bezpośredni dostęp do usługi Azure Stack lub połączenia sieci VPN dla Eksploratora usługi storage można uzyskać dostępu do subskrypcji usługi Azure Stack. Aby dowiedzieć się, jak skonfigurować połączenie VPN do usługi Azure Stack, zapoznaj się z tematem [Connect to Azure Stack with VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) (Łączenie z usługą Azure Stack przy użyciu sieci VPN).

Dla usługi Azure Stack Development Kit (ASDK), należy wyeksportować certyfikat główny urzędu usługi Azure Stack.

> [!Note]  
> Dla ASDK Jeśli łączysz się z ASDK za pośrednictwem sieci VPN, nie należy używać certyfikatu głównego (CA.cer), który został utworzony podczas procesu konfiguracji sieci VPN.  Jest to certyfikat zakodowany w formacie DER, a nie zezwolą Eksploratora usługi Storage można pobrać subskrypcji usługi Azure Stack. Wykonaj poniższe kroki, aby wyeksportować certyfikat zakodowany Base-64 za pomocą Eksploratora usługi Storage.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Wyeksportowanie i zaimportowanie certyfikatu usługi Azure Stack

Wyeksportować, a następnie zaimportuj certyfikat usługi Azure Stack dla ASDK. Zintegrowany system publicznie podpisany jest certyfikat. Tak ten krok nie jest konieczne, podczas konfigurowania połączenia Eksploratora usługi Storage do usługi Azure Stack zintegrowany system.

1. Otwórz `mmc.exe` na maszynie hosta usługi Azure Stack lub lokalnej maszynie z połączeniem VPN z usługą Azure Stack. 

2. W **pliku**, wybierz opcję **Dodaj/Usuń przystawkę**. Wybierz **certyfikaty** w dostępne przystawki. 

3. Wybierz **konto komputera**, a następnie wybierz pozycję **dalej**. Wybierz **komputera lokalnego**, a następnie wybierz pozycję **Zakończ**.

4.  W obszarze **konsoli katalog główny konsoli\certyfikowane (komputer lokalny) \Trusted główne urzędy certyfikacji\Certyfikaty**. Find **AzureStackSelfSignedRootCert**.

    ![Ładowanie certyfikatu głównego usługi Azure Stack za pośrednictwem pliku mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

5. Kliknij prawym przyciskiem myszy certyfikat, wybierz **wszystkie zadania** > **wyeksportować**, a następnie postępuj zgodnie z instrukcjami, aby wyeksportować certyfikat z **certyfikat x.509 szyfrowany algorytmem Base-64 (. CER)**.

    Wyeksportowany certyfikat będzie używany w następnym kroku.

6. Uruchom Eksploratora usługi storage, i jeśli zostanie wyświetlony **łączenie z usługą Azure Storage** okna dialogowego pole, anulować.

7. Na **Edytuj** menu wskaż **certyfikaty SSL**, a następnie wybierz pozycję **Importuj certyfikaty**. Użyj okna dialogowego selektora plików, aby wyszukać i otworzyć certyfikat wyeksportowany w poprzednim kroku.

    Po zaimportowaniu certyfikatu, monit o ponowne uruchomienie Eksploratora usługi storage.

    ![Zaimportuj certyfikat do Eksploratora usługi storage](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

8. Po ponownym uruchomieniu Eksploratora usługi storage wybierz **Edytuj** menu, a następnie sprawdź, czy **interfejsów API programu docelowej usługi Azure Stack** jest zaznaczone. Jeśli nie, wybierz **docelowej usługi Azure Stack**, a następnie ponownie uruchom Eksploratora usługi storage aby zmiany zaczęły obowiązywać. Ta konfiguracja jest wymagana w celu zachowania zgodności ze środowiskiem usługi Azure Stack.

    ![Sprawdzanie, czy pozycja Oznacz usługę Azure Stack jako cel została zaznaczona](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription-with-azure-ad"></a>Łączenie się z subskrypcją usługi Azure Stack z usługą Azure AD

Wykonaj następujące kroki, aby łączenie Eksploratora usługi storage z subskrypcji usługi Azure Stack, który należy do konta usługi Azure Active Directory (Azure AD).

1. W lewym okienku programu storage explorer, wybierz **Zarządzanie kontami**. 
    Wyświetlane są wszystkie subskrypcją Microsoft Azure, zalogowano się.

2. Aby połączyć z subskrypcją usługi Azure Stack, wybierz **Dodaj konto**.

    ![Dodawanie konta usługi Azure Stack](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. W przypadku nawiązywania połączenia z usługi Azure Storage okno dialogowe w obszarze **środowiska platformy Azure**, wybierz opcję **Azure**, **Azure (Chiny)**, **Azure (Niemcy)**,  **Azure dla administracji USA**, lub **Dodaj nowe środowisko**, która jest zależna od konta usługi Azure Stack, który jest używany. Wybierz **Zaloguj** zalogować się przy użyciu konta usługi Azure Stack skojarzone z co najmniej jedną aktywną subskrypcją usługi Azure Stack.

    ![Łączenie z usługą Azure Storage](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Po pomyślnym zalogowaniu się przy użyciu konta usługi Azure Stack w okienku po lewej stronie zostaną wyświetlone wszystkie subskrypcje usługi Azure Stack skojarzone z tym kontem. Wybierz subskrypcje usługi Azure Stack, z którymi chcesz pracować, a następnie wybierz przycisk **Zastosuj**. (Zaznaczenie lub usunięcie zaznaczenia pola wyboru **Wszystkie subskrypcje** powoduje przełączenie między wyświetlaniem wszystkich lub żadnej z wymienionych subskrypcji usługi Azure Stack).

    ![Wybieranie subskrypcji usługi Azure Stack po wypełnieniu pól okna dialogowego Środowisko niestandardowe chmury](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    W okienku po lewej stronie są wyświetlane wszystkie konta magazynu skojarzone z wybranymi subskrypcjami usługi Azure Stack.

    ![Lista kont magazynu, w tym kont subskrypcji usługi Azure Stack](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-subscription-with-ad-fs-account"></a>Łączenie się z subskrypcją usługi Azure Stack przy użyciu konta usług AD FS

> [!Note]  
> Środowisko logowania usługa federacyjna platformy Azure (AD FS) obsługuje Eksploratora usługi Storage 1.2.0 lub nowszej wersji za pomocą usługi Azure Stack w wersji 1804 lub nowsza aktualizacja.
Wykonaj następujące kroki, aby łączenie Eksploratora usługi storage z subskrypcją usługi Azure Stack, do której należy konto usług AD FS.

1. Wybierz **Zarządzanie kontami**. Eksplorator Wyświetla listę subskrypcji firmy Microsoft, które użytkownik zalogowany do.
2. Wybierz **Dodaj konto** nawiązać połączenia z subskrypcją usługi Azure Stack.

    ![Dodaj konto](media/azure-stack-storage-connect-se/add-an-account.png)

3. Wybierz opcję **Dalej**. W przypadku nawiązywania połączenia z usługi Azure Storage okno dialogowe w obszarze **środowiska platformy Azure**, wybierz opcję **użycia niestandardowe środowisko**, następnie kliknij przycisk **dalej**.

    ![Łączenie z usługą Azure Storage](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Wprowadź wymagane informacje środowiska niestandardowego usługi Azure Stack. 

    | Pole | Uwagi |
    | ---   | ---   |
    | Nazwa środowiska | Pola można dostosować przez użytkownika. |
    | Punkt końcowy usługi Azure Resource Manager | Przykłady programu Azure Stack Development Kit, punktów końcowych zasobów usługi Azure Resource Manager.<br>Dla operatorów: https://adminmanagement.local.azurestack.external <br> Dla użytkowników: https://management.local.azurestack.external |

    Jeśli pracujesz w usłudze Azure Stack zintegrowany system i nie wiedzieć, punkt końcowy zarządzania, skontaktuj się z operatorem.

    ![Dodaj konto](./media/azure-stack-storage-connect-se/custom-environments.png)

5. Wybierz **Zaloguj**, aby nawiązać połączenie z kontem usługi Azure Stack, z którą skojarzona z co najmniej jedną aktywną subskrypcją usługi Azure Stack.



6. Wybierz subskrypcje usługi Azure Stack, z którymi chcesz pracować. Wybierz przycisk **Zastosuj**.

    ![Zarządzanie kontami](./media/azure-stack-storage-connect-se/account-management.png)

    W okienku po lewej stronie są wyświetlane wszystkie konta magazynu skojarzone z wybranymi subskrypcjami usługi Azure Stack.

    ![Lista skojarzonych subskrypcji](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Łączenie z kontem magazynu usługi Azure Stack

Możesz również nawiązać przy użyciu nazwy konta magazynu i pary kluczy konta magazynu usługi Azure Stack.

1. W lewym okienku programu storage explorer wybierz Zarządzanie kontami. Są wyświetlane wszystkie konta Microsoft, które użytkownik zalogowany.

    ![Dodaj konto](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Aby połączyć z subskrypcją usługi Azure Stack, wybierz **Dodaj konto**.

    ![Dodaj konto](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. Łączenie z usługą Azure Storage, okno dialogowe, wybierz **Użyj klucza i nazwy konta magazynu**.

4. Wprowadź nazwę konta w **nazwa konta**, Wklej klucz konta do **klucz konta** pola tekstowego, wybierz opcję **inne (podaj poniżej)** w **punkty końcowe usługi Storage domeny** i wejściowy punkt końcowy usługi Azure Stack.

    Punkt końcowy usługi Azure Stack zawiera dwie części: Nazwa regionu i domeny usługi Azure Stack. W usłudze Azure Stack Development Kit jest domyślny punkt końcowy **local.azurestack.external**. Jeśli nie masz pewności o punkt końcowy usługi, skontaktuj się z administratorem chmury.

    ![Dołącz nazwy i klucza](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Wybierz przycisk **Połącz**.
6. Po pomyślnym dołączeniu konto magazynu, konto magazynu jest wyświetlane z (**zewnętrznych, inne**) dołączonym do jego nazwy.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do Eksploratora usługi storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Magazyn usługi Azure Stack: różnice i zagadnienia](azure-stack-acs-differences.md)
* Aby dowiedzieć się więcej o usłudze Azure storage, zobacz [wprowadzenie do usługi Microsoft Azure storage](../../storage/common/storage-introduction.md)
