---
title: Aktualizacja Azure stosu 1712 | Dokumentacja firmy Microsoft
description: Więcej informacji na temat nowości w aktualizacji 1712 stosu Azure zintegrowanych systemów, znane problemy i pobierania aktualizacji.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: b14f79ad-025f-45d8-9e1d-e53d2b420bb1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: brenduns
ms.openlocfilehash: c3cb8ab8a838a3f831ece617fcf3e218a9510ea5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-1712-update"></a>Azure aktualizacji 1712 stosu

*Dotyczy: Azure stosu zintegrowane systemy*

W tym artykule opisano ulepszenia i poprawki w tym pakiecie aktualizacji znane problemy dotyczące tej wersji i pobierania aktualizacji. Znane problemy są podzielone na znane problemy bezpośrednio związane z procesem aktualizacji i znane problemy związane z kompilacją (po instalacji).

> [!IMPORTANT]
> Ten pakiet aktualizacji dotyczy tylko systemów stosu Azure zintegrowanych. Nie dotyczą tego pakietu aktualizacji Azure stosu Development Kit.

## <a name="build-reference"></a>Tworzenie odwołania

Numer kompilacji aktualizacji 1712 stosu Azure jest **180106.1**. Jeśli klient został wdrożony **180103.2** wcześniej, nie trzeba zastosować **180106.1**.

## <a name="before-you-begin"></a>Przed rozpoczęciem

