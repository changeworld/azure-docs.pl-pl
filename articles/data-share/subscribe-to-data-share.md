---
title: 'Samouczek: akceptowanie & odbierania danych — udział danych platformy Azure'
description: Samouczek — akceptowanie i odbieranie danych za pomocą udziału danych platformy Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 9c24f54fe846459187488b0a65b2582914e25e2a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499352"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Samouczek: akceptowanie i odbieranie danych za pomocą udziału danych platformy Azure  

W tym samouczku dowiesz się, jak zaakceptować zaproszenie do udziału danych przy użyciu udziału danych platformy Azure. Dowiesz się, jak odbierać dane udostępniane Tobie, a także jak włączyć okres regularnego odświeżania, aby upewnić się, że zawsze masz najnowszą migawkę danych udostępnionych Tobie. 

> [!div class="checklist"]
> * Jak zaakceptować zaproszenie do udziału danych platformy Azure
> * Tworzenie konta udziału danych platformy Azure
> * Określ miejsce docelowe dla danych
> * Utwórz subskrypcję udziału danych na potrzeby zaplanowanego odświeżania

## <a name="prerequisites"></a>Wymagania wstępne
Przed zaakceptowaniem zaproszenia do udziału danych musisz udostępnić kilka zasobów platformy Azure, które są wymienione poniżej. 

Przed zaakceptowaniem zaproszenia udziału danych upewnij się, że wszystkie wymagania wstępne zostały ukończone. 

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Zaproszenie do udziału danych: zaproszenie od Microsoft Azure z podmiotem zatytułowanym "zaproszenie do udziału danych platformy Azure z **<yourdataprovider@domain.com>** ".

### <a name="receive-data-into-a-storage-account"></a>Odbierz dane do konta magazynu: 

