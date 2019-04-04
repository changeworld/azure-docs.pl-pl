---
title: Skalowanie akcji węzła jednostki w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić stan węzła i wykorzystać możliwości mocy, wyłącz, wyłącz i wznowić operacje węzła w zintegrowanym systemie Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 01/22/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/22/2019
ms.openlocfilehash: cd7e66961a0b9a80150a3d3e132efd29485cdb66
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483152"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Liczba akcji skalowania jednostki węzła w usłudze Azure Stack

*Dotyczy: Zintegrowane systemy usługi Azure Stack*

W tym artykule opisano sposób wyświetlania stanu jednostki skalowania. Możesz wyświetlić węzły, jednostki. Można uruchomić węzeł akcje, takie jak power na mocy, wyłącz, Zamknij, opróżnić, wznowić i naprawić. Zazwyczaj korzystają z tych akcji węzła, podczas zastępowania pola części lub odzyskiwania węzła.

> [!Important]  
> Wszystkie akcje węzła opisanych w tym artykule powinien dotyczyć jednym węźle naraz.

## <a name="view-the-node-status"></a>Widok stanu węzła

W portalu administratora możesz wyświetlić stan jednostki skalowania i jego skojarzone węzły.

Aby wyświetlić stan jednostki skalowania:

1. Na **zarządzanie regionami** kafelka, wybierz region.
2. Po lewej stronie w obszarze **zasoby infrastruktury**, wybierz opcję **jednostek skalowania**.
3. W wynikach wybierz jednostkę skalowania.
4. Po lewej stronie w obszarze **ogólne**, wybierz opcję **węzłów**.

   Wyświetl następujące informacje:

   - Lista pojedynczych węzłów
   - Stan operacyjny (patrz lista poniżej)
   - Stan zasilania (uruchomiona lub zatrzymana)
   - model serwera
   - Adres IP kontrolera zarządzania płytą główną (BMC)
   - Łączna liczba rdzeni
   - Całkowita ilość pamięci

