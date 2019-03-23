---
title: Wdrażanie usługi Azure Stack App Service w konfiguracji o wysokiej dostępności | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć usługi App Service w usłudze Azure Stack przy użyciu konfiguracji o wysokiej dostępności.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: ''
ms.date: 03/23/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 1c105548f19994c4ca0ce161eedcfe11736864c7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370027"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>Wdrażanie usługi App Service w konfiguracji wysokiej dostępności

W tym artykule pokazano, jak wdrożyć usługę aplikacji dla usługi Azure Stack w konfiguracji o wysokiej dostępności przy użyciu elementów portalu marketplace usługi Azure Stack. Oprócz elementów portalu marketplace dostępne, to rozwiązanie korzysta z [appservice-udziału plików — sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) szablonu szybkiego startu platformy Azure Stack. Ten szablon umożliwia zautomatyzowanie tworzenie wysoce dostępną infrastrukturę do obsługi dostawcy zasobów usługi App Service. Usługa App Service jest instalowany na tym wysoce dostępną infrastrukturę maszyny Wirtualnej. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>Wdrażanie wysoce dostępną infrastrukturę usługi App Service maszyn wirtualnych
[Appservice-udziału plików — sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) szablonu szybkiego startu platformy Azure Stack upraszcza wdrażanie usługi App Service w konfiguracji wysokiej dostępności. Powinny zostać wdrożone w subskrypcji dostawcy domyślne. 

W przypadku użycia, aby utworzyć zasób niestandardowy w usłudze Azure Stack, ten szablon tworzy:
- Sieć wirtualna i podsieci wymagana.
- Sieciowe grupy zabezpieczeń dla serwera plików, programu SQL Server i podsieci usługi Active Directory Domain Services (AD DS).
- Konta magazynu dla dysków maszyny Wirtualnej i monitora klastra w chmurze.
- Jeden wewnętrzny moduł równoważenia obciążenia dla maszyn wirtualnych SQL z prywatnym adresem IP, powiązany z odbiornikiem funkcji SQL AlwaysOn.
- Trzy zestawy dostępności dla usług AD DS, klaster serwera plików i klastra SQL.
- Dwoma węzłami klastra SQL.
- Dwoma węzłami klastra serwera plików.
- Dwóch kontrolerów domeny.

### <a name="required-azure-stack-marketplace-items"></a>Wymagane elementy portalu marketplace usługi Azure Stack
Przed rozpoczęciem korzystania z tego szablonu, upewnij się, że następujące [elementów portalu marketplace usługi Azure Stack](azure-stack-marketplace-azure-items.md) są dostępne w Twoim wystąpieniu usługi Azure Stack:

- Obraz Core systemu Windows Server 2016 Datacenter (dla usługi AD DS i serwerów plików maszyn wirtualnych)
- SQL Server 2016 z dodatkiem SP2 w systemie Windows Server 2016 (dla wersji Enterprise)
- Najnowsze rozszerzenie programu SQL IaaS 
- Najnowszą wersję programu PowerShell Desired State Configuration rozszerzenia 

> [!TIP]
> Przegląd [plik readme szablonu](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) w serwisie GitHub, aby uzyskać więcej informacji o wymaganiach szablonu i wartości domyślne. 

### <a name="deploy-the-app-service-infrastructure"></a>Wdrażanie infrastruktury usługi App Service
Wykonaj kroki w tej sekcji, aby utworzyć niestandardowe wdrożenie przy użyciu **appservice-udziału plików — sqlserver-ha** szablonu szybkiego startu platformy Azure Stack.

