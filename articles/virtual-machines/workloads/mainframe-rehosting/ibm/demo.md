---
title: Konfigurowanie dystrybucji kontrolowanej przez deweloperów aplikacji (ADCD) w programie IBM zD&T v1 | Dokumenty firmy Microsoft
description: Uruchom środowisko ibm z development and test environment (zD&T) na maszynach wirtualnych platformy Azure (VM).
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 66f80c79219090c27da37dfc1d9149df5604961f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68841394"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Konfigurowanie dystrybucji kontrolowanej przez deweloperów aplikacji (ADCD) w programie IBM zD&T v1

Środowisko IBM Z Development and Test Environment (zD&T) można uruchomić na maszynach wirtualnych platformy Azure(VM). To środowisko emuluje architekturę IBM Z Series. Może obsługiwać różne systemy operacyjne z serii Z lub instalacje (nazywane także wystąpieniami lub pakietami Z), które są udostępniane za pośrednictwem niestandardowych pakietów zwanych dystrybucjami kontrolowanymi przez programistów (IBM Application Developers Controlled Distributions).

W tym artykule pokazano, jak skonfigurować wystąpienie ADCD w środowisku zD&T na platformie Azure. AdCDs utworzyć pełne implementacje systemu operacyjnego serii Z dla środowisk deweloperskich i testowych, które działają w zD&T.

