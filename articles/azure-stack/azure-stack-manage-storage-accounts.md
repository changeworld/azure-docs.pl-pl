---
title: Zarządzanie kontami magazynu Azure stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak znaleźć, zarządzania, odzyskiwania i odzyskania konta magazynu Azure stosu
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 2ae2b628b2e61893a5289151c3b405e7412e7d13
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076934"
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Zarządzanie kontami magazynu Azure stosu
Dowiedz się, jak zarządzać kontami magazynu Azure stosu, odzyskiwanie, a odzyskać pojemność magazynu, w zależności od potrzeb biznesowych.

## <a name="find"></a>Znajdź konto magazynu
Lista kont magazynu w regionie można wyświetlić w stosie Azure przez:

1. W przeglądarce sieci Web, przejdź do https://adminportal.local.azurestack.external.
2. Zaloguj się do portalu administracyjnego stosu Azure jako chmury operatora (przy użyciu poświadczeń, których podana podczas wdrażania)
3. Na pulpicie nawigacyjnym domyślny — Znajdź **zarządzania Region** listę i wybierz region chcesz się dowiedzieć, na przykład **(lokalnego**).
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. Wybierz **magazynu** z **dostawców zasobów** listy.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. Teraz, w okienku administrator dostawcy zasobów magazynu — przewiń w dół do **kont magazynu** i wybierz go.
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   Wynikowa strona jest lista kont magazynu w tym regionie.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

Domyślnie 10 pierwszych konta są wyświetlane. Możesz pobrać więcej klikając **załadować więcej** łącze umieszczone u dołu listy.

LUB

Jeśli interesuje Cię na koncie magazynu określonym — możesz **filtrowania i pobrać odpowiednie konta** tylko.


**Aby filtrować kont:**

1. Wybierz **filtru** u góry okienka.
2. W okienku filtru go służy do określenia **nazwa konta**, ** identyfikator subskrypcji lub **stan** można dostosować listę kont magazynu, który będzie wyświetlany. Użyj ich, zależnie od potrzeb.
3. Wybierz **aktualizacji**. W związku z tym odświeżenia listy.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. Aby zresetować filtr: Wybierz **filtru**, wyczyszczenie zaznaczenia i aktualizacji.

Pole tekstowe wyszukiwania (w górnej części okienka listy kont magazynu) umożliwia wyróżnianie tekstu zaznaczonego w listy kont. Umożliwia to, gdy pełna nazwa lub identyfikator nie jest łatwo dostępny.

Dowolny tekst tutaj służy do znajdowania konta, które planuje się.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Sprawdź szczegóły konta
Po przejściu do kont, które jest wyświetlane, można wybrać określonego konta, aby wyświetlić niektóre szczegóły. Nowe okienko Otwiera szczegóły konta, takich jak: typ konta, czas utworzenia, lokalizacji itp.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Odzyskiwanie usuniętego konta
Można w sytuacji, gdy trzeba przywrócić usuniętego konta.

W stosie Azure jest prosty sposób, w tym:

