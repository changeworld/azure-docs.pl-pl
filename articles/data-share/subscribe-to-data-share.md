---
title: 'Samouczek: Akceptowanie & odbieranie danych — Udostępnianie danych platformy Azure'
description: Samouczek — akceptowanie i odbieranie danych przy użyciu udziału danych platformy Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 5b7d9cd7e7d438cf2beac76d5d8bcc78d377a8f4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77083111"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Samouczek: Akceptowanie i odbieranie danych przy użyciu udziału danych platformy Azure  

W tym samouczku dowiesz się, jak zaakceptować zaproszenie do udostępniania danych przy użyciu usługi Azure Data Share. Dowiesz się, jak odbierać dane udostępniane, a także jak włączyć regularny interwał odświeżania, aby upewnić się, że zawsze masz najnowszą migawkę danych udostępnianych Użytkownikowi. 

> [!div class="checklist"]
> * Jak zaakceptować zaproszenie do udostępniania danych platformy Azure
> * Tworzenie konta udziału danych platformy Azure
> * Określanie miejsca docelowego dla danych
> * Tworzenie subskrypcji udziału danych w celu zaplanowanego odświeżenia

## <a name="prerequisites"></a>Wymagania wstępne
Aby można było zaakceptować zaproszenie do udostępniania danych, należy aprowizować szereg zasobów platformy Azure, które są wymienione poniżej. 

Przed zaakceptowaniem zaproszenia do udostępniania danych upewnij się, że wszystkie wymagania wstępne zostały ukończone. 

* Subskrypcja platformy Azure: jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem subskrypcji.
* Zaproszenie do udostępniania danych: zaproszenie od platformy Microsoft Azure z **<yourdataprovider@domain.com>** tematem zatytułowanym "Zaproszenie do udostępniania danych platformy Azure z ".
* Zarejestruj [dostawcę zasobów Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) w ramach subskrypcji platformy Azure, w której utworzysz zasób udziału danych i subskrypcję platformy Azure, w której znajdują się docelowe magazyny danych platformy Azure.

### <a name="receive-data-into-a-storage-account"></a>Odbieranie danych na konto magazynu: 

