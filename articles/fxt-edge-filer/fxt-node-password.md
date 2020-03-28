---
title: 'Samouczek: Inicjowanie sprzętu — Azure FXT Edge Filer'
description: Jak ustawić hasło początkowe w węzłach filera usługi Azure FXT Edge
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 8cb5f639deb0630575c46db30efe70ad967324a8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75550895"
---
# <a name="tutorial-set-hardware-passwords"></a>Samouczek: Ustawianie haseł sprzętowych

Przy pierwszym wstrząśniu węzłem filera usługi Azure FXT Edge należy ustawić hasło główne. Węzły sprzętowe nie są dostarczane z domyślnym hasłem. 

Porty sieciowe są wyłączone do momentu ustawienia hasła i zalogowania się użytkownika głównego.

Wykonaj ten krok po zainstalowaniu i okablowaniu węzła, ale przed podjęciem próby utworzenia klastra. 

W tym samouczku wyjaśniono, jak połączyć się z węzłem sprzętowym i ustawić hasło. 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Podłącz klawiaturę i monitor do węzła i włącz ją
> * Ustawianie haseł dla portu iDRAC i użytkownika root w tym węźle
> * Zaloguj się jako katalog główny 

Powtórz te kroki dla każdego węzła, który będzie używany w klastrze. 

Ten samouczek trwa około 15 minut. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka wykonaj następujące czynności: 

* [Zainstaluj](fxt-install.md) każdy węzeł Usługi Azure FXT Edge Filer w stojaku na sprzęt i podłącz kable zasilające i dostęp do sieci zgodnie z opisem we [wcześniejszym samouczku](fxt-network-power.md). 
* Znajdź klawiaturę podłączoną do portu USB i monitor podłączony do sieci VGA, który można podłączyć do węzłów sprzętowych. (Port szeregowy węzła jest nieaktywny przed ustawieniem hasła).

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Podłączanie klawiatury i monitora do węzła

Fizycznie podłącz monitor i klawiaturę do węzła Azure FXT Edge Filer. 

* Podłącz monitor do portu VGA.
* Podłącz klawiaturę do jednego z portów USB. 

Ten diagram referencyjny służy do lokalizowania portów z tyłu obudowy. 

> [!NOTE]
> Port szeregowy jest nieaktywny, dopóki po ustawieniu hasła. 

![diagram tylnej części narzędzia Azure FXT Edge Filer z portami szeregowymi, VGA i USB](media/fxt-back-serial-vga-usb.png)

Można użyć przełącznika KVM, jeśli chcesz podłączyć więcej niż jeden węzeł do tych samych urządzeń peryferyjnych. 

Włącz węzeł, naciskając przycisk zasilania z przodu. 

![diagram z przodu narzędzia Azure FXT Edge Filer - okrągły przycisk zasilania jest oznaczony w prawym górnym rogu](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Ustawianie haseł początkowych 

Węzeł azure FXT Edge Filer będzie drukować różne komunikaty na monitorze podczas uruchamiania. Po kilku chwilach pokazuje początkowy ekran konfiguracji w ten sposób:

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

Wprowadzone hasło służy do dwóch rzeczy: 

* Jest to tymczasowe hasło główne dla tego węzła Azure FXT Edge Filer. 

  To hasło zmieni się podczas tworzenia klastra przy użyciu tego węzła lub po dodaniu tego węzła do klastra. Hasło do zarządzania klastrem ``admin``(skojarzone z użytkownikiem) jest również hasłem głównym dla wszystkich węzłów w klastrze.

* Jest to długoterminowe hasło do portu zarządzania sprzętem iDRAC/IPMI.

  Pamiętaj o haśle na wypadek, gdyby trzeba było zalogować się za pomocą protokołu IPMI później, aby rozwiązać problem ze sprzętem.

Wprowadź i potwierdź hasło: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Po wprowadzeniu hasła system kontynuuje uruchamianie. Po zakończeniu daje ``login:`` monit. 

## <a name="sign-in-as-root"></a>Zaloguj się jako katalog główny

Zaloguj się ``root`` tak, jak za pomocą hasła, które właśnie ustawisz. 

```
login: root
Password:**********
```

Po zalogowaniu się jako katalog główny porty sieciowe są aktywne i skontaktują się z serwerem DHCP w celu uzyskania adresów IP. 

## <a name="next-steps"></a>Następne kroki

Węzeł jest gotowy do udziału w klastrze. Można go użyć do utworzenia klastra Azure FXT Edge Filer lub można [dodać go do istniejącego klastra](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Tworzenie klastra](fxt-cluster-create.md)
