---
title: Rozwiązywanie problemów z błędami odnajdowania vCenter vMware w usłudze Azure Site Recovery
description: W tym artykule opisano sposób rozwiązywania problemów z błędami odnajdowania vCenter vMware w usłudze Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: f00c7b12accde9df9a5708a2b8b378d70428318d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74091249"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>Rozwiązywanie problemów z błędami odnajdowania serwera vCenter

Ten artykuł ułatwia rozwiązywanie problemów występujących z powodu błędów odnajdowania vCenter VMware.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Wartości nienumeryczne we właściwości maxSnapShots

W wersjach wcześniejszych niż 9.20 vCenter rozłącza się, gdy `snapshot.maxSnapShots` pobiera wartość nieliczbową dla właściwości właściwości na maszynie Wirtualnej.

Ten problem jest identyfikowany przez identyfikator błędu 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Aby rozwiązać ten problem:

- Zidentyfikuj maszynę wirtualną i ustaw wartość liczbową (ustawienia edycji maszyny Wirtualnej w vCenter).

Lub

- Uaktualnij serwer konfiguracji do wersji 9.20 lub nowszej.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problemy z konfiguracją serwera proxy dla łączności vCenter

VCenter Discovery honoruje domyślne ustawienia serwera proxy systemu skonfigurowane przez użytkownika systemu. Usługa DRA honoruje ustawienia serwera proxy dostarczone przez użytkownika podczas instalacji serwera konfiguracji przy użyciu ujednoliconego instalatora lub szablonu OVA. 

Ogólnie rzecz biorąc serwer proxy jest używany do komunikowania się z sieciami publicznymi; komunikacji z platformą Azure. Jeśli serwer proxy jest skonfigurowany i vCenter znajduje się w środowisku lokalnym, nie będzie mógł komunikować się z usługą DRA.

Następujące sytuacje występują, gdy ten problem występuje:

- Serwer vCenter \<vCenter> nie jest osiągalny z powodu błędu: Serwer zdalny zwrócił błąd: (503) Serwer niedostępny
- VCenter server \<vCenter> nie jest osiągalny z powodu błędu: Serwer zdalny zwrócił błąd: Nie można połączyć się z serwerem zdalnym.
- Nie można połączyć się z serwerem vCenter/ESXi.

Aby rozwiązać ten problem:

Pobierz [narzędzie PsExec](https://aka.ms/PsExec). 

Użyj narzędzia PsExec, aby uzyskać dostęp do kontekstu użytkownika systemu i określić, czy adres serwera proxy jest skonfigurowany. Następnie można dodać vCenter do listy pomijania przy użyciu następujących procedur.

W przypadku konfiguracji serwera proxy odnajdywania:

1. Otwórz IE w kontekście użytkownika systemu za pomocą narzędzia PsExec.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Zmodyfikuj ustawienia serwera proxy w programie Internet Explorer, aby pominąć adres IP vCenter.
3. Uruchom ponownie usługę tmanssvc.

W przypadku konfiguracji serwera proxy DRA:

1. Otwórz wiersz polecenia i otwórz folder Dostawca odzyskiwania witryny platformy Microsoft Azure.
 
    **cd C:\Pliki programów\Dostawca odzyskiwania witryny platformy Microsoft Azure**

3. W wierszu polecenia uruchom następujące polecenie.
   
   **DRCONFIGURATOR. EXE /configure /AddBypassUrls [Adres IP/FQDN serwera vCenter dostarczone w momencie dodawania vCenter]**

4. Uruchom ponownie usługę dostawcy usługi DRA.

## <a name="next-steps"></a>Następne kroki

[Zarządzanie serwerem konfiguracji odzyskiwania po awarii VMware VMware VM](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
