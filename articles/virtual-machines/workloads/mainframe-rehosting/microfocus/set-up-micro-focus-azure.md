---
title: Instalowanie programu Micro Focus Enterprise Server 4,0 i Enterprise Developer 4,0 na platformie Azure | Microsoft Docs
description: Przehostaj obciążenia systemu mainframe firmy IBM z/OS przy użyciu środowiska deweloperskiego i testowego na maszynach wirtualnych platformy Azure.
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
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834574"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Instalowanie programu Micro Focus Enterprise Server 4,0 i Enterprise Developer 4,0 na platformie Azure

W tym artykule przedstawiono sposób konfigurowania programu [Micro Focus Enterprise Server 4,0](https://www.microfocus.com/documentation/enterprise-developer/es30/) i [deweloperów Enterprise Developer 4,0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) na platformie Azure.

Typowym obciążeniem na platformie Azure jest środowisko programistyczne i testowe. Ten scenariusz jest powszechny, ponieważ jest to ekonomiczne i łatwe do wdrożenia i odrywania. Dzięki serwerowi korporacyjnemu firma Micro Focus stworzyła jedną z największych dostępnych platform do udostępniania komputerów mainframe. Obciążenia z/OS można uruchamiać na tańszej platformie x86 na platformie Azure przy użyciu maszyn wirtualnych z systemem Windows lub Linux.

Ta konfiguracja korzysta z maszyn wirtualnych platformy Azure z systemem Windows Server 2016 z witryny Azure Marketplace z zainstalowanym programem Microsoft SQL Server 2017. Ta konfiguracja dotyczy również Azure Stack.

Odpowiednie środowisko programistyczne dla programu Enterprise Server to deweloper przedsiębiorstwa, który działa w Microsoft Visual Studio 2017 lub nowszej, Visual Studio Community (bezpłatnie do pobrania) lub przezaćmienie. W tym artykule pokazano, jak wdrożyć go za pomocą maszyny wirtualnej z systemem Windows Server 2016, która jest dostarczana z programem Visual Studio 2017 lub nowszym.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem sprawdź następujące wymagania wstępne:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Oprogramowanie mikro Focus i ważną licencję (lub licencję próbną). Jeśli jesteś istniejącym klientem programu Micro Focus, skontaktuj się z przedstawicielem firmy Micro Focus. W przeciwnym razie [Poproś o wersję próbną](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Zapoznaj się z dokumentacją dla [przedsiębiorstw Server i Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> Najlepszym rozwiązaniem jest skonfigurowanie tunelu wirtualnej sieci prywatnej (VPN) typu lokacja-lokacja lub serwera przesiadkowego, dzięki czemu można kontrolować dostęp do maszyn wirtualnych platformy Azure.

## <a name="install-enterprise-server"></a>Instalowanie serwera Enterprise Server

1. Aby zapewnić lepsze zabezpieczenia i możliwości zarządzania, należy rozważyć utworzenie nowej grupy zasobów tylko dla tego projektu — na przykład **RGMicroFocusEntServer**. Użyj pierwszej części nazwy na platformie Azure, aby wybrać typ zasobu, który ma być łatwiejszy na liście.

2. Tworzy maszynę wirtualną. W portalu Azure Marketplace wybierz maszynę wirtualną i żądany system operacyjny. Oto zalecana konfiguracja:

    - **Serwer przedsiębiorstwa**: Wybierz maszynę wirtualną ES2 v3 (z 2 procesorów wirtualnych vCPU i 16 GB pamięci) z zainstalowanym systemem Windows Server 2016 i SQL Server 2017. Ten obraz jest dostępny w portalu Azure Marketplace. Serwer przedsiębiorstwa może również używać Azure SQL Database.

    - **Deweloper przedsiębiorstwa**: Wybierz maszynę wirtualną B2ms (z 2 procesorów wirtualnych vCPU i 8 GB pamięci) z zainstalowanym systemem Windows 10 i Visual Studio. Ten obraz jest dostępny w portalu Azure Marketplace.

3. W sekcji **podstawowe** wprowadź nazwę użytkownika i hasło. Wybierz **subskrypcję** i **lokalizację/region** , których chcesz użyć dla maszyn wirtualnych. Wybierz pozycję **RGMicroFocusEntServer** dla grupy zasobów.

4. Umieść obie maszyny wirtualne w tej samej sieci wirtualnej, aby mogły komunikować się ze sobą.

5. Zaakceptuj wartości domyślne dla pozostałych ustawień. Zapamiętaj nazwę użytkownika i hasło, które tworzysz dla administratora tych maszyn wirtualnych.

6. Po utworzeniu maszyn wirtualnych Otwórz porty przychodzące 9003, 86 i 80 dla protokołów HTTP i 3389 dla protokołu RDP na komputerze z systemem Enterprise Server i 3389 na komputerze dewelopera.

7. Aby zalogować się do maszyny wirtualnej z serwerem przedsiębiorstwa, w Azure Portal wybierz maszynę wirtualną z ES2 v3. Przejdź do sekcji **Przegląd** i wybierz pozycję **Połącz** , aby uruchomić sesję RDP. Zaloguj się przy użyciu poświadczeń utworzonych dla maszyny wirtualnej.

8. W sesji RDP Załaduj następujące dwa pliki. Ponieważ używasz systemu Windows, możesz przeciągać i upuszczać pliki do sesji RDP:

    - **es\_40. exe**, plik instalacyjny serwera przedsiębiorstwa.

    - **mflic**, odpowiedni plik licencji — serwer przedsiębiorstwa nie zostanie załadowany bez tego.

9. Kliknij dwukrotnie plik, aby rozpocząć instalację. W pierwszym oknie Wybierz lokalizację instalacji i zaakceptuj umowę licencyjną użytkownika końcowego.

     ![Ekran instalacji serwera korporacyjnego Micro Focus](media/01-enterprise-server.png)

     Po zakończeniu instalacji zostanie wyświetlony następujący komunikat:

     ![Ekran instalacji serwera korporacyjnego Micro Focus](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Sprawdź aktualizacje

Po zakończeniu instalacji upewnij się, że zainstalowano dodatkowe aktualizacje, ponieważ liczba wymagań wstępnych, takich jak C++ pakiet redystrybucyjny firmy Microsoft i .NET Framework, została zainstalowana razem z serwerem przedsiębiorstwa.

### <a name="upload-the-license"></a>Przekaż licencję

1. Rozpocznij Administrowanie licencjami programu Micro Focus.

2. Kliknij kolejno pozycje **Start** \> **Zarządzanie licencjami** **Menedżer** \> licencji, a następnie kliknij kartę **Instaluj** . Wybierz typ formatu licencji do przekazania: plik licencji lub 16-znakowy kod licencji. Na przykład dla pliku w **pliku licencji**przejdź do pliku **mflic** , który został przekazany wcześniej do maszyny wirtualnej, a następnie wybierz pozycję **Zainstaluj licencje**.

     ![Okno dialogowe Administrowanie licencjami w programie Micro Focus](media/03-enterprise-server.png)

3. Sprawdź, czy serwer przedsiębiorstwa ładuje. Spróbuj uruchomić witrynę administracyjną przedsiębiorstwa Server z przeglądarki przy użyciu tego adresu <http://localhost:86/>URL . Zostanie wyświetlona strona Administracja serwera przedsiębiorstwa, jak pokazano.

     ![Strona administrowania serwerem przedsiębiorstwa](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Zainstaluj dewelopera przedsiębiorstwa na komputerze dewelopera

1. Wybierz utworzoną wcześniej grupę zasobów (na przykład **RGMicroFocusEntServer**), a następnie wybierz obraz dewelopera.

2. Aby zalogować się do maszyny wirtualnej, przejdź do sekcji **Omówienie** i wybierz pozycję **Połącz**. Ten logowania uruchamia sesję RDP. Zaloguj się przy użyciu poświadczeń utworzonych dla maszyny wirtualnej.

3. W sesji RDP Załaduj następujące dwa pliki (przeciągnij i upuść, jeśli chcesz):

    - **edvs2017. exe**, plik instalacyjny serwera przedsiębiorstwa.

    - **mflic**, odpowiedni plik licencji (deweloper przedsiębiorstwa nie będzie ładowany).

4. Kliknij dwukrotnie plik **edvs2017. exe** , aby rozpocząć instalację. W pierwszym oknie Wybierz lokalizację instalacji i zaakceptuj umowę licencyjną użytkownika końcowego. Jeśli chcesz, wybierz pozycję **Zainstaluj Rumba 9,5** , aby zainstalować ten emulator terminalu, którego prawdopodobnie potrzebujesz.

     ![Okno dialogowe Instalator programu Enterprise Developer dla programu Visual Studio 2017](media/04-enterprise-server.png)

5. Po zakończeniu instalacji zostanie wyświetlony następujący komunikat:

     ![Komunikat o pomyślnym skonfigurowaniu](media/05-enterprise-server.png)

6. Uruchom Menedżera licencji programu Micro Focus tak samo jak w przypadku programu Enterprise Server. Wybierz kolejno pozycje **Start** \> **Zarządzanie**licencjami **Menedżer** \> licencji, a następnie kliknij kartę **Instaluj** .

7. Wybierz typ formatu licencji do przekazania: plik licencji lub 16-znakowy kod licencji. Na przykład dla pliku w **pliku licencji**przejdź do pliku **mflic** , który został przekazany wcześniej do maszyny wirtualnej, a następnie wybierz pozycję **Zainstaluj licencje**.

     ![Okno dialogowe Administrowanie licencjami w programie Micro Focus](media/07-enterprise-server.png)

Podczas ładowania dewelopera przedsiębiorstwa wdrożenie mikrofokusu i środowiska testowego na platformie Azure zostało zakończone.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie aplikacji demonstracyjnej banku](./demo.md)
- [Uruchamianie serwera korporacyjnego w kontenerach platformy Docker](./run-enterprise-server-container.md)
- [Migracja aplikacji mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
