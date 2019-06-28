---
title: Inicjowanie sprzęt — Microsoft Azure FXT krawędzi filtr
description: Jak ustawić początkowe hasło w węzłach filtr Edge FXT platformy Azure
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 11cf9f49014648fff1e78aff91c5a724a812e9e7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450296"
---
# <a name="tutorial-set-hardware-passwords"></a>Samouczek: Zestaw sprzętu hasła

Po raz pierwszy możesz zużycia się węzeł filtr Edge FXT platformy Azure, należy ustawić hasło główne. Węzły sprzętu nie są dostarczane za pomocą hasła domyślne. 

Porty sieciowe są wyłączone do czasu, po ustawić hasła i loguje się jako użytkownik root.

Ten krok należy wykonać po zainstalowaniu i okablowania węzła, ale przed przystąpieniem do tworzenia klastra. 

W tym samouczku wyjaśniono, jak łączyć się z węzłem sprzętu i ustawić hasło. 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Nawiązać jest klawiatura i monitor do węzła i włączenie go
> * Ustaw hasła dla użytkownika iDRAC portu i katalog główny, w tym węźle
> * Zaloguj się jako użytkownik główny 

Powtórz te kroki dla każdego węzła, który będzie używany w klastrze. 

Ten samouczek przedstawia około 15 minut. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, wykonaj następujące kroki: 

* [Zainstaluj](fxt-install.md) w sprzęt w każdym węźle filtr Edge FXT Azure stojaku i Dołącz przewodów zasilania oraz dostęp do sieci, zgodnie z opisem w [wcześniej samouczek](fxt-network-power.md). 
* Znajdź urządzenia USB klawiatury i to połączone VGA monitor możesz dołączyć do węzłami sprzętowymi. (Port szeregowy węzeł jest nieaktywny, przed skonfigurowaniem hasła).

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Nawiązać jest klawiatura i monitor do węzła

Fizycznie połączyć do węzła filtr Edge FXT Azure monitor i klawiaturę. 

* Podłącz monitor do portu VGA.
* Podłącz klawiaturę do jednego z portów USB. 

Zlokalizuj porty na odwrocie podkładki obudowy przy użyciu tego diagramu odwołania. 

> [!NOTE]
> Port szeregowy jest nieaktywne do momentu, po ustawieniu hasła. 

![Diagram tylnej stronie filtr Edge FXT platformy Azure z numerem seryjnym, VGA i portów USB etykietą](media/fxt-back-serial-vga-usb.png)

Można użyć przełącznika KVM, jeśli chcesz nawiązać połączenie tych samych urządzeń peryferyjnych więcej niż jeden węzeł. 

Włącz węzła przez naciśnięcie przycisku zasilania w przód. 

![Diagram początku filtr Edge FXT Azure - round przycisku zasilania jest oznaczona etykietą u góry prawej](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Hasła początkowego zestawu 

Węzeł Filtr Edge FXT Azure zostanie wydrukowany różne komunikaty do monitora podczas rozruchu. Po kilku chwilach pokazuje ekran początkowej konfiguracji, tak jak to:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set iDRAC and temporary root password.
Minimum password length is 8.
Enter new password:
```

Wprowadzone hasło jest używane dotyczy dwóch składników: 

* To hasło tymczasowe głównego dla tego węzła filtr Edge FXT platformy Azure. 

  To hasło zmieni się podczas tworzenia klastra przy użyciu tego węzła lub dodać ten węzeł do klastra. Hasło zarządzania klastrem (skojarzone z użytkownikiem ``admin``) jest również hasła głównego dla wszystkich węzłów w klastrze.

* To długoterminowe hasła za pośrednictwem portu usługi zarządzania sprzętem iDRAC/IPMI.

  Upewnij się, że pamiętasz hasła, w przypadku, gdy trzeba zalogować się przy użyciu interfejsu IPMI później, aby rozwiązać problem ze sprzętem.

Wprowadź i Potwierdź hasło: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Po wprowadzeniu hasła, system będzie nadal uruchamiany. Po zakończeniu zapewnia ``login:`` wiersza. 

## <a name="sign-in-as-root"></a>Zaloguj się jako użytkownik główny

Zaloguj się jako ``root`` przy użyciu hasła, który został ustawiony. 

```
login: root
Password:**********
```

Po zalogowaniu się jako użytkownik główny porty sieciowe są aktywne i skontaktuje się z serwera DHCP dla adresów IP. 

## <a name="next-steps"></a>Kolejne kroki

Ten węzeł jest gotowe do przeniesienia poza częścią klastra. Służy do utworzenia klastra filtr Edge FXT platformy Azure, możesz też [dodać go do istniejącego klastra](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Tworzenie klastra](fxt-cluster-create.md)
