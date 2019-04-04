---
title: Usługa Azure Stack dodać skalowanie węzłów | Dokumentacja firmy Microsoft
description: Dodawanie węzłów na skalowanie do jednostek w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 17da540bd6077b8e045f125fd3cf13dc0e043000
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882042"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack"></a>Dodaj węzły jednostki rozbudowywać w usłudze Azure Stack

Operatorzy usługi Azure Stack może zwiększyć ogólną wydajność istniejącej jednostki skalowania przez dodanie dodatkowych komputera fizycznego. Komputer fizyczny również nazywa się węzeł jednostki skalowania. Każdy nowy węzeł jednostki skalowania, dodawanych musi być jednolite w typ procesora CPU, pamięci, a numer dysku i rozmiar węzłów, które już znajdują się w jednostce skalowania.

Aby dodać węzeł jednostki skalowania, działają w usłudze Azure Stack i uruchomienie narzędzia z producentem sprzętu sprzętu (OEM). Narzędzia OEM działa na hoście cyklu życia sprzętu (HLH) i upewnij się, że w tym samym poziomie oprogramowania układowego, co istniejące węzły pasuje do nowego komputera fizycznego.

Poniższy diagram przepływu przedstawia ogólny proces dodawania węzła jednostki skalowania.

![Dodaj przepływ jednostki skalowania](media/azure-stack-add-scale-node/add-node-flow.png) &#42; *czy z dostawcą sprzętu OEM enacts umieszczania stojak serwerów fizycznych i aktualizacje oprogramowania układowego różni się w oparciu o umowę na pomoc techniczną.*

Operacja dodawania nowego węzła może potrwać kilka godzin lub dni, aby zakończyć.

> [!Note]  
> Nie próbuj jedną z następujących czynności gdy trwa już operacja węzła jednostki skalowania dodawania:
>
>  - Aktualizacja usługi Azure Stack
>  - Obróć certyfikatów
>  - Zatrzymaj usługę Azure Stack
>  - Węzeł jednostki skalowania naprawy


## <a name="add-scale-unit-nodes"></a>Dodawanie węzłów do jednostki skalowania

Poniższe kroki są ogólne omówienie sposobu dodawania węzła. Nie wykonaj następujące kroki bez pierwszy odwołujące się do dokumentacji rozszerzenia dostarczonego przez producenta OEM pojemności.

