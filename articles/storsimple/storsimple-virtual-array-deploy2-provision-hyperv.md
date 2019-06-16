---
title: Aprowizowanie macierzy wirtualnej StorSimple w funkcji Hyper-V | Dokumentacja firmy Microsoft
description: Ten drugi samouczek we wdrożeniu rozwiązania StorSimple Virtual Array obejmuje Inicjowanie obsługi administracyjnej macierzy wirtualnej w funkcji Hyper-V.
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
ms.date: 03/15/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5104d630e2b4e97b80a6fedfb6d863061c2722fb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61416341"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Wdrażanie macierzy wirtualnej StorSimple — Aprowizacja za pomocą funkcji Hyper-V
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Omówienie
W tym samouczku opisano, jak wykonać aprowizację rozwiązania StorSimple Virtual Array na komputerze hosta z funkcją Hyper-V w systemie Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2. Ten artykuł dotyczy wdrożenia macierzach wirtualnych StorSimple w witrynie Azure portal i chmury platformy Microsoft Azure dla instytucji rządowych.

Niezbędne są uprawnienia administratora do aprowizacji i konfigurowanie macierzy wirtualnej. Aprowizacja i wstępna konfiguracja może zająć około 10 minut.

## <a name="provisioning-prerequisites"></a>Inicjowanie obsługi administracyjnej wstępnie wymagane składniki
W tym miejscu znajdziesz wymagania wstępne dotyczące aprowizowanie macierzy wirtualnej w systemie hosta z funkcją Hyper-V w systemie Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple
Przed rozpoczęciem upewnij się, że:

* Zostały wykonane wszystkie kroki [przygotowywanie portalu dla rozwiązania StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Pobrano obraz macierzy wirtualnej funkcji Hyper-v w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz **krok 3: Pobierz obraz macierzy wirtualnej** z [przygotowywanie portalu przewodnik rozwiązania StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > Oprogramowanie działające na macierz wirtualną StorSimple można używać tylko w usłudze Menedżer urządzeń StorSimple.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Dla macierzy wirtualnej StorSimple
Przed wdrożeniem macierzy wirtualnej, upewnij się, że:

* Masz dostęp do systemu hosta funkcji Hyper-V w systemie Windows Server 2008 R2 lub nowszym, które mogą być używane do aprowizacji urządzenia.
* System hosta jest w stanie dedykować następujących zasobów, aby aprowizować macierz wirtualna:

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli użytkownik chce skonfigurować macierzy wirtualnej jako serwera plików, 8 GB obsługuje mniej niż 2 miliony plików. Należy 16 GB pamięci RAM na potrzeby obsługi 2-4 miliony plików.
  * Jeden interfejs sieciowy.
  * Dysk wirtualny 500 GB danych.

### <a name="for-the-network-in-the-datacenter"></a>Sieć w centrum danych
Przed rozpoczęciem należy przejrzeć wymagania sieciowe wdrażania rozwiązania StorSimple Virtual Array i odpowiednio skonfigurować sieci centrum danych. Aby uzyskać więcej informacji, zobacz [rozwiązania StorSimple Virtual Array wymagania sieciowe](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Instrukcje krok po kroku inicjowania obsługi administracyjnej
Do aprowizacji i nawiązać połączenie z macierzy wirtualnej, należy wykonać następujące czynności:

1. Upewnij się, że system hosta ma wystarczające zasoby do spełnienia wymagań minimalnych macierzy wirtualnej.
2. Aprowizowanie macierzy wirtualnej w Twojej funkcji hypervisor.
3. Uruchom macierz wirtualną i Uzyskaj adres IP.

Każdy z tych kroków jest opisane w poniższych sekcjach.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Krok 1: Upewnij się, że system hosta spełnia wymagania minimalne macierzy wirtualnej
Aby utworzyć macierz wirtualną, potrzebne są:

* Rola funkcji Hyper-V zainstalowana na Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2 z dodatkiem SP1.
* Menedżer funkcji Microsoft Hyper-V na kliencie z systemem Microsoft Windows połączonym z hostem.

Upewnij się, że bazowego sprzętu (w systemie hosta), na którym tworzona macierz wirtualna jest w stanie przeznaczyć w następujących zasobach macierz wirtualna:

* Co najmniej 4 rdzenie.
* Co najmniej 8 GB pamięci RAM. Jeśli użytkownik chce skonfigurować macierzy wirtualnej jako serwera plików, 8 GB obsługuje mniej niż 2 miliony plików. Należy 16 GB pamięci RAM na potrzeby obsługi 2-4 miliony plików.
* Jeden interfejs sieciowy.
* Dysk wirtualny 500 GB danych systemu.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Krok 2: Aprowizowanie macierzy wirtualnej w funkcji hypervisor
Wykonaj następujące czynności, aby aprowizować urządzenie w funkcji hypervisor.

#### <a name="to-provision-a-virtual-array"></a>Do aprowizowania macierzy wirtualnej
1. Na hoście systemu Windows Server należy skopiować obraz macierzy wirtualnej na dysk lokalny. Ten obraz (VHD lub VHDX) są pobierane za pośrednictwem witryny Azure portal. Zanotuj lokalizację, do której został skopiowany obraz, ponieważ będzie on używany w dalszej części tej procedury.
2. Otwórz **Menedżera serwera**. W prawym górnym rogu kliknij przycisk **Narzędzia** i wybierz pozycję **Menedżer funkcji Hyper-V**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Jeśli używasz systemu Windows Server 2008 R2, otwórz Menedżera funkcji Hyper-V. W Menedżerze serwera kliknij **role > funkcji Hyper-V > Menedżer funkcji Hyper-V**.
3. W **Menedżerze funkcji Hyper-V** w okienku zakresu kliknij prawym przyciskiem myszy węzeł systemu, aby otworzyć menu kontekstowe, a następnie kliknij kolejno pozycje **Nowy** > **Maszyna wirtualna**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Na stronie **Przed rozpoczęciem** w kreatorze nowej maszyny wirtualnej kliknij przycisk **Dalej**.
5. Na **Określ nazwę i lokalizację** podaj **nazwa** dla macierz wirtualna. Kliknij przycisk **Dalej**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Na **Określanie generacji** strony, wybierz typ obrazu urządzenia, a następnie kliknij przycisk **dalej**. Jeśli używasz systemu Windows Server 2008 R2 nie jest wyświetlane na tej stronie.

   * Wybierz **generacja 2** Jeśli pobrano obraz vhdx dla systemu Windows Server 2012 lub nowszego.
   * Wybierz **generacja 1** Jeśli pobrano obrazu vhd systemu Windows Server 2008 R2 lub nowszym.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Na stronie **Przypisywanie pamięci** w polu **Pamięć początkowa** wprowadź wartość nie mniejszą niż **8192 MB**, nie włączaj pamięci dynamicznej, a następnie kliknij przycisk **Dalej**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Na stronie **Konfigurowanie sieci** określ przełącznik wirtualny połączony z Internetem, a następnie kliknij przycisk **Dalej**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Na **Podłączanie wirtualnego dysku twardego** wybierz **Użyj istniejącego wirtualnego dysku twardego**Określ lokalizację obrazu macierzy wirtualnej (vhdx lub VHD), a następnie kliknij przycisk **dalej**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Przejrzyj informacje na stronie **Podsumowanie**, a następnie kliknij pozycję **Zakończ**, aby utworzyć maszynę wirtualną.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Aby spełnić wymagania minimalne, potrzebujesz 4 rdzeni. Aby dodać 4 procesory wirtualne, wybierz swój system hosta w oknie **Menedżera funkcji Hyper-V**. Na liście **Maszyny wirtualne** w okienku po prawej stronie znajdź utworzoną przed chwilą maszynę wirtualną. Zaznacz i kliknij prawym przyciskiem myszy nazwę maszyny, a następnie wybierz pozycję **Ustawienia**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Na stronie **Ustawienia** w lewym okienku kliknij pozycję **Procesor**. W okienku po prawej stronie ustaw wartość 4 lub większą w polu **Liczba procesorów wirtualnych**. Kliknij przycisk **Zastosuj**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Aby spełnić wymagania minimalne, należy również dodać dysk wirtualny danych 500 GB. Na stronie **Ustawienia**:

    1. W okienku po lewej stronie wybierz pozycję **Kontroler SCSI**.
    2. W okienku po prawej stronie wybierz pozycję **Dysk twardy** i kliknij przycisk **Dodaj**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Na stronie **Dysk twardy** wybierz opcję **Wirtualny dysk twardy**, a następnie kliknij przycisk **Nowy**. Zostanie uruchomiony **Kreator nowego wirtualnego dysku twardego**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Na stronie **Przed rozpoczęciem** w kreatorze nowego wirtualnego dysku twardego kliknij przycisk **Dalej**.
16. Na stronie **Wybieranie formatu dysku** zaakceptuj domyślną opcję formatu **VHDX**. Kliknij przycisk **Dalej**. Ten ekran nie jest wyświetlane, jeżeli z systemem Windows Server 2008 R2.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Na stronie **Wybieranie typu dysku** ustaw typ wirtualnego dysku twardego **Dynamicznie powiększający się** (zalecane). Dysk **O stałym rozmiarze** również byłby odpowiedni, ale jego tworzenie mogłoby zająć więcej czasu. Nie zalecamy używania opcji **Różnicowy**. Kliknij przycisk **Dalej**. W systemie Windows Server 2012 R2 i Windows Server 2012 **dynamicznie powiększających się** to opcja domyślna, natomiast w systemie Windows Server 2008 R2, wartość domyślna to **ustalony rozmiar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Na stronie **Określanie nazwy i lokalizacji** podaj **nazwę** dysku danych oraz jego **lokalizację** (możesz skorzystać z opcji Przeglądaj). Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Na **Konfigurowanie dysku** stronie, wybierz opcję **Tworzenie nowego pustego wirtualnego dysku twardego** i określ rozmiar jako **500 GB** (lub więcej). 500 GB, jest minimalnym wymaganiem, zawsze można udostępnić większy dysk. Należy pamiętać, nie można rozwinąć lub zmniejszania dysku po aprowizacji. Aby uzyskać więcej informacji na podstawie rozmiaru dysku, aby aprowizować, zapoznaj się z sekcją zmiany rozmiaru, w [najlepszych praktyk dokumentu](storsimple-ova-best-practices.md). Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Sprawdź dane dysku wirtualnego na stronie **Podsumowanie** i, jeśli wszystkie dane są prawidłowe, kliknij przycisk **Zakończ**, aby utworzyć dysk. Kreator zostanie zamknięty, a wirtualny dysk twardy zostanie dodany do maszyny.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Wróć do strony **Ustawienia**. Kliknij przycisk **OK**, aby zamknąć stronę **Ustawienia**, a następnie wróć do okna Menedżera funkcji Hyper-V.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Krok 3: Uruchom macierz wirtualną i Uzyskaj adres IP
Wykonaj poniższe kroki, aby uruchomić macierz wirtualna i nawiązać z nim.

#### <a name="to-start-the-virtual-array"></a>Aby uruchomić macierzy wirtualnej
1. Uruchom macierz wirtualną.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Po uruchomieniu urządzenia wybierz je, kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Połącz**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Może być konieczne poczekaj 5 – 10 minut, zanim urządzenie będzie gotowa. W konsoli zostanie wyświetlony komunikat o stanie, wskazujący postęp procesu. Gdy urządzenie będzie gotowe, przejdź do pozycji **Akcja**. Naciśnij klawisz `Ctrl + Alt + Delete` zalogować się do macierzy wirtualnej. Użytkownik domyślny jest *StorSimpleAdmin* i domyślne hasło *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Ze względów bezpieczeństwa hasło administratora urządzenia wygasa przy pierwszym logowaniu. Zostanie wyświetlony monit informujący o konieczności zmiany hasła.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Wprowadź hasło zawierające co najmniej 8 znaków. Hasło musi zawierać co najmniej 3 z następujących 4 elementów: wielkie litery, małe litery, cyfry i znaki specjalne. Wprowadź ponownie hasło w celu potwierdzenia. Otrzymasz powiadomienie, że hasło zostało zmienione.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Po pomyślnym zmiany hasła może ponownie uruchomić macierzy wirtualnej. Poczekaj na uruchomienie urządzenia.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Zostanie wyświetlona konsola programu Windows PowerShell urządzenia wraz z paskiem postępu.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Kroki od 6 do 8 mają zastosowanie tylko w przypadku uruchamiania w środowisku bez protokołu DHCP. W przypadku środowiska protokołu DHCP należy pominąć te kroki i przejść do kroku 9. Jeśli uruchomiono konfigurację urządzenia w środowisku protokołu DHCP, zostanie wyświetlony następujący ekran.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Następnie skonfiguruj sieci.
7. Użyj `Get-HcsIpAddress` polecenie, aby wyświetlić listę interfejsów sieciowych, włączone na macierz wirtualna. Jeśli urządzenie ma włączony jeden interfejs sieciowy, jego domyślną nazwą jest `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Skonfiguruj sieć za pomocą polecenia cmdlet `Set-HcsIpAddress`. Zobacz poniższy przykład:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Po zakończeniu początkowej konfiguracji i uruchomieniu urządzenia zobaczysz tekst baneru urządzenia. Zanotuj adresy IP i URL wyświetlane w tekście baneru. Posłużą one do zarządzania urządzeniem. Ten adres IP umożliwia nawiązywanie połączenia internetowego interfejsu użytkownika macierz wirtualna i ukończyć konfigurowanie lokalnego i rejestracji.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Opcjonalnie) Ten krok należy wykonać tylko wtedy, gdy są wdrażane urządzenia w chmurze dla instytucji rządowych. Na urządzeniu, będzie teraz Włącz tryb Stanów Zjednoczonych informacji przetwarzania Standard FIPS (Federal). Standard FIPS 140 definiuje zatwierdzone do użycia przez nas federalnych instytucji rządowych systemów komputerowych ochrony poufnych danych algorytmów kryptograficznych.

    1. Aby włączyć tryb ze standardem FIPS, uruchom następujące polecenie cmdlet:

        `Enable-HcsFIPSMode`
    2. Po włączeniu trybu FIPS, tak, aby kryptograficznych walidacji zaczęły obowiązywać, należy ponownie uruchomić urządzenie.

       > [!NOTE]
       > Można włączyć lub wyłączyć tryb FIPS na urządzeniu. Przełączanie urządzeń między trybem FIPS i ze standardem FIPS nie jest obsługiwane.
       >
       >

Jeśli urządzenie nie spełnia minimalnych wymagań dotyczących konfiguracji, zostanie wyświetlony następujący błąd w tekście transparent (pokazana poniżej). Zmodyfikuj konfigurację urządzenia tak, aby zapewnić zasoby zgodne z wymaganiami minimalnymi. Następnie możesz ponownie uruchomić urządzenie i połączyć się z nim. Zapoznaj się z wymaganiami minimalnej konfiguracji w kroku 1: Upewnij się, że system hosta spełnia wymagania minimalne macierzy wirtualnej.

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Jeśli napotkasz inny błąd podczas konfiguracji początkowej, przy użyciu lokalnego Interfejsu w przeglądarce odwoływać się do następujących przepływów:

* Uruchom testy diagnostyczne do [Rozwiązywanie problemów z instalacją interfejsu użytkownika sieci web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Wygeneruj pakiet dziennika i wyświetlanie plików dziennika](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Kolejne kroki
* [Konfigurowanie rozwiązania StorSimple Virtual Array do serwera jako serwera plików](storsimple-virtual-array-deploy3-fs-setup.md)
* [Konfigurowanie rozwiązania StorSimple Virtual Array do serwera jako serwera iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
