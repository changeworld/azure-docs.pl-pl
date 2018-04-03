---
title: Aktualizacja Azure stosu 1711 | Dokumentacja firmy Microsoft
description: Więcej informacji na temat nowości w aktualizacji 1711 stosu Azure zintegrowanych systemów, znane problemy i pobierania aktualizacji.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 2b66fe05-3655-4f1a-9b30-81bd64ba0013
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: f067656f510c14c4a2ac02d3629ae71432c342d2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-1711-update"></a>Azure aktualizacji 1711 stosu

*Dotyczy: Azure stosu zintegrowane systemy*

W tym artykule opisano ulepszenia i poprawki w tym pakiecie aktualizacji znane problemy dotyczące tej wersji i pobierania aktualizacji. Znane problemy są podzielone na znane problemy bezpośrednio związane z procesem aktualizacji i znane problemy związane z kompilacją (po instalacji).

> [!IMPORTANT]
> Ten pakiet aktualizacji dotyczy tylko systemów stosu Azure zintegrowanych. Nie dotyczą tego pakietu aktualizacji Azure stosu Development Kit.

## <a name="build-reference"></a>Tworzenie odwołania

Numer kompilacji aktualizacji 1711 stosu Azure jest **171201.3**.

## <a name="before-you-begin"></a>Przed rozpoczęciem

### <a name="prerequisites"></a>Wymagania wstępne

- Należy najpierw zainstalować na stosie Azure [zaktualizować 1710](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1710) przed zastosowaniem tej aktualizacji.

- Przejrzyj użycie **CloudAdmin** jako nazwę konta, przed zainstalowaniem aktualizacji 1711. Począwszy od wersji 1711, *CloudAdmin* jest nazwą konta zastrzeżone i nie powinien być określony ręcznie. Podczas aktualizacji do wersji 1711 aktualizacja usuwa istniejące wystąpienia konto wdrażania (zwykle nazywane AzureStackAdmin). Jeśli konto wdrażania *CloudAdmin*, aktualizacja do 1711 usunięcia go. 

  *CloudAdmin* jest wbudowane konto używane do nawiązywania połączenia z [ *uprzywilejowanych punktu końcowego* ](azure-stack-privileged-endpoint.md) (program ten). Usunięcie tego konta może spowodować blokady program ten, chyba że innego konta użytkownika, który jest członkiem grupy CloudAdmin już istnieje. 

  Jeśli CloudAdmin jest używana jako nazwa konta wdrożenia, należy dodać nowego użytkownika CloudAdmin do Twojej program ten, przed rozpoczęciem aktualizacji 1711, aby uniknąć blokowanie poza stosu Azure. Aby dodać nowego użytkownika CloudAdmin, uruchom polecenie cmdlet **CloudAdminUser nowy** na program ten.

### <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki

Ta aktualizacja obejmuje następujące ulepszenia i poprawki dla stosu Azure.

#### <a name="new-features"></a>Nowe funkcje

- Obsługa Syndicating szablony rozwiązań
- Aktualizacje w Azure stosu Graph rejestrowania i obsługa błędów
- Włączanie i wyłączanie hostów
- Użytkownicy teraz aktywować automatycznie maszyn wirtualnych systemu Windows
- Dodano punkt końcowy uprzywilejowanych polecenia cmdlet programu PowerShell, aby pobrać klucze odzyskiwania funkcji BitLocker na potrzeby przechowywania
- Obsługa aktualizowania obrazów w trybie offline podczas aktualizowania infrastruktury
- Włączenia kopii zapasowej infrastruktury włączenia usługi tworzenia kopii zapasowej

#### <a name="fixes"></a>Poprawki

- Stałe wyścigu w systemie DNS podczas rejestrowania również zaktualizowany klastra i pola (jednostkę FRU replaceable unit)
- Poprawka dla możliwość ponownego uruchomienia disable-host w polu (jednostkę FRU replaceable unit)
- Różne inne wydajności, zabezpieczeń oraz poprawki stabilności

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 nowe funkcje i poprawki