* Konto usługi Azure Storage: Jeśli jeszcze go nie masz, możesz utworzyć [konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Uprawnienie do dodawania przypisywania roli do konta magazynu, które jest obecne w uprawnieniach *Microsoft. Autoryzacja/Przypisywanie ról/zapis* . To uprawnienie istnieje w roli właściciela. 
* Rejestracja dostawcy zasobów dla elementu Microsoft. datashare. Informacje o tym, jak to zrobić, zawiera dokumentacja [dostawcy zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) . 

> [!IMPORTANT]
> Aby zaakceptować i odebrać udział danych platformy Azure, musisz najpierw zarejestrować dostawcę zasobów Microsoft. datashare i musisz być właścicielem konta magazynu, do którego są akceptowane dane. Postępuj zgodnie z instrukcjami opisanymi w temacie [Rozwiązywanie problemów z udziałem danych platformy Azure](data-share-troubleshoot.md) , aby zarejestrować dostawcę zasobów udziału danych oraz dodać siebie jako właściciela konta magazynu. 

### <a name="receive-data-into-a-sql-based-source"></a>Odbieraj dane w źródle opartym na języku SQL:

* Uprawnienie do pliku MSI udostępniania danych w celu uzyskania dostępu do Azure SQL Database lub Azure SQL Data Warehouse. Można to zrobić, wykonując następujące czynności: 
    1. Ustaw siebie jako administratora Azure Active Directory dla serwera.
    1. Nawiąż połączenie z magazynem Azure SQL Database/danymi przy użyciu Azure Active Directory.
    1. Użyj edytora zapytań (wersja zapoznawcza), aby wykonać poniższy skrypt w celu dodania pliku MSI udziału danych jako db_owner. Musisz nawiązać połączenie przy użyciu Active Directory, a nie SQL Server uwierzytelniania. 

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Należy pamiętać, że *< share_acc_name >* to nazwa konta udziału danych. Jeśli konto udziału danych nie zostało jeszcze utworzone, możesz wrócić do tego wymagania wstępnego później.         

* Adres IP klienta SQL Server dostęp do zapory: można to zrobić, wykonując następujące kroki: 1. Przejdź do obszarze *zapory i sieci wirtualne* 1. Kliknij przełącznik **, aby zezwolić na dostęp** do usług platformy Azure. 

Po ukończeniu tych wymagań wstępnych możesz otrzymywać dane do SQL Server.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Otwórz zaproszenie

Sprawdź zaproszenie od dostawcy danych w skrzynce odbiorczej. Zaproszenie pochodzi z Microsoft Azure, zatytułowane **zaproszenie udziału danych platformy Azure z <yourdataprovider@domain.com>** . Zwróć uwagę na nazwę udziału, aby upewnić się, że zaakceptujesz prawidłowy udział, jeśli istnieje wiele zaproszeń. 

Wybierz pozycję **Wyświetl zaproszenie** , aby zobaczyć zaproszenie na platformie Azure. Spowoduje to przejście do widoku otrzymane udziały.

![Ogłoszeń](./media/invitations.png "Lista zaproszeń") 

Wybierz udział, który chcesz wyświetlić. 

## <a name="accept-invitation"></a>Zaakceptuj zaproszenie
Upewnij się, że wszystkie pola są przeglądane, w tym **warunki użytkowania**. Jeśli akceptujesz warunki użytkowania, musisz zaznaczyć pole wyboru, aby wskazać, że zgadzasz się. 

![Warunki użytkowania](./media/terms-of-use.png "Warunki użytkowania") 

W obszarze *docelowe konto udziału danych*wybierz subskrypcję i grupę zasobów, w ramach której będziesz wdrażać udział danych. 

W polu **konto udostępniania danych** wybierz pozycję **Utwórz nową** , jeśli nie masz istniejącego konta udziału danych. W przeciwnym razie wybierz istniejące konto udziału danych, do którego chcesz zaakceptować udział danych. 

W polu *Nazwa otrzymanego udziału* można pozostawić wartość domyślną określoną przez dane lub określić nową nazwę dla odebranego udziału. 

![Docelowe konto udziału danych](./media/target-data-share.png "Docelowe konto udziału danych") 

Po uzgodnieniu warunków użytkowania i określeniu lokalizacji dla udziału wybierz opcję *Zaakceptuj i skonfiguruj*. W przypadku wybrania tej opcji zostanie utworzona subskrypcja udziału, a na następnym ekranie zostanie wyświetlony monit o wybranie docelowego konta magazynu, do którego mają zostać skopiowane dane. 

![Zaakceptuj opcje](./media/accept-options.png "Zaakceptuj opcje") 

Jeśli wolisz zaakceptować zaproszenie teraz, ale skonfigurować magazyn w późniejszym czasie, wybierz opcję *Zaakceptuj i skonfiguruj później*. Ta opcja umożliwia późniejsze skonfigurowanie docelowego konta magazynu. Aby kontynuować konfigurowanie magazynu później, zobacz [jak skonfigurować swoją stronę konta magazynu](how-to-configure-mapping.md) , aby uzyskać szczegółowe instrukcje dotyczące wznawiania konfiguracji udziału danych. 

Jeśli nie chcesz zaakceptować zaproszenia, wybierz pozycję *Odrzuć*. 

## <a name="configure-storage"></a>Konfigurowanie magazynu
W obszarze *docelowe ustawienia magazynu*wybierz subskrypcję, grupę zasobów i konto magazynu, do których chcesz otrzymywać dane. 

![Ustawienia magazynu docelowego](./media/target-storage-settings.png "Magazyn docelowy") 

Aby otrzymywać regularne odświeżanie danych, upewnij się, że zostały włączone ustawienia migawki. Należy zauważyć, że tylko harmonogram ustawień migawek zostanie wyświetlony, jeśli dostawca danych włączył go w udziale danych. 

![Ustawienia migawki](./media/snapshot-settings.png "Ustawienia migawki") 

Wybierz pozycję *Zapisz*. 

> [!IMPORTANT]
> Jeśli otrzymujesz dane oparte na języku SQL i chcesz otrzymywać te dane do źródła opartego na języku SQL, zapoznaj się z naszym przewodnikiem [Konfigurowanie mapowania zestawu danych](how-to-configure-mapping.md) , aby dowiedzieć się, jak skonfigurować SQL Server jako lokalizację docelową zestawu danych. 

## <a name="trigger-a-snapshot"></a>Wyzwalanie migawki

Możesz wyzwolić migawkę na karcie otrzymane udziały — > Szczegóły, wybierając pozycję **Wyzwól migawkę**. Tutaj można wyzwolić pełną lub przyrostową migawkę danych. Jeśli po raz pierwszy otrzymujesz dane od dostawcy danych, wybierz pozycję pełna kopia. 

![Wyzwalanie migawki](./media/trigger-snapshot.png "Wyzwalanie migawki") 

Po *pomyślnym*zakończeniu ostatniego uruchomienia programu Otwórz konto magazynu, aby wyświetlić odebrane dane. 

Aby sprawdzić, które konto magazynu zostało użyte, wybierz pozycję **zestawy danych**. 

![Zestawy danych dla odbiorców](./media/consumer-datasets.png "Mapowanie zestawu danych klienta") 

## <a name="view-history"></a>Wyświetlanie historii
Aby wyświetlić historię migawek, przejdź do obszaru otrzymane udziały — Historia >. W tym miejscu znajdziesz historię wszystkich migawek, które zostały wygenerowane w ciągu ostatnich 60 dni. 

## <a name="next-steps"></a>Następne kroki
W tym samouczku dowiesz się, jak akceptować i odbierać udział danych platformy Azure. Aby dowiedzieć się więcej o pojęciach dotyczących udziałów danych platformy Azure, przejdź do [pojęcia: Terminologia dotycząca udziałów danych platformy Azure](terminology.md).