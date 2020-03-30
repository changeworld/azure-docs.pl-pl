---
title: Przewodnik po przyspieszonym instalacyjnym laboratorium w klasie dla usług Azure Lab Services
description: Ten przewodnik pomaga twórcom laboratorium szybko skonfigurować konto laboratoryjne do użytku w ich szkole.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: d58e11551c2857c269e8985e81f84138f6d389ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370822"
---
# <a name="classroom-lab-setup-guide"></a>Przewodnik po konfiguracji laboratorium w klasie

Proces publikowania laboratorium dla uczniów może potrwać do kilku godzin w zależności od liczby maszyn wirtualnych (VM), które zostaną utworzone w laboratorium.  Należy dać sobie co najmniej jeden dzień, aby skonfigurować laboratorium, aby upewnić się, że działa poprawnie i dać wystarczająco dużo czasu, aby opublikować maszyny wirtualne studentów.

## <a name="understand-your-classs-lab-requirements"></a>Poznaj wymagania laboratoryjne swojej klasy

Przed skonfigurowaniem nowego laboratorium należy wziąć pod uwagę następujące pytania:

**Jakie wymagania dotyczące oprogramowania ma klasa?**

Na podstawie celów uczenia się klasy należy zdecydować, który system operacyjny, aplikacje, narzędzia itp.   Aby skonfigurować maszyny wirtualne w laboratorium, dostępne są trzy opcje:

- **Użyj obrazu portalu Azure Marketplace** — marketplace zawiera setki obrazów, których można użyć podczas tworzenia laboratorium.  W przypadku niektórych klas obraz rynku może już zawierać wszystko, czego potrzebujesz do swojej klasy.

- **Utwórz nowy obraz niestandardowy** — możesz utworzyć własny obraz niestandardowy, używając obrazu z rynku jako punktu wyjścia i dostosowując go, instalując dodatkowe oprogramowanie, dokonując zmian w konfiguracji itp.

- **Użyj istniejącego obrazu niestandardowego** — możesz ponownie użyć istniejących obrazów niestandardowych, które zostały wcześniej utworzone lub utworzone przez innych administratorów lub wykładowców w szkole; wymaga to od administratorów skonfigurowania galerii obrazów udostępnionych, która jest repozytorium do zapisywania obrazów niestandardowych.

> [!NOTE]
> Administratorzy są odpowiedzialni za włączenie witryny Marketplace i obrazów niestandardowych, dzięki czemu można z nich korzystać; należy skoordynować z działem IT, aby upewnić się, że obrazy, których potrzebujesz, są włączone.  Obrazy niestandardowe, które tworzysz są automatycznie włączane do użytku w laboratoriach, które jesteś właścicielem.

**Jakie wymagania sprzętowe ma klasa?**

Istnieje wiele rozmiarów obliczeń, które można wybrać, który obejmuje:

- Zagnieżdżone rozmiary wirtualizacji, dzięki czemu można dać dostęp do studentów do maszyny Wirtualnej, która jest w stanie hostować wiele zagnieżdżonych maszyn wirtualnych; na przykład ten rozmiar obliczeń jest często używany dla kursów sieciowych.

- Rozmiary procesorów GPU, dzięki czemu uczniowie mogą korzystać z aplikacji intensywnie korzystających z komputera, takich jak sztuczna inteligencja i uczenie maszynowe.

Zapoznaj się z przewodnikiem na [temat rozmiaru maszyny Wirtualnej,](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) aby zobaczyć pełną listę dostępnych rozmiarów obliczeń.

> [!NOTE]
> W zależności od regionu, w którego zostanie utworzone laboratorium, może zostać wyświetlonych mniej rozmiarów obliczeń dostępnych, ponieważ zależy to od regionu.  Naszym ogólnym zaleceniem jest wybranie najmniejszego rozmiaru obliczeń, który jest najbliższy Twoim potrzebom.  Dzięki usłudze Azure Lab Services można później skonfigurować nowe laboratorium o innej pojemności obliczeniowej.

**Jakie zależności ma klasa na zewnętrznej platformy Azure lub zasobów sieciowych?**

Jeśli maszyny wirtualne laboratorium muszą używać zasobów zewnętrznych, takich jak baza danych, udział plików, serwer licencjonowania itp., należy skoordynować z administratorami, aby upewnić się, że laboratorium ma dostęp do tych zasobów.

