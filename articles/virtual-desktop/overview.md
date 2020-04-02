---
title: Co to jest Windows Virtual Desktop? — Azure
description: Omówienie pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 01767e88714bfb4e134957298505edd218d462d3
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546923"
---
# <a name="what-is-windows-virtual-desktop"></a>Co to jest Windows Virtual Desktop? 

Pulpit wirtualny systemu Windows to usługa wirtualizacji pulpitu i aplikacji, która działa w chmurze.

Oto, co możesz zrobić po uruchomieniu pulpitu wirtualnego systemu Windows na platformie Azure:

* Konfigurowanie wielosesyjnego wdrożenia systemu Windows 10, które zapewnia pełny system Windows 10 ze skalowalnością
* Zwirtualizuj office 365 ProPlus i optymalizuj ją tak, aby działała w wirtualnych scenariuszach dla wielu użytkowników
* Zapewnienie pulpitom wirtualnym systemu Windows 7 bezpłatnych rozszerzonych aktualizacji zabezpieczeń
* Przenieś istniejące usługi pulpitu zdalnego (RDS) oraz pulpity i aplikacje systemu Windows Server na dowolnym komputerze
* Wirtualizuj zarówno pulpity, jak i aplikacje
* Zarządzanie pulpitami i aplikacjami dla systemów Windows 10, Windows Server i Windows 7 dzięki ujednoliconemu interfejsowi zarządzania

## <a name="introductory-video"></a>Film wprowadzający

