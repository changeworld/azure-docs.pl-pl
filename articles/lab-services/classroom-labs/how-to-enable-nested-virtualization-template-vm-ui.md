---
title: Włączanie wirtualizacji zagnieżdżonej na szablonie maszyny Wirtualnej w usłudze Azure Lab Services (UI) | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć szablon maszyny Wirtualnej z wieloma maszynami wirtualnymi wewnątrz.  Innymi słowy włącz zagnieżdżoną wirtualizację na szablonie maszyny Wirtualnej w usłudze Azure Lab Services.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 10f8d2760474631fbcabac4d66139aedf4c7560c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503077"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Ręczne włączanie wirtualizacji zagnieżdżonej na maszynie wirtualnej szablonu w usłudze Azure Lab Services

Wirtualizacja zagnieżdżona umożliwia utworzenie środowiska wielu maszyn wirtualnych wewnątrz maszyny wirtualnej szablonu laboratorium. Publikowanie szablonu zapewni każdemu użytkownikowi w laboratorium maszynę wirtualną skonfigurowaną z wieloma maszynami wirtualnymi w nim.  Aby uzyskać więcej informacji na temat wirtualizacji zagnieżdżonej i usług Azure Lab Services, zobacz [Włączanie wirtualizacji zagnieżdżonej na maszynie wirtualnej szablonu w usługach Azure Lab Services.](how-to-enable-nested-virtualization-template-vm.md)

W tym artykule opisano sposób konfigurowania zagnieżdżonej wirtualizacji na komputerze szablonu w usłudze Azure Lab Services przy użyciu ról i narzędzi systemu Windows bezpośrednio.  Istnieje kilka rzeczy potrzebnych do umożliwienia klasy do korzystania z zagnieżdżonej wirtualizacji.  W poniższych krokach opisano sposób ręcznego konfigurowania szablonu komputera usług laboratoryjnych z funkcji Hyper-V.  Kroki są przeznaczone dla systemu Windows Server 2016 lub Windows Server 2019.  

>[!IMPORTANT]
>Wybierz **opcję Duże (wirtualizacja zagnieżdżona)** lub **Średnia (wirtualizacja zagnieżdżona)** dla rozmiaru maszyny wirtualnej podczas tworzenia laboratorium.  Zagnieżdżona wirtualizacja nie będzie działać inaczej.  

## <a name="enable-hyper-v-role"></a>Włączanie roli funkcji funkcji Funkcji Hyper-V

W poniższych krokach opisano akcje potrzebne do włączenia funkcji Hyper-V w systemie Windows Server przy użyciu Menedżera serwera.  Po pomyślnym zakończeniu instalacji menedżer funkcji Hyper-V będzie dostępny do dodawania, modyfikowania i usuwania maszyn wirtualnych klienta.

1. W **Menedżerze serwera**na stronie Pulpit nawigacyjny kliknij pozycję **Dodaj role i funkcje**.
2. Na stronie **Zanim rozpoczniesz** kliknij przycisk **Dalej**.
3. Na stronie **Wybierz typ instalacji** zachowaj domyślny wybór instalacji opartej na rolach lub funkcjach, a następnie kliknij przycisk **Dalej**.
4. Na stronie **Wybierz serwer docelowy** wybierz pozycję Wybierz serwer z puli serwerów.   Bieżący serwer zostanie już wybrany.  Kliknij przycisk Dalej.
5. Na **Wybieranie ról serwera** Wybierz **funkcji Hyper-V**.  
6. Zostanie wyświetlone okno **podręczne Kreator dodawania ról i funkcji.**  Wybierz **pozycję Uwzględnij narzędzia do zarządzania (jeśli dotyczy)**.  Kliknij przycisk **Dodaj funkcje.**
7. Na stronie **Wybieranie ról serwera** kliknij przycisk **Dalej**.
8. Na **stronie Wybierz funkcje**kliknij przycisk **Dalej**.
9. Na stronie **Hyper-V** kliknij przycisk **Dalej**.
10. Na stronie **Tworzenie przełączników wirtualnych** zaakceptuj wartości domyślne i kliknij przycisk **Dalej**.
11. Na stronie **Migracja maszyn wirtualnych** zaakceptuj wartości domyślne i kliknij przycisk **Dalej**.
12. Na stronie **Sklepy domyślne** zaakceptuj wartości domyślne i kliknij przycisk **Dalej**.
13. Na stronie **Potwierdź wybór instalacji** wybierz opcję **Automatycznie uruchom ponownie serwer docelowy, jeśli jest to wymagane**.
14. Po wyświetleniu okna **podręcznego Kreatora dodawania ról i funkcji** kliknij przycisk **Tak**.
15. Kliknij **pozycję Zainstaluj**.
16. Poczekaj, aż strona **postępu instalacji** wskaże, że rola funkcji Hyper-V została ukończona.  Urządzenie może zostać ponownie uruchomione w trakcie instalacji.
17. Kliknij przycisk **Zamknij**.

