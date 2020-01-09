---
title: 'Samouczek: udostępnianie poza swoją organizacji — udział danych platformy Azure'
description: Samouczek — udostępnianie danych klientom i partnerom za pomocą udziału danych platformy Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 8749f7dee2ceeb09e37cc97d4e5bfe76c52e2da6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438733"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Samouczek: udostępnianie danych za pomocą udziału danych platformy Azure  

W tym samouczku dowiesz się, jak skonfigurować nowy udział danych platformy Azure i zacząć udostępniać dane klientom i partnerom spoza organizacji platformy Azure. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz usługę Data Share.
> * Dodaj zestawy danych do usługi Data Share.
> * Włącz harmonogram synchronizacji dla usługi Data Share. 
> * Dodaj odbiorców do usługi Data Share. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Adres e-mail logowania odbiorcy platformy Azure (przy użyciu aliasu poczty e-mail nie będzie działał).

### <a name="share-from-a-storage-account"></a>Udostępnianie z konta magazynu:

* Konto usługi Azure Storage: Jeśli jeszcze go nie masz, możesz utworzyć [konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Uprawnienie do dodawania przypisywania roli do konta magazynu, które jest obecne w uprawnieniach *Microsoft. Autoryzacja/Przypisywanie ról/zapis* . To uprawnienie istnieje w roli właściciela. 

### <a name="share-from-a-sql-based-source"></a>Udostępnianie z poziomu źródła opartego na języku SQL:

* Azure SQL Database lub Azure SQL Data Warehouse z tabelami i widokami, które chcesz udostępnić.
* Uprawnienie do udziału danych w celu uzyskania dostępu do magazynu danych. Można to zrobić, wykonując następujące czynności: 
    1. Ustaw siebie jako administratora Azure Active Directory dla serwera.
    1. Nawiąż połączenie z magazynem Azure SQL Database/danymi przy użyciu Azure Active Directory.
    1. Użyj edytora zapytań (wersja zapoznawcza), aby wykonać poniższy skrypt w celu dodania pliku MSI udziału danych jako db_owner. Musisz nawiązać połączenie przy użyciu Active Directory, a nie SQL Server uwierzytelniania. 
    
```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```                   
Należy pamiętać, że *< share_acc_name >* jest nazwą konta udziału danych. Jeśli konto udziału danych nie zostało jeszcze utworzone, możesz wrócić do tego wymagania wstępnego później.  

* [Azure SQL Database użytkownikowi z dostępem `db_owner`](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users) do nawigowania i wybierania tabel i/lub widoków, które chcesz udostępnić. 

* Adres IP klienta SQL Server dostęp do zapory: można to zrobić, wykonując następujące kroki: 1. Przejdź do obszarze *zapory i sieci wirtualne* 1. Kliknij przełącznik **, aby zezwolić na dostęp** do usług platformy Azure. 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [portalu Azure](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Tworzenie konta udziału danych

Utwórz zasób udziału danych platformy Azure w grupie zasobów platformy Azure.

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu.

1. Wyszukaj *udział danych*.

1. Wybierz pozycję udział danych i wybierz pozycję **Utwórz**.

1. Wypełnij podstawowe szczegóły zasobu udziału danych platformy Azure, korzystając z poniższych informacji. 

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Nazwa | *datashareacount* | Określ nazwę konta udziału danych. |
    | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla konta udziału danych.|
    | Grupa zasobów | *test-resource-group* | Użyj istniejącej grupy zasobów lub utwórz nową. |
    | Lokalizacja | *Wschodnie stany USA 2* | Wybierz region dla konta udziału danych.
    | | |

1. Wybierz pozycję **Utwórz** , aby zainicjować obsługę administracyjną konta udziału danych. Inicjowanie obsługi nowego konta udziału danych zwykle trwa około 2 minuty. 

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

## <a name="create-a-data-share"></a>Tworzenie udziału danych

1. Przejdź do strony Przegląd udostępniania danych.

    ![Udostępnianie danych](./media/share-receive-data.png "Udostępnianie danych") 

1. Wybierz pozycję **Rozpocznij udostępnianie danych**.

1. Wybierz pozycję **Utwórz**.   

1. Wypełnij szczegóły dotyczące udziału danych. Określ nazwę, opis zawartości udziału i warunki użytkowania (opcjonalnie). 

    ![EnterShareDetails](./media/enter-share-details.png "Wprowadź szczegóły udostępniania") 

1. Wybierz pozycję **Kontynuuj**

1. Aby dodać zbiory danych do swojego udziału, wybierz pozycję **Dodaj zestawy**. 

    ![Zestawy danych](./media/datasets.png "Zestawy danych")

1. Wybierz typ zestawu danych, który chcesz dodać. W przypadku udostępniania z Azure SQL Database lub usługi Azure SQL DataWarehouse zostanie wyświetlony monit o podanie pewnych poświadczeń SQL. Uwierzytelnianie przy użyciu użytkownika utworzonego w ramach wymagań wstępnych.

    ![Adddatasets](./media/add-datasets.png "Dodaj zestawy danych")    

1. Przejdź do obiektu, który chcesz udostępnić, i wybierz pozycję "Dodaj zestawy danych". 

    ![SelectDatasets](./media/select-datasets.png "Wybierz zestawy danych")    

1. Na karcie adresaci wprowadź adres e-mail odbiorcy danych, wybierając pozycję "+ Dodaj odbiorcę". 

    ![Addrecipients](./media/add-recipient.png "Dodawanie adresatów") 

1. Wybierz pozycję **Kontynuuj**

1. Jeśli chcesz, aby odbiorca danych mógł uzyskać aktualizacje przyrostowe danych, Włącz harmonogram migawek. 

    ![EnableSnapshots](./media/enable-snapshots.png "Włącz migawki") 

1. Wybierz czas rozpoczęcia i interwał cyklu. 

1. Wybierz pozycję **Kontynuuj**

1. Na karcie Recenzja + tworzenie przejrzyj zawartość pakietu, ustawienia, adresatów i ustawienia synchronizacji. Wybierz pozycję **Utwórz**

Udział danych platformy Azure został utworzony, a odbiorca Twojego udziału danych jest teraz gotowy do zaakceptowania Twojego zaproszenia. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia udziału danych platformy Azure i zapraszania adresatów. Aby dowiedzieć się, jak odbiorca danych może zaakceptować i odebrać udział danych, przejdź do samouczka [akceptowanie i odbieranie danych](subscribe-to-data-share.md) . 
