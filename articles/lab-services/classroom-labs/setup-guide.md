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
ms.openlocfilehash: e00b6dd5af1cb489aee9e8b4c9f3337eb02e4b14
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878106"
---
# <a name="classroom-lab-setup-guide"></a>Przewodnik po konfiguracji laboratorium w klasie

Proces publikowania laboratorium dla uczniów może potrwać do kilku godzin, w zależności od liczby maszyn wirtualnych (maszyn wirtualnych), które zostaną utworzone w laboratorium. Odczekaj co najmniej jeden dzień na skonfigurowanie laboratorium, aby upewnić się, że działa poprawnie i dać wystarczająco dużo czasu na publikowanie maszyn wirtualnych uczniów.

## <a name="understand-the-lab-requirements-of-your-class"></a>Poznaj wymagania laboratoryjne swojej klasy

Przed skonfigurowaniem nowego laboratorium należy wziąć pod uwagę następujące pytania.

### <a name="what-software-requirements-does-the-class-have"></a>Jakie wymagania dotyczące oprogramowania ma klasa?

Na podstawie celów uczenia się klasy zdecyduj, który system operacyjny, aplikacje i narzędzia muszą być zainstalowane na maszynach wirtualnych laboratorium. Aby skonfigurować maszyny wirtualne w laboratorium, dostępne są trzy opcje:

- **Użyj obrazu portalu Azure Marketplace:** usługa Azure Marketplace zawiera setki obrazów, których można użyć podczas tworzenia laboratorium. W przypadku niektórych klas jeden z tych obrazów może już zawierać wszystko, czego potrzebujesz do zajęć.

- **Utwórz nowy obraz niestandardowy:** Możesz utworzyć własny obraz niestandardowy przy użyciu obrazu portalu Azure Marketplace jako punktu wyjścia i dostosować go, instalując dodatkowe oprogramowanie i wprowadzać zmiany konfiguracji.

- **Użyj istniejącego obrazu niestandardowego:** można ponownie użyć istniejących obrazów niestandardowych, które zostały wcześniej utworzone lub które zostały utworzone przez innych administratorów lub wykładowców w szkole. Wymaga to od administratorów skonfigurowania udostępnionej galerii obrazów, która jest repozytorium do zapisywania obrazów niestandardowych.

> [!NOTE]
> Administratorzy są odpowiedzialni za włączenie obrazów i obrazów niestandardowych w portalu Azure Marketplace, dzięki czemu można ich używać. Koordynuj działania z działem IT, aby upewnić się, że potrzebne obrazy są włączone. Obrazy niestandardowe, które tworzysz są automatycznie włączane do użytku w laboratoriach, które jesteś właścicielem.

### <a name="what-hardware-requirements-does-the-class-have"></a>Jakie wymagania sprzętowe ma klasa?

Istnieje wiele rozmiarów obliczeń, które można wybrać:

- Zagnieżdżone rozmiary wirtualizacji, dzięki czemu można udzielić dostępu do uczniów do maszyny Wirtualnej, która jest w stanie hostować wiele zagnieżdżonych maszyn wirtualnych. Na przykład można użyć tego rozmiaru obliczeń dla kursów sieciowych.

- Rozmiary procesorów GPU, dzięki czemu uczniowie mogą korzystać z typów aplikacji intensywnie korzystających z komputera. Na przykład ten wybór może być odpowiedni dla sztucznej inteligencji i uczenia maszynowego.

Zapoznaj się z przewodnikiem na [temat rozmiaru maszyny Wirtualnej,](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) aby zobaczyć pełną listę dostępnych rozmiarów obliczeń.

> [!NOTE]
> W zależności od regionu laboratorium może być widoczna mniejsza liczba dostępnych rozmiarów obliczeń, ponieważ różni się to w zależności od regionu. Ogólnie rzecz biorąc należy wybrać najmniejszy rozmiar obliczeń, który jest najbliżej Twoich potrzeb. Dzięki usłudze Azure Lab Services można później skonfigurować nowe laboratorium o innej pojemności obliczeniowej.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Jakie zależności ma klasa na zewnętrznej platformy Azure lub zasobów sieciowych?