## <a name="enable-dhcp-role"></a>Włączanie roli DHCP

Wszystkie maszyny wirtualne klienta funkcji Hyper-V utworzone, potrzebuje adresu IP w sieci NAT.  Utworzymy sieć NAT później.  Jednym ze sposobów przypisywania adresów IP jest skonfigurowanie hosta, w tym przypadku szablonu maszyny wirtualnej laboratorium, jako serwera DHCP.  Poniżej znajdują się kroki wymagane do włączenia roli DHCP.

1. W **Menedżerze serwera**na stronie **Pulpit nawigacyjny** kliknij pozycję Dodaj role **i funkcje**.
2. Na stronie **Zanim rozpoczniesz** kliknij przycisk **Dalej**.
3. Na **Wybieranie typu instalacji** Wybierz **Instalacja roli lub funkcji** a następnie kliknij przycisk **Dalej**.
4. Na stronie **Wybierz serwer docelowy** wybierz bieżący serwer z puli serwerów, a następnie kliknij przycisk **Dalej**.
5. Na stronie **Wybierz role serwera** wybierz pozycję Serwer **DHCP**.  
6. Zostanie wyświetlone okno **podręczne Kreator dodawania ról i funkcji.**  Wybierz **pozycję Uwzględnij narzędzia do zarządzania (jeśli dotyczy)**.  Kliknij przycisk **dodawania funkcji**.

    >[!NOTE]
    >Może zostać wyświetlony błąd sprawdzania poprawności informujący, że nie znaleziono statycznych adresów IP.  To ostrzeżenie może być ignorowane dla naszego scenariusza.

7. Na stronie **Wybieranie ról serwera** kliknij przycisk **Dalej**.
8. Na stronie **Wybierz funkcje** kliknij przycisk **Dalej**.
9. Na stronie **Serwer DHCP** kliknij przycisk **Dalej**.
10. Na stronie **Potwierdzanie opcji instalacji** kliknij pozycję **Zainstaluj**.
11. Poczekaj, aż **strona Postępu instalacji** wskaże, że rola DHCP została ukończona.
12. Kliknij przycisk Zamknij.

## <a name="enable-routing-and-remote-access-role"></a>Włącz rolę Routing i dostęp zdalny

1. W **Menedżerze serwera**na stronie **Pulpit nawigacyjny** kliknij pozycję Dodaj role **i funkcje**.
2. Na stronie **Zanim rozpoczniesz** kliknij przycisk **Dalej**.
3. Na **Wybieranie typu instalacji** Wybierz **Instalacja roli lub funkcji** a następnie kliknij przycisk **Dalej**.
4. Na stronie **Wybierz serwer docelowy** wybierz bieżący serwer z puli serwerów, a następnie kliknij przycisk **Dalej**.
5. Na stronie **Wybierz role serwera** wybierz pozycję Dostęp **zdalny**. Kliknij przycisk **OK**.
6. Na stronie **Wybierz funkcje** kliknij przycisk **Dalej**.
7. Na stronie **Dostęp zdalny** kliknij przycisk **Dalej**.
8. Na stronie **Usługi ról** wybierz pozycję **Routing**.
9. Zostanie wyświetlone okno **podręczne Kreator dodawania ról i funkcji.**  Wybierz **pozycję Uwzględnij narzędzia do zarządzania (jeśli dotyczy)**.  Kliknij przycisk **dodawania funkcji**.
10. Kliknij przycisk **alej**.
11. Na stronie **Rola serwera sieci Web (IIS)** kliknij przycisk **Dalej**.
12. Na ekranie **Wybieranie usług ról** kliknij przycisk **Dalej**.
13. Na stronie **Potwierdzanie opcji instalacji** kliknij pozycję **Zainstaluj**.
14. Poczekaj, aż strona **Postępu instalacji** wskaże, że rola dostępu zdalnego została ukończona.  
15. Kliknij przycisk **Zamknij**.

