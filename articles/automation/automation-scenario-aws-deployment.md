---
title: Automatyzowanie wdrożenia maszyny wirtualnej w Amazon Web Services
description: W tym artykule pokazano, jak za pomocą Azure Automation zautomatyzować tworzenie maszyny wirtualnej usługi Amazon Web Service
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: b149a21ef60b22c2e549c91007265ce99babdbe4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420923"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Scenariusz Azure Automation — Inicjowanie obsługi administracyjnej maszyny wirtualnej AWS
W tym artykule dowiesz się, jak skorzystać z Azure Automation, aby zainicjować obsługę administracyjną maszyny wirtualnej w ramach subskrypcji usługi Amazon Web Service (AWS) i nadać maszynie wirtualnej konkretną nazwę, która AWS odnosi się do "tagowania".

## <a name="prerequisites"></a>Wymagania wstępne
Na potrzeby tego artykułu musisz mieć konto Azure Automation i subskrypcję usługi AWS. Aby uzyskać więcej informacji na temat konfigurowania konta Azure Automation i konfigurowania go przy użyciu poświadczeń subskrypcji usługi AWS, przejrzyj temat [Konfigurowanie uwierzytelniania przy użyciu Amazon Web Services](automation-config-aws-account.md). To konto należy utworzyć lub zaktualizować przy użyciu poświadczeń subskrypcji usługi AWS przed kontynuowaniem, ponieważ należy odwołać się do tego konta w poniższych krokach.

## <a name="deploy-amazon-web-services-powershell-module"></a>Wdróż moduł Amazon Web Services PowerShell
Element Runbook aprowizacji maszyn wirtualnych korzysta z modułu AWS PowerShell w celu wykonania jego pracy. Wykonaj następujące kroki, aby dodać moduł do konta usługi Automation, które skonfigurowano przy użyciu poświadczeń subskrypcji usługi AWS.  

