---
title: Zarządzanie poświadczeniami konta magazynu, urządzenie storsimple z serii 8000
description: W tym artykule wyjaśniono, jak za pomocą strony Konfigurowanie Menedżera urządzeń StorSimple można dodawać, edytować, usuwać lub obracać klucze zabezpieczeń dla konta magazynu.
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
ms.openlocfilehash: 65aa83731be97b59a36a5baf60ea308690a0dcf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297743"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Zarządzanie poświadczeniami konta magazynu za pomocą usługi StorSimple Device Manager

## <a name="overview"></a>Omówienie

Sekcja **Konfiguracja** w bloku usługi StorSimple Device Manager przedstawia wszystkie parametry usługi globalnej, które można utworzyć w usłudze StorSimple Device Manager. Parametry te mogą być stosowane do wszystkich urządzeń podłączonych do usługi i obejmują:

* Poświadczenia konta magazynu
* Szablony przepustowości 
* Rekordy kontroli dostępu 

W tym samouczku wyjaśniono, jak dodawać, edytować lub usuwać poświadczenia konta magazynu lub obracać klucze zabezpieczeń dla konta magazynu.

 ![Lista poświadczeń konta magazynu](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Konta magazynu zawierają poświadczenia używane przez urządzenie StorSimple do uzyskiwania dostępu do konta magazynu u dostawcy usług w chmurze. W przypadku kont magazynu platformy Microsoft Azure są to poświadczenia, takie jak nazwa konta i podstawowy klucz dostępu. 

W bloku **poświadczenia konta magazynu** wszystkie konta magazynu utworzone dla subskrypcji rozliczeniowej są wyświetlane w formacie tabelarycznym zawierającym następujące informacje:

* **Nazwa** — unikatowa nazwa przypisana do konta podczas jego tworzenia.
* **SSL włączone** — czy TLS jest włączony i komunikacja między urządzeniami do chmury jest za pomocą bezpiecznego kanału.
* **Używane przez** — liczba woluminów przy użyciu konta magazynu.

Najczęstsze zadania związane z kontami magazynu, które można wykonać, to:

* Dodawanie konta magazynu 
* Edytowanie konta magazynu 
* Usuwanie konta magazynu 
* Rotacja kluczy kont magazynu 

## <a name="types-of-storage-accounts"></a>Typy kont magazynu

Istnieją trzy typy kont magazynu, które mogą być używane z urządzeniem StorSimple.

* **Automatycznie generowane konta magazynu** — jak sama nazwa wskazuje, ten typ konta magazynu jest generowany automatycznie podczas tworzenia usługi. Aby dowiedzieć się więcej o sposobie tworzenia tego konta magazynu, zobacz [Krok 1: Tworzenie nowej usługi](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) w urządzeniu Lokalny [StorSimple](storsimple-8000-deployment-walkthrough-u2.md). 
* **Konta magazynu w subskrypcji usługi** — są to konta magazynu platformy Azure, które są skojarzone z tej samej subskrypcji, jak usługi. Aby dowiedzieć się więcej o sposobie tworzenia tych kont magazynu, zobacz [Informacje o kontach usługi Azure Storage](../storage/common/storage-create-storage-account.md). 
* **Konta magazynu poza subskrypcją usługi** — są to konta magazynu platformy Azure, które nie są skojarzone z usługą i prawdopodobnie istniały przed utworzeniem usługi.

## <a name="add-a-storage-account"></a>Dodawanie konta magazynu

Konto magazynu można dodać, podając unikatową przyjazną nazwę i poświadczenia dostępu, które są połączone z kontem magazynu (z określonym dostawcą usług w chmurze). Istnieje również możliwość włączenia trybu TLS (Transport Layer Security), znanego wcześniej jako tryb SSL (Secure Sockets Layer), aby utworzyć bezpieczny kanał komunikacji sieciowej między urządzeniem a chmurą.

Można utworzyć wiele kont dla danego dostawcy usług w chmurze. Należy jednak pamiętać, że po utworzeniu konta magazynu nie można zmienić dostawcy usług w chmurze.

Podczas zapisywania konta magazynu usługa próbuje komunikować się z dostawcą usług w chmurze. Poświadczenia i materiał dostępu, które zostały dostarczone zostaną uwierzytelnione w tej chwili. Konto magazynu jest tworzone tylko wtedy, gdy uwierzytelnianie zakończy się pomyślnie. Jeśli uwierzytelnianie nie powiedzie się, zostanie wyświetlony odpowiedni komunikat o błędzie.

Aby dodać poświadczenia konta magazynu platformy Azure, użyj następujących procedur:

* Aby dodać poświadczenia konta magazynu, które ma taką samą subskrypcję platformy Azure jak usługa Menedżer urządzeń
* Aby dodać poświadczenia konta magazynu platformy Azure, które znajduje się poza subskrypcją usługi Menedżer urządzeń

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Aby dodać poświadczenia konta magazynu platformy Azure poza subskrypcją usługi StorSimple Device Manager

1. Przejdź do usługi StorSimple Device Manager, wybierz ją i kliknij dwukrotnie. Spowoduje to otwarcie **overview** blade.
2. Wybierz **poświadczenia konta magazynu** w sekcji **Konfiguracja.** Spowoduje to wyświetlenie wszystkich istniejących poświadczeń konta magazynu skojarzonych z usługą Menedżer urządzeń StorSimple.
3. Kliknij przycisk **Dodaj**.
4. W bloku **Dodawania poświadczeń konta magazynu** wykonaj następujące czynności:
   
    1. W obszarze **Subskrypcja**wybierz pozycję **Inne**.
   
    2. Podaj nazwę poświadczenia konta magazynu platformy Azure.
   
    3. W polu tekstowym **klucz dostępu do konta magazynu** podaj podstawowy klucz dostępu dla poświadczeń konta magazynu platformy Azure. Aby uzyskać ten klucz, przejdź do usługi Azure Storage, wybierz poświadczenia konta magazynu i kliknij pozycję **Zarządzaj kluczami kont**. Teraz można skopiować podstawowy klucz dostępu.
   
    4. Aby włączyć funkcję TLS, kliknij przycisk **Włącz,** aby utworzyć bezpieczny kanał komunikacji sieciowej między usługą StorSimple Device Manager a chmurą. Kliknij przycisk **Wyłącz** tylko wtedy, gdy działasz w chmurze prywatnej.
   
    5. Kliknij przycisk **Dodaj**. Użytkownik jest powiadamiany po pomyślnym utworzeniu poświadczenia konta magazynu.

5. Nowo utworzone poświadczenia konta magazynu są wyświetlane w bloku usługi StorSimple Configure Device Manager w obszarze **Poświadczenia konta magazynu**.
   


## <a name="edit-a-storage-account"></a>Edytowanie konta magazynu

Można edytować konto magazynu używane przez kontener woluminu. Jeśli edytujesz konto magazynu, które jest obecnie używane, jedynym polem dostępnym do zmodyfikowania jest klucz dostępu dla konta magazynu. Można podać nowy klucz dostępu do magazynu i zapisać zaktualizowane ustawienia.

#### <a name="to-edit-a-storage-account"></a>Aby edytować konto magazynu

1. Przejdź do usługi Menedżer urządzeń StorSimple. W sekcji **Konfiguracja** kliknij pozycję **Poświadczenia konta magazynu**.

    ![Poświadczenia konta magazynu](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. W bloku **poświadczenia konta magazynu** z listy poświadczeń konta magazynu wybierz i kliknij ten, który chcesz edytować. 

3. Można zmodyfikować wybór **Włącz SSL.** Możesz również kliknąć **więcej...** a następnie wybrać **klucz dostępu Synchronizuj, aby obrócić klucze** dostępu do konta magazynu. Przejdź do [kluczowego obracania kont magazynu,](#key-rotation-of-storage-accounts) aby uzyskać więcej informacji na temat wykonywania rotacji kluczy. Po zmodyfikowaniu ustawień kliknij przycisk **Zapisz**. 

    ![Zapisywanie edytowanych poświadczeń konta magazynu](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. 

    ![Potwierdź modyfikacje](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

Ustawienia zostaną zaktualizowane i zapisane dla twojego konta magazynu. 

## <a name="delete-a-storage-account"></a>Usuwanie konta magazynu

> [!IMPORTANT]
> Konto magazynu można usunąć tylko wtedy, gdy nie jest używane przez kontener woluminu. Jeśli konto magazynu jest używane przez kontener woluminu, najpierw usuń kontener woluminu, a następnie usuń skojarzone konto magazynu.

#### <a name="to-delete-a-storage-account"></a>Aby usunąć konto magazynu

1. Przejdź do usługi Menedżer urządzeń StorSimple. W sekcji **Konfiguracja** kliknij pozycję **Poświadczenia konta magazynu**.

2. Na tabelarycznej liście kont magazynu umieść wskaźnik myszy na koncie, które chcesz usunąć. Kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe i kliknij polecenie **Usuń**.

    ![Usuwanie poświadczeń konta magazynu](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak,** aby kontynuować usuwanie. Lista tabelaryczny zostanie zaktualizowana w celu odzwierciedlenia zmian.

    ![Potwierdzenie usunięcia](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Rotacja kluczy kont magazynu

Ze względów bezpieczeństwa rotacja kluczy jest często wymagana w centrach danych. Każda subskrypcja platformy Microsoft Azure może mieć jedno lub więcej skojarzonych kont magazynu. Dostęp do tych kont jest kontrolowany przez klucze subskrypcji i dostępu dla każdego konta magazynu. 

Podczas tworzenia konta magazynu platforma Microsoft Azure generuje dwa 512-bitowe klucze dostępu do magazynu, które są używane do uwierzytelniania podczas uzyskiwania dostępu do konta magazynu. Posiadanie dwóch kluczy dostępu do magazynu umożliwia ponowne generowanie kluczy bez przerw w usłudze magazynu lub dostęp do tej usługi. Klucz, który jest obecnie używany jest klucz *podstawowy* i klucz kopii zapasowej jest określany jako klucz *pomocniczy.* Jeden z tych dwóch kluczy musi być dostarczony, gdy urządzenie Microsoft Azure StorSimple uzyskuje dostęp do dostawcy usług magazynu w chmurze.

## <a name="what-is-key-rotation"></a>Co to jest rotacja kluczy?

Zazwyczaj aplikacje używają tylko jednego z kluczy, aby uzyskać dostęp do danych. Po pewnym czasie aplikacje mogą przełączyć się na drugi klucz. Po przełączeniu aplikacji na klucz pomocniczy można wycofać pierwszy klucz, a następnie wygenerować nowy klucz. Korzystanie z dwóch klawiszy w ten sposób umożliwia aplikacjom dostęp do danych bez ponoszenia żadnych przestojów.

Klucze konta magazynu są zawsze przechowywane w usłudze w postaci zaszyfrowanej. Można je jednak zresetować za pomocą usługi StorSimple Device Manager. Usługa może uzyskać klucz podstawowy i klucz pomocniczy dla wszystkich kont magazynu w tej samej subskrypcji, w tym kont utworzonych w usłudze Magazynowanie, a także domyślnych kont magazynu wygenerowanych, gdy usługa Menedżer urządzeń StorSimple była pierwsza Utworzone. Usługa StorSimple Device Manager zawsze pobierze te klucze z klasycznego portalu platformy Azure, a następnie zapisz je w zaszyfrowany sposób.

## <a name="rotation-workflow"></a>Przepływ pracy obrót

Administrator platformy Microsoft Azure może ponownie wygenerować lub zmienić klucz podstawowy lub pomocniczy, uzyskując bezpośredni dostęp do konta magazynu (za pośrednictwem usługi Microsoft Azure Storage). Usługa StorSimple Device Manager nie widzi tej zmiany automatycznie.

Aby poinformować usługę StorSimple Device Manager o zmianie, należy uzyskać dostęp do usługi Menedżer urządzeń StorSimple, uzyskać dostęp do konta magazynu, a następnie zsynchronizować klucz podstawowy lub pomocniczy (w zależności od tego, który z nich został zmieniony). Następnie usługa pobiera najnowszy klucz, szyfruje klucze i wysyła zaszyfrowany klucz do urządzenia.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Aby zsynchronizować klucze dla kont magazynu w tej samej subskrypcji co usługa 
1. Przejdź do usługi Menedżer urządzeń StorSimple. W sekcji **Konfiguracja** kliknij pozycję **Poświadczenia konta magazynu**.
2. Na tabelarycznej liście kont magazynu kliknij konto, które chcesz zmodyfikować. 

    ![synchronizowanie klawiszy](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Kliknij **... Więcej,** a następnie wybierz **pozycję Przycisk dostępu Synchronizuj, aby obrócić**.   

    ![synchronizowanie klawiszy](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. W usłudze StorSimple Device Manager należy zaktualizować klucz, który został wcześniej zmieniony w usłudze Microsoft Azure Storage. Jeśli podstawowy klucz dostępu został zmieniony (ponownie wygenerowany), wybierz klucz **podstawowy.** Jeśli klucz pomocniczy został zmieniony, wybierz klucz **pomocniczy.** Kliknij **przycisk Przycisk Synchronizuj klucz**.
      
      ![synchronizowanie klawiszy](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Zostaniesz powiadomiony po pomyślnym zsynchronizowaniu klucza.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Aby zsynchronizować klucze dla kont magazynu poza subskrypcją usługi
1. Na stronie **Usługi** kliknij kartę **Konfiguruj.**
2. Kliknij **pozycję Dodaj/Edytuj konta magazynu**.
3. W oknie dialogowym wykonaj następujące czynności:
   
   1. Wybierz konto magazynu z kluczem dostępu, który chcesz zaktualizować.
   2. Należy zaktualizować klucz dostępu do magazynu w usłudze StorSimple Device Manager. W takim przypadku można wyświetlić klucz dostępu do magazynu. Wprowadź nowy klucz w polu **Klucz dostępu do konta magazynu.** 
   3. Zapisz zmiany. Klucz dostępu do konta magazynu powinien zostać zaktualizowany.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zabezpieczeniach StorSimple](storsimple-8000-security.md).
* Dowiedz się więcej o [administrowaniu urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-8000-manager-service-administration.md)

