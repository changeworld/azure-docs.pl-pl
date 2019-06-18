---
title: Uruchom Micro Focus Enterprise Server w wersji 4.0 w kontenerze platformy Docker na maszynach wirtualnych platformy Azure
description: Ponowne hostowanie obciążeń mainframe firmy IBM z/OS, uruchamiając Micro fokus Enterprise Server w kontenerze platformy Docker na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61488460"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Uruchamianie Micro Focus Enterprise Server w wersji 4.0 w kontenerze platformy Docker na platformie Azure

Możesz uruchomić Micro fokus Enterprise Server 4.0 w kontenerze platformy Docker na platformie Azure. Ten samouczek pokazuje sposób. Enterprise Server używa pokaz acctdemo CICS Windows (System kontroli klienta informacji).

Docker dodaje przenośność i izolacji aplikacji. Na przykład można wyeksportować obrazu platformy Docker z jedną Windows z maszyn wirtualnych do uruchomienia na inną, lub z repozytorium do serwera Windows za pomocą platformy Docker. Obraz platformy Docker działa w nowej lokalizacji, z tą samą konfiguracją — bez konieczności instalowania serwera przedsiębiorstwa. Jego część obrazu. Zagadnienia dotyczące licencjonowania jest nadal mają zastosowanie.

W tym samouczku instaluje **Windows Datacenter 2016 z kontenerami** maszynę wirtualną (VM) w portalu Azure Marketplace. Ta maszyna wirtualna zawiera **Docker 18.09.0**. Poniższe kroki pokazują jak miało miejsce wdrożenie kontenera, należy ją uruchomić, a następnie połączyć się z nim 3270 emulatora.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy zapoznaj się z tych wymagań wstępnych:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Oprogramowanie Micro fokus i prawidłowej licencji (lub licencji próbnej). Jeśli jesteś istniejącym klientem Micro fokus, skontaktuj się z przedstawicielem Micro fokus. W przeciwnym razie [Zamów wersję próbną](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > Pliki pokaz platformy Docker są dołączone do Enterprise Server 4.0. Ten samouczek używa ent\_serwera\_pliki dockerfile\_4.0\_windows.zip. Do niego dostęp z tym samym miejscu, dostęp do pliku instalacyjnego Enterprise Server, lub przejdź do *fokus Micro* na rozpoczęcie pracy.

- W dokumentacji dotyczącej [Enterprise Server i Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1. Bezpieczny nośnika z ent\_serwera\_pliki dockerfile\_4.0\_windows.zip pliku. Zabezpiecz ES-Docker-Prod-XXXXXXXX.mflic pliku licencji (wymagane do tworzenia obrazów platformy Docker).

2. Tworzenie maszyny Wirtualnej. Aby to zrobić, otwórz Azure portal, wybierz opcję **Utwórz zasób** od lewego górnego rogu i Filtruj według *systemu windows server*. W wynikach wybierz **systemu Windows Server 2016 Datacenter — z kontenerami**.

     ![Wyniki wyszukiwania portalu Azure](media/01-portal.png)

3. Aby skonfigurować właściwości maszyny wirtualnej, wybierz Szczegóły wystąpienia:

    1. Wybierz rozmiar maszyny wirtualnej. W tym samouczku należy wziąć pod uwagę przy użyciu **standardowa DS2\_v2** maszyny Wirtualnej przy użyciu 2 procesorów Vcpu i 7 GB pamięci.

    2. Wybierz **Region** i **grupy zasobów** chcesz wdrożyć.

    3. Aby uzyskać **opcji dostępności**, użyj ustawienia domyślnego.

    4. Aby uzyskać **Username**, typ, którego chcesz użyć konta administratora i hasło.

    5. Upewnij się, że **portu 3389 protokołu RDP** jest otwarty. Tylko ten port musi być publicznie udostępniany, dzięki czemu możesz zalogować się do maszyny Wirtualnej. Następnie zaakceptuj wartości domyślne i kliknij przycisk **przeglądanie + tworzenie**.

     ![Utwórz okienka maszyny wirtualnej](media/container-02.png)

4. Oczekiwanie na zakończenie (kilka minut) wdrożenia. Wyświetlany jest komunikat informujący o tym, że Twoja maszyna wirtualna została utworzona.

5. Kliknij przycisk **przejdź do zasobu** można przejść do **Przegląd** bloku maszyny wirtualnej.

6. Po prawej stronie, kliknij przycisk **Connect** przycisku. **Połącz z maszyną wirtualną** opcje są wyświetlane po prawej stronie.

7. Kliknij przycisk **Pobierz plik RDP** przycisk, aby pobrać plik protokołu RDP, który umożliwia dołączanie do maszyny Wirtualnej.

8. Po zakończeniu pobierania, otwórz go i wpisz nazwę użytkownika i hasło utworzone dla maszyny Wirtualnej.

     > [!NOTE]
     > Nie należy używać poświadczeń firmowych do logowania. (Klienta RDP przyjęto założenie, że chcesz użyć. Nie.)

9.  Wybierz **więcej opcji**, następnie wybierz swoje poświadczenia maszyny Wirtualnej.

W tym momencie maszyna wirtualna jest uruchomiona i dołączone za pośrednictwem protokołu RDP. Po zarejestrowaniu się i przygotowane do kolejnego kroku.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Utwórz katalog piaskownica i przekaż plik zip

1.  Utwórz katalog na maszynie Wirtualnej, w którym możesz przekazać pliki pokazów i licencji. Na przykład **C:\\piaskownicy**.

2.  Przekaż **ent\_serwera\_pliki dockerfile\_4.0\_windows.zip** i **ES-Docker-Prod-XXXXXXXX.mflic** plik do katalogu, który został utworzony.

3.  Wyodrębnij zawartość pliku zip do **ent\_serwera\_pliki dockerfile\_4.0\_windows** Katalog utworzony przez proces wyodrębniania. Ten katalog zawiera plik readme (jako plik HTML, a txt) i dwa podkatalogów **EnterpriseServer** i **przykłady**.

4.  Kopiuj **ES-Docker-Prod-XXXXXXXX.mflic** na dysku C:\\piaskownicy\\ent\_serwera\_pliki dockerfile\_4.0\_windows\\ EnterpriseServer i C:\\piaskownicy\\ent\_serwera\_pliki dockerfile\_4.0\_windows\\przykłady\\CICS katalogów.

> [!NOTE]
> Upewnij się, że kopiujesz plik licencjonowania w obu katalogach. Są one wymagane dla kroku kompilacji platformy Docker upewnić się, że obrazy są licencjonowane.

## <a name="check-docker-version-and-create-base-image"></a>Zapoznaj się z wersją platformy Docker i utworzyć obrazu podstawowego

> [!IMPORTANT]
> Tworzenie odpowiedni obraz platformy Docker jest procesem dwuetapowym. Najpierw utwórz obraz podstawowy Enterprise Server 4.0. Następnie utwórz inny obraz x64 platformy. Chociaż można utworzyć x86 (32-bitowy) obrazu, należy użyć 64-bitowego obrazu.

1. Otwórz wiersz polecenia.

2. Sprawdź, czy platforma Docker jest zainstalowana. W wierszu polecenia wpisz polecenie:

    ```
        docker version
    ```

     Na przykład wersja był 18.09.0, kiedy to zostało zapisane.

3. Aby zmienić katalog, wpisz **cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**.

4. Typ **bld.bat IacceptEULA** do rozpoczęcia procesu tworzenia początkowej podstawowy obraz. Poczekaj kilka minut, aż do uruchomienia tego procesu. W wynikach, zwróć uwagę, dwa obrazy, które zostały utworzone — jeden dla x64 i jeden dla x86:

     ![Okno polecenia pokazywanie obrazów](media/container-04.png)

5. Aby utworzyć obraz końcowy demonstracyjne CICS, przełącz się do katalogu CICS, wpisując **cd\\piaskownicy\\ent\_serwera\_pliki dockerfile\_4.0\_systemuwindows\\ Przykłady\\CICS**.

6. Do utworzenia obrazu, wpisz **bld.bat x64**. Poczekaj kilka minut, aż do uruchamiania procesu i komunikat informujący o tym, że obraz został utworzony.

7. Typ **obrazów platformy docker** do wyświetlania listy wszystkich obrazów platformy Docker zainstalowany na maszynie Wirtualnej. Upewnij się, że **microfocus/es-acctdemo** jest jednym z nich.

     ![Okno polecenia przedstawiający es acctdemo obrazu](media/container-05.png)

## <a name="run-the-image"></a>Uruchom obraz 

1.  Aby uruchomić Enterprise Server 4.0 i aplikacji acctdemo, wpisz w wierszu polecenia:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Takich jak instalowania emulatora terminala 3270 [x3270](http://x3270.bgp.nu/) i przy jego użyciu, aby dołączyć za pośrednictwem portu 9040, do obrazu, który jest uruchomiony.

3.  Uzyskaj adres IP kontenera acctdemo, dlatego Docker może działać jako serwer DHCP dla kontenerów zarządza:

    1.  Pobierz identyfikator uruchomionego kontenera. Typ **Docker ps** w wierszu polecenia i Zanotuj identyfikator (**22a0fe3159d0** w tym przykładzie). Zapisz go do kolejnego kroku.

    2.  Aby uzyskać adres IP dla kontenera acctdemo, użyj Identyfikatora kontenera z poprzedniego kroku w następujący sposób:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Na przykład:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Zanotuj adres IP dla obrazu acctdemo. Na przykład adres w następujących danych wyjściowych jest 172.19.202.52.

     ![Okno polecenia wyświetlanie adresu IP](media/container-06.png)

5. Zainstaluj obraz przy użyciu emulatora. Skonfigurować emulator, aby użyć adresu acctdemo obrazu i port 9040. W tym miejscu ma **172.19.202.52:9040**. Należy do Ciebie będzie podobny. **Logować się CICS** zostanie otwarty ekran.

    ![Logować do ekranu CICS](media/container-07.png)

6. Zaloguj się do regionu CICS, wprowadzając **SYSAD** dla **USERID** i **SYSAD** dla **hasło**.

7. Wyczyść ekran przy użyciu emulatora usługi keymap. W przypadku x3270, wybrać **Keymap** opcji menu.

8. Aby uruchomić aplikację acctdemo, wpisz **ACCT**. Jest wyświetlany ekran początkowy dla aplikacji.

     ![Ekran pokaz konta](media/container-08.png)

9. Poeksperymentuj z wyświetlanie typów kont. Na przykład wpisz **D** dla żądania i **11111** dla **konta**. Inne numery kont, aby spróbować są 22222, 33333 i tak dalej.

     ![Ekran pokaz konta](media/container-09.png)

10. Aby wyświetlić konsolę administracyjną serwera przedsiębiorstwa, przejdź do wiersza polecenia i wpisz **start http:172.19.202.52:86**

     ![Konsola administracyjna serwera przedsiębiorstwa](media/container-010.png)

To wszystko! Jesteś teraz uruchomiona i zarządzania aplikacją CICS w kontenerze platformy Docker.

## <a name="next-steps"></a>Kolejne kroki

- [Instalowanie Micro Focus Enterprise Server w wersji 4.0 i Enterprise, Developer 4.0 na platformie Azure](./set-up-micro-focus-azure.md)
- [Migracja aplikacji mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
