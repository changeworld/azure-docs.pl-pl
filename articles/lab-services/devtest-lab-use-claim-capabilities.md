---
title: Korzystanie z funkcji oświadczeń w laboratoriach devtest platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o różnych scenariuszach korzystania z funkcji oświadczeń/nieocenie w laboratoriach devtest platformy Azure
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: spelluru
ms.openlocfilehash: a15148260bccadc59966c86031100f0e0332b0f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67861432"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Korzystanie z funkcji oświadczeń w laboratoriach devtest platformy Azure
Usługa Azure DevTest Labs zwiększa skuteczność i wydajność deweloperów i testerów. W tym artykule koncentruje się na możliwość żądania lub odliczyć maszyn wirtualnych w laboratorium devtest platformy Azure. Zawiera również listę różnych sposobów, że ta funkcja poprawia środowisko użytkownika. Zanim przyjrzymy się różnym scenariuszom, w których ta funkcja może być używana, przyjrzyjmy się, co jest **roszczeniem** i jak działa.

## <a name="claimable-machines"></a>Maszyny, które można zgłaszać
Maszyna podlegające żądaniu to maszyna wirtualna (VM), która jest tworzona w laboratorium bez właściciela. Po odebraniu komputera użytkownik ma pełny zakres opcji dla tej maszyny Wirtualnej. Gdy użytkownik twierdzi, że komputer, kilka zmian są wprowadzane. Maszyna wirtualna jest przenoszona z listy **maszyny wirtualne z żądaniem** do listy **Moje maszyny wirtualne** w witrynie Azure portal. 

Użytkownik może połączyć się z maszyną wirtualną, dostosować artefakty, ponownie uruchomić, zatrzymać lub odsunąć komputer. Istnieje kilka sposobów, aby znak wirtualny można zgłaszać:

- Utwórz maszynę i odwzdroniaj ją tak, aby została przesunięta do puli podlegania roszczeń. 
- Utwórz maszynę wirtualną i umieść w puli udostępnionej przy użyciu [ustawień zaawansowanych](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/).

Istnieją dwa przypadki, w których możliwości oświadczenia/un-claim mogą być skutecznie wykorzystywane. Pierwszy przypadek wymaga większej przemyślanej i planowania, aby być odpowiednio zaprojektowanym i wykonanym. A drugi jest bardziej sytuacyjny. Oto kilka przykładów różnych przypadków.

## <a name="designed-use-of-claimable-machines"></a>Zaprojektowane wykorzystanie maszyn, które mogą być przeznaczone do składania wniosków

- **Tworzenie / testowanie oprogramowania:** Zezwalaj deweloperom lub testerom na większą produktywność dzięki skonfigurowaniu maszyn gotowych i w stanie nieodebranym. Posiadanie zestawu maszyn wirtualnych z różnymi konfiguracjami, niezbędnymi narzędziami i najnowszym kodem umożliwia użytkownikom odnajdowanie się na maszynie wirtualnej i rozpoczęcie pracy bez konieczności spędzania czasu na konfigurowaniu komputera. Przed maszynami wirtualnymi są zgłaszane, maszyny są aprowizacji, ale są zamykania minimalizując koszty posiadania maszyn, które są używane rzadziej. Gdy maszyny wirtualne są potrzebne, użytkownik po prostu twierdzi, że maszyna wirtualna, która uruchamia komputer. Opcja unclaim nie jest tak przydatna w tym przypadku, ponieważ tworzenie nowej maszyny Wirtualnej jest często łatwiejsze i tańsze.
- **Klasa/laboratoria:** Mieć maszyny wirtualne wstępnie skonfigurowane dla klasy lub laboratorium, dzięki czemu uczniowie mogą natychmiast połączyć się z komputerem za pomocą witryny Azure portal.  Gdy uczeń twierdzi, że maszyna wirtualna, laboratorium zapewnia, że nikt nie może ubiegać się o tej samej maszynie. Automatyzacja tego procesu zapewnia, że wymagana liczba maszyn z określonym środowiskiem są dostępne. Jeśli uczniowie nie pojawiają się lub spóźniają się, nieodebrane maszyny mogą być dostępne do czasu zakończenia sesji przy minimalnych kosztach. Opcja unclaim nie jest tak skuteczne w tym scenariuszu, ponieważ maszyna wirtualna jest w nieznanym stanie, gdy poprzedni użytkownik jest wykonywana.
- **Demonstracje:** Używaj maszyn do demonstracji, gdzie maszyny w laboratorium są skonfigurowane w określonych środowiskach. Ta funkcja jest przydatna, gdy wiele osób może dawać demonstrację w tym samym czasie lub w przypadkowych porach, takich jak na konferencji. Opcja unclaim może być przydatne w tej sytuacji, ponieważ demo nie należy zmieniać stanu komputera, umożliwiając użytkownikom powrót maszyny Wirtualnej z powrotem do puli oświadczeń dla następnej demonstracji. Z nieodebranych maszyny są de-aprowizowana i ponoszenia minimalnych kosztów, maszyny wirtualne mogą być pozostawione w laboratorium przez dłuższy czas.
- **Pracownicy tymczasowi/kontraktowi:** Zezwalaj użytkownikom na korzystanie z komputera. Po opuszczeniu zwracają maszynę wirtualną do puli podlegające roszczeniu bez utraty danych. Z maszyny Wirtualnej nieodebrane, inny użytkownik może ubiegać się o maszynę wirtualną i kontynuować lub przejrzeć komputer w celu uzyskania dodatkowych informacji.
- **Ogólnie rzecz biorąc:** Możliwość automatycznego konfigurowania i wdrażania maszyn wirtualnych w określonym zakresie jest przydatna w wielu różnych sytuacjach. Istnieje kilka różnych sytuacji, w których funkcja oświadczeń/nieociągłości pomaga użytkownikom być bardziej wydajne przez zautomatyzowany proces tworzenia maszyn wirtualnych w stanie nieodebranych z konfiguracją zestawu. Konfiguracje mogą obejmować różne systemy operacyjne, języki, dyski lub [inne oprogramowanie (artefakty)](devtest-lab-artifact-author.md) w zależności od potrzeb. Możliwość żądania maszyny Wirtualnej z laboratorium umożliwia użytkownikowi laboratorium uzyskanie prawidłowo skonfigurowanego systemu bez poświęcenia czasu lub wysiłku na konfigurowanie komputera. Menedżer laboratorium może użyć deklarowanego stanu maszyn wirtualnych, aby zwiększyć liczbę wygenerowanych maszyn, oczyścić maszyny i określić priorytet konfiguracji. [Fabryka obrazów](image-factory-create.md) jest dobrym przykładem zautomatyzowanego procesu tworzenia maszyn wirtualnych i obrazów dla wielu laboratoriów. Skrypty mogą być modyfikowane w celu wykonania dowolnej z następujących sytuacji z odpowiednimi zmianami lub być używane jako odwołanie do tworzenia systemu niestandardowego.