1. Umieść nowego serwera fizycznego w stojaku i Podłączanie kabli odpowiednio. 
2. Włącz porty przełącznika fizycznego i dostosowanie listy kontroli dostępu (ACL), jeśli ma to zastosowanie.
3. Skonfiguruj prawidłowego adresu IP w kontroler zarządzania płytą główną (BMC) i Zastosuj wszystkie ustawienia systemu BIOS na dokumentacji dostarczonego przez producenta OEM.
4. Zastosuj bieżącą bazową oprogramowania układowego dla wszystkich składników przy użyciu narzędzia, które zostały udostępnione przez producenta sprzętu i uruchamianych na HLH.
5. Uruchamianie operacji dodawania węzła w portalu administracyjnym usługi Azure Stack.
6. Sprawdź, czy operacji dodawania node zakończy się pomyślnie. Aby to zrobić, należy sprawdzić [ **stan** jednostki skalowania](#monitor-add-node-operations). 

## <a name="add-the-node"></a>Dodaj węzeł

Portal administracyjny lub programu PowerShell służy do dodawania nowych węzłów. Operacja węźle Dodaj najpierw dodaje nowy węzeł jednostki skalowania jako możliwości obliczeniowych dostępnych i automatycznie rozszerza pojemność magazynu. Pojemność automatycznie rozszerza się, ponieważ usługa Azure Stack to system hiperzbieżnego gdzie *obliczenia* i *magazynu* skalowane razem.

### <a name="use-the-admin-portal"></a>Korzystać z portalu administratora

1. Zaloguj się do portalu administracyjnego usługi Azure Stack jako operatorów usługi Azure Stack.
2. Przejdź do **+ Utwórz zasób** > **pojemności** > **węzła jednostki skalowania**.
   ![Węzeł jednostki skalowania](media/azure-stack-add-scale-node/select-node1.png)
3. Na **Dodaj węzeł** okienku wybierz *Region*, a następnie wybierz pozycję *jednostki skalowania* chcesz dodać węzeł do. Również określić *adres IP kontrolera BMC* dla węzła jednostki skalowania, przystępując do dodawania. W danym momencie można dodawać tylko jeden węzeł.
   ![Dodawanie szczegółów węzłów](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Użyj **New AzsScaleUnitNodeObject** polecenia cmdlet, aby dodać węzeł.  

Przed rozpoczęciem korzystania z jednej z następujących przykładowych skryptów programu PowerShell, Zastąp wartości *nazwy węzłów* i *adresów IP* wartościami z używanego środowiska Azure Stack.

  > [!Note]  
  > Podczas określania nazwy węzła musi przechowywać nazwy do mniej niż 15 znaków. Również nie można użyć nazwy, która zawiera spację lub zawiera dowolne z następujących znaków: `\`, `/`, `:`, `*`, `?`, `"`, `<`, `>`, `|`, `\`, `~`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `(`, `)`, `{`, `}`, `_`.

**Dodawanie węzła:**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

## <a name="monitor-add-node-operations"></a>Monitor dodać operacje węzła 
Można uzyskać stanu operacji dodawania węzłów można użyć programu PowerShell lub portalu administracyjnego. Dodaj węzeł operacji może zająć kilka godzin, dni, aby zakończyć.

### <a name="use-the-admin-portal"></a>Korzystać z portalu administratora 
Aby monitorować dodanie nowego węzła, w portalu administracyjnym przejrzeć jednostki skalowania lub obiekty węzła jednostki skalowania. Aby to zrobić, przejdź do **zarządzanie regionami** > **jednostek skalowania**. Następnie wybierz jednostkę skalowania lub węzła jednostki skalowania, którego chcesz przejrzeć. 

### <a name="use-powershell"></a>Korzystanie z programu PowerShell
Stan dla jednostki skalowania i węzły jednostki skalowania można pobrać przy użyciu programu PowerShell w następujący sposób:
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>Stan operacji dodawania węzła 
**Dla jednostki skalowania:**

|Stan               |Opis  |
|---------------------|---------|
|Działanie              |Wszystkie węzły są aktywnie uczestniczy w jednostce skalowania.|
|Zatrzymano              |Węzeł jednostki skalowania w dół lub jest niedostępny.|
|Rozszerzanie            |Co najmniej jeden węzeł jednostki skalowania aktualnie są dodawane jako moc obliczeniową.|
|Konfigurowanie magazynu  |Moc obliczeniową została rozwinięta i konfiguracja magazynu jest uruchomiony.|
|Wymaga korygowania |Wykryto błąd, która wymaga co najmniej jeden węzeł jednostki skalowania naprawy.|


**Dla węzła jednostki skalowania:**

|Stan                |Opis  |
|----------------------|---------|
|Działanie               |Węzeł jest aktywnie uczestniczy w jednostce skalowania.|
|Zatrzymano               |Ten węzeł jest niedostępny.|
|Dodawanie                |Węzeł jest aktywnie dodawana do jednostki skalowania.|
|Naprawianie             |Węzeł jest aktywnie naprawiane.|
|Konserwacja           |Węzeł jest wstrzymany, a nie obciążenie aktywnego użytkownika działa. |
|Wymaga korygowania  |Wykryto błąd, wymagającym węzeł, który ma zostać naprawiony.|


## <a name="troubleshooting"></a>Rozwiązywanie problemów
Poniżej przedstawiono typowych problemów występujących podczas dodawania węzła. 

**Scenariusz 1:**  Operacja węzła jednostki skalowania Dodaj zakończy się niepowodzeniem, ale jeden lub więcej węzłów są wyświetlane ze stanem zatrzymania.  
- Środki zaradcze: Napraw jeden lub więcej węzłów za pomocą operacji naprawy. Tylko operacji naprawy pojedynczego można uruchomić w tym samym czasie.

**Scenariusz 2:** Co najmniej jeden węzeł jednostki skalowania zostały dodane, ale magazynu nie powiodło się. W tym scenariuszu obiekt węzła jednostki skalowania zgłasza stan uruchomione, ale zadanie konfigurowania magazynu nie jest uruchomiona.  
- Środki zaradcze: Użycie uprzywilejowanych punktu końcowego, aby sprawdzić kondycję magazynu, uruchamiając następujące polecenie cmdlet programu PowerShell:
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**Scenariusz 3:** Odebrano alert, który oznacza, że zadanie skalowalnego w poziomie magazynu nie powiodło się.  
- Środki zaradcze: W tym przypadku zadanie konfiguracji magazynu nie powiodło się. Ten problem, należy się z pomocą techniczną.


## <a name="next-steps"></a>Kolejne kroki 
[Dodaj publiczne adresy IP](azure-stack-add-ips.md) 
