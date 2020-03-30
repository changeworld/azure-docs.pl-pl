---
title: Zarządzanie poświadczeniami konta magazynu tablicy wirtualnej StorSimple | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak za pomocą strony StorSimple Device Manager Configure do dodawania, edytowania, usuwania lub obracania kluczy zabezpieczeń dla poświadczeń konta magazynu skojarzonych z macierzą wirtualną StorSimple.
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
ms.openlocfilehash: 5cedde1e7daa49aaa7a2786c9ad8a65fb8e452f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297578"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Zarządzanie poświadczeniami konta magazynu dla tablicy wirtualnej StorSimple za pomocą Menedżera urządzeń StorSimple

## <a name="overview"></a>Omówienie
Sekcja **Konfiguracja** bloku usługi StorSimple Device Manager tablicy wirtualnej StorSimple przedstawia globalne parametry usługi, które można utworzyć w usłudze StorSimple Manager. Parametry te mogą być stosowane do wszystkich urządzeń podłączonych do usługi i obejmują:

* Poświadczenia konta magazynu
* Rekordy kontroli dostępu
  
  ![Pulpit nawigacyjny usługi Menedżera urządzeń](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

W tym samouczku wyjaśniono, jak można dodawać, edytować lub usuwać poświadczenia konta magazynu dla tablicy wirtualnej StorSimple. Informacje zawarte w tym samouczku dotyczą tylko tablicy wirtualnej StorSimple. Aby uzyskać informacje na temat zarządzania kontami magazynu w serii 8000, zobacz [Zarządzanie kontem magazynu za pomocą usługi StorSimple Manager](storsimple-manage-storage-accounts.md).

Poświadczenia konta magazynu zawierają poświadczenia używane przez urządzenie do uzyskiwania dostępu do konta magazynu u dostawcy usług w chmurze. W przypadku kont magazynu platformy Microsoft Azure są to poświadczenia, takie jak nazwa konta i podstawowy klucz dostępu.

W bloku **poświadczenia konta magazynu** wszystkie poświadczenia konta magazynu utworzone dla subskrypcji rozliczeniowej są wyświetlane w formacie tabelarycznym zawierającym następujące informacje:

* **Nazwa** — unikatowa nazwa przypisana do konta podczas jego tworzenia.
* **SSL włączone** — czy TLS jest włączony i komunikacja między urządzeniami do chmury jest za pomocą bezpiecznego kanału.
  
  ![Sekcja Konfiguracji](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

Najczęstsze zadania związane z poświadczeniami konta magazynu, które można wykonać w bloku **poświadczeń konta magazynu** to:

* Dodawanie poświadczeń konta magazynu
* Edytowanie poświadczeń konta magazynu
* Usuwanie poświadczeń konta magazynu

## <a name="types-of-storage-account-credentials"></a>Typy poświadczeń konta magazynu
Istnieją trzy typy poświadczeń konta magazynu, które mogą być używane z urządzeniem StorSimple.

* **Automatycznie generowane poświadczenia konta magazynu** — jak sama nazwa wskazuje, ten typ poświadczenia konta magazynu jest generowany automatycznie podczas pierwszego tworzenia usługi. Aby dowiedzieć się więcej o sposobie tworzenia tego poświadczenia konta magazynu, zobacz [Tworzenie nowej usługi](storsimple-virtual-array-manage-service.md#create-a-service).
* **poświadczenia konta magazynu w subskrypcji usługi** — są to poświadczenia konta usługi Azure storage, które są skojarzone z tą samą subskrypcją co usługa. Aby dowiedzieć się więcej o sposobie tworzenia tych poświadczeń konta magazynu, zobacz [Informacje o kontach usługi Azure Storage](../storage/common/storage-create-storage-account.md).
* **poświadczenia konta magazynu poza subskrypcją usługi** — są to poświadczenia konta usługi Azure storage, które nie są skojarzone z usługą i prawdopodobnie istniały przed utworzeniem usługi.

## <a name="add-a-storage-account-credential"></a>Dodawanie poświadczeń konta magazynu
Poświadczenia konta magazynu można dodać do konfiguracji usługi StorSimple Device Manager, podając unikatową przyjazną nazwę i poświadczenia dostępu, które są połączone z kontem magazynu. Istnieje również możliwość włączenia trybu TLS (Transport Layer Security), znanego wcześniej jako tryb SSL (Secure Sockets Layer), aby utworzyć bezpieczny kanał komunikacji sieciowej między urządzeniem a chmurą.

Można utworzyć wiele kont dla danego dostawcy usług w chmurze. Podczas zapisywania poświadczeń konta magazynu usługa próbuje komunikować się z dostawcą usług w chmurze. Poświadczenia i materiał dostępu, które zostały dostarczone są uwierzytelnione w tej chwili. Poświadczenie konta magazynu jest tworzone tylko wtedy, gdy uwierzytelnianie zakończy się pomyślnie. Jeśli uwierzytelnianie nie powiedzie się, zostanie wyświetlony odpowiedni komunikat o błędzie.

Aby dodać poświadczenia konta magazynu platformy Azure, użyj następujących procedur:

* Aby dodać poświadczenia konta magazynu, które ma taką samą subskrypcję platformy Azure jak usługa Menedżer urządzeń
* Aby dodać poświadczenia konta magazynu platformy Azure, które znajduje się poza subskrypcją usługi Menedżer urządzeń

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Aby dodać poświadczenia konta magazynu, które ma taką samą subskrypcję platformy Azure jak usługa Menedżer urządzeń

1. Przejdź do usługi Menedżer urządzeń, wybierz ją i kliknij dwukrotnie. Spowoduje to otwarcie **overview** blade.
2. Wybierz **poświadczenia konta magazynu** w sekcji **Konfiguracja.**
3. Kliknij przycisk **Dodaj**.
4. W bloku **Dodaj konto magazynu** wykonaj następujące czynności:
   
    1. W obszarze **Subskrypcja**wybierz pozycję **Bieżący**.
    2. Podaj nazwę swojego konta magazynu platformy Azure.
    3. Wybierz **włącz,** aby utworzyć bezpieczny kanał komunikacji sieciowej między urządzeniem StorSimple a chmurą. Wybierz **opcję Wyłącz** tylko wtedy, gdy działasz w chmurze prywatnej.
    4. Kliknij przycisk **Dodaj**. Użytkownik zostanie powiadomiony po pomyślnym utworzeniu konta magazynu.<br></br>
   
        ![Dodawanie istniejącego poświadczenia konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Aby dodać poświadczenia konta magazynu platformy Azure, które znajduje się poza subskrypcją usługi Menedżer urządzeń

1. Przejdź do usługi Menedżer urządzeń, wybierz ją i kliknij dwukrotnie. Spowoduje to otwarcie **overview** blade.
2. Wybierz **poświadczenia konta magazynu** w sekcji **Konfiguracja.** Spowoduje to wyświetlenie wszystkich istniejących poświadczeń konta magazynu skojarzonych z usługą Menedżer urządzeń StorSimple.
3. Kliknij przycisk **Dodaj**.
4. W bloku **Dodaj konto magazynu** wykonaj następujące czynności:
   
    1. W obszarze **Subskrypcja**wybierz pozycję **Inne**.
   
    2. Podaj nazwę poświadczenia konta magazynu platformy Azure.
   
    3. W polu tekstowym **klucz dostępu do konta magazynu** podaj podstawowy klucz dostępu dla poświadczeń konta magazynu platformy Azure. Aby uzyskać ten klucz, przejdź do usługi Azure Storage, wybierz poświadczenia konta magazynu i kliknij pozycję **Zarządzaj kluczami kont**. Teraz można skopiować podstawowy klucz dostępu.
   
    4. Aby włączyć funkcję TLS, kliknij przycisk **Włącz,** aby utworzyć bezpieczny kanał komunikacji sieciowej między usługą StorSimple Device Manager a chmurą. Kliknij przycisk **Wyłącz** tylko wtedy, gdy działasz w chmurze prywatnej.
   
    5. Kliknij przycisk **Dodaj**. Użytkownik jest powiadamiany po pomyślnym utworzeniu poświadczenia konta magazynu.

5. Nowo utworzone poświadczenia konta magazynu są wyświetlane w bloku usługi StorSimple Configure Device Manager w obszarze **Poświadczenia konta magazynu**.
   
    ![Dodawanie poświadczenia konta magazynu poza subskrypcją usługi Menedżer urządzeń](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Edytowanie poświadczeń konta magazynu
Poświadczenia konta magazynu używanego przez urządzenie można edytować. Jeśli edytujesz poświadczenia konta magazynu, który jest obecnie używany, pola dostępne do zmodyfikowania są klucz dostępu i tryb TLS dla poświadczeń konta magazynu. Można podać nowy klucz dostępu do magazynu lub zmodyfikować wybór **trybu SSL** i zapisać zaktualizowane ustawienia.

#### <a name="to-edit-a-storage-account-credential"></a>Aby edytować poświadczenia konta magazynu
1. Przejdź do usługi Menedżer urządzeń, wybierz ją i kliknij dwukrotnie. Spowoduje to otwarcie **overview** blade.
2. Wybierz **poświadczenia konta magazynu** w sekcji **Konfiguracja.** Spowoduje to wyświetlenie wszystkich istniejących poświadczeń konta magazynu skojarzonych z usługą Menedżer urządzeń StorSimple.
3. Na tabelarycznej liście poświadczeń konta magazynu wybierz i kliknij dwukrotnie konto, które chcesz zmodyfikować.
4. W bloku **właściwości** poświadczeń konta magazynu wykonaj następujące czynności:
   
   1. W razie potrzeby można zmodyfikować wybór trybu **Włączania SSL.**
   2. Można wybrać opcję ponownego generowania kluczy dostępu do poświadczeń konta magazynu. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md). Podaj nowy klucz poświadczeń konta magazynu. W przypadku konta magazynu platformy Azure jest to podstawowy klucz dostępu.
   3. Kliknij **przycisk Zapisz** u góry bloku **Właściwości,** aby zapisać ustawienia. Ustawienia są aktualizowane w bloku **poświadczenia konta magazynu.**
      
      ![Edytowanie poświadczeń konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Usuwanie poświadczeń konta magazynu
> [!IMPORTANT]
> Poświadczenia konta magazynu można usunąć tylko wtedy, gdy nie są używane. Jeśli dane uwierzytelniające konta magazynu są używane, użytkownik zostanie o tym powiadomiony.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Aby usunąć poświadczenia konta magazynu
1. Przejdź do usługi Menedżer urządzeń, wybierz ją i kliknij dwukrotnie. Spowoduje to otwarcie **overview** blade.
2. Wybierz **poświadczenia konta magazynu** w sekcji **Konfiguracja.** Spowoduje to wyświetlenie wszystkich istniejących poświadczeń konta magazynu skojarzonych z usługą Menedżer urządzeń StorSimple.
3. Na tabelarycznej liście poświadczeń konta magazynu wybierz i kliknij dwukrotnie konto, które chcesz usunąć.
4. W bloku **właściwości** poświadczeń konta magazynu wykonaj następujące czynności:
   
   1. Kliknij **przycisk Usuń,** aby usunąć poświadczenia.
   2. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak,** aby kontynuować usuwanie. Lista tabelaryczny zostanie zaktualizowana w celu odzwierciedlenia zmian.
      
      ![Usuwanie poświadczeń konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Synchronizowanie kluczy poświadczeń konta magazynu
Ze względów bezpieczeństwa rotacja kluczy jest często wymagana w centrach danych. Administrator platformy Microsoft Azure może ponownie wygenerować lub zmienić klucz podstawowy lub pomocniczy, uzyskując bezpośredni dostęp do poświadczeń konta magazynu (za pośrednictwem usługi Microsoft Azure Storage). Usługa StorSimple Device Manager nie widzi tej zmiany automatycznie.

Aby poinformować usługę StorSimple Device Manager o zmianie, należy uzyskać dostęp do usługi Menedżer urządzeń StorSimple, uzyskać dostęp do poświadczeń konta magazynu, a następnie zsynchronizować klucz podstawowy lub pomocniczy (w zależności od tego, który z nich został zmieniony). Następnie usługa pobiera najnowszy klucz, szyfruje klucze i wysyła zaszyfrowany klucz do urządzenia.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Aby zsynchronizować klucze dla poświadczeń konta magazynu w tej samej subskrypcji co usługa (tylko platforma Azure)
1. W bloku docelowym usługi wybierz usługę, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Konfiguracja** kliknij pozycję **Poświadczenia konta magazynu**.
2. W bloku **poświadczenia konta magazynu** na liście poświadczeń konta magazynu wybierz poświadczenie konta magazynu, którego klucze chcesz zsynchronizować.
3. W bloku **Właściwości** dla wybranego poświadczenia konta magazynu wykonaj następujące czynności:
   
    1. Kliknij pozycję **Więcej**, a następnie kliknij pozycję **Synchronizuj klucz dostępu**.
   
    2. Po wyświetleniu monitu o potwierdzenie kliknij **przycisk Synchronizuj klucz,** aby zakończyć synchronizację.
    
4. W usłudze StorSimple Device Manager należy zaktualizować klucz, który został wcześniej zmieniony w usłudze Microsoft Azure Storage. Jeśli klucz dostępu podstawowego został zmieniony (ponownie **wygenerowany),** kliknij pozycję Podstawowy, a następnie kliknij pozycję **Synchronizuj klucz .** Jeśli klucz pomocniczy został zmieniony, kliknij przycisk **Pomocniczy**, a następnie kliknij przycisk **Synchronizuj klucz**.
   
    ![Klucz dostępu do synchronizacji](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [administrować tablicą wirtualną StorSimple](storsimple-ova-web-ui-admin.md).

