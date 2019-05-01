---
title: Ustaw się aplikacja deweloperów kontrolowane dystrybucji (ADCD) w IBM zD & T w wersji 1 | Dokumentacja firmy Microsoft
description: Uruchom środowisko testowe (zD & T) środowiska deweloperskiego Z IBM i usłudze Azure Virtual Machines (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: c6fcb345b49ce6354a24408ebe163fb928990252
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925692"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Ustaw się aplikacja deweloperów kontrolowane dystrybucji (ADCD) w IBM zD & T w wersji 1

Można uruchomić środowiska testowego (zD & T) środowiska deweloperskiego Z IBM i usłudze Azure Virtual Machines (VMs). To środowisko emuluje architektury systemu IBM Z serii. Może on obsługiwać różnych systemów operacyjnych Z serii lub instalacje (nazywane również Z wystąpień lub pakietów), które są udostępniane za pośrednictwem dostosowane pakiety o nazwie IBM aplikacji deweloperów kontrolowane dystrybucji (ADCDs).

W tym artykule pokazano, jak poświęcona jest konfigurowaniu wystąpienia ADCD w zD & T środowiska na platformie Azure. ADCDs Utwórz pełną implementacje systemu operacyjnego Z serii dla środowisk programowania i testowania działających w zD & t

Podobnie jak zD & T ADCDs są dostępne tylko dla partnerów i klientów firmy IBM i wyłącznie do celów projektowania i testowania. Są one nie może być używane w środowiskach produkcyjnych. Wiele pakietów instalacyjnych IBM są dostępne do pobrania za pośrednictwem [zaletą usługi Passport](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) lub [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [ZD & T środowiska] [ ibm-install-z] uprzednio skonfigurowane na platformie Azure. W tym artykule założono, że używasz tego samego obrazu maszyny Wirtualnej systemu Ubuntu 16.04 utworzone wcześniej.

- Dostęp do nośnika ADCD za pośrednictwem IBM PartnerWorld lub korzystać z usługi Passport.

- A [serwer licencyjny](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Jest to wymagane do uruchomienia IBM zD & t Sposób jego tworzenia, zależy od tego, jak licencji oprogramowania od firmy IBM:

  - **Oparte na sprzęcie serwer licencyjny** wymaga urządzenie USB, które zawiera tokeny wymierne niezbędnych do uzyskania dostępu wszystkie składniki oprogramowania. Należy to uzyskać od firmy IBM.

  - **Serwer licencyjny opartych na oprogramowaniu** wymaga skonfigurowania scentralizowanego serwera zarządzania kluczami licencjonowania. Ta metoda jest preferowana i wymaga skonfigurowania kluczy otrzymany od firmy IBM na serwerze zarządzania.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Pobierz pakiety instalacyjne z zalet usługi Passport

Wymagany jest dostęp do nośnika ADCD. W poniższych krokach przyjęto są klientami firmy IBM i użyć korzyści z usługi Passport. IBM partnerzy mogą używać [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd).

> [!NOTE]
> W tym artykule założono, że komputera z systemem Windows jest używany, aby dostęp do witryny Azure portal i pobrać media firmy IBM. Jeśli używasz komputera Mac lub systemu Ubuntu desktop, polecenia i proces uzyskiwania IBM media mogą się nieznacznie różnić.

1. Zaloguj się do [zaletą usługi Passport](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Wybierz **pobierania oprogramowania** i **dostępu do nośnika**.

3. Wybierz **programu oferty oraz umowy, numer**i kliknij przycisk **Kontynuuj**.

4. Wprowadź opis części lub numer części, a następnie kliknij przycisk **wyszukiwania**.

5. Opcjonalnie kliknij listy alfabetycznej do wyświetlania i wyświetlić produktu według nazwy.

6. Wybierz **wszystkich systemów operacyjnych** w **pole systemu operacyjnego**, i **wszystkie języki** w **pola języków**. Następnie kliknij przycisk **Przejdź**.

7. Kliknij przycisk **wybierać poszczególne pliki** rozwiń listę i wyświetlić pojedyncze nośniki do pobrania.

8. Sprawdzić pakiety, które chcesz pobrać, wybierz opcję **Pobierz**, a następnie pobrać pliki do katalogu, który chcesz.

## <a name="upload-the-adcd-packages"></a>Przekaż pakiety ADCD

Teraz, gdy pakiety, możesz przekazać je do maszyny Wirtualnej na platformie Azure.

1. W witrynie Azure portal, należy zainicjować **ssh** sesji przy użyciu utworzonej maszyny Wirtualnej systemu Ubuntu. Przejdź do maszyny Wirtualnej, wybierz **Przegląd** bloku, a następnie wybierz **Connect**.

2. Wybierz **SSH** kartę, a następnie skopiuj polecenie ssh do Schowka.

3. Zaloguj się do maszyny Wirtualnej przy użyciu poświadczeń i [klienta SSH](/azure/virtual-machines/linux/use-remote-desktop) wyboru. Ten pokaz używa rozszerzeń systemu Linux w systemie Windows 10, który dodaje powłoki bash w wierszu polecenia programu Windows. PuTTY działa równie dobrze.

4. Po zalogowaniu się, Utwórz katalog do przekazywania pakietów firmy IBM. Należy pamiętać, systemu Linux jest uwzględniana wielkość liter. Na przykład w tej wersji demonstracyjnej zakłada się, że pakiety są przekazywane do:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Przekazywanie plików przy użyciu klienta SSH, takich jak[WinSCP](https://winscp.net/eng/index.php). Punkt połączenia usługi jest częścią SSH, używa portu 22, co jest protokół SSH używa. Jeśli komputer lokalny nie jest Windows, należy wpisać [polecenia scp](http://man7.org/linux/man-pages/man1/scp.1.html) w sesji SSH.

6. Inicjowanie przekazania nowo utworzony katalog maszyny Wirtualnej platformy Azure, która staje się przechowywanie obrazów zD & T.

    > [!NOTE]
    > Upewnij się, że **ADCDTOOLS. XML** znajduje się w przekazania **głównej/MyUserID/ZDT/adcd/nov2017** katalogu. Będzie potrzebny później.

7. Poczekaj, aż pliki do przekazania, co może zająć trochę czasu, w zależności od połączenia na platformie Azure.

8. Po zakończeniu przekazywania przejdź do katalogu, woluminów i zdekompresować wszystkie **gz** woluminów:

    ```
        gunzip \*.gz
    ```
    
![Eksplorator plików dekompresja gz woluminów](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Skonfiguruj magazyn obrazów

Następnym krokiem jest skonfigurować zD & T, aby używać przekazanego pakietów. Proces przechowywania obrazu, w ramach zD & T umożliwia instalowanie i korzystanie z obrazów. Może używać klienta SSH lub FTP.

1. Rozpocznij **zDTServer**. Aby to zrobić, musi być na poziomie głównym. Wprowadź dwa poniższe polecenia w kolejności:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Zanotuj adres URL danych wyjściowych za pomocą polecenia, a następnie użyj tego adresu URL, aby uzyskać dostęp do serwera sieci web. Będzie on podobny do:
     > https://(your VM name or IP Address):9443/ZDTMC/index.HTML
     >
     > Należy pamiętać, że dostęp do usługi sieci web używa portu 9443. Służy do logowania się do serwera sieci web. Identyfikator użytkownika dla ZD & T jest **zdtadmin** , a hasło to **hasło**.

    ![IBM zD & T Enterprise Edition startowa](media/02-welcome.png)

3. Na **— Szybki Start** w obszarze **Konfiguruj**, wybierz opcję **magazyn obrazów**.

     ![IBM zD & T Enterprise Edition — Szybki Start ekran](media/03-quickstart.png)

4. Na **skonfigurować magazyn obrazów** wybierz opcję **SSH File Transfer Protocol**.

5. Aby uzyskać **nazwy hosta**, typ **Localhost** i wprowadź ścieżkę katalogu, do którego został przekazany obrazów. Na przykład /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Wprowadź **identyfikator użytkownika** i **hasło** dla maszyny Wirtualnej. Nie należy używać ZD & T, identyfikator użytkownika i hasło.

7. Testuj połączenie, aby upewnić się, mają dostęp, a następnie wybierz pozycję **Zapisz** Aby zapisać konfigurację.

## <a name="configure-the-target-environments"></a>Konfigurowanie środowiska docelowego

Następnym krokiem jest skonfigurowanie zD & T środowiska docelowego. Ten emulowanej hostowanym środowiskiem jest, gdzie obrazów uruchamiania.

1. Na **— Szybki Start** w obszarze **Konfiguruj**, wybierz opcję **docelowych środowisk**.

2. Na **skonfigurowania środowisk docelowych** wybierz opcję **Dodawanie obiektu docelowego**.

3. Wybierz **Linux**. IBM obsługuje dwa rodzaje środowisk systemów Linux i Cloud(OpenStack), ale ten pokaz działa w systemie Linux.

4. Na **środowiska docelowego Dodaj** strony dla **nazwy hosta**, wprowadź **localhost**. Zachowaj **portu SSH** równa **22**.

5. W **etykiety środowiska docelowego** wprowadź etykietę, takich jak **MyCICS.**

     ![Dodawanie ekranu środowiska docelowego](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Konfigurowanie ADCD i wdrażanie

Po wykonaniu poprzednich kroków konfiguracji, należy skonfigurować zD & T, aby korzystać ze środowiska pakiety i docelowej. Ponownie użyj procesu magazynu obrazu zD & T, dzięki czemu można zainstalować obrazy i używać ich w. Może używać klienta SSH lub FTP.

1. Na **— Szybki Start** w obszarze **Konfiguruj**, wybierz opcję **ADCD**. Pojawiają się zestaw instrukcji informujące czynności, które należy wykonać przed pakietu ADCD mogą być instalowane. To wyjaśnia, dlaczego firma Microsoft o nazwie katalogu docelowego sposób, które były wykonywane wcześniej.

2. Zakładając, że wszystkie obrazy zostały przekazane do katalogów poprawne, kliknij przycisk **obraz z ADCD** łącza wyświetlane w lewym dolnym rogu (przedstawionym w kroku 7 na następującym zrzucie ekranu).

     ![IBM zD & T Enterprise Edition — Konfigurowanie ADCD ekranu](media/05-adcd.png)

## <a name="create-the-image"></a>Tworzenie obrazu

Po ukończeniu poprzedniego kroku konfiguracji **utworzyć obraz przy użyciu składników ADCD** zostanie wyświetlona strona.

1. Wybierz wolumin (listopada 2017 r. w tym przypadku), aby wyświetlić różnych pakietach, które znajdują się w tym woluminie.

2. W tej wersji demonstracyjnej, wybierz **klienta informacji kontroli systemu (CICS) - 5.3**.

3. W **nazwa obrazu** wpisz nazwę obrazu, takich jak **obraz MyCICS**.

4. Wybierz **Utwórz obraz** przycisk w prawym dolnym rogu.

     ![IBM zD & T Enterprise Edition — Tworzenie obrazu przy użyciu składników ADCD ekranu](media/06-adcd.png)

5. W wyświetlonym oknie informujący, obraz został pomyślnie wdrożony, wybierz polecenie **wdrażać obrazy**.

6. Na **wdrożenia obrazu środowiska docelowego** wybierz obraz, który został utworzony na poprzedniej stronie (**obraz MyCICS**) oraz środowiska docelowego utworzonego wcześniej (**MyCICS**).

7. Na następnym ekranie wprowadź swoje poświadczenia dla maszyny Wirtualnej (czyli nie ztadmin poświadczeń).

8. W okienku właściwości wprowadź liczbę **centralnej procesorów (CPs)**, ilość **pamięci systemowej (GB)** i **katalogu wdrażania** uruchamianie obrazu. Ponieważ jest to pokaz, zachowywanie małych.

9. Upewnij się, że pole jest zaznaczone dla **automatyczne wdrażanie polecenia IPL problemu z/OS, po**.

     ![Właściwości ekranu](media/07-properties.png)

10. Wybierz **pełną**.

11. Wybierz **wdrażanie obrazu** z **wdrożenia obrazu środowiska docelowego** strony.

Obraz można teraz wdrożyć i jest gotowa do zainstalowanych w wyniku 3270 emulatora terminala.

> [!NOTE]
> Jeśli zostanie wyświetlony błąd informujący o tym, że nie masz wystarczająco dużo miejsca na dysku, należy pamiętać, że region wymaga 151 Gb.

Gratulacje! Środowisko komputera mainframe firmy IBM są uruchomione na platformie Azure.

## <a name="learn-more"></a>Dowiedz się więcej

- [Migracja komputera mainframe: mitów i faktów](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Rozwiązywanie problemów](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Demystifying mainframe do migracji na platformę Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