## <a name="create-virtual-nat-network"></a>Tworzenie wirtualnej sieci NAT

Teraz, gdy wszystkie niezbędne role zostały zainstalowane, nadszedł czas, aby utworzyć sieć NAT.  Proces tworzenia będzie polegał na utworzeniu przełącznika i samej sieci NAT.  Sieć TRANSLATORA adresów sieciowych (translacja adresów sieciowych) przypisuje publiczny adres IP do grupy maszyn wirtualnych w sieci prywatnej, aby umożliwić łączność z Internetem.  W naszym przypadku grupa prywatnych maszyn wirtualnych będzie zagnieżdżonych maszyn wirtualnych.  Sieć NAT umożliwi zagnieżdżone maszyny wirtualne komunikować się ze sobą. Przełącznik to urządzenie sieciowe obsługujące odbieranie i routing ruchu w sieci.

### <a name="create-a-new-virtual-switch"></a>Tworzenie nowego przełącznika wirtualnego

1. Otwórz **Menedżera funkcji Hyper-V** z narzędzi administracyjnych systemu Windows.
2. Wybierz bieżący serwer w menu nawigacji po lewej stronie.
3. Kliknij **wirtualny menedżer przełączników...** z menu **Akcje** po prawej stronie Menedżera **funkcji Hyper-V**.
4. W wyskakującym okienku **Menedżer przełączników wirtualnych** wybierz pozycję **Wewnętrzny** dla typu przełącznika do utworzenia.  Kliknij **pozycję Utwórz przełącznik wirtualny**.
5. W przypadku nowo utworzonego przełącznika wirtualnego ustaw nazwę na coś niezapomnianego.  W tym przykładzie użyjemy "LabServicesSwitch".  Kliknij przycisk **OK**.
6. Zostanie utworzona nowa karta sieciowa.  Nazwa będzie podobna do "vEthernet (LabServicesSwitch)".  Aby zweryfikować otwarcie **Panelu sterowania**, kliknij pozycję Sieć **i Internet**, kliknij pozycję Wyświetl stan sieci **i zadania**.  Po lewej stronie kliknij pozycję **Zmień ustawienia karty**.

### <a name="create-a-nat-network"></a>Tworzenie sieci NAT

1. Otwórz narzędzie **Routing i dostęp zdalny** w menu Narzędzia administracyjne systemu Windows.
2. Wybierz serwer lokalny na lewej stronie nawigacji.
3. Wybierz **pozycję Akcja** -> **Konfiguruj i włącz routing i dostęp zdalny**.
4. Po **wyświetleniu Kreatora instalacji serwera routingu i dostępu zdalnego** kliknij przycisk **Dalej**.
5. Na stronie **Konfiguracja** wybierz pozycję **Konfiguracja translacji adresów sieciowych (NAT).**  Kliknij przycisk **alej**.

    >[!WARNING]
    >Nie należy wybierać opcji "Dostęp do wirtualnej sieci prywatnej (VPN) i NAT".

6. Na stronie **Nat Internet Connection** wybierz "Ethernet".  Nie wybieraj połączenia "vEthernet (LabServicesSwitch)", które utworzyliśmy w Menedżerze funkcji Hyper-V. Kliknij przycisk **alej**.
7. Kliknij **pozycję Zakończ** na ostatniej stronie kreatora.
8. Po **wyświetleniu okna dialogowego Rozpocznij usługę** kliknij przycisk **Uruchom usługę**.
9. Poczekaj, aż usługa zostanie uruchomiona.

## <a name="update-network-adapter-settings"></a>Aktualizowanie ustawień karty sieciowej

Karta sieciowa będzie skojarzona z adresem IP używanym dla domyślnego adresu IP bramy dla sieci NAT utworzonej wcześniej.  W tym przykładzie tworzymy adres IP 192.168.0.1 z maską podsieci 255.255.255.0.  Użyjemy wirtualnego przełącznika utworzonego wcześniej.