Aby uzyskać dostęp do zasobów platformy Azure, które *nie* są zabezpieczone przez sieć wirtualną, można uzyskać dostęp do tych zasobów za pośrednictwem publicznego Internetu bez dodatkowej konfiguracji przez administratorów.

> [!NOTE]
> Należy rozważyć, czy można zmniejszyć zależności laboratorium do zasobów zewnętrznych, zapewniając zasób bezpośrednio na maszynie Wirtualnej.  Na przykład, aby wyeliminować konieczność odczytywania danych z zewnętrznej bazy danych, można zainstalować bazę danych bezpośrednio na maszynie Wirtualnej.  

**W jaki sposób koszty będą kontrolowane?**

Usługi Lab services używa modelu wyceny płatności zgodnie z rzeczywistym użyciem, co oznacza, że płacisz tylko za czas, w którym działa maszyna wirtualna w laboratorium.  Aby kontrolować koszty, dostępne są trzy opcje, które są zwykle używane w połączeniu ze sobą:

- **Harmonogram** — harmonogram umożliwia automatyczne sterowanie, kiedy maszyny wirtualne w laboratoriach są uruchamiane i zamykane.
- **Przydział** — przydział kontroluje liczbę godzin, przez które uczniowie będą mieli dostęp do maszyny Wirtualnej poza zaplanowanymi godzinami.  Jeśli przydział zostanie osiągnięty, gdy student go używa, maszyna wirtualna jest automatycznie zamykana, a uczeń nie może ponownie uruchomić maszyny Wirtualnej, chyba że przydział zostanie zwiększony.
- **Automatyczne zamykanie** — po włączeniu ustawienie automatycznego zamykania powoduje, że maszyny wirtualne z systemem Windows automatycznie zamykają się po pewnym czasie, gdy uczeń odłączy się od sesji RDP.  To ustawienie jest domyślnie wyłączone.  

    > [!NOTE]
    > To ustawienie obecnie istnieje tylko dla systemu Windows.

**W jaki sposób uczniowie uratują swoją pracę?**

Studenci są przypisane ich własnej maszyny Wirtualnej, który jest przypisany do nich przez cały okres istnienia laboratorium.  Mogą wybrać:

- Zapisz bezpośrednio na maszynie wirtualnej.
- Zapisz w zewnętrznym repozytorium, takim jak OneDrive, GitHub itp.

Aby korzystać z usługi OneDrive, można skonfigurować to automatycznie dla studentów na ich maszyn wirtualnych laboratorium.  Dodatkowe informacje na ten temat znajdują się poniżej.

> [!NOTE]
> Aby upewnić się, że twoi uczniowie mają stały dostęp do zapisanej pracy poza laboratorium, która obejmuje po zakończeniu zajęć, zalecamy uczniom zapisanie swojej pracy w zewnętrznym repozytorium.

**W jaki sposób uczniowie będą łączyć się ze swoją maszyną wirtualną?**

