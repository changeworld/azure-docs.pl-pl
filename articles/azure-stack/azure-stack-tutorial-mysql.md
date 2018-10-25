---
title: Oferują wysoką dostępność bazy danych MySQL w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć dostawcy zasobów MySQL Server komputer-host i baz danych MySQL o wysokiej dostępności przy użyciu usługi Azure Stack.
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
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 84aaa5534c629554074544b4bb56ae8da8825397
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986459"
---
# <a name="tutorial-offer-highly-available-mysql-databases"></a>Samouczek: Oferują baz danych MySQL o wysokiej dostępności

Operator usługi Azure Stack można skonfigurować maszyny wirtualne server do hostowania baz danych z serwera MySQL. MySQL klastra po pomyślnie tworzone i zarządzane przez usługę Azure Stack, użytkownicy, którzy subskrybuje usługi MySQL można łatwo utworzyć baz danych MySQL o wysokiej dostępności.

W tym samouczku pokazano, jak używać usługi Azure Stack elementów portalu marketplace w celu utworzenia [MySQL przy użyciu replikacji klastra](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). To rozwiązanie wymaga wielu maszyn wirtualnych, aby replikować baz danych z węzła głównego do konfigurowalną liczbę replik. Po utworzeniu klastra następnie można dodać serwer usługi Azure Stack MySQL hostingu, a następnie użytkownicy mogą tworzyć baz danych MySQL o wysokiej dostępności.

> [!IMPORTANT]
> **MySQL przy użyciu replikacji** elementu portalu marketplace usługi Azure Stack mogą nie być dostępne dla wszystkich środowisk subskrypcji platformy Azure w chmurze. Sprawdź dostępne w Twojej subskrypcji, przed podjęciem próby postępuj zgodnie z pozostałą część tym tutoral elementu portalu marketplace.

Co dowiesz się:

> [!div class="checklist"]
> * Tworzenie klastra serwera MySQL na podstawie elementów portalu marketplace
> * Tworzenie serwera MySQL z usługi Azure Stack jest hostem serwera
> * Utwórz bazę danych MySQL o wysokiej dostępności

W tym samouczku trzy klastra serwera MySQL na maszynie Wirtualnej zostanie utworzony i skonfigurowany przy użyciu dostępnych elementów portalu marketplace usługi Azure Stack. 

Przed rozpoczęciem wykonywania tych kroków w ramach tego samouczka, upewnij się, że [dostawcy zasobów MySQL Server](azure-stack-mysql-resource-provider-deploy.md) został pomyślnie zainstalowany i czy następujące elementy są dostępne w witrynie marketplace usługi Azure Stack:

> [!IMPORTANT]
> Wszystkie z następujących czynności są wymagane do utworzenia klastra MySQL.