Dowiedz się więcej o pulpicie wirtualnym systemu Windows, dlaczego jest unikatowy i co nowego w tym filmie:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Aby uzyskać więcej filmów o Windows Virtual Desktop, zobacz [naszą listę odtwarzania](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>Najważniejsze możliwości

Desktop wirtualny systemu Windows umożliwia skonfigurowanie skalowalnego i elastycznego środowiska:

* Utwórz pełne środowisko wirtualizacji pulpitu w ramach subskrypcji platformy Azure bez konieczności uruchamiania dodatkowych serwerów bramy.
* Publikowanie jak najwięcej pul hostów, jak trzeba, aby pomieścić różnych obciążeń.
* Przynieś własny obraz dla obciążeń produkcyjnych lub testuj z galerii Azure.
* Zmniejsz koszty dzięki łączonych zasobom wielosesyjnym. Dzięki nowej funkcji wielu sesji systemu Windows 10 Enterprise, która jest dostępna wyłącznie w przypadku pulpitu wirtualnego systemu Windows i hosta sesji pulpitu zdalnego (RDSH) w systemie Windows Server, można znacznie zmniejszyć liczbę narzutów na maszyny wirtualne i system operacyjny, jednocześnie zapewniając użytkownikom te same zasoby.
* Zapewnij indywidualną własność za pośrednictwem osobistych (trwałych) komputerów stacjonarnych.

Pulpity wirtualne można wdrażać i zarządzać nimi:

* Interfejsy programu Windows Virtual Desktop PowerShell i REST umożliwia konfigurowanie pul hostów, tworzenie grup aplikacji, przypisywanie użytkowników i publikowanie zasobów.
* Publikuj pełne aplikacje stacjonarne lub pojedyncze aplikacje zdalne z jednej puli hostów, twórz poszczególne grupy aplikacji dla różnych grup użytkowników, a nawet przypisz użytkowników do wielu grup aplikacji, aby zmniejszyć liczbę obrazów.
* Podczas zarządzania środowiskiem należy używać wbudowanego delegowanego dostępu do przypisywania ról i zbierania diagnostyki w celu zrozumienia różnych błędów konfiguracji lub użytkownika.
* Użyj nowej usługi diagnostyki, aby rozwiązywać problemy z błędami.
* Zarządzaj tylko obrazem i maszynami wirtualnymi, a nie infrastrukturą. Nie musisz osobiście zarządzać rolami pulpitu zdalnego, tak jak w przypadku usług pulpitu zdalnego, tylko maszynami wirtualnymi w subskrypcji platformy Azure.

Można również przypisać i połączyć użytkowników z pulpitami wirtualnymi:

* Po przypisaniu użytkownicy mogą uruchomić dowolnego klienta pulpitu wirtualnego systemu Windows, aby połączyć użytkowników z opublikowanymi pulpitami i aplikacjami systemu Windows. Połącz się z dowolnego urządzenia za pośrednictwem aplikacji natywnej na urządzeniu lub klienta sieci Web HTML5 pulpitu wirtualnego systemu Windows.
* Bezpiecznie ustanawiaj użytkowników za pośrednictwem odwrotnych połączeń z usługą, dzięki czemu nigdy nie trzeba pozostawiać otwartych portów przychodzących.

## <a name="requirements"></a>Wymagania

Istnieje kilka rzeczy, które należy skonfigurować pulpit wirtualny systemu Windows i pomyślnie połączyć użytkowników z ich pulpitów systemu Windows i aplikacji.

Planujemy dodać obsługę następujących os, więc upewnij się, że masz [odpowiednie licencje](https://azure.microsoft.com/pricing/details/virtual-desktop/) dla użytkowników na podstawie pulpitu i aplikacji, które zamierzasz wdrożyć:

|System operacyjny|Wymagana licencja|
|---|---|
|Windows 10 Enterprise multi-session lub Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Biznes<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Biznes<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Licencja cal (RDS Client Access License) z pakietem Software Assurance|

Infrastruktura wymaga następujących elementów do obsługi pulpitu wirtualnego systemu Windows:

* Usługa [Azure Active Directory](/azure/active-directory/)
* Usługa Active Directory systemu Windows Server jest synchronizowana z usługą Azure Active Directory. Można to skonfigurować za pomocą jednej z następujących czynności:
  * Usługa Azure AD Connect (dla organizacji hybrydowych)
  * Usługi domenowe usługi Azure AD (dla organizacji hybrydowych lub chmurowych)
* Subskrypcja platformy Azure zawierająca sieć wirtualną zawierającą lub połączoną z usługą Active Directory Windows Server
  
Maszyny wirtualne platformy Azure utworzone dla pulpitu wirtualnego systemu Windows muszą być:

* [Standardowa przyłączona do domeny](../active-directory-domain-services/active-directory-ds-comparison.md) lub [hybrydowa przyłączona do usługi AD](../active-directory/devices/hybrid-azuread-join-plan.md). Maszyny wirtualne nie mogą być przyłączone do usługi Azure AD.
* Uruchamianie jednego z następujących [obsługiwanych obrazów systemu operacyjnego](#supported-virtual-machine-os-images).

>[!NOTE]
>Jeśli potrzebujesz subskrypcji platformy Azure, możesz [zarejestrować się w celu uzyskania miesięcznej bezpłatnej wersji próbnej.](https://azure.microsoft.com/free/) Jeśli używasz bezpłatnej wersji próbnej platformy Azure, należy użyć usług domenowych usługi Azure AD, aby zachować synchronizację usługi Active Directory systemu Windows Server z usługą Azure Active Directory.

Maszyny wirtualne platformy Azure utworzone dla pulpitu wirtualnego systemu Windows muszą mieć dostęp do następujących adresów URL:

|Adres|Wychodzący port TCP|Przeznaczenie|Tag usługi|
|---|---|---|---|
|*.wvd.microsoft.com|443|Ruch serwisowy|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Aktualizacje stosu agenta i SXS|AzureCloud|
|*.core.windows.net|443|Ruch agenta|AzureCloud|
|*.servicebus.windows.net|443|Ruch agenta|AzureCloud|
|prod.warmpath.msftcloudes.com|443|Ruch agenta|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Aktywacja systemu Windows|Internet|



>[!IMPORTANT]
>Otwarcie tych adresów URL jest niezbędne do niezawodnego wdrożenia pulpitu wirtualnego systemu Windows. Zablokowanie dostępu do tych adresów URL nie jest obsługiwane i wpłynie na funkcjonalność usługi. Te adresy URL odpowiadają tylko witrynom i zasobom pulpitu wirtualnego systemu Windows i nie zawierają adresów URL innych usług, takich jak usługa Azure Active Directory.

W poniższej tabeli wymieniono opcjonalne adresy URL, do których maszyny wirtualne platformy Azure mogą mieć dostęp:

|Adres|Wychodzący port TCP|Przeznaczenie|Tag usługi|
|---|---|---|---|
|*.microsoftonline.com|443|Uwierzytelnianie w usługach MS Online|Brak|
|*.events.data.microsoft.com|443|Usługa telemetrii|Brak|
|www.msftconnecttest.com|443|Wykrywa, czy system operacyjny jest podłączony do Internetu|Brak|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Brak|
|login.windows.net|443|Logowanie do usług MS Online Services, Office 365|Brak|
|*.sfx.ms|443|Aktualizacje oprogramowania klienckiego usługi OneDrive|Brak|
|*.digicert.com|443|Sprawdzanie odwołania certyfikatu|Brak|


>[!NOTE]
>Pulpit wirtualny systemu Windows nie ma obecnie listy zakresów adresów IP, które można umieszczać na białej liście, aby zezwolić na ruch sieciowy. Obsługujemy tylko białe listy określonych adresów URL w tej chwili.
>
>Aby uzyskać listę adresów URL związanych z pakietem Office, w tym wymagane adresy URL związane z usługą Azure Active Directory, zobacz [Adresy URL i zakresy adresów IP usługi Office 365](/office365/enterprise/urls-and-ip-address-ranges).
>
>W przypadku adresów URL dotyczących ruchu usługowego należy użyć symbolu wieloznacznego (*). Jeśli wolisz nie używać * dla ruchu związanego z agentami, oto jak znaleźć adresy URL bez symboli wieloznacznych:
>
>1. Zarejestruj swoje maszyny wirtualne w puli hostów pulpitu wirtualnego systemu Windows.
>2. Otwórz **przeglądarkę zdarzeń** i przejdź do **programu Windows loguje** > **aplikację** > **WVD-Agent** i poszukaj identyfikatora zdarzenia 3702.
>3. Umieszczaj na białej liście adresy URL w obszarze Identyfikator zdarzenia 3702. Adresy URL w obszarze Event ID 3702 są specyficzne dla regionu. Musisz powtórzyć proces umieszczania na białej liście z odpowiednimi adresami URL dla każdego regionu, w którego chcesz wdrożyć maszyny wirtualne.

Pulpit wirtualny systemu Windows obejmuje pulpity i aplikacje systemu Windows dostarczane użytkownikom oraz rozwiązanie do zarządzania, które jest hostowane jako usługa na platformie Azure przez firmę Microsoft. Komputery stacjonarne i aplikacje można wdrożyć na maszynach wirtualnych (VM) w dowolnym regionie platformy Azure, a rozwiązanie do zarządzania i dane dla tych maszyn wirtualnych będą znajdować się w Stanach Zjednoczonych. Może to spowodować transfer danych do Stanów Zjednoczonych.

Aby uzyskać optymalną wydajność, upewnij się, że sieć spełnia następujące wymagania:

* Opóźnienie w obie strony (RTT) z sieci klienta do regionu platformy Azure, w którym wdrożono pule hostów, powinno być mniejsze niż 150 ms.
* Ruch sieciowy może przepływać poza granicami kraju/regionu, gdy maszyny wirtualne obsługujące komputery stacjonarne i aplikacje łączą się z usługą zarządzania.
* Aby zoptymalizować pod kątem wydajności sieci, zaleca się, że maszyny wirtualne hosta sesji są kolokowane w tym samym regionie platformy Azure jako usługi zarządzania.

## <a name="supported-remote-desktop-clients"></a>Obsługiwani klienci pulpitu zdalnego

Następujący klienci pulpitu zdalnego obsługują pulpit wirtualny systemu Windows:

* [Pulpit systemu Windows](connect-windows-7-and-10.md)
* [Sieć Web](connect-web.md)
* [Macos](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (wersja zapoznawcza)](connect-android.md)

> [!IMPORTANT]
> Pulpit wirtualny systemu Windows nie obsługuje klienta usługi RemoteApp i połączenia pulpitu (RADC) ani klienta MSTSC (Remote Desktop Connection).

> [!IMPORTANT]
> Pulpit wirtualny systemu Windows obecnie nie obsługuje klienta pulpitu zdalnego ze Sklepu Windows. Obsługa tego klienta zostanie dodana w przyszłej wersji.

Klienci pulpitu zdalnego muszą mieć dostęp do następujących adresów URL:

|Adres|Wychodzący port TCP|Przeznaczenie|Klienci|
|---|---|---|---|
|*.wvd.microsoft.com|443|Ruch serwisowy|Wszystkie|
|*.servicebus.windows.net|443|Rozwiązywanie problemów z danymi|Wszystkie|
|go.microsoft.com|443|Microsoft FWLinks|Wszystkie|
|aka.ms|443|Skracacz adresów URL firmy Microsoft|Wszystkie|
|docs.microsoft.com|443|Dokumentacja|Wszystkie|
|privacy.microsoft.com|443|Oświadczenie o ochronie prywatności|Wszystkie|
|query.prod.cms.rt.microsoft.com|443|Aktualizacje klienta|Pulpit systemu Windows|

>[!IMPORTANT]
>Otwarcie tych adresów URL jest niezbędne dla niezawodnego doświadczenia klienta. Zablokowanie dostępu do tych adresów URL nie jest obsługiwane i wpłynie na funkcjonalność usługi. Te adresy URL odpowiadają tylko witrynom i zasobom klienta i nie zawierają adresów URL innych usług, takich jak usługa Azure Active Directory.

## <a name="supported-virtual-machine-os-images"></a>Obsługiwane obrazy systemu operacyjnego maszyny wirtualnej

Pulpit wirtualny systemu Windows obsługuje następujące obrazy systemu operacyjnego x64:

* Windows 10 Enterprise multi-session, wersja 1809 lub nowsza
* Windows 10 Enterprise, wersja 1809 lub nowsza
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Pulpit wirtualny systemu Windows nie obsługuje obrazów systemu operacyjnego X86 (32-bitowy), Windows 10 Enterprise N ani Windows 10 Enterprise KN. System Windows 7 nie obsługuje również żadnych rozwiązań profilu opartych na dyskach VHD lub VHDX hostowanych w zarządzanym usłudze Azure Storage ze względu na ograniczenie rozmiaru sektora.

Dostępne opcje automatyzacji i wdrażania zależą od wybranego systemu operacyjnego i wersji, jak pokazano w poniższej tabeli: 

|System operacyjny|Galeria obrazów platformy Azure|Ręczne wdrażanie maszyny wirtualnej|Integracja szablonów usługi Azure Resource Manager|Aprowizuj pule hostów w portalu Azure Marketplace|Aktualizacje programu Windows Virtual Desktop Agent|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Windows 10 multi-session, wersja 1903|Tak|Tak|Tak|Tak|Automatyczny|
|Windows 10 multi-session, wersja 1809|Tak|Tak|Nie|Nie|Automatyczny|
|Windows 10 Enterprise, wersja 1903|Tak|Tak|Tak|Tak|Automatyczny|
|Windows 10 Enterprise, wersja 1809|Tak|Tak|Nie|Nie|Automatyczny|
|Windows 7 Enterprise|Tak|Tak|Nie|Nie|Ręcznie|
|Windows Server 2019|Tak|Tak|Nie|Nie|Automatyczny|
|Windows Server 2016|Tak|Tak|Tak|Tak|Automatyczny|
|Windows Server 2012 R2|Tak|Tak|Nie|Nie|Automatyczny|

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, musisz utworzyć dzierżawę. Aby dowiedzieć się więcej o tym, jak utworzyć dzierżawę, przejdź do samouczka tworzenia dzierżawy.

> [!div class="nextstepaction"]
> [Tworzenie dzierżawy w usłudze Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
