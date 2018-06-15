---
title: Zapewnij tablicy wirtualnego StorSimple w funkcji Hyper-V | Dokumentacja firmy Microsoft
description: W tym samouczku drugi we wdrożeniu tablicy wirtualnego StorSimple obejmuje udostępniania wirtualnego tablicy w funkcji Hyper-V.
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
ms.openlocfilehash: bad431c8958f7d381bb9c0410caa3a57c6e75c19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876593"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Wdrażanie tablicy wirtualnego StorSimple - Provision w funkcji Hyper-V
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Omówienie
W tym samouczku opisano, jak zainicjować tablicą wirtualnego StorSimple na komputerze hosta z funkcją Hyper-V w systemie Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2. Ten artykuł dotyczy wdrożenia tablice wirtualnego StorSimple w portalu Azure i Microsoft Azure dla instytucji rządowych chmury.

Potrzebne są uprawnienia administratora, aby udostępnić i skonfigurować wirtualny tablicy. Inicjowania obsługi administracyjnej i początkowej instalacji może zająć około 10 minut, aby zakończyć.

## <a name="provisioning-prerequisites"></a>Wymagania wstępne dotyczące inicjowania obsługi administracyjnej
Tutaj znajdziesz warunków wstępnych, aby udostępnić wirtualny tablicy na komputerze hosta z funkcją Hyper-V w systemie Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple
Przed rozpoczęciem upewnij się, że:

* Zostały ukończone wszystkie kroki [przygotowanie portalu dla tablicy wirtualnego StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Po jej pobraniu obrazu tablicy wirtualnego funkcji Hyper-v z portalu Azure. Aby uzyskać więcej informacji, zobacz **krok 3: Pobierz obraz wirtualnego tablicy** z [przygotowanie portalu przewodnik tablicy wirtualnego StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > Oprogramowanie działające na tablicy wirtualnego StorSimple można używać tylko w usłudze Menedżer StorSimple urządzenia.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Dla tablicy wirtualnego StorSimple
Przed wdrożeniem wirtualnego tablicy, upewnij się, że:

* Masz dostęp do systemu hosta funkcji Hyper-V w systemie Windows Server 2008 R2 lub nowszym, który może być używany do udostępniania urządzenia.
* System hosta jest w stanie dedykować następujących zasobów, aby udostępnić wirtualny tablica:

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurować wirtualny tablicy jako serwer plików, 8 GB obsługuje mniej niż 2 miliony plików. Należy 16 GB pamięci RAM 2-4 miliony plików obsługi.
  * Jeden interfejs sieciowy.
  * Dysk wirtualny 500 GB danych.

### <a name="for-the-network-in-the-datacenter"></a>Sieć w centrum danych
Przed rozpoczęciem należy zapoznać się z wymaganiami sieci można wdrożyć tablicą wirtualnego StorSimple i odpowiednio skonfigurować sieci centrum danych. Aby uzyskać więcej informacji, zobacz [tablicy wirtualnego StorSimple wymagań sieciowych](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Krok po kroku inicjowania obsługi administracyjnej
Do obsługi administracyjnej i połączenia wirtualnej tablicy, należy wykonać następujące czynności:

1. Upewnij się, że systemu hosta ma wystarczające zasoby, aby spełnić wymagania minimalne tablicy wirtualnego.
2. Udostępnianie wirtualnych tablicy w Twojej funkcji hypervisor.
3. Uruchom wirtualnego tablicy i uzyskać adres IP.

Każdy z tych kroków znajduje się w poniższych sekcjach.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Krok 1: Upewnij się, że system hosta spełnia wymagania minimalne tablicy wirtualnego
Aby utworzyć wirtualny tablicy, potrzebne są:

* Rola funkcji Hyper-V, w systemie Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2 z dodatkiem SP1.
* Microsoft Hyper-V Manager na komputerze klienckim Microsoft Windows podłączona do hosta.

Upewnij się, że używany sprzęt (w systemie hosta), na którym tworzysz wirtualnego tablicy jest w stanie przeznaczyć w następujących zasobach tablica wirtualnego:

* Co najmniej 4 rdzenie.
* Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurować wirtualny tablicy jako serwer plików, 8 GB obsługuje mniej niż 2 miliony plików. Należy 16 GB pamięci RAM 2-4 miliony plików obsługi.
* Jeden interfejs sieciowy.
* Dysk wirtualny 500 GB danych systemu.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Krok 2: Udostępnianie wirtualnych tablicy w funkcji hypervisor
Wykonaj poniższe kroki, aby udostępnić urządzenie w Twojej funkcji hypervisor.

#### <a name="to-provision-a-virtual-array"></a>Aby udostępnić wirtualny tablicy
1. Na hoście z systemem Windows Server kopiowanie obrazu tablicy wirtualny dysk lokalny. Pobrano tego obrazu (plik VHD lub VHDX) za pośrednictwem portalu Azure. Zanotuj lokalizację, w których obraz został skopiowany korzystania z tego obrazu w dalszej części procedury.
2. Otwórz **Menedżera serwera**. W prawym górnym rogu, kliknij przycisk **narzędzia** i wybierz **Menedżera funkcji Hyper-V**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Jeśli używasz systemu Windows Server 2008 R2 otwórz Menedżera funkcji Hyper-V. W Menedżerze serwera kliknij **ról > funkcji Hyper-V > Menedżera funkcji Hyper-V**.
3. W **Menedżera funkcji Hyper-V**, w okienku zakres, kliknij prawym przyciskiem myszy węzeł systemu, aby otworzyć menu kontekstowe, a następnie kliknij przycisk **nowy** > **maszyny wirtualnej**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Na **przed rozpoczęciem** strony Kreatora nowej maszyny wirtualnej, kliknij przycisk **dalej**.
5. Na **Określ nazwę i lokalizację** podaj **nazwa** dla sieci wirtualnej tablicy. Kliknij przycisk **Dalej**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Na **określić generację** , wybierz typ obrazu urządzenia, a następnie kliknij przycisk **dalej**. Ta strona nie zostanie wyświetlone, jeśli używasz systemu Windows Server 2008 R2.

   * Wybierz **generacji 2** Jeśli pobrany obraz vhdx dla systemu Windows Server 2012 lub nowszego.
   * Wybierz **generacja 1** Jeśli pobrano obrazu vhd systemu Windows Server 2008 R2 lub nowszym.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Na **przypisanie pamięci** określ **pamięć początkowa** z co najmniej **8192 MB**, nie włączać pamięci dynamicznej, a następnie kliknij przycisk **dalej**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Na **Konfigurowanie sieci** strony, określ przełącznik wirtualny, który jest połączony z Internetem, a następnie kliknij przycisk **dalej**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Na **Podłączanie wirtualnego dysku twardego** wybierz pozycję **Użyj istniejącego wirtualnego dysku twardego**Określ lokalizację obrazu tablicy wirtualnego (vhdx lub VHD), a następnie kliknij przycisk **dalej**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Przegląd **Podsumowanie** , a następnie kliknij przycisk **Zakończ** można utworzyć maszyny wirtualnej.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Aby spełnić wymagania minimalne, należy 4 rdzenie. Aby dodać 4 procesory wirtualne, wybierz system hosta w **Menedżera funkcji Hyper-V** okna. W prawym okienku w obszarze listy **maszyn wirtualnych**, odszukaj nowo utworzoną maszynę wirtualną. Wybierz i kliknij prawym przyciskiem myszy nazwę komputera i wybierz **ustawienia**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Na **ustawienia** w lewym okienku kliknij **procesora**. W prawym okienku zestawu **liczba procesorów wirtualnych** 4 (lub więcej). Kliknij przycisk **Zastosuj**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Aby spełnić wymagania minimalne, należy również dodać dysk wirtualny danych 500 GB. W **ustawienia** strony:

    1. W okienku po lewej stronie wybierz **kontroler SCSI**.
    2. W okienku po prawej stronie zaznacz **dysk twardy** i kliknij przycisk **Dodaj**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Na **dysk twardy** wybierz pozycję **wirtualny dysk twardy** opcję i kliknij przycisk **nowy**. **Kreatora nowego wirtualnego dysku twardego** uruchamia.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Na **przed rozpoczęciem** strony Kreatora nowego wirtualnego dysku twardego, kliknij przycisk **dalej**.
16. Na **strona Wybieranie formatu dysku**, zaakceptuj domyślną opcją **VHDX** format. Kliknij przycisk **Dalej**. Ten ekran nie jest podana, jeśli system Windows Server 2008 R2.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Na **stronę Wybieranie typu dysku**, Ustaw typ wirtualnego dysku twardego jako **dynamicznie powiększających się** (zalecane). **Stały rozmiar** dysku będzie działać, ale może być konieczne odczekanie przez długi czas. Firma Microsoft zaleca, aby nie używać **Differencing** opcji. Kliknij przycisk **Dalej**. W systemie Windows Server 2012 R2 i Windows Server 2012 **dynamicznie powiększających się** jest domyślną opcją w systemie Windows Server 2008 R2, jest domyślnie **stały rozmiar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Na **Określanie nazwy i lokalizacji** podaj **nazwa** oraz **lokalizacji** (można przejść do jeden) dla dysku danych. Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Na **Konfigurowanie dysku** wybierz opcję **utworzyć nowy pusty wirtualny dysk twardy** i określ rozmiar jako **500 GB** (lub więcej). Minimalnym wymaganiem jest 500 GB, będzie zawsze możliwe alokowanie większy dysk. Należy pamiętać, nie można rozwinąć lub zmniejszania dysku raz udostępnione. Aby uzyskać więcej informacji na rozmiar dysku, aby udostępnić, przejrzyj sekcję zmiany rozmiaru w [najlepszych praktyk dokumentu](storsimple-ova-best-practices.md). Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Na **Podsumowanie** , przejrzyj szczegóły dysku wirtualnego danych i jeśli spełnione, kliknij przycisk **Zakończ** można utworzyć na dysku. Powoduje zamknięcie kreatora i dodaniu wirtualnego dysku twardego na komputerze.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Wróć do **ustawienia** strony. Kliknij przycisk **OK** zamknąć **ustawienia** strony i powrócić do okna Menedżera funkcji Hyper-V.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Krok 3: Uruchom wirtualnego tablicy i Uzyskaj adres IP
Wykonaj poniższe kroki, aby uruchomić tablica wirtualnego i nawiązać z nią.

#### <a name="to-start-the-virtual-array"></a>Aby uruchomić wirtualnego tablicy
1. Uruchom wirtualnego tablicy.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Po uruchomieniu urządzenia, wybierz urządzenie, kliknij prawym przyciskiem myszy, a następnie wybierz **Connect**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Może być konieczne Zaczekaj 5 – 10 minut na urządzenie będzie gotowa. Komunikat o stanie jest wyświetlana na konsoli do informowania o postępie. Gdy urządzenie jest gotowe, przejdź do **akcji**. Naciśnij klawisz `Ctrl + Alt + Delete` logować się do wirtualnego tablicy. Użytkownik domyślny *StorSimpleAdmin* i domyślne hasło jest *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Ze względów bezpieczeństwa hasło administratora urządzenia wygasa przy pierwszym logowaniu. Monit o zmianę hasła.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Wprowadź hasło o długości co najmniej 8 znaków. Hasło musi spełniać co najmniej 3 z 4 następujące wymagania: wielkie litery, małe litery, liczbowego i znaki specjalne. Wpisz ponownie hasło, aby je potwierdzić. Użytkownik jest powiadamiany, że hasło została zmieniona.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Po pomyślnym zmiany hasła może ponownie uruchomić wirtualnego tablicy. Poczekaj, aż urządzenie, aby rozpocząć.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Konsolę programu Windows PowerShell urządzenia zostanie wyświetlona wraz z pasek postępu.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Kroki od 6 do 8 stosowane tylko wtedy, gdy rozruch w środowisku protokołu DHCP. Jeśli pracujesz w środowisku DHCP, Pomiń te kroki i przejdź do kroku 9. Jeśli uruchomiono zapasowych urządzenia w środowisku protokołu DHCP, zostanie wyświetlony następujący ekran.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Skonfiguruj sieci.
7. Użyj `Get-HcsIpAddress` Aby wyświetlić listę interfejsów sieciowych, włączone na tablica wirtualnego. Jeśli urządzenie ma jeden interfejs sieci włączone, domyślną nazwą przypisane do tego interfejsu jest `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Użyj `Set-HcsIpAddress` polecenia cmdlet, aby skonfigurować sieć. Zobacz poniższy przykład:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Po zakończeniu początkowej konfiguracji, urządzenie uruchomi się w górę, pojawi się tekst transparentu urządzenia. Zanotuj adres IP i adres URL wyświetlany w tekście transparent do zarządzania urządzeniem. Użyj tego adresu IP, aby nawiązać interfejsu użytkownika macierzy wirtualnego sieci web i ukończyć lokalnego instalację i rejestrację.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Opcjonalnie) Wykonaj ten krok tylko wtedy, gdy wdrażasz urządzenia w chmurze dla instytucji rządowych. Na urządzeniu zostanie teraz włączyć tryb Stanów Zjednoczonych informacji przetwarzania Standard FIPS (Federal). Standard FIPS 140 definiuje zatwierdzone do użycia przez nas federalne dla instytucji rządowych systemów komputerowych do ochrony danych poufnych algorytmów kryptograficznych.

    1. Aby włączyć tryb FIPS, uruchom następujące polecenie cmdlet:

        `Enable-HcsFIPSMode`
    2. Po włączeniu trybu FIPS, dzięki czemu kryptograficzne operacji sprawdzania poprawności zostały zastosowane, należy zrestartować urządzenie.

       > [!NOTE]
       > Można włączyć lub wyłączyć tryb FIPS na urządzeniu. Naprzemiennych urządzenia między trybem FIPS i z systemem innym niż FIPS nie jest obsługiwane.
       >
       >

Jeśli urządzenie nie spełnia minimalne wymagania dotyczące konfiguracji, zostanie wyświetlony następujący błąd w tekście transparent (pokazana poniżej). Tak, aby komputer miał odpowiednie zasoby, aby spełniać minimalne wymagania, należy zmodyfikować konfigurację urządzenia. Następnie można ponownie uruchomić i nawiąż połączenie z urządzeniem. Wymagania minimalnej konfiguracji w [krok 1: Upewnij się, że system hosta spełnia wymagania minimalne tablicy wirtualnego](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Inny błąd czoła podczas początkowej konfiguracji przy użyciu lokalnego interfejsu użytkownika sieci web, można znaleźć następujących przepływy pracy:

* Uruchamianie testów diagnostycznych do [Rozwiązywanie problemów z instalacją interfejsu użytkownika sieci web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generowanie pakietu dziennika i wyświetlanie plików dziennika](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Następne kroki
* [Konfigurowanie tablica wirtualnego StorSimple jako serwer plików](storsimple-virtual-array-deploy3-fs-setup.md)
* [Konfigurowanie tablica wirtualnego StorSimple iSCSI na serwerze](storsimple-virtual-array-deploy3-iscsi-setup.md)