- [MySQL przy użyciu replikacji](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Jest to szablon rozwiązania Bitnami, która będzie służyć do wdrożenia klastra MySQL.
- [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/credativ.Debian8backports?tab=Overview). Debian 8 "Jessie" za pośrednictwem jądra backports dla systemu Microsoft Azure dostarczana przez credativ. Debian GNU/Linux jest jednym z najbardziej popularnych dystrybucji systemu Linux.
- [Niestandardowego skryptu dla systemu linux w wersji 2.0](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview). Rozszerzenie niestandardowego skryptu jest narzędziem do wykonywania usługi aprowizacji maszyny Wirtualnej maszyny Wirtualnej dostosowania zadania wpis. Po dodaniu tego rozszerzenia do maszyny wirtualnej jej pobieranie skryptów z usługi Azure storage i uruchamiać je na maszynie Wirtualnej. Niestandardowe rozszerzenie skryptu zadania można też zautomatyzować przy użyciu poleceń cmdlet programu Azure PowerShell i interfejsu wiersza polecenia dla wielu Platform Azure (xPlat CLI).
- Dostęp do maszyny Wirtualnej systemu Linux rozszerzenie 1.4.7. Rozszerzenie dostępu do maszyny Wirtualnej Umożliwia resetowanie hasła lub klucza SSH, konfiguracje protokołu SSH, dzięki czemu można odzyskać dostęp do maszyny Wirtualnej. Możesz również dodać nowego użytkownika za pomocą hasła lub klucza SSH lub usunąć użytkownika za pomocą tego rozszerzenia. To rozszerzenie jest przeznaczona dla maszyn wirtualnych systemu Linux.

Aby dowiedzieć się więcej na temat dodawania elementów portalu Marketplace usługi Azure Stack, zobacz [Omówienie usługi Azure Stack w portalu Marketplace](azure-stack-marketplace.md).

Należy także klienta protokołu SSH, takich jak [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) do zalogowania się do maszyn wirtualnych systemu Linux po ich wdrożeniu.

## <a name="create-a-mysql-server-cluster"></a>Tworzenie klastra serwera MySQL 
Użyj kroków w tej sekcji, aby wdrożyć serwer MySQL klastrem za pomocą [MySQL przy użyciu replikacji](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) elementu portalu marketplace. Ten szablon umożliwia wdrożenie trzy wystąpienia serwera MySQL skonfigurowane w klastrze MySQL o wysokiej dostępności. Domyślnie powoduje utworzenie następujących zasobów:

- Sieć wirtualna
- Sieciowa grupa zabezpieczeń
- Konto magazynu
- Zestaw dostępności
- Trzy sieci interfejsów (po jednym dla każdej domyślnej maszyn wirtualnych)
- Publiczny adres IP (w przypadku podstawowego MySQL klastra maszyny Wirtualnej)
- Trzech maszyn wirtualnych systemu Linux do hostowania klaster programu MySQL

1. 
[!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. Wybierz **\+** **Utwórz zasób** > **obliczenia**, a następnie **MySQL przy użyciu replikacji**.

   ![Wdrożenie szablonu niestandardowego](media/azure-stack-tutorial-mysqlrp/1.png)

3. Podaj informacje podstawowe wdrożenie na **podstawy** strony. Przejrzyj wartości domyślne i zmień odpowiednio do potrzeb i kliknij przycisk **OK**.<br><br>Jako minimum należy dostarczyć następujące elementy:
   - Nazwa wdrożenia (wartość domyślna to mymysql)
   - Hasło katalogu głównego aplikacji. Podaj hasło alfanumeryczne 12 znaków z **żadnych znaków specjalnych**
   - Nazwa bazy danych aplikacji (wartość domyślna to bitnami)
   - Liczba replik bazy danych MySQL maszyn wirtualnych, aby utworzyć (wartość domyślna to 2)
   - Wybierz subskrypcję do użycia
   - Wybierz grupę zasobów do użycia lub Utwórz nową
   - Wybierz lokalizację (domyślnie jest lokalny dla ASDK)

   [![](media/azure-stack-tutorial-mysqlrp/2-sm.PNG "Podstawowe informacje dotyczące wdrażania")](media/azure-stack-tutorial-mysqlrp/2-lg.PNG#lightbox)

4. Na **konfiguracji środowiska** strony, należy podać następujące informacje, a następnie kliknij przycisk **OK**: 
   - Hasło lub protokół SSH klucza publicznego do użycia na potrzeby uwierzytelniania protokołu secure shell (SSH). Jeśli używasz hasła, musi zawierać litery, cyfry i **można** zawierać znaków specjalnych
   - Rozmiar maszyny Wirtualnej (wartość domyślna to standardowa D1 v2 z maszyn wirtualnych)
   - Rozmiar w GB kliknij dysku danych **OK**

   [![](media/azure-stack-tutorial-mysqlrp/3-sm.PNG "Konfiguracja środowiska")](media/azure-stack-tutorial-mysqlrp/3-lg.PNG#lightbox)

5. Przegląd wdrożenia **Podsumowanie**. Opcjonalnie można pobrać dostosowany szablon i parametry, a następnie kliknij **OK**.

   [![](media/azure-stack-tutorial-mysqlrp/4-sm.PNG "Podsumowanie")](media/azure-stack-tutorial-mysqlrp/4-lg.PNG#lightbox)

6. Kliknij przycisk **Utwórz** na **Kup** strony, aby rozpocząć wdrażanie.

   ![Kup](media/azure-stack-tutorial-mysqlrp/5.png)

    > [!NOTE]
    > Wdrożenie potrwa około godziny. Upewnij się, że wdrożenie zostało zakończone, a klaster programu MySQL została całkowicie skonfigurowana przed kontynuowaniem. 

7. Po pomyślnym zakończeniu wszystkich wdrożeń Przejrzyj elementy grupy zasobów, a następnie wybierz **mysqlip** element adres publiczny adres IP. Zapisz publiczny adres IP i pełną nazwę FQDN publicznego adresu IP dla klastra.<br><br>Konieczne będzie podanie tego operatora usługi Azure Stack, dzięki czemu mogą utworzyć MySQL serwera hostingu, wykorzystując ten klaster programu MySQL.


### <a name="create-a-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń
Domyślnie nie publicznego dostępu jest skonfigurowany dla MySQL do hosta maszyny Wirtualnej. Reguły Sieciowej grupy zabezpieczeń sieci dla ruchu przychodzącego dla dostawcy zasobów usługi Azure Stack MySQL do nawiązywania połączeń i zarządzania klastrem MySQL, musi zostać utworzona.

1. W portalu administratora, przejdź do grupy zasobów utworzonej w przypadku wdrażania klastra MySQL, a następnie wybierz grupę zabezpieczeń sieci (**domyślna podsieci sg**):

   ![open](media/azure-stack-tutorial-mysqlrp/6.png)

2. Wybierz **reguły zabezpieczeń dla ruchu przychodzącego** a następnie kliknij przycisk **Dodaj**.<br><br>Wprowadź **3306** w **zakres portów docelowych** i opcjonalnie wprowadź opis w **nazwa** i **opis** pola. Kliknij przycisk Dodaj, aby zamknąć okno dialogowe reguły zabezpieczeń dla ruchu przychodzącego.

   ![open](media/azure-stack-tutorial-mysqlrp/7.png)

### <a name="configure-external-access-to-the-mysql-cluster"></a>Skonfiguruj dostęp zewnętrzny do klaster programu MySQL
Klaster programu MySQL można było dodać jako hosta serwera MySQL usługi Azure Stack, musi być włączona dostępu zewnętrznego.

1. Przy użyciu klienta SSH, w tym przykładzie użyto [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), zaloguj się na podstawowej maszynie MySQL z poziomu komputera, które mogą uzyskiwać dostęp do publicznego adresu IP. Nazwa podstawowa maszyna wirtualna MySQL zazwyczaj kończy się **0** i ma publiczny adres IP przypisany do niego.<br><br>Użyj publicznego adresu IP i dziennika na tę maszynę Wirtualną przy użyciu nazwy użytkownika **bitnami** i hasła aplikacji utworzone wcześniej bez znaków specjalnych.

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)


2. W oknie klienta SSH Użyj następującego polecenia, aby upewnić się, że usługa bitnami jest aktywny i uruchomiona. Podaj hasło bitnami ponownie po wyświetleniu monitu:

   `sudo service bitnami status`

   ![Sprawdź usługi](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. Utwórz konto użytkownika dostępu zdalnego do użycia przez usługi Azure Stack hostingu serwera MySQL można połączyć z bazą danych MySQL, a następnie Zamknij klienta SSH.<br><br>Uruchom następujące polecenia, aby zalogować się do MySQL jako użytkownik główny, przy użyciu hasła głównego utworzony wcześniej i Utwórz nowego użytkownika administracyjnego, Zastąp *\<username\>* i *\<hasło\>* zgodnie z wymaganiami dla danego środowiska. W tym przykładzie nosi nazwę użytkownika, który ma zostać utworzony **sqlsa** i jest używane silne hasło:

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```
   ![Utwórz administratora](media/azure-stack-tutorial-mysqlrp/bitnami3.png)


4. Zapisz nowe informacje o użytkowniku MySQL.<br><br>Należy podać nazwę i hasło, wraz z publiczny adres IP lub pełną nazwę FQDN publicznego adresu IP dla klastra, operatorowi stosu Azure, dzięki czemu mogą utworzyć hostingu serwera MySQL przy użyciu to klaster programu MySQL.


## <a name="create-an-azure-stack-mysql-hosting-server"></a>Tworzenie serwera MySQL z usługi Azure Stack jest hostem serwera
Po klastra serwerów MySQL utworzono i poprawnie skonfigurowane, operatora usługi Azure Stack, należy utworzyć serwera usługi Azure Stack MySQL hostingu do udostępnienia dodatkowej pojemności dla użytkowników do tworzenia baz danych. 

Należy użyć publiczny adres IP klastra MySQL — podstawowa maszyna wirtualna zarejestrowane wcześniej, po utworzeniu grupy zasobów klastra MySQL na publiczny adres IP lub pełną nazwę FQDN (**mysqlip**). Ponadto operator muszą dowiedzieć się poświadczenia uwierzytelniania utworzonego zdalnie uzyskiwać dostęp do bazy danych MySQL klastra serwera MySQL.

> [!NOTE]
> Ten krok należy uruchomić z portalu administratora usługi Azure Stack przez operatora usługi Azure Stack.

Przy użyciu klastra MySQL publiczny adres IP i bazy danych MySQL informacje logowania uwierzytelniania, Operator usługi Azure Stack można teraz [tworzenie przy użyciu nowego klastra MySQL hostingu serwera MySQL](azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server). 

Upewnij się również został utworzony plany i oferty udostępnić użytkownikom tworzenie bazy danych MySQL. Operator konieczne będzie dodanie **Microsoft.MySqlAdapter** usługę do planu i tworzenie nowego limitu przydziału dla baz danych o wysokiej dostępności. Aby uzyskać więcej informacji na temat tworzenia planów, zobacz [omówienie planu, oferty, przydziału i subskrypcji](azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> **Microsoft.MySqlAdapter** usługa nie będzie można dodawać do planów do momentu [dostawcy zasobów MySQL Server został wdrożony](azure-stack-mysql-resource-provider-deploy.md).



## <a name="create-a-highly-available-mysql-database"></a>Utwórz bazę danych MySQL o wysokiej dostępności
Po klaster programu MySQL utworzono, skonfigurować i dodany jako usługi Azure Stack hostingu serwera MySQL przez operatora usługi Azure Stack, użytkownika dzierżawy z subskrypcją, w tym możliwości bazy danych serwera MySQL można utworzyć baz danych MySQL o wysokiej dostępności przez czynności opisane w tej sekcji. 

> [!NOTE]
> Uruchom następujące kroki, za pomocą portalu użytkownika usługi Azure Stack jako użytkownik dzierżawy z subskrypcją, dostarczając funkcje serwera MySQL (Microsoft.MySQLAdapter service).

1. 
[!INCLUDE [azs-user-portal](../../includes/azs-user-portal.md)]

2. Wybierz **\+** **Utwórz zasób** > **danych \+ magazynu**, a następnie **bazy danych MySQL** .<br><br>Podaj informacje właściwości wymaganej bazy danych, w tym nazwę, sortowania, subskrypcję i lokalizację do użycia dla wdrożenia. 

   ![Tworzenie bazy danych MySQL](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. Wybierz **jednostki SKU** , a następnie wybierz odpowiednią MySQL hostingu serwera jednostkę SKU do użycia. W tym przykładzie został utworzony — Azure Stack Operator **MySQL-HA** jednostki SKU, aby zapewnić wysoką dostępność dla baz danych MySQL klastra.

   ![Wybierz jednostkę SKU](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. Wybierz **logowania** > **Utwórz nowy identyfikator logowania** a następnie wprowadź poświadczenia uwierzytelniania MySQL, który ma być używany dla nowej bazy danych. Po zakończeniu kliknij przycisk **OK** i następnie **Utwórz** do rozpoczęcia procesu wdrażania bazy danych.

   ![Dodaj logowanie](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. Po pomyślnym zakończeniu wdrażania bazy danych MySQL, przejrzyj właściwości bazy danych, aby dowiedzieć się, parametry połączenia służące do łączenia się z nową bazę danych o wysokiej dostępności. 

   ![Wyświetl parametry połączenia](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie klastra serwera MySQL na podstawie elementów portalu marketplace
> * Tworzenie serwera MySQL z usługi Azure Stack jest hostem serwera
> * Utwórz bazę danych MySQL o wysokiej dostępności

Przejdź do następnego samouczka, aby dowiedzieć się, jak:
> [!div class="nextstepaction"]
> [Oferowanie aplikacji web apps](azure-stack-tutorial-app-service.md)
