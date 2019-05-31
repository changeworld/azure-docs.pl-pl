---
title: Instalowanie Micro Focus Enterprise Server w wersji 4.0 i Enterprise, Developer 4.0 na platformie Azure | Dokumentacja firmy Microsoft
description: Ponowne hostowanie za pomocą programowania fokus Micro obciążeń mainframe firmy IBM z/OS i przetestuj środowiska na maszynach wirtualnych platformy Azure (maszyny wirtualne).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 45d6f8606c665d78783f987c2f2b49a77801639c
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304615"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Instalowanie Micro Focus Enterprise Server w wersji 4.0 i Enterprise, Developer 4.0 na platformie Azure

W tym artykule przedstawiono sposób konfigurowania [Micro fokus Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) i [Micro fokus Enterprise Developer 4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) na platformie Azure.

Typowe obciążenia na platformie Azure to środowisko deweloperskie i testowe. Ten scenariusz jest typowy, ponieważ jest to ekonomiczna oraz łatwa do wdrożenia i zatrzymywania. Za pomocą serwera Enterprise Micro fokus został utworzony, jedną z największych mainframe rehosting platform dostępne. Możesz uruchamiać obciążenia z/OS na mniej kosztowne x86 platform na platformie Azure przy użyciu Windows lub Linux virtual machines (VMs).

Ta konfiguracja korzysta z maszyn wirtualnych platformy Azure, uruchamiając obraz systemu Windows Server 2016 z portalu Azure Marketplace z zainstalowanym programem Microsoft SQL Server 2017. Ten Instalator ma zastosowanie również do usługi Azure Stack.

