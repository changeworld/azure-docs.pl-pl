---
title: Aprowizowanie macierzy wirtualnej StorSimple w środowisku VMware | Dokumentacja firmy Microsoft
description: Ten drugi samouczek z serii wdrażania rozwiązania StorSimple Virtual Array obejmuje aprowizowania urządzenia wirtualnego w środowisku VMware.
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
ms.date: 01/11/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c9fe597957057dc61da5c2b1cf6f9216711764a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61419158"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Wdrażanie macierzy wirtualnej StorSimple — Aprowizacja w oprogramowaniu VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Omówienie
W tym samouczku opisano, jak aprowizować i nawiązać połączenie z rozwiązania StorSimple Virtual Array na komputerze hosta z systemem VMware ESXi 5.0, 5.5, 6.0 lub 6.5. Ten artykuł dotyczy wdrożenia macierzach wirtualnych StorSimple w witrynie Azure portal i chmury platformy Microsoft Azure dla instytucji rządowych.

Do aprowizowania urządzenia wirtualnego i nawiązania z nim połączenia wymagane są uprawnienia administratora. Aprowizacja i wstępna konfiguracja może zająć około 10 minut.

## <a name="provisioning-prerequisites"></a>Inicjowanie obsługi administracyjnej wstępnie wymagane składniki
Dostępne są następujące wymagania wstępne dotyczące aprowizowania urządzenia wirtualnego na komputerze hosta z systemem VMware ESXi 5.0, 5.5, 6.0 lub 6.5.

### <a name="for-the-storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple
Przed rozpoczęciem upewnij się, że:

* Zostały wykonane wszystkie kroki [przygotowywanie portalu dla rozwiązania StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Po jej pobraniu obrazu urządzenia wirtualnego dla oprogramowania VMware z witryny Azure portal. Aby uzyskać więcej informacji, zobacz **krok 3: Pobranie obrazu urządzenia wirtualnego** z [przygotowywanie portalu przewodnik rozwiązania StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Dla urządzenia wirtualnego StorSimple
Przed wdrożeniem urządzenia wirtualnego upewnij się, że są spełnione następujące warunki:

* Masz dostęp do systemu hosta funkcji Hyper-V (2008 R2 lub nowszy), może być używany do aprowizacji urządzenia.
* System hosta może przeznaczyć następujące zasoby wyłącznie na potrzeby aprowizacji urządzenia wirtualnego:

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli użytkownik chce skonfigurować macierzy wirtualnej jako serwera plików, 8 GB obsługuje mniej niż 2 miliony plików. Należy 16 GB pamięci RAM na potrzeby obsługi 2-4 miliony plików.
  * Jeden interfejs sieciowy.
  * Dysk wirtualny 500 GB danych systemu.

### <a name="for-the-network-in-datacenter"></a>Sieć w centrum danych
Przed rozpoczęciem upewnij się, że:

* Przejrzeniu wymagań sieciowych, aby wdrożyć urządzenie wirtualne StorSimple i sieci centrum danych, zgodnie z wymaganiami. 

## <a name="step-by-step-provisioning"></a>Instrukcje krok po kroku inicjowania obsługi administracyjnej
Do aprowizacji i nawiązać połączenie z urządzeniem wirtualnym, należy wykonać następujące czynności:

1. Upewnij się, że system hosta ma wystarczające zasoby do spełnienia wymagań minimalnych urządzenia wirtualnego.
2. Aprowizuj urządzenia wirtualnego w Twojej funkcji hypervisor.
3. Uruchom urządzenie wirtualne, a następnie Uzyskaj adres IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Krok 1: Upewnij się, że system hosta spełnia wymagania minimalne urządzenia wirtualnego
Aby utworzyć urządzenie wirtualne, konieczne będzie:

* Dostęp do systemu hosta VMware ESXi Server 5.0, 5.5, 6.0 lub 6.5.
* Klient VMware vSphere w systemie na potrzeby zarządzania hostem ESXi.

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli użytkownik chce skonfigurować macierzy wirtualnej jako serwera plików, 8 GB obsługuje mniej niż 2 miliony plików. Należy 16 GB pamięci RAM na potrzeby obsługi 2-4 miliony plików.
  * Jeden interfejs sieciowy połączony z siecią umożliwiającą kierowanie ruchu do Internetu. Minimalnej przepustowości Internetu, powinna być 5 MB/s, aby umożliwić optymalne pracy urządzenia.
  * Dysk wirtualny 500 GB danych.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Krok 2: Aprowizowanie urządzenia wirtualnego w funkcji hypervisor
Wykonaj następujące czynności, aby aprowizować urządzenie wirtualne w funkcji hypervisor.

1. Skopiuj obraz urządzenia wirtualnego do swojego systemu. Możesz pobrać ten obraz wirtualnych za pośrednictwem witryny Azure portal.

   1. Upewnij się, że pobrano najnowszy plik obrazu. Jeśli wcześniej pobrano obraz pobierz ją ponownie, aby upewnić się, że najnowszego obrazu. Najnowsze obraz ma dwa pliki (zamiast jednego).
   2. Zanotuj lokalizację, do której został skopiowany obraz, ponieważ będzie on używany w dalszej części tej procedury.

2. Zaloguj się na serwerze ESXi przy użyciu klienta vSphere. Musisz mieć uprawnienia administratora, aby utworzyć maszynę wirtualną.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. W kliencie vSphere, w sekcji spisu w okienku po lewej stronie wybierz serwer ESXi.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Przekaż dysk VMDK na serwer ESXi. Przejdź do **konfiguracji** karty w okienku po prawej stronie. W obszarze **sprzętu**, wybierz opcję **magazynu**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. W okienku po prawej stronie, w obszarze **Datastores** (Magazyny danych), wybierz magazyn danych, do którego chcesz przekazać dysk VMDK. Magazyn danych musi mieć wystarczającą ilością wolnego miejsca dla dysków systemu operacyjnego i danych.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Kliknij prawym przyciskiem myszy i wybierz polecenie **Browse Datastore** (Przeglądaj magazyn danych).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Zostanie wyświetlone okno **Datastore Browser** (Przeglądarka magazynu danych).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na pasku narzędzi kliknij polecenie ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) ikonę, aby utworzyć nowy folder. Określ nazwę folderu i zanotuj ją. Ta nazwa folderu będzie używana później, podczas tworzenia maszyny wirtualnej (zalecane najlepsze rozwiązanie). Kliknij przycisk **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Nowy folder zostanie wyświetlony w okienku po lewej stronie okna **Datastore Browser** (Przeglądarka magazynu danych).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Kliknij ikonę przekazywania ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) i wybierz **Przekaż plik**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Wskaż pobrane pliki VMDK. Pliki są dwa. Wybierz plik do przekazania.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Kliknij przycisk **Open** (Otwórz). Rozpocznie się przekazywanie pliku VMDK do określonego magazynu danych. Przekazanie pliku może zająć kilka minut.
13. Po zakończeniu przekazywania plik będzie widoczny w magazynie danych w utworzonym folderze.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Teraz przekaż drugi plik VMDK do tego samego magazynu danych.
14. Wróć do okna klienta vSphere. Wybranego serwera ESXi, kliknij prawym przyciskiem myszy i wybierz **nowej maszyny wirtualnej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. A **tworzenia nowej maszyny wirtualnej** zostanie wyświetlone okno. Na **konfiguracji** wybierz opcję **niestandardowe** opcji. Kliknij przycisk **Dalej**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Na **nazwę i lokalizację** Określ nazwę maszyny wirtualnej. Ta nazwa powinna odpowiadać nazwa folderu (zalecane), który określono wcześniej w kroku 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Na **magazynu** wybierz magazyn danych, którego chcesz użyć do aprowizowania maszyny Wirtualnej.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Na **wersji maszyny wirtualnej** wybierz opcję **wersji maszyny wirtualnej: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Na **System operacyjny gościa** wybierz opcję **System operacyjny gościa** jako **Windows**. Aby uzyskać **wersji**, z listy rozwijanej wybierz **Microsoft Windows Server 2012 (64-bitowy)** .

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Na **procesorów** strony, dostosować **liczbę gniazd wirtualnego** i **liczbę rdzeni na gniazdo wirtualnego** tak, aby **łącznej liczby rdzeni** jest 4 (lub więcej). Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Na **pamięci** Określ 8 GB lub więcej pamięci RAM. Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Na **sieci** Określ liczbę interfejsów sieciowych. Musi istnieć co najmniej jeden interfejs sieciowy.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Na **kontroler SCSI** strony, zaakceptuj wartość domyślną **kontrolera LSI logiki sygnatury dostępu Współdzielonego**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Na **wybierz dysk** wybierz **Użyj istniejącego dysku wirtualnego**. Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Na **wybierz istniejący dysk** w obszarze **ścieżkę pliku dysku**, kliknij przycisk **Przeglądaj**. Spowoduje to otwarcie **Przeglądaj magazynów danych** okna dialogowego. Przejdź do lokalizacji, w której został przekazany VMDK. Teraz zobaczysz tylko jeden plik z magazynu danych, jak dwa pliki, które początkowo przekazane zostały scalone. Wybierz plik, a następnie kliknij przycisk **OK**. Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Na **zaawansowane opcje** strony, zaakceptuj wartości domyślne i kliknij przycisk **dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Na stronie **Ready to Complete** (Gotowe do ukończenia) przejrzyj wszystkie ustawienia skojarzone z nową maszyną wirtualną. Sprawdź **zmodyfikuj ustawienia maszyny wirtualnej przed ukończeniem**. Kliknij pozycję **Kontynuuj**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Na **właściwości maszyn wirtualnych** stronie **sprzętu** kartę, odszukaj urządzeń sprzętowych. Wybierz **nowy dysk twardy**. Kliknij pozycję **Add** (Dodaj).

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Zostanie wyświetlony **dodawania sprzętu** okna. Na **typu urządzenia** w obszarze **wybierz typ urządzenia, które chcesz dodać**, wybierz opcję **dysku twardego**i kliknij przycisk **dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Na **wybierz dysk** wybierz **Tworzenie nowego dysku wirtualnego**. Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Na **Tworzenie dysku** strony, zmień **rozmiar dysku** do 500 GB (lub więcej). 500 GB, jest minimalnym wymaganiem, zawsze można udostępnić większy dysk. Należy pamiętać, nie można rozwinąć lub zmniejszania dysku po aprowizacji. Aby uzyskać więcej informacji na podstawie rozmiaru dysku, aby aprowizować, zapoznaj się z sekcją zmiany rozmiaru, w [najlepszych praktyk dokumentu](storsimple-ova-best-practices.md). W obszarze **inicjowania obsługi administracyjnej dysku**, wybierz opcję **elastycznej**. Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Na **zaawansowane opcje** strony, zaakceptuj wartość domyślną.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Na **gotowe do wykonania** Przejrzyj opcje dysku. Kliknij przycisk **Zakończ**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Powrót do strony właściwości maszyny wirtualnej. Nowy dysk twardy jest dodawany do maszyny wirtualnej. Kliknij przycisk **Zakończ**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Z maszyny wirtualnej wybrany w okienku po prawej stronie, przejść do **Podsumowanie** kartę. Przejrzyj ustawienia maszyny wirtualnej.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Maszyna wirtualna jest teraz aprowizowana. Następnym krokiem jest uruchomienie tej maszyny i uzyskanie adresu IP.

> [!NOTE]
> Zaleca się nie instalować oprogramowania VMware na macierz wirtualna (jak aprowizowanego ponad). Zainstalowanie narzędzi VMware spowoduje powstanie nieobsługiwanej konfiguracji.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Krok 3: Uruchamianie urządzenia wirtualnego i uzyskiwanie adresu IP
Wykonaj poniższe kroki, aby uruchomić urządzenie wirtualne i nawiązać z nim połączenie.

#### <a name="to-start-the-virtual-device"></a>Aby uruchomić urządzenie wirtualne
1. Uruchom urządzenie wirtualne. W vSphere programu Configuration Manager w okienku po lewej stronie wybierz urządzenie, a następnie kliknij prawym przyciskiem myszy, aby wyświetlić menu kontekstowe. Wybierz pozycję **Power** (Zasilanie), a następnie pozycję **Power on** (Włącz). Maszyna wirtualna powinna zostać włączona. Możesz wyświetlić stan w dolnej części **ostatnich zadań** okienko klienta vSphere.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Zadania instalacji potrwa kilka minut. Gdy urządzenie jest uruchomiona, przejdź do **konsoli** kartę. Wyślij klawisze Ctrl + Alt + Delete, aby zalogować się do urządzenia. Alternatywnie możesz punktu kursora w oknie konsoli i naciśnij klawisze Ctrl + Alt + Insert. Użytkownik domyślny jest *StorSimpleAdmin* i domyślne hasło *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Ze względów bezpieczeństwa hasło administratora urządzenia wygasa przy pierwszym logowaniu. Zostanie wyświetlony monit informujący o konieczności zmiany hasła.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Wprowadź hasło zawierające co najmniej 8 znaków. Hasło musi zawierać 3 z 4 z tych wymagań: wielkie litery, małe litery, cyfry i znaki specjalne. Wprowadź ponownie hasło w celu potwierdzenia. Powiadomimy Cię, że hasło została zmieniona.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Po pomyślnym zmiany hasła może ponownie uruchomić urządzenie wirtualne. Oczekiwanie na ponowne uruchomienie w celu ukończenia. Konsolę programu Windows PowerShell urządzenia mogą być wyświetlane wraz z pasek postępu.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Kroki od 6 do 8 mają zastosowanie tylko w przypadku uruchamiania w środowisku bez protokołu DHCP. W przypadku środowiska protokołu DHCP należy pominąć te kroki i przejść do kroku 9. Jeśli uruchomiono konfigurację urządzenia w środowisku protokołu DHCP, zostanie wyświetlony następujący ekran.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Następnie skonfiguruj sieci.
7. Użyj `Get-HcsIpAddress` polecenie, aby wyświetlić listę interfejsów sieciowych, włączone na urządzeniu wirtualnym. Jeśli urządzenie ma włączony jeden interfejs sieciowy, jego domyślną nazwą jest `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Skonfiguruj sieć za pomocą polecenia cmdlet `Set-HcsIpAddress`. Przykład przedstawiono poniżej:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Po zakończeniu początkowej konfiguracji i uruchomieniu urządzenia zobaczysz tekst baneru urządzenia. Zanotuj adresy IP i URL wyświetlane w tekście baneru. Posłużą one do zarządzania urządzeniem. Użyjesz tego adresu IP, aby nawiązać połączenie z internetowego interfejsu użytkownika urządzenia wirtualnego i ukończyć konfigurowanie lokalnego i rejestracji.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Opcjonalnie) Ten krok należy wykonać tylko wtedy, gdy są wdrażane urządzenia w chmurze dla instytucji rządowych. Na urządzeniu, będzie teraz Włącz tryb Stanów Zjednoczonych informacji przetwarzania Standard FIPS (Federal). Standard FIPS 140 definiuje zatwierdzone do użycia przez nas federalnych instytucji rządowych systemów komputerowych ochrony poufnych danych algorytmów kryptograficznych.

    1. Aby włączyć tryb ze standardem FIPS, uruchom następujące polecenie cmdlet:

        `Enable-HcsFIPSMode`
    2. Po włączeniu trybu FIPS, tak, aby kryptograficznych walidacji zaczęły obowiązywać, należy ponownie uruchomić urządzenie.

       > [!NOTE]
       > Można włączyć lub wyłączyć tryb FIPS na urządzeniu. Przełączanie urządzeń między trybem FIPS i ze standardem FIPS nie jest obsługiwane.
       >
       >

Jeśli urządzenie nie spełnia minimalnych wymagań dotyczących konfiguracji, w tekście baneru zostanie wyświetlony błąd (widoczny poniżej). Trzeba zmodyfikować konfigurację urządzenia tak, aby zapewnić zasoby spełniające minimalne wymagania. Następnie możesz ponownie uruchomić urządzenie i połączyć się z nim. Zapoznaj się z wymaganiami minimalnej konfiguracji w [krok 1: Upewnij się, że system hosta spełnia wymagania minimalne urządzenia wirtualnego](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Jeśli napotkasz inny błąd podczas konfiguracji początkowej, przy użyciu lokalnego Interfejsu w przeglądarce odwoływać się do następujących przepływów:

* Uruchom testy diagnostyczne do [Rozwiązywanie problemów z instalacją interfejsu użytkownika sieci web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Wygeneruj pakiet dziennika i wyświetlanie plików dziennika](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Kolejne kroki
* [Konfigurowanie rozwiązania StorSimple Virtual Array do serwera jako serwera plików](storsimple-virtual-array-deploy3-fs-setup.md)
* [Konfigurowanie rozwiązania StorSimple Virtual Array do serwera jako serwera iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
