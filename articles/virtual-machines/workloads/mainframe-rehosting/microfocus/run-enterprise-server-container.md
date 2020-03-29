---
title: Uruchamianie programu Micro Focus Enterprise Server 4.0 w kontenerze platformy Docker na maszynach wirtualnych platformy Azure
description: Hostuj ponownie obciążenia komputerami mainframe IBM z/OS, uruchamiając program Micro Focus Enterprise Server w kontenerze platformy Docker na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61488460"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Uruchamianie programu Micro Focus Enterprise Server 4.0 w kontenerze platformy Docker na platformie Azure

Program Micro Focus Enterprise Server 4.0 można uruchomić w kontenerze platformy Docker na platformie Azure. Ten samouczek pokazuje, jak to zrobić. Używa demonstracji acctdemo systemu Windows CICS (Customer Information Control System) dla programu Enterprise Server.

Docker dodaje przenośność i izolację do aplikacji. Na przykład można wyeksportować obraz platformy Docker z jednej maszyny Wirtualnej systemu Windows do uruchomienia na innej lub z repozytorium do serwera systemu Windows z platformą Docker. Obraz platformy Docker jest uruchamiany w nowej lokalizacji w tej samej konfiguracji — bez konieczności instalowania programu Enterprise Server. To część obrazu. Nadal obowiązują zagadnienia dotyczące licencjonowania.

