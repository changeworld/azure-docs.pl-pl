---
title: Zarządzanie kontami magazynu usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak znaleźć, zarządzanie, odzyskiwanie i odzyskiwaniu kont magazynu usługi Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: ceb092192924b79f1584d72b307e3bc3512c0025
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484173"
---
# <a name="manage-azure-stack-storage-accounts"></a>Zarządzanie kontami magazynu usługi Azure Stack

Dowiedz się, jak zarządzać kontami magazynu usługi Azure Stack. Znajdź, odzyskiwania i odzyskać pojemność magazynu na podstawie potrzeb biznesowych.

## <a name="find-a-storage-account"></a>Znajdź konto magazynu

Lista kont magazynu w regionie mogą być wyświetlane w usłudze Azure Stack przez:

1. Zaloguj się do [portalu administracyjnego](https://adminportal.local.azurestack.external).

2. Wybierz **wszystkich usług** > **magazynu** > **kont magazynu**.

   ![Konta magazynu w usłudze Azure Stack](media/azure-stack-manage-storage-accounts/image4.png)

Domyślnie są wyświetlane pierwsze 10 kont. Istnieje możliwość pobrania więcej, klikając **Załaduj więcej** link w dolnej części listy.

LUB

Jeśli interesują Cię określone konto magazynu — możesz to zrobić **filtrowania i pobrać odpowiednie konta** tylko.


**Aby odfiltrować konta:**

1. Wybierz **filtru** u góry okienka.
2. Na okienka filtru umożliwia określanie **nazwa konta**, **identyfikator subskrypcji**, lub **stan** można dostosować listę kont magazynu, które mają być wyświetlane. Ich używać zgodnie z potrzebami.
3. Podczas wpisywania lista automatycznie zastosuje filtr.  .
   
    ![Filtruj kont magazynu usługi Azure Stack](media/azure-stack-manage-storage-accounts/image5.png)

4. Aby zresetować filtr: Wybierz **filtru**, wyczyść wybrane elementy i aktualizacji.

Pole tekstowe wyszukiwania (w górnej części okienka listy kont magazynu) umożliwia wyróżnianie zaznaczonego tekstu na liście kont. Umożliwia to, gdy pełna nazwa lub identyfikator nie jest łatwo dostępny.

Dowolny tekst w tym miejscu służy do znajdowania kont, który Cię interesuje.

![Znajdowanie konta magazynu usługi Azure Stack](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Przyjrzyj się szczegóły konta
Po zlokalizowaniu kont, które Cię interesują wyświetlania można wybrać określone konto, aby wyświetlić niektóre szczegóły. Nowe okienko otwiera z szczegóły konta, takie jak: typ konta, czas utworzenia, lokalizacji itp.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Odzyskiwanie usuniętego konta
Może być w sytuacji, gdy trzeba odzyskać usuniętego konta.

W usłudze Azure Stack Brak najprościej można to zrobić:

1. Przejdź do listy kont magazynu. Zobacz Znajdź konto magazynu, w tym artykule, aby uzyskać więcej informacji.
2. Znajdź tego konkretnego konta na liście. Może być konieczne do filtrowania.
3. Sprawdź *stanu* konta. Powinna być widoczna nazwa **usunięte**.
4. Wybierz konto, co spowoduje otwarcie okienka szczegółów konta.
5. Na górze w tym okienku Znajdź **odzyskać** przycisku i wybierz ją.
6. Wybierz pozycję **Tak**, aby potwierdzić.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. Odzyskiwanie jest teraz w *proces... Czekaj* dla wskazanie, czy zakończyła się pomyślnie.
   Można również wybrać ikonę "dzwonka" w górnej części portalu, aby wyświetlić postęp oznaczeń.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Po pomyślnym zsynchronizowaniu odzyskane konta mogą ponownie używane.

### <a name="some-gotchas"></a>Niektóre pytania dotyczące usługi
* Twoje konto usunięte pokazuje stan jako **poza przechowywania**.
  
  Poza przechowywania oznacza, że że usuniętego konta przekroczył okres przechowywania i nie może być możliwe do odzyskania.
* Twoje konto usunięte nie są wyświetlane na liście kont.
  
  Konta nie mogą być wyświetlane na liście kont, gdy usuniętego konta została już bezużyteczne. W tym przypadku nie można odzyskać. Zobacz [odzyskać pojemności](#reclaim) w tym artykule.

## <a name="set-the-retention-period"></a>Ustaw okres przechowywania
Ustawienie okresu zachowywania umożliwia operatorowi chmury określić okres czasu w dniach (od 0 do 9999 dni), podczas których potencjalnie można odzyskać wszystkie usunięte konto. Domyślny okres przechowywania jest ustawiona na wartość 0 dni. Ustawienie wartości na "0" oznacza, że wszystkie usunięte konto jest natychmiast poza przechowywania i oznaczone do okresowego wyrzucania elementów bezużytecznych.

**Aby zmienić okres przechowywania:**

1. Zaloguj się do [portalu administracyjnego](https://adminportal.local.azurestack.external).
2. Wybierz **wszystkich usług** > **zarządzanie regionami** w obszarze **administracji**.
3. Wybierz **dostawców zasobów** > **magazynu** > **ustawienia**. Ścieżka jest strona główna > *region* — dostawcy zasobów > magazynu.
4. Wybierz **konfiguracji** następnie zmodyfikuj wartość okresu przechowywania.

   Ustaw liczbę dni, a następnie zapisz go.
   
   Ta wartość natychmiast zaczyna się i ustawiono dla całego regionu.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Odzyskaj pojemności
Jednym z efekty uboczne mające okres przechowywania jest, że usunięte konto w dalszym ciągu korzystać pojemności, dopóki nie pochodzi z okresu przechowywania. Operator chmury może być konieczne sposobem odzyskania miejsca usuniętego konta, nawet jeśli nie upłynął jeszcze okres przechowywania.

Można odzyskać pojemność przy użyciu portalu lub programu PowerShell.

**Aby odzyskać pojemność przy użyciu portalu:**
1. Przejdź do okienka kont magazynu. Zobacz Znajdź konto magazynu.
2. Wybierz **odzyskać miejsce** u góry okienka.
3. Przeczytaj komunikat, a następnie wybierz pozycję **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Poczekaj, aż powiadomienie o powodzeniu Zobacz ikonę dzwonka w portalu.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Odśwież stronę kont magazynu. Usuniętego konta nie są wyświetlane na liście, ponieważ zostały usunięte.

Można także jawnie przesłonić okres przechowywania przy użyciu programu PowerShell, a następnie natychmiast odzyskać pojemności.

**Aby odzyskać pojemność przy użyciu programu PowerShell:**   

1. Upewnij się, że programu Azure PowerShell zainstalowany i skonfigurowany. W przeciwnym razie użyj poniższych instrukcji: 
   * Aby zainstalować najnowszą wersję programu Azure PowerShell i skojarzyć je z subskrypcją platformy Azure, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Aby uzyskać więcej informacji o poleceniach cmdlet usługi Azure Resource Manager, zobacz [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](https://go.microsoft.com/fwlink/?LinkId=394767)
2. Uruchom następujące polecenia cmdlet:

> [!NOTE]  
> Po uruchomieniu tych poleceń cmdlet, możesz trwale usunąć to konto i jego zawartość. Nie jest możliwe do odzyskania. To należy używać ostrożnie.

```powershell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

Aby uzyskać więcej informacji, zobacz [dokumentacji usługi Azure Stack PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
 

## <a name="next-steps"></a>Kolejne kroki

 - Instrukcje dotyczące zarządzania uprawnieniami zobacz [kontroli dostępu Manage Role-Based](azure-stack-manage-permissions.md).
 - Aby uzyskać informacji na temat pojemności magazynu zarządzania dla usługi Azure Stack, zobacz [Zarządzanie pojemnością magazynu dla usługi Azure Stack](azure-stack-manage-storage-shares.md).