## <a name="situational-use-of-claimable-machines"></a>Sytuacyjne wykorzystanie maszyn podlegaujących z roszczeniem

- Użyj funkcji oświadczenia/un-oświadczenia, która pozwala użytkownikom na przekazywanie kontroli nad maszynami z jednego do drugiego i nie trzeba wiedzieć wyraźnie, kto będzie podnoszenia maszyny obok.
- Programowanie, testowanie i debugowanie scenariusza, w którym określona konfiguracja komputera może odtworzyć błąd, a następnie komputer może być nieodebrany, dzięki czemu inny deweloper może ubiegać się o pracę i kontynuować pracę. Ta funkcja jest szczególnie przydatna, ponieważ coraz więcej osób pracuje zdalnie w różnych częściach świata. 
- Członkowie zespołu mogą pracować z jednym środowiskiem. Na przykład można ręcznie skonfigurować złożone środowisko, którego nie można zautomatyzować ani tworzyć zasobów, które mogą obsługiwać tylko modyfikacje dla pojedynczych danych wejściowych, takich jak obrazy. W przeszłości problem ten był rozwiązywany przez uruchomienie dedykowanej maszyny. Funkcja, która może być godną roszczenia, jest ulepszeniem w stosunku do procesu ręcznego poprzez wbudowaną kontrolę dostępu użytkownika i identyfikację wizualną, jeśli jest dostępna. Gdy nieodebrane maszyny Wirtualnej jest de-aprowizowana w celu zmniejszenia kosztów.
- Mieć dysk danych, który jest dołączony do maszyny Wirtualnej. Każdy dysk do ~ 1 TB danych pozwala na dużą ilość danych, które mają być przekazywane bez konieczności kopiowania lub duplikowania danych. Maszyna wirtualna zostanie początkowo utworzona z dołączonym dyskiem, który miał dużą ilość danych.  Każdy użytkownik może następnie zgłosić roszczenie do komputera i uzyskać dostęp do danych. Po zakończeniu odwróć oparć maszynę wirtualną, aby umożliwić innym użytkownikom komputer.

Istnieją pewne zastrzeżenia dotyczące korzystania z maszyn, które można zgłaszać, najczęściej wokół uzyskiwania dostępu do maszyny. Jeśli komputer jest przyłączony do domeny, użytkownik twierdząc, że maszyna musi już uzyskać dostęp, zwykle odbywa się to przez udzielenie dostępu do grupy, która obejmuje wszystkich użytkowników w laboratorium podczas tworzenia maszyny Wirtualnej. Jeśli komputer nie jest przyłączony do domeny, artefakt **Resetowanie hasła maszyny Wirtualnej** w publicznym repozytorium będzie musiał zostać uruchomiony, aby dodać użytkownika jako administratora.  Artefakty można stosować nawet po uruchomieniu lub odebraniu urządzenia.

## <a name="next-steps"></a>Następne kroki
Zobacz następujący artykuł: [Tworzenie maszyn wirtualnych z żądaniem i zarządzanie nimi w laboratoriach devtest usługi Azure DevTest Labs](devtest-lab-add-claimable-vm.md)
