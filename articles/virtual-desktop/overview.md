---
title: Co to jest Windows wirtualnego pulpitu (wersja zapoznawcza)?  — Azure
description: Przegląd Windows wirtualnego Desktop w wersji zapoznawczej.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0d606a489a069c4265088d8e07301693dc2f1c83
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694271"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>Co to jest Windows wirtualnego pulpitu (wersja zapoznawcza)? 

Windows wirtualnego pulpitu (wersja zapoznawcza), teraz dostępna w publicznej wersji zapoznawczej, jest usługa wirtualizacji pulpitu i aplikacji, która działa w chmurze.

Oto, co można zrobić podczas uruchamiania pulpitu wirtualnego Windows na platformie Azure:

* Konfigurowanie wielu sesji wdrożenia systemu Windows 10, który zapewnia pełną systemu Windows 10 ze skalowalnością
* Wirtualizacja usługi Office 365 ProPlus i Optymalizuj pod kątem uruchamiania w scenariuszach wirtualnych wielu użytkowników
* Udostępnianie pulpitów wirtualnych Windows 7 przy użyciu bezpłatnego rozszerzone aktualizacje zabezpieczeń
* Przenieść istniejących usług pulpitu zdalnego (RDS) i komputerów stacjonarnych z systemem Windows Server oraz aplikacje na dowolnym komputerze
* Wirtualizacja zarówno pulpity nawigacyjne i aplikacje
* Zarządzania komputerami stacjonarnymi z systemem Windows 10, Windows Server i Windows 7 i aplikacje ze środowiskiem ujednolicone zarządzanie

## <a name="key-capabilities"></a>Najważniejsze możliwości

Za pomocą Windows pulpitu wirtualnego można skonfigurować skalowalne i elastyczne środowisko:

* Utwórz środowisko wirtualizacji całego pulpitu w subskrypcji platformy Azure bez konieczności uruchamiania dowolne serwery bramy dodatkowe.
* Opublikuj jako hosta wielu pul, potrzebnych do obsługi różnych obciążeń.
* Możliwość skorzystania z własnego obrazu dla obciążeń produkcyjnych lub testowania w galerii platformy Azure.
* Zmniejsz koszty przy użyciu puli "," sesja wielu zasobów. Za pomocą nowego systemu Windows 10 Enterprise wielu sesji funkcji wyłącznie w roli pulpitu wirtualnego Windows i hosta sesji usług pulpitu zdalnego (RDSH) w systemie Windows Server można znacznie zmniejszyć liczbę maszyn wirtualnych i systemu operacyjnego (OS) obciążenie mając nadal udostępnianie tych samych zasobów dla użytkowników.
* Podaj poszczególnych własność przy użyciu osobistych pulpitów (stałe).

Można wdrażać i zarządzać pulpitami wirtualnymi:

* Można skonfigurować pule hosta, tworzenie grupy aplikacji, przypisywanie użytkowników i publikowania zasobów, należy użyć programu PowerShell pulpitu wirtualnego Windows i REST interfejsów.
* Publikowanie pełnej pulpitu lub poszczególne aplikacje zdalne z puli jednego hosta, tworzenie grup poszczególnych aplikacji dla różnych zestawów użytkowników lub nawet przypisać użytkowników do wielu grup aplikacji w celu zmniejszenia liczby obrazów.
* W przypadku zarządzania środowiska, umożliwia delegowany dostęp do wbudowanych przypisywanie ról i zbieranie danych diagnostycznych, aby poznać różne błędy konfiguracji lub użytkownika.
* Nowa usługa diagnostyki umożliwia rozwiązywanie problemów z błędami.
* Tylko obraz i zarządzać maszyn wirtualnych, a nie na infrastrukturze. Nie trzeba osobiście zarządzać ról usług pulpitu zdalnego, jak za pomocą usług pulpitu zdalnego, po prostu maszyn wirtualnych w subskrypcji platformy Azure.

Można również przypisać i łączenia użytkowników z pulpitami wirtualnymi:

* Po przypisaniu, użytkownicy mogą uruchamiać dowolnego klienta pulpitu wirtualnego Windows do łączenia użytkowników z ich opublikowanych Windows pulpitów i aplikacji. Nawiąż połączenie z dowolnym urządzeniu za pośrednictwem natywnych aplikacji na urządzeniu lub klienta sieci web Windows wirtualnego pulpitu w języku HTML5.
* Bezpieczne nawiązywanie otwarte użytkowników za pomocą odwrotnego połączenia z usługą, dzięki czemu nigdy nie trzeba pozostaw żadnych portów przychodzących.

## <a name="requirements"></a>Wymagania