Jeśli maszyny wirtualne laboratorium muszą używać zasobów zewnętrznych, takich jak baza danych, udział plików lub serwer licencjonowania, koordynuj z administratorami, aby upewnić się, że laboratorium ma dostęp do tych zasobów.

Aby uzyskać dostęp do zasobów platformy Azure, które *nie* są zabezpieczone przez sieć wirtualną, nie trzeba szukać dodatkowej konfiguracji przez administratorów. Dostęp do tych zasobów można uzyskać za pośrednictwem publicznego internetu.

> [!NOTE]
> Należy rozważyć, czy można zmniejszyć zależności laboratorium do zasobów zewnętrznych, zapewniając zasób bezpośrednio na maszynie Wirtualnej. Na przykład, aby wyeliminować konieczność odczytywania danych z zewnętrznej bazy danych, można zainstalować bazę danych bezpośrednio na maszynie Wirtualnej.  

### <a name="how-will-costs-be-controlled"></a>W jaki sposób koszty będą kontrolowane?

Usługi Lab services używają modelu cenowego płatności zgodnie z rzeczywistym użyciem, co oznacza, że płacisz tylko za czas, w którym działa maszyna wirtualna w laboratorium. Aby kontrolować koszty, dostępne są trzy opcje, które są zwykle używane w połączeniu ze sobą:

- **Harmonogram:** Harmonogram umożliwia automatyczne kontrolowanie, kiedy maszyny wirtualne w laboratoriach są uruchamiane i zamykane.
- **Przydział:** Przydział kontroluje liczbę godzin, przez które uczniowie będą mieli dostęp do maszyny Wirtualnej poza zaplanowanymi godzinami. Jeśli przydział zostanie osiągnięty, gdy student go używa, maszyna wirtualna jest automatycznie zamykana. Student nie może ponownie uruchomić maszyny Wirtualnej, chyba że przydział zostanie zwiększony.
- **Automatyczne zamykanie:** Po włączeniu ustawienie automatycznego zamykania powoduje automatyczne zamykanie maszyn wirtualnych systemu Windows po pewnym czasie, po odłączeniu studenta od sesji protokołu RDP (Remote Desktop Protocol). To ustawienie jest domyślnie wyłączone.  

    > [!NOTE]
    > To ustawienie obecnie istnieje tylko dla systemu Windows.

### <a name="how-will-students-save-their-work"></a>W jaki sposób uczniowie uratują swoją pracę?

Uczniowie są przypisywane ich własnej maszyny Wirtualnej, który jest przypisany do nich przez cały okres istnienia laboratorium. Mogą wybrać:

- Zapisz bezpośrednio na maszynie wirtualnej.
- Zapisz w lokalizacji zewnętrznej, takiej jak OneDrive lub GitHub.

Usługa OneDrive jest możliwa do automatycznego skonfigurowania usługi OneDrive dla uczniów na maszynach wirtualnych w laboratorium.

> [!NOTE]
> Aby upewnić się, że twoi uczniowie nadal mają dostęp do zapisanej pracy poza laboratorium, a po zakończeniu zajęć zalecamy uczniom zapisanie swojej pracy w zewnętrznym repozytorium.

### <a name="how-will-students-connect-to-their-vm"></a>W jaki sposób uczniowie będą łączyć się ze swoją maszyną wirtualną?

