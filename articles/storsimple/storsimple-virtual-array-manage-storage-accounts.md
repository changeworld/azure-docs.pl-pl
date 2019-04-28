---
title: Zarządzanie poświadczeniami konta magazynu StorSimple Virtual Array | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak można użyć strony konfiguracji Menedżera urządzeń StorSimple można dodawać, edytować, usunąć lub wymiany kluczy zabezpieczeń dla poświadczeń konta magazynu skojarzone z rozwiązania StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: a992851deda0659509c0ee4ea5de76b19734f017
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128842"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Menedżer urządzeń StorSimple Użyj zarządzania poświadczeń konta magazynu dla macierzy wirtualnej StorSimple

## <a name="overview"></a>Omówienie
**Konfiguracji** części bloku usługi Menedżer urządzeń StorSimple z rozwiązania StorSimple Virtual Array przedstawia parametry usługi global service, które mogą być tworzone w usłudze StorSimple Manager. Te parametry mogą być stosowane do wszystkich urządzeń podłączonych do usługi i obejmują:

* Poświadczenia konta magazynu
* Rekordy kontroli dostępu
  
  ![Pulpit nawigacyjny usługi Menedżer urządzeń](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

W tym samouczku wyjaśniono, jak można dodawać, edytować lub usunąć poświadczenia konta magazynu dla rozwiązania StorSimple Virtual Array. Informacje przedstawione w tym samouczku dotyczą tylko macierzy wirtualnej StorSimple. Aby uzyskać informacje na temat sposobu zarządzania kontami magazynu, w serii 8000, zobacz [Zarządzanie kontem magazynu przy użyciu usługi StorSimple Manager](storsimple-manage-storage-accounts.md).

Poświadczenia konta magazynu zawierają poświadczenia, które jest wykorzystywany do uzyskania dostępu do konta magazynu z dostawcy usług w chmurze. Dla kont usługi Microsoft Azure storage są to poświadczenia, takie jak nazwa konta i podstawowy klucz dostępu.

Na **poświadczeń konta magazynu** bloku, wszystkie poświadczenia konta magazynu, utworzone na potrzeby rozliczeń subskrypcji są wyświetlane w formacie tabelarycznym, zawierający następujące informacje:

* **Nazwa** — unikatowa nazwa przypisana do konta, gdy został on utworzony.
* **Włączony protokół SSL** — czy protokół SSL jest włączony i urządzenia do chmury komunikacja odbywa się za pośrednictwem bezpiecznego kanału.
  
  ![Sekcja konfiguracji](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

Najbardziej typowe zadania związane z poświadczeń konta magazynu, które mogą być wykonywane na **poświadczeń konta magazynu** bloku są:

* Dodawanie poświadczeń konta magazynu
* Edytuj poświadczenia konta magazynu
* Usuwanie poświadczeń konta magazynu

## <a name="types-of-storage-account-credentials"></a>Typy poświadczeń konta magazynu
Istnieją trzy typy poświadczeń konta magazynu, które mogą być używane z urządzeniem StorSimple.

* **Poświadczenia konta magazynu generowanych automatycznie** — jak sama nazwa wskazuje ten typ poświadczeń konta magazynu jest generowana automatycznie podczas tworzenia usługi. Aby dowiedzieć się więcej na temat sposobu tworzenia tych poświadczeń konta magazynu, zobacz [Utwórz nową usługę](storsimple-virtual-array-manage-service.md#create-a-service).
* **poświadczenia konta magazynu w ramach subskrypcji usługi** — są to poświadczenia konta magazynu platformy Azure, które są skojarzone z tą samą subskrypcją, jak w przypadku usługi. Aby dowiedzieć się więcej na temat używania tych magazynu poświadczeń konta są tworzone, zobacz [o kontach magazynu Azure](../storage/common/storage-create-storage-account.md).
* **poświadczenia konta magazynu poza subskrypcją usługi** — są to poświadczenia konta magazynu platformy Azure, które nie są skojarzone z Twoją usługą i prawdopodobnie istniały przed usługa została utworzona.

## <a name="add-a-storage-account-credential"></a>Dodawanie poświadczeń konta magazynu
Do konfiguracji usługi Menedżer urządzeń StorSimple można dodać poświadczeń konta magazynu, podając unikatową przyjazną nazwę i poświadczenia dostępu, które są połączone z kontem magazynu. Masz również opcję włączania trybu protokołu secure sockets layer (SSL), aby utworzyć bezpieczny kanał komunikacji sieciowej między urządzeniem i chmurą.

Można utworzyć wiele kont, dla danej chmury dostawcy usług. Gdy trwa zapisywanie poświadczeń konta magazynu, usługa próbuje komunikować się z dostawcą usług w chmurze. Poświadczenia i dostęp do materiałów, którą podano są uwierzytelniani w tej chwili. Poświadczenia konta magazynu jest tworzony tylko wtedy, gdy uwierzytelnienie zakończy się pomyślnie. W przypadku niepowodzenia uwierzytelniania, wyświetlany jest komunikat odpowiedni komunikat o błędzie.

Aby dodać poświadczenia konta usługi Azure storage, należy użyć poniższych procedur:

* Aby dodać poświadczeń konta magazynu, który ma tej samej subskrypcji platformy Azure jako usługi Menedżer urządzeń
* Aby dodać poświadczeń konta magazynu platformy Azure, który znajduje się poza subskrypcją usługi Menedżer urządzeń

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Aby dodać poświadczeń konta magazynu, który ma tej samej subskrypcji platformy Azure jako usługi Menedżer urządzeń

1. Przejdź do usługi Menedżer urządzeń, wybierz i kliknij ją dwukrotnie. Spowoduje to otwarcie **Przegląd** bloku.
2. Wybierz **poświadczeń konta magazynu** w ramach **konfiguracji** sekcji.
3. Kliknij pozycję **Add** (Dodaj).
4. W **Dodaj konto magazynu** blok, wykonaj następujące czynności:
   
    1. Aby uzyskać **subskrypcji**, wybierz opcję **bieżącego**.
    2. Podaj nazwę konta usługi Azure storage.
    3. Wybierz **Włącz** tworzy bezpieczny kanał komunikacji sieciowej między chmurą a urządzeniem StorSimple. Wybierz **wyłączyć** tylko wtedy, gdy pracujesz w chmurze prywatnej.
    4. Kliknij pozycję **Add** (Dodaj). Otrzymasz powiadomienie po pomyślnym utworzeniu konta magazynu.<br></br>
   
        ![Dodawanie istniejących poświadczeń konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Aby dodać poświadczeń konta magazynu platformy Azure, który znajduje się poza subskrypcją usługi Menedżer urządzeń

1. Przejdź do usługi Menedżer urządzeń, wybierz i kliknij ją dwukrotnie. Spowoduje to otwarcie **Przegląd** bloku.
2. Wybierz **poświadczeń konta magazynu** w ramach **konfiguracji** sekcji. Ta lista zawiera wszystkie istniejące poświadczenia konta magazynu skojarzone z usługą Menedżera urządzeń StorSimple.
3. Kliknij pozycję **Add** (Dodaj).
4. W **Dodaj konto magazynu** blok, wykonaj następujące czynności:
   
    1. Aby uzyskać **subskrypcji**, wybierz opcję **innych**.
   
    2. Podaj nazwę użytkownika poświadczeń konta magazynu platformy Azure.
   
    3. W **klucz dostępu konta magazynu** tekst Podaj podstawowy klucz dostępu dla poświadczeń konta usługi Azure storage. Aby pobrać ten klucz, przejdź do usługi Azure Storage, wybierz swoje poświadczenia konta magazynu, a następnie kliknij przycisk **zarządzać kluczami konta**. Teraz możesz skopiować podstawowy klucz dostępu.
   
    4. Aby włączyć protokół SSL, kliknij przycisk **Włącz** przycisk, aby utworzyć bezpieczny kanał komunikacji sieciowej między chmurą a usługi Menedżer urządzeń StorSimple. Kliknij przycisk **wyłączyć** przycisk tylko wtedy, gdy pracujesz w chmurze prywatnej.
   
    5. Kliknij pozycję **Add** (Dodaj). Otrzymasz powiadomienie po pomyślnym utworzeniu poświadczenia konta magazynu.

5. Poświadczenia konta nowo utworzonego magazynu jest wyświetlana w bloku usługi Menedżer urządzeń StorSimple z skonfigurować w obszarze **poświadczeń konta magazynu**.
   
    ![Dodawanie poświadczeń konta magazynu, poza subskrypcją usługi Menedżer urządzeń](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Edytuj poświadczenia konta magazynu
Możesz edytować poświadczeń konta magazynu używanego przez urządzenie. Po zmodyfikowaniu poświadczeń konta magazynu, który jest obecnie w użyciu dostępnych do modyfikowania pól są kluczem dostępu i tryb SSL dla poświadczeń konta magazynu. Możesz podać klucz dostępu do magazynu, lub zmodyfikować **tryb Włącz SSL** wyboru i Zapisz zaktualizowane ustawienia.

#### <a name="to-edit-a-storage-account-credential"></a>Aby edytować poświadczeń konta magazynu
1. Przejdź do usługi Menedżer urządzeń, wybierz i kliknij ją dwukrotnie. Spowoduje to otwarcie **Przegląd** bloku.
2. Wybierz **poświadczeń konta magazynu** w ramach **konfiguracji** sekcji. Ta lista zawiera wszystkie istniejące poświadczenia konta magazynu skojarzone z usługą Menedżera urządzeń StorSimple.
3. Na tabelarycznej liście poświadczeń konta magazynu wybierz pozycję, a następnie kliknij dwukrotnie konto które chcesz zmodyfikować.
4. W przypadku poświadczeń konta magazynu **właściwości** blok, wykonaj następujące czynności:
   
   1. Jeśli to konieczne, można zmodyfikować **Włącz SSL** wybór trybu.
   2. Można ponownie wygenerować klucze dostępu do poświadczeń konta magazynu. Aby uzyskać więcej informacji, zobacz [ponowne generowanie kluczy konta magazynu](../storage/common/storage-account-manage.md#access-keys). Podaj nowy klucz poświadczeń konta magazynu. Konto usługi Azure storage to podstawowy klucz dostępu.
   3. Kliknij przycisk **Zapisz** w górnej części **właściwości** bloku, aby zapisać ustawienia. Ustawienia są aktualne na **poświadczeń konta magazynu** bloku.
      
      ![Edytuj poświadczenia konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Usuwanie poświadczeń konta magazynu
> [!IMPORTANT]
> Tylko wtedy, gdy nie jest używany, można usunąć poświadczeń konta magazynu. Jeśli poświadczenia konta magazynu jest używany, otrzymasz powiadomienie.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Można usunąć poświadczeń konta magazynu
1. Przejdź do usługi Menedżer urządzeń, wybierz i kliknij ją dwukrotnie. Spowoduje to otwarcie **Przegląd** bloku.
2. Wybierz **poświadczeń konta magazynu** w ramach **konfiguracji** sekcji. Ta lista zawiera wszystkie istniejące poświadczenia konta magazynu skojarzone z usługą Menedżera urządzeń StorSimple.
3. Na tabelarycznej liście poświadczeń konta magazynu wybierz pozycję, a następnie kliknij dwukrotnie konto które chcesz usunąć.
4. W przypadku poświadczeń konta magazynu **właściwości** blok, wykonaj następujące czynności:
   
   1. Kliknij przycisk **Usuń** można usunąć poświadczeń.
   2. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak** aby kontynuować usuwanie. Tabelarycznej liście jest aktualizowana w celu odzwierciedlenia zmian.
      
      ![Usuwanie poświadczeń konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Synchronizowanie kluczy dostępu do poświadczeń konta magazynu
Ze względów bezpieczeństwa rotacji kluczy często jest to wymagane w centrach danych. Administratora usługi Microsoft Azure można ponownie wygenerować lub zmienić klucz podstawowy lub pomocniczy, uzyskując bezpośrednio dostęp do poświadczeń konta magazynu (za pośrednictwem usługi Microsoft Azure Storage). Usługa Menedżer urządzeń StorSimple nie zobaczą tę zmianę automatycznie.

Informowanie usługi Menedżer urządzeń StorSimple zmiany, należy uzyskać dostęp do usługi Menedżer urządzeń StorSimple dostęp do poświadczeń konta magazynu, a następnie zsynchronizować klucz podstawowy lub pomocniczy (zależności od tego, co zostało zmienione). Następnie usługa pobiera najnowszego klucza, szyfruje kluczy i wysyła zaszyfrowany klucz do urządzenia.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Można zsynchronizować kluczy dla poświadczeń konta magazynu w tej samej subskrypcji, jako usługa (tylko Azure)
1. W bloku docelowej usługi, wybierz usługę, kliknij dwukrotnie nazwę usługi, a następnie w polu **konfiguracji** , kliknij przycisk **poświadczeń konta magazynu**.
2. Na **poświadczeń konta magazynu** bloku na liście poświadczeń konta magazynu, wybierz konto magazynu poświadczeń, których klucze, które mają być synchronizowane.
3. W **właściwości** bloku wybrany magazyn poświadczeń konta, wykonaj następujące czynności:
   
    1. Kliknij przycisk **więcej**, a następnie kliknij przycisk **zsynchronizuj klucz dostępu**.
   
    2. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **Synchronizuj klucz** na ukończenie synchronizacji.
    
4. W usłudze Menedżer urządzeń StorSimple należy zaktualizować klucz, który wcześniej został zmieniony w usłudze Microsoft Azure Storage. W **klucza konta magazynu Synchronize** bloku, jeśli podstawowy klucz dostępu został zmieniony (Wygenerowano), kliknij pozycję podstawowy, a następnie kliknij **Synchronizuj klucz**. Jeśli klucz pomocniczy został zmieniony, kliknij przycisk **dodatkowej**, a następnie kliknij przycisk **Synchronizuj klucz**.
   
    ![Synchronizuj klucz dostępu](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [administrowania rozwiązania StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