Istnieje kilka rzeczy, które należy skonfigurować pulpitu wirtualnego Windows i pomyślnym nawiązaniu połączenia użytkownicy na swoich komputerach stacjonarnych Windows i aplikacji.

Obecnie pulpit wirtualny w Windows obsługuje tylko następujące dwa systemy operacyjne:

* Wiele sesji systemu Windows 10 Enterprise
* Windows Server 2016

Planujemy dodanie obsługi następujących systemów operacyjnych, dlatego upewnij się, że masz [odpowiednie licencje](https://azure.microsoft.com/pricing/details/virtual-desktop/) dla użytkowników na podstawie pulpitu i aplikacje, które planujesz wdrożyć:

|System operacyjny|Wymagana licencja|
|---|---|
|Wiele sesji systemu Windows 10 Enterprise lub Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016 r. 2019 r|Licencja dostępu klienta usług pulpitu zdalnego (CAL) z pakietem Software Assurance|

Infrastruktury musi mieć następujące elementy do obsługi Windows pulpitu wirtualnego:

* An [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* Windows Server Active Directory w synchronizacji z usługą Azure Active Directory. Tę można włączyć za pomocą:
  * Azure AD Connect
  * Azure AD Domain Services
* Subskrypcji platformy Azure zawierająca sieć wirtualną, która zawiera lub jest połączony z usługą Windows Server Active Directory
  
Maszyny wirtualne Azure utworzonym dla Windows pulpitu wirtualnego musi być:

* [Standardowa przyłączone do domeny](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) lub [hybrydowe przyłączone do usługi AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Maszyny wirtualne nie może być przyłączone do usługi AD platformy Azure.
* Uruchomiony jeden z następujących obsługiwanych obrazów systemu operacyjnego:
  * Wiele sesji systemu Windows 10 Enterprise
  * Windows Server 2016

>[!NOTE]
>Jeśli potrzebujesz subskrypcji platformy Azure, możesz to zrobić [Zarejestruj się w miesięcznej bezpłatnej wersji próbnej](https://azure.microsoft.com/free/). Jeśli używasz bezpłatnej wersji próbnej platformy Azure, należy używać usługi Azure AD Domain Services do synchronizowania usługi systemu Windows Server Active Directory z usługą Azure Active Directory.

Pulpit wirtualny Windows składa się z pulpitami Windows i aplikacje, które można dostarczać do użytkowników i rozwiązania do zarządzania, który jest hostowany jako usługa na platformie Azure przez firmę Microsoft. W publicznej wersji zapoznawczej pulpity nawigacyjne i aplikacje można wdrożyć na maszynach wirtualnych (VM) w dowolnym regionie systemu Azure, a rozwiązaniem do zarządzania i dane dla tych maszyn wirtualnych będą znajdować się w Stanach Zjednoczonych (region wschodnie stany USA 2). Może to spowodować przesyłanie danych do Stanów Zjednoczonych podczas testowania tej usługi w ramach publicznej wersji zapoznawczej. Zaczniemy do skalowania w poziomie lokalizacji danych i rozwiązań zarządzania do wszystkich regionów platformy Azure, począwszy od ogólnej dostępności.

Aby uzyskać optymalną wydajność upewnij się, że sieć spełnia następujące wymagania:

* Opóźnienie Rundy (RTT) z sieci klienta do regionu platformy Azure, w której zostały wdrożone pule hosta powinien być mniej niż 150 ms.
* Ruch sieciowy może przepływać zewnętrzne obramowania kraju, gdy maszyny wirtualne, które hostują pulpity nawigacyjne i aplikacje, które łączą się z usługą zarządzania z.
* Aby zoptymalizować wydajność sieci, zaleca się, że maszyny wirtualne hosta sesji jest zlokalizowana w tym samym regionie platformy Azure jako usługi zarządzania.

## <a name="provide-feedback"></a>Przekazywanie opinii

Odwiedź stronę [społeczności technicznej pulpitu wirtualnego Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) w celu omówienia usługi Windows pulpitu wirtualnego przy użyciu zespół pracujący nad produktem i elementów członkowskich aktywnej społeczności użytkowników. Firma Microsoft obecnie nie są zbyt przypadki pomocy technicznej Windows pulpitu wirtualnego jest dostępna w wersji zapoznawczej.

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę, należy utworzyć dzierżawę. Aby dowiedzieć się więcej o tym, jak utworzyć dzierżawę, przejdź do samouczka tworzenia dzierżawy.

> [!div class="nextstepaction"]
> [Tworzenie dzierżawy w wersji zapoznawczej pulpitu wirtualnego Windows](tenant-setup-azure-active-directory.md)