* Konto usługi Azure Storage: jeśli jeszcze go nie masz, możesz utworzyć [konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Uprawnienie do zapisu na koncie magazynu, które jest obecne w *programie Microsoft.Storage/storageKonta/zapis.* To uprawnienie istnieje w roli współautora. 
* Uprawnienie do dodawania przypisania roli do konta magazynu, które znajduje się w programie *Microsoft.Authorization/role assignments/write*. To uprawnienie istnieje w roli Właściciela.  

### <a name="receive-data-into-a-sql-based-source"></a>Odbieranie danych do źródła opartego na języku SQL:

* Uprawnienie do zapisywania do baz danych na serwerze SQL, który jest obecny w *microsoft.sql/servers/databases/write*. To uprawnienie istnieje w roli współautora. 
* Uprawnienie do zarządzanej tożsamości zasobu udziału danych w celu uzyskania dostępu do bazy danych SQL Azure lub usługi Azure SQL Data Warehouse. Można to zrobić za pomocą następujących kroków: 
    1. Ustaw się jako administrator usługi Azure Active Directory dla serwera SQL.
    1. Połącz się z usługą Azure SQL Database/Data Warehouse przy użyciu usługi Azure Active Directory.
    1. Użyj Edytora zapytań (wersja zapoznawcza), aby wykonać następujący skrypt, aby dodać tożsamość zarządzaną udziału danych jako "db_datareader, db_datawriter, db_ddladmin". Należy połączyć się przy użyciu usługi Active Directory, a nie uwierzytelniania programu SQL Server. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Należy zauważyć, że *<share_acc_name>* jest nazwą zasobu udziału danych. Jeśli nie utworzono jeszcze zasobu udziału danych, możesz wrócić do tego wstępnego wymaganego później.         

* Dostęp do zapory serwera SQL Server klienta. Można to zrobić za pomocą następujących kroków: 
    1. Na serwerze SQL w witrynie Azure portal przejdź do *zapór i sieci wirtualnych*
    1. Kliknij przełącznik **na,** aby zezwolić na dostęp do usług platformy Azure.
    1. Kliknij **pozycję +Dodaj adres IP klienta** i kliknij pozycję **Zapisz**. Adres IP klienta może ulec zmianie. Ten proces może być konieczne powtórzenie przy następnym odbierania danych do obiektu docelowego SQL z witryny Azure portal. Można również dodać zakres adresów IP. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Odbieranie danych do klastra usługi Azure Data Explorer: 

* Klaster Eksploratora danych platformy Azure w tym samym centrum danych platformy Azure co klaster eksploratora danych dostawcy danych: Jeśli jeszcze go nie masz, możesz utworzyć [klaster Eksploratora danych platformy Azure.](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) Jeśli nie znasz centrum danych platformy Azure klastra dostawcy danych, możesz utworzyć klaster w dalszej części procesu.
* Uprawnienie do zapisywania w klastrze usługi Azure Data Explorer, który jest obecny w pliku *Microsoft.Kusto/clusters/write*. To uprawnienie istnieje w roli współautora. 
* Uprawnienie do dodawania przypisania roli do klastra Usługi Azure Data Explorer, który znajduje się w programie *Microsoft.Authorization/role assignments/write*. To uprawnienie istnieje w roli Właściciela. 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Otwarte zaproszenie

1. Sprawdź w skrzynce odbiorczej zaproszenie od dostawcy danych. Zaproszenie pochodzi z platformy Microsoft Azure, zatytułowanej **Zaproszenie do udostępniania danych platformy Azure z programu <yourdataprovider@domain.com> **. Zanotuj nazwę udziału, aby upewnić się, że akceptujesz poprawny udział, jeśli istnieje wiele zaproszeń. 

1. Wybierz na **wyświetl zaproszenie,** aby wyświetlić zaproszenie na platformie Azure. Spowoduje to przejście do widoku Odebrane udziały.

   ![Zaproszenia](./media/invitations.png "Lista zaproszeń") 

1. Wybierz udział, który chcesz wyświetlić. 

## <a name="accept-invitation"></a>Zaakceptuj zaproszenie
1. Upewnij się, że wszystkie pola zostały sprawdzone, w tym **Warunki użytkowania**. Jeśli zgadzasz się na warunki użytkowania, musisz zaznaczyć to pole, aby wyrazić na to zgodę. 

   ![Warunki użytkowania](./media/terms-of-use.png "Warunki użytkowania") 

1. W obszarze *Docelowe konto udostępniania danych*wybierz grupę subskrypcji i zasobów, w której będziesz wdrażać udział danych. 

   W polu **Konto udostępniania danych** wybierz pozycję **Utwórz nowy,** jeśli nie masz istniejącego konta udziału w danych. W przeciwnym razie wybierz istniejące konto udostępniania danych, na które chcesz zaakceptować udostępnianie danych. 

   W polu **Odebrana nazwa udziału** można pozostawić wartość domyślną określoną przez podane przez dane lub określić nową nazwę odebranego udziału. 

   ![Docelowe konto udziału danych](./media/target-data-share.png "Docelowe konto udziału danych") 

1. Po zaakceptowaniu warunków użytkowania i określeniu lokalizacji udziału wybierz w polu *Zaakceptuj i skonfiguruj*. Zostanie utworzona subskrypcja udziałów.

   W przypadku udostępniania opartego na migawce na następnym ekranie zostanie wyświetlony z prośbą o wybranie docelowego konta magazynu dla danych, które mają zostać skopiowane. 

   ![Zaakceptuj opcje](./media/accept-options.png "Zaakceptuj opcje") 

   Jeśli wolisz zaakceptować zaproszenie teraz, ale skonfigurować docelowy magazyn danych w późniejszym czasie, wybierz *zaakceptuj i skonfiguruj później*. Aby kontynuować konfigurowanie magazynu później, zobacz konfigurowanie strony [mapowań zestawów danych,](how-to-configure-mapping.md) aby uzyskać szczegółowe kroki dotyczące wznowienia konfiguracji udostępniania danych. 

   Aby uzyskać informacje o udostępnianiu w miejscu, zobacz konfigurowanie strony [mapowania zestawów danych,](how-to-configure-mapping.md) aby uzyskać szczegółowe instrukcje dotyczące wznawiania konfiguracji udostępniania danych. 

   Jeśli nie chcesz akceptować zaproszenia, wybierz pozycję *Odrzuć*. 

## <a name="configure-storage"></a>Konfigurowanie magazynu
1. W obszarze *Docelowe ustawienia magazynu*wybierz konto Subskrypcja, Grupa zasobów i magazyn, na które chcesz otrzymywać dane. 

   ![Docelowe ustawienia magazynu](./media/target-storage-settings.png "Magazyn docelowy") 

1. Aby regularnie aktualizować dane, upewnij się, że włączysz ustawienia migawki. Należy zauważyć, że harmonogram ustawiania migawki będzie widoczny tylko wtedy, gdy dostawca danych uwzględnił go w udziale danych. 

   ![Ustawienia migawki](./media/snapshot-settings.png "Ustawienia migawki") 

1. Wybierz *pozycję Zapisz*. 

> [!IMPORTANT]
> Jeśli otrzymujesz dane oparte na języku SQL i chcesz odbierać te dane do źródła opartego na języku SQL, odwiedź [stronę konfigurowania przewodnika mapowania zestawu danych,](how-to-configure-mapping.md) aby dowiedzieć się, jak skonfigurować program SQL Server jako miejsce docelowe zestawu danych. 

## <a name="trigger-a-snapshot"></a>Wyzwalanie migawki
Te kroki dotyczą tylko udostępniania opartego na migawkowaniu.

1. Migawkę można wyzwolić na karcie Odebrane udziały -> Szczegóły, wybierając **migawkę wyzwalacza**. W tym miejscu można wyzwolić pełną lub przyrostową migawkę danych. Jeśli po raz pierwszy odbierasz dane od dostawcy danych, wybierz pełną kopię. 

   ![Migawka wyzwalania](./media/trigger-snapshot.png "Migawka wyzwalania") 

1. Po *pomyślnym*zakończeniu ostatniego uruchomienia przejdź do docelowego magazynu danych, aby wyświetlić odebrane dane. Wybierz **pozycję Zestawy danych**i kliknij łącze w ścieżce docelowej. 

   ![Zestawy danych konsumenckich](./media/consumer-datasets.png "Mapowanie zestawu danych konsumenckich") 

## <a name="view-history"></a>Wyświetlanie historii
Aby wyświetlić historię migawek, przejdź do sekcji Odebrane udziały - > historia. Tutaj znajdziesz historię wszystkich migawek, które zostały wygenerowane w ciągu ostatnich 60 dni. 

## <a name="next-steps"></a>Następne kroki
W tym samouczku dowiesz się, jak zaakceptować i odbierać udział danych platformy Azure. Aby dowiedzieć się więcej o pojęciach dotyczących udostępniania danych platformy Azure, przejdź do [koncepcji: Terminologia udostępniania danych platformy Azure](terminology.md).