Odpowiednie środowisko programistyczne dla serwera Enterprise jest Enterprise Developer uruchamiająca albo programu Microsoft Visual Studio 2017 lub nowszej, program Visual Studio Community (dostępna do bezpłatnego pobierania), lub środowiska Eclipse. W tym artykule przedstawiono sposób wdrażania go za pomocą maszyny wirtualnej systemu Windows Server 2016, który jest dostarczany z programem Visual Studio 2017 lub nowszej.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy zapoznaj się z tych wymagań wstępnych:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Oprogramowanie Micro fokus i prawidłowej licencji (lub licencji próbnej). Jeśli jesteś istniejącym klientem Micro fokus, skontaktuj się z przedstawicielem Micro fokus. W przeciwnym razie [Zamów wersję próbną](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Uzyskaj dokumentację dla [Enterprise Server i Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> Najlepszym rozwiązaniem jest ustanowienie tunelu wirtualnej sieci prywatnej (VPN) lokacja lokacja lub serwera przesiadkowego, dzięki czemu można kontrolować dostęp do maszyn wirtualnych platformy Azure.

## <a name="install-enterprise-server"></a>Instalowanie serwera Enterprise Server

1. Aby uzyskać lepsze bezpieczeństwo i łatwość zarządzania należy rozważyć utworzenie nowej grupy zasobów tylko dla tego projektu — na przykład **RGMicroFocusEntServer**. Użyj pierwszą część nazwy w systemie Azure, aby wybrać typ zasobu, aby ułatwić zauważenia na liście.

2. Tworzy maszynę wirtualną. W portalu Azure Marketplace wybierz maszynę wirtualną i systemu operacyjnego, który ma. Poniżej przedstawiono zalecane ustawienia:

    - **Enterprise Server**: Wybierz maszynę Wirtualną v3 ES2 (z 2 procesorów wirtualnych i 16 GB pamięci) za pomocą systemu Windows Server 2016 i zainstalowanego programu SQL Server 2017. Ten obraz jest dostępny w witrynie Azure Marketplace. Enterprise Server można również użyć usługi Azure SQL Database.

    - **Enterprise Developer**: Wybierz maszynę Wirtualną B2ms (z 2 procesorów wirtualnych i 8 GB pamięci RAM), systemu Windows 10 i zainstalowanego programu Visual Studio. Ten obraz jest dostępny w witrynie Azure Marketplace.

3. W **podstawy** sekcji, wprowadź nazwę użytkownika i hasło. Wybierz **subskrypcji** i **regionu/lokalizacji** chcesz używać dla maszyn wirtualnych. Wybierz **RGMicroFocusEntServer** dla grupy zasobów.

4. Umieść obie maszyny wirtualne w tej samej sieci wirtualnej, dzięki czemu mogą one komunikować się ze sobą.

5. Zaakceptuj wartości domyślne pozostałych ustawień. Zapamiętaj nazwę użytkownika i hasło, utworzonym dla administratora dla tych maszyn wirtualnych.

6. Podczas tworzenia maszyn wirtualnych należy otworzyć porty dla ruchu przychodzącego 9003, 86 i 80 dla protokołu HTTP i port 3389 dla protokołu RDP na komputerze z serwerem przedsiębiorstwa i 3389 na komputerze dewelopera.

7. Aby zalogować się do maszyny wirtualnej serwera przedsiębiorstwa w witrynie Azure portal, wybierz maszynę Wirtualną, ES2 v3. Przejdź do **Przegląd** i wybierz pozycję **Connect** można uruchomić sesję RDP. Zaloguj się przy użyciu poświadczeń, który został utworzony dla maszyny Wirtualnej.

8. W sesji protokołu RDP należy załadować następujące dwa pliki. Ponieważ używasz Windows możesz przeciągać i upuszczać pliki do sesji protokołu RDP:

    - **ES\_40. exe**, plik instalacyjny serwera przedsiębiorstwa.

    - **mflic**, odpowiedni plik licencji — Enterprise Server nie można załadować bez niego.

9. Kliknij dwukrotnie plik, aby rozpocząć instalację. W pierwszym oknie Wybierz lokalizację instalacji i zaakceptuj umowę licencyjną użytkownika końcowego.

     ![Micro ekran Instalatora serwera Enterprise koncentracji uwagi](media/01-enterprise-server.png)

     Po zakończeniu instalacji zostanie wyświetlony następujący komunikat:

     ![Micro ekran Instalatora serwera Enterprise koncentracji uwagi](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Sprawdź dostępność aktualizacji

Po zakończeniu instalacji upewnij się sprawdzić, czy są dodatkowych aktualizacji, ponieważ pewne wymagania wstępne, takie jak pakiet redystrybucyjny Microsoft C++ i .NET Framework są instalowane wraz z serwerem Enterprise.

### <a name="upload-the-license"></a>Przekaż licencji

1. Rozpocznij Micro Focus administracji licencji.

2. Kliknij przycisk **Start** \> **Micro fokus License Manager** \> **licencji, administracji**, a następnie kliknij przycisk **zainstalować** kartę. Wybierz typ formatu licencji do przekazania: plik licencji lub licencji na 16-znakowy kod. Na przykład dla pliku w **plik licencji**, przejdź do **mflic** plik wcześniej przekazany do maszyny Wirtualnej, a następnie wybierz pozycję **licencje instalowanie**.

     ![Okno dialogowe wczesnych administracji licencji koncentracji uwagi](media/03-enterprise-server.png)

3. Sprawdź, czy Enterprise Server ładuje. Spróbuj uruchomić witrynę administracji serwera przedsiębiorstwa z poziomu przeglądarki przy użyciu tego adresu URL <http://localhost:86/> . Zostanie wyświetlona strona Administracja Server Enterprise, jak pokazano.

     ![Strony administrowania serwerem przedsiębiorstwa](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Enterprise Developer zostaną zainstalowane na komputerze dewelopera

1. Wybierz grupę zasobów utworzoną wcześniej (na przykład **RGMicroFocusEntServer**), następnie wybierz obraz dla deweloperów.

2. Aby zalogować się do maszyny wirtualnej, przejdź do **Przegląd** i wybierz pozycję **Connect**. Logowania uruchamia sesję RDP. Zaloguj się przy użyciu poświadczeń, który został utworzony dla maszyny Wirtualnej.

3. W sesji protokołu RDP obciążenia następujące dwa pliki (przeciągania i upuszczania, jeśli chcesz):

    - **edvs2017.exe**, plik instalacyjny serwera przedsiębiorstwa.

    - **mflic**, odpowiedni plik licencji (Enterprise Developer nie załaduje bez niego).

4. Kliknij dwukrotnie **edvs2017.exe** plik, aby rozpocząć instalację. W pierwszym oknie Wybierz lokalizację instalacji i zaakceptuj umowę licencyjną użytkownika końcowego. Jeśli chcesz, wybierz opcję **zainstalować 9.5 Rumba** emulatora terminala, który prawdopodobnie będzie trzeba zainstalować.

     ![Micro Enterprise Developer koncentracji uwagi dla okna dialogowego Instalator programu Visual Studio 2017](media/04-enterprise-server.png)

5. Po zakończeniu instalacji zostanie wyświetlony następujący komunikat:

     ![Instalator pomyślnie wiadomości](media/05-enterprise-server.png)

6. Tak, jak została ona Enterprise Server, należy uruchomić Menedżera licencji Micro fokus. Wybierz **Start** \> **Micro fokus License Manager** \> **licencji, administracji**i kliknij przycisk **zainstalować**kartę.

7. Wybierz typ formatu licencji do przekazania: plik licencji lub licencji na 16-znakowy kod. Na przykład dla pliku w **plik licencji**, przejdź do **mflic** plik wcześniej przekazany do maszyny Wirtualnej, a następnie wybierz pozycję **licencje instalowanie**.

     ![Okno dialogowe wczesnych administracji licencji koncentracji uwagi](media/07-enterprise-server.png)

Po załadowaniu Enterprise Developer, zakończeniu wdrożenia środowiska deweloperskie i testowe Micro skoncentrować się na platformie Azure!

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie aplikacji pokazowej Bank](./demo.md)
- [Uruchom serwer przedsiębiorstwa w kontenerach platformy Docker](./run-enterprise-server-container.md)
- [Migracja aplikacji mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
