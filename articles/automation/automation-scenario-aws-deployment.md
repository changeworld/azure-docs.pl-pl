---
title: Automatyzowanie wdrażania maszyny Wirtualnej na platformie usług Amazon Web Services
description: W tym artykule pokazano, jak zautomatyzować tworzenie maszyny Wirtualnej usługi Amazon Web przy użyciu usługi Azure Automation
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 348c28f6a2d72048e34f117e802abf243597b458
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60737643"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Scenariusz automatyzacji platformy Azure — aprowizowanie maszyny wirtualnej usług AWS
W tym artykule dowiesz się, jak dzięki usłudze Azure Automation do aprowizowania maszyny wirtualnej w ramach subskrypcji usługi Amazon Web Service (AWS) i nadaj tej maszyny Wirtualnej określonej nazwie — który AWS odnosi się do jako "" tagowania maszyny Wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne
Na potrzeby tego artykułu musisz mieć konto usługi Azure Automation i subskrypcja usługi AWS. Aby uzyskać więcej informacji na temat konfigurowania konta usługi Azure Automation i konfigurując go przy użyciu poświadczeń usługi AWS subskrypcji, przejrzyj [Konfigurowanie uwierzytelniania za pomocą usług Amazon Web Services](automation-config-aws-account.md). Tego konta należy utworzone lub zaktualizowane przy użyciu swoich poświadczeń usługi AWS subskrypcji, zanim przejdziesz dalej, jako odwołania do tego konta w poniższych krokach.

## <a name="deploy-amazon-web-services-powershell-module"></a>Wdrażanie modułu programu PowerShell usługi sieci Web Amazon
Inicjowanie obsługi administracyjnej runbook maszyny Wirtualnej korzysta z modułu programu PowerShell usługi AWS, aby wykonać swoją pracę. Wykonaj poniższe kroki, aby dodać moduł do konta usługi Automation, który jest skonfigurowany przy użyciu poświadczeń usługi AWS subskrypcji.  