> [!IMPORTANT]
> Nie należy próbować tworzyć maszyn wirtualnych podczas procesu instalacji aktualizacji 1712. Zobacz [zarządzania aktualizacjami w omówieniu stosu Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#plan-for-updates) więcej szczegółów.

### <a name="prerequisites"></a>Wymagania wstępne

Należy najpierw zainstalować na stosie Azure [zaktualizować 1711](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1711) przed zastosowaniem tej aktualizacji.

### <a name="post-update-steps"></a>Czynności po aktualizacji

Ta aktualizacja wymaga również zainstalować aktualizacje oprogramowania układowego od partnera OEM po zakończeniu instalacji aktualizacji 1712 stosu Azure.

> [!NOTE]
> Zapoznaj się z witryny partnera OEM do pobierania aktualizacji.

### <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki

Ta aktualizacja obejmuje następujące ulepszenia i poprawki dla stosu Azure.

#### <a name="new-features"></a>Nowe funkcje

- Polecenia cmdlet test AzureStack do sprawdzania poprawności dostępne za pośrednictwem punktu końcowego uprzywilejowanych chmury Azure stosu
- Możliwość zarejestrowania odłączonego wdrożenia stosu Azure
- Monitorowanie alertów dotyczących certyfikatów i użytkownika okres ważności konta
- Dodano zestaw BmcPassword dotyczących polecenia cmdlet w program ten obrotu hasła BMC
- Aktualizacje rejestrowania sieci do obsługi rejestrowania na żądanie
- Operacja odtworzenia z obrazu pomocy technicznej dla zestawów skali maszyny wirtualnej (VMSS)
- Włącz tryb kiosku na maszynie Wirtualnej ERCS CloudAdmin logowania
- Dzierżawcy mogą automatycznie aktywować maszyn wirtualnych systemu Windows

#### <a name="fixes"></a>Poprawki

- Napraw, aby wyświetlić stan operacyjny węzła w trybie konserwacji podczas uruchamiania naprawy
- Napraw, aby poprawne sygnatury datę i godzinę rekordów użycia publicznego adresu IP
- Różne inne wydajność, stabilność i poprawki zabezpieczeń
- Źródła czasu i Defender uprzywilejowany poprawki błędów modułu punktu końcowego

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 nowe funkcje i poprawki

- [Stycznia, 3-2018 — KB4056890 (kompilacja 14393.2007 systemu operacyjnego)](https://support.microsoft.com/help/4056890/windows-10-update-kb4056890)
    - Ta aktualizacja obejmuje poprawki oprogramowania problemu zabezpieczeń branżowym opisanego przez [MSRC Security Advisory ADV 180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002).

### <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji

Ta sekcja zawiera znane problemy, które mogą wystąpić podczas instalacji aktualizacji 1712.

1. **Objaw:** operatory stosu Azure może zostać wyświetlony następujący błąd podczas procesu aktualizacji: *"typu"CheckHealth"rola"VirtualMachines"zgłosił wyjątek: \n\nVirtual sprawdzenie kondycji maszyny, dla - ACS01 wyprodukowanych następujące błędy. \nThere wystąpił błąd pobierania informacji o maszyn wirtualnych z hostów. Szczegóły wyjątku: \nGet-VM: operacji na komputerze "Node03" nie powiodło się: Usługa WS-Management nie może przetworzyć żądania. WMI \nservice lub Dostawca WMI zwróciła nieznany błąd: HRESULT 0x8004106c. "*
    1. **Przyczyna:** przyczyną tego problemu jest problem z systemem Windows Server, który ma zostać poprawione w kolejnych aktualizacji serwera okna.
    2. **Rozwiązanie:** skontaktuj się z Microsoft dział obsługi klienta i pomocy technicznej (CSS), aby uzyskać pomoc.
<br><br>
2. **Objaw:** operatory stosu Azure może zostać wyświetlony następujący błąd podczas procesu aktualizacji:*"Włączanie pierścień inicjatora maszyny Wirtualnej nie powiodło się w węźle Node03 hosta z powodu błędu: [hosta Node03] łączenie się z serwera zdalnego Node03 hosta nie powiodło się następujący komunikat o błędzie: Klient WinRM odebrał stan błędu serwera HTTP (500), ale usługi zdalnej nie zawiera inne informacje dotyczące przyczyny błędu. "*
    1. **Przyczyna:** przyczyną tego problemu jest problem z systemem Windows Server, który ma zostać poprawione w kolejnych aktualizacji serwera okna. 
    2. **Rozwiązanie:** skontaktuj się z Microsoft dział obsługi klienta i pomocy technicznej (CSS), aby uzyskać pomoc.
<br><br>

### <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)

Ta sekcja zawiera poinstalacyjne znane problemy z kompilacją **180106.1**.

#### <a name="portal"></a>Portal

- Może nie być możliwe do wyświetlania zasobów obliczeniowych i magazynu w portalu administratora. Oznacza to, że wystąpił błąd podczas instalowania aktualizacji i aktualizacji niepoprawnie Zgłoszono następujący powiodło się. Jeśli ten problem występuje, skontaktuj się z CSS firmy Microsoft, aby uzyskać pomoc.
- Może pojawić się pustego pulpitu nawigacyjnego w portalu. Aby odzyskać pulpitu nawigacyjnego, wybierz koło zębate ikonę w prawym górnym rogu portalu, a następnie wybierz **przywrócić ustawienia domyślne**.
- Po wyświetleniu właściwości grupy zasobów, **Przenieś** przycisk jest niedostępny. To zachowanie jest oczekiwane. Przeniesienie grup zasobów między subskrypcjami nie jest obecnie obsługiwane.
- Na każdy przepływ pracy, gdzie wybierz subskrypcji, grupy zasobów lub lokalizacji w listy rozwijanej może wystąpić jeden lub więcej z następujących problemów:

   - Może pojawić się pusty wiersz na początku listy. Nadal można wybrać elementu zgodnie z oczekiwaniami.
   - Jeśli na liście elementów na liście rozwijanej jest krótki, nie można przeglądać nazwy elementu.
   - Jeśli masz wiele subskrypcji użytkownika listy rozwijanej grupy zasobów może być pusta.

        > [!NOTE]
        > Aby obejść problemy z ostatnich dwóch, możesz wpisać nazwę subskrypcji lub grupy zasobów (jeśli ją znasz), lub można użyć programu PowerShell.

- Usunięcie użytkownika powoduje subskrypcje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkownika lub grupy zasobów całej, a następnie usuń subskrypcji użytkownika.
- Nie jest możliwe wyświetlić uprawnienia do subskrypcji przy użyciu portali stosu Azure. Jako rozwiązanie alternatywne można sprawdzić uprawnień za pomocą programu PowerShell.
- **Kondycja usługi** bloku nie udało się załadować. Po otwarciu bloku usługi kondycji w portalu administratora lub użytkownika, stosu Azure jest wyświetlany błąd i nie zostanie załadowany informacji. Jest to oczekiwane zachowanie. Chociaż można wybrać i otworzyć usługi kondycji, ta funkcja nie jest jeszcze dostępny, ale będzie wykonywany w przyszłych wersjach programu Azure stosu.

#### <a name="health-and-monitoring"></a>Monitorowania kondycji i

- Jeśli ponowne uruchomienie wystąpienia roli infrastruktury, może zostać wyświetlony komunikat informujący, że ponowne uruchomienie nie powiodło się. Jednak ponownego uruchamiania faktycznie zakończyło się pomyślnie.

#### <a name="marketplace"></a>Portal Marketplace
- Niektóre elementy marketplace są usuwane w tej wersji z powodu problemów ze zgodnością. Te zostaną ponownie włączone po dalszych weryfikacji.
- Użytkownicy można przeglądać pełnego portalu marketplace bez subskrypcji i widoczne elementy administracyjne, takie jak plany i oferty. Te elementy są niefunkcjonalne dla użytkowników.

#### <a name="compute"></a>Wystąpienia obliczeniowe
- Użytkownicy skorzystać z opcji, aby utworzyć maszynę wirtualną z magazynu geograficznie nadmiarowego. Ta konfiguracja powoduje niepowodzenie tworzenia maszyny wirtualnej.
- Można skonfigurować dostępność maszyn wirtualnych, ustaw tylko z jednej domeny błędów i jedną domenę aktualizacji.
- Brak nie doświadczenie marketplace, aby utworzyć zestawy skalowania maszyny wirtualnej. Można utworzyć skali ustawić za pomocą szablonu.
- Ustawienia skalowania dla zestawy skalowania maszyny wirtualnej nie są dostępne w portalu. Jako rozwiązanie alternatywne można zastosować [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z powodu różnic wersji programu PowerShell, należy użyć `-Name` parametru zamiast `-VMScaleSetName`.

#### <a name="networking"></a>Networking
- Nie można utworzyć modułu równoważenia obciążenia z publicznym adresem IP, za pomocą portalu. Jako rozwiązanie alternatywne można utworzyć modułu równoważenia obciążenia za pomocą programu PowerShell.
- Podczas tworzenia modułu równoważenia obciążenia sieciowego, należy utworzyć regułę translatora adresów sieciowych adres. Jeśli nie, otrzymasz wystąpił błąd podczas próby dodania reguły NAT po utworzeniu usługi równoważenia obciążenia.
- Nie można usunąć skojarzenie publicznego adresu IP z maszyną wirtualną (VM), po utworzeniu maszyny Wirtualnej i skojarzonych z tym adresem IP. Usuwanie skojarzeń pojawi się do pracy, ale poprzednio przypisanych publiczny adres IP pozostają skojarzone z oryginalna maszyna wirtualna. Dzieje się tak nawet w przypadku ponownego przypisywania adresów IP do nowej maszyny Wirtualnej (nazywane *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonego VM, a nie nowy. Obecnie tylko musi używać nowego publiczne adresy IP do tworzenia nowej maszyny Wirtualnej.
- Operatory stosu Azure może być niemożliwe do wdrożenia, usuwanie, modyfikowanie, sieciami wirtualnymi lub grup zabezpieczeń sieci. Ten problem występuje przede wszystkim prób kolejnych aktualizacji tego samego pakietu. Jest to spowodowane problemem pakietów za pomocą aktualizacji, który jest obecnie badanym.
- Wewnętrzny równoważenia obciążenia (ILB) nieprawidłowo obsługuje adresów MAC dla maszyn wirtualnych zaplecza, które dzieli wystąpień systemu Linux.

#### <a name="sqlmysql"></a>SQL/MySQL
- Może potrwać do godziny dzierżawców można utworzyć bazy danych w nowym SQL lub MySQL jednostki SKU.
- Tworzenie elementów bezpośrednio na SQL i MySQL serwerów, które nie są wykonywane przez dostawcę zasobów hosta nie jest obsługiwana i może spowodować niezgodne stanu.

    > [!NOTE]
    > Nie powinna mieć wpływu na istniejących użytkowników dostawcy zasobów SQL lub MySQL podczas aktualizacji do wersji 1712 systemów zintegrowanego stosu Azure. Można nadal używać bieżącej kompilacji dostawcy zasobów SQL lub MySQL, dopóki dostępna jest nowa aktualizacja stosu Azure.

#### <a name="app-service"></a>App Service
- Użytkownik musi zarejestrować dostawcy zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

#### <a name="identity"></a>Tożsamość

W usłudze Azure Active Directory Federation Services (ADFS) wdrożone w środowiskach **azurestack\azurestackadmin** konto nie jest już właściciela subskrypcji domyślny dostawca. Zamiast podczas logowania się na **portalu administracyjnego / punktu końcowego adminmanagement** z **azurestack\azurestackadmin**, można użyć **azurestack\cloudadmin** konta, dlatego Czy można zarządzać i korzystania z subskrypcji domyślny dostawca.

> [!IMPORTANT]
> Mimo że **azurestack\cloudadmin** konta jest właścicielem subskrypcji dostawcy domyślne w środowiskach usług AD FS wdrożona, nie ma uprawnień do protokołu RDP na hoście. Nadal używać **azurestack\azurestackadmin** konta lub lokalnego konta administratora do logowania, dostęp i zarządzać hostem zgodnie z potrzebami.

## <a name="download-the-update"></a>Pobierz aktualizację

Możesz pobrać pakiet aktualizacji 1712 stosu Azure z [tutaj](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>Więcej informacji

Firma Microsoft udostępnia sposób monitorowania i wznowić aktualizacji za pomocą uprzywilejowanych punktu końcowego (program ten) zainstalowanych z 1712 aktualizacji.

- Zobacz [monitorowania aktualizacji w stosie Azure, korzystając z dokumentacji uprzywilejowanych punktu końcowego](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 

## <a name="see-also"></a>Zobacz także

- Zobacz [zarządzania aktualizacjami w omówieniu stosu Azure](azure-stack-updates.md) omówienie zarządzania aktualizacjami w stosie Azure.
- Zobacz [stosowanie aktualizacji w stosie Azure](azure-stack-apply-updates.md) Aby uzyskać więcej informacji dotyczących sposobu stosowania aktualizacji za pomocą usługi Azure stosu.
