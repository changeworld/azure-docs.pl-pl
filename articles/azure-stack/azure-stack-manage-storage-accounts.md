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
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: anirudha
ms.openlocfilehash: ce61dec785d6207e2d2df21884525f76a6778f4a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Zarządzanie kontami magazynu Azure stosu
Dowiedz się, jak zarządzać kontami magazynu Azure stosu, odzyskiwanie, a odzyskać pojemność magazynu, w zależności od potrzeb biznesowych.

## <a name="find"></a>Znajdź konto magazynu
Lista kont magazynu w regionie można wyświetlić w stosie Azure przez:

1. W przeglądarce sieci Web, przejdź do https://adminportal.local.azurestack.external.
2. Zaloguj się do portalu administracyjnego stosu Azure jako chmury operatora (przy użyciu poświadczeń, których podana podczas wdrażania)
3. Na pulpicie nawigacyjnym domyślny — Znajdź **zarządzania Region** listy i kliknij region chcesz się dowiedzieć, na przykład **(lokalnego**).
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. Wybierz **magazynu** z **dostawców zasobów** listy.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. Teraz, w okienku administrator dostawcy zasobów magazynu — przewiń w dół do **kont magazynu** i kliknij ją.
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   Wynikowa strona jest lista kont magazynu w tym regionie.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

Domyślnie 10 pierwszych konta są wyświetlane. Możesz pobrać więcej klikając **załadować więcej** łącze umieszczone u dołu listy.

LUB

Jeśli interesuje Cię na koncie magazynu określonym — możesz **filtrowania i pobrać odpowiednie konta** tylko.


**Aby filtrować kont:**

1. Kliknij przycisk **filtru** u góry okienka.
2. W okienku filtru go służy do określenia **nazwa konta**, ** identyfikator subskrypcji lub **stan** można dostosować listę kont magazynu, który będzie wyświetlany. Użyj ich, zależnie od potrzeb.
3. Kliknij przycisk **aktualizacji**. W związku z tym odświeżenia listy.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. Aby zresetować filtr: kliknij **filtru**, wyczyszczenie zaznaczenia i aktualizować.

Pole tekstowe wyszukiwania (w górnej części okienka listy kont magazynu) umożliwia wyróżnianie tekstu zaznaczonego w listy kont. Umożliwia to, gdy pełna nazwa lub identyfikator nie jest łatwo dostępny.

Dowolny tekst tutaj służy do znajdowania konta, które planuje się.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Sprawdź szczegóły konta
Po przejściu do kont, które jest wyświetlane, możesz kliknąć szczególną uwagę, aby wyświetlić niektóre szczegóły. Nowe okienko Otwiera szczegóły konta, takich jak: typ konta, czas utworzenia, lokalizacji itp.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Odzyskiwanie usuniętego konta
Można w sytuacji, gdy trzeba przywrócić usuniętego konta.

W stosie Azure jest prosty sposób, w tym:

1. Przejdź do listy kont magazynu. Zobacz [znaleźć konta magazynu](#find) w tym temacie, aby uzyskać więcej informacji.
2. Znajdź tego konkretnego konta na liście. Konieczne może być filtru.
3. Sprawdź *stanu* konta. Powinny przekazać komunikat **usunięte**.
4. Kliknij konto, które umożliwia otwarcie okienka szczegółów konta.
5. U góry w tym okienku, zlokalizuj **odzyskać** przycisk i kliknij ją.
6. Kliknij przycisk **Tak**, aby potwierdzić.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. Odzyskiwanie jest teraz w *... przetworzyć oczekiwania* dla wskazanie, że nie powiodło się.
   Można również kliknąć ikonę "dzwonka" w górnej części portalu, aby wyświetlić postęp oznaczenia.
   
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
3. Na pulpicie nawigacyjnym domyślny — Znajdź **zarządzania Region** listy i kliknij go, aby eksplorować — na przykład **(lokalnego**).
4. Wybierz **magazynu** z **dostawców zasobów** listy.
5. Kliknij przycisk **ustawienia** u góry, aby otworzyć okienko ustawienia.
6. Kliknij przycisk **konfiguracji** następnie Edytuj wartość okresu przechowywania.

   Ustaw liczbę dni, a następnie zapisz go.
   
   Ta wartość natychmiast zaczyna się i ustawiono dla całego regionu.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Odzyskiwania pojemności
Jednym z efektami ubocznymi mające okres przechowywania jest usuniętego konta nadal zużywają pojemności do momentu zakończenia poza okresem przechowywania. Jako operatorowi chmury może być konieczne sposób, aby odzyskać miejsce usuniętego konta, mimo że nie upłynął jeszcze okresu przechowywania.

Można odzyskać pojemności za pomocą portalu lub programu PowerShell.

**Aby odzyskać pojemności przy użyciu portalu:**
1. Przejdź do okienka kont magazynu. Zobacz [znaleźć konta magazynu](#find).
2. Kliknij przycisk **odzyskać miejsce** u góry okienka.
3. Przeczytaj komunikat, a następnie kliknij przycisk **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Poczekaj na powodzenie powiadomień można znaleźć w portalu ikonę dzwonka.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Odśwież stronę, konta magazynu. Usunięte konta nie są wyświetlane na liście, ponieważ zostały usunięte.

Można również jawnie przesłonić okres przechowywania przy użyciu programu PowerShell i natychmiast odzyskiwania pojemności.

**Aby odzyskać pojemności przy użyciu programu PowerShell:**   

1. Upewnij się, że masz programu Azure PowerShell zainstalowana i skonfigurowana. Jeśli nie, użyj poniższych instrukcji: 
   * Aby zainstalować najnowszą wersję programu Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Aby uzyskać więcej informacji na temat poleceń cmdlet usługi Azure Resource Manager, zobacz [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Uruchom następujące polecenie cmdlet:

> [!NOTE]
> Po uruchomieniu tego polecenia cmdlet należy trwale usunąć konta i jego zawartość. Nie jest możliwe do odzyskania. Użyj tego ostrożność.


        Clear-ACSStorageAccount -ResourceGroupName system.local -FarmName <farm ID>


Aby uzyskać więcej informacji, zobacz [dokumentacji programu powershell Azure stosu.](https://msdn.microsoft.com/library/mt637964.aspx)
 

## <a name="migrate-a-container"></a>Migrowanie kontenera
Ze względu na użycie magazynu nierówna przez dzierżawców operatorowi chmury mogą znaleźć jednego lub więcej podstawowych dzierżawy udziałów, za pomocą więcej miejsca niż inne. W takim przypadku operatorowi chmury mogą próbować Zwolnij miejsce w udziale korzystającym za pomocą ręcznie migracji niektórych kontenerów obiektów blob do innego udziału. 

Należy użyć programu PowerShell do migracji kontenerów.
> [!NOTE]
>Migracja kontenera obiektów blob nie obsługuje migracji na żywo i jest obecnie w trybie offline operacji. Podczas migracji i do czasu ukończenia podstawowej obiektów blob w tym kontenerze nie można użyć "offline". 

**Aby przeprowadzić migrację kontenerów przy użyciu programu PowerShell:**

1. Upewnij się, że masz programu Azure PowerShell zainstalowana i skonfigurowana. Jeśli nie, użyj poniższych instrukcji:
    * Aby zainstalować najnowszą wersję programu Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/). Aby uzyskać więcej informacji na temat poleceń cmdlet usługi Azure Resource Manager, zobacz [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Uzyskaj nazwy farmy: 
      
      `$farm = Get-ACSFarm -ResourceGroupName system.local`
3. Pobierz akcji: 

   `$shares = Get-ACSShare -ResourceGroupName system.local -FarmName $farm.FarmName`

4. Pobierz kontenerów dla danego udziału. Należy pamiętać, że liczba i przeznaczeniu są następujące parametry opcjonalne:
            
   `$containers = Get-ACSContainer -ResourceGroupName system.local -FarmName $farm.FarmName -ShareName $shares[0].ShareName -Count 4 -Intent Migration`  

   Następnie sprawdź $containers:

   `$containers`

    ![](media/azure-stack-manage-storage-accounts/image13.png)
5. Uzyskać najlepsze udziałów docelowy dla migracji kontenera:

    `$destinationshares= Get-ACSSharesForMigration  -ResourceGroupName system.local -FarmName $farm.farmname -SourceShareName $shares[0].ShareName`

    Następnie sprawdź $destinationshares:

    `$destinationshares`

    ![](media/azure-stack-manage-storage-accounts/image14.png)
6. Rozpocząć poza migracji dla kontenera, należy zauważyć, że jest implementacja asynchroniczne, dlatego jeden pętli wszystkich kontenerów w udziale i śledzenie stanu przy użyciu identyfikatora zwrócone zadania.

    `$jobId = Start-ACSContainerMigration -ResourceGroupName system.local -FarmName $farm.farmname -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares.UncPath`

    Następnie sprawdź $jobId:

   ```
   $jobId
   d1d5277f-6b8d-4923-9db3-8bb00fa61b65
   ```
7. Sprawdź stan zadania migracji przez jego identyfikator zadania. Po zakończeniu migracji kontenera MigrationStatus ma ustawioną wartość "Completed".

    `Get-ACSContainerMigrationStatus -ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image15.png)

8. Możesz anulować zadanie migracji w toku. Ponownie jest operacją asynchroniczną i mogą być śledzone za pomocą $jobid:

    `Stop-ACSContainerMigration-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId-Verbose`

    ![](media/azure-stack-manage-storage-accounts/image16.png)

    Można sprawdzić, czy stany migracji ponownie anulować:

    `Get-ACSContainerMigrationStatus-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image17.png)




  
  