1. Otwórz przeglądarkę internetową i przejdź do [Galeria programu PowerShell](https://www.powershellgallery.com/packages/AWSPowerShell/) i kliknij **przycisk Wdróż do Azure Automation**.<br><br> ![](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png) importowania modułu AWS PS
2. Nastąpi przekierowanie do strony logowania platformy Azure, a po uwierzytelnieniu zostanie nakierowany do Azure Portal i przedstawiony na następującej stronie:<br><br> ![Strona importowania modułu](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Wybierz konto usługi Automation, którego chcesz użyć, a następnie kliknij przycisk **OK** , aby rozpocząć wdrażanie.

   > [!NOTE]
   > Podczas importowania modułu programu PowerShell do Azure Automation są one również wyodrębniane z poleceń cmdlet, a te działania nie są wyświetlane, dopóki moduł nie zakończył procesu importowania i wyodrębniania poleceń cmdlet. Ten proces może potrwać kilka minut.  
   > <br>

1. W Azure Portal Otwórz konto usługi Automation, do którego istnieje odwołanie w kroku 3.
2. Kliknij kafelek **zasoby** i w okienku **zasoby** wybierz kafelek **moduły** .
3. Na stronie **moduły** na liście zostanie wyświetlony moduł **AWSPowerShell** .

## <a name="create-aws-deploy-vm-runbook"></a>Utwórz element Runbook AWS wdrożenia maszyny wirtualnej
Po wdrożeniu modułu AWS PowerShell możesz teraz utworzyć element Runbook, aby zautomatyzować Inicjowanie obsługi maszyny wirtualnej w programie AWS przy użyciu skryptu programu PowerShell. W poniższych krokach pokazano, jak korzystać z macierzystego skryptu programu PowerShell w Azure Automation.  

> [!NOTE]
> Aby uzyskać więcej opcji i informacji dotyczących tego skryptu, odwiedź [Galeria programu PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Pobierz skrypt programu PowerShell New-AwsVM z Galeria programu PowerShell, otwierając sesję programu PowerShell i wpisując następujące polecenie:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. W Azure Portal Otwórz konto usługi Automation i wybierz pozycję **elementy Runbook** w sekcji **Automatyzacja procesu** po lewej stronie.  
3. Na stronie **elementy Runbook** wybierz pozycję **Dodaj element Runbook**.
4. W okienku **Dodawanie elementu Runbook** wybierz pozycję **szybkie tworzenie** (Utwórz nowy element Runbook).
5. W okienku właściwości **elementu Runbook** wpisz nazwę w polu Nazwa dla elementu Runbook, a następnie z listy rozwijanej **Typ elementu Runbook** wybierz pozycję **PowerShell**, a następnie kliknij pozycję **Utwórz**.<br><br> ](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png) ![tworzenia okienka elementu Runbook
6. Gdy zostanie wyświetlona strona Edytowanie elementu Runbook programu PowerShell, skopiuj i wklej skrypt programu PowerShell do kanwy tworzenia elementów Runbook.<br><br> ![Skrypt programu PowerShell dla elementu Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Podczas pracy z przykładowym skryptem programu PowerShell należy pamiętać o następujących kwestiach:
    > 
    > * Element Runbook zawiera szereg domyślnych wartości parametrów. Oceń wszystkie wartości domyślne i zaktualizuj je w razie potrzeby.
    > * Jeśli poświadczenia AWS są przechowywane jako zasób poświadczeń o nazwie inaczej niż **AWScred**, należy zaktualizować skrypt w wierszu 57 w celu dopasowania odpowiednio.  
    > * W przypadku korzystania z poleceń interfejsu wiersza polecenia AWS w programie PowerShell, szczególnie w przypadku tego przykładowego elementu Runbook, należy określić region AWS. W przeciwnym razie polecenia cmdlet zakończą się niepowodzeniem. Aby uzyskać więcej informacji, zobacz temat AWS w temacie [Określanie regionu AWS](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) w dokumencie narzędzia AWS Tools for PowerShell.  
    >

7. Aby pobrać listę nazw obrazów z subskrypcji programu AWS, uruchom program PowerShell ISE i zaimportuj moduł AWS PowerShell. Uwierzytelnianie względem AWS przez zastępowanie **Get-AutomationPSCredential** w środowisku ISE za pomocą **AWScred = Get-Credential**. Zostanie wyświetlony komunikat z prośbą o podanie poświadczeń i podanie **identyfikatora klucza dostępu** dla nazwy użytkownika i **klucza dostępu tajnego** dla hasła. Zapoznaj się z poniższym przykładem:  

        ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
        ```
        
    Zwracane są następujące dane wyjściowe:<br><br>
   ![Pobierz obrazy AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Skopiuj i wklej jedną z nazw obrazów w zmiennej automatyzacji, która jest przywoływana w elemencie Runbook jako **$instanceType**. Ponieważ w tym przykładzie korzystasz z bezpłatnej subskrypcji AWS z warstwą, użyj **T2. Micro** dla przykładu elementu Runbook.  
9. Zapisz element Runbook, a następnie kliknij pozycję **Opublikuj** , aby opublikować element Runbook, a następnie przycisk **tak** po wyświetleniu monitu.

### <a name="testing-the-aws-vm-runbook"></a>Testowanie elementu Runbook maszyny wirtualnej AWS
Przed przejściem do testowania elementu Runbook należy sprawdzić kilka rzeczy. W szczególności:  

* Utworzono zasób do uwierzytelniania w usłudze AWS o nazwie **AWScred** lub skrypt został zaktualizowany w celu odwoływania się do nazwy zasobu poświadczeń.    
* Moduł AWS PowerShell został zaimportowany w Azure Automation  
* Utworzono nowy element Runbook, a wartości parametrów zostały zweryfikowane i zaktualizowane, gdy jest to konieczne.  
* **Rejestruj pełne rekordy** i opcjonalnie **Rejestruj rekordy postępu** w ustawieniach elementu Runbook **Rejestrowanie i śledzenie** zostały ustawione na wartość **włączone**.<br><br> ![rejestrowanie i śledzenie elementów Runbook](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Chcesz uruchomić element Runbook, a następnie kliknij przycisk **Start** , a następnie kliknij przycisk **OK** po otwarciu okienka uruchamiania elementu Runbook.
2. W okienku Uruchamianie elementu Runbook Podaj **VMName**. Zaakceptuj wartości domyślne dla innych parametrów wstępnie skonfigurowanych we wcześniejszym skrypcie. Kliknij przycisk **OK** , aby uruchomić zadanie elementu Runbook.<br><br> ![Uruchom nowy element Runbook AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Zostanie otwarte okienko zadań dla utworzonego zadania elementu Runbook. Zamknij to okienko.
4. Możesz wyświetlić postęp zadania i wyświetlić **strumienie** wyjściowe, wybierając kafelek **wszystkie dzienniki** na stronie zadania elementu Runbook.<br><br> ](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png) danych wyjściowych strumienia ![
5. Aby upewnić się, że maszyna wirtualna jest obsługiwana, zaloguj się do konsoli zarządzania AWS, jeśli obecnie nie jesteś zalogowany.<br><br> ![Wdrożona maszyna wirtualna w konsoli AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Następne kroki
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
* Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
* Aby uzyskać więcej informacji o funkcji obsługi skryptów programu PowerShell, zobacz artykuł [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Obsługa natywnych skryptów programu PowerShell w usłudze Azure Automation).