1. Otwórz **Panel sterowania**, kliknij pozycję **Sieć i Internet**, kliknij pozycję Wyświetl stan sieci i **zadania**.
2. Po lewej stronie kliknij pozycję **Zmień ustawienia karty**.  
3. W oknie **Połączenia sieciowe** kliknij dwukrotnie przycisk 'vEthernet (LabServicesSwitch)', aby wyświetlić okno dialogowe **szczegółów stanu vEthernet (LabServicesSwitch).**
4. Kliknij przycisk **Właściwości.**
5. Wybierz pozycję **Protokół internetowy w wersji 4 (TCP/IPv4)** i kliknij przycisk **Właściwości.**
6. W oknie dialogowym **Właściwości protokołu internetowego w wersji 4 (TCP/IPv4)** wybierz pozycję **Użyj następującego adresu IP**.  W przypadku adresu IP wpisz 192.168.0.1. W przypadku maski podsieci wprowadź 255.255.255.0.  Pozostaw bramę domyślną pustą.  Pozostaw również puste serwery DNS.

    >[!NOTE]
    > Nasza oferta dla naszej sieci NAT będzie, w notacji CIDR, 192.168.0.0/24.  Spowoduje to utworzenie zakresu użytecznych adresów IP od 192.168.0.1 do 192.168.0.254.  Zgodnie z konwencją bramy mają pierwszy adres IP w zakresie podsieci.

7. Kliknij przycisk OK.

## <a name="create-dhcp-scope"></a>Tworzenie zakresu DHCP

Poniższe kroki to instrukcje dodawania zakresu DHCP.  W tym artykule nasza sieć NAT jest 192.168.0.0/24 w notacji CIDR.  Spowoduje to utworzenie zakresu użytecznych adresów IP od 192.168.0.1 do 192.168.0.254.  Utworzony zakres musi znajdować się w tym zakresie użytecznych adresów, z wyłączeniem adresu IP utworzonego wcześniej.

1. Otwórz **narzędzia administracyjne** i otwórz narzędzie administracyjne **DHCP.**
2. W narzędziu **DHCP** rozwiń węzeł dla bieżącego serwera i wybierz pozycję **IPv4**.
3. Z menu Akcja wybierz polecenie **Nowy zakres...**
4. Po wyświetleniu **Kreatora nowego zakresu** kliknij przycisk **Dalej** na stronie **powitalnej.**
5. Na stronie **Nazwa zakresu** wprowadź "LabServicesDhcpScope" lub coś innego niezapomniany dla nazwy.  Kliknij przycisk **alej**.
6. Na stronie **Zakres adresów IP** wprowadź następujące wartości.

    - 192.168.0.100 dla początkowego adresu IP
    - 192.168.0.200 dla końcowego adresu IP
    - 24 dla długości
    - 255.255.255.0 dla maski podsieci

7. Kliknij przycisk **alej**.
8. Na stronie **Dodawanie wykluczeń i opóźnień** kliknij przycisk **Dalej**.
9. Na stronie **Czas trwania dzierżawy** kliknij przycisk **Dalej**.
10. Na stronie **Konfigurowanie opcji DHCP** wybierz pozycję **Tak, chcę teraz skonfigurować te opcje**. Kliknij przycisk **alej**.
11. Na **routerze (brama domyślna)**
12. Dodaj 192.168.0.1, jeśli nie zostało już zrobione. Kliknij przycisk **alej**.
13. Na stronie **Domain Name and DNS Servers (Nazwa domeny i serwery DNS)** dodaj 168.63.129.16 jako adres IP serwera DNS, jeśli nie zostało to już zrobione.  168.63.129.16 to adres IP statycznego serwera DNS platformy Azure. Kliknij przycisk **alej**.
14. Na stronie **Serwery WINS** kliknij przycisk **Dalej**.
15. Jedna strona **Aktywuj zakres,** wybierz **tak, chcę teraz aktywować ten zakres**.  Kliknij przycisk **alej**.
16. Na stronie **Kończenie Kreatora nowego zakresu** kliknij pozycję **Zakończ**.

## <a name="conclusion"></a>Podsumowanie

Teraz maszyna szablonu jest gotowa do tworzenia maszyn wirtualnych funkcji Hyper-V.   Zobacz [Tworzenie maszyny wirtualnej w funkcji Hyper-V,](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) aby uzyskać instrukcje dotyczące tworzenia maszyn wirtualnych funkcji Hyper-V.  Zobacz też [Centrum oceny firmy Microsoft,](https://www.microsoft.com/evalcenter/) aby sprawdzić dostępne systemy operacyjne i oprogramowanie.

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne dla konfigurowania dowolnego laboratorium.

- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustawianie przydziału](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji wiadomości e-mail do studentów](how-to-configure-student-usage.md#send-invitations-to-users)