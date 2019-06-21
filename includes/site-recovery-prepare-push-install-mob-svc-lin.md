---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 5ba55e339db4c33d1b0d759e4682481e20318938
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203741"
---
### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Przygotowanie do instalacji wypychanej na serwerze z systemem Linux

1. Upewnij się, że istnieje połączenie sieciowe między komputer z systemem Linux a serwerem przetwarzania.
1. Utwórz konto, za pomocą którego serwer przetwarzania będzie mógł uzyskać dostęp do komputera. Konto powinno być użytkownikiem **root** na źródłowym serwerze z systemem Linux. Tylko w przypadku instalacji wypychanej i aktualizacje, należy używać tego konta.
1. Sprawdź, czy plik /etc/hosts na źródłowym serwerze z systemem Linux zawiera wpisy mapujące lokalną nazwę hosta na adres IP skojarzony ze wszystkimi kartami sieciowymi.
1. Zainstaluj najnowsze pakiety openssh, openssh-server i openssl na komputerze, który chcesz replikować.
1. Upewnij się, że protokół Secure Shell (SSH) jest włączony i uruchomiony na porcie 22.
1. Włącz podsystem SFTP i hasło uwierzytelnianie w pliku sshd_config. Wykonaj następujące kroki:

    a. Zaloguj się jako użytkownik **root**.

    b. W **/etc/ssh/sshd_config** plików, znajdź wiersz, który rozpoczyna się od **PasswordAuthentication**.

    c. Usuń znaczniki komentarza i zmień wartość na **tak**.

    d. Znajdź wiersz, który rozpoczyna się od **podsystemu**, i usuń znaczniki komentarza.

      ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

    e. Uruchom ponownie usługę **sshd**.

1. Dodaj konto utworzone w narzędziu CSPSConfigtool. Wykonaj następujące kroki:

    a. Zaloguj się do serwera konfiguracji.

    b. Otwórz plik **cspsconfigtool.exe**. Jest on dostępny jako skrót na pulpicie i w folderze %ProgramData%\home\svsystems\bin.

    c. Na **Zarządzanie kontami** zaznacz **Dodaj konto**.

    d. Dodaj utworzone konto.

    d. Wprowadź używane poświadczenia po włączeniu replikacji dla komputera.