![stan jednostki skalowania](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>Stany operacyjne węzłów

| Stan | Opis |
|----------------------|-------------------------------------------------------------------|
| Działanie | Węzeł jest aktywnie uczestniczy w jednostce skalowania. |
| Zatrzymano | Ten węzeł jest niedostępny. |
| Dodawanie | Węzeł jest aktywnie dodawana do jednostki skalowania. |
| Naprawianie | Węzeł jest aktywnie naprawiane. |
| Konserwacja | Węzeł jest wstrzymany, a nie obciążenie aktywnego użytkownika działa. |
| Wymaga korygowania | Wykryto błąd, wymagającym węzeł, który ma zostać naprawiony. |

## <a name="scale-unit-node-actions"></a>Liczba akcji węzła jednostki skalowania

Podczas wyświetlania informacji na temat węzła jednostki skalowania można również wykonać akcje węzła takich jak:
 - Uruchamianie i zatrzymywanie (w zależności od bieżącego stanu zasilania)
 - Wyłącz i wznowić (w zależności od stanu operacji)
 - Napraw
 - Shutdown

Stan operacyjny węzła Określa, które opcje są dostępne.

Musisz zainstalować moduły programu Azure Stack PowerShell. Tych poleceń cmdlet znajdują się w **Azs.Fabric.Admin** modułu. Aby zainstalować lub Zweryfikuj instalację programu PowerShell dla usługi Azure Stack, zobacz [Instalowanie programu PowerShell dla usługi Azure Stack](azure-stack-powershell-install.md).

## <a name="stop"></a>Stop

**Zatrzymać** akcja powoduje wyłączenie węzła. Jest to ten sam tak, jakby naciśnij przycisk zasilania. Nie wysyła sygnał zamknięcia systemu operacyjnego. Dla operacji zatrzymania planowane zawsze najpierw spróbuj wykonać operację zamknięcia. 

Ta akcja służy zazwyczaj gdy węzeł jest w stanie zawieszone i już nie odpowiada na żądania.

Aby uruchomić akcję stop, otwórz wiersz PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie cmdlet:

```powershell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

W rzadkich przypadkach, że Akcja zatrzymania nie działa, spróbuj ponownie wykonać operację, a jeśli go nie powiedzie się po raz drugi interfejs sieci web kontrolera zarządzania płytą GŁÓWNĄ zamiast tego użyj.

Aby uzyskać więcej informacji, zobacz [Stop AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Uruchamianie

**Start** Akcja włącza się w węźle. Jest to ten sam tak, jakby naciśnij przycisk zasilania. 
 
Aby uruchomić akcję uruchamiania, otwórz wiersz PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie cmdlet:

```powershell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

W mało prawdopodobnym przypadku, akcji uruchamiania nie działa, spróbuj ponownie wykonać operację, a jeśli go nie powiedzie się po raz drugi interfejs sieci web kontrolera zarządzania płytą GŁÓWNĄ zamiast tego użyj.

Aby uzyskać więcej informacji, zobacz [Start AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Opróżniania

**Opróżnić** akcja zostanie przeniesiona do pozostałych węzłów w tej jednostce skalowania określonego wszystkie aktywne obciążenia.

Ta akcja jest zwykle używany podczas zastępowania pola części, takie jak zastąpienie całego węzła.

> [!Important]
> Upewnij się, należy użyć operacji opróżniania w węźle podczas okna zaplanowanej konserwacji, gdzie użytkownicy zostali powiadomieni. W niektórych warunkach aktywne obciążenia mogą występować przerw w działaniu.

Aby uruchomić akcję opróżniania, otwórz wiersz PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie cmdlet:

```powershell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Aby uzyskać więcej informacji, zobacz [AzsScaleUnitNode Wyłącz](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Resume

**Wznowić** akcji wznawia węzeł wyłączony i oznacza je jako aktywny rozmieszczenia obciążeń. Wcześniej obciążeń, które były uruchomione w węźle nie powrót po awarii. (Jeśli użyjesz operacji opróżniania w węźle należy wyłączyć. Gdy użytkownik włącza węzła nie jest oznaczony jako aktywny rozmieszczenia obciążeń. Gdy wszystko będzie gotowe, należy użyć akcji Wznów można oznaczyć węzeł jako aktywny.)

Aby uruchomić akcję wznawianie, otwórz wiersz PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie cmdlet:

```powershell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Aby uzyskać więcej informacji, zobacz [AzsScaleUnitNode Włącz](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Napraw

**Napraw** akcji naprawia węzła. Używać tylko w przypadku jednego z następujących scenariuszy:
 - Zastąpienie pełną węzła (z lub bez nowych dysków danych)
 - Po awarii składników sprzętowych i zamieniania (jeśli jest to zalecane w pole dokumentacji jednostkę (FRU) replaceable unit).

> [!Important]  
> W dokumentacji dostawcy sprzętu OEM FRU dokładne kroki gdy trzeba wymienić węzła lub składników sprzętowych. Dokumentacja jednostki FRU określą, czy istnieje potrzeba Uruchom akcję naprawy po zastąpieniu składnik sprzętowy. 

Po uruchomieniu akcji naprawy, należy określić adres IP kontrolera BMC. 

Uruchom akcję naprawy, otwórz wiersz PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie cmdlet:

  ```powershell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>Shutdown

**Zamknięcia** fist akcji przenosi wszystkie aktywne obciążenia do pozostałych węzłów w tej samej jednostce skalowania. Następnie bezpiecznie zamknięcie węzła jednostki skalowania.

Po uruchomieniu węzła, który został zamknięty, musisz uruchomić [wznowić](#resume) akcji. Wcześniej obciążeń, które były uruchomione w węźle nie powrót po awarii.

Jeśli operacja zamknięcia nie powiedzie się, spróbuj [opróżnić](#drain) operacji następuje operacja zamknięcia.

Aby uruchomić akcję zamknięcia, otwórz wiersz PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie cmdlet:

  ```powershell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```



## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o module administratora usługi Azure Stack w sieci szkieletowej, zobacz [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0).
