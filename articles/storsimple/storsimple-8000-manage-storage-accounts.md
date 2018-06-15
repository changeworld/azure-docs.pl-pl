---
title: Zarządzanie poświadczeń konta magazynu StorSimple dla urządzeń z serii StorSimple 8000 z aktualizacją Microsoft Azure | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak można użyć strony konfiguracji Menedżera urządzeń StorSimple do dodawania, edytowania, usuwania, lub Obróć kluczy zabezpieczeń dla konta magazynu.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 36058ad69ea670998b50cf9038741c294a5b79ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875130"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Umożliwia zarządzanie poświadczeń konta magazynu usługi Menedżer StorSimple urządzenia

## <a name="overview"></a>Omówienie

**Konfiguracji** części bloku usługi Menedżer StorSimple urządzenia przedstawia wszystkie parametry usługi global service, które można utworzyć w usłudze Menedżer StorSimple urządzenia. Te parametry można zastosować do wszystkich urządzeń podłączonych do usługi i obejmują:

* Poświadczenia konta magazynu
* Szablony przepustowości 
* Rekordy kontroli dostępu 

W tym samouczku wyjaśniono, jak dodać, edytować, lub usuń poświadczeń konta magazynu lub Obróć kluczy zabezpieczeń dla konta magazynu.

 ![Lista poświadczeń konta magazynu](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Konta magazynu zawierają poświadczenia używane przez urządzenia StorSimple do uzyskania dostępu do konta magazynu z dostawcy usług w chmurze. W przypadku kont magazynu Microsoft Azure są to poświadczenia, takie jak nazwa konta i podstawowy klucz dostępu. 

Na **poświadczeń konta magazynu** bloku, wszystkie konta magazynu utworzone na potrzeby rozliczeń subskrypcji są wyświetlane w formacie tabelarycznym zawierający następujące informacje:

* **Nazwa** — unikatowa nazwa przypisana do konta, jeśli został on utworzony.
* **Włączony protokół SSL** — czy SSL jest włączona i urządzenia do chmury komunikacja odbywa się za pośrednictwem bezpiecznego kanału.
* **Używane przez** — liczba woluminów przy użyciu konta magazynu.

Najbardziej typowe zadania związane z kont magazynu, które mogą być wykonywane są:

* Dodawanie konta magazynu 
* Edytuj konto magazynu 
* Usuwanie konta magazynu 
* Rotacją kluczy kont magazynu 

## <a name="types-of-storage-accounts"></a>Typy kont magazynu

Istnieją trzy typy kont magazynu, które mogą być używane z urządzenia StorSimple.

* **Konta magazynu automatycznie generowanej** — zgodnie z sugestią, nazwa, ten typ konta magazynu jest generowana automatycznie po utworzeniu usługi. Aby dowiedzieć się więcej na temat tworzenia tego konta magazynu, zobacz [krok 1: Tworzenie nowej usługi](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) w [wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md). 
* **Konta magazynu w ramach subskrypcji usługi** — są to konta magazynu platformy Azure, które są skojarzone z tej samej subskrypcji co usługa. Aby dowiedzieć się więcej, Magazyn, jak te informacje są tworzone konta, zobacz [o kontach magazynu Azure](../storage/common/storage-create-storage-account.md). 
* **Konta magazynu poza subskrypcji usługi** — są to konta magazynu platformy Azure, które nie są skojarzone z Twoją usługą i prawdopodobnie istniały przed usługa została utworzona.

## <a name="add-a-storage-account"></a>Dodawanie konta magazynu

Można dodać konto magazynu, podając unikatową przyjazną nazwę i poświadczenia dostępu, które są połączone z kontem magazynu (określonej chmury dostawcy usług). Masz również opcję włączenia trybu bezpiecznego sockets layer (SSL), aby utworzyć bezpiecznego kanału komunikacji sieciowej między urządzeniem i chmury.

Można utworzyć wiele kont dla określonej chmury dostawcy usług. Należy jednak pamiętać, że po utworzeniu konta magazynu nie można zmienić dostawcy usług w chmurze.

Przy zapisywaniu konta magazynu, usługa próbuje nawiązać połączenia z dostawcą usług w chmurze. W tej chwili będą uwierzytelniane poświadczenia i materiały dostępu, który został dostarczony. Konto magazynu jest tworzony tylko wtedy, gdy uwierzytelnienie zakończy się pomyślnie. Jeśli uwierzytelnianie nie powiedzie się, następnie pojawi się odpowiedni komunikat o błędzie.

Aby dodać poświadczeń konta magazynu platformy Azure, należy użyć poniższych procedur:

* Aby dodać poświadczeniami konta magazynu, które mają tej samej subskrypcji platformy Azure jako usługa Menedżera urządzeń
* Aby dodać poświadczenia konta usługi Azure storage znajduje się poza subskrypcji usługi Menedżera urządzeń

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Aby dodać poświadczeń dla konta magazynu Azure poza subskrypcji usługi Menedżer StorSimple urządzenia

1. Przejdź do usługi Menedżer StorSimple urządzenia, wybierz opcję i kliknij ją dwukrotnie. Spowoduje to otwarcie **omówienie** bloku.
2. Wybierz **poświadczeń konta magazynu** w **konfiguracji** sekcji. Ta lista zawiera wszystkie istniejących poświadczeń konta magazynu skojarzone z usługą Menedżera urządzeń StorSimple.
3. Kliknij pozycję **Dodaj**.
4. W **dodać poświadczeń konta magazynu** blok, wykonaj następujące czynności:
   
    1. Aby uzyskać **subskrypcji**, wybierz pozycję **innych**.
   
    2. Podaj nazwę Twoje poświadczenia konta magazynu platformy Azure.
   
    3. W **klucz dostępu do konta magazynu** tekst Podaj podstawowy klucz dostępu dla Twoje poświadczenia konta magazynu platformy Azure. Aby uzyskać ten klucz, przejdź do usługi Azure Storage, wybierz Twoje poświadczenia konta magazynu, a następnie kliknij przycisk **zarządzania kluczami konta**. Można teraz skopiuj podstawowy klucz dostępu.
   
    4. Aby włączyć protokół SSL, kliknij przycisk **włączyć** przycisk, aby utworzyć bezpieczny kanał komunikacji sieciowej między usługą Menedżer urządzenia StorSimple i chmury. Kliknij przycisk **wyłączyć** przycisk tylko wtedy, gdy pracujesz w chmurze prywatnej.
   
    5. Kliknij pozycję **Dodaj**. Użytkownik jest powiadamiany po pomyślnym utworzeniu poświadczeń konta magazynu.

5. Nowo utworzonego magazynu poświadczeń konta jest wyświetlany w bloku usługi StorSimple Konfigurowanie Menedżera urządzeń, w obszarze **poświadczeń konta magazynu**.
   


## <a name="edit-a-storage-account"></a>Edytuj konto magazynu

Można edytować konto magazynu, który jest używany przez kontener woluminów. Po zmodyfikowaniu konta magazynu, który jest obecnie używany tylko pole umożliwia modyfikację jest klucz dostępu dla konta magazynu. Możesz podać nowy klucz dostępu do magazynu i Zapisz zaktualizowane ustawienia.

#### <a name="to-edit-a-storage-account"></a>Aby edytować konto magazynu

1. Przejdź do usługi Menedżer urządzeń StorSimple. W sekcji **Konfiguracja** kliknij pozycję **Poświadczenia konta magazynu**.

    ![Poświadczenia konta magazynu](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. W **poświadczeń konta magazynu** bloku, z listy poświadczeń konta magazynu, wybierz i kliknij ten, który chcesz edytować. 

3. Można zmodyfikować **Włącz SSL** zaznaczenia. Możesz również kliknąć **więcej...**  , a następnie wybierz **synchronizacji klucz dostępu do obracania** kluczy dostępu konta magazynu. Przejdź do [klucza obrotu kont magazynu](#key-rotation-of-storage-accounts) Aby uzyskać więcej informacji na temat sposobu wykonywania rotacją kluczy. Po zmodyfikowaniu ustawień, kliknij przycisk **zapisać**. 

    ![Zapisywanie poświadczeń konta magazynu edytowany](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. 

    ![Potwierdzenie zmiany](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

Ustawienia będą aktualizowane i zapisywane dla konta magazynu. 

## <a name="delete-a-storage-account"></a>Usuwanie konta magazynu

> [!IMPORTANT]
> Konto magazynu można usunąć tylko wtedy, gdy nie jest on używany przez kontener woluminów. Jeśli konto magazynu jest używana przez kontener woluminów, należy najpierw usunąć kontener woluminów, a następnie usuń skojarzonego konta magazynu.

#### <a name="to-delete-a-storage-account"></a>Aby usunąć konto magazynu

1. Przejdź do usługi Menedżer urządzeń StorSimple. W sekcji **Konfiguracja** kliknij pozycję **Poświadczenia konta magazynu**.

2. Na liście tabelarycznym kont magazynu umieść kursor nad konta, które chcesz usunąć. Kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowego, a następnie kliknij przycisk **usunąć**.

    ![Usuń poświadczenia konta magazynu](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak** aby kontynuować usuwanie. Tabelarycznej listę zostaną zaktualizowane, aby odzwierciedlić zmiany.

    ![Potwierdzenie usunięcia](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Rotacją kluczy kont magazynu

Ze względów bezpieczeństwa klucza obrotu często jest to wymagane w centrach danych. Każda subskrypcja Microsoft Azure może mieć co najmniej jeden skojarzone konta magazynu. Dostęp do tych kont jest kontrolowana przez klucze dostępu i subskrypcji dla każdego konta magazynu. 

Podczas tworzenia konta magazynu Microsoft Azure generuje dwa klucze dostępu do magazynu 512-bitowe, które są używane do uwierzytelniania podczas uzyskiwania dostępu do konta magazynu. Mając dwa klucze dostępu do magazynu umożliwia ponowne generowanie kluczy nie przerw w usłudze magazynu i dostęp do tej usługi. Klucz, który jest obecnie używany jest *głównej* i klucz kopii zapasowej jest określany jako *dodatkowej* klucza. Gdy urządzenia Microsoft Azure StorSimple uzyskuje dostęp do dostawcy usługi w chmurze magazynu należy podać jedną z tych dwóch kluczy.

## <a name="what-is-key-rotation"></a>Co to jest rotacją kluczy?

Zazwyczaj aplikacje korzystają tylko jeden z kluczy dostępu do danych. Po upływie pewnego czasu program może przełączyć się do przy użyciu drugi klucz aplikacji. Po przełączeniu aplikacji klucza pomocniczego, można wycofać pierwszy klucz, a następnie wygeneruj nowy klucz. W ten sposób za pomocą dwóch kluczy umożliwia aplikacji dostęp do danych bez jakiegokolwiek przestoju.

Klucze konta magazynu zawsze są przechowywane w usłudze w postaci zaszyfrowanej. Jednak te można zresetować za pomocą usługi Menedżera urządzeń StorSimple. Usługę można uzyskać klucza podstawowego i dodatkowego klucza dla wszystkich kont magazynu w tej samej subskrypcji, w tym kont utworzonych w usłudze magazynowania, a także domyślne konta magazynu generowane, gdy usługa Menedżer urządzeń StorSimple najpierw utworzyć. Usługę Menedżer StorSimple urządzenia będzie zawsze pobiera klucze z klasycznego portalu Azure i przechowywać je w sposób zaszyfrowane.

## <a name="rotation-workflow"></a>Obracanie przepływu pracy

Administratora programu Microsoft Azure można ponownie wygenerować lub zmienić klucz podstawowy lub pomocniczy, uzyskując bezpośrednio dostęp do konta magazynu (za pomocą usługi Microsoft Azure Storage). Usługę Menedżer StorSimple urządzenia nie widzi automatycznie tej zmiany.

Informują usługę Menedżer StorSimple urządzenia zmiany, można będzie muszą uzyskać dostęp do usługi Menedżer urządzeń StorSimple, uzyskać dostęp do konta magazynu, a następnie zsynchronizować klucz podstawowy lub pomocniczy (w zależności od tego, co zostało zmienione). Następnie usługa pobiera najnowszego klucza, szyfruje kluczy i wysyła zaszyfrowany klucz do urządzenia.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Aby zsynchronizować klucze kont magazynu w tej samej subskrypcji co usługa 
1. Przejdź do usługi Menedżer urządzeń StorSimple. W sekcji **Konfiguracja** kliknij pozycję **Poświadczenia konta magazynu**.
2. Tabelarycznej listę kont magazynu, kliknij ten, który chcesz zmodyfikować. 

    ![Synchronizowanie kluczy](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Kliknij przycisk **... Więcej** , a następnie wybierz **synchronizacji klucz dostępu do obracania**.   

    ![Synchronizowanie kluczy](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. W usłudze Menedżer urządzeń StorSimple należy zaktualizować klucz, który wcześniej został zmieniony w usłudze Microsoft Azure Storage. Jeśli podstawowy klucz dostępu została zmieniona (wygenerowano ponownie), wybierz **głównej** klucza. Jeśli klucz pomocniczy został zmieniony, wybierz **dodatkowej** klucza. Kliknij przycisk **klucza synchronizacji**.
      
      ![Synchronizowanie kluczy](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Po klucz został pomyślnie sycnhronized, otrzymasz powiadomienie.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Aby zsynchronizować klucze kont magazynu poza subskrypcji usługi
1. Na **usług** kliknij przycisk **Konfiguruj** kartę.
2. Kliknij przycisk **Dodawanie/edytowanie konta magazynu**.
3. W oknie dialogowym wykonaj następujące czynności:
   
   1. Wybierz konto magazynu z kluczem dostępu, który chcesz zaktualizować.
   2. Należy zaktualizować klucz dostępu do magazynu w usłudze Menedżer StorSimple urządzenia. W takim przypadku można wyświetlić klucz dostępu do magazynu. Wprowadź nowy klucz w **klucz dostępu do konta magazynu** pole. 
   3. Zapisz zmiany. Należy teraz zaktualizować klucz dostępu konta magazynu.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zabezpieczenia usługi StorSimple](storsimple-8000-security.md).
* Dowiedz się więcej o [przy użyciu usługi Menedżer StorSimple urządzenia do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