1. [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. Wybierz **\+** **Utwórz zasób** > **niestandardowe**, a następnie **wdrożenie szablonu**.

   ![Wdrożenie szablonu niestandardowego](media/app-service-deploy-ha/1.png)


3. Na **wdrożenie niestandardowe** bloku wybierz **Edytuj szablon** > **szablon szybkiego startu** , a następnie użyj listy rozwijanej dostępnych szablonów niestandardowych, aby Wybierz **appservice-udziału plików — sqlserver-ha** szablonu, kliknij przycisk **OK**, a następnie **Zapisz**.

   ![Wybierz szablon usługi App Service — udziału plików — sqlserver-ha Szybki Start](media/app-service-deploy-ha/2.png)

4. Na **wdrożenie niestandardowe** bloku wybierz **Edytuj parametry** i przewiń w dół do przeglądu wartości szablonu domyślnego. Zmodyfikuj te wartości, co jest niezbędne do zapewnienia wszystkie wymagane parametry, a następnie kliknij przycisk **OK**.<br><br> Jako minimum Podaj złożonych haseł ADMINPASSWORD FILESHAREOWNERPASSWORD, FILESHAREUSERPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD i SQLLOGINPASSWORD parametrów.
    
   ![Edytuj parametry wdrożenia niestandardowego](media/app-service-deploy-ha/3.png)

5. Na **wdrożenie niestandardowe** bloku, upewnij się, **domyślne subskrypcję dostawcy** jest wybrany jako subskrypcję, której chcesz użyć, a następnie utwórz nową grupę zasobów lub wybierz istniejącą grupę zasobów dla niestandardowego wdrożenie.<br><br> Następnie wybierz lokalizację grupy zasobów (**lokalnego** dla instalacji ASDK) a następnie kliknij przycisk **Utwórz**. Niestandardowe ustawienia wdrażania zostaną zweryfikowane, przed rozpoczęciem wdrażania szablonu.

    ![Tworzenie wdrożenia niestandardowego](media/app-service-deploy-ha/4.png)

6. W portalu administracyjnym wybierz **grup zasobów** i następnie nazwę grupy zasobów utworzoną w ramach wdrożenia niestandardowego (**app-service-ha** w tym przykładzie). Wyświetl stan wdrożenia, aby upewnić się, że wszystkie wdrożenia zostały ukończone pomyślnie.

   > [!NOTE]
   > Wdrożenie szablonu zostanie zająć około godziny.

   [![](media/app-service-deploy-ha/5-sm.png "Sprawdź stan wdrożenia szablonu")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>Dane wyjściowe szablonu rekordu
Po szablonie wdrażania zakończy się pomyślnie, rekord, który generuje wdrożenia szablonu. Należy podać te informacje podczas uruchamiania Instalatora usługi App Service. 

Upewnij się, że rejestrowania każdej z tych wartości danych wyjściowych:
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

Wykonaj następujące kroki, aby odnaleźć wartości danych wyjściowych szablonu:

1. [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. W portalu administracyjnym wybierz **grup zasobów** i następnie nazwę grupy zasobów utworzoną w ramach wdrożenia niestandardowego (**app-service-ha** w tym przykładzie). 

3. Kliknij przycisk **wdrożeń** i wybierz **Microsoft.Template**.

    ![Microsoft.Template wdrożenia](media/app-service-deploy-ha/6.png)

4. Po wybraniu **Microsoft.Template** wdrażania, wybór **dane wyjściowe** i Zapisz dane wyjściowe parametru szablonu. Te informacje będą wymagane podczas wdrażania usługi App Service.

    ![Parametr wyjściowy](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>Wdrażanie usługi App Service w konfiguracji wysokiej dostępności
Postępuj zgodnie z instrukcjami w tej sekcji, aby wdrożyć usługę aplikacji dla usługi Azure Stack w konfiguracji o wysokiej dostępności, na podstawie [appservice-udziału plików — sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) szablonu szybkiego startu platformy Azure Stack. 

Po zainstalowaniu dostawcy zasobów usługi App Service, można uwzględnić go w swojej oferty i plany. Użytkownicy mogą następnie subskrybować Uzyskaj usługi i zacznij tworzyć aplikacje.

> [!IMPORTANT]
> Przed uruchomieniem Instalatora dostawcy zasobów, upewnij się, że użytkownik przeczytał informacje o wersji, decydując się na każdej wersji usługi App Service, aby dowiedzieć się więcej na temat nowych funkcji, poprawek i znanych problemach, które mogą mieć wpływ na wdrożenie.

### <a name="prerequisites"></a>Wymagania wstępne
Przed uruchomieniem Instalatora usługi App Service, kilka czynności są wymagane, zgodnie z opisem w [przed rozpoczęciem pracy z usługą App Service w usłudze Azure Stack artykule](azure-stack-app-service-before-you-get-started.md):

> [!TIP]
> Nie wszystkie kroki opisane w przed rozpoczęciem pracy artykule są wymagane, ponieważ deploymnet szablonu konfiguruje infrastrukturą maszyn wirtualnych. 

- [Pobieranie skryptów Instalatora i pomocnika usługi App Service](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts).
- [Pobierz najnowsze rozszerzenia niestandardowego skryptu do witryny marketplace usługi Azure Stack](azure-stack-app-service-before-you-get-started.md#syndicate-the-custom-script-extension-from-the-marketplace).
- [Generowanie certyfikatów wymaganych](azure-stack-app-service-before-you-get-started.md#get-certificates).
- Utwórz aplikację identyfikator oparte na dostawcy identyfikowanie, wybrany dla usługi Azure Stack. Identyfikator aplikacji można przewidzieć albo [usługi Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-application) lub [Active Directory Federation Services](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-application) i zarejestruj identyfikator aplikacji.
- Upewnij się, że dodano obrazu systemu Windows Server 2016 Datacenter w portalu Marketplace usługi Azure Stack. Jest to wymagane do instalacji usługi App Service.

### <a name="deploy-app-service-in-highly-available-configuration"></a>Wdrażanie usługi App Service w konfiguracji wysokiej dostępności
Instalowanie dostawcy zasobów usługi App Service ma co najmniej godzinę. Czas potrzebny jest zależny od roli liczbę wystąpień, możesz wdrożyć. Podczas wdrażania program Instalator uruchamia następujące zadania:

- Utwórz kontener obiektów blob w ramach określonego konta magazynu usługi Azure Stack.
- Tworzenie strefy DNS i wpisy dla usługi App Service.
- Zarejestruj dostawcę zasobów usługi App Service.
- Zarejestruj elementy galerii usługi App Service.

Aby wdrożyć dostawcy zasobów usługi App Service, wykonaj następujące kroki:

1. Uruchom wcześniej pobrany program instalacyjny usługi App Service (**appservice.exe**) jako administrator z poziomu komputera, które mogą uzyskiwać dostęp do usługi Azure Stack administratora Azure zasobu punktu końcowego zarządzania.

2. Wybierz **wdrażanie usługi App Service lub uaktualnienia do najnowszej wersji**.

    ![Wdrażanie i uaktualnianie](media/app-service-deploy-ha/01.png)

3. Zaakceptuj postanowienia licencyjne firmy Microsoft, a następnie kliknij przycisk **dalej**.

    ![Postanowienia licencyjne firmy Microsoft](media/app-service-deploy-ha/02.png)

4. Zaakceptuj postanowienia licencyjne firmy Microsoft, a następnie kliknij przycisk **dalej**.

    ![Warunki licencjonowania nienależące do firmy Microsoft](media/app-service-deploy-ha/03.png)

5. Podaj usługi App Service w konfiguracji punktu końcowego w chmurze w środowisku usługi Azure Stack.

    ![Konfiguracji punktu końcowego w chmurze usługi App Service](media/app-service-deploy-ha/04.png)

6. **Połącz** do subskrypcji usługi Azure Stack do użycia podczas instalacji i wybierz lokalizację. 

    ![Połącz z subskrypcją usługi Azure Stack](media/app-service-deploy-ha/05.png)

7. Wybierz **użyć istniejącej sieci wirtualnej i podsieci** i **nazwy grupy zasobów** dla grupy zasobów umożliwiają wdrażanie szablonu o wysokiej dostępności.<br><br>Następnie wybierz sieć wirtualną, utworzone w ramach wdrożenia szablonu, a następnie wybierz odpowiednią rolę podsieci z opcji listy rozwijanej. 

    ![Wybieranie sieci wirtualnej](media/app-service-deploy-ha/06.png)

8. Zapewnić wcześniej zarejestrowane szablon generuje informacje o ścieżce udziału plików i parametrów właściciela udziału plików. Po zakończeniu kliknij przycisk **dalej**.

    ![Informacje o danych wyjściowych udziale plików](media/app-service-deploy-ha/07.png)

9. Ponieważ maszyny są używane do instalowania usługi App Service nie znajduje się w tej samej sieci wirtualnej jako serwera plików używany do hostowania udziału plików w usłudze App Service, nie można rozpoznać nazwy. **Jest to oczekiwane zachowanie**.<br><br>Sprawdź poprawność wprowadzonej informacje konta i ścieżka UNC udziału plików, a następnie naciśnij klawisz **tak** w oknie dialogowym alertu, aby kontynuować instalację usługi App Service.

    ![Oczekiwano pojawia się okno dialogowe](media/app-service-deploy-ha/08.png)

    Jeśli wybierzesz do wdrożenia w istniejącej sieci wirtualnej i wewnętrzny adres IP, aby nawiązać połączenie z serwerem plików, należy dodać regułę zabezpieczeń dla ruchu wychodzącego włączanie ruchu SMB między podsieci procesów roboczych i serwera plików. Przejdź do WorkersNsg w portalu administracyjnym i dodawanie reguły zabezpieczeń dla ruchu wychodzącego z następującymi właściwościami:
    - Źródło: Dowolne
    - Zakres portów źródłowych: *
    - Miejsce docelowe: Adresy IP
    - Docelowy zakres adresów IP: Zakres adresów IP dla serwera plików
    - Zakres portów docelowych: 445
    - Protokół: TCP
    - Akcja: Zezwalaj
    - Priorytet: 700
    - Nazwa: Outbound_Allow_SMB445

10. Podaj identyfikator aplikacji tożsamości i ścieżki i hasła, certyfikaty tożsamości, a następnie kliknij przycisk **dalej**:
    - Certyfikat aplikacji tożsamości (w formacie **sso.appservice.local.azurestack.external.pfx**)
    - Certyfikat główny usługi Azure Resource Manager (**AzureStackCertificationAuthority.cer**)

    ![Identyfikator aplikacji certyfikatów i certyfikatu głównego](media/app-service-deploy-ha/008.png)

10. Następnie podaj pozostałe wymagane informacje dotyczące następujących certyfikatów i kliknij przycisk **dalej**:
    - Domyślny certyfikat SSL stosu Azure (w formacie **_.appservice.local.azurestack.external.pfx**)
    - Certyfikat protokołu SSL interfejsu API (w formacie **api.appservice.local.azurestack.external.pfx**)
    - Certyfikat wydawcy (w postaci **ftp.appservice.local.azurestack.external.pfx**) 

    ![Dodatkowa konfiguracja certyfikatów](media/app-service-deploy-ha/09.png)

11. Podaj informacje o połączeniu programu SQL Server, korzystając z informacji połączenia programu SQL Server z danych wyjściowych z wdrożenia o wysokiej dostępności szablonu:

    ![Informacje o połączeniu programu SQL Server](media/app-service-deploy-ha/10.png)

12. Ponieważ maszyny są używane do instalowania usługi App Service nie znajduje się w tej samej podsieci co program SQL server używana do hostowania baz danych usługi App Service, nie będziesz w stanie rozpoznać nazwę.  **Jest to oczekiwane zachowanie**.<br><br>Sprawdź poprawność wprowadzonej nazwy i konta informacje programu SQL Server, a następnie naciśnij klawisz **tak** w celu kontynuowania instalacji usługi App Service. Kliknij przycisk **Dalej**.

    ![Informacje o połączeniu programu SQL Server](media/app-service-deploy-ha/11.png)

13. Zaakceptuj domyślne wartości konfiguracji roli lub zmienić zalecane wartości i kliknij przycisk **dalej**.<br><br>Zaleca się, że wartości domyślne dla wystąpień roli infrastruktury usługi App Service można zmienić w następujący sposób w przypadku konfiguracji o wysokiej dostępności:

    |Rola|Domyślne|Zalecenia o wysokiej dostępności|
    |-----|-----|-----|
    |Rola kontrolera|2|2|
    |Rola zarządzania|1|3|
    |Rola wydawcy|1|3|
    |Rola frontonu|1|3|
    |Rola współdzielonego procesu roboczego|1|10|
    |     |     |     |

    ![Wartości wystąpienia roli infrastruktury](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Zmiana wartości domyślnych do tych zalecana w tym tutoral zwiększa wymagania sprzętowe dotyczące instalowania usługi App Service. Łącznie 26 rdzeni i 46,592 MB pamięci RAM jest potrzebny do obsługi zalecanych 21 maszyn wirtualnych zamiast domyślnego 18 rdzeni i 32 256 MB pamięci RAM dla maszyn wirtualnych z 15.

14. Wybierz obraz platformy ma używać podczas instalowania na maszynach wirtualnych infrastruktury usługi App Service, a następnie kliknij przycisk **dalej**:

    ![Wybieranie obrazu platformy](media/app-service-deploy-ha/13.png)

15. Podaj usługi App Service informacji o poświadczeniach roli infrastruktury można użyć, a następnie kliknij przycisk **dalej**:

    ![Poświadczenia roli infrastruktury](media/app-service-deploy-ha/14.png)

16. Przejrzyj informacje, które ma być używany do wdrażania usługi App Service, a następnie kliknij przycisk **dalej** do rozpoczęcia wdrożenia. 

    ![Przejrzyj podsumowanie instalacji](media/app-service-deploy-ha/15.png)

17. Przejrzyj postęp wdrażania usługi App Service. Może to potrwać ponad godzinę w zależności od sprzętu i konfiguracji wdrażania. Po pomyślnym zakończeniu pracy Instalatora wybierz **zakończenia**.

    ![Konfigurowanie zostało zakończone](media/app-service-deploy-ha/16.png)


## <a name="next-steps"></a>Kolejne kroki

[Skalowanie usługi App Service](azure-stack-app-service-add-worker-roles.md). Może być konieczne dodanie dodatkowych usługi App Service infrastruktury ról procesów roboczych do spełnienia określonych wymagań oczekiwanych aplikacji w danym środowisku. Domyślnie usługi App Service w usłudze Azure Stack obsługuje warstwy bezpłatna i współdzielona procesu roboczego. Aby dodać inne warstwy procesu roboczego, należy dodać większej liczby ról procesów roboczych.

[Konfigurowanie źródeł wdrożenia](azure-stack-app-service-configure-deployment-sources.md). Dodatkowa konfiguracja jest wymagana do obsługi wdrożenia na żądanie od wielu dostawców kontroli źródła, takich jak GitHub, BitBucket, OneDrive i DropBox.

[Tworzenie kopii zapasowej usługi App Service](app-service-back-up.md). Po pomyślnie wdrażanie i konfigurowanie usługi App Service należy upewnić się, że wszystkie składniki niezbędne do odzyskiwania po awarii kopie zapasowe są tworzone zapobiegania utracie danych i unikanie niepotrzebnych przestojów podczas operacji odzyskiwania.