---
title: Eksplorator usługi Storage łączenia się z subskrypcji platformy Azure stosu lub konto magazynu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć Eksploratora usługi Storage z subskrypcją platformy Azure stosu
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/14/2018
ms.author: mattbriggs
ms.reviewer: xiaofmao
ms.openlocfilehash: a3a69f3cf91c0d202d54d0da5dc5fe0531ef9db1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Eksplorator usługi Storage łączenia się z subskrypcji platformy Azure stosu lub konto magazynu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

W tym artykule dowiesz się, jak łączyć się z Twojej subskrypcji platformy Azure stosu i kont magazynu za pomocą Eksploratora usługi Storage. Eksplorator usługi Storage platformy Azure jest aplikacją autonomiczną, która pozwala łatwo pracować z danymi Azure stos magazynu w systemie Windows, macOS i Linux.

>[!NOTE]
>Istnieje kilka dostępne narzędzia do przenoszenia danych do i z usługi Azure Storage stosu. Aby uzyskać więcej informacji, zobacz [narzędzia do magazynu Azure stosu transferu danych](azure-stack-storage-transfer.md).

Jeśli nie został jeszcze zainstalowany Eksploratora usługi Storage [Pobierz Eksploratora usługi Storage](http://www.storageexplorer.com/) i zainstaluj go.

Po nawiązaniu połączenia z subskrypcją platformy Azure stosu lub konto magazynu, można użyć [artykuły Eksploratora usługi Storage Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) do pracy z danymi stosu Azure. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Przygotowanie do nawiązywania połączenia z Azure stosu

Należy bezpośredni dostęp do stosu Azure lub połączenia sieci VPN dla Eksploratora usługi Storage, dostęp do subskrypcji Azure stosu. Aby dowiedzieć się, jak skonfigurować połączenie VPN do usługi Azure Stack, zapoznaj się z tematem [Connect to Azure Stack with VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) (Łączenie z usługą Azure Stack przy użyciu sieci VPN).

Azure stosu Development Kit należy wyeksportować certyfikat główny urzędu stosu Azure.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Wyeksportowanie i zaimportowanie certyfikatu Azure stosu

1. Otwórz `mmc.exe` na stos Azure komputer hosta lub komputera lokalnego z połączenia sieci VPN Azure stosu. 

2. W **pliku**, wybierz pozycję **Dodaj/Usuń przystawkę**, a następnie dodaj **certyfikaty** do zarządzania **Moje konto użytkownika**.

3. W obszarze **\Trusted Root Certification Authorities\Certificates Root\Certificated konsoli (komputer lokalny)** znaleźć **AzureStackSelfSignedRootCert**.

    ![Ładowanie certyfikatu głównego usługi Azure Stack za pośrednictwem pliku mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. Kliknij prawym przyciskiem myszy certyfikat, wybierz **wszystkie zadania** > **wyeksportować**, a następnie postępuj zgodnie z instrukcjami, aby wyeksportować certyfikat z **certyfikat x.509 szyfrowany algorytmem Base-64 (. CER)**.

    Wyeksportowany certyfikat będzie używany w następnym kroku.

5. Uruchom Eksploratora usługi Storage, i jeśli zostanie wyświetlony **nawiązywanie połączenia z usługą Azure Storage** okna dialogowego pozycję Anuluj ją.

6. Na **Edytuj** menu wskaż **certyfikaty SSL**, a następnie wybierz **importu certyfikatów**. Użyj okna dialogowego selektora plików, aby wyszukać i otworzyć certyfikat wyeksportowany w poprzednim kroku.

    Po zaimportowaniu certyfikatu, zostanie wyświetlony monit o ponowne uruchomienie Eksploratora usługi Storage.

    ![Zaimportuj certyfikat do Eksploratora usługi Storage](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. Po ponownym uruchomieniu Eksploratora usługi Storage, wybierz **Edytuj** menu, a następnie sprawdź, czy **stosu Azure docelowej** jest zaznaczone. Jeśli nie, wybierz **stosu Azure docelowego**, a następnie ponownie uruchom Eksploratora usługi Storage zmiany zaczęły obowiązywać. Ta konfiguracja jest wymagana w celu zachowania zgodności ze środowiskiem usługi Azure Stack.

    ![Sprawdzanie, czy pozycja Oznacz usługę Azure Stack jako cel została zaznaczona](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription"></a>Łączenie z subskrypcją usługi Azure Stack

Poniższe kroki umożliwiają Eksploratora usługi Storage nawiązać połączenia z subskrypcją platformy Azure stosu.

1. W okienku po lewej stronie Eksploratora usługi Storage wybierz **Zarządzanie kontami**. 
    Wyświetlane są wszystkie subskrypcji firmy Microsoft, która zalogowano.

2. Aby połączyć się z subskrypcją Azure stosu, wybierz **Dodaj konto**.

    ![Dodawanie konta usługi Azure Stack](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. W oknie łączenia z usługi Azure Storage okno dialogowe w obszarze **środowiska platformy Azure**, wybierz pozycję **Azure** lub **chińskiej wersji platformy Azure**, w zależności od używanego konta Azure stosu. Wybierz **Zaloguj** do logowania się na konto Azure stosu skojarzoną z co najmniej jedną aktywną subskrypcją Azure stosu.

    ![Łączenie się z magazynem Azure](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Po pomyślnym zalogowaniu się przy użyciu konta usługi Azure Stack w okienku po lewej stronie zostaną wyświetlone wszystkie subskrypcje usługi Azure Stack skojarzone z tym kontem. Wybierz subskrypcje usługi Azure Stack, z którymi chcesz pracować, a następnie wybierz przycisk **Zastosuj**. (Zaznaczenie lub usunięcie zaznaczenia pola wyboru **Wszystkie subskrypcje** powoduje przełączenie między wyświetlaniem wszystkich lub żadnej z wymienionych subskrypcji usługi Azure Stack).

    ![Wybieranie subskrypcji usługi Azure Stack po wypełnieniu pól okna dialogowego Środowisko niestandardowe chmury](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    W okienku po lewej stronie są wyświetlane wszystkie konta magazynu skojarzone z wybranymi subskrypcjami usługi Azure Stack.

    ![Lista kont magazynu, w tym kont subskrypcji usługi Azure Stack](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Połącz się z kontem magazynu Azure stosu

Można również nawiązać konta magazynu Azure stosu przy użyciu nazwy konta magazynu i parę kluczy.

1. W okienku po lewej stronie Eksploratora usługi Storage wybierz Zarządzanie kontami. Są wyświetlane wszystkie konta Microsoft, które użytkownik zalogowany.

    ![Dodaj konto](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Aby połączyć się z subskrypcją Azure stosu, wybierz **Dodaj konto**.

    ![Dodaj konto](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. Łączenie z usługą Azure Storage, okno dialogowe, wybierz **użyć nazwy konta magazynu i klucza**.

4. Wprowadź nazwę konta w **nazwa konta**, Wklej klucz konta do **klucz konta** pole tekstowe, wybierz opcję **innych (podaj poniżej)** w **punkty końcowe usługi Storage domeny** i wejściowego punktu końcowego stosu Azure.

    Punkt końcowy stosu Azure zawiera dwie części: Nazwa regionu i domena stosu Azure. W zestawie Azure stosu Development Kit jest domyślny punkt końcowy **local.azurestack.external**. Jeśli nie wiesz o punktu końcowego, skontaktuj się z administratorem chmury.

    ![Dołącz nazwy i klucza](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Wybierz przycisk **Połącz**.
6. Po pomyślnym dołączeniu konto magazynu, konta magazynu są wyświetlane z (**zewnętrznych, inne**) dołączonym do jego nazwy.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do Eksploratora usługi Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Magazynu Azure stosu: różnice i zagadnienia dotyczące](azure-stack-acs-differences.md)
* Aby dowiedzieć się więcej na temat usługi Azure Storage, zobacz [wprowadzenie do usługi Magazyn Microsoft Azure](../../storage/common/storage-introduction.md)
