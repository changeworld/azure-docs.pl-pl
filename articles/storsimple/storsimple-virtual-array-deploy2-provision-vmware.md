---
title: Zapewnij tablicy wirtualnego StorSimple w środowisku programu VMware | Dokumentacja firmy Microsoft
description: W tym samouczku drugi w tablicy wirtualnego StorSimple wdrażania serii obejmuje Inicjowanie obsługi urządzenia wirtualnego w środowisku programu VMware.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: be737550aa1cff22bc413ee8ce243f8d8588f6a5
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
ms.locfileid: "29972344"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Wdrażanie tablicy wirtualnego StorSimple - Provision w środowisku programu VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Przegląd
Ten samouczek zawiera opis sposobu udostępniania i Połącz z tablicą wirtualnego StorSimple na komputerze hosta z systemem VMware ESXi 5.0, 5.5 lub 6.0. Ten artykuł dotyczy wdrożenia tablice wirtualnego StorSimple w portalu Azure i w chmurze Microsoft Azure dla instytucji rządowych.

Potrzebne są uprawnienia administratora, aby udostępnić i nawiązać połączenia z urządzeniem wirtualnym. Inicjowania obsługi administracyjnej i początkowej instalacji może zająć około 10 minut, aby zakończyć.

## <a name="provisioning-prerequisites"></a>Wymagania wstępne dotyczące inicjowania obsługi administracyjnej
Dostępne są następujące wymagania wstępne dotyczące udostępniania urządzenia wirtualnego na komputerze hosta z systemem VMware ESXi 5.0, 5.5 lub w wersji 6.0.

### <a name="for-the-storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple
Przed rozpoczęciem upewnij się, że:

* Zostały ukończone wszystkie kroki [przygotowanie portalu dla tablicy wirtualnego StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Po jej pobraniu obrazu urządzenia wirtualnego dla VMware z portalu Azure. Aby uzyskać więcej informacji, zobacz **krok 3: pobranie obrazu urządzenia wirtualnego** z [przygotowanie portalu przewodnik tablicy wirtualnego StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Dla urządzenia wirtualnego StorSimple
Przed wdrożeniem urządzenia wirtualnego, upewnij się, że:

* Masz dostęp do systemu hosta funkcji Hyper-V (2008 R2 lub nowszy) które może być używane do udostępniania urządzenia.
* System hosta jest w stanie dedykować następujących zasobów, aby udostępnić urządzenia wirtualnego:

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurować wirtualny tablicy jako serwer plików, 8 GB obsługuje mniej niż 2 miliony plików. Należy 16 GB pamięci RAM 2-4 miliony plików obsługi.
  * Jeden interfejs sieciowy.
  * Dysk wirtualny 500 GB danych systemu.

### <a name="for-the-network-in-datacenter"></a>Dla sieci w centrum danych
Przed rozpoczęciem upewnij się, że:

* Zostanie sprawdzone wymagania sieciowe do wdrażania urządzenia wirtualnego StorSimple i skonfigurowany zgodnie z wymaganiami sieci centrum danych. 

## <a name="step-by-step-provisioning"></a>Krok po kroku inicjowania obsługi administracyjnej
Do obsługi administracyjnej i połączyć urządzenie wirtualne, należy wykonać następujące czynności:

1. Upewnij się, że systemu hosta ma wystarczające zasoby, aby spełnić wymagania minimalne urządzenia wirtualnego.
2. Udostępnianie w funkcji hypervisor, Twoje urządzenie wirtualne.
3. Uruchom wirtualne urządzenie i uzyskać adres IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Krok 1: Upewnij się, że system hosta spełnia wymagania minimalne urządzenia wirtualnego
Aby utworzyć urządzenie wirtualne, będą potrzebne:

* Dostęp do systemu hosta z systemem VMware ESXi Server 5.0, 5.5 lub 6.0.
* VMware vSphere klienta w systemie zarządzania hosta ESXi.

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurować wirtualny tablicy jako serwer plików, 8 GB obsługuje mniej niż 2 miliony plików. Należy 16 GB pamięci RAM 2-4 miliony plików obsługi.
  * Jeden interfejs sieciowy połączony z siecią może routingu ruch do Internetu. Minimalnej przepustowości Internetu, powinna być 5 MB/s, aby umożliwić optymalne pracy urządzenia.
  * Dysk wirtualny 500 GB danych.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Krok 2: Udostępnianie urządzenie wirtualne w ramach funkcji hypervisor
Wykonaj poniższe kroki, aby udostępnić w funkcji hypervisor, Twoje urządzenie wirtualne.

1. Kopiowanie obrazu urządzenia wirtualnego, w tym systemie. Możesz pobrać ten obraz wirtualnych za pośrednictwem portalu Azure.

   1. Upewnij się, że pobrano najnowszy plik obrazu. Jeśli wcześniej pobrano obrazu, pobierz go ponownie, aby upewnić się, że najnowsze obrazu. Najnowsze obraz ma dwa plików (zamiast jeden).
   2. Zanotuj lokalizację, w których obraz został skopiowany korzystania z tego obrazu w dalszej części procedury.

2. Zaloguj się na serwerze ESXi przy użyciu klienta programu vSphere. Musisz mieć uprawnienia administratora, aby utworzyć maszynę wirtualną.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. W kliencie programu vSphere w sekcji magazynu, w lewym okienku wybierz serwer ESXi.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Przesłanie VMDK serwer ESXi. Przejdź do **konfiguracji** karty w okienku po prawej stronie. W obszarze **sprzętu**, wybierz pozycję **magazynu**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. W prawym okienku w obszarze **Datastores**, wybierz magazyn danych, które chcesz przekazać VMDK. Magazyn danych musi mieć wystarczającą ilość wolnego miejsca dla systemu operacyjnego i dysków z danymi.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Kliknij prawym przyciskiem myszy i wybierz **Przeglądaj Datastore**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. A **przeglądarki Datastore** zostanie wyświetlone okno.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na pasku narzędzi kliknij przycisk ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) ikonę, aby utworzyć nowy folder. Podaj nazwę folderu i zapisz go. Ta nazwa folderu będzie używana później, podczas tworzenia maszyny wirtualnej (najlepsze rozwiązanie zalecane). Kliknij przycisk **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. W lewym okienku zostanie wyświetlony nowy folder **przeglądarki Datastore**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Kliknij ikonę przekazywania ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) i wybierz **Przekaż plik**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Przeglądaj, a następnie wskaż pobranych plików VMDK. Istnieją dwa pliki. Wybierz plik do przekazania.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Kliknij przycisk **Otwórz**. Rozpocznie się przekazywanie pliku VMDK z określonym magazynem danych. Może upłynąć kilka minut przekazać plik.
13. Po zakończeniu przekazywania można znaleźć w pliku w magazynie danych w folderze, który został utworzony.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Teraz Przekaż drugi plik VMDK do tego samego magazynu danych.
14. Wróć do okna vSphere klienta. ESXi wybranego serwera, kliknij prawym przyciskiem myszy i wybierz **nowej maszyny wirtualnej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. A **tworzenia nowej maszyny wirtualnej** pojawi się okno. Na **konfiguracji** wybierz pozycję **niestandardowy** opcji. Kliknij przycisk **Dalej**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Na **nazwy i lokalizacji** strony, określ nazwę maszyny wirtualnej. Ta nazwa powinna odpowiadać nazwie folderu (zalecane), określony wcześniej w kroku 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Na **magazynu** wybierz magazyn danych chcesz użyć do udostępnienia maszyny Wirtualnej.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Na **wersją maszyny wirtualnej** wybierz pozycję **wersją maszyny wirtualnej: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Na **systemu operacyjnego gościa** wybierz pozycję **systemu operacyjnego gościa** jako **Windows**. Aby uzyskać **wersji**, z listy rozwijanej wybierz **Microsoft Windows Server 2012 (64-bitowy)**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Na **procesorów** pozycję Dostosuj **liczbę gniazd wirtualnego** i **liczba rdzeni na gniazdo wirtualnego** , aby **łączna liczba rdzeni** 4 (lub więcej). Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Na **pamięci** Określ 8 GB lub więcej pamięci RAM. Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Na **sieci** Określ liczba interfejsów sieciowych. Minimalna wymagana wartość to jeden interfejs sieciowy.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Na **kontroler SCSI** pozycję Zaakceptuj wartość domyślną **kontrolera LSI Logic SAS**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Na **wybierz dysk** wybierz pozycję **Użyj istniejącego dysku wirtualnego**. Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Na **wybierz istniejący dysk** w obszarze **ścieżkę pliku dysku**, kliknij przycisk **Przeglądaj**. Spowoduje to otwarcie **Przeglądaj Datastores** okna dialogowego. Przejdź do lokalizacji, gdzie możesz przekazać VMDK. Tylko jeden plik z magazynu danych teraz Zobacz, jak dwa pliki, które początkowo przekazane zostały scalone. Wybierz plik, a następnie kliknij przycisk **OK**. Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Na **zaawansowane opcje** , zaakceptuj wartość domyślną i kliknij przycisk **dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Na **gotowe do wykonania** Przejrzyj wszystkie ustawienia skojarzone z nowej maszyny wirtualnej. Sprawdź **edytować ustawienia maszyny wirtualnej przed ukończeniem**. Kliknij przycisk **kontynuować**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Na **właściwości maszyny wirtualnej** strony w **sprzętu** zlokalizuj urządzeń sprzętowych. Wybierz **nowy dysk twardy**. Kliknij pozycję **Add** (Dodaj).

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Zostanie wyświetlony **dodawania sprzętu** okna. Na **typu urządzenia** w obszarze **wybierz typ urządzenia, które chcesz dodać**, wybierz pozycję **dysku twardego**i kliknij przycisk **dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Na **wybierz dysk** wybierz pozycję **tworzenia nowego dysku wirtualnego**. Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Na **Tworzenie dysku** Zmień **rozmiar dysku** do 500 GB (lub więcej). Minimalnym wymaganiem jest 500 GB, będzie zawsze możliwe alokowanie większy dysk. Należy pamiętać, nie można rozwinąć lub zmniejszania dysku raz udostępnione. Aby uzyskać więcej informacji na rozmiar dysku, aby udostępnić, przejrzyj sekcję zmiany rozmiaru w [najlepszych praktyk dokumentu](storsimple-ova-best-practices.md). W obszarze **udostępniania dysku**, wybierz pozycję **alokacji elastycznej**. Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Na **zaawansowane opcje** pozycję Zaakceptuj wartość domyślną.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Na **gotowe do wykonania** Przejrzyj opcje dysku. Kliknij przycisk **Zakończ**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Powrót do strony właściwości maszyny wirtualnej. Na nowym dysku twardym jest dodawany do maszyny wirtualnej. Kliknij przycisk **Zakończ**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Z maszyny wirtualnej wybrany w okienku po prawej stronie, przejdź do **Podsumowanie** kartę. Przejrzyj ustawienia maszyny wirtualnej.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Maszyna wirtualna jest teraz udostępniony. Następnym krokiem jest zasilania na tym komputerze i uzyskać adres IP.

