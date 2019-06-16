---
title: Zarządzanie poświadczenia konta magazynu StorSimple dla urządzeń z serii Microsoft Azure StorSimple 8000 | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak można strona Konfigurowanie Menedżera urządzeń StorSimple służy do dodawania, edytowania, usuwania lub wymiany kluczy zabezpieczeń dla konta magazynu.
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
ms.openlocfilehash: 53aa442b86f5c82ded2f212a64f43852e6b3d2c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60632777"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Usługa Menedżer urządzeń StorSimple do zarządzania poświadczeń konta magazynu

## <a name="overview"></a>Omówienie

**Konfiguracji** sekcji w bloku usługi Menedżer urządzeń StorSimple przedstawia wszystkie parametry usługi global service, które mogą być tworzone w usłudze Menedżer urządzeń StorSimple. Te parametry mogą być stosowane do wszystkich urządzeń podłączonych do usługi i obejmują:

* Poświadczenia konta magazynu
* Szablony przepustowości 
* Rekordy kontroli dostępu 

W tym samouczku wyjaśniono, jak dodawanie, edytowanie, usuwanie poświadczeń konta magazynu lub wymiany kluczy zabezpieczeń dla konta magazynu.

 ![Lista poświadczeń konta magazynu](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Konta magazynu zawierają poświadczenia, których urządzenie StorSimple korzysta z dostępu do konta magazynu z dostawcy usług w chmurze. Dla kont usługi Microsoft Azure storage są to poświadczenia, takie jak nazwa konta i podstawowy klucz dostępu. 

Na **poświadczeń konta magazynu** bloku, wszystkie konta magazynu, utworzone na potrzeby rozliczeń subskrypcji są wyświetlane w formacie tabelarycznym, zawierający następujące informacje:

* **Nazwa** — unikatowa nazwa przypisana do konta, gdy został on utworzony.
* **Włączony protokół SSL** — czy protokół SSL jest włączony i urządzenia do chmury komunikacja odbywa się za pośrednictwem bezpiecznego kanału.
* **Używane przez** — liczba woluminów przy użyciu konta magazynu.

Najbardziej typowe zadania związane z kont magazynu, które mogą być wykonywane są następujące:

* Dodawanie konta magazynu 
* Edytuj konto magazynu 
* Usuwanie konta magazynu 
* Wymiana kluczy kont magazynu 

## <a name="types-of-storage-accounts"></a>Typy kont magazynu

Istnieją trzy typy kont magazynu, które mogą być używane z urządzeniem StorSimple.

* **Konta magazynu generowanych automatycznie** — jak sama nazwa wskazuje ten typ konta magazynu jest generowana automatycznie podczas tworzenia usługi. Aby dowiedzieć się więcej na temat sposobu tworzenia tego konta magazynu, zobacz [krok 1: Utwórz nową usługę](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) w [wdrażanie urządzenia StorSimple w środowisku lokalnym](storsimple-8000-deployment-walkthrough-u2.md). 
* **Konta magazynu w ramach subskrypcji usługi** — są to konta usługi Azure storage, które są skojarzone z tej samej subskrypcji, jak w przypadku usługi. Aby dowiedzieć się więcej na temat używania tych magazynu konta są tworzone, zobacz [o kontach magazynu Azure](../storage/common/storage-create-storage-account.md). 
* **Konta magazynu poza subskrypcją usługi** — są to konta usługi Azure storage, które nie są skojarzone z Twoją usługą i prawdopodobnie istniały przed usługa została utworzona.

## <a name="add-a-storage-account"></a>Dodawanie konta magazynu

Możesz dodać konto magazynu, podając unikatową przyjazną nazwę i poświadczenia dostępu, które są połączone z kontem usługi storage (za pomocą dostawcy usług w chmurze określona). Masz również opcję włączania trybu protokołu secure sockets layer (SSL), aby utworzyć bezpieczny kanał komunikacji sieciowej między urządzeniem i chmurą.

Można utworzyć wiele kont, dla danej chmury dostawcy usług. Należy jednak pamiętać, że po utworzeniu konta magazynu nie można zmienić dostawcy usług w chmurze.

Gdy są zapisywane na koncie magazynu, usługa próbuje komunikować się z dostawcą usług w chmurze. Poświadczenia i dostęp do materiałów, którą podano będzie odbywać się w tej chwili. Konto magazynu jest tworzony tylko wtedy, gdy uwierzytelnienie zakończy się pomyślnie. Jeśli uwierzytelnianie zakończy się niepowodzeniem, następnie pojawi się odpowiedni komunikat o błędzie.

Aby dodać poświadczenia konta usługi Azure storage, należy użyć poniższych procedur:

* Aby dodać poświadczeń konta magazynu, który ma tej samej subskrypcji platformy Azure jako usługi Menedżer urządzeń
* Aby dodać poświadczeń konta magazynu platformy Azure, który znajduje się poza subskrypcją usługi Menedżer urządzeń

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Aby dodać poświadczeń konta magazynu platformy Azure poza subskrypcją usługi Menedżer urządzeń StorSimple

1. Przejdź do usługi Menedżer urządzeń StorSimple, wybierz i kliknij go dwukrotnie. Spowoduje to otwarcie **Przegląd** bloku.
2. Wybierz **poświadczeń konta magazynu** w ramach **konfiguracji** sekcji. Ta lista zawiera wszystkie istniejące poświadczenia konta magazynu skojarzone z usługą Menedżera urządzeń StorSimple.
3. Kliknij pozycję **Add** (Dodaj).
4. W **Dodawanie poświadczeń konta magazynu** blok, wykonaj następujące czynności:
   
    1. Aby uzyskać **subskrypcji**, wybierz opcję **innych**.
   
    2. Podaj nazwę użytkownika poświadczeń konta magazynu platformy Azure.
   
    3. W **klucz dostępu konta magazynu** tekst Podaj podstawowy klucz dostępu dla poświadczeń konta usługi Azure storage. Aby pobrać ten klucz, przejdź do usługi Azure Storage, wybierz swoje poświadczenia konta magazynu, a następnie kliknij przycisk **zarządzać kluczami konta**. Teraz możesz skopiować podstawowy klucz dostępu.
   
    4. Aby włączyć protokół SSL, kliknij przycisk **Włącz** przycisk, aby utworzyć bezpieczny kanał komunikacji sieciowej między chmurą a usługi Menedżer urządzeń StorSimple. Kliknij przycisk **wyłączyć** przycisk tylko wtedy, gdy pracujesz w chmurze prywatnej.
   
    5. Kliknij pozycję **Add** (Dodaj). Otrzymasz powiadomienie po pomyślnym utworzeniu poświadczenia konta magazynu.

5. Poświadczenia konta nowo utworzonego magazynu jest wyświetlana w bloku usługi Menedżer urządzeń StorSimple z skonfigurować w obszarze **poświadczeń konta magazynu**.
   


## <a name="edit-a-storage-account"></a>Edytuj konto magazynu

Możesz edytować konto magazynu, który jest używany przez kontener woluminów. Po zmodyfikowaniu konta magazynu, który jest obecnie w użyciu jedyne pole można zmodyfikować jest klucz dostępu dla konta magazynu. Można podać klucz dostępu do magazynu i Zapisz zaktualizowane ustawienia.

#### <a name="to-edit-a-storage-account"></a>Aby edytować konto magazynu

1. Przejdź do usługi Menedżer urządzeń StorSimple. W sekcji **Konfiguracja** kliknij pozycję **Poświadczenia konta magazynu**.

    ![Poświadczenia konta magazynu](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. W **poświadczeń konta magazynu** bloku, z listy poświadczeń konta magazynu, wybierz i kliknij ten, który chcesz edytować. 

3. Możesz zmodyfikować **Włącz SSL** zaznaczenia. Możesz również kliknąć **więcej...**  , a następnie wybierz **zsynchronizuj klucz dostępu, aby obrócić** klucze dostępu konta magazynu. Przejdź do [klucza obrotu kont magazynu](#key-rotation-of-storage-accounts) Aby uzyskać więcej informacji na temat sposobu wykonania wymiany kluczy. Po zmodyfikowaniu ustawień, kliknij przycisk **Zapisz**. 

    ![Zapisywanie poświadczeń konta magazynu edytowany](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. 

    ![Potwierdzenie zmiany](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

Ustawienia będą aktualizowane i zapisywane na koncie magazynu. 

## <a name="delete-a-storage-account"></a>Usuwanie konta magazynu

> [!IMPORTANT]
> Konto magazynu można usunąć tylko wtedy, gdy nie jest używany przez kontener woluminów. Jeśli konto magazynu jest używane przez kontener woluminów, należy najpierw usunąć kontenera woluminów, a następnie usuń skojarzonego konta magazynu.

#### <a name="to-delete-a-storage-account"></a>Aby usunąć konto magazynu

1. Przejdź do usługi Menedżer urządzeń StorSimple. W sekcji **Konfiguracja** kliknij pozycję **Poświadczenia konta magazynu**.

2. Tabelarycznej liście kont magazynu umieść kursor konta, które chcesz usunąć. Kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe, a następnie kliknij przycisk **Usuń**.

    ![Usuwanie poświadczeń konta magazynu](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak** aby kontynuować usuwanie. Tabelarycznej liście zostanie zaktualizowany w celu odzwierciedlenia zmian.

    ![Potwierdzenie usunięcia](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Wymiana kluczy kont magazynu

Ze względów bezpieczeństwa rotacji kluczy często jest to wymagane w centrach danych. Każda subskrypcja Microsoft Azure może mieć co najmniej jeden skojarzone konta magazynu. Dostęp do tych kont jest kontrolowana przez subskrypcję i kluczy dostępu dla każdego konta magazynu. 

Podczas tworzenia konta magazynu Microsoft Azure generuje dwa klucze dostępu do magazynu 512-bitowe, które są używane do uwierzytelniania podczas uzyskiwania dostępu do konta magazynu. Mając dwa klucze dostępu do magazynu umożliwia ponowne generowanie kluczy żadnych zakłóceń w usłudze magazynu lub dostęp do tej usługi. Klucz, który jest aktualnie używany jest *podstawowego* i klucz kopii zapasowej jest określany jako *dodatkowej* klucza. Jeden z tych dwóch kluczy muszą być dostarczone, gdy urządzenie Microsoft Azure StorSimple uzyskuje dostęp do sieci dostawcy usług magazynu w chmurze.

## <a name="what-is-key-rotation"></a>Co to jest wymiana kluczy?

Zazwyczaj aplikacje, użyj tylko jeden z kluczy dostępu do danych. Po upływie pewnego czasu program może przełączyć się do korzystania z drugiego klucza aplikacji. Po przełączeniu aplikacji do klucza pomocniczego, możesz wycofać pierwszy klucz i następnie wygenerować nowy klucz. W ten sposób za pomocą dwóch kluczy umożliwia aplikacji dostęp do danych bez żadnych przestojów.

Klucze konta magazynu zawsze są przechowywane w usłudze w postaci zaszyfrowanej. Jednak można je zresetować za pomocą usługi Menedżer urządzeń StorSimple. Usługę można uzyskać klucz podstawowy i klucz pomocniczy dla wszystkich kont magazynu w tej samej subskrypcji, w tym kont utworzonych w usłudze magazynu, a także konta magazynu domyślny wygenerowany podczas pierwszego usługi Menedżer urządzeń StorSimple utworzony. Usługa Menedżer urządzeń StorSimple będzie zawsze pobiera klucze z klasycznego portalu Azure, a następnie zapisywać je w sposób zaszyfrowany.

## <a name="rotation-workflow"></a>Obrót przepływu pracy

Administratora usługi Microsoft Azure można ponownie wygenerować lub zmienić klucz podstawowy lub pomocniczy, uzyskując bezpośrednio dostęp do konta magazynu (za pośrednictwem usługi Microsoft Azure Storage). Usługa Menedżer urządzeń StorSimple nie zobaczą tę zmianę automatycznie.

Poinformować usługi Menedżer urządzeń StorSimple zmiany, możesz będzie potrzebne do uzyskania dostępu do usługi Menedżer urządzeń StorSimple dostęp do konta magazynu, a następnie zsynchronizować klucz podstawowy lub pomocniczy (zależności od tego, co zostało zmienione). Następnie usługa pobiera najnowszego klucza, szyfruje kluczy i wysyła zaszyfrowany klucz do urządzenia.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Można zsynchronizować kluczy konta magazynu w tej samej subskrypcji co usługa 
1. Przejdź do usługi Menedżer urządzeń StorSimple. W sekcji **Konfiguracja** kliknij pozycję **Poświadczenia konta magazynu**.
2. Z tabelarycznej listy kont magazynu, kliknij ten, który chcesz zmodyfikować. 

    ![Synchronizuj klucze](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Kliknij przycisk **... Więcej** , a następnie wybierz **zsynchronizuj klucz dostępu, aby obrócić**.   

    ![Synchronizuj klucze](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. W usłudze Menedżer urządzeń StorSimple należy zaktualizować klucz, który wcześniej został zmieniony w usłudze Microsoft Azure Storage. Jeśli podstawowy klucz dostępu został zmieniony (Wygenerowano), wybierz opcję **głównej** klucza. Jeśli klucz pomocniczy został zmieniony, wybierz opcję **dodatkowej** klucza. Kliknij przycisk **Synchronizuj klucz**.
      
      ![Synchronizuj klucze](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Otrzymasz powiadomienie po klucza jest pomyślnie zsynchronizowane.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Można zsynchronizować kluczy konta magazynu poza subskrypcji usługi
1. Na **usług** kliknij **Konfiguruj** kartę.
2. Kliknij przycisk **Dodawanie/edytowanie konta magazynu**.
3. W oknie dialogowym wykonaj następujące czynności:
   
   1. Wybierz konto magazynu z kluczem dostępu, który chcesz zaktualizować.
   2. Należy zaktualizować klucz dostępu do magazynu w usłudze Menedżer urządzeń StorSimple. W tym przypadku widać klucz dostępu do magazynu. Wprowadź nowy klucz w **klucz dostępu konta magazynu** pole. 
   3. Zapisz zmiany. Teraz klucz dostępu konta magazynu powinien zostać zaktualizowany.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [zabezpieczenia usługi StorSimple](storsimple-8000-security.md).
* Dowiedz się więcej o [przy użyciu usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