1. Otwórz przeglądarkę internetową i przejdź do [galerii programu PowerShell](https://www.powershellgallery.com/packages/AWSPowerShell/) i kliknij pozycję **wdrażanie do usługi Azure Automation przycisku**.<br><br> ![Importowanie modułów PS usług AWS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Nastąpi przekierowanie do strony logowania do platformy Azure i po uwierzytelnieniu, będziesz kierowane do witryny Azure portal i wyświetlona następująca strona:<br><br> ![Importowanie modułu strony](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Wybierz konto usługi Automation, a następnie kliknij przycisk **OK** rozpocząć wdrażanie.

   > [!NOTE]
   > Podczas importowania modułu programu PowerShell do usługi Azure Automation, jego również wyodrębnia poleceń cmdlet, a działania te nie są wyświetlane do momentu moduł całkowitego zakończenia importowania i wyodrębniania poleceń cmdlet programu. Ten proces może potrwać kilka minut.  
   > <br>

1. W witrynie Azure portal Otwórz konto usługi Automation, do którego odwołuje się krok 3.
2. Kliknij **zasoby** Kafelek i **zasoby** okienku wybierz **modułów** kafelka.
3. Na **modułów** stronie zostanie wyświetlony **AWSPowerShell** modułu na liście.

## <a name="create-aws-deploy-vm-runbook"></a>Tworzenie usługi AWS wdrożenia maszyny Wirtualnej elementu runbook
Po wdrożeniu modułu programu PowerShell usługi AWS można teraz tworzyć elementu runbook, aby zautomatyzować aprowizację maszyny wirtualnej na platformie AWS, za pomocą skryptu programu PowerShell. Poniższe kroki pokazują, jak korzystać z natywnych skryptów programu PowerShell w usłudze Azure Automation.  

> [!NOTE]
> Aby uzyskać dalsze opcje i informacje dotyczące tego skryptu, odwiedź [galerii programu PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Pobierz skrypt programu PowerShell, New-AwsVM z galerii programu PowerShell, otwierając sesję programu PowerShell i wpisując następujące czynności:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. W witrynie Azure Portal otwórz konto usługi Automation, a następnie wybierz pozycję **elementów Runbook** sekcji **automatyzacji procesów** po lewej stronie.  
3. Z **elementów Runbook** wybierz opcję **Dodaj element runbook**.
4. Na **Dodaj element runbook** okienku wybierz **szybkie tworzenie** (Utwórz nowy element runbook).
5. Na **Runbook** w okienku właściwości, wpisz nazwę w polu Nazwa elementu runbook i z **typ elementu Runbook** listy rozwijanej wybierz **PowerShell**, a następnie kliknij przycisk **Tworzenie**.<br><br> ![Utwórz okienka elementu runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Gdy pojawi się Strona Edytuj element Runbook programu PowerShell, skopiuj i wklej skrypt programu PowerShell do tworzenia obszaru roboczego elementu runbook.<br><br> ![Skrypt programu PowerShell w elemencie Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Należy pamiętać, że podczas pracy z przykładowy skrypt programu PowerShell:
    > 
    > * Element runbook zawiera szereg domyślnych wartości parametrów. Oceń wszystkie wartości domyślne i aktualizacji, gdy jest to konieczne.
    > * Jeśli są przechowywane poświadczenia usługi AWS, jako zasób poświadczeń o nazwie inaczej niż **AWScred**, musisz zaktualizować ten skrypt w wierszu 57, aby dopasować odpowiednio.  
    > * Podczas pracy z usługą AWS poleceń interfejsu wiersza polecenia w programie PowerShell, szczególnie w przypadku tego przykładowego elementu runbook, należy określić region platformy AWS. W przeciwnym razie niepowodzenie polecenia cmdlet. Wyświetl AWS tematu [Określ Region platformy AWS](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) w narzędziach usługi AWS dla dokumentu programu PowerShell, aby uzyskać więcej informacji.  
    >

7. Pobieranie listy nazw obrazów z subskrypcji usługi AWS, uruchom program PowerShell ISE, a następnie zaimportuj moduł programu PowerShell usługi AWS. Uwierzytelnianie względem usługi AWS, zastępując **Get-AutomationPSCredential** w środowisku platformy ISE przy użyciu **AWScred = Get-Credential**. To wyświetli monit o podanie poświadczeń i można podać swoje **identyfikator klucza dostępu** nazwy użytkownika i **tajny klucz dostępu** hasła. Zapoznaj się z poniższym przykładem:  

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
        
    Następujące dane wyjściowe zostaną zwrócone:<br><br>
   ![Pobieranie obrazów usług AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Skopiuj i Wklej jeden z nazwy obrazów w zmiennej automatyzacji zgodnie z odwołaniem w elemencie runbook jako **$InstanceType**. Ponieważ w tym przykładzie są przy użyciu bezpłatnej usługi AWS warstwowego subskrypcji, możesz użyć **t2.micro** dla przykładu elementu runbook.  
9. Zapisz element runbook, a następnie kliknij przycisk **Publikuj** opublikować elementu runbook i następnie **tak** po wyświetleniu monitu.

### <a name="testing-the-aws-vm-runbook"></a>Testowanie elementu runbook maszyny Wirtualnej usług AWS
Przed przystąpieniem do testowania elementu runbook, należy sprawdzić kilka rzeczy. W szczególności:  

* Zasób do uwierzytelniania w odniesieniu do usług AWS została utworzona o nazwie **AWScred** lub skrypt został zaktualizowany, aby odwoływać się do nazwy zasobu swoje poświadczenia.    
* Moduł programu PowerShell usługi AWS został zaimportowany w usłudze Azure Automation  
* Utworzono nowy element runbook oraz wartości parametrów, została zweryfikowana i zaktualizowana, gdy jest to konieczne  
* **Rejestrowania rekordów pełnych** i opcjonalnie **rejestrowania rekordów postępu** w obszarze Ustawienia elementu runbook **rejestrowanie i śledzenie** zostały ustawione na **na**.<br><br> ![Element Runbook, rejestrowanie i śledzenie](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Aby uruchomić element runbook, więc klikamy **Start** a następnie kliknij przycisk **OK** po otwarciu okienka uruchamianie elementu Runbook.
2. W okienku uruchamianie elementu Runbook, podać **VMname**. Zaakceptuj wartości domyślne dla parametrów, wstępnie w skrypcie wcześniej. Kliknij przycisk **OK** można uruchomić zadania elementu runbook.<br><br> ![Uruchamianie elementu runbook New AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Zostanie otwarte okienko zadania zadanie elementu runbook, który został utworzony. Zamknąć to okienko.
4. Możesz przeglądać postęp danych wyjściowych zadań i widoku **strumieni** , wybierając **wszystkie dzienniki** kafelka na stronie zadania elementu runbook.<br><br> ![Dane wyjściowe Stream](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Aby upewnić się, że maszyna wirtualna jest aprowizowana, zaloguj się do konsoli zarządzania usług AWS, jeśli użytkownik nie jest aktualnie zalogowany.<br><br> ![Konsoli usług AWS wdrożonych maszyn wirtualnych](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Kolejne kroki
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
* Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
* Aby uzyskać więcej informacji o funkcji obsługi skryptów programu PowerShell, zobacz [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Obsługa natywnych skryptów programu PowerShell w usłudze Azure Automation).