> [!NOTE]
> Firma Microsoft zaleca nie zainstalowanie narzędzi VMware na wirtualnych tablica (jak udostępnione powyżej). Instalacja narzędzi VMware spowoduje nieobsługiwaną konfiguracją.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Krok 3: Uruchom urządzenie wirtualne i Uzyskaj adres IP
Wykonaj poniższe kroki, aby uruchomić urządzenie wirtualne i nawiązać z nim.

#### <a name="to-start-the-virtual-device"></a>Aby uruchomić urządzenie wirtualne
1. Uruchom urządzenie wirtualne. W vSphere programu Configuration Manager w okienku po lewej stronie Wybierz urządzenia, a następnie kliknij prawym przyciskiem myszy, aby wyświetlić menu kontekstowe. Wybierz **zasilania** , a następnie wybierz **włączają**. Powinno to zasilania na maszynie wirtualnej. Można wyświetlić stan w dolnej części **ostatnich zadań** okienko klienta vSphere.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Zadania konfiguracji potrwa kilka minut. Gdy urządzenie jest uruchomiona, przejdź do **konsoli** kartę. Wyślij klawisze Ctrl + Alt + Delete, aby zalogować się na urządzeniu. Alternatywnie możesz punktu kursora w oknie konsoli i naciśnij klawisze Ctrl + Alt + Insert. Użytkownik domyślny *StorSimpleAdmin* i domyślne hasło jest *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Ze względów bezpieczeństwa hasło administratora urządzenia wygasa przy pierwszym logowaniu. Monit o zmianę hasła.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Wprowadź hasło o długości co najmniej 8 znaków. Hasło musi zawierać 3 z 4 z tych wymagań: wielkie litery, małe litery, liczbowego i znaki specjalne. Wpisz ponownie hasło, aby je potwierdzić. Otrzymasz powiadomienie zmienił hasło.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Po pomyślnym zmiany hasła urządzenia wirtualnego może zostać uruchomiony ponownie. Poczekaj na ukończenie ponownego uruchomienia komputera. Konsoli programu Windows PowerShell, które urządzenia mogą być wyświetlane wraz z pasek postępu.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Kroki od 6 do 8 stosowane tylko wtedy, gdy rozruch w środowisku protokołu DHCP. Jeśli pracujesz w środowisku DHCP, Pomiń te kroki i przejdź do kroku 9. Jeśli uruchomiono zapasowych urządzenia w środowisku protokołu DHCP, zostanie wyświetlony następujący ekran.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Skonfiguruj sieci.
7. Użyj `Get-HcsIpAddress` polecenie, aby wyświetlić listę interfejsów sieci, włączone na urządzeniu wirtualnym. Jeśli urządzenie ma jeden interfejs sieci włączone, domyślną nazwą przypisane do tego interfejsu jest `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Użyj `Set-HcsIpAddress` polecenia cmdlet, aby skonfigurować sieć. Poniżej przedstawiono przykład:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Po zakończeniu początkowej konfiguracji, urządzenie uruchomi się w górę, pojawi się tekst transparentu urządzenia. Zanotuj adres IP i adres URL wyświetlany w tekście transparent do zarządzania urządzeniem. Ten adres IP umożliwia nawiązywanie połączenia sieci web interfejsu użytkownika z urządzeniem wirtualnym i ukończyć lokalnego instalację i rejestrację.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Opcjonalnie) Wykonaj ten krok tylko wtedy, gdy wdrażasz urządzenia w chmurze dla instytucji rządowych. Na urządzeniu zostanie teraz włączyć tryb Stanów Zjednoczonych informacji przetwarzania Standard FIPS (Federal). Standard FIPS 140 definiuje zatwierdzone do użycia przez nas federalne dla instytucji rządowych systemów komputerowych do ochrony danych poufnych algorytmów kryptograficznych.

    1. Aby włączyć tryb FIPS, uruchom następujące polecenie cmdlet:

        `Enable-HcsFIPSMode`
    2. Po włączeniu trybu FIPS, dzięki czemu kryptograficzne operacji sprawdzania poprawności zostały zastosowane, należy zrestartować urządzenie.

       > [!NOTE]
       > Można włączyć lub wyłączyć tryb FIPS na urządzeniu. Naprzemiennych urządzenia między trybem FIPS i z systemem innym niż FIPS nie jest obsługiwane.
       >
       >

Jeśli urządzenie nie spełnia minimalne wymagania dotyczące konfiguracji, zostanie wyświetlony błąd w tekście transparent (pokazana poniżej). Należy zmodyfikować konfigurację urządzenia, aby odpowiednie zasoby, aby spełnić wymagania minimalne. Następnie można ponownie uruchomić i nawiąż połączenie z urządzeniem. Wymagania minimalnej konfiguracji w [krok 1: Upewnij się, że system hosta spełnia wymagania minimalne urządzenia wirtualnego](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Inny błąd czoła podczas początkowej konfiguracji przy użyciu lokalnego interfejsu użytkownika sieci web, można znaleźć następujących przepływy pracy:

* Uruchamianie testów diagnostycznych do [Rozwiązywanie problemów z instalacją interfejsu użytkownika sieci web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generowanie pakietu dziennika i wyświetlanie plików dziennika](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Kolejne kroki
* [Konfigurowanie tablica wirtualnego StorSimple jako serwer plików](storsimple-virtual-array-deploy3-fs-setup.md)
* [Konfigurowanie tablica wirtualnego StorSimple iSCSI na serwerze](storsimple-virtual-array-deploy3-iscsi-setup.md)
