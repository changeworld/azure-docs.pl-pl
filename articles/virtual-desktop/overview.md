---
title: Co to jest wersja zapoznawcza pulpitu wirtualnego systemu Windows?  — Azure
description: Omówienie wersji zapoznawczej pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 08/07/2019
ms.author: helohr
ms.openlocfilehash: 8827aa660d348ef9e42c617067c2837b8697ba6e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876775"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>Co to jest wersja zapoznawcza pulpitu wirtualnego systemu Windows? 

Teraz dostępne w publicznej wersji zapoznawczej system Windows Virtual Desktop Preview to usługa wirtualizacji pulpitu i aplikacji działająca w chmurze.

Oto co można zrobić w przypadku uruchamiania pulpitu wirtualnego systemu Windows na platformie Azure:

* Konfigurowanie wdrożenia obejmującego wiele sesji systemu Windows 10, które zapewnia pełną skalowalność systemu Windows 10
* Wirtualizacja pakietu Office 365 ProPlus i optymalizacja do działania w scenariuszach wirtualnych obejmujących wiele użytkowników
* Udostępnianie pulpitów wirtualnych systemu Windows 7 z bezpłatnymi rozszerzonymi aktualizacjami zabezpieczeń
* Przenoszenie istniejących Usługi pulpitu zdalnego (RDS) i komputerów stacjonarnych i aplikacji z systemem Windows Server do dowolnego komputera
* Wirtualizacja zarówno komputerów stacjonarnych, jak i aplikacji
* Zarządzanie komputerami stacjonarnymi i aplikacjami z systemem Windows 10, Windows Server i Windows 7 za pomocą ujednoliconego środowiska zarządzania

## <a name="introductory-video"></a>Wstępne wideo