1. Przejdź do listy kont magazynu. Zobacz [znaleźć konta magazynu](#find) w tym temacie, aby uzyskać więcej informacji.
2. Znajdź tego konkretnego konta na liście. Konieczne może być filtru.
3. Sprawdź *stanu* konta. Powinny przekazać komunikat **usunięte**.
4. Wybierz konto, które umożliwia otwarcie okienka szczegółów konta.
5. U góry w tym okienku, zlokalizuj **odzyskać** i wybrać je.
6. Wybierz **tak** o potwierdzenie.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. Odzyskiwanie jest teraz w *... przetworzyć oczekiwania* dla wskazanie, że nie powiodło się.
   Można również wybrać ikonę "dzwonka" w górnej części portalu, aby wyświetlić postęp oznaczenia.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Po pomyślnie jest synchronizowane konta odzyskane, mogą ponownie używane.

### <a name="some-gotchas"></a>Niektóre pytań
* Twoje konto usunięte pokazuje stan jako **poza przechowywania**.
  
  Poza sposób przechowywania czy usuniętego konta przekroczył okres przechowywania i może nie być możliwe do odzyskania.
* Usunięto konta nie są wyświetlane na liście kont.
  
  Twoje konto nie mogą być wyświetlane na liście kont podczas usuniętego konta została już bezużytecznych. W takim przypadku nie można odzyskać. Zobacz [odzyskiwania pojemności](#reclaim) w tym temacie.

## <a name="set-the-retention-period"></a>Ustaw okres przechowywania
Ustawienie okresu przechowywania umożliwia operatorowi chmury określić okres czasu w dni (od 0 do 9999 dni), w których wszystkie usuniętego konta potencjalnie mogą zostać odzyskane. Domyślny okres przechowywania wynosi 0 dni. Ustawienie wartości "0" oznacza, że wszystkie konta usuniętego natychmiast Brak przechowywania i oznaczone do okresowego wyrzucanie elementów bezużytecznych.

**Aby zmienić okres przechowywania:**

1. W przeglądarce sieci Web, przejdź do https://adminportal.local.azurestack.external.
2. Zaloguj się do portalu administracyjnego stosu Azure jako chmury operatora (przy użyciu poświadczeń, których podana podczas wdrażania)
3. Na pulpicie nawigacyjnym domyślny — Znajdź **zarządzania Region** listę i wybierz region, aby eksplorować — na przykład **(lokalnego**).
4. Wybierz **magazynu** z **dostawców zasobów** listy.
5. Wybierz **ustawienia** u góry, aby otworzyć okienko ustawienia.
6. Wybierz **konfiguracji** następnie Edytuj wartość okresu przechowywania.

   Ustaw liczbę dni, a następnie zapisz go.
   
   Ta wartość natychmiast zaczyna się i ustawiono dla całego regionu.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Odzyskiwania pojemności
Jednym z efektami ubocznymi mające okres przechowywania jest usuniętego konta nadal zużywają pojemności do momentu zakończenia poza okresem przechowywania. Jako operatorowi chmury może być konieczne sposób, aby odzyskać miejsce usuniętego konta, mimo że nie upłynął jeszcze okresu przechowywania.

Można odzyskać pojemności za pomocą portalu lub programu PowerShell.

**Aby odzyskać pojemności przy użyciu portalu:**
1. Przejdź do okienka kont magazynu. Zobacz [znaleźć konta magazynu](#find).
2. Wybierz **odzyskać miejsce** u góry okienka.
3. Przeczytaj komunikat, a następnie wybierz **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Poczekaj na powodzenie powiadomień można znaleźć w portalu ikonę dzwonka.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Odśwież stronę, konta magazynu. Usunięte konta nie są wyświetlane na liście, ponieważ zostały usunięte.

Można również jawnie przesłonić okres przechowywania przy użyciu programu PowerShell i natychmiast odzyskiwania pojemności.

**Aby odzyskać pojemności przy użyciu programu PowerShell:**   

1. Upewnij się, że masz programu Azure PowerShell zainstalowana i skonfigurowana. Jeśli nie, użyj poniższych instrukcji: 
   * Aby zainstalować najnowszą wersję programu Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Aby uzyskać więcej informacji na temat poleceń cmdlet usługi Azure Resource Manager, zobacz [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Uruchom następujące polecenia cmdlet:

> [!NOTE]
> Po uruchomieniu tych poleceń cmdlet należy trwale usunąć konta i jego zawartość. Nie jest możliwe do odzyskania. Użyj tego ostrożność.

```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
````

Aby uzyskać więcej informacji, zobacz [dokumentacji programu PowerShell usługi Azure stosu.](https://msdn.microsoft.com/library/mt637964.aspx)
 

## <a name="next-steps"></a>Kolejne kroki

 - Informacje dotyczące zarządzania uprawnieniami sekcji [kontroli dostępu Manage Role-Based](azure-stack-manage-permissions.md).
 - Uzyskać informacji o pojemności Zarządzaj stosu Azure, zobacz [Zarządzanie pojemność magazynu dla usługi Azure stosu](azure-stack-manage-storage-shares.md).