---
title: Rozwiązywanie problemów z powrotem po awarii do stanu lokalnego podczas odzyskiwania po awarii maszyny wirtualnej VMware na platformie Azure za pomocą Azure Site Recovery
description: W tym artykule opisano sposoby rozwiązywania problemów z odzyskiwaniem po awarii i ochroną podczas odzyskiwania po awarii maszyny wirtualnej VMware na platformę Azure przy użyciu Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: e9213637f45a4761af60de9dfac7add6324f6b96
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053847"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>Rozwiązywanie problemów z awariami odnajdywania w programie vCenter

Ten artykuł pomaga w rozwiązywaniu problemów występujących w wyniku awarii programu VMware vCenter Discovery.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Wartości inne niż liczbowe we właściwości maxSnapShots

W wersjach wcześniejszych niż 9,20 program vCenter rozłącza się, gdy pobiera nieliczbową wartość właściwości `snapshot.maxSnapShots` właściwości na maszynie wirtualnej.

Ten problem jest identyfikowany przez błąd o IDENTYFIKATORze 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Aby rozwiązać ten problem:

- Zidentyfikuj maszynę wirtualną i ustaw wartość na wartość liczbową (ustawienia ustawień maszyny wirtualnej w programie vCenter).

Lub

- Uaktualnij serwer konfiguracji do wersji 9,20 lub nowszej.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problemy z konfiguracją serwera proxy dla łączności vCenter

Odnajdowanie vCenter uwzględnia domyślne ustawienia serwera proxy systemu skonfigurowane przez użytkownika systemu. Usługa DRA przestrzega ustawień serwera proxy dostarczonych przez użytkownika podczas instalacji serwera konfiguracji za pomocą ujednoliconego Instalatora Instalatora lub szablonu komórki jajowe. 

Ogólnie rzecz biorąc, serwer proxy jest używany do komunikacji z sieciami publicznymi; na przykład komunikacja z platformą Azure. Jeśli serwer proxy jest skonfigurowany i program vCenter znajduje się w środowisku lokalnym, nie będzie mógł komunikować się z agentem DRA.

Podczas napotkania tego problemu występują następujące sytuacje:

- Serwer vCenter \<vCenter > jest nieosiągalny z powodu błędu: serwer zdalny zwrócił błąd: (503) serwer niedostępny
- Serwer vCenter \<vCenter > jest nieosiągalny z powodu błędu: serwer zdalny zwrócił błąd: nie można nawiązać połączenia z serwerem zdalnym.
- Nie można nawiązać połączenia z serwerem vCenter/ESXi.

Aby rozwiązać ten problem:

Pobierz [Narzędzie PsExec](https://aka.ms/PsExec). 

Użyj narzędzia PsExec, aby uzyskać dostęp do kontekstu użytkownika systemu i określić, czy adres serwera proxy jest skonfigurowany. Następnie można dodać program vCenter do listy obejścia przy użyciu poniższych procedur.

W przypadku konfiguracji serwera proxy odnajdywania:

1. Otwórz program IE w kontekście użytkownika systemu przy użyciu narzędzia PsExec.
    
    PsExec-s-i "użycie ścieżki%ProgramFiles%\Internet Explorer\iexplore.exe"

2. Zmodyfikuj ustawienia serwera proxy w programie Internet Explorer, aby pominąć adres IP programu vCenter.
3. Uruchom ponownie usługę tmanssvc.

W przypadku konfiguracji serwera proxy agenta DRA:

1. Otwórz wiersz polecenia i Otwórz folder Microsoft Azure Site Recovery dostawcy.
 
    **CD C:\Program Files\Microsoft Azure Site Recovery Provider**

3. W wierszu polecenia Uruchom następujące polecenie.
   
   **DRCONFIGURATOR. EXE/Configure/AddBypassUrls [adres IP/nazwa FQDN vCenter Server podano w czasie dodawania programu vCenter]**

4. Uruchom ponownie usługę dostawcy DRA.

## <a name="next-steps"></a>Następne kroki

[Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii maszyny wirtualnej VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