Podobnie jak zD&T, adcdy są dostępne tylko dla klientów i partnerów IBM i służą wyłącznie do celów rozwojowych i testowych. Nie są one używane w środowiskach produkcyjnych. Wiele pakietów instalacyjnych IBM jest dostępnych do pobrania za pośrednictwem [programu Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) lub IBM [PartnerWorld.](https://www.ibm.com/partnerworld/public)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Środowisko [zD&T][ibm-install-z] wcześniej skonfigurowane na platformie Azure. W tym artykule założono, że używasz tego samego obrazu maszyny Wirtualnej Ubuntu 16.04 utworzonego wcześniej.

- Dostęp do nośników ADCD za pośrednictwem programu IBM PartnerWorld lub Passport Advantage.

- [Serwer licencjonowania](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Jest to wymagane do uruchomienia ibm zD&T. Sposób jego tworzenia zależy od sposobu licencjonowannia oprogramowania od IBM:

  - **Sprzętowy serwer licencjonowania** wymaga urządzenia sprzętowego USB, które zawiera tokeny Rational niezbędne do uzyskania dostępu do wszystkich części oprogramowania. Należy to uzyskać od IBM.

  - **Serwer licencjonowania oparty na oprogramowaniu** wymaga skonfigurowania scentralizowanego serwera do zarządzania kluczami licencyjnymi. Ta metoda jest preferowana i wymaga skonfigurowania kluczy otrzymywanych od IBM na serwerze zarządzania.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Pobierz pakiety instalacyjne z usługi Passport Advantage

Wymagany jest dostęp do nośnika ADCD. Poniższe kroki zakładają, że jesteś klientem IBM i możesz korzystać z usługi Passport Advantage. Partnerzy IBM mogą korzystać z [programu IBM PartnerWorld.](https://www.ibm.com/partnerworld/public)

> [!NOTE]
> W tym artykule założono, że komputer z systemem Windows jest używany do uzyskiwania dostępu do witryny Azure portal i pobierania multimediów IBM. Jeśli używasz komputera Mac lub Ubuntu, polecenia i proces uzyskiwania nośnika IBM mogą się nieznacznie różnić.

1. Zaloguj się do [Passport Advantage](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Wybierz **pozycję Pobieranie oprogramowania** i dostęp do **multimediów**.

3. Wybierz **pozycję Oferta programu i numer umowy**, a następnie kliknij przycisk **Kontynuuj**.

4. Wprowadź opis części lub numer części, a następnie kliknij przycisk **Znajdź**.

5. Opcjonalnie kliknij listę kolejności alfabetycznych, aby wyświetlić i wyświetlić produkt według nazwy.

6. Wybierz **pozycję Wszystkie systemy operacyjne** w polu System **operacyjny**i **Wszystkie języki** w polu **Języki**. Następnie kliknij przycisk **Przejdź**.

7. Kliknij **pozycję Wybierz pojedyncze pliki,** aby rozwinąć listę i wyświetlić poszczególne nośniki do pobrania.

8. Sprawdź pakiety, które chcesz pobrać, wybierz pozycję **Pobierz**, a następnie pobierz pliki do odpowiedniego katalogu.

## <a name="upload-the-adcd-packages"></a>Przekazywanie pakietów ADCD

Teraz, gdy masz pakiety, należy przekazać je do maszyny Wirtualnej na platformie Azure.

1. W witrynie Azure portal zainicjuj sesję **ssh** z utworzoną maszyną wirtualną Ubuntu. Przejdź do maszyny Wirtualnej, wybierz blok **Przegląd,** a następnie wybierz pozycję **Połącz**.

2. Wybierz kartę **SSH,** a następnie skopiuj polecenie schowek do schowka.

3. Zaloguj się do maszyny Wirtualnej przy użyciu poświadczeń i [wybranego klienta SSH.](/azure/virtual-machines/linux/use-remote-desktop) To demo używa rozszerzeń Linuksa dla systemu Windows 10, który dodaje powłokę bash do wiersza polecenia systemu Windows. PuTTY działa równie dobrze.

4. Po zalogowaniu utwórz katalog, aby przesłać pakiety IBM. Należy pamiętać, że w systemie Linux rozróżniana jest wielkość liter. Na przykład to demo zakłada, że pakiety są przekazywane do:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Przekaż pliki przy użyciu klienta SSH, takiego jak[WinSCP](https://winscp.net/eng/index.php). Ponieważ protokół SCP jest częścią protokołu SSH, używa portu 22, czyli tego, co wykorzystuje SSH. Jeśli komputer lokalny nie jest systemem Windows, można [wpisać polecenie scp](http://man7.org/linux/man-pages/man1/scp.1.html) w sesji SSH.

6. Inicjuj przekazywanie do utworzonego katalogu maszyny Wirtualnej platformy Azure, który staje się magazynem obrazów dla zD&T.

    > [!NOTE]
    > Upewnij się, że **ADCDTOOLS. Kod XML** jest zawarty w katalogu przesłania do **katalogu home/MyUserID/ZDT/adcd/nov2017.** Będzie potrzebny później.

7. Poczekaj na przekazanie plików, co może zająć trochę czasu w zależności od połączenia z platformą Azure.

8. Po zakończeniu przesyłania przejdź do katalogu woluminów i zdekompresuj wszystkie woluminy **gz:**

    ```
        gunzip \*.gz
    ```
    
![Eksplorator plików przedstawiający zdekompresowane woluminy gz](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Konfigurowanie magazynu obrazów

Następnym krokiem jest skonfigurowanie zD&T do korzystania z przekazanych pakietów. Proces przechowywania obrazu w zD&T pozwala na montaż i korzystanie z obrazów. Może używać SSH lub FTP.

1. Uruchom **serwer zDTServer**. Aby to zrobić, musisz być na poziomie głównym. Wprowadź następujące dwa polecenia w kolejności:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Zanotuj dane wyjściowe adresu URL za pomocą polecenia i użyj tego adresu URL, aby uzyskać dostęp do serwera sieci web. Wygląda podobnie do:
     > https://(twoja nazwa maszyny Wirtualnej lub adres IP):9443/ZDTMC/index.html
     >
     > Pamiętaj, że twój dostęp do internetu używa portu 9443. Służy do logowania się na serwerze sieci web. Identyfikator użytkownika ZD&T to **zdtadmin,** a hasłem jest **hasło.**

    ![Ekran powitalny IBM zD&T Enterprise Edition](media/02-welcome.png)

3. Na stronie **Szybki start** w obszarze **Konfigurowanie**wybierz pozycję **Magazyn obrazów**.

     ![Ekran szybki start programu IBM zD&T Enterprise Edition](media/03-quickstart.png)

4. Na stronie **Konfigurowanie przechowywania obrazów** wybierz pozycję **SSH File Transfer Protocol**.

5. W przypadku **nazwy hosta**wpisz **Localhost** i wprowadź ścieżkę katalogu, w której przesłano obrazy. Na przykład /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Wprowadź **identyfikator użytkownika** i **hasło** maszyny Wirtualnej. Nie używaj identyfikatora użytkownika ZD&T i hasła.

7. Przetestuj połączenie, aby upewnić się, że masz dostęp, a następnie wybierz pozycję **Zapisz,** aby zapisać konfigurację.

## <a name="configure-the-target-environments"></a>Konfigurowanie środowisk docelowych

Następnym krokiem jest skonfigurowanie środowiska docelowego zD&T. To emulowane środowisko hostowane jest miejscem, w którym są uruchamiane obrazy.

1. Na stronie **Szybki start** w obszarze **Konfigurowanie**wybierz pozycję **Środowiska docelowe**.

2. Na stronie **Konfigurowanie środowisk docelowych** wybierz pozycję **Dodaj obiekt docelowy**.

3. Wybierz **Linux**. IBM obsługuje dwa typy środowisk, Linux i Cloud (OpenStack), ale to demo działa na Linuksie.

4. Na stronie **Dodawanie środowiska docelowego** w polu **Nazwa hosta**wprowadź **localhost**. Zachowaj **port SSH** ustawiony na **22**.

5. W polu **etykiety Środowisko docelowe** wprowadź etykietę, taką jak **MyCICS.**

     ![Dodaj ekran środowiska docelowego](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Konfigurowanie adcd i wdrażanie

Po wykonaniu poprzednich kroków konfiguracji należy skonfigurować zD&T, aby używały pakietu i środowiska docelowego. Ponownie, należy użyć procesu przechowywania obrazu w zD&T, który pozwala na montaż i korzystanie z obrazów. Może używać SSH lub FTP.

1. Na stronie **Szybki start** w obszarze **Konfigurowanie**wybierz pozycję **ADCD**. Pojawi się zestaw instrukcji, informujący o krokach, które należy wykonać przed zamontowaniem pakietu ADCD. To wyjaśnia, dlaczego nazwaliśmy katalog docelowy tak, jak zrobiliśmy to wcześniej.

2. Zakładając, że wszystkie obrazy zostały przesłane do odpowiednich katalogów, kliknij łącze **IMAGE z ADCD** wyświetlane w prawym dolnym dolnym przycisku (pokazane w kroku 7 na poniższym zrzucie ekranu).

     ![IBM zD&T Enterprise Edition — konfigurowanie ekranu ADCD](media/05-adcd.png)

## <a name="create-the-image"></a>Tworzenie obrazu

Po zakończeniu poprzedniego kroku konfiguracji zostanie wyświetlona strona **Utwórz obraz przy użyciu składników ADCD.**

1. Wybierz wolumin (w tym przypadku listopad 2017), aby wyświetlić różne pakiety, które znajdują się w tym woluminie.

2. W przypadku tego dema wybierz **system kontroli informacji o klientach (CICS) - 5.3**.

3. W polu **Nazwa obrazu** wpisz nazwę obrazu, takiego jak **MyCICS Image**.

4. Wybierz przycisk **Utwórz obraz** w prawym dolnym dolnym momencie.

     ![IBM zD&T Enterprise Edition — tworzenie obrazu na ekranie SKŁADNIKI ADCD](media/06-adcd.png)

5. W wyświetlonym oknie z informacją, że obraz został pomyślnie wdrożony, wybierz pozycję **Wdrażanie obrazów**.

6. Na stronie **Wdrażanie obrazu w środowisku docelowym** wybierz obraz utworzony na poprzedniej stronie (**Obraz MyCICS**) i środowisko docelowe utworzone wcześniej (**MyCICS**).

7. Na następnym ekranie podaj poświadczenia dla maszyny Wirtualnej (czyli nie poświadczenia ztadmin).

8. W okienku Właściwości wprowadź liczbę **procesorów centralnych ,** ilość **pamięci systemowej (GB)** i **katalog wdrażania** dla uruchomionego obrazu. Ponieważ jest to demo, zachowaj go w małym rozmiarze.

9. Upewnij się, że pole jest zaznaczone dla **Automatycznie wystawiaj polecenie IPL z/OS po wdrożeniu**.

     ![Ekran Właściwości](media/07-properties.png)

10. Wybierz **opcję Zakończ**.

11. Wybierz **pozycję Wdrażanie obrazu** na stronie **Wdrażanie obrazu w środowisku docelowym.**

Obraz można teraz wdrożyć i jest gotowy do zamontowania za pomocą emulatora terminala 3270.

> [!NOTE]
> Jeśli zostanie wyświetlony błąd informujący, że nie ma wystarczającej ilości miejsca na dysku, należy pamiętać, że region wymaga 151 Gb.

Gratulacje! Obecnie na platformie Azure działa środowisko mainframe IBM.

## <a name="learn-more"></a>Dowiedz się więcej

- [Migracja mainframe: mity i fakty](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Rozwiązywanie problemów](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Demistyfikacja elementu mainframe z migracją na platformę Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