- [14 listopada 2017 — KB4048953 (kompilacja 14393.1884 systemu operacyjnego) ](https://support.microsoft.com/help/4048953)

### <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji

Ta sekcja zawiera znane problemy, które mogą wystąpić podczas instalacji aktualizacji 1711.


1. **Objaw:** operatory stosu Azure może zostać wyświetlony następujący błąd podczas procesu aktualizacji: *"name: Zainstaluj aktualizację.", "opis": "Instalacja aktualizacji na hostach i Infra maszyn wirtualnych.", "errorMessage": "typu"LiveUpdate"roli" VirtualMachines zgłoszony wyjątek: \n\nThere nie jest za mało miejsca na disk.\n\nat <ScriptBlock>, <No file>: line22 ","status":"Błąd","startTimeUtc":" 2017-11-10T16:46:59.123Z ","endTimeUtc":" 2017-11-10T19:20:29.669Z ","kroki": []"*
    2. **Przyczyna:** przyczyną tego problemu jest Brak wolnego miejsca na co najmniej jeden maszyn wirtualnych, które są częścią infrastruktury Azure stosu
    3. **Rozwiązanie:** skontaktuj się z Microsoft dział obsługi klienta i pomocy technicznej (CSS), aby uzyskać pomoc.
<br><br>
2. **Objaw:** operatory stosu Azure może zostać wyświetlony następujący błąd podczas procesu aktualizacji:*wyjątek podczas wywoływania "ExtractToFile" z "3" argumentów: "proces nie może uzyskać dostępu do pliku" <\\<machineName>— ERCS01\C$ \ Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Diagnostics\Microsoft.AzureStack.Common.Tools.Diagnostics.AzureStackDiagnostics.dll > "*
    1. **Przyczyna:** ten problem występuje po wznowieniu wznawianie aktualizacji z portalu, który został wcześniej, używając uprzywilejowanego punktu końcowego (program ten).
    2. **Rozwiązanie:** skontaktuj się z Microsoft dział obsługi klienta i pomocy technicznej (CSS), aby uzyskać pomoc.
<br><br>
3. **Objaw:** operatory stosu Azure może zostać wyświetlony następujący błąd podczas procesu aktualizacji:*"typu"CheckHealth"rola"VirtualMachines"zgłoszony wyjątek: \n\nVirtual maszyny kondycji Sprawdź <machineName>-ACS01 utworzone następujące błędy. \nThere wystąpił błąd pobierania informacji o maszyn wirtualnych z hostów. Szczegóły wyjątku: \nGet-VM: operacji na komputerze "Node03" nie powiodło się: Usługa WS-Management nie może przetworzyć żądania. WMI \nservice lub Dostawca WMI zwróciła nieznany błąd: HRESULT 0x8004106c ".*
    1. **Przyczyna:** przyczyną tego problemu jest problem z systemem Windows Server, który ma zostać poprawione w kolejnych aktualizacji serwera okna.
    2. **Rozwiązanie:** skontaktuj się z Microsoft dział obsługi klienta i pomocy technicznej (CSS), aby uzyskać pomoc.
<br><br>
4. **Objaw:** operatory stosu Azure może zostać wyświetlony następujący błąd podczas procesu aktualizacji:*"typu"DefenderUpdate"rola"URP"zgłosił wyjątek: nie powiodło się pobieranie wersji z \\SU1FileServer\SU1_Public\ DefenderUpdates\x64\{nazwę pliku} .exe po 60 prób AzSDefenderFiles kopiowania, C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Defender\Microsoft.AzureStack.Defender.psm1: wiersz 262"*
    1. **Przyczyna:** przyczyną tego problemu w tle nie powiodło się lub są one niepełne pobranie aktualizacji definicji programu Windows Defender.
    2. **Rozwiązanie:** od pierwszej aktualizacji spróbuj minęło próba Sprawdź wznowienie aktualizacji po maksymalnie 8 godzin.

### <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)

Ta sekcja zawiera poinstalacyjne znane problemy z kompilacją **20171201.3**.

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
- Podczas dodawania elementów do stosu Azure marketplace przy użyciu **Dodaj z platformy Azure** opcji, nie wszystkie elementy mogą być widoczne do pobrania.
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

#### <a name="app-service"></a>App Service
- Użytkownik musi zarejestrować dostawcy zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

#### <a name="identity"></a>Tożsamość

W usłudze Azure Active Directory Federation Services (ADFS) wdrożone w środowiskach **azurestack\azurestackadmin** konto nie jest już właściciela subskrypcji domyślny dostawca. Zamiast podczas logowania się na **portalu administracyjnego / punktu końcowego adminmanagement** z **azurestack\azurestackadmin**, można użyć **azurestack\cloudadmin** konta, dlatego Czy można zarządzać i korzystania z subskrypcji domyślny dostawca.

> [!IMPORTANT]
> Mimo że **azurestack\cloudadmin** konta jest właścicielem subskrypcji dostawcy domyślne w środowiskach usług AD FS wdrożona, nie ma uprawnień do protokołu RDP na hoście. Nadal używać **azurestack\azurestackadmin** konta lub lokalnego konta administratora do logowania, dostęp i zarządzać hostem zgodnie z potrzebami.

#### <a name="infrastructure-backup-sevice"></a>Infrastruktura usługi tworzenia kopii zapasowej
<!-- 1974890-->

- **Wstępnie 1711 kopie zapasowe nie są obsługiwane dla chmury odzyskiwania.**  
  Wstępnie 1711 kopie zapasowe nie są zgodne z chmury odzyskiwania. Należy zaktualizować 1711 i włączyć kopie zapasowe. Jeśli kopie zapasowe jest już włączony, upewnij się, że po zaktualizowaniu do 1711 tworzenie kopii zapasowej. Kopie zapasowe Pre-1711 powinien zostać usunięty.

- **Włączanie infrastruktura kopii zapasowej na ASDK jest tylko do celów testowych.**  
  Infrastruktura kopii zapasowych może służyć do przywrócenia rozwiązania z wieloma węzłami. Można włączyć infrastruktury wykonanie kopii zapasowej na ASDK, ale nie istnieje sposób do testowania odzyskiwania.

Aby uzyskać więcej informacji, zobacz [kopii zapasowej i odzyskiwanie danych dla stosu Azure w usłudze Kopia zapasowa infrastruktury](azure-stack-backup-infrastructure-backup.md).

## <a name="download-the-update"></a>Pobierz aktualizację

Możesz pobrać pakiet aktualizacji 1711 stosu Azure z [tutaj](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>Więcej informacji

Firma Microsoft udostępnia sposób monitorowania i wznowić aktualizacji za pomocą uprzywilejowanych punktu końcowego (program ten) zainstalowanych z 1711 aktualizacji.

- Zobacz [monitorowania aktualizacji w stosie Azure, korzystając z dokumentacji uprzywilejowanych punktu końcowego](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 

## <a name="see-also"></a>Zobacz także

- Zobacz [zarządzania aktualizacjami w omówieniu stosu Azure](azure-stack-updates.md) omówienie zarządzania aktualizacjami w stosie Azure.
- Zobacz [stosowanie aktualizacji w stosie Azure](azure-stack-apply-updates.md) Aby uzyskać więcej informacji dotyczących sposobu stosowania aktualizacji za pomocą usługi Azure stosu.
