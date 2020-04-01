---
title: 'Samouczek: Udostępnianie poza swoją organizacji - Azure Data Share'
description: Samouczek — udostępnianie danych klientom i partnerom przy użyciu usługi Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: a8265680f74b2d5679d1ebfbb2873dd096f498a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77083053"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Samouczek: Udostępnianie danych przy użyciu udziału danych platformy Azure  

W tym samouczku dowiesz się, jak skonfigurować nowy udział danych platformy Azure i rozpocząć udostępnianie danych klientom i partnerom spoza organizacji platformy Azure. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz usługę Data Share.
> * Dodaj zestawy danych do usługi Data Share.
> * Włącz harmonogram migawek dla udziału danych. 
> * Dodaj odbiorców do usługi Data Share. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem subskrypcji.
* Adres e-mail logowania do logowania adresata platformy Azure (przy użyciu aliasu e-mail nie będzie działać).
* Jeśli źródłowy magazyn danych platformy Azure znajduje się w innej subskrypcji platformy Azure niż ta, której użyjesz do utworzenia zasobu udostępniania danych, zarejestruj [dostawcę zasobów Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) w ramach subskrypcji, w której znajduje się magazyn danych platformy Azure. 

### <a name="share-from-a-storage-account"></a>Udostępnianie z konta magazynu:

* Konto usługi Azure Storage: jeśli jeszcze go nie masz, możesz utworzyć [konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Uprawnienie do zapisu na koncie magazynu, które jest obecne w *programie Microsoft.Storage/storageKonta/zapis.* To uprawnienie istnieje w roli współautora.
* Uprawnienie do dodawania przypisania roli do konta magazynu, które znajduje się w programie *Microsoft.Authorization/role assignments/write*. To uprawnienie istnieje w roli Właściciela. 


### <a name="share-from-a-sql-based-source"></a>Udostępnianie ze źródła opartego na języku SQL:

* Usługa Azure SQL Database lub usługa Azure Synapse Analytics (dawniej usługa Azure SQL Data Warehouse) z tabelami i widokami, które chcesz udostępnić.
* Uprawnienie do zapisywania w bazach danych na serwerze SQL, który jest obecny w *programie Microsoft.Sql/servers/databases/write*. To uprawnienie istnieje w roli współautora.
* Uprawnienie do udostępniania danych w celu uzyskania dostępu do magazynu danych. Można to zrobić za pomocą następujących kroków: 
    1. Ustaw się jako administrator usługi Azure Active Directory dla serwera SQL.
    1. Połącz się z usługą Azure SQL Database/Data Warehouse przy użyciu usługi Azure Active Directory.
    1. Użyj Edytora zapytań (wersja zapoznawcza), aby wykonać następujący skrypt, aby dodać tożsamość zarządzaną zasobu udziału danych jako db_datareader. Należy połączyć się przy użyciu usługi Active Directory, a nie uwierzytelniania programu SQL Server. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Należy zauważyć, że *<share_acc_name>* jest nazwą zasobu udziału danych. Jeśli nie utworzono jeszcze zasobu udziału danych, możesz wrócić do tego wstępnego wymaganego później.  

* Użytkownik bazy danych SQL platformy Azure z dostępem "db_datareader" do nawigacji i wybierz tabele i/lub widoki, które chcesz udostępnić. 

* Dostęp do zapory serwera SQL Server klienta. Można to zrobić za pomocą następujących kroków: 
    1. Na serwerze SQL w witrynie Azure portal przejdź do *zapór i sieci wirtualnych*
    1. Kliknij przełącznik **na,** aby zezwolić na dostęp do usług platformy Azure.
    1. Kliknij **pozycję +Dodaj adres IP klienta** i kliknij pozycję **Zapisz**. Adres IP klienta może ulec zmianie. Ten proces może być konieczne powtórzenie przy następnym udostępnianiu danych SQL z witryny Azure portal. Można również dodać zakres adresów IP. 

### <a name="share-from-azure-data-explorer"></a>Udostępnianie z Eksploratora danych platformy Azure
* Klaster usługi Azure Data Explorer z bazami danych, które chcesz udostępnić.
* Uprawnienie do zapisywania w klastrze usługi Azure Data Explorer, który jest obecny w programie *Microsoft.Kusto/clusters/write*. To uprawnienie istnieje w roli współautora.
* Uprawnienie do dodawania przypisania roli do klastra Usługi Azure Data Explorer, który znajduje się w programie *Microsoft.Authorization/role assignments/write*. To uprawnienie istnieje w roli Właściciela.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Tworzenie konta udziału danych

Tworzenie zasobu udziału danych platformy Azure w grupie zasobów platformy Azure.

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu.

1. Wyszukaj *udział danych*.

1. Wybierz pozycję Udostępnianie danych i wybierz pozycję **Utwórz**.

1. Wypełnij podstawowe szczegóły zasobu udostępniania danych platformy Azure o następujące informacje. 

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Nazwa | *datashareacount (liczba danych)* | Określ nazwę konta udziału danych. |
    | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć dla swojego konta udziału danych.|
    | Grupa zasobów | *grupa test-zasób* | Użyj istniejącej grupy zasobów lub utwórz nową. |
    | Lokalizacja | *Wschodnie stany USA 2* | Wybierz region dla konta udostępniania danych.
    | | |

1. Wybierz **pozycję Utwórz,** aby aprowizować konto udziału danych. Inicjowanie obsługi administracyjnej nowego konta udziału danych zwykle trwa około 2 minut lub mniej. 

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

## <a name="create-a-data-share"></a>Tworzenie udziału danych

1. Przejdź do strony Przegląd udostępniania danych.

    ![Udostępnianie danych](./media/share-receive-data.png "Udostępnianie danych") 

1. Wybierz **pozycję Rozpocznij udostępnianie danych**.

1. Wybierz **pozycję Utwórz**.   

1. Wypełnij szczegóły udziału danych. Określ nazwę, typ udziału, opis zawartości udziału i warunki użytkowania (opcjonalnie). 

    ![EnterShareDetails (EnterShareDetails)](./media/enter-share-details.png "Wprowadź szczegóły udostępniania") 

1. Wybierz **przycisk Kontynuuj**

1. Aby dodać zestawy danych do udziału danych, wybierz pozycję **Dodaj zestawy danych**. 

    ![Zestawy danych](./media/datasets.png "Zestawy danych")

1. Wybierz typ zestawu danych, który chcesz dodać. Zostanie wyświetlona inna lista typów zestawów danych w zależności od typu udziału (migawki lub miejsca) wybranego w poprzednim kroku. Jeśli udostępnianie z bazy danych SQL Azure lub usługi Azure SQL Data Warehouse, zostanie wyświetlony monit o niektóre poświadczenia SQL. Uwierzytelnij się przy użyciu użytkownika utworzonego jako część wymagań wstępnych.

    ![Zestawy danych add](./media/add-datasets.png "Dodawanie zestawów danych")    

1. Przejdź do obiektu, który chcesz udostępnić, i wybierz opcję "Dodaj zestawy danych". 

    ![Wybierz zestawy danych](./media/select-datasets.png "Wybieranie zestawów danych")    

1. Na karcie Adresaci wprowadź adresy e-mail konsumenta danych, wybierając opcję "+ Dodaj odbiorcę". 

    ![AddRecipients (AddRecipients)](./media/add-recipient.png "Dodawanie adresatów") 

1. Wybierz **przycisk Kontynuuj**

1. Jeśli wybrano typ udziału migawki, można skonfigurować harmonogram migawek, aby zapewnić aktualizacje danych do konsumenta danych. 

    ![EnableSnapshots (Włącznapshots)](./media/enable-snapshots.png "Włączanie migawek") 

1. Wybierz interwał czasu rozpoczęcia i cyklu. 

1. Wybierz **przycisk Kontynuuj**

1. Na karcie Recenzja + Utwórz przejrzyj zawartość pakietu, ustawienia, adresaci i ustawienia synchronizacji. Wybierz **pozycję Utwórz**

Twój udział danych platformy Azure został utworzony, a odbiorca udostępniania danych jest teraz gotowy do zaakceptowania zaproszenia. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak utworzyć udział danych platformy Azure i zaprosić adresatów. Aby dowiedzieć się, jak konsument danych może akceptować i odbierać udostępnianie danych, kontynuuj [akceptowanie i odbieranie danych](subscribe-to-data-share.md) samouczek. 
