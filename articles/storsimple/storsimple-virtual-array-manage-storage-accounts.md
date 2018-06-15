---
title: Zarządzanie poświadczeń konta magazynu tablicy wirtualnego StorSimple | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak umożliwia strony konfiguracji Menedżera urządzeń StorSimple dodać, edytować, usuwać lub Obróć kluczy zabezpieczeń dla poświadczeń konta magazynu skojarzone z tablicy wirtualne StorSimple.
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
ms.openlocfilehash: a4ce2d329d0e1399cffaf886adf2b95e34b9cd7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876985"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Użyj Menedżera urządzeń StorSimple do zarządzania poświadczeń konta magazynu dla macierzy wirtualnego StorSimple

## <a name="overview"></a>Omówienie
**Konfiguracji** części bloku usługi Menedżer StorSimple urządzenia macierzy wirtualnego StorSimple przedstawia parametry usługi global service, które mogą zostać utworzone w usłudze Menedżer StorSimple. Te parametry można zastosować do wszystkich urządzeń podłączonych do usługi i obejmują:

* Poświadczenia konta magazynu
* Rekordy kontroli dostępu
  
  ![Pulpit nawigacyjny usługi Menedżera urządzeń](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

W tym samouczku wyjaśniono, jak można dodać, edytować lub usunąć poświadczeń konta magazynu dla macierzy wirtualne StorSimple. Informacje przedstawione w tym samouczku dotyczą tylko tablicy wirtualne StorSimple. Aby uzyskać informacje na temat zarządzania kont magazynu w serii 8000, zobacz [Zarządzanie kontem magazynu za pomocą usługi Menedżer StorSimple](storsimple-manage-storage-accounts.md).

Poświadczenia konta magazynu zawiera poświadczenia, których urządzenie używa do uzyskania dostępu do konta magazynu z dostawcy usług w chmurze. W przypadku kont magazynu Microsoft Azure są to poświadczenia, takie jak nazwa konta i podstawowy klucz dostępu.

Na **poświadczeń konta magazynu** bloku, wszystkie poświadczenia konta magazynu utworzone na potrzeby rozliczeń subskrypcji są wyświetlane w formacie tabelarycznym zawierający następujące informacje:

* **Nazwa** — unikatowa nazwa przypisana do konta, jeśli został on utworzony.
* **Włączony protokół SSL** — czy SSL jest włączona i urządzenia do chmury komunikacja odbywa się za pośrednictwem bezpiecznego kanału.
  
  ![Sekcja konfiguracji](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

Typowe zadania związane z poświadczeń konta magazynu, które mogą być wykonywane na **poświadczeń konta magazynu** bloku są:

* Dodawanie poświadczeń konta magazynu
* Edytowanie poświadczeń konta magazynu
* Usuń poświadczenia konta magazynu

## <a name="types-of-storage-account-credentials"></a>Typy poświadczeń konta magazynu
Istnieją trzy typy poświadczeń konta magazynu, które mogą być używane z urządzenia StorSimple.

* **Poświadczenia konta magazynu automatycznie generowanej** — zgodnie z sugestią, nazwa, ten typ poświadczeń dla konta magazynu jest generowana automatycznie po utworzeniu usługi. Aby dowiedzieć się więcej na temat sposobu tworzenia poświadczeń dla tego konta magazynu, zobacz [Utwórz nową usługę](storsimple-virtual-array-manage-service.md#create-a-service).
* **poświadczenia konta magazynu w ramach subskrypcji usługi** — są to poświadczenia konta magazynu Azure, które są skojarzone z tą samą subskrypcją, jak te usługi. Aby dowiedzieć się więcej, Magazyn, jak te informacje są tworzone poświadczenia konta, zobacz [o kontach magazynu Azure](../storage/common/storage-create-storage-account.md).
* **poświadczenia konta magazynu poza subskrypcji usługi** — są to poświadczenia konta magazynu platformy Azure, które nie są skojarzone z Twoją usługą i prawdopodobnie istniały przed usługa została utworzona.

## <a name="add-a-storage-account-credential"></a>Dodawanie poświadczeń konta magazynu
Do konfiguracji usługi Menedżer StorSimple urządzenia można dodać poświadczeń konta magazynu, podając unikatową przyjazną nazwę i poświadczenia dostępu, które są połączone z kontem magazynu. Masz również opcję włączenia trybu bezpiecznego sockets layer (SSL), aby utworzyć bezpiecznego kanału komunikacji sieciowej między urządzeniem i chmury.

Można utworzyć wiele kont dla określonej chmury dostawcy usług. Podczas zapisywania poświadczeń konta magazynu, usługa próbuje nawiązać połączenia z dostawcą usług w chmurze. Poświadczenia i materiały dostępu, który został dostarczony są uwierzytelniani w tej chwili. Poświadczenia konta magazynu jest tworzony tylko wtedy, gdy uwierzytelnienie zakończy się pomyślnie. Jeśli uwierzytelnianie nie powiedzie się, zostanie wyświetlony odpowiedni komunikat o błędzie.

Aby dodać poświadczeń konta magazynu platformy Azure, należy użyć poniższych procedur:

* Aby dodać poświadczeniami konta magazynu, które mają tej samej subskrypcji platformy Azure jako usługa Menedżera urządzeń
* Aby dodać poświadczenia konta usługi Azure storage znajduje się poza subskrypcji usługi Menedżera urządzeń

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Aby dodać poświadczeniami konta magazynu, które mają tej samej subskrypcji platformy Azure jako usługa Menedżera urządzeń

1. Przejdź do usługi Menedżera urządzeń, wybierz opcję i kliknij ją dwukrotnie. Spowoduje to otwarcie **omówienie** bloku.
2. Wybierz **poświadczeń konta magazynu** w **konfiguracji** sekcji.
3. Kliknij pozycję **Dodaj**.
4. W **dodawania konta magazynu** blok, wykonaj następujące czynności:
   
    1. Aby uzyskać **subskrypcji**, wybierz pozycję **bieżącego**.
    2. Podaj nazwę konta magazynu Azure.
    3. Wybierz **włączyć** można utworzyć bezpiecznego kanału komunikacji sieciowej między urządzeniem StorSimple i chmury. Wybierz **wyłączyć** tylko wtedy, gdy pracujesz w chmurze prywatnej.
    4. Kliknij pozycję **Dodaj**. Użytkownik jest powiadamiany po pomyślnym utworzeniu konta magazynu.<br></br>
   
        ![Dodawanie istniejących poświadczeń dla konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Aby dodać poświadczenia konta usługi Azure storage znajduje się poza subskrypcji usługi Menedżera urządzeń

1. Przejdź do usługi Menedżera urządzeń, wybierz opcję i kliknij ją dwukrotnie. Spowoduje to otwarcie **omówienie** bloku.
2. Wybierz **poświadczeń konta magazynu** w **konfiguracji** sekcji. Ta lista zawiera wszystkie istniejących poświadczeń konta magazynu skojarzone z usługą Menedżera urządzeń StorSimple.
3. Kliknij pozycję **Dodaj**.
4. W **dodawania konta magazynu** blok, wykonaj następujące czynności:
   
    1. Aby uzyskać **subskrypcji**, wybierz pozycję **innych**.
   
    2. Podaj nazwę Twoje poświadczenia konta magazynu platformy Azure.
   
    3. W **klucz dostępu do konta magazynu** tekst Podaj podstawowy klucz dostępu dla Twoje poświadczenia konta magazynu platformy Azure. Aby uzyskać ten klucz, przejdź do usługi Azure Storage, wybierz Twoje poświadczenia konta magazynu, a następnie kliknij przycisk **zarządzania kluczami konta**. Można teraz skopiuj podstawowy klucz dostępu.
   
    4. Aby włączyć protokół SSL, kliknij przycisk **włączyć** przycisk, aby utworzyć bezpieczny kanał komunikacji sieciowej między usługą Menedżer urządzenia StorSimple i chmury. Kliknij przycisk **wyłączyć** przycisk tylko wtedy, gdy pracujesz w chmurze prywatnej.
   
    5. Kliknij pozycję **Dodaj**. Użytkownik jest powiadamiany po pomyślnym utworzeniu poświadczeń konta magazynu.

5. Nowo utworzonego magazynu poświadczeń konta jest wyświetlany w bloku usługi StorSimple Konfigurowanie Menedżera urządzeń, w obszarze **poświadczeń konta magazynu**.
   
    ![Dodawanie poświadczeń konta magazynu, poza subskrypcji usługi Menedżera urządzeń](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Edytowanie poświadczeń konta magazynu
Można edytować poświadczeń konta magazynu używane przez urządzenia. Edycja poświadczeń konta magazynu, który jest obecnie używany, można modyfikować pola są trybie protokołu SSL dla poświadczenia konta magazynu i klucza dostępu. Należy podać nowy klucz dostępu do magazynu lub zmodyfikować **tryb Włącz SSL** wybór i Zapisz zaktualizowane ustawienia.

#### <a name="to-edit-a-storage-account-credential"></a>Aby edytować poświadczeń konta magazynu
1. Przejdź do usługi Menedżera urządzeń, wybierz opcję i kliknij ją dwukrotnie. Spowoduje to otwarcie **omówienie** bloku.
2. Wybierz **poświadczeń konta magazynu** w **konfiguracji** sekcji. Ta lista zawiera wszystkie istniejących poświadczeń konta magazynu skojarzone z usługą Menedżera urządzeń StorSimple.
3. Na liście tabelarycznym poświadczeń konta magazynu wybierz, a następnie kliknij dwukrotnie ikonę konta, które chcesz zmodyfikować.
4. W poświadczeń konta magazynu **właściwości** blok, wykonaj następujące czynności:
   
   1. Jeśli to konieczne, można zmodyfikować **Włącz SSL** wybór trybu.
   2. Można ponownie wygenerować klucze dostępu do poświadczeń konta magazynu. Aby uzyskać więcej informacji, zobacz [ponownie wygenerować kluczy konta magazynu](../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys). Należy podać nowy klucz poświadczeń konta magazynu. Konto magazynu platformy Azure jest to podstawowy klucz dostępu.
   3. Kliknij przycisk **zapisać** w górnej części **właściwości** bloku, aby zapisać ustawienia. Ustawienia są aktualizowane na **poświadczeń konta magazynu** bloku.
      
      ![Edytowanie poświadczeń konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Usuń poświadczenia konta magazynu
> [!IMPORTANT]
> Poświadczenia konta magazynu można usunąć tylko wtedy, gdy nie jest używany. Jeśli poświadczenia konta magazynu jest używana, użytkownik jest powiadamiany.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Aby usunąć poświadczeń konta magazynu
1. Przejdź do usługi Menedżera urządzeń, wybierz opcję i kliknij ją dwukrotnie. Spowoduje to otwarcie **omówienie** bloku.
2. Wybierz **poświadczeń konta magazynu** w **konfiguracji** sekcji. Ta lista zawiera wszystkie istniejących poświadczeń konta magazynu skojarzone z usługą Menedżera urządzeń StorSimple.
3. Na liście tabelarycznym poświadczeń konta magazynu wybierz, a następnie kliknij dwukrotnie ikonę konta, które chcesz usunąć.
4. W poświadczeń konta magazynu **właściwości** blok, wykonaj następujące czynności:
   
   1. Kliknij przycisk **usunąć** można usunąć poświadczenia.
   2. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak** aby kontynuować usuwanie. Tabelarycznej listę jest aktualizowany w celu odzwierciedlenia zmian.
      
      ![Usuń poświadczenia konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Synchronizowanie kluczy dostępu do poświadczeń konta magazynu
Ze względów bezpieczeństwa klucza obrotu często jest to wymagane w centrach danych. Administratora programu Microsoft Azure można ponownie wygenerować lub zmienić klucz podstawowy lub pomocniczy, uzyskując bezpośrednio dostęp do poświadczeń konta magazynu (za pomocą usługi Microsoft Azure Storage). Usługę Menedżer StorSimple urządzenia nie widzi automatycznie tej zmiany.

Informują usługę Menedżer StorSimple urządzenia zmiany, należy uzyskać dostęp do usługi Menedżer urządzeń StorSimple, dostęp do poświadczeń konta magazynu, a następnie zsynchronizować klucz podstawowy lub pomocniczy (w zależności od tego, co zostało zmienione). Następnie usługa pobiera najnowszego klucza, szyfruje kluczy i wysyła zaszyfrowany klucz do urządzenia.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Aby zsynchronizować klucze dla poświadczeń konta magazynu w tej samej subskrypcji co usługa (tylko wersja Azure)
1. W bloku docelowej usługi, wybierz usługę, kliknij dwukrotnie nazwę usługi, a następnie w **konfiguracji** kliknij **poświadczeń konta magazynu**.
2. Na **poświadczeń konta magazynu** bloku na liście poświadczeń konta magazynu, wybierz konto magazynu poświadczeń której klucze, które mają być synchronizowane.
3. W **właściwości** bloku dla wybranego magazynu poświadczenia konta, wykonaj następujące czynności:
   
    1. Kliknij przycisk **więcej**, a następnie kliknij przycisk **klucz dostępu synchronizacji**.
   
    2. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **klucza synchronizacji** na ukończenie synchronizacji.
    
4. W usłudze Menedżer urządzeń StorSimple należy zaktualizować klucz, który wcześniej został zmieniony w usłudze Microsoft Azure Storage. W **klucz konta magazynu Synchronizuj** bloku, jeśli podstawowy klucz dostępu został zmieniony (wygenerowano ponownie), kliknij przycisk podstawowym, a następnie kliknij **klucza synchronizacji**. Jeśli klucz pomocniczy został zmieniony, kliknij przycisk **dodatkowej**, a następnie kliknij przycisk **klucza synchronizacji**.
   
    ![Klucz dostępu do synchronizacji](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-acess-key.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [administrowania tablica wirtualnego StorSimple](storsimple-ova-web-ui-admin.md).