W przypadku maszyn wirtualnych z protokołu RDP do systemu Windows zaleca się, aby uczniowie korzystali z [klienta pulpitu zdalnego firmy Microsoft](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  Klient pulpitu zdalnego obsługuje komputery Mac, Chromebooki i system Windows.

W przypadku maszyn wirtualnych z systemem Linux uczniowie mogą używać protokołu SSH lub RDP.   Aby połączyć się przy użyciu protokołu RDP, użytkownik jest odpowiedzialny za instalowanie i konfigurowanie niezbędnych pakietów RDP i GUI.  Więcej szczegółów na ten temat znajduje się poniżej.

## <a name="set-up-your-lab"></a>Konfigurowanie laboratorium

Po zrozumieniu wymagań dla laboratorium klasy, można go skonfigurować.  Skorzystaj z łączy w tej sekcji, aby dowiedzieć się, jak skonfigurować laboratorium.

1. **Tworzenie laboratorium**

   Instrukcje można znaleźć w samouczku na temat [tworzenia laboratorium w klasie.](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab)

    > [!NOTE]
    > Jeśli klasa wymaga zagnieżdżonej wirtualizacji, zapoznaj się z instrukcjami w przewodniku, który pokazuje [włączenie wirtualizacji zagnieżdżonej.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)

1. **Dostosowywanie obrazów i publikowanie maszyn wirtualnych w laboratorium**

    Aby dostosować obrazy i opublikować maszyny wirtualne w laboratorium, należy połączyć się ze specjalną maszyną wirtualną o nazwie szablon maszyny Wirtualnej; należy zapoznać się z instrukcjami następującymi przewodnikami:
    - [Tworzenie szablonu maszyny Wirtualnej i zarządzanie nim](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Korzystanie z galerii obrazów udostępnionych](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Jeśli korzystasz z systemu Windows, należy również zapoznać się z instrukcjami zawartymi w instrukcji [przygotowania szablonu maszyny Wirtualnej systemu Windows](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template).  Te instrukcje obejmują kroki konfigurowania usługi OneDrive i pakietu Office dla uczniów do korzystania.

1. **Zarządzanie pulą i pojemnością maszyn wirtualnych**

   W zależności od klasy można łatwo skalować w górę lub w dół pojemność maszyn wirtualnych.  Należy pamiętać, że zwiększenie pojemności maszyny Wirtualnej może potrwać kilka godzin, ponieważ wymaga to konfigurowania nowych maszyn wirtualnych.  Zapoznaj się z instrukcjami dotyczącymi [konfigurowania puli maszyn wirtualnych i zarządzania nim.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)

1. **Dodawanie użytkowników laboratorium i zarządzanie nimi**

   Aby dodać użytkowników do laboratorium, zapoznaj się z krokami w następujących samouczkach:
   - [Dodawanie użytkowników do laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Wysyłanie zaproszeń do użytkowników](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Więcej informacji na temat typów kont, z których mogą korzystać uczniowie, zapoznaj się z następującym artykułem:
    - [Konta uczniów](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)
  
1. **Ustawianie kontroli kosztów**

    Aby kontrolować koszty laboratorium, należy ustawić harmonogramy, przydziały i automatyczne zamykanie; zapoznaj się z instrukcjami w następujących samouczkach:

   - [Ustawianie harmonogramu](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > W zależności od typu zainstalowanego systemu operacyjnego maszyna wirtualna może potrwać kilka minut.  Aby upewnić się, że maszyna wirtualna laboratorium jest gotowa do użycia w zaplanowanych godzinach, zaleca się uruchamianie maszyn wirtualnych z 30-minutowym wyprzedzeniem, aby upewnić się, że maszyny wirtualne są uruchomione i gotowe do użycia.

   - [Ustawianie przydziałów dla użytkowników](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) i [ustawianie dodatkowego przydziału dla określonego użytkownika](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quota-for-a-specific-user)
  
   - [Włączanie automatycznego zamykania po rozłączeniu](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Harmonogramy, przydziały i automatyczne zamykanie *nie* mają zastosowania do maszyny Wirtualnej szablonu.  W związku z tym należy upewnić się, że zostanie zamknięty szablon maszyny Wirtualnej, gdy nie jest używany w przeciwnym razie będzie nadal ponosić koszty.  Ponadto domyślnie podczas tworzenia laboratorium szablon maszyny Wirtualnej jest uruchamiany automatycznie, więc należy upewnić się, że natychmiast zakończyć konfigurowanie laboratorium i zamknąć szablon maszyny Wirtualnej.

1. **Korzystanie z pulpitu nawigacyjnego**

    Instrukcje można znaleźć w instrukcji obsługi [przy użyciu pulpitu nawigacyjnego laboratorium.](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)

    > [!NOTE]
    > Szacowane koszty wyświetlane na pulpicie nawigacyjnym to maksymalny koszt, jakiego można oczekiwać od uczniów podczas korzystania z laboratorium.  Na przykład *nie* zostanie naliczona opłata za niewykorzystane godziny przydziału przez uczniów.  Szacowane koszty *nie* odzwierciedlają żadnych opłat za korzystanie z szablonu maszyny Wirtualnej lub Galerii obrazów udostępnionych.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły:

- [Śledzenie użycia laboratorium na potrzeby zajęć](tutorial-track-usage.md)
  
- [Dostęp do laboratorium na potrzeby zajęć](tutorial-connect-virtual-machine-classroom-lab.md)