Dowiedz się więcej o usłudze Windows Virtual Desktop, dlaczego jest ona unikatowa i co nowego w tym filmie wideo:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Aby uzyskać więcej filmów wideo dotyczących pulpitu wirtualnego systemu Windows, zobacz [nasze listy odtwarzania](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>Najważniejsze możliwości

Za pomocą pulpitu wirtualnego systemu Windows można skonfigurować skalowalne i elastyczne środowisko:

* Utwórz pełne środowisko wirtualizacji pulpitu w ramach subskrypcji platformy Azure bez konieczności uruchamiania dodatkowych serwerów bramy.
* Publikuj tyle pul hostów, ile potrzebujesz do obsługi różnych obciążeń.
* Korzystaj z własnego obrazu dla obciążeń produkcyjnych lub testów z galerii platformy Azure.
* Obniż koszty dzięki zasobom w puli, wielu sesjach. Dzięki nowej funkcji wielosesyjnej systemu Windows 10 Enterprise na wyłączność dla pulpitu wirtualnego systemu Windows i roli hosta sesji Pulpit zdalny w systemie Windows Server można znacznie zmniejszyć liczbę maszyn wirtualnych i nakładów pracy systemu operacyjnego, jednocześnie udostępnianie tych samych zasobów użytkownikom.
* Udostępnianie poszczególnych własności za poorednictwem osobistych (trwałych) komputerów stacjonarnych.

Pulpity wirtualne można wdrażać i zarządzać nimi:

* Za pomocą programu PowerShell i interfejsów REST systemu Windows można skonfigurować pule hostów, tworzyć grupy aplikacji, przypisywać użytkowników i publikować zasoby.
* Publikowanie pełnych pulpitów lub pojedynczych aplikacji zdalnych z jednej puli hostów, tworzenie pojedynczych grup aplikacji dla różnych zestawów użytkowników, a nawet przypisywanie użytkowników do wielu grup aplikacji w celu zmniejszenia liczby obrazów.
* Podczas zarządzania środowiskiem użyj wbudowanego delegowanego dostępu, aby przypisać role i zbierać diagnostykę, aby poznać różne konfiguracje lub błędy użytkowników.
* Użyj nowej usługi diagnostycznej do rozwiązywania problemów z błędami.
* Zarządzaj obrazami i maszynami wirtualnymi, a nie z infrastrukturą. Nie musisz zarządzać rolami Pulpit zdalny, takimi jak w przypadku Usługi pulpitu zdalnego, tylko maszyn wirtualnych w ramach subskrypcji platformy Azure.

Możesz również przypisywać użytkowników i łączyć ich z pulpitami wirtualnymi:

* Po przypisaniu użytkownicy mogą uruchomić dowolnego klienta pulpitu wirtualnego systemu Windows, aby połączyć użytkowników z opublikowanymi pulpitami i aplikacjami systemu Windows. Połącz się z dowolnego urządzenia za pośrednictwem aplikacji natywnej na urządzeniu lub klienta sieci Web HTML5 programu Windows Virtual Desktop.
* Bezpiecznie ustanawiaj użytkowników przez połączenia odwrotne do usługi, dzięki czemu nie musisz opuszczać żadnych otwartych portów przychodzących.

## <a name="requirements"></a>Wymagania

Istnieje kilka rzeczy potrzebnych do skonfigurowania pulpitu wirtualnego systemu Windows i pomyślnego połączenia użytkowników z komputerami stacjonarnymi i aplikacjami z systemem Windows.

Planujemy dodanie obsługi następujących systemów operacyjnych, więc upewnij się, że masz [odpowiednie licencje](https://azure.microsoft.com/pricing/details/virtual-desktop/) dla użytkowników na podstawie pulpitu i aplikacji, które planujesz wdrożyć:

|OS|Wymagana licencja|
|---|---|
|Wiele sesji systemu Windows 10 Enterprise lub Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Licencja dostępu klienta usług pulpitu zdalnego z programem Software Assurance|

Aby zapewnić obsługę pulpitu wirtualnego systemu Windows, Twoja infrastruktura wymaga następujących czynności:

* [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* Active Directory systemu Windows Server synchronizacja z Azure Active Directory. Można to zrobić w następujący sposób:
  * Azure AD Connect
  * Azure AD Domain Services
* Subskrypcja platformy Azure zawierająca sieć wirtualną, która zawiera lub jest połączona z systemem Windows Server Active Directory
  
Maszyny wirtualne platformy Azure tworzone dla pulpitu wirtualnego systemu Windows muszą być:

* [Standardowe przyłączone do domeny](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) lub [hybrydowe usługi AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Maszyny wirtualne nie mogą być przyłączone do usługi Azure AD.
* Uruchamianie jednego z następujących [obsługiwanych obrazów systemu operacyjnego](#supported-virtual-machine-os-images).

>[!NOTE]
>Jeśli potrzebujesz subskrypcji platformy Azure, możesz [zarejestrować się w celu korzystania z miesięcznej bezpłatnej wersji próbnej](https://azure.microsoft.com/free/). W przypadku korzystania z bezpłatnej wersji próbnej platformy Azure należy używać Azure AD Domain Services, aby zapewnić synchronizację Active Directory systemu Windows Server z Azure Active Directory.

Pulpit wirtualny systemu Windows składa się z komputerów stacjonarnych i aplikacji z systemem Windows dostarczanych użytkownikom i rozwiązanie do zarządzania, które jest hostowane jako usługa na platformie Azure przez firmę Microsoft. W publicznej wersji zapoznawczej pulpity i aplikacje można wdrażać na maszynach wirtualnych w dowolnym regionie świadczenia usługi Azure, a rozwiązanie do zarządzania i dane dla tych maszyn wirtualnych będą znajdować się w Stany Zjednoczone (region Wschodnie stany USA 2). Może to spowodować transfer danych do Stany Zjednoczone podczas testowania usługi w ramach publicznej wersji zapoznawczej. Rozpocznie się skalowanie rozwiązania do zarządzania i lokalizacji danych do wszystkich regionów świadczenia usługi Azure, począwszy od ogólnej dostępności.

W celu uzyskania optymalnej wydajności upewnij się, że sieć spełnia następujące wymagania:

* Opóźnienie rundy (RTT) od sieci klienta do regionu platformy Azure, w którym wdrożono pule hostów, powinno być mniejsze niż 150 MS.
* Ruch sieciowy może przepływać poza granicami kraju/regionu, gdy maszyny wirtualne, które obsługują komputery stacjonarne i aplikacje, łączą się z usługą zarządzania.
* Aby zoptymalizować pod kątem wydajności sieci, zalecamy zapoznanie się z maszynami wirtualnymi hosta sesji w tym samym regionie platformy Azure co usługa zarządzania.

## <a name="supported-remote-desktop-clients"></a>Obsługiwani klienci Pulpit zdalny

Następujący klienci Pulpit zdalny obsługują pulpit wirtualny systemu Windows:

* [Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-windows-7-and-10)
* [HTML5](https://docs.microsoft.com/azure/virtual-desktop/connect-web)

## <a name="supported-virtual-machine-os-images"></a>Obsługiwane obrazy systemu operacyjnego maszyny wirtualnej

Pulpit wirtualny systemu Windows obsługuje następujące obrazy systemu operacyjnego:

* Wiele sesji systemu Windows 10 Enterprise
* Windows Server 2016

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, musisz utworzyć dzierżawę. Aby dowiedzieć się więcej na temat tworzenia dzierżawy, przejdź do samouczka dotyczącego tworzenia dzierżawy.

> [!div class="nextstepaction"]
> [Tworzenie dzierżawy w wersji zapoznawczej pulpitu wirtualnego systemu Windows](tenant-setup-azure-active-directory.md)