W tym samouczku instaluje **centrum danych systemu Windows 2016 z** maszyną wirtualną kontenery (VM) z portalu Azure Marketplace. Ta maszyna wirtualna zawiera **dok 18.09.0**. Poniższe kroki pokazują, jak wdrożyć kontener, uruchomić go, a następnie połączyć się z nim za pomocą emulatora 3270.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem zapoznaj się z tymi wymaganiami:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Oprogramowanie Micro Focus i ważna licencja (lub licencja próbna). Jeśli jesteś istniejącym klientem micro focus, skontaktuj się z przedstawicielem firmy Micro Focus. W przeciwnym razie [poproś o wersję próbną](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > Pliki demonstracyjny platformy Docker są dołączone do programu Enterprise Server 4.0. W tym samouczku\_\_użyto pliku\_dockerfiles\_4.0 windows.zip. Uzyskaj do niego dostęp z tego samego miejsca, w które uzyskałeś dostęp do pliku instalacyjnego programu Enterprise Server, lub przejdź do *micro focus,* aby rozpocząć.

- Dokumentacja dla [enterprise server i enterprise developer](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1. Zabezpiecz nośnik z\_pliku\_windows.zip\_serwera ent.\_ Zabezpiecz plik licencjonowania ES-Docker-Prod-XXXXXXXX.mflic (wymagany do tworzenia obrazów platformy Docker).

2. Utwórz maszynę wirtualną. Aby to zrobić, otwórz witrynę Azure Portal, wybierz pozycję **Utwórz zasób** z lewej górnej części i filtruj według *serwera systemu Windows*. W wynikach wybierz pozycję **Centrum danych systemu Windows Server 2016 — z kontenerami**.

     ![Wyniki wyszukiwania w portalu Azure](media/01-portal.png)

3. Aby skonfigurować właściwości maszyny Wirtualnej, wybierz szczegóły wystąpienia:

    1. Wybierz rozmiar maszyny wirtualnej. W tym samouczku należy rozważyć użycie standardowej maszyny Wirtualnej **\_DS2 v2** z 2 procesorami wirtualnymi i 7 GB pamięci.

    2. Wybierz **region** i **grupę zasobów,** w których chcesz wdrożyć.

    3. W przypadku **opcji dostępności**użyj ustawienia domyślnego.

    4. W obszarze **Nazwa użytkownika**wpisz konto administratora, którego chcesz użyć, oraz hasło.

    5. Upewnij **się, że port 3389 RDP** jest otwarty. Tylko ten port musi być publicznie naświetlony, dzięki czemu można zalogować się do maszyny Wirtualnej. Następnie zaakceptuj wszystkie wartości domyślne i kliknij przycisk **Przejrzyj+ utwórz**.

     ![Tworzenie okienka maszyny wirtualnej](media/container-02.png)

4. Poczekaj na zakończenie wdrożenia (kilka minut). Komunikat stwierdza, że maszyna wirtualna została utworzona.

5. Kliknij **przycisk Przejdź do zasobu,** aby przejść do bloku **Przegląd** maszyny Wirtualnej.

6. Po prawej stronie kliknij przycisk **Połącz.** Opcje **Połącz z maszyną wirtualną** są wyświetlane po prawej stronie.

7. Kliknij przycisk **Pobierz plik RDP,** aby pobrać plik RDP, który umożliwia dołączenie do maszyny Wirtualnej.

8. Po zakończeniu pobierania pliku otwórz go i wpisz nazwę użytkownika i hasło utworzone dla maszyny Wirtualnej.

     > [!NOTE]
     > Do logowania nie należy używać poświadczeń firmowych. (Klient RDP zakłada, że można ich użyć. Nie.)

9.  Wybierz **pozycję Więcej opcji**, a następnie wybierz poświadczenia maszyny Wirtualnej.

W tym momencie maszyna wirtualna jest uruchomiona i podłączona za pośrednictwem protokołu RDP. Zalogujesz się i przygotuj do następnego kroku.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Tworzenie katalogu piaskownicy i przesyłanie pliku zip

1.  Utwórz katalog na maszynie Wirtualnej, w którym można przekazać pliki demo i licencji. Na przykład **\\C: Piaskownica**.

2.  Przekaż plik **dockerfiles\_windows.zip\_\_serwera\_ent 4.0** i plik **ES-Docker-Prod-XXXXXXXX.mflic** do utworzonego katalogu.

3.  Wyodrębnij zawartość pliku zip do katalogu **\_dockerfiles\_systemu Windows\_4.0\_** utworzonego przez proces wyodrębniania. Ten katalog zawiera plik readme (jako plik .html i txt) oraz dwa podkatalogi, **EnterpriseServer** i **Examples**.

4.  Kopiowanie **ES-Docker-Prod-XXXXXXXX.mflic** do\\\\C:\_Sandbox ent\_server\_dockerfiles\_4.0 windows\\EnterpriseServer i C:\\Sandbox\\ent\_\_server\_dockerfiles 4.0\_windows\\Przykłady katalogów\\CICS.

> [!NOTE]
> Upewnij się, że plik licencjonowania został skopiowany do obu katalogów. Są one wymagane dla kroku kompilacji platformy Docker, aby upewnić się, że obrazy są poprawnie licencjonowane.

## <a name="check-docker-version-and-create-base-image"></a>Sprawdź wersję platformy Docker i utwórz obraz podstawowy

> [!IMPORTANT]
> Tworzenie odpowiedniego obrazu platformy Docker jest procesem dwuetapowym. Najpierw utwórz obraz podstawowy programu Enterprise Server 4.0.Następnie utwórz kolejny obraz dla platformy x64. Chociaż można utworzyć obraz x86 (32-bitowy), użyj obrazu 64-bitowego.

1. Otwórz wiersz polecenia.

2. Sprawdź, czy program Docker jest zainstalowany. W wierszu polecenia wpisz polecenie:

    ```
        docker version
    ```

     Na przykład wersja była 18.09.0, gdy została napisana.

3. Aby zmienić katalog, wpisz **cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**.

4. Wpisz **bld.bat IacceptEULA,** aby rozpocząć proces kompilacji dla początkowego obrazu podstawowego. Poczekaj kilka minut na uruchomienie tego procesu. W wynikach zwróć uwagę na dwa utworzone obrazy — jeden dla x64 i jeden dla x86:

     ![Okno polecenia z obrazami](media/container-04.png)

5. Aby utworzyć obraz końcowy dla demonstracji CICS, przełącz się do katalogu CICS, wpisując **cd\\Sandbox\\\\ent\\\_\_server dockerfiles\_4.0\_windows Examples CICS**.

6. Aby utworzyć obraz, wpisz **bld.bat x64**. Poczekaj kilka minut na uruchomienie procesu i komunikat informujący, że obraz został utworzony.

7. Wpisz **obrazy docker,** aby wyświetlić listę wszystkich obrazów platformy Docker zainstalowanych na maszynie Wirtualnej. Upewnij się, że **microfocus/es-acctdemo** jest jednym z nich.

     ![Okno polecenia z obrazem es-acctdemo](media/container-05.png)

## <a name="run-the-image"></a>Uruchamianie obrazu 

1.  Aby uruchomić enterprise server 4.0 i aplikację acctdemo, w wierszu polecenia typu:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Zainstaluj emulator terminala 3270, taki jak [x3270,](http://x3270.bgp.nu/) i użyj go do podłączenia, za pośrednictwem portu 9040, do uruchomionego obrazu.

3.  Pobierz adres IP kontenera acctdemo, aby firma Docker mogła działać jako serwer DHCP dla kontenerów, którymi zarządza:

    1.  Pobierz identyfikator uruchomionego kontenera. Wpisz **docker ps** w wierszu polecenia i zanotuj identyfikator (**22a0fe3159d0** w tym przykładzie). Zapisz go do następnego kroku.

    2.  Aby uzyskać adres IP kontenera acctdemo, użyj identyfikatora kontenera z poprzedniego kroku w następujący sposób:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Przykład:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Zanotuj adres IP obrazu acctdemo. Na przykład adres w następujących danych wyjściowych jest 172.19.202.52.

     ![Okno polecenia z adresem IP](media/container-06.png)

5. Zamontuj obraz za pomocą emulatora. Skonfiguruj emulator do używania adresu obrazu acctdemo i portu 9040. Tutaj jest **172.19.202.52:9040**. Twoje będą podobne. Zostanie otwarty ekran **Signon to CICS.**

    ![Ekran Logowania do cics](media/container-07.png)

6. Zaloguj się do regionu CICS, wprowadzając pozycję **SYSAD** dla **identyfikatora użytkownika** i **identyfikatora SYSAD** dla **hasła**.

7. Wyczyść ekran, używając mapy klawiszy emulatora. W przypadku x3270 wybierz opcję menu **Mapa klawiszy.**

8. Aby uruchomić aplikację acctdemo, wpisz **ACCT**. Zostanie wyświetlony ekran początkowy aplikacji.

     ![Ekran wersji demonstracyjnej konta](media/container-08.png)

9. Eksperymentuj z typami kont wyświetlanych. Na przykład wpisz **D** dla żądania i **11111** dla **konta**. Inne numery kont do wypróbowania to 22222, 33333 i tak dalej.

     ![Ekran wersji demonstracyjnej konta](media/container-09.png)

10. Aby wyświetlić konsolę Enterprise Server Administration, przejdź do wiersza polecenia i wpisz **start http:172.19.202.52:86**

     ![Konsola administracji serwerem Przedsiębiorstwa](media/container-010.png)

Gotowe. Teraz jest uruchomiony i zarządzanie aplikacją CICS w kontenerze platformy Docker.

## <a name="next-steps"></a>Następne kroki

- [Instalowanie programu Micro Focus Enterprise Server 4.0 i Enterprise Developer 4.0 na platformie Azure](./set-up-micro-focus-azure.md)
- [Migracja aplikacji komputerów mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