W przypadku maszyn wirtualnych z protokołu RDP do systemu Windows zaleca się, aby uczniowie korzystali z [klienta pulpitu zdalnego firmy Microsoft](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Klient pulpitu zdalnego obsługuje komputery Mac, Chromebooki i system Windows.

W przypadku maszyn wirtualnych z systemem Linux uczniowie mogą używać protokołu SSH lub RDP. Aby uczniowie połączyli się za pomocą protokołu RDP, należy zainstalować i skonfigurować niezbędne pakiety RDP i GUI.

## <a name="set-up-your-lab"></a>Konfigurowanie laboratorium

Po zrozumieniu wymagań dla laboratorium klasy, jesteś gotowy, aby go skonfigurować. Skorzystaj z łączy w tej sekcji, aby dowiedzieć się, jak skonfigurować laboratorium.

1. **Tworzenie laboratorium.** Instrukcje można znaleźć w samouczku na temat [tworzenia laboratorium w klasie.](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab)

    > [!NOTE]
    > Jeśli klasa wymaga zagnieżdżonej wirtualizacji, zobacz kroki [włączania zagnieżdżonej wirtualizacji](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Dostosuj obrazy i publikuj maszyny wirtualne w laboratorium.** Połącz się ze specjalną maszyną wirtualną o nazwie szablon maszyny Wirtualnej. Zobacz kroki opisane w następujących przewodnikach:
    - [Tworzenie szablonu maszyny Wirtualnej i zarządzanie nim](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Używanie galerii obrazów udostępnionych](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Jeśli używasz systemu Windows, należy również zapoznać się z instrukcjami [dotyczącymi przygotowywania szablonu maszyny Wirtualnej systemu Windows](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template). Te instrukcje obejmują kroki konfigurowania usługi OneDrive i pakietu Office dla uczniów do korzystania.

1. **Zarządzanie pulą maszyn wirtualnych i pojemnością.** Można łatwo skalować w górę lub w dół pojemności maszyny Wirtualnej, zgodnie z potrzebami klasy. Należy pamiętać, że zwiększenie pojemności maszyny Wirtualnej może potrwać kilka godzin, ponieważ wiąże się to z konfigurowaniem nowych maszyn wirtualnych. Zobacz kroki konfigurowania [puli maszyn wirtualnych i zarządzania nim](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords).

1. **Dodawanie użytkowników laboratorium i zarządzanie nimi.** Aby dodać użytkowników do laboratorium, zapoznaj się z krokami w następujących samouczkach:
   - [Dodawanie użytkowników do laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Wysyłanie zaproszeń do użytkowników](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Aby uzyskać informacje na temat typów kont, z których mogą korzystać uczniowie, zobacz [Konta uczniów](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts).
  
1. **Ustaw kontrolki kosztów.** Aby kontrolować koszty laboratorium, należy ustawić harmonogramy, przydziały i automatyczne zamykanie. Zobacz następujące samouczki:

   - [Ustawianie harmonogramu](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > W zależności od typu zainstalowanego systemu operacyjnego uruchomienie maszyny wirtualnej może potrwać kilka minut. Aby upewnić się, że maszyna wirtualna laboratorium jest gotowa do użycia w zaplanowanych godzinach, zaleca się uruchomienie maszyn wirtualnych z 30-minutowym wyprzedzeniem.

   - [Ustawianie przydziałów dla użytkowników](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) i [ustawianie dodatkowego przydziału dla określonego użytkownika](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Włączanie automatycznego zamykania po rozłączeniu](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Harmonogramy, przydziały i automatyczne zamykanie nie mają zastosowania do szablonu maszyny Wirtualnej. W związku z tym należy upewnić się, że zostanie zamknięty szablon maszyny Wirtualnej, gdy nie jest używany. W przeciwnym razie nadal ponosi koszty. Ponadto domyślnie podczas tworzenia laboratorium szablon maszyny Wirtualnej jest uruchamiany automatycznie. Upewnij się, że natychmiast zakończysz konfigurowanie laboratorium i zamknij szablon maszyny Wirtualnej.

1. **Użyj pulpitu nawigacyjnego.** Aby uzyskać instrukcje, zobacz [korzystanie z pulpitu nawigacyjnego laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard).

    > [!NOTE]
    > Szacowany koszt wyświetlany na pulpicie nawigacyjnym to maksymalny koszt, jakiego można oczekiwać od uczniów podczas korzystania z laboratorium. Na przykład *nie* zostanie naliczona opłata za niewykorzystane godziny przydziału przez uczniów. Szacowane koszty *nie* odzwierciedlają żadnych opłat za korzystanie z szablonu maszyny Wirtualnej lub galerii obrazów udostępnionych.

## <a name="next-steps"></a>Następne kroki

- [Śledzenie użycia laboratorium na potrzeby zajęć](tutorial-track-usage.md)
  
- [Dostęp do laboratorium na potrzeby zajęć](tutorial-connect-virtual-machine-classroom-lab.md)