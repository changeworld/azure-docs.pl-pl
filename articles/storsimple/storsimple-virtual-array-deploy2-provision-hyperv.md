---
title: Aprowizacja storsimple tablicy wirtualnej w funkcji Hyper-V | Dokumenty firmy Microsoft
description: Ten drugi samouczek w storsimple virtual array wdrożenia obejmuje inicjowanie obsługi administracyjnej tablicy wirtualnej w funkcji Hyper-V.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d3f4f4ab6cc1c928761fce740d39f3f73426e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267536"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Wdrażanie tablicy wirtualnej StorSimple — aprowizowanie w funkcji Hyper-V
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

W tym samouczku opisano sposób aprowizowania tablicy wirtualnej StorSimple w systemie hosta z systemem Hyper-V w systemach Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2. Ten artykuł dotyczy wdrażania tablic wirtualnych StorSimple w witrynie Azure portal i microsoft azure government cloud.

Do aprowienia i konfigurowania tablicy wirtualnej potrzebne są uprawnienia administratora. Aprowizacja i wstępna konfiguracja może zająć około 10 minut.

## <a name="provisioning-prerequisites"></a>Wymagania wstępne inicjowania obsługi administracyjnej
W tym miejscu znajdują się wymagania wstępne dotyczące aprowizowania tablicy wirtualnej w systemie hosta z systemem Hyper-V w systemach Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple
Przed rozpoczęciem upewnij się, że:

* Wykonano wszystkie kroki opisane w trybie [Przygotowywanie portalu dla tablicy wirtualnej StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Obraz tablicy wirtualnej dla funkcji Hyper-V został pobrany z witryny Azure portal. Aby uzyskać więcej informacji, zobacz **Krok 3: Pobieranie obrazu tablicy wirtualnej** [przewodnika Przygotowywanie portalu dla tablicy wirtualnej StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > Oprogramowanie uruchomione w macierzy wirtualnej StorSimple może być używane tylko z usługą StorSimple Device Manager.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Dla tablicy wirtualnej StorSimple
Przed wdrożeniem tablicy wirtualnej upewnij się, że:

* Masz dostęp do systemu hosta z systemem Hyper-V w systemie Windows Server 2008 R2 lub nowszym, który może służyć do aprowizowania urządzenia.
* System hosta jest w stanie poświęcić następujące zasoby do aprowizowania tablicy wirtualnej:

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurować tablicę wirtualną jako serwer plików, 8 GB obsługuje mniej niż 2 miliony plików. Potrzebujesz 16 GB pamięci RAM do obsługi 2 - 4 milionów plików.
  * Jeden interfejs sieciowy.
  * Dysk wirtualny o przekątną 500 GB dla danych.

### <a name="for-the-network-in-the-datacenter"></a>Sieć w centrum danych
Przed rozpoczęciem przejrzyj wymagania sieciowe, aby wdrożyć tablicę wirtualną StorSimple i odpowiednio skonfigurować sieć centrum danych. Aby uzyskać więcej informacji, zobacz [StorSimple Virtual Array wymagania dotyczące sieci](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Inicjowanie administracyjne krok po kroku
Aby aprowizować i łączyć się z tablicą wirtualną, należy wykonać następujące kroki:

1. Upewnij się, że system hosta ma wystarczające zasoby, aby spełnić minimalne wymagania tablicy wirtualnej.
2. Aprowizuj tablicę wirtualną w hipernadzorcy.
3. Uruchom tablicę wirtualną i uzyskaj adres IP.

Każdy z tych kroków jest wyjaśniony w poniższych sekcjach.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Krok 1: Upewnij się, że system hosta spełnia minimalne wymagania tablicy wirtualnej
Aby utworzyć tablicę wirtualną, potrzebujesz:

* Rola funkcji Hyper-V zainstalowana w systemach Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2 z dodatku SP1.
* Menedżer funkcji Microsoft Hyper-V na kliencie z systemem Microsoft Windows połączonym z hostem.

Upewnij się, że podstawowy sprzęt (system hosta), na którym tworzysz tablicę wirtualną, może przeznaczyć następujące zasoby na tablicę wirtualną:

* Co najmniej 4 rdzenie.
* Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurować tablicę wirtualną jako serwer plików, 8 GB obsługuje mniej niż 2 miliony plików. Potrzebujesz 16 GB pamięci RAM do obsługi 2 - 4 milionów plików.
* Jeden interfejs sieciowy.
* Dysk wirtualny o przekątną 500 GB do danych systemowych.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Krok 2: Aprowizuj tablicę wirtualną w funkcji hypervisor
Wykonaj następujące czynności, aby aprowizować urządzenie w funkcji hypervisor.

#### <a name="to-provision-a-virtual-array"></a>Aby aprowizować tablicę wirtualną
1. Na hoście systemu Windows Server skopiuj obraz tablicy wirtualnej na dysk lokalny. Ten obraz został pobrany (VHD lub VHDX) za pośrednictwem witryny Azure portal. Zanotuj lokalizację, do której został skopiowany obraz, ponieważ będzie on używany w dalszej części tej procedury.
2. Otwórz **Menedżera serwera**. W prawym górnym rogu kliknij przycisk **Narzędzia** i wybierz pozycję **Menedżer funkcji Hyper-V**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Jeśli korzystasz z systemu Windows Server 2008 R2, otwórz Menedżera funkcji Hyper-V. W Menedżerze serwera kliknij pozycję **Role > Menedżer funkcji Hyper-V > funkcji Hyper-V**.
3. W **Menedżerze funkcji Hyper-V** w okienku zakresu kliknij prawym przyciskiem myszy węzeł systemu, aby otworzyć menu kontekstowe, a następnie kliknij kolejno pozycje **Nowy** > **Maszyna wirtualna**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Na stronie **Przed rozpoczęciem** w kreatorze nowej maszyny wirtualnej kliknij przycisk **Dalej**.
5. Na stronie **Określanie nazwy i lokalizacji** podaj **nazwę** tablicy wirtualnej. Kliknij przycisk **alej**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Na stronie **Określanie generacji** wybierz typ obrazu urządzenia, a następnie kliknij przycisk **Dalej**. Ta strona nie jest wyświetlana, jeśli używasz systemu Windows Server 2008 R2.

   * Wybierz **pozycję Generacja 2,** jeśli pobrano obraz vhdx dla systemu Windows Server 2012 lub nowszego.
   * Wybierz **pozycję Generacja 1,** jeśli pobrano obraz vhd dla systemu Windows Server 2008 R2 lub nowszego.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Na stronie **Przypisywanie pamięci** w polu **Pamięć początkowa** wprowadź wartość nie mniejszą niż **8192 MB**, nie włączaj pamięci dynamicznej, a następnie kliknij przycisk **Dalej**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Na stronie **Konfigurowanie sieci** określ przełącznik wirtualny połączony z Internetem, a następnie kliknij przycisk **Dalej**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Na stronie **Połącz wirtualny dysk twardy** wybierz pozycję **Użyj istniejącego wirtualnego dysku twardego**, określ lokalizację obrazu tablicy wirtualnej (vhdx lub vhd), a następnie kliknij przycisk **Dalej**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Przejrzyj informacje na stronie **Podsumowanie**, a następnie kliknij pozycję **Zakończ**, aby utworzyć maszynę wirtualną.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Aby spełnić wymagania minimalne, potrzebujesz 4 rdzeni. Aby dodać 4 procesory wirtualne, wybierz swój system hosta w oknie **Menedżera funkcji Hyper-V**. Na liście **Maszyny wirtualne** w okienku po prawej stronie znajdź utworzoną przed chwilą maszynę wirtualną. Zaznacz i kliknij prawym przyciskiem myszy nazwę maszyny, a następnie wybierz pozycję **Ustawienia**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Na stronie **Ustawienia** w lewym okienku kliknij pozycję **Procesor**. W okienku po prawej stronie ustaw wartość 4 lub większą w polu **Liczba procesorów wirtualnych**. Kliknij przycisk **Zastosuj**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Aby spełnić minimalne wymagania, należy również dodać wirtualny dysk danych o wartości 500 GB. Na stronie **Ustawienia**:

    1. W okienku po lewej stronie wybierz pozycję **Kontroler SCSI**.
    2. W okienku po prawej stronie wybierz pozycję **Dysk twardy** i kliknij przycisk **Dodaj**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Na stronie **Dysk twardy** wybierz opcję **Wirtualny dysk twardy**, a następnie kliknij przycisk **Nowy**. Zostanie uruchomiony **Kreator nowego wirtualnego dysku twardego**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Na stronie **Przed rozpoczęciem** w kreatorze nowego wirtualnego dysku twardego kliknij przycisk **Dalej**.
16. Na stronie **Wybieranie formatu dysku** zaakceptuj domyślną opcję formatu **VHDX**. Kliknij przycisk **alej**. Ten ekran nie jest wyświetlany, jeśli jest uruchomiony system Windows Server 2008 R2.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Na stronie **Wybieranie typu dysku** ustaw typ wirtualnego dysku twardego **Dynamicznie powiększający się** (zalecane). Dysk **O stałym rozmiarze** również byłby odpowiedni, ale jego tworzenie mogłoby zająć więcej czasu. Nie zalecamy używania opcji **Różnicowy**. Kliknij przycisk **alej**. W systemach Windows Server 2012 R2 i Windows Server 2012 **dynamiczne rozwijanie** jest opcją domyślną, podczas gdy w systemie Windows Server 2008 R2 domyślnym **rozmiarem**jest Stały rozmiar .

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Na stronie **Określanie nazwy i lokalizacji** podaj **nazwę** dysku danych oraz jego **lokalizację** (możesz skorzystać z opcji Przeglądaj). Kliknij przycisk **alej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Na stronie **Konfigurowanie dysku** wybierz opcję **Utwórz nowy pusty wirtualny dysk twardy** i określ rozmiar **500 GB** (lub więcej). Podczas gdy 500 GB jest minimalnym wymaganiem, zawsze można aprowizować większy dysk. Należy zauważyć, że nie można rozwinąć ani zmniejszyć dysku po zainicjowaniu obsługi administracyjnej. Aby uzyskać więcej informacji na temat rozmiaru dysku do zainicjowania, zapoznaj się z sekcją zmiany rozmiaru w [dokumencie najlepszych praktyk](storsimple-ova-best-practices.md). Kliknij przycisk **alej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Sprawdź dane dysku wirtualnego na stronie **Podsumowanie** i, jeśli wszystkie dane są prawidłowe, kliknij przycisk **Zakończ**, aby utworzyć dysk. Kreator zostanie zamknięty, a wirtualny dysk twardy zostanie dodany do maszyny.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Wróć do strony **Ustawienia**. Kliknij przycisk **OK**, aby zamknąć stronę **Ustawienia**, a następnie wróć do okna Menedżera funkcji Hyper-V.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Krok 3: Uruchom tablicę wirtualną i uzyskaj adres IP
Wykonaj następujące kroki, aby uruchomić tablicę wirtualną i połączyć się z nią.

#### <a name="to-start-the-virtual-array"></a>Aby uruchomić tablicę wirtualną
1. Uruchom tablicę wirtualną.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Po uruchomieniu urządzenia wybierz je, kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Połącz**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Może być trzeba czekać 5-10 minut, aby urządzenie było gotowe. W konsoli zostanie wyświetlony komunikat o stanie, wskazujący postęp procesu. Gdy urządzenie będzie gotowe, przejdź do pozycji **Akcja**. Naciśnij, `Ctrl + Alt + Delete` aby zalogować się do tablicy wirtualnej. Domyślnym użytkownikiem jest *StorSimpleAdmin,* a domyślnym hasłem jest *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Ze względów bezpieczeństwa hasło administratora urządzenia wygasa przy pierwszym logowaniu. Zostanie wyświetlony monit informujący o konieczności zmiany hasła.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Wprowadź hasło zawierające co najmniej 8 znaków. Hasło musi zawierać co najmniej 3 z następujących 4 elementów: wielkie litery, małe litery, cyfry i znaki specjalne. Wprowadź ponownie hasło w celu potwierdzenia. Otrzymasz powiadomienie, że hasło zostało zmienione.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Po pomyślnej zmianie hasła tablica wirtualna może zostać ponownie uruchomiona. Poczekaj na uruchomienie urządzenia.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Zostanie wyświetlona konsola programu Windows PowerShell urządzenia wraz z paskiem postępu.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Kroki od 6 do 8 mają zastosowanie tylko w przypadku uruchamiania w środowisku bez protokołu DHCP. W przypadku środowiska protokołu DHCP należy pominąć te kroki i przejść do kroku 9. Jeśli urządzenie uruchomiliśmy w środowisku innych niż DHCP, zostanie wyświetlony następujący ekran.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Następnie skonfiguruj sieć.
7. Użyj `Get-HcsIpAddress` polecenia, aby wyświetlić listę interfejsów sieciowych włączonych w macierzy wirtualnej. Jeśli urządzenie ma włączony jeden interfejs sieciowy, jego domyślną nazwą jest `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Skonfiguruj sieć za pomocą polecenia cmdlet `Set-HcsIpAddress`. Zobacz poniższy przykład:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Po zakończeniu początkowej konfiguracji i uruchomieniu urządzenia zobaczysz tekst baneru urządzenia. Zanotuj adresy IP i URL wyświetlane w tekście baneru. Posłużą one do zarządzania urządzeniem. Ten adres IP służy do łączenia się z internetowym interfejsem użytkownika tablicy wirtualnej i ukończenia konfiguracji lokalnej i rejestracji.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Opcjonalnie) Ten krok należy wykonać tylko w przypadku wdrażania urządzenia w chmurze instytucji rządowych. Teraz włączysz tryb FIPS (United States Federal Information Processing Standard) na swoim urządzeniu. Standard FIPS 140 definiuje algorytmy kryptograficzne zatwierdzone do użytku przez amerykańskie systemy komputerowe rządu federalnego w celu ochrony poufnych danych.

    1. Aby włączyć tryb FIPS, uruchom następujące polecenie cmdlet:

        `Enable-HcsFIPSMode`
    2. Uruchom ponownie urządzenie po włączeniu trybu FIPS, aby umożliwić zajście w stan działania.

       > [!NOTE]
       > W urządzeniu można włączyć lub wyłączyć tryb FIPS. Przemiennie urządzenia między trybem FIPS i nie-FIPS nie jest obsługiwane.
       >
       >

Jeśli urządzenie nie spełnia minimalnych wymagań konfiguracyjnych, w tekście baneru (pokazanym poniżej) zostanie wyświetlony następujący błąd. Zmodyfikuj konfigurację urządzenia tak, aby zapewnić zasoby zgodne z wymaganiami minimalnymi. Następnie możesz ponownie uruchomić urządzenie i połączyć się z nim. Zapoznaj się z minimalnymi wymaganiami konfiguracyjnymi w kroku 1: Upewnij się, że system hosta spełnia minimalne wymagania tablicy wirtualnej.

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Jeśli podczas początkowej konfiguracji przy użyciu lokalnego interfejsu użytkownika sieci Web napotkasz inny błąd, zapoznaj się z następującymi przepływami pracy:

* Uruchom testy diagnostyczne w celu [rozwiązywania problemów z konfiguracją interfejsu użytkownika sieci](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)Web .
* [Generowanie pakietu dziennika i wyświetlania plików dziennika](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Następne kroki
* [Konfigurowanie tablicy wirtualnej StorSimple jako serwera plików](storsimple-virtual-array-deploy3-fs-setup.md)
* [Konfigurowanie tablicy wirtualnej StorSimple jako serwera iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
