---
title: Instalowanie programu Micro Focus Enterprise Server 4.0 i Enterprise Developer 4.0 na platformie Azure | Dokumenty firmy Microsoft
description: Hostuj ponownie obciążenia komputerów mainframe IBM z/OS przy użyciu środowiska deweloperskiego i testowego micro focus na maszynach wirtualnych platformy Azure.Rehost your IBM z/OS mainframe workloads using the Micro Focus development and test environment on Azure virtual machines (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 05/29/2019
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: a5426c3cd7552b24739f9a20e01d5a4b42bd383c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68834574"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Instalowanie programu Micro Focus Enterprise Server 4.0 i Enterprise Developer 4.0 na platformie Azure

W tym artykule pokazano, jak skonfigurować [micro focus enterprise server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) i Micro Focus Enterprise Developer [4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) na platformie Azure.

Typowe obciążenie na platformie Azure jest środowiskiem deweloperskim i testowym. Ten scenariusz jest typowy, ponieważ jest tak opłacalne i łatwe do wdrożenia i zburzyć. Dzięki serwerowi Enterprise Server firma Micro Focus stworzyła jedną z największych dostępnych platform hostowania mainframe. Obciążeń z/OS można uruchamiać na tańszej platformie x86 na platformie Azure przy użyciu maszyn wirtualnych systemu Windows lub Linux (maszyn wirtualnych).

Ta konfiguracja używa maszyn wirtualnych platformy Azure z obrazem systemu Windows Server 2016 z portalu Azure Marketplace z już zainstalowanym programem Microsoft SQL Server 2017. Ta konfiguracja dotyczy również usługi Azure Stack.

Odpowiednim środowiskiem programistycznym dla serwera Enterprise Server jest deweloper dla przedsiębiorstw, który działa w programie Microsoft Visual Studio 2017 lub nowszym, Visual Studio Community (do pobrania za darmo) lub Eclipse. W tym artykule pokazano, jak wdrożyć go przy użyciu maszyny wirtualnej systemu Windows Server 2016, która jest zainstalowana w programie Visual Studio 2017 lub nowszym.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem zapoznaj się z tymi wymaganiami:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Oprogramowanie Micro Focus i ważna licencja (lub licencja próbna). Jeśli jesteś istniejącym klientem micro focus, skontaktuj się z przedstawicielem firmy Micro Focus. W przeciwnym razie [poproś o wersję próbną](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Pobierz dokumentację dla [enterprise server i enterprise developer](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> Najlepszym rozwiązaniem jest skonfigurowanie tunelu wirtualnej sieci prywatnej lokacji (VPN) lub skrzynki szybkiego, aby można było kontrolować dostęp do maszyn wirtualnych platformy Azure.

## <a name="install-enterprise-server"></a>Instalowanie serwera Enterprise Server

1. Aby uzyskać większe bezpieczeństwo i łatwość zarządzania, należy rozważyć utworzenie nowej grupy zasobów tylko dla tego projektu — na przykład **RGMicroFocusEntServer**. Użyj pierwszej części nazwy na platformie Azure, aby wybrać typ zasobu, aby ułatwić wykryć na liście.

2. Tworzy maszynę wirtualną. W portalu Azure Marketplace wybierz odpowiednią maszynę wirtualną i system operacyjny. Oto zalecana konfiguracja:

    - **Serwer enterprise:** Wybierz maszynę wirtualną ES2 v3 (z 2 procesorami wirtualnymi i 16 GB pamięci) z zainstalowanymi systemami Windows Server 2016 i SQL Server 2017. Ten obraz jest dostępny w portalu Azure Marketplace. Enterprise Server można również użyć usługi Azure SQL Database.

    - **Deweloper dla przedsiębiorstw:** Wybierz maszynę wirtualną B2ms (z 2 procesorami wirtualnymi i 8 GB pamięci) z zainstalowanymi systemami Windows 10 i Visual Studio. Ten obraz jest dostępny w portalu Azure Marketplace.

3. W sekcji **Podstawy** wprowadź swoją nazwę użytkownika i hasło. Wybierz **subskrypcję** i **lokalizację/region,** który chcesz używać dla maszyn wirtualnych. Wybierz **pozycję RGMicroFocusEntServer** dla grupy zasobów.

4. Umieść obie maszyny wirtualne w tej samej sieci wirtualnej, aby mogły komunikować się ze sobą.

5. Zaakceptuj ustawienia domyślne dla pozostałych ustawień. Zapamiętaj nazwę użytkownika i hasło utworzone dla administratora tych maszyn wirtualnych.

6. Po utworzeniu maszyn wirtualnych otwórz porty przychodzące 9003, 86 i 80 dla protokołu HTTP i 3389 dla protokołu RDP na komputerze Enterprise Server i 3389 na komputerze dewelopera.

7. Aby zalogować się do maszyny wirtualnej serwera Enterprise Server, w witrynie Azure portal wybierz maszynę wirtualną ES2 w wersji 3. Przejdź do sekcji **Przegląd** i wybierz pozycję **Połącz,** aby uruchomić sesję RDP. Zaloguj się przy użyciu poświadczeń utworzonych dla maszyny Wirtualnej.

8. Z sesji RDP załaduj następujące dwa pliki. Ponieważ używasz systemu Windows, możesz przeciągnąć i upuścić pliki do sesji RDP:

    - **es\_40.exe**, plik instalacyjny programu Enterprise Server.

    - **mflic**, odpowiedni plik licencji — Enterprise Server nie zostanie załadowany bez niego.

9. Kliknij dwukrotnie plik, aby rozpocząć instalację. W pierwszym oknie wybierz lokalizację instalacji i zaakceptuj umowę licencyjną użytkownika końcowego.

     ![Ekran Ustawienia serwera micro Focus Enterprise](media/01-enterprise-server.png)

     Po zakończeniu instalacji zostanie wyświetlony następujący komunikat:

     ![Ekran Ustawienia serwera micro Focus Enterprise](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Sprawdź aktualizacje

Po zakończeniu instalacji należy sprawdzić dostępność dodatkowych aktualizacji, ponieważ wraz z serwerem Enterprise Server jest zainstalowanych wiele wymagań wstępnych, takich jak program Microsoft C++ Redystrybucyjny i program .NET Framework.

### <a name="upload-the-license"></a>Prześlij licencję

1. Uruchom administrację licencją micro focus.

2. Kliknij **pozycję Uruchom** \> **zarządzanie licencjami**Menedżera \> licencji Micro **Focus** , a następnie kliknij kartę **Zainstaluj.** Wybierz typ formatu licencji do przekazania: plik licencji lub 16-znakowy kod licencji. Na przykład dla pliku w **polu Plik licencji**przejdź do pliku **mflic** przesłanego wcześniej do maszyny Wirtualnej i wybierz pozycję **Zainstaluj licencje**.

     ![Okno dialogowe Administrowanie licencjami micro Focus](media/03-enterprise-server.png)

3. Sprawdź, czy serwer Enterprise Server jest wczytytyny. Spróbuj uruchomić witrynę administracji serwerem <http://localhost:86/> Enterprise z przeglądarki przy użyciu tego adresu URL . Strona Enterprise Server Administration jest wyświetlana w sposób pokazany.

     ![Strona Administracja serwerem przedsiębiorstwa](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Instalowanie dewelopera w przedsiębiorstwie na komputerze deweloperskim

1. Wybierz grupę zasobów utworzoną wcześniej (na przykład **RGMicroFocusEntServer),** a następnie wybierz obraz dewelopera.

2. Aby zalogować się do maszyny wirtualnej, przejdź do sekcji **Przegląd** i wybierz pozycję **Połącz**. Ten znak uruchamia sesję RDP. Zaloguj się przy użyciu poświadczeń utworzonych dla maszyny Wirtualnej.

3. Z sesji RDP załaduj dwa następujące pliki (przeciąganie i upuszczanie, jeśli chcesz):

    - **edvs2017.exe**, plik instalacyjny programu Enterprise Server.

    - **mflic**, odpowiedni plik licencji (Enterprise Developer nie będzie ładować bez niego).

4. Kliknij dwukrotnie plik **edvs2017.exe,** aby rozpocząć instalację. W pierwszym oknie wybierz lokalizację instalacji i zaakceptuj umowę licencyjną użytkownika końcowego. Jeśli chcesz, wybierz **Zainstaluj Rumba 9.5,** aby zainstalować ten emulator terminala, który prawdopodobnie będziesz potrzebować.

     ![Okno dialogowe Ustawienia programu Micro Focus Enterprise Developer dla programu Visual Studio 2017](media/04-enterprise-server.png)

5. Po zakończeniu instalacji zostanie wyświetlony następujący komunikat:

     ![Konfiguracja pomyślnego komunikatu](media/05-enterprise-server.png)

6. Uruchom Menedżera licencji Micro Focus tak samo, jak w przypadku serwera Enterprise Server. Wybierz **pozycję Uruchom** \> **zarządzanie licencjami**Menedżera licencji Micro \> **Focus** i kliknij kartę **Zainstaluj.**

7. Wybierz typ formatu licencji do przekazania: plik licencji lub 16-znakowy kod licencji. Na przykład dla pliku w **polu Plik licencji**przejdź do pliku **mflic** przesłanego wcześniej do maszyny Wirtualnej i wybierz pozycję **Zainstaluj licencje**.

     ![Okno dialogowe Administrowanie licencjami micro Focus](media/07-enterprise-server.png)

Po załadowaniu dewelopera korporacyjnego wdrożenie środowiska deweloperskiego i testowego micro focus na platformie Azure zostanie zakończone!

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie aplikacji Bank Demo](./demo.md)
- [Uruchamianie serwera enterprise w kontenerach platformy Docker](./run-enterprise-server-container.md)
- [Migracja aplikacji komputerów mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